---
title: Använd körnings profilen för att utvärdera frågor i Azure Cosmos DB Gremlin-API
description: Lär dig hur du felsöker och förbättrar dina Gremlin-frågor med hjälp av steget kör profil.
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 03/27/2019
ms.author: chrande
ms.openlocfilehash: 18cefb1dd80368a8ccdad9f6f3ffc30881a8a889
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087493"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Använda stegen i körningsprofilen för att utvärdera Gremlin-frågor
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Den här artikeln innehåller en översikt över hur du använder steget körningsprofil för Azure Cosmos DB Gremlin-API-grafdatabaser. Det här steget ger relevant information för felsökning och frågekörning, och den är kompatibel med alla Gremlin-frågor som kan utföras mot ett Cosmos DB Gremlin API-konto.

Om du vill använda det här steget lägger du bara till `executionProfile()` funktions anropet i slutet av din Gremlin-fråga. **Din Gremlin-fråga körs** och resultatet av åtgärden returnerar ett JSON-svars objekt med frågans körnings profil.

Exempel:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

När du har anropat `executionProfile()` steget blir svaret ett JSON-objekt som innehåller det utförda Gremlin-steget, den totala tiden det tog och en matris av de Cosmos DB runtime-operatörer som instruktionen resulterade i.

> [!NOTE]
> Den här implementeringen av körnings profilen har inte definierats i Apache Tinkerpop-specifikationen. Den är unik för implementering av Azure Cosmos DB Gremlin-API.


## <a name="response-example"></a>Svars exempel

Följande är ett kommenterat exempel på utdata som kommer att returneras:

> [!NOTE]
> Det här exemplet är kommenterat med kommentarer som förklarar svarets allmänna struktur. Ett faktiskt executionProfile-svar innehåller inte några kommentarer.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> ExecutionProfile-steget kommer att köra frågan Gremlin. Detta omfattar `addV` eller `addE` -stegen, vilket leder till att de skapas och kommer att verkställa de ändringar som anges i frågan. Därför kommer de enheter för programbegäran som genereras av Gremlin-frågan också att debiteras.

## <a name="execution-profile-response-objects"></a>Svars objekt för körnings profil

Svaret på en executionProfile ()-funktion kommer att ge en hierarki med JSON-objekt med följande struktur:
  - **Gremlin-åtgärds objekt**: representerar hela Gremlin-åtgärden som kördes. Innehåller följande egenskaper.
    - `gremlin`: Den explicita Gremlin-instruktion som kördes.
    - `totalTime`: Tiden, i millisekunder, som körningen av steget i. 
    - `metrics`: En matris som innehåller var och en av de Cosmos DB runtime-operatörer som kördes för att uppfylla frågan. Den här listan sorteras i körnings ordning.
    
  - **Cosmos DB runtime-operatörer**: representerar var och en av komponenterna i hela Gremlin-åtgärden. Den här listan sorteras i körnings ordning. Varje-objekt innehåller följande egenskaper:
    - `name`: Namnet på operatorn. Detta är den typ av steg som utvärderades och kördes. Läs mer i tabellen nedan.
    - `time`: Hur lång tid i millisekunder som en specifik operatör vidtog.
    - `annotations`: Innehåller ytterligare information, som är speciell för den operator som kördes.
    - `annotations.percentTime`: Procent andel av den totala tiden som det tog att utföra den angivna operatorn.
    - `counts`: Antalet objekt som har returnerats från lagrings skiktet av den här operatorn. Detta finns i det `counts.resultCount` skalära värdet i.
    - `storeOps`: Representerar en lagrings åtgärd som kan omfatta en eller flera partitioner.
    - `storeOps.fanoutFactor`: Representerar antalet partitioner som den här angivna lagrings åtgärden har åtkomst till.
    - `storeOps.count`: Representerar antalet resultat som den här lagrings åtgärden returnerade.
    - `storeOps.size`: Representerar storleken i byte för resultatet av en specifik lagrings åtgärd.

