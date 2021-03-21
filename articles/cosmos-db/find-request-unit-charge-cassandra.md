---
title: Sök efter avgift för begär ande enhet (RU) för en API för Cassandra fråga i Azure Cosmos DB
description: Lär dig hur du hittar avgiften för begär ande enheten (RU) för Cassandra-frågor som körs mot en Azure Cosmos-behållare. Du kan använda driv rutinerna Azure Portal, .NET och Java för att hitta avgift för RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: b504702ad0c74ae9728c0a8b34fa94df26184f70
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200509"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-cassandra-api"></a>Hitta avgiften för enhets begär Anden för åtgärder som körs i Azure Cosmos DB API för Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB stöder många API: er, till exempel SQL, MongoDB, Cassandra, Gremlin och Table. Varje API har en egen uppsättning databas åtgärder. Dessa åtgärder sträcker sig från enkla punkter som läser och skriver till komplexa frågor. Varje databas åtgärd förbrukar system resurser baserat på åtgärdens komplexitet.

Kostnaden för alla databasåtgärder normaliseras av Azure Cosmos DB och uttrycks i form av enheter för programbegäran (Request Units, RU:er). Avgift för begäran är de enheter för programbegäran som används av alla dina databas åtgärder. Du kan tänka dig ru: er som en prestanda valuta som sammanfattar system resurserna, till exempel CPU, IOPS och minne som krävs för att utföra de databas åtgärder som stöds av Azure Cosmos DB. Oavsett vilket API du använder för att interagera med din Azure Cosmos-behållare mäts kostnaderna alltid med RU:er. Om databas åtgärden är en skrivning, en läsning eller fråga, mäts kostnader alltid i ru: er. Mer information finns i artikeln om [enheter för programbegäran och dess överväganden](request-units.md) .

I den här artikeln beskrivs de olika sätt som du kan hitta för att köra den [begärda enheten](request-units.md) (ru) för alla åtgärder som utförs mot en behållare i Azure Cosmos DB API för Cassandra. Om du använder ett annat API, se [API för MongoDB](find-request-unit-charge-mongodb.md), [SQL API](find-request-unit-charge.md), [Gremlin API](find-request-unit-charge-gremlin.md)och [tabell-API](find-request-unit-charge-table.md) artiklar för att hitta ru/s-avgiften.

När du utför åtgärder mot Azure Cosmos DB API för Cassandra returneras RU-avgiften i inkommande nytto last som ett fält med namnet `RequestCharge` . Du har flera alternativ för att hämta RU-avgiften.

## <a name="use-the-net-sdk"></a>Använda .NET SDK

När du använder [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/)kan du hämta inkommande nytto Last under `Info` ett `RowSet` objekts egenskap:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Mer information finns i [snabb start: bygga en Cassandra-app med hjälp av .NET SDK och Azure Cosmos DB](create-cassandra-dotnet.md).

## <a name="use-the-java-sdk"></a>Använda Java SDK

När du använder [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)kan du hämta den inkommande nytto lasten genom att anropa `getExecutionInfo()` metoden på ett `ResultSet` objekt:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Mer information finns i [snabb start: bygga en Cassandra-app med hjälp av Java SDK och Azure Cosmos DB](create-cassandra-java.md).

## <a name="next-steps"></a>Nästa steg

Information om hur du optimerar din RU-förbrukning finns i följande artiklar:

* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)
* [Optimera kostnaden för etablerat dataflöde i Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimera kostnaden för frågor i Azure Cosmos DB](./optimize-cost-reads-writes.md)