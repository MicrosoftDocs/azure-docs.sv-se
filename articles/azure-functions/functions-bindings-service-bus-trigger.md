---
title: Azure Service Bus utlösare för Azure Functions
description: Lär dig hur du kör en Azure-funktion när Azure Service Bus-meddelanden skapas.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4b95c25400317b2baac694f4ba2b1b1dc1eae098
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435162"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Azure Service Bus utlösare för Azure Functions

Använd Service Bus-utlösaren för att svara på meddelanden från en Service Bus kö eller ett ämne. Från och med Extension version 3.1.0 kan du aktivera en kö eller ett ämne som är i kö.

Information om konfiguration och konfigurations information finns i [översikten](functions-bindings-service-bus.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som läser [meddelandets metadata](#message-metadata) och loggar ett Service Bus Queue-meddelande:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

I följande exempel visas en Service Bus trigger-bindning i en *function.jspå* filen och en [C#-skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen läser [meddelandets metadata](#message-metadata) och loggar ett Service Bus Queue-meddelande.

Här är bindnings data i *function.jspå* filen:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Här är C#-skript koden:

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

# <a name="java"></a>[Java](#tab/java)

Följande Java-funktion använder `@ServiceBusQueueTrigger` anteckningen från Java Functions [runtime-biblioteket](/java/api/overview/azure/functions/runtime) för att beskriva konfigurationen för en Service Bus Queue-utlösare. Funktionen tar bort meddelandet som placerats i kön och lägger till det i loggarna.

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

Java-funktioner kan också aktive ras när ett meddelande läggs till i ett Service Bus ämne. I följande exempel används `@ServiceBusTopicTrigger` anteckningen för att beskriva utlösarens konfiguration.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en Service Bus trigger-bindning i en *function.jsi* filen och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen läser [meddelandets metadata](#message-metadata) och loggar ett Service Bus Queue-meddelande.

Här är bindnings data i *function.jspå* filen:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Här är skript koden för Java Script:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

I följande exempel visas en Service Bus trigger-bindning i en *function.jsi* filen och en [PowerShell-funktion](functions-reference-powershell.md) som använder bindningen. 

Här är bindnings data i *function.jspå* filen:

```json
{
  "bindings": [
    {
      "name": "mySbMsg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "topicName": "mytopic",
      "subscriptionName": "mysubscription",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

Här är en funktion som körs när ett Service Bus meddelande skickas.

```powershell
param([string] $mySbMsg, $TriggerMetadata)

Write-Host "PowerShell ServiceBus queue trigger function processed message: $mySbMsg"
```

# <a name="python"></a>[Python](#tab/python)

Följande exempel visar hur du läser ett Service Bus Queue-meddelande via en utlösare.

En Service Bus bindning definieras i *function.jspå* WHERE- *typ* har angetts till `serviceBusTrigger` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

Koden i *_\_ init_ \_ . py* deklarerar en parameter som `func.ServiceBusMessage` , vilket gör att du kan läsa meddelandet i kön i din funktion.

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
        'metadata' : msg.metadata
    })

    logging.info(result)
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

Använd följande attribut i [C#-klass bibliotek](functions-dotnet-class-library.md)för att konfigurera en Service Bus-utlösare:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Attributets konstruktor tar namnet på kön eller ämnet och prenumerationen. I Azure Functions version 1. x kan du också ange anslutningens behörigheter. Om du inte anger åtkomst behörighet är standardvärdet `Manage` . Mer information finns i avsnittet om [Utlösar-konfiguration](#configuration) .

  Här är ett exempel som visar attributet som används med en sträng parameter:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Eftersom `Connection` egenskapen inte är definierad söker Functions efter en app-inställning med namnet `AzureWebJobsServiceBus` , som är standard namnet för Service Bus anslutnings strängen. Du kan också ange `Connection` egenskapen för att ange namnet på en program inställning som innehåller den Service Bus anslutnings sträng som ska användas, som du ser i följande exempel:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Ett fullständigt exempel finns i [Utlös ande-exempel](#example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Ger ett annat sätt att ange det Service Bus konto som ska användas. Konstruktorn tar namnet på en app-inställning som innehåller en Service Bus anslutnings sträng. Attributet kan användas på parameter-, metod-eller klass nivå. I följande exempel visas klass nivå och metod nivå:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

Service Bus kontot som ska användas fastställs i följande ordning:

* `ServiceBusTrigger`Attributets `Connection` egenskap.
* `ServiceBusAccount`Attributet som används för samma parameter som `ServiceBusTrigger` attributet.
* `ServiceBusAccount`Attributet som används för funktionen.
* `ServiceBusAccount`Attributet som används för klassen.
* App-inställningen "AzureWebJobsServiceBus".

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="java"></a>[Java](#tab/java)

Med `ServiceBusQueueTrigger` anteckningen kan du skapa en funktion som körs när ett Service Bus Queue-meddelande skapas. Tillgängliga konfigurations alternativ inkluderar könamn och namn på anslutnings sträng.

Med `ServiceBusTopicTrigger` anteckningen kan du ange ett ämne och en prenumeration för att rikta in dig på vilka data som utlöser funktionen.

Se utlösnings [exemplet](#example) för mer information.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribut stöds inte av PowerShell.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `ServiceBusTrigger` attributet.

|function.jspå egenskap | Attributets egenskap |Description|
|---------|---------|----------------------|
|**bastyp** | saknas | Måste vara inställd på "serviceBusTrigger". Den här egenskapen anges automatiskt när du skapar utlösaren i Azure Portal.|
|**position** | saknas | Måste vara inställt på "in". Den här egenskapen anges automatiskt när du skapar utlösaren i Azure Portal. |
|**Namn** | saknas | Namnet på variabeln som representerar kön eller ämnes meddelandet i funktions koden. |
|**queueName**|**QueueName**|Namnet på kön som ska övervakas.  Ange endast om övervakning av en kö, inte för ett ämne.
|**topicName**|**TopicName**|Namn på det ämne som ska övervakas. Ange endast om du övervakar ett ämne, inte för en kö.|
|**subscriptionName**|**SubscriptionName**|Namnet på den prenumeration som ska övervakas. Ange endast om du övervakar ett ämne, inte för en kö.|
|**anslutningen**|**Anslutning**|Namnet på en app-inställning som innehåller den Service Bus anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet. Om du till exempel anger `connection` "MyServiceBus" söker Functions-körningen efter en app-inställning med namnet "AzureWebJobsMyServiceBus". Om du lämnar `connection` tomt använder Functions-körningen standard Service Bus anslutnings strängen i appens inställning med namnet "AzureWebJobsServiceBus".<br><br>Om du vill hämta en anslutnings sträng följer du stegen som visas i [Hämta autentiseringsuppgifter för hantering](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Anslutnings strängen måste vara för ett Service Bus-namnområde, inte begränsat till en viss kö eller ett ämne. |
|**accessRights**|**Åtkomst**|Åtkomst behörighet för anslutnings strängen. Tillgängliga värden är `manage` och `listen` . Standardvärdet är `manage` , vilket anger att `connection` har behörigheten **Hantera** . Om du använder en anslutnings sträng som inte har behörigheten **Hantera** , anger `accessRights` du "lyssna". I annat fall kan funktions körningen Miss kan försöka utföra åtgärder som kräver behörighet att hantera. I Azure Functions version 2. x och högre är den här egenskapen inte tillgänglig eftersom den senaste versionen av Service Bus SDK inte stöder hanterings åtgärder.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true` Om du ansluter till en session som är [medveten](../service-bus-messaging/message-sessions.md) om en kö eller prenumeration. `false` Annars, vilket är standardvärdet.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

Följande parameter typer är tillgängliga för kön eller ämnes meddelandet:

* `string` – Om meddelandet är text.
* `byte[]` – Användbart för binära data.
* En anpassad typ – om meddelandet innehåller JSON Azure Functions försöker deserialisera JSON-data.
* `BrokeredMessage` – Ger dig det deserialiserade meddelandet med metoden [BrokeredMessage. GetBody \<T> ()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .
* [`MessageReceiver`](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) – Används för att ta emot och bekräfta meddelanden från meddelande behållaren (krävs när [`autoComplete`](functions-bindings-service-bus-output.md#hostjson-settings) har angetts till `false` )

Dessa parameter typer är för Azure Functions version 1. x. för 2. x och högre använder du [`Message`](/dotnet/api/microsoft.azure.servicebus.message) i stället för `BrokeredMessage` .

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Följande parameter typer är tillgängliga för kön eller ämnes meddelandet:

* `string` – Om meddelandet är text.
* `byte[]` – Användbart för binära data.
* En anpassad typ – om meddelandet innehåller JSON Azure Functions försöker deserialisera JSON-data.
* `BrokeredMessage` – Ger dig det deserialiserade meddelandet med metoden [BrokeredMessage. GetBody \<T> ()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Dessa parametrar är för Azure Functions version 1. x. för 2. x och högre använder du [`Message`](/dotnet/api/microsoft.azure.servicebus.message) i stället för `BrokeredMessage` .

# <a name="java"></a>[Java](#tab/java)

Det inkommande Service Buss meddelandet är tillgängligt via en- `ServiceBusQueueMessage` eller- `ServiceBusTopicMessage` parameter.

[Se exemplet för mer information](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Få åtkomst till kön eller ämnes meddelandet med hjälp av `context.bindings.<name from function.json>` . Service Bus meddelandet skickas till funktionen som antingen en sträng eller ett JSON-objekt.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Service Bus-instansen är tillgänglig via den parameter som kon figurer ATS i egenskapen *function.jsi* filens namn.

# <a name="python"></a>[Python](#tab/python)

Queue-meddelandet är tillgängligt för funktionen via en parameter som har angetts som `func.ServiceBusMessage` . Service Bus meddelandet skickas till funktionen som antingen en sträng eller ett JSON-objekt.

---

## <a name="poison-messages"></a>Poison-meddelanden

Hantering av skadligt meddelande kan inte styras eller konfigureras i Azure Functions. Service Bus hanterar själva Poison-meddelandena.

## <a name="peeklock-behavior"></a>PeekLock beteende

Functions-körningen tar emot ett meddelande i [PeekLock-läge](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Det anropar `Complete` meddelandet om funktionen har slutförts eller anropas `Abandon` om funktionen Miss lyckas. Om funktionen körs längre än `PeekLock` tids gränsen förnyas låset automatiskt så länge funktionen körs.

`maxAutoRenewDuration`Kan konfigureras i *host.jspå*, som mappar till [OnMessageOptions. MaxAutoRenewDuration](/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration). Det högsta tillåtna värdet för den här inställningen är 5 minuter enligt Service Bus-dokumentationen, men du kan öka tids gränsen för funktioner från standardvärdet 5 minuter till 10 minuter. För Service Bus funktioner vill du inte göra det eftersom du skulle överskrida den Service Bus förnyelse gränsen.

## <a name="message-metadata"></a>Metadata för meddelande

Service Bus utlösaren innehåller flera [Egenskaper för metadata](./functions-bindings-expressions-patterns.md#trigger-metadata). Dessa egenskaper kan användas som en del av bindnings uttryck i andra bindningar eller som parametrar i koden. De här egenskaperna är medlemmar i [meddelande](/dotnet/api/microsoft.azure.servicebus.message) klassen.

|Egenskap|Typ|Description|
|--------|----|-----------|
|`ContentType`|`string`|En innehålls typ identifierare som används av avsändaren och mottagaren för programspecifik logik.|
|`CorrelationId`|`string`|Korrelations-ID: t.|
|`DeadLetterSource`|`string`|Källan för obeställbara meddelanden.|
|`DeliveryCount`|`Int32`|Antalet leveranser.|
|`EnqueuedTimeUtc`|`DateTime`|Den köade tiden i UTC.|
|`ExpiresAtUtc`|`DateTime`|Förfallo tiden i UTC.|
|`Label`|`string`|Den programspecifika etiketten.|
|`MessageId`|`string`|Ett användardefinierat värde som Service Bus kan använda för att identifiera duplicerade meddelanden, om det är aktiverat.|
|`MessageReceiver`|`MessageReceiver`|Service Bus meddelande mottagare. Kan användas för att överge, slutföra eller obeställbara meddelanden kön meddelandet.|
|`MessageSession`|`MessageSession`|En meddelande mottagare som är specifikt för sessionsbaserade köer och ämnen.|
|`ReplyTo`|`string`|Svar på Queue-adress.|
|`SequenceNumber`|`long`|Det unika nummer som tilldelas ett meddelande av Service Bus.|
|`To`|`string`|Skicka till-adressen.|
|`UserProperties`|`IDictionary<string, object>`|Egenskaper som anges av avsändaren.|

Se [kod exempel](#example) som använder dessa egenskaper tidigare i den här artikeln.

## <a name="next-steps"></a>Nästa steg

- [Skicka Azure Service Bus meddelanden från Azure Functions (utgående bindning)](./functions-bindings-service-bus-output.md)
