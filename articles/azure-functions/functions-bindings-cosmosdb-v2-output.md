---
title: Azure Cosmos DB utgående bindning för funktioner 2. x och högre
description: Lär dig att använda Azure Cosmos DB utgående bindning i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 779b66412319ec8422977a7e56570a4d16f89aa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98071552"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x-and-higher"></a>Azure Cosmos DB utgående bindning för Azure Functions 2. x och högre

Med Azure Cosmos DB utgående bindning kan du skriva ett nytt dokument till en Azure Cosmos DB-databas med hjälp av SQL-API: et.

Information om konfiguration och konfigurations information finns i [översikten](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Det här avsnittet innehåller följande exempel:

* [Köa utlösare, skriva ett dokument](#queue-trigger-write-one-doc-c)
* [Köa utlösare, skriva dokument med IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

Exemplen refererar till en enkel `ToDoItem` Typ:

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

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>Köa utlösare, skriva ett dokument

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som lägger till ett dokument i en databas med hjälp av data som anges i meddelande från Queue Storage.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Köa utlösare, skriva dokument med IAsyncCollector

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som lägger till en samling dokument i en databas med hjälp av data som finns i ett meddelande-JSON för köer.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Det här avsnittet innehåller följande exempel:

* [Köa utlösare, skriva ett dokument](#queue-trigger-write-one-doc-c-script)
* [Köa utlösare, skriva dokument med IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

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
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är C#-skript koden:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Köa utlösare, skriva dokument med IAsyncCollector

Om du vill skapa flera dokument kan du binda till `ICollector<T>` eller `IAsyncCollector<T>` där `T` är en av de typer som stöds.

Det här exemplet refererar till en enkel `ToDoItem` Typ:

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

Här är function.jspå filen:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Här är C#-skript koden:

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

* [Köa utlösare, Spara meddelande till databas via retur värde](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP-utlösare, spara ett dokument till databasen via retur värde](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP-utlösare, spara ett dokument till databasen via OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP-utlösare, spara flera dokument till databasen via OutputBinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Köa utlösare, Spara meddelande till databas via retur värde

I följande exempel visas en Java-funktion som lägger till ett dokument i en databas med data från ett meddelande i Queue Storage.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>HTTP-utlösare, spara ett dokument till databasen via retur värde

I följande exempel visas en Java-funktion vars signatur är kommenterad ```@CosmosDBOutput``` och har returnerat värde av typen ```String``` . JSON-dokumentet som returnerades av funktionen skrivs automatiskt till motsvarande CosmosDB-samling.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>HTTP-utlösare, spara ett dokument till databasen via OutputBinding

I följande exempel visas en Java-funktion som skriver ett dokument till CosmosDB via en ```OutputBinding<T>``` utdataparameter. I det här exemplet ```outputItem``` måste parametern vara kommenterad ```@CosmosDBOutput``` , inte Function-signaturen. Med ```OutputBinding<T>``` kan du använda funktionen för att dra nytta av bindningen för att skriva dokumentet till CosmosDB, samtidigt som du även tillåter att ett annat värde returneras till funktions anrop, till exempel ett JSON-eller XML-dokument.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>HTTP-utlösare, spara flera dokument till databasen via OutputBinding

I följande exempel visas en Java-funktion som skriver flera dokument till CosmosDB via en ```OutputBinding<T>``` utdataparameter. I det här exemplet ```outputItem``` är parametern kommenterad ```@CosmosDBOutput``` , inte Function-signaturen. Parametern output ```outputItem``` innehåller en lista med ```ToDoItem``` objekt som mall-parameter typ. Med ```OutputBinding<T>``` kan du använda funktionen för att dra nytta av bindningen för att skriva dokumenten till CosmosDB samtidigt som du också tillåter att ett annat värde returneras till funktions anrop, till exempel ett JSON-eller XML-dokument.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@CosmosDBOutput` anteckningen för parametrar som ska skrivas till Cosmos dB.  Typ av antecknings parameter bör vara ```OutputBinding<T>``` , där T är antingen en ursprunglig Java-typ eller en POJO.

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
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

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

För Mass infogning utgör objekten först och kör sedan stringify-funktionen. Här är JavaScript-koden:

```javascript
    module.exports = function (context) {
    
        context.bindings.employeeDocument = JSON.stringify([
        {
            "id": "John Henry-123456",
            "name": "John Henry",
            "employeeId": "123456",
            "address": "A town nearby"
        },
        {
            "id": "John Doe-123457",
            "name": "John Doe",
            "employeeId": "123457",
            "address": "A town far away"
        }]);
    
      context.done();
    };
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

I följande exempel visas hur du skriver data till Cosmos DB med hjälp av en utgående bindning. Bindningen deklareras i funktionens konfigurations fil (_functions.jspå_) och tar data från ett köat meddelande och skriver ut till ett Cosmos DB dokument.

```json
{ 
  "name": "EmployeeDocument",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connectionStringSetting": "MyStorageConnectionAppSetting",
  "direction": "out" 
} 
```

I _run.ps1_ -filen mappas objektet som returnerades från funktionen till ett `EmployeeDocument` -objekt, som sparas i databasen.

```powershell
param($QueueItem, $TriggerMetadata) 

Push-OutputBinding -Name EmployeeDocument -Value @{ 
    id = $QueueItem.name + '-' + $QueueItem.employeeId 
    name = $QueueItem.name 
    employeeId = $QueueItem.employeeId 
    address = $QueueItem.address 
} 
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas hur du skriver ett dokument till en Azure CosmosDB-databas som utdata till en funktion.

Bindnings definitionen definieras i *function.jspå* WHERE- *typ* är inställd på `cosmosDB` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Skicka ett dokument objekt till- `set` metoden för databas parametern om du vill skriva till databasen.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

Använd attributet [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/dev/test/WebJobs.Extensions.CosmosDB.Tests) i [C#-klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar databasens namn och samlings namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [utdata-konfiguration](#configuration). Här är ett `CosmosDB` attribut exempel i en metodsignatur:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="java"></a>[Java](#tab/java)

`CosmosDBOutput`Anteckningen är tillgänglig för att skriva data till Cosmos dB. Du kan använda anteckningen till funktionen eller en enskild funktions parameter. När det används i funktions metoden är returvärdet för funktionen det som skrivs till Cosmos DB. Om du använder anteckningen med en parameter måste parameterns typ deklareras som en `OutputBinding<T>` där `T` en ursprunglig Java-typ eller en POJO.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribut stöds inte av PowerShell.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `CosmosDB` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp**     | saknas | Måste anges till `cosmosDB` .        |
|**position**     | saknas | Måste anges till `out` .         |
|**Namn**     | saknas | Namnet på den bindnings parameter som representerar dokumentet i funktionen.  |
|**Databas** | **DatabaseName**|Databasen som innehåller den samling där dokumentet skapas.     |
|**Samling** |**CollectionName**  | Namnet på den samling där dokumentet skapas. |
|**createIfNotExists**  |**CreateIfNotExists**    | Ett booleskt värde som anger om samlingen skapas när den inte finns. Standardvärdet är *false* eftersom nya samlingar skapas med reserverat data flöde, vilket innebär kostnads konsekvenser. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**PartitionKey** |När `CreateIfNotExists` är true definierar den partitionens nyckel Sök väg för den skapade samlingen.|
|**collectionThroughput**|**CollectionThroughput**| När `CreateIfNotExists` är sant definierar den [data flödet](../cosmos-db/set-throughput.md) för den skapade samlingen.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Namnet på den app-inställning som innehåller Azure Cosmos DB anslutnings strängen.        |
|**preferredLocations**| **PreferredLocations**| Valfritt Definierar önskade platser (regioner) för geo-replikerade databas konton i Azure Cosmos DBs tjänsten. Värdena ska vara kommaavgränsade. Till exempel "östra USA, södra centrala USA, norra Europa". |
|**useMultipleWriteLocations**| **UseMultipleWriteLocations**| Valfritt När det är inställt på `true` tillsammans med `PreferredLocations` kan den utnyttja [flera regioner](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions) i Azure Cosmos DBS tjänsten. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

Som standard skapas ett dokument i din databas när du skriver till Utdataparametern i funktionen. Det här dokumentet har ett automatiskt genererat GUID som dokument-ID. Du kan ange dokument-ID: t för utmatnings dokumentet genom att ange `id` egenskapen i JSON-objektet som skickas till Utdataparametern.

> [!Note]
> När du anger ID: t för ett befintligt dokument skrivs det över av det nya utdata-dokumentet.

## <a name="exceptions-and-return-codes"></a>Undantag och retur koder

| Bindning | Referens |
|---|---|
| CosmosDB | [Felkoder för CosmosDB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>host.jspå Inställningar

I det här avsnittet beskrivs globala konfigurations inställningar som är tillgängliga för den här bindningen i version 2. x. Mer information om globala konfigurations inställningar i version 2. x finns [host.jsför referens för Azure Functions version 2. x](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Egenskap  |Standardvärde | Beskrivning |
|---------|---------|---------|
|GatewayMode|Gateway|Anslutnings läget som används av funktionen vid anslutning till Azure Cosmos DBs tjänsten. Alternativen är `Direct` och `Gateway`|
|Protokoll|Https|Anslutnings protokollet som används av funktionen vid anslutning till Azure Cosmos DBs tjänsten.  Läs [här om du vill ha en förklaring av båda lägena](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|saknas|Lease-prefix som ska användas för alla funktioner i en app.|

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion när ett Azure Cosmos DB-dokument skapas eller ändras (utlösare)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Läsa ett Azure Cosmos DB dokument (ingående bindning)](./functions-bindings-cosmosdb-v2-input.md)
