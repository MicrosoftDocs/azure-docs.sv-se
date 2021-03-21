---
title: Virtuella Azure-datorer med hög tillgänglighet för SAP NW på RHEL med Azure NetApp Files | Microsoft Docs
description: Upprätta hög tillgänglighet för SAP NW på Azure Virtual Machines (VM) RHEL med Azure NetApp Files.
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
ms.openlocfilehash: e652d1374db12d797dc4505f07350e6e110d6408
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674440"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux med Azure NetApp Files för SAP-program

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

Den här artikeln beskriver hur du distribuerar virtuella datorer, konfigurerar de virtuella datorerna, installerar kluster ramverket och installerar ett SAP NetWeaver 7,50-system med hög tillgänglighet med hjälp av [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).
I exemplen konfigurationer, installations kommandon osv. ASCS-instansen är Number 00, ERS-instansen är Number 01, primär program instansen (PAS) är 02 och program instansen (AAS) är 03. SAP-system-ID-QAS används. 

Databas lagret beskrivs inte i detalj i den här artikeln.  

Läs följande SAP-anteckningar och dokument först:

* [Azure NetApp Files dokumentation][anf-azure-doc] 
* SAP anmärkning [1928533], som har:
  * Lista över storlekar på virtuella Azure-datorer som stöds för distribution av SAP-program
  * Viktig kapacitets information för Azure VM-storlekar
  * Stöd för SAP-program och operativ system (OS) och databas kombinationer
  * Nödvändig SAP kernel-version för Windows och Linux på Microsoft Azure

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
  * [Konfigurera SAP S/4HANA ASCS/ERS med fristående server 2 (ENSA2) i pacemaker på RHEL ](https://access.redhat.com/articles/3974941)
* Azure-speciell RHEL-dokumentation:
  * [Support principer för RHEL-kluster med hög tillgänglighet – Microsoft Azure Virtual Machines som kluster medlemmar](https://access.redhat.com/articles/3131341)
  * [Installera och konfigurera en Red Hat Enterprise Linux 7,4 (och senare) High-Availability kluster på Microsoft Azure](https://access.redhat.com/articles/3252491)
* [NetApp SAP-program på Microsoft Azure med Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Översikt

Hög tillgänglighet för SAP NetWeaver Central Services kräver delad lagring.
För att uppnå detta i Red Hat Linux var det nödvändigt att skapa separata GlusterFS-kluster med hög tillgänglighet. 

Nu är det möjligt att uppnå SAP NetWeaver HA med hjälp av delad lagring, distribuerat på Azure NetApp Files. Om du använder Azure NetApp Files för delad lagring elimineras behovet av ytterligare [GlusterFS-kluster](./high-availability-guide-rhel-glusterfs.md). Pacemaker krävs fortfarande för HA av SAP NetWeaver Central Services (ASCS/SCS).

![Översikt över SAP NetWeaver-hög tillgänglighet](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS och SAP HANA Database använder virtuella värdnamn och virtuella IP-adresser. I Azure krävs en belastningsutjämnare för att använda en virtuell IP-adress. Vi rekommenderar att du använder [standard belastnings utjämning](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). I följande lista visas konfigurationen av belastningsutjämnaren med separata klient dels-IP: er för (A) SCS-och ERS.

### <a name="ascs"></a>En SCS

* Konfiguration av klient del
  * IP-192.168.14.9
* Avsöknings port
  * Port 620<strong> &lt; nr &gt; </strong>
* Belastnings Utjämnings regler
  * Om du använder Standard Load Balancer väljer du **ha-portar**
  * 32<strong> &lt; nr &gt; </strong> TCP
  * 36<strong> &lt; nr &gt; </strong> TCP
  * 39<strong> &lt; nr &gt; </strong> TCP
  * 81<strong> &lt; nr &gt; </strong> TCP
  * 5<strong> &lt; nr &gt; </strong>13 TCP
  * 5<strong> &lt; nr &gt; </strong>14 TCP
  * 5<strong> &lt; nr &gt; </strong>16 TCP

### <a name="ers"></a>ERS

* Konfiguration av klient del
  * IP-192.168.14.10
* Avsöknings port
  * Port 621<strong> &lt; nr &gt; </strong>
* Belastnings Utjämnings regler
  * Om du använder Standard Load Balancer väljer du **ha-portar**
  * 32<strong> &lt; nr &gt; </strong> TCP
  * 33<strong> &lt; nr &gt; </strong> TCP
  * 5<strong> &lt; nr &gt; </strong>13 TCP
  * 5<strong> &lt; nr &gt; </strong>14 TCP
  * 5<strong> &lt; nr &gt; </strong>16 TCP

* Server dels konfiguration
  * Anslutna till primära nätverks gränssnitt för alla virtuella datorer som ska ingå i (A) SCS/ERS-kluster

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Konfigurera Azure NetApp Files-infrastrukturen 

SAP NetWeaver kräver delad lagring för transport-och profil katalogen.  Innan du fortsätter med installationen av Azure NetApp-infrastrukturen kan du bekanta dig med Azure NetApp Files- [dokumentationen][anf-azure-doc]. Kontrol lera om den valda Azure-regionen har Azure NetApp Files. Följande länk visar tillgängligheten för Azure NetApp Files av Azure-regionen: [Azure NetApp Files tillgänglighet per Azure-region][anf-avail-matrix].

Azure NetApp-filer finns i flera [Azure-regioner](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Innan du distribuerar Azure NetApp Files kan du begära onboarding till Azure NetApp Files enligt [anvisningarna i registrera dig för Azure NetApp-filer][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Distribuera Azure NetApp Files-resurser  

Stegen förutsätter att du redan har distribuerat [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Azure NetApp Files resurser och de virtuella datorerna där Azure NetApp Files resurserna ska monteras måste distribueras i samma Azure-Virtual Network eller i peer-anslutna virtuella Azure-nätverk.  

1. Om du inte redan har gjort det kan du begära [att registrera Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).  
2. Skapa NetApp-kontot i den valda Azure-regionen genom [att följa anvisningarna för att skapa NetApp-konto](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).  
3. Konfigurera Azure NetApp Files kapacitets pool genom att följa [anvisningarna om hur du konfigurerar Azure NetApp Files kapacitets pool](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).  
SAP NetWeaver-arkitekturen som presenteras i den här artikeln använder pool för enskild Azure NetApp Files kapacitet, Premium SKU. Vi rekommenderar Azure NetApp Files Premium SKU för SAP NetWeaver-program arbets belastning på Azure.  
4. Delegera ett undernät till Azure NetApp-filer enligt beskrivningen i [instruktionerna delegera ett undernät till Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

5. Distribuera Azure NetApp Files volymer genom att följa [anvisningarna för att skapa en volym för Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Distribuera volymerna i det angivna Azure NetApp Files- [undernätet](/rest/api/virtualnetwork/subnets). IP-adresserna för Azure NetApp-volymerna tilldelas automatiskt. Tänk på att Azure NetApp Files-resurser och de virtuella Azure-datorerna måste finnas i samma Azure-Virtual Network eller i peer-datorer med virtuella Azure-nätverk. I det här exemplet använder vi två Azure NetApp Files-volymer: SAP<b>QAS</b> och transSAP. De fil Sök vägar som monteras på motsvarande monterings punkter är/usrsap<b>QAS</b>/sapmnt<b>QAS</b>,/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys osv.  

   1. volym SAP-<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/sapmnt<b>QAS</b>)
   2. volym SAP-<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ASCs)
   3. volym SAP-<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys)
   4. volym SAP-<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ERS)
   5. volym transSAP (nfs://192.168.24.4/transSAP)
   6. volym SAP-<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>PAS)
   7. volym SAP-<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>AAS)
  
I det här exemplet använde vi Azure NetApp Files för alla fil system i SAP NetWeaver för att demonstrera hur Azure NetApp Files kan användas. SAP-filsystem som inte behöver monteras via NFS kan också distribueras som [Azure disk Storage](../../disks-types.md#premium-ssd) . I det här exemplet måste <b>a-e</b> vara på Azure NetApp Files <b>och f-g</b> (det vill säga/usr/SAP/<b>QAS</b>/d<b>02</b>,/usr/SAP/<b>QAS</b>/d<b>03</b>) kan distribueras som Azure disk Storage. 

### <a name="important-considerations"></a>Att tänka på

Tänk på följande viktiga överväganden när du överväger Azure NetApp Files för SAP-NetWeaver på SUSE hög tillgänglighets arkitektur:

- Den minsta kapacitets poolen är 4 TiB. Kapacitets bassängens storlek kan ökas i steg om 1 TiB.
- Den minsta volymen är 100 GiB
- Azure NetApp Files och alla virtuella datorer, där Azure NetApp Files volymer ska monteras, måste finnas i samma Azure-Virtual Network eller i [peer-anslutna virtuella nätverk](../../../virtual-network/virtual-network-peering-overview.md) i samma region. Azure NetApp Files åtkomst över VNET-peering i samma region stöds nu. Azure NetApp-åtkomst över global peering stöds inte ännu.
- Det valda virtuella nätverket måste ha ett undernät, delegerat till Azure NetApp Files.
- Azure NetApp Files erbjuder [export princip](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): du kan kontrol lera tillåtna klienter, åtkomst typ (Läs&Skriv, skrivskyddad, osv.). 
- Azure NetApp Files funktionen är inte en zon medveten än. För närvarande är Azure NetApp Files funktionen inte distribuerad i alla tillgänglighets zoner i en Azure-region. Var medveten om potentiella fördröjnings konsekvenser i vissa Azure-regioner. 
- Azure NetApp Files volymer kan distribueras som NFSv3-eller NFSv 4.1-volymer. Båda protokollen stöds för SAP-program skiktet (ASCS/ERS, SAP-program servrar). 

## <a name="setting-up-ascs"></a>Konfigurera (A) SCS

I det här exemplet distribuerades resurserna manuellt via [Azure Portal](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Distribuera Linux manuellt via Azure Portal

Först måste du skapa Azure NetApp Files volymerna. Distribuera de virtuella datorerna. Därefter skapar du en belastningsutjämnare och använder de virtuella datorerna i backend-poolen.

1. Skapa belastningsutjämnare (intern, standard):  
   1. Skapa IP-adresser för klient delen
      1. IP-192.168.14.9 för ASCS
         1. Öppna belastningsutjämnaren, Välj klient delens IP-pool och klicka på Lägg till
         1. Ange namnet på den nya IP-poolen för klient delen (till exempel **frontend. QAS. ASCS**)
         1. Ange tilldelningen till statisk och ange IP-adressen (till exempel **192.168.14.9**)
         1. Klicka på OK
      1. IP-192.168.14.10 för ASCS-ERS
         * Upprepa stegen ovan under "a" för att skapa en IP-adress för ERS (till exempel **192.168.14.10** och **frontend. QAS. ERS**)
   1. Skapa serverdelspoolen
      1. Öppna belastningsutjämnaren, Välj backend-pooler och klicka på Lägg till
      1. Ange namnet på den nya backend-poolen (till exempel **Server del. QAS**)
      1. Klicka på Lägg till en virtuell dator.
      1. Välj Virtuell dator. 
      1. Välj de virtuella datorerna i (A) SCS-klustret och deras IP-adresser.
      1. Klicka på Lägg till
   1. Skapa hälso avsökningar
      1. Port 620 **00** för ASCS
         1. Öppna belastningsutjämnaren, Välj hälso avsökningar och klicka på Lägg till
         1. Ange namnet på den nya hälso avsökningen (till exempel **hälso tillstånd. QAS. ASCS**)
         1. Välj TCP som protokoll, Port 620 **00**, Behåll intervallet 5 och tröskelvärde 2
         1. Klicka på OK
      1. Port 621 **01** för ASCS ers
            * Upprepa stegen ovan under "c" för att skapa en hälso avsökning för ERS (till exempel 621 **01** och **Health. QAS. ERS**)
   1. Belastnings Utjämnings regler
      1. Regler för belastnings utjämning för ASCS
         1. Öppna belastningsutjämnaren, Välj belastnings Utjämnings regler och klicka på Lägg till
         1. Ange namnet på den nya belastnings Utjämnings regeln (till exempel **lb. QAS. ASCS**)
         1. Välj IP-adressen för klient delen för ASCS, backend-poolen och hälso avsökningen som du skapade tidigare (till exempel **frontend. QAS. ASCS**, **Server del. QAS** och **hälsa. QAS. ASCS**)
         1. Välj **ha-portar**
         1. Öka tids gränsen för inaktivitet till 30 minuter
         1. **Se till att aktivera flytande IP**
         1. Klicka på OK
         * Upprepa stegen ovan för att skapa belastnings Utjämnings regler för ERS (till exempel **lb. QAS. ERS**)
1. Om scenariot kräver grundläggande belastningsutjämnare (internt) följer du dessa steg:  
   1. Skapa IP-adresser för klient delen
      1. IP-192.168.14.9 för ASCS
         1. Öppna belastningsutjämnaren, Välj klient delens IP-pool och klicka på Lägg till
         1. Ange namnet på den nya IP-poolen för klient delen (till exempel **frontend. QAS. ASCS**)
         1. Ange tilldelningen till statisk och ange IP-adressen (till exempel **192.168.14.9**)
         1. Klicka på OK
      1. IP-192.168.14.10 för ASCS-ERS
         * Upprepa stegen ovan under "a" för att skapa en IP-adress för ERS (till exempel **192.168.14.10** och **frontend. QAS. ERS**)
   1. Skapa serverdelspoolen
      1. Öppna belastningsutjämnaren, Välj backend-pooler och klicka på Lägg till
      1. Ange namnet på den nya backend-poolen (till exempel **Server del. QAS**)
      1. Klicka på Lägg till en virtuell dator.
      1. Välj den tillgänglighets uppsättning som du skapade tidigare för ASCS 
      1. Välj virtuella datorer för (A) SCS-klustret
      1. Klicka på OK
   1. Skapa hälso avsökningar
      1. Port 620 **00** för ASCS
         1. Öppna belastningsutjämnaren, Välj hälso avsökningar och klicka på Lägg till
         1. Ange namnet på den nya hälso avsökningen (till exempel **hälso tillstånd. QAS. ASCS**)
         1. Välj TCP som protokoll, Port 620 **00**, Behåll intervallet 5 och tröskelvärde 2
         1. Klicka på OK
      1. Port 621 **01** för ASCS ers
            * Upprepa stegen ovan under "c" för att skapa en hälso avsökning för ERS (till exempel 621 **01** och **Health. QAS. ERS**)
   1. Belastnings Utjämnings regler
      1. 32 **00** TCP för ASCS
         1. Öppna belastningsutjämnaren, Välj belastnings Utjämnings regler och klicka på Lägg till
         1. Ange namnet på den nya belastnings Utjämnings regeln (till exempel **lb. QAS. ASCS. 3200**)
         1. Välj IP-adressen för klient delen för ASCS, backend-poolen och hälso avsökningen som du skapade tidigare (till exempel **frontend. QAS. ASCS**)
         1. Behåll protokollets **TCP**, ange port **3200**
         1. Öka tids gränsen för inaktivitet till 30 minuter
         1. **Se till att aktivera flytande IP**
         1. Klicka på OK
      1. Ytterligare portar för ASCS
         * Upprepa stegen ovan under "d" för portarna 36 **00**, 39 **00**, 81 **00**, 5 **00** 13, 5 **00** 14, 5 **00 16 och** TCP för ASCS
      1. Ytterligare portar för ASCS-ERS
         * Upprepa stegen ovan under "d" för portarna 32 **01**, 33 **01**, 5 **01** 13, 5 **01** 14, 5 **01** och TCP för ASCS-ers

      > [!IMPORTANT]
      > Flytande IP stöds inte på en sekundär NIC-IP-konfiguration i belastnings Utjämnings scenarier. Mer information finns i [begränsningar för Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Om du behöver ytterligare IP-adress för den virtuella datorn distribuerar du ett andra nätverkskort.  

      > [!Note]
      > När virtuella datorer utan offentliga IP-adresser placeras i backend-poolen för intern (ingen offentlig IP-adress) standard Azure-belastningsutjämnare, kommer det inte att finnas någon utgående Internet anslutning, om inte ytterligare konfiguration utförs för att tillåta routning till offentliga slut punkter. Mer information om hur du uppnår utgående anslutningar finns i Översikt över [offentliga slut punkter för Virtual Machines med Azure standard Load Balancer i SAP-scenarier med hög tillgänglighet](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

      > [!IMPORTANT]
      > Aktivera inte TCP-tidsstämplar på virtuella Azure-datorer som placerats bakom Azure Load Balancer. Om du aktiverar TCP-tidsstämplar kommer hälso avsökningarna att Miss skadas. Ange parametern **net.IPv4.tcp_timestamps** till **0**. Mer information finns i [Load Balancer hälso avsökningar](../../../load-balancer/load-balancer-custom-probe-overview.md).

## <a name="disable-id-mapping-if-using-nfsv41"></a>Inaktivera ID-mappning (om du använder NFSv 4.1)

Anvisningarna i det här avsnittet gäller endast om du använder Azure NetApp Files volymer med NFSv 4.1-protokollet. Utför konfigurationen på alla virtuella datorer, där Azure NetApp Files NFSv 4.1-volymer ska monteras.  

1. Verifiera NFS-domän inställningen. Kontrol lera att domänen är konfigurerad som standard Azure NetApp Files domän, dvs. **`defaultv4iddomain.com`** mappningen är inställd på **ingen**.  

    > [!IMPORTANT]
    > Se till att ange att NFS-domänen på `/etc/idmapd.conf` den virtuella datorn ska matcha standard domän konfigurationen på Azure NetApp Files: **`defaultv4iddomain.com`** . Om det finns ett matchnings fel mellan domän konfigurationen på NFS-klienten (d.v.s. den virtuella datorn) och NFS-servern, t. ex. Azure NetApp-konfigurationen, så visas behörigheterna för filer på Azure NetApp-volymer som är monterade på de virtuella datorerna som `nobody` .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** verifiera `nfs4_disable_idmapping` . Den måste anges till **Y**. Kör monterings kommandot för att skapa en katalog struktur där `nfs4_disable_idmapping` finns. Du kan inte skapa katalogen manuellt under/sys/modules eftersom åtkomst är reserverad för kernel/driv rutiner.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Mer information om hur du ändrar `nfs4_disable_idmapping` parametern finns i https://access.redhat.com/solutions/1749883 .

### <a name="create-pacemaker-cluster"></a>Skapa pacemaker-kluster

Följ stegen i [Konfigurera pacemaker på Red Hat Enterprise Linux i Azure](high-availability-guide-rhel-pacemaker.md) för att skapa ett grundläggande pacemaker-kluster för den här (a) SCS-servern.

### <a name="prepare-for-sap-netweaver-installation"></a>Förbered för SAP NetWeaver-installation

Följande objekt har prefixet **[A]** -tillämpligt för alla noder, **[1]** , som endast gäller nod 1 eller **[2]** -gäller endast nod 2.

1. **[A]** namn matchning för värdnamn

   Du kan antingen använda en DNS-server eller ändra/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adress och värdnamn i följande kommandon

    ```
    sudo vi /etc/hosts
    ```

   Infoga följande rader i/etc/hosts. Ändra IP-adress och värdnamn för att matcha din miljö

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** skapa SAP-kataloger i Azure NetApp Files volym.  
   Montera tillfälligt Azure NetApp Files volym på en av de virtuella datorerna och skapa SAP-katalogerna (fil Sök vägar).  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp
    ``` 

1. **[A]** skapa delade kataloger

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** installera NFS-klient och andra krav

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** kontrol lera version av resurs agenter – SAP

   Kontrol lera att versionen av de installerade resurs agenterna – SAP-paketet är minst 3.9.5 -124. el7
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** Lägg till monterings poster

   Om du använder NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Om du använder NFSv 4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > Se till att matcha NFS-Protokollversionen för Azure NetApp Files volymer när du monterar volymerna. Om Azure NetApp Files volymer skapas som NFSv3-volymer använder du motsvarande NFSv3-konfiguration. Om Azure NetApp Files volymer skapas som NFSv 4.1-volymer följer du anvisningarna för att inaktivera ID-mappning och se till att använda motsvarande NFSv 4.1-konfiguration. I det här exemplet har Azure NetApp Files volymer skapats som NFSv3-volymer.  

   Montera de nya resurserna

   ```
   sudo mount -a  
   ```

1. **[A]** konfigurera växlings fil

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Starta om agenten för att aktivera ändringen

   ```
   sudo service waagent restart
   ```

1. **[A]** RHEL-konfiguration

   Konfigurera RHEL enligt beskrivningen i SAP anmärkning [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Installera SAP NetWeaver ASCS/ERS

1. **[1]** skapa en virtuell IP-resurs och en hälso avsökning för ASCS-instansen

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Kontrol lera att klustrets status är OK och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** installera SAP NetWeaver ASCS  

   Installera SAP NetWeaver-ASCS som rot på den första noden med ett virtuellt värdnamn som mappar till IP-adressen för belastningsutjämnarens frontend-konfiguration för ASCS, till exempel <b>anftstsapvh</b>, <b>192.168.14.9</b> och det instans nummer som du använde för avsökningen av belastningsutjämnaren, till exempel <b>00</b>.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta att en användare som inte är rot användare ansluter till sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Om installationen Miss lyckas med att skapa en undermapp i/usr/SAP/**QAS**/ASCS **00**, så försök att ange ägare och grupp för mappen ASCS **00** och försök igen.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** skapa en virtuell IP-resurs och en hälso avsökning för ers-instansen

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Kontrol lera att klustrets status är OK och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** installera SAP NetWeaver ers  

   Installera SAP NetWeaver ERS som rot på den andra noden med ett virtuellt värdnamn som mappar till IP-adressen för belastningsutjämnarens frontend-konfiguration för ERS, till exempel <b>anftstsapers</b>, <b>192.168.14.10</b> och det instans nummer som du använde för avsökningen av belastningsutjämnaren, till exempel <b>01</b>.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta att en användare som inte är rot användare ansluter till sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Om installationen Miss lyckas med att skapa en undermapp i/usr/SAP/**QAS**/ers **01**, försök att ange ägare och grupp för ers **01** -mappen och försök igen.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** anpassa ASCS/SCS och ers instance-profiler

   * ASCS/SCS-profil

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   ```

   Se till att `keepalive` OS-parametrarna är inställda enligt beskrivningen i SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736)för både ENSA1 och ENSA2.  

   * ERS-profil

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** konfigurera Keep Alive

   Kommunikationen mellan SAP NetWeaver program Server och ASCS/SCS dirigeras via en belastningsutjämnare för program vara. Belastningsutjämnaren kopplar från inaktiva anslutningar efter en konfigurerbar tids gräns. För att förhindra detta måste du ange en parameter i SAP NetWeaver-ASCS/SCS-profilen, om du använder ENSA1 och ändra Linux `keepalive` -systeminställningarna på alla SAP-servrar för både ENSA1/ENSA2. Läs [SAP Note 1410736][1410736] för mer information.

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   ```

1. **[A]** uppdatera/usr/SAP/sapservices-filen

   För att förhindra att instanserna startas av sapinit-startskriptet måste alla instanser som hanteras av pacemaker vara kommenterade från/usr/SAP/sapservices-filen. Kommentera inte ut SAP HANA-instansen om den ska användas med HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** skapa SAP-kluster resurser

   Om du använder ENSA1 (enqueue Server 1 Architecture) definierar du resurserna på följande sätt:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP introducerade stöd för att köa Server 2, inklusive replikering, från SAP NW 7,52. Från och med ABAP Platform 1809 installeras som standard Server 2. Se SAP NOTE [2630416](https://launchpad.support.sap.com/#/notes/2630416) för Server 2-stöd.
   Om du använder[ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)(Queue server 2 Architecture) installerar du resurs agent resurs-agenter-SAP-4.1.1-12.el7.x86_64 eller senare och definierar resurserna på följande sätt:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Om du uppgraderar från en äldre version och växlar till att köa Server 2, se SAP anmärkning [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > Tids gränsen i konfigurationen ovan är bara exempel och kan behöva anpassas till den angivna SAP-konfigurationen. 

   Kontrol lera att klustrets status är OK och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** Lägg till brand Väggs regler för ASCS och ers på båda noderna genom att lägga till brand Väggs reglerna för ASCS och ers på båda noderna.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3201/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3201/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver Application Server-förberedelse

   Vissa databaser kräver att installationen av databas instansen körs på en program Server. Förbered de virtuella datorerna för program servern så att de kan använda dem i dessa fall.  

   Stegen nedan förutsätter att du installerar program servern på en annan server än ASCS/SCS-och HANA-servrarna. Annars behövs inte några av stegen nedan (som att konfigurera värd namns matchning).  

   Följande objekt föregås av antingen **[A]** – tillämpligt på både Pas och AAS, **[P]** – endast tillämpligt på Pas eller **[s]** – endast tillämpligt på AAS.  

1. **[A]** matchning värd namns matchning du kan antingen använda en DNS-server eller ändra/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon:  

   ```
   sudo vi /etc/hosts
   ```

   Infoga följande rader i/etc/hosts. Ändra IP-adress och värdnamn för att matcha din miljö.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** skapa sapmnt-katalogen skapa katalogen sapmnt.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** installera NFS-klient och andra krav  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Lägg till monterings poster  
   Om du använder NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Om du använder NFSv 4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   Montera de nya resurserna

   ```
   sudo mount -a
   ```

1. **[P]** skapa och montera katalogen PAS  
   Om du använder NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Om du använder NFSv 4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** skapa och montera AAS-katalogen  
   Om du använder NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Om du använder NFSv 4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** konfigurera växlings fil
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Starta om agenten för att aktivera ändringen

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Installera databas

I det här exemplet installeras SAP NetWeaver på SAP HANA. Du kan använda alla databaser som stöds för den här installationen. Mer information om hur du installerar SAP HANA i Azure finns i [hög tillgänglighet för SAP HANA på virtuella Azure-datorer på Red Hat Enterprise Linux][sap-hana-ha] . For a list of supported databases, see [SAP Note 1928533][1928533] .

1. Kör installationen av SAP Database instance

   Installera SAP NetWeaver Database-instansen som rot med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för belastnings utjämningens frontend-konfiguration för-databasen.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta att en användare som inte är rot användare ansluter till sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Installation av SAP NetWeaver-program Server

Följ dessa steg om du vill installera en SAP-Programserver.

1. Förbered program Server

   Följ stegen i kapitel [SAP NetWeaver Application Server-förberedelsen](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) ovan för att förbereda program servern.

1. Installera SAP NetWeaver program Server

   Installera en primär eller ytterligare SAP NetWeaver program Server.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta att en användare som inte är rot användare ansluter till sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Uppdatera SAP HANA säker lagring

   Uppdatera den SAP HANA säkra lagringen så att den pekar på det virtuella namnet på installations programmet för SAP HANA system replikering.

   Kör följande kommando för att lista posterna som \<sapsid> ADM

   ```
   hdbuserstore List
   ```

   Detta bör Visa alla poster och bör se ut ungefär så här
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   Utdata visar att IP-adressen för standard posten pekar på den virtuella datorn och inte till belastningsutjämnarens IP-adress. Posten måste ändras så att den pekar på det virtuella värd namnet för belastningsutjämnaren. Se till att använda samma port (**30313** i utdata ovan) och databas namnet (**QAS** i utdata ovan)!

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>Testa kluster konfigurationen

1. Migrera ASCS-instansen manuellt

   Resurs tillstånd innan du startar testet:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Kör följande kommandon som rot för att migrera ASCS-instansen.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resurs tillstånd efter testet:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Simulera Node-krasch

   Resurs tillstånd innan du startar testet:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Kör följande kommando som rot på noden där ASCS-instansen körs

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   Statusen när noden har startats igen bör se ut så här.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Använd följande kommando för att rensa de misslyckade resurserna.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resurs tillstånd efter testet:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Stoppa meddelande Server process

   Resurs tillstånd innan du startar testet:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   Kör följande kommandon som rot för att identifiera processen för meddelande servern och avsluta den.

   ```
   [root@anftstsapcl1 ~]# pgrep -f ms.sapQAS | xargs kill -9
   ```

   Om du bara avdödar meddelande servern en gång, startas den om av `sapstart` . Om du tar bort det ofta räcker pacemaker att flytta ASCS-instansen till den andra noden. Kör följande kommandon som rot för att rensa resurs statusen för ASCS-och ERS-instansen efter testet.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resurs tillstånd efter testet:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Stoppa Server process för att stoppa kön

   Resurs tillstånd innan du startar testet:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Kör följande kommandon som rot på noden där ASCS-instansen körs för att avsluta servern.

   ```
   #If using ENSA1
   [root@anftstsapcl2 ~]# pgrep -f en.sapQAS | xargs kill -9
   #If using ENSA2
   [root@anftstsapcl2 ~]# pgrep -f enq.sapQAS | xargs kill -9
   ```

   ASCS-instansen bör omedelbart redundansväxla till den andra noden. ERS-instansen bör också redundansväxla efter att ASCS-instansen har startats. Kör följande kommandon som rot för att rensa resurs statusen för ASCS-och ERS-instansen efter testet.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resurs tillstånd efter testet:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Stoppa processen för att köa replikerings Server

   Resurs tillstånd innan du startar testet:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Kör följande kommando som rot på noden där ERS-instansen körs för att stoppa processen för att köa replikerings Server.

   ```
   #If using ENSA1
   [root@anftstsapcl2 ~]# pgrep -f er.sapQAS | xargs kill -9
   #If using ENSA2
   [root@anftstsapcl2 ~]# pgrep -f enqr.sapQAS | xargs kill -9
   ```

   Om du bara kör kommandot en gång `sapstart` startas processen om. Om du kör det tillräckligt ofta, `sapstart` startar inte om processen och resursen är i ett stoppat tillstånd. Kör följande kommandon som rot för att rensa resurs statusen för ERS-instansen efter testet.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resurs tillstånd efter testet:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Avsluta sapstartsrv process

   Resurs tillstånd innan du startar testet:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Kör följande kommandon som rot på den nod där ASCS körs.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   Sapstartsrv-processen bör alltid startas om av pacemaker-resurs agenten som en del av övervakningen. Resurs tillstånd efter testet:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>Nästa steg

* [HA för SAP NW på virtuella Azure-datorer på RHEL för SAP-program med flera SID-guide](./high-availability-guide-rhel-multi-sid.md)
* [Azure Virtual Machines planera och implementera SAP][planning-guide]
* [Azure Virtual Machines distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Information om hur du upprättar hög tillgänglighet och planerar för haveri beredskap för SAP HANA på Azure (stora instanser) finns i [SAP HANA (stora instanser) hög tillgänglighet och haveri beredskap på Azure](hana-overview-high-availability-disaster-recovery.md).
* Information om hur du upprättar hög tillgänglighet och planerar för haveri beredskap för SAP HANA på virtuella Azure-datorer finns i [hög tillgänglighet för SAP HANA på Azure-Virtual Machines (VM)][sap-hana-ha]