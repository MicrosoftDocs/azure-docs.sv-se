---
title: Migrera ditt program till att använda Azure Cosmos DB Java SDK v4 (com. Azure. Cosmos)
description: 'Lär dig hur du uppgraderar ditt befintliga Java-program från att använda de äldre Azure Cosmos DB Java SDK: erna till det nyare Java SDK 4,0-paketet (com. Azure. Cosmos) för Core-API (SQL).'
author: anfeldma-ms
ms.custom: devx-track-java
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2020
ms.reviewer: sngun
ms.openlocfilehash: 92a9abec36bd75c594c67843286bf8fa067d7dba
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658545"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Migrera ditt program till att använda Azure Cosmos DB Java SDK v4
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]  
> Mer information om det här SDK: n finns i Azure Cosmos DB Java SDK v4- [viktig information](sql-api-sdk-java-v4.md), [maven-lagringsplats](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB Java SDK v4- [prestanda tips](performance-tips-java-sdk-v4-sql.md)och Azure Cosmos DB [fel söknings guide](troubleshoot-java-sdk-v4-sql.md)för Java SDK v4.
>

Den här artikeln beskriver hur du uppgraderar ditt befintliga Java-program som använder en äldre Azure Cosmos DB Java SDK till den nyare Azure Cosmos DB Java SDK 4,0 för Core (SQL) API. Azure Cosmos DB Java SDK v4 motsvarar `com.azure.cosmos` paketet. Du kan använda instruktionerna i det här dokumentet om du migrerar ditt program från någon av följande Azure Cosmos DB Java-SDK: er: 

* Synkronisera Java SDK 2. x. x
* Asynkron Java SDK 2. x. x
* Java SDK 3. x. x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Azure Cosmos DB Java SDK: s och paket mappningar

I följande tabell visas olika Azure Cosmos DB Java-SDK: er, paket namnet och versions informationen:

| Java SDK| Utgivningsdatum | Paketerade API: er   | Maven jar  | Java-paketets namn  |API-referens   | Viktig information  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Async 2. x. x  | Juni 2018    | Asynkron (RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Viktig information](sql-api-sdk-async-java.md) |
| Synkronisera 2. x. x     | Sept 2018    | Synkronisera   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Viktig information](sql-api-sdk-java.md)  |
| 3. x. x    | Juli 2019    | Async (reaktor)/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4,0   | Juni 2020   | Async (reaktor)/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | [API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1/index.html)  |

## <a name="sdk-level-implementation-changes"></a>Implementerings ändringar för SDK-nivå

Följande är viktiga implementerings skillnader mellan olika SDK: er:

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>RxJava ersätts med reaktor i Azure Cosmos DB Java SDK-versioner 3. x och 4,0

Om du inte känner till asynkron programmering eller reaktiv programmering kan du läsa mer i [reaktor mönster guiden](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) för att få en introduktion till asynkron programmering och projekt reaktorer. Den här guiden kan vara användbar om du har använt Azure Cosmos DB Sync Java SDK 2. x eller Azure Cosmos DB Java SDK 3. x. x Sync API tidigare.

Om du har använt Azure Cosmos DB async Java SDK 2. x, och du planerar att migrera till 4,0 SDK, kan du läsa mer i [reaktor vs RxJava-guiden](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) för att få hjälp med att konvertera RxJava-kod till reaktor.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB Java SDK v4 har direkt anslutnings läge i både asynkrona och synkroniserade API: er

Om du har använt Azure Cosmos DB Sync Java SDK 2. x. x, Observera att det direkta anslutnings läget som baseras på TCP (i stället för HTTP) har implementerats i Azure Cosmos DB Java SDK 4,0 för både asynkrona API: er och synkronisering.

## <a name="api-level-changes"></a>Ändringar i API-nivå

Följande är API-nivå ändringar i Azure Cosmos DB Java SDK 4. x. x jämfört med tidigare SDK: er (Java SDK 3. x, asynkront Java SDK 2. x. x, och synkroniserar Java SDK 2. x. x):

:::image type="content" source="./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png" alt-text="Azure Cosmos DB namngivnings konventioner för Java SDK":::

