---
title: Virtuella Azure-datorer med hög tillgänglighet för SAP NW på SLES med Azure NetApp Files | Microsoft Docs
description: Hög tillgänglighets guide för SAP NetWeaver på SUSE Linux Enterprise Server med Azure NetApp Files för SAP-program
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
ms.openlocfilehash: 83e770cb7eb1aace49745253dac3c757cebb4047
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668681"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server med Azure NetApp Files för SAP-program

[dbms-guide]:dbms_guide_general.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

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

Den här artikeln beskriver hur du distribuerar virtuella datorer, konfigurerar de virtuella datorerna, installerar kluster ramverket och installerar ett SAP NetWeaver 7,50-system med hög tillgänglighet med hjälp av [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).
I exemplen konfigurationer, installations kommandon osv., är ASCS-instansen Number 00, ERS instance Number 01, den primära program instansen (PAS) är 02 och program instansen (AAS) är 03. SAP-system-ID-QAS används. 

Den här artikeln förklarar hur du uppnår hög tillgänglighet för SAP NetWeaver-program med Azure NetApp Files. Databas lagret beskrivs inte i detalj i den här artikeln.

Läs följande SAP-anteckningar och dokument först:

* [Azure NetApp Files dokumentation][anf-azure-doc] 
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
* SAP community WIKI] ( https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP-anteckningar för Linux.
* [Azure Virtual Machines planera och implementera SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [Guider för SUSE SAP-ha bästa praxis][suse-ha-guide] Guiderna innehåller all information som krävs för att konfigurera NetWeaver HA och SAP HANA systemreplikering lokalt. Använd dessa guider som en allmän bas linje. De ger mycket mer detaljerad information.
* [Viktig information om SUSEt hög tillgänglighets tillägg 12 SP3][suse-ha-12sp3-relnotes]
* [NetApp SAP-program på Microsoft Azure med Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Översikt

Hög tillgänglighet för SAP NetWeaver Central Services kräver delad lagring.
För att uppnå detta i SUSE Linux var det nödvändigt att skapa separata NFS-kluster med hög tillgänglighet. 

Nu är det möjligt att uppnå SAP NetWeaver HA med hjälp av delad lagring, distribuerat på Azure NetApp Files. Om du använder Azure NetApp Files för delad lagring elimineras behovet av ytterligare [NFS-kluster](./high-availability-guide-suse-nfs.md). Pacemaker krävs fortfarande för HA av SAP NetWeaver Central Services (ASCS/SCS).


![Översikt över SAP NetWeaver-hög tillgänglighet](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS och SAP HANA Database använder virtuella värdnamn och virtuella IP-adresser. I Azure krävs en [belastningsutjämnare](../../../load-balancer/load-balancer-overview.md) för att använda en virtuell IP-adress. Vi rekommenderar att du använder [standard belastnings utjämning](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). I följande lista visas konfigurationen av (A) SCS-och ERS-belastningsutjämnaren.

### <a name="ascs"></a>En SCS

* Konfiguration av klient del
  * IP-10.1.1.20
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
  * IP-10.1.1.21
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

5. Distribuera Azure NetApp Files volymer genom att följa [anvisningarna för att skapa en volym för Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Distribuera volymerna i det angivna Azure NetApp Files- [undernätet](/rest/api/virtualnetwork/subnets). IP-adresserna för Azure NetApp-volymerna tilldelas automatiskt. Tänk på att Azure NetApp Files-resurser och de virtuella Azure-datorerna måste finnas i samma Azure-Virtual Network eller i peer-datorer med virtuella Azure-nätverk. I det här exemplet använder vi två Azure NetApp Files-volymer: SAP<b>QAS</b> och trans. De fil Sök vägar som monteras på motsvarande monterings punkter är/usrsap<b>QAS</b>/sapmnt<b>QAS</b>,/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys osv.  

   1. volym SAP-<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/sapmnt<b>QAS</b>)
   2. volym SAP-<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>ASCs)
   3. volym SAP-<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys)
   4. volym SAP-<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>ERS)
   5. volym transaktion (nfs://10.1.0.4/trans)
   6. volym SAP-<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>PAS)
   7. volym SAP-<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>AAS)

   
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

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Distribuera virtuella Linux-datorer manuellt via Azure Portal

