---
title: Sök efter begär ande enhets avgifter (RU) för Gremlin API-frågor i Azure Cosmos DB
description: Lär dig hur du hittar avgiften för begär ande enheten (RU) för Gremlin-frågor som körs mot en Azure Cosmos-behållare. Du kan använda Azure Portal .NET, Java-drivrutiner för att hitta avgift för RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 295ed3eb2312a5f614a4214b5b627f5657fba1eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201345"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-gremlin-api"></a>Hitta begär ande enhets avgiften för åtgärder som körs i Azure Cosmos DB Gremlin API
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB stöder många API: er, till exempel SQL, MongoDB, Cassandra, Gremlin och Table. Varje API har en egen uppsättning databas åtgärder. Dessa åtgärder sträcker sig från enkla punkter som läser och skriver till komplexa frågor. Varje databas åtgärd förbrukar system resurser baserat på åtgärdens komplexitet.

Kostnaden för alla databasåtgärder normaliseras av Azure Cosmos DB och uttrycks i form av enheter för programbegäran (Request Units, RU:er). Avgift för begäran är de enheter för programbegäran som används av alla dina databas åtgärder. Du kan tänka dig ru: er som en prestanda valuta som sammanfattar system resurserna, till exempel CPU, IOPS och minne som krävs för att utföra de databas åtgärder som stöds av Azure Cosmos DB. Oavsett vilket API du använder för att interagera med din Azure Cosmos-behållare mäts kostnaderna alltid med RU:er. Om databas åtgärden är en skrivning, en läsning eller fråga, mäts kostnader alltid i ru: er. Mer information finns i artikeln om [enheter för programbegäran och dess överväganden](request-units.md) .

I den här artikeln beskrivs de olika sätt som du kan hitta för att köra den [begärda enheten](request-units.md) (ru) för alla åtgärder som utförs mot en behållare i Azure Cosmos DB Gremlin API. Om du använder ett annat API, se [API för MongoDB](find-request-unit-charge-mongodb.md), [API för Cassandra](find-request-unit-charge-cassandra.md), [SQL API](find-request-unit-charge.md)och [tabell-API](find-request-unit-charge-table.md) artiklar för att hitta ru/s-avgiften.

Huvuden som returneras av Gremlin-API: t mappas till anpassade status-attribut, som för närvarande finns i Gremlin .NET och Java SDK. Begär ande avgiften är tillgänglig under `x-ms-request-charge` nyckeln. När du använder Gremlin-API: et har du flera alternativ för att hitta RU-förbrukningen för en åtgärd mot en Azure Cosmos-behållare.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-graph-gremlin-console.md#create-a-database-account) och mata in det med data, eller Välj ett befintligt konto som redan innehåller data.

1. Gå till fönstret **datautforskaren** och välj den behållare som du vill arbeta med.

1. Ange en giltig fråga och välj sedan **Kör Gremlin-fråga**.

1. Välj **fråga statistik** för att visa den faktiska begär ande avgiften för den begäran du utförde.

:::image type="content" source="./media/find-request-unit-charge/portal-gremlin-query.png" alt-text="Skärm bild för att få en Gremlin för förfrågningar om begär anden i Azure Portal":::

## <a name="use-the-net-sdk-driver"></a>Använd .NET SDK-drivrutinen

När du använder [GREMLIN.NET SDK](https://www.nuget.org/packages/Gremlin.Net/)är attributet status tillgängligt under `StatusAttributes` `ResultSet<>` objektets egenskap:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Mer information finns i [snabb start: bygga en .NET Framework eller ett kärn program med hjälp av ett Azure Cosmos DB GREMLIN API-konto](create-graph-dotnet.md).

## <a name="use-the-java-sdk-driver"></a>Använda Java SDK-drivrutinen

När du använder [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)kan du hämta attribut för status genom att anropa- `statusAttributes()` metoden på `ResultSet` objektet:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Mer information finns i [snabb start: skapa en graf-databas i Azure Cosmos dB med Java SDK](create-graph-java.md).

## <a name="next-steps"></a>Nästa steg

Information om hur du optimerar din RU-förbrukning finns i följande artiklar:

* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)
* [Optimera kostnaden för etablerat dataflöde i Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimera kostnaden för frågor i Azure Cosmos DB](./optimize-cost-reads-writes.md)