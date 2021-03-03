---
title: Virtuella Azure-datorer med hög tillgänglighet för SAP NetWeaver på SLES | Microsoft Docs
description: Guide med hög tillgänglighet för SAP NetWeaver på SUSE Linux Enterprise Server för SAP-program
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/22/2020
ms.author: radeltch
ms.openlocfilehash: e33b514f61aec69c566eae455d2e59b1a66813f6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673814"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Den här artikeln beskriver hur du distribuerar virtuella datorer, konfigurerar de virtuella datorerna, installerar kluster ramverket och installerar ett SAP NetWeaver 7,50-system med hög tillgänglighet.
I exemplen konfigurationer, installations kommandon osv. ASCS instance Number 00, ERS instance Number 02 och SAP system-ID NW1 används. Namnen på resurserna (till exempel virtuella datorer, virtuella nätverk) i exemplet förutsätter att du har använt den [konvergerade mallen][template-converged] med SAP-system-ID NW1 för att skapa resurserna.

Läs följande SAP-anteckningar och dokument först

* SAP anmärkning [1928533][1928533], som har:
  * Lista över storlekar på virtuella Azure-datorer som stöds för distribution av SAP-program
  * Viktig kapacitets information för Azure VM-storlekar
  * Stöd för SAP-program och operativ system (OS) och databas kombinationer
  * Nödvändig SAP kernel-version för Windows och Linux på Microsoft Azure

