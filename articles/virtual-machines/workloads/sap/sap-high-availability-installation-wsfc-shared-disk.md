---
title: Installera SAP NetWeaver HA på ett Windows-redundanskluster och en delad disk för en SAP ASCS/SCS-instans i Azure | Microsoft Docs
description: Lär dig hur du installerar SAP NetWeaver HA på ett Windows-redundanskluster och en delad disk för en SAP-ASCS/SCS-instans.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81721cb52035a51cffa2667568914c780b4c397b
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588666"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Installera SAP NetWeaver HA på ett Windows-redundanskluster och en delad disk för en SAP ASCS/SCS-instans i Azure

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
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md



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
[sap-ha-guide-figure-3046-ers2]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ers2-virtual-name-ip.png
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

Den här artikeln beskriver hur du installerar och konfigurerar ett SAP-system med hög tillgänglighet i Azure med hjälp av ett Windows Server-redundanskluster och en klusterdelad disk för att klustra en SAP ASCS/SCS-instans. Som det beskrivs i [arkitektur guiden: kluster en SAP ASCS/SCS-instans på ett Windows-redundanskluster med hjälp av en klusterdelad disk][sap-high-availability-guide-wsfc-shared-disk]finns det två alternativ för *klusterdelad disk*:

- [Delade diskar i Azure](../../disks-shared.md)
- Använda [SIOS DataKeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster/) för att skapa speglad lagring, som simulerar klustrad delad disk 

## <a name="prerequisites"></a>Krav

Granska dessa dokument innan du påbörjar installationen:

* [Arkitektur guide: klustra en SAP ASCS/SCS-instans på ett Windows-redundanskluster med hjälp av en klusterdelad disk][sap-high-availability-guide-wsfc-shared-disk]

* [Förbered Azure-infrastrukturen för SAP-HA med hjälp av ett Windows-redundanskluster och en delad disk för en SAP ASCS/SCS-instans][sap-high-availability-infrastructure-wsfc-shared-disk]

Vi beskriver inte DBMS-installationen i den här artikeln eftersom installationen varierar beroende på vilket DBMS-system du använder. Vi förutsätter att hög tillgänglighets problem med DBMS är riktade mot de funktioner som olika DBMS-leverantörer stöder för Azure. Exempel är AlwaysOn-eller databas spegling för SQL Server och Oracle data Guard för Oracle-databaser. Scenarier med hög tillgänglighet för DBMS omfattas inte av den här artikeln.

Det finns inga särskilda överväganden när olika DBMS-tjänster interagerar med en klustrad SAP-ASCS eller SCS-konfiguration i Azure.

> [!NOTE]
> Installations procedurerna för SAP NetWeaver ABAP Systems, Java Systems och ABAP + Java system är nästan identiska. Den viktigaste skillnaden är att ett SAP ABAP-system har en ASCS-instans. SAP Java-systemet har en SCS-instans. SAP ABAP + Java-systemet har en ASCS-instans och en SCS-instans som körs i samma kluster grupp för Microsoft-redundans. Eventuella installations skillnader för varje SAP NetWeaver-installations stack anges uttryckligen. Du kan anta att resten av stegen är desamma.  

## <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Installera SAP med en ASCS/SCS-instans med hög tillgänglighet

> [!IMPORTANT]
> Placera inte växlings filen på de SIOS DataKeeper speglade volymerna om du använder SIOS för att visa delade diskar. Du kan lämna växlings filen på den temporära enheten D för en virtuell Azure-dator, vilket är standard. Om den inte redan finns där flyttar du Windows-Page-filen till enhet D på den virtuella Azure-datorn.  


Att installera SAP med en ASCS/SCS-instans med hög tillgänglighet omfattar följande uppgifter:

* Skapa ett virtuellt värdnamn för den klustrade SAP-ASCS/SCS-instansen.
* Installera SAP på den första klusternoden.
* Ändra SAP-profilen för ASCS/SCS-instansen.
* Lägg till en avsöknings port.
* Öppna avsöknings porten för Windows-brandväggen.

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Skapa ett virtuellt värdnamn för den klustrade SAP ASCS/SCS-instansen

