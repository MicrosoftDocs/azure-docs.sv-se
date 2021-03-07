---
title: Övervakare i Durable Functions (python) – Azure
description: Lär dig hur du implementerar en status övervakare med Durable Functions-tillägget för Azure Functions (python).
author: davidmrdavid
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: azfuncdf
ms.openlocfilehash: 62b3c9bb1c6fd53d9f11227a9d7e774d56859d04
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434771"
---
# <a name="monitor-scenario-in-durable-functions---github-issue-monitoring-sample"></a>Övervaka scenario i Durable Functions GitHub ärende övervaknings exempel

Övervaknings mönstret avser en flexibel återkommande process i ett arbets flöde – till exempel avsökningen tills vissa villkor är uppfyllda. I den här artikeln beskrivs ett exempel som använder Durable Functions för att implementera övervakning.

## <a name="prerequisites"></a>Förutsättningar

* [Slutför snabb starts artikeln](quickstart-python-vscode.md)
* [Klona eller hämta exempel projektet från GitHub](https://github.com/Azure/azure-functions-durable-python/tree/main/samples/)


## <a name="scenario-overview"></a>Översikt över scenario

Det här exemplet övervakar antalet problem i en GitHub-lagrings platsen och varnar användaren om det finns fler än tre öppna problem. Du kan använda en vanlig timer-utlöst funktion för att begära antalet öppnade ärenden med jämna mellanrum. Ett problem med den här metoden är dock **livs längds hantering**. Om endast en avisering ska skickas måste övervakaren inaktivera sig själv efter 3 eller fler problem har identifierats. Övervaknings mönstret kan avsluta sin egen körning, bland andra fördelar:

* Övervakare körs i intervall, inte scheman: en timer-utlösare *körs* varje timme. en övervakare *väntar* en timme mellan åtgärder. En övervaknings åtgärd överlappar inte om inget anges, vilket kan vara viktigt för långvariga uppgifter.
* Övervakare kan ha dynamiska intervall: vänte tiden kan ändras baserat på ett villkor.
* Övervakare kan avslutas när ett villkor är uppfyllt eller avslutas av en annan process.
* Övervakare kan ta parametrar. Exemplet visar hur samma lagrings platsen kan användas för alla begärda offentliga GitHub-lagrings platsen och telefonnummer.
* Övervakare är skalbara. Eftersom varje övervakare är en Dirigerings instans kan flera övervakare skapas utan att behöva skapa nya funktioner eller definiera mer kod.
* Övervakar lätt att integrera i större arbets flöden. En övervakare kan vara en del av en mer komplex Orchestration-funktion eller en [under dirigering](durable-functions-sub-orchestrations.md).

## <a name="configuration"></a>Konfiguration

### <a name="configuring-twilio-integration"></a>Konfigurera Twilio-integrering

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Funktionerna

I den här artikeln beskrivs följande funktioner i exempel appen:

* `E3_Monitor`: En [Orchestrator-funktion](durable-functions-bindings.md#orchestration-trigger) som anropar `E3_TooManyOpenIssues` regelbundet. Den anropas `E3_SendAlert` om returvärdet för `E3_TooManyOpenIssues` är `True` .
* `E3_TooManyOpenIssues`: En [aktivitets funktion](durable-functions-bindings.md#activity-trigger) som kontrollerar om det finns för många öppna problem i en lagrings plats. För demonstrations syfte anser vi att fler än tre öppna problem är för många.
* `E3_SendAlert`: En aktivitets funktion som skickar ett SMS-meddelande via Twilio.

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor Orchestrator-funktion


Funktionen **E3_Monitor** använder standard *function.jspå* för Orchestrator-funktioner.

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/function.json)]

Här är den kod som implementerar funktionen:

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/\_\_init\_\_.py)]


Den här Orchestrator-funktionen utför följande åtgärder:

1. Hämtar *lagrings platsen* för att övervaka och *telefonnumret* som det ska skicka ett SMS-meddelande till.
2. Anger övervakningens förfallo tid. Exemplet använder ett hårdkodat värde för det kortfattat.
3. Anropar **E3_TooManyOpenIssues** för att avgöra om det finns för många öppna problem på den begärda lagrings platsen.
4. Om det finns för många problem anropar **E3_SendAlert** för att skicka ett SMS-meddelande till det begärda telefonnumret.
5. Skapar en varaktig timer för att återuppta dirigeringen vid nästa avsöknings intervall. Exemplet använder ett hårdkodat värde för det kortfattat.
6. Fortsätter att köras tills den aktuella UTC-tiden passerar övervakarens förfallo tid, eller så skickas en SMS-avisering.

