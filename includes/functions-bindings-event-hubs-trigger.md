---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 145db7693db126d4e114e8c8a885ea7fd7809e69
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608923"
---
Använd funktions utlösaren för att svara på en händelse som skickas till händelse strömmen i Event Hub. Du måste ha Läs behörighet till den underliggande händelsehubben för att konfigurera utlösaren. När funktionen utlöses skrivs meddelandet som skickas till funktionen som en sträng.

## <a name="scaling"></a>Skalning

Varje instans av en händelse utlöst funktion backas upp av en enda [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) -instans. Utlösaren (drivs av Event Hubs) säkerställer att endast en [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) -instans kan få ett lån på en specifik partition.

Ta till exempel en Event Hub enligt följande:

* 10 partitioner
* 1 000 händelser distribueras jämnt över alla partitioner, med 100-meddelanden i varje partition

När funktionen först aktive ras finns det bara en instans av funktionen. Vi kallar den första funktions instansen `Function_0` . `Function_0`Funktionen har en enda instans av [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) som innehåller ett lån på alla tio partitionerna. Den här instansen läser händelser från partitionerna 0-9. Från den här punkten sker något av följande:

* **Nya funktions instanser behövs inte**: kan `Function_0` bearbeta alla 1 000-händelser innan logiken för functions-skalning börjar gälla. I det här fallet bearbetas alla 1 000-meddelanden av `Function_0` .

* **En ytterligare funktions instans läggs till**: om logiken för functions-skalning avgör att `Function_0` har fler meddelanden än vad som kan bearbeta, skapas en ny function-instans ( `Function_1` ). Den här nya funktionen har också en associerad instans av [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor). Eftersom den underliggande Event Hubs upptäcker att en ny värd instans försöker läsa meddelanden, kan du belastningsutjämna partitionerna över värd instanserna. Partitioner 0-4 kan till exempel tilldelas till `Function_0` och partitioner 5-9 till `Function_1` .

* **N fler funktions instanser läggs till**: om logiken Functions skalning avgör att både `Function_0` och `Function_1` har fler meddelanden än vad de kan bearbeta, `Functions_N` skapas nya Function App-instanser.  Appar skapas till den plats där `N` är större än antalet Event Hub-partitioner. I vårt exempel kan Event Hubs återigen belastningsutjämna partitionerna, i det här fallet över instanserna `Function_0` ... `Functions_9` .

Som skalning inträffar `N` är instanser ett tal som är större än antalet Event Hub-partitioner. Det här mönstret används för att se till att [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) -instanser är tillgängliga för att hämta lås på partitioner när de blir tillgängliga från andra instanser. Du debiteras bara för de resurser som används när funktions instansen körs. Med andra ord debiteras du inte för den här överetableringen.

När all funktions körning slutförs (med eller utan fel) läggs kontroll punkter till i det associerade lagrings kontot. När check Pointed lyckas hämtas aldrig alla 1 000-meddelanden igen.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](../articles/azure-functions/functions-dotnet-class-library.md) som loggar meddelande texten i Event Hub-utlösaren.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

