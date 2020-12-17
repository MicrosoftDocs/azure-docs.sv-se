---
title: RabbitMQ-utlösare för Azure Functions
description: Lär dig hur du kör en Azure-funktion när ett RabbitMQ-meddelande skapas.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/16/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 1db27db97cdc1746b3392bd386ee6539980cd6d6
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630742"
---
# <a name="rabbitmq-trigger-for-azure-functions-overview"></a>RabbitMQ-utlösare för Azure Functions översikt

> [!NOTE]
> RabbitMQ-bindningarna stöds bara fullt ut i **Windows Premium och dedikerade** planer. Användning och Linux stöds inte för närvarande.

Använd RabbitMQ-utlösaren för att svara på meddelanden från en RabbitMQ-kö.

Information om konfiguration och konfigurations information finns i [översikten](functions-bindings-rabbitmq.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som läser och loggar rabbitmq-meddelandet som en [rabbitmq-händelse](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html):

```cs
[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnection")] BasicDeliverEventArgs args,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(args.Body)}");
}
```

I följande exempel visas hur du läser meddelandet som en POCO.

```cs
public class TestClass
{
    public string x { get; set; }
}

[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnection")] TestClass pocObj,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(pocObj)}");
}
```

Precis som med JSON-objekt inträffar ett fel om meddelandet inte är korrekt formaterat som ett C#-objekt. Om det är det, binds det sedan till variabeln pocObj, som kan användas för vad det behövs för.

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

I följande exempel visas en RabbitMQ-utlösare bindning i en *function.jspå* fil och en [C#-skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen läser och loggar RabbitMQ-meddelandet.

Här är bindnings data i *function.jspå* filen:

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ]
}
```

Här är C#-skript koden:

```csx
using System;

public static void Run(string myQueueItem, ILogger log)
{
    log.LogInformation($"C# Script RabbitMQ trigger function processed: {myQueueItem}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en RabbitMQ-utlösare bindning i en *function.jsi* filen och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen läser och loggar ett RabbitMQ-meddelande.

Här är bindnings data i *function.jspå* filen:

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ]
}
```

Här är skript koden för Java Script:

```javascript
module.exports = async function (context, myQueueItem) {
    context.log('JavaScript RabbitMQ trigger function processed work item', myQueueItem);
};
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas hur du läser ett RabbitMQ-köat via en utlösare.

En RabbitMQ-bindning definieras i *function.jspå* WHERE- *typ* har angetts till `RabbitMQTrigger` .

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ]
}
```

```python
import logging
import azure.functions as func

def main(myQueueItem) -> None:
    logging.info('Python RabbitMQ trigger function processed a queue item: %s', myQueueItem)
```

# <a name="java"></a>[Java](#tab/java)

Följande Java-funktion använder `@RabbitMQTrigger` anteckningen från [Java rabbitmq-typerna](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) för att beskriva konfigurationen för en rabbitmq Queue-utlösare. Funktionen tar bort meddelandet som placerats i kön och lägger till det i loggarna.

```java
@FunctionName("RabbitMQTriggerExample")
public void run(
    @RabbitMQTrigger(connectionStringSetting = "rabbitMQConnection", queueName = "queue") String input,
    final ExecutionContext context)
{
    context.getLogger().info("Java HTTP trigger processed a request." + input);
}
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

