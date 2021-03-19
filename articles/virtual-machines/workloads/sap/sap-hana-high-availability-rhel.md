---
title: Hög tillgänglighet för SAP HANA på virtuella Azure-datorer på RHEL | Microsoft Docs
description: Upprätta hög tillgänglighet för SAP HANA på virtuella datorer i Azure.
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
ms.openlocfilehash: daa0a6b15d4c187efdea96fd8067b08c89fa0e82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599875"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Hög tillgänglighet för SAP HANA på virtuella Azure-datorer på Red Hat Enterprise Linux

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

För lokal utveckling kan du använda antingen HANA-systemreplikering eller använda delad lagring för att upprätta hög tillgänglighet för SAP HANA.
På Azure Virtual Machines (VM) är HANA-systemreplikeringen på Azure för närvarande den enda funktion för hög tillgänglighet som stöds.
SAP HANA replikeringen består av en primär nod och minst en sekundär nod. Ändringar av data på den primära noden replikeras till den sekundära noden synkront eller asynkront.

I den här artikeln beskrivs hur du distribuerar och konfigurerar de virtuella datorerna, installerar kluster ramverket och installerar och konfigurerar SAP HANA system replikering.
I exempel konfigurationerna används installations kommandon, instans nummer **03** och Hana system-ID **HN1** .

Läs följande SAP-anteckningar och dokument först:

* SAP anmärkning [1928533], som har:
  * Listan med virtuella Azure-storlekar som stöds för distribution av SAP-program.
  * Viktig kapacitets information för storlekar på virtuella Azure-datorer.
  * De SAP-program och-operativ system och databas kombinationer som stöds.
  * Den nödvändiga SAP kernel-versionen för Windows och Linux på Microsoft Azure.
