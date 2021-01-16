---
title: Azure Cosmos DB tjänst kvoter
description: Azure Cosmos DB tjänst kvoter och standard gränser för olika resurs typer.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 793ff9eedb747da0edcbbf2df50b62f06f407892
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247433"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB tjänst kvoter

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Den här artikeln innehåller en översikt över standardkvoterna som erbjuds till olika resurser i Azure Cosmos DB.

## <a name="storage-and-database-operations"></a>Lagrings-och databas åtgärder

När du har skapat ett Azure Cosmos-konto under din prenumeration kan du hantera data i ditt konto genom att [skapa databaser, behållare och objekt](account-databases-containers-items.md).

### <a name="provisioned-throughput"></a>Etablerat dataflöde

Du kan etablera data flöde på en behållar nivå eller på databas nivå avseende [enheter för programbegäran (ru/s eller ru: er)](request-units.md). I följande tabell visas gränserna för lagring och data flöde per container/databas.

| Resurs | Standardgräns |
| --- | --- |
| Maximalt antal ru: er per behållare ([dedikerat data flöde har allokerat läge](account-databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 som standard. Du kan öka det genom att [arkivera ett support ärende för Azure](create-support-request-quota-increase.md) |
| Maximalt antal ru: er per databas ([delat data flöde har allokerat läge](account-databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 som standard. Du kan öka det genom att [arkivera ett support ärende för Azure](create-support-request-quota-increase.md) |
| Maximal ru: er per (logisk) partition | 10 000 |
| Maximalt lagrings utrymme för alla objekt per (logisk) partition | 20 GB |
| Maximalt antal distinkta (logiska) sessionsnycklar | Obegränsat |
| Maximalt lagrings utrymme per behållare | Obegränsat |
| Maximalt lagrings utrymme per databas | Obegränsat |
| Maximal storlek på bifogade filer per konto (bilage funktionen är föråldrad) | 2 GB |
| Lägsta RU/s krävs per 1 GB | 10 RU/s<br>**Obs!** om din behållare eller databas innehåller mer än 1 TB data kan ditt konto vara berättigat till vårt [program för "hög lagring/låg data flöde"](set-throughput.md#high-storage-low-throughput-program) |

> [!NOTE]
> Mer information om metod tips för att hantera arbets belastningar som har partitionsnyckel som kräver högre gränser för lagring eller data flöde finns i [skapa en syntetisk partitionsnyckel](synthetic-partition-keys.md).

### <a name="minimum-throughput-limits"></a>Minsta data flödes gränser

En Cosmos-behållare (eller delad data flödes databas) måste ha ett minsta data flöde på 400 RU/s. När behållaren växer kräver Cosmos DB ett minsta data flöde för att se till att databasen eller behållaren har tillräckligt med resurs för driften.

Det aktuella och lägsta data flödet för en behållare eller en databas kan hämtas från Azure Portal eller SDK: er. Mer information finns i [etablera data flöde på behållare och databaser](set-throughput.md). 

Det faktiska antalet RU/s kan variera beroende på din konto konfiguration. Du kan använda [Azure Monitor mått](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) för att visa historiken över det etablerade data flödet (ru/s) och lagrings utrymme på en resurs. 

#### <a name="minimum-throughput-on-container"></a>Lägsta data flöde för container 

Om du vill uppskatta det minsta data flöde som krävs för en behållare med manuellt genomflöde kan du hitta Max:

* 400 RU/s 
* Aktuellt lagrings utrymme i GB * 10 RU/s
* Mest RU/s etablerad på behållaren/100

Exempel: anta att du har en behållare etablerad med 400 RU/s och 0 GB lagring. Du ökar data flödet till 50 000 RU/s och importerar 20 GB data. Lägsta RU/s är nu `MAX(400, 20 * 10 RU/s per GB, 50,000 RU/s / 100)` = 500 ru/s. Med tiden växer lagringen till 200 GB. Lägsta RU/s är nu `MAX(400, 200 * 10 RU/s per GB, 50,000 / 100)` = 2000 ru/s. 

**Obs!** om din behållare eller databas innehåller mer än 1 TB data kan ditt konto vara berättigat till vårt [program för "hög lagring/låg data flöde"](set-throughput.md#high-storage-low-throughput-program).

#### <a name="minimum-throughput-on-shared-throughput-database"></a>Minsta data flöde på delad data flödes databas 
För att uppskatta det minsta data flöde som krävs för en delad data flödes databas med manuellt data flöde, hitta Max:

* 400 RU/s 
* Aktuellt lagrings utrymme i GB * 10 RU/s
* Mest RU/s etablerad i databasen/100
* 400 + MAX (antal behållare-25,0) * 100 RU/s

Exempel: anta att du har en databas etablerad med 400 RU/s, 15 GB lagring och 10 behållare. Lägsta RU/s är `MAX(400, 15 * 10 RU/s per GB, 400 / 100, 400 + 0 )` = 400 ru/s. Om det fanns 30 behållare i databasen skulle minimivärdet RU/s vara `400 + MAX(30 - 25, 0) * 100 RU/s` = 900 ru/s. 

**Obs!** om din behållare eller databas innehåller mer än 1 TB data kan ditt konto vara berättigat till vårt [program för "hög lagring/låg data flöde"](set-throughput.md#high-storage-low-throughput-program).

Här är de lägsta etablerade RU-gränserna i sammanfattning. 

| Resurs | Standardgräns |
| --- | --- |
| Lägsta ru: er per container ([dedikerat data flöde har allokerat läge](./account-databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Lägsta ru: er per databas ([delat data flöde, etablerings läge](./account-databases-containers-items.md#azure-cosmos-containers)) | 400 RU/s för de första 25 behållarna. Ytterligare 100 RU/s för varje behållare efteråt. |

Cosmos DB stöder programmerings skalning av data flöde (RU/s) per behållare eller databas via SDK: er eller portalen.    

Beroende på de aktuella inställningarna för RU/s och resurs inställningarna kan varje resurs skalas synkront och omedelbart mellan de lägsta RU/s och upp till 100x minst RU/s. Om det begärda data flöde svärdet ligger utanför intervallet utförs skalningen asynkront. Asynkron skalning kan ta minuter till timmar att slutföra beroende på begärt data flöde och data lagrings storlek i behållaren.  

### <a name="serverless"></a>Utan server

Utan [Server](serverless.md) kan du använda dina Azure Cosmos DB resurser på ett konsumtions sätt. I följande tabell visas gränserna för lagring och data flödes kapacitet per behållare/databas.

| Resurs | Gräns |
| --- | --- |
| Maximalt RU/s per (logisk) partition | 5 000 |
| Maximalt lagrings utrymme för alla objekt per (logisk) partition | 20 GB |
| Maximalt antal distinkta (logiska) sessionsnycklar | Obegränsat |
| Maximalt lagrings utrymme per behållare | 50 GB |

## <a name="control-plane-operations"></a>Kontroll Plans åtgärder

Du kan [etablera och hantera ditt Azure Cosmos-konto](how-to-manage-database-account.md) med hjälp av Azure Portal, Azure PowerShell, Azure CLI och Azure Resource Manager mallar. I följande tabell visas gränserna per prenumeration, konto och antal åtgärder.

| Resurs | Standardgräns |
| --- | --- |
| Maximalt antal databas konton per prenumeration | 50 som standard. Du kan öka det genom att [arkivera ett support ärende för Azure](create-support-request-quota-increase.md)|
| Maximalt antal regionala redundanser | 1/timme som standard. Du kan öka det genom att [arkivera ett support ärende för Azure](create-support-request-quota-increase.md)|

> [!NOTE]
> Regional redundans gäller endast för enskilda region skrivningar. Skriv konton i flera regioner kräver inte eller har inga begränsningar för ändring av Skriv regionen.

Cosmos DB automatiskt tar säkerhets kopior av dina data med jämna mellanrum. Mer information om säkerhets kopierings intervall och Windows finns i säkerhets [kopiering online och data återställning på begäran i Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Gräns per konto

### <a name="provisioned-throughput"></a>Etablerat dataflöde

| Resurs | Standardgräns |
| --- | --- |
| Maximalt antal databaser | Obegränsat |
| Maximalt antal behållare per databas med delat data flöde |25 |
| Maximalt antal behållare per databas eller konto med dedikerat data flöde  |obegränsat |
| Maximalt antal regioner | Ingen gräns (alla Azure-regioner) |

### <a name="serverless"></a>Utan server

| Resurs | Gräns |
| --- | --- |
| Maximalt antal databaser | Obegränsat |
| Maximalt antal behållare per konto  | 100 |
| Maximalt antal regioner | 1 (alla Azure-regioner) |

## <a name="per-container-limits"></a>Gränser per behållare

Beroende på vilket API du använder kan en Azure Cosmos-behållare representera antingen en samling, en tabell eller ett diagram. Behållare har stöd för konfigurationer för [unika nyckel begränsningar](unique-keys.md), [lagrade procedurer, utlösare och UDF: er](stored-procedures-triggers-udfs.md), samt [indexerings princip](how-to-manage-indexing-policy.md). I följande tabell visas de gränser som är begränsade till konfigurationer i en behållare. 

| Resurs | Standardgräns |
| --- | --- |
| Maximal längd på databas-eller behållar namn | 255 |
| Maximalt antal lagrade procedurer per behållare | 100 <sup>*</sup>|
| Maximalt antal UDF: er per behållare | 25.1 <sup>*</sup>|
| Maximalt antal sökvägar i indexerings principen| 100 <sup>*</sup>|
| Maximalt antal unika nycklar per behållare|10 <sup>*</sup>|
| Maximalt antal sökvägar per unik nyckel begränsning|16 <sup>*</sup>|
| Maximalt TTL-värde |2 147 483 647|

<sup>*</sup> Du kan öka alla dessa gränser per behållare genom att skapa en [Azure-supportbegäran](create-support-request-quota-increase.md).

## <a name="per-item-limits"></a>Begränsningar per objekt

Beroende på vilket API du använder kan ett Azure Cosmos-objekt representera antingen ett dokument i en samling, en rad i en tabell eller en nod eller kant i ett diagram. I följande tabell visas gränserna per objekt i Cosmos DB. 

| Resurs | Standardgräns |
| --- | --- |
| Maximal storlek för ett objekt | 2 MB (UTF-8-längd på JSON-representation) |
| Maximal längd på partitionerings nyckel värde | 2048 byte |
| Maximal längd på ID-värde | 1023 byte |
| Maximalt antal egenskaper per objekt | Ingen genomförbar gräns |
| Maximal längd för egenskaps namn | Ingen genomförbar gräns |
| Maximal längd för egenskap svärdet | Ingen genomförbar gräns |
| Maximal längd för sträng egenskaps värde | Ingen genomförbar gräns |
| Maximal längd på numeriskt egenskaps värde | IEEE754 dubbel precision 64-bit |
| Maximal kapslings nivå för inbäddade objekt/matriser | 128 |
| Maximalt TTL-värde |2 147 483 647|

Det finns inga begränsningar för objektets nytto laster som antalet egenskaper och kapslings djup, förutom längd begränsningarna för partitionsnyckel och ID-värden och den övergripande storleks begränsningen på 2 MB. Du kan behöva konfigurera indexerings princip för behållare med stora eller komplexa objekt strukturer för att minska RU-förbrukningen. Se [modell objekt i Cosmos DB](how-to-model-partition-example.md) för ett verkligt exempel och mönster för att hantera stora objekt.

## <a name="per-request-limits"></a>Begränsningar per begäran

Azure Cosmos DB stöder [CRUD-och Query-åtgärder](/rest/api/cosmos-db/) mot resurser som behållare, objekt och databaser. Det stöder också [transaktionella batch-begäranden](/dotnet/api/microsoft.azure.cosmos.transactionalbatch) mot flera objekt med samma partitionsnyckel i en behållare.

| Resurs | Standardgräns |
| --- | --- |
| Maximal körnings tid för en enskild åtgärd (t. ex. en lagrad procedur körning eller en enskild frågevy)| 5 SEK |
| Maximal begär ande storlek (till exempel lagrad procedur, CRUD)| 2 MB |
| Maximal svars storlek (till exempel en sid brytnings fråga) | 4 MB |
| Maximalt antal åtgärder i en transaktionell batch | 100 |

När en åtgärd som fråga når tids gränsen för körning eller svars storlek returneras en sida med resultat och en fortsättnings-token till klienten för att återuppta körningen. Det finns ingen genomförbar gräns för varaktigheten som en enskild fråga kan köras mellan sidor/fortsättningar.

Cosmos DB använder HMAC för auktorisering. Du kan antingen använda en primär nyckel eller en [resurs-token](secure-access-to-data.md) för detaljerad åtkomst kontroll till resurser som behållare, partitionsalternativ eller objekt. I följande tabell visas begränsningar för autentiseringstoken i Cosmos DB.

| Resurs | Standardgräns |
| --- | --- |
| Maximal förfallo tid för primär token | 15 min  |
| Minsta förfallo tid för resurs-token | 10 min  |
| Maximal förfallo tid för resurs-token | 24 timmar som standard. Du kan öka det genom att [arkivera ett support ärende för Azure](create-support-request-quota-increase.md)|
| Maximal klock skevning för token-auktorisering| 15 min |

Cosmos DB stöder körning av utlösare under skrivningar. Tjänsten har stöd för maximalt en för-utlösare och en efter-utlösare per Skriv åtgärd.

## <a name="metadata-request-limits"></a>Begränsningar för metadata

Azure Cosmos DB underhåller systemmetadata för varje konto. Med dessa metadata kan du räkna upp samlingar, databaser, andra Azure Cosmos DB resurser och deras konfigurationer utan kostnad.

| Resurs | Standardgräns |
| --- | --- |
|Maximal samlings skapande frekvens per minut|    100|
|Högsta antal skapade databaser per minut|    100|
|Högsta allokerade data flödes uppdaterings frekvens per minut|    5|

## <a name="limits-for-autoscale-provisioned-throughput"></a>Begränsningar för autoskalning av allokerat data flöde

Mer detaljerad information om data flödes-och lagrings gränser med autoskalning finns i artikeln om [autoskalning](provision-throughput-autoscale.md#autoscale-limits) och [vanliga frågor och svar](autoscale-faq.md#lowering-the-max-rus) .

| Resurs | Standardgräns |
| --- | --- |
| Maximalt RU/s systemet kan skalas till |  `Tmax`, den autoskalning Max RU/s som anges av användaren|
| Lägsta RU/s-systemet kan skalas till | `0.1 * Tmax`|
| Aktuella RU/s systemet skalas till  |  `0.1*Tmax <= T <= Tmax`, baserat på användning|
| Lägsta fakturerbara RU/s per timme| `0.1 * Tmax` <br></br>Faktureringen görs per timme, där du debiteras för de högsta RU/s-systemet som skalats till i timmen eller `0.1*Tmax` , beroende på vilket som är högre. |
| Minsta autoskalning för max RU/s för en behållare  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)` avrundat till närmaste 1000 RU/s |
| Minsta autoskalad Max RU/s för en databas  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`, avrundat till närmaste 1000 RU/s. <br></br>Observera att om din databas har fler än 25 behållare, ökar systemet minimi kraven för autoskalning Max RU/s av 1000 RU/s per ytterligare behållare. Om du till exempel har 30 behållare är det lägsta antalet RU/s som du kan ställa in den lägsta storleken som är 9000 RU/s (skalas mellan 900-9000 RU/s).

## <a name="sql-query-limits"></a>Begränsningar för SQL-frågor

Cosmos DB stöder frågor mot objekt med hjälp av [SQL](./sql-query-getting-started.md). I följande tabell beskrivs begränsningar i frågeuttryck, till exempel vad gäller antal satser eller frågor.

| Resurs | Standardgräns |
| --- | --- |
| Maximal längd på SQL-fråga| 256 kB |
| Maximalt antal kopplingar per fråga| 5 <sup>*</sup>|
| Maximalt antal UDF: er per fråga| 10 <sup>*</sup>|
| Maximalt antal poäng per polygon| 4096 |
| Maximalt antal inkluderade sökvägar per behållare| 500 |
| Maximalt antal undantagna sökvägar per behållare| 500 |
| Maximalt antal egenskaper i ett sammansatt index| 8 |

<sup>*</sup> Du kan öka dessa begränsningar för SQL-frågor genom att skapa en [Azure-supportbegäran](create-support-request-quota-increase.md).

## <a name="mongodb-api-specific-limits"></a>MongoDB API-begränsade gränser

Cosmos DB stöder MongoDB Wire Protocol för program som skrivits mot MongoDB. Du hittar de kommandon och protokoll versioner som stöds i [MongoDB funktioner och syntax](mongodb-feature-support.md).

I följande tabell visas de gränser som är begränsade till stöd för MongoDB-funktioner. Andra tjänst gränser som nämns för SQL (Core) API: t gäller även för MongoDB-API: et.

| Resurs | Standardgräns |
| --- | --- |
| Maximal minnes storlek för MongoDB-fråga (den här begränsningen gäller endast 3,2-Server version) | 40 MB |
| Maximal körnings tid för MongoDB-åtgärder| 30 s |
| Tids gräns för inaktiv anslutning för anslutning till Server Sidan * | 30 minuter |

\* Vi rekommenderar att klient program ställer in tids gränsen för inaktiv anslutning i driv rutins inställningarna till 2-3 minuter, eftersom [Standard-timeout för Azure Loadbalancer är 4 minuter](../load-balancer/load-balancer-tcp-idle-timeout.md).  Den här tids gränsen ser till att inaktiva anslutningar inte stängs av en mellanliggande belastningsutjämnare mellan klient datorn och Azure Cosmos DB.

## <a name="try-cosmos-db-free-limits"></a>Försök Cosmos DB fria gränser

I följande tabell visas gränserna för try- [Azure Cosmos dB för kostnads fri](https://azure.microsoft.com/try/cosmosdb/) utvärdering.

| Resurs | Standardgräns |
| --- | --- |
| Utvärderings periodens varaktighet | 30 dagar (en ny utvärderings version kan begäras efter förfallo datum) <br> Efter förfallo datum raderas informationen som lagras. |
| Maximalt antal behållare per prenumeration (SQL, Gremlin, Tabell-API) | 1 |
| Maximalt antal behållare per prenumeration (MongoDB-API) | 3 |
| Maximalt data flöde per behållare | 5000 |
| Maximalt data flöde per delat data flödes databas | 20000 |
| Maximalt totalt lagrings utrymme per konto | 10 GB |

Testa Cosmos DB stöder global distribution bara i Central USA, Nord Europa och Sydostasien regioner. Det går inte att skapa biljetter för Azure-Support för try Azure Cosmos DB-konton. Support ges dock för prenumeranter med befintliga support avtal.

## <a name="azure-cosmos-db-free-tier-account-limits"></a>Konto gränser för Azure Cosmos DB kostnads fri nivå

I följande tabell visas gränserna för [Azure Cosmos DB kostnads fria nivå konton.](optimize-dev-test.md#azure-cosmos-db-free-tier)

| Resurs | Standardgräns |
| --- | --- |
| Antal kostnads fria nivå konton per Azure-prenumeration | 1 |
| Varaktighet för rabatt på kostnads fri nivå | Kontots livstid. Måste välja när kontot skapas. |
| Maximalt RU/s kostnads fritt | 400 RU/s |
| Maximal lagring kostnads fritt | 5 GB |
| Maximalt antal delade data flödes databaser | 5 |
| Maximalt antal behållare i en delad data flödes databas | 25 <br>På kostnads fria nivå konton är de lägsta RU/s för en delad data flödes databas med upp till 25 behållare 400 RU/s. |

Utöver ovanstående gäller [gräns värdena per konto](#per-account-limits) även för konton på den kostnads fria nivån.

> [!NOTE]
> Azure Cosmos DB kostnads fri nivå skiljer sig från det kostnads fria Azure-kontot. Azures kostnads fria konto erbjuder Azure-krediter och-resurser kostnads fritt under en begränsad tid. När du använder Azure Cosmos DB som en del av det här kostnads fria kontot får du 25 GB lagring och 400 RU/s med etablerade data flöde i 12 månader.

## <a name="next-steps"></a>Nästa steg

Läs mer om Cosmos DBs grundläggande koncept [global distribution](distribute-data-globally.md) och [partitionering](partitioning-overview.md) och [allokerat data flöde](request-units.md).

Kom igång med Azure Cosmos DB med någon av våra snabbstarter:

* [Komma igång med SQL-API för Azure Cosmos DB](create-sql-api-dotnet.md)
* [Kom igång med Azure Cosmos DB-API:n för Azure MongoDB](create-mongodb-nodejs.md)
* [Kom igång med Cassandra-API för Azure Cosmos DB](create-cassandra-dotnet.md)
* [Komma igång med Gremlin API för Azure Cosmos DB](create-graph-dotnet.md)
* [Komma igång med Tabell-API för Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/)
