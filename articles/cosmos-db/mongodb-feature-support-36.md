---
title: Azure Cosmos DB s API för MongoDB (3,6-version) som stöds av funktioner och syntax
description: Läs mer om Azure Cosmos DBs API för MongoDB (3,6-version) som stöds av funktioner och syntax.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 08/07/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 50414d48c3368ddf409630422d3316cdc45a63fe
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587403"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>Azure Cosmos DB:s API för MongoDB (version 3.6): Funktioner och syntax som stöds

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan kommunicera med Azure Cosmos DBens API för MongoDB med någon av MongoDB-klient [driv rutinerna](https://docs.mongodb.org/ecosystem/drivers)med öppen källkod. Azure Cosmos DB:s API för MongoDB tillåter gör det möjligt att använda befintliga klientdrivrutiner genom att följa MongoDB-[trådprotokollet](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Genom att använda Azure Cosmos DBs API för MongoDB kan du dra nytta av fördelarna med MongoDB som du använder för att, med alla Enterprise-funktioner som Cosmos DB ger: [global distribution](distribute-data-globally.md), [Automatisk horisontell partitionering](partition-data.md), tillgänglighet och latens garantier, kryptering i vila, säkerhets kopiering och mycket mer.

## <a name="protocol-support"></a>Protokollstöd

Azure Cosmos DBens API för MongoDB är kompatibelt med MongoDB Server version **3,6** som standard för nya konton. De operatorer som stöds och eventuella begränsningar eller undantag anges nedan. Klientdrivrutiner som förstår dessa protokoll bör kunna ansluta till Cosmos DB:s API för MongoDB. Observera att när du använder Azure Cosmos DB s API för MongoDB-konton har 3,6-versionen av konton slut punkten i formatet, `*.mongo.cosmos.azure.com` medan 3,2-versionen av kontona har slut punkten i formatet `*.documents.azure.com` .

## <a name="query-language-support"></a>Stöd för frågespråk

Azure Cosmos DB:s API för MongoDB erbjuder omfattande stöd för MongoDB-frågespråkskonstruktioner. Nedan hittar du en detaljerad lista över de åtgärder, operatorer, stadier, kommandon och alternativ som stöds för närvarande.

## <a name="database-commands"></a>Databaskommandon

Azure Cosmos DB:s API för MongoDB stöder följande databaskommandon:

### <a name="query-and-write-operation-commands"></a>Fråga och skriv-åtgärdskommandon

|Kommando  |Stöds |
|---------|---------|
|delete | Yes |
|find | Yes     |
|findAndModify | Yes  |
|getLastError|   Yes |
|getMore  |  Yes  |
|getPrevError | No  |
|insert  |   Yes  |
|parallelCollectionScan  | Yes   |
|resetError |    No  |
|update  |   Yes  |
|[Ändra dataströmmar](mongodb-change-streams.md)  |  Yes  |
|GridFS |   Yes  |

### <a name="authentication-commands"></a>Autentiseringskommandon

|Kommando  |Stöds |
|---------|---------|
|authenticate    |   Yes      |
|logout    |      Yes   |
|getnonce   |    Yes     |


### <a name="administration-commands"></a>Administrationskommandon

|Kommando  |Stöds |
|---------|---------|
|Tak samlingar   |   No      |
|cloneCollectionAsCapped     |   No      |
|collMod     |   No      |
|collMod: expireAfterSeconds   |   No      |
|convertToCapped   |  No       |
|copydb     |  No       |
|skapa   |    Yes     |
|createIndexes     |  Yes       |
|currentOp     |  Yes       |
|drop     |   Yes      |
|dropDatabase     |  Yes       |
|dropIndexes     |   Yes      |
|filemd5    |   Yes      |
|killCursors    |  Yes       |
|killOp     |   No      |
|listCollections     |  Yes       |
|listDatabases     |  Yes       |
|listIndexes     |  Yes       |
|reIndex     |    Yes     |
|renameCollection     |    No     |
|connectionStatus    |     No    |

### <a name="diagnostics-commands"></a>Diagnostiska kommandon

|Kommando  |Stöds |
|---------|---------|
|buildInfo         |   Yes      |
|collStats    |  Yes       |
|connPoolStats     |  No       |
|connectionStatus     |  No       |
|dataSize     |   No      |
|dbHash    |    No     |
|dbStats     |   Yes      |
|Vad     |   Yes      |
|förklaring: executionStats     |   Yes      |
|funktioner     |    No     |
|hostInfo     |   No      |
|listDatabases         |   Yes      |
|listCommands     |  No       |
|profiler     |  No       |
|serverStatus     |  No       |
|top     |    No     |
|whatsmyuri     |   Yes      |

<a name="aggregation-pipeline"></a>

## <a name="aggregation-pipelinea"></a>Sammansättningspipeline</a>

### <a name="aggregation-commands"></a>Sammansättningskommandon

|Kommando  |Stöds |
|---------|---------|
|aggregate |   Yes  |
|antal     |   Yes  |
|distinct  | Yes |
|mapReduce | No |

### <a name="aggregation-stages"></a>Sammansättningsfaser

|Kommando  |Stöds |
|---------|---------|
|$collStats    |No|
|$project    |Yes|
|$match    |Yes|
|$redact|    Yes|
|$limit    |Yes|
|$skip    |Yes|
|$unwind|    Yes|
|$group    |    Yes|
|$sample|        Yes|
|$sort    |Yes|
|$geoNear|    No|
|$lookup    |    Yes|
|$out        |Yes|
|$indexStats|        No|
|$facet    |Yes|
|$bucket|    No|
|$bucketAuto|    No|
|$sortByCount|    Yes|
|$addFields    |Yes|
|$replaceRoot|    Yes|
|$count    |Yes|
|$currentOp|    No|
|$listLocalSessions    |No|
|$listSessions    |No|
|$graphLookup    |No|

### <a name="boolean-expressions"></a>Booleska uttryck

|Kommando  |Stöds |
|---------|---------|
|$and| Yes|
|$or|Yes|
|$not|Yes|

### <a name="set-expressions"></a>Uttryck för angivelse

|Kommando  |Stöds |
|---------|---------|
| $setEquals | Yes|
|$setIntersection|Yes|
| $setUnion|Yes|
| $setDifference|Yes|
| $setIsSubset|Yes|
| $anyElementTrue|Yes|
| $allElementsTrue|Yes|

### <a name="comparison-expressions"></a>Jämförelseuttryck

|Kommando  |Stöds |
|---------|---------|
|$cmp     |  Yes       |
|$eq|    Yes| 
|$gt |    Yes| 
|$gte|    Yes| 
|$lt    |Yes|
|$lte|    Yes| 
|$ne    |    Yes| 
|$in    |    Yes| 
|$nin    |    Yes| 

### <a name="arithmetic-expressions"></a>Aritmetiska uttryck

|Kommando  |Stöds |
|---------|---------|
|$abs |  Yes       |
| $add |  Yes       |
| $ceil |  Yes       |
| $divide |  Yes       |
| $exp |  Yes       |
| $floor |  Yes       |
| $ln |  Yes       |
| $log |  Yes       |
| $log10 |  Yes       |
| $mod |  Yes       |
| $multiply |  Yes       |
| $pow |  Yes       |
| $sqrt |  Yes       |
| $subtract |  Yes       |
| $trunc |  Yes       |

### <a name="string-expressions"></a>Stränguttryck

|Kommando  |Stöds |
|---------|---------|
|$concat |  Yes       |
| $indexOfBytes|  Yes       |
| $indexOfCP|  Yes       |
| $split|  Yes       |
| $strLenBytes|  Yes       |
| $strLenCP|  Yes       |
| $strcasecmp|  Yes       |
| $substr|  Yes       |
| $substrBytes|  Yes       |
| $substrCP|  Yes       |
| $toLower|  Yes       |
| $toUpper|  Yes       |

### <a name="text-search-operator"></a>Text Sök operator

|Kommando  |Stöds |
|---------|---------|
| $meta | No|

### <a name="array-expressions"></a>Matrisuttryck

|Kommando  |Stöds |
|---------|---------|
|$arrayElemAt    |    Yes|
|$arrayToObject|    Yes|
|$concatArrays    |    Yes|
|$filter    |    Yes|
|$indexOfArray    |Yes|
|$isArray    |    Yes|
|$objectToArray    |Yes|
|$range    |Yes|
|$reverseArray    |    Yes|
|$reduce|    Yes|
|$size    |    Yes|
|$slice    |    Yes|
|$zip    |    Yes|
|$in    |    Yes|

### <a name="variable-operators"></a>Variabel operatörer

|Kommando  |Stöds |
|---------|---------|
|$map    |No|
|$let    |Yes|

### <a name="system-variables"></a>Systemvariabler

|Kommando  |Stöds |
|---------|---------|
|$ $CURRENT|    Yes|
|$ $DESCEND|        Yes|
|$ $KEEP        |Yes|
|$ $PRUNE    |    Yes|
|$ $REMOVE    |Yes|
|$ $ROOT        |Yes|

### <a name="literal-operator"></a>Litteral operator

|Kommando  |Stöds |
|---------|---------|
|$literal    |Yes|

### <a name="date-expressions"></a>Datumuttryck

|Kommando  |Stöds |
|---------|---------|
|$dayOfYear    |Yes    |
|$dayOfMonth|    Yes    |
|$dayOfWeek    |Yes    |
|$year    |Yes    |
|$month    |Yes|    
|$week    |Yes    |
|$hour    |Yes    |
|$minute|    Yes|    
|$second    |Yes    |
|$millisecond|    Yes|    
|$dateToString    |Yes    |
|$isoDayOfWeek    |Yes    |
|$isoWeek    |Yes    |
|$dateFromParts|    No|    
|$dateToParts    |No    |
|$dateFromString|    No|
|$isoWeekYear    |Yes    |

### <a name="conditional-expressions"></a>Villkorliga uttryck

|Kommando  |Stöds |
|---------|---------|
| $cond| Yes|
| $ifNull| Yes|
| $switch |Yes|

### <a name="data-type-operator"></a>Data typs operator

|Kommando  |Stöds |
|---------|---------|
| $type| Yes|

### <a name="accumulator-expressions"></a>Ackumulator uttryck

|Kommando  |Stöds |
|---------|---------|
|$sum    |Yes    |
|$avg    |Yes    |
|$first|    Yes|
|$last    |Yes    |
|$max    |Yes    |
|$min    |Yes    |
|$push|    Yes|
|$addToSet|    Yes|
|$stdDevPop|    No    |
|$stdDevSamp|    No|

### <a name="merge-operator"></a>Slå samman operatör

|Kommando  |Stöds |
|---------|---------|
| $mergeObjects | Yes|

## <a name="data-types"></a>Datatyper

|Kommando  |Stöds |
|---------|---------|
|Double    |Yes    |
|Sträng    |Yes    |
|Objekt    |Yes    |
|Matris    |Yes    |
|Binära data    |Yes|    
|ObjectId    |Yes    |
|Boolesk    |Yes    |
|Datum    |Yes    |
|Null    |Yes    |
|32-bitars heltal (int)    |Yes    |
|Timestamp    |Yes    |
|64-bitars heltal (långt)    |Yes    |
|MinKey    |Yes    |
|MaxKey    |Yes    |
|Decimal128    |Yes|    
|Reguljärt uttryck    |Yes|
|JavaScript    |Yes|
|Java Script (med omfång)|    Yes    |
|Undefined (Odefinierad)    |Yes    |

## <a name="indexes-and-index-properties"></a>Index och index egenskaper

### <a name="indexes"></a>Index

|Kommando  |Stöds |
|---------|---------|
|Index för enskilt fält    |Yes    |
|Sammansatt index    |Yes    |
|MultiKey-index    |Yes    |
|Text index    |No|
|2dsphere    |Yes    |
|2D-index    |No    |
|Hashed index    | Yes|

### <a name="index-properties"></a>Index egenskaper

|Kommando  |Stöds |
|---------|---------|
|TTL-värde|    Yes    |
|Unik    |Yes|
|Delvis|    No|
|Skift läges okänslig    |No|
|Utspridda    |No |
|Bakgrund|    Yes |

## <a name="operators"></a>Operatorer

### <a name="logical-operators"></a>Logiska operatorer

|Kommando  |Stöds |
|---------|---------|
|$or    |    Yes|
|$and    |    Yes|
|$not    |    Yes|
|$nor    |    Yes| 

### <a name="element-operators"></a>Element operatörer

|Kommando  |Stöds |
|---------|---------|
|$exists|    Yes|
|$type    |    Yes|

### <a name="evaluation-query-operators"></a>Operatorer för utvärderings fråga

|Kommando  |Stöds |
|---------|---------|
|$expr    |    No|
|$jsonSchema    |    No|
|$mod    |    Yes|
|$regex |    Yes|
|$text    | Nej (stöds inte. Använd $regex i stället.)| 
|$where    |No| 

I $regex-frågor tillåter vänster-förankrade uttryck indexs ökning. Om du använder modifierarna i (skiftlägesokänsligt) och m (flera rader) så får du dock samlingsskanningen i alla uttryck.

När det finns ett behov att inkludera $ eller | så är det bäst att skapa två (eller flera) regex-frågor. Till exempel, med följande ursprungliga fråga: ```find({x:{$regex: /^abc$/})``` , måste ändras på följande sätt:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.

Den första delen använder indexet för att begränsa sökningen till dokument som börjar med ^abc och den andra delen matchar de exakta posterna. Fält-operatorn | fungerar som en or-funktion – frågan ```find({x:{$regex: /^abc|^def/})``` matchar dokumenten där fältet x har värden som börjar med abc eller def. Om du vill använda indexet så rekommenderar vi att du delar upp frågan i två olika frågor anslutna med operatorn $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="array-operators"></a>Mat ris operatorer

|Kommando  |Stöds | 
|---------|---------|
| $all | Yes| 
| $elemMatch | Yes| 
| $size | Yes | 

### <a name="comment-operator"></a>Kommentars operator

|Kommando  |Stöds | 
|---------|---------|
$comment |Yes| 

### <a name="projection-operators"></a>Projektions operatörer

|Kommando  |Stöds |
|---------|---------|
|$elemMatch    |Yes|
|$meta|    No|
|$slice    | Yes|

### <a name="update-operators"></a>Uppdateringsoperatorer

#### <a name="field-update-operators"></a>Fältuppdateringsoperatorer

|Kommando  |Stöds |
|---------|---------|
|$inc    |    Yes|
|$mul    |    Yes|
|$rename    |    Yes|
|$setOnInsert|    Yes|
|$set    |Yes|
|$unset| Yes|
|$min    |Yes|
|$max    |Yes|
|$currentDate    | Yes|

#### <a name="array-update-operators"></a>Matrisuppdateringsoperatorer

|Kommando  |Stöds |
|---------|---------|
|$    |Yes|
|$[]|    Yes|
|$[<identifier>]|    Yes|
|$addToSet    |Yes|
|$pop    |Yes|
|$pullAll|    Yes|
|$pull    |Yes|
|$push    |Yes|
|$pushAll| Yes|


#### <a name="update-modifiers"></a>Uppdatera modifierare

|Kommando  |Stöds |
|---------|---------|
|$each    |    Yes|
|$slice    |Yes|
|$sort    |Yes|
|$position    |Yes|

#### <a name="bitwise-update-operator"></a>Binär uppdateringsoperator

|Kommando  |Stöds |
|---------|---------|
| $bit    |    Yes|    
|$bitsAllSet    |    No|
|$bitsAnySet    |    No|
|$bitsAllClear    |No|
|$bitsAnyClear    |No|

### <a name="geospatial-operators"></a>Geospatiala operatorer

Operator | Stöds| 
--- | --- |
$geoWithin | Yes |
$geoIntersects | Yes | 
$near |  Yes |
$nearSphere |  Yes |
$geometry |  Yes |
$minDistance | Yes |
$maxDistance | Yes |
$center | No |
$centerSphere | No |
$box | No |
$polygon |  No |

## <a name="cursor-methods"></a>Markör-metoder

|Kommando  |Stöds |
|---------|---------|
|cursor.batchSize ()    |    Yes|
|cursor. Close ()    |Yes|
|cursor. isClosed ()|        Yes|
|cursor. COLLATE ()|    No|
|cursor. comment ()    |Yes|
|cursor. Count ()    |Yes|
|cursor. förklaring ()|    No|
|cursor. ()    |Yes|
|cursor. hasNext ()    |Yes|
|cursor. ledtråd ()    |Yes|
|cursor. isExhausted ()|    Yes|
|cursor. itcount ()    |Yes|
|cursor. Limit ()    |Yes|
|cursor. map ()    |Yes|
|cursor. maxScan ()    |Yes|
|cursor. maxTimeMS ()|    Yes|
|markör. Max ()    |Yes|
|cursor. min ()    |Yes|
|markör. Next ()|    Yes|
|cursor. noCursorTimeout ()    |No|
|cursor. objsLeftInBatch ()    |Yes|
|cursor. Pretty ()|    Yes|
|cursor. readConcern ()|    Yes|
|cursor. readPref ()        |Yes|
|markör. renyckels ()    |No|
|cursor. showRecordId ()|    No|
|cursor. size ()    |Yes|
|cursor. SKIP ()    |Yes|
|cursor.sort()    |    Yes|
|cursor. ensidigt ()|    No|
|cursor. toArray ()    |Yes|

## <a name="sort-operations"></a>Sorterings åtgärder

Med åtgärden `findOneAndUpdate` kan du använda sorteringsåtgärder på ett enda fält, men inte på flera fält.

## <a name="unique-indexes"></a>Unika index

[Unika index](mongodb-indexing.md#unique-indexes) säkerställer att ett särskilt fält inte har dubblettvärden i alla dokument i en samling, på liknande sätt som unika bevaras i standard nyckeln "_ID". Du kan skapa unika index i Cosmos DB genom att använda `createIndex` kommandot med `unique` begränsnings parametern:

```javascript
globaldb:PRIMARY> db.coll.createIndex( { "amount" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

## <a name="compound-indexes"></a>Sammansatta index

[Sammansatta index](mongodb-indexing.md#compound-indexes-mongodb-server-version-36) ger ett sätt att skapa ett index för fält grupper för upp till åtta fält. Den här typen av index skiljer sig från de interna MongoDB-sammansatta indexen. I Azure Cosmos DB används sammansatta index för sorterings åtgärder som tillämpas på flera fält. Om du vill skapa ett sammansatt index måste du ange mer än en egenskap som parameter:

```javascript
globaldb:PRIMARY> db.coll.createIndex({"amount": 1, "other":1})
{
        "createdCollectionAutomatically" : false, 
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 2,
        "ok" : 1
}
```

## <a name="time-to-live-ttl"></a>TTL-värde (time to live)

Cosmos DB stöder Time to Live (TTL) baserat på dokumentets tidsstämpel. Du kan aktivera TTL för samlingar genom att gå till [Azure Portal](https://portal.azure.com).

## <a name="user-and-role-management"></a>Användar- och rollhantering

Cosmos DB stöder ännu inte användare och roller. Cosmos DB stöder dock rollbaserad åtkomst kontroll (RBAC) och skriv-och skriv skydds-och skrivskyddade lösen ord/nycklar som kan hämtas via [Azure Portal](https://portal.azure.com) (anslutnings sträng).

## <a name="replication"></a>Replikering

Cosmos DB stöder automatisk, inbyggd replikering på de understa lagren. Denna logik utökas för att även uppnå global replikering med låga svarstider. Cosmos DB stöder inte manuella replikeringskommandon.

## <a name="write-concern"></a>Skrivanmärkningar

Vissa program förlitar sig på en [Write Concern](https://docs.mongodb.com/manual/reference/write-concern/) (skrivanmärkning) som anger det antal svar som krävs under en skrivåtgärd. På grund av hur Cosmos DB hanterar replikering i bakgrunden är alla skrivningar automatiskt kvorumskrivningar som standard. Skrivanmärkningar som anges av klientkoden ignoreras. Läs mer om hur du [maximerar tillgänglighet och prestanda med hjälp av konsekvensnivåer](consistency-levels.md).

## <a name="sharding"></a>Horisontell partitionering

Azure Cosmos DB stöder automatisk, horisontell partitionering på serversidan. Den hanterar Shard skapande, placering och balansering automatiskt. Azure Cosmos DB stöder inte manuella horisontell partitionering-kommandon, vilket innebär att du inte behöver anropa kommandon som addShard, balancerStart, moveChunk osv. Du behöver bara ange nyckeln Shard när du skapar behållarna eller frågar efter data.

## <a name="sessions"></a>Sessioner

Azure Cosmos DB har ännu inte stöd för kommandon på Server sidans sessioner.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Mongo 3,6 versions funktioner](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.

<sup>OBS! den här artikeln beskriver en funktion i Azure Cosmos DB som ger till gång till Wire Protocol-kompatibilitet med MongoDB-databaser. Microsoft kör inte MongoDB-databaser för att tillhandahålla den här tjänsten. Azure Cosmos DB är inte kopplad till MongoDB, Inc.</sup>
