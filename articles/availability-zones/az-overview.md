---
title: Regioner och Tillgänglighetszoner i Azure
description: Lär dig mer om regioner och Tillgänglighetszoner i Azure för att uppfylla dina tekniska och reglerande krav.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/27/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 485042c795fd94ef7daa8e00b9869190e9df61c8
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678545"
---
# <a name="regions-and-availability-zones-in-azure"></a>Regioner och Tillgänglighetszoner i Azure

Microsoft Azure-tjänster är tillgängliga globalt för att driva moln åtgärder på en optimal nivå. Du kan välja den bästa regionen för dina behov baserat på tekniska och reglerande överväganden: service funktioner, data placering, krav på efterlevnad och svars tid.

## <a name="terminology"></a>Terminologi

För att bättre förstå regioner och Tillgänglighetszoner i Azure, hjälper det till att förstå viktiga termer eller begrepp.

| Term eller begrepp | Beskrivning |
| --- | --- |
| region | En uppsättning data Center som distribueras inom en latens-definierad perimeter och är anslutna via ett dedikerat regionalt nätverk med låg latens. |
| geography | Ett område i världen som innehåller minst en Azure-region. Geografiska områden definierar en diskret marknad som bevarar data placering och kontroll gränser. Geografiska områden hjälper kunder med specifika behov kring dataplacering och regelefterlevnad att hålla sina data och program nära. De geografiska områdena är feltoleranta för att motstå ett fullständigt fel i regionen via anslutningen till vår dedikerade nätverks infrastruktur med hög kapacitet. |
| Tillgänglighetszon | Unika fysiska platser inom en region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. |
| Rekommenderad region | En region som tillhandahåller flest utbud av tjänst funktioner och som har utformats för att stödja Tillgänglighetszoner nu eller i framtiden. De anges i Azure Portal enligt **rekommendationer**. |
| Alternativt (annan) region | En region som utökar Azures plats i en data placering-gränser där det även finns en rekommenderad region. Alternativa regioner bidrar till att optimera svars tiden och tillhandahålla en andra region för haveri beredskap. De är inte utformade för att stödja Tillgänglighetszoner (även om Azure genomför regelbunden utvärdering av dessa regioner för att avgöra om de bör bli rekommenderade regioner). De anges i Azure Portal som **andra**. |
| grundläggande tjänst | En grundläggande Azure-tjänst som är tillgänglig i alla regioner när regionen är allmänt tillgänglig. |
| vanlig tjänst | En Azure-tjänst som är tillgänglig i alla rekommenderade regioner inom 12 månader från den region/tjänstens allmänna tillgänglighet eller efter frågans drivna tillgänglighet i alternativa regioner. |
| specialiserad tjänst | En Azure-tjänst som är till gångs driven tillgänglig mellan regioner som backas upp av anpassad/specialiserad maskin vara. |
| regional tjänst | En Azure-tjänst som distribueras regionalt och gör det möjligt för kunden att ange den region där tjänsten ska distribueras. En fullständig lista finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| icke-regional tjänst | En Azure-tjänst för vilken det inte finns något beroende på en angiven Azure-region. Icke-regionala tjänster distribueras till två eller flera regioner och om det uppstår ett regionalt haveri fortsätter instansen av tjänsten i en annan region att betjäna kunder. En fullständig lista finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=all). |

## <a name="regions"></a>Regioner

En region är en uppsättning data Center som distribueras inom en latens-definierad perimeter och är anslutna via ett dedikerat regionalt nätverk med låg latens. Azure ger dig flexibiliteten att distribuera program där du behöver, inklusive över flera regioner för att leverera återhämtning mellan regioner. Mer information finns i [Översikt över återhämtnings pelaren](/azure/architecture/framework/resiliency/overview).

## <a name="availability-zones"></a>Tillgänglighetszoner

En tillgänglighets zon är ett erbjudande med hög tillgänglighet som skyddar dina program och data från data Center problem. Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Den fysiska avgränsningen av tillgänglighetszonerna inom en region skyddar program och data mot datacenterfel. Zoner – redundanta tjänster replikerar dina program och data över Tillgänglighetszoner för att skydda från enskilda platser. Med tillgänglighetszonerna kan Azure erbjuda branschens bästa serviceavtal med en drifttid på 99,99 % för virtuella datorer. I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/) förklaras den garanterade tillgängligheten för Azure som helhet.

