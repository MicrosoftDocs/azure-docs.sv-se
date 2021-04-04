---
title: Azure Cosmos DB bindningar för funktioner 1. x
description: Förstå hur du använder Azure Cosmos DB utlösare och bindningar i Azure Functions 1. x.
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 524df7805207ce517c7ae805fb17de1b041a2248
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002126"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Azure Cosmos DB bindningar för Azure Functions 1. x

> [!div class="op_single_selector" title1="Välj den version av Azure Functions runtime som du använder: "]
> * [Version 1](functions-bindings-cosmosdb.md)
> * [Version 2](functions-bindings-cosmosdb-v2.md)

Den här artikeln förklarar hur du arbetar med [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) -bindningar i Azure Functions. Azure Functions stöd för utlösare, indata och utgående bindningar för Azure Cosmos DB.

> [!NOTE]
> Den här artikeln är för Azure Functions 1. x. Information om hur du använder dessa bindningar i functions 2. x och högre finns [Azure Cosmos DB bindningar för Azure Functions 2. x](functions-bindings-cosmosdb-v2.md).
>
>Den här bindningen hade ursprungligen namnet DocumentDB. I functions version 1. x har bara utlösaren bytt namn Cosmos DB; indata bindningen, utgående bindningen och NuGet-paketet behåller DocumentDB namn.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Azure Cosmos DB-bindningar stöds endast för användning med SQL-API:n. För alla andra Azure Cosmos DB-API: er ska du komma åt databasen från din funktion med hjälp av den statiska klienten för ditt API, inklusive [Azure Cosmos DB s API för MongoDB](../cosmos-db/mongodb-introduction.md), [API för Cassandra](../cosmos-db/cassandra-introduction.md), [Gremlin API](../cosmos-db/graph-introduction.md)och [tabell-API](../cosmos-db/table-introduction.md).

## <a name="packages---functions-1x"></a>Paket-funktioner 1. x

