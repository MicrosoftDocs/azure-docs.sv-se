---
title: Regioner och Tillgänglighetszoner i Azure
description: Lär dig mer om regioner och Tillgänglighetszoner i Azure för att uppfylla dina tekniska och reglerande krav.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 44ff938d0990a1a7794140a82c0e9222bb69a5d7
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384263"
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
| vanlig tjänst | En Azure-tjänst som är tillgänglig i alla rekommenderade regioner inom 90 dagar från den allmänna tillgänglighets-eller demand driven tillgänglighet i alternativa regioner. |
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

- **Grundläggande** – tillgängligt i alla rekommenderade och alternativa regioner när regionen är allmänt tillgänglig, eller inom 90 dagar från en ny grundläggande tjänst som blir allmänt tillgänglig.
- **Vanlig** – tillgänglig i alla rekommenderade regioner inom 90 dagar från det allmänna tillgänglighets området, demand-driven i alternativa regioner (många har redan distribuerats till en stor del av alternativa regioner).
- **Specialiserade** – riktade tjänst erbjudanden, ofta branschledande eller backas upp av anpassad/specialiserad maskin vara. Behovs driven tillgänglighet i flera regioner (många har redan distribuerats till en stor del av rekommenderade regioner).

Om du vill se vilka tjänster som distribueras i en specifik region, samt den framtida översikten över för hands versionen eller allmän tillgänglighet för tjänster i en region, se [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=all).

Om ett tjänst erbjudande inte är tillgängligt i en speciell region kan du dela ditt intresse genom att kontakta din Microsoft-representant.

| Regions typ | Icke-regional | Grundläggande | Konventionell | Specialiserade | Tillgänglighetszoner | Dataplacering |
| --- | --- | --- | --- | --- | --- | --- |
| Rekommenderas | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Demand-driven | :heavy_check_mark: | :heavy_check_mark: |
| Alternativa | :heavy_check_mark: | :heavy_check_mark: | Demand-driven | Demand-driven | Ej tillämpligt | :heavy_check_mark: |

### <a name="services-by-category"></a>Tjänster efter kategori

Som tidigare nämnts klassificerar Azure tjänster i tre kategorier: grundläggande, traditionell och specialiserad. Tjänste kategorier tilldelas allmänt tillgängliga. Ofta startar tjänster deras livs cykel som en specialiserad tjänst och när efter frågan och användnings ökningar kan befordras till vanlig eller grundläggande. I följande tabell visas kategorin för tjänster som grundläggande, traditionell. Tänk på följande om tabellen:

- Vissa tjänster är icke-regionala. Information och en lista över icke-regionala tjänster finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/).
- Äldre generation tjänster eller virtuella datorer visas inte. Mer information finns i dokumentationen [för tidigare generationer av virtuella dator storlekar](../virtual-machines/sizes-previous-gen.md).
- Tjänster har inte tilldelats någon kategori förrän allmän tillgänglighet (GA). Information och en lista över förhands gransknings tjänster finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/). 

