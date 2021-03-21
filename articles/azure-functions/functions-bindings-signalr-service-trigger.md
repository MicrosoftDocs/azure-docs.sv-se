---
title: Bindning för utlösare för Azure Functions SignalR-tjänst
description: Lär dig att skicka signalerande tjänst meddelanden från Azure Functions.
author: chenyl
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: 2482a26987ec142880acc51bf470d844655b6e3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97763529"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>Signalerar tjänst utlöser bindning för Azure Functions

Använd *signalen* utlösare för att svara på meddelanden som skickas från Azure SignalR-tjänsten. När funktionen utlöses, parsas meddelanden som skickas till funktionen som ett JSON-objekt.

I signalerar tjänsten Server lös läge använder signal tjänsten den [överordnade](../azure-signalr/concept-upstream.md) funktionen för att skicka meddelanden från klienten till Funktionsapp. Och Funktionsapp använder SignalR tjänstens Utlös ande bindning för att hantera dessa meddelanden. Den allmänna arkitekturen visas nedan: :::image type="content" source="media/functions-bindings-signalr-service/signalr-trigger.png" alt-text="signal utlösare arkitektur":::

Information om konfiguration och konfigurations information finns i [översikten](functions-bindings-signalr-service.md).

## <a name="example"></a>Exempel

I följande exempel visas en funktion som tar emot ett meddelande med hjälp av Utlösar-bindningen och loggar meddelandet.

# <a name="c"></a>[C#](#tab/csharp)

Signalerar tjänst utlösare bindning för C# har två programmerings modeller. Klass baserad modell och traditionell modell. Klassbaserade modeller kan ge en konsekvent programmerings miljö på Server sidan. Och traditionell modell ger mer flexibilitet och liknar andra funktions bindningar.

### <a name="with-class-based-model"></a>Med klass baserad modell

