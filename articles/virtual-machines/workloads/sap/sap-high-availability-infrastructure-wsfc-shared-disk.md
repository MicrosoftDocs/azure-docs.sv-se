---
title: Azure-infrastruktur för SAP ASCS/SCS med WSFC&delad disk | Microsoft Docs
description: Lär dig hur du förbereder Azure-infrastrukturen för SAP HA med hjälp av ett Windows-redundanskluster och en delad disk för en SAP ASCS/SCS-instans.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4218b4c00b79d78965eaf6e73028e63f52b1ff17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673616"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Förbered Azure-infrastrukturen för SAP-HA med hjälp av ett Windows-redundanskluster och en delad disk för SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

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

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows OS][Logo_Windows] Windows


Den här artikeln beskriver de steg som du vidtar för att förbereda Azure-infrastrukturen för att installera och konfigurera en ASCS-instans med hög tillgänglighet i ett Windows-redundanskluster med hjälp av en *klusterdelad disk* som ett alternativ för att klustra en SAP ASCS-instans.
Två alternativ för *klusterdelad disk* visas i dokumentationen:

- [Delade diskar i Azure](../../disks-shared.md)
- Använda [SIOS DataKeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster/) för att skapa speglad lagring, som simulerar klustrad delad disk 

Den uppvisade konfigurationen förlitar sig på [Azure närhets placerings grupper (PPG)](./sap-proximity-placement-scenarios.md) för att uppnå optimal nätverks fördröjning för SAP-arbetsbelastningar. Dokumentationen behandlar inte databas skiktet.  

> [!NOTE]
> Placerings grupper för Azure närhet är nödvändiga för att använda Azure Shared disk.
 

## <a name="prerequisites"></a>Förutsättningar

Läs igenom den här artikeln innan du påbörjar installationen:

* [Arkitektur guide: klustra en SAP ASCS/SCS-instans på ett Windows-redundanskluster med hjälp av en klusterdelad disk][sap-high-availability-guide-wsfc-shared-disk]

## <a name="create-the-ascs-vms"></a>Skapa de virtuella ASCS-datorerna

För SAP ASCS/SCS-kluster distribuerar du två virtuella datorer i tillgänglighets uppsättningen för Azure. Distribuera de virtuella datorerna i samma närhets placerings grupp. När de virtuella datorerna har distribuerats:  
- Skapa en intern Azure-Load Balancer för SAP ASCS/SCS-instansen 
- Lägga till virtuella Windows-datorer i AD-domänen

Värd namnen och IP-adresserna för det presenterade scenariot är:

| Värd namns roll | Värdnamn | Statisk IP-adress | Tillgänglighetsuppsättning | Placerings grupp för närhet |
| --- | --- | --- |---| ---|
| första klusternoden ASCS/SCS-kluster |PR1-ASCs-10 |10.0.0.4 |PR1-ASCs-avset |PR1PPG |
| andra klusternoder ASCS/SCS-kluster |PR1-ASCs-11 |10.0.0.5 |PR1-ASCs-avset |PR1PPG |
| Kluster nätverks namn | pr1clust |10.0.0.42 (**endast** för Win 2016-kluster) | saknas | saknas |
| Nätverks namn för ASCS-kluster | pr1-ascscl |10.0.0.43 | saknas | saknas |
| ERS kluster nätverks namn (**endast** för ERS2) | pr1-erscl |10.0.0.44 | saknas | saknas |


## <a name="create-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Skapa intern Azure-belastningsutjämnare

SAP ASCS, SAP SCS och den nya SAP-ERS2 använder du virtuella värdnamn och virtuella IP-adresser. I Azure krävs en [belastningsutjämnare](../../../load-balancer/load-balancer-overview.md) för att använda en virtuell IP-adress. Vi rekommenderar starkt att du använder [standard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). 

