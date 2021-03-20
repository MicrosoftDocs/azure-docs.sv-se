---
title: Hög tillgänglighet för NFS på virtuella Azure-datorer på SLES | Microsoft Docs
description: Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 993baa521530ffa6a702f8324a1691850687c366
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668698"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server

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

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

Den här artikeln beskriver hur du distribuerar virtuella datorer, konfigurerar de virtuella datorerna, installerar kluster ramverket och installerar en NFS-server med hög tillgänglighet som kan användas för att lagra delade data i ett SAP-system med hög tillgänglighet.
I den här guiden beskrivs hur du konfigurerar en NFS-server med hög tillgänglighet som används av två SAP-system, NW1 och NW2. Namnen på resurserna (till exempel virtuella datorer, virtuella nätverk) i exemplet förutsätter att du har använt [mallen SAP File Server][template-file-server] med Resource prefixet **Prod**.


> [!NOTE]
> Den här artikeln innehåller referenser till villkoren *slav* och *Master*, termer som Microsoft inte längre använder. När villkoren tas bort från program varan tar vi bort dem från den här artikeln.

Läs följande SAP-anteckningar och dokument först

* SAP anmärkning [1928533], som har:
  * Lista över storlekar på virtuella Azure-datorer som stöds för distribution av SAP-program
  * Viktig kapacitets information för Azure VM-storlekar
  * Stöd för SAP-program och operativ system (OS) och databas kombinationer
  * Nödvändig SAP kernel-version för Windows och Linux på Microsoft Azure