* SAP NOTE [2015553] visar krav för SAP-program distributioner som stöds i Azure.
* SAP NOTE [2002167] har rekommenderade OS-inställningar för Red Hat Enterprise Linux
* SAP NOTE [2009879] har SAP HANA rikt linjer för Red Hat Enterprise Linux
* SAP NOTE [2178632] innehåller detaljerad information om alla övervaknings mått som rapporter ATS för SAP i Azure.
* SAP NOTE [2191498] har den version av SAP host agent som krävs för Linux i Azure.
* SAP NOTE [2243692] innehåller information om SAP-licensiering på Linux i Azure.
* SAP anmärkning [1999351] innehåller ytterligare felsöknings information för Azure Enhanced Monitoring-tillägget för SAP.
* [SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP-anteckningar för Linux.
* [Azure Virtual Machines planera och implementera SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux (den här artikeln)][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [SAP HANA system replikering i pacemaker-kluster](https://access.redhat.com/articles/3004101)
* Allmän dokumentation om RHEL
  * [Översikt över Add-On med hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Add-On administration med hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Add-On referens för hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Dokumentation om Azure Specific RHEL:
  * [Support principer för RHEL-kluster med hög tillgänglighet – Microsoft Azure Virtual Machines som kluster medlemmar](https://access.redhat.com/articles/3131341)
  * [Installera och konfigurera en Red Hat Enterprise Linux 7,4 (och senare) High-Availability kluster på Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Installera SAP HANA på Red Hat Enterprise Linux för användning i Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Översikt

SAP HANA installeras på två virtuella datorer för att uppnå hög tillgänglighet. Data replikeras med hjälp av HANA-systemreplikering.

![Översikt över SAP HANA hög tillgänglighet](./media/sap-hana-high-availability-rhel/ha-hana.png)

Installations programmet för SAP HANA system replikering använder ett dedikerat virtuellt värdnamn och virtuella IP-adresser. I Azure krävs en belastningsutjämnare för att använda en virtuell IP-adress. I följande lista visas belastnings Utjämnings konfigurationen:

* Konfiguration av klient sidan: IP-10.0.0.13 för HN1-DB
* Konfiguration på Server sidan: anslutna till primära nätverks gränssnitt för alla virtuella datorer som ska ingå i HANA-systemreplikering
* Avsöknings port: port 62503
* Belastnings Utjämnings regler: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Distribuera för Linux

Azure Marketplace innehåller en avbildning för Red Hat Enterprise Linux 7,4 för SAP HANA som du kan använda för att distribuera nya virtuella datorer.

### <a name="deploy-with-a-template"></a>Distribuera med en mall

Du kan använda en av snabb starts mallarna som finns på GitHub för att distribuera alla nödvändiga resurser. Mallen distribuerar de virtuella datorerna, belastningsutjämnaren, tillgänglighets uppsättningen och så vidare.
Följ dessa steg om du vill distribuera mallen:

1. Öppna [databas mal len][template-multisid-db] på Azure Portal.
1. Ange följande parametrar:
    * **SAP-system-ID**: Ange SAP-system-ID: t för det SAP-system som du vill installera. ID används som prefix för de resurser som distribueras.
    * **OS-typ**: Välj en av Linux-distributionerna. I det här exemplet väljer du **RHEL 7**.
    * **Db-typ**: Välj **Hana**.
    * **SAP-system storlek**: Ange antalet SAPS som det nya systemet ska tillhandahålla. Om du inte är säker på hur många SAPS systemet kräver kan du fråga din SAP Technology-partner eller system integrerare.
    * **System tillgänglighet**: Välj **ha**.
    * **Administratörens användar namn, administratörs lösen ord eller SSH-nyckel**: en ny användare skapas som kan användas för att logga in på datorn.
    * **Undernäts-ID**: om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har ett undernät definierat måste den virtuella datorn vara tilldelad, namnge ID: t för det aktuella under nätet. ID: t ser vanligt vis ut som **/Subscriptions/ \<subscription ID> /resourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /subnets/ \<subnet name>**. Lämna tomt om du vill skapa ett nytt virtuellt nätverk

### <a name="manual-deployment"></a>Manuell distribution

1. Skapa en resursgrupp.
1. Skapa ett virtuellt nätverk.
1. Skapa en tillgänglighets uppsättning.  
   Ange Max uppdaterings domän.
1. Skapa en belastningsutjämnare (intern). Vi rekommenderar [standard Load Balancer](../../../load-balancer/load-balancer-overview.md).
   * Välj det virtuella nätverk som skapades i steg 2.
1. Skapa virtuell dator 1.  
   Använd minst Red Hat Enterprise Linux 7,4 för SAP HANA. I det här exemplet används Red Hat Enterprise Linux 7,4 för SAP HANA avbildningen <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Välj den tillgänglighets uppsättning som skapades i steg 3.
1. Skapa virtuell dator 2.  
   Använd minst Red Hat Enterprise Linux 7,4 för SAP HANA. I det här exemplet används Red Hat Enterprise Linux 7,4 för SAP HANA avbildningen <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Välj den tillgänglighets uppsättning som skapades i steg 3.
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
   1. Konfigurera belastningsutjämnaren. Börja med att skapa en IP-pool på klient sidan:

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

## <a name="install-sap-hana"></a>Installera SAP HANA

Stegen i det här avsnittet använder följande prefix:

* **[A]**: steget gäller för alla noder.
* **[1]**: steget gäller endast nod 1.
* **[2]**: steget gäller endast nod 2 i pacemaker-klustret.

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

1. **[A]** RHEL för Hana-konfiguration

   Konfigurera RHEL enligt beskrivningen i <https://access.redhat.com/solutions/2447641> och i följande SAP-anteckningar:  
   - [2292690-SAP HANA DB: rekommenderade OS-inställningar för RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782-SAP HANA DB: rekommenderade OS-inställningar för RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 – Linux: köra SAP-program som kompileras med GCC 6. x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 – Linux: köra SAP-program som kompileras med GCC 7. x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 – Linux: köra SAP-program som kompileras med GCC 9. x](https://launchpad.support.sap.com/#/notes/2886607)

1. **[A]** installera SAP HANA

   Följ för att installera SAP HANA system-replikering <https://access.redhat.com/articles/3004101> .

   * Kör **hdblcm** -programmet från Hana-DVD: n. Ange följande värden i prompten:
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

1. **[A]** konfigurera brand vägg

   Skapa brand Väggs regeln för avsöknings porten för Azure Load Balancer.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurera SAP HANA 2,0-systemreplikering

Stegen i det här avsnittet använder följande prefix:

* **[A]**: steget gäller för alla noder.
* **[1]**: steget gäller endast nod 1.
* **[2]**: steget gäller endast nod 2 i pacemaker-klustret.

1. **[A]** konfigurera brand vägg

   Skapa brand Väggs regler som tillåter HANA-systemreplikering och klient trafik. De portar som krävs visas på [TCP/IP-portar för alla SAP-produkter](https://help.sap.com/viewer/ports). Följande kommandon är bara ett exempel för att tillåta HANA 2,0-systemreplikering och klient trafik till Database SYSTEMDB, HN1 och NW1.

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

1. **[1]** skapa klient organisations databasen.

   Om du använder SAP HANA 2,0 eller MDC skapar du en klient databas för ditt SAP NetWeaver-system. Ersätt **NW1** med sid för ditt SAP-system.

   Kör som <hanasid \> ADM följande kommando:

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

1. **[1]** kontrol lera replikeringsstatus

   Kontrol lera replikeringsstatus och vänta tills alla databaser är synkroniserade. Om statusen förblir okänd kontrollerar du brand Väggs inställningarna.

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

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurera SAP HANA 1,0-systemreplikering

Stegen i det här avsnittet använder följande prefix:

* **[A]**: steget gäller för alla noder.
* **[1]**: steget gäller endast nod 1.
* **[2]**: steget gäller endast nod 2 i pacemaker-klustret.

1. **[A]** konfigurera brand vägg

   Skapa brand Väggs regler som tillåter HANA-systemreplikering och klient trafik. De portar som krävs visas på [TCP/IP-portar för alla SAP-produkter](https://help.sap.com/viewer/ports). Följande kommandon är bara ett exempel för att tillåta replikering av HANA 2,0-system. Anpassa den till din SAP HANA 1,0-installation.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

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

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Skapa ett pacemaker-kluster

Följ stegen i [Konfigurera pacemaker på Red Hat Enterprise Linux i Azure](high-availability-guide-rhel-pacemaker.md) för att skapa ett grundläggande pacemaker-kluster för den här Hana-servern.

## <a name="create-sap-hana-cluster-resources"></a>Skapa SAP HANA kluster resurser

Installera SAP HANA resurs agenter på **alla noder**. Se till att aktivera en lagrings plats som innehåller paketet. Du behöver inte aktivera fler databaser, om du använder RHEL 8. x HA-aktiverad avbildning.  

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Skapa sedan HANA-topologin. Kör följande kommandon på en av pacemaker-klusternoderna:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Skapa sedan HANA-resurserna.

> [!NOTE]
> Den här artikeln innehåller referenser till termen *slav*, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.

Om du skapar ett kluster på **RHEL 7. x**, använder du följande kommandon:  

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

Om du skapar ett kluster på **RHEL 8. x** använder du följande kommandon:  

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

Kontrol lera att klustrets status är OK och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

> [!NOTE]
> Tids gränsen i konfigurationen ovan är bara exempel och kan behöva anpassas till den angivna HANA-installationen. Du kan till exempel behöva öka tids gränsen för start, om det tar längre tid att starta SAP HANA databasen.  

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


## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Konfigurera HANA aktiv/Läs aktive rad system replikering i pacemaker-kluster

Från och med SAP HANA 2,0 SPS 01 SAP tillåts aktiva/skrivskyddade installations program för SAP HANA system replikering, där de sekundära systemen i SAP HANA systemreplikering kan användas aktivt för Läs intensiva arbets belastningar. För att stödja den här installationen i ett kluster krävs en andra virtuell IP-adress som gör att klienterna kan komma åt den sekundära Read-aktiverade SAP HANA databasen. För att säkerställa att den sekundära replikerings platsen fortfarande kan nås efter det att ett övertag har inträffat måste klustret flytta den virtuella IP-adressen med den sekundära av SAPHana-resursen.

I det här avsnittet beskrivs de ytterligare steg som krävs för att hantera HANA aktiv/läsning aktive rad systemreplikering i ett kluster med Red Hat hög tillgänglighet med andra virtuella IP-adresser.    

Innan du fortsätter bör du kontrol lera att du har konfigurerat ett kluster med hög tillgänglighet för Red Hat-kluster som hanterar SAP HANA databasen, enligt beskrivningen i ovanstående segment i dokumentationen.  

![SAP HANA hög tillgänglighet med Read-aktiverad sekundär](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>Ytterligare konfiguration i Azure Load Balancer för aktiv/Read-aktiverad installation

Om du vill fortsätta med ytterligare steg om hur du konfigurerar andra virtuella IP-adresser kontrollerar du att du har konfigurerat Azure Load Balancer enligt beskrivningen i avsnittet [manuell distribution](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#manual-deployment) .

1. För en **standard** belastningsutjämnare följer du ytterligare steg på samma belastningsutjämnare som du skapade i föregående avsnitt.

   a. Skapa en andra IP-adresspool på klient sidan: 

   - Öppna belastningsutjämnaren, Välj **klient delens IP-pool** och välj **Lägg till**.
   - Ange namnet på den andra frontend-IP-poolen (till exempel **Hana-secondaryIP**).
   - Ange **tilldelningen** till **statisk** och ange IP-adressen (till exempel **10.0.0.14**).
   - Välj **OK**.
   - När den nya frontend-IP-poolen har skapats noterar du poolens IP-adress.

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

Stegen för att konfigurera HANA-systemreplikering beskrivs i avsnittet [konfigurera SAP HANA 2,0-system replikering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-sap-hana-20-system-replication) . Om du distribuerar Read-aktiverad sekundärt scenario, medan du konfigurerar systemreplikering på den andra noden, kör du följande kommando som **hanasid** ADM:

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2 --operationMode=logreplay_readaccess 
```

### <a name="adding-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>Lägga till en sekundär virtuell IP-adressresurs för en aktiv/Read-aktiverad installation

Den andra virtuella IP-adressen och lämplig samplacerings begränsning kan konfigureras med följande kommandon:

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
Kontrol lera att klustrets status är OK och att alla resurser har startats. Den andra virtuella IP-adressen kommer att köras på den sekundära platsen tillsammans med den sekundära SAPHana-resursen.

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

I nästa avsnitt hittar du en typisk uppsättning redundanstest som du kan köra.

Tänk på det andra virtuella IP-beteendet, samtidigt som du testar ett HANA-kluster som kon figurer ATS med Read-aktiverad sekundär:

1. När du migrerar **SAPHana_HN1_HDB03** kluster resurs till **HN1-DB-1** flyttas den andra virtuella IP-adressen till den andra servern **HN1-dB-0**. Om du har konfigurerat AUTOMATED_REGISTER = "falskt" och HANA-systemreplikering inte registreras automatiskt, kommer den andra virtuella IP-adressen att köras på **HN1-dB-0,** eftersom servern är tillgänglig och kluster tjänsterna är online.  

2. När du testar server kraschar, kommer de andra virtuella IP-resurserna (**rsc_secip_HN1_HDB03**) och Azure Load Balancer-port resursen (**rsc_secnc_HN1_HDB03**) att köras på den primära servern tillsammans med de primära virtuella IP-resurserna.  Även om den sekundära servern inte är igång, kommer de program som är anslutna till den Read-aktiverade HANA-databasen att ansluta till den primära HANA-databasen. Beteendet förväntas eftersom du inte vill att program som är anslutna till den Read-aktiverade HANA-databasen ska vara otillgängliga när den sekundära Server tiden inte är tillgänglig.

3. När den sekundära servern är tillgänglig och kluster tjänsterna är online flyttas den andra virtuella IP-adressen och port resurserna automatiskt till den sekundära servern, även om HANA-systemreplikeringen kanske inte registreras som sekundär. Du måste se till att du registrerar den sekundära HANA-databasen som skrivskyddad innan du startar kluster tjänsterna på den servern. Du kan konfigurera HANA-instansens kluster resurs för att automatiskt registrera den sekundära genom att ange parametern AUTOMATED_REGISTER = True.
   
4. Under redundansväxling och återställning kan befintliga anslutningar för program med hjälp av den andra virtuella IP-adressen för att ansluta till HANA-databasen avbrytas.  

## <a name="test-the-cluster-setup"></a>Testa kluster konfigurationen

I det här avsnittet beskrivs hur du kan testa installationen. Innan du startar ett test kontrollerar du att pacemaker inte har någon misslyckad åtgärd (via dator status). det finns inga oväntade plats begränsningar (till exempel rester av ett migreringsjobb) och att HANA är Sync-tillståndet, till exempel med systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Testa migreringen

Resurs tillstånd innan du startar testet:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Du kan migrera SAP HANA Master-noden genom att köra följande kommando:

<pre><code># On RHEL <b>7.x</b> 
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-clone --master
</code></pre>

Om du ställer in `AUTOMATED_REGISTER="false"` bör det här kommandot migrera SAP HANA huvud-noden och gruppen som innehåller den virtuella IP-adressen till HN1-DB-1.

När migreringen är färdig ser utdata för status för sudo-datorer ut så här

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

SAP HANA resursen på HN1-dB-0 har stoppats. I det här fallet konfigurerar du HANA-instansen som sekundär genom att köra det här kommandot:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

Migreringen skapar plats begränsningar som behöver tas bort igen:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Övervaka tillståndet för HANA-resursen med "PC-status". När HANA har startats på HN1-dB-0 bör utdata se ut så här

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Testa Azure-avgränsnings agenten

> [!NOTE]
> Den här artikeln innehåller referenser till termen *slav*, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.  

Resurs tillstånd innan du startar testet:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Du kan testa konfigurationen av Azure-avgränsnings agenten genom att inaktivera nätverks gränssnittet på noden där SAP HANA körs som huvud server.
Se [artikeln om Red Hat-kunskaps artikel 79523](https://access.redhat.com/solutions/79523) för en beskrivning av hur du simulerar ett nätverks fel. I det här exemplet använder vi net_breaker-skriptet för att blockera all åtkomst till nätverket.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

Den virtuella datorn bör nu startas om eller stoppas beroende på kluster konfigurationen.
Om du ställer in `stonith-action` inställningen till av stoppas den virtuella datorn och resurserna migreras till den virtuella datorn som körs.

När du har startat den virtuella datorn igen går det inte att starta SAP HANA resursen som sekundär om du anger `AUTOMATED_REGISTER="false"` . I det här fallet konfigurerar du HANA-instansen som sekundär genom att köra det här kommandot:

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

Resurs tillstånd efter testet:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Testa en manuell redundansväxling

Resurs tillstånd innan du startar testet:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Du kan testa en manuell redundansväxling genom att stoppa klustret på noden HN1-dB-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Efter redundansväxlingen kan du starta klustret igen. Om du ställer in `AUTOMATED_REGISTER="false"` går det inte att starta SAP HANA resursen på noden HN1-dB-0 som sekundär. I det här fallet konfigurerar du HANA-instansen som sekundär genom att köra det här kommandot:

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

Resurs tillstånd efter testet:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-a-manual-failover"></a>Testa en manuell redundansväxling

Resurs tillstånd innan du startar testet:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Du kan testa en manuell redundansväxling genom att stoppa klustret på noden HN1-dB-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>


## <a name="next-steps"></a>Nästa steg

* [Azure Virtual Machines planera och implementera SAP][planning-guide]
* [Azure Virtual Machines distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* [SAP HANA VM Storage-konfigurationer](./hana-vm-operations-storage.md)