* Azure Cosmos DB Java SDK 3. x och 4,0 hänvisar till klient resurserna som `Cosmos<resourceName>` . Till exempel,,, `CosmosClient` `CosmosDatabase` `CosmosContainer` . I version 2. x. x har Azure Cosmos DB Java SDK: er inget enhetligt namngivnings schema.

* Azure Cosmos DB Java SDK 3. x. x och 4,0 erbjuder både synkronisering och asynkrona API: er.

  * **Java SDK 4,0** : alla klasser tillhör Sync-API om inte klass namnet läggs till med `Async` efter `Cosmos` .

  * **Java SDK 3. x**: alla klasser tillhör det asynkrona API: et om inte klass namnet läggs till med `Async` efter `Cosmos` .

  * **Asynkron Java SDK 2. x**: klass namn liknar synkronisering Java SDK 2. x. x, men namnet börjar med *async*.

### <a name="hierarchical-api-structure"></a>Hierarkisk API-struktur

Azure Cosmos DB Java SDK 4,0 och 3. x inför en hierarkisk API-struktur som ordnar klienter, databaser och behållare på ett kapslat sätt som visas i följande 4,0 SDK-kodfragment:

```java
CosmosContainer container = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");

```

I version 2. x av Azure Cosmos DB Java SDK utförs alla åtgärder på resurser och dokument genom klient instansen.

### <a name="representing-documents"></a>Representerar dokument

I Azure Cosmos DB Java SDK 4,0 är anpassade POJO och `JsonNodes` de två alternativen för att läsa och skriva dokumenten från Azure Cosmos dB.

I Azure Cosmos DB Java SDK 3. x, `CosmosItemProperties` exponeras objektet av det offentliga API: t och hanteras som en dokument representation. Den här klassen exponeras inte längre offentligt i version 4,0.

### <a name="imports"></a>Kina

* Azure Cosmos DB Java SDK 4,0-paket börjar med `com.azure.cosmos`
* Azure Cosmos DB Java SDK 3. x. x-paket börjar med `com.azure.data.cosmos`
* Azure Cosmos DB Java SDK 2. x. x Sync API-paket börjar med `com.microsoft.azure.documentdb`

* Azure Cosmos DB Java SDK 4,0 placerar flera klasser i ett kapslat paket `com.azure.cosmos.models` . Några av dessa paket är:

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * Asynkrona API-analoga för alla ovanstående paket
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode` ... annat.

### <a name="accessors"></a>Accessorer

Azure Cosmos DB Java SDK 4,0 visar `get` och `set` metoder för åtkomst till instans medlemmar. Till exempel `CosmosContainer` har instansen `container.getId()` och- `container.setId()` metoderna.

Detta skiljer sig från Azure Cosmos DB Java SDK 3. x. x som visar ett Fluent-gränssnitt. Till exempel har en `CosmosSyncContainer` instans `container.id()` som är överbelastad för att hämta eller ange `id` värdet.

## <a name="code-snippet-comparisons"></a>Jämförelse av kodfragment

### <a name="create-resources"></a>Skapa resurser

Följande kodfragment visar skillnaderna i hur resurser skapas mellan de asynkrona API: erna 4,0, 3. x. x och 2. x. x Sync-API: er:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0-asynkront API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateJavaSDKv4ResourceAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Asynkront API för Java SDK 3. x. x](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x Sync-API](#tab/java-v2-sync)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.GetDefault();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));

//  Create document client
//  <CreateDocumentClient>
client = new DocumentClient("your.hostname", "your.masterkey", defaultPolicy, ConsistencyLevel.Eventual)

// Create database with specified name
Database databaseDefinition = new Database();
databaseDefinition.setId("YourDatabaseName");
ResourceResponse<Database> databaseResourceResponse = client.createDatabase(databaseDefinition, new RequestOptions());

// Read database with specified name
String databaseLink = "dbs/YourDatabaseName";
databaseResourceResponse = client.readDatabase(databaseLink, new RequestOptions());
Database database = databaseResourceResponse.getResource();

// Create container with specified name
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="item-operations"></a>Objekt åtgärder

Följande kodfragment visar skillnaderna i hur objekt åtgärder utförs mellan asynkrona API: erna 4,0, 3. x. x och 2. x. x Sync-API: er:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0-asynkront API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemOpsAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Asynkront API för Java SDK 3. x. x](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x Sync-API](#tab/java-v2-sync)

```java
//  Container is created. Generate documents to insert.
Document document = new Document();
document.setId("YourDocumentId");
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```
---

### <a name="indexing"></a>Indexering

Följande kodfragment visar skillnaderna i hur indexeringen skapas mellan asynkrona API: er för 4,0, 3. x. x och 2. x. x Sync-API: er:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0-asynkront API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Asynkront API för Java SDK 3. x. x](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.includedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x Sync-API](#tab/java-v2-sync)

```java
// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.Consistent); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Create container with specified name and indexing policy
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection.setIndexingPolicy(indexingPolicy);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="stored-procedures"></a>Lagrade procedurer