* SAP NOTE [2015553][2015553] visar krav för SAP-program distributioner som stöds i Azure.
* SAP NOTE [2205917][2205917] har rekommenderade OS-inställningar för SUSE Linux Enterprise Server för SAP-program
* SAP NOTE [1944799][1944799] har SAP HANA rikt linjer för SUSE Linux Enterprise Server för SAP-program
* SAP NOTE [2178632][2178632] innehåller detaljerad information om alla övervaknings mått som rapporter ATS för SAP i Azure.
* SAP NOTE [2191498][2191498] har den version av SAP host agent som krävs för Linux i Azure.
* SAP NOTE [2243692][2243692] innehåller information om SAP-licensiering på Linux i Azure.
* SAP NOTE [1984787][1984787] innehåller allmän information om SUSE Linux Enterprise Server 12.
* SAP anmärkning [1999351][1999351] innehåller ytterligare felsöknings information för Azure Enhanced Monitoring-tillägget för SAP.
* [SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP-anteckningar för Linux.
* [Azure Virtual Machines planera och implementera SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [Guider för SUSE SAP-ha bästa praxis][suse-ha-guide] Guiderna innehåller all information som krävs för att konfigurera NetWeaver HA och SAP HANA systemreplikering lokalt. Använd dessa guider som en allmän bas linje. De ger mycket mer detaljerad information.
* [Viktig information om SUSEt hög tillgänglighets tillägg 12 SP3][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Översikt

För att uppnå hög tillgänglighet kräver SAP NetWeaver en NFS-server. NFS-servern är konfigurerad i ett separat kluster och kan användas av flera SAP-system.

![Översikt över SAP NetWeaver-hög tillgänglighet](./media/high-availability-guide-suse/ha-suse.png)

NFS-servern, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS och SAP HANA Database använder virtuella värdnamn och virtuella IP-adresser. I Azure krävs en belastningsutjämnare för att använda en virtuell IP-adress. Vi rekommenderar att du använder [standard belastnings utjämning](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). I följande lista visas konfigurationen av (A) SCS-och ERS-belastningsutjämnaren.

### <a name="ascs"></a>En SCS

* Konfiguration av klient del
  * IP-10.0.0.7
* Avsöknings port
  * Port 620<strong> &lt; nr &gt; </strong>
* Belastnings Utjämnings regler
  * Om du använder Standard Load Balancer väljer du **ha-portar**
  * Om du använder grundläggande Load Balancer skapa belastnings Utjämnings regler för följande portar
    * 32<strong> &lt; nr &gt; </strong> TCP
    * 36<strong> &lt; nr &gt; </strong> TCP
    * 39<strong> &lt; nr &gt; </strong> TCP
    * 81<strong> &lt; nr &gt; </strong> TCP
    * 5<strong> &lt; nr &gt; </strong>13 TCP
    * 5<strong> &lt; nr &gt; </strong>14 TCP
    * 5<strong> &lt; nr &gt; </strong>16 TCP

### <a name="ers"></a>ERS

* Konfiguration av klient del
  * IP-10.0.0.8
* Avsöknings port
  * Port 621<strong> &lt; nr &gt; </strong>
* Belastnings Utjämnings regler
  * Om du använder Standard Load Balancer väljer du **ha-portar**
  * Om du använder grundläggande Load Balancer skapa belastnings Utjämnings regler för följande portar
    * 32<strong> &lt; nr &gt; </strong> TCP
    * 33<strong> &lt; nr &gt; </strong> TCP
    * 5<strong> &lt; nr &gt; </strong>13 TCP
    * 5<strong> &lt; nr &gt; </strong>14 TCP
    * 5<strong> &lt; nr &gt; </strong>16 TCP

* Server dels konfiguration
  * Anslutna till primära nätverks gränssnitt för alla virtuella datorer som ska ingå i (A) SCS/ERS-kluster


## <a name="setting-up-a-highly-available-nfs-server"></a>Konfigurera en NFS-server med hög tillgänglighet

SAP NetWeaver kräver delad lagring för transport-och profil katalogen. Läs [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server][nfs-ha] om hur du konfigurerar en NFS-server för SAP NetWeaver.

## <a name="setting-up-ascs"></a>Konfigurera (A) SCS

Du kan antingen använda en Azure-mall från GitHub för att distribuera alla nödvändiga Azure-resurser, inklusive virtuella datorer, tillgänglighets uppsättning och belastningsutjämnare, eller så kan du distribuera resurserna manuellt.

### <a name="deploy-linux-via-azure-template"></a>Distribuera Linux via Azure-mall

Azure Marketplace innehåller en avbildning för SUSE Linux Enterprise Server för SAP-program 12 som du kan använda för att distribuera nya virtuella datorer. Marketplace-avbildningen innehåller resurs agenten för SAP-NetWeaver.

Du kan använda en av snabb starts mallarna på GitHub för att distribuera alla nödvändiga resurser. Mallen distribuerar de virtuella datorerna, belastningsutjämnaren, tillgänglighets uppsättningen osv. Följ de här stegen för att distribuera mallen:

1. Öppna [mallen ASCS/SCS multi sid][template-multisid-xscs] eller [konvergerad mall][template-converged] på Azure Portal. 
   ASCS/SCS-mallen skapar bara regler för belastnings utjämning för SAP NetWeaver-ASCS/SCS-och ERS-instanser (endast Linux) medan den konvergerade mallen skapar belastnings Utjämnings regler för en databas (till exempel Microsoft SQL Server eller SAP HANA). Om du planerar att installera ett SAP NetWeaver-baserat system och du även vill installera databasen på samma datorer använder du den [konvergerade mallen][template-converged].
1. Ange följande parametrar
   1. Resource prefix (endast ASCS/SCS multi SID-mall)  
      Ange prefixet som du vill använda. Värdet används som prefix för de resurser som distribueras.
   3. SAP-system-ID (endast konvergerad mall)  
      Ange SAP-system-ID: t för det SAP-system som du vill installera. ID används som prefix för de resurser som distribueras.
   4. Typ av stack  
      Välj typ av SAP NetWeaver-stack
   5. OS-typ  
      Välj en av Linux-distributionerna. I det här exemplet väljer du SLES 12 BYOS
   6. DB-typ  
      Välj HANA
   7. SAP-systemets storlek.  
      Mängden SAPS som det nya systemet erbjuder. Om du inte är säker på hur många SAPS systemet kräver kan du fråga din SAP Technology-partner eller system integrerare
   8. System tillgänglighet  
      Välj HA
   9. Administratörens användar namn och administratörs lösen ord  
      En ny användare skapas som kan användas för att logga in på datorn.
   10. Undernät-ID  
   Om du vill distribuera den virtuella datorn till ett befintligt VNet där du har angett ett undernät som har definierats för den virtuella datorn ska du namnge ID: t för det aktuella under nätet. ID: t ser vanligt vis ut som/Subscriptions/**&lt; PRENUMERATIONS &gt; -ID**/ResourceGroups/**&lt; resurs grupp namn &gt;**/providers/Microsoft.Network/virtualNetworks/**&lt; virtuellt nätverks namn &gt;**/subnets/**&lt; under näts namn &gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Distribuera Linux manuellt via Azure Portal

Du måste först skapa de virtuella datorerna för det här NFS-klustret. Därefter skapar du en belastningsutjämnare och använder de virtuella datorerna i backend-poolen.

1. Skapa en resursgrupp
1. Skapa ett virtuellt nätverk
1. Skapa en tillgänglighets uppsättning  
   Ange Max uppdaterings domän
1. Skapa virtuell dator 1  
   Använd minst SLES4SAP 12 SP1, i det här exemplet SLES4SAP 12 SP1-avbildningen https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES för SAP-program 12 SP1 används  
   Välj den tillgänglighets uppsättning som skapades tidigare  
1. Skapa virtuell dator 2  
   Använd minst SLES4SAP 12 SP1, i det här exemplet SLES4SAP 12 SP1-avbildningen https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES för SAP-program 12 SP1 används  
   Välj den tillgänglighets uppsättning som skapades tidigare  
1. Lägg till minst en datadisk till båda virtuella datorerna  
   Data diskarna används för/usr/SAP/>- `<SAPSID` katalogen
1. Skapa belastningsutjämnare (intern, standard):  
   1. Skapa IP-adresser för klient delen
      1. IP-10.0.0.7 för ASCS
         1. Öppna belastningsutjämnaren, Välj klient delens IP-pool och klicka på Lägg till
         1. Ange namnet på den nya IP-poolen för klient delen (till exempel **NW1-ASCs-frontend**)
         1. Ange tilldelningen till statisk och ange IP-adressen (till exempel **10.0.0.7**)
         1. Klicka på OK
      1. IP-10.0.0.8 för ASCS-ERS
         * Upprepa stegen ovan för att skapa en IP-adress för ERS (till exempel **10.0.0.8** och **NW1-AERS-backend**)
   1. Skapa serverdelspoolen
      1. Öppna belastningsutjämnaren, Välj backend-pooler och klicka på Lägg till
      1. Ange namnet på den nya backend-poolen (till exempel **NW1-backend**)
      1. Klicka på Lägg till en virtuell dator.
      1. Välj virtuell dator
      1. Välj de virtuella datorerna i (A) SCS-klustret och deras IP-adresser.
      1. Klicka på Lägg till
   1. Skapa hälso avsökningar
      1. Port 620 **00** för ASCS
         1. Öppna belastningsutjämnaren, Välj hälso avsökningar och klicka på Lägg till
         1. Ange namnet på den nya hälso avsökningen (till exempel **NW1-ASCs-HP**)
         1. Välj TCP som protokoll, Port 620 **00**, Behåll intervallet 5 och tröskelvärde 2
         1. Klicka på OK
      1. Port 621 **02** för ASCS ers
         * Upprepa stegen ovan för att skapa en hälso avsökning för ERS (till exempel 621 **02** och **NW1-AERS-HP**)
   1. Belastnings Utjämnings regler
      1. Regler för belastnings utjämning för ASCS
         1. Öppna belastningsutjämnaren, Välj belastnings Utjämnings regler och klicka på Lägg till
         1. Ange namnet på den nya belastnings Utjämnings regeln (till exempel **NW1 – lb-ASCs**)
         1. Välj IP-adressen för klient delen, backend-poolen och hälso avsökningen som du skapade tidigare (till exempel **NW1-ASCs-frontend**, **NW1-backend** och **NW1-ASCs-HP**)
         1. Välj **ha-portar**
         1. Öka tids gränsen för inaktivitet till 30 minuter
         1. **Se till att aktivera flytande IP**
         1. Klicka på OK
         * Upprepa stegen ovan för att skapa belastnings Utjämnings regler för ERS (till exempel **NW1-lb-ers**)
1. Om scenariot kräver grundläggande belastningsutjämnare (internt) följer du dessa steg:  
   1. Skapa IP-adresser för klient delen
      1. IP-10.0.0.7 för ASCS
         1. Öppna belastningsutjämnaren, Välj klient delens IP-pool och klicka på Lägg till
         1. Ange namnet på den nya IP-poolen för klient delen (till exempel **NW1-ASCs-frontend**)
         1. Ange tilldelningen till statisk och ange IP-adressen (till exempel **10.0.0.7**)
         1. Klicka på OK
      1. IP-10.0.0.8 för ASCS-ERS
         * Upprepa stegen ovan för att skapa en IP-adress för ERS (till exempel **10.0.0.8** och **NW1-AERS-frontend**)
   1. Skapa serverdelspoolen
      1. Öppna belastningsutjämnaren, Välj backend-pooler och klicka på Lägg till
      1. Ange namnet på den nya backend-poolen (till exempel **NW1-backend**)
      1. Klicka på Lägg till en virtuell dator.
      1. Välj den tillgänglighets uppsättning som du skapade tidigare
      1. Välj virtuella datorer för (A) SCS-klustret
      1. Klicka på OK
   1. Skapa hälso avsökningar
      1. Port 620 **00** för ASCS
         1. Öppna belastningsutjämnaren, Välj hälso avsökningar och klicka på Lägg till
         1. Ange namnet på den nya hälso avsökningen (till exempel **NW1-ASCs-HP**)
         1. Välj TCP som protokoll, Port 620 **00**, Behåll intervallet 5 och tröskelvärde 2
         1. Klicka på OK
      1. Port 621 **02** för ASCS ers
         * Upprepa stegen ovan för att skapa en hälso avsökning för ERS (till exempel 621 **02** och **NW1-AERS-HP**)
   1. Belastnings Utjämnings regler
      1. 32 **00** TCP för ASCS
         1. Öppna belastningsutjämnaren, Välj belastnings Utjämnings regler och klicka på Lägg till
         1. Ange namnet på den nya belastnings Utjämnings regeln (till exempel **NW1 – lb-3200**)
         1. Välj IP-adressen för klient delen, backend-poolen och hälso avsökningen som du skapade tidigare (till exempel **NW1-ASCs-frontend**)
         1. Behåll protokollets **TCP**, ange port **3200**
         1. Öka tids gränsen för inaktivitet till 30 minuter
         1. **Se till att aktivera flytande IP**
         1. Klicka på OK
      1. Ytterligare portar för ASCS
         * Upprepa stegen ovan för portarna 36 **00**, 39 **00**, 81 **00**, 5 **00** 13, 5 **00 14,** 5 **00** 16 och TCP för ASCS
      1. Ytterligare portar för ASCS-ERS
         * Upprepa stegen ovan för portarna 33 **02**, 5 **02** 13, 5 **02** 14, 5 **02** 16 och TCP för ASCS-ers

> [!IMPORTANT]
> Flytande IP stöds inte på en sekundär NIC-IP-konfiguration i belastnings Utjämnings scenarier. Mer information finns i [begränsningar för Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Om du behöver ytterligare IP-adress för den virtuella datorn distribuerar du ett andra nätverkskort.  

> [!Note]
> När virtuella datorer utan offentliga IP-adresser placeras i backend-poolen för intern (ingen offentlig IP-adress) standard Azure-belastningsutjämnare, kommer det inte att finnas någon utgående Internet anslutning, om inte ytterligare konfiguration utförs för att tillåta routning till offentliga slut punkter. Mer information om hur du uppnår utgående anslutningar finns i Översikt över [offentliga slut punkter för Virtual Machines med Azure standard Load Balancer i SAP-scenarier med hög tillgänglighet](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

> [!IMPORTANT]
> Aktivera inte TCP-tidsstämplar på virtuella Azure-datorer som placerats bakom Azure Load Balancer. Om du aktiverar TCP-tidsstämplar kommer hälso avsökningarna att Miss skadas. Ange parametern **net.IPv4.tcp_timestamps** till **0**. Mer information finns i [Load Balancer hälso avsökningar](../../../load-balancer/load-balancer-custom-probe-overview.md).

### <a name="create-pacemaker-cluster"></a>Skapa pacemaker-kluster

Följ stegen i [Konfigurera pacemaker på SUSE Linux Enterprise Server i Azure](high-availability-guide-suse-pacemaker.md) för att skapa ett grundläggande pacemaker-kluster för den här (a) SCS-servern.

### <a name="installation"></a>Installation

Följande objekt har prefixet **[A]** -tillämpligt för alla noder, **[1]** , som endast gäller nod 1 eller **[2]** -gäller endast nod 2.

1. **[A]** installera SUSE Connector

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Det kända problemet med att använda ett bindestreck i värdnamn korrigeras med version **3.1.1** av paketet **SAP-SUSE-Cluster-Connector**. Kontrol lera att du använder minst version 3.1.1 av paketet SAP-SUSE-Cluster-Connector, om du använder klusternoder med bindestreck i värd namnet. Annars fungerar inte klustret. 

   Se till att du har installerat den nya versionen av SAP SUSE Cluster Connector. Den gamla har anropats sap_suse_cluster_connector och den nya kallas **SAP-SUSE-Cluster-Connector**.

   ```
   sudo zypper info sap-suse-cluster-connector
   
   Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   <b>Version        : 3.0.0-2.2</b>
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 41.6 KiB
   <b>Installed      : Yes</b>
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.0.0-2.2.src
   Summary        : SUSE High Availability Setup for SAP Products
   ```

1. **[A]** uppdatera SAP-resurs agenter  
   
   En korrigering för resurs agent paketet krävs för att använda den nya konfigurationen, som beskrivs i den här artikeln. Du kan kontrol lera om korrigerings filen redan är installerad med följande kommando

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   Utdata bör likna

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Om grep-kommandot inte hittar IS_ERS-parametern, måste du installera korrigerings filen som visas på [sidan för SUSE nedladdning](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]** namn matchning för värdnamn

   Du kan antingen använda en DNS-server eller ändra/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adress och värdnamn i följande kommandon

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Infoga följande rader i/etc/hosts. Ändra IP-adress och värdnamn för att matcha din miljö   

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Förbered för SAP NetWeaver-installation

1. **[A]** skapa delade kataloger

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>
   
   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** konfigurera autofs

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Skapa en fil med

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   </code></pre>

   Starta om autofs för att montera de nya resurserna

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** konfigurera växlings fil

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Starta om agenten för att aktivera ändringen

   <pre><code>sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Installera SAP NetWeaver ASCS/ERS

1. **[1]** skapa en virtuell IP-resurs och en hälso avsökning för ASCS-instansen

   > [!IMPORTANT]
   > De senaste testerna visade situationer, där netcat slutar svara på begär Anden på grund av en efter släpning och dess begränsning av hantering av endast en anslutning. Netcat-resursen slutar lyssna på Azure Load Balancer-begäranden och den flytande IP-adressen blir otillgänglig.  
   > För befintliga pacemaker-kluster rekommenderar vi att du tidigare ersätter netcat med socat. För närvarande rekommenderar vi att du använder Azure-lb Resource agent, som är en del av paketets resurs agenter, med följande paket versions krav:
   > - För SLES 12 SP4/SP5 måste versionen vara minst resurs agenter-4.3.018. a7fb5035-3.30.1.  
   > - För SLES 15/15 SP1 måste versionen vara minst resurs agenter-4.3.0184.6 ee15eb2-4.13.1.  
   >
   > Observera att ändringen kräver kortare stillestånds tid.  
   > För befintliga pacemaker-kluster, om konfigurationen redan har ändrats för att använda socat enligt beskrivningen i [azure Load-Balancer Avkännings härdning](https://www.suse.com/support/kb/doc/?id=7024128), finns det inget krav på att växla direkt till Azure-lb Resource agent.

   <pre><code>sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ASCS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCS' directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b>
   
   sudo crm configure group g-<b>NW1</b>_ASCS fs_<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Kontrol lera att klustrets status är OK och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

   <pre><code>sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]** installera SAP NetWeaver ASCS  

   Installera SAP NetWeaver ASCS som rot på den första noden med ett virtuellt värdnamn som mappar till IP-adressen för belastningsutjämnarens frontend-konfiguration för ASCS, till exempel <b>NW1-ASCS</b>, <b>10.0.0.7</b> och det instans nummer som du använde för avsökningen av belastningsutjämnaren, till exempel <b>00</b>.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta att en användare som inte är rot användare ansluter till sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Om installationen Miss lyckas med att skapa en undermapp i/usr/SAP/**NW1**/ASCS **00**, så försök att ange ägare och grupp för mappen ASCS **00** och försök igen.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** skapa en virtuell IP-resurs och en hälso avsökning för ers-instansen

   <pre><code>sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ERS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS' directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS azure-lb port=621<b>02</b>
   
   sudo crm configure group g-<b>NW1</b>_ERS fs_<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>

   Kontrol lera att klustrets status är OK och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

   <pre><code>sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]** installera SAP NetWeaver ers

   Installera SAP NetWeaver ERS som rot på den andra noden med ett virtuellt värdnamn som mappar till IP-adressen för belastningsutjämnarens frontend-konfiguration för ERS, till exempel <b>NW1-AERS</b>, <b>10.0.0.8</b> och det instans nummer som du använde för avsökningen av belastningsutjämnaren, till exempel <b>02</b>.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta att en användare som inte är rot användare ansluter till sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Använd SWPM SP 20 PL 05 eller högre. Lägre versioner ställer inte in behörigheterna korrekt och installationen kommer inte att fungera.

   Om installationen Miss lyckas med att skapa en undermapp i/usr/SAP/**NW1**/ers **02**, kan du försöka att ange ägare och grupp för ers **02** -mappen och försöka igen.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>


1. **[1]** anpassa ASCS/SCS och ers instance-profiler
 
   * ASCS/SCS-profil

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   </code></pre>

   Se till att `keepalive` OS-parametrarna är inställda enligt beskrivningen i SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736)för både ENSA1 och ENSA2.    

   * ERS-profil

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

1. **[A]** konfigurera Keep Alive

   Kommunikationen mellan SAP NetWeaver program Server och ASCS/SCS dirigeras via en belastningsutjämnare för program vara. Belastningsutjämnaren kopplar från inaktiva anslutningar efter en konfigurerbar tids gräns. För att förhindra detta måste du ange en parameter i SAP NetWeaver-ASCS/SCS-profilen, om du använder ENSA1 och ändra Linux `keepalive` -systeminställningarna på alla SAP-servrar för både ENSA1/ENSA2. Läs [SAP Note 1410736][1410736] för mer information.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   </code></pre>

1. **[A]** konfigurera SAP-användare efter installationen

   <pre><code># Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm
   </code></pre>

1. **[1]** Lägg till ASCS-och ers SAP-tjänsterna till sapservice-filen

   Lägg till ASCS-tjänst posten till den andra noden och kopiera ERS-tjänst posten till den första noden.

   <pre><code>cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]** skapa SAP-kluster resurser

