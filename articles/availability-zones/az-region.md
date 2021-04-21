---
title: Azure-tjänster som har stöd för tillgänglighetszoner
description: Om du vill skapa program med hög tillgång och återhämtning i Azure Tillgänglighetszoner tillhandahålla fysiskt separata platser som du kan använda för att köra dina resurser.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 04/21/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 4c592c2d67df1e792200cc36449a6268807bbb56
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816268"
---
# <a name="azure-services-that-support-availability-zones"></a>Azure-tjänster som har stöd för tillgänglighetszoner

Microsoft Azure global infrastruktur utformas och konstrueras på varje lager för att leverera högsta möjliga redundans och återhämtning till sina kunder. Azure-infrastrukturen består av geografiska områden, regioner och Tillgänglighetszoner, som begränsar radien för ett haveri och därmed begränsar den potentiella påverkan på kundprogram och data. Den Azure-tillgänglighetszoner har utvecklats för att tillhandahålla en programvaru- och nätverkslösning för att skydda mot datacenterfel och för att ge våra kunder ökad hög tillgänglighet (HA).

Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon består av ett eller flera datacenter med oberoende ström, kylning och nätverk. Den fysiska avgränsningen av Tillgänglighetszoner inom en region begränsar påverkan på program och data från zonfel, till exempel storskaliga översvämningar, större stormar och superstormingar och andra händelser som kan störa åtkomsten till webbplatser, säker genomgångar, längre drifttid för hjälpmedel och tillgängligheten för resurser. Tillgänglighetszoner och deras associerade datacenter är utformade så att tjänster, kapacitet och tillgänglighet stöds av de andra Tillgänglighetszoner i regionen om en zon komprometteras.

Alla Azure-hanteringstjänster är uppritade för att vara motståndskraftiga mot fel på regionnivå. I spektrumet av fel har ett eller flera fel i en tillgänglighetszon inom en region en mindre felradie jämfört med ett helt regionsfel. Azure kan återställas från ett fel på zonnivå för hanteringstjänster inom en region. Azure utför kritiskt underhåll en zon i taget inom en region för att förhindra eventuella fel som påverkar kundresurser som distribueras över Tillgänglighetszoner inom en region.


![konceptuell vy av en Azure-region med 3 zoner](./media/az-region/azure-region-availability-zones.png)


Azure-tjänster Tillgänglighetszoner indela i tre kategorier: **zonindela,** **zonredundant** **och icke-regionala** tjänster. Kundarbetsbelastningar kan kategoriseras för att använda någon av dessa arkitekturscenarier för att uppfylla programprestanda och hållbarhet.

- **Zonindelade** tjänster – En resurs kan distribueras till en specifik, självvald tillgänglighetszon för att uppnå strängare svarstider eller prestandakrav.  Återhämtning bygger på egen arkitektur genom att replikera program och data till en eller flera zoner i regionen.  Resurser kan fästas på en specifik zon. Till exempel kan virtuella datorer, hanterade diskar eller IP-standardadresser fästas på en specifik zon, vilket möjliggör ökad återhämtning genom att ha en eller flera instanser av resurser fördelade mellan zoner.

- **Zonredundant tjänst** – Resurser replikeras eller distribueras automatiskt mellan zoner. Till exempel replikerar ZRS data över tre zoner så att ett zonfel inte påverkar datas HA.  

- **Icke-regionala tjänster** – Tjänster är alltid tillgängliga från geografiska Azure-områden och är motståndskraftiga mot zonomfattande avbrott samt regionomfattande avbrott. 


För att uppnå omfattande affärskontinuum i Azure kan du skapa din programarkitektur med hjälp av Tillgänglighetszoner med Azure-regionpar. Du kan replikera program och data synkront med Tillgänglighetszoner inom en Azure-region för hög tillgänglighet och asynkront replikera mellan Azure-regioner för haveriberedskapsskydd. Mer information finns i Skapa [lösningar för hög tillgänglighet med hjälp av Tillgänglighetszoner](/azure/architecture/high-availability/building-solutions-for-high-availability). 

