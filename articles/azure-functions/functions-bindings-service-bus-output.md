---
title: Azure Service Bus utgående bindningar för Azure Functions
description: Lär dig att skicka Azure Service Bus meddelanden från Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: ae2be8dbcb4839c7d16b864c484c3360fdcfe324
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425591"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Service Bus utgående bindning för Azure Functions

Använd Azure Service Bus utgående bindning för att skicka kö-eller ämnes meddelanden.

Information om konfiguration och konfigurations information finns i [översikten](functions-bindings-service-bus.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som skickar ett Service Bus Queue-meddelande:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

I följande exempel visas en Service Bus utgående bindning i en *function.jspå* filen och en [C#-skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen använder en timer-utlösare för att skicka ett köat meddelande var 15: e sekund.

Här är bindnings data i *function.jspå* filen:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Här är C#-skript kod som skapar ett enda meddelande:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Här är C#-skript kod som skapar flera meddelanden:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas en Java-funktion som skickar ett meddelande till en Service Bus `myqueue` -kö när den utlöses av en HTTP-begäran.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@QueueOutput` anteckningen för funktions parametrar vars värde skrivs till en Service Bus kö.  Parameter typen bör vara `OutputBinding<T>` , där T är en ursprunglig Java-typ för en POJO.

Java-funktioner kan också skriva till ett Service Bus ämne. I följande exempel används `@ServiceBusTopicOutput` anteckningen för att beskriva konfigurationen för utgående bindning. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en Service Bus utgående bindning i en *function.jsi* filen och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen använder en timer-utlösare för att skicka ett köat meddelande var 15: e sekund.

Här är bindnings data i *function.jspå* filen:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Här är skript koden för Java Script som skapar ett enda meddelande:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Här är skript koden för Java Script som skapar flera meddelanden:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

I följande exempel visas en Service Bus utgående bindning i en *function.jsi* filen och en [PowerShell-funktion](functions-reference-powershell.md) som använder bindningen. 

Här är bindnings data i *function.jspå* filen:

```json
{
  "bindings": [
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "outputSbMsg",
      "queueName": "outqueue",
      "topicName": "outtopic"
    }
  ]
}
```

Här är PowerShell som skapar ett meddelande som funktionens utdata.

```powershell
param($QueueItem, $TriggerMetadata) 

Push-OutputBinding -Name outputSbMsg -Value @{ 
    name = $QueueItem.name 
    employeeId = $QueueItem.employeeId 
    address = $QueueItem.address 
} 
```

# <a name="python"></a>[Python](#tab/python)

Följande exempel visar hur du skriver ut till en Service Bus kö i python.

En Service Bus bindnings definition definieras i *function.jspå* WHERE- *typ* har angetts till `serviceBus` .

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
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

I *_\_ init_ \_ . py* kan du skriva ut ett meddelande till kön genom att skicka ett värde till- `set` metoden.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

Använd [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs)i [C#-klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar namnet på kön eller ämnet och prenumerationen. Du kan också ange anslutningens behörigheter. Hur du väljer inställningen åtkomst rättigheter beskrivs i avsnittet [utdata-konfiguration](#configuration) . Här är ett exempel som visar attributet som används för funktionens retur värde:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Du kan ange `Connection` egenskapen för att ange namnet på en app-inställning som innehåller Service Bus anslutnings strängen som ska användas, som du ser i följande exempel:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i [utdata-exempel](#example).

Du kan använda `ServiceBusAccount` attributet för att ange Service Bus konto som ska användas på klass-, metod-eller parameter nivå.  Mer information finns i avsnittet om [Utlösar-attribut](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="java"></a>[Java](#tab/java)

`ServiceBusQueueOutput` `ServiceBusTopicOutput` Anteckningarna och är tillgängliga för att skriva ett meddelande som ett funktions resultat. Parametern dekorerade dessa anteckningar måste deklareras som en `OutputBinding<T>` WHERE `T` är den typ som motsvarar meddelandets typ.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribut stöds inte av PowerShell.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `ServiceBus` attributet.

|function.jspå egenskap | Attributets egenskap |Description|
|---------|---------|----------------------|
|**bastyp** | saknas | Måste vara inställd på "Service Bus". Den här egenskapen anges automatiskt när du skapar utlösaren i Azure Portal.|
|**position** | saknas | Måste anges till "out". Den här egenskapen anges automatiskt när du skapar utlösaren i Azure Portal. |
|**Namn** | saknas | Namnet på variabeln som representerar kön eller ämnes meddelandet i funktions koden. Ange till "$return" för att referera till funktionens retur värde. |
|**queueName**|**QueueName**|Köns namn.  Ange endast om köa meddelanden ska skickas, inte för ett ämne.
|**topicName**|**TopicName**|Namn på ämnet. Ange endast om meddelande ämnen skickas, inte för en kö.|
|**anslutningen**|**Anslutning**|Namnet på en app-inställning som innehåller den Service Bus anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet. Om du till exempel anger `connection` "MyServiceBus" söker Functions-körningen efter en app-inställning med namnet "AzureWebJobsMyServiceBus". Om du lämnar `connection` tomt använder Functions-körningen standard Service Bus anslutnings strängen i appens inställning med namnet "AzureWebJobsServiceBus".<br><br>Om du vill hämta en anslutnings sträng följer du stegen som visas i [Hämta autentiseringsuppgifter för hantering](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Anslutnings strängen måste vara för ett Service Bus-namnområde, inte begränsat till en viss kö eller ett ämne.|
|**accessRights** (endast v1)|**Åtkomst**|Åtkomst behörighet för anslutnings strängen. Tillgängliga värden är `manage` och `listen` . Standardvärdet är `manage` , vilket anger att `connection` har behörigheten **Hantera** . Om du använder en anslutnings sträng som inte har behörigheten **Hantera** , anger `accessRights` du "lyssna". I annat fall kan funktions körningen Miss kan försöka utföra åtgärder som kräver behörighet att hantera. I Azure Functions version 2. x och högre är den här egenskapen inte tillgänglig eftersom den senaste versionen av Service Bus SDK inte stöder hanterings åtgärder.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

I Azure Functions 1. x skapar körnings kön om den inte finns och du har angett `accessRights` till `manage` . I functions version 2. x och senare måste kön eller ämnet redan finnas. Om du anger en kö eller ett ämne som inte finns fungerar inte funktionen. 

# <a name="c"></a>[C#](#tab/csharp)

Använd följande parameter typer för utgående bindning:

* `out T paramName` - `T` kan vara valfri JSON-serialiserbar typ. Om parametervärdet är null när funktionen avslutas, skapar funktionen ett meddelande med ett null-objekt.
* `out string` -Om parametervärdet är null när funktionen avslutas, skapas inget meddelande i functions.
* `out byte[]` -Om parametervärdet är null när funktionen avslutas, skapas inget meddelande i functions.
* `out BrokeredMessage` -Om parametervärdet är null när funktionen avslutas, skapar inte Functions ett meddelande (för funktioner 1. x)
* `out Message` – Om parametervärdet är null när funktionen avslutas, skapar inte Functions ett meddelande (för funktioner 2. x och högre)
* `ICollector<T>` eller `IAsyncCollector<T>` (för asynkrona metoder) – för att skapa flera meddelanden. Ett meddelande skapas när du anropar- `Add` metoden.

När du arbetar med C#-funktioner:

* Async Functions behöver ett retur värde eller `IAsyncCollector` i stället för en `out` parameter.

* För att få åtkomst till sessions-ID: t binder du till en [`Message`](/dotnet/api/microsoft.azure.servicebus.message) typ och använder `sessionId` egenskapen.

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Använd följande parameter typer för utgående bindning:

* `out T paramName` - `T` kan vara valfri JSON-serialiserbar typ. Om parametervärdet är null när funktionen avslutas, skapar funktionen ett meddelande med ett null-objekt.
* `out string` -Om parametervärdet är null när funktionen avslutas, skapas inget meddelande i functions.
* `out byte[]` -Om parametervärdet är null när funktionen avslutas, skapas inget meddelande i functions.
* `out BrokeredMessage` -Om parametervärdet är null när funktionen avslutas, skapar inte Functions ett meddelande (för funktioner 1. x)
* `out Message` – Om parametervärdet är null när funktionen avslutas, skapar inte Functions ett meddelande (för funktioner 2. x och högre)
* `ICollector<T>` eller `IAsyncCollector<T>` – för att skapa flera meddelanden. Ett meddelande skapas när du anropar- `Add` metoden.

När du arbetar med C#-funktioner:

* Async Functions behöver ett retur värde eller `IAsyncCollector` i stället för en `out` parameter.

* För att få åtkomst till sessions-ID: t binder du till en [`Message`](/dotnet/api/microsoft.azure.servicebus.message) typ och använder `sessionId` egenskapen.

# <a name="java"></a>[Java](#tab/java)

Använd [Azure Service Bus SDK](../service-bus-messaging/index.yml) i stället för den inbyggda utgående bindningen.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Få åtkomst till kön eller ämnet med hjälp av `context.bindings.<name from function.json>` . Du kan tilldela en sträng, en byte mat ris eller ett JavaScript-objekt (avserialiserat till JSON) till `context.binding.<name>` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Utdata till Service Bus är tillgängligt via `Push-OutputBinding` cmdleten där du skickar argument som matchar namnet som anges av bindningens namn parameter i *function.jsi* filen.

# <a name="python"></a>[Python](#tab/python)

Använd [Azure Service Bus SDK](../service-bus-messaging/index.yml) i stället för den inbyggda utgående bindningen.

---

## <a name="exceptions-and-return-codes"></a>Undantag och retur koder

| Bindning | Referens |
|---|---|
| Service Bus | [Service Bus felkoder](../service-bus-messaging/service-bus-messaging-exceptions.md) |
| Service Bus | [Service Bus gränser](../service-bus-messaging/service-bus-quotas.md) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.jspå Inställningar

I det här avsnittet beskrivs de globala konfigurations inställningarna som är tillgängliga för den här bindningen i version 2. x och högre. Exemplet host.jspå filen nedan innehåller bara inställningarna för den här bindningen. Mer information om globala konfigurations inställningar finns i [host.jsreferens för Azure Functions version](functions-host-json.md).

> [!NOTE]
> En referens för host.jspå i functions 1. x finns i [host.jsreferens för Azure Functions 1. x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

Om du har `isSessionsEnabled` ställt in till `true` , `sessionHandlerOptions` kommer att användas.  Om du har `isSessionsEnabled` ställt in till `false` , `messageHandlerOptions` kommer att användas.

|Egenskap  |Standardvärde | Description |
|---------|---------|---------|
|prefetchCount|0|Hämtar eller anger antalet meddelanden som meddelande mottagaren samtidigt kan begära.|
|maxAutoRenewDuration|00:05:00|Den längsta tid som meddelande låset ska förnyas automatiskt.|
|Automatisk|true|Anger om utlösaren ska anropa Complete efter bearbetning eller om funktions koden ska anropas manuellt.<br><br>Inställningen till `false` stöds bara i C#.<br><br>Om detta är inställt på `true` , slutför utlösaren meddelandet automatiskt om funktions körningen slutförs utan problem, och överger meddelandet annars.<br><br>När det är inställt på `false` , ansvarar du för att anropa [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) -metoder för att slutföra, överge eller obeställbara meddelanden kön meddelandet. Om ett undantag genereras (och ingen av `MessageReceiver` metoderna anropas), kommer låset att fortsätta. När låset har gått ut placeras meddelandet i kö igen `DeliveryCount` och låset förnyas automatiskt.<br><br>I icke-C #-funktioner resulterar undantag i funktionen i körnings anropen `abandonAsync` i bakgrunden. Om inget undantag inträffar, `completeAsync` anropas sedan i bakgrunden. |
|maxConcurrentCalls|16|Det maximala antalet samtidiga anrop till motringningen som meddelande pumpen ska initiera per skalad instans. Som standard bearbetar Functions-körningen flera meddelanden samtidigt.|
|maxConcurrentSessions|2000|Maximalt antal sessioner som kan hanteras samtidigt per skalad instans.|

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion när en Service Bus kö eller ett ämnes meddelande skapas (utlösare)](./functions-bindings-service-bus-trigger.md)