En tillgänglighetszon i en Azure-region är en kombination av en feldomän och en uppdateringsdomän. Om du skapar tre eller flera virtuella datorer över tre zoner i en Azure-region distribueras i praktiken dina virtuella datorer mellan tre feldomäner och tre uppdateringsdomäner. Azure-plattformen känner igen distributionen över uppdaterings domäner för att se till att virtuella datorer i olika zoner inte är schemalagda att uppdateras samtidigt.

Bygg hög tillgänglighet i din program arkitektur genom att samplacera din beräkning, lagring, nätverk och data resurser inom en zon och replikera i andra zoner. Azure-tjänster som har stöd för tillgänglighetszoner är indelade i två kategorier:

- **Zonindelade Services** – där en resurs fästs i en speciell zon (till exempel virtuella datorer, hanterade diskar, standard-IP-adresser) eller
- **Zoner – redundanta tjänster** – när Azure-plattformen replikeras automatiskt mellan zoner (till exempel zon redundant lagring SQL Database).

För att uppnå omfattande affärs kontinuitet i Azure kan du bygga din program arkitektur genom att kombinera Tillgänglighetszoner med Azures region par. Du kan synkront replikera dina program och data med hjälp av Tillgänglighetszoner inom en Azure-region för hög tillgänglighet och asynkront replikera över Azure-regioner för haveri beredskaps skydd.
 
![Konceptuell visning av en zon i en region](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Tillgänglighets zon identifierarna (talen 1, 2 och 3 i bilden ovan) mappas logiskt till de faktiska fysiska zonerna för varje prenumeration oberoende av varandra. Det innebär att tillgänglighets Zon 1 i en specifik prenumeration kan referera till en annan fysisk zon än tillgänglighets Zon 1 i en annan prenumeration. Därför rekommenderar vi att du inte förlitar sig på tillgänglighets zon-ID: n mellan olika prenumerationer för virtuell dator placering.

## <a name="region-and-service-categories"></a>Regions-och tjänst kategorier

Azures metod för tillgänglighet för Azure-tjänster i olika regioner beskrivs bäst genom att uttrycka tjänster som görs tillgängliga i rekommenderade regioner och alternativa regioner.

- **Rekommenderad region** – en region som tillhandahåller det bredaste utbudet av tjänster och som har utformats för att stödja Tillgänglighetszoner nu eller i framtiden. De anges i Azure Portal enligt **rekommendationer**.
- **Alternativt (annan) region** – en region som utökar Azures plats i en data placering-gränser där det även finns en rekommenderad region. Alternativa regioner bidrar till att optimera svars tiden och tillhandahålla en andra region för haveri beredskap. De är inte utformade för att stödja Tillgänglighetszoner (även om Azure genomför regelbunden utvärdering av dessa regioner för att avgöra om de bör bli rekommenderade regioner). De anges i Azure Portal som **andra**.

### <a name="comparing-region-types"></a>Jämföra regions typer

Azure-tjänster är grupperade i tre kategorier: grundläggande tjänster, vanliga tjänster och specialiserade tjänster. Azures allmänna policy om distribution av tjänster till en bestämd region drivs främst av regions typ, tjänst kategorier och kund efter frågan:

- **Grundläggande** – tillgängligt i alla rekommenderade och alternativa regioner när regionen är allmänt tillgänglig, eller inom 12 månader från en ny grundläggande tjänst som blir allmänt tillgänglig.
- **Vanlig** – tillgänglig i alla rekommenderade regioner inom 12 månader från den region/tjänst som är allmänt tillgänglig; demand-driven i alternativa regioner (många har redan distribuerats till en stor del av alternativa regioner).
- **Specialiserade** – riktade tjänst erbjudanden, ofta branschledande eller backas upp av anpassad/specialiserad maskin vara. Behovs driven tillgänglighet i flera regioner (många har redan distribuerats till en stor del av rekommenderade regioner).

Om du vill se vilka tjänster som distribueras i en specifik region, samt den framtida översikten över för hands versionen eller allmän tillgänglighet för tjänster i en region, se [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=all).

Om ett tjänst erbjudande inte är tillgängligt i en speciell region kan du dela ditt intresse genom att kontakta din Microsoft-representant.

| Regions typ | Icke-regional | Grundläggande | Konventionell | Specialiserade | Tillgänglighetszoner | Dataplacering |
| --- | --- | --- | --- | --- | --- | --- |
| Rekommenderas | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Demand-driven | :heavy_check_mark: | :heavy_check_mark: |
| Alternativa | :heavy_check_mark: | :heavy_check_mark: | Demand-driven | Demand-driven | Saknas | :heavy_check_mark: |

### <a name="services-by-category"></a>Tjänster efter kategori

Som tidigare nämnts klassificerar Azure tjänster i tre kategorier: grundläggande, traditionell och specialiserad. Tjänste kategorier tilldelas allmänt tillgängliga. Ofta startar tjänster deras livs cykel som en specialiserad tjänst och när efter frågan och användnings ökningar kan befordras till vanlig eller grundläggande. I följande tabell visas kategorin för tjänster som grundläggande, traditionell eller specialiserad. Tänk på följande om tabellen:

- Vissa tjänster är icke-regionala. Information och en lista över icke-regionala tjänster finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/).
- Äldre generationens virtuella datorer visas inte i listan. Mer information finns i dokumentationen [för tidigare generationer av virtuella dator storlekar](../virtual-machines/sizes-previous-gen.md).