Cosmos DB Gremlin runtime-operator|Beskrivning
---|---
`GetVertices`| Det här steget hämtar en predikat uppsättning objekt från beständiga skiktet. 
`GetEdges`| Det här steget hämtar de kanter som gränsar till en uppsättning formhörn. Det här steget kan resultera i en eller flera lagrings åtgärder.
`GetNeighborVertices`| Det här steget hämtar de hörn som är anslutna till en uppsättning kanter. Kanterna innehåller partitionerings nycklar och ID: n för både käll-och mål hörn.
`Coalesce`| I det här steget visas en utvärdering av två åtgärder när `coalesce()` Gremlin-steget körs.
`CartesianProductOperator`| Det här steget beräknar en kartesiska-produkt mellan två data uppsättningar. Utförs vanligt vis när predikat `to()` eller `from()` används.
`ConstantSourceOperator`| Det här steget beräknar ett uttryck för att skapa ett konstant värde som ett resultat.
`ProjectOperator`| Det här steget förbereder och serialiserar ett svar med hjälp av resultatet från föregående åtgärder.
`ProjectAggregation`| I det här steget förbereds och serialiseras ett svar för en agg regerings åtgärd.

> [!NOTE]
> Den här listan fortsätter att uppdateras när nya operatörer läggs till.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Exempel på hur du analyserar ett svar på körnings profilen

Följande är exempel på vanliga optimeringar som kan Spotted med hjälp av körnings profil svaret:
  - Blind fläkt-out-fråga.
  - Ofiltrerad fråga.

### <a name="blind-fan-out-query-patterns"></a>Fråga mönster för blinda fläktar

Antag följande körnings profil svar från en **partitionerad graf**:

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

Följande slut satser kan göras från den:
- Frågan är en enskild ID-sökning, eftersom Gremlin-instruktionen följer mönstret `g.V('id')` .
- Bedömnings från `time` måttet, verkar svars tiden för den här frågan vara hög eftersom det är [mer än 10ms för en enda punkt-Läs åtgärd](./introduction.md#guaranteed-speed-at-any-scale).
- Om vi tittar på `storeOps` objektet kan vi se att `fanoutFactor` är `5` , vilket innebär att [5 partitioner](./partitioning-overview.md) har öppnats av den här åtgärden.

I slutet av den här analysen kan vi fastställa att den första frågan har åtkomst till fler partitioner än vad som behövs. Detta kan åtgärdas genom att ange partitionerings nyckeln i frågan som ett predikat. Detta leder till mindre latens och mindre kostnad per fråga. Lär dig mer om [diagram partitionering](graph-partitioning.md). En mer optimal fråga är `g.V('tt0093640').has('partitionKey', 't1001')` .

### <a name="unfiltered-query-patterns"></a>Ofiltrerad fråga mönster

Jämför följande två svar på körnings profilen. För enkelhetens skull använder dessa exempel ett enda partitionerat diagram.

Den här första frågan hämtar alla formhörn med etiketten `tweet` och hämtar sedan sina grann hörn:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

Lägg märke till profilen för samma fråga, men nu med ett ytterligare filter, `has('lang', 'en')` innan du utforskar de intilliggande hörnen:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

De här två frågorna har uppnått samma resultat, men den första måste dock kräva fler enheter för programbegäran eftersom den behövde upprepa en större inledande data uppsättning innan den frågar efter intilliggande objekt. Vi kan se indikatorer för det här beteendet när du jämför följande parametrar från båda svaren:
- `metrics[0].time`Värdet är högre i det första svaret, vilket indikerar att det här enskilda steget tog längre tid att lösa.
- `metrics[0].counts.resultsCount`Värdet är högre och det första svaret, som anger att den första arbets data uppsättningen var större.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om de [Gremlin-funktioner som stöds](gremlin-support.md) i Azure Cosmos dB. 
* Läs mer om [Gremlin-API: et i Azure Cosmos DB](graph-introduction.md).