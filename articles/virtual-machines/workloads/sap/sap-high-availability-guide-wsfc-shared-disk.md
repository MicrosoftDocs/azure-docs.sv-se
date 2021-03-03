---
title: Kluster SAP ASCS/SCS-instans på WSFC med delad disk i Azure | Microsoft Docs
description: Lär dig att klustra en SAP ASCS/SCS-instans på ett Windows-redundanskluster med hjälp av en klusterdelad disk.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: f6fb85f8-c77a-4af1-bde8-1de7e4425d2e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c903cf06981e1336ae30942775de11d09bb1299b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675350"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-cluster-shared-disk-in-azure"></a>Klustra en SAP ASCS/SCS-instans på ett Windows-redundanskluster med hjälp av en klusterdelad disk i Azure

> ![Windows OS][Logo_Windows] Windows
>

Windows Server-redundanskluster är grunden för en hög tillgänglig SAP-ASCS/SCS-installation och DBMS i Windows.

Ett redundanskluster är en grupp med 1 + n-oberoende servrar (noder) som arbetar tillsammans för att öka tillgängligheten för program och tjänster. Om ett nodfel inträffar beräknar Windows Server-redundanskluster antalet fel som kan uppstå och fortfarande upprätthåller ett felfritt kluster för att tillhandahålla program och tjänster. Du kan välja mellan olika kvorumresurser för att nå redundanskluster.

## <a name="prerequisites"></a>Förutsättningar
Läs följande artikel innan du påbörjar uppgifterna i den här artikeln:

* [Azure Virtual Machines hög tillgänglighets arkitektur och scenarier för SAP NetWeaver][sap-high-availability-architecture-scenarios]


## <a name="windows-server-failover-clustering-in-azure"></a>Kluster för växling vid fel i Windows Server i Azure

Kluster för växling vid fel i Windows Server med Azure Virtual Machines kräver ytterligare konfigurations steg. När du skapar ett kluster måste du ange flera IP-adresser och virtuella värdnamn för SAP ASCS/SCS-instansen.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Namn matchning i Azure och klustrets virtuella värd namn

Azure Cloud Platform erbjuder inte alternativet att konfigurera virtuella IP-adresser, till exempel flytande IP-adresser. Du behöver en alternativ lösning för att skapa en virtuell IP-adress för att komma åt kluster resursen i molnet. 

Tjänsten Azure Load Balancer tillhandahåller en *intern belastningsutjämnare* för Azure. Med den interna belastningsutjämnaren når klienterna klustret via klustrets virtuella IP-adress. 

Distribuera den interna belastningsutjämnaren i resurs gruppen som innehåller klusternoderna. Konfigurera sedan alla nödvändiga regler för port vidarebefordran med hjälp av avsöknings portarna för den interna belastningsutjämnaren. Klienter kan ansluta via det virtuella värd namnet. DNS-servern matchar klustrets IP-adress och den interna belastningsutjämnaren hanterar port vidarebefordran till klustrets aktiva nod.

