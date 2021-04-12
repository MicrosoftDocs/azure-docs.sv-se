---
title: Data bindningar för Azure Table Storage för Azure Functions
description: Lär dig hur du använder Azure Table Storage utgående bindningar i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: f793f96f55a258b2d7cb11f214984416557618df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453010"
---
# <a name="azure-table-storage-output-bindings-for-azure-functions"></a>Data bindningar för Azure Table Storage för Azure Functions

Använd en Azure Table Storage utgående bindning för att skriva entiteter till en tabell i ett Azure Storage konto.

> [!NOTE]
> Den här utgående bindningen stöder inte uppdatering av befintliga entiteter. Använd `TableOperation.Replace` åtgärden [från Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) för att uppdatera en befintlig entitet.

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som använder en http-utlösare för att skriva en enskild tabell rad.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

I följande exempel visas en bindning av tabellens utdata i en *function.jspå* fil-och [C#-skript](functions-reference-csharp.md) kod som använder bindningen. Funktionen skriver flera tabell enheter.

Här är *function.jspå* filen:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är C#-skript koden:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas en Java-funktion som använder en HTTP-utlösare för att skriva en enskild tabell rad.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

I följande exempel visas en Java-funktion som använder en HTTP-utlösare för att skriva flera tabell rader.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en bindning av tabellens utdata i en *function.jsi* filen och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skriver flera tabell enheter.

Här är *function.jspå* filen:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }

    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Följande exempel visar hur du skriver flera entiteter till en tabell från en funktion.

Bindnings konfiguration i _function.jspå_:

```json
{
  "bindings": [
    {
      "name": "InputData",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "TableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

PowerShell-kod i _run.ps1_:

```powershell
param($InputData, $TriggerMetadata)
  
foreach ($i in 1..10) {
    Push-OutputBinding -Name TableBinding -Value @{
        PartitionKey = 'Test'
        RowKey = "$i"
        Name = "Name $i"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Följande exempel visar hur du använder bindningen för tabell lagrings utdata. `table`Bindningen konfigureras i *function.jspå* genom att tilldela värden till `name` , `tableName` ,, `partitionKey` och `connection` :

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
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
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Följande funktion genererar en unik UUI för `rowKey` värdet och sparar meddelandet i Table Storage.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

Använd [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)i [C#-klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar tabellens namn. Attributet kan användas för en `out` parameter eller i funktionens retur värde, som visas i följande exempel:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Du kan ställa in `Connection` egenskapen för att ange det lagrings konto som ska användas, som du ser i följande exempel:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i C#- [exemplet](#example).

Du kan använda `StorageAccount` attributet för att ange lagrings kontot på klass-, metod-eller parameter nivå. Mer information finns i [indata-attribut](./functions-bindings-storage-table-input.md#attributes-and-annotations).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="java"></a>[Java](#tab/java)

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) -anteckningen för parametrar för att skriva värden i Table Storage.

Se [exemplet för mer information](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribut stöds inte av PowerShell.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `Table` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp** | saknas | Måste anges till `table` . Den här egenskapen anges automatiskt när du skapar bindningen i Azure Portal.|
|**position** | saknas | Måste anges till `out` . Den här egenskapen anges automatiskt när du skapar bindningen i Azure Portal. |
|**Namn** | saknas | Variabel namnet som används i funktions koden som representerar tabellen eller entiteten. Ange till `$return` att referera till funktionens retur värde.| 
|**tableName** |**TableName** | Namnet på tabellen.| 
|**partitionKey** |**PartitionKey** | Partitionsnyckel för den tabell entitet som ska skrivas. I [avsnittet användning](#usage) finns information om hur du använder den här egenskapen.| 
|**rowKey** |**RowKey** | Rad nyckeln för den tabell entitet som ska skrivas. I [avsnittet användning](#usage) finns information om hur du använder den här egenskapen.| 
|**anslutningen** |**Anslutning** | Namnet på en app-inställning som innehåller den lagrings anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till exempel ställer in `connection` på "telestorage" söker Functions-körningen efter en app-inställning med namnet "Storage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den angivna app-inställningen `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

Öppna entiteten utdata-tabell med hjälp av en metod parameter `ICollector<T> paramName` eller `IAsyncCollector<T> paramName` där `T` innehåller `PartitionKey` `RowKey` egenskaperna och. Dessa egenskaper åtföljs ofta av implementering `ITableEntity` eller arv `TableEntity` .

Du kan också använda en `CloudTable` metod parameter för att skriva till tabellen med hjälp av Azure Storage SDK. Om du försöker binda till `CloudTable` och få ett fel meddelande, se till att du har en referens till [rätt Storage SDK-version](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Öppna entiteten utdata-tabell med hjälp av en metod parameter `ICollector<T> paramName` eller `IAsyncCollector<T> paramName` där `T` innehåller `PartitionKey` `RowKey` egenskaperna och. Dessa egenskaper åtföljs ofta av implementering `ITableEntity` eller arv `TableEntity` . `paramName`Värdet anges i `name` egenskapen för *function.jspå*.

Du kan också använda en `CloudTable` metod parameter för att skriva till tabellen med hjälp av Azure Storage SDK. Om du försöker binda till `CloudTable` och få ett fel meddelande, se till att du har en referens till [rätt Storage SDK-version](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="java"></a>[Java](#tab/java)

Det finns två alternativ för att lägga till en tabell lagrings rad från en funktion med hjälp av [TableStorageOutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput) -anteckningen:

- **RETUR värde**: genom att använda anteckningen i själva funktionen sparas returvärdet för funktionen som en tabell lagrings rad.

- **Tvingande**: om du uttryckligen vill ange ett värde för meddelandet använder du anteckningen på en specifik parameter av typen [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , där `T` innehåller `PartitionKey` `RowKey` egenskaperna och. Dessa egenskaper åtföljs ofta av implementering `ITableEntity` eller arv `TableEntity` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Få åtkomst till utdata-händelsen `context.bindings.<name>` genom `<name>` att använda WHERE är värdet som anges i `name` egenskapen för *function.jspå*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill skriva till tabell data använder du `Push-OutputBinding` cmdleten, ställer in `-Name TableBinding` parametern och `-Value` parametern som motsvarar radens data. Mer information finns i [PowerShell-exemplet](#example) .

# <a name="python"></a>[Python](#tab/python)

Det finns två alternativ för att placera ett rad meddelande för tabell lagring från en funktion:

- **RETUR värde**: ange `name` egenskapen i *function.jspå* till `$return` . Med den här konfigurationen sparas funktionens retur värde som en tabell lagrings rad.

- **Tvingande**: Skicka ett värde till [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) -metoden för den parameter som deklarerats som [Utdatatyp.](/python/api/azure-functions/azure.functions.out) Värdet som skickas till `set` behålls som ett Event Hub-meddelande.

---

## <a name="exceptions-and-return-codes"></a>Undantag och retur koder

| Bindning | Referens |
|---|---|
| Tabell | [Tabell fel koder](/rest/api/storageservices/fileservices/table-service-error-codes) |
| BLOB, tabell, kö | [Lagrings fel koder](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabell, kö | [Felsökning](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)
