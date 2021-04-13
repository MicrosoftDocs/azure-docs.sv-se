---
title: Hög tillgänglighet för SAP HANA virtuella Azure-datorer på RHEL-| Microsoft Docs
description: Upprätta hög tillgänglighet för SAP HANA virtuella Azure-datorer (VM).
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: 3d1b05560c02f3bf4de199a3d5cad48907ee16fb
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365815"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Hög tillgänglighet för SAP HANA virtuella Azure-datorer på Red Hat Enterprise Linux

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
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

För lokal utveckling kan du antingen använda HANA-systemreplikering eller delad lagring för att upprätta hög tillgänglighet för SAP HANA.
På virtuella Azure-datorer (VM) är HANA-systemreplikering i Azure för närvarande den enda funktion för hög tillgänglighet som stöds.
SAP HANA Replikering består av en primär nod och minst en sekundär nod. Ändringar av data på den primära noden replikeras till den sekundära noden synkront eller asynkront.

Den här artikeln beskriver hur du distribuerar och konfigurerar virtuella datorer, installerar klusterramverket och installerar och konfigurerar SAP HANA systemreplikering.
I exempelkonfigurationerna används installationskommandon, **instansnummer 03** och HANA System ID **HN1.**

Läs följande SAP-anteckningar och -artiklar först:

* [SAP-anteckning 1928533], som har:
  * Listan över storlekar på virtuella Azure-datorer som stöds för distribution av SAP-programvara.
  * Viktig kapacitetsinformation för storlekar på virtuella Azure-datorer.
  * SAP-programvara som stöds, operativsystem (OS) och databaskombinationer.
  * Den sap kernel-version som krävs för Windows och Linux på Microsoft Azure.