1. I Windows DNS Manager skapar du en DNS-post för det virtuella värd namnet för ASCS/SCS-instansen.

   > [!IMPORTANT]
   > Den IP-adress som du tilldelar det virtuella värd namnet för ASCS/SCS-instansen måste vara samma som den IP-adress som du tilldelade till Azure Load Balancer.  


   ![Bild 1: definiera DNS-posten för SAP ASCS/SCS-klustrets virtuella namn och TCP/IP-adress][sap-ha-guide-figure-3046]

   _Definiera DNS-posten för SAP ASCS/SCS-klustrets virtuella namn och TCP/IP-adress_

2. Om använder den nya SAP Queue server 2, som också är en klustrad instans, måste du reservera i DNS ett virtuellt värdnamn för ERS2 också. 

   > [!IMPORTANT]
   > IP-adressen som du tilldelar till det virtuella värd namnet för ERS2-instansen måste vara den andra IP-adressen som du tilldelade till Azure Load Balancer.    


   ![Bild 1A: definiera DNS-posten för SAP ASCS/SCS-klustrets virtuella namn och TCP/IP-adress][sap-ha-guide-figure-3046-ers2]

   _Definiera DNS-posten för det virtuella SAP ERS2-klustrets virtuella namn och TCP/IP-adress_


3. Om du vill definiera IP-adressen som är kopplad till det virtuella värd namnet väljer du **DNS Manager-**  >  **domän**.

   ![Bild 2: nytt virtuellt namn och TCP/IP-adress för SAP ASCS/SCS-kluster konfiguration][sap-ha-guide-figure-3047]

   _Nytt virtuellt namn och TCP/IP-adress för SAP ASCS/SCS kluster konfiguration_

### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Installera den första SAP-klusternoden

1. Kör det första alternativet för klusternoden på klusternoden A. Välj:

   * **ABAP system**: **ASCS** instance Number **00**
   * **Java system**: **SCS** instance Number **01**
   * **ABAP + Java-system**: **ASCS** instance Number **00** och **SCS** instance Number **01**


   > [!IMPORTANT]
   > Kom ihåg att konfigurationen i de interna belastnings Utjämnings reglerna för Azures belastnings utjämning (om du använder Basic SKU) och de valda SAP-instans numren måste stämma överens.

2. Följ anvisningarna för att installera SAP. Se till att starta installations alternativet "första klusternod", för att välja alternativet "klusterdelad disk" som konfigurations alternativ.

> [!TIP]
> I installations dokumentationen för SAP beskrivs hur du installerar den första ASCS/SCS-klusternoden.



### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Ändra SAP-profilen för ASCS/SCS-instansen

Om du har en kö för replikering av Server 1 lägger du till SAP Profile-parametern `enque/encni/set_so_keepalive` enligt beskrivningen nedan. Profil parametern förhindrar anslutningar mellan SAP-arbetsprocesser och-servern från att stängas när de är inaktiva för länge. SAP-parametern krävs inte för ERS2. 

1. Lägg till den här profil parametern till instansen av SAP ASCS/SCS, om du använder ERS1.

   ```
   enque/encni/set_so_keepalive = true
   ```

   Se till att `keepalive` OS-parametrarna är inställda enligt beskrivningen i SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736)för både ERS1 och ERS2.   

2. Om du vill tillämpa ändringarna för parametern för SAP-profilen startar du om SAP ASCS/SCS-instansen.

### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Lägg till en avsöknings port

Använd den interna belastningsutjämnaren för att göra så att hela kluster konfigurationen fungerar med Azure Load Balancer. Azures interna belastningsutjämnare distribuerar vanligt vis den inkommande arbets belastningen lika mellan deltagande virtuella datorer.

Detta fungerar dock inte i vissa klusterkonfigurationer eftersom endast en instans är aktiv. Den andra instansen är passiv och kan inte acceptera någon av arbets belastningarna. En avsöknings funktion hjälper när den interna Azure-belastningsutjämnaren identifierar vilken instans som är aktiv och endast är riktad mot den aktiva instansen.  

> [!IMPORTANT]
> I den här exempel konfigurationen är **ProbePort** inställd på 620 **nr**. För SAP ASCS-instansen med Number **00** är den 620 **00**. Du måste ändra konfigurationen så att den matchar dina SAP-instansnamn och SAP SID.