> [!div class="mx-tableFixed"]
> | Grundläggande | Konventionell | Specialiserade |
> | --- | --- | --- |
> | Konto lagring | API Management | Azure API för FHIR |
> | Application Gateway | App Configuration | Azure Analysis Services |
> | Azure Backup | App Service | Azure Blockchain Service |
> | Azure Cosmos DB | Automation | Azure Blueprint |
> | Azure Data Lake Storage Gen2 | Azure Active Directory Domain Services | Azure-databas för MariaDB |
> | Azure ExpressRoute | Azure Bastion | Dedikerad HSM i Azure |
> | Azure SQL Database | Azure Cache for Redis | Azure Dev Spaces |
> | Cloud Services | Azure Cognitive Search | Azure Digital Twins |
> | Cloud Services: Av2-Series | Azure-datautforskaren | Azure Lab Services |
> | Cloud Services: Dv2-Series | Azure Data Share | Azure NetApp Files |
> | Cloud Services: Dv3-Series | Azure Database for MySQL | Azure Quantum |
> | Cloud Services: Ev3-Series | Azure Database for PostgreSQL | Azure våren Cloud service |
> | Cloud Services: IP-adresser på instans nivå | Azure Database Migration Service | Azure Time Series Insights |
> | Cloud Services: Reserverad IP | Azure Databricks | Azure VMware Solution by CloudSimple |
> | Disklagring | Azure DDoS Protection | Cloud Services: G-serien |
> | Event Hubs | Azure DevTest Labs | Cloud Services: H-serien |
> | Key Vault | Azure Firewall | Cognitive Services: Custom Vision |
> | Lastbalanserare | Azure Firewall Manager | Cognitive Services: Talarigenkänning |
> | Service Bus | Azure Functions | Data Box – tung |
> | Service Fabric | Azure HPC Cache | Data Catalog |
> | Virtual Machine Scale Sets | Azure IoT Hub | Data Factory: Data Factory v1 |
> | Virtual Machines | Azure Kubernetes Service (AKS) | Data Lake Analytics |
> | Virtual Machines: Av2-Series | Azure Machine Learning | Azure Machine Learning Studio (klassisk)|
> | Virtual Machines: Bs-Series | Azure Private Link | Microsoft Genomics |
> | Virtual Machines: DSv2-Series | Azure Red Hat OpenShift | Remote Rendering |
> | Virtual Machines: DSv3-Series | Azure SignalR Service | Spatial Anchors |
> | Virtual Machines: Dv2-Series | Azure Site Recovery | StorSimple |
> | Virtual Machines: Dv3-Series | Azure Stack Hub | Video Indexer |
> | Virtual Machines: ESv3-Series | Azure Stream Analytics | Virtual Machines: DASv4-Series |
> | Virtual Machines: Ev3-Series | Azure Synapse Analytics | Virtual Machines: DAv4-Series |
> | Virtual Machines: F-serien | Batch | Virtual Machines: DCsv2-serien |
> | Virtual Machines: FS-Series | Cloud Services: M-serien | Virtual Machines: EASv4-Series |
> | Virtual Machines: IP-adresser på instans nivå | Cognitive Services | Virtual Machines: EAv4-Series |
> | Virtual Machines: Reserverad IP | Cognitive Services: Visuellt innehåll | Virtual Machines: G-serien |
> | Virtual Network | Cognitive Services: Content Moderator | Virtual Machines: GS-Series |
> | VPN Gateway | Cognitive Services: ansikte | Virtual Machines: HBv1-Series |
> |  | Cognitive Services: formulär igenkänning | Virtual Machines: HBv2-Series |
> |  | Cognitive Services: Language Understanding | Virtual Machines: HCv1-Series |
> |  | Cognitive Services: QnA Maker | Virtual Machines: H-serien |
> |  | Cognitive Services: tal tjänster | Virtual Machines: LS-Series |
> |  | Container Instances | Virtual Machines: LSv2-Series |
> |  | Container Registry | Virtual Machines: Mv2-Series |
> |  | Data Factory | Virtual Machines: NC-Series |
> |  | Event Grid | Virtual Machines: NCv2-Series |
> |  | HDInsight | Virtual Machines: NCv3-Series |
> |  | Logic Apps | Virtual Machines: NDs-Series |
> |  | Media Services | Virtual Machines: NDv2-Series |
> |  | Network Watcher | Virtual Machines: NV-Series |
> |  | Notification Hubs | Virtual Machines: NVv3-Series |
> |  | Power BI Embedded | Virtual Machines: NVv4-Series |
> |  | Premium-Blob Storage | Virtual Machines: SAP HANA på stora Azure-instanser |
> |  | Premium Files-lagring | Visual Studio App Center |
> |  | Lagring: Arkivlagring |  |
> |  | Ultra Disklagring |  |
> |  | Virtual Machines: Ddsv4-Series |  |
> |  | Virtual Machines: Ddv4-Series |  |
> |  | Virtual Machines: Dsv4-Series |  |
> |  | Virtual Machines: Dv4-Series |  |
> |  | Virtual Machines: Edsv4-Series |  |
> |  | Virtual Machines: Edv4-Series |  |
> |  | Virtual Machines: Esv4-Series |  |
> |  | Virtual Machines: Ev4-Series |  |
> |  | Virtual Machines: Fsv2-Series |  |
> |  | Virtual Machines: M-serien |  |
> |  | Virtual WAN |  |

