---
title: Hög tillgänglighet för SAP HANA virtuella Azure-datorer på SLES-| Microsoft Docs
description: Hög tillgänglighet för SAP HANA virtuella Azure-datorer på SUSE Linux Enterprise Server
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
ms.openlocfilehash: ea1296fd4e31c2deaed79e980ab764c523a2bfd7
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364370"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Hög tillgänglighet för SAP HANA virtuella Azure-datorer på SUSE Linux Enterprise Server

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
[401162]:https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

För lokal utveckling kan du antingen använda HANA-systemreplikering eller delad lagring för att upprätta hög tillgänglighet för SAP HANA.
På virtuella Azure-datorer (VM) är HANA-systemreplikering i Azure för närvarande den enda funktion för hög tillgänglighet som stöds. SAP HANA Replikering består av en primär nod och minst en sekundär nod. Ändringar av data på den primära noden replikeras till den sekundära noden synkront eller asynkront.

Den här artikeln beskriver hur du distribuerar och konfigurerar virtuella datorer, installerar klusterramverket och installerar och konfigurerar SAP HANA systemreplikering.
I exempelkonfigurationerna används installationskommandon, **instansnummer 03** och HANA System ID **HN1.**

Läs följande SAP-anteckningar och -artiklar först:

* [SAP-anteckning 1928533], som har:
  * Listan över storlekar på virtuella Azure-datorer som stöds för distribution av SAP-programvara.
  * Viktig kapacitetsinformation för storlekar på virtuella Azure-datorer.
  * SAP-programvara som stöds, operativsystem (OS) och databaskombinationer.
  * Den sap kernel-version som krävs för Windows och Linux på Microsoft Azure.
* [SAP-anteckningen 2015553] innehåller en lista över förhandskrav för SAP-programvarudistributioner som stöds i Azure.
* SAP Note [2205917] har rekommenderade os-inställningar för SUSE Linux Enterprise Server för SAP-program.
* SAP Note [1944799] has SAP HANA Guidelines for SUSE Linux Enterprise Server for SAP Applications.
* [SAP-anteckningen 2178632] innehåller detaljerad information om alla övervakningsmått som rapporteras för SAP i Azure.
* SAP Note [2191498] har den version av SAP-värdagenten som krävs för Linux i Azure.
* SAP Note [2243692 innehåller] information om SAP-licensiering på Linux i Azure.
* [SAP-anteckningen 1984787] innehåller allmän information om SUSE Linux Enterprise Server 12.
* SAP Note [1999351] innehåller ytterligare felsökningsinformation för Azure Enhanced Monitoring Extension for SAP.
* [SAP-anteckning 401162] innehåller information om hur du undviker "adress som redan används" när du ställer in HANA-systemreplikering.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP-anteckningar för Linux.
* [SAP HANA certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Azure Virtual Machines planering och implementering för SAP på Linux.][planning-guide]
* [Azure Virtual Machines för SAP på Linux][deployment-guide] (den här artikeln).
* [Guide Virtual Machines för Azure Virtual Machines DBMS-distribution för SAP på Linux.][dbms-guide]
* [SUSE Linux Enterprise Server för guider med metodtips för SAP Applications 12 SP3][sles-for-sap-bp]
  * Konfigurera en SAP HANA SR Performance Optimized Infrastructure (SLES för SAP Applications 12 SP1). Guiden innehåller all information som krävs för att konfigurera SAP HANA systemreplikering för lokal utveckling. Använd den här guiden som baslinje.
  * Konfigurera en SAP HANA SR Cost Optimized Infrastructure (SLES för SAP Applications 12 SP1)

## <a name="overview"></a>Översikt

För att uppnå hög tillgänglighet SAP HANA installeras på två virtuella datorer. Data replikeras med hjälp av HANA-systemreplikering.

![SAP HANA översikt över hög tillgänglighet](./media/sap-hana-high-availability/ha-suse-hana.png)

SAP HANA för systemreplikering använder ett dedikerat virtuellt värdnamn och virtuella IP-adresser. I Azure krävs en lastbalanserare för att använda en virtuell IP-adress. I följande lista visas konfigurationen av lastbalanseraren:

* Frontend-konfiguration: IP-adress 10.0.0.13 för hn1-db
* Serverdelskonfiguration: Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i HANA-systemreplikering
* Avsökningsport: Port 62503
* Belastningsutjämningsregler: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Distribuera för Linux

