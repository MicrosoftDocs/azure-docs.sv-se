---
title: Azure-tjänster som har stöd för tillgänglighetszoner
description: Om du vill skapa hög tillgängliga och elastiska program i Azure kan Tillgänglighetszoner tillhandahålla fysiskt åtskilda platser som du kan använda för att köra dina resurser.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 5152b0ecf6eebe87031a2165d5d8ba11d99209cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601193"
---
# <a name="azure-services-that-support-availability-zones"></a>Azure-tjänster som har stöd för tillgänglighetszoner

Microsoft Azure global infrastruktur är utformad och konstruerad på varje lager för att leverera de högsta nivåerna av redundans och återhämtning till sina kunder. Azure-infrastrukturen består av geografiska områden, regioner och Tillgänglighetszoner, vilket begränsar den förstärkta radien vid ett haveri och därmed begränsar potentiell påverkan på kund program och data. Azure-tillgänglighetszoner konstruktion utvecklades för att tillhandahålla en program-och nätverks lösning som skyddar mot data Center problem och ger ökad hög tillgänglighet till våra kunder.

Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon består av ett eller flera data Center med oberoende strömförsörjning, kylning och nätverk. Den fysiska avgränsningen av Tillgänglighetszoner inom en region begränsar påverkan på program och data från zon fel, till exempel storskalig översvämning, större storm-och SuperStorm och andra händelser som kan störa åtkomsten till platsen, säkra passager, utökade verktyg, drift tid och resursernas tillgänglighet. Tillgänglighetszoner och deras associerade data Center är utformade så att om en zon komprometteras, stöds tjänsterna, kapaciteten och tillgängligheten av de andra Tillgänglighetszoner i regionen.

Alla Azures hanterings tjänster är utformad för att vara elastiska från problem på regions nivå. I spektrumet av problem har en eller flera tillgänglighets zon problem inom en region en mindre felradie jämfört med ett hela regions haveri. Azure kan återställas från fel i hanterings tjänster på zon nivå inom en region. Azure utför kritiska underhåll en zon i taget inom en region, för att förhindra att eventuella problem påverkar kund resurser som distribueras i Tillgänglighetszoner inom en region.


![Konceptuell visning av en Azure-region med 3 zoner](./media/az-region/azure-region-availability-zones.png)


Azure-tjänster som stöder Tillgänglighetszoner delas in i tre kategorier: **zonindelade**, **zon-redundanta** och **icke-regionala** tjänster. Kund arbets belastningar kan kategoriseras för att använda något av dessa arkitektur scenarier för att möta programmets prestanda och hållbarhet.

- **Zonindelade Services** – en resurs kan distribueras till en viss, själv vald tillgänglighets zon för att uppnå mer strängare svars tider eller prestanda krav.  Återhämtning är självutformad genom att replikera program och data till en eller flera zoner i regionen.  Resurser kan fästas i en speciell zon. Till exempel kan virtuella datorer, hanterade diskar eller standard-IP-adresser fästas i en speciell zon, vilket ger ökad återhämtning genom att en eller flera instanser av resurser sprids mellan zoner.

- **Zoner – redundanta tjänster** – Azure-plattformen replikerar resursen och data mellan zoner.  Microsoft hanterar leverans av hög tillgänglighet eftersom Azure automatiskt replikerar och distribuerar instanser inom regionen.  ZRS replikerar exempelvis data över tre zoner så att ett zon haveri inte påverkar datans data. 

- **Icke-regionala tjänster** – tjänster är alltid tillgängliga från Azures geografiska områden och är elastiska för hela Zone-avbrott samt verksamhets-och drift avbrott. 


För att uppnå omfattande affärs kontinuitet i Azure kan du bygga din program arkitektur genom att kombinera Tillgänglighetszoner med Azures region par. Du kan synkront replikera dina program och data med hjälp av Tillgänglighetszoner inom en Azure-region för hög tillgänglighet och asynkront replikera över Azure-regioner för haveri beredskaps skydd. Läs mer i [skapa lösningar för hög tillgänglighet med hjälp av Tillgänglighetszoner](/azure/architecture/high-availability/building-solutions-for-high-availability). 

