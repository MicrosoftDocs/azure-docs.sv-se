---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: dadd86521a7b6c20dab2ed036555b798b869344c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511103"
---
Använd Event Hubs utgående bindning för att skriva händelser till en händelse ström. Du måste ha behörighet att skicka till en händelsehubb för att kunna skicka händelser till den.

Se till att de nödvändiga paket referenserna är på plats innan du försöker implementera en utgående bindning.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](../articles/azure-functions/functions-dotnet-class-library.md) som skriver ett meddelande till en Event Hub med hjälp av metoden Return-värdet som utdata:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

I följande exempel visas hur du använder `IAsyncCollector` gränssnittet för att skicka en batch med meddelanden. Det här scenariot är vanligt när du bearbetar meddelanden som kommer från en Händelsehubben och skickar resultatet till en annan Händelsehubben.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

I följande exempel visas en Utlös ande bindning för Event Hub i en *function.jspå* filen och en [C#-skript funktion](../articles/azure-functions/functions-reference-csharp.md) som använder bindningen. Funktionen skriver ett meddelande till en Event Hub.

I följande exempel visas Event Hubs data bindnings data i *function.jspå* filen. Det första exemplet är för functions 2. x och högre, och det andra är för funktionerna 1. x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Här är C#-skript kod som skapar ett meddelande:

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Här är C#-skript kod som skapar flera meddelanden:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en Utlös ande bindning för Event Hub i en *function.jsi* filen och en [JavaScript-funktion](../articles/azure-functions/functions-reference-node.md) som använder bindningen. Funktionen skriver ett meddelande till en Event Hub.

I följande exempel visas Event Hubs data bindnings data i *function.jspå* filen. Det första exemplet är för functions 2. x och högre, och det andra är för funktionerna 1. x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Här är JavaScript-kod som skickar ett enda meddelande:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Här är JavaScript-kod som skickar flera meddelanden:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en Utlös ande bindning för Event Hub i en *function.jspå* filen och en [python-funktion](../articles/azure-functions/functions-reference-python.md) som använder bindningen. Funktionen skriver ett meddelande till en Event Hub.

I följande exempel visas Event Hubs data bindnings data i *function.jspå* filen.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Här är python-koden som skickar ett enda meddelande:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas en Java-funktion som skriver ett meddelande som innehåller den aktuella tiden till en Event Hub.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@EventHubOutput` anteckningen för parametrar vars värde skulle publiceras till Event Hub.  Parametern ska vara av typen `OutputBinding<T>` , där T är en POJO eller en ursprunglig Java-typ.

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

För [klass bibliotek i C#](../articles/azure-functions/functions-dotnet-class-library.md)använder du attributet [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs) .

Attributets konstruktor tar namnet på händelsehubben och namnet på en app-inställning som innehåller anslutnings strängen. Mer information om de här inställningarna finns i [output-Configuration](#configuration). Här är ett `EventHub` exempel på attribut:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i [exempel på utdata-C#](#example).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="java"></a>[Java](#tab/java)

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du antecknings anteckningen [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) för parametrar vars värde skulle publiceras till Event Hub. Parametern ska vara av typen `OutputBinding<T>` , där `T` är en POJO eller en ursprunglig Java-typ.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `EventHub` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp** | saknas | Måste vara inställd på eventHub. |
|**position** | saknas | Måste anges till "out". Den här parametern anges automatiskt när du skapar bindningen i Azure Portal. |
|**Namn** | saknas | Variabel namnet som används i funktions koden som representerar händelsen. |
|**sökväg** |**EventHubName** | Functions 1. x. Namnet på händelsehubben. När namnet på händelsehubben också finns i anslutnings strängen, åsidosätter det värdet den här egenskapen vid körning. |
|**eventHubName** |**EventHubName** | Funktioner 2. x och högre. Namnet på händelsehubben. När namnet på händelsehubben också finns i anslutnings strängen, åsidosätter det värdet den här egenskapen vid körning. |
|**anslutningen** |**Anslutning** | Namnet på en app-inställning som innehåller anslutnings strängen till Event Hub-namnområdet. Kopiera den här anslutnings strängen genom att klicka på knappen **anslutnings information** för *namn området*, inte själva händelsehubben. Den här anslutnings strängen måste ha Send-behörighet för att kunna skicka meddelandet till händelse strömmen.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

Skicka meddelanden med hjälp av en metod parameter, till exempel `out string paramName` . I C#-skript `paramName` är det värde som anges i `name` egenskapen för *function.jspå*. Om du vill skriva flera meddelanden kan du använda `ICollector<string>` eller `IAsyncCollector<string>` i stället för `out string` .

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Skicka meddelanden med hjälp av en metod parameter, till exempel `out string paramName` . I C#-skript `paramName` är det värde som anges i `name` egenskapen för *function.jspå*. Om du vill skriva flera meddelanden kan du använda `ICollector<string>` eller `IAsyncCollector<string>` i stället för `out string` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Få åtkomst till utdata-händelsen `context.bindings.<name>` genom `<name>` att använda WHERE är värdet som anges i `name` egenskapen för *function.jspå*.

# <a name="python"></a>[Python](#tab/python)

Det finns två alternativ för att placera ett Event Hub-meddelande från en funktion:

- **RETUR värde**: ange `name` egenskapen i *function.jspå* till `$return` . Med den här konfigurationen sparas funktionens retur värde som ett Event Hub-meddelande.

- **Tvingande**: Skicka ett värde till [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) -metoden för den parameter som deklarerats som [Utdatatyp.](/python/api/azure-functions/azure.functions.out) Värdet som skickas till `set` behålls som ett Event Hub-meddelande.

# <a name="java"></a>[Java](#tab/java)

Det finns två alternativ för att mata ut ett Event Hub-meddelande från en funktion med hjälp av [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) -anteckningen:

- **RETUR värde**: genom att använda anteckningen i själva funktionen sparas returvärdet för funktionen som ett Event Hub-meddelande.

- **Tvingande**: om du uttryckligen vill ange ett värde för meddelandet använder du anteckningen på en specifik parameter av typen [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.OutputBinding) , där `T` är en POJO eller en ursprunglig Java-typ. Med den här konfigurationen `setValue` kvarstår värdet som ett Event Hub-meddelande när du skickar ett värde till metoden.

---

## <a name="exceptions-and-return-codes"></a>Undantag och retur koder

| Bindning | Referens |
|---|---|
| Händelsehubb | [Drift guide](/rest/api/eventhub/publisher-policy-operations) |