###  <a name="services-resiliency"></a>Tjänste återhämtning

Alla Azures hanterings tjänster är utformad för att vara elastiska från problem på regions nivå. I spektrumet av problem har en eller flera tillgänglighets zon problem inom en region en mindre felradie jämfört med ett hela regions haveri. Azure kan återställas från ett fel i hanterings tjänster på zon nivå i regionen eller från en annan Azure-region. Azure utför kritiska underhåll en zon i taget inom en region, för att förhindra att eventuella problem påverkar kund resurser som distribueras i Tillgänglighetszoner inom en region.

### <a name="pricing-for-vms-in-availability-zones"></a>Priser för virtuella datorer i Tillgänglighetszoner

Det kostar ingen ytterligare kostnad för virtuella datorer som distribueras i en tillgänglighets zon. service nivå avtal för 99,99% VM-drift tid erbjuds när två eller flera virtuella datorer distribueras över två eller fler Tillgänglighetszoner inom en Azure-region. Det kommer att finnas ytterligare avgifter för VM-till-VM-dataöverföringar mellan olika tillgänglighets zoner. Mer information finns på [prissättnings](https://azure.microsoft.com/pricing/details/bandwidth/) sidan för bandbredd.

### <a name="get-started-with-availability-zones"></a>Kom igång med Tillgänglighetszoner

- [Skapa en virtuell dator](../virtual-machines/windows/create-portal-availability-zone.md)
- [Lägga till en hanterad disk med PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Skapa en virtuell dators skalnings uppsättning för redundant virtuell dator](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Belastningsutjämna virtuella datorer över zoner med hjälp av en Standard Load Balancer med en zon redundant klient del](../load-balancer/quickstart-load-balancer-standard-public-cli.md)
- [Belastningsutjämna virtuella datorer inom en zon med hjälp av en Standard Load Balancer med en zonindelade-frontend](../load-balancer/quickstart-load-balancer-standard-public-cli.md)
- [Zonredundant lagring](../storage/common/storage-redundancy.md)
- [SQL Database generell användnings nivå](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Geohaveriberedskap för Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geohaveriberedskap för Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Skapa en zonredundant virtuell nätverksgateway](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Lägg till redundant region i zonen för Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Komma igång Azure cache för Redis Tillgänglighetszoner](https://aka.ms/redis/az/getstarted)
- [Skapa en Azure Active Directory Domain Services-instans](../active-directory-domain-services/tutorial-create-instance.md)
- [Skapa ett Azure Kubernetes service-kluster (AKS) som använder Tillgänglighetszoner](../aks/availability-zones.md)
- [Aktivera zon-redundans i Azure Container Registry](../container-registry/zone-redundancy.md)

## <a name="next-steps"></a>Nästa steg

- [Regioner som stöder Tillgänglighetszoner i Azure](az-region.md)
- [Snabbstartsmallar](https://aka.ms/azqs)