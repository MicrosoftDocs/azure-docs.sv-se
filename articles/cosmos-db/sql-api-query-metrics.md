---
title: SQL-frågans mått för Azure Cosmos DB SQL API
description: Lär dig mer om hur du instrumenterar och felsöker SQL-frågeresultaten för Azure Cosmos DB begär Anden.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 019ca26143a4879efafa973299703f0abcb21162
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102488094"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Justera frågeprestanda med Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB innehåller ett [SQL-API för att fråga data](./sql-query-getting-started.md), utan att kräva schema eller sekundära index. Den här artikeln innehåller följande information för utvecklare:

* Information på hög nivå om hur Azure Cosmos DB SQL-frågekörningen fungerar
* Information om fråge förfrågnings-och svars rubriker och alternativ för klient-SDK
* Tips och metod tips för frågornas prestanda
* Exempel på hur du använder SQL-körnings statistik för att felsöka frågans prestanda

## <a name="about-sql-query-execution"></a>Om SQL-frågekörningen

I Azure Cosmos DB lagrar du data i behållare som kan växa till valfri [lagrings storlek eller genom att begära data flöde](partitioning-overview.md). Azure Cosmos DB skalar data sömlöst över fysiska partitioner under försättsblad för att hantera data tillväxt eller öka i ett allokerat data flöde. Du kan skicka SQL-frågor till alla behållare med hjälp av REST API eller någon av de [SQL-SDK](sql-api-sdk-dotnet.md): er som stöds.

En kort översikt över partitionering: du definierar en partitionsnyckel som "stad", som avgör hur data delas mellan fysiska partitioner. Data som tillhör en enskild partitionsnyckel (till exempel "stad" = = "Seattle") lagras i en fysisk partition, men vanligt vis har en enda fysisk partition flera partitionsnyckel. När en partition når sin lagrings storlek delar tjänsten sömlöst upp partitionen i två nya partitioner och delar upp partitionsnyckel jämnt mellan dessa partitioner. Eftersom partitioner är tillfälliga, använder API: erna en abstraktion av ett "partitionsnyckel", som anger intervallet för partitionens nyckel-hashar. 

När du skickar en fråga till Azure Cosmos DB utför SDK följande logiska steg:

* Parsa SQL-frågan för att fastställa frågans körnings plan. 
* Om frågan innehåller ett filter mot partitionsnyckel, till exempel `SELECT * FROM c WHERE c.city = "Seattle"` , dirigeras den till en enda partition. Om frågan inte har något filter i partitionsnyckel, körs den i alla partitioner och resultatet slås samman på klient sidan.
* Frågan körs inom varje partition i serien eller parallellt, baserat på klient konfigurationen. I varje partition kan frågan göra en eller flera tur och svar beroende på hur komplex frågan är, konfigurerad sid storlek och ett allokerat data flöde för samlingen. Varje körning returnerar antalet enheter för [programbegäran](request-units.md) som förbrukas av frågekörning, och om du vill kan du även fråga körnings statistik. 
* SDK utför en sammanfattning av frågeresultatet över partitioner. Om frågan t. ex. omfattar en ordning efter flera partitioner, sammanfogas resultaten från enskilda partitioner för att returnera resultat i en globalt sorterad ordning. Om frågan är en agg regering `COUNT` , så summeras antalet från enskilda partitioner för att producera det totala antalet.

SDK: erna tillhandahåller olika alternativ för frågekörningen. I .NET är de här alternativen till exempel tillgängliga i- `FeedOptions` klassen. I följande tabell beskrivs de här alternativen och hur de påverkar frågans körnings tid. 