För att lägga till en avsöknings port kör den här PowerShell-modulen på en av de virtuella kluster datorerna:

- När det gäller SAP ASC/SCS-instans 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62000
   ```

- Om du använder ERS2, som är klustrad. Det finns inget behov av att konfigurera avsöknings porten för ERS1, eftersom den inte är klustrad.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62001 -IsSAPERSClusteredInstance $True
   ```

 Koden för funktionen `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` ser ut så här:
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {

    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.

    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 

    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.

    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 

    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.

    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.

    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.

    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.


    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 

    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manualy restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False

    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True

    #> 

        [CmdletBinding()]
        param(

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,

            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,

            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        )

        BEGIN{}

        PROCESS{
            try{                                      

                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 

                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "

                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

                Write-Output " "

                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 

                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5

                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName

                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "

                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
        }
        END {}
    }

   ```

### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Öppna avsöknings porten för Windows-brandväggen

Öppna en avsöknings port för Windows-brandväggen på båda klusternoderna. Använd följande skript för att öppna en avsöknings port för Windows-brandväggen. Uppdatera PowerShell-variablerna för din miljö.  
Om du använder ERS2 måste du också öppna brand Väggs porten för ERS2 PROBE-porten.  

  ```powershell
    $ProbePort = 62000   # ProbePort of the Azure internal load balancer
    New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

## <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Installera databas instansen

Om du vill installera databas instansen följer du processen som beskrivs i installations dokumentationen för SAP.

## <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Installera den andra klusternoden

Följ stegen som beskrivs i installations guiden för SAP för att installera det andra klustret.

## <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Installera den primära SAP-programservern

Installera den primära program servern (PAS) instance \<SID\> -di-0 på den virtuella dator som du har angivit för att vara värd för Pas. Det finns inga beroenden i Azure. Om du använder SIOS finns det inga DataKeeper inställningar.

## <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Installera den ytterligare SAP-programservern

Installera en SAP-ytterligare program Server (AAS) på alla virtuella datorer som du har angivit för att vara värd för en SAP Application Server-instans. 

## <a name="test-the-sap-ascsscs-instance-failover"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testa redundansväxling av SAP ASCS/SCS-instansen

För de utväxlade redundanstestningen-testerna antar vi att SAP ASCS är aktivt på nod A.  

1. Kontrol lera att SAP-systemet kan redundansväxla från nod A till nod B Välj något av dessa alternativ för att initiera en redundansväxling av SAP- \<SID\> klusterresursen från klusternod A till klusternod B:
    - Klusterhanteraren för växling vid fel  
    - PowerShell för redundanskluster

    ```powershell
    $SAPSID = "PR1"     # SAP <SID>

    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```

2. Starta om klusternoden A i Windows gäst operativ system. Detta initierar en automatisk redundansväxling av SAP- \<SID\> kluster gruppen från nod A till nod B.  
3. Starta om klusternoden A från Azure Portal. Detta initierar en automatisk redundansväxling av SAP- \<SID\> kluster gruppen från nod A till nod B.  
4. Starta om klusternoden A med hjälp av Azure PowerShell. Detta initierar en automatisk redundansväxling av SAP- \<SID\> kluster gruppen från nod A till nod B.

5. Verifiering
   - Efter redundansväxlingen kontrollerar du att SAP- \<SID\> kluster gruppen körs på klusternod B. 

      ![Figur 8: i Klusterhanteraren för växling vid fel \< \> körs kluster gruppen SAP sid på klusternod B][sap-ha-guide-figure-5002]

      _I Klusterhanteraren för växling vid fel körs SAP- \<SID\> kluster gruppen på klusternod B_

   - Efter redundansväxlingen är verifiera delad disk nu monterad på klusternod B. 
   - Efter redundansväxlingen, om du använder SIOS, verifierar du att SIOS DataKeeper replikerar data från käll volym enhet S på klusternod B till mål volym enheter i kluster nod A. 

      ![Bild 9: SIOS DataKeeper replikerar den lokala volymen från klusternod B till klusternod A][sap-ha-guide-figure-5003]

      _SIOS DataKeeper replikerar den lokala volymen från klusternod B till klusternod A_