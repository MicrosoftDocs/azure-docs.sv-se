---
title: Azure-tjänster som har stöd för tillgänglighetszoner
description: Om du vill skapa hög tillgängliga och elastiska program i Azure kan Tillgänglighetszoner tillhandahålla fysiskt åtskilda platser som du kan använda för att köra dina resurser.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 12/17/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: ea21a56249c8eaa7c6b329b755e96b21b91ebaa1
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632316"
---
# <a name="azure-services-that-support-availability-zones"></a>Azure-tjänster som har stöd för tillgänglighetszoner

Tillgänglighetszoner är ett erbjudande med hög tillgänglighet som skyddar dina program och data från data Center problem. En lista över befintliga och kommande regioner som stöder Tillgänglighetszoner finns i [regioner och Tillgänglighetszoner i Azure](az-overview.md).  

I det här avsnittet visas de Azure-tjänster som stöder Tillgänglighetszoner. 

Tjänster som är tillgängliga i varje region, tillsammans med den kommande översikten över tillgänglighet, finns på [produkter tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/).

Alla Azures hanterings tjänster är utformad för att vara elastiska från problem på regions nivå. I spektrumet av problem har en eller flera tillgänglighets zon problem inom en region en mindre felradie jämfört med ett hela regions haveri. Azure kan återställas från fel i hanterings tjänster på zon nivå inom en region. Azure utför kritiska underhåll en zon i taget inom en region, för att förhindra att eventuella problem påverkar kund resurser som distribueras i Tillgänglighetszoner inom en region.


![Konceptuell visning av en Azure-region med 3 zoner](./media/az-region/azure-region-availability-zones.png)


Azure-tjänster som stöder Tillgänglighetszoner delas in i tre kategorier: **zonindelade**, **zon-redundanta** och **icke-regionala** tjänster. Kund arbets belastningar kan kategoriseras för att använda något av dessa arkitektur scenarier för att möta programmets prestanda och hållbarhet.

- **Zonindelade Services** – en resurs kan distribueras till en viss, själv vald tillgänglighets zon för att uppnå mer strängare svars tider eller prestanda krav.  Återhämtning är självutformad genom att replikera program och data till en eller flera zoner i regionen.  Resurser kan fästas i en speciell zon. Till exempel kan virtuella datorer, hanterade diskar eller standard-IP-adresser fästas i en speciell zon, vilket ger ökad återhämtning genom att en eller flera instanser av resurser sprids mellan zoner.

- **Zoner – redundanta tjänster** – Azure-plattformen replikerar resursen och data mellan zoner.  Microsoft hanterar leverans av hög tillgänglighet eftersom Azure automatiskt replikerar och distribuerar instanser inom regionen.  ZRS replikerar exempelvis data över tre zoner så att ett zon haveri inte påverkar datans data. 

- **Icke-regionala tjänster** – tjänster som inte är beroende av en speciell Azure-region, vilket gör dem elastiska till globala drifts avbrott samt verksamhets-och drift avbrott.