## <a name="azure-services-supporting-availability-zones"></a>Azure-tjänster som stöder Tillgänglighetszoner

 - De äldre generationens virtuella datorer visas inte i listan. Mer information finns i [tidigare generationer av virtuella dator storlekar](../virtual-machines/sizes-previous-gen.md).
 - Som nämnts i [regionerna och Tillgänglighetszoner i Azure](az-overview.md)är vissa tjänster icke-regionala. De här tjänsterna är inte beroende av en speciell Azure-region, eftersom de är elastiska för globala drifts avbrott och drift avbrott i hela regionen.  Du hittar en lista över icke-regionala tjänster på [produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/).


## <a name="azure-regions-with-availability-zones"></a>Azure-regioner med Tillgänglighetszoner


| Nord- och Sydamerika           | Europa               | Afrika              | Asien och stillahavsområdet   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| Brasilien, södra       | Frankrike, centrala       | Södra Afrika, norra * | Japan, östra     |
| Kanada, centrala     | Tyskland, västra centrala |                     | Sydostasien |
| Central US         | Europa, norra         |                     | Australien, östra |
| East US            | Storbritannien, södra             |                     |                |
| USA, östra 2          | Europa, västra          |                     |                |
| Södra centrala USA |                      |                     |                |
| US Gov, Virginia     |                      |                     |                |
| USA, västra 2        |                      |                     |                |


\* Kontakta din Microsoft-säljare eller kund representant om du vill veta mer om hur du Tillgänglighetszoner och tjänster som är tillgängliga i dessa regioner. För kommande regioner som har stöd för Tillgänglighetszoner, se [Azure-geografiska](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)områden.


## <a name="azure-services-supporting-availability-zones"></a>Azure-tjänster som stöder Tillgänglighetszoner

- Äldre generationens virtuella datorer visas inte nedan. Mer information finns i [tidigare generationer av virtuella dator storlekar](../virtual-machines/sizes-previous-gen.md).

- Vissa tjänster är icke-regionala, se [regioner och Tillgänglighetszoner i Azure](az-overview.md) för mer information. Tjänsterna är inte beroende av en speciell Azure-region, vilket gör att de är elastiska för globala drift avbrott och drift avbrott i hela regionen.  Du hittar en lista över icke-regionala tjänster på [produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/).


### <a name="zone-resilient-services"></a>Zon elastiska tjänster 

: globe_with_meridians: icke-regionala tjänster – tjänster är alltid tillgängliga från Azures geografiska områden och är elastiska för hela Zone-avbrott samt för regioner.

: large_blue_diamond: elastiska i hela zonens drift avbrott 

**Grundläggande tjänster**

