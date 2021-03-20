---
title: Felsöka problem med frågor när du använder Azure Cosmos DB
description: Lär dig att identifiera, diagnostisera och felsöka Azure Cosmos DB problem med SQL-frågor.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/16/2021
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 6701a580cbe7790dcce2cbbcc46889f9dff00107
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100559986"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Felsöka problem med frågor när du använder Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln vägleder dig genom en allmänt rekommenderad metod för fel sökning av frågor i Azure Cosmos DB. Även om du inte bör tänka på de steg som beskrivs i den här artikeln får du ett fullständigt försvar mot potentiella frågor, men vi har inkluderat de vanligaste prestanda tipsen här. Använd den här artikeln som utgångspunkt vid felsökning av långsamma eller resurskrävande frågor i SQL-API:et i Azure Cosmos DB. Du kan också använda [diagnostikloggarna](cosmosdb-monitor-resource-logs.md) till att identifiera vilka frågor som är långsamma eller som förbrukar stora mängder dataflöde. Om du använder Azure Cosmos DBs API för MongoDB bör du använda [Azure Cosmos DB s API för fel söknings guide för MongoDB-frågor](mongodb-troubleshoot-query.md)

Optimeringar av frågor i Azure Cosmos DB är i stort sett kategoriserade enligt följande:

- Optimeringar som minskar antalet begär ande enheter (RU) för frågan
- Optimeringar som bara minskar svars tiden

Om du minskar avgiften för en fråga så minskar du normalt svars tiden.

