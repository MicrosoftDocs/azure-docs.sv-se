---
title: Utgående bindning för Azure Queue Storage för Azure Functions
description: Lär dig att skapa Azure Queue Storage-meddelanden i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 778424cbb81f8fe51a57dd41d94aa9015ffad94e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381519"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Utgående bindningar för Azure Queue Storage för Azure Functions

Azure Functions kan skapa nya Azure Queue Storage-meddelanden genom att konfigurera en utgående bindning.

Information om konfiguration och konfigurations information finns i [översikten](./functions-bindings-storage-queue.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som skapar ett Queue-meddelande för varje http-begäran som tas emot.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

I följande exempel visas en HTTP trigger-bindning i en *function.jspå* fil [-och C#-skript (. CSX)](functions-reference-csharp.md) som använder bindningen. Funktionen skapar ett köobjekt med en **CustomQueueMessage** objekt nytto last för varje http-begäran som tas emot.

Här är *function.jspå* filen:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är C#-skript kod som skapar ett enda köat meddelande:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Du kan skicka flera meddelanden samtidigt med hjälp av en- `ICollector` eller- `IAsyncCollector` parameter. Här är C#-skript kod som skickar flera meddelanden, en med HTTP-begärans data och en med hårdkodade värden:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="java"></a>[Java](#tab/java)

 I följande exempel visas en Java-funktion som skapar ett Queue-meddelande för när det utlöses av en HTTP-begäran.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@QueueOutput` anteckningen för parametrar vars värde ska skrivas till Queue Storage.  Parameter typen bör vara `OutputBinding<T>` , där `T` är valfri ursprunglig Java-typ för en POJO.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en HTTP trigger-bindning i en *function.jsi* filen och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skapar ett köobjekt för varje HTTP-begäran som tas emot.

Här är *function.jspå* filen:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "myQueueItem",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

Du kan skicka flera meddelanden samtidigt genom att definiera en meddelande mat ris för `myQueueItem` utgående bindning. Följande JavaScript-kod skickar två köa meddelanden med hårdkodade värden för varje HTTP-begäran som tas emot.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Följande kod exempel visar hur du skickar ett Queue-meddelande från en HTTP-utlöst funktion. Konfigurations avsnittet med `type` i `queue` definierar utgående bindning.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "Msg",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Med den här bindnings konfigurationen kan en PowerShell-funktion skapa ett köat meddelande med hjälp av `Push-OutputBinding` . I det här exemplet skapas ett meddelande från en frågesträng eller en text parameter.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

Om du vill skicka flera meddelanden samtidigt definierar du en meddelande mat ris och använder `Push-OutputBinding` för att skicka meddelanden till den utgående bindningen för kön.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = @("message1", "message2")
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas hur du matar utdata av enstaka och flera värden till lagrings köer. Konfigurationen som krävs för *function.jspå* är på samma sätt.

En bindning för lagrings kön definieras i *function.jspå* WHERE- *typ* är inställd på `queue` .

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
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Om du vill ange ett enskilt meddelande i kön skickar du ett enda värde till- `set` metoden.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Om du vill skapa flera meddelanden i kön, deklarera en parameter som lämplig typ av lista och skicka en matris med värden (som matchar List typen) till- `set` metoden.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

Använd [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs)i [C#-klass bibliotek](functions-dotnet-class-library.md).

Attributet gäller för en `out` parameter eller returvärdet för funktionen. Attributets konstruktor tar köns namn, som visas i följande exempel:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Du kan ställa in `Connection` egenskapen för att ange det lagrings konto som ska användas, som du ser i följande exempel:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i [output-exemplet](#example).

Du kan använda `StorageAccount` attributet för att ange lagrings kontot på klass-, metod-eller parameter nivå. Mer information finns i avsnittet om Utlösar-attribut.

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="java"></a>[Java](#tab/java)

Med `QueueOutput` anteckningen kan du skriva ett meddelande som utdata till en funktion. I följande exempel visas en HTTP-utlöst funktion som skapar ett Queue-meddelande.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Egenskap    | Beskrivning |
|-------------|-----------------------------|
|`name`       | Deklarerar parameter namnet i funktions under skriften. När funktionen utlöses har den här parameterns värde innehållet i Queue-meddelandet. |
|`queueName`  | Deklarerar könamnet i lagrings kontot. |
|`connection` | Pekar på anslutnings strängen för lagrings kontot. |

Den parameter som är associerad med `QueueOutput` anteckningen skrivs som [en \<T\> OutputBinding](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) -instans.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribut stöds inte av PowerShell.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `Queue` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp** | saknas | Måste anges till `queue` . Den här egenskapen anges automatiskt när du skapar utlösaren i Azure Portal.|
|**position** | saknas | Måste anges till `out` . Den här egenskapen anges automatiskt när du skapar utlösaren i Azure Portal. |
|**Namn** | saknas | Namnet på variabeln som representerar kön i funktions koden. Ange till `$return` att referera till funktionens retur värde.|
|**queueName** |**QueueName** | Köns namn. |
|**anslutningen** | **Anslutning** |Namnet på en app-inställning som innehåller den lagrings anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här.<br><br>Om du till exempel ställer in `connection` på "telestorage" söker Functions-körningen efter en app-inställning med namnet "Storage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den angivna app-inställningen `AzureWebJobsStorage` .<br><br>Om du använder [version 5. x eller högre av tillägget](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher), i stället för en anslutnings sträng, kan du ange en referens till ett konfigurations avsnitt som definierar anslutningen. Se [anslutningar](./functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Standardvärde

Skriv ett enskilt Queue-meddelande med hjälp av en metod parameter, till exempel `out T paramName` . Du kan använda metoden Return-typ i stället för en `out` parameter och `T` kan vara någon av följande typer:

* Ett objekt som är serialiserbar som JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Om du försöker binda till `CloudQueueMessage` och få ett fel meddelande, se till att du har en referens till [rätt Storage SDK-version](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

I C#-och C#-skript skriver du flera meddelanden i kön genom att använda någon av följande typer: 

* `ICollector<T>` eller `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>Ytterligare typer

Appar som använder [5.0.0 eller en senare version av lagrings tillägget](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) kan också använda typer från [Azure SDK för .net](/dotnet/api/overview/azure/storage.queues-readme). Den här versionen har stöd för de äldre `CloudQueue` och `CloudQueueMessage` typer som du prioriterar av följande typer:

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- [QueueClient](/dotnet/api/azure.storage.queues.queueclient) för att skriva flera köa meddelanden

Exempel som använder de här typerna finns i [GitHub-lagringsplatsen för tillägget](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

### <a name="default"></a>Standardvärde

Skriv ett enskilt Queue-meddelande med hjälp av en metod parameter, till exempel `out T paramName` . `paramName`Värdet som anges i `name` egenskapen för *function.jspå*. Du kan använda metoden Return-typ i stället för en `out` parameter och `T` kan vara någon av följande typer:

* Ett objekt som är serialiserbar som JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Om du försöker binda till `CloudQueueMessage` och få ett fel meddelande, se till att du har en referens till [rätt Storage SDK-version](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

I C#-och C#-skript skriver du flera meddelanden i kön genom att använda någon av följande typer: 

* `ICollector<T>` eller `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>Ytterligare typer

Appar som använder [5.0.0 eller en senare version av lagrings tillägget](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) kan också använda typer från [Azure SDK för .net](/dotnet/api/overview/azure/storage.queues-readme). Den här versionen har stöd för de äldre `CloudQueue` och `CloudQueueMessage` typer som du prioriterar av följande typer:

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- [QueueClient](/dotnet/api/azure.storage.queues.queueclient) för att skriva flera köa meddelanden

Exempel som använder de här typerna finns i [GitHub-lagringsplatsen för tillägget](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="java"></a>[Java](#tab/java)

Det finns två alternativ för att placera ett Queue-meddelande från en funktion med hjälp av [QueueOutput](/java/api/com.microsoft.azure.functions.annotation.queueoutput) -anteckningen:

- **RETUR värde**: genom att använda anteckningen i själva funktionen sparas returvärdet för funktionen som ett Queue-meddelande.

- **Tvingande**: om du uttryckligen vill ange ett värde för meddelandet använder du anteckningen på en specifik parameter av typen [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , där `T` är en POJO eller en ursprunglig Java-typ. Med den här konfigurationen behåller ett värde till- `setValue` metoden värdet som ett Queue-meddelande.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Objektet utgående kö är tillgängligt via `context.bindings.<NAME>` där `<NAME>` matchar namnet som definierades i *function.jspå*. Du kan använda en sträng eller ett JSON-serialiserbar objekt för nytto lasten i objektet.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Utdata till Queue-meddelandet är tillgängligt via `Push-OutputBinding` där du skickar argument som matchar namnet som anges av bindnings `name` parametern i *function.jsi* filen.

# <a name="python"></a>[Python](#tab/python)

Det finns två alternativ för att placera ett Queue-meddelande från en funktion:

- **RETUR värde**: ange `name` egenskapen i *function.jspå* till `$return` . Med den här konfigurationen sparas funktionens retur värde som ett kö lagrings meddelande.

- **Tvingande**: Skicka ett värde till [set](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true#set-val--t-----none) -metoden för den parameter som deklarerats som [Utdatatyp.](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true) Värdet som skickas till `set` behålls som ett kö lagrings meddelande.

---

## <a name="exceptions-and-return-codes"></a>Undantag och retur koder

| Bindning |  Referens |
|---|---|
| Kö | [Fel koder för kö](/rest/api/storageservices/queue-service-error-codes) |
| BLOB, tabell, kö | [Lagrings fel koder](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabell, kö |  [Felsökning](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Nästa steg

- [Kör en funktion som ändringar i Queue Storage-data (utlösare)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
