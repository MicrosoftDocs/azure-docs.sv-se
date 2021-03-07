---
title: Azure Cosmos DB binding för functions 2. x och högre
description: Lär dig hur du använder Azure Cosmos DB binding i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 21ca30b24c4824a2d303d02f3df712328885e199
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435213"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x-and-higher"></a>Azure Cosmos DB bindning för Azure Functions 2. x och högre

Azure Cosmos DB-indatabindningen använder SQL API för att hämta en eller flera Azure Cosmos DB-dokument och skickar dem till indataparametern för funktionen. Dokument-ID:t och frågeparametrarna kan fastställas baserat på den utlösare som anropar funktionen.

Information om konfiguration och konfigurations information finns i [översikten](./functions-bindings-cosmosdb-v2.md).

> [!NOTE]
> Om samlingen är [partitionerad](../cosmos-db/partitioning-overview.md#logical-partitions)måste Sök åtgärder också ange värdet för partitionsnyckel.
>

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Det här avsnittet innehåller följande exempel:

* [Köa utlösare, slå upp ID från JSON](#queue-trigger-look-up-id-from-json-c)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-c)
* [HTTP-utlösare, slå upp ID från flödes data](#http-trigger-look-up-id-from-route-data-c)
* [HTTP-utlösare, slå upp ID från routa data med SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP-utlösare, hämta flera dokument med SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP-utlösare, hämta flera dokument med DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Exemplen refererar till en enkel `ToDoItem` Typ:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        [JsonProperty("id")]
        public string Id { get; set; }
        
        [JsonProperty("partitionKey")]
        public string PartitionKey { get; set; }
        
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Köa utlösare, slå upp ID från JSON 

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av ett Queue-meddelande som innehåller ett JSON-objekt. Queue-utlösaren parsar JSON till ett objekt av typen `ToDoItemLookup` , som innehåller det ID och det partitionsnyckel som ska sökas upp. Detta ID och partitionerings nyckel värde används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-utlösare, leta upp ID från frågesträng

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange det ID och det partitionsnyckel som ska sökas upp. Detta ID och partitionerings nyckel värde används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

>[!NOTE]
>Parametern HTTP-frågesträng är Skift läges känslig.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från flödes data

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder flödes data för att ange det ID och det partitionsnyckel som ska sökas upp. Detta ID och partitionerings nyckel värde används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-utlösare, slå upp ID från routa data med SqlQuery

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder flödes data för att ange det ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

Exemplet visar hur du använder ett bindnings uttryck i `SqlQuery` parametern. Du kan skicka flödes data till `SqlQuery` parametern som visas, men för närvarande kan [du inte skicka frågesträngs värden](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).

> [!NOTE]
> Om du bara behöver fråga efter ID: t rekommenderar vi att du använder ett uppslag, precis som i [föregående exempel](#http-trigger-look-up-id-from-query-string-c), eftersom det förbrukar mindre [enheter för programbegäran](../cosmos-db/request-units.md). Punkt läsnings åtgärder (GET) är [mer effektiva](../cosmos-db/optimize-cost-reads-writes.md) än frågor efter ID.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-utlösare, hämta flera dokument med SqlQuery

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. Frågan anges i `SqlQuery` egenskapen Attribute.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-utlösare, hämta flera dokument med DocumentClient

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. I koden används en `DocumentClient` instans som tillhandahålls av Azure Cosmos DB bindningen för att läsa en lista över dokument. `DocumentClient`Instansen kan också användas för Skriv åtgärder.

> [!NOTE]
> Du kan också använda [IDocumentClient](/dotnet/api/microsoft.azure.documents.idocumentclient) -gränssnittet för att göra testningen enklare.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Det här avsnittet innehåller följande exempel:

* [Köa utlösare, leta upp ID från sträng](#queue-trigger-look-up-id-from-string-c-script)
* [Köa utlösare, hämta flera dokument med SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP-utlösare, slå upp ID från flödes data](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP-utlösare, hämta flera dokument med SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-utlösare, hämta flera dokument med DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Exempel på HTTP-utlösare hänvisar till en enkel `ToDoItem` Typ:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Köa utlösare, leta upp ID från sträng

I följande exempel visas en Cosmos DB indata-bindning i en *function.jspå* fil och en [C#-skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen läser ett enda dokument och uppdaterar dokumentets text värde.

Här är bindnings data i *function.jspå* filen:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är C#-skript koden:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Köa utlösare, hämta flera dokument med SqlQuery

I följande exempel visas en Azure Cosmos DB indata-bindning i en *function.jspå* fil och en [C#-skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen hämtar flera dokument som anges av en SQL-fråga med hjälp av en Queue-utlösare för att anpassa frågeparametrar.

Utlösaren för kön innehåller en parameter `departmentId` . Ett Queue-meddelande i `{ "departmentId" : "Finance" }` returnerar alla poster för ekonomi avdelningen.

Här är bindnings data i *function.jspå* filen:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är C#-skript koden:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-utlösare, leta upp ID från frågesträng

I följande exempel visas en [C#-skript funktion](functions-reference-csharp.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange det ID och det partitionsnyckel som ska sökas upp. Detta ID och partitionerings nyckel värde används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

Här är *function.jspå* filen:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Här är C#-skript koden:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från flödes data

I följande exempel visas en [C#-skript funktion](functions-reference-csharp.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder flödes data för att ange det ID och det partitionsnyckel som ska sökas upp. Detta ID och partitionerings nyckel värde används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

Här är *function.jspå* filen:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Här är C#-skript koden:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-utlösare, hämta flera dokument med SqlQuery

I följande exempel visas en [C#-skript funktion](functions-reference-csharp.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. Frågan anges i `SqlQuery` egenskapen Attribute.

Här är *function.jspå* filen:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Här är C#-skript koden:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-utlösare, hämta flera dokument med DocumentClient

I följande exempel visas en [C#-skript funktion](functions-reference-csharp.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. I koden används en `DocumentClient` instans som tillhandahålls av Azure Cosmos DB bindningen för att läsa en lista över dokument. `DocumentClient`Instansen kan också användas för Skriv åtgärder.

Här är *function.jspå* filen:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Här är C#-skript koden:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="java"></a>[Java](#tab/java)

Det här avsnittet innehåller följande exempel:

* [HTTP-utlösare, leta upp ID från frågesträngparametern-String-parameter](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [HTTP-utlösare, leta upp ID från frågesträng-POJO-parameter](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [HTTP-utlösare, slå upp ID från flödes data](#http-trigger-look-up-id-from-route-data-java)
* [HTTP-utlösare, slå upp ID från routa data med SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [HTTP-utlösare, hämta flera dokument från väg data med SqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Exemplen refererar till en enkel `ToDoItem` Typ:

```java
public class ToDoItem {

  private String id;
  private String description;

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }

  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}
```

<a id="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>HTTP-utlösare, leta upp ID från frågesträngparametern-String-parameter

I följande exempel visas en Java-funktion som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange det ID och det partitionsnyckel som ska sökas upp. Detta ID och nyckel värde används för att hämta ett dokument från den angivna databasen och samlingen i sträng format.

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@CosmosDBInput` anteckningen för funktions parametrar vars värde kommer från Cosmos dB.  Den här anteckningen kan användas med inbyggda Java-typer, Pojo eller null-värden med hjälp av `Optional<T>` .

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>HTTP-utlösare, leta upp ID från frågesträng-POJO-parameter

I följande exempel visas en Java-funktion som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange det ID och det partitionsnyckel som ska sökas upp. Det ID-och partitionsnyckel som används för att hämta ett dokument från den angivna databasen och samlingen. Dokumentet konverteras sedan till en instans av POJO som ```ToDoItem``` tidigare skapats och skickades som ett argument till funktionen.

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

<a id="http-trigger-look-up-id-from-route-data-java"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från flödes data

I följande exempel visas en Java-funktion som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en Route-parameter för att ange det ID och det partitionsnyckel som ska sökas upp. Detta ID och nyckel värde används för att hämta ett dokument från den angivna databasen och samlingen, och returnerar det som en ```Optional<String>``` .

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-utlösare, slå upp ID från routa data med SqlQuery

I följande exempel visas en Java-funktion som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en väg parameter för att ange det ID som ska sökas upp. Detta ID används för att hämta ett dokument från den angivna databasen och samlingen, vilket konverterar resultat uppsättningen till ett ```ToDoItem[]``` , eftersom många dokument kan returneras, beroende på frågevillkor.

> [!NOTE]
> Om du bara behöver fråga efter ID: t rekommenderar vi att du använder ett uppslag, precis som i [föregående exempel](#http-trigger-look-up-id-from-query-string---pojo-parameter-java), eftersom det förbrukar mindre [enheter för programbegäran](../cosmos-db/request-units.md). Punkt läsnings åtgärder (GET) är [mer effektiva](../cosmos-db/optimize-cost-reads-writes.md) än frågor efter ID.
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>HTTP-utlösare, hämta flera dokument från väg data med SqlQuery

I följande exempel visas en Java-funktion som hämtar flera dokument. Funktionen utlöses av en HTTP-begäran som använder en Route-parameter ```desc``` för att ange strängen som ska sökas efter i ```description``` fältet. Sök termen används för att hämta en samling dokument från den angivna databasen och samlingen, vilket konverterar resultat uppsättningen till en ```ToDoItem[]``` och skickar den som ett argument till funktionen.

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Det här avsnittet innehåller följande exempel som läser ett enda dokument genom att ange ett ID-värde från olika källor:

* [Köa utlösare, slå upp ID från JSON](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP-utlösare, slå upp ID från flödes data](#http-trigger-look-up-id-from-route-data-javascript)
* [Köa utlösare, hämta flera dokument med SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Köa utlösare, slå upp ID från JSON

I följande exempel visas en Cosmos DB indata-bindning i en *function.jsi* filen och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen läser ett enda dokument och uppdaterar dokumentets text värde.

Här är bindnings data i *function.jspå* filen:

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är JavaScript-koden:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-utlösare, leta upp ID från frågesträng

I följande exempel visas en [JavaScript-funktion](functions-reference-node.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange det ID och det partitionsnyckel som ska sökas upp. Detta ID och partitionerings nyckel värde används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

Här är *function.jspå* filen:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Här är JavaScript-koden:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från flödes data

I följande exempel visas en [JavaScript-funktion](functions-reference-node.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder flödes data för att ange det ID och det partitionsnyckel som ska sökas upp. Detta ID och partitionerings nyckel värde används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

Här är *function.jspå* filen:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Här är JavaScript-koden:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Köa utlösare, hämta flera dokument med SqlQuery

I följande exempel visas en Azure Cosmos DB indata-bindning i en *function.jsi* filen och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen hämtar flera dokument som anges av en SQL-fråga med hjälp av en Queue-utlösare för att anpassa frågeparametrar.

Utlösaren för kön innehåller en parameter `departmentId` . Ett Queue-meddelande i `{ "departmentId" : "Finance" }` returnerar alla poster för ekonomi avdelningen.

Här är bindnings data i *function.jspå* filen:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function (context, input) {
  var documents = context.bindings.documents;
  for (var i = 0; i < documents.length; i++) {
    var document = documents[i];
    // operate on each document
  }
  context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* [Köa utlösare, slå upp ID från JSON](#queue-trigger-look-up-id-from-json-ps)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-id-query-string-ps)
* [HTTP-utlösare, slå upp ID från flödes data](#http-trigger-id-route-data-ps)
* [Köa utlösare, hämta flera dokument med SqlQuery](#queue-trigger-multiple-docs-sqlquery-ps)

### <a name="queue-trigger-look-up-id-from-json"></a>Köa utlösare, slå upp ID från JSON

Följande exempel visar hur du läser och uppdaterar ett enda Cosmos DB-dokument. Dokumentets unika identifierare tillhandahålls via JSON-värdet i ett Queue-meddelande.

Den Cosmos DB indata-bindningen visas först i listan över bindningar som finns i funktionens konfigurations fil (_function.jspå_).

<a name="queue-trigger-look-up-id-from-json-ps"></a>

```json
{
  "name": "InputDocumentIn",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id": "{queueTrigger_payload_property}",
  "partitionKey": "{queueTrigger_payload_property}",
  "connectionStringSetting": "CosmosDBConnection",
  "direction": "in"
},
{
  "name": "InputDocumentOut",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": false,
  "partitionKey": "{queueTrigger_payload_property}",
  "connectionStringSetting": "CosmosDBConnection",
  "direction": "out"
}
```

_run.ps1_ -filen har PowerShell-koden som läser det inkommande dokumentet och ändringar i utdata.

```powershell
param($QueueItem, $InputDocumentIn, $TriggerMetadata) 

$Document = $InputDocumentIn 
$Document.text = 'This was updated!' 

Push-OutputBinding -Name InputDocumentOut -Value $Document  
```

<a name="http-trigger-id-query-string-ps"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-utlösare, leta upp ID från frågesträng

Följande exempel visar hur du läser och uppdaterar ett enda Cosmos DB-dokument från ett webb-API. Dokumentets unika identifierare anges via en QueryString-parameter från HTTP-begäran, enligt definitionen i bindningens `"Id": "{Query.Id}"` egenskap.

Den Cosmos DB indata-bindningen visas först i listan över bindningar som finns i funktionens konfigurations fil (_function.jspå_).

```json
{ 
  "bindings": [ 
    { 
      "type": "cosmosDB", 
      "name": "ToDoItem", 
      "databaseName": "ToDoItems", 
      "collectionName": "Items", 
      "connectionStringSetting": "CosmosDBConnection", 
      "direction": "in", 
      "Id": "{Query.id}", 
      "PartitionKey": "{Query.partitionKeyValue}" 
    },
    { 
      "authLevel": "anonymous", 
      "name": "Request", 
      "type": "httpTrigger", 
      "direction": "in", 
      "methods": [ 
        "get", 
        "post" 
      ] 
    }, 
    { 
      "name": "Response", 
      "type": "http", 
      "direction": "out" 
    },
  ], 
  "disabled": false 
} 
```
  
_run.ps1_ -filen har PowerShell-koden som läser det inkommande dokumentet och ändringar i utdata.

```powershell
using namespace System.Net 

param($Request, $ToDoItem, $TriggerMetadata) 

Write-Host 'PowerShell HTTP trigger function processed a request' 

if (-not $ToDoItem) { 
    Write-Host 'ToDo item not found' 

    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::NotFound 
        Body = $ToDoItem.Description 
    }) 

} else { 

    Write-Host "Found ToDo item, Description=$($ToDoItem.Description)" 
 
    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::OK 
        Body = $ToDoItem.Description 
    }) 
}
```

<a name="http-trigger-id-route-data-ps"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från flödes data

Följande exempel visar hur du läser och uppdaterar ett enda Cosmos DB-dokument från ett webb-API. Dokumentets unika identifierare anges via en väg parameter. Väg parametern definieras i bindnings egenskapen för HTTP-begäran `route` och refereras till i egenskapen Cosmos DB `"Id": "{Id}"` Binding.

Den Cosmos DB indata-bindningen visas först i listan över bindningar som finns i funktionens konfigurations fil (_function.jspå_).

```json
{ 
  "bindings": [ 
    { 
      "type": "cosmosDB", 
      "name": "ToDoItem", 
      "databaseName": "ToDoItems", 
      "collectionName": "Items", 
      "connectionStringSetting": "CosmosDBConnection", 
      "direction": "in", 
      "Id": "{id}", 
      "PartitionKey": "{partitionKeyValue}" 
    },
    { 
      "authLevel": "anonymous", 
      "name": "Request", 
      "type": "httpTrigger", 
      "direction": "in", 
      "methods": [ 
        "get", 
        "post" 
      ], 
      "route": "todoitems/{partitionKeyValue}/{id}" 
    }, 
    { 
      "name": "Response", 
      "type": "http", 
      "direction": "out" 
    }
  ], 
  "disabled": false 
} 
```

_run.ps1_ -filen har PowerShell-koden som läser det inkommande dokumentet och ändringar i utdata.

```powershell
using namespace System.Net 

param($Request, $ToDoItem, $TriggerMetadata) 

Write-Host 'PowerShell HTTP trigger function processed a request' 

if (-not $ToDoItem) { 
    Write-Host 'ToDo item not found' 

    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::NotFound 
        Body = $ToDoItem.Description 
    }) 

} else { 
    Write-Host "Found ToDo item, Description=$($ToDoItem.Description)" 
  
    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::OK 
        Body = $ToDoItem.Description 
    }) 
} 
```

<a name="queue-trigger-multiple-docs-sqlquery-ps"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Köa utlösare, hämta flera dokument med SqlQuery

Följande exempel visar hur du läser flera Cosmos DB dokument. Funktionens konfigurations fil (_function.jspå_) definierar bindnings egenskaperna, som innehåller `sqlQuery` . SQL-instruktionen som angavs för `sqlQuery` egenskapen väljer den uppsättning av dokument som har angetts för funktionen.

```json
{ 
  "name": "Documents", 
  "type": "cosmosDB", 
  "direction": "in", 
  "databaseName": "MyDb", 
  "collectionName": "MyCollection", 
  "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}", 
  "connectionStringSetting": "CosmosDBConnection" 
} 
```

_Run1.PS_ -filen har PowerShell-koden som läser de inkommande dokumenten.

```powershell
param($QueueItem, $Documents, $TriggerMetadata) 

foreach ($Document in $Documents) { 
    # operate on each document 
} 
```

# <a name="python"></a>[Python](#tab/python)

Det här avsnittet innehåller följande exempel som läser ett enda dokument genom att ange ett ID-värde från olika källor:

* [Köa utlösare, slå upp ID från JSON](#queue-trigger-look-up-id-from-json-python)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-python)
* [HTTP-utlösare, slå upp ID från flödes data](#http-trigger-look-up-id-from-route-data-python)
* [Köa utlösare, hämta flera dokument med SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Köa utlösare, slå upp ID från JSON

I följande exempel visas en Cosmos DB indata-bindning i en *function.jspå* filen och en [python-funktion](functions-reference-python.md) som använder bindningen. Funktionen läser ett enda dokument och uppdaterar dokumentets text värde.

Här är bindnings data i *function.jspå* filen:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är python-koden:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-utlösare, leta upp ID från frågesträng

I följande exempel visas en [python-funktion](functions-reference-python.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange det ID och det partitionsnyckel som ska sökas upp. Detta ID och partitionerings nyckel värde används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

Här är *function.jspå* filen:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "scriptFile": "__init__.py"
}
```

Här är python-koden:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

<a id="http-trigger-look-up-id-from-route-data-python"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från flödes data

I följande exempel visas en [python-funktion](functions-reference-python.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder flödes data för att ange det ID och det partitionsnyckel som ska sökas upp. Detta ID och partitionerings nyckel värde används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

Här är *function.jspå* filen:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Här är python-koden:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Köa utlösare, hämta flera dokument med SqlQuery

I följande exempel visas en Azure Cosmos DB indata-bindning i en *function.jspå* fil och en [python-funktion](functions-reference-python.md) som använder bindningen. Funktionen hämtar flera dokument som anges av en SQL-fråga med hjälp av en Queue-utlösare för att anpassa frågeparametrar.

Utlösaren för kön innehåller en parameter `departmentId` . Ett Queue-meddelande i `{ "departmentId" : "Finance" }` returnerar alla poster för ekonomi avdelningen.

Här är bindnings data i *function.jspå* filen:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är python-koden:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

 ---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

Använd attributet [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) i [C#-klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar databasens namn och samlings namn. Information om de inställningar och andra egenskaper som du kan konfigurera finns i [följande konfigurations avsnitt](#configuration).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="java"></a>[Java](#tab/java)

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@CosmosDBOutput` anteckningen för parametrar som skriver till Cosmos dB. Kommentar parameter typen bör vara `OutputBinding<T>` , där `T` är antingen en ursprunglig Java-typ eller en POJO.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribut stöds inte av PowerShell.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `CosmosDB` attributet.

|function.jspå egenskap | Attributets egenskap |Description|
|---------|---------|----------------------|
|**bastyp**     | saknas | Måste anges till `cosmosDB` .        |
|**position**     | saknas | Måste anges till `in` .         |
|**Namn**     | saknas | Namnet på den bindnings parameter som representerar dokumentet i funktionen.  |
|**Databas** |**DatabaseName** |Databasen som innehåller dokumentet.        |
|**Samling** |**CollectionName** | Namnet på den samling som innehåller dokumentet. |
|**id**    | **Identitet** | ID för det dokument som ska hämtas. Den här egenskapen stöder [bindnings uttryck](./functions-bindings-expressions-patterns.md). Ange inte både `id` egenskaperna och **sqlQuery** . Om du inte anger någon, hämtas hela samlingen. |
|**sqlQuery**  |**SqlQuery**  | En Azure Cosmos DB SQL-fråga som används för att hämta flera dokument. Egenskapen stöder runtime-bindningar, som i det här exemplet: `SELECT * FROM c where c.departmentId = {departmentId}` . Ange inte både `id` egenskaperna och `sqlQuery` . Om du inte anger någon, hämtas hela samlingen.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Namnet på den app-inställning som innehåller Azure Cosmos DB anslutnings strängen. |
|**partitionKey**|**PartitionKey**|Anger partitionens nyckel värde för sökningen. Kan innehålla bindnings parametrar. Det krävs för uppslag i [partitionerade](../cosmos-db/partitioning-overview.md#logical-partitions) samlingar.|
|**preferredLocations**| **PreferredLocations**| Valfritt Definierar önskade platser (regioner) för geo-replikerade databas konton i Azure Cosmos DBs tjänsten. Värdena ska vara kommaavgränsade. Till exempel "östra USA, södra centrala USA, norra Europa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

När funktionen avslutas sparas alla ändringar som gjorts i indatamängds dokumentet via namngivna indataparametrar automatiskt.

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

När funktionen avslutas sparas alla ändringar som gjorts i indatamängds dokumentet via namngivna indataparametrar automatiskt.

# <a name="java"></a>[Java](#tab/java)

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime) [@CosmosDBInput](/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) visar anteckningen Cosmos db data till funktionen. Den här anteckningen kan användas med inbyggda Java-typer, Pojo eller null-värden med hjälp av `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Uppdateringar görs inte automatiskt när funktionen avslutas. Använd `context.bindings.<documentName>In` och `context.bindings.<documentName>Out` för att göra uppdateringar i stället. Se [JavaScript-exemplet](#example) för mer information.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Uppdateringar av dokument görs inte automatiskt när funktionen avslutas. Om du vill uppdatera dokument i en funktion använder du en [utgående bindning](./functions-bindings-cosmosdb-v2-input.md). Mer information finns i [PowerShell-exemplet](#example) .

# <a name="python"></a>[Python](#tab/python)

Data görs tillgängliga för funktionen via en `DocumentList` parameter. Ändringar som görs i dokumentet sparas inte automatiskt.

---

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion när ett Azure Cosmos DB-dokument skapas eller ändras (utlösare)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Spara ändringar i ett Azure Cosmos DB dokument (utgående bindning)](./functions-bindings-cosmosdb-v2-output.md)