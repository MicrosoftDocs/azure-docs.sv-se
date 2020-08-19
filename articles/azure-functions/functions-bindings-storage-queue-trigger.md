---
title: Azure Queue Storage-utlösare för Azure Functions
description: Lär dig hur du kör en Azure-funktion när data ändringar i Azure Queue Storage.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 1755c2d572b44f1e0d8597a108ca83d429405f25
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565663"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Azure Queue Storage-utlösare för Azure Functions

Kön Storage-utlösare kör en funktion när meddelanden läggs till i Azure Queue Storage.

## <a name="encoding"></a>Kodning

Functions förväntar sig en *base64* -kodad sträng. Eventuella justeringar av kodnings typen (för att förbereda data som en *base64* -kodad sträng) måste implementeras i anrops tjänsten.

## <a name="example"></a>Exempel

Använd kön utlösare för att starta en funktion när ett nytt objekt tas emot i en kö. Kömeddelandet anges som indata till funktionen.

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som söker efter `myqueue-items` kön och skriver en logg varje gången ett köobjekt bearbetas.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

I följande exempel visas en kö trigger-bindning i en *function.jspå* fil [-och C#-skript (. CSX)](functions-reference-csharp.md) som använder bindningen. Funktionen avsöker `myqueue-items` kön och skriver en logg varje gången ett köobjekt bearbetas.

Här är *function.jspå* filen:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är C#-skript koden:

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

I avsnittet [användning](#usage) beskrivs `myQueueItem` , som namnges av `name` egenskapen i function.jspå.  I [avsnittet meddelande metadata](#message-metadata) beskrivs alla andra variabler som visas.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en Utlös ande bindning för kö i en *function.jsi* filen och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen avsöker `myqueue-items` kön och skriver en logg varje gången ett köobjekt bearbetas.

Här är *function.jspå* filen:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

> [!NOTE]
> Parametern name återspeglar som `context.bindings.<name>` i JavaScript-koden som innehåller objektets nytto Last. Den här nytto lasten skickas också som den andra parametern till funktionen.

Här är JavaScript-koden:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

I avsnittet [användning](#usage) beskrivs `myQueueItem` , som namnges av `name` egenskapen i function.jspå.  I [avsnittet meddelande metadata](#message-metadata) beskrivs alla andra variabler som visas.

# <a name="python"></a>[Python](#tab/python)

Följande exempel visar hur du läser ett Queue-meddelande som skickas till en funktion via en utlösare.

En utlösare för lagrings kön definieras i *function.jspå* WHERE- *typ* är inställd på `queueTrigger` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Koden * _ \_ init_ \_ . py* deklarerar en parameter som `func.QueueMessage` , vilket gör att du kan läsa meddelandet i kön i din funktion.

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

# <a name="java"></a>[Java](#tab/java)

I följande Java-exempel visas en utlösnings funktion i Storage Queue, som loggar det Utlös ande meddelandet som placeras i kön `myqueuename` .

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

 ---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

Använd följande attribut i [C#-klass bibliotek](functions-dotnet-class-library.md)för att konfigurera en Queue-utlösare:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  Attributets konstruktor tar namnet på kön som ska övervakas, som visas i följande exempel:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Du kan ställa in `Connection` egenskapen för att ange den app-inställning som innehåller lagrings kontots anslutnings sträng som ska användas, som du ser i följande exempel:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Ett fullständigt exempel finns i [exemplet](#example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Ger ett annat sätt att ange det lagrings konto som ska användas. Konstruktorn tar namnet på en app-inställning som innehåller en lagrings anslutnings sträng. Attributet kan användas på parameter-, metod-eller klass nivå. I följande exempel visas klass nivå och metod nivå:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Lagrings kontot som ska användas fastställs i följande ordning:

* `QueueTrigger`Attributets `Connection` egenskap.
* `StorageAccount`Attributet som används för samma parameter som `QueueTrigger` attributet.
* `StorageAccount`Attributet som används för funktionen.
* `StorageAccount`Attributet som används för klassen.
* App-inställningen "AzureWebJobsStorage".

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="java"></a>[Java](#tab/java)

`QueueTrigger`Anteckningen ger dig åtkomst till den kö som utlöser funktionen. I följande exempel blir Queue-meddelandet tillgängligt för funktionen via `message` parametern.

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| Egenskap    | Beskrivning |
|-------------|-----------------------------|
|`name`       | Deklarerar parameter namnet i funktions under skriften. När funktionen utlöses har den här parameterns värde innehållet i Queue-meddelandet. |
|`queueName`  | Deklarerar könamnet i lagrings kontot. |
|`connection` | Pekar på anslutnings strängen för lagrings kontot. |

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `QueueTrigger` attributet.

|function.jspå egenskap | Attributets egenskap |Description|
|---------|---------|----------------------|
|**bastyp** | saknas| Måste anges till `queueTrigger` . Den här egenskapen anges automatiskt när du skapar utlösaren i Azure Portal.|
|**position**| saknas | Endast i *function.jsendast på* fil. Måste anges till `in` . Den här egenskapen anges automatiskt när du skapar utlösaren i Azure Portal. |
|**Namn** | saknas |Namnet på den variabel som innehåller köns objektets nytto Last i funktions koden.  |
|**queueName** | **QueueName**| Namnet på kön som ska avsökas. |
|**anslutningen** | **Anslutning** |Namnet på en app-inställning som innehåller den lagrings anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till exempel ställer in `connection` på "telestorage" söker Functions-körningen efter en app-inställning med namnet "Storage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den angivna app-inställningen `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

Få åtkomst till meddelande data med hjälp av en metod parameter, till exempel `string paramName` . Du kan binda till någon av följande typer:

* Objekt-funktions körningen deserialiserar en JSON-nyttolast till en instans av en godtycklig klass som definierats i din kod. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Om du försöker binda till `CloudQueueMessage` och få ett fel meddelande, se till att du har en referens till [rätt Storage SDK-version](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Få åtkomst till meddelande data med hjälp av en metod parameter, till exempel `string paramName` . `paramName`Värdet som anges i `name` egenskapen för *function.jspå*. Du kan binda till någon av följande typer:

* Objekt-funktions körningen deserialiserar en JSON-nyttolast till en instans av en godtycklig klass som definierats i din kod. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Om du försöker binda till `CloudQueueMessage` och få ett fel meddelande, se till att du har en referens till [rätt Storage SDK-version](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Objektet för köobjekt är tillgängligt via `context.bindings.<NAME>` där `<NAME>` matchar det namn som definierats i *function.jspå*. Om nytto lasten är JSON deserialiseras värdet i ett objekt.

# <a name="python"></a>[Python](#tab/python)

Få åtkomst till Queue-meddelandet via parametern som anges som [QueueMessage](/python/api/azure-functions/azure.functions.queuemessage?view=azure-python).

# <a name="java"></a>[Java](#tab/java)

[QueueTrigger](/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) -anteckningen ger dig åtkomst till det Queue meddelande som utlöste funktionen.

---

## <a name="message-metadata"></a>Metadata för meddelande

Utlösaren för kön innehåller flera [Egenskaper för metadata](./functions-bindings-expressions-patterns.md#trigger-metadata). Dessa egenskaper kan användas som en del av bindnings uttryck i andra bindningar eller som parametrar i koden. Egenskaperna är medlemmar i klassen [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) .

|Egenskap|Typ|Description|
|--------|----|-----------|
|`QueueTrigger`|`string`|Köns nytto Last (om en giltig sträng). Om nytto lasten i kön är en sträng, `QueueTrigger` har samma värde som variabeln som namnges av `name` egenskapen i *function.jspå*.|
|`DequeueCount`|`int`|Antal gånger som det här meddelandet har tagits ur kö.|
|`ExpirationTime`|`DateTimeOffset`|Tiden som meddelandet upphör att gälla.|
|`Id`|`string`|Meddelande-ID för kö.|
|`InsertionTime`|`DateTimeOffset`|Den tidpunkt då meddelandet lades till i kön.|
|`NextVisibleTime`|`DateTimeOffset`|Tiden då meddelandet kommer att visas nästa.|
|`PopReceipt`|`string`|Meddelandets pop-kvitto.|

## <a name="poison-messages"></a>Poison-meddelanden

Om en utlösare funktion Miss lyckas försöker Azure Functions utföra funktionen upp till fem gånger för ett angivet Queue-meddelande, inklusive det första försöket. Om alla fem försök Miss lyckas lägger Functions-körningen till ett meddelande i en kö med namnet * &lt; originalqueuename>-Poison*. Du kan skriva en funktion för att bearbeta meddelanden från den Poison-kön genom att logga dem eller skicka ett meddelande om att det krävs en manuell åtgärd.

Om du vill hantera Poison-meddelanden manuellt kontrollerar du [dequeueCount](#message-metadata) i Queue-meddelandet.

## <a name="polling-algorithm"></a>Avsöknings algoritm

Utlösaren för kön implementerar en slumpmässig algoritm för att ta bort inaktivt kön på lagrings transaktions kostnader.

Algoritmen använder följande logik:

- När ett meddelande hittas väntar körningen i två sekunder och söker sedan efter ett annat meddelande
- När inget meddelande hittas väntar det cirka fyra sekunder innan det försöker igen.
- Efter efterföljande misslyckade försök att hämta ett Queue-meddelande fortsätter vänte tiden att öka tills den når den maximala vänte tiden, vilket är en minut som standard.
- Maximal vänte tid kan konfigureras via `maxPollingInterval` egenskapen i [host.jsi filen](functions-host-json.md#queues).

För lokal utveckling är det maximala avsöknings intervallet två sekunder som standard.

I samband med faktureringen är tids åtgången för avsökning av körningen "kostnads fri" och inte räknas till ditt konto.

## <a name="concurrency"></a>Samtidighet

När det finns flera köa meddelanden som väntar, hämtar kön utlösare en batch med meddelanden och anropar funktions instanserna samtidigt för att bearbeta dem. Som standard är batchstorleken 16. När antalet som bearbetas går ned till 8, får körningen en annan batch och börjar bearbeta dessa meddelanden. Det maximala antalet samtidiga meddelanden som bearbetas per funktion på en virtuell dator (VM) är 24. Den här gränsen gäller separat för varje funktion som utlöses av kön på varje virtuell dator. Om din Function-app skalar ut till flera virtuella datorer väntar varje virtuell dator på utlösare och försöker köra funktioner. Om till exempel en Function-app skalar ut till tre virtuella datorer är det maximala antalet samtidiga instanser av en funktion som utlöses av en kö 72.

Batchstorleken och tröskelvärdet för att hämta en ny batch kan konfigureras ihost.jsi [ filen](functions-host-json.md#queues). Om du vill minimera parallell körning för köade funktioner i en Function-app kan du ange batchstorleken till 1. Med den här inställningen elimineras samtidighet endast så länge din funktions program körs på en enda virtuell dator (VM). 

Queue-utlösaren förhindrar automatiskt en funktion från att bearbeta ett köobjekt flera gånger samtidigt.

## <a name="hostjson-properties"></a>host.jspå egenskaper

[host.js](functions-host-json.md#queues) filen innehåller inställningar som styr beteendet för kön utlösare. Mer information om tillgängliga inställningar finns i avsnittet [host.jsi inställningar](functions-bindings-storage-queue-output.md#hostjson-settings) .

## <a name="next-steps"></a>Nästa steg

- [Skriva kö lagrings meddelanden (utgående bindning)](./functions-bindings-storage-blob-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
