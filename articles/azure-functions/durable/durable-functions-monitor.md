---
title: Övervakare i Durable Functions – Azure
description: Lär dig hur du implementerar en status övervakare med Durable Functions-tillägget för Azure Functions.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: e70c50098ece516312e1e92984185624c276301b
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028428"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Övervaka scenario i Durable Functions – väder vakts exempel

Övervaknings mönstret avser en flexibel *återkommande* process i ett arbets flöde – till exempel avsökningen tills vissa villkor är uppfyllda. I den här artikeln beskrivs ett exempel som använder [Durable Functions](durable-functions-overview.md) för att implementera övervakning.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Översikt över scenario

Det här exemplet övervakar en platss aktuella väder förhållanden och varnar en användare av SMS när Skies är klar. Du kan använda en vanlig timer-utlöst funktion för att kontrol lera väder och skicka aviseringar. Ett problem med den här metoden är dock **livs längds hantering**. Om endast en avisering ska skickas måste övervakaren stänga av sig själv när tydliga väder har upptäckts. Övervaknings mönstret kan avsluta sin egen körning, bland andra fördelar:

* Övervakare körs i intervall, inte scheman: en timer-utlösare *körs* varje timme. en övervakare *väntar* en timme mellan åtgärder. En övervaknings åtgärd överlappar inte om inget anges, vilket kan vara viktigt för långvariga uppgifter.
* Övervakare kan ha dynamiska intervall: vänte tiden kan ändras baserat på ett villkor.
* Övervakare kan avslutas när ett villkor är uppfyllt eller avslutas av en annan process.
* Övervakare kan ta parametrar. Exemplet visar hur samma väder övervaknings process kan tillämpas på alla begärda platser och telefonnummer.
* Övervakare är skalbara. Eftersom varje övervakare är en Dirigerings instans kan flera övervakare skapas utan att behöva skapa nya funktioner eller definiera mer kod.
* Övervakar lätt att integrera i större arbets flöden. En övervakare kan vara en del av en mer komplex Orchestration-funktion eller en [under dirigering](durable-functions-sub-orchestrations.md).

## <a name="configuration"></a>Konfiguration

### <a name="configuring-twilio-integration"></a>Konfigurera Twilio-integrering

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

### <a name="configuring-weather-underground-integration"></a>Konfigurera väder underjords integrering

Det här exemplet förutsätter att du använder väder-API: et för att kontrol lera aktuella väder förhållanden för en plats.

Det första du behöver är ett väderlek underjords konto. Du kan skapa ett kostnads fritt på [https://www.wunderground.com/signup](https://www.wunderground.com/signup) . När du har ett konto måste du skaffa en API-nyckel. Du kan göra det genom [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1) att besöka och sedan välja nyckel inställningar. Stratus Developer plan är gratis och tillräckligt för att köra det här exemplet.

När du har en API-nyckel lägger du till följande **app-inställning** i din Function-app.

| Namn på App-inställning | Värde Beskrivning |
| - | - |
| **WeatherUndergroundApiKey**  | Din API-nyckel för väder under jord. |

## <a name="the-functions"></a>Funktionerna

I den här artikeln beskrivs följande funktioner i exempel appen:

* `E3_Monitor`: En [Orchestrator-funktion](durable-functions-bindings.md#orchestration-trigger) som anropar `E3_GetIsClear` regelbundet. Den anropas `E3_SendGoodWeatherAlert` om `E3_GetIsClear` returnerar true.
* `E3_GetIsClear`: En [aktivitets funktion](durable-functions-bindings.md#activity-trigger) som kontrollerar aktuella väder förhållanden för en plats.
* `E3_SendGoodWeatherAlert`: En aktivitets funktion som skickar ett SMS-meddelande via Twilio.

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor Orchestrator-funktion

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=41-78,97-115)]

Orchestrator kräver en plats för att övervaka och ett telefonnummer för att skicka ett meddelande till när det blir klart på platsen. Dessa data skickas till Orchestrator som ett starkt skrivet `MonitorRequest` objekt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Funktionen **E3_Monitor** använder standard *function.jspå* för Orchestrator-funktioner.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/function.json)]

Här är den kod som implementerar funktionen:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

