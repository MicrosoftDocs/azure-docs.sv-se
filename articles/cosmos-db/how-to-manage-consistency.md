---
title: Hantera konsekvens i Azure Cosmos DB
description: Lär dig hur du konfigurerar och hanterar konsekvensnivåer i Azure Cosmos DB med Azure Portal, .NET SDK, Java SDK och olika andra SDK:er
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/10/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b7cab67b49196a3d50ce5483282971bbb7b9ece1
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483290"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Hantera konsekvensnivåer i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln förklarar hur du hanterar konsekvensnivåer i Azure Cosmos DB. Du lär dig att konfigurera standardkonsekvensnivån, åsidosätta standardkonsekvensen, manuellt hantera sessionstoken och förstå PBS-mått (probabilistiskt begränsad föråldring).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Konfigurera standardkonsekvensnivån

[Standardkonsekvensnivån](consistency-levels.md) är den konsekvensnivå som klienter använder som standard.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Om du vill visa eller ändra standardkonsekvensnivån loggar du in på Azure-portalen. Leta upp ditt Azure Cosmos-konto och öppna **fönstret Standardkonsekvens.** Välj den konsekvensnivå som du vill ha som den nya standarden, och välj sedan **Spara**. I Azure Portal också en visualisering av olika konsekvensnivåer med musikanteckningar. 

:::image type="content" source="./media/how-to-manage-consistency/consistency-settings.png" alt-text="Konsekvensmeny på Azure-portalen":::

# <a name="cli"></a>[CLI](#tab/cli)

Skapa ett Cosmos-konto med sessionskonsekvens och uppdatera sedan standardkonsekvensen.

```azurecli
# Create a new account with Session consistency
az cosmosdb create --name $accountName --resource-group $resourceGroupName --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name $accountName --resource-group $resourceGroupName --default-consistency-level Strong
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Skapa ett Cosmos-konto med sessionskonsekvens och uppdatera sedan standardkonsekvensen.

```azurepowershell-interactive
# Create a new account with Session consistency
New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Location $locations -Name $accountName -DefaultConsistencyLevel "Session"

# Update an existing account's default consistency
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Name $accountName -DefaultConsistencyLevel "Strong"
```

---

## <a name="override-the-default-consistency-level"></a>Åsidosätta standardkonsekvensnivån

Klienter kan åsidosätta standardkonsekvensnivån som anges av tjänsten. Konsekvensnivån kan anges för en per begäran, vilket åsidosätter standardkonsekvensnivån som angetts på kontonivå.

> [!TIP]
> Konsekvens kan bara vara **avslappnad** på begäransnivå. Om du vill gå från svagare till starkare konsekvens uppdaterar du standardkonsekvensen för Cosmos-kontot.

### <a name="net-sdk"></a><a id="override-default-consistency-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item,
        new PartitionKey(itemPartitionKey),
        requestOptions);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[Asynkrona](#tab/api-async)

   Java SDK V4 (Maven com.azure::azure-cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencyAsync)]

# <a name="sync"></a>[Synkronisera](#tab/api-sync)

   Java SDK V4 (Maven com.azure::azure-cosmos) Synkroniserings-API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySync)]

--- 

### <a name="java-v2-sdks"></a><a id="override-default-consistency-javav2"></a> Java V2-SDK:er

# <a name="async"></a>[Asynkrona](#tab/api-async)

AsyncÂ JavaÂ V2Â SDKÂ (MavenÂ com.microsoft.azure::azure-cosmosdb)

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

# <a name="sync"></a>[Synkronisera](#tab/api-sync)

SyncÂ JavaÂ V2Â SDKÂ (MavenÂ com.microsoft.azure::azure-documentdb)

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a name="python-sdk"></a><a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Använda sessionstoken

En av konsekvensnivåerna i Azure Cosmos DB *sessionskonsekvens.* Det här är standardnivån som tillämpas på Cosmos-konton som standard. När du arbetar *med sessionskonsekvens* använder klienten en sessionstoken internt med varje läs-/frågebegäran för att säkerställa att uppsättningens konsekvensnivå upprätthålls.

Om du vill hantera sessionstoken manuellt hämtar du sessionstoken från svaret och anger dem per begäran. Om du inte behöver hantera sessionstoken manuellt behöver du inte använda de här exemplen. SDK håller reda på sessionstoken automatiskt. Om du inte anger sessionstoken manuellt använder SDK som standard den senaste sessionstoken.

### <a name="net-sdk"></a><a id="utilize-session-tokens-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[Asynkrona](#tab/api-async)

   Java SDK V4 (Maven com.azure::azure-cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencySessionAsync)]

# <a name="sync"></a>[Synkronisera](#tab/api-sync)

   Java SDK V4 (Maven com.azure::azure-cosmos) Synkroniserings-API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySessionSync)]

--- 

### <a name="java-v2-sdks"></a><a id="utilize-session-tokens-javav2"></a>Java V2-SDK:er

# <a name="async"></a>[Asynkrona](#tab/api-async)

AsyncÂ JavaÂ V2Â SDKÂ (MavenÂ com.microsoft.azure::azure-cosmosdb)

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

# <a name="sync"></a>[Synkronisera](#tab/api-sync)

SyncÂ JavaÂ V2Â SDKÂ (MavenÂ com.microsoft.azure::azure-documentdb)

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a name="python-sdk"></a><a id="utilize-session-tokens-python"></a>Python SDK

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>Övervaka PBS-mått (probabilistiskt begränsad föråldring)

Hur slutlig är slutlig konsekvens? I genomsnitt kan vi erbjuda gränser för föråldrighet vad gäller versionshistorik och -tid. [**PBS-måttet (Probabilistically Bounded Staleness)**](https://pbs.cs.berkeley.edu/) försöker kvantifiera sannolikheten för föråldrering och visar det som ett mått. Om du vill visa PBS-måttet går du till ditt Azure Cosmos-konto i Azure Portal. Öppna **fönstret Mått** och välj **fliken** Konsekvens. Titta på diagrammet med namnet **Probability of strongly consistent reads based on your workload (see PBS)**(Sannolikheten för starkt konsekventa läsningar baserat på din arbetsbelastning (se PBS).

:::image type="content" source="./media/how-to-manage-consistency/pbs-metric.png" alt-text="PBS-graf i Azure-portalen":::

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du hanterar datakonflikter eller gå vidare till nästa viktiga begrepp i Azure Cosmos DB. Se följande artiklar:

* [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md)
* [Partitionering och datadistribution](./partitioning-overview.md)
* [Hantera konflikter mellan regioner](how-to-manage-conflicts.md)
* [Partitionering och datadistribution](partitioning-overview.md)
* [Kompromisser för konsekvens i modern design av distribuerade databassystem](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Hög tillgänglighet](high-availability.md)
* [Azure Cosmos DB serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