Först måste du skapa Azure NetApp Files volymerna. Distribuera de virtuella datorerna. Därefter skapar du en belastningsutjämnare och använder de virtuella datorerna i backend-poolerna.

1. Skapa en resursgrupp
1. Skapa ett virtuellt nätverk
1. Skapa en tillgänglighets uppsättning för ASCS  
   Ange Max uppdaterings domän
1. Skapa virtuell dator 1  
   Använd minst SLES4SAP 12 SP3, i det här exemplet används SLES4SAP 12 SP3-avbildningen  
   Välj den tillgänglighets uppsättning som skapades tidigare för ASCS  
1. Skapa virtuell dator 2  
   Använd minst SLES4SAP 12 SP3, i det här exemplet används SLES4SAP 12 SP3-avbildningen  
   Välj den tillgänglighets uppsättning som skapades tidigare för ASCS  
1. Skapa en tillgänglighets uppsättning för SAP Application instances (PAS, AAS)    
   Ange Max uppdaterings domän
1. Skapa virtuell dator 3  
   Använd minst SLES4SAP 12 SP3, i det här exemplet används SLES4SAP 12 SP3-avbildningen  
   Välj den tillgänglighets uppsättning som skapades tidigare för PAS/AAS   
1. Skapa virtuell dator 4  
   Använd minst SLES4SAP 12 SP3, i det här exemplet används SLES4SAP 12 SP3-avbildningen  
   Välj den tillgänglighets uppsättning som skapades tidigare för PAS/AAS  

## <a name="disable-id-mapping-if-using-nfsv41"></a>Inaktivera ID-mappning (om du använder NFSv 4.1)

Anvisningarna i det här avsnittet gäller endast om du använder Azure NetApp Files volymer med NFSv 4.1-protokollet. Utför konfigurationen på alla virtuella datorer, där Azure NetApp Files NFSv 4.1-volymer ska monteras.  

1. Verifiera NFS-domän inställningen. Kontrol lera att domänen är konfigurerad som standard Azure NetApp Files domän, dvs. **`defaultv4iddomain.com`** mappningen är inställd på **ingen**.  

    > [!IMPORTANT]
    > Se till att ange att NFS-domänen på `/etc/idmapd.conf` den virtuella datorn ska matcha standard domän konfigurationen på Azure NetApp Files: **`defaultv4iddomain.com`** . Om det finns ett matchnings fel mellan domän konfigurationen på NFS-klienten (d.v.s. den virtuella datorn) och NFS-servern, t. ex. Azure NetApp-konfigurationen, så visas behörigheterna för filer på Azure NetApp-volymer som är monterade på de virtuella datorerna som `nobody` .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
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
    mount 10.1.0.4:/sapmnt/<b>qas</b> /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>


## <a name="setting-up-ascs"></a>Konfigurera (A) SCS