|     Produkter                                                    | Återhämtning             |
|-----------------------------------------------------------------|:----------------------------:|
|     Lagrings konto                                           | : large_blue_diamond:  |
|     Application Gateway (v2)                                  | : large_blue_diamond:  |
|     Azure Backup                                                | : large_blue_diamond:  |
|     Azure Cosmos DB                                           | : large_blue_diamond:  |
|     Azure Data Lake Storage gen 2                             | : large_blue_diamond:  |
|     Azure Express-väg                                       | : large_blue_diamond:  |
|     Offentlig Azure-IP                                           | : large_blue_diamond:  |
|     Azure SQL Database (Generell användning nivå)                 | : large_blue_diamond:  |
|     Azure SQL Database (Premium & Affärskritisk-nivå)     | : large_blue_diamond:  |
|     Disklagring                                                | : large_blue_diamond:  |
|     Event Hubs                                                  | : large_blue_diamond:  |
|     Key Vault                                                   | : large_blue_diamond:  |
|     Load Balancer                                               | : large_blue_diamond:  |
|     Service Bus                                                 | : large_blue_diamond:  |
|     Service Fabric                                            | : large_blue_diamond:  |
|     Lagring: frekvent/låg frekvent Blob Storage-nivåer                      | : large_blue_diamond:  |
|     Lagring: Managed Disks                                    | : large_blue_diamond:  |
|     Virtual Machines skalnings uppsättningar                               | : large_blue_diamond:  |
|     Virtual Machines                                          | : large_blue_diamond:  |
|     Virtual Machines: Av2-Series                              | : large_blue_diamond:  |
|     Virtual Machines: Bs-Series                               | : large_blue_diamond:  |
|     Virtual Machines: DSv2-Series                             | : large_blue_diamond:  |
|     Virtual Machines: DSv3-Series                             | : large_blue_diamond:  |
|     Virtual Machines: Dv2-Series                              | : large_blue_diamond:  |
|     Virtual Machines: Dv3-Series                              | : large_blue_diamond:  |
|     Virtual Machines: ESv3-Series                             | : large_blue_diamond:  |
|     Virtual Machines: Ev3-Series                              | : large_blue_diamond:  |
|     Virtual Machines: F-serien                                | : large_blue_diamond:  |
|     Virtual Machines: FS-Series                               | : large_blue_diamond:  |
|     Virtual Network                                           | : large_blue_diamond:  |
|     VPN Gateway                                                 | : large_blue_diamond:  |


**Vanliga tjänster**

| Produkter                                        | Återhämtning |
|-------------------------------------------------|:------------:|
| Apptjänstmiljöer                        |      : large_blue_diamond:  |
| Azure Active Directory Domain Services          |      : large_blue_diamond:  |
| Azure Bastion                                   |      : large_blue_diamond:  |
| Azure Cache for Redis                           |      : large_blue_diamond:  |
| Azure Cognitive Services: Textanalys        |      : large_blue_diamond:  |
| Öppna Azure-datautforskaren                             |      : large_blue_diamond:  |
| Azure Database for MySQL – flexibel Server      |      : large_blue_diamond:  |
| Azure Database for PostgreSQL – flexibel Server |      : large_blue_diamond:  |
| Azure DDoS Protection                           |      : large_blue_diamond:  |
| Azure Disk Encryption                           |      : large_blue_diamond:  |
| Azure Firewall                                  |      : large_blue_diamond:  |
| Azure Firewall Manager                          |      : large_blue_diamond:  |
| Azure Kubernetes Service (AKS)                  |      : large_blue_diamond:  |
| Azure Private Link                              |      : large_blue_diamond:  |
| Azure Red Hat OpenShift                         |      : large_blue_diamond:  |
| Azure Site Recovery                             |      : large_blue_diamond:  |
| Azure SQL: virtuell dator                      |      : large_blue_diamond:  |
| Azure Search                                    |      : large_blue_diamond:  |
| Azure Web Application Firewall                  |      : large_blue_diamond:  |
| Container Registry                              |      : large_blue_diamond:  |
| Event Grid                                      |      : large_blue_diamond:  |
| Network Watcher                                 |      : large_blue_diamond:  |
| Network Watcher: Trafikanalys              |      : large_blue_diamond:  |
| Power BI Embedded                               |      : large_blue_diamond:  |
| Premium-Blob Storage                            |      : large_blue_diamond:  |
| Lagring: Azure Premium-filer                    |      : large_blue_diamond:  |
| Virtual Machines: Azure-dedikerad värd          |      : large_blue_diamond:  |
| Virtual Machines: Ddsv4-Series                  |      : large_blue_diamond:  |
| Virtual Machines: Ddv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Dsv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Dv4-Series                    |      : large_blue_diamond:  |
| Virtual Machines: Edsv4-Series                  |      : large_blue_diamond:  |
| Virtual Machines: Edv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Esv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Ev4-Series                    |      : large_blue_diamond:  |
| Virtual Machines: Fsv2-Series                   |      : large_blue_diamond:  |
| Virtual Machines: M-serien                      |      : large_blue_diamond:  |
| Virtuellt WAN                                     |      : large_blue_diamond:  |
| Virtuellt WAN: ExpressRoute                       |      : large_blue_diamond:  |
| Virtuellt WAN: punkt-till-plats-VPN Gateway          |      : large_blue_diamond:  |
| Virtuellt WAN: plats-till-plats-VPN Gateway           |      : large_blue_diamond:  |