Mer information finns i [klassbaserade modeller](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model) .

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("SignalRTest")]
    public async Task SendMessage([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
    }
}
```

### <a name="with-traditional-model"></a>Med traditionell modell

Traditionell modell följer konventionen för Azure Function som utvecklats av C#. Om du inte är bekant med det kan du lära dig av [dokument](./functions-dotnet-class-library.md).

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>Använd attributet `[SignalRParameter]` för att förenkla `ParameterNames`

Eftersom det är lite besvärligt att `ParameterNames` använda `SignalRParameter` ges det att uppnå samma syfte.

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage")]InvocationContext invocationContext, [SignalRParameter]string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Här är bindnings data i *function.jspå* filen:

Exempel function.jspå:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Här är C#-skript koden:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using System;
using Microsoft.Azure.WebJobs.Extensions.SignalRService;
using Microsoft.Extensions.Logging;

public static void Run(InvocationContext invocation, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Här är bindnings data i *function.jspå* filen:

Exempel function.jspå:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Här är JavaScript-koden:

```javascript
module.exports = function (context, invocation) {
    context.log(`Receive ${context.bindingData.message} from ${invocation.ConnectionId}.`)
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Här är bindnings data i *function.jspå* filen:

Exempel function.jspå:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Här är python-koden:

```python
import logging
import json
import azure.functions as func

def main(invocation) -> None:
    invocation_json = json.loads(invocation)
    logging.info("Receive {0} from {1}".format(invocation_json['Arguments'][0], invocation_json['ConnectionId']))
```

---

## <a name="configuration"></a>Konfiguration

### <a name="signalrtrigger"></a>SignalRTrigger

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `SignalRTrigger` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp**| saknas | Måste anges till `SignalRTrigger` .|
|**position**| saknas | Måste anges till `in` .|
|**Namn**| saknas | Variabel namn som används i funktions kod för kontext objekt för Utlös ande anrop. |
|**hubName**|**HubName**| Värdet måste anges till namnet på Signals Hub för den funktion som ska utlösas.|
|**kategori**|**Kategori**| Värdet måste anges som kategori för meddelanden för funktionen som ska utlösas. Kategorin kan vara något av följande värden: <ul><li>**anslutningar**: inklusive *anslutna* och *frånkopplade* händelser</li><li>**meddelanden**: inklusive alla andra händelser utom de i kategorin *anslutningar*</li></ul> |
|**händelse**|**Händelse**| Värdet måste anges som händelse för meddelanden för att funktionen ska kunna utlösas. I kategorin *meddelanden* är händelsen *målet* i [anrops meddelandet](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) som klienter skickar. För kategorin *anslutningar* används endast *anslutna* och *frånkopplade* . |
|**parameterNames**|**ParameterNames**| Valfritt En lista med namn som binder till parametrarna. |
|**connectionStringSetting**|**ConnectionStringSetting**| Namnet på den app-inställning som innehåller signal tjänstens anslutnings sträng (Standardvärdet är "AzureSignalRConnectionString") |

## <a name="payload"></a>Innehållet

Indatatypen trigger deklareras som antingen `InvocationContext` eller som en anpassad typ. Om du väljer `InvocationContext` att få fullständig åtkomst till innehållet i begäran. För en anpassad typ försöker körningen parsa JSON-begärantext för att ange objekt egenskaperna.

### <a name="invocationcontext"></a>InvocationContext

InvocationContext innehåller allt innehåll i meddelandet skicka från SignalR-tjänsten.

|Egenskap i InvocationContext | Beskrivning|
|------------------------------|------------|
|Argument| Tillgängligt för kategorin *meddelanden* . Innehåller *argument* i [anrops meddelande](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)|
|Fel| Tillgängligt för *frånkopplad* händelse. Det kan vara tomt om anslutningen stängs utan fel eller om den innehåller fel meddelanden.|
|Hubb| Det Hubbs namn som meddelandet tillhör.|
|Kategori| Meddelandets kategori.|
|Händelse| Händelse i meddelandet.|
|ConnectionId| Anslutnings-ID för klienten som skickar meddelandet.|
|UserId| Användar identiteten för klienten som skickar meddelandet.|
|Sidhuvuden| Rubrikerna för begäran.|
|Fråga| Frågan om begäran när klienterna ansluter till tjänsten.|
|Anspråk| Klientens anspråk.|

## <a name="using-parameternames"></a>Använda `ParameterNames`

Med egenskapen `ParameterNames` i `SignalRTrigger` kan du binda argument för anrops meddelanden till funktions parametrar. Det namn som du definierar kan användas som en del av [bindnings uttryck](../azure-functions/functions-bindings-expressions-patterns.md) i annan bindning eller som parametrar i koden. Det ger dig ett bekvämare sätt att komma åt argument för `InvocationContext` .

Anta att du har en JavaScript-signal klient som försöker anropa metoden `broadcast` i Azure-funktionen med två argument `message1` `message2` .

```javascript
await connection.invoke("broadcast", message1, message2);
```

När du har angett `parameterNames` motsvarar det namn du definierade de argument som skickas på klient sidan. 

```cs
[SignalRTrigger(parameterNames: new string[] {"arg1, arg2"})]
```

Sedan innehåller innehållet i `arg1` `message1` och `arg2` kommer att innehålla innehållet i `message2` .


### <a name="remarks"></a>Kommentarer

För parameter bindningen är ordnings saken. Om du använder `ParameterNames` matchar ordningen i `ParameterNames` ordningen på de argument som du anropar i klienten. Om du använder attribut `[SignalRParameter]` i C# matchar ordningen på argumenten i Azure Function-ordningen i-klienterna.

`ParameterNames` det `[SignalRParameter]` **går inte** att använda attributet samtidigt, eller så får du ett undantag.

## <a name="signalr-service-integration"></a>Signalerar tjänst integrering

Signalerar tjänsten behöver en URL för att få åtkomst till Funktionsapp när du använder SignalR tjänstens Utlös ande bindning. URL: en ska konfigureras i **överordnade inställningar** på signal tjänst sidan. 

:::image type="content" source="../azure-signalr/media/concept-upstream/upstream-portal.png" alt-text="Överordnad Portal":::

När du använder SignalR tjänst utlösaren kan URL: en vara enkel och formaterad enligt nedan:

```http
<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>
```

Du hittar den `Function_App_URL` på Funktionsapp översikts sida och `API_KEY` genereras av Azure function. Du kan hämta `API_KEY` från `signalr_extension` i bladet **app-nycklar** i Funktionsapp.
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="API-nyckel":::

Om du vill använda mer än en Funktionsapp tillsammans med en SignalR-tjänst kan överströms även stödja komplexa regler för routning. Mer information finns i [Inställningar för överordnade](../azure-signalr/concept-upstream.md).

## <a name="step-by-step-sample"></a>Steg för steg-exempel

Du kan följa exemplet i GitHub för att distribuera ett chattrum på Funktionsapp med utlösare för utlösare av Signalerare och uppströms funktion: [dubbelriktat chattrum-exempel](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)

## <a name="next-steps"></a>Nästa steg

* [Azure Functions-utveckling och -konfiguration med Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)
* [Exempel på Utlös ande bindning för SignalR tjänst](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)