* SAP Note [2015553 innehåller] en lista över förhandskrav för SAP-programvarudistributioner som stöds i Azure.
* SAP Note [2002167] har rekommenderade os-inställningar för Red Hat Enterprise Linux
* SAP Note [2009879] har SAP HANA Guidelines for Red Hat Enterprise Linux
* [SAP-anteckningen 2178632] innehåller detaljerad information om alla övervakningsmått som rapporterats för SAP i Azure.
* SAP Note [2191498] har den version av SAP-värdagenten som krävs för Linux i Azure.
* [SAP-anteckningen 2243692] innehåller information om SAP-licensiering på Linux i Azure.
* [SAP-anteckningen 1999351] innehåller ytterligare felsökningsinformation för Azure Enhanced Monitoring Extension för SAP.
* [SAP Community WIKI har](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) alla nödvändiga SAP-anteckningar för Linux.
* [Azure Virtual Machines planering och implementering för SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux (den här artikeln)][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [SAP HANA systemreplikering i pacemakerkluster](https://access.redhat.com/articles/3004101)
* Allmän RHEL-dokumentation
  * [Översikt över Add-On tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administration med Add-On tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referens för Add-On tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Azure-specifik RHEL-dokumentation:
  * [Supportprinciper för RHEL-kluster med hög tillgänglighet – Microsoft Azure Virtual Machines som klustermedlemmar](https://access.redhat.com/articles/3131341)
  * [Installera och konfigurera ett Red Hat Enterprise Linux 7.4 (och senare) High-Availability kluster på Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Installera SAP HANA på Red Hat Enterprise Linux för användning i Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Översikt

För att uppnå hög tillgänglighet SAP HANA installeras på två virtuella datorer. Data replikeras med hjälp av HANA-systemreplikering.

![SAP HANA översikt över hög tillgänglighet](./media/sap-hana-high-availability-rhel/ha-hana.png)

SAP HANA installation av systemreplikering använder ett dedikerat virtuellt värdnamn och virtuella IP-adresser. I Azure krävs en lastbalanserare för att använda en virtuell IP-adress. I följande lista visas konfigurationen av lastbalanseraren:

* Frontend-konfiguration: IP-adress 10.0.0.13 för hn1-db
* Serverdelskonfiguration: Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i HANA-systemreplikering
* Avsökningsport: Port 62503
* Belastningsutjämningsregler: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Distribuera för Linux

Den Azure Marketplace innehåller en avbildning Red Hat Enterprise Linux 7.4 för SAP HANA som du kan använda för att distribuera nya virtuella datorer.

### <a name="deploy-with-a-template"></a>Distribuera med en mall

Du kan använda en av snabbstartsmallarna som finns på GitHub för att distribuera alla nödvändiga resurser. Mallen distribuerar de virtuella datorerna, lastbalanseraren, tillgänglighetsuppsättningen och så vidare.
Följ dessa steg för att distribuera mallen:

1. Öppna [databasmallen][template-multisid-db] på Azure Portal.
1. Ange följande parametrar:
    * **Sap System-ID:** Ange SAP-system-ID för det SAP-system som du vill installera. ID:t används som ett prefix för de resurser som distribueras.
    * **Os-typ:** Välj en av Linux-distributionerna. I det här exemplet väljer **du RHEL 7**.
    * **Db-typ:** Välj **HANA**.
    * **Sap-systemstorlek:** Ange antalet SAPS som det nya systemet ska tillhandahålla. Om du inte är säker på hur många SAPS som systemet kräver kan du fråga din SAP Technology Partner eller System Integrator.
    * **Systemtillgänglighet:** Välj **HA**.
    * **Administratörsanvändarnamn, administratörslösenord** eller SSH-nyckel: En ny användare skapas som kan användas för att logga in på datorn.
    * **Undernäts-ID: Om** du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har ett undernät definierat som den virtuella datorn ska tilldelas, namn på ID för det specifika undernätet. ID:t ser vanligtvis **ut så här: /subscriptions/ \<subscription ID> /resourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /subnets/ \<subnet name>**. Lämna tomt om du vill skapa ett nytt virtuellt nätverk

### <a name="manual-deployment"></a>Manuell distribution

1. Skapa en resursgrupp.
1. Skapa ett virtuellt nätverk.
1. Skapa en tillgänglighetsuppsättning.  
   Ange högsta uppdateringsdomän.
1. Skapa en lastbalanserare (intern). Vi rekommenderar [standardlastbalanserare](../../../load-balancer/load-balancer-overview.md).
   * Välj det virtuella nätverk som skapades i steg 2.
1. Skapa virtuell dator 1.  
   Använd minst Red Hat Enterprise Linux 7.4 för SAP HANA. I det här exemplet används Red Hat Enterprise Linux 7.4 SAP HANA avbildningen <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Välj den tillgänglighetsuppsättning som skapades i steg 3.
1. Skapa virtuell dator 2.  
   Använd minst Red Hat Enterprise Linux 7.4 för SAP HANA. I det här exemplet används Red Hat Enterprise Linux 7.4 SAP HANA avbildningen <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Välj den tillgänglighetsuppsättning som skapades i steg 3.
1. Lägg till datadiskar.

> [!IMPORTANT]
> Flytande IP stöds inte på en sekundär IP-konfiguration för nätverkskort i scenarier för belastningsutjämning. Mer information finns i [Begränsningar för Azure Load Balancer.](../../../load-balancer/load-balancer-multivip-overview.md#limitations) Om du behöver ytterligare IP-adress för den virtuella datorn distribuerar du ett andra nätverkskort.    

> [!Note]
> När virtuella datorer utan offentliga IP-adresser placeras i serverpoolen för intern (ingen offentlig IP-adress) Standard Azure-lastbalanserare, kommer det inte att finnas någon utgående Internetanslutning, såvida inte ytterligare konfiguration utförs för att tillåta routning till offentliga slutpunkter. Mer information om hur du uppnår utgående anslutningar finns i Offentlig slutpunktsanslutning [för Virtual Machines med Azure Standard Load Balancer i SAP-scenarier med hög tillgänglighet.](./high-availability-guide-standard-load-balancer-outbound-connections.md)  

1. Om du använder en standardlastbalanserare följer du dessa konfigurationssteg:
   1. Skapa först en IP-pool på frontend-sidan:

      1. Öppna lastbalanseraren, välj **IP-pool på frontend** och välj Lägg **till**.
      1. Ange namnet på den nya IP-adresspoolen för frontend (till **exempel hana-frontend**).
      1. Ange Tilldelning **till** **Statisk och** ange IP-adressen (till exempel **10.0.0.13**).
      1. Välj **OK**.
      1. Observera poolens IP-adress när den nya IP-adresspoolen på frontend-sidan har skapats.

   1. Skapa sedan en backend-pool:

      1. Öppna lastbalanseraren, välj **backend-pooler** och välj Lägg **till**.
      1. Ange namnet på den nya backend-poolen (till exempel **hana-backend**).
      1. Välj **Lägg till en virtuell dator.**
      1. Välj ** Virtuell dator**.
      1. Välj de virtuella datorerna i SAP HANA och deras IP-adresser.
      1. Välj **Lägg till**.

   1. Skapa sedan en hälsoavsökning:

      1. Öppna lastbalanseraren, välj **hälsoavsökningar och** välj Lägg **till**.
      1. Ange namnet på den nya hälsoavsökningen (till exempel **hana-hp**).
      1. Välj **TCP** som protokoll och port 625 **03.** Behåll värdet **Intervall** inställt på 5 och **tröskelvärdet Inte feltillstånd** inställt på 2.
      1. Välj **OK**.

   1. Skapa sedan reglerna för belastningsutjämning:
   
      1. Öppna lastbalanseraren, välj **lastbalanseringsregler och** välj Lägg **till**.
      1. Ange namnet på den nya lastbalanseringsregeln (till exempel **hana-lb**).
      1. Välj IP-adressen för frontend, backend-poolen och hälsoavsökningen som du skapade tidigare (till exempel **hana-frontend**, **hana-backend** och **hana-hp**).
      1. Välj **HA-portar.**
      1. Öka **tidsgränsen för inaktivitet** till 30 minuter.
      1. Se till att **aktivera flytande IP.**
      1. Välj **OK**.


1. Om ditt scenario kräver att du använder en grundläggande lastbalanserare kan du också följa dessa konfigurationssteg:
   1. Konfigurera lastbalanseraren. Skapa först en IP-adresspool på frontend-sidan:

      1. Öppna lastbalanseraren, välj **IP-pool på frontend-sidan** och välj Lägg **till**.
      1. Ange namnet på den nya IP-adresspoolen på frontend-sidan (till **exempel hana-frontend**).
      1. Ställ in **Tilldelning** på **Statisk** och ange IP-adressen (till exempel **10.0.0.13**).
      1. Välj **OK**.
      1. När den nya IP-adresspoolen på frontend-sidan har skapats noterar du poolens IP-adress.

   1. Skapa sedan en backend-pool:

      1. Öppna lastbalanseraren, välj **backend-pooler** och välj Lägg **till**.
      1. Ange namnet på den nya backend-poolen (till exempel **hana-backend**).
      1. Välj **Lägg till en virtuell dator.**
      1. Välj den tillgänglighetsuppsättning som skapades i steg 3.
      1. Välj de virtuella datorerna i SAP HANA klustret.
      1. Välj **OK**.

   1. Skapa sedan en hälsoavsökning:

      1. Öppna lastbalanseraren, välj **hälsoavsökningar och** välj Lägg **till**.
      1. Ange namnet på den nya hälsoavsökningen (till exempel **hana-hp**).
      1. Välj **TCP** som protokoll och port 625 **03.** Behåll värdet **Intervall** inställt på 5 och **tröskelvärdet Inte feltillstånd** inställt på 2.
      1. Välj **OK**.

   1. Skapa SAP HANA 1.0 för belastningsutjämning:

      1. Öppna lastbalanseraren, välj **lastbalanseringsregler och** välj Lägg **till**.
      1. Ange namnet på den nya lastbalanseringsregeln (till exempel hana-lb-3 **03** 15).
      1. Välj IP-adressen för frontend, backend-poolen och hälsoavsökningen som du skapade tidigare (till exempel **hana-frontend**).
      1. Behåll Protokollet **inställt** på **TCP** och ange port 3 **03** 15.
      1. Öka **tidsgränsen för inaktivitet** till 30 minuter.
      1. Se till att **aktivera flytande IP.**
      1. Välj **OK**.
      1. Upprepa dessa steg för port 3 **03** 17.

   1. För SAP HANA 2.0 skapar du belastningsutjämningsregler för systemdatabasen:

      1. Öppna lastbalanseraren, välj **lastbalanseringsregler och** välj Lägg **till**.
      1. Ange namnet på den nya lastbalanseringsregeln (till exempel hana-lb-3 **03** 13).
      1. Välj IP-adressen för frontend, backend-poolen och hälsoavsökningen som du skapade tidigare (till exempel **hana-frontend**).
      1. Behåll Protokollet **inställt** på **TCP** och ange port 3 **03** 13.
      1. Öka **tidsgränsen för inaktivitet** till 30 minuter.
      1. Se till att **aktivera flytande IP.**
      1. Välj **OK**.
      1. Upprepa dessa steg för port 3 **03** 14.

   1. För SAP HANA 2.0 skapar du först belastningsutjämningsregler för klientdatabasen:

      1. Öppna lastbalanseraren, välj **lastbalanseringsregler och** välj Lägg **till**.
      1. Ange namnet på den nya lastbalanseringsregeln (till exempel hana-lb-3 **03** 40).
      1. Välj den IP-adress för frontend, backend-pool och hälsoavsökning som du skapade tidigare (till exempel **hana-frontend**).
      1. Behåll Protokollet **inställt** på **TCP** och ange port 3 **03** 40.
      1. Öka **tidsgränsen för inaktivitet** till 30 minuter.
      1. Se till att **aktivera flytande IP.**
      1. Välj **OK**.
      1. Upprepa dessa steg för portarna **3 03** 41 och 3 **03** 42.

Mer information om de portar som krävs för [](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) SAP HANA finns i kapitlet Anslutningar till klientdatabaser i [guiden för SAP HANA-klientdatabaser](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) eller [SAP-anteckningen 2388694.][2388694]

> [!IMPORTANT]
> Aktivera inte TCP-tidsstämplar på virtuella Azure-datorer som placerats bakom Azure Load Balancer. Om du aktiverar TCP-tidsstämplar misslyckas hälsoavsökningarna. Ange **parametern net.ipv4.tcp_timestamps** **till 0**. Mer information finns [i Load Balancer hälsoavsökningar](../../../load-balancer/load-balancer-custom-probe-overview.md).
> Se även [SAP-anteckningen 2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="install-sap-hana"></a>Installera SAP HANA

Stegen i det här avsnittet använder följande prefix:

* **[A]**: Steget gäller för alla noder.
* **[1]**: Steget gäller endast nod 1.
* **[2]**: Steget gäller endast för nod 2 i pacemakerklustret.

1. **[A]** Konfigurera disklayouten: **Logical Volume Manager (LVM)**.

   Vi rekommenderar att du använder LVM för volymer som lagrar data och loggfiler. I följande exempel förutsätts att de virtuella datorerna har fyra anslutna datadiskar som används för att skapa två volymer.

   Lista alla tillgängliga diskar:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Exempel på utdata:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Skapa fysiska volymer för alla diskar som du vill använda:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Skapa en volymgrupp för datafilerna. Använd en volymgrupp för loggfilerna och en för den delade katalogen i SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Skapa de logiska volymerna. En linjär volym skapas när du använder `lvcreate` utan `-i` växeln. Vi rekommenderar att du skapar en stripe-volym för bättre I/O-prestanda och justerar stripe-storlekarna till de värden som beskrivs [i SAP HANA vm-lagringskonfigurationer](./hana-vm-operations-storage.md). Argumentet `-i` ska vara antalet underliggande fysiska volymer och argumentet är `-I` stripe-storleken. I det här dokumentet används två fysiska volymer för datavolymen, så `-i` switchargumentet är inställt på **2**. Stripe-storleken för datavolymen är **256KiB**. En fysisk volym används för loggvolymen, så inga `-i` växlar eller växlar används uttryckligen för `-I` loggvolymkommandona.  

   > [!IMPORTANT]
   > Använd växeln och ställ in den på antalet underliggande fysiska volymer när du använder mer än en fysisk volym för `-i` varje data, logg eller delade volymer. Använd `-I` växeln för att ange stripe-storleken när du skapar en stripe-volym.  
   > Se [SAP HANA vm-lagringskonfigurationer](./hana-vm-operations-storage.md) för rekommenderade lagringskonfigurationer, inklusive stripe-storlekar och antal diskar.  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Skapa monteringskatalogerna och kopiera UUID för alla logiska volymer:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Skapa `fstab` poster för de tre logiska volymerna:

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Infoga följande rad i `/etc/fstab` filen:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Montera de nya volymerna:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Konfigurera disklayouten: **Plain Disks**.

   För demosystem kan du placera dina HANA-data och loggfiler på en disk. Skapa en partition på /dev/disk/azure/scsi1/lun0 och formatera den med xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Infoga den här raden i filen /etc/fstab:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Skapa målkatalogen och montera disken:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** Konfigurera värdnamnsmatchning för alla värdar.

   Du kan antingen använda en DNS-server eller ändra filen /etc/hosts på alla noder. Det här exemplet visar hur du använder filen /etc/hosts.
   Ersätt IP-adressen och värdnamnet i följande kommandon:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Infoga följande rader i filen /etc/hosts. Ändra IP-adressen och värdnamnet så att de matchar din miljö:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** RHEL för HANA-konfiguration

   Konfigurera RHEL enligt beskrivningen <https://access.redhat.com/solutions/2447641> i och i följande SAP-anteckningar:  
   - [2292690 – SAP HANA DB: Rekommenderade OS-inställningar för RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 – SAP HANA DB: Rekommenderade OS-inställningar för RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 – Linux: Köra SAP-program som kompilerats med GCC 6.x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 – Linux: Köra SAP-program som kompilerats med GCC 7.x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 – Linux: Köra SAP-program som kompilerats med GCC 9.x](https://launchpad.support.sap.com/#/notes/2886607)

1. **[A]** Installera SAP HANA

   Om du SAP HANA systemreplikering följer du <https://access.redhat.com/articles/3004101> .

   * Kör **hdblcm-programmet** från HANA-DVD:n. Ange följande värden i prompten:
   * Välj installation: Ange **1**.
   * Välj ytterligare komponenter för installation: Ange **1**.
   * Ange Installationssökväg [/hana/shared]: Välj Retur.
   * Ange Lokalt värdnamn [..]: Välj Retur.
   * Vill du lägga till ytterligare värdar i systemet? (j/n) [n]: Välj Retur.
   * Ange SAP HANA system-ID: Ange SID för HANA, till exempel: **HN1**.
   * Ange Instansnummer [00]: Ange HANA-instansnumret. Ange **03** om du använde Azure-mallen eller följde avsnittet om manuell distribution i den här artikeln.
   * Välj Databasläge/Ange Index [1]: Välj Retur.
   * Välj Systemanvändning/Ange Index [4]: Välj systemanvändningsvärdet.
   * Ange Plats för datavolymer [/hana/data/HN1]: Välj Retur.
   * Ange plats för loggvolymer [/hana/log/HN1]: Välj Retur.
   * Begränsa maximal minnesallokering? [n]: Välj Retur.
   * Ange certifikatets värdnamn som värd "..."" –: Välj Retur.
   * Ange lösenord för SAP-värdagentanvändare (sapadm): Ange användarlösenordet för värdagenten.
   * Bekräfta lösenord för SAP-värdagentanvändare (sapadm): Ange värdagentens användarlösenord igen för att bekräfta.
   * Ange systemadministratörslösenord (hdbadm): Ange systemadministratörens lösenord.
   * Bekräfta systemadministratörslösenordet (hdbadm): Ange systemadministratörens lösenord igen för att bekräfta.
   * Ange Systemadministratörens hemkatalog [/usr/sap/HN1/home]: Välj Retur.
   * Ange Inloggningsgränssnitt för systemadministratör [/bin/sh]: Välj Retur.
   * Ange användar-ID för systemadministratör [1001]: Välj Retur.
   * Ange ID för användargruppen (sapsys) [79]: Välj Retur.
   * Ange lösenord för databasanvändare (SYSTEM): Ange databasanvändarlösenordet.
   * Bekräfta lösenord för databasanvändare (SYSTEM): Ange databasanvändarlösenordet igen för att bekräfta.
   * Vill du starta om systemet efter omstarten av datorn? [n]: Välj Retur.
   * Vill du fortsätta? (j/n): Validera sammanfattningen. Ange **y för** att fortsätta.

1. **[A]** Uppgradera SAP-värdagenten.

   Ladda ned det senaste SAP-värdagentarkivet från [SAP Software Center][sap-swcenter] och kör följande kommando för att uppgradera agenten. Ersätt sökvägen till arkivet så att den pekar på filen som du laddade ned:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

1. **[A] Konfigurera** brandväggen

   Skapa brandväggsregeln för avsökningsporten för Azure Load Balancer.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurera SAP HANA 2.0-systemreplikering

Stegen i det här avsnittet använder följande prefix:

* **[A]**: Steget gäller för alla noder.
* **[1]**: Steget gäller endast nod 1.
* **[2]**: Steget gäller endast för nod 2 i pacemakerklustret.

1. **[A] Konfigurera** brandväggen

   Skapa brandväggsregler för att tillåta HANA-systemreplikering och klienttrafik. De portar som krävs visas på [TCP/IP-portar för alla SAP-produkter.](https://help.sap.com/viewer/ports) Följande kommandon är bara ett exempel för att tillåta HANA 2.0-systemreplikering och klienttrafik till databasen SYSTEMDB, HN1 och NW1.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** Skapa klientdatabasen.

   Om du använder en SAP HANA 2.0 eller MDC skapar du en klientdatabas för ditt SAP NetWeaver-system. Ersätt **NW1** med SID för ditt SAP-system.

   Kör följande <hanasid \> adm:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1] Konfigurera** systemreplikering på den första noden:

   Säkerhetskopiera databaserna som <hanasid \> adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Kopiera systemets PKI-filer till den sekundära platsen:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Skapa den primära platsen:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2] Konfigurera** systemreplikering på den andra noden:
    
   Registrera den andra noden för att starta systemreplikeringen. Kör följande kommando som <hanasid \> adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1] Kontrollera** replikeringsstatus

   Kontrollera replikeringsstatusen och vänta tills alla databaser är synkroniserade. Om statusen är OKÄND kontrollerar du brandväggsinställningarna.

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurera SAP HANA 1.0-systemreplikering

Stegen i det här avsnittet använder följande prefix:

* **[A]**: Steget gäller för alla noder.
* **[1]**: Steget gäller endast nod 1.
* **[2]**: Steget gäller endast för nod 2 i pacemakerklustret.

1. **[A] Konfigurera** brandväggen

   Skapa brandväggsregler som tillåter HANA-systemreplikering och klienttrafik. De portar som krävs visas på [TCP/IP-portar för alla SAP-produkter.](https://help.sap.com/viewer/ports) Följande kommandon är bara ett exempel på hur du tillåter systemreplikering i HANA 2.0. Anpassa det till din SAP HANA 1.0-installation.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]** Skapa de användare som krävs.

   Kör följande kommando som rot. Ersätt fet sträng (HANA System ID **HN1** och instansnummer **03**) med värdena för SAP HANA installation:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** Skapa nyckellagringsposten.

   Kör följande kommando som rot för att skapa en ny nyckellagringspost:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. [1] Back up the database **.[1]** Back up the database.

   Back up the databases as root(2006 2006 2012

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Om du använder en installation med flera klientorganisationen ska du även databasen för klientorganisationen:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1] Konfigurera** systemreplikering på den första noden.

   Skapa den primära platsen som <hanasid \> adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2] Konfigurera** systemreplikering på den sekundära noden.

   Registrera den sekundära platsen som <hanasid \> adm:

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Skapa ett pacemakerkluster

Följ stegen i Konfigurera [pacemaker på en Red Hat Enterprise Linux Azure](high-availability-guide-rhel-pacemaker.md) för att skapa ett grundläggande pacemakerkluster för den här HANA-servern.

## <a name="implement-the-python-system-replication-hook-saphanasr"></a>Implementera Python-systemreplikerings hooken SAPHanaSR

Det här är ett viktigt steg för att optimera integreringen med klustret och förbättra identifieringen när ett kluster redundans krävs. Vi rekommenderar starkt att du konfigurerar PYTHON-hooken SAPHanaSR.    

1. **[A]** Installera HANA "systemreplikerings hook". Hooken måste installeras på båda HANA DB-noderna.           

   > [!TIP]
   > Python-hooken kan bara implementeras för HANA 2.0.        

   1. Förbered hooken som `root` .  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/srHook/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Stoppa HANA på båda noderna. Kör som <sid \> adm:  
   
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. Justera `global.ini` på varje klusternod.  
 
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[A]** Klustret kräver sudoers-konfiguration på varje klusternod för <sid \> adm. I det här exemplet uppnås detta genom att skapa en ny fil. Kör kommandona som `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # Needed for SAPHanaSR python hook
    hn1adm ALL=(ALL) NOPASSWD: /usr/sbin/crm_attribute -n hana_hn1_site_srHook_*
    EOF
    ```

3. **[A]** Starta SAP HANA på båda noderna. Kör som <sid \> adm.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1] Kontrollera** hook-installationen. Kör som <sid \> adm på replikeringsplatsen för det aktiva HANA-systemet.   

    ```bash
     cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*
     # Example output
     # 2021-04-12 21:36:16.911343 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:36:29.147808 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:37:04.898680 ha_dr_SAPHanaSR SOK

    ```

Mer information om implementeringen av replikerings-SAP HANA finns i [Aktivera SAP HA/DR-providern hook](https://access.redhat.com/articles/3004101#enable-srhook).  
 
## <a name="create-sap-hana-cluster-resources"></a>Skapa SAP HANA klusterresurser

Installera SAP HANA resursagenter på **alla noder**. Se till att aktivera en lagringsplats som innehåller paketet. Du behöver inte aktivera ytterligare lagringsplatsen om du använder RHEL 8.x HA-aktiverad avbildning.  

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Skapa sedan HANA-topologin. Kör följande kommandon på en av pacemakerklusternoderna:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Skapa sedan HANA-resurserna.

> [!NOTE]
> Den här artikeln innehåller referenser till termen *slave*, en term som Microsoft inte längre använder. När termen tas bort från programvaran tar vi bort den från den här artikeln.

Om du skapar ett kluster **på RHEL 7.x** använder du följande kommandon:  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Om du skapar ett kluster **på RHEL 8.x** använder du följande kommandon:  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
promotable meta notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-clone symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-clone 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Kontrollera att klusterstatusen är ok och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

> [!NOTE]
> Tidsgränserna i konfigurationen ovan är bara exempel och kan behöva anpassas till den specifika HANA-konfigurationen. Du kan till exempel behöva öka tidsgränsen för start om det tar längre tid att starta SAP HANA databasen.  

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>


## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Konfigurera aktiv/läsaktiverad systemreplikering i Pacemaker-kluster i HANA

Från och med SAP HANA 2.0 SPS 01 tillåter SAP aktiva/läsaktiverade konfigurationer för SAP HANA System Replication, där de sekundära systemen för SAP HANA-systemreplikering kan användas aktivt för läsintensiva arbetsbelastningar. För att stödja sådan konfiguration i ett kluster krävs en andra virtuell IP-adress som ger klienter åtkomst till den sekundära SAP HANA databasen. För att säkerställa att den sekundära replikeringsplatsen fortfarande kan nås efter ett övertagande måste klustret flytta runt den virtuella IP-adressen med den sekundära för SAPHana-resursen.

I det här avsnittet beskrivs de ytterligare steg som krävs för att hantera aktiv/läs-aktiverad systemreplikering i ett Red Hat-kluster med hög tillgänglighet med andra virtuella IP-adresser.    

Innan du fortsätter kontrollerar du att du har konfigurerat red hat-kluster med hög tillgänglighet som hanterar SAP HANA databas enligt beskrivningen i ovanstående avsnitt i dokumentationen.  

![SAP HANA hög tillgänglighet med läsaktiverad sekundär](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>Ytterligare konfiguration i Azure Load Balancer för aktiv/läsaktiverad konfiguration

Om du vill fortsätta med ytterligare steg för att etablera en andra virtuell IP-adress kontrollerar du att du har konfigurerat Azure Load Balancer enligt beskrivningen i [avsnittet Manuell](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#manual-deployment) distribution.

1. För  standardlastbalanserare följer du nedanstående ytterligare steg för samma lastbalanserare som du skapade i föregående avsnitt.

   a. Skapa en andra IP-adresspool på frontend-sidan: 

   - Öppna lastbalanseraren, välj **IP-pool på frontend** och välj Lägg **till**.
   - Ange namnet på den andra IP-adresspoolen på frontend-sidan (till **exempel hana-secondaryIP**).
   - Ange Tilldelning **till** **Statisk och** ange IP-adressen (till exempel **10.0.0.14**).
   - Välj **OK**.
   - Observera poolens IP-adress när den nya IP-adresspoolen på frontend-sidan har skapats.

   b. Skapa sedan en hälsoavsökning:

   - Öppna lastbalanseraren, välj **hälsoavsökningar och** välj Lägg **till**.
   - Ange namnet på den nya hälsoavsökningen (till exempel **hana-secondaryhp**).
   - Välj **TCP** som protokoll och port **62603.** Behåll värdet **Intervall** inställt på 5 och **tröskelvärdet Inte feltillstånd** inställt på 2.
   - Välj **OK**.

   c. Skapa sedan reglerna för belastningsutjämning:

   - Öppna lastbalanseraren, välj **lastbalanseringsregler och** välj Lägg **till**.
   - Ange namnet på den nya lastbalanseringsregeln (till exempel **hana-secondarylb**).
   - Välj IP-adressen för frontend, backend-poolen och hälsoavsökningen som du skapade tidigare (till exempel **hana-secondaryIP,** **hana-backend** och **hana-secondaryhp**).
   - Välj **HA-portar.**
   - Se till att **aktivera flytande IP.**
   - Välj **OK**.

### <a name="configure-hana-activeread-enabled-system-replication"></a>Konfigurera aktiv/läsaktiverad systemreplikering i HANA

Stegen för att konfigurera HANA-systemreplikering beskrivs i [avsnittet Konfigurera SAP HANA 2.0 System Replication.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-sap-hana-20-system-replication) Om du distribuerar ett läsaktiverad sekundärt scenario när du konfigurerar systemreplikering på den andra noden kör du följande kommando **som hanasid** adm:

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2 --operationMode=logreplay_readaccess 
```

### <a name="adding-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>Lägga till en sekundär virtuell IP-adressresurs för en aktiv/läsaktiverad konfiguration

Den andra virtuella IP-adressen och lämplig samlokaliseringsbegränsning kan konfigureras med följande kommandon:

```
pcs property set maintenance-mode=true

pcs resource create secvip_HN1_03 ocf:heartbeat:IPaddr2 ip="10.40.0.16"

pcs resource create secnc_HN1_03 ocf:heartbeat:azure-lb port=62603

pcs resource group add g_secip_HN1_03 secnc_HN1_03 secvip_HN1_03

RHEL 8.x: 
pcs constraint colocation add g_secip_HN1_03 with slave SAPHana_HN1_03-clone 4000
RHEL 7.x:
pcs constraint colocation add g_secip_HN1_03 with slave SAPHana_HN1_03-master 4000

pcs property set maintenance-mode=false
```
Kontrollera att klusterstatusen är ok och att alla resurser har startats. Den andra virtuella IP-adressen körs på den sekundära platsen tillsammans med den sekundära SAPHana-resursen.

```
sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full List of Resources:
#   rsc_hdb_azr_agt     (stonith:fence_azure_arm):      Started hn1-db-0
#   Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]:
#     Started: [ hn1-db-0 hn1-db-1 ]
#   Clone Set: SAPHana_HN1_03-clone [SAPHana_HN1_03] (promotable):
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
#   Resource Group: g_ip_HN1_03:
#     nc_HN1_03         (ocf::heartbeat:azure-lb):      Started hn1-db-0
#     vip_HN1_03        (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#   Resource Group: g_secip_HN1_03:
#     secnc_HN1_03      (ocf::heartbeat:azure-lb):      Started hn1-db-1
#     secvip_HN1_03     (ocf::heartbeat:IPaddr2):       Started hn1-db-1
```

I nästa avsnitt hittar du den typiska uppsättningen redundanstester som ska köras.

Tänk på det andra virtuella IP-beteendet när du testar ett HANA-kluster som konfigurerats med läsaktiverad sekundär:

1. När du **SAPHana_HN1_HDB03** till **hn1-db-1** flyttas den andra virtuella IP-adressen till den andra servern **hn1-db-0**. Om du har konfigurerat AUTOMATED_REGISTER="false" och HANA-systemreplikering inte registreras automatiskt, körs den andra virtuella **IP-adressen på hn1-db-0,** eftersom servern är tillgänglig och klustertjänsterna är online.  

2. När servern testas kraschar, körs de andra virtuella IP-resurserna **(rsc_secip_HN1_HDB03)** och Azure Load Balancer-portresursen **(rsc_secnc_HN1_HDB03)** på den primära servern tillsammans med de primära virtuella IP-resurserna.  När den sekundära servern är nere ansluter de program som är anslutna till den skrivskyddade HANA-databasen till den primära HANA-databasen. Beteendet är förväntat eftersom du inte vill att program som är anslutna till den läsaktiverade HANA-databasen ska vara otillgängliga medan den sekundära servern inte är tillgänglig.

3. När den sekundära servern är tillgänglig och klustertjänsterna är online flyttas automatiskt den andra virtuella IP-adressen och portresurserna till den sekundära servern, även om HANA-systemreplikering kanske inte registreras som sekundär. Du måste se till att du registrerar den sekundära HANA-databasen som läsaktiverad innan du startar klustertjänster på den servern. Du kan konfigurera HANA-instansklusterresursen så att den automatiskt registrerar den sekundära genom att ange parametern AUTOMATED_REGISTER=true.
   
4. Under redundans och återställning kan befintliga anslutningar för program som använder den andra virtuella IP-adressen för att ansluta till HANA-databasen avbrytas.  

## <a name="test-the-cluster-setup"></a>Testa klusterkonfigurationen

I det här avsnittet beskrivs hur du kan testa konfigurationen. Innan du startar ett test kontrollerar du att Pacemaker inte har någon misslyckad åtgärd (via pcs-status), att det inte finns några oväntade platsbegränsningar (till exempel rester av ett migreringstest) och att HANA är synkroniseringstillstånd, till exempel med systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Testa migreringen

Resurstillstånd innan du startar testet:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Du kan migrera SAP HANA huvudnoden genom att köra följande kommando:

<pre><code># On RHEL <b>7.x</b> 
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-clone --master
</code></pre>

Om du anger ska det här kommandot migrera SAP HANA huvudnoden och gruppen som innehåller den virtuella `AUTOMATED_REGISTER="false"` IP-adressen till hn1-db-1.

När migreringen är klar ser "sudo pcs status"-utdata ut så här

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Den SAP HANA resursen på hn1-db-0 har stoppats. I det här fallet konfigurerar du HANA-instansen som sekundär genom att köra följande kommando:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

Migreringen skapar platsbegränsningar som måste tas bort igen:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Övervaka tillståndet för HANA-resursen med hjälp av "pcs status". När HANA har startats på hn1-db-0 bör utdata se ut så här

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Testa Azure-avstängningsagenten

> [!NOTE]
> Den här artikeln innehåller referenser till termen *"slave",* en term som Microsoft inte längre använder. När termen tas bort från programvaran tar vi bort den från den här artikeln.  

Resurstillstånd innan du startar testet:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Du kan testa konfigurationen av Azure-avstängningsagenten genom att inaktivera nätverksgränssnittet på noden där SAP HANA körs som huvudnod.
En [beskrivning av hur du simulerar ett nätverksfel finns i Red Hat Knowledgebase-artikel 79523.](https://access.redhat.com/solutions/79523) I det här exemplet använder vi net_breaker för att blockera all åtkomst till nätverket.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

Den virtuella datorn bör nu starta om eller stoppa beroende på klusterkonfigurationen.
Om du ställer `stonith-action` in inställningen på av stoppas den virtuella datorn och resurserna migreras till den virtuella dator som körs.

När du startar den virtuella datorn igen kan SAP HANA inte starta som sekundär om du anger `AUTOMATED_REGISTER="false"` . I det här fallet konfigurerar du HANA-instansen som sekundär genom att köra följande kommando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

Resurstillstånd efter testet:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Testa en manuell redundans

Resurstillstånd innan du startar testet:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Du kan testa en manuell redundans genom att stoppa klustret på noden hn1-db-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Efter redundansen kan du starta klustret igen. Om du anger `AUTOMATED_REGISTER="false"` kan SAP HANA på noden hn1-db-0 inte starta som sekundär. I det här fallet konfigurerar du HANA-instansen som sekundär genom att köra följande kommando:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

Resurstillstånd efter testet:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-a-manual-failover"></a>Testa en manuell redundans

Resurstillstånd innan du startar testet:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Du kan testa en manuell redundans genom att stoppa klustret på noden hn1-db-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>


## <a name="next-steps"></a>Nästa steg

* [Azure Virtual Machines planering och implementering för SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* [SAP HANA vm-lagringskonfigurationer](./hana-vm-operations-storage.md)