## <a name="azure-services-supporting-availability-zones"></a>Azure-tjänster som stöder Tillgänglighetszoner

 - De äldre virtuella generationdatorerna visas inte. Mer information finns i [Tidigare generationer av storlekar på virtuella datorer.](../virtual-machines/sizes-previous-gen.md)
 - Som vi nämnde i [Regioner Tillgänglighetszoner i Azure](az-overview.md)är vissa tjänster icke-regionala. Dessa tjänster är inte beroende av en specifik Azure-region, eftersom de är motståndskraftiga mot zonomfattande avbrott och regionomfattande avbrott.  Listan över icke-regionala tjänster finns i Produkt [tillgänglig per region.](https://azure.microsoft.com/global-infrastructure/services/)


## <a name="azure-regions-with-availability-zones"></a>Azure-regioner med Tillgänglighetszoner
 

| Nord- och Sydamerika           | Europa               | Afrika              | Asien och stillahavsområdet   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| Brasilien, södra       | Frankrike, centrala       | Sydafrika, norra* | Australien, östra |
| Kanada, centrala     | Tyskland, västra centrala |                     | Indien, centrala* |
| Central US         | Europa, norra         |                     | Japan, östra     |
| East US            | Storbritannien, södra             |                     | Sydkorea, centrala* |
| USA, östra 2          | Europa, västra          |                     | Sydostasien |
| USA, södra centrala |                      |                     |                |
| US Gov, Virginia    |                      |                     |                |
| USA, västra 2        |                      |                     |                |
| USA, västra 3*       |                      |                     |                |

\* Om du vill veta mer Tillgänglighetszoner och tillgängliga tjänster i dessa regioner kan du kontakta din Microsoft-säljare eller kundrepresentant. Information om kommande regioner som stöder Tillgänglighetszoner finns i [Azure-geografiska områden.](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)


## <a name="azure-services-supporting-availability-zones"></a>Azure-tjänster som stöder Tillgänglighetszoner

- Äldre virtuella datorer för generation visas inte nedan. Mer information finns i [tidigare generationer av storlekar på virtuella datorer.](../virtual-machines/sizes-previous-gen.md)

- Vissa tjänster är icke-regionala. Mer [information finns i Regioner Tillgänglighetszoner i Azure.](az-overview.md) Dessa tjänster är inte beroende av en specifik Azure-region, vilket gör dem motståndskraftiga mot zonomfattande avbrott och regionomfattande avbrott.  Listan över icke-regionala tjänster finns i Produkt [tillgänglig per region.](https://azure.microsoft.com/global-infrastructure/services/)


### <a name="zone-resilient-services"></a>Zonre elastiska tjänster 

:globe_with_meridians: Icke-regionala tjänster – Tjänster är alltid tillgängliga från Azure-geografiska områden och är motståndskraftiga mot zonomfattande avbrott samt regionomfattande avbrott.

:large_blue_diamond: Motståndskraftig mot avbrott i zonen 

**Grundläggande tjänster**

|     Produkter                                                    | Återhämtning             |
|-----------------------------------------------------------------|:----------------------------:|
|     [Application Gateway (V2)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)                                  | :large_blue_diamond:  |
|     [Azure Backup](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy)                                                | :large_blue_diamond:  |
|     [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support)                                           | :large_blue_diamond:  |
|     [Azure Data Lake Storage Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)                             | :large_blue_diamond:  |
|     [Azure Express Route](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute)                                       | :large_blue_diamond:  |
|     [Offentlig IP-adress i Azure](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)                                           | :large_blue_diamond:  |
|     Azure SQL Database ([Generell användning nivå](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla))                 | :large_blue_diamond:  |
|     Azure SQL Database([Premium & Affärskritisk Tier](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla))     | :large_blue_diamond:  |
|     [Disklagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                                | :large_blue_diamond:  |
|     [Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones)                                                  | :large_blue_diamond:  |
|     [Key Vault](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)                                                   | :large_blue_diamond:  |
|     [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)                                               | :large_blue_diamond:  |
|     [Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-geo-dr#availability-zones)                                                 | :large_blue_diamond:  |
|     [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications)                                            | :large_blue_diamond:  |
|     [Lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                           | :large_blue_diamond:  |
|     Lagring:   [Nivåer för Blob Storage/kall lagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                      | :large_blue_diamond:  |
|     Lagring:   [Managed Disks](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview)                                    | :large_blue_diamond:  |
|     [Virtual Machines skalningsuppsättningar](https://docs.microsoft.com/azure/virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set)                               | :large_blue_diamond:  |
|     [Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                          | :large_blue_diamond:  |
|     Virtual Machines: [Av2-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [Bs-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [DSv2-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Virtual Machines: [DSv3-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                            | :large_blue_diamond:  |
|     Virtual Machines: [Dv2-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Virtual Machines: [Dv3-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [ESv3-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Virtual Machines: [Ev3-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [F-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Virtual Machines: [FS-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Shared Image Gallery](https://docs.microsoft.com/azure/virtual-machines/shared-image-galleries#make-your-images-highly-available) | :large_blue_diamond:  |
|     [Virtual Network](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway)                                         | :large_blue_diamond:  |
|     [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                                             | :large_blue_diamond:  |


**Vanliga tjänster**


|     Produkter                                                    | Återhämtning             |
|-----------------------------------------------------------------|:----------------------------:|
|     [Apptjänstmiljöer](https://docs.microsoft.com/azure/app-service/environment/zone-redundancy)                                    | :large_blue_diamond:  |
|     [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview)                      | :large_blue_diamond:  |
|     [Azure API Management](https://docs.microsoft.com/azure/api-management/zone-redundancy)                      | :large_blue_diamond:  |
|     [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview)                                               | :large_blue_diamond:  |
|     [Azure Cache for Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-high-availability)                              | :large_blue_diamond:  |
|     [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-performance-optimization#availability-zones)               | :large_blue_diamond:  |
|     Azure Cognitive Services: [Textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)                    | :large_blue_diamond:  |
|     [Azure-datautforskaren](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal)                               | :large_blue_diamond:  |
|     Azure Database for MySQL – [flexibel server](https://docs.microsoft.com/azure/mysql/flexible-server/concepts-high-availability)                  | :large_blue_diamond:  |
|     Azure Database for PostgreSQL – [flexibel server](https://docs.microsoft.com/azure/postgresql/flexible-server/overview)             | :large_blue_diamond:  |
|     [Azure DDoS Protection](https://docs.microsoft.com/azure/ddos-protection/ddos-faq)                                       | :large_blue_diamond:  |
|     [Azure Disk Encryption](https://docs.microsoft.com/azure/virtual-machines/disks-redundancy)                                       | :large_blue_diamond:  |
|     [Azure Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                                              | :large_blue_diamond:  |
|     [Azure Firewall Manager](https://docs.microsoft.com/azure/firewall-manager/quick-firewall-policy)                                      | :large_blue_diamond:  |
|     [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/availability-zones)                              | :large_blue_diamond:  |
|     [Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview)                                          | :large_blue_diamond:  |
|     [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery)                                         | :large_blue_diamond:  |
|     Azure SQL: [Virtuell dator](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla)                                  | :large_blue_diamond:  |
|     [Azure Web Application Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                              | :large_blue_diamond:  |
|     [Container Registry](https://docs.microsoft.com/azure/container-registry/zone-redundancy)                                          | :large_blue_diamond:  |
|     [Event Grid](https://docs.microsoft.com/azure/event-grid/overview)                                                  | :large_blue_diamond:  |
|     [Network Watcher](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                                             | :large_blue_diamond:  |
|     Network Watcher: [Trafikanalys](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                          | :large_blue_diamond:  |
|     [Power BI Embedded](https://docs.microsoft.com/power-bi/admin/service-admin-failover#what-does-high-availability)                                           | :large_blue_diamond:  |
|     [Premium-Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-performance-tiers#:~:text=Table%201%20%20%20%20Area%20%20,%20%20Currently%20supports%20only%20locally-redundan%20...%20)                                        | :large_blue_diamond:  |
|     Storage: [Azure Premium Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning)                                | :large_blue_diamond:  |
|     Virtual Machines: [Azure Dedicated Host](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                     | :large_blue_diamond:  |
|     Virtual Machines: [Ddsv4-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [Ddv4-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Dsv4-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Dv4-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Virtual Machines: [Edsv4-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [Edv4-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Esv4-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Ev4-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Virtual Machines: [Fsv2-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [M-serien](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                  | :large_blue_diamond:  |
|     [Virtuellt WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                                 | :large_blue_diamond:  |
|     Virtual WAN: [ExpressRoute](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                   | :large_blue_diamond:  |
|     Virtual WAN: [Punkt-till-plats-VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                      | :large_blue_diamond:  |
|     Virtual WAN: [Plats-till-plats-VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                       | :large_blue_diamond:  |


**Specialiserade tjänster**

|     Produkter                                                    | Återhämtning             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure Red Hat OpenShift                                     | :large_blue_diamond:  |
|     Cognitive Services: Avvikelseidentifiering                        | :large_blue_diamond:  |
|     Cognitive Services: Formigenkänning                         | :large_blue_diamond:  |
|     Lagring: Ultra Disk                                         | :large_blue_diamond:  |


**Icke-regional**

|     Produkter                                                    | Återhämtning             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure DNS                                                   | :globe_with_meridians: |
|     Azure Active Directory                                    | :globe_with_meridians: |
|     Azure Advanced Threat Protection                            | :globe_with_meridians: |
|     Azure Advisor                                               | :globe_with_meridians: |
|     Azure Blueprints                                            | :globe_with_meridians: |
|     Azure Bot Services                                          | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Azure Defender for IoT                                    | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Azure Information Protection                              | :globe_with_meridians: |
|     Azure Lighthouse                                          | :globe_with_meridians: |
|     Azure Managed Applications                                | :globe_with_meridians: |
|     Azure Maps                                                  | :globe_with_meridians: |
|     Azure Performance Diagnostics                               | :globe_with_meridians: |
|     Azure Policy                                                | :globe_with_meridians: |
|     Azure Resource Graph                                      | :globe_with_meridians: |
|     Azure Sentinel                                              | :globe_with_meridians: |
|     Azure Stack                                                 | :globe_with_meridians: |
|     Azure Stack Edge                                          | :globe_with_meridians: |
|     Cloud Shell                                                 | :globe_with_meridians: |
|     Content Delivery Network                                    | :globe_with_meridians: |
|     Cost Management                                             | :globe_with_meridians: |
|     Customer Lockbox för Microsoft Azure                      | :globe_with_meridians: |
|     Intune                                                      | :globe_with_meridians: |
|     Microsoft Azure Peering Service                           | :globe_with_meridians: |
|     Microsoft Azure-portalen                                    | :globe_with_meridians: |
|     Microsoft Cloud App Security                                | :globe_with_meridians: |
|     Microsoft Graph                                             | :globe_with_meridians: |
|     Security Center                                           | :globe_with_meridians: |
|     Traffic Manager                                           | :globe_with_meridians: |


## <a name="pricing-for-vms-in-availability-zones"></a>Prissättning för virtuella datorer i Tillgänglighetszoner

Azure-tillgänglighetszoner är tillgängliga med din Azure-prenumeration. Läs mer här – [prissättningssidan för bandbredd.](https://azure.microsoft.com/pricing/details/bandwidth/)


## <a name="get-started-with-availability-zones"></a>Kom igång med Tillgänglighetszoner

- [Skapa en virtuell dator](../virtual-machines/windows/create-portal-availability-zone.md)
- [Lägga till en hanterad disk med PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Skapa en zonredundant VM-skalningsuppsättning](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Belastningsutjämna virtuella datorer mellan zoner med Standard Load Balancer med en zonredundant frontend](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Belastningsutjämna virtuella datorer i en zon med hjälp Standard Load Balancer en zonindelade frontend](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Zonredundant lagring](../storage/common/storage-redundancy.md)
- [SQL Database för generell användning](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Geohaveriberedskap för Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geohaveriberedskap för Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Skapa en zonredundant virtuell nätverksgateway](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Lägga till zonredundant region för Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Komma igång Azure Cache for Redis Tillgänglighetszoner](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Skapa en Azure Active Directory Domain Services-instans](../active-directory-domain-services/tutorial-create-instance.md)
- [Skapa ett Azure Kubernetes Service (AKS) som använder Tillgänglighetszoner](../aks/availability-zones.md)


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Regioner och tillgänglighetszoner i Azure](az-overview.md)