**Icke-regional**

|     Produkter                                  |     Återhämtning    |
|-----------------------------------------------|:-------------------:|
|     Azure DNS                                 |     : globe_with_meridians:             |
|     Azure Active Directory                  |     : globe_with_meridians:             |
|     Azure Advanced Threat Protection          |     : globe_with_meridians:             |
|     Azure Advisor                             |     : globe_with_meridians:             |
|     Azure Blueprints                          |     : globe_with_meridians:             |
|     Azure Bot Services                        |     : globe_with_meridians:             |
|     Azure Front Door                          |     : globe_with_meridians:             |
|     Azure Defender för IoT                  |     : globe_with_meridians:             |
|     Azure Front Door                           |     : globe_with_meridians:             |
|     Azure Information Protection            |     : globe_with_meridians:             |
|     Azure-Lighthouse                        |     : globe_with_meridians:             |
|     Azure Managed Applications              |     : globe_with_meridians:             |
|     Azure Maps                                |     : globe_with_meridians:             |
|     Azure Policy                              |     : globe_with_meridians:             |
|     Azure-resurs diagram                    |     : globe_with_meridians:             |
|     Azure Sentinel                            |     : globe_with_meridians:             |
|     Azure Stack                               |     : globe_with_meridians:             |
|     Azure Stack Edge                        |     : globe_with_meridians:             |
|     Cloud Shell                               |     : globe_with_meridians:             |
|     Content Delivery Network                  |     : globe_with_meridians:             |
|     Cost Management                           |     : globe_with_meridians:             |
|     Customer Lockbox för Microsoft Azure    |     : globe_with_meridians:             |
|     Intune                                    |     : globe_with_meridians:             |
|     Microsoft Azure peering-tjänsten         |     : globe_with_meridians:             |
|     Microsoft Azure-portalen                  |     : globe_with_meridians:             |
|     Microsoft Cloud App Security              |     : globe_with_meridians:             |
|     Microsoft Graph                           |     : globe_with_meridians:             |
|     Security Center                         |     : globe_with_meridians:             |
|     Traffic Manager                         |     : globe_with_meridians:             |


## <a name="pricing-for-vms-in-availability-zones"></a>Priser för virtuella datorer i Tillgänglighetszoner

Det kostar ingen ytterligare kostnad för virtuella datorer som distribueras i en tillgänglighets zon. Mer information finns på [prissättnings sidan för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Kom igång med Tillgänglighetszoner

- [Skapa en virtuell dator](../virtual-machines/windows/create-portal-availability-zone.md)
- [Lägga till en hanterad disk med PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Skapa en virtuell dators skalnings uppsättning för redundant virtuell dator](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Belastningsutjämna virtuella datorer över zoner med hjälp av en Standard Load Balancer med en zon redundant klient del](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Belastningsutjämna virtuella datorer inom en zon med hjälp av en Standard Load Balancer med en zonindelade-frontend](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Zonredundant lagring](../storage/common/storage-redundancy.md)
- [SQL Database generell användnings nivå](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Geohaveriberedskap för Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geohaveriberedskap för Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Skapa en zonredundant virtuell nätverksgateway](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Lägg till redundant region i zonen för Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Komma igång Azure cache för Redis Tillgänglighetszoner](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Skapa en Azure Active Directory Domain Services-instans](../active-directory-domain-services/tutorial-create-instance.md)
- [Skapa ett Azure Kubernetes service-kluster (AKS) som använder Tillgänglighetszoner](../aks/availability-zones.md)


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Regioner och tillgänglighetszoner i Azure](az-overview.md)