> [!IMPORTANT]
> Flytande IP stöds inte på en sekundär NIC-IP-konfiguration i belastnings Utjämnings scenarier. Mer information finns i [begränsningar för Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Om du behöver ytterligare IP-adress för den virtuella datorn distribuerar du ett andra nätverkskort.    


I följande lista visas konfigurationen av belastningsutjämnaren (A) SCS/ERS. Konfigurationen för både SAP ASCS och ERS2 i utförs i samma Azure Load Balancer.  

**En SCS**
- Konfiguration av klient del
    - Statisk ASCS/SCS IP- **10.0.0.43**
- Server dels konfiguration  
    Lägg till alla virtuella datorer som ska ingå i (A) SCS/ERS-klustret. I det här exemplet på virtuella datorer **PR1-ASCs-10** och **PR1-ASCs-11**.
- Avsöknings port
    - Port 620 **nr** lämna standard alternativet för protokoll (TCP), intervall (5), tröskelvärde för fel (2)
- Belastnings Utjämnings regler
    - Om du använder Standard Load Balancer väljer du HA-portar
    - Om du använder grundläggande Load Balancer skapa belastnings Utjämnings regler för följande portar
        - 32 **nr** TCP
        - 36 **nr** TCP
        - 39 **nr** TCP
        - 81 **nr** TCP
        - 5 **nr** 13 TCP
        - 5 **nr** 14 TCP
        - 5 **nr** 16 TCP

    - Kontrol lera att tids gränsen för inaktivitet (minuter) är inställd på max värdet 30 och att den flytande IP-adressen (direkt Server retur) är aktive rad.

**ERS2**

Eftersom ERS2 (Queue server 2) också är klustrad måste ERS2 virtuell IP-adress också konfigureras på Azure ILB förutom över SAP ASCS/SCS IP. Det här avsnittet gäller endast om du använder en arkitektur för Server 2 i kön.  
- konfiguration av andra klient delen
    - Statisk SAP ERS2 IP- **10.0.0.44**

- Server dels konfiguration  
  De virtuella datorerna har redan lagts till i ILB-adresspoolen.  

- andra avsöknings port
    - Port 621 **nr**  
    Lämna standard alternativet för protokoll (TCP), intervall (5), tröskelvärde för fel (2)

- andra regler för belastnings utjämning
    - Om du använder Standard Load Balancer väljer du HA-portar
    - Om du använder grundläggande Load Balancer skapa belastnings Utjämnings regler för följande portar
        - 32 **nr** TCP
        - 33 **nr** TCP
        - 5 **nr** 13 TCP
        - 5 **nr** 14 TCP
        - 5 **nr** 16 TCP

    - Kontrol lera att tids gränsen för inaktivitet (minuter) är inställd på max värdet 30 och att den flytande IP-adressen (direkt Server retur) är aktive rad.


> [!TIP]
> Med [Azure Resource Manager mall för WSFC för SAP ASCS/SCS-instansen med Azure Shared disk](https://github.com/robotechredmond/301-shared-disk-sap)kan du automatisera infrastruktur förberedelserna med hjälp av Azure Shared disk för en SAP sid med ERS1.  
> Azure ARM-mallen kommer att skapa två Windows 2019-eller 2016-datorer, skapa en Azure-delad disk och ansluta till de virtuella datorerna. Azure Internal Load Balancer skapas och konfigureras också. Mer information finns i ARM-mallen. 

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Lägg till register poster på båda klusternoderna för ASCS/SCS-instansen

Azure Load Balancer kan stänga anslutningar, om anslutningarna är inaktiva under en period och överskrider tids gränsen för inaktivitet. SAP-arbetsprocesserna öppnar anslutningar till SAP-kön så snart den första kön/avqueue-begäran måste skickas. Undvik att avbryta dessa anslutningar genom att ändra TCP/IP-KeepAliveTime och KeepAliveInterval-värden på båda klusternoderna. Om du använder ERS1 är det också nödvändigt att lägga till parametrar för SAP-profiler, enligt beskrivningen längre fram i den här artikeln.
Följande register poster måste ändras på båda klusternoderna:

- KeepAliveTime
- KeepAliveInterval

| Sökväg| Variabelnamn | Variabel typ  | Värde | Dokumentation |
| --- | --- | --- |---| ---|
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveTime |REG_DWORD (decimal) |120000 |[KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveInterval |REG_DWORD (decimal) |120000 |[KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |


Om du vill tillämpa ändringarna startar du om båda klusternoderna.
  
## <a name="add-the-windows-vms-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Lägg till de virtuella Windows-datorerna i domänen
När du har tilldelat statiska IP-adresser till de virtuella datorerna lägger du till de virtuella datorerna i domänen. 

## <a name="install-and-configure--windows-failover-cluster"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Installera och konfigurera Windows-redundanskluster 

### <a name="install-the-windows-failover-cluster-feature"></a>Installera funktionen Windows-redundanskluster

Kör det här kommandot på en av klusternoderna:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # Install Windows features.
    # After the feature installs, manually reboot both nodes
    Invoke-Command $ClusterNodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools }
   ```

Starta om båda klusternoderna när funktions installationen har slutförts.  

### <a name="test-and-configure-windows-failover-cluster"></a>Testa och konfigurera Windows-redundanskluster 

I Windows 2019 kommer klustret automatiskt att identifiera att det körs i Azure, och som standard alternativ för kluster hanteringens IP, använder det distribuerade nätverks namn. Därför kommer den att använda alla de lokala IP-adresserna för klusternoderna. Därför behöver inte ett dedikerat (virtuellt) nätverks namn för klustret, och det finns inget behov av att konfigurera den här IP-adressen på interna Azure-Load Balancer.

Mer information finns i [Windows Server 2019-redundanskluster nya funktioner](https://techcommunity.microsoft.com/t5/failover-clustering/windows-server-2019-failover-clustering-new-features/ba-p/544029) kör det här kommandot på en av klusternoderna:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # IP adress for cluster network name is needed ONLY on Windows Server 2016 cluster
    $ClusterStaticIPAddress = "10.0.0.42"
        
    # Test cluster
    Test-Cluster –Node $ClusterNodes -Verbose
    
    $ComputerInfo = Get-ComputerInfo
    
    $WindowsVersion = $ComputerInfo.WindowsProductName
    
    if($WindowsVersion -eq "Windows Server 2019 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2019 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes -Verbose
    }elseif($WindowsVersion -eq "Windows Server 2016 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2016 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes –StaticAddress $ClusterStaticIPAddress -Verbose 
    }else{
        Write-Error "Not supported Windows version!"
    }
   ```

### <a name="configure-cluster-cloud-quorum"></a>Konfigurera kluster molnets kvorum
När du använder Windows Server 2016 eller 2019 rekommenderar vi att du konfigurerar [Azure Cloud-vittnet](/windows-server/failover-clustering/deploy-cloud-witness), som klusterkvorum.

Kör det här kommandot på en av klusternoderna:

   ```powershell
    $AzureStorageAccountName = "cloudquorumwitness"
    Set-ClusterQuorum –CloudWitness –AccountName $AzureStorageAccountName -AccessKey <YourAzureStorageAccessKey> -Verbose
   ```

### <a name="tuning-the-windows-failover-cluster-thresholds"></a>Justera tröskelvärdena för Windows-redundanskluster
 
När du har installerat Windows-redundansklustret måste du justera vissa tröskelvärden så att de passar kluster som distribueras i Azure. De parametrar som ska ändras dokumenteras i [Justera tröskelvärden för redundanskluster i nätverket](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834). Om du antar att de två virtuella datorerna som utgör Windows-klustrets konfiguration för ASCS/SCS finns i samma undernät, ändrar du följande parametrar till följande värden:
- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

De här inställningarna har testats med kunder och ger en bättre kompromiss. De är tillräckligt flexibla, men de ger även redundans som är tillräckligt snabbt för att få riktiga fel villkor i SAP-arbetsbelastningar eller VM-fel.  

## <a name="configure-azure-shared-disk"></a>Konfigurera Azure Shared disk
Det här avsnittet är endast tillämpligt om du använder Azure Shared disk. 

### <a name="create-and-attach-azure-shared-disk-with-powershell"></a>Skapa och ansluta Azure Shared disk med PowerShell
Kör det här kommandot på en av klusternoderna. Du måste justera värdena för resurs gruppen, Azure-regionen, SAPSID och så vidare.  

   ```powershell
    #############################
    # Create Azure Shared Disk
    #############################
    
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyAzureRegion"
    $SAPSID = "PR1"
    
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    
    # With parameter '-MaxSharesCount', we define the maximum number of cluster nodes to attach the shared disk
    $NumberOfWindowsClusterNodes = 2
            
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "$SAPSID-ascs-10"
    
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "$SAPSID-ascs-11"
    
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```

### <a name="format-the-shared-disk-with-powershell"></a>Formatera den delade disken med PowerShell
1. Hämta disk numret. Kör dessa PowerShell-kommandon på en av klusternoderna:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 2      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Formatera disken. I det här exemplet är det disk nummer 2. 

   ```powershell
    # Format SAP ASCS Disk number '2', with drive letter 'S'
    $SAPSID = "PR1"
    $DiskNumber = 2
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR1SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Kontrol lera att disken nu är synlig som en kluster disk.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : 88ff1d94-0cf1-4c70-89ae-cbbb2826a484
    # Name       : Cluster Disk 1
    # Number     : 2
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk2\Partition2\}
   ```
4. Registrera disken i klustret.  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 1 Online Available Storage Physical Disk
   ```

## <a name="sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> SIOS DataKeeper Cluster Edition för SAP ASCS/SCS Cluster Share disk
Det här avsnittet är endast tillämpligt om du använder program vara från tredje part SIOS DataKeeper Cluster Edition för att skapa en speglad lagring som simulerar klusterdelad disk.  

Nu har du en fungerande konfiguration av redundanskluster för Windows Server i Azure. Om du vill installera en SAP ASCS/SCS-instans behöver du en delad disk resurs. Ett av alternativen är att använda SIOS DataKeeper Cluster Edition är en lösning från tredje part som du kan använda för att skapa delade disk resurser.  

Att installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS-klustrets resurs disk omfattar följande uppgifter:
- Lägg till Microsoft .NET Framework, om det behövs. Se [SIOS-dokumentationen] (( https://us.sios.com/products/datakeeper-cluster/) för de senaste .NET Framework-kraven 
- Installera SIOS DataKeeper
- Konfigurera SIOS-DataKeeper

### <a name="install-sios-datakeeper"></a>Installera SIOS DataKeeper
Installera SIOS DataKeeper Cluster Edition på varje nod i klustret. Skapa en virtuell delad lagring med SIOS DataKeeper genom att skapa en synkroniserad spegling och sedan simulera klusterdelad lagring.

Innan du installerar SIOS-programvaran skapar du DataKeeperSvc-domän användaren.

> [!NOTE]
> Lägg till domän användaren DataKeeperSvc i den lokala administratörs gruppen på båda klusternoderna.
>

1. Installera SIOS-programvaran på båda klusternoderna.

   ![Installations program för SIOS][sap-ha-guide-figure-3030]

   ![Bild 31: första sidan i SIOS DataKeeper-installationen][sap-ha-guide-figure-3031]

   _Första sidan i SIOS DataKeeper-installationen_

2. Välj **Ja** i dialog rutan.

   ![Bild 32: DataKeeper informerar dig om att en tjänst kommer att inaktive ras][sap-ha-guide-figure-3032]

   _DataKeeper informerar dig om att en tjänst kommer att inaktive ras_

3. I dialog rutan rekommenderar vi att du väljer domän- **eller Server konto**.

   ![Bild 33: val av användare för SIOS-DataKeeper][sap-ha-guide-figure-3033]

   _Val av användare för SIOS-DataKeeper_

4. Ange det användar namn och lösen ord för domän kontot som du skapade för SIOS-DataKeeper.

   ![Bild 34: ange domän användar namn och lösen ord för SIOS DataKeeper-installationen][sap-ha-guide-figure-3034]

   _Ange domänens användar namn och lösen ord för SIOS DataKeeper-installationen_

5. Installera licens nyckeln för SIOS DataKeeper-instansen enligt bilden 35.

   ![Bild 35: Ange licens nyckeln för SIOS DataKeeper][sap-ha-guide-figure-3035]

   _Ange licens nyckeln för SIOS DataKeeper_

6. Starta om den virtuella datorn när du uppmanas till det.

### <a name="configure-sios-datakeeper"></a>Konfigurera SIOS-DataKeeper
När du har installerat SIOS DataKeeper på båda noderna startar du konfigurationen. Målet med konfigurationen är att ha synkron datareplikering mellan de ytterligare diskar som är anslutna till var och en av de virtuella datorerna.

1. Starta hanterings-och konfigurations verktyget för DataKeeper och välj sedan **Anslut Server**.

   ![Bild 36: hanterings-och konfigurations verktyg för SIOS DataKeeper][sap-ha-guide-figure-3036]

   _Hanterings-och konfigurations verktyg för SIOS-DataKeeper_

2. Ange namnet eller TCP/IP-adressen för den första noden som hanterings-och konfigurations verktyget ska ansluta till och, i ett andra steg, den andra noden.

   ![Bild 37: Infoga namnet eller TCP/IP-adressen för den första noden som hanterings-och konfigurations verktyget ska ansluta till, och i ett andra steg, den andra noden][sap-ha-guide-figure-3037]

   _Infoga namnet eller TCP/IP-adressen för den första noden som hanterings-och konfigurations verktyget ska ansluta till, och i ett andra steg, den andra noden_

3. Skapa ett replikerings jobb mellan de två noderna.

   ![Bild 38: skapa ett jobb för replikering][sap-ha-guide-figure-3038]

   _Skapa ett jobb för replikering_

   En guide vägleder dig genom processen med att skapa ett migreringsjobb.

4. Definiera namnet på replikeringslänken.

   ![Bild 39: definiera namnet på replikeringssystemet][sap-ha-guide-figure-3039]

   _Definiera namnet på det replikerade jobbet_

   ![Bild 40: definiera grunddata för noden, som ska vara den aktuella Källnoden][sap-ha-guide-figure-3040]

   _Definiera grunddata för noden, som ska vara den aktuella Källnoden_

5. Definiera namn, TCP/IP-adress och disk volym för målnoden.

   ![Bild 41: definiera namn, TCP/IP-adress och disk volym för den aktuella målnoden][sap-ha-guide-figure-3041]

   _Definiera namn, TCP/IP-adress och disk volym för den aktuella målnoden_

6. Definiera algoritmerna för komprimering. I vårt exempel rekommenderar vi att du komprimerar replik strömmen. I synnerhet i omsynkroniseringen minskar replikeringen av replikeringsuppsättningen dramatiskt omsynkroniseringen avsevärt. Komprimering använder processor-och RAM-resurser för en virtuell dator. I takt med att komprimerings hastigheten ökar, så är volymen av de processor resurser som används. Du kan ändra den här inställningen senare.

7. En annan inställning som du måste kontrol lera är om replikeringen sker asynkront eller synkront. När du skyddar SAP ASCS/SCS-konfigurationer måste du använda synkron replikering.  

   ![Bild 42: definiera replikeringsinformation][sap-ha-guide-figure-3042]

   _Definiera replikeringsinformation_

8. Ange om den volym som replikeras av replikeringssystemet ska representeras för en Windows Server-redundanskluster som en delad disk. För SAP ASCS/SCS-konfigurationen väljer du **Ja** så att Windows-klustret ser den replikerade volymen som en delad disk som kan användas som en kluster volym.

   ![Bild 43: Välj Ja för att ställa in den replikerade volymen som en kluster volym][sap-ha-guide-figure-3043]

   _Välj **Ja** för att ställa in den replikerade volymen som en kluster volym_

   När volymen har skapats visar hanterings-och konfigurations verktyget för DataKeeper att replikeringsläget är aktivt.

   ![Bild 44: DataKeeper synkron spegling för resursen SAP ASCS/SCS är aktiv][sap-ha-guide-figure-3044]

   _DataKeeper synkron spegling för resurs disken för SAP ASCS/SCS är aktiv_

   Klusterhanteraren för växling vid fel visar nu disken som en DataKeeper disk, som du ser i bild 45:

   ![Bild 45: Klusterhanteraren för växling vid fel visar den disk som DataKeeper replikerade][sap-ha-guide-figure-3045]

   _Klusterhanteraren för växling vid fel visar den disk som DataKeeper replikerade_


## <a name="next-steps"></a>Nästa steg

* [Installera SAP NetWeaver HA med hjälp av ett Windows-redundanskluster och en delad disk för en SAP-ASCS/SCS-instans][sap-high-availability-installation-wsfc-shared-disk]