| Alternativ | Beskrivning |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Måste anges till true för alla frågor som måste köras i fler än en partition. Det här är en uttrycklig flagga som gör det möjligt för dig att göra medveten prestanda kompromisser under utvecklings tiden. |
| `EnableScanInQuery` | Måste anges till sant om du har valt att inte indexera, men vill köra frågan via en sökning ändå. Endast tillämpligt om indexering för den begärda filter Sök vägen är inaktive rad. | 
| `MaxItemCount` | Det maximala antalet objekt som ska returneras per Round-resa till servern. Genom att ange till-1 kan du låta servern hantera antalet objekt. Eller så kan du sänka det här värdet för att bara hämta ett litet antal objekt per tur och retur. 
| `MaxBufferedItemCount` | Det här är ett alternativ på klient sidan och används för att begränsa minnes förbrukningen när du utför kors sorterings ordningen. Ett högre värde bidrar till att minska svars tiden för sortering mellan olika partitioner. |
| `MaxDegreeOfParallelism` | Hämtar eller anger antalet samtidiga åtgärder som kör klient sidan under parallell frågekörningen i Azure Cosmos Database-tjänsten. Ett positivt egenskaps värde begränsar antalet samtidiga åtgärder till set-värdet. Om den är inställd på mindre än 0 bestämmer systemet automatiskt antalet samtidiga åtgärder som ska köras. |
| `PopulateQueryMetrics` | Möjliggör detaljerad loggning av statistik över tid som ägnats åt olika faser av frågekörningen, till exempel kompileringstid, tids åtgång för tid och dokument inläsnings tid. Du kan dela utdata från frågans statistik med Azure-supporten för att diagnostisera problem med frågans prestanda. |
| `RequestContinuation` | Du kan återuppta frågekörningen genom att skicka i den ogenomskinliga fortsättnings-token som returnerades av en fråga. Fortsättnings-token kapslar in alla tillstånd som krävs för frågekörningen. |
| `ResponseContinuationTokenLimitInKb` | Du kan begränsa den maximala storleken för den önskade token som returneras av servern. Du kan behöva ange detta om program värden har gränser för svars huvudets storlek. Inställningen kan öka den totala varaktigheten och ru: er som förbrukas för frågan.  |

Anta till exempel att du använder en exempel fråga på en partitionsnyckel som begärts i en samling med `/city` som partitionsnyckel och etablerade med 100 000 ru/s av data flödet. Du begär den här frågan med `CreateDocumentQuery<T>` i .net som följande:

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
        MaxItemCount = -1, 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();
```

SDK-kodfragmentet som visas ovan motsvarar följande REST API begäran:

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

Varje sida för frågekörningen motsvarar en REST API `POST` med `Accept: application/query+json` rubriken och SQL-frågan i bröd texten. Varje fråga gör en eller flera turer till servern med en `x-ms-continuation` token som är ekoad mellan klienten och servern för att återuppta körningen. Konfigurations alternativen i FeedOptions skickas till servern i form av begärandehuvuden. Motsvarar till exempel `MaxItemCount` `x-ms-max-item-count` . 

Begäran returnerar följande (trunkerat för läsbarhet) svar:

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

De Key Response-huvuden som returneras från frågan inkluderar följande:

| Alternativ | Beskrivning |
| ------ | ----------- |
| `x-ms-item-count` | Antal objekt som returnerats i svaret. Detta är beroende av angivet `x-ms-max-item-count` , antalet objekt som kan anpassas inom den maximala nytto Last storleken för svar, det etablerade data flödet och frågans körnings tid. |  
| `x-ms-continuation:` | Fortsättnings-token för att återuppta körningen av frågan, om ytterligare resultat finns tillgängliga. | 
| `x-ms-documentdb-query-metrics` | Frågans statistik för körningen. Det här är en avgränsad sträng som innehåller statistik över tid som ägnats åt de olika faserna i frågekörningen. Returneras om `x-ms-documentdb-populatequerymetrics` är inställt på `True` . | 
| `x-ms-request-charge` | Antalet enheter för [programbegäran](request-units.md) som konsumeras av frågan. | 

Mer information om REST API begärandehuvuden och alternativ finns i [fråga resurser med hjälp av REST API](/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Metod tips för frågans prestanda
Följande är de vanligaste faktorer som påverkar Azure Cosmos DB frågans prestanda. Vi går djupare i vart och ett av de här avsnitten i den här artikeln.

| Faktor | Tips | 
| ------ | -----| 
| Etablerat dataflöde | Mät RU per fråga och se till att du har det nödvändiga etablerade data flödet för dina frågor. | 
| Partitionering och partitionerings nycklar | Prioritera frågor med värdet partition nyckel i filter satsen för låg latens. |
| SDK och frågealternativ | Följ metod tips för SDK som direkt anslutning och finjustera körnings alternativ för frågekörning på klient sidan. |
| Indexeringsprincip | Kontrol lera att du har de nödvändiga indexerings Sök vägarna/principen för frågan. |
| Fråga om körnings mått | Analysera frågans körnings mått för att identifiera eventuella omskrivningar av fråge-och data former.  |

### <a name="provisioned-throughput"></a>Etablerat dataflöde
I Cosmos DB skapar du behållare med data, var och en med reserverat data flöde som uttrycks i enheter för programbegäran (RU) per sekund. En läsning av ett dokument på 1 KB är 1 RU och varje åtgärd (inklusive frågor) normaliseras till ett fast antal ru: er baserat på dess komplexitet. Om du till exempel har 1000 RU/s etablerad för din behållare och du har en fråga som `SELECT * FROM c WHERE c.city = 'Seattle'` använder 5 ru: er, kan du utföra (1000 ru/s)/(5 ru/fråga) = 200 fråga/s sådana frågor per sekund. 

Om du skickar fler än 200 frågor per sekund börjar tjänsten att begränsa antalet inkommande begär anden över 200/s. SDK: erna hanterar det här ärendet automatiskt genom att utföra en backoff/omförsök, vilket kan innebära en högre svars tid för dessa frågor. Om du ökar det etablerade data flödet till det obligatoriska värdet förbättras svars tiden och data flödet. 

Mer information om enheter för programbegäran finns i [enheter för programbegäran](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Partitionering och partitionerings nycklar
Med Azure Cosmos DB utför vanliga frågor i följande ordning från snabbast/mest effektiv till långsammare/mindre effektiv. 

* Hämta en enda partitionsnyckel och en objekt nyckel
* Fråga med en filter-sats i en enskild partitionsnyckel
* Fråga utan en likhets-eller Range filter-sats på någon egenskap
* Fråga utan filter

Frågor som måste kontakta alla partitioner behöver högre latens och kan använda högre ru: er. Eftersom varje partition har automatisk indexering för alla egenskaper kan frågan hanteras effektivt från indexet i det här fallet. Du kan göra frågor som sträcker sig över partitioner snabbare genom att använda alternativen för parallellitet.

Mer information om partitionering och partitionerings nycklar finns i [partitionering i Azure Cosmos DB](partitioning-overview.md).

### <a name="sdk-and-query-options"></a>SDK och frågealternativ
Se [prestanda tips](performance-tips.md) och [prestanda testning](performance-testing.md) för att få bästa prestanda på klient sidan från Azure Cosmos dB. Detta inkluderar användning av de senaste SDK: erna, konfiguration av plattformsspecifika konfigurationer som standard antal anslutningar, frekvens för skräp insamling och användning av alternativ för Lightweight-anslutning som direkt/TCP. 


#### <a name="max-item-count"></a>Maximalt antal objekt
För frågor kan värdet för `MaxItemCount` ha en betydande inverkan på tiden från slut punkt till slut punkt. Varje Round-resa till servern returnerar högst antalet objekt i `MaxItemCount` (standard 100 objekt). Om du anger ett högre värde (-1 är maximalt och rekommenderat) förbättrar du varaktigheten för frågan genom att begränsa antalet fördröjningar mellan server och klient, särskilt för frågor med stora resultat uppsättningar.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Maximal grad av parallellitet
För frågor kan du justera `MaxDegreeOfParallelism` för att identifiera de bästa konfigurationerna för ditt program, särskilt om du utför frågor över olika partitioner (utan ett filter på partitionens nyckel värde). `MaxDegreeOfParallelism`  styr det maximala antalet parallella aktiviteter, d.v.s. det högsta antalet partitioner som ska besökas parallellt. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();
```

