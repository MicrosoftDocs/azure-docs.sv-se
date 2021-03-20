---
title: RabbitMQ utgående bindningar för Azure Functions
description: Lär dig att skicka RabbitMQ-meddelanden från Azure Functions.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 1664656f82492e664b7574339893cd688f0a061d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097321"
---
# <a name="rabbitmq-output-binding-for-azure-functions-overview"></a>RabbitMQ utgående bindning för Azure Functions översikt

> [!NOTE]
> RabbitMQ-bindningarna stöds bara fullt ut av **Premium och dedikerade** planer. Förbrukning stöds inte.

Använd RabbitMQ utgående bindning för att skicka meddelanden till en RabbitMQ-kö.

Information om konfiguration och konfigurations information finns i [översikten](functions-bindings-rabbitmq-output.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som skickar ett rabbitmq-meddelande när det utlöses av en TimerTrigger var 5: e minut med metoden Return-värdet som utdata:

```cs
[FunctionName("RabbitMQOutput")]
[return: RabbitMQ(QueueName = "outputQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

I följande exempel visas hur du använder IAsyncCollector-gränssnittet för att skicka meddelanden.

```cs
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] string rabbitMQEvent,
[RabbitMQ(QueueName = "destinationQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]IAsyncCollector<string> outputEvents,
ILogger log)
{
     // send the message
    await outputEvents.AddAsync(JsonConvert.SerializeObject(rabbitMQEvent));
}
```

I följande exempel visas hur du skickar meddelanden som POCOs.

```cs
namespace Company.Function
{
    public class TestClass
    {
        public string x { get; set; }
    }
    public static class RabbitMQOutput{
        [FunctionName("RabbitMQOutput")]
        public static async Task Run(
        [RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] TestClass rabbitMQEvent,
        [RabbitMQ(QueueName = "destinationQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]IAsyncCollector<TestClass> outputPocObj,
        ILogger log)
        {
            // send the message
            await outputPocObj.AddAsync(rabbitMQEvent);
        }
    }
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

I följande exempel visas en RabbitMQ utgående bindning i en *function.jspå* filen och en [C#-skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen läser i meddelandet från en HTTP-utlösare och matar den till RabbitMQ-kön.

Här är bindnings data i *function.jspå* filen:

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

Här är C#-skript koden:

```C#
using System;
using Microsoft.Extensions.Logging;

public static void Run(string input, out string outputMessage, ILogger log)
{
    log.LogInformation(input);
    outputMessage = input;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en RabbitMQ utgående bindning i en *function.jsi* filen och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen läser i meddelandet från en HTTP-utlösare och matar den till RabbitMQ-kön.

Här är bindnings data i *function.jspå* filen:

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

Här är JavaScript-koden:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en RabbitMQ utgående bindning i en *function.jsi* filen och en python-funktion som använder bindningen. Funktionen läser i meddelandet från en HTTP-utlösare och matar den till RabbitMQ-kön.

Här är bindnings data i *function.jspå* filen:

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
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

I *_\_ init_ \_ . py*:

```python
import azure.functions as func

def main(req: func.HttpRequest, outputMessage: func.Out[str]) -> func.HttpResponse:
    input_msg = req.params.get('message')
    outputMessage.set(input_msg)
    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

Följande Java-funktion använder `@RabbitMQOutput` anteckningen från [Java rabbitmq-typerna](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) för att beskriva konfigurationen för en dataRabbitMQ i kön. Funktionen skickar ett meddelande till RabbitMQ-kön när den utlöses av en TimerTrigger var 5: e minut.

```java
@FunctionName("RabbitMQOutputExample")
public void run(
@TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
@RabbitMQOutput(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "hello") OutputBinding<String> output,
final ExecutionContext context) {
    output.setValue("Some string");
}
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

Använd [RabbitMQAttribute](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/RabbitMQAttribute.cs)i [C#-klass bibliotek](functions-dotnet-class-library.md).

Här är ett `RabbitMQAttribute` attribut i en metodsignatur:

```csharp
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("SourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("DestinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i C#- [exempel](#example).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="java"></a>[Java](#tab/java)

Med `RabbitMQOutput` anteckningen kan du skapa en funktion som körs när du skickar ett rabbitmq-meddelande. Tillgängliga konfigurations alternativ inkluderar könamn och namn på anslutnings sträng. Ytterligare parameter information finns i [RabbitMQOutput Java-anteckningar](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQOutput.java).

Se [exempel](#example) på utdata-bindning för mer information.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `RabbitMQ` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp** | saknas | Måste vara inställd på "RabbitMQ".|
|**position** | saknas | Måste anges till "out". |
|**Namn** | saknas | Namnet på variabeln som representerar kön i funktions koden. |
|**queueName**|**QueueName**| Namnet på kön som meddelanden ska skickas till. |
|**Värdnamn**|**Värdnamn**|(ignoreras om du använder ConnectStringSetting) <br>Värdnamn för kön (t. ex. 10.26.45.210)|
|**Användar**|**Användar**|(ignoreras om du använder ConnectionStringSetting) <br>Namnet på den app-inställning som innehåller användar namnet som ska användas för att komma åt kön. t.ex. UserNameSetting: "< UserNameFromSettings >"|
|**lösenord**|**Lösenord**|(ignoreras om du använder ConnectionStringSetting) <br>Namnet på den app-inställning som innehåller lösen ordet för att komma åt kön. t.ex. UserNameSetting: "< UserNameFromSettings >"|
|**connectionStringSetting**|**ConnectionStringSetting**|Namnet på den app-inställning som innehåller anslutnings strängen för RabbitMQ meddelande kön. Observera att om du anger anslutnings strängen direkt och inte via en app-inställning i local.settings.jspå, kommer utlösaren inte att fungera. (T. ex.: i *function.jspå*: connectionStringSetting: "rabbitMQConnection" <br> I *local.settings.jspå*: "rabbitMQConnection": "< ActualConnectionstring >")|
|**lastning**|**Port**|(ignoreras om du använder ConnectionStringSetting) Hämtar eller anger den port som används. Standardvärdet är 0 som pekar på rabbitmq klients standard port inställning: 5672.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

Använd följande parameter typer för utgående bindning:

* `byte[]` -Om parametervärdet är null när funktionen avslutas, skapas inget meddelande i functions.
* `string` -Om parametervärdet är null när funktionen avslutas, skapas inget meddelande i functions.
* `POCO` – Om parametervärdet inte är formaterat som ett C#-objekt, kommer ett fel att tas emot. Ett fullständigt exempel finns i C#- [exempel](#example).

När du arbetar med C#-funktioner:

* Async Functions behöver ett retur värde eller `IAsyncCollector` i stället för en `out` parameter.

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Använd följande parameter typer för utgående bindning:

* `byte[]` -Om parametervärdet är null när funktionen avslutas, skapas inget meddelande i functions.
* `string` -Om parametervärdet är null när funktionen avslutas, skapas inget meddelande i functions.
* `POCO` – Om parametervärdet inte är formaterat som ett C#-objekt, kommer ett fel att tas emot. Ett fullständigt exempel finns i C#-skript [exempel](#example).

När du arbetar med C#-skript funktioner:

* Async Functions behöver ett retur värde eller `IAsyncCollector` i stället för en `out` parameter.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Queue-meddelandet är tillgängligt via context. bindings.<NAME> var <NAME> matchar det namn som definierats i function.jspå. Om nytto lasten är JSON deserialiseras värdet i ett objekt.

# <a name="python"></a>[Python](#tab/python)

Se python- [exemplet](#example).

# <a name="java"></a>[Java](#tab/java)

Använd följande parameter typer för utgående bindning:

* `byte[]` -Om parametervärdet är null när funktionen avslutas, skapas inget meddelande i functions.
* `string` -Om parametervärdet är null när funktionen avslutas, skapas inget meddelande i functions.
* `POJO` – Om parametervärdet inte är formaterat som ett Java-objekt tas ett fel emot.

---

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion när ett RabbitMQ-meddelande skapas (utlösare)](./functions-bindings-rabbitmq-trigger.md)
