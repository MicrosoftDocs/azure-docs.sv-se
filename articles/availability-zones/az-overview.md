---
title: Regioner och Tillgänglighetszoner i Azure
description: Lär dig mer om regioner och Tillgänglighetszoner i Azure för att uppfylla dina tekniska och reglerande krav.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 01/26/2021
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 0b67f113fb8ab3835419a75697e60a732d4ee390
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896061"
---
# <a name="regions-and-availability-zones-in-azure"></a>Regioner och Tillgänglighetszoner i Azure

Microsoft Azure-tjänster är tillgängliga globalt för att driva moln åtgärder på en optimal nivå. Du kan välja den bästa regionen för dina behov baserat på tekniska och reglerande överväganden: service funktioner, data placering, krav på efterlevnad och svars tid.

## <a name="terminology"></a>Terminologi

För att bättre förstå regioner och Tillgänglighetszoner i Azure, hjälper det till att förstå viktiga termer eller begrepp.

| Term eller begrepp | Description |
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
| Alternativa | :heavy_check_mark: | :heavy_check_mark: | Demand-driven | Demand-driven | Ej tillämpligt | :heavy_check_mark: |

### <a name="services-by-category"></a>Tjänster efter kategori

Som tidigare nämnts klassificerar Azure tjänster i tre kategorier: grundläggande, traditionell och specialiserad. Tjänste kategorier tilldelas allmänt tillgängliga. Ofta startar tjänster deras livs cykel som en specialiserad tjänst och när efter frågan och användnings ökningar kan befordras till vanlig eller grundläggande. I följande tabell visas kategorin för tjänster som grundläggande, traditionell eller specialiserad. Tänk på följande om tabellen:

- Vissa tjänster är icke-regionala. Information och en lista över icke-regionala tjänster finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/).
- Äldre generationens virtuella datorer visas inte i listan. Mer information finns i dokumentationen [för tidigare generationer av virtuella dator storlekar](../virtual-machines/sizes-previous-gen.md)
- . Tjänster har inte tilldelats någon kategori förrän allmän tillgänglighet (GA). Information och en lista över förhands gransknings tjänster finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/). 