Följande kodfragment visar skillnaderna i hur lagrade procedurer skapas mellan asynkrona API: er för 4,0, 3. x. x och 2. x. x Sync-API: er:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0-asynkront API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateSprocAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Asynkront API för Java SDK 3. x. x](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x Sync-API](#tab/java-v2-sync)

```java
logger.info("Creating stored procedure...\n");

String sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
    "var documentToCreate = {\"id\":\"test_doc\"}\n" +
    "var context = getContext();\n" +
    "var collection = context.getCollection();\n" +
    "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
    "    function (err, documentCreated) {\n" +
    "if (err) throw new Error('Error' + err.message);\n" +
    "context.getResponse().setBody(documentCreated.id)\n" +
    "});\n" +
    "if (!accepted) return;\n" +
    "}";
StoredProcedure storedProcedureDef = new StoredProcedure();
storedProcedureDef.setId(sprocId);
storedProcedureDef.setBody(sprocBody);
StoredProcedure storedProcedure = client.createStoredProcedure(documentCollection.getSelfLink(), storedProcedureDef, new RequestOptions())
                                        .getResource();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

StoredProcedureResponse storedProcedureResponse =
    client.executeStoredProcedure(storedProcedure.getSelfLink(), options, null);
logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
    sprocId,
    storedProcedureResponse.getResponseAsString(),
    storedProcedureResponse.getStatusCode(),
    storedProcedureResponse.getRequestCharge()));
```
---

### <a name="change-feed"></a>Ändringsfeed

Följande kodfragment visar skillnaderna i hur ändring av flödes åtgärder körs mellan asynkrona API: erna 4,0 och 3. x. x:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0-asynkront API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateCFAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Asynkront API för Java SDK 3. x. x](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x Sync-API](#tab/java-v2-sync)

* Den här funktionen stöds inte från Java SDK v2-synkronisering. 
---

### <a name="container-level-time-to-livettl"></a>TTL-värde (Time-to-Live) för container nivå

Följande kodfragment visar skillnaderna i hur du skapar tid för data i behållaren med hjälp av asynkrona API: erna 4,0, 3. x. x och 2. x. x Sync-API: er:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0-asynkront API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateContainerTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Asynkront API för Java SDK 3. x. x](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x Sync-API](#tab/java-v2-sync)

```java
DocumentCollection documentCollection;

// Create a new container with TTL enabled with default expiration value
documentCollection.setDefaultTimeToLive(90 * 60 * 60 * 24);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="item-level-time-to-livettl"></a>TTL (Time-to-Live) på objekt nivå

Följande kodfragment visar skillnaderna i hur du skapar tid för att leva för ett objekt med hjälp av asynkrona API: er för 4,0, 3. x. x och 2. x. x Sync-API: er:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0-asynkront API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLClassAsync)]

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Asynkront API för Java SDK 3. x. x](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x Sync-API](#tab/java-v2-sync)

```java
Document document = new Document();
document.setId("YourDocumentId");
document.setTimeToLive(60 * 60 * 24 * 30 ); // Expire document in 30 days
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```
---

## <a name="next-steps"></a>Nästa steg

* [Bygg en Java-app](create-sql-api-java.md) för att hantera Azure Cosmos DB SQL API-data med v4 SDK
* Lär dig mer om de [reaktorbaserade Java SDK: erna](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)
* Lär dig mer om att konvertera RxJava async Code till reaktor asynkron kod med [reaktor vs RxJava-guiden](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md)
