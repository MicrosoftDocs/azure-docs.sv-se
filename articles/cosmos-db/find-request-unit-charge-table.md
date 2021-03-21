---
title: Sök efter avgift för begär ande enhet (RU) för en Tabell-API frågor i Azure Cosmos DB
description: Lär dig hur du hittar avgiften för begär ande enheten (RU) för Tabell-API frågor som körs mot en Azure Cosmos-behållare. Du kan använda språken Azure Portal, .NET, Java, python och Node.js för att hitta avgiften för RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 0c3f2495630358f48900a9cf27cbc0feb4192e1d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201316"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-table-api"></a>Hitta avgiften för enhets begär Anden för åtgärder som körs i Azure Cosmos DB Tabell-API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Azure Cosmos DB stöder många API: er, till exempel SQL, MongoDB, Cassandra, Gremlin och Table. Varje API har en egen uppsättning databas åtgärder. Dessa åtgärder sträcker sig från enkla punkter som läser och skriver till komplexa frågor. Varje databas åtgärd förbrukar system resurser baserat på åtgärdens komplexitet.

Kostnaden för alla databasåtgärder normaliseras av Azure Cosmos DB och uttrycks i form av enheter för programbegäran (Request Units, RU:er). Avgift för begäran är de enheter för programbegäran som används av alla dina databas åtgärder. Du kan tänka dig ru: er som en prestanda valuta som sammanfattar system resurserna, till exempel CPU, IOPS och minne som krävs för att utföra de databas åtgärder som stöds av Azure Cosmos DB. Oavsett vilket API du använder för att interagera med din Azure Cosmos-behållare mäts kostnaderna alltid med RU:er. Om databas åtgärden är en skrivning, en läsning eller fråga, mäts kostnader alltid i ru: er. Mer information finns i artikeln om [enheter för programbegäran och dess överväganden](request-units.md) .

I den här artikeln beskrivs de olika sätt som du kan hitta för att köra den [begärda enheten](request-units.md) (ru) för alla åtgärder som utförs mot en behållare i Azure Cosmos DB tabell-API. Om du använder ett annat API, se [API för MongoDB](find-request-unit-charge-mongodb.md), [API för Cassandra](find-request-unit-charge-cassandra.md), [Gremlin API](find-request-unit-charge-gremlin.md)och [SQL API](find-request-unit-charge.md) -artiklar för att hitta ru/s-avgiften.

## <a name="use-the-net-sdk"></a>Använda .NET SDK

För närvarande är den enda SDK som returnerar RU-avgiften för tabell åtgärder är [.net standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). `TableResult`Objektet visar en `RequestCharge` egenskap som är ifylld av SDK: n när du använder den mot Azure Cosmos DB tabell-API:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Mer information finns i [snabb start: bygga en tabell-API-app med hjälp av .NET SDK och Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Nästa steg

Information om hur du optimerar din RU-förbrukning finns i följande artiklar:

* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)
* [Optimera kostnaden för etablerat dataflöde i Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimera kostnaden för frågor i Azure Cosmos DB](./optimize-cost-reads-writes.md)