För att få åtkomst till [Event metadata](#event-metadata) i funktions kod, bind till ett [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) -objekt (kräver en using-instruktion för `Microsoft.Azure.EventHubs` ). Du kan också komma åt samma egenskaper genom att använda bindnings uttryck i Metodsignaturen.  I följande exempel visas båda sätten för att hämta samma data:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

För att ta emot händelser i en batch, skapa `string` eller `EventData` en matris.  

> [!NOTE]
> När du tar emot i en batch kan du inte binda till metod parametrar som i exemplet ovan med `DateTime enqueuedTimeUtc` och måste ta emot dessa från varje `EventData` objekt  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

I följande exempel visas en Utlös ande bindning för Event Hub i en *function.jspå* filen och en [C#-skript funktion](../articles/azure-functions/functions-reference-csharp.md) som använder bindningen. Funktionen loggar meddelande texten i Event Hub-utlösaren.

I följande exempel visas Event Hubs data bindnings data i *function.jspå* filen.

### <a name="version-2x-and-higher"></a>Version 2. x och högre

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Version 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Här är C#-skript koden:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

För att få åtkomst till [Event metadata](#event-metadata) i funktions kod, bind till ett [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) -objekt (kräver en using-instruktion för `Microsoft.Azure.EventHubs` ). Du kan också komma åt samma egenskaper genom att använda bindnings uttryck i Metodsignaturen.  I följande exempel visas båda sätten för att hämta samma data:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

För att ta emot händelser i en batch, gör `string` eller `EventData` en matris:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en Utlös ande bindning för Event Hub i en *function.jsi* filen och en [JavaScript-funktion](../articles/azure-functions/functions-reference-node.md) som använder bindningen. Funktionen läser [händelse-metadata](#event-metadata) och loggar meddelandet.

I följande exempel visas Event Hubs data bindnings data i *function.jspå* filen.

### <a name="version-2x-and-higher"></a>Version 2. x och högre

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Version 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Här är JavaScript-koden:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

För att ta emot händelser i en batch, ställer du in på `cardinality` `many` i *function.jspå* filen, som du ser i följande exempel.

### <a name="version-2x-and-higher"></a>Version 2. x och högre

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Version 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Här är JavaScript-koden:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en Utlös ande bindning för Event Hub i en *function.jspå* filen och en [python-funktion](../articles/azure-functions/functions-reference-python.md) som använder bindningen. Funktionen läser [händelse-metadata](#event-metadata) och loggar meddelandet.

I följande exempel visas Event Hubs data bindnings data i *function.jspå* filen.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Här är python-koden:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)

    # Metadata
    for key in event.metadata:
        logging.info(f'Metadata: {key} = ', event.metadata[key])
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas en trigger-bindning (Event Hub) som loggar meddelande texten i Event Hub-utlösaren.

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `EventHubTrigger` anteckningen för parametrar vars värde kommer från händelsehubben. Parametrar med dessa anteckningar gör att funktionen körs när en händelse tas emot.  Den här anteckningen kan användas med inbyggda Java-typer, Pojo eller null-värden med hjälp av `Optional<T>` .

 ---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

Använd attributet [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) i [C#-klass bibliotek](../articles/azure-functions/functions-dotnet-class-library.md).

Attributets konstruktor tar namnet på händelsehubben, namnet på konsument gruppen och namnet på en app-inställning som innehåller anslutnings strängen. Mer information om de här inställningarna finns i [avsnittet om utlösarens konfiguration](#configuration). Här är ett `EventHubTriggerAttribute` exempel på attribut:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i [exempel på Utlös ande C#](#example).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="java"></a>[Java](#tab/java)

I Java [Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du [EventHubTrigger](/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) -anteckningen för parametrar vars värde kommer från händelsehubben. Parametrar med dessa anteckningar gör att funktionen körs när en händelse tas emot. Den här anteckningen kan användas med inbyggda Java-typer, Pojo eller null-värden med hjälp av `Optional<T>` .

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `EventHubTrigger` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp** | saknas | Måste anges till `eventHubTrigger` . Den här egenskapen anges automatiskt när du skapar utlösaren i Azure Portal.|
|**position** | saknas | Måste anges till `in` . Den här egenskapen anges automatiskt när du skapar utlösaren i Azure Portal. |
|**Namn** | saknas | Namnet på variabeln som representerar händelse posten i funktions koden. |
|**sökväg** |**EventHubName** | Functions 1. x. Namnet på händelsehubben. När namnet på händelsehubben också finns i anslutnings strängen, åsidosätter det värdet den här egenskapen vid körning. |
|**eventHubName** |**EventHubName** | Funktioner 2. x och högre. Namnet på händelsehubben. När namnet på händelsehubben också finns i anslutnings strängen, åsidosätter det värdet den här egenskapen vid körning. Kan refereras via [app-inställningar](../articles/azure-functions/functions-bindings-expressions-patterns.md#binding-expressions---app-settings)`%eventHubName%` |
|**consumerGroup** |**ConsumerGroup** | En valfri egenskap som anger den [konsument grupp](../articles/event-hubs/event-hubs-features.md#event-consumers) som används för att prenumerera på händelser i hubben. Om detta utelämnas `$Default` används konsument gruppen. |
|**kardinalitet** | saknas | Används för alla språk som inte är C-C. Ställ in på för `many` att aktivera batchbearbetning.  Om detta utelämnas eller anges till `one` skickas ett enskilt meddelande till funktionen.<br><br>I C# tilldelas den här egenskapen automatiskt när utlösaren har en matris för typen.|
|**anslutningen** |**Anslutning** | Namnet på en app-inställning som innehåller anslutnings strängen till Event Hub-namnområdet. Kopiera den här anslutnings strängen genom att klicka på knappen **anslutnings information** för [namn området](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), inte själva händelsehubben. Den här anslutnings strängen måste ha minst Läs behörighet för att aktivera utlösaren.<br><br>Om du använder [version 5. x eller högre av tillägget](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher), i stället för en anslutnings sträng, kan du ange en referens till ett konfigurations avsnitt som definierar anslutningen. Se [anslutningar](../articles/azure-functions/functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Standardvärde

Du kan använda följande parameter typer för att utlösa händelsehubben:

* `string`
* `byte[]`
* `POCO`
* `EventData` -Standard egenskaperna för EventData anges i [namn området för Microsoft. Azure. EventHubs](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet).

### <a name="additional-types"></a>Ytterligare typer 
Appar som använder 5.0.0 eller senare versioner av Event Hub-tillägget använder `EventData` typen i [Azure. Messaging. EventHubs](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) i stället för det som är i [namn rymden Microsoft. Azure. EventHubs](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet). Den här versionen har stöd för den äldre `Body` typen som prioriteras av följande typer:

- [EventBody](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

### <a name="default"></a>Standardvärde

Du kan använda följande parameter typer för att utlösa händelsehubben:

* `string`
* `byte[]`
* `POCO`
* `EventData` -Standard egenskaperna för EventData anges i [namn området för Microsoft. Azure. EventHubs](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet).

### <a name="additional-types"></a>Ytterligare typer 
Appar som använder 5.0.0 eller senare versioner av Event Hub-tillägget använder `EventData` typen i [Azure. Messaging. EventHubs](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) i stället för det som är i [namn rymden Microsoft. Azure. EventHubs](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet). Den här versionen har stöd för den äldre `Body` typen som prioriteras av följande typer:

- [EventBody](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="java"></a>[Java](#tab/java)

Se exempel på Java- [utlösare](#example) för mer information.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Mer information finns i exempel på JavaScript- [utlösare](#example) .

# <a name="python"></a>[Python](#tab/python)

Mer information finns i exempel på python- [utlösare](#example) .


---


## <a name="event-metadata"></a>Metadata för händelse

Event Hubs utlösaren innehåller flera [Egenskaper för metadata](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Egenskaper för metadata kan användas som en del av bindnings uttryck i andra bindningar eller som parametrar i koden. Egenskaperna kommer från [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) -klassen.

|Egenskap|Typ|Beskrivning|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext`Instansen.|
|`EnqueuedTimeUtc`|`DateTime`|Den köade tiden i UTC.|
|`Offset`|`string`|Data förskjutningen i förhållande till partitions strömmen för Händelsehubben. Offset är en markör eller identifierare för en händelse i Event Hubs data strömmen. Identifieraren är unik i en partition av Event Hubs data strömmen.|
|`PartitionKey`|`string`|Den partition som händelse data ska skickas till.|
|`Properties`|`IDictionary<String,Object>`|Användar egenskaperna för händelse data.|
|`SequenceNumber`|`Int64`|Händelsens logiska ordnings nummer.|
|`SystemProperties`|`IDictionary<String,Object>`|System egenskaper, inklusive händelse data.|

Se [kod exempel](#example) som använder dessa egenskaper tidigare i den här artikeln.