> [!div class="mx-tableFixed"]
> | Grundläggande                          | Konventionell                                        | Specialiserade                                          |
> |---------------------------------------|---------------------------------------------------|------------------------------------------------------|
> | Lagringskonton                      | API Management                                    | Azure API för FHIR                                   |
> | Application Gateway                   | App Configuration                                 | Azure Analysis Services                              |
> | Azure Backup                          | App Service                                       | Azure Cognitive Services: avvikelse detektor           |
> | Azure Cosmos DB                       | Automation                                        | Azure-Cognitive Services: Custom Vision              |
> | Azure Data Lake Storage Gen2          | Azure Active Directory Domain Services            | Azure Cognitive Services: formulär igenkänning            |
> | Azure ExpressRoute                    | Azure Bastion                                     | Azure Cognitive Services: Personanpassare               |
> | Azure Public IP                       | Azure Cache for Redis                             | Azure-Cognitive Services: QnA Maker                  |
> | Azure SQL Database                    | Azure Cognitive Search                            | Azure-databas för MariaDB                           |
> | Azure SQL: hanterad instans          | Azure Cognitive Services                          | Azure Database Migration Service                     |
> | Cloud Services                        | Azure Cognitive Services: Visuellt innehåll         | Dedikerad HSM i Azure                                  |
> | Cloud Services: Av2-Series            | Azure-Cognitive Services: Content Moderator       | Azure Digital Twins                                  |
> | Cloud Services: Dv2-Series            | Azure Cognitive Services: ansikte                    | Azure Health-robot                                     |
> | Cloud Services: Dv3-Series            | Azure Cognitive Services: avancerad läsare        | Azure HPC Cache                                      |
> | Cloud Services: Ev3-Series            | Azure-Cognitive Services: Language Understanding  | Azure Lab Services                                   |
> | Cloud Services: IP-adresser på instans nivå    | Azure Cognitive Services: tal tjänster         | Azure NetApp Files                                   |
> | Cloud Services: Reserverad IP           | Azure Cognitive Services: Textanalys          | Azure SignalR Service                                |
> | Disklagring                          | Azure Cognitive Services: Translator              | Azure våren Cloud service                           |
> | Event Hubs                            | Azure-datautforskaren                               | Azure Time Series Insights                           |
> | Key Vault                             | Azure Data Share                                  | Azure VMware Solution                                |
> | Lastbalanserare                         | Azure Database for MySQL                          | Azure VMware Solution by CloudSimple                 |
> | Service Bus                           | Azure Database for PostgreSQL                     | Cloud Services: H-serien                             |
> | Service Fabric                        | Azure Databricks                                  | Data Catalog                                         |
> | Lagring: frekvent/låg frekvent Blob Storage-nivåer  | Azure DDoS Protection                             | Data Lake Analytics                                  |
> | Lagring: Managed Disks                | Azure DevTest Labs                                | Azure Machine Learning Studio (klassisk)              |
> | Virtual Machine Scale Sets            | Azure Firewall                                    | Spatial Anchors                                      |
> | Virtual Machines                      | Azure Firewall Manager                            | Lagring: Arkivlagring                             |
> | Virtual Machines: Av2-Series          | Azure Functions                                   | StorSimple                                           |
> | Virtual Machines: Bs-Series           | Azure IoT Hub                                     | Ultra Disklagring                                   |
> | Virtual Machines: DSv2-Series         | Azure Kubernetes Service (AKS)                    | Video Indexer                                        |
> | Virtual Machines: DSv3-Series         | Azure Machine Learning                            | Virtual Machines: DASv4-Series                       |
> | Virtual Machines: Dv2-Series          | Azure Monitor: Application Insights               | Virtual Machines: DAv4-Series                        |
> | Virtual Machines: Dv3-Series          | Azure Monitor: Log Analytics                      | Virtual Machines: DCsv2-serien                       |
> | Virtual Machines: ESv3-Series         | Azure Private Link                                | Virtual Machines: EASv4-Series                       |
> | Virtual Machines: Ev3-Series          | Azure Red Hat OpenShift                           | Virtual Machines: EAv4-Series                        |
> | Virtual Machines: IP-adresser på instans nivå  | Azure Site Recovery                               | Virtual Machines: HBv1-Series                        |
> | Virtual Machines: Reserverad IP         | Azure Stream Analytics                            | Virtual Machines: HBv2-Series                        |
> | Virtual Network                       | Azure Synapse Analytics                           | Virtual Machines: HCv1-Series                        |
> | VPN Gateway                           | Batch                                             | Virtual Machines: H-serien                           |
> |                                       | Cloud Services: M-serien                          | Virtual Machines: LSv2-Series                        |
> |                                       | Container Instances                               | Virtual Machines: Mv2-Series                         |
> |                                       | Container Registry                                | Virtual Machines: NCv3-Series                        |
> |                                       | Data Factory                                      | Virtual Machines: NDv2-Series                        |
> |                                       | Event Grid                                        | Virtual Machines: NVv3-Series                        |
> |                                       | HDInsight                                         | Virtual Machines: NVv4-Series                        |> 
> |                                       | Logic Apps                                        | Virtual Machines: SAP HANA på stora Azure-instanser  |
> |                                       | Media Services                                    |                                                      |
> |                                       | Network Watcher                                   |                                                      |
> |                                       | Notification Hubs                                 |                                                      |
> |                                       | Premium-Blob Storage                              |                                                      |
> |                                       | Premium Files-lagring                             |                                                      |
> |                                       | Virtual Machines: Ddsv4-Series                    |                                                      |
> |                                       | Virtual Machines: Ddv4-Series                     |                                                      |
> |                                       | Virtual Machines: Dsv4-Series                     |                                                      |
> |                                       | Virtual Machines: Dv4-Series                      |                                                      |
> |                                       | Virtual Machines: Edsv4-Series                    |                                                      |
> |                                       | Virtual Machines: Edv4-Series                     |                                                      |
> |                                       | Virtual Machines: Esv4-Series                     |                                                      |
> |                                       | Virtual Machines: Ev4-Series                      |                                                      |
> |                                       | Virtual Machines: Fsv2-Series                     |                                                      |
> |                                       | Virtual Machines: M-serien                        |                                                      |
> |                                       | Virtuellt WAN                                       |                                                      |


## <a name="next-steps"></a>Nästa steg

- [Regioner som stöder Tillgänglighetszoner i Azure](az-region.md)
- [Snabbstartsmallar](https://aka.ms/azqs)
