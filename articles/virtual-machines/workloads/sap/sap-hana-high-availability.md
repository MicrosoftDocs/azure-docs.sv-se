---
title: Hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SLES | Microsoft Docs
description: Hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SUSE Linux Enterprise Server
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2021
ms.author: radeltch
ms.openlocfilehash: 42a4c4a41f6c8bdf9d4a8e78f634893722c8f389
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104576413"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SUSE Linux Enterprise Server

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

För lokal utveckling kan du använda antingen HANA-systemreplikering eller använda delad lagring för att upprätta hög tillgänglighet för SAP HANA.
På Azure Virtual Machines (VM) är HANA-systemreplikeringen på Azure för närvarande den enda funktion för hög tillgänglighet som stöds. SAP HANA replikeringen består av en primär nod och minst en sekundär nod. Ändringar av data på den primära noden replikeras till den sekundära noden synkront eller asynkront.

I den här artikeln beskrivs hur du distribuerar och konfigurerar de virtuella datorerna, installerar kluster ramverket och installerar och konfigurerar SAP HANA system replikering.
I exempel konfigurationerna används installations kommandon, instans nummer **03** och Hana system-ID **HN1** .

Läs följande SAP-anteckningar och dokument först:

* SAP anmärkning [1928533], som har:
  * Listan med virtuella Azure-storlekar som stöds för distribution av SAP-program.
  * Viktig kapacitets information för storlekar på virtuella Azure-datorer.
  * De SAP-program och-operativ system och databas kombinationer som stöds.
  * Den nödvändiga SAP kernel-versionen för Windows och Linux på Microsoft Azure.
* SAP NOTE [2015553] listar kraven för SAP-program distributioner som stöds i Azure.
* SAP NOTE [2205917] har rekommenderade OS-inställningar för SUSE Linux Enterprise Server för SAP-program.
* SAP NOTE [1944799] har SAP HANA rikt linjer för SUSE Linux Enterprise Server för SAP-program.
* SAP NOTE [2178632] innehåller detaljerad information om alla övervaknings mått som rapporteras för SAP i Azure.
* SAP NOTE [2191498] har den version av SAP host agent som krävs för Linux i Azure.
* SAP NOTE [2243692] innehåller information om SAP-licensiering på Linux i Azure.
* SAP NOTE [1984787] innehåller allmän information om SUSE Linux Enterprise Server 12.
* SAP anmärkning [1999351] innehåller ytterligare felsöknings information för Azure Enhanced Monitoring-tillägget för SAP.
* SAP anmärkning [401162] innehåller information om hur du undviker "adressen som redan används" när du konfigurerar Hana-systemreplikering.
* [SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla de SAP-anteckningar som krävs för Linux.
* [SAP HANA certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Azure Virtual Machines planera och implementera för SAP på Linux][planning-guide] -guiden.
* [Azure Virtual Machines-distribution för SAP på Linux][deployment-guide] (den här artikeln).
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide] -guide.
* [SUSE Linux Enterprise Server för SAP-program 12 SP3 guider för bästa praxis][sles-for-sap-bp]
  * Konfigurera en SAP HANA SR Performance-optimerad infrastruktur (SLES för SAP-program 12 SP1). Guiden innehåller all information som krävs för att konfigurera SAP HANA system replikering för lokal utveckling. Använd den här guiden som en bas linje.
  * Konfigurera en SAP HANA SR-kostnads optimerad infrastruktur (SLES för SAP-program 12 SP1)

## <a name="overview"></a>Översikt

SAP HANA installeras på två virtuella datorer för att uppnå hög tillgänglighet. Data replikeras med hjälp av HANA-systemreplikering.

![Översikt över SAP HANA hög tillgänglighet](./media/sap-hana-high-availability/ha-suse-hana.png)

Installations programmet för SAP HANA system replikering använder ett dedikerat virtuellt värdnamn och virtuella IP-adresser. I Azure krävs en belastningsutjämnare för att använda en virtuell IP-adress. I följande lista visas belastnings Utjämnings konfigurationen:

* Konfiguration av klient sidan: IP-10.0.0.13 för HN1-DB
* Konfiguration på Server sidan: anslutna till primära nätverks gränssnitt för alla virtuella datorer som ska ingå i HANA-systemreplikering
* Avsöknings port: port 62503
* Belastnings Utjämnings regler: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Distribuera för Linux

Resurs agenten för SAP HANA ingår i SUSE Linux Enterprise Server för SAP-program.
Azure Marketplace innehåller en avbildning för SUSE Linux Enterprise Server för SAP-program 12 som du kan använda för att distribuera nya virtuella datorer.

### <a name="deploy-with-a-template"></a>Distribuera med en mall

Du kan använda en av snabb starts mallarna som finns på GitHub för att distribuera alla nödvändiga resurser. Mallen distribuerar de virtuella datorerna, belastningsutjämnaren, tillgänglighets uppsättningen och så vidare.
Följ dessa steg om du vill distribuera mallen:

