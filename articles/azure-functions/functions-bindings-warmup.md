---
title: Azure Functions uppvärmnings-utlösare
description: Lär dig hur du använder uppvärmnings-utlösaren i Azure Functions.
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions, functions, Event Processing, uppvärmnings, kall start, Premium, dynamisk beräkning, Server lös arkitektur
ms.service: azure-functions
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/08/2019
ms.author: cshoe
ms.openlocfilehash: ea418576ab8fe06964a61e48f16393e1a0566ce8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182255"
---
# <a name="azure-functions-warm-up-trigger"></a>Azure Functions värme utlösare

Den här artikeln förklarar hur du arbetar med uppvärmnings-utlösaren i Azure Functions. En uppvärmnings-utlösare anropas när en instans läggs till för att skala en app som körs i funktion. Du kan använda en uppvärmnings-utlösare för att i förväg läsa in anpassade beroenden under för [uppvärmnings processen](./functions-premium-plan.md#pre-warmed-instances) , så att funktionerna kan börja bearbeta begär Anden direkt. 

> [!NOTE]
> Uppvärmnings-utlösaren stöds inte för Function-appar som körs i en förbruknings plan.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Paket-funktioner 2. x och högre

[Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet-paketet, version **3.0.5 eller högre** krävs. Käll koden för paketet finns i [Azure-WebJobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/main/src/WebJobs.Extensions/Extensions/Warmup) lagringsplatsen. 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Utlösare

Med uppvärmnings-utlösaren kan du definiera en funktion som ska köras på en ny instans när den läggs till i appen som körs. Du kan använda en uppvärmnings-funktion för att öppna anslutningar, läsa in beroenden eller köra någon annan anpassad logik innan appen tar emot trafik. 

Uppvärmnings-utlösaren är avsedd att skapa delade beroenden som kommer att användas av andra funktioner i din app. [Se exempel på delade beroenden här](./manage-connections.md#client-code-examples).

Observera att uppvärmnings-utlösaren endast anropas under skalnings åtgärder, inte under omstarter eller andra icke-skalnings starter. Du måste se till att din logik kan läsa in alla nödvändiga beroenden utan att använda uppvärmnings-utlösaren. Enkel inläsning är ett utmärkt mönster för att uppnå detta.

## <a name="trigger---example"></a>Utlös – exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som körs på varje ny instans när den läggs till i din app. Ett attribut för retur värde är inte obligatoriskt.


* Funktionen måste ha namnet (inte Skift läges ```warmup``` känsligt) och det får bara finnas en uppvärmnings-funktion per app.
* Om du vill använda uppvärmnings som en .NET-klass biblioteks funktion kontrollerar du att du har en paket referens till **Microsoft. Azure. WebJobs. extensions >= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


Plats hållare kommentarer visar var i programmet att deklarera och initiera delade beroenden. 
[Lär dig mer om delade beroenden här](./manage-connections.md#client-code-examples).

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-script"></a>[C#-skript](#tab/csharp-script)


I följande exempel visas en uppvärmnings-utlösare i en *function.jspå* filen och en [C#-skript funktion](functions-reference-csharp.md) som ska köras på varje ny instans när den läggs till i din app.

Funktionen måste ha namnet (inte Skift läges ```warmup``` känsligt) och det kan bara finnas en uppvärmnings-funktion per app.

Här är *function.jspå* filen:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

I [konfigurations](#trigger---configuration) avsnittet förklaras dessa egenskaper.

```cs
public static void Run(WarmupContext warmupContext, ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en uppvärmnings-utlösare i en *function.jsi* filen och en [JavaScript-funktion](functions-reference-node.md)  som körs på varje ny instans när den läggs till i din app.

Funktionen måste ha namnet (inte Skift läges ```warmup``` känsligt) och det får bara finnas en uppvärmnings-funktion per app.

Här är *function.jspå* filen:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

I [konfigurations](#trigger---configuration) avsnittet förklaras dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
};
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en uppvärmnings-utlösare i en *function.jspå* fil och en [python-funktion](functions-reference-python.md) som ska köras på varje ny instans när den läggs till i din app.

Funktionen måste ha namnet (inte Skift läges ```warmup``` känsligt) och det får bara finnas en uppvärmnings-funktion per app.

Här är *function.jspå* filen:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

I [konfigurations](#trigger---configuration) avsnittet förklaras dessa egenskaper.

Här är python-koden:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas en uppvärmnings-utlösare som körs när varje ny instans läggs till i din app.

Funktionen måste ha namnet (inte Skift läges `warmup` känsligt) och det får bara finnas en uppvärmnings-funktion per app.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Utlös ande attribut

Attributet är tillgängligt i [C#-klass bibliotek](functions-dotnet-class-library.md) `WarmupTrigger` för att konfigurera funktionen.

# <a name="c"></a>[C#](#tab/csharp)

Det här exemplet visar hur du använder attributet [uppvärmnings](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) .

Observera att funktionen måste anropas ```Warmup``` och att det bara får finnas en uppvärmnings-funktion per app.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Ett fullständigt exempel finns i [utlösaren exempel](#trigger---example).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="java"></a>[Java](#tab/java)

Uppvärmnings-utlösaren stöds inte i Java som ett attribut.

---

## <a name="trigger---configuration"></a>Utlös konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `WarmupTrigger` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
| **bastyp** | saknas| Required-måste anges till `warmupTrigger` . |
| **position** | saknas| Required-måste anges till `in` . |
| **Namn** | saknas| Obligatoriskt – variabel namnet som används i funktions koden.|

## <a name="trigger---usage"></a>Utlös användning

Ingen ytterligare information ges till en uppvärmnings-utlöst funktion när den anropas.

## <a name="trigger---limits"></a>Utlösare – gränser

* Uppvärmnings-utlösaren är endast tillgänglig för appar som körs i [Premium-planen](./functions-premium-plan.md).
* Uppvärmnings-utlösaren anropas bara vid skalnings åtgärder, inte under omstarter eller andra icke-skalnings starter. Du måste se till att din logik kan läsa in alla nödvändiga beroenden utan att använda uppvärmnings-utlösaren. Enkel inläsning är ett utmärkt mönster för att uppnå detta.
* Uppvärmnings-utlösaren kan inte anropas när en instans redan körs.
* Det får bara finnas en funktion för uppvärmnings-utlösare per Function-app.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)
