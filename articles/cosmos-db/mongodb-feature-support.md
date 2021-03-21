---
title: Azure Cosmos DB s API för MongoDB (3,2-version) som stöds av funktioner och syntax
description: Läs mer om Azure Cosmos DBs API för MongoDB (3,2-version) som stöds av funktioner och syntax.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/16/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: ce6b01a99c29020b0ede5a860da9d3d6f37e9f06
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104605757"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-32-version-supported-features-and-syntax"></a>Azure Cosmos DB:s API för MongoDB (version 3.2): Funktioner och syntax som stöds
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan kommunicera med Azure Cosmos DBens API för MongoDB med någon av MongoDB-klient [driv rutinerna](https://docs.mongodb.org/ecosystem/drivers)med öppen källkod. Azure Cosmos DB:s API för MongoDB tillåter gör det möjligt att använda befintliga klientdrivrutiner genom att följa MongoDB-[trådprotokollet](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Med hjälp av Azure Cosmos DB:s API för MongoDB kan du dra nytta av fördelarna med den MongoDB som du är van vid och alla de företagsfunktioner som Azure Cosmos DB erbjuder: [global distribution](distribute-data-globally.md), [automatisk horisontell positionering](partitioning-overview.md), garantier avseende tillgänglighet och svarstid, automatisk indexering av varje fält, kryptering i vila, säkerhetskopior och mycket mer.

> [!NOTE]
> Version 3,2 av Cosmos DB API för MongoDB har inga aktuella planer för livs längd (EOL). Det minsta meddelandet för en framtida EOL är tre år.

## <a name="protocol-support"></a>Protokollstöd

Alla nya konton för Azure Cosmos DBs API för MongoDB är kompatibla med MongoDB Server version **3,6**. Den här artikeln beskriver MongoDB version 3.2. De operatorer som stöds och eventuella begränsningar eller undantag anges nedan. Klientdrivrutiner som förstår dessa protokoll bör kunna ansluta till Cosmos DB:s API för MongoDB. 

Azure Cosmos DBs API för MongoDB erbjuder också en sömlös uppgraderings upplevelse för kvalificerings konton. Läs mer i [uppgraderings hand boken för MongoDB-versionen](mongodb-version-upgrade.md).

## <a name="query-language-support"></a>Stöd för frågespråk

Azure Cosmos DB:s API för MongoDB erbjuder omfattande stöd för MongoDB-frågespråkskonstruktioner. Nedan hittar du en detaljerad lista över de åtgärder, operatorer, stadier, kommandon och alternativ som stöds för närvarande.

## <a name="database-commands"></a>Databaskommandon

Azure Cosmos DB:s API för MongoDB stöder följande databaskommandon:

> [!NOTE]
> Den här artikeln listar de Server kommandon som stöds och omfattar inte funktioner på klient sidan. Klient sidans wrapper-funktioner som `deleteMany()` och `updateMany()` internt använder-och-Server- `delete()` `update()` kommandon. Funktioner som använder Server kommandon som stöds är kompatibla med Azure Cosmos DB s API för MongoDB.

### <a name="query-and-write-operation-commands"></a>Fråga och skriv-åtgärdskommandon

- delete
- find
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>Autentiseringskommandon

- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>Administrationskommandon

- dropDatabase
- listCollections
- drop
- skapa
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Diagnostiska kommandon

- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"></a>

## <a name="aggregation-pipelinea"></a>Sammansättningspipeline</a>

Cosmos DB stöder en agg regerings pipeline för MongoDB 3,2 i offentlig för hands version. Se [Azure-bloggen](https://azure.microsoft.com/blog/azure-cosmosdb-extends-support-for-mongodb-aggregation-pipeline-unique-indexes-and-more/) för anvisningar om hur du registrerar för den allmänna förhandsversionen.

### <a name="aggregation-commands"></a>Sammansättningskommandon

- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>Sammansättningsfaser

- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>Sammansättningsuttryck

#### <a name="boolean-expressions"></a>Booleska uttryck

- $and
- $or
- $not

#### <a name="set-expressions"></a>Uttryck för angivelse

- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Jämförelseuttryck

- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Aritmetiska uttryck

- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>Stränguttryck

- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Matrisuttryck

- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>Datumuttryck

- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Villkorliga uttryck

- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Sammansättningsackumulatorer

- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operatorer

Följande operatorer stöds med motsvarande användningsexempel. Överväg det här exempeldokumentet som används i frågorna nedan:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

| Operator | Exempel |
| --- | --- |
| $eq | `{ "Volcano Name": { $eq: "Rainier" } }` |
| $gt | `{ "Elevation": { $gt: 4000 } }` | 
| $gte | `{ "Elevation": { $gte: 4392 } }` | 
| $lt | `{ "Elevation": { $lt: 5000 } }` | 
| $lte | `{ "Elevation": { $lte: 5000 } }` | 
| $ne | `{ "Elevation": { $ne: 1 } }` | 
| $in | `{ "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } }` |
| $nin | `{ "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } }` |
| $or | `{ $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] }` | 
| $and | `{ $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |
| $not | `{ "Elevation": { $not: { $gt: 5000 } } }`| 
| $nor | `{ $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] }` |
| $exists | `{ "Status": { $exists: true } }`|
| $type | `{ "Status": { $type: "string" } }`| 
| $mod | `{ "Elevation": { $mod: [ 4, 0 ] } }` | 
| $regex | `{ "Volcano Name": { $regex: "^Rain"} }`| 

### <a name="notes"></a>Kommentarer

I $regex-frågor, tillåter vänsterförankrade uttryck indexsökning. Om du använder modifierarna i (skiftlägesokänsligt) och m (flera rader) så får du dock samlingsskanningen i alla uttryck.
När det finns ett behov att inkludera $ eller | så är det bäst att skapa två (eller flera) regex-frågor.
Med till exempel följande ursprungliga fråga: ```find({x:{$regex: /^abc$/})``` så måste den ändras på följande sätt: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
Den första delen använder indexet för att begränsa sökningen till dokument som börjar med ^abc och den andra delen matchar de exakta posterna.
Fält-operatorn | fungerar som en or-funktion – frågan ```find({x:{$regex: /^abc|^def/})``` matchar dokumenten där fältet x har värden som börjar med abc eller def. Om du vill använda indexet så rekommenderar vi att du delar upp frågan i två olika frågor anslutna med operatorn $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="update-operators"></a>Uppdateringsoperatorer

#### <a name="field-update-operators"></a>Fältuppdateringsoperatorer

- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>Matrisuppdateringsoperatorer

- $addToSet
- $pop
- $pullAll
- $pull (Obs: $pull med villkor stöds inte)
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>Binär uppdateringsoperator

- $bit

### <a name="geospatial-operators"></a>Geospatiala operatorer

Operator | Exempel | Stöds |
--- | --- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Yes |
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes |
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes |
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Yes |
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes |
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Yes |
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Yes |
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Yes |
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Yes |
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Yes |
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes |

## <a name="sort-operations"></a>Sortera åtgärder

Med åtgärden `findOneAndUpdate` kan du använda sorteringsåtgärder på ett enda fält, men inte på flera fält.

## <a name="other-operators"></a>Andra operatörer

Operator | Exempel | Anteckningar
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` |
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |
$size | ```{ "Location.coordinates": { $size: 2 } }``` |
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` |
$text |  | Stöds inte. Använd $regex i stället.

## <a name="unsupported-operators"></a>Operatorer som inte stöds

Operatorerna ```$where``` och ```$eval``` stöds inte av Azure Cosmos DB.

### <a name="methods"></a>Metoder

Följande metoder stöds:

#### <a name="cursor-methods"></a>Markör-metoder

Metod | Exempel | Anteckningar
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | Dokument utan sorteringsnyckel returneras inte

## <a name="unique-indexes"></a>Unika index

Cosmos DB indexerar som standard varje fält i dokument som skrivs till databasen. Unika index säkerställer att ett särskilt fält inte har dubblettvärden i alla dokument i en samling, på liknande sätt som unika bevaras på standard `_id` nyckeln. Du kan skapa anpassade index i Cosmos DB med hjälp av kommandot createIndex, inklusive begränsningen "Unique".

Unika index är tillgängliga för alla Cosmos-konton med hjälp av Azure Cosmos DB:s API för MongoDB.

## <a name="time-to-live-ttl"></a>TTL-värde (time to live)

Cosmos DB stöder Time to Live (TTL) baserat på dokumentets tidsstämpel. Du kan aktivera TTL för samlingar genom att gå till [Azure Portal](https://portal.azure.com).

## <a name="user-and-role-management"></a>Användar- och rollhantering

Cosmos DB stöder ännu inte användare och roller. Cosmos DB stöder dock rollbaserad åtkomst kontroll i Azure (Azure RBAC) och skriv-och skriv skydds-och skrivskyddade lösen ord/nycklar som kan hämtas via [Azure Portal](https://portal.azure.com) (anslutnings sträng).

## <a name="replication"></a>Replikering

Cosmos DB stöder automatisk, inbyggd replikering på de understa lagren. Denna logik utökas för att även uppnå global replikering med låga svarstider. Cosmos DB stöder inte manuella replikeringskommandon.

## <a name="write-concern"></a>Skrivanmärkningar

Vissa program är beroende av en [Skriv](https://docs.mongodb.com/manual/reference/write-concern/) åtgärd som anger antalet svar som krävs under en Skriv åtgärd. På grund av hur Cosmos DB hanterar replikering i bakgrunden är alla skrivningar automatiskt kvorumskrivningar som standard. Skrivanmärkningar som anges av klientkoden ignoreras. Läs mer om hur du [maximerar tillgänglighet och prestanda med hjälp av konsekvensnivåer](consistency-levels.md).

## <a name="sharding"></a>Horisontell partitionering

Azure Cosmos DB stöder automatisk, horisontell partitionering på serversidan. Den hanterar Shard skapande, placering och balansering automatiskt. Azure Cosmos DB stöder inte manuella horisontell partitionering-kommandon, vilket innebär att du inte behöver anropa kommandon som shardCollection, addShard, balancerStart, moveChunk osv. Du behöver bara ange nyckeln Shard när du skapar behållarna eller frågar efter data.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.