Resursagenten för SAP HANA ingår i SUSE Linux Enterprise Server för SAP-program.
Den Azure Marketplace innehåller en avbildning SUSE Linux Enterprise Server för SAP Applications 12 som du kan använda för att distribuera nya virtuella datorer.

### <a name="deploy-with-a-template"></a>Distribuera med en mall

Du kan använda en av snabbstartsmallarna som finns på GitHub för att distribuera alla nödvändiga resurser. Mallen distribuerar de virtuella datorerna, lastbalanseraren, tillgänglighetsuppsättningen och så vidare.
Följ dessa steg för att distribuera mallen:

1. Öppna [databasmallen][template-multisid-db] eller [den konvergerade mallen][template-converged] på Azure Portal. 
    Databasmallen skapar belastningsutjämningsregler endast för en databas. Den konvergerade mallen skapar också belastningsutjämningsregler för en ASCS/SCS- och ERS-instans (endast Linux). Om du planerar att installera ett SAP NetWeaver-baserat system och du vill installera ASCS/SCS-instansen på samma datorer använder du den [konvergerade mallen][template-converged].

1. Ange följande parametrar:
    - **Sap System-ID:** Ange SAP-system-ID för det SAP-system som du vill installera. ID:t används som ett prefix för de resurser som distribueras.
    - **Stacktyp:**(Den här parametern gäller endast om du använder den konvergerade mallen.) Välj stacktypen SAP NetWeaver.
    - **Os-typ:** Välj en av Linux-distributionerna. I det här exemplet väljer **du SLES 12**.
    - **Db-typ:** Välj **HANA**.
    - **Sap-systemstorlek:** Ange antalet SAPS som det nya systemet ska tillhandahålla. Om du inte är säker på hur många SAPS som systemet kräver kan du fråga din SAP-teknikpartner eller systemintegratör.
    - **Systemtillgänglighet:** Välj **HA**.
    - **Administratörsanvändarnamn och** administratörslösenord: En ny användare skapas som kan användas för att logga in på datorn.
    - **Nytt eller befintligt undernät:** Avgör om ett nytt virtuellt nätverk och undernät ska skapas eller om ett befintligt undernät ska användas. Om du redan har ett virtuellt nätverk som är anslutet till ditt lokala nätverk väljer du **Befintlig.**
    - **Undernäts-ID: Om** du vill distribuera den virtuella datorn till ett befintligt VNet där du har ett undernät definierat som den virtuella datorn ska tilldelas, namn på ID för det specifika undernätet. ID:t ser vanligtvis **ut så här: /subscriptions/ \<subscription ID> /resourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /subnets/ \<subnet name>**.

### <a name="manual-deployment"></a>Manuell distribution