# <a name="python"></a>[Python](#tab/python)
Vi har en annan självstudie för övervaknings mönstret på python, se den [här](durable-functions-monitor-python.md).

---

Den här Orchestrator-funktionen utför följande åtgärder:

1. Hämtar den **MonitorRequest** som består av *platsen* som ska övervakas och *telefonnumret* som den ska skicka ett SMS-meddelande till.
2. Anger övervakningens förfallo tid. Exemplet använder ett hårdkodat värde för det kortfattat.
3. Anropar **E3_GetIsClear** för att avgöra om det finns tydliga Skies på den begärda platsen.
4. Om väderet är klart anropar **E3_SendGoodWeatherAlert** att skicka ett SMS-meddelande till det begärda telefonnumret.
5. Skapar en varaktig timer för att återuppta dirigeringen vid nästa avsöknings intervall. Exemplet använder ett hårdkodat värde för det kortfattat.
6. Fortsätter att köras tills den aktuella UTC-tiden passerar övervakarens förfallo tid, eller så skickas en SMS-avisering.

Flera Orchestrator-instanser kan köras samtidigt genom att anropa Orchestrator-funktionen flera gånger. Den plats som ska övervakas och telefonnumret som en SMS-avisering ska skickas till kan anges. Tänk på att Orchestrator-funktionen *inte* körs medan du väntar på timern, så att du inte debiteras för den.
### <a name="e3_getisclear-activity-function"></a>Funktionen E3_GetIsClear aktivitet

Som med andra exempel är hjälp aktivitetens funktioner vanliga funktioner som använder sig av `activityTrigger` trigger-bindningen. Funktionen **E3_GetIsClear** hämtar aktuella väder förhållanden med hjälp av väder-API: et och avgör om himmelen är klar.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=80-85)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.jspå* definieras enligt följande:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/function.json)]

Här är implementeringen.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

# <a name="python"></a>[Python](#tab/python)
Vi har en annan självstudie för övervaknings mönstret på python, se den [här](durable-functions-monitor-python.md).

---

### <a name="e3_sendgoodweatheralert-activity-function"></a>Funktionen E3_SendGoodWeatherAlert aktivitet

Funktionen **E3_SendGoodWeatherAlert** använder bindningen Twilio för att skicka ett SMS-meddelande som meddelar slutanvändaren att det är en lämplig tid för en guidad användning.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=87-96,140-205)]

> [!NOTE]
> Du måste installera `Microsoft.Azure.WebJobs.Extensions.Twilio` NuGet-paketet för att köra exempel koden.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dess *function.jspå* är enkelt:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/function.json)]

Här är koden som skickar SMS-meddelandet:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

# <a name="python"></a>[Python](#tab/python)
Vi har en annan självstudie för övervaknings mönstret på python, se den [här](durable-functions-monitor-python.md).

---

## <a name="run-the-sample"></a>Kör exemplet

Genom att använda HTTP-utlösta funktioner som ingår i exemplet kan du starta dirigeringen genom att skicka följande HTTP POST-begäran:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

**E3_Monitor** -instansen startar och frågar efter aktuella väder förhållanden för den begärda platsen. Om väderet är klart anropas en aktivitets funktion för att skicka en avisering. annars anges en timer. När timern upphör att gälla återupptas dirigeringen.

Du kan se dirigeringens aktivitet genom att titta på funktions loggarna i Azure Functions portalen.

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

Dirigeringen slutförs när tids gränsen har nåtts eller också är Clear Skies identifierade. Du kan också använda `terminate` API i en annan funktion eller anropa **TERMINATEPOSTURI** http post webhook som refereras i 202-svaret ovan. Om du vill använda webhooken ersätter du `{text}` med orsaken till den tidigaste avslutningen. HTTP POST-URL: en kommer att se ut ungefär så här:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Nästa steg

Det här exemplet har visat hur du använder Durable Functions för att övervaka en extern källas status med hjälp av [varaktiga timers](durable-functions-timers.md) och villkorlig logik. Nästa exempel visar hur du använder externa händelser och [varaktiga timers](durable-functions-timers.md) för att hantera mänsklig interaktion.

> [!div class="nextstepaction"]
> [Kör exempel på mänsklig interaktion](durable-functions-phone-verification.md)
