---
title: Sök efter avgift för begär ande enhet (RU) för en SQL-fråga i Azure Cosmos DB
description: Lär dig hur du hittar avgiften för begär ande enheten (RU) för SQL-frågor som körs mot en Azure Cosmos-behållare. Du kan använda språken Azure Portal, .NET, Java, python och Node.js för att hitta avgiften för RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: f716245d93727a0447bd1c67924ce7577c70b503
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201288"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-sql-api"></a>Hitta avgiften för enhets begär Anden för åtgärder som körs i Azure Cosmos DB SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB stöder många API: er, till exempel SQL, MongoDB, Cassandra, Gremlin och Table. Varje API har en egen uppsättning databas åtgärder. Dessa åtgärder sträcker sig från enkla punkter som läser och skriver till komplexa frågor. Varje databas åtgärd förbrukar system resurser baserat på åtgärdens komplexitet.

Kostnaden för alla databasåtgärder normaliseras av Azure Cosmos DB och uttrycks i form av enheter för programbegäran (Request Units, RU:er). Avgift för begäran är de enheter för programbegäran som används av alla dina databas åtgärder. Du kan tänka dig ru: er som en prestanda valuta som sammanfattar system resurserna, till exempel CPU, IOPS och minne som krävs för att utföra de databas åtgärder som stöds av Azure Cosmos DB. Oavsett vilket API du använder för att interagera med din Azure Cosmos-behållare mäts kostnaderna alltid med RU:er. Om databas åtgärden är en skrivning, en läsning eller fråga, mäts kostnader alltid i ru: er. Mer information finns i artikeln om [enheter för programbegäran och dess överväganden](request-units.md) .

I den här artikeln beskrivs de olika sätt som du kan hitta för att köra den [begärda enheten](request-units.md) (ru) för alla åtgärder som utförs mot en behållare i Azure Cosmos DB SQL API. Om du använder ett annat API, se [API för MongoDB](find-request-unit-charge-mongodb.md), [API för Cassandra](find-request-unit-charge-cassandra.md), [Gremlin API](find-request-unit-charge-gremlin.md)och [tabell-API](find-request-unit-charge-table.md) artiklar för att hitta ru/s-avgiften.

För närvarande kan du endast mäta den här förbrukningen genom att använda Azure Portal eller genom att granska svaret som skickats tillbaka från Azure Cosmos DB via en av SDK: erna. Om du använder SQL-API: et har du flera alternativ för att hitta RU-förbrukningen för en åtgärd mot en Azure Cosmos-behållare.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account) och mata in det med data, eller Välj ett befintligt Azure Cosmos-konto som redan innehåller data.

1. Gå till fönstret **datautforskaren** och välj den behållare som du vill arbeta med.

1. Välj **Ny SQL-fråga**.

1. Ange en giltig fråga och välj sedan **Kör fråga**.

1. Välj **fråga statistik** för att visa den faktiska begär ande avgiften för den begäran du utförde.

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Skärm bild av en begäran om SQL-fråga i Azure Portal":::

## <a name="use-the-net-sdk"></a>Använda .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

Objekt som returneras från [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) exponerar en `RequestCharge` egenskap:

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Objekt som returneras från [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) exponerar en `RequestCharge` egenskap:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Mer information finns i [snabb start: bygga en .NET-webbapp genom att använda ett SQL API-konto i Azure Cosmos DB](create-sql-api-dotnet.md).

---

## <a name="use-the-java-sdk"></a>Använda Java SDK

Objekt som returneras från [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) exponerar en `getRequestCharge()` metod:

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

Mer information finns i [snabb start: bygga ett Java-program med ett Azure Cosmos DB SQL API-konto](create-sql-api-java.md).

## <a name="use-the-nodejs-sdk"></a>Använd Node.js SDK

Objekt som returneras från [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) exponerar ett under `headers` objekt som mappar alla huvuden som returneras av det underliggande http-API: et. Begär ande avgiften är tillgänglig under `x-ms-request-charge` nyckeln:

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

Mer information finns i [snabb start: bygga en Node.js-app genom att använda ett Azure Cosmos DB SQL API-konto](create-sql-api-nodejs.md). 

## <a name="use-the-python-sdk"></a>Använda Python SDK

`CosmosClient`Objektet från [python SDK](https://pypi.org/project/azure-cosmos/) visar en `last_response_headers` ord lista som mappar alla huvuden som returneras av det underliggande http-API: t för den senaste åtgärden som utfördes. Begär ande avgiften är tillgänglig under `x-ms-request-charge` nyckeln:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Mer information finns i [snabb start: bygga en python-app med ett Azure Cosmos DB SQL API-konto](create-sql-api-python.md). 

## <a name="next-steps"></a>Nästa steg

Information om hur du optimerar din RU-förbrukning finns i följande artiklar:

* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)
* [Optimera kostnaden för etablerat dataflöde i Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimera kostnaden för frågor i Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Skala etablerat dataflöde globalt](./request-units.md)
* [Etablera dataflöde på containrar och databaser](set-throughput.md)
* [Etablera data flöde för en behållare](how-to-provision-container-throughput.md)
* [Övervaka och felsöka med mått i Azure Cosmos DB](use-metrics.md)