* SAP NOTE [2015553] visar krav för SAP-program distributioner som stöds i Azure.
* SAP NOTE [2205917] har rekommenderade OS-inställningar för SUSE Linux Enterprise Server för SAP-program
* SAP NOTE [1944799] har SAP HANA rikt linjer för SUSE Linux Enterprise Server för SAP-program
* SAP NOTE [2178632] innehåller detaljerad information om alla övervaknings mått som rapporter ATS för SAP i Azure.
* SAP NOTE [2191498] har den version av SAP host agent som krävs för Linux i Azure.
* SAP NOTE [2243692] innehåller information om SAP-licensiering på Linux i Azure.
* SAP NOTE [1984787] innehåller allmän information om SUSE Linux Enterprise Server 12.
* SAP anmärkning [1999351] innehåller ytterligare felsöknings information för Azure Enhanced Monitoring-tillägget för SAP.
* [SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP-anteckningar för Linux.
* [Azure Virtual Machines planera och implementera SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux (den här artikeln)][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [SUSE Linux Enterprise hög tillgänglighet tillägg 12 SP3 Best Practices-guider][sles-hae-guides]
  * NFS-lagring med hög tillgänglighet med DRBD och pacemaker
* [SUSE Linux Enterprise Server för SAP-program 12 SP3 guider för bästa praxis][sles-for-sap-bp]
* [Viktig information om SUSEt hög tillgänglighets tillägg 12 SP3][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Översikt

För att uppnå hög tillgänglighet kräver SAP NetWeaver en NFS-server. NFS-servern är konfigurerad i ett separat kluster och kan användas av flera SAP-system.

![Översikt över SAP NetWeaver-hög tillgänglighet](./media/high-availability-guide-nfs/ha-suse-nfs.png)

NFS-servern använder ett dedikerat virtuellt värdnamn och virtuella IP-adresser för varje SAP-system som använder den här NFS-servern. I Azure krävs en belastningsutjämnare för att använda en virtuell IP-adress. I följande lista visas belastnings utjämningens konfiguration.        

* Konfiguration av klient del
  * IP-10.0.0.4 för NW1
  * IP-10.0.0.5 för NW2
* Server dels konfiguration
  * Anslutna till primära nätverks gränssnitt för alla virtuella datorer som ska ingå i NFS-klustret
* Avsöknings port
  * Port 61000 för NW1
  * Port 61001 för NW2
* Belastnings Utjämnings regler (om du använder Basic Load Balancer)
  * 2049 TCP för NW1
  * 2049 UDP för NW1
  * 2049 TCP för NW2
  * 2049 UDP för NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Konfigurera en NFS-server med hög tillgänglighet

Du kan antingen använda en Azure-mall från GitHub för att distribuera alla nödvändiga Azure-resurser, inklusive virtuella datorer, tillgänglighets uppsättning och belastningsutjämnare, eller så kan du distribuera resurserna manuellt.

### <a name="deploy-linux-via-azure-template"></a>Distribuera Linux via Azure-mall

Azure Marketplace innehåller en avbildning för SUSE Linux Enterprise Server för SAP-program 12 som du kan använda för att distribuera nya virtuella datorer.
Du kan använda en av snabb starts mallarna på GitHub för att distribuera alla nödvändiga resurser. Mallen distribuerar de virtuella datorerna, belastningsutjämnaren, tillgänglighets uppsättningen osv. Följ de här stegen för att distribuera mallen:

1. Öppna [fil Server mal len SAP][template-file-server] i Azure Portal   
1. Ange följande parametrar
   1. Resource prefix  
      Ange prefixet som du vill använda. Värdet används som prefix för de resurser som distribueras.
   2. Antal SAP-system  
      Ange det antal SAP-system som ska använda den här fil servern. Detta distribuerar den nödvändiga mängden klient konfigurations inställningar, belastnings Utjämnings regler, avsöknings portar, diskar osv.
   3. OS-typ  
      Välj en av Linux-distributionerna. I det här exemplet väljer du SLES 12
   4. Administratörens användar namn och administratörs lösen ord  
      En ny användare skapas som kan användas för att logga in på datorn.
   5. Undernät-ID  
      Om du vill distribuera den virtuella datorn till ett befintligt VNet där du har angett ett undernät som har definierats för den virtuella datorn ska du namnge ID: t för det aktuella under nätet. ID: t ser vanligt vis ut som/Subscriptions/**&lt; PRENUMERATIONS &gt; -ID**/ResourceGroups/**&lt; resurs grupp namn &gt;**/providers/Microsoft.Network/virtualNetworks/**&lt; virtuellt nätverks namn &gt;**/subnets/**&lt; under näts namn &gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Distribuera Linux manuellt via Azure Portal

Du måste först skapa de virtuella datorerna för det här NFS-klustret. Därefter skapar du en belastningsutjämnare och använder de virtuella datorerna i backend-poolerna.

1. Skapa en resursgrupp
1. Skapa ett virtuellt nätverk
1. Skapa en tillgänglighets uppsättning  
   Ange Max uppdaterings domän
1. Skapa den virtuella datorn 1 Använd minst SLES4SAP 12 SP3, i det här exemplet används SLES4SAP 12 SP3 BYOS image SLES for SAP Applications 12 SP3 (BYOS)  
   Välj den tillgänglighets uppsättning som skapades tidigare  
1. Skapa virtuell dator 2 Använd minst SLES4SAP 12 SP3, i det här exemplet SLES4SAP 12 SP3 BYOS image  
   SLES för SAP-program 12 SP3 (BYOS) används  
   Välj den tillgänglighets uppsättning som skapades tidigare  
1. Lägg till en datadisk för varje SAP-system till båda virtuella datorerna.
1. Skapa en Load Balancer (intern). Vi rekommenderar [standard Load Balancer](../../../load-balancer/load-balancer-overview.md).  
   1. Följ de här anvisningarna för att skapa en standard Load Balancer:
      1. Skapa IP-adresser för klient delen
         1. IP-10.0.0.4 för NW1
            1. Öppna belastningsutjämnaren, Välj klient delens IP-pool och klicka på Lägg till
            1. Ange namnet på den nya IP-poolen för klient delen (till exempel **NW1-frontend**)
            1. Ange tilldelningen till statisk och ange IP-adressen (till exempel **10.0.0.4**)
            1. Klicka på OK
         1. IP-10.0.0.5 för NW2
            * Upprepa stegen ovan för NW2
      1. Skapa backend-pooler
         1. Anslutna till primära nätverks gränssnitt för alla virtuella datorer som ska ingå i NFS-klustret
            1. Öppna belastningsutjämnaren, Välj backend-pooler och klicka på Lägg till
            1. Ange namnet på den nya backend-poolen (till exempel **NW-backend**)
            1. Välj Virtual Network
            1. Klicka på Lägg till en virtuell dator
            1. Välj de virtuella datorerna i NFS-klustret och deras IP-adresser.
            1. Klicka på Add (Lägg till).
      1. Skapa hälso avsökningar
         1. Port 61000 för NW1
            1. Öppna belastningsutjämnaren, Välj hälso avsökningar och klicka på Lägg till
            1. Ange namnet på den nya hälso avsökningen (till exempel **NW1 – HP**)
            1. Välj TCP som protokoll, Port 610 **00**, Behåll intervallet 5 och tröskelvärde 2
            1. Klicka på OK
         1. Port 61001 för NW2
            * Upprepa stegen ovan för att skapa en hälso avsökning för NW2
      1. Belastnings Utjämnings regler
         1. Öppna belastningsutjämnaren, Välj belastnings Utjämnings regler och klicka på Lägg till
         1. Ange namnet på den nya belastnings Utjämnings regeln (till exempel **NW1 – lb**)
         1. Välj IP-adressen för klient delen, backend-poolen och hälso avsökningen som du skapade tidigare (till exempel **NW1-frontend**. **NW-backend** och **NW1 – HP**)
         1. Välj **ha-portar**.
         1. Öka tids gränsen för inaktivitet till 30 minuter
         1. **Se till att aktivera flytande IP**
         1. Klicka på OK
         * Upprepa stegen ovan för att skapa belastnings Utjämnings regeln för NW2
   1. Om scenariot kräver en grundläggande belastningsutjämnare följer du dessa anvisningar:
      1. Skapa IP-adresser för klient delen
         1. IP-10.0.0.4 för NW1
            1. Öppna belastningsutjämnaren, Välj klient delens IP-pool och klicka på Lägg till
            1. Ange namnet på den nya IP-poolen för klient delen (till exempel **NW1-frontend**)
            1. Ange tilldelningen till statisk och ange IP-adressen (till exempel **10.0.0.4**)
            1. Klicka på OK
         1. IP-10.0.0.5 för NW2
            * Upprepa stegen ovan för NW2
      1. Skapa backend-pooler
         1. Anslutna till primära nätverks gränssnitt för alla virtuella datorer som ska ingå i NFS-klustret
            1. Öppna belastningsutjämnaren, Välj backend-pooler och klicka på Lägg till
            1. Ange namnet på den nya backend-poolen (till exempel **NW-backend**)
            1. Klicka på Lägg till en virtuell dator
            1. Välj den tillgänglighets uppsättning som du skapade tidigare
            1. Välj virtuella datorer för NFS-klustret
            1. Klicka på OK
      1. Skapa hälso avsökningar
         1. Port 61000 för NW1
            1. Öppna belastningsutjämnaren, Välj hälso avsökningar och klicka på Lägg till
            1. Ange namnet på den nya hälso avsökningen (till exempel **NW1 – HP**)
            1. Välj TCP som protokoll, Port 610 **00**, Behåll intervallet 5 och tröskelvärde 2
            1. Klicka på OK
         1. Port 61001 för NW2
            * Upprepa stegen ovan för att skapa en hälso avsökning för NW2
      1. Belastnings Utjämnings regler
         1. 2049 TCP för NW1
            1. Öppna belastningsutjämnaren, Välj belastnings Utjämnings regler och klicka på Lägg till
            1. Ange namnet på den nya belastnings Utjämnings regeln (till exempel **NW1 – lb-2049**)
            1. Välj IP-adressen för klient delen, backend-poolen och hälso avsökningen som du skapade tidigare (till exempel **NW1-frontend**)
            1. Behåll protokollets **TCP**, ange port **2049**
            1. Öka tids gränsen för inaktivitet till 30 minuter
            1. **Se till att aktivera flytande IP**
            1. Klicka på OK
         1. 2049 UDP för NW1
            * Upprepa stegen ovan för port 2049 och UDP för NW1
         1. 2049 TCP för NW2
            * Upprepa stegen ovan för port 2049 och TCP för NW2
         1. 2049 UDP för NW2
            * Upprepa stegen ovan för port 2049 och UDP för NW2

> [!IMPORTANT]
> Flytande IP stöds inte på en sekundär NIC-IP-konfiguration i belastnings Utjämnings scenarier. Mer information finns i [begränsningar för Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Om du behöver ytterligare IP-adress för den virtuella datorn distribuerar du ett andra nätverkskort.  

> [!Note]
> När virtuella datorer utan offentliga IP-adresser placeras i backend-poolen för intern (ingen offentlig IP-adress) standard Azure-belastningsutjämnare, kommer det inte att finnas någon utgående Internet anslutning, om inte ytterligare konfiguration utförs för att tillåta routning till offentliga slut punkter. Mer information om hur du uppnår utgående anslutningar finns i Översikt över [offentliga slut punkter för Virtual Machines med Azure standard Load Balancer i SAP-scenarier med hög tillgänglighet](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

> [!IMPORTANT]
> Aktivera inte TCP-tidsstämplar på virtuella Azure-datorer som placerats bakom Azure Load Balancer. Om du aktiverar TCP-tidsstämplar kommer hälso avsökningarna att Miss skadas. Ange parametern **net.IPv4.tcp_timestamps** till **0**. Mer information finns i [Load Balancer hälso avsökningar](../../../load-balancer/load-balancer-custom-probe-overview.md).

### <a name="create-pacemaker-cluster"></a>Skapa pacemaker-kluster

Följ stegen i [Konfigurera pacemaker på SUSE Linux Enterprise Server i Azure](high-availability-guide-suse-pacemaker.md) för att skapa ett grundläggande pacemaker-kluster för den här NFS-servern.

### <a name="configure-nfs-server"></a>Konfigurera NFS-server

Följande objekt har prefixet **[A]** -tillämpligt för alla noder, **[1]** , som endast gäller nod 1 eller **[2]** -gäller endast nod 2.

1. **[A]** namn matchning för värdnamn

   Du kan antingen använda en DNS-server eller ändra/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adress och värdnamn i följande kommandon

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Infoga följande rader i/etc/hosts. Ändra IP-adress och värdnamn för att matcha din miljö
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** aktivera NFS-server

   Skapa rot-NFS-export posten

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** installera drbd-komponenter

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** skapa en partition för drbd-enheterna

   Lista alla tillgängliga data diskar

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Exempel på utdata
   
   ```
   lun0  lun1
   ```

   Skapa partitioner för varje data disk

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]** skapa LVM-konfigurationer

   Lista alla tillgängliga partitioner

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Exempel på utdata
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Skapa LVM-volymer för varje partition

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]** konfigurera drbd

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Kontrol lera att filen drbd. conf innehåller följande två rader

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Ändra den globala drbd-konfigurationen

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Lägg till följande poster i avsnittet hanterare och net.

   <pre><code>global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]** skapa NFS-drbd enheter

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Infoga konfigurationen för den nya drbd-enheten och avsluta

   <pre><code>resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Infoga konfigurationen för den nya drbd-enheten och avsluta

   <pre><code>resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   Skapa drbd-enheten och starta den

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** hoppa över första synkronisering

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** ange den primära noden

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** vänta tills de nya drbd-enheterna har synkroniserats

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** skapa fil system på drbd-enheterna

   <pre><code>sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]** installations drbd Split-hjärna-identifiering

   När du använder drbd för att synkronisera data från en värd till en annan så kallas det delade hjärna. En delad hjärna är ett scenario där båda klusternoderna befordrade att drbd-enheten är primär och slutade synkronisera. Det kan vara en sällsynt situation, men du vill ändå hantera och lösa en delad hjärna så snabbt som möjligt. Det är därför viktigt att bli informerad när en delad hjärna har inträffat.

   Läs [den officiella drbd-dokumentationen](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-split-brain-notification) om hur du konfigurerar en delad hjärna-avisering.

   Det är också möjligt att automatiskt återställa från ett delat hjärna-scenario. Om du vill ha mer information läser du [Automatisk delning av hjärna återställnings principer](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Konfigurera kluster ramverk

1. **[1]** Lägg till NFS-drbd enheter för SAP system NW1 i kluster konfigurationen

   > [!IMPORTANT]
   > De senaste testerna visade situationer, där netcat slutar svara på begär Anden på grund av en efter släpning och dess begränsning av hantering av endast en anslutning. Netcat-resursen slutar lyssna på Azure Load Balancer-begäranden och den flytande IP-adressen blir otillgänglig.  
   > För befintliga pacemaker-kluster rekommenderar vi att du tidigare ersätter netcat med socat. För närvarande rekommenderar vi att du använder Azure-lb Resource agent, som är en del av paketets resurs agenter, med följande paket versions krav:
   > - För SLES 12 SP4/SP5 måste versionen vara minst resurs agenter-4.3.018. a7fb5035-3.30.1.  
   > - För SLES 15/15 SP1 måste versionen vara minst resurs agenter-4.3.0184.6 ee15eb2-4.13.1.  
   >
   > Observera att ändringen kräver kortare stillestånds tid.  
   > För befintliga pacemaker-kluster, om konfigurationen redan har ändrats för att använda socat enligt beskrivningen i [azure Load-Balancer Avkännings härdning](https://www.suse.com/support/kb/doc/?id=7024128), finns det inget krav på att växla direkt till Azure-lb Resource agent.

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs azure-lb port=<b>61000</b>
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** Lägg till NFS-drbd enheter för SAP system NW2 i kluster konfigurationen

   <pre><code># Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs azure-lb port=<b>61001</b>
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

   `crossmnt`Alternativet i `exportfs` kluster resurserna finns i vår dokumentation för bakåtkompatibilitet med äldre SLES-versioner.  

1. **[1]** inaktivera underhålls läge
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Nästa steg

* [Installera SAP-ASCS och-databasen](high-availability-guide-suse.md)
* [Azure Virtual Machines planera och implementera SAP][planning-guide]
* [Azure Virtual Machines distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Information om hur du upprättar hög tillgänglighet och planerar för haveri beredskap för SAP HANA på virtuella Azure-datorer finns i [hög tillgänglighet för SAP HANA på Azure-Virtual Machines (VM)][sap-hana-ha]