1. Öppna [databas mal len][template-multisid-db] eller den [konvergerade mallen][template-converged] på Azure Portal. 
    Databas mal len skapar belastnings Utjämnings regler för en databas. Den konvergerade mallen skapar också regler för belastnings utjämning för en instans av ASCS/SCS och ERS (Linux). Om du planerar att installera ett SAP NetWeaver-baserat system och du vill installera ASCS/SCS-instansen på samma datorer använder du den [konvergerade mallen][template-converged].

1. Ange följande parametrar:
    - **SAP-system-ID**: Ange SAP-system-ID: t för det SAP-system som du vill installera. ID används som prefix för de resurser som distribueras.
    - **Typ av stack**: (den här parametern kan bara användas om du använder konvergerad mall.) Välj typ av SAP NetWeaver-stack.
    - **OS-typ**: Välj en av Linux-distributionerna. I det här exemplet väljer du **SLES 12**.
    - **Db-typ**: Välj **Hana**.
    - **SAP-system storlek**: Ange antalet SAPS som det nya systemet ska tillhandahålla. Om du inte är säker på hur många SAPS systemet kräver kan du fråga din SAP Technology-partner eller system integrerare.
    - **System tillgänglighet**: Välj **ha**.
    - **Administratörens användar namn och administratörs lösen ord**: en ny användare skapas som kan användas för att logga in på datorn.
    - **Nytt eller befintligt undernät**: anger om ett nytt virtuellt nätverk och undernät ska skapas eller om ett befintligt undernät används. Om du redan har ett virtuellt nätverk som är anslutet till ditt lokala nätverk väljer du **befintligt**.
    - **Undernäts-ID**: om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har ett undernät definierat måste den virtuella datorn vara tilldelad, namnge ID: t för det aktuella under nätet. ID: t ser vanligt vis ut som **/Subscriptions/ \<subscription ID> /resourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /subnets/ \<subnet name>**.

### <a name="manual-deployment"></a>Manuell distribution