> [!IMPORTANT]
> Flytande IP stöds inte på en sekundär NIC-IP-konfiguration i belastnings Utjämnings scenarier. Mer information finns i [begränsningar för Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Om du behöver ytterligare IP-adress för den virtuella datorn distribuerar du ett andra nätverkskort.  

![Bild 1: konfiguration av Windows-redundanskluster i Azure utan en delad disk][sap-ha-guide-figure-1001]

_Konfiguration av redundanskluster för Windows Server i Azure utan en delad disk_

### <a name="sap-ascsscs-ha-with-cluster-shared-disks"></a>SAP ASCS/SCS HA med klusterdelade diskar
I Windows innehåller en SAP ASCS/SCS-instans SAP-centrala tjänster, SAP Message Server, köa Server processer och SAP global Host-filer. SAP global Host-filer lagrar centrala filer för hela SAP-systemet.

En SAP ASCS/SCS-instans har följande komponenter:

* SAP-centrala tjänster:
    * Två processer, ett meddelande och en server, och en \<ASCS/SCS virtual host name> , som används för att komma åt dessa två processer.
    * Fil struktur: S:\usr\sap \\ &lt; sid &gt; \ ASCS/SCS\<instance number\>


* SAP global Hosts-filer:
  * Fil struktur: S:\usr\sap \\ &lt; sid- &gt; \SYS \. ..
  * Fil resursen sapmnt, som ger åtkomst till dessa globala S:\usr\sap- \\ &lt; &gt; \SYS \. ..-filer med hjälp av följande UNC-sökväg:

    \\\\<ASCS/SCS Virtual Host Name \> \Sapmnt \\ &lt; sid &gt; \SYS \. ..


![Bild 2: processer, fil struktur och global värd sapmnt fil resurs för en SAP ASCS/SCS-instans][sap-ha-guide-figure-8001]

_Processer, fil struktur och global värd sapmnt fil resurs för en SAP ASCS/SCS-instans_

I en hög tillgänglighets inställning kan du klustra SAP ASCS/SCS-instanser. Vi använder *klustrade delade diskar* (enhets, i vårt exempel) för att placera SAP-ASCS/SCS-och SAP global-värd-filerna.

![Bild 3: SAP ASCS/SCS HA-arkitektur med delad disk][sap-ha-guide-figure-8002]

_SAP ASCS/SCS HA-arkitektur med delad disk_


Med arkitektur 1 för Server-replikering 1:
* Samma \<ASCS/SCS virtual host name> används för att komma åt SAP-meddelandet och köa Server processer, och SAP global Hosts-filer via fil resursen sapmnt.
* Samma klusterdelade disk enhet är delad mellan dem.  

Med arkitektur 2 för Server replikering 2: 
* Samma \<ASCS/SCS virtual host name> används för att få åtkomst till SAP Message Server-processen och SAP global Hosts-filer via fil resursen sapmnt.
* Samma klusterdelade disk enhet är delad mellan dem.
* Det finns \<ERS virtual host name> en separat åtkomst till Server processen för kön  

![Bild 4: SAP ASCS/SCS HA-arkitektur med delad disk][sap-ha-guide-figure-8003]

_SAP ASCS/SCS HA-arkitektur med delad disk_

#### <a name="shared-disk-and-enqueue-replication-server"></a>Delad disk och transaktionskö för Server 

1. Delad disk stöds med en arkitektur för Server-replikering 1, där ERS-instansen (Queue Replication Server):   

   - är inte klustrad
   - använder `localhost` namn
   - distribueras på lokala diskar på alla klusternoder

2. Delad disk stöds också med Server-till-Server-replikering 2-arkitektur, där ERS2-instansen (Queue Replication Server 2):  

   - är klustrad
   - använder dedikerat virtuellt/nätverks värd namn
   - behöver IP-adressen för det virtuella ERS-värdnamnet som ska konfigureras på interna Azure-Load Balancer, utöver (A) SCS-IP-adressen
   - distribueras på **lokala diskar** på varje klustrad nod, och därför behövs ingen delad disk

   > [!TIP]
   > Du hittar mer information om replikeringsscopet Server 1 och 2 (ERS1 och ERS2) här:  
   > [Köa replikeringstjänsten i ett Microsoft-redundanskluster](https://help.sap.com/viewer/3741bfe0345f4892ae190ee7cfc53d4c/CURRENT_VERSION_SWPM20/en-US/8abd4b52902d4b17a105c2fabdf5c0cf.html)  
   > [Ny köad replikerare i miljöer för redundanskluster](https://blogs.sap.com/2019/03/19/new-enqueue-replicator-in-failover-cluster-environments/)  

#### <a name="options-for-shared-disk-in-azure-for-sap-workloads"></a>Alternativ för delad disk i Azure för SAP-arbetsbelastningar

Det finns två alternativ för delad disk i ett Windows-redundanskluster i Azure:

- [Azure Shared disks](../../disks-shared.md) – funktion som gör det möjligt att ansluta Azure Managed disk till flera virtuella datorer samtidigt. 
- Använd program vara från tredje part [SIOS DataKeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster) för att skapa en speglad lagring som simulerar klusterdelad lagring. 

Tänk på följande när du väljer teknik för delad disk:

**Azure-delad disk för SAP-arbetsbelastningar**
- Gör att du kan ansluta Azure Managed disk till flera virtuella datorer samtidigt utan att behöva ytterligare program vara för att underhålla och fungera 
- Du kommer att arbeta med en enda Azure-delad disk på ett lagrings kluster. Det påverkar tillförlitligheten hos din SAP-lösning.
- För närvarande är den enda distribution som stöds med Azure Shared Premium disk i tillgänglighets uppsättning. Azure Shared disk stöds inte i zonindelade-distribution.     
- Se till att etablera en Azure Premium-disk med en minsta disk storlek som anges i [Premium SSD intervall](../../disks-shared.md#disk-sizes) för att kunna ansluta till det antal virtuella datorer som krävs samtidigt (vanligt vis 2 för SAP ASCS Windows-redundanskluster). 
- Azure Shared Ultra disk stöds inte för SAP-arbetsbelastningar eftersom den inte stöder distribution i tillgänglighets uppsättningar eller zonindelade-distribution.  
 
**SIOS**
- SIOS-lösningen tillhandahåller synkron data replikering i real tid mellan två diskar
- Med SIOS-lösningen som du använder med två hanterade diskar och om du använder antingen tillgänglighets uppsättningar eller tillgänglighets zoner hamnar de hanterade diskarna på olika lagrings kluster. 
- Distribution i tillgänglighets zoner stöds
- Kräver att du installerar och använder program från tredje part, som du behöver köpa vidare

### <a name="shared-disk-using-azure-shared-disk"></a>Delad disk med hjälp av Azure Shared disk

Microsoft erbjuder [Azure delade diskar](../../disks-shared.md), som kan användas för att implementera SAP ASCS/SCS hög tillgänglighet med alternativet för delad disk.

#### <a name="prerequisites-and-limitations"></a>Krav och begränsningar

För närvarande kan du använda Azure Premium SSD-diskar som en Azure-delad disk för SAP ASCS/SCS-instansen. Följande begränsningar är för närvarande på plats:

-  [Azure Ultra disk](../../disks-types.md#ultra-disk) stöds inte som Azure-delad disk för SAP-arbetsbelastningar. För närvarande går det inte att placera virtuella Azure-datorer med Azure Ultra disk i tillgänglighets uppsättning
-  [Azure Shared disk](../../disks-shared.md) med Premium SSD-diskar stöds bara med virtuella datorer i tillgänglighets uppsättningen. Det stöds inte i Tillgänglighetszoner-distribution. 
-  Värdet för Azure-delad disk [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) avgör hur många klusternoder som kan använda den delade disken. Normalt för SAP ASCS/SCS-instans konfigurerar du två noder i Windows-redundanskluster, och därför måste värdet för `maxShares` vara inställt på två.
-  Alla virtuella SAP ASCS/SCS-kluster måste distribueras i samma [placerings grupp för Azure närhet](../../windows/proximity-placement-groups.md).   
   Även om du kan distribuera virtuella Windows-kluster i tillgänglighets uppsättning med Azure Shared disk utan PPG, ser PPG till att det går nära fysiskt nära Azure-delade diskar och de virtuella datorerna i klustret, vilket ger kortare latens mellan de virtuella datorerna och lagrings skiktet.    

Mer information om begränsningar för Azure Shared disk finns i avsnittet [begränsningar](../../disks-shared.md#limitations) i dokumentationen för Azure Shared disk.

> [!IMPORTANT]
> När du distribuerar SAP ASCS/SCS Windows-redundanskluster med Azure Shared disk bör du vara medveten om att distributionen kommer att fungera med en enda delad disk i ett lagrings kluster. Din SAP ASCS/SCS-instans skulle påverkas, i händelse av problem med lagrings klustret, där den Azure-delade disken distribueras.    

> [!TIP]
> Granska [SAP-NetWeaver på Azures planerings guide](./planning-guide.md) och [Azure Storages guide för SAP-arbetsbelastningar](./planning-guide-storage.md) för viktiga överväganden när du planerar SAP-distributionen.

### <a name="supported-os-versions"></a>OS-versioner som stöds

Både Windows Server 2016 och 2019 stöds (Använd de senaste data Center avbildningarna).

Vi rekommenderar starkt att du använder **Windows Server 2019 Data Center** som:
- Windows 2019-kluster för växling vid fel är Azure medveten
- Vi har lagt till integrering och medvetenhet om underhåll av Azure-värd och bättre erfarenhet genom övervakning av Azure Schedule-händelser.
- Det går att använda det distribuerade nätverks namnet (det är standard alternativet). Det finns därför inget behov av att ha en dedikerad IP-adress för klustrets nätverks namn. Du behöver inte heller konfigurera den här IP-adressen på intern Azure-Load Balancer. 

### <a name="shared-disks-in-azure-with-sios-datakeeper"></a>Delade diskar i Azure med SIOS DataKeeper

Ett annat alternativ för delad disk är att använda program vara från tredje part SIOS DataKeeper Cluster Edition för att skapa en speglad lagring som simulerar delat kluster lagring. SIOS-lösningen tillhandahåller synkron data replikering i real tid.

Så här skapar du en delad disk resurs för ett kluster:

1. Koppla ytterligare en disk till var och en av de virtuella datorerna i en Windows-kluster konfiguration.
2. Kör SIOS DataKeeper Cluster Edition på båda noderna för virtuella datorer.
3. Konfigurera SIOS DataKeeper Cluster Edition så att den speglar innehållet i den extra disk-anslutna volymen från den virtuella käll datorn till den ytterligare disk som är ansluten till den virtuella mål datorn. SIOS DataKeeper sammanfattar käll-och mål volymerna och visar dem sedan Windows Server-redundanskluster som en delad disk.

Få mer information om [SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![Bild 5: konfiguration av redundanskluster för Windows Server i Azure med SIOS DataKeeper][sap-ha-guide-figure-1002]

_Konfiguration av Windows-redundanskluster i Azure med SIOS DataKeeper_

> [!NOTE]
> Du behöver inte delade diskar för hög tillgänglighet med vissa DBMS-produkter, t. ex. SQL Server. SQL Server AlwaysOn replikerar DBMS-data och loggfiler från den lokala disken på en klusternod till den lokala disken på en annan klusternod. I det här fallet behöver konfigurationen för Windows-klustret ingen delad disk.
>

## <a name="next-steps"></a>Nästa steg

* [Förbered Azure-infrastrukturen för SAP-HA med hjälp av ett Windows-redundanskluster och en delad disk för en SAP ASCS/SCS-instans][sap-high-availability-infrastructure-wsfc-shared-disk]

* [Installera SAP NetWeaver HA på ett Windows-redundanskluster och en delad disk för en SAP-ASCS/SCS-instans][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID-konfiguration med hög tillgänglighet)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md