Azure Cosmos DB bindningar för functions version 1. x finns i [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet-paketet, version 1. x. Käll koden för bindningarna finns i [Azure-WebJobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB) lagringsplatsen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Utlösare

Azure Cosmos DB-utlösaren använder [Azure Cosmos DB ändra feed](../cosmos-db/change-feed.md) för att lyssna efter infogningar och uppdateringar över partitioner. Ändrings flödet publicerar infogningar och uppdateringar, tas inte bort.

## <a name="trigger---example"></a>Utlös – exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som anropas när det finns infogningar eller uppdateringar i den angivna databasen och samlingen.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV1
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            TraceWriter log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.Info($"Documents modified: {documents.Count}");
                log.Info($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

I följande exempel visas en Cosmos DB trigger-bindning i en *function.jspå* filen och en [C#-skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen skriver logg meddelanden när Cosmos DB poster ändras.

Här är bindnings data i *function.jspå* filen:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Här är C#-skript koden:

```cs
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en Cosmos DB trigger-bindning i en *function.jsi* filen och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skriver logg meddelanden när Cosmos DB poster ändras.

Här är bindnings data i *function.jspå* filen:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Här är JavaScript-koden:

```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

---

## <a name="trigger---attributes"></a>Utlös ande attribut

# <a name="c"></a>[C#](#tab/csharp)

Använd attributet [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) i [C#-klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar databasens namn och samlings namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [trigger-Configuration](#trigger---configuration). Här är ett `CosmosDBTrigger` attribut exempel i en metodsignatur:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Ett fullständigt exempel finns i [exempel på Utlös ande C#](#trigger).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

---

## <a name="trigger---configuration"></a>Utlös konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `CosmosDBTrigger` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp** | saknas | Måste anges till `cosmosDBTrigger` . |
|**position** | saknas | Måste anges till `in` . Den här parametern anges automatiskt när du skapar utlösaren i Azure Portal. |
|**Namn** | saknas | Variabel namnet som används i funktions kod som representerar listan med dokument med ändringar. |
|**connectionStringSetting**|**ConnectionStringSetting** | Namnet på en app-inställning som innehåller anslutnings strängen som används för att ansluta till det Azure Cosmos DB-konto som övervakas. |
|**Databas**|**DatabaseName**  | Namnet på Azure Cosmos DB databasen med den samling som övervakas. |
|**Samling** |**CollectionName** | Namnet på den samling som övervakas. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Valfritt Namnet på en app-inställning som innehåller anslutnings strängen till tjänsten som innehåller låne samlingen. Om värdet inte anges `connectionStringSetting` används värdet. Den här parametern anges automatiskt när bindningen skapas i portalen. Anslutnings strängen för samlingen lån måste ha Skriv behörighet.|
|**leaseDatabaseName** |**LeaseDatabaseName** | Valfritt Namnet på databasen som innehåller den samling som används för att lagra lån. Om inställningen inte anges används värdet för `databaseName` inställningen. Den här parametern anges automatiskt när bindningen skapas i portalen. |
|**leaseCollectionName** | **LeaseCollectionName** | Valfritt Namnet på den samling som används för att lagra lån. Om värdet inte anges används värdet `leases` . |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Valfritt När den är inställd på `true` skapas låne samlingen automatiskt när den inte redan finns. Standardvärdet är `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Valfritt Definierar mängden enheter för programbegäran som ska tilldelas när låne samlingen skapas. Den här inställningen används bara när `createLeaseCollectionIfNotExists` har angetts till `true` . Den här parametern anges automatiskt när bindningen skapas med hjälp av portalen.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Valfritt När den är aktive rad lägger den till ett prefix till de lån som skapats i leasing samlingen för den här funktionen, vilket effektivt tillåter två separata Azure Functions att dela samma Lease-samling med olika prefix.
|**feedPollDelay**| **FeedPollDelay**| Valfritt När det är inställt definierar den i millisekunder fördröjningen mellan att avsöka en partition efter nya ändringar i flödet, efter att alla aktuella ändringar har tömts. Standardvärdet är 5000 (5 sekunder).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| Valfritt När det är inställt definierar den, i millisekunder, intervallet för att starta en aktivitet för att beräkna om partitioner distribueras jämnt mellan kända värd instanser. Standardvärdet är 13000 (13 sekunder).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| Valfritt När det är inställt definierar den, i millisekunder, intervallet för vilken lånet tas i ett lån som representerar en partition. Om lånet inte förnyas inom det här intervallet kommer det att förfalla och ägarskapet av partitionen flyttas till en annan instans. Standardvärdet är 60000 (60 sekunder).
|**leaseRenewInterval**| **LeaseRenewInterval**| Valfritt När det är inställt definierar förnyelse intervallet i millisekunder för alla lån för partitioner som för närvarande innehas av en instans. Standardvärdet är 17000 (17 sekunder).
|**checkpointFrequency**| **CheckpointFrequency**| Valfritt När det är inställt definierar den, i millisekunder, intervallet mellan låne kontroll punkter. Standardvärdet är alltid efter varje funktions anrop.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Valfritt När det är inställt anpassas den maximala mängden objekt som tas emot per funktions anrop.
|**startFromBeginning**| **StartFromBeginning**| Valfritt När det är inställt, instrueras utlösaren att börja läsa ändringar från början av samlingens historik i stället för den aktuella tiden. Detta fungerar bara första gången utlösaren startar, precis som vid efterföljande körningar, så är kontroll punkterna redan lagrade. Att ställa in detta på `true` när det redan har skapats lån har ingen påverkan.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlös användning

Utlösaren kräver en andra samling som används för att lagra _lån_ över partitionerna. Både samlingen som övervakas och samlingen som innehåller lånet måste vara tillgängliga för att utlösaren ska fungera.

>[!IMPORTANT]
> Om flera funktioner har kon figurer ATS för att använda en Cosmos DB-utlösare för samma samling, ska varje funktion använda en dedikerad Lease-samling eller ange en annan `LeaseCollectionPrefix` för varje funktion. Annars aktive ras bara en av funktionerna. Information om prefixet finns i [avsnittet konfiguration](#trigger---configuration).

Utlösaren anger inte om ett dokument har uppdaterats eller infogats. det innehåller bara själva dokumentet. Om du behöver hantera uppdateringar och infogningar på olika sätt kan du göra det genom att implementera tidsstämpelfält för infogning eller uppdatering.

## <a name="input"></a>Indata

Azure Cosmos DB-indatabindningen använder SQL API för att hämta en eller flera Azure Cosmos DB-dokument och skickar dem till indataparametern för funktionen. Dokument-ID:t och frågeparametrarna kan fastställas baserat på den utlösare som anropar funktionen.

## <a name="input---example"></a>Indatamängds exempel

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
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Köa utlösare, slå upp ID från JSON

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av ett Queue-meddelande som innehåller ett JSON-objekt. Queue-utlösaren parsar JSON till ett objekt med namnet `ToDoItemLookup` , som innehåller det ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-utlösare, leta upp ID från frågesträng

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange vilket ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från flödes data

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder flödes data för att ange det ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static HttpResponseMessage Run(
            [HttpTrigger(
                AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Hoppa över inmatade exempel](#input---attributes)

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-utlösare, slå upp ID från routa data med SqlQuery

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder flödes data för att ange det ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")] IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Hoppa över inmatade exempel](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-utlösare, hämta flera dokument med SqlQuery

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. Frågan anges i `SqlQuery` egenskapen Attribute.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Hoppa över inmatade exempel](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP-utlösare, hämta flera dokument med DocumentClient (C#)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. I koden används en `DocumentClient` instans som tillhandahålls av Azure Cosmos DB bindningen för att läsa en lista över dokument. `DocumentClient`Instansen kan också användas för Skriv åtgärder.

```cs
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
            string searchterm = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
                .Value;

            if (searchterm == null)
            {
                return req.CreateResponse(HttpStatusCode.NotFound);
            }

            log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.Info(result.Description);
                }
            }
            return req.CreateResponse(HttpStatusCode.OK);
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
namespace CosmosDBSamplesV1
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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

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

I följande exempel visas en [C#-skript funktion](functions-reference-csharp.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange vilket ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

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
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Här är C#-skript koden:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från flödes data

I följande exempel visas en [C#-skript funktion](functions-reference-csharp.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder flödes data för att ange det ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

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
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Här är C#-skript koden:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
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
      "type": "documentDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
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

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.Info(toDoItem.Description);
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
      "type": "documentDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
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

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.Info(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Det här avsnittet innehåller följande exempel:

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

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

I följande exempel visas en [JavaScript-funktion](functions-reference-node.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange vilket ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

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
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
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

I följande exempel visas en [JavaScript-funktion](functions-reference-node.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange vilket ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem` dokument från den angivna databasen och samlingen.

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
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

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

---

## <a name="input---attributes"></a>In-attribut

# <a name="c"></a>[C#](#tab/csharp)

Använd attributet [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) i [C#-klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar databasens namn och samlings namn. Information om de inställningar och andra egenskaper som du kan konfigurera finns i [följande konfigurations avsnitt](#input---configuration).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

---

## <a name="input---configuration"></a>Indatamängds konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `DocumentDB` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp**     | saknas | Måste anges till `documentdb` .        |
|**position**     | saknas | Måste anges till `in` .         |
|**Namn**     | saknas | Namnet på den bindnings parameter som representerar dokumentet i funktionen.  |
|**Databas** |**DatabaseName** |Databasen som innehåller dokumentet.        |
|**Samling** |**CollectionName** | Namnet på den samling som innehåller dokumentet. |
|**id**    | **Identitet** | ID för det dokument som ska hämtas. Den här egenskapen stöder [bindnings uttryck](./functions-bindings-expressions-patterns.md). Ange inte egenskaperna för både **ID** och **sqlQuery** . Om du inte anger någon, hämtas hela samlingen. |
|**sqlQuery**  |**SqlQuery**  | En Azure Cosmos DB SQL-fråga som används för att hämta flera dokument. Egenskapen stöder runtime-bindningar, som i det här exemplet: `SELECT * FROM c where c.departmentId = {departmentId}` . Ange inte egenskaperna för både **ID** och **sqlQuery** . Om du inte anger någon, hämtas hela samlingen.|
|**anslutningen**     |**ConnectionStringSetting**|Namnet på den app-inställning som innehåller Azure Cosmos DB anslutnings strängen.        |
|**partitionKey**|**PartitionKey**|Anger partitionens nyckel värde för sökningen. Kan innehålla bindnings parametrar.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Inmatad användning

# <a name="c"></a>[C#](#tab/csharp)

När funktionen avslutas sparas alla ändringar som gjorts i indatamängds dokumentet via namngivna indataparametrar automatiskt.

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

När funktionen avslutas sparas alla ändringar som gjorts i indatamängds dokumentet via namngivna indataparametrar automatiskt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Uppdateringar görs inte automatiskt när funktionen avslutas. Använd `context.bindings.<documentName>In` och `context.bindings.<documentName>Out` för att göra uppdateringar i stället. Se [exemplet på indatamängden](#input).

---

## <a name="output"></a>Utdata

Med Azure Cosmos DB utgående bindning kan du skriva ett nytt dokument till en Azure Cosmos DB-databas med hjälp av SQL-API: et.

## <a name="output---example"></a>Utdata-exempel

# <a name="c"></a>[C#](#tab/csharp)

Det här avsnittet innehåller följande exempel:

* Köa utlösare, skriva ett dokument
* Köa utlösare, skriva dokument med `IAsyncCollector`

Exemplen refererar till en enkel `ToDoItem` Typ:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

### <a name="queue-trigger-write-one-doc"></a>Köa utlösare, skriva ett dokument

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som lägger till ett dokument i en databas med hjälp av data som anges i meddelande från Queue Storage.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System;

namespace CosmosDBSamplesV1
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Köa utlösare, skriva dokument med IAsyncCollector

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som lägger till en samling dokument i en databas med hjälp av data som finns i ett meddelande-JSON för köer.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.Info($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Det här avsnittet innehåller följande exempel:

* Köa utlösare, skriva ett dokument
* Köa utlösare, skriva dokument med `IAsyncCollector`

### <a name="queue-trigger-write-one-doc"></a>Köa utlösare, skriva ett dokument

I följande exempel visas en Azure Cosmos DB utgående bindning i en *function.jspå* filen och en [C#-skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen använder en indatatyps bindning för en kö som tar emot JSON i följande format:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funktionen skapar Azure Cosmos DB dokument i följande format för varje post:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Här är bindnings data i *function.jspå* filen:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

I [konfigurations](#output---configuration) avsnittet förklaras dessa egenskaper.

Här är C#-skript koden:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        dynamic employee = JObject.Parse(myQueueItem);

        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Köa utlösare, skriva dokument med IAsyncCollector

Om du vill skapa flera dokument kan du binda till `ICollector<T>` eller `IAsyncCollector<T>` där `T` är en av de typer som stöds.

Det här exemplet refererar till en enkel `ToDoItem` Typ:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

Här är function.jspå filen:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "documentDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Här är C#-skript koden:

```cs
using System;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.Info($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en Azure Cosmos DB utgående bindning i en *function.jsi* filen och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen använder en indatatyps bindning för en kö som tar emot JSON i följande format:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funktionen skapar Azure Cosmos DB dokument i följande format för varje post:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Här är bindnings data i *function.jspå* filen:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

I [konfigurations](#output---configuration) avsnittet förklaras dessa egenskaper.

Här är JavaScript-koden:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

---

## <a name="output---attributes"></a>Utdata-attribut

# <a name="c"></a>[C#](#tab/csharp)

Använd attributet [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) i [C#-klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar databasens namn och samlings namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [utdata-konfiguration](#output---configuration). Här är ett `DocumentDB` attribut exempel i en metodsignatur:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Ett fullständigt exempel finns i [utdata](#output).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

---

## <a name="output---configuration"></a>Utdata-konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `DocumentDB` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp**     | saknas | Måste anges till `documentdb` .        |
|**position**     | saknas | Måste anges till `out` .         |
|**Namn**     | saknas | Namnet på den bindnings parameter som representerar dokumentet i funktionen.  |
|**Databas** | **DatabaseName**|Databasen som innehåller den samling där dokumentet skapas.     |
|**Samling** |**CollectionName**  | Namnet på den samling där dokumentet skapas. |
|**createIfNotExists**  |**CreateIfNotExists**    | Ett booleskt värde som anger om samlingen skapas när den inte finns. Standardvärdet är *false* eftersom nya samlingar skapas med reserverat data flöde, vilket innebär kostnads konsekvenser. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**partitionKey**|**PartitionKey** |När `CreateIfNotExists` är true definierar sökvägen till partitionsnyckel för den skapade samlingen.|
|**collectionThroughput**|**CollectionThroughput**| När `CreateIfNotExists` är sant definierar [data flödet](../cosmos-db/set-throughput.md) för den skapade samlingen.|
|**anslutningen**    |**ConnectionStringSetting** |Namnet på den app-inställning som innehåller Azure Cosmos DB anslutnings strängen.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utmatnings användning

Som standard skapas ett dokument i din databas när du skriver till Utdataparametern i funktionen. Det här dokumentet har ett automatiskt genererat GUID som dokument-ID. Du kan ange dokument-ID: t för utmatnings dokumentet genom att ange `id` egenskapen i JSON-objektet som skickas till Utdataparametern.

> [!Note]
> När du anger ID: t för ett befintligt dokument skrivs det över av det nya utdata-dokumentet.

## <a name="exceptions-and-return-codes"></a>Undantag och retur koder

| Bindning | Referens |
|---|---|
| CosmosDB | [Felkoder för CosmosDB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om databas bearbetning utan server med Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Lär dig mer om Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