Den här artikeln innehåller exempel som du kan återskapa med hjälp av [närings data uppsättningen](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

## <a name="common-sdk-issues"></a>Vanliga SDK-problem

Innan du läser den här guiden är det bra att överväga vanliga SDK-problem som inte är relaterade till frågemotor.

- Följ de här [tipsen för SDK-prestanda](performance-tips.md).
    - [Fel söknings guide för .NET SDK](troubleshoot-dot-net-sdk.md)
    - [Fel söknings guide för Java SDK](troubleshoot-java-sdk-v4-sql.md)
- SDK gör det möjligt att ställa in en `MaxItemCount` för dina frågor, men du kan inte ange ett minsta antal objekt.
    - Koden ska hantera vilken sid storlek som helst, från noll till `MaxItemCount` .
- Ibland kan frågor ha tomma sidor även om det finns resultat på en kommande sida. Orsaker till detta kan vara:
    - SDK kan utföra flera nätverks anrop.
    - Frågan kan ta lång tid att hämta dokumenten.
- Alla frågor har en fortsättnings-token som gör att frågan kan fortsätta. Se till att tömma frågan helt. Läs mer om hur du [hanterar flera resultat sidor](sql-query-pagination.md#handling-multiple-pages-of-results)

## <a name="get-query-metrics"></a>Hämta frågans mått

När du optimerar en fråga i Azure Cosmos DB, är det första steget alltid att [Hämta frågans mått](profile-sql-api-query.md) för frågan. Dessa mått är också tillgängliga via Azure Portal. När du kör din fråga i Datautforskaren visas frågans mått bredvid fliken **resultat** :

:::image type="content" source="./media/troubleshoot-query-performance/obtain-query-metrics.png" alt-text="Hämta Frågeregler" lightbox="./media/troubleshoot-query-performance/obtain-query-metrics.png":::

När du har hämtat frågeresultaten jämför du **antalet hämtade dokument** med **antalet utdata** i frågan. Använd den här jämförelsen för att identifiera relevanta avsnitt som ska granskas i den här artikeln.

**Antalet hämtade dokument** är antalet dokument som frågemotor behöver läsa in. **Antalet utgående dokument** är antalet dokument som behövdes för frågeresultatet. Om **antalet hämtade dokument** är betydligt högre än **antalet utgående dokument**, fanns det minst en del av din fråga som inte kunde använda ett index och behövdes för att göra en genomsökning.

Se följande avsnitt för att förstå relevanta optimeringar av frågor för ditt scenario.

### <a name="querys-ru-charge-is-too-high"></a>Frågans RU-avgift är för hög

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Antalet hämtade dokument är betydligt högre än antalet utgående dokument

- [Inkludera nödvändiga sökvägar i indexerings principen.](#include-necessary-paths-in-the-indexing-policy)

- [Förstå vilka system funktioner som använder indexet.](#understand-which-system-functions-use-the-index)

- [Förbättra körningen av sträng system funktionen.](#improve-string-system-function-execution)

- [Förstå vilka mängd frågor som använder indexet.](#understand-which-aggregate-queries-use-the-index)

- [Optimera frågor som har både ett filter och en ORDER BY-sats.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Optimera KOPPLINGs uttryck med hjälp av en under fråga.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Antalet hämtade dokument är ungefär lika med antalet utgående dokument

- [Minimera kors partitions frågor.](#minimize-cross-partition-queries)

- [Optimera frågor som har filter på flera egenskaper.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Optimera frågor som har både ett filter och en ORDER BY-sats.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>Frågans RU-avgift är acceptabel, men fördröjningen är fortfarande för hög

- [Förbättra närhet.](#improve-proximity)

- [Öka det etablerade data flödet.](#increase-provisioned-throughput)

- [Öka MaxConcurrency.](#increase-maxconcurrency)

- [Öka MaxBufferedItemCount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Frågor där antal hämtade dokument överskrider antalet utgående dokument

 **Antalet hämtade dokument** är antalet dokument som frågemotor behöver läsa in. Antalet **Utgående dokument** är antalet dokument som returneras av frågan. Om **antalet hämtade dokument** är betydligt högre än **antalet utgående dokument**, fanns det minst en del av din fråga som inte kunde använda ett index och behövdes för att göra en genomsökning.

Här är ett exempel på en genomsöknings fråga som inte helt hanterades av indexet:

Fråga:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Fråga efter mått:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

**Antalet hämtade dokument** (60 951) är betydligt högre än **antalet utgående dokument** (7), vilket innebär att frågan resulterade i en dokuments ökning. I det här fallet använder systemfunktionen [()](sql-query-upper.md) inte ett index.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Inkludera nödvändiga sökvägar i indexerings principen

Din indexerings princip ska omfatta egenskaper som ingår i `WHERE` satser, `ORDER BY` satser, `JOIN` och de flesta system funktioner. Önskade sökvägar som anges i index principen ska matcha egenskaperna i JSON-dokumenten.

> [!NOTE]
> Egenskaper i Azure Cosmos DB indexerings princip är Skift läges känsliga

Om du kör följande enkla fråga på [näringsvärdes](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) data uppsättningen, ser du en mycket lägre ru-avgift när egenskapen i `WHERE` satsen är indexerad:

#### <a name="original"></a>Ursprunglig

Fråga:

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
```

Indexerings princip:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**Avgift för ru:** 409,51 ru: er

#### <a name="optimized"></a>Optimerad

Indexerings princip har uppdaterats:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**Avgift för ru:** 2,98 ru: er

Du kan när som helst lägga till egenskaper till indexerings principen, utan någon påverkan på Skriv-eller Läs tillgänglighet. Du kan [spåra förloppet för index omvandling](./how-to-manage-indexing-policy.md#dotnet-sdk).

### <a name="understand-which-system-functions-use-the-index"></a>Förstå vilka system funktioner som använder indexet

De flesta system Functions använder index. Här är en lista över några vanliga sträng funktioner som använder index:

- StartsWith
- Innehåller
- RegexMatch
- Vänster
- Del sträng – men endast om det första num_expr är 0

Nedan följer några vanliga system funktioner som inte använder indexet och måste läsa in varje dokument när det används i en- `WHERE` sats:

| **System funktion**                     | **Idéer för optimering**             |
| --------------------------------------- |------------------------------------------------------------ |
| Övre/nedre                         | I stället för att använda systemfunktionen för att normalisera data för jämförelser normaliserar du höljet vid infogning. En fråga som ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` blir ```SELECT * FROM c WHERE c.name = 'BOB'``` . |
| GetCurrentDateTime/GetCurrentTimestamp/GetCurrentTicks | Beräkna den aktuella tiden innan frågekörningen körs och Använd det sträng värdet i- `WHERE` satsen. |
| Matematiska funktioner (icke-mängder) | Om du behöver beräkna ett värde ofta i din fråga bör du lagra värdet som en egenskap i JSON-dokumentet. |

Dessa system funktioner kan använda index, förutom när de används i frågor med agg regeringar:

| **System funktion**                     | **Idéer för optimering**             |
| --------------------------------------- |------------------------------------------------------------ |
| Funktioner i spatial system                        | Lagra frågeresultatet i en förmaterialiserad vy i real tid |

När det används i `SELECT` -satsen påverkar ineffektiva system funktioner inte hur frågor kan använda index.

### <a name="improve-string-system-function-execution"></a>Förbättra körning av sträng system funktion

För vissa system funktioner som använder index kan du förbättra frågekörningen genom att lägga till en `ORDER BY` sats i frågan. 

Mer specifikt, alla systemfunktioner vars RU-avgift ökar när egenskapens kardinalitet ökar kan dra nytta av `ORDER BY` frågan. Dessa frågor gör en indexs ökning, så att frågeresultatet kan göra frågan mer effektiv.

Den här optimeringen kan förbättra körningen för följande system funktioner:

- StartsWith (där Skift läges okänslig = sant)
- StringEquals (där Skift läges okänslig = sant)
- Innehåller
- RegexMatch
- EndsWith

Överväg till exempel nedanstående fråga med `CONTAINS` . `CONTAINS` använder index men ibland, även efter att du har lagt till det relevanta indexet, kan du fortfarande se en mycket hög RU-avgift när du kör frågan nedan.

Ursprunglig fråga:

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
```

Du kan förbättra frågekörningen genom att lägga till `ORDER BY` :

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
ORDER BY c.town
```

Samma optimering kan hjälpa i frågor med ytterligare filter. I det här fallet är det bäst att även lägga till egenskaper med likhets filter till- `ORDER BY` satsen.

Ursprunglig fråga:

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
```

Du kan förbättra frågekörningen genom att lägga till `ORDER BY` och [ett sammansatt index](index-policy.md#composite-indexes) för (c.Name, c. stad):

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
ORDER BY c.name, c.town
```

### <a name="understand-which-aggregate-queries-use-the-index"></a>Förstå vilka mängd frågor som använder indexet

I de flesta fall använder agg regerings system funktioner i Azure Cosmos DB indexet. Men beroende på filter eller ytterligare satser i en mängd fråga kan du behöva läsa in ett stort antal dokument i Frågeredigeraren. Som vanligt vis används likhets-och intervall filter först i Frågeredigeraren. När du har tillämpat dessa filter kan du utvärdera ytterligare filter och ställa in återstående dokument för att beräkna mängden, om det behövs.

Till exempel är frågan med både likhets-och `CONTAINS` system funktions filter mer effektiv än en fråga med bara ett `CONTAINS` funktion filter i systemet. Detta beror på att likhets filtret används först och använder indexet innan dokument måste läsas in för det dyrare `CONTAINS` filtret.

Fråga med endast `CONTAINS` filter högre ru-avgift:

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

Fråga med både likhets filter och `CONTAINS` filter-lägsta ru-avgift:

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Här följer ytterligare exempel på sammanställda frågor som inte fullt ut använder indexet:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Frågor med system funktioner som inte använder indexet

Du bör referera till den relevanta [system funktions sidan](sql-query-system-functions.md) för att se om den använder indexet.

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Sammanställda frågor med användardefinierade funktioner (UDF)

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Frågor med GROUP BY

RU-avgiften för frågor med `GROUP BY` ökar när egenskaperna i `GROUP BY` satsen ökar. I nedanstående fråga ökar t. ex. avgifts avgiften för frågan eftersom det totala antalet unika beskrivningar ökar.

Avgiften för en agg regerings funktion med en `GROUP BY` sats är högre än ru-avgiften för en sammansatt funktion separat. I det här exemplet måste frågemotor läsa in alla dokument som matchar `c.foodGroup = "Sausages and Luncheon Meats"` filtret så att avgiften för ru förväntas vara hög.

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

Om du planerar att ofta köra samma mängd frågor kan det vara mer effektivt att skapa en materialiserad vy i real tid med [Azure Cosmos DB ändra feed](change-feed.md) än att köra enskilda frågor.

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Optimera frågor som har både ett filter och en ORDER BY-sats

Även om frågor som har ett filter och en `ORDER BY` sats normalt använder ett intervall index, blir de mer effektiva om de kan hanteras från ett sammansatt index. Förutom att ändra indexerings principen bör du lägga till alla egenskaper i det sammansatta indexet i- `ORDER BY` satsen. Den här ändringen av frågan säkerställer att den använder det sammansatta indexet.  Du kan se effekten genom att köra en fråga på [närings](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) data uppsättningen:

#### <a name="original"></a>Ursprunglig

Fråga:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
```

Indexerings princip:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**Avgift för ru:** 44,28 ru: er

#### <a name="optimized"></a>Optimerad

Uppdaterad fråga (inkluderar båda egenskaperna i `ORDER BY` satsen):

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

Indexerings princip har uppdaterats:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**Avgift för ru:** 8,86 ru: er

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Optimera KOPPLINGs uttryck med hjälp av en under fråga

Under frågor med flera värden kan optimera `JOIN` uttryck genom att push-överföra predikat efter varje Select-many-uttryck i stället för efter alla kors kopplingar i- `WHERE` satsen.

Tänk på följande fråga:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Avgift för ru:** 167,62 ru: er

I den här frågan matchar indexet alla dokument som har en tagg med namnet, som `infant formula` är `nutritionValue` större än 0 och `amount` större än 1. `JOIN`Uttrycket här utför kors produkten av alla objekt i taggar, näringsämnen och betjänar matriser för varje matchande dokument innan något filter tillämpas. - `WHERE` Satsen tillämpar sedan filtrets predikat på varje `<c, t, n, s>` tupel.

Om ett matchande dokument till exempel har 10 objekt i var och en av de tre matriserna, expanderas det till 1 x 10 x 10 x 10 (d.v.s. 1 000) tupler. Användningen av under frågor här kan hjälpa dig att filtrera ut sammanfogade mat ris objekt innan du ansluter till nästa uttryck.

Den här frågan motsvarar den tidigare en men använder under frågor:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Avgift för ru:** 22,17 ru: er

Anta att endast ett objekt i matrisen taggar matchar filtret och att det finns fem objekt för både näringsämnen och betjänande matriser. `JOIN`Uttrycken utökas till 1 x 1 x 5 x 5 = 25 objekt, i stället för 1 000 objekt i den första frågan.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Frågor där antal hämtade dokument är lika med antalet utgående dokument

Om **antalet hämtade dokument** är ungefär lika med **antalet utgående dokument**, behövde inte frågemotor genomsöka många onödiga dokument. För många frågor, t. ex. de som använder `TOP` nyckelordet, kan **antalet hämtade dokument** överstiga **antalet utgående dokument** med 1. Du behöver inte bekymra dig om detta.

### <a name="minimize-cross-partition-queries"></a>Minimera kors partitions frågor

Azure Cosmos DB använder [partitionering](partitioning-overview.md) för att skala enskilda behållare som begär ande enhet och data lagring måste öka. Varje fysisk partition har ett separat och oberoende index. Om frågan har ett likhets filter som matchar din behållares partitionsnyckel, behöver du bara kontrol lera den relevanta partitionens index. Den här optimeringen minskar det totala antalet ru: er som frågan kräver.

Om du har ett stort antal etablerade ru: er (mer än 30 000) eller en stor mängd data som lagras (mer än cirka 100 GB) har du förmodligen en stor mängd behållare för att se en betydande minskning av frågans RU-avgifter.

Om du till exempel skapar en behållare med foodGroup, behöver följande frågor bara kontrol lera en enda fysisk partition:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Frågor som har ett `IN` filter med partitionsnyckel kommer bara att kontrol lera de relevanta fysiska partitionerna och kommer inte att "fläkt ut":

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Frågor som har intervall filter i partitionsnyckel eller som inte har några filter i partitionsnyckel måste vara "fläkt-out" och kontrol lera varje fysisk partitions index för resultat:

```sql
SELECT *
FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT *
FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Optimera frågor som har filter på flera egenskaper

Även om frågor som har filter på flera egenskaper ofta använder ett intervall index, blir de mer effektiva om de kan hanteras från ett sammansatt index. För små mängder data får den här optimeringen ingen betydande inverkan. Det kan dock vara användbart för stora mängder data. Du kan bara optimera högst ett icke-jämlikhets filter per sammansatt index. Om frågan har flera filter som inte är lika likhet väljer du en av dem som ska använda det sammansatta indexet. Resten kommer att fortsätta att använda intervall index. Filtret för icke-jämlikhet måste definieras sist i det sammansatta indexet. [Läs mer om sammansatta index](index-policy.md#composite-indexes).

Här följer några exempel på frågor som kan optimeras med ett sammansatt index:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Här är det relevanta sammansatta indexet:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>Optimeringar som minskar svars tiden för frågor

I många fall kan avgiften för RU vara acceptabel när svars tiden för frågor fortfarande är för hög. I följande avsnitt får du en översikt över tips för att minska svars tiden för frågor. Om du kör samma fråga flera gånger på samma data uppsättning har den normalt samma RU-avgift varje gång. Men svars tiden kan variera mellan frågekörningen.

### <a name="improve-proximity"></a>Förbättra närhet

Frågor som körs från en annan region än Azure Cosmos DB kontot har högre latens än om de kördes i samma region. Om du till exempel kör kod på din station ära dator, bör du förvänta dig att fördröjningen ska vara flera eller hundratals millisekunder högre (eller mer) än om frågan kom från en virtuell dator i samma Azure-region som Azure Cosmos DB. Det är enkelt att [Distribuera data i Azure Cosmos DB globalt](distribute-data-globally.md) för att se till att du kan ta med dina data närmare din app.

### <a name="increase-provisioned-throughput"></a>Öka det etablerade data flödet

I Azure Cosmos DB mäts ditt allokerade data flöde i ru: er (Request units). Anta att du har en fråga som använder 5 ru: er data flöde. Om du till exempel etablerar 1 000 ru: er kan du köra frågan 200 gånger per sekund. Om du försökte köra frågan när det inte fanns tillräckligt med data flöde, skulle Azure Cosmos DB returnera ett HTTP 429-fel. Alla aktuella Core (SQL) API SDK: er kommer automatiskt att försöka utföra frågan igen efter en kort tid. Begränsade begär Anden tar längre tid, vilket ökar det etablerade data flödet kan förbättra svars tiden. Du kan se det [totala antalet begränsade begär Anden](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) på bladet **mått** i Azure Portal.

### <a name="increase-maxconcurrency"></a>Öka MaxConcurrency

Parallella frågor fungerar genom att fråga flera partitioner parallellt. Men data från en enskild partitionerad samling hämtas seriellt i förhållande till frågan. Så om du ställer in MaxConcurrency på antalet partitioner har du den bästa möjligheten att nå den mest utförda frågan, förutsatt att alla andra system villkor är desamma. Om du inte vet antalet partitioner kan du ange MaxConcurrency (eller MaxDegreesOfParallelism i äldre SDK-versioner) till ett stort antal. Systemet väljer det lägsta (antal partitioner, indata från användaren) som den högsta graden av parallellitet.

### <a name="increase-maxbuffereditemcount"></a>Öka MaxBufferedItemCount

Frågor är utformade för att hämta resultat när den aktuella resultat gruppen bearbetas av klienten. För hämtning bidrar till att förbättra den övergripande svars tiden för en fråga. Om du anger MaxBufferedItemCount begränsas antalet i förväg hämtade resultat. Om du anger det här värdet till det förväntade antalet returnerade resultat (eller ett högre tal) kan frågan få ut mesta möjliga av för hämtning. Om du anger värdet-1 avgör systemet automatiskt antalet objekt som ska buffras.

## <a name="next-steps"></a>Nästa steg
I följande artiklar finns information om hur du mäter ru: er per fråga, hämtar körnings statistik för att finjustera dina frågor med mera:

* [Hämta SQL-frågans körnings mått med hjälp av .NET SDK](profile-sql-api-query.md)
* [Justera frågeprestanda med Azure Cosmos DB](./sql-api-query-metrics.md)
* [Prestandatips för .NET SDK](performance-tips.md)
* [Prestanda tips för Java v4 SDK](performance-tips-java-sdk-v4-sql.md)