I det här exemplet distribuerades resurserna manuellt via [Azure Portal](https://portal.azure.com/#home) .

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Distribuera Azure Load Balancer manuellt via Azure Portal

Först måste du skapa Azure NetApp Files volymerna. Distribuera de virtuella datorerna. Därefter skapar du en belastningsutjämnare och använder de virtuella datorerna i backend-poolen.

1. Skapa belastningsutjämnare (intern, standard):  
   1. Skapa IP-adresser för klient delen
      1. IP-10.1.1.20 för ASCS
         1. Öppna belastningsutjämnaren, Välj klient delens IP-pool och klicka på Lägg till
         1. Ange namnet på den nya IP-poolen för klient delen (till exempel **frontend. QAS. ASCS**)
         1. Ange tilldelningen till statisk och ange IP-adressen (till exempel **10.1.1.20**)
         1. Klicka på OK
      1. IP-10.1.1.21 för ASCS-ERS
         * Upprepa stegen ovan under "a" för att skapa en IP-adress för ERS (till exempel **10.1.1.21** och **frontend. QAS. ERS**)
   1. Skapa serverdelspoolen
      1. Öppna belastningsutjämnaren, Välj backend-pooler och klicka på Lägg till
      1. Ange namnet på den nya backend-poolen (till exempel **Server del. QAS**)
      1. Klicka på Lägg till en virtuell dator.
      1. Välj virtuell dator
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
      1. Skapa en backend-pool för ASCS
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
      1. IP-10.1.1.20 för ASCS
         1. Öppna belastningsutjämnaren, Välj klient delens IP-pool och klicka på Lägg till
         1. Ange namnet på den nya IP-poolen för klient delen (till exempel **frontend. QAS. ASCS**)
         1. Ange tilldelningen till statisk och ange IP-adressen (till exempel **10.1.1.20**)
         1. Klicka på OK
      1. IP-10.1.1.21 för ASCS-ERS
         * Upprepa stegen ovan under "a" för att skapa en IP-adress för ERS (till exempel **10.1.1.21** och **frontend. QAS. ERS**)
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

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
    # Information for package sap-suse-cluster-connector:
    # ---------------------------------------------------
    # Repository     : SLE-12-SP3-SAP-Updates
    # Name           : sap-suse-cluster-connector
    # Version        : 3.1.0-8.1
    # Arch           : noarch
    # Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
    # Support Level  : Level 3
    # Installed Size : 45.6 KiB
    # Installed      : Yes
    # Status         : up-to-date
    # Source package : sap-suse-cluster-connector-3.1.0-8.1.src
    # Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]** uppdatera SAP-resurs agenter  
   
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

3. **[A]** namn matchning för värdnamn

   Du kan antingen använda en DNS-server eller ändra/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adress och värdnamn i följande kommandon

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Infoga följande rader i/etc/hosts. Ändra IP-adress och värdnamn för att matcha din miljö   

   <pre><code>
   # IP address of cluster node 1
   <b>10.1.1.18    anftstsapcl1</b>
   # IP address of cluster node 2
   <b>10.1.1.6     anftstsapcl2</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
   <b>10.1.1.20    anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ERS
   <b>10.1.1.21    anftstsapers</b>
   </code></pre>

4. **[1]** skapa SAP-kataloger i Azure NetApp Files volym.  
   Montera tillfälligt Azure NetApp Files volym på en av de virtuella datorerna och skapa SAP-katalogerna (fil Sök vägar).  

   ```
    # mount temporarily the volume
    sudo mkdir -p /saptmp
    # If using NFSv3
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 10.1.0.4:/sapQAS /saptmp
    # If using NFSv4.1
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 10.1.0.4:/sapQAS /saptmp
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

## <a name="prepare-for-sap-netweaver-installation"></a>Förbered för SAP NetWeaver-installation

1. **[A]** skapa delade kataloger

   <pre><code>sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>QAS</b>/SYS
   sudo mkdir -p /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>QAS</b>/ERS<b>01</b>
   
   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>QAS</b>/SYS
   sudo chattr +i /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>

2. **[A]** konfigurera `autofs`

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Om du använder NFSv3 skapar du en fil med:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind 10.1.0.4:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind 10.1.0.4:/usrsap<b>qas</b>/usrsap<b>QAS</b>sys
   </code></pre>
   
   Om du använder NFSv 4.1 skapar du en fil med:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/usrsap<b>qas</b>/usrsap<b>QAS</b>sys
   </code></pre>
   
   > [!NOTE]
   > Se till att matcha NFS-Protokollversionen för Azure NetApp Files volymer när du monterar volymerna. Om Azure NetApp Files volymer skapas som NFSv3-volymer använder du motsvarande NFSv3-konfiguration. Om Azure NetApp Files volymer skapas som NFSv 4.1-volymer följer du anvisningarna för att inaktivera ID-mappning och se till att använda motsvarande NFSv 4.1-konfiguration. I det här exemplet har Azure NetApp Files volymer skapats som NFSv3-volymer.  
   
   Starta om `autofs` för att montera de nya resurserna
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A]** konfigurera växlings fil

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

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' options='sec=sys,vers=4.1' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS azure-lb port=620<b>00</b>
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Kontrol lera att klustrets status är OK och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]** installera SAP NetWeaver ASCS  

   Installera SAP NetWeaver-ASCS som rot på den första noden med ett virtuellt värdnamn som mappar till IP-adressen för belastningsutjämnarens frontend-konfiguration för ASCS, till exempel <b>anftstsapvh</b>, <b>10.1.1.20</b> och det instans nummer som du använde för avsökningen av belastningsutjämnaren, till exempel <b>00</b>.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta att en användare som inte är rot användare ansluter till sapinst. Du kan använda parameter SAPINST_USE_HOSTNAME för att installera SAP med hjälp av virtuellt värdnamn.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   Om installationen Miss lyckas med att skapa en undermapp i/usr/SAP/**QAS**/ASCS **00**, så försök att ange ägare och grupp för mappen ASCS **00**  och försök igen. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]** skapa en virtuell IP-resurs och en hälso avsökning för ers-instansen

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' options='sec=sys,vers=4.1' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS azure-lb port=621<b>01</b>
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   Kontrol lera att klustrets status är OK och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **[2]** installera SAP NetWeaver ers

   Installera SAP NetWeaver ERS som rot på den andra noden med ett virtuellt värdnamn som mappar till IP-adressen för belastningsutjämnarens frontend-konfiguration för ERS, till exempel <b>anftstsapers</b>, <b>10.1.1.21</b> och det instans nummer som du använde för avsökningen av belastningsutjämnaren, till exempel <b>01</b>.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta att en användare som inte är rot användare ansluter till sapinst. Du kan använda parameter SAPINST_USE_HOSTNAME för att installera SAP med hjälp av virtuellt värdnamn.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > Använd SWPM SP 20 PL 05 eller högre. Lägre versioner ställer inte in behörigheterna korrekt och installationen kommer inte att fungera.

   Om installationen Miss lyckas med att skapa en undermapp i/usr/SAP/**QAS**/ers **01**, försök att ange ägare och grupp för ers **01** -mappen och försök igen.

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]** anpassa ASCS/SCS och ers instance-profiler
 
   * ASCS/SCS-profil

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
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

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

6. **[A]** konfigurera Keep Alive

   Kommunikationen mellan SAP NetWeaver program Server och ASCS/SCS dirigeras via en belastningsutjämnare för program vara. Belastningsutjämnaren kopplar från inaktiva anslutningar efter en konfigurerbar tids gräns. För att förhindra detta måste du ange en parameter i SAP NetWeaver-ASCS/SCS-profilen, om du använder ENSA1 och ändra Linux `keepalive` -systeminställningarna på alla SAP-servrar för både ENSA1/ENSA2. Läs [SAP Note 1410736][1410736] för mer information.

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   </code></pre>

7. **[A]** konfigurera SAP-användare efter installationen

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]** Lägg till ASCS-och ers SAP-tjänsterna i `sapservice` filen

   Lägg till ASCS-tjänst posten till den andra noden och kopiera ERS-tjänst posten till den första noden.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]** skapa SAP-kluster resurser

Om du använder ENSA1 (enqueue Server 1 Architecture) definierar du resurserna på följande sätt:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure location loc_sap_<b>QAS</b>_failover_to_ers rsc_sap_<b>QAS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>QAS</b> eq 1
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   SAP introducerade stöd för att köa Server 2, inklusive replikering, från SAP NW 7,52. Från och med ABAP Platform 1809 installeras som standard Server 2. Se SAP NOTE [2630416](https://launchpad.support.sap.com/#/notes/2630416) för Server 2-stöd.
Om du använder[ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)(Queue server 2 Architecture) definierar du resurserna på följande sätt:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Om du uppgraderar från en äldre version och växlar till att köa Server 2, se SAP anmärkning [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Kontrol lera att klustrets status är OK och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver Application Server-förberedelse 

Vissa databaser kräver att installationen av databas instansen körs på en program Server. Förbered de virtuella datorerna för program servern så att de kan använda dem i dessa fall.

Stegen nedan förutsätter att du installerar program servern på en annan server än ASCS/SCS-och HANA-servrarna. Annars behövs inte några av stegen nedan (som att konfigurera värd namns matchning).

Följande objekt föregås av antingen **[A]** – tillämpligt på både Pas och AAS, **[P]** – endast tillämpligt på Pas eller **[s]** – endast tillämpligt på AAS.


1. **[A]** konfigurera operativ system

   Minska storleken på den ändrade cachen. Mer information finns i [låga skriv prestanda på SLES 11/12-servrar med stort RAM-minne](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** namn matchning för värdnamn

   Du kan antingen använda en DNS-server eller ändra/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adress och värdnamn i följande kommandon

   ```bash
   sudo vi /etc/hosts
   ```

   Infoga följande rader i/etc/hosts. Ändra IP-adress och värdnamn för att matcha din miljö

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]** skapa sapmnt-katalogen

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]** skapa katalogen PAS

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]** skapa AAS-katalogen

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]** konfigurera `autofs` på Pas

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Om du använder NFSv3 skapar du en ny fil med:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>pas
   </code></pre>

   Om du använder NFSv 4.1 skapar du en ny fil med:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>pas
   </code></pre>

   Starta om `autofs` för att montera de nya resurserna

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]** konfigurera `autofs` på AAS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Om du använder NFSv3 skapar du en ny fil med:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>aas
   </code></pre>

   Om du använder NFSv 4.1 skapar du en ny fil med:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>aas
   </code></pre>

   Starta om `autofs` för att montera de nya resurserna

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** konfigurera växlings fil

   <pre><code>
   sudo vi /etc/waagent.conf
   
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

* Kör installationen av SAP Database instance

   Installera SAP NetWeaver Database-instansen som rot med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för belastnings utjämningens frontend-konfiguration för-databasen.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta att en användare som inte är rot användare ansluter till sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Installation av SAP NetWeaver-program Server

Följ dessa steg om du vill installera en SAP-Programserver.

1. **[A]** Förbered program Server genom att följa stegen i kapitlet [SAP NetWeaver Application Server Preparation](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) ovan för att förbereda program servern.

2. **[A]** installera SAP NetWeaver Application Server installera en primär eller ytterligare SAP NetWeaver program Server.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta att en användare som inte är rot användare ansluter till sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]** uppdatera SAP HANA säker lagring

   Uppdatera den SAP HANA säkra lagringen så att den pekar på det virtuella namnet på installations programmet för SAP HANA system replikering.

   Kör följande kommando för att lista posterna
   <pre><code>
   hdbuserstore List
   </code></pre>

   Detta bör Visa alla poster och bör se ut ungefär så här
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   Utdata visar att IP-adressen för standard posten pekar på den virtuella datorn och inte till belastningsutjämnarens IP-adress. Posten måste ändras så att den pekar på det virtuella värd namnet för belastningsutjämnaren. Se till att använda samma port (**30313** i utdata ovan) och databas namnet (**QAS** i utdata ovan)!

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testa kluster konfigurationen

Följande tester är en kopia av test fallen i [Best Practices-handböcker för SUSE][suse-ha-guide]. De kopieras för din bekvämlighet. Läs alltid de bästa tipsen och utför alla ytterligare tester som kan ha lagts till.

1. Testa HAGetFailoverConfig, HACheckConfig och HACheckFailoverConfig

   Kör följande kommandon som \<sapsid> adm på noden där ASCS-instansen körs för tillfället. Om kommandona inte fungerar: det finns inte tillräckligt med minne, det kan bero på att det finns bindestreck i värd namnet. Detta är ett känt problem och kommer att korrigeras av SUSE i paketet SAP-SUSE-Cluster-Connector.

   <pre><code>
   anftstsapcl1:qasadm 52> sapcontrol -nr 00 -function HAGetFailoverConfig
   07.03.2019 20:08:59
   HAGetFailoverConfig
   OK
   HAActive: TRUE
   HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3
   HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3 (sap_suse_cluster_connector 3.1.0)
   HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   HAActiveNode: anftstsapcl1
   HANodes: anftstsapcl1, anftstsapcl2

   anftstsapcl1:qasadm 54> sapcontrol -nr 00 -function HACheckConfig
   07.03.2019 23:28:29
   HACheckConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (anftstsapvh_QAS_00), SAPInstance includes is-ers patch
   SUCCESS, SAP CONFIGURATION, Enqueue replication (anftstsapvh_QAS_00), Enqueue replication enabled
   SUCCESS, SAP STATE, Enqueue replication state (anftstsapvh_QAS_00), Enqueue replication active
   
   anftstsapcl1:qasadm 55> sapcontrol -nr 00 -function HACheckFailoverConfig
   07.03.2019 23:30:48
   HACheckFailoverConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

2. Migrera ASCS-instansen manuellt

   Resurs tillstånd innan du startar testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   Kör följande kommandon som rot för att migrera ASCS-instansen.

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Resurs tillstånd efter testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. Testa HAFailoverToNode

   Resurs tillstånd innan du startar testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Kör följande kommandon som \<sapsid> ADM för att MIGRERA ASCS-instansen.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   # Remove migration constraints
   anftstsapcl1:~ # crm resource clear rsc_sap_QAS_ASCS00
   #INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   </code></pre>

   Resurs tillstånd efter testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. Simulera Node-krasch 

   Resurs tillstånd innan du startar testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Kör följande kommando som rot på noden där ASCS-instansen körs

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Om du använder SBD bör pacemaker inte starta automatiskt på den stoppade noden. Statusen när noden har startats igen bör se ut så här.

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   Använd följande kommandon för att starta pacemaker på den stoppade noden, rensa SBD-meddelandena och rensa de misslyckade resurserna.

   <pre><code>
   # run as root
   # list the SBD device(s)
   anftstsapcl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405b730e31e7d5a4516a2a697dcf;/dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e;/dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a"

   anftstsapcl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e11 -d /dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e -d /dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a message anftstsapcl2 clear

   anftstsapcl2:~ # systemctl start pacemaker
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Resurs tillstånd efter testet:

   <pre><code>
   Full list of resources:
   
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. Testa Manuell omstart av ASCS-instansen

   Resurs tillstånd innan du startar testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Skapa ett köobjekt genom att t. ex. Redigera en användare i Transaction su01. Kör följande kommandon som <sapsid \> adm på den nod där ASCS-instansen körs. Kommandona stoppar ASCS-instansen och startar den igen. Om du använder en arkitektur för Server 1, förväntas det att ta bort det här testet. Om du använder Server 2 arkitektur för Server 2 kommer kön att behållas. 

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   ASCS-instansen bör nu inaktive ras i pacemaker

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Starta ASCS-instansen igen på samma nod.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Su01 i transaktions kön bör gå förlorad, om du använder en arkitektur för Server för replikering 1 och Server delen måste ha återställts. Resurs tillstånd efter testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. Stoppa meddelande Server process

   Resurs tillstånd innan du startar testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Kör följande kommandon som rot för att identifiera processen för meddelande servern och avsluta den.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   Om du bara avdödar meddelande servern en gång, startas den om av `sapstart` . Om du tar bort det ofta räcker pacemaker att flytta ASCS-instansen till den andra noden. Kör följande kommandon som rot för att rensa resurs statusen för ASCS-och ERS-instansen efter testet.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Resurs tillstånd efter testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. Stoppa Server process för att stoppa kön

   Resurs tillstånd innan du startar testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Kör följande kommandon som rot på noden där ASCS-instansen körs för att avsluta servern.

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   ASCS-instansen bör omedelbart redundansväxla till den andra noden. ERS-instansen bör också redundansväxla efter att ASCS-instansen har startats. Kör följande kommandon som rot för att rensa resurs statusen för ASCS-och ERS-instansen efter testet.

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Resurs tillstånd efter testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. Stoppa processen för att köa replikerings Server

   Resurs tillstånd innan du startar testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Kör följande kommando som rot på noden där ERS-instansen körs för att stoppa processen för att köa replikerings Server.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   Om du bara kör kommandot en gång `sapstart` startas processen om. Om du kör det tillräckligt ofta, `sapstart` startar inte om processen och resursen är i ett stoppat tillstånd. Kör följande kommandon som rot för att rensa resurs statusen för ERS-instansen efter testet.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Resurs tillstånd efter testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. Avsluta sapstartsrv process

   Resurs tillstånd innan du startar testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Kör följande kommandon som rot på den nod där ASCS körs.

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   Sapstartsrv-processen bör alltid startas om av pacemaker-resurs agenten. Resurs tillstånd efter testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>Nästa steg

* [HA för SAP NW på virtuella Azure-datorer på SLES för SAP-program med flera SID-guide](./high-availability-guide-suse-multi-sid.md)
* [Azure Virtual Machines planera och implementera SAP][planning-guide]
* [Azure Virtual Machines distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Information om hur du upprättar hög tillgänglighet och planerar för haveri beredskap för SAP HANA på virtuella Azure-datorer finns i [hög tillgänglighet för SAP HANA på Azure-Virtual Machines (VM)][sap-hana-ha]