> [!IMPORTANT]
> Kontrollera att det operativsystem som du väljer är SAP-certifierat för SAP HANA på de specifika VM-typer som du använder. Listan över SAP HANA certifierade TYPER av virtuella datorer och operativsystemsutgåningar för dessa finns i [SAP HANA Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Se till att klicka på informationen för den VM-typ som visas för att få en fullständig lista över SAP HANA operativsystems versioner som stöds för den specifika VM-typen
>  

1. Skapa en resursgrupp.
1. Skapa ett virtuellt nätverk.
1. Skapa en tillgänglighetsuppsättning.
   - Ange högsta uppdateringsdomän.
1. Skapa en lastbalanserare (intern). Vi rekommenderar [standardlastbalanserare](../../../load-balancer/load-balancer-overview.md).
   - Välj det virtuella nätverk som skapades i steg 2.
1. Skapa virtuell dator 1.
   - Använd en SLES4SAP-avbildning i Azure-galleriet som stöds för SAP HANA på den VM-typ som du har valt.
   - Välj den tillgänglighetsuppsättning som skapades i steg 3.
1. Skapa virtuell dator 2.
   - Använd en SLES4SAP-avbildning i Azure-galleriet som stöds för SAP HANA på den VM-typ som du har valt.
   - Välj den tillgänglighetsuppsättning som skapades i steg 3. 
1. Lägg till datadiskar.

> [!IMPORTANT]
> Flytande IP stöds inte på en sekundär IP-konfiguration för nätverkskort i scenarier med belastningsutjämning. Mer information finns i [Begränsningar för Azure Load Balancer.](../../../load-balancer/load-balancer-multivip-overview.md#limitations) Om du behöver ytterligare IP-adress för den virtuella datorn distribuerar du ett andra nätverkskort.   

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
      1. Välj **Virtual Network**.
      1. Välj **Lägg till en virtuell dator.**
      1. Välj ** Virtuell dator**.
      1. Välj de virtuella datorerna i SAP HANA och deras IP-adresser.
      1. Välj **Lägg till**.
   
   1. Skapa sedan en hälsoavsökning:
   
      1. Öppna lastbalanseraren, välj **hälsoavsökningar och** välj Lägg **till**.
      1. Ange namnet på den nya hälsoavsökningen (till exempel **hana-hp**).
      1. Välj **TCP** som protokoll och port 625 **03.** Behåll värdet **Intervall** inställt på 5 och **tröskelvärdet Inte feltillstånd** inställt på 2.
      1. Välj **OK**.
   
   1. Skapa sedan belastningsutjämningsregler:
   
      1. Öppna lastbalanseraren, välj **lastbalanseringsregler och** välj Lägg **till**.
      1. Ange namnet på den nya lastbalanseringsregeln (till exempel **hana-lb**).
      1. Välj IP-adressen för frontend, backend-poolen och hälsoavsökningen som du skapade tidigare (till exempel **hana-frontend**, **hana-backend** **och hana-hp**).
      1. Välj **HA-portar.**
      1. Se till att **aktivera flytande IP.**
      1. Välj **OK**.

1. Om ditt scenario kräver att du använder en grundläggande lastbalanserare kan du också följa dessa konfigurationssteg:
   1. Skapa först en IP-adresspool på frontend-sidan:
   
      1. Öppna lastbalanseraren, välj **IP-pool på frontend-sidan** och välj Lägg **till**.
      1. Ange namnet på den nya IP-adresspoolen på frontend-sidan (till **exempel hana-frontend**).
      1. Ange Tilldelning **till** **Statisk och** ange IP-adressen (till exempel **10.0.0.13**).
      1. Välj **OK**.
      1. Observera poolens IP-adress när den nya IP-adresspoolen på frontend-sidan har skapats.
   
   1. Skapa sedan en backend-pool:
   
      1. Öppna lastbalanseraren, välj **backend-pooler** och välj Lägg **till**.
      1. Ange namnet på den nya backend-poolen (till exempel **hana-backend**).
      1. Välj **Lägg till en virtuell dator.**
      1. Välj den tillgänglighetsuppsättning som skapades i steg 3.
      1. Välj de virtuella datorerna SAP HANA klustret.
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
      1. Välj frontend-IP-adressen, backend-poolen och hälsoavsökningen som du skapade tidigare (till exempel **hana-frontend**).
      1. Behåll Protokollet **inställt** på **TCP** och ange port 3 **03** 13.
      1. Öka **tidsgränsen för inaktivitet** till 30 minuter.
      1. Se till att **aktivera flytande IP.**
      1. Välj **OK**.
      1. Upprepa dessa steg för port 3 **03** 14.
   
   1. För SAP HANA 2.0 skapar du först belastningsutjämningsregler för klientdatabasen:
   
      1. Öppna lastbalanseraren, välj **lastbalanseringsregler och** välj Lägg **till**.
      1. Ange namnet på den nya lastbalanseringsregeln (till exempel hana-lb-3 **03** 40).
      1. Välj ip-adressen för frontend, backend-poolen och hälsoavsökningen som du skapade tidigare (till exempel **hana-frontend**).
      1. Behåll Protokollet **inställt** på **TCP** och ange port 3 **03** 40.
      1. Öka **tidsgränsen för inaktivitet** till 30 minuter.
      1. Se till att **aktivera flytande IP.**
      1. Välj **OK**.
      1. Upprepa dessa steg för portarna **3 03** 41 och 3 **03** 42.

   Mer information om de portar som krävs för [](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) SAP HANA finns i kapitlet Anslutningar till klientdatabaser i [guiden för SAP HANA-klientdatabaser](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) eller [SAP-anteckningen 2388694.][2388694]

> [!IMPORTANT]
> Aktivera inte TCP-tidsstämplar på virtuella Azure-datorer som placerats bakom Azure Load Balancer. Om du aktiverar TCP-tidsstämplar misslyckas hälsoavsökningarna. Ange **parametern net.ipv4.tcp_timestamps** **till 0**. Mer information finns [i Load Balancer hälsoavsökningar](../../../load-balancer/load-balancer-custom-probe-overview.md).
> Se även [SAP-anteckningen 2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="create-a-pacemaker-cluster"></a>Skapa ett pacemakerkluster

Följ stegen i Konfigurera [pacemaker på en SUSE Linux Enterprise Server Azure](high-availability-guide-suse-pacemaker.md) för att skapa ett grundläggande pacemakerkluster för den här HANA-servern. Du kan använda samma Pacemaker-kluster för SAP HANA SAP NetWeaver (A)SCS.

## <a name="install-sap-hana"></a>Installera SAP HANA

Stegen i det här avsnittet använder följande prefix:
- **[A]**: Steget gäller för alla noder.
- **[1]**: Steget gäller endast för nod 1.
- **[2]**: Steget gäller endast för nod 2 i pacemakerklustret.

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

   Skapa de logiska volymerna. En linjär volym skapas när du använder `lvcreate` utan `-i` växeln. Vi rekommenderar att du skapar en stripe-volym för bättre I/O-prestanda och justerar stripe-storlekarna till de värden som beskrivs [i SAP HANA vm-lagringskonfigurationer](./hana-vm-operations-storage.md). Argumentet `-i` ska vara antalet underliggande fysiska volymer och argumentet är `-I` stripe-storleken. I det här dokumentet används två fysiska volymer för datavolymen, så `-i` växelargumentet är inställt på **2**. Stripe-storleken för datavolymen är **256KiB**. En fysisk volym används för loggvolymen, så inga `-i` växlar eller växlar används explicit för `-I` loggvolymkommandona.  

   > [!IMPORTANT]
   > Använd växeln och ställ in den på antalet underliggande fysiska volymer när du använder mer än en fysisk volym för varje `-i` data, logg eller delade volymer. Använd `-I` växeln för att ange stripe-storleken när du skapar en stripe-volym.  
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

1. **[A]** Installera SAP HANA paket med hög tillgänglighet:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Om du SAP HANA systemreplikering följer du kapitel 4 SAP HANA guiden för [SR-prestandaoptimerade scenarier.](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/)

1. **[A]** Kör **hdblcm-programmet** från HANA-DVD:n. Ange följande värden i prompten:
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
   * Bekräfta lösenordet för SAP-värdagentanvändaren (sapadm): Ange värdagentens användarlösenord igen för att bekräfta.
   * Ange lösenord för systemadministratör (hdbadm): Ange systemadministratörens lösenord.
   * Bekräfta systemadministratörslösenordet (hdbadm): Ange systemadministratörens lösenord igen för att bekräfta.
   * Ange Systemadministratörens hemkatalog [/usr/sap/HN1/home]: Välj Retur.
   * Ange systemadministratörens inloggningsgränssnitt [/bin/sh]: Välj Retur.
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

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurera SAP HANA 2.0-systemreplikering

Stegen i det här avsnittet använder följande prefix:

* **[A]**: Steget gäller för alla noder.
* **[1]**: Steget gäller endast för nod 1.
* **[2]**: Steget gäller endast för nod 2 i pacemakerklustret.

1. **[1]** Skapa klientdatabasen.

   Om du använder en SAP HANA 2.0 eller MDC skapar du en klientdatabas för ditt SAP NetWeaver-system. Ersätt **NW1** med SID för ditt SAP-system.

   Kör följande kommando som <hanasid \> adm :

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1] Konfigurera** systemreplikering på den första noden:

   Back up the databases as <hanasid \> adm:

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
    
   Registrera den andra noden för att starta systemreplikeringen. Kör följande kommando som <hanasid \> adm :

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurera SAP HANA 1.0-systemreplikering

Stegen i det här avsnittet använder följande prefix:

* **[A]**: Steget gäller för alla noder.
* **[1]**: Steget gäller endast för nod 1.
* **[2]**: Steget gäller endast för nod 2 i pacemakerklustret.

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

   Öka databaserna som rot:

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

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="implement-the-python-system-replication-hook-saphanasr"></a>Implementera SapHanaSR för Python-systemreplikering

Det här är ett viktigt steg för att optimera integreringen med klustret och förbättra identifieringen när ett kluster redundans krävs. Vi rekommenderar starkt att du konfigurerar PYTHON-hooken SAPHanaSR.    

1. **[A]** Installera HANA "system replication hook". Hooken måste installeras på båda HANA DB-noderna.           

   > [!TIP]
   > Kontrollera att paketet SAPHanaSR är minst version 0.153 för att kunna använda python-hookfunktionen i SAPHanaSR.       
   > Python-hooken kan bara implementeras för HANA 2.0.        

   1. Förbered hooken som `root` .  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/SAPHanaSR.py /hana/shared/myHooks
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
Mer information om implementeringen av replikeringsuppsättningen för SAP HANA finns i [Konfigurera HANA HA/DR-providers.](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-sr-guide-PerfOpt-12/index.html#_set_up_sap_hana_hadr_providers)  

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
     # 2021-04-08 22:18:15.877583 ha_dr_SAPHanaSR SFAIL
     # 2021-04-08 22:18:46.531564 ha_dr_SAPHanaSR SFAIL
     # 2021-04-08 22:21:26.816573 ha_dr_SAPHanaSR SOK

    ```

## <a name="create-sap-hana-cluster-resources"></a>Skapa SAP HANA klusterresurser

Skapa först HANA-topologin. Kör följande kommandon på en av noderna i pacemakerklustret:

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Skapa sedan HANA-resurserna:

> [!IMPORTANT]
> Den senaste testningen visade situationer där netcat slutar svara på begäranden på grund av förfrågningsloggar och dess begränsning av hantering av endast en anslutning. Netcat-resursen slutar att lyssna på Azure Load Balancer-begäranden och den flytande IP-adressen blir otillgänglig.  
> För befintliga pacemakerkluster rekommenderar vi att du tidigare ersätter netcat med socat. För närvarande rekommenderar vi att du använder azure-lb-resursagenten, som är en del av paketresursagenter, med följande paketversionskrav:
> - För SLES 12 SP4/SP5 måste versionen minst vara resource-agents-4.3.018.a7fb5035-3.30.1.  
> - För SLES 15/15 SP1 måste versionen minst vara resource-agents-4.3.0184.6ee15eb2-4.13.1.  
>
> Observera att ändringen kräver kort stilleståndstid.  
> För befintliga Pacemaker-kluster, om konfigurationen redan har ändrats för att använda socat enligt beskrivningen i [Azure Load-Balancer Detection Hardening](https://www.suse.com/support/kb/doc/?id=7024128), finns det inget krav på att omedelbart växla till azure-lb resursagenten.


> [!NOTE]
> Den här artikeln innehåller referenser till *termerna master* *och slave*, termer som Microsoft inte längre använder. När dessa villkor tas bort från programvaran tar vi bort dem från den här artikeln.

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
  operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
  op start interval="0" timeout="3600" \
  op stop interval="0" timeout="3600" \
  op promote interval="0" timeout="3600" \
  op monitor interval="60" role="Master" timeout="700" \
  op monitor interval="61" role="Slave" timeout="700" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
  DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
  meta notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> azure-lb port=625<b>03</b> \
  meta resource-stickiness=0

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

Kontrollera att klusterstatusen är ok och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
</code></pre>

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Konfigurera aktiv/läsaktiverad systemreplikering i Pacemaker-kluster i HANA

Från och med SAP HANA 2.0 SPS 01 tillåter SAP aktiv/läsaktiverad konfiguration för SAP HANA System Replication, där de sekundära systemen för SAP HANA-systemreplikering kan användas aktivt för läsintensiva arbetsbelastningar. För att stödja sådan konfiguration i ett kluster krävs en andra virtuell IP-adress som ger klienter åtkomst till den sekundära SAP HANA databasen. För att säkerställa att den sekundära replikeringsplatsen fortfarande kan nås efter ett övertagande måste klustret flytta runt den virtuella IP-adressen med den sekundära för SAPHana-resursen.

I det här avsnittet beskrivs de ytterligare steg som krävs för att hantera aktiv/läs-aktiverad systemreplikering i ett SUSE-kluster med hög tillgänglighet med andra virtuella IP-adresser.    
Innan du fortsätter kontrollerar du att du har konfigurerat SUSE-kluster med hög tillgänglighet som hanterar SAP HANA databasen enligt beskrivningen i ovanstående segment i dokumentationen.  

![SAP HANA hög tillgänglighet med läsaktiverad sekundär](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>Ytterligare konfiguration i Azure Load Balancer för aktiv/läsaktiverad konfiguration

Om du vill fortsätta med ytterligare steg för att etablera en andra virtuell IP-adress kontrollerar du att du har konfigurerat Azure Load Balancer enligt beskrivningen i [avsnittet Manuell](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability#manual-deployment) distribution.

1. För  standardlastbalanserare följer du de ytterligare stegen nedan på samma lastbalanserare som du skapade i föregående avsnitt.

   a. Skapa en andra IP-adresspool på frontend-sidan: 

   - Öppna lastbalanseraren, välj **IP-pool på frontend** och välj Lägg **till**.
   - Ange namnet på den andra IP-adresspoolen på frontend-sidan (till **exempel hana-secondaryIP**).
   - Ange Tilldelning **till** **Statisk och** ange IP-adressen (till exempel **10.0.0.14**).
   - Välj **OK**.
   - När den nya IP-adresspoolen för frontend har skapats noterar du IP-adressen för frontend.

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
   - Öka **tidsgränsen för inaktivitet** till 30 minuter.
   - Se till att **aktivera flytande IP.**
   - Välj **OK**.

### <a name="configure-hana-activeread-enabled-system-replication"></a>Konfigurera aktiv/läsaktiverad systemreplikering i HANA

Stegen för att konfigurera HANA-systemreplikering beskrivs [i avsnittet Konfigurera SAP HANA 2.0-systemreplikering.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability#configure-sap-hana-20-system-replication) Om du distribuerar ett läsaktiverad sekundärt scenario när du konfigurerar systemreplikering på den andra noden kör du följande kommando **som hanasid** adm:

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2 --operationMode=logreplay_readaccess 
```

### <a name="adding-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>Lägga till en sekundär virtuell IP-adressresurs för en aktiv/läsaktiverad konfiguration

Den andra virtuella IP-adressen och lämplig samlokaliseringsbegränsning kan konfigureras med följande kommandon:

```
crm configure property maintenance-mode=true

crm configure primitive rsc_secip_HN1_HDB03 ocf:heartbeat:IPaddr2 \
 meta target-role="Started" \
 operations \$id="rsc_secip_HN1_HDB03-operations" \
 op monitor interval="10s" timeout="20s" \
 params ip="10.0.0.14"

crm configure primitive rsc_secnc_HN1_HDB03 azure-lb port=62603 \
 meta resource-stickiness=0

crm configure group g_secip_HN1_HDB03 rsc_secip_HN1_HDB03 rsc_secnc_HN1_HDB03

crm configure colocation col_saphana_secip_HN1_HDB03 4000: g_secip_HN1_HDB03:Started \
 msl_SAPHana_HN1_HDB03:Slave 

crm configure property maintenance-mode=false
```
Kontrollera att klusterstatusen är ok och att alla resurser har startats. Den andra virtuella IP-adressen körs på den sekundära platsen tillsammans med den sekundära SAPHana-resursen.

```
sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
# Resource Group: g_secip_HN1_HDB03:
#     rsc_secip_HN1_HDB03       (ocf::heartbeat:IPaddr2):        Started hn1-db-1
#     rsc_secnc_HN1_HDB03       (ocf::heartbeat:azure-lb):       Started hn1-db-1

```

I nästa avsnitt hittar du den typiska uppsättningen redundanstester som ska köras.

Tänk på det andra virtuella IP-beteendet när du testar ett HANA-kluster som konfigurerats med läsaktiverad sekundär:

1. När du **SAPHana_HN1_HDB03** till **hn1-db-1** flyttas den andra virtuella IP-adressen till den andra servern **hn1-db-0**. Om du har konfigurerat AUTOMATED_REGISTER="false" och HANA-systemreplikering inte registreras automatiskt, körs den andra virtuella **IP-adressen på hn1-db-0,** eftersom servern är tillgänglig och klustertjänsterna är online.  

2. När du testar en serverkrasch körs de andra virtuella IP-resurserna **(rsc_secip_HN1_HDB03)** och Azure Load Balancer-portresursen **(rsc_secnc_HN1_HDB03)** på den primära servern tillsammans med de primära virtuella IP-resurserna. När den sekundära servern är nere ansluter de program som är anslutna till den läsaktiverade HANA-databasen till den primära HANA-databasen. Beteendet är förväntat eftersom du inte vill att program som är anslutna till den läsaktiverade HANA-databasen ska vara otillgängliga medan den sekundära servern inte är tillgänglig.
  
3. När den sekundära servern är tillgänglig och klustertjänsterna är online flyttas den andra virtuella IP-adressen och portresurserna automatiskt till den sekundära servern, även om HANA-systemreplikering kanske inte registreras som sekundär. Du måste se till att du registrerar den sekundära HANA-databasen som läsaktiverad innan du startar klustertjänster på den servern. Du kan konfigurera HANA-instansklusterresursen så att den automatiskt registrerar den sekundära genom att ange parametern AUTOMATED_REGISTER=true.       

4. Under redundans och återställning kan befintliga anslutningar för program som använder den andra virtuella IP-adressen för att ansluta till HANA-databasen avbrytas.  

## <a name="test-the-cluster-setup"></a>Testa klusterkonfigurationen

I det här avsnittet beskrivs hur du kan testa konfigurationen. Varje test förutsätter att du är rot och SAP HANA master körs på den virtuella **datorn hn1-db-0.**

### <a name="test-the-migration"></a>Testa migreringen

Innan du startar testet kontrollerar du att Pacemaker inte har någon misslyckad åtgärd (via crm_mon -r), att det inte finns några oväntade platsbegränsningar (till exempel rester av ett migreringstest) och att HANA är synkroniseringstillstånd, till exempel med SAPHanaSR-showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr
Sites    srHook
----------------
SITE2    SOK

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

Du kan migrera SAP HANA huvudnoden genom att köra följande kommando:

<pre><code>crm resource move msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b> force
</code></pre>

Om du anger ska den här sekvensen med kommandon migrera SAP HANA huvudnoden och gruppen som innehåller den virtuella `AUTOMATED_REGISTER="false"` IP-adressen till hn1-db-1.

När migreringen är klar ser utdata crm_mon -r ut så här

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

Den SAP HANA resursen på hn1-db-0 kan inte starta som sekundär. I det här fallet konfigurerar du HANA-instansen som sekundär genom att köra följande kommando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

Migreringen skapar platsbegränsningar som måste tas bort igen:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource clear msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Du måste också rensa tillståndet för den sekundära nodresursen:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Övervaka tillståndet för HANA-resursen med hjälp crm_mon -r. När HANA har startats på hn1-db-0 bör utdata se ut så här

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Testa Azure-avstängningsagenten (inte SBD)

Du kan testa konfigurationen av Azure-stängselagenten genom att inaktivera nätverksgränssnittet på noden hn1-db-0:

<pre><code>sudo ifdown eth0
</code></pre>

Den virtuella datorn bör nu startas om eller stoppas beroende på klusterkonfigurationen.
Om du ställer `stonith-action` in inställningen på av stoppas den virtuella datorn och resurserna migreras till den virtuella dator som körs.

När du startar den virtuella datorn igen kan SAP HANA inte starta som sekundär om du anger `AUTOMATED_REGISTER="false"` . I det här fallet konfigurerar du HANA-instansen som sekundär genom att köra följande kommando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Testa SBD-avstängning

Du kan testa konfigurationen av SBD genom att ta bort inkvisitor-processen.

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

Klusternoden hn1-db-0 bör startas om. Pacemakertjänsten kanske inte kommer igång efteråt. Se till att starta det igen.

### <a name="test-a-manual-failover"></a>Testa en manuell redundans

Du kan testa en manuell redundans genom att stoppa `pacemaker` tjänsten på noden hn1-db-0:

<pre><code>service pacemaker stop
</code></pre>

Efter redundansen kan du starta tjänsten igen. Om du anger `AUTOMATED_REGISTER="false"` kan SAP HANA på noden hn1-db-0 inte starta som sekundär. I det här fallet konfigurerar du HANA-instansen som sekundär genom att köra följande kommando:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>SUSE-tester

> [!IMPORTANT]
> Kontrollera att det operativsystem som du väljer är SAP-certifierat för SAP HANA på de specifika VM-typer som du använder. Listan över SAP HANA certifierade TYPER av virtuella datorer och operativsystemsutgåningar för dessa finns i [SAP HANA Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Se till att klicka på informationen för den VM-typ som visas för att få en fullständig lista över SAP HANA operativsystems versioner som stöds för den specifika VM-typen

Kör alla testfall som anges i guiden SAP HANA SR Performance Optimized Scenario eller SAP HANA SR Cost Optimized Scenario, beroende på ditt användningsfall. Du hittar guiderna på sidan [med metodtips för SLES för SAP.][sles-for-sap-bp]

Följande tester är en kopia av testbeskrivningarna för guiden SAP HANA SR Performance Optimized Scenario SUSE Linux Enterprise Server for SAP Applications 12 SP1. Om du vill ha en uppdaterad version bör du alltid läsa guiden. Kontrollera alltid att HANA är synkroniserat innan du startar testet och se även till att pacemakerkonfigurationen är korrekt.

I följande testbeskrivningar antar vi att PREFER_SITE_TAKEOVER="true" och AUTOMATED_REGISTER="false".
Obs! Följande tester är utformade för att köras i följd och beror på avslutstillståndet för föregående tester.

1. TEST 1: STOPPA PRIMÄR DATABAS PÅ NOD 1

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som <hanasid \> adm på noden hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker bör identifiera den stoppade HANA-instansen och redundans till den andra noden. När redundansen är klar stoppas HANA-instansen på noden hn1-db-0 eftersom Pacemaker inte registrerar noden automatiskt som HANA sekundär.

   Kör följande kommandon för att registrera noden hn1-db-0 som sekundär och rensa den misslyckade resursen.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 2: STOPPA PRIMÄR DATABAS PÅ NOD 2

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Kör följande kommandon som <hanasid \> adm på noden hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker bör identifiera den stoppade HANA-instansen och redundans till den andra noden. När redundansen är klar stoppas HANA-instansen på noden hn1-db-1 eftersom Pacemaker inte registrerar noden automatiskt som HANA sekundär.

   Kör följande kommandon för att registrera noden hn1-db-1 som sekundär och rensa den misslyckade resursen.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 3: KRASCHA PRIMÄR DATABAS PÅ NOD

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som <hanasid \> adm på noden hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Pacemaker bör identifiera den avlivade HANA-instansen och redundans till den andra noden. När redundansen är klar stoppas HANA-instansen på noden hn1-db-0 eftersom Pacemaker inte registrerar noden automatiskt som HANA sekundär.

   Kör följande kommandon för att registrera noden hn1-db-0 som sekundär och rensa den misslyckade resursen.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 4: KRASCH PRIMÄR DATABAS PÅ NOD 2

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Kör följande kommandon som <hanasid \> adm på noden hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker bör identifiera den avlivade HANA-instansen och redundans till den andra noden. När redundansen är klar stoppas HANA-instansen på noden hn1-db-1 eftersom Pacemaker inte registrerar noden automatiskt som HANA sekundär.

   Kör följande kommandon för att registrera noden hn1-db-1 som sekundär och rensa den misslyckade resursen.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 5: KRASCHNOD FÖR PRIMÄR PLATS (NOD 1)

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som rot på noden hn1-db-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker bör identifiera den avlivade klusternoden och stängs av noden. När noden är inhägnad utlöser Pacemaker ett övertagande av HANA-instansen. När den inhäglade noden startas om startar inte Pacemaker automatiskt.

   Kör följande kommandon för att starta Pacemaker, rensa SBD-meddelandena för noden hn1-db-0, registrera noden hn1-db-0 som sekundär och rensa den misslyckade resursen.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 6: KRASCH SEKUNDÄR PLATSNOD (NOD 2)

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Kör följande kommandon som rot på noden hn1-db-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker bör identifiera den avlivade klusternoden och stängs av noden. När noden är inhägnad utlöser Pacemaker ett övertagande av HANA-instansen. När den inhäglade noden startas om startar pacemakern inte automatiskt.

   Kör följande kommandon för att starta Pacemaker, rensa SBD-meddelanden för noden hn1-db-1, registrera noden hn1-db-1 som sekundär och rensa den misslyckade resursen.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 7: STOPPA DEN SEKUNDÄRA DATABASEN PÅ NOD 2

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som <hanasid \> adm på noden hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker identifierar den stoppade HANA-instansen och markerar resursen som misslyckad på noden hn1-db-1. Pacemaker bör starta om HANA-instansen automatiskt. Kör följande kommando för att rensa det misslyckade tillståndet.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 8: KRASCHA DEN SEKUNDÄRA DATABASEN PÅ NOD 2

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som <hanasid \> adm på noden hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker identifierar den avlivade HANA-instansen och markerar resursen som misslyckad på noden hn1-db-1. Kör följande kommando för att rensa det misslyckade tillståndet. Pacemaker bör sedan starta om HANA-instansen automatiskt.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 9: KRASCH SEKUNDÄR PLATSNOD (NOD 2) SOM KÖR SEKUNDÄR HANA-DATABAS

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som rot på noden hn1-db-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pacemaker bör identifiera den avlivade klusternoden och stängs av noden. När den inhäglade noden startas om startar inte Pacemaker automatiskt.

   Kör följande kommandon för att starta Pacemaker, rensa SBD-meddelandena för noden hn1-db-1 och rensa den misslyckade resursen.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Nästa steg

* [Azure Virtual Machines planering och implementering för SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
