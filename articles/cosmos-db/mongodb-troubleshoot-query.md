---
title: Felsök problem med frågor när du använder Azure Cosmos DB API för MongoDB
description: Lär dig att identifiera, diagnostisera och felsöka Azure Cosmos DBs API för frågor om MongoDB-frågor.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.subservice: cosmosdb-mongo
ms.date: 03/02/2021
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 5302cb7bb3f4683d200f6f9ea106991bb934fc17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101659910"
---
# <a name="troubleshoot-query-issues-when-using-the-azure-cosmos-db-api-for-mongodb"></a>Felsök problem med frågor när du använder Azure Cosmos DB API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Den här artikeln vägleder dig genom en allmänt rekommenderad metod för fel sökning av frågor i Azure Cosmos DB. Även om du inte bör tänka på de steg som beskrivs i den här artikeln får du ett fullständigt försvar mot potentiella frågor, men vi har inkluderat de vanligaste prestanda tipsen här. Du bör använda den här artikeln som en start plats för fel sökning av långsamma eller kostsamma frågor i Azure Cosmos DBs API för MongoDB. Om du använder API: et för Azure Cosmos DB Core (SQL) läser du artikeln om [fel söknings guiden för SQL API-frågor](troubleshoot-query-performance.md) .

Optimeringar av frågor i Azure Cosmos DB är i stort sett kategoriserade enligt följande:

- Optimeringar som minskar antalet begär ande enheter (RU) för frågan
- Optimeringar som bara minskar svars tiden

Om du minskar avgiften för en fråga så minskar du normalt svars tiden.