> [!IMPORTANT]
> Kontrol lera att det operativ system du väljer är SAP-certifierat för SAP HANA på de angivna VM-typerna som du använder. Listan med SAP HANA certifierade VM-typer och OS-versioner för dem kan slås upp i [SAP HANA certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Se till att klicka i informationen om den VM-typ som anges för att få en fullständig lista över SAP HANA operativ system versioner som stöds för den angivna VM-typen
>  

1. Skapa en resursgrupp.
1. Skapa ett virtuellt nätverk.
1. Skapa en tillgänglighets uppsättning.
   - Ange Max uppdaterings domän.
1. Skapa en belastningsutjämnare (intern). Vi rekommenderar [standard Load Balancer](../../../load-balancer/load-balancer-overview.md).
   - Välj det virtuella nätverk som skapades i steg 2.
1. Skapa virtuell dator 1.
   - Använd en SLES4SAP-avbildning i Azure-galleriet som stöds för SAP HANA på den VM-typ som du har valt.
   - Välj den tillgänglighets uppsättning som skapades i steg 3.
1. Skapa virtuell dator 2.
   - Använd en SLES4SAP-avbildning i Azure-galleriet som stöds för SAP HANA på den VM-typ som du har valt.
   - Välj den tillgänglighets uppsättning som skapades i steg 3. 
1. Lägg till data diskar.

> [!IMPORTANT]
> Flytande IP stöds inte på en sekundär NIC-IP-konfiguration i belastnings Utjämnings scenarier. Mer information finns i [begränsningar för Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Om du behöver ytterligare IP-adress för den virtuella datorn distribuerar du ett andra nätverkskort.   

> [!Note]
> När virtuella datorer utan offentliga IP-adresser placeras i backend-poolen för intern (ingen offentlig IP-adress) standard Azure-belastningsutjämnare, kommer det inte att finnas någon utgående Internet anslutning, om inte ytterligare konfiguration utförs för att tillåta routning till offentliga slut punkter. Mer information om hur du uppnår utgående anslutningar finns i Översikt över [offentliga slut punkter för Virtual Machines med Azure standard Load Balancer i SAP-scenarier med hög tillgänglighet](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

1. Följ dessa konfigurations steg om du använder standard Load Balancer:
   1. Börja med att skapa en IP-pool på klient sidan:
   
      1. Öppna belastningsutjämnaren, Välj **klient delens IP-pool** och välj **Lägg till**.
      1. Ange namnet på den nya frontend-IP-poolen (till exempel **Hana-frontend**).
      1. Ange **tilldelningen** till **statisk** och ange IP-adressen (till exempel **10.0.0.13**).
      1. Välj **OK**.
      1. När den nya frontend-IP-poolen har skapats noterar du poolens IP-adress.
   
   1. Skapa sedan en backend-pool:
   
      1. Öppna belastningsutjämnaren, Välj **backend-pooler** och välj **Lägg till**.
      1. Ange namnet på den nya backend-poolen (till exempel **Hana-backend**).
      1. Välj **Virtual Network**.
      1. Välj **Lägg till en virtuell dator**.
      1. Välj * * virtuell dator * *.
      1. Välj de virtuella datorerna i SAP HANA klustret och deras IP-adresser.
      1. Välj **Lägg till**.
   
   1. Skapa sedan en hälso avsökning:
   
      1. Öppna belastningsutjämnaren, Välj **hälso avsökningar** och välj **Lägg till**.
      1. Ange namnet på den nya hälso avsökningen (till exempel **Hana-HP**).
      1. Välj **TCP** som protokoll och port 625 **03**. Behåll värdet för **Interval** inställt på 5 och **tröskelvärdet för tröskelvärdet** har värdet 2.
      1. Välj **OK**.
   
   1. Skapa sedan reglerna för belastnings utjämning:
   
      1. Öppna belastningsutjämnaren, Välj **belastnings Utjämnings regler** och välj **Lägg till**.
      1. Ange namnet på den nya belastnings Utjämnings regeln (till exempel **Hana-lb**).
      1. Välj IP-adressen för klient delen, backend-poolen och hälso avsökningen som du skapade tidigare (till exempel **Hana-frontend**, **Hana-backend** och **Hana-HP**).
      1. Välj **ha-portar**.
      1. Öka **tids gränsen för inaktivitet** till 30 minuter.
      1. Se till att **Aktivera flytande IP**.
      1. Välj **OK**.

1. Om ditt scenario till exempel använder Basic Load Balancer, följer du dessa konfigurations steg:
   1. Börja med att skapa en IP-pool på klient sidan:
   
      1. Öppna belastningsutjämnaren, Välj **klient delens IP-pool** och välj **Lägg till**.
      1. Ange namnet på den nya frontend-IP-poolen (till exempel **Hana-frontend**).
      1. Ange **tilldelningen** till **statisk** och ange IP-adressen (till exempel **10.0.0.13**).
      1. Välj **OK**.
      1. När den nya frontend-IP-poolen har skapats noterar du poolens IP-adress.
   
   1. Skapa sedan en backend-pool:
   
      1. Öppna belastningsutjämnaren, Välj **backend-pooler** och välj **Lägg till**.
      1. Ange namnet på den nya backend-poolen (till exempel **Hana-backend**).
      1. Välj **Lägg till en virtuell dator**.
      1. Välj den tillgänglighets uppsättning som skapades i steg 3.
      1. Välj de virtuella datorerna i SAP HANA klustret.
      1. Välj **OK**.
   
   1. Skapa sedan en hälso avsökning:
   
      1. Öppna belastningsutjämnaren, Välj **hälso avsökningar** och välj **Lägg till**.
      1. Ange namnet på den nya hälso avsökningen (till exempel **Hana-HP**).
      1. Välj **TCP** som protokoll och port 625 **03**. Behåll värdet för **Interval** inställt på 5 och **tröskelvärdet för tröskelvärdet** har värdet 2.
      1. Välj **OK**.
   
   1. Skapa regler för belastnings utjämning för SAP HANA 1,0:
   
      1. Öppna belastningsutjämnaren, Välj **belastnings Utjämnings regler** och välj **Lägg till**.
      1. Ange namnet på den nya belastnings Utjämnings regeln (till exempel Hana – lb-3 **03**).
      1. Välj IP-adressen för klient delen, backend-poolen och hälso avsökningen som du skapade tidigare (till exempel **Hana-frontend**).
      1. Behåll **protokollet** inställt på **TCP** och ange Port 3 **03** 15.
      1. Öka **tids gränsen för inaktivitet** till 30 minuter.
      1. Se till att **Aktivera flytande IP**.
      1. Välj **OK**.
      1. Upprepa de här stegen för Port 3 **03** 17.
   
   1. Skapa regler för belastnings utjämning för system databasen för SAP HANA 2,0:
   
      1. Öppna belastningsutjämnaren, Välj **belastnings Utjämnings regler** och välj **Lägg till**.
      1. Ange namnet på den nya belastnings Utjämnings regeln (till exempel Hana-lb-3 **03** 13).
      1. Välj IP-adressen för klient delen, backend-poolen och hälso avsökningen som du skapade tidigare (till exempel **Hana-frontend**).
      1. Behåll **protokollet** inställt på **TCP** och ange Port 3 **03** 13.
      1. Öka **tids gränsen för inaktivitet** till 30 minuter.
      1. Se till att **Aktivera flytande IP**.
      1. Välj **OK**.
      1. Upprepa dessa steg för Port 3 **03**.
   
   1. För SAP HANA 2,0 skapar du först reglerna för belastnings utjämning för klient databasen:
   
      1. Öppna belastningsutjämnaren, Välj **belastnings Utjämnings regler** och välj **Lägg till**.
      1. Ange namnet på den nya belastnings Utjämnings regeln (till exempel Hana – lb-3 **03** 40).
      1. Välj IP-adressen för klient delen, backend-poolen och hälso avsökningen som du skapade tidigare (till exempel **Hana-frontend**).
      1. Behåll **protokollet** inställt på **TCP** och ange Port 3 **03** 40.
      1. Öka **tids gränsen för inaktivitet** till 30 minuter.
      1. Se till att **Aktivera flytande IP**.
      1. Välj **OK**.
      1. Upprepa de här stegen för portarna 3 **03** 41 och 3 **03** 42.

   Om du vill ha mer information om de portar som krävs för SAP HANA kan du läsa kapitel [anslutningarna till klient databaserna](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) i guiden för [SAP HANA klient databaser](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) eller [SAP NOTE 2388694][2388694].

> [!IMPORTANT]
> Aktivera inte TCP-tidsstämplar på virtuella Azure-datorer som placerats bakom Azure Load Balancer. Om du aktiverar TCP-tidsstämplar kommer hälso avsökningarna att Miss skadas. Ange parametern **net.IPv4.tcp_timestamps** till **0**. Mer information finns i [Load Balancer hälso avsökningar](../../../load-balancer/load-balancer-custom-probe-overview.md).
> Se även SAP anmärkning [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="create-a-pacemaker-cluster"></a>Skapa ett pacemaker-kluster

Följ stegen i [Konfigurera pacemaker på SUSE Linux Enterprise Server i Azure](high-availability-guide-suse-pacemaker.md) för att skapa ett grundläggande pacemaker-kluster för den här Hana-servern. Du kan använda samma pacemaker-kluster för SAP HANA och SAP NetWeaver (A) SCS.

## <a name="install-sap-hana"></a>Installera SAP HANA

Stegen i det här avsnittet använder följande prefix:
- **[A]**: steget gäller för alla noder.
- **[1]**: steget gäller endast nod 1.
- **[2]**: steget gäller endast nod 2 i pacemaker-klustret.

1. **[A]** konfiguration av disklayouten: **Logical Volume Manager (LVM)**.

   Vi rekommenderar att du använder LVM för volymer som lagrar data och loggfiler. I följande exempel förutsätts att de virtuella datorerna har fyra anslutna data diskar som används för att skapa två volymer.

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

   Skapa en volym grupp för datafilerna. Använd en volym grupp för loggfilerna och en för den delade katalogen för SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Skapa de logiska volymerna. En linjär volym skapas när du använder `lvcreate` utan `-i` växeln. Vi rekommenderar att du skapar en stripe-volym för bättre I/O-prestanda och justerar stripe-storlekarna mot värdena som dokumenteras i [SAP HANA VM Storage-konfigurationer](./hana-vm-operations-storage.md). `-i`Argumentet ska vara antalet underliggande fysiska volymer och `-I` argumentet är stripe-storleken. I det här dokumentet används två fysiska volymer för data volymen, så `-i` växel argumentet är inställt på **2**. Stripe-storleken för data volymen är **256KiB**. En fysisk volym används för logg volymen, så inga `-i` eller `-I` växlar används explicit för logg volym kommandona.  

   > [!IMPORTANT]
   > Använd `-i` växeln och Ställ in den på den underliggande fysiska volymens nummer när du använder mer än en fysisk volym för varje data, logg eller delade volymer. Använd `-I` växeln för att ange stripe-storlek när du skapar en stripe-volym.  
   > Se [SAP HANA VM Storage-konfigurationer](./hana-vm-operations-storage.md) för rekommenderade diskpartitionskonfigurationer, inklusive stripe-storlekar och antal diskar.  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>
  
   Skapa monterings kataloger och kopiera UUID: n för alla logiska volymer:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Skapa `fstab` poster för de tre logiska volymerna:       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Lägg till följande rad i `/etc/fstab` filen:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Montera de nya volymerna:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** konfigurera disklayouten: **enkla diskar**.

   För demo system kan du placera dina HANA-data och loggfiler på en disk. Skapa en partition på/dev/disk/Azure/SCSI1/LUN0 och formatera den med xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Infoga den här raden i/etc/fstab-filen:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Skapa mål katalogen och montera disken:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** konfigurera värd namns matchning för alla värdar.

   Du kan antingen använda en DNS-server eller ändra/etc/hosts-filen på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Infoga följande rader i/etc/hosts-filen. Ändra IP-adress och värdnamn så att de matchar din miljö:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** installera SAP HANA hög tillgänglighets paket:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Om du vill installera SAP HANA systemreplikering följer du kapitel 4 i den [optimerade scenario guiden för SAP HANA SR-prestanda](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/).

1. **[A]** kör **hdblcm** -programmet från Hana-DVD: n. Ange följande värden i prompten:
   * Välj installation: ange **1**.
   * Välj ytterligare komponenter för installation: ange **1**.
   * Ange installations Sök väg [/Hana/Shared]: Välj RETUR.
   * Ange namnet på den lokala värden [..]: Välj RETUR.
   * Vill du lägga till fler värdar i systemet? (j/n) [n]: Välj RETUR.
   * Ange SAP HANA system-ID: Ange SID för HANA, till exempel: **HN1**.
   * Ange instans nummer [00]: Ange antalet HANA-instanser. Ange **03** om du använde Azure-mallen eller följde avsnittet manuell distribution i den här artikeln.
   * Välj databas läge/ange index [1]: Välj RETUR.
   * Välj system användning/ange index [4]: Välj system användning svärdet.
   * Ange plats för data volymer [/hana/data/HN1]: Välj RETUR.
   * Ange plats för logg volymer [/hana/log/HN1]: Välj RETUR.
   * Begränsa maximal minnesallokering? [n]: Välj RETUR.
   * Ange ett certifikat värd namn för värden '... ' [...]: Välj RETUR.
   * Ange lösen ord för SAP host agent-användare (sapadm): Ange användar lösen ordet för värd agenten.
   * Bekräfta lösen ordet för SAP host agent-användare (sapadm): Ange värd agentens användar lösen ord igen för att bekräfta.
   * Ange system administratörs lösen ord (hdbadm): Ange system administratörens lösen ord.
   * Bekräfta lösen ord för system administratör (hdbadm): Ange system administratörens lösen ord igen för att bekräfta.
   * Ange system administratörens Hem Katalog [/usr/sap/HN1/home]: Välj RETUR.
   * Ange system administratör inloggnings gränssnitt [/bin/sh]: Välj RETUR.
   * Ange användar-ID för system administratör [1001]: Välj RETUR.
   * Ange ID för användar gruppen (sapsys) [79]: Välj RETUR.
   * Ange lösen ord för databas användare (SYSTEM): Ange användar lösen ordet för databasen.
   * Bekräfta lösen ord för databas användare (SYSTEM): Ange användar lösen ordet för databasen igen för att bekräfta.
   * Vill du starta om systemet efter omstart av datorn? [n]: Välj RETUR.
   * Vill du fortsätta? (j/n): verifiera sammanfattningen. Fortsätt genom att ange **y** .

1. **[A]** uppgradera SAP-värd agenten.

   Hämta det senaste SAP-värd agent arkivet från [SAP Software Center][sap-swcenter] och kör följande kommando för att uppgradera agenten. Ersätt sökvägen till arkivet så att den pekar på den fil som du laddade ned:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurera SAP HANA 2,0-systemreplikering

Stegen i det här avsnittet använder följande prefix:

* **[A]**: steget gäller för alla noder.
* **[1]**: steget gäller endast nod 1.
* **[2]**: steget gäller endast nod 2 i pacemaker-klustret.

1. **[1]** skapa klient organisations databasen.

   Om du använder SAP HANA 2,0 eller MDC skapar du en klient databas för ditt SAP NetWeaver-system. Ersätt **NW1** med sid för ditt SAP-system.

   Kör följande kommando som <hanasid \> ADM:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** konfigurera systemreplikering på den första noden:

   Säkerhetskopiera databaserna som <hanasid \> ADM:

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

1. **[2]** konfigurera systemreplikering på den andra noden:
    
   Registrera den andra noden för att starta systemreplikeringen. Kör följande kommando som <hanasid \> ADM:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurera SAP HANA 1,0-systemreplikering

Stegen i det här avsnittet använder följande prefix:

* **[A]**: steget gäller för alla noder.
* **[1]**: steget gäller endast nod 1.
* **[2]**: steget gäller endast nod 2 i pacemaker-klustret.

1. **[1]** skapa de användare som krävs.

   Kör följande kommando som rot. Se till att ersätta fetstils strängar (HANA system-ID **HN1** och instance Number **03**) med värdena för din SAP HANA-installation:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** skapa posten i nyckel arkivet.

   Kör följande kommando som rot för att skapa en ny nyckel lagrings post:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** säkerhetskopiera databasen.

   Säkerhetskopiera databaserna som rot:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Om du använder en installation med flera klient organisationer säkerhetskopierar du även klient databasen:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** konfigurera systemreplikering på den första noden.

   Skapa den primära platsen som <hanasid \> ADM:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** konfigurera systemreplikering på den sekundära noden.

   Registrera den sekundära platsen som <hanasid \> ADM:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Skapa SAP HANA kluster resurser

Skapa först HANA-topologin. Kör följande kommandon på en av pacemaker-klusternoderna:

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
> De senaste testerna visade situationer, där netcat slutar svara på begär Anden på grund av en efter släpning och dess begränsning av hantering av endast en anslutning. Netcat-resursen slutar lyssna på Azure Load Balancer-begäranden och den flytande IP-adressen blir otillgänglig.  
> För befintliga pacemaker-kluster rekommenderar vi att du tidigare ersätter netcat med socat. För närvarande rekommenderar vi att du använder Azure-lb Resource agent, som är en del av paketets resurs agenter, med följande paket versions krav:
> - För SLES 12 SP4/SP5 måste versionen vara minst resurs agenter-4.3.018. a7fb5035-3.30.1.  
> - För SLES 15/15 SP1 måste versionen vara minst resurs agenter-4.3.0184.6 ee15eb2-4.13.1.  
>
> Observera att ändringen kräver kortare stillestånds tid.  
> För befintliga pacemaker-kluster, om konfigurationen redan har ändrats för att använda socat enligt beskrivningen i [azure Load-Balancer Avkännings härdning](https://www.suse.com/support/kb/doc/?id=7024128), finns det inget krav på att växla direkt till Azure-lb Resource agent.


> [!NOTE]
> Den här artikeln innehåller referenser till villkors *huvud* och *slav*, och villkor som Microsoft inte längre använder. När de här villkoren tas bort från program varan tar vi bort dem från den här artikeln.

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

Kontrol lera att klustrets status är OK och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

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

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Konfigurera HANA aktiv/Läs aktive rad system replikering i pacemaker-kluster

Från och med SAP HANA 2,0 SPS 01 SAP tillåts aktiv/Read-aktiverad installation av SAP HANA system replikering där de sekundära systemen i SAP HANA systemreplikering kan användas aktivt för Läs intensiva arbets belastningar. För att stödja den här installationen i ett kluster krävs en andra virtuell IP-adress som gör att klienterna kan komma åt den sekundära Read-aktiverade SAP HANA databasen. För att säkerställa att den sekundära replikerings platsen fortfarande kan nås efter det att ett övertag har inträffat måste klustret flytta den virtuella IP-adressen med den sekundära av SAPHana-resursen.

I det här avsnittet beskrivs de ytterligare steg som krävs för att hantera HANA aktiv/läsning aktive rad system replikering i ett SUSEt kluster med hög tillgänglighet med andra virtuella IP-adresser.    
Innan du fortsätter bör du kontrol lera att du har konfigurerat ett kluster med hög tillgänglighet för SUSEt med hög tillgänglighet för att hantera SAP HANA databasen, enligt beskrivningen ovan i dokumentationen.  

![SAP HANA hög tillgänglighet med Read-aktiverad sekundär](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>Ytterligare konfiguration i Azure Load Balancer för aktiv/Read-aktiverad installation

Om du vill fortsätta med ytterligare steg om hur du konfigurerar andra virtuella IP-adresser kontrollerar du att du har konfigurerat Azure Load Balancer enligt beskrivningen i avsnittet [manuell distribution](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability#manual-deployment) .

1. För en **standard** belastningsutjämnare följer du de ytterligare stegen nedan på samma belastningsutjämnare som du skapade i föregående avsnitt.

   a. Skapa en andra IP-adresspool på klient sidan: 

   - Öppna belastningsutjämnaren, Välj **klient delens IP-pool** och välj **Lägg till**.
   - Ange namnet på den andra frontend-IP-poolen (till exempel **Hana-secondaryIP**).
   - Ange **tilldelningen** till **statisk** och ange IP-adressen (till exempel **10.0.0.14**).
   - Välj **OK**.
   - När den nya frontend-IP-poolen har skapats noterar du klient delens IP-adress.

   b. Skapa sedan en hälso avsökning:

   - Öppna belastningsutjämnaren, Välj **hälso avsökningar** och välj **Lägg till**.
   - Ange namnet på den nya hälso avsökningen (till exempel **Hana-secondaryhp**).
   - Välj **TCP** som protokoll och port **62603**. Behåll värdet för **Interval** inställt på 5 och **tröskelvärdet för tröskelvärdet** har värdet 2.
   - Välj **OK**.

   c. Skapa sedan reglerna för belastnings utjämning:

   - Öppna belastningsutjämnaren, Välj **belastnings Utjämnings regler** och välj **Lägg till**.
   - Ange namnet på den nya belastnings Utjämnings regeln (till exempel **Hana-secondarylb**).
   - Välj IP-adressen för klient delen, backend-poolen och hälso avsökningen som du skapade tidigare (till exempel **Hana-secondaryIP**, **Hana-backend** och **Hana-secondaryhp**).
   - Välj **ha-portar**.
   - Öka **tids gränsen för inaktivitet** till 30 minuter.
   - Se till att **Aktivera flytande IP**.
   - Välj **OK**.

### <a name="configure-hana-activeread-enabled-system-replication"></a>Konfigurera HANA aktiv/läsning aktive rad system replikering

Stegen för att konfigurera HANA-systemreplikering beskrivs i avsnittet [konfigurera SAP HANA 2,0-system replikering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability#configure-sap-hana-20-system-replication) . Om du distribuerar Read-aktiverad sekundärt scenario, medan du konfigurerar systemreplikering på den andra noden, kör du följande kommando som **hanasid** ADM:

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2 --operationMode=logreplay_readaccess 
```

### <a name="adding-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>Lägga till en sekundär virtuell IP-adressresurs för en aktiv/Read-aktiverad installation

Den andra virtuella IP-adressen och lämplig samplacerings begränsning kan konfigureras med följande kommandon:

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
Kontrol lera att klustrets status är OK och att alla resurser har startats. Den andra virtuella IP-adressen kommer att köras på den sekundära platsen tillsammans med den sekundära SAPHana-resursen.

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

I nästa avsnitt hittar du en typisk uppsättning redundanstest som du kan köra.

Tänk på det andra virtuella IP-beteendet, samtidigt som du testar ett HANA-kluster som kon figurer ATS med Read-aktiverad sekundär:

1. När du migrerar **SAPHana_HN1_HDB03** kluster resurs till **HN1-DB-1** flyttas den andra virtuella IP-adressen till den andra servern **HN1-dB-0**. Om du har konfigurerat AUTOMATED_REGISTER = "falskt" och HANA-systemreplikering inte registreras automatiskt, kommer den andra virtuella IP-adressen att köras på **HN1-dB-0,** eftersom servern är tillgänglig och kluster tjänsterna är online.  

2. När du testar en server krasch körs de andra virtuella IP-resurserna (**rsc_secip_HN1_HDB03**) och Azure Load Balancer-port resursen (**rsc_secnc_HN1_HDB03**) på den primära servern tillsammans med de primära virtuella IP-resurserna. Även om den sekundära servern inte är igång, kommer de program som är anslutna till den Read-aktiverade HANA-databasen att ansluta till den primära HANA-databasen. Beteendet förväntas eftersom du inte vill att program som är anslutna till den Read-aktiverade HANA-databasen ska vara otillgängliga när den sekundära servern inte är tillgänglig.
  
3. När den sekundära servern är tillgänglig och kluster tjänsterna är online flyttas den andra virtuella IP-adressen och port resurserna automatiskt till den sekundära servern, även om HANA-systemreplikeringen kanske inte registreras som sekundär. Du måste se till att du registrerar den sekundära HANA-databasen som skrivskyddad innan du startar kluster tjänsterna på den servern. Du kan konfigurera HANA-instansens kluster resurs för att automatiskt registrera den sekundära genom att ange parametern AUTOMATED_REGISTER = True.       

4. Under redundansväxling och återställning kan befintliga anslutningar för program med hjälp av den andra virtuella IP-adressen för att ansluta till HANA-databasen avbrytas.  

## <a name="test-the-cluster-setup"></a>Testa kluster konfigurationen

I det här avsnittet beskrivs hur du kan testa installationen. Varje test förutsätter att du är rot och att SAP HANA Master körs på den virtuella datorn **HN1-dB-0** .

### <a name="test-the-migration"></a>Testa migreringen

Innan du startar testet kontrollerar du att pacemaker inte har någon misslyckad åtgärd (via crm_mon-r), det finns inga oväntade plats begränsningar (till exempel rester av ett migreringsjobb) och att HANA är Sync-tillstånd, till exempel med SAPHanaSR-showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

Du kan migrera SAP HANA Master-noden genom att köra följande kommando:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Om du ställer in `AUTOMATED_REGISTER="false"` bör den här sekvensen av kommandon migrera SAP HANA huvud-noden och gruppen som innehåller den virtuella IP-adressen till HN1-DB-1.

När migreringen är färdig ser crm_mon r-utdata ut så här

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

Det går inte att starta den SAP HANA resursen på HN1-dB-0 som sekundär. I det här fallet konfigurerar du HANA-instansen som sekundär genom att köra det här kommandot:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

Migreringen skapar plats begränsningar som behöver tas bort igen:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Du måste också rensa statusen för den sekundära noden:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Övervaka statusen för HANA-resursen med crm_mon-r. När HANA har startats på HN1-dB-0 bör utdata se ut så här

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

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Testa Azure-avgränsnings agenten (inte SBD)

Du kan testa konfigurationen av Azure-avgränsnings agenten genom att inaktivera nätverks gränssnittet på noden HN1-dB-0:

<pre><code>sudo ifdown eth0
</code></pre>

Den virtuella datorn bör nu startas om eller stoppas beroende på kluster konfigurationen.
Om du ställer in `stonith-action` inställningen till av stoppas den virtuella datorn och resurserna migreras till den virtuella datorn som körs.

När du har startat den virtuella datorn igen går det inte att starta SAP HANA resursen som sekundär om du anger `AUTOMATED_REGISTER="false"` . I det här fallet konfigurerar du HANA-instansen som sekundär genom att köra det här kommandot:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Testa SBD staket

Du kan testa installationen av SBD genom att avsluta Inquisitor-processen.

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

Klusternoden HN1-dB-0 måste startas om. Pacemaker-tjänsten kanske inte kommer igång efteråt. Se till att starta om den igen.

### <a name="test-a-manual-failover"></a>Testa en manuell redundansväxling

Du kan testa en manuell redundansväxling genom att stoppa `pacemaker` tjänsten på noden HN1-dB-0:

<pre><code>service pacemaker stop
</code></pre>

Efter redundansväxlingen kan du starta tjänsten igen. Om du ställer in `AUTOMATED_REGISTER="false"` går det inte att starta SAP HANA resursen på noden HN1-dB-0 som sekundär. I det här fallet konfigurerar du HANA-instansen som sekundär genom att köra det här kommandot:

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
> Kontrol lera att det operativ system du väljer är SAP-certifierat för SAP HANA på de angivna VM-typerna som du använder. Listan med SAP HANA certifierade VM-typer och OS-versioner för dem kan slås upp i [SAP HANA certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Se till att klicka i informationen om den VM-typ som anges för att få en fullständig lista över SAP HANA operativ system versioner som stöds för den angivna VM-typen

Kör alla test fall som anges i scenariot för SAP HANA optimala prestanda optimerings scenario eller SAP HANA SR-kostnads optimerings guide, beroende på ditt användnings fall. Du hittar guiderna på [sidan SLES for SAP Best Practices][sles-for-sap-bp].

Följande tester är en kopia av test beskrivningarna av SAP HANA optimala prestanda optimerings scenario SUSE Linux Enterprise Server för SAP-program 12 SP1-guide. För en uppdaterad version ska du alltid läsa själva guiden. Se alltid till att HANA är synkroniserat innan du startar testet och kontrol lera också att pacemaker-konfigurationen är korrekt.

I följande test beskrivningar antar vi PREFER_SITE_TAKEOVER = "true" och AUTOMATED_REGISTER = "false".
Obs! följande tester är utformade för att köras i följd och är beroende av avsluts läget för föregående tester.

1. TEST 1: STOPPA PRIMÄR DATABAS PÅ NOD 1

   Resurs tillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som <hanasid \> adm på noden HN1-dB-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker bör identifiera den stoppade HANA-instansen och redundansväxla till den andra noden. När redundansväxlingen är färdig stoppas HANA-instansen på Node HN1-dB-0 eftersom pacemaker inte automatiskt registrerar noden som HANA sekundär.

   Kör följande kommandon för att registrera Node HN1-dB-0 som sekundär och rensa den misslyckade resursen.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Resurs tillstånd efter testet:

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

   Resurs tillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Kör följande kommandon som <hanasid \> adm på noden HN1-DB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker bör identifiera den stoppade HANA-instansen och redundansväxla till den andra noden. När redundansväxlingen är färdig stoppas HANA-instansen på Node HN1-DB-1 eftersom pacemaker inte automatiskt registrerar noden som HANA sekundär.

   Kör följande kommandon för att registrera Node HN1-DB-1 som sekundär och rensa den misslyckade resursen.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resurs tillstånd efter testet:

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

   Resurs tillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som <hanasid \> adm på noden HN1-dB-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Pacemaker ska identifiera den stoppade HANA-instansen och redundansväxla till den andra noden. När redundansväxlingen är färdig stoppas HANA-instansen på Node HN1-dB-0 eftersom pacemaker inte automatiskt registrerar noden som HANA sekundär.

   Kör följande kommandon för att registrera Node HN1-dB-0 som sekundär och rensa den misslyckade resursen.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Resurs tillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 4: KRASCHA PRIMÄR DATABAS PÅ NOD 2

   Resurs tillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Kör följande kommandon som <hanasid \> adm på noden HN1-DB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker ska identifiera den stoppade HANA-instansen och redundansväxla till den andra noden. När redundansväxlingen är färdig stoppas HANA-instansen på Node HN1-DB-1 eftersom pacemaker inte automatiskt registrerar noden som HANA sekundär.

   Kör följande kommandon för att registrera Node HN1-DB-1 som sekundär och rensa den misslyckade resursen.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resurs tillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 5: NOD FÖR PRIMÄR PLATS FÖR KRASCH (NOD 1)

   Resurs tillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som rot på Node HN1-dB-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker ska identifiera den stoppade klusternoden och stängsel noden. När noden har avgräns ATS utlöses pacemaker av HANA-instansen. När den avgränsade noden startas om startar inte pacemaker automatiskt.

   Kör följande kommandon för att starta pacemaker, rensa SBD-meddelandena för Node HN1-dB-0, registrera noden HN1-dB-0 som sekundär och rensa den misslyckade resursen.

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

   Resurs tillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 6: KRASCHA SEKUNDÄR PLATS NOD (NOD 2)

   Resurs tillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Kör följande kommandon som rot på Node HN1-DB-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker ska identifiera den stoppade klusternoden och stängsel noden. När noden har avgräns ATS utlöses pacemaker av HANA-instansen. När den avgränsade noden startas om startar inte pacemaker automatiskt.

   Kör följande kommandon för att starta pacemaker, rensa SBD-meddelandena för Node HN1-DB-1, registrera Node HN1-DB-1 som sekundär och rensa den misslyckade resursen.

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

   Resurs tillstånd efter testet:

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

   Resurs tillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som <hanasid \> adm på noden HN1-DB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker kommer att identifiera den stoppade HANA-instansen och markera resursen som misslyckad på noden HN1-DB-1. Pacemaker bör starta om HANA-instansen automatiskt. Kör följande kommando för att rensa det felaktiga läget.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resurs tillstånd efter testet:

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

   Resurs tillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som <hanasid \> adm på noden HN1-DB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker kommer att identifiera den stoppade HANA-instansen och markera resursen som misslyckad på noden HN1-DB-1. Kör följande kommando för att rensa det felaktiga läget. Pacemaker bör sedan starta om HANA-instansen automatiskt.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resurs tillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 9: KRASCHA SEKUNDÄR PLATS-NOD (NOD 2) SOM KÖR SEKUNDÄR HANA-DATABAS

   Resurs tillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som rot på Node HN1-DB-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pacemaker ska identifiera den stoppade klusternoden och stängsel noden. När den avgränsade noden startas om startar inte pacemaker automatiskt.

   Kör följande kommandon för att starta pacemaker, rensa SBD-meddelandena för Node HN1-DB-1 och rensa den misslyckade resursen.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resurs tillstånd efter testet:

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

* [Azure Virtual Machines planera och implementera SAP][planning-guide]
* [Azure Virtual Machines distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
