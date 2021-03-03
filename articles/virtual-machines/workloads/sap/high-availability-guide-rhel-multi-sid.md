---
title: Virtuella Azure-datorer med hög tillgänglighet för SAP NW på RHEL multi-SID-guide | Microsoft Docs
description: Upprätta hög tillgänglighet för SAP NW på Azure Virtual Machines (VM) RHEL multi-SID.
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
ms.date: 01/11/2021
ms.author: radeltch
ms.openlocfilehash: ec2121754a24a44288c158e630a4e84219c744e3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676921"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på Red Hat Enterprise Linux för SAP-program med flera SID-guide

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Den här artikeln beskriver hur du distribuerar flera SAP NetWeaver-system med hög tillgänglighet (dvs. flera SID) i ett kluster med två noder på virtuella Azure-datorer med Red Hat Enterprise Linux för SAP-program.  

I exempel konfigurationerna är installations kommandon osv. tre SAP NetWeaver 7,50-system distribueras i ett enda kluster med två noder med hög tillgänglighet. SAP-systemets sid är:
* **NW1**: ASCS instance Number **00** och Virtual Host Name **msnw1ascs**; ERS instance Number **02** och Virtual Host Name **msnw1ers**.  
* **NW2**: ASCS instance Number **10** och Virtual hostname **msnw2ascs**; ERS-instans nummer **12** och virtuellt värd namn **msnw2ers**.  
* **NW3**: ASCS instance Number **20** och Virtual hostname **msnw3ascs**; ERS-instans nummer **22** och virtuellt värd namn **msnw3ers**.  

Artikeln behandlar inte databas skiktet och distributionen av SAP NFS-resurser. I exemplen i den här artikeln använder vi [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)  Volume **sapMSID** för NFS-resurser, förutsatt att volymen redan har distribuerats. Vi antar också att Azure NetApp Files volym distribueras med NFSv3-protokollet och att följande fil Sök vägar finns för kluster resurserna för ASCS-och ERS-instanserna för SAP Systems NW1, NW2 och NW3:  

* volym sapMSID (nfs://10.42.0.4/sapmnt<b>NW1</b>)
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ASCs)
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>sys)
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ERS)
* volym sapMSID (nfs://10.42.0.4/sapmnt<b>NW2</b>)
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ASCs)
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>sys)
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ERS)
* volym sapMSID (nfs://10.42.0.4/sapmnt<b>NW3</b>)
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ASCs)
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ERS)

Innan du börjar, se följande SAP-anteckningar och dokument först:

* SAP anmärkning [1928533], som har:
  * Lista över storlekar på virtuella Azure-datorer som stöds för distribution av SAP-program
  * Viktig kapacitets information för Azure VM-storlekar
  * Stöd för SAP-program och operativ system (OS) och databas kombinationer
  * Nödvändig SAP kernel-version för Windows och Linux på Microsoft Azure