Den här artikeln innehåller exempel som du kan återskapa med hjälp av [närings data uppsättningen](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

> [!NOTE] 
> Den här artikeln förutsätter att du använder Azure Cosmos DBs API för MongoDB-konton med version 3,6 och högre. Vissa frågor som fungerar dåligt i version 3,2 har betydande förbättringar i versionerna 3.6 +. Uppgradera till version 3,6 genom att skicka en [support förfrågan](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="use-explain-command-to-get-metrics"></a>Använd $explain-kommandot för att hämta mått

När du optimerar en fråga i Azure Cosmos DB, är det första steget alltid att [Hämta ru-avgiften](find-request-unit-charge-mongodb.md) för din fråga. Som en grov rikt linje bör du utforska olika sätt att sänka avgiften för RU för frågor med kostnader som är större än 50 ru: er. 

Förutom att erhålla avgiften för RU bör du använda `$explain` kommandot för att hämta användnings statistik för frågor och index. Här är ett exempel som kör en fråga och använder `$explain` kommandot för att Visa användnings statistik för frågor och index:

**$explain kommando:**

```
db.coll.find({foodGroup: "Baby Foods"}).explain({"executionStatistics": true })
```

**Utdataparametrar**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 905.2888,
            "timeExclusiveMS" : 905.2888,
            "in" : 362,
            "out" : 362,
            "details" : {
                "database" : "db-test",
                "collection" : "collection-test",
                "query" : {
                    "foodGroup" : {
                        "$eq" : "Baby Foods"
                    }
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "e68e6bdd-5e89-4ec5-b053-3dbbc2428140",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 788.5867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 362,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 362,
                    "outputDocumentSizeBytes" : 2553535,
                    "indexHitRatio" : 0.0016802042237178,
                    "totalQueryExecutionTimeMS" : 777.72,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.19,
                        "logicalPlanBuildTimeMS" : 0.14,
                        "physicalPlanBuildTimeMS" : 0.09,
                        "queryOptimizationTimeMS" : 0.03
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 687.22,
                    "vmExecutionTimeMS" : 774.09,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 37.45,
                        "systemFunctionExecutionTimeMS" : 10.82,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 49.42
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

`$explain`Kommandots utdata är långa och innehåller detaljerad information om frågekörningen. I allmänhet finns det några avsnitt där du bör fokusera på när du optimerar frågans prestanda:

| Metric | Beskrivning | 
| ------ | ----------- |
| `timeInclusiveMS` | Svars tid för backend-fråga |
| `pathsIndexed` | Visar index som används av frågan | 
| `pathsNotIndexed` | Visar index som frågan kan ha använt, om den är tillgänglig | 
| `shardInformation` | Översikt över frågans prestanda för en viss [fysisk partition](./partitioning-overview.md#physical-partitions) | 
| `retrievedDocumentCount` | Antal dokument som lästs in av frågemotor | 
| `outputDocumentCount` | Antal dokument som returneras i frågeresultatet | 
| `estimatedDelayFromRateLimitingInMilliseconds` | Beräknad ytterligare svars tid på grund av hastighets begränsning | 

När du har hämtat frågeresultaten jämför du `retrievedDocumentCount` med `outputDocumentCount` för frågan. Använd den här jämförelsen för att identifiera relevanta avsnitt som ska granskas i den här artikeln. `retrievedDocumentCount`Är antalet dokument som frågemotor behöver läsa in. `outputDocumentCount`Är antalet dokument som behövs för frågans resultat. Om `retrievedDocumentCount`  är betydligt högre än `outputDocumentCount` , fanns det minst en del av din fråga som inte kunde använda ett index och som behövdes för att göra en genomsökning.

Se följande avsnitt för att förstå relevanta optimeringar av frågor för ditt scenario.

### <a name="querys-ru-charge-is-too-high"></a>Frågans RU-avgift är för hög

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Antalet hämtade dokument är betydligt högre än antalet utgående dokument

- [Inkludera nödvändiga index.](#include-necessary-indexes)

- [Förstå vilka agg regerings åtgärder som använder indexet.](#understand-which-aggregation-operations-use-the-index)

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Antalet hämtade dokument är ungefär lika med antalet utgående dokument

- [Minimera kors partitions frågor.](#minimize-cross-partition-queries)

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>Frågans RU-avgift är acceptabel, men fördröjningen är fortfarande för hög

- [Förbättra närhet.](#improve-proximity)

- [Öka det etablerade data flödet.](#increase-provisioned-throughput)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Frågor där antal hämtade dokument överskrider antalet utgående dokument

 `retrievedDocumentCount`Är antalet dokument som frågemotor behöver läsa in. `outputDocumentCount`Är antalet dokument som returneras av frågan. Om `retrievedDocumentCount` är betydligt högre än `outputDocumentCount` , fanns det minst en del av din fråga som inte kunde använda ett index och som behövdes för att göra en genomsökning.

Här är ett exempel på en genomsöknings fråga som inte helt hanterades av indexet:

**$explain kommando:**

```
db.coll.find(
  {
    $and : [
            { "foodGroup" : "Cereal Grains and Pasta"}, 
            { "description" : "Oat bran, cooked"}
        ]
  }
).explain({"executionStatistics": true })
```

**Utdataparametrar**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 436.5716,
            "timeExclusiveMS" : 436.5716,
            "in" : 1,
            "out" : 1,
            "details" : {
                "database" : "db-test",
                "collection" : "indexing-test",
                "query" : {
                    "$and" : [ 
                        {
                            "foodGroup" : {
                                "$eq" : "Cereal Grains and Pasta"
                            }
                        }, 
                        {
                            "description" : {
                                "$eq" : "Oat bran, cooked"
                            }
                        }
                    ]
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "13a5977e-a10a-4329-b68e-87e4f0081cac",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 435.4867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 1,
                    "outputDocumentSizeBytes" : 6064,
                    "indexHitRatio" : 0.0,
                    "totalQueryExecutionTimeMS" : 433.64,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.12,
                        "logicalPlanBuildTimeMS" : 0.09,
                        "physicalPlanBuildTimeMS" : 0.1,
                        "queryOptimizationTimeMS" : 0.02
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 387.44,
                    "vmExecutionTimeMS" : 432.93,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 45.36,
                        "systemFunctionExecutionTimeMS" : 16.86,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 0.13
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

`retrievedDocumentCount`(8618) är betydligt högre än `outputDocumentCount` (1), vilket innebär att frågan krävde en dokuments ökning. 

### <a name="include-necessary-indexes"></a>Inkludera nödvändiga index

Du bör kontrol lera `pathsNotIndexed` matrisen och lägga till dessa index. I det här exemplet ska Sök vägarna `foodGroup` och `description` indexeras.

```json
"pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ]
```

Indexering av metod tips i Azure Cosmos DBs API för MongoDB skiljer sig från MongoDB. I Azure Cosmos DB s API för MongoDB används sammansatta index endast i frågor som behöver sorteras på ett effektivt sätt med flera egenskaper. Om du har frågor med filter på flera egenskaper bör du skapa enstaka fält index för var och en av dessa egenskaper. Fråga-predikat kan använda flera fält index.

[Jokertecken index](mongodb-indexing.md#wildcard-indexes) kan förenkla indexeringen. Till skillnad från i MongoDB kan jokertecken indexeras ha stöd för flera fält i frågesyntaxen. Det kommer inte att finnas någon skillnad i frågans prestanda om du använder ett enda index i ett jokertecken i stället för att skapa ett separat index för varje egenskap. Att lägga till ett Wildcard-index för alla egenskaper är det enklaste sättet att optimera alla dina frågor.

Du kan lägga till nya index när som helst, utan någon påverkan på Skriv-eller Läs tillgänglighet. Du kan [spåra förloppet för index omvandling](./how-to-manage-indexing-policy.md#dotnet-sdk).

### <a name="understand-which-aggregation-operations-use-the-index"></a>Förstå vilka agg regerings åtgärder som använder indexet

I de flesta fall kommer agg regerings åtgärder i Azure Cosmos DBs API för MongoDB att delvis använda index. Som vanligt vis tillämpar frågespråket likhets-och intervall filter först och använder index. När du har tillämpat dessa filter kan du utvärdera ytterligare filter och ställa in återstående dokument för att beräkna mängden, om det behövs. 

Här är ett exempel:

```
db.coll.aggregate( [
   { $match: { foodGroup: 'Fruits and Fruit Juices' } },
   {
     $group: {
        _id: "$foodGroup",
        total: { $max: "$version" }
     }
   }
] )
```

I det här fallet kan index optimera `$match` scenen. Om du lägger till ett index för `foodGroup` förbättras prestanda avsevärt. Precis som i MongoDB bör du placera `$match` så tidigt i agg regerings pipelinen som möjligt för att maximera användningen av index.

I Azure Cosmos DB s API för MongoDB används inte index för den faktiska agg regeringen, vilket i det här fallet är `$max` . Om du lägger till ett index på `version` förbättras inte frågans prestanda.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Frågor där antal hämtade dokument är lika med antalet utgående dokument

Om `retrievedDocumentCount` är ungefär lika med `outputDocumentCount` , behövde inte frågemotor genomsöka många onödiga dokument.

### <a name="minimize-cross-partition-queries"></a>Minimera kors partitions frågor

Azure Cosmos DB använder [partitionering](partitioning-overview.md) för att skala enskilda behållare som begär ande enhet och data lagring måste öka. Varje fysisk partition har ett separat och oberoende index. Om frågan har ett likhets filter som matchar din behållares partitionsnyckel, behöver du bara kontrol lera den relevanta partitionens index. Den här optimeringen minskar det totala antalet ru: er som frågan kräver. [Läs mer om skillnaderna mellan frågor i partitionen och frågor om flera partitioner](how-to-query-container.md).

Om du har ett stort antal etablerade ru: er (mer än 30 000) eller en stor mängd data som lagras (mer än cirka 100 GB) har du förmodligen en stor mängd behållare för att se en betydande minskning av frågans RU-avgifter. 

Du kan kontrol lera `shardInformation` matrisen för att förstå frågans mått för varje enskild fysisk partition. Antalet unika `shardKeyRangeId` värden är antalet fysiska partitioner där frågan behövde köras. I det här exemplet kördes frågan på fyra fysiska partitioner. Det är viktigt att förstå att körningen är helt oberoende från användningen av index. Med andra ord kan frågor om flera partitioner fortfarande använda index.

```json
  "shardInformation" : [ 
                    {
                        "activityId" : "42f670a8-a201-4c58-8023-363ac18d9e18",
                        "shardKeyRangeId" : "5",
                        "durationMS" : 24.3859,
                        "preemptions" : 1,
                        "outputDocumentCount" : 463,
                        "retrievedDocumentCount" : 463
                    }, 
                    {
                        "activityId" : "a8bf762a-37b9-4c07-8ed4-ae49961373c0",
                        "shardKeyRangeId" : "2",
                        "durationMS" : 35.8328,
                        "preemptions" : 1,
                        "outputDocumentCount" : 905,
                        "retrievedDocumentCount" : 905
                    }, 
                    {
                        "activityId" : "3754e36b-4258-49a6-8d4d-010555628395",
                        "shardKeyRangeId" : "1",
                        "durationMS" : 67.3969,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1479,
                        "retrievedDocumentCount" : 1479
                    }, 
                    {
                        "activityId" : "a69a44ee-db97-4fe9-b489-3791f3d52878",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 185.1523,
                        "preemptions" : 1,
                        "outputDocumentCount" : 867,
                        "retrievedDocumentCount" : 867
                    }
                ]
```

## <a name="optimizations-that-reduce-query-latency"></a>Optimeringar som minskar svars tiden för frågor

I många fall kan avgiften för RU vara acceptabel när svars tiden för frågor fortfarande är för hög. I följande avsnitt får du en översikt över tips för att minska svars tiden för frågor. Om du kör samma fråga flera gånger på samma data uppsättning har den normalt samma RU-avgift varje gång. Men svars tiden kan variera mellan frågekörningen.

### <a name="improve-proximity"></a>Förbättra närhet

Frågor som körs från en annan region än Azure Cosmos DB kontot har högre latens än om de kördes i samma region. Om du till exempel kör kod på din station ära dator, bör du förvänta dig att fördröjningen ska vara flera eller hundratals millisekunder högre (eller mer) än om frågan kom från en virtuell dator i samma Azure-region som Azure Cosmos DB. Det är enkelt att [Distribuera data i Azure Cosmos DB globalt](tutorial-global-distribution-mongodb.md) för att se till att du kan ta med dina data närmare din app.

### <a name="increase-provisioned-throughput"></a>Öka det etablerade data flödet

I Azure Cosmos DB mäts ditt allokerade data flöde i ru: er (Request units). Anta att du har en fråga som använder 5 ru: er data flöde. Om du till exempel etablerar 1 000 ru: er kan du köra frågan 200 gånger per sekund. Om du försökte köra frågan när det inte fanns tillräckligt med data flöde, kommer Azure Cosmos DB att begränsa antalet begär Anden. Azure Cosmos DBs API för MongoDB kommer automatiskt att försöka utföra frågan igen efter en kort tid. Begränsade begär Anden tar längre tid, vilket ökar det etablerade data flödet kan förbättra svars tiden.

Värdet `estimatedDelayFromRateLimitingInMilliseconds` ger en uppfattning om möjliga latens-förmåner om du ökar data flödet.

## <a name="next-steps"></a>Nästa steg

* [Felsöka frågans prestanda (SQL API)](troubleshoot-query-performance.md)
* [Hantera indexering i Azure Cosmos DB-API för MongoDB](mongodb-indexing.md)
