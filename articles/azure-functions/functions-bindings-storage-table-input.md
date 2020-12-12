---
title: Data bindningar för Azure Table Storage för Azure Functions
description: Lär dig hur du använder Azure Table Storage-databindningar i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 20dc6cde9cce6a9d57047940a38adb5cf004ae6a
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347684"
---
# <a name="azure-table-storage-input-bindings-for-azure-functions"></a>Data bindningar för Azure Table Storage för Azure Functions

Använd data bindningen för Azure Table Storage för att läsa en tabell i ett Azure Storage-konto.

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>En entitet

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som läser en enskild tabell rad. För varje meddelande som skickas till kön utlöses funktionen.

Rad nyckel svärdet {queueTrigger} anger att rad nyckeln kommer från köns meddelande sträng.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="cloudtable"></a>CloudTable

`CloudTable` stöds endast i [funktionerna v2 och högre körningar](functions-versions.md).

Använd en `CloudTable` metod parameter för att läsa tabellen med hjälp av Azure Storage SDK. Här är ett exempel på en funktion som frågar en Azure Functions logg tabell:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

Mer information om hur du använder CloudTable finns i [komma igång med Azure Table Storage](../cosmos-db/tutorial-develop-table-dotnet.md).

Om du försöker binda till `CloudTable` och få ett fel meddelande, se till att du har en referens till [rätt Storage SDK-version](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

### <a name="iqueryable"></a>IQueryable

`IQueryable` stöds bara i [funktionerna v1 runtime](functions-versions.md).

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som läser flera tabell rader där `MyPoco` klassen härleds från `TableEntity` .

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

### <a name="one-entity"></a>En entitet

I följande exempel visas en tabell indata-bindning i en *function.jspå* fil-och [C#-skript](functions-reference-csharp.md) kod som använder bindningen. Funktionen använder en Queue-utlösare för att läsa en enskild tabell rad. 

*function.js* filen anger en `partitionKey` och en `rowKey` . `rowKey`Värdet {queueTrigger} anger att rad nyckeln kommer från köns meddelande sträng.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är C#-skript koden:

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` stöds inte i functions-körningen för [version 2. x och högre)](functions-versions.md). Ett alternativ är att använda en `CloudTable` metod parameter för att läsa tabellen med hjälp av Azure Storage SDK. Här är ett exempel på en funktion som frågar en Azure Functions logg tabell:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

Mer information om hur du använder CloudTable finns i [komma igång med Azure Table Storage](../cosmos-db/tutorial-develop-table-dotnet.md).

Om du försöker binda till `CloudTable` och få ett fel meddelande, se till att du har en referens till [rätt Storage SDK-version](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

### <a name="iqueryable"></a>IQueryable

I följande exempel visas en tabell indata-bindning i en *function.jspå* fil-och [C#-skript](functions-reference-csharp.md) kod som använder bindningen. Funktionen läser entiteter för en partitionsnyckel som anges i ett Queue-meddelande.

Här är *function.jspå* filen:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

C#-skript koden lägger till en referens till Azure Storage SDK så att entitetstypen kan härledas från `TableEntity` :

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en tabell indata-bindning i en *function.jspå* fil-och [JavaScript-kod](functions-reference-node.md) som använder bindningen. Funktionen använder en Queue-utlösare för att läsa en enskild tabell rad. 

*function.js* filen anger en `partitionKey` och en `rowKey` . `rowKey`Värdet {queueTrigger} anger att rad nyckeln kommer från köns meddelande sträng.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Enskild tabell rad 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas en HTTP-utlöst funktion som returnerar en lista med person objekt som finns i en angiven partition i Table Storage. I exemplet extraheras partitionsnyckel från http-vägen och tableName och anslutning från funktions inställningarna. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

TableInput-anteckningen kan också extrahera bindningarna från JSON-innehållet i begäran, som i följande exempel visas.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

I följande exempel används filtret för att fråga efter personer med ett särskilt namn i en Azure-tabell och begränsar antalet möjliga matchningar till 10 resultat.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

 Använd följande attribut i [C#-klass bibliotek](functions-dotnet-class-library.md)för att konfigurera en tabell indelnings bindning:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Attributets konstruktor tar tabellens namn, partitionsnyckel och rad nyckel. Attributet kan användas för en `out` parameter eller i funktionens retur värde, som visas i följande exempel:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Du kan ställa in `Connection` egenskapen för att ange det lagrings konto som ska användas, som du ser i följande exempel:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Ett fullständigt exempel finns i C#-exemplet.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Ger ett annat sätt att ange det lagrings konto som ska användas. Konstruktorn tar namnet på en app-inställning som innehåller en lagrings anslutnings sträng. Attributet kan användas på parameter-, metod-eller klass nivå. I följande exempel visas klass nivå och metod nivå:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Lagrings kontot som ska användas fastställs i följande ordning:

* `Table`Attributets `Connection` egenskap.
* `StorageAccount`Attributet som används för samma parameter som `Table` attributet.
* `StorageAccount`Attributet som används för funktionen.
* `StorageAccount`Attributet som används för klassen.
* Standard lagrings kontot för app-inställningen ("AzureWebJobsStorage").

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="java"></a>[Java](#tab/java)

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@TableInput` anteckningen för parametrar vars värde kommer från Table Storage.  Den här anteckningen kan användas med inbyggda Java-typer, Pojo eller null-värden med hjälp av `Optional<T>` .

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `Table` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp** | saknas | Måste anges till `table` . Den här egenskapen anges automatiskt när du skapar bindningen i Azure Portal.|
|**position** | saknas | Måste anges till `in` . Den här egenskapen anges automatiskt när du skapar bindningen i Azure Portal. |
|**Namn** | saknas | Namnet på variabeln som representerar tabellen eller entiteten i funktions koden. | 
|**tableName** | **TableName** | Namnet på tabellen.| 
|**partitionKey** | **PartitionKey** |Valfritt. Partitionsnyckel för den tabell entitet som ska läsas. I avsnittet [användning](#usage) finns information om hur du använder den här egenskapen.| 
|**rowKey** |**RowKey** | Valfritt. Rad nyckeln för den tabell entitet som ska läsas. I avsnittet [användning](#usage) finns information om hur du använder den här egenskapen.| 
|**take** |**Gå** | Valfritt. Det maximala antalet entiteter som ska läsas i Java Script. I avsnittet [användning](#usage) finns information om hur du använder den här egenskapen.| 
|**Synkroniseringsfilter** |**Filter** | Valfritt. Ett OData filter-uttryck för tabell indatatyp i Java Script. I avsnittet [användning](#usage) finns information om hur du använder den här egenskapen.| 
|**anslutningen** |**Anslutning** | Namnet på en app-inställning som innehåller den lagrings anslutnings sträng som ska användas för den här bindningen. Inställningen kan vara namnet på en "AzureWebJobs"-inställning för den fasta appen eller ett anslutnings sträng namn. Om ditt inställnings namn till exempel är "AzureWebJobsMyStorage" kan du ange "Storage" här. Functions-körningen söker automatiskt efter en app-inställning som heter "AzureWebJobsMyStorage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den angivna app-inställningen `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

* **Läs en rad i**

  Ange `partitionKey` och `rowKey` . Få åtkomst till tabell data med hjälp av en metod parameter `T <paramName>` . I C#-skript `paramName` är det värde som anges i `name` egenskapen för *function.jspå*. `T` är vanligt vis en typ som implementerar `ITableEntity` eller härleder från `TableEntity` . `filter`Egenskaperna och `take` används inte i det här scenariot.

* **Läs en eller flera rader**

  Få åtkomst till tabell data med hjälp av en metod parameter `IQueryable<T> <paramName>` . I C#-skript `paramName` är det värde som anges i `name` egenskapen för *function.jspå*. `T` måste vara en typ som implementerar `ITableEntity` eller härleds från `TableEntity` . Du kan använda `IQueryable` metoder för att utföra filtrering som krävs. `partitionKey`Egenskaperna, `rowKey` , `filter` och `take` används inte i det här scenariot.  

  > [!NOTE]
  > `IQueryable` stöds inte i [Functions v2-körningen](functions-versions.md). Ett alternativ är att [använda en CloudTable paramName-metod parameter](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) för att läsa tabellen med hjälp av Azure Storage SDK. Om du försöker binda till `CloudTable` och få ett fel meddelande, se till att du har en referens till [rätt Storage SDK-version](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

* **Läs en rad i**

  Ange `partitionKey` och `rowKey` . Få åtkomst till tabell data med hjälp av en metod parameter `T <paramName>` . I C#-skript `paramName` är det värde som anges i `name` egenskapen för *function.jspå*. `T` är vanligt vis en typ som implementerar `ITableEntity` eller härleder från `TableEntity` . `filter`Egenskaperna och `take` används inte i det här scenariot.

* **Läs en eller flera rader**

  Få åtkomst till tabell data med hjälp av en metod parameter `IQueryable<T> <paramName>` . I C#-skript `paramName` är det värde som anges i `name` egenskapen för *function.jspå*. `T` måste vara en typ som implementerar `ITableEntity` eller härleds från `TableEntity` . Du kan använda `IQueryable` metoder för att utföra filtrering som krävs. `partitionKey`Egenskaperna, `rowKey` , `filter` och `take` används inte i det här scenariot.  

  > [!NOTE]
  > `IQueryable` stöds inte i [Functions v2-körningen](functions-versions.md). Ett alternativ är att [använda en CloudTable paramName-metod parameter](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) för att läsa tabellen med hjälp av Azure Storage SDK. Om du försöker binda till `CloudTable` och få ett fel meddelande, se till att du har en referens till [rätt Storage SDK-version](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ange `filter` egenskaperna och `take` . Ange inte `partitionKey` eller `rowKey` . Få åtkomst till entiteten för indatakälla (eller entiteter) med hjälp av `context.bindings.<BINDING_NAME>` . De avserialiserade objekten har `RowKey` och `PartitionKey` Egenskaper.

# <a name="python"></a>[Python](#tab/python)

Tabell data skickas till funktionen som en JSON-sträng. Deserialisera meddelandet genom `json.loads` att anropa så som visas i [exemplet](#example)på indatamängden.

# <a name="java"></a>[Java](#tab/java)

Attributet [TableInput](/java/api/com.microsoft.azure.functions.annotation.tableinput) ger dig åtkomst till den tabell rad som utlöste funktionen.

---

## <a name="next-steps"></a>Nästa steg

* [Skriv tabell lagrings data från en funktion](./functions-bindings-storage-table-output.md)