Flera Orchestrator-instanser kan köras samtidigt genom att anropa Orchestrator-funktionen flera gånger. Lagrings platsen som ska övervakas och telefonnumret för att skicka en SMS-avisering till kan anges. Tänk på att Orchestrator-funktionen *inte* körs medan du väntar på timern, så att du inte debiteras för den.


### <a name="e3_toomanyopenissues-activity-function"></a>Funktionen E3_TooManyOpenIssues aktivitet

Som med andra exempel är hjälp aktivitetens funktioner vanliga funktioner som använder sig av `activityTrigger` trigger-bindningen. Funktionen **E3_TooManyOpenIssues** hämtar en lista över problem som är öppna för tillfället på lagrings platsen och avgör om det finns för många av dem: mer än 3 som per exempel.


*function.jspå* definieras enligt följande:

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

Här är implementeringen.

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/\_\_init\_\_.py)]


### <a name="e3_sendalert-activity-function"></a>Funktionen E3_SendAlert aktivitet

Funktionen **E3_SendAlert** använder bindningen Twilio för att skicka ett SMS-meddelande som meddelar slutanvändaren att det finns minst tre öppna problem som avvaktar en lösning.


Dess *function.jspå* är enkelt:

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

Här är koden som skickar SMS-meddelandet:

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_SendAlert/\_\_init\_\_.py)]


## <a name="run-the-sample"></a>Kör exemplet

Du behöver ett [GitHub](https://github.com/) -konto. Med den skapar du en tillfällig offentlig lagrings plats som du kan öppna problem med.

Genom att använda HTTP-utlösta funktioner som ingår i exemplet kan du starta dirigeringen genom att skicka följande HTTP POST-begäran:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "repo": "<your github handle>/<a new github repo under your user>", "phone": "+1425XXXXXXX" }
```

Om ditt GitHub-användarnamn till exempel är `foo` och din lagrings plats, `bar` ska ditt värde `"repo"` vara `"foo/bar"` .

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

**E3_Monitor** -instansen startar och frågar den angivna lagrings platsen efter öppna problem. Om det finns minst tre öppna problem anropas en aktivitets funktion för att skicka en avisering. annars anges en timer. När timern upphör att gälla återupptas dirigeringen.

Du kan se dirigeringens aktivitet genom att titta på funktions loggarna i Azure Functions portalen.

```
[2020-12-04T18:24:30.007Z] Executing 'Functions.HttpStart' (Reason='This function was programmatically 
called via the host APIs.', Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6)
[2020-12-04T18:24:30.769Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=058e656e-bcb1-418c-95b3-49afcd07bd08)
[2020-12-04T18:24:30.847Z] Started orchestration with ID = '788420bb31754c50acbbc46e12ef4f9c'.
[2020-12-04T18:24:30.932Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=058e656e-bcb1-418c-95b3-49afcd07bd08, Duration=174ms)
[2020-12-04T18:24:30.955Z] Executed 'Functions.HttpStart' (Succeeded, Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6, Duration=1028ms)
[2020-12-04T18:24:31.229Z] Executing 'Functions.E3_TooManyOpenIssues' (Reason='(null)', Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3)
[2020-12-04T18:24:31.772Z] Executed 'Functions.E3_TooManyOpenIssues' (Succeeded, Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3, Duration=555ms)
[2020-12-04T18:24:40.754Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=23915e4c-ddbf-46f9-b3f0-53289ed66082)
[2020-12-04T18:24:40.789Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=23915e4c-ddbf-46f9-b3f0-53289ed66082, Duration=38ms)
(...trimmed...)
```

Dirigeringen slutförs när tids gränsen har nåtts eller mer än tre öppna problem har identifierats. Du kan också använda `terminate` API i en annan funktion eller anropa **TERMINATEPOSTURI** http post webhook som refereras i 202-svaret ovan. Om du vill använda webhooken ersätter du `{text}` med orsaken till den tidigaste avslutningen. HTTP POST-URL: en kommer att se ut ungefär så här:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Nästa steg

Det här exemplet har visat hur du använder Durable Functions för att övervaka en extern källas status med hjälp av [varaktiga timers](durable-functions-timers.md) och villkorlig logik. Nästa exempel visar hur du använder externa händelser och [varaktiga timers](durable-functions-timers.md) för att hantera mänsklig interaktion.

> [!div class="nextstepaction"]
> [Kör exempel på mänsklig interaktion](durable-functions-phone-verification.md)
