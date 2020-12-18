---
title: Regioner som stöder Tillgänglighetszoner i Azure
description: Om du vill skapa hög tillgängliga och elastiska program i Azure kan Tillgänglighetszoner tillhandahålla fysiskt åtskilda platser som du kan använda för att köra dina resurser.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 11/17/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 2aa9a6e5a574d000bde5039ec1f13e307cd84c21
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678778"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Regioner som stöder Tillgänglighetszoner i Azure

Tillgänglighetszoner är ett erbjudande med hög tillgänglighet som skyddar dina program och data från data Center problem. Mer information om Tillgänglighetszoner finns [i regioner och Tillgänglighetszoner i Azure](az-overview.md).

I det här avsnittet visas de Azure-tjänster och regioner som stöder Tillgänglighetszoner.

Tjänster som är tillgängliga i varje region, tillsammans med kommande översikt över tillgänglighet, finns på [produkter tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/).


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
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Databaser** |  |  |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure-datautforskaren](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database (Generell användning nivå)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | röntgen | : heavy_check_mark: (förhands granskning) | : heavy_check_mark: (förhands granskning) | : heavy_check_mark: (förhands granskning) | röntgen |
| [Azure SQL Database (Premium & Affärskritisk nivåer)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analys** |  |  |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrering** |  |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Säkerhet** |  |  |  |  |  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="europe"></a>Europa

| **Produkter** | **Frankrike, centrala** | **Norra Europa** | **Storbritannien, södra** | **Europa, västra** |
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
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Databaser** |  |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure-datautforskaren](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database (Generell användning nivå)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | röntgen | : heavy_check_mark: (förhands granskning) | röntgen | : heavy_check_mark: (förhands granskning) |
| [Azure SQL Database (Premium & Affärskritisk nivåer)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analys** |  |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrering** |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
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
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Databaser** |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure-datautforskaren](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database (Generell användning nivå)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | röntgen | : heavy_check_mark: (förhands granskning) | : heavy_check_mark: (förhands granskning) |
| [Azure SQL Database (Premium & Affärskritisk nivåer)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analys** |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrering** |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Säkerhet** |  |  |  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: |  |





## <a name="other"></a>Övrigt

Azure erbjuder också Tillgänglighetszoner support i följande regioner: • US Gov, Virginia • Sydafrika, norra • södra centrala USA

Om du vill veta mer om Tillgänglighetszoner support i de här tre regionerna kontaktar du din Microsoft-säljare eller kund representant.


## <a name="next-steps"></a>Nästa steg

- [Regioner och tillgänglighetszoner i Azure](az-overview.md)