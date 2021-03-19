---
title: Förstå skillnaderna mellan Azure Cosmos DB NoSQL och Relations databaser
description: Den här artikeln räknar upp skillnaderna mellan NoSQL och Relations databaser
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: d986106337eb1ede2f6d61303d8a4c487bbed276
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93088479"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>Förstå skillnaderna mellan NoSQL och Relations databaser
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Den här artikeln innehåller en lista över några av de viktiga fördelarna med NoSQL-databaser över Relations databaser. Vi kommer också att diskutera några av utmaningarna med att arbeta med NoSQL. För en djupgående titt på de olika data lager som finns kan du titta närmare på vår artikel om hur [du väljer rätt data lager](/azure/architecture/guide/technology-choices/data-store-overview).

## <a name="high-throughput"></a>Högt genomflöde

En av de mest uppenbara utmaningarna vid underhåll av ett Relations databas system är att de flesta Relations motorer använder lås och lås för att genomdriva strikt [syra semantik](https://en.wikipedia.org/wiki/ACID). Den här metoden har fördelar med att säkerställa ett konsekvent data tillstånd i databasen. Det finns dock tunga kompromisser med avseende på samtidighet, svars tid och tillgänglighet. På grund av dessa grundläggande arkitektur begränsningar kan hög transaktions volymer resultera i att Shard data måste vara manuellt. Implementering av manuella horisontell partitionering kan vara en tids krävande och smärtsamt övning.

I dessa scenarier kan [distribuerade databaser](https://en.wikipedia.org/wiki/Distributed_database) erbjuda en mer skalbar lösning. Underhåll kan dock fortfarande vara en kostsam och tids krävande övning. Administratörer kan behöva göra extra arbete för att säkerställa att systemets distribuerade natur är transparent. De kan också behöva beakta databasens "frånkopplad" typ.

[Azure Cosmos DB](./introduction.md) fören klar dessa utmaningar genom att distribueras över hela världen över alla Azure-regioner. Partitions intervall kan delas upp dynamiskt för att sömlöst utöka databasen i linje med programmet, samtidigt som hög tillgänglighet upprätthålls. Med detaljerade resurser för flera innehavare och tätt kontrollerade, underlättar moln styrningen av [förvånande latens](./consistency-levels.md#consistency-levels-and-latency) och förutsägbara prestanda. Partitionering är fullständigt hanterad, så administratörer behöver inte skriva kod eller hantera partitioner.

Om dina transaktions volymer når extrema nivåer, till exempel många tusentals transaktioner per sekund, bör du överväga en distribuerad NoSQL-databas. Överväg Azure Cosmos DB för maximal effektivitet, enkel underhåll och minskad ägande kostnad.

:::image type="content" source="./media/relational-or-nosql/backend-scaled.png" alt-text="Backend" border="false":::

## <a name="hierarchical-data"></a>Hierarkiska data

Det finns ett stort antal användnings fall där transaktioner i databasen kan innehålla flera överordnade-underordnade-relationer. Dessa relationer kan växa avsevärt över tiden och bevisa svårt att hantera. Former av [hierarkiska databaser](https://en.wikipedia.org/wiki/Hierarchical_database_model) uppstod under 1980s, men var inte populära på grund av ineffektiv lagring. De förlorade även drag som [valda Codd-Relations modell](https://en.wikipedia.org/wiki/Relational_model) blev den facto-standard som används av praktiskt taget alla vanliga databas hanterings system.

I dag är det dock betydligt större av dokument format databaser. De här databaserna kan vara en omstrukturering av den hierarkiska databasens paradigm, som nu har hindrats av att det rör sig om kostnaden för att lagra data på disk. Det innebär att du kan se ett antimönster jämfört med moderna dokument metoder genom att upprätthålla många komplexa relationer över överordnade och underordnade entiteter i en Relations databas.

Uppkomsten av [objektorienterad design](https://en.wikipedia.org/wiki/Object-oriented_design)och den [avvikelse](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) som uppstår när den kombineras med relations modeller, visar också ett anti-mönster i Relations databaser för vissa användnings fall. Dolt men ofta betydande underhålls kostnader kan uppstå som ett resultat. Även om [orm-metoder](https://en.wikipedia.org/wiki/Object-relational_mapping) har utvecklats för att delvis minimera detta, så sammansamlingen av dokumentbaserade databaser på ett mycket bättre sätt med objektorienterade metoder. Med den här metoden tvingas utvecklare inte att allokeras till ORM-drivrutiner eller eker-språkspecifika [säkerhets-databasmotor](https://en.wikipedia.org/wiki/Object_database). Om dina data innehåller många överordnade och underordnade relationer och djup hierarkier, kanske du vill överväga att använda en NoSQL-dokument databas som [Azure Cosmos DB SQL API](./introduction.md).

:::image type="content" source="./media/relational-or-nosql/order-orderdetails.jpg" alt-text="OrderDetails":::

## <a name="complex-networks-and-relationships"></a>Komplexa nätverk och relationer

Ironically, med tanke på deras namn, visar Relations databaser mindre än den optimala lösningen för modellering av djup och komplexa relationer. Orsaken till detta är att relationerna mellan entiteter egentligen inte finns i en Relations databas. De måste beräknas vid körning, med komplexa relationer som kräver kartesiska-kopplingar för att tillåta mappning med hjälp av frågor. Därför blir driften exponentiellt dyrare när det gäller beräkningen när relationerna ökar. I vissa fall kommer en Relations databas som försöker hantera sådana entiteter att bli oanvändbar.

Olika former av "nätverks"-databaser pågick under den tid då Relations databaser uppnåddes, men precis som med hierarkiska databaser har dessa system fått en popularitet. Långsam införande berodde på brist på användnings fall vid tidpunkten och lagrings ineffektivitet. I dag kan Graph Database-motorer betraktas som en ny uppkomst av nätverks databasens paradigm. De främsta fördelarna med dessa system är att relationer lagras som "första klass medborgarna" i databasen. Det innebär att du kan gå igenom relationer i konstant tid i stället för att öka komplexiteten med varje ny koppling eller kors produkt.

Om du upprätthåller ett komplext nätverk av relationer i databasen kanske du vill överväga en graf-databas som [Azure Cosmos DB Gremlin-API](./graph-introduction.md) för att hantera dessa data.

:::image type="content" source="./media/relational-or-nosql/graph.png" alt-text="Databas diagrammet visar flera anställda och avdelningar som är anslutna till varandra.":::

Azure Cosmos DB är en databas tjänst för flera modeller som erbjuder en API-projektion för alla större NoSQL modell typer; Kolumn – familj, dokument, graf och nyckel värde. API-skikten [Gremlin (graf)](./gremlin-support.md) och SQL (Core) Document är helt driftskompatibla. Detta har fördelar för att växla mellan olika modeller på programmerings nivå. Diagram lager kan frågas i termer av både komplexa nätverks bläddringskontroll och transaktioner som modelleras som dokument poster i samma butik.

## <a name="fluid-schema"></a>Flytande schema

En annan speciell egenskap i Relations databaser är att scheman måste definieras i design läge. Detta har fördelar avseende referens integritet och data överensstämmelse. Men det kan också vara restriktivt eftersom programmet växer. Att svara på ändringar i schemat över logiskt åtskilda modeller som delar samma tabell-eller databas definition kan bli komplex över tid. Sådana användnings fall drar ofta nytta av schemat för att ansvara för programmet för hantering per post. Detta kräver att databasen är "schema oberoende" och att posterna ska vara "självbeskriva" i termer av de data som finns i dem.

Om du hanterar data vars strukturer ständigt förändras i hög grad, särskilt om transaktioner kan komma från externa källor där det är svårt att genomdriva överensstämmelse i databasen, kanske du vill överväga en mer schema-oberoende metod med hjälp av en hanterad NoSQL Database-tjänst som Azure Cosmos DB.

## <a name="microservices"></a>Mikrotjänster

Mönstret för [mikrotjänster](https://en.wikipedia.org/wiki/Microservices) har vuxit avsevärt under de senaste åren. Det här mönstret har sina rötter i [tjänsteorienterad arkitektur](https://en.wikipedia.org/wiki/Service-oriented_architecture). De facto-standarder som används för data överföring i dessa moderna mikrotjänst arkitekturer är [JSON](https://en.wikipedia.org/wiki/JSON), vilket även sker för att vara lagrings medium för de stora majoriteten dokumentbaserade NoSQL-databaser. Detta gör NoSQL-dokument en mycket smidigare anpassning för både persistence och synkronisering (med hjälp av [händelse källor](https://en.wikipedia.org/wiki/Event-driven_architecture)) över komplexa mikrotjänst implementeringar. Mer traditionella Relations databaser kan vara mycket mer komplexa för att underhålla i dessa arkitekturer. Detta beror på den större mängd omvandling som krävs för både tillstånd och synkronisering över API: er. Azure Cosmos DB särskilt har ett antal funktioner som gör att den ännu mer sömlöst passar för JSON-baserade mikrotjänster-arkitekturer än många NoSQL-databaser:

* ett urval av rena JSON-datatyper
* en JavaScript-motor och ett [fråge-API](./javascript-query-api.md) som är inbyggt i databasen.
* en tillstånds känslig [ändrings matning](./change-feed.md) som klienter kan prenumerera på för att få meddelanden om ändringar i en behållare.

## <a name="some-challenges-with-nosql-databases"></a>Vissa utmaningar med NoSQL-databaser

Även om det finns några tydliga fördelar när du implementerar NoSQL-databaser, finns det också vissa utmaningar som du kanske vill ta hänsyn till. De är kanske inte tillgängliga i samma grad när du arbetar med relations modellen:

* transaktioner med många relationer som pekar på samma entitet.
* transaktioner som kräver stark konsekvens i hela data mängden.

Den första utmaningen är att använda NoSQL-databaser i-databaser är vanligt vis avnormaliserat, vilket som tidigare har ändrats, vilket ger mer effektiva läsningar i ett distribuerat system. Det finns dock några design utmaningar som kommer i spel med den här metoden. Låt oss ta ett exempel på en produkt som är relaterad till en kategori och flera Taggar:

:::image type="content" source="./media/relational-or-nosql/many-joins.png" alt-text="Kopplingar":::

En metod för metod tips i en NoSQL-dokument databas är att avnormalisera kategori namn och taggnamn direkt i ett "produkt dokument". För att se till att kategorier, taggar och produkter är synkroniserade har design alternativen för att under lätta detta ytterligare underhåll, eftersom data dupliceras över flera poster i produkten, i stället för en enkel uppdatering i en "1-till-många"-relation och en koppling för att hämta data. 

Detta är att läsningar är mer effektivt i den avnormaliserade posten och blir allt mer effektivt när antalet konceptuellt anslutna enheter ökar. Men trots att Läs effektiviteten ökar med ökande antal anslutna entiteter i en avnormaliserad post, så kan det vara underhålls komplexiteten för att hålla enheterna synkroniserade. Ett sätt att begränsa denna kompromiss är att skapa en [hybrid data modell](./modeling-data.md#hybrid-data-models).

Även om det finns större flexibilitet i NoSQL-databaser för att hantera dessa kompromisser kan ökad flexibilitet också skapa fler design beslut. Se vår artikel [om hur du kan modellera och partitionera data på Azure Cosmos dB med ett verkligt exempel](./how-to-model-partition-example.md)som innehåller en metod för att hålla [avnormaliserade användar data synkroniserade](./how-to-model-partition-example.md#denormalizing-usernames) där användare inte bara sitter i olika partitioner, men i olika behållare.

I förhållande till stark konsekvens är det sällsynt att detta krävs för hela data uppsättningen. Men i de fall där detta är nödvändigt kan det vara en utmaning i distribuerade databaser. För att säkerställa stark konsekvens måste data synkroniseras över alla repliker och regioner innan klienter kan läsa den. Detta kan öka svars tiden för läsningar.

Azure Cosmos DB erbjuder mer flexibilitet än Relations databaser för de olika kompromisser som är relevanta här, men för små skalnings implementeringar kan den här metoden lägga till fler design överväganden. Se vår artikel om [konsekvens, tillgänglighet och prestanda](./consistency-levels.md) för mer information om det här ämnet.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du hanterar ditt Azure Cosmos-konto och andra koncept:

* [Så här hanterar du ditt Azure Cosmos-konto](how-to-manage-database-account.md)
* [Global distribution](distribute-data-globally.md)
* [Konsekvensnivåer](consistency-levels.md)
* [Arbeta med Azure Cosmos-behållare och-objekt](account-databases-containers-items.md)
* [VNET-tjänstens slut punkt för ditt Azure Cosmos-konto](how-to-configure-vnet-service-endpoint.md)
* [IP-brandvägg för ditt Azure Cosmos-konto](how-to-configure-firewall.md)
* [Anvisningar för att lägga till och ta bort Azure-regioner i ditt Azure Cosmos-konto](how-to-manage-database-account.md)
* [Azure Cosmos DB service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)