Vi antar att
* D = standard maximalt antal parallella aktiviteter (= totalt antal processorer på klient datorn)
* P = användardefinierat maximalt antal parallella uppgifter
* N = antal partitioner som måste besökas för att besvara en fråga

Nedan visas konsekvenserna av hur parallella frågor beter sig för olika värden för P.
* (P = = 0) => serie läge
* (P = = 1) => högst en aktivitet
* (P > 1) => min (P, N) parallell aktiviteter 
* (P < 1) => min (N, D) Parallel tasks

För SDK-versions information och information om implementerade klasser och metoder se [SQL SDK](sql-api-sdk-dotnet.md) : er

### <a name="network-latency"></a>Svarstid för nätverk
Se [Azure Cosmos DB global distribution](tutorial-global-distribution-sql-api.md) för att konfigurera global distribution och ansluta till den närmaste regionen. Nätverks fördröjningen har en betydande inverkan på frågornas prestanda när du behöver göra flera turer eller hämta en stor resultat uppsättning från frågan. 

I avsnittet om körnings statistik för frågekörning förklaras hur du hämtar Server körnings tiden för frågor ( `totalExecutionTimeInMs` ), så att du kan skilja mellan hur lång tid som ägnats åt att köra frågor och tids åtgång för nätverks överföring.

### <a name="indexing-policy"></a>Indexeringsprincip
Se [Konfigurera indexerings princip](index-policy.md) för indexering av sökvägar, typer och lägen och hur de påverkar frågekörningen. Som standard använder indexerings principen hash-indexering för strängar som är effektiva för likhets frågor, men inte för intervall frågor/order by-frågor. Om du behöver intervall frågor för strängar rekommenderar vi att du anger intervallets index typ för alla strängar. 