Använd attributet [RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/Trigger/RabbitMQTriggerAttribute.cs) i [C#-klass bibliotek](functions-dotnet-class-library.md).

Här är ett `RabbitMQTrigger` attribut i en metodsignatur:

```csharp
[FunctionName("RabbitMQTest")]
public static void RabbitMQTest([RabbitMQTrigger("queue")] string message, ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i C#-exempel.

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="java"></a>[Java](#tab/java)

Med `RabbitMQTrigger` anteckningen kan du skapa en funktion som körs när ett rabbitmq-meddelande skapas. Tillgängliga konfigurations alternativ inkluderar könamn och namn på anslutnings sträng. Ytterligare parameter information finns i [RabbitMQTrigger Java-anteckningar](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQTrigger.java).

Se utlösnings [exemplet](#example) för mer information.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `RabbitMQTrigger` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp** | saknas | Måste vara inställd på "RabbitMQTrigger".|
|**position** | saknas | Måste vara inställt på "in".|
|**Namn** | saknas | Namnet på variabeln som representerar kön i funktions koden. |
|**queueName**|**QueueName**| Namnet på kön att ta emot meddelanden från. |
|**Värdnamn**|**Värdnamn**|(ignoreras om du använder ConnectStringSetting) <br>Värdnamn för kön (t. ex. 10.26.45.210)|
|**userNameSetting**|**UserNameSetting**|(ignoreras om du använder ConnectionStringSetting) <br>Namnet på den app-inställning som innehåller användar namnet som ska användas för att komma åt kön. Till exempel UserNameSetting: "% < UserNameFromSettings >%"|
|**passwordSetting**|**PasswordSetting**|(ignoreras om du använder ConnectionStringSetting) <br>Namnet på den app-inställning som innehåller lösen ordet för att komma åt kön. Till exempel PasswordSetting: "% < PasswordFromSettings >%"|
|**connectionStringSetting**|**ConnectionStringSetting**|Namnet på den app-inställning som innehåller anslutnings strängen för RabbitMQ meddelande kön. Observera att om du anger anslutnings strängen direkt och inte via en app-inställning i local.settings.jspå, kommer utlösaren inte att fungera. (T. ex.: i *function.jspå*: connectionStringSetting: "rabbitMQConnection" <br> I *local.settings.jspå*: "rabbitMQConnection": "< ActualConnectionstring >")|
|**lastning**|**Port**|(ignoreras om du använder ConnectionStringSetting) Hämtar eller anger den port som används. Standardvärdet är 0.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

Standard meddelande typen är [rabbitmq-händelse](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html)och `Body` egenskapen för rabbitmq-händelsen kan läsas som de typer som anges nedan:

* `An object serializable as JSON` – Meddelandet levereras som en giltig JSON-sträng.
* `string`
* `byte[]`
* `POCO` – Meddelandet är formaterat som ett C#-objekt. Ett fullständigt exempel finns i C#- [exempel](#example).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Standard meddelande typen är [rabbitmq-händelse](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html)och `Body` egenskapen för rabbitmq-händelsen kan läsas som de typer som anges nedan:

* `An object serializable as JSON` – Meddelandet levereras som en giltig JSON-sträng.
* `string`
* `byte[]`
* `POCO` – Meddelandet är formaterat som ett C#-objekt. Ett fullständigt exempel finns i C#-skript [exempel](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Queue-meddelandet är tillgängligt via context. bindings.<NAME> var <NAME> matchar det namn som definierats i function.jspå. Om nytto lasten är JSON deserialiseras värdet i ett objekt.

# <a name="python"></a>[Python](#tab/python)

Se python- [exemplet](#example).

# <a name="java"></a>[Java](#tab/java)

Se Java- [attribut och anteckningar](#attributes-and-annotations).

---

## <a name="dead-letter-queues"></a>Köer för obeställbara meddelanden
Köer för obeställbara meddelanden och byten kan inte styras eller konfigureras från RabbitMQ-utlösaren.  För att kunna använda köer för obeställbara meddelanden ska du förkonfigurera kön som används av utlösaren i RabbitMQ. Läs [rabbitmq-dokumentationen](https://www.rabbitmq.com/dlx.html).

## <a name="hostjson-settings"></a>host.jspå Inställningar

I det här avsnittet beskrivs de globala konfigurations inställningarna som är tillgängliga för den här bindningen i version 2. x och högre. Exemplet *host.jspå* filen nedan innehåller bara inställningarna för den här bindningen. Mer information om globala konfigurations inställningar finns i [host.jsreferens för Azure Functions version](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            "prefetchCount": 100,
            "queueName": "queue",
            "connectionString": "amqp://user:password@url:port",
            "port": 10
        }
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------|
|prefetchCount|30|Hämtar eller anger antalet meddelanden som meddelande mottagaren kan begära och cachelagras samtidigt.|
|queueName|saknas| Namnet på kön att ta emot meddelanden från. |
|Begär|saknas|Namnet på den app-inställning som innehåller anslutnings strängen för RabbitMQ meddelande kön. Observera att om du anger anslutnings strängen direkt och inte via en app-inställning i local.settings.jspå, kommer utlösaren inte att fungera.|
|port|0|(ignoreras om connectionString används) Maximalt antal sessioner som kan hanteras samtidigt per skalad instans.|

## <a name="local-testing"></a>Lokal testning

> [!NOTE]
> ConnectionString har företräde framför "värdnamn", "userName" och "Password". Om alla är inställda åsidosätter connectionString de andra två.

Om du testar lokalt utan någon anslutnings sträng ska du ange inställningen "hostName" och "userName" och "Password" om det är tillämpligt i avsnittet "rabbitMQ" i *host.jspå*:

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            ...
            "hostName": "localhost",
            "username": "userNameSetting",
            "password": "passwordSetting"
        }
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------|
|Värdnamn|saknas|(ignoreras om du använder ConnectStringSetting) <br>Värdnamn för kön (t. ex. 10.26.45.210)|
|userName|saknas|(ignoreras om du använder ConnectionStringSetting) <br>Namn för att komma åt kön |
|password|saknas|(ignoreras om du använder ConnectionStringSetting) <br>Lösen ord för att komma åt kön|

## <a name="monitoring-rabbitmq-endpoint"></a>Övervakar RabbitMQ-slutpunkt
Så här övervakar du köer och utbyten för en viss RabbitMQ-slutpunkt:

* Aktivera [rabbitmq Management-plugin-programmet](https://www.rabbitmq.com/management.html)
* Bläddra till http://{Node-hostname}: 15672 och logga in med ditt användar namn och lösen ord.

## <a name="next-steps"></a>Nästa steg

- [Skicka RabbitMQ-meddelanden från Azure Functions (utgående bindning)](./functions-bindings-rabbitmq-output.md)