För att uppnå omfattande affärs kontinuitet i Azure kan du bygga din program arkitektur genom att kombinera Tillgänglighetszoner med Azures region par. Du kan synkront replikera dina program och data med hjälp av Tillgänglighetszoner inom en Azure-region för hög tillgänglighet och asynkront replikera över Azure-regioner för haveri beredskaps skydd. Läs mer i [skapa lösningar för hög tillgänglighet med hjälp av Tillgänglighetszoner](https://docs.microsoft.com/azure/architecture/high-availability/building-solutions-for-high-availability). 


### <a name="azure-services-supporting-availability-zones"></a>Azure-tjänster som stöder Tillgänglighetszoner

- Äldre generationens virtuella datorer visas inte nedan. Mer information finns i [tidigare generationer av virtuella dator storlekar](../virtual-machines/sizes-previous-gen.md).

- Vissa tjänster är icke-regionala, se [regioner och Tillgänglighetszoner i Azure](az-overview.md) för mer information. Tjänsterna är inte beroende av en speciell Azure-region, vilket gör att de är elastiska för globala drift avbrott och drift avbrott i hela regionen.  Du hittar en lista över icke-regionala tjänster på [produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/).



## <a name="americas"></a>Nord- och Sydamerika

| **Produkter** | **USA, centrala** | **East US** | **USA, östra 2** | **USA, västra 2** | **Kanada, centrala** |
|--|--|--|--|--|--|
| **Beräkning** |  |  |  |  |  |
| [App Service miljöer (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines skalnings uppsättningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 
| **Containrar** |  |  |  |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Container Registry](../container-registry/zone-redundancy.md) |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  | 
| **Storage** |  |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Premium Files-lagring](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob Storage](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Nätverk** |  |  |  |  |  |
| [Application Gateway v2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express-väg](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Standard-IP-adress](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Databaser** |  |  |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure-datautforskaren](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for MySQL-flexibel Server](../mysql/flexible-server/concepts-high-availability.md) | röntgen | röntgen | :heavy_check_mark: | :heavy_check_mark: | röntgen |
| [Azure Database for PostgreSQL-flexibel Server](../postgresql/flexible-server/overview.md) | röntgen | röntgen | :heavy_check_mark: | :heavy_check_mark: | röntgen |
| [Azure SQL Database (Generell användning nivå)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | röntgen | : heavy_check_mark: (förhands granskning) | : heavy_check_mark: (förhands granskning) | : heavy_check_mark: (förhands granskning) | röntgen |
| [Azure SQL Database (Premium & Affärskritisk nivåer)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analys** |  |  |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrering** |  |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Hantering och styrning** |  |  |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | röntgen | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | röntgen |
| **Säkerhet** |  |  |  |  |  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="europe"></a>Europa

| **Produkter** | **Frankrike, centrala** | **Europa, norra** | **Storbritannien, södra** | **Europa, västra** |
|--|--|--|--|--|
| **Beräkning** |  |  |  |  |
| [App Service miljöer (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines skalnings uppsättningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Premium Files-lagring](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Blob Storage](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Nätverk** |  |  |  |  |
| [Application Gateway v2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express-väg](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Standard-IP-adress](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Databaser** |  |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure-datautforskaren](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for MySQL-flexibel Server](../mysql/flexible-server/concepts-high-availability.md) | röntgen | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for PostgreSQL-flexibel Server](../postgresql/flexible-server/overview.md) | röntgen | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database (Generell användning nivå)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | : heavy_check_mark: (förhands granskning) | : heavy_check_mark: (förhands granskning) | röntgen | : heavy_check_mark: (förhands granskning) |
| [Azure SQL Database (Premium & Affärskritisk nivåer)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analys** |  |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrering** |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Hantering och styrning** |  |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :heavy_check_mark: | röntgen | :heavy_check_mark: |
| **Säkerhet** |  |  |  |  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>Asien och stillahavsområdet



| **Produkter** | **Japan, östra** | **Sydostasien** | **Australien, östra** |
|--|--|--|--|
| **Beräkning** |  |  |  |
| [App Service miljöer (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines skalnings uppsättningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Premium Files-lagring](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob Storage](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Nätverk** |  |  |  |
| [Application Gateway v2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express-väg](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Standard-IP-adress](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Databaser** |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure-datautforskaren](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for MySQL-flexibel Server](../mysql/flexible-server/concepts-high-availability.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for PostgreSQL-flexibel Server](../postgresql/flexible-server/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database (Generell användning nivå)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | : heavy_check_mark: (förhands granskning) | : heavy_check_mark: (förhands granskning) | : heavy_check_mark: (förhands granskning) |
| [Azure SQL Database (Premium & Affärskritisk nivåer)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analys** |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrering** |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Hantering och styrning** |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | röntgen | röntgen |
| **Säkerhet** |  |  |  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: |  |


## <a name="upcoming-availability-zones"></a>Kommande Tillgänglighetszoner 

Azure erbjuder Tillgänglighetszoner support i följande regioner:
- US Gov, Virginia
- Sydafrika, norra
- USA, södra centrala
- Tyskland, västra centrala

Listan över befintliga och kommande regioner som har stöd för Tillgänglighetszoner finns [här](https://azure.microsoft.com/global-infrastructure/geographies/).    

Om du vill veta mer om Tillgänglighetszoner support i dessa regioner kontaktar du din Microsoft-säljare eller kund representant.


## <a name="pricing-for-vms-in-availability-zones"></a>Priser för virtuella datorer i Tillgänglighetszoner

Det kostar ingen ytterligare kostnad för virtuella datorer som distribueras i en tillgänglighets zon. Mer information finns på [prissättnings sidan för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Kom igång med Tillgänglighetszoner

- [Skapa en virtuell dator](../virtual-machines/windows/create-portal-availability-zone.md)
- [Lägga till en hanterad disk med PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Skapa en virtuell dators skalnings uppsättning för redundant virtuell dator](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Belastningsutjämna virtuella datorer över zoner med hjälp av en Standard Load Balancer med en zon redundant klient del](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [Belastningsutjämna virtuella datorer inom en zon med hjälp av en Standard Load Balancer med en zonindelade-frontend](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [Zonredundant lagring](../storage/common/storage-redundancy.md)
- [SQL Database generell användnings nivå](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Geohaveriberedskap för Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geohaveriberedskap för Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Skapa en zonredundant virtuell nätverksgateway](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Lägg till redundant region i zonen för Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Komma igång Azure cache för Redis Tillgänglighetszoner](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Skapa en Azure Active Directory Domain Services-instans](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)
- [Skapa ett Azure Kubernetes service-kluster (AKS) som använder Tillgänglighetszoner](../aks/availability-zones.md)


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Regioner och tillgänglighetszoner i Azure](az-overview.md)