Som standard kommer Azure Cosmos DB att använda automatisk indexering för alla data. För scenarier med hög prestanda måste du överväga att utesluta sökvägar eftersom detta minskar RU-kostnaden för varje infognings åtgärd. 

## <a name="query-execution-metrics"></a>Fråga om körnings mått
Du kan få detaljerade Mät värden för frågekörningen genom att skicka i den valfria `x-ms-documentdb-populatequerymetrics` rubriken ( `FeedOptions.PopulateQueryMetrics` i .NET SDK). Värdet som returneras i `x-ms-documentdb-query-metrics` har följande nyckel/värde-par avsedda för avancerad fel sökning av frågekörningen. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;

```

| Metric | Enhet | Beskrivning | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | millisekunder | Körnings tid för fråga | 
| `queryCompileTimeInMs` | millisekunder | Tid för kompilering av fråga  | 
| `queryLogicalPlanBuildTimeInMs` | millisekunder | Tid för att bygga en logisk frågeplan | 
| `queryPhysicalPlanBuildTimeInMs` | millisekunder | Tid för att bygga en fysisk frågeplan | 
| `queryOptimizationTimeInMs` | millisekunder | Tids åtgång för att optimera frågan | 
| `VMExecutionTimeInMs` | millisekunder | Tid som ägnats åt fråge körning | 
| `indexLookupTimeInMs` | millisekunder | Tid som ägnats åt fysiskt index skikt | 
| `documentLoadTimeInMs` | millisekunder | Tids åtgång för att läsa in dokument  | 
| `systemFunctionExecuteTimeInMs` | millisekunder | Total tid som krävs för att köra system (inbyggda) funktioner i millisekunder  | 
| `userFunctionExecuteTimeInMs` | millisekunder | Total tid som krävs för att köra användardefinierade funktioner i millisekunder | 
| `retrievedDocumentCount` | count | Totalt antal hämtade dokument  | 
| `retrievedDocumentSize` | skickade | Total storlek på hämtade dokument i byte  | 
| `outputDocumentCount` | count | Antal utgående dokument | 
| `writeOutputTimeInMs` | millisekunder | Frågans körnings tid i millisekunder | 
| `indexUtilizationRatio` | förhållande (<= 1) | Förhållandet mellan antalet dokument som matchas med filtret till antalet inlästa dokument  | 

Klient-SDK: erna kan internt göra flera frågor för att betjäna frågan i varje partition. Klienten gör fler än ett anrop per partition om det totala antalet resultat överstiger `x-ms-max-item-count` det etablerade data flödet för partitionen, eller om frågans nytto Last når den maximala storleken per sida eller om frågan når den maximala tids gränsen för systemet. Varje partiell fråga som körs returnerar en `x-ms-documentdb-query-metrics` för den sidan. 

Här följer några exempel frågor och hur du tolkar några av de mått som returneras från frågekörningen: 

| Fråga | Exempel mått | Beskrivning | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Antalet dokument som hämtats är 100 + 1 för att matcha den översta satsen. Fråge tiden används främst i `WriteOutputTime` och `DocumentLoadTime` eftersom det är en genomsökning. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount är nu högre (500 + 1 för att matcha den övre satsen). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Cirka 0,9 MS används i IndexLookupTime för en nyckels ökning eftersom det är ett index uppslag på `/N/?` . | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Något mer gång (1,7 MS) som ägnats åt IndexLookupTime över en intervalls ökning, eftersom det är ett index uppslag på `/N/?` . | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Samma tid som har lagts på `DocumentLoadTime` som tidigare frågor, men lägre `WriteOutputTime` eftersom vi bara projicerar en egenskap. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Cirka 213 MS används för `UserDefinedFunctionExecutionTime` att köra UDF-filen på varje värde av `c.N` . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Cirka 0,6 MS har lagts `IndexLookupTime` på `/Name/?` . Merparten av frågans körnings tid (~ 7 MS) i `SystemFunctionExecutionTime` . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Frågan utförs som en sökning eftersom den använder `LOWER` och 500 av 2491 hämtade dokument returneras. |


## <a name="next-steps"></a>Nästa steg
* Information om de SQL-frågeparametrar och nyckelord som stöds finns i [SQL-fråga](sql-query-getting-started.md). 
* Information om enheter för programbegäran finns i [enheter för programbegäran](request-units.md).
* Läs mer om indexerings principen i [indexerings princip](index-policy.md)