* [Azure NetApp Files dokumentation][anf-azure-doc]
* SAP NOTE [2015553] visar krav för SAP-program distributioner som stöds i Azure.
* SAP NOTE [2002167] har rekommenderade OS-inställningar för Red Hat Enterprise Linux
* SAP NOTE [2009879] har SAP HANA rikt linjer för Red Hat Enterprise Linux
* SAP NOTE [2178632] innehåller detaljerad information om alla övervaknings mått som rapporter ATS för SAP i Azure.
* SAP NOTE [2191498] har den version av SAP host agent som krävs för Linux i Azure.
* SAP NOTE [2243692] innehåller information om SAP-licensiering på Linux i Azure.
* SAP anmärkning [1999351] innehåller ytterligare felsöknings information för Azure Enhanced Monitoring-tillägget för SAP.
* [SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP-anteckningar för Linux.
* [Azure Virtual Machines planera och implementera SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [SAP-NetWeaver i pacemaker-kluster](https://access.redhat.com/articles/3150081)
* Allmän dokumentation om RHEL
  * [Översikt över Add-On med hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Add-On administration med hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Add-On referens för hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurera ASCS/ERS för SAP NetWeaver med fristående resurser i RHEL 7,5](https://access.redhat.com/articles/3569681)
  * [Konfigurera SAP S/4HANA ASCS/ERS med fristående server 2 (ENSA2) i pacemaker på RHEL](https://access.redhat.com/articles/3974941)
* Azure-speciell RHEL-dokumentation:
  * [Support principer för RHEL-kluster med hög tillgänglighet – Microsoft Azure Virtual Machines som kluster medlemmar](https://access.redhat.com/articles/3131341)
  * [Installera och konfigurera en Red Hat Enterprise Linux 7,4 (och senare) High-Availability kluster på Microsoft Azure](https://access.redhat.com/articles/3252491)
* [NetApp SAP-program på Microsoft Azure med Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Översikt

De virtuella datorerna som ingår i klustret måste ha en storlek för att kunna köra alla resurser, om redundans inträffar. Varje SAP-SID kan växlas över oberoende av varandra i kluster med hög tillgänglighet för flera-SID.  

För att uppnå hög tillgänglighet kräver SAP NetWeaver hög tillgängliga resurser. I den här dokumentationen presenterar vi exemplen med SAP-resurserna som distribuerats på [Azure NETAPP Files NFS-volymer](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Det är också möjligt att vara värd för resurserna i [GlusterFS-kluster](./high-availability-guide-rhel-glusterfs.md)med hög tillgänglighet som kan användas av flera SAP-system.  

![Översikt över SAP NetWeaver-hög tillgänglighet](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> Stödet för multi-SID-klustring av SAP ASCS/ERS med Red Hat Linux som gäst operativ system i virtuella Azure-datorer är begränsat till **fem** SAP-sid i samma kluster. Varje nytt SID ökar komplexiteten. Det finns **inte stöd** för en blandning av SAP-server 1 och transaktionskö för replikering Server 2 i samma kluster. Multi-SID-klustring beskriver installationen av flera SAP ASCS/ERS-instanser med olika sid i ett pacemaker-kluster. För närvarande stöds inte Multi-SID-klustring för ASCS/ERS.  

> [!TIP]
> Multi-SID-klustring av SAP ASCS/ERS är en lösning med bättre komplexitet. Det är mer komplicerat att implementera. Det innebär också en högre administrativ ansträngning vid körning av underhålls aktiviteter (t. ex. operativ system uppdatering). Innan du börjar den faktiska implementeringen tar du tid att planera distributionen och alla komponenter som virtuella datorer, NFS monterar, VIP, konfigurationer för belastnings utjämning och så vidare.  

SAP NetWeaver ASCS, SAP NetWeaver SCS och SAP NetWeaver ERS använder virtuella värdnamn och virtuella IP-adresser. I Azure krävs en belastningsutjämnare för att använda en virtuell IP-adress. Vi rekommenderar att du använder [standard belastnings utjämning](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

I följande lista visas konfigurationen av (A) SCS-och ERS-belastningsutjämnaren för det här kluster exemplet med flera säkerhets identifierare med tre SAP-system. Du behöver separata IP-adresser för klient del, hälso avsökningar och regler för belastnings utjämning för varje ASCS-och ERS-instans för varje säkerhets-ID. Tilldela alla virtuella datorer som är en del av ASCS/ASCS-klustret till en backend-pool för en enda ILB.  

### <a name="ascs"></a>En SCS

* Konfiguration av klient del
  * IP-adress för NW1:10.3.1.50
  * IP-adress för NW2:10.3.1.52
  * IP-adress för NW3:10.3.1.54

* Avsöknings portar
  * Port 620 <strong> &lt; nr &gt;</strong>, därför för NW1, NW2 och NW3 avsöknings portar 620 **00**, 620 **10** och 620 **20**
* Belastnings Utjämnings regler – skapa en för varje instans, det vill säga NW1/ASCS, NW2/ASCS och NW3/ASCS.
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
  * IP-adress för NW1-10.3.1.51
  * IP-adress för NW2-10.3.1.53
  * IP-adress för NW3-10.3.1.55

* Avsöknings port
  * Port 621 <strong> &lt; nr &gt;</strong>, därför för NW1, NW2 och N3 avsöknings portar 621 **02**, 621 **12** och 621 **22**
* Belastnings Utjämnings regler – skapa en för varje instans, det vill säga NW1/ERS, NW2/ERS och NW3/ERS.
  * Om du använder Standard Load Balancer väljer du **ha-portar**
  * Om du använder grundläggande Load Balancer skapa belastnings Utjämnings regler för följande portar
    * 32<strong> &lt; nr &gt; </strong> TCP
    * 33<strong> &lt; nr &gt; </strong> TCP
    * 5<strong> &lt; nr &gt; </strong>13 TCP
    * 5<strong> &lt; nr &gt; </strong>14 TCP
    * 5<strong> &lt; nr &gt; </strong>16 TCP

* Server dels konfiguration
  * Anslutna till primära nätverks gränssnitt för alla virtuella datorer som ska ingå i (A) SCS/ERS-kluster

> [!IMPORTANT]
> Flytande IP stöds inte på en sekundär NIC-IP-konfiguration i belastnings Utjämnings scenarier. Mer information finns i [begränsningar för Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Om du behöver ytterligare IP-adress för den virtuella datorn distribuerar du ett andra nätverkskort.  

> [!Note]
> När virtuella datorer utan offentliga IP-adresser placeras i backend-poolen för intern (ingen offentlig IP-adress) standard Azure-belastningsutjämnare, kommer det inte att finnas någon utgående Internet anslutning, om inte ytterligare konfiguration utförs för att tillåta routning till offentliga slut punkter. Mer information om hur du uppnår utgående anslutningar finns i Översikt över [offentliga slut punkter för Virtual Machines med Azure standard Load Balancer i SAP-scenarier med hög tillgänglighet](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

> [!IMPORTANT]
> Aktivera inte TCP-tidsstämplar på virtuella Azure-datorer som placerats bakom Azure Load Balancer. Om du aktiverar TCP-tidsstämplar kommer hälso avsökningarna att Miss skadas. Ange parametern **net.IPv4.tcp_timestamps** till **0**. Mer information finns i [Load Balancer hälso avsökningar](../../../load-balancer/load-balancer-custom-probe-overview.md).

## <a name="sap-shares"></a>SAP-resurser

SAP NetWeaver kräver delad lagring för transport-, profil katalogen och så vidare. För SAP-system med hög tillgänglighet är det viktigt att ha resurser med hög tillgänglighet. Du måste bestämma arkitekturen för dina SAP-resurser. Ett alternativ är att distribuera resurserna på [Azure NETAPP Files NFS-volymer](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  Med Azure NetApp Files får du inbyggd hög tillgänglighet för SAP NFS-resurserna.

Ett annat alternativ är att bygga [GlusterFS på virtuella Azure-datorer på Red Hat Enterprise Linux för SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md), som kan delas mellan flera SAP-system. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Distribuera det första SAP-systemet i klustret

Nu när du har bestämt dig för arkitekturen för SAP-resurserna distribuerar du det första SAP-systemet i klustret enligt motsvarande dokumentation.

* Om du använder Azure NetApp Files NFS-volymer följer du [virtuella Azure-datorer med hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux med Azure NetApp Files för SAP-program](./high-availability-guide-rhel-netapp-files.md)  
* Om du använder GlusterFS-kluster följer du [GlusterFS på virtuella Azure-datorer på Red Hat Enterprise Linux för SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md).  

Dokumenten som anges ovan vägleder dig genom stegen för att förbereda den infrastruktur som krävs, skapa klustret, förbereda operativ systemet för att köra SAP-programmet.  

> [!TIP]
> Testa alltid växlings funktionen för klustret efter att det första systemet har distribuerats, innan du lägger till ytterligare SAP-sid i klustret. På så sätt vet du att kluster funktionerna fungerar, innan du lägger till komplexiteten för ytterligare SAP-system i klustret.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>Distribuera ytterligare SAP-system i klustret

I det här exemplet antar vi att system- **NW1** redan har distribuerats i klustret. Vi visar hur du distribuerar i kluster SAP-systemen **NW2** och **NW3**. 

Följande objekt har prefixet **[A]** -tillämpligt för alla noder, **[1]** , som endast gäller nod 1 eller **[2]** -gäller endast nod 2.

### <a name="prerequisites"></a>Förutsättningar 

> [!IMPORTANT]
> Innan du följer anvisningarna för att distribuera ytterligare SAP-system i klustret, följer du anvisningarna för att distribuera det första SAP-systemet i klustret, eftersom det finns steg som bara behövs under den första system distributionen.  

Den här dokumentationen förutsätter att:
* Pacemaker-klustret har redan kon figurer ATS och körs.  
* Minst ett SAP-system (ASCS/ERS-instans) har redan distribuerats och körs i klustret.  
* Funktionen för redundanskluster har testats.  
* NFS-resurserna för alla SAP-system distribueras.  

### <a name="prepare-for-sap-netweaver-installation"></a>Förbered för SAP NetWeaver-installation

1. Lägg till konfiguration för det nyligen distribuerade systemet (det vill säga **NW2**, **NW3**) till den befintliga Azure Load Balancer genom att följa anvisningarna [Distribuera Azure Load Balancer manuellt via Azure Portal](./high-availability-guide-rhel-netapp-files.md#deploy-linux-manually-via-azure-portal). Justera IP-adresserna, hälso avsöknings portarna, belastnings Utjämnings regler för din konfiguration.  

2. **[A]** matchning av installations namn för ytterligare SAP-system. Du kan antingen använda DNS-servern eller ändra `/etc/hosts` på alla noder. Det här exemplet visar hur du använder `/etc/hosts` filen.  Anpassa IP-adresserna och värd namnen till din miljö. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
   ```

3. **[A]** skapa delade kataloger för de ytterligare **NW2** -och **NW3** SAP-system som du distribuerar till klustret. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** Lägg till monterings poster för fil systemen/Sapmnt/sid och/usr/SAP/sid/sys för de ytterligare SAP-system som du distribuerar till klustret. I det här exemplet **NW2** och **NW3**.  

   Uppdatera filen `/etc/fstab` med fil systemen för de ytterligare SAP-system som du distribuerar till klustret.  

   * Om du använder Azure NetApp Files följer du anvisningarna [här](./high-availability-guide-rhel-netapp-files.md#prepare-for-sap-netweaver-installation)  
   * Om du använder GlusterFS-kluster följer du anvisningarna [här](./high-availability-guide-rhel.md#prepare-for-sap-netweaver-installation)  

### <a name="install-ascs--ers"></a>Installera ASCS/ERS

1. Skapa de virtuella IP-och hälso avsöknings kluster resurserna för ASCS-instanserna av de ytterligare SAP-system som du distribuerar till klustret. Exemplet som visas här är för **NW2** och **NW3** ASCS, med hjälp av NFS på Azure NetApp Files volymer med NFSv3-protokollet.  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   Kontrol lera att klustrets status är OK och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.  

2. **[1]** installera SAP NetWeaver ASCS  

   Installera SAP NetWeaver ASCS som root med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för belastningsutjämnarens frontend-konfiguration för ASCS. För till exempel system **NW2**, är det virtuella värd namnet <b>msnw2ascs</b>, <b>10.3.1.52</b> och det instans nummer som du använde för avsökningen av belastningsutjämnaren, till exempel <b>10</b>. För system- **NW3** är det virtuella värd namnet <b>msnw3ascs</b>, <b>10.3.1.54</b> och det instans nummer som du använde för avsökningen av belastningsutjämnaren, till exempel <b>20</b>. Anteckna vilken klusternod som du installerade ASCS för varje SAP SID.  

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta att en användare som inte är rot användare ansluter till sapinst. Du kan använda parameter SAPINST_USE_HOSTNAME för att installera SAP med hjälp av ett virtuellt värdnamn.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   Om installationen Miss lyckas med att skapa en undermapp i/usr/SAP/**sid**/ASCS **instans #**, kan du försöka att ange ägare till **sid** adm och grupp till sapsys för ASCS-**instansen #** och försöka igen.

3. **[1]** skapa en virtuell IP-och hälso avsöknings kluster resurser för ers-instansen av det extra SAP-system som du distribuerar till klustret. Exemplet som visas här är för **NW2** och **NW3** ers, med hjälp av NFS på Azure NetApp Files volymer med NFSv3-protokollet.  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   Kontrol lera att klustrets status är OK och att alla resurser har startats.  

   Se sedan till att resurserna för den nyligen skapade ERS-gruppen körs på klusternoden, i motsatt till klusternoden där ASCS-instansen för samma SAP-system installerades.  Om t. ex. NW2 ASCS har installerats på `rhelmsscl1` kontrollerar du att gruppen NW2 ers körs på `rhelmsscl2` .  Du kan migrera gruppen NW2 ERS till `rhelmsscl2` genom att köra följande kommando för en av kluster resurserna i gruppen: 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** installera SAP NetWeaver ers

   Installera SAP NetWeaver ERS som rot på den andra noden med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för belastningsutjämnarens frontend-konfiguration för ERS. Till exempel för system **NW2** blir det virtuella värd namnet <b>msnw2ers</b>, <b>10.3.1.53</b> och det instans nummer som du använde för avsökningen av belastningsutjämnaren, till exempel <b>12</b>. För system- **NW3** är det virtuella värd namnet <b>msnw3ers</b>, <b>10.3.1.55</b> och det instans nummer som du använde för avsökningen av belastningsutjämnaren, till exempel <b>22</b>. 

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta att en användare som inte är rot användare ansluter till sapinst. Du kan använda parameter SAPINST_USE_HOSTNAME för att installera SAP med hjälp av ett virtuellt värdnamn.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Använd SWPM SP 20 PL 05 eller högre. Lägre versioner ställer inte in behörigheterna korrekt och installationen kommer inte att fungera.

   Om installationen Miss lyckas med att skapa en undermapp i/usr/SAP/**NW2**/ers **instance #**, kan du försöka att ange ägare till **sid** adm och gruppen till sapsys för mappen ers **instance #** och försök igen.

   Om det var nödvändigt för att migrera ERS-gruppen för det nyligen distribuerade SAP-systemet till en annan klusternod, glöm inte att ta bort plats begränsningen för ERS-gruppen. Du kan ta bort begränsningen genom att köra följande kommando (exemplet anges för SAP Systems **NW2** och **NW3**). Se till att ta bort tillfälliga begränsningar för samma resurs som du använde i kommandot för att flytta kluster gruppen ERS.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** anpassa ASCS/SCS-och ers-instans profiler för de nyligen installerade SAP-systemen. Exemplet som visas nedan är för NW2. Du måste anpassa ASCS-/SCS-och ERS-profilerna för alla SAP-instanser som läggs till i klustret.  
 
   * ASCS/SCS-profil

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter, if using ENSA1
      enque/encni/set_so_keepalive = true
      ```

     Se till att `keepalive` OS-parametrarna är inställda enligt beskrivningen i SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736)för både ENSA1 och ENSA2.    

   * ERS-profil

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** uppdatera/usr/SAP/sapservices-filen

   För att förhindra att instanserna startas av sapinit-startskriptet måste alla instanser som hanteras av pacemaker vara kommenterade från `/usr/sap/sapservices` filen.  Exemplet som visas nedan är för SAP-system **NW2** och **NW3**.  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** skapa SAP-kluster resurserna för det nyligen installerade SAP-systemet.  

   Om du använder ENSA1 (enqueue Server 1 Architecture) definierar du resurserna för SAP Systems **NW2** och **NW3** enligt följande:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   SAP introducerade stöd för att köa Server 2, inklusive replikering, från SAP NW 7,52. Från och med ABAP Platform 1809 installeras som standard Server 2. Se SAP NOTE [2630416](https://launchpad.support.sap.com/#/notes/2630416) för Server 2-stöd.
   Om du använder [ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)(Queue server 2 Architecture) definierar du resurserna för SAP Systems **NW2** och **NW3** enligt följande:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Om du uppgraderar från en äldre version och växlar till att köa Server 2, se SAP anmärkning [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   > [!NOTE]
   > Tids gränsen i konfigurationen ovan är bara exempel och kan behöva anpassas till den angivna SAP-konfigurationen. 

   Kontrol lera att klustrets status är OK och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.
   I följande exempel visas status för kluster resurser, efter att SAP-systemen **NW2** och **NW3** har lagts till i klustret. 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** Lägg till brand Väggs regler för ASCS och ers på båda noderna.  I exemplet nedan visas brand Väggs reglerna för både SAP-systemen **NW2** och **NW3**.  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3212/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3212/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3222/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3222/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>Fortsätt med SAP-installationen 

Slutför din SAP-installation genom att:

* [Förbereda dina SAP NetWeaver-programservrar](./high-availability-guide-rhel-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Installera en DBMS-instans](./high-availability-guide-rhel-netapp-files.md#install-database)
* [Installera en primär SAP-Programserver](./high-availability-guide-rhel-netapp-files.md#sap-netweaver-application-server-installation)
* Installera en eller flera ytterligare SAP-programinstanser

## <a name="test-the-multi-sid-cluster-setup"></a>Testa installationen av multi-SID-kluster

Följande test är en delmängd av test fallen i Best Practices-guider för Red Hat. De ingår för din bekvämlighet. För en fullständig lista över kluster test, referera till följande dokumentation:

* Om du använder Azure NetApp Files NFS-volymer följer du [virtuella Azure-datorer med hög tillgänglighet för SAP NetWeaver på RHEL med Azure NetApp Files för SAP-program](./high-availability-guide-rhel-netapp-files.md)
* Om du använder hög `GlusterFS` tillgänglighet följer du [virtuella Azure-datorer med hög tillgänglighet för SAP NETWEAVER på RHEL för SAP-program](./high-availability-guide-rhel.md).  

Läs alltid Best Practices-guider för Red Hat och utför alla ytterligare tester som kan ha lagts till.  
De tester som presenteras finns i två noder, multi-SID-kluster med tre SAP-system installerade.  

1. Migrera ASCS-instansen manuellt. Exemplet visar migrering av ASCS-instansen för SAP system NW3.

   Resurs tillstånd innan du startar testet:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Kör följande kommandon som rot för att migrera NW3 ASCS-instansen.

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   Resurs tillstånd efter testet:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. Simulera Node-krasch

   Resurs tillstånd innan du startar testet:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   Kör följande kommando som rot på en nod, där minst en ASCS-instans körs. I det här exemplet körde vi kommandot på `rhelmsscl1` , där ASCS-instanserna för NW1, NW2 och NW3 körs.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   Statusen efter testet och efter att noden har startats igen bör se ut så här.

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Om det finns meddelanden om misslyckade resurser rensar du statusen för de misslyckade resurserna. Exempel:

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>Nästa steg

* [Azure Virtual Machines planera och implementera SAP][planning-guide]
* [Azure Virtual Machines distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Information om hur du upprättar hög tillgänglighet och planerar för haveri beredskap för SAP HANA på virtuella Azure-datorer finns i [hög tillgänglighet för SAP HANA på Azure-Virtual Machines (VM)][sap-hana-ha]