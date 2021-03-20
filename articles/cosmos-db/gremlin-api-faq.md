---
title: 'Vanliga frågor om Gremlin-API: et i Azure Cosmos DB'
description: 'Få svar på vanliga frågor om Gremlin-API: et i Azure Cosmos DB'
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 0374886482b43eefe6df5e107fd4da371633099d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359796"
---
# <a name="frequently-asked-questions-about-the-gremlin-api-in-azure-cosmos-db"></a>Vanliga frågor om Gremlin-API: et i Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Den här artikeln beskriver svar på några vanliga frågor om Gremlin-API i Azure Cosmos DB.

## <a name="how-to-evaluate-the-efficiency-of-gremlin-queries"></a>Så här utvärderar du effektiviteten för Gremlin-frågor

Förhands gransknings steget **executionProfile ()** kan användas för att tillhandahålla en analys av fråge körnings planen. Det här steget måste läggas till i slutet av en Gremlin-fråga som illustreras i följande exempel:

**Exempel på frågor**

```
g.V('mary').out('knows').executionProfile()
```

**Exempel på utdata**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

Resultatet av profilen ovan visar hur lång tid som krävs för att hämta hörn objekt, kant objekt och storleken på den aktiva data uppsättningen. Detta är relaterat till standardkostnads måtten för Azure Cosmos DB frågor.

## <a name="other-frequently-asked-questions"></a>Andra vanliga frågor och svar

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Hur debiteras RU/s vid körning av frågor i en diagram databas?

Alla graf-objekt, hörn och kanter visas som JSON-dokument i Server delen. Eftersom en Gremlin-fråga kan ändra ett eller flera diagram objekt i taget, är kostnaden som är kopplad till den direkt relaterad till de objekt, kanter som bearbetas av frågan. Detta är samma process som Azure Cosmos DB används för alla andra API: er. Mer information finns i [Enheter för programbegäran i Azure Cosmos DB](request-units.md).

Avgiften för RU baseras på arbets data uppsättningen för genom gången och inte i resultat uppsättningen. Om en fråga till exempel syftar på att få ett enda formhörn som ett resultat men behöver gå över till fler än ett annat objekt på vägen, kommer kostnaden att baseras på alla graf-objekt som det tar för att beräkna den ena resultat Bryt punkten.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Vad är den maximala skalan som en diagram databas kan ha i Azure Cosmos DB Gremlin API?

Azure Cosmos DB använder [vågrät partitionering](partitioning-overview.md) för att automatiskt öka antalet lagrings-och data flödes krav. Högsta data flöde och lagrings kapacitet för en arbets belastning bestäms av antalet partitioner som associeras med en specifik behållare. En Gremlin API-behållare har dock en uppsättning rikt linjer för att säkerställa en korrekt prestanda upplevelse i stor skala. Mer information om partitionering och bästa praxis finns i [partitionering i Azure Cosmos DB](partitioning-overview.md) artikel.

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>För C#/.NET-distribution.-utveckling ska jag använda Microsoft. Azure. graphs-paketet eller Gremlin.NET?

Azure Cosmos DB Gremlin API använder driv rutiner med öppen källkod som huvud anslutningar för tjänsten. Det rekommenderade alternativet är att använda [driv rutiner som stöds av Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Hur kan jag skydda mot injektions angrepp med Gremlin-drivrutiner?

De flesta inbyggda Apache Tinkerpop Gremlin-drivrutiner ger möjlighet att tillhandahålla en lista över parametrar för frågekörningen. Detta är ett exempel på hur du gör det i [Gremlin.net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) och i [Gremlin-Java Script](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/main/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Varför får jag fel meddelandet "Gremlin Query Compilation: det går inte att hitta någon metod"?

Azure Cosmos DB Gremlin-API implementerar en delmängd av funktionerna som definierats i området Gremlin-yta. För steg som stöds och mer information, se [Gremlin support](gremlin-support.md) -artikeln.

Den bästa lösningen är att skriva om nödvändiga Gremlin-steg med funktioner som stöds, eftersom alla viktiga Gremlin-steg stöds av Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Varför får jag "WebSocketException: Servern returnerade status koden" 200 "när status koden" 101 "förväntades" fel?

Det här felet uppstår troligt vis när fel slut punkten används. Slut punkten som genererar det här felet har följande mönster:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Detta är dokument slut punkten för diagram databasen.  Den korrekta slut punkten som ska användas är Gremlin-slutpunkten, som har följande format:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Varför får jag fel meddelandet "RequestRateIsTooLarge"?

Det här felet innebär att allokerade enheter för programbegäran per sekund inte räcker för att betjäna frågan. Det här felet visas vanligt vis när du kör en fråga som hämtar alla formhörn:

```
// Query example:
g.V()
```

Den här frågan kommer att försöka hämta alla hörn från grafen. Detta innebär att kostnaden för den här frågan motsvarar minst antalet hörn i ru: er. Inställningen RU/s bör justeras för att åtgärda den här frågan.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Varför försvinner mina Gremlin-drivrutins anslutningar så småningom?

En Gremlin-anslutning görs via en WebSocket-anslutning. Även om WebSocket-anslutningar inte har en angiven tid för Live, avslutar Azure Cosmos DB Gremlin-API: et inaktiva anslutningar efter 30 minuters inaktivitet.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Varför kan jag inte använda Fluent API-anrop i de interna Gremlin-drivrutinerna?

Fluent API-anrop stöds ännu inte av Azure Cosmos DB Gremlin-API: et. Fluent API-anrop kräver en intern formaterings funktion som kallas bytekod-stöd som för närvarande inte stöds av Azure Cosmos DB Gremlin API. På grund av samma orsak stöds inte den senaste Gremlin-JavaScript driv rutinen för närvarande.

## <a name="next-steps"></a>Nästa steg

* [Stöd för Azure Cosmos DB Gremlin Wire Protocol](gremlin-support.md)
* Skapa, fråga och bläddra i en Azure Cosmos DB graf-databas med hjälp av [Gremlin-konsolen](create-graph-gremlin-console.md)