> [!div class="mx-tableFixed"]
> | Grundläggande                           | Konventionell                                        | 
> |----------------------------------------|---------------------------------------------------|
> | Lagringskonton                       | API Management                                    | 
> | Application Gateway                    | App Configuration                                 | 
> | Azure Backup                           | App Service                                       | 
> | Azure Cosmos DB                        | Automation                                        | 
> | Azure Data Lake Storage Gen2           | Azure Active Directory Domain Services            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | Azure Public IP                        | Azure Cache for Redis                             | 
> | Azure SQL Database                     | Azure Cognitive Search                            | 
> | Azure SQL Managed Instance             | Azure Cognitive Services                          | 
> | Disklagring                           | Azure Cognitive Services: Visuellt innehåll         | 
> | Event Hubs                             | Azure-Cognitive Services: Content Moderator       | 
> | Key Vault                              | Azure Cognitive Services: ansikte                    | 
> | Lastbalanserare                          | Azure Cognitive Services: avancerad läsare        | 
> | Service Bus                            | Azure-Cognitive Services: Language Understanding  | 
> | Service Fabric                         | Azure Cognitive Services: tal tjänster         | 
> | Lagring: frekvent/låg frekvent Blob Storage-nivåer   | Azure Cognitive Services: Textanalys          | 
> | Lagring: Managed Disks                 | Azure Cognitive Services: Translator              | 
> | Virtual Machine Scale Sets             | Öppna Azure-datautforskaren                               | 
> | Virtual Machines                       | Azure Data Share                                  | 
> | Virtual Machines: Azure-dedikerad värd | Azure Database for MySQL                          | 
> | Virtual Machines: Av2-Series           | Azure Database for PostgreSQL                     | 
> | Virtual Machines: Bs-Series            | Azure DDoS Protection                             | 
> | Virtual Machines: DSv2-Series          | Azure Firewall                                    | 
> | Virtual Machines: DSv3-Series          | Azure Firewall Manager                            | 
> | Virtual Machines: Dv2-Series           | Azure Functions                                   | 
> | Virtual Machines: Dv3-Series           | Azure IoT Hub                                     |     
> | Virtual Machines: ESv3-Series          | Azure Kubernetes Service (AKS)                    | 
> | Virtual Machines: Ev3-Series           | Azure Machine Learning                            | 
> | Virtual Network                        | Azure Monitor: Application Insights               | 
> | VPN Gateway                            | Azure Monitor: Log Analytics                      | 
> |                                        | Azure Private Link                                | 
> |                                        | Azure Red Hat OpenShift                           | 
> |                                        | Azure Site Recovery                               | 
> |                                        | Azure Stream Analytics                            | 
> |                                        | Azure Synapse Analytics                           | 
> |                                        | Batch                                             | 
> |                                        | Cloud Services: M-serien                          | 
> |                                        | Container Instances                               | 
> |                                        | Container Registry                                | 
> |                                        | Data Factory                                      | 
> |                                        | Event Grid                                        | 
> |                                        | HDInsight                                         |  
> |                                        | Logic Apps                                        | 
> |                                        | Media Services                                    | 
> |                                        | Network Watcher                                   | 
> |                                        | Notification Hubs                                 | 
> |                                        | Premium-Blob Storage                              | 
> |                                        | Premium Files-lagring                             | 
> |                                        | Virtual Machines: Ddsv4-Series                    | 
> |                                        | Virtual Machines: Ddv4-Series                     | 
> |                                        | Virtual Machines: Dsv4-Series                     | 
> |                                        | Virtual Machines: Dv4-Series                      | 
> |                                        | Virtual Machines: Edsv4-Series                    | 
> |                                        | Virtual Machines: Edv4-Series                     | 
> |                                        | Virtual Machines: Esv4-Series                     | 
> |                                        | Virtual Machines: Ev4-Series                      | 
> |                                        | Virtual Machines: Fsv2-Series                     | 
> |                                        | Virtual Machines: M-serien                        | 
> |                                        | Virtuellt WAN                                       | 



### <a name="specialized-services"></a>Specialiserade tjänster
Som tidigare nämnts klassificerar Azure tjänster i tre kategorier: grundläggande, traditionell och specialiserad. Tjänste kategorier tilldelas allmänt tillgängliga. Ofta startar tjänster deras livs cykel som en specialiserad tjänst och när efter frågan och användnings ökningar kan befordras till vanlig eller grundläggande. I följande tabell visas specialiserade tjänster. 

> [!div class="mx-tableFixed"]
> | Specialiserade                                          |
> |------------------------------------------------------|
> | Azure API för FHIR                                   |
> | Azure Analysis Services                              |
> | Azure Cognitive Services: avvikelse detektor           |
> | Azure-Cognitive Services: Custom Vision              |
> | Azure Cognitive Services: formulär igenkänning            |
> | Azure Cognitive Services: Personanpassare               |
> | Azure-Cognitive Services: QnA Maker                  |
> | Azure Database for MariaDB                           |
> | Azure Database Migration Service                     |
> | Dedikerad HSM i Azure                                  |
> | Azure Digital Twins                                  |
> | Azure Health Bot                                     |
> | Azure HPC Cache                                      |
> | Azure Lab Services                                   |
> | Azure NetApp Files                                   |
> | Azure SignalR Service                                |
> | Azure våren Cloud service                           |
> | Azure Time Series Insights                           |
> | Azure VMware Solution                                |
> | Azure VMware Solution by CloudSimple                 |
> | Data Lake Analytics                                  |
> | Azure Machine Learning Studio (klassisk)              |
> | Spatial Anchors                                      |
> | Lagring: Arkivlagring                             |
> | Ultra Disklagring                                   |
> | Video Indexer                                        |
> | Virtual Machines: DASv4-Series                       |
> | Virtual Machines: DAv4-Series                        |
> | Virtual Machines: DCsv2-serien                       |
> | Virtual Machines: EASv4-Series                       |
> | Virtual Machines: EAv4-Series                        |
> | Virtual Machines: HBv1-Series                        |
> | Virtual Machines: HBv2-Series                        |
> | Virtual Machines: HCv1-Series                        |
> | Virtual Machines: H-serien                           |
> | Virtual Machines: LSv2-Series                        |
> | Virtual Machines: Mv2-Series                         |
> | Virtual Machines: NCv3-Series                        |
> | Virtual Machines: NDv2-Series                        |
> | Virtual Machines: NVv3-Series                        |
> | Virtual Machines: NVv4-Series                        | 
> | Virtual Machines: SAP HANA på stora Azure-instanser  |




## <a name="next-steps"></a>Nästa steg

- [Regioner som stöder Tillgänglighetszoner i Azure](az-region.md)
- [Snabbstartsmallar](https://aka.ms/azqs)