Om du använder ENSA1 (enqueue Server 1 Architecture) definierar du resurserna på följande sätt:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  SAP introducerade stöd för att köa Server 2, inklusive replikering, från SAP NW 7,52. Från och med ABAP Platform 1809 installeras som standard Server 2. Se SAP NOTE [2630416](https://launchpad.support.sap.com/#/notes/2630416) för Server 2-stöd.
Om du använder[ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)(Queue server 2 Architecture) definierar du resurserna på följande sätt:

<pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true 
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  Om du uppgraderar från en äldre version och växlar till att köa Server 2, se SAP anmärkning [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Kontrol lera att klustrets status är OK och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.


   <pre><code>sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver Application Server-förberedelse

Vissa databaser kräver att installationen av databas instansen körs på en program Server. Förbered de virtuella datorerna för program servern så att de kan använda dem i dessa fall.

Stegen nedan förutsätter att du installerar program servern på en annan server än ASCS/SCS-och HANA-servrarna. Annars behövs inte några av stegen nedan (som att konfigurera värd namns matchning).

1. Konfigurera operativ system

   Minska storleken på den ändrade cachen. Mer information finns i [låga skriv prestanda på SLES 11/12-servrar med stort RAM-minne](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. Konfigurera värd namns matchning

   Du kan antingen använda en DNS-server eller ändra/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adress och värdnamn i följande kommandon

   ```bash
   sudo vi /etc/hosts
   ```

   Infoga följande rader i/etc/hosts. Ändra IP-adress och värdnamn för att matcha din miljö

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. Skapa sapmnt-katalogen

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Konfigurera autofs

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Skapa en ny fil med

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   Starta om autofs för att montera de nya resurserna

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. Konfigurera VÄXLINGs fil

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Starta om agenten för att aktivera ändringen

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Installera databas

I det här exemplet installeras SAP NetWeaver på SAP HANA. Du kan använda alla databaser som stöds för den här installationen. Mer information om hur du installerar SAP HANA i Azure finns i [hög tillgänglighet för SAP HANA på Azure-Virtual Machines (VM)][sap-hana-ha]. En lista över databaser som stöds finns i [SAP anmärkning 1928533][1928533].

1. Kör installationen av SAP Database instance

   Installera SAP NetWeaver Database-instansen som rot med ett virtuellt värdnamn som mappar till IP-adressen för belastningsutjämnarens frontend-konfiguration för-databasen till exempel <b>NW1-DB</b> och <b>10.0.0.13</b>.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta att en användare som inte är rot användare ansluter till sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Installation av SAP NetWeaver-program Server

Följ dessa steg om du vill installera en SAP-Programserver.

1. Förbered program Server

   Följ stegen i kapitel [SAP NetWeaver Application Server-förberedelsen](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) ovan för att förbereda program servern.

1. Installera SAP NetWeaver program Server

   Installera en primär eller ytterligare SAP NetWeaver program Server.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta att en användare som inte är rot användare ansluter till sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Uppdatera SAP HANA säker lagring

   Uppdatera den SAP HANA säkra lagringen så att den pekar på det virtuella namnet på installations programmet för SAP HANA system replikering.

   Kör följande kommando för att lista posterna
   <pre><code>hdbuserstore List
   </code></pre>

   Detta bör Visa alla poster och bör se ut ungefär så här
   <pre><code>DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>HN1</b>
   </code></pre>

   Utdata visar att IP-adressen för standard posten pekar på den virtuella datorn och inte till belastningsutjämnarens IP-adress. Posten måste ändras så att den pekar på det virtuella värd namnet för belastningsutjämnaren. Se till att använda samma port (**30313** i utdata ovan) och databas namnet (**HN1** i utdata ovan)!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db:30313@HN1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testa kluster konfigurationen

Följande tester är en kopia av test fallen i Best Practices-handböcker för SUSE. De kopieras för din bekvämlighet. Läs alltid de bästa tipsen och utför alla ytterligare tester som kan ha lagts till.

1. Testa HAGetFailoverConfig, HACheckConfig och HACheckFailoverConfig

   Kör följande kommandon som \<sapsid> adm på noden där ASCS-instansen körs för tillfället. Om kommandona inte fungerar: det finns inte tillräckligt med minne, det kan bero på att det finns bindestreck i värd namnet. Detta är ett känt problem och kommer att korrigeras av SUSE i paketet SAP-SUSE-Cluster-Connector.

   <pre><code>nw1-cl-0:nw1adm 54> sapcontrol -nr <b>00</b> -function HAGetFailoverConfig
   
   # 15.08.2018 13:50:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: Toolchain Module
   # HASAPInterfaceVersion: Toolchain Module (sap_suse_cluster_connector 3.0.1)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode:
   # HANodes: nw1-cl-0, nw1-cl-1
   
   nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -function HACheckConfig
   
   # 15.08.2018 14:00:04
   # HACheckConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   # SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   # SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   # SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   # SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   # SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   # SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   # SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   # SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   # SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (nw1-ascs_NW1_00), SAPInstance includes is-ers patch
   # SUCCESS, SAP CONFIGURATION, Enqueue replication (nw1-ascs_NW1_00), Enqueue replication enabled
   # SUCCESS, SAP STATE, Enqueue replication state (nw1-ascs_NW1_00), Enqueue replication active
   
   nw1-cl-0:nw1adm 56> sapcontrol -nr 00 -function HACheckFailoverConfig
   
   # 15.08.2018 14:04:08
   # HACheckFailoverConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

1. Migrera ASCS-instansen manuellt

   Resurs tillstånd innan du startar testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Kör följande kommandon som rot för att migrera ASCS-instansen.

   <pre><code>nw1-cl-0:~ # crm resource migrate rsc_sap_NW1_ASCS00 force
   # INFO: Move constraint created for rsc_sap_NW1_ASCS00
   
   nw1-cl-0:~ # crm resource unmigrate rsc_sap_NW1_ASCS00
   # INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resurs tillstånd efter testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Testa HAFailoverToNode

   Resurs tillstånd innan du startar testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Kör följande kommandon som \<sapsid> ADM för att MIGRERA ASCS-instansen.

   <pre><code>nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -host nw1-ascs -user nw1adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   # Remove migration constraints
   nw1-cl-0:~ # crm resource clear rsc_sap_NW1_ASCS00
   #INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   </code></pre>

   Resurs tillstånd efter testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Simulera Node-krasch

   Resurs tillstånd innan du startar testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Kör följande kommando som rot på noden där ASCS-instansen körs

   <pre><code>nw1-cl-0:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Om du använder SBD bör pacemaker inte starta automatiskt på den stoppade noden. Statusen när noden har startats igen bör se ut så här.

   <pre><code>Online: [ nw1-cl-1 ]
   OFFLINE: [ nw1-cl-0 ]
   
   Full list of resources:
   
   stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-1 'not running' (7): call=219, status=complete, exitreason='none',
       last-rc-change='Wed Aug 15 14:38:38 2018', queued=0ms, exec=0ms
   </code></pre>

   Använd följande kommandon för att starta pacemaker på den stoppade noden, rensa SBD-meddelandena och rensa de misslyckade resurserna.

   <pre><code># run as root
   # list the SBD device(s)
   nw1-cl-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   nw1-cl-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message nw1-cl-0 clear
   
   nw1-cl-0:~ # systemctl start pacemaker
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resurs tillstånd efter testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Testa Manuell omstart av ASCS-instansen

   Resurs tillstånd innan du startar testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Skapa ett köobjekt genom att t. ex. Redigera en användare i Transaction su01. Kör följande kommandon som \<sapsid> adm på den nod där ASCS-instansen körs. Kommandona stoppar ASCS-instansen och startar den igen. Om du använder en arkitektur för Server 1, förväntas det att ta bort det här testet. Om du använder Server 2 arkitektur för Server 2 kommer kön att behållas. 

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   ASCS-instansen bör nu inaktive ras i pacemaker

   <pre><code>rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Starta ASCS-instansen igen på samma nod.

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Su01 i transaktions kön bör förloras och Server delen måste ha återställts. Resurs tillstånd efter testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Stoppa meddelande Server process

   Resurs tillstånd innan du startar testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Kör följande kommandon som rot för att identifiera processen för meddelande servern och avsluta den.

   <pre><code>nw1-cl-1:~ # pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Om du bara avsluter meddelande servern en gång kommer den att startas om av sapstart. Om du tar bort det ofta räcker pacemaker att flytta ASCS-instansen till den andra noden. Kör följande kommandon som rot för att rensa resurs statusen för ASCS-och ERS-instansen efter testet.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resurs tillstånd efter testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Stoppa Server process för att stoppa kön

   Resurs tillstånd innan du startar testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Kör följande kommandon som rot på noden där ASCS-instansen körs för att avsluta servern.

   <pre><code>nw1-cl-0:~ # pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   ASCS-instansen bör omedelbart redundansväxla till den andra noden. ERS-instansen bör också redundansväxla efter att ASCS-instansen har startats. Kör följande kommandon som rot för att rensa resurs statusen för ASCS-och ERS-instansen efter testet.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resurs tillstånd efter testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Stoppa processen för att köa replikerings Server

   Resurs tillstånd innan du startar testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Kör följande kommando som rot på noden där ERS-instansen körs för att stoppa processen för att köa replikerings Server.

   <pre><code>nw1-cl-0:~ # pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Om du bara kör kommandot en gång kommer sapstart att starta om processen. Om du kör den ofta räcker det sapstart inte att starta om processen och resursen kommer att vara i ett stoppat tillstånd. Kör följande kommandon som rot för att rensa resurs statusen för ERS-instansen efter testet.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resurs tillstånd efter testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Avsluta sapstartsrv process

   Resurs tillstånd innan du startar testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Kör följande kommandon som rot på den nod där ASCS körs.

   <pre><code>nw1-cl-1:~ # pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   nw1-cl-1:~ # kill -9 59545
   </code></pre>

   Sapstartsrv-processen bör alltid startas om av pacemaker-resurs agenten. Resurs tillstånd efter testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

## <a name="next-steps"></a>Nästa steg

* [HA för SAP NW på virtuella Azure-datorer på SLES för SAP-program med flera SID-guide](./high-availability-guide-suse-multi-sid.md)
* [Azure Virtual Machines planera och implementera SAP][planning-guide]
* [Azure Virtual Machines distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Information om hur du upprättar hög tillgänglighet och planerar för haveri beredskap för SAP HANA på virtuella Azure-datorer finns i [hög tillgänglighet för SAP HANA på Azure-Virtual Machines (VM)][sap-hana-ha]