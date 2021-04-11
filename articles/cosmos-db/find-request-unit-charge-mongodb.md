---
title: Sök efter avgift för begär ande enhet för Azure Cosmos DB-API för MongoDB-åtgärder
description: Lär dig hur du hittar avgiften för begär ande enheten (RU) för MongoDB-frågor som körs mot en Azure Cosmos-behållare. Du kan använda Azure Portal, MongoDB .NET, Java Node.js driv rutiner.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 6b2944c1d29849ea44b5afd878d5b0e030358cc5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801833"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-api-for-mongodb"></a>Sök efter begär ande enhets avgiften för åtgärder som körs i Azure Cosmos DB API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB stöder många API: er, till exempel SQL, MongoDB, Cassandra, Gremlin och Table. Varje API har en egen uppsättning databas åtgärder. Dessa åtgärder sträcker sig från enkla punkter som läser och skriver till komplexa frågor. Varje databas åtgärd förbrukar system resurser baserat på åtgärdens komplexitet.

Kostnaden för alla databasåtgärder normaliseras av Azure Cosmos DB och uttrycks i form av enheter för programbegäran (Request Units, RU:er). Avgift för begäran är de enheter för programbegäran som används av alla dina databas åtgärder. Du kan tänka dig ru: er som en prestanda valuta som sammanfattar system resurserna, till exempel CPU, IOPS och minne som krävs för att utföra de databas åtgärder som stöds av Azure Cosmos DB. Oavsett vilket API du använder för att interagera med din Azure Cosmos-behållare mäts kostnaderna alltid med RU:er. Om databas åtgärden är en skrivning, en läsning eller fråga, mäts kostnader alltid i ru: er. Mer information finns i artikeln om [enheter för programbegäran och dess överväganden](request-units.md) .

I den här artikeln beskrivs de olika sätt som du kan hitta för att köra den [begärda enheten](request-units.md) (ru) för alla åtgärder som utförs mot en behållare i Azure Cosmos DB API för MongoDB. Om du använder ett annat API, se [SQL API](find-request-unit-charge.md), [API för Cassandra](find-request-unit-charge-cassandra.md), [Gremlin-API](find-request-unit-charge-gremlin.md)och [tabell-API](find-request-unit-charge-table.md) artiklar för att hitta ru/s-avgiften.

Avgiften för RU visas med ett anpassat [databas kommando](https://docs.mongodb.com/manual/reference/command/) med namnet `getLastRequestStatistics` . Kommandot returnerar ett dokument som innehåller namnet på den senaste åtgärden som utfördes, dess begär ande avgift och dess varaktighet. Om du använder Azure Cosmos DB API för MongoDB har du flera alternativ för att hämta avgiften för RU.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-mongodb-dotnet.md#create-a-database-account) och mata in det med data, eller Välj ett befintligt konto som redan innehåller data.

1. Gå till fönstret **datautforskaren** och välj den behållare som du vill arbeta med.

1. Välj **...** bredvid behållar namnet och välj **ny fråga**.

1. Ange en giltig fråga och välj sedan **Kör fråga**.

1. Välj **fråga statistik** för att visa den faktiska begär ande avgiften för den begäran du utförde. Med den här Frågeredigeraren kan du köra och Visa avgifter för begär ande enheter för endast frågeuttryck. Du kan inte använda den här redigeraren för data behandlings kommandon som INSERT-instruktioner.

   :::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Skärm bild av en MongoDB för förfrågningar om begär anden i Azure Portal":::

1. Om du vill hämta begär ande avgifter för kommandon för att manipulera data kan du köra `getLastRequestStatistics` kommandot från ett gränssnitt baserat användar gränssnitt, till exempel Mongo Shell, [Robo 3T](mongodb-robomongo.md), [MONGODB kompass](mongodb-compass.md)eller ett vs Code-tillägg med Shell-skript.

   `db.runCommand({getLastRequestStatistics: 1})`

## <a name="use-the-mongodb-net-driver"></a>Använda MongoDB .NET-driv rutinen

När du använder den [officiella MongoDB .net-driv rutinen](https://docs.mongodb.com/ecosystem/drivers/csharp/)kan du köra kommandon genom att anropa `RunCommand` metoden för ett `IMongoDatabase` objekt. Den här metoden kräver en implementering av den `Command<>` abstrakta klassen:

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

Mer information finns i [snabb start: bygga en .NET-webbapp med hjälp av en Azure Cosmos DB-API för MongoDB](create-mongodb-dotnet.md).

## <a name="use-the-mongodb-java-driver"></a>Använda Java-drivrutinen MongoDB


När du använder den [officiella MongoDB Java-drivrutinen](https://mongodb.github.io/mongo-java-driver/)kan du köra kommandon genom att anropa- `runCommand` metoden på ett `MongoDatabase` objekt:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Mer information finns i [snabb start: bygga en webbapp med hjälp av Azure Cosmos DB API för MongoDB och Java SDK](create-mongodb-java.md).

## <a name="use-the-mongodb-nodejs-driver"></a>Använd Node.js driv rutinen för MongoDB

När du använder den [officiella MongoDB Node.js-drivrutinen](https://mongodb.github.io/node-mongodb-native/)kan du köra kommandon genom att anropa `command` metoden för ett `db` objekt:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Mer information finns i [snabb start: Migrera en befintlig MongoDB Node.js-webbapp till Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="next-steps"></a>Nästa steg

Information om hur du optimerar din RU-förbrukning finns i följande artiklar:

* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)
* [Optimera kostnaden för etablerat dataflöde i Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimera kostnaden för frågor i Azure Cosmos DB](./optimize-cost-reads-writes.md)