---
title: 'Registrera och använda lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB SDK: er'
description: Lär dig hur du registrerar och anropar lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB SDK:er
author: timsander1
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tisande
ms.custom: devx-track-python, devx-track-javascript
ms.openlocfilehash: fccd3209d88ecd0f7e2baa06a55555a1370c4ec4
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586315"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Registrera och använda lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB

SQL API i Azure Cosmos DB har stöd för registrering och anrop av lagrade procedurer, utlösare och användardefinierade funktioner (UDF) som har skrivits i JavaScript. Du kan använda SQL API [.net](sql-api-sdk-dotnet.md), [.net Core](sql-api-sdk-dotnet-core.md), [java](sql-api-sdk-java.md), [Java Script](sql-api-sdk-node.md), [Node.js](sql-api-sdk-node.md)eller [python](sql-api-sdk-python.md) SDK: er för att registrera och anropa de lagrade procedurerna. När du har definierat en eller flera lagrade procedurer, utlösare och användardefinierade funktioner kan du läsa in och visa dem i [Azure Portal](https://portal.azure.com/) med hjälp av datautforskaren.

## <a name="how-to-run-stored-procedures"></a><a id="stored-procedures"></a>Köra lagrade procedurer

Lagrade procedurer är skrivna med JavaScript. De kan skapa, uppdatera, läsa, fråga och ta bort objekt i en Azure Cosmos-container. Mer information om hur du skriver lagrade procedurer i Azure Cosmos DB finns i [så här skriver du lagrade procedurer i Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures) artikeln.

Följande exempel visar hur du registrerar och anropar en lagrad procedur med hjälp av Azure Cosmos DB SDK:er. Se [Skapa ett dokument](how-to-write-stored-procedures-triggers-udfs.md#create-an-item) eftersom källan till denna lagrade procedur är sparad som `spCreateToDoItem.js`.

> [!NOTE]
> När du kör en lagrad procedur med partitionerade containrar måste ett partitionsnyckelvärde anges i alternativen för begäran. Lagrade procedurer är alltid begränsade till en partitionsnyckel. Objekt som har ett annan partitionsnyckelvärde visas inte för den lagrade proceduren. Detta gäller även för utlösare.

### <a name="stored-procedures---net-sdk-v2"></a>Lagrade procedurer – .NET SDK v2

I följande exempel visas hur du registrerar en lagrad procedur med hjälp av .NET SDK v2:

```csharp
string storedProcedureId = "spCreateToDoItems";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

Följande kod visar hur du anropar en lagrad procedur med hjälp av .NET SDK v2:

```csharp
dynamic[] newItems = new dynamic[]
{
    new {
        category = "Personal",
        name = "Groceries",
        description = "Pick up strawberries",
        isComplete = false
    },
    new {
        category = "Personal",
        name = "Doctor",
        description = "Make appointment for check up",
        isComplete = false
    }
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, new[] { newItems });
```

### <a name="stored-procedures---net-sdk-v3"></a>Lagrade procedurer – .NET SDK v3

I följande exempel visas hur du registrerar en lagrad procedur med .NET SDK v3:

```csharp
string storedProcedureId = "spCreateToDoItems";
StoredProcedureResponse storedProcedureResponse = await client.GetContainer("myDatabase", "myContainer").Scripts.CreateStoredProcedureAsync(new StoredProcedureProperties
{
    Id = storedProcedureId,
    Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
});
```

Följande kod visar hur du anropar en lagrad procedur med .NET SDK v3:

```csharp
dynamic[] newItems = new dynamic[]
{
    new {
        category = "Personal",
        name = "Groceries",
        description = "Pick up strawberries",
        isComplete = false
    },
    new {
        category = "Personal",
        name = "Doctor",
        description = "Make appointment for check up",
        isComplete = false
    }
};

var result = await client.GetContainer("database", "container").Scripts.ExecuteStoredProcedureAsync<string>("spCreateToDoItem", new PartitionKey("Personal"), new[] { newItems });
```

### <a name="stored-procedures---java-sdk"></a>Lagrade procedurer – Java SDK

Följande exempel visar hur du registrerar en lagrad procedur med hjälp av Java-SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItems'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItems.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

Följande kod visar hur du anropar en lagrad procedur med hjälp av Java-SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItems");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

List<ToDoItem> ToDoItems = new ArrayList<ToDoItem>();

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

ToDoItems.add(newItem)

newItem.category = "Personal";
newItem.name = "Doctor";
newItem.description = "Make appointment for check up";
newItem.isComplete = false;

ToDoItems.add(newItem)

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { ToDoItems };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        successfulCompletionLatch.countDown();
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Lagrade procedurer – JavaScript SDK

Följande exempel visar hur du registrerar en lagrad procedur med hjälp av JavaScript-SDK

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItems";
await container.scripts.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

Följande kod visar hur du anropar en lagrad procedur med hjälp av JavaScript-SDK:

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItems";
const {body: result} = await container.scripts.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Lagrade procedurer – Python SDK

I följande exempel visar hur du registrerar en lagrad procedur med hjälp av Python-SDK

```python
with open('../js/spCreateToDoItems.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
sproc_definition = {
    'id': 'spCreateToDoItems',
    'serverScript': file_contents,
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
sproc = container.create_stored_procedure(container_link, sproc_definition)
```

Följande kod visar hur du anropar en lagrad procedur med hjälp av Python-SDK

```python
sproc_link = 'dbs/myDatabase/colls/myContainer/sprocs/spCreateToDoItems'
new_item = [{
    'category':'Personal',
    'name':'Groceries',
    'description':'Pick up strawberries',
    'isComplete': False
}]
container.execute_stored_procedure(sproc_link, new_item, {'partitionKey': 'Personal'}
```

## <a name="how-to-run-pre-triggers"></a><a id="pre-triggers"></a>Så här kör du förutlösare

Följande exempel visar hur du registrerar och anropar en förutlösare med hjälp av Azure Cosmos DB SDK:er. Se [Exempel på förutlösare](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers) eftersom källan till denna förutlösare är sparad som `trgPreValidateToDoItemTimestamp.js`.

Vid körning skickas förutlösare i RequestOptions-objektet genom att ange `PreTriggerInclude`. Sedan skickas namnet på utlösaren i ett listobjekt.

> [!NOTE]
> Även om namnet på utlösaren skickas som en lista kan du fortfarande endast köra en utlösare per åtgärd.

### <a name="pre-triggers---net-sdk-v2"></a>För-utlösare – .NET SDK v2

Följande kod visar hur du registrerar en för utlösare med .NET SDK v2:

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Följande kod visar hur du anropar en för utlösare med .NET SDK v2:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---net-sdk-v3"></a>För-utlösare – .NET SDK v3

Följande kod visar hur du registrerar en för utlösare med .NET SDK v3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPreValidateToDoItemTimestamp",
    Body = File.ReadAllText("@..\js\trgPreValidateToDoItemTimestamp.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
});
```

Följande kod visar hur du anropar en för utlösare med .NET SDK v3:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PreTriggers = new List<string> { "trgPreValidateToDoItemTimestamp" } });
```

### <a name="pre-triggers---java-sdk"></a>Förutlösare – Java SDK

I följande kod visar hur du registrerar en förutlösare med hjälp av Java-SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

I följande kod visar hur du anropar en förutlösare med hjälp av Java-SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>Förutlösare – JavaScript SDK

I följande kod visar hur du registrerar en förutlösare med hjälp av JavaScript-SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

I följande kod visar hur du anropar en förutlösare med hjälp av JavaScript-SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>Förutlösare – Python SDK

I följande kod visar hur du registrerar en förutlösare med hjälp av Python-SDK:

```python
with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPreValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Pre,
    'triggerOperation': documents.TriggerOperation.Create
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
trigger = container.create_trigger(container_link, trigger_definition)
```

I följande kod visar hur du anropar en förutlösare med hjälp av Python-SDK:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
container.create_item(container_link, item, {
                  'pre_trigger_include': 'trgPreValidateToDoItemTimestamp'})
```

## <a name="how-to-run-post-triggers"></a><a id="post-triggers"></a>Så här kör du efterutlösare

Följande exempel visar hur du registrerar en efterutlösare med hjälp av Azure Cosmos DB SDK:er. Se [Exempel på efterutlösare](how-to-write-stored-procedures-triggers-udfs.md#post-triggers) eftersom källan till denna efterutlösare är sparad som `trgPostUpdateMetadata.js`.

### <a name="post-triggers---net-sdk-v2"></a>Efter utlösare – .NET SDK v2

Följande kod visar hur du registrerar en post-utlösare med hjälp av .NET SDK v2:

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Följande kod visar hur du anropar en post-utlösare med .NET SDK v2:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

RequestOptions options = new RequestOptions { PostTriggerInclude = new List<string> { "trgPostUpdateMetadata" } };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---net-sdk-v3"></a>Efter utlösare – .NET SDK v3

Följande kod visar hur du registrerar en efter utlösare med .NET SDK v3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPostUpdateMetadata",
    Body = File.ReadAllText(@"..\js\trgPostUpdateMetadata.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
});
```

Följande kod visar hur du anropar en post-utlösare med .NET SDK v3:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PostTriggers = new List<string> { "trgPostUpdateMetadata" } });
```

### <a name="post-triggers---java-sdk"></a>Efterutlösare – Java SDK

I följande kod visar hur du registrerar en efterutlösare med hjälp av Java-SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

I följande kod visar hur du anropar en efterutlösare med hjälp av Java-SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>Efterutlösare – JavaScript SDK

I följande kod visar hur du registrerar en efterutlösare med hjälp av JavaScript-SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

I följande kod visar hur du anropar en efterutlösare med hjälp av JavaScript-SDK:

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>Efterutlösare – Python SDK

I följande kod visar hur du registrerar en efterutlösare med hjälp av Python-SDK:

```python
with open('../js/trgPostUpdateMetadata.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPostUpdateMetadata',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Post,
    'triggerOperation': documents.TriggerOperation.Create
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
trigger = container.create_trigger(container_link, trigger_definition)
```

I följande kod visar hur du anropar en efterutlösare med hjälp av Python-SDK:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'name': 'artist_profile_1023', 'artist': 'The Band',
        'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
container.create_item(container_link, item, {
                  'post_trigger_include': 'trgPostUpdateMetadata'})
```

## <a name="how-to-work-with-user-defined-functions"></a><a id="udfs"></a>Arbeta med användardefinierade funktioner

Följande exempel visar hur du registrerar en användardefinierad funktion med hjälp av Azure Cosmos DB SDK:er. Se [Exempel på användardefinierad funktion](how-to-write-stored-procedures-triggers-udfs.md#udfs) eftersom källan till denna användardefinierade funktion är sparad som `udfTax.js`.

### <a name="user-defined-functions---net-sdk-v2"></a>Användardefinierade funktioner – .NET SDK v2

Följande kod visar hur du registrerar en användardefinierad funktion med hjälp av .NET SDK v2:

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js")
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

Följande kod visar hur du anropar en användardefinierad funktion med hjälp av .NET SDK v2:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---net-sdk-v3"></a>Användardefinierade funktioner – .NET SDK v3

Följande kod visar hur du registrerar en användardefinierad funktion med .NET SDK v3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateUserDefinedFunctionAsync(new UserDefinedFunctionProperties
{
    Id = "Tax",
    Body = File.ReadAllText(@"..\js\Tax.js")
});
```

Följande kod visar hur du anropar en användardefinierad funktion med .NET SDK v3:

```csharp
var iterator = client.GetContainer("database", "container").GetItemQueryIterator<dynamic>("SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000");
while (iterator.HasMoreResults)
{
    var results = await iterator.ReadNextAsync();
    foreach (var result in results)
    {
        //iterate over results
    }
}
```

### <a name="user-defined-functions---java-sdk"></a>Användardefinierade funktioner – Java SDK

I följande kod visar hur du registrerar en användardefinierad funktion med hjälp av Java-SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

I följande kod visar hur du anropar en användardefinierad funktion med hjälp av Java-SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>Användardefinierade funktioner – JavaScript SDK

I följande kod visar hur du registrerar en användardefinierad funktion med hjälp av JavaScript-SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

I följande kod visar hur du anropar en användardefinierad funktion med hjälp av JavaScript-SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Användardefinierade funktioner – Python SDK

I följande kod visar hur du registrerar en användardefinierad funktion med hjälp av Python-SDK:

```python
with open('../js/udfTax.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
udf_definition = {
    'id': 'Tax',
    'serverScript': file_contents,
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
udf = container.create_user_defined_function(container_link, udf_definition)
```

I följande kod visar hur du anropar en användardefinierad funktion med hjälp av Python-SDK:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(container.query_items(
    container_link, 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om begrepp och hur du skriver och använder lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB:

- [Arbeta med lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Arbeta med det språkintegrerade fråge-API:et för JavaScript i Azure Cosmos DB](javascript-query-api.md)
- [Skriva lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md)
- [Skriva lagrade procedurer och utlösare med hjälp fråge-API:et för Javascript i Azure Cosmos DB](how-to-write-javascript-query-api.md)
