---
title: Diagnostik i Durable Functions – Azure
description: Lär dig hur du diagnostiserar problem med Durable Functions-tillägget för Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: azfuncdf
ms.openlocfilehash: 62cc5e1762a2a54b26cbebae5aa7cfbf64204ba5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100584617"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostik i Durable Functions i Azure

Det finns flera alternativ för att diagnosticera problem med [Durable Functions](durable-functions-overview.md). Vissa av dessa alternativ är desamma för vanliga funktioner medan andra är unika för Durable Functions.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) är det rekommenderade sättet att utföra diagnostik och övervakning i Azure Functions. Samma sak gäller för Durable Functions. En översikt över hur du utnyttjar Application Insights i din Function-app finns i [övervaka Azure Functions](../functions-monitoring.md).

Med Azure Functions tåligt tillägg kan du också spåra *händelser* som gör att du kan spåra slut punkt till slut punkt för ett dirigering. Dessa spårnings händelser kan hittas och frågas med hjälp av [Application Insights Analytics](../../azure-monitor/logs/log-query-overview.md) -verktyget i Azure Portal.

### <a name="tracking-data"></a>Spårnings data

Varje livs cykel händelse i en Orchestration-instans gör att en spårnings händelse skrivs till **trace** -samlingen i Application Insights. Den här händelsen innehåller en **customDimensions** nytto last med flera fält.  Fält namn är alla anpassningsprefix med `prop__` .

* **hubName**: namnet på den aktivitets hubb som dirigeringarna körs i.
* **APPNAME**: namnet på Function-appen. Det här fältet är användbart när du har flera Function-appar som delar samma Application Insights-instans.
* **slotName**: [distributions platsen](../functions-deployment-slots.md) där den aktuella Function-appen körs. Det här fältet är användbart när du använder distributions platser för att välja version för ditt dirigering.
* **functionname**: namnet på Orchestrator-eller Activity-funktionen.
* **functionType**: typen av funktion, till exempel **Orchestrator** eller **Activity**.
* **InstanceID**: det unika ID: t för Orchestration-instansen.
* **State**: instansens livs cykel körnings tillstånd. Giltiga värden är:
  * **Schemalagd**: funktionen har schemalagts för körning men har ännu inte startats.
  * **Startade**: funktionen har börjat köras men har ännu inte förväntats eller slutförts.
  * **Förväntades**: Orchestrator har schemalagt ett visst arbete och väntar på att den ska slutföras.
  * **Lyssning**: Orchestrator lyssnar efter en extern händelse avisering.
  * **Slutfört**: funktionen har slutförts.
  * **Misslyckades**: funktionen misslyckades med ett fel.
* **orsak**: ytterligare data som är associerade med spårnings händelsen. Om exempelvis en instans väntar på en extern händelse meddelande, anger det här fältet namnet på händelsen som väntar på. Om en funktion har misslyckats kommer det här fältet innehålla fel informationen.
* **isReplay**: booleskt värde som anger om spårnings händelsen är för en upprepad körning.
* **extensionVersion**: versionen av det varaktiga aktivitets tillägget. Versions informationen är särskilt viktig när du rapporterar möjliga buggar i tillägget. Tids krävande instanser kan rapportera flera versioner om en uppdatering inträffar när den körs.
* **sequenceNumber**: körnings ordnings nummer för en händelse. Tillsammans med tidsstämpeln kan du beställa händelserna efter körnings tid. *Observera att det här antalet återställs till noll om värden startas om medan instansen körs, så det är viktigt att alltid sortera efter tidsstämpel först, sedan sequenceNumber.*

Utförligheten för spårnings data som skickas till Application Insights kan konfigureras i `logger` avsnittet (Functions 1. x) eller `logging` (functions 2,0) i `host.json` filen.

#### <a name="functions-10"></a>Functions 1,0

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-20"></a>Functions 2,0

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Som standard genereras alla icke-omuppspelnings spårnings händelser. Mängden data kan minskas genom att ställa in `Host.Triggers.DurableTask` till `"Warning"` eller `"Error"` i så fall kommer spårning av händelser bara att genereras för exceptionella situationer.

Om du vill aktivera sändning av utförliga Dirigerings händelser `LogReplayEvents` kan du ställa in på `true` i `host.json` filen under `durableTask` som visas:

#### <a name="functions-10"></a>Functions 1,0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Functions 2,0

```json
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Som standard samplas Application Insights telemetri av Azure Functions runtime för att undvika sändning av data för ofta. Detta kan göra att spårnings informationen går förlorad när många livs cykel händelser inträffar under en kort tids period. I [artikeln Azure Functions övervakning](../configure-monitoring.md#configure-sampling) förklaras hur du konfigurerar det här beteendet.

### <a name="single-instance-query"></a>Fråga för enskild instans

I följande fråga visas historiska spårnings data för en enda instans av funktionen Orchestration i [Hello-sekvensen](durable-functions-sequence.md) . Det skrivs med hjälp av [Kusto-frågespråket](/azure/data-explorer/kusto/query/). Den filtrerar fram uppspelnings körningen så att bara den *logiska* körnings Sök vägen visas. Händelser kan sorteras genom att sortera efter `timestamp` och `sequenceNumber` som visas i frågan nedan:

```kusto
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

Resultatet är en lista över spårnings händelser som visar körnings vägen för dirigeringen, inklusive alla aktivitets funktioner ordnade efter körnings tiden i stigande ordning.

![Beställd fråga för Application Insights enskild instans](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Instans sammanfattnings fråga

Följande fråga visar status för alla Dirigerings instanser som kördes inom ett angivet tidsintervall.

```kusto
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

Resultatet är en lista över instans-ID: n och deras aktuella körnings status.

![Application Insights enskild instans fråga](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="durable-task-framework-logging"></a>Beständiga aktivitets Ramverks loggning

De varaktiga tilläggs loggarna är användbara för att förstå hur din organisations logik fungerar. Dessa loggar innehåller dock inte alltid tillräckligt med information för att felsöka problem med prestanda och tillförlitlighet på Ramverks nivå. Från och med **v-2.3.0** av det varaktiga tillägget är de loggar som genereras av det underliggande DTFx-ramverket också tillgängliga för insamling.

När du tittar på loggar som skickats av DTFx är det viktigt att förstå att DTFx-motorn består av två komponenter: kärn sändnings motorn ( `DurableTask.Core` ) och en av många leverantörer som stöds (Durable Functions använder `DurableTask.AzureStorage` som standard).

* **DurableTask. Core**: innehåller information om Orchestration-körning och schemaläggning på låg nivå.
* **DurableTask. AzureStorage**: innehåller information som rör interaktioner med Azure Storage artefakter, inklusive interna köer, blobbar och lagrings tabeller som används för att lagra och hämta interna Orchestration-tillstånd.

Du kan aktivera dessa loggar genom att uppdatera `logging/logLevel` avsnittet i appens funktions program **host.jspå** fil. I följande exempel visas hur du aktiverar varnings-och fel loggar från både `DurableTask.Core` och `DurableTask.AzureStorage` :

```json
{
  "version": "2.0",
  "logging": {
    "logLevel": {
      "DurableTask.AzureStorage": "Warning",
      "DurableTask.Core": "Warning"
    }
  }
}
```

Om du Application Insights aktive rad läggs loggarna automatiskt till i `trace` samlingen. Du kan söka på dem på samma sätt som du söker efter andra `trace` loggar med Kusto-frågor.

> [!NOTE]
> För produktions program rekommenderar vi att du aktiverar `DurableTask.Core` och `DurableTask.AzureStorage` loggar med hjälp av `"Warning"` filtret. Högre utförliga filter, som `"Information"` är mycket användbara för fel sökning av prestanda problem. Dessa logg händelser är dock höga och ökar avsevärt Application Insights kostnader för data lagring.

Följande Kusto-fråga visar hur du frågar efter DTFx-loggar. Den viktigaste delen av frågan är sedan den `where customerDimensions.Category startswith "DurableTask"` som filtrerar resultaten till loggar i `DurableTask.Core` `DurableTask.AzureStorage` kategorierna och.

```kusto
traces
| where customDimensions.Category startswith "DurableTask"
| project
    timestamp,
    severityLevel,
    Category = customDimensions.Category,
    EventId = customDimensions.EventId,
    message,
    customDimensions
| order by timestamp asc 
```
Resultatet är en uppsättning loggar som skrivits av de ständiga aktivitets strukturernas logg leverantörer.

![Application Insights DTFx frågeresultat](./media/durable-functions-diagnostics/app-insights-dtfx.png)

Mer information om vilka logg händelser som är tillgängliga finns i den [varaktiga aktivitets ramverket som är strukturerade loggnings dokumentation på GitHub](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Core/Logging#durabletaskcore-logging).

## <a name="app-logging"></a>Loggning av app

Det är viktigt att du behåller Orchestrator-omuppspelnings beteendet i åtanke när du skriver loggar direkt från en Orchestrator-funktion. Överväg till exempel följande Orchestrator-funktion:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

# <a name="python"></a>[Python](#tab/python)
```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    logging.info("Calling F1.")
    yield context.call_activity("F1")
    logging.info("Calling F2.")
    yield context.call_activity("F2")
    logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

De resulterande loggdata kommer att se ut ungefär som i följande exempel på utdata:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Kom ihåg att även om loggarna som ska anropa F1, F2 och F3, så kallas dessa funktioner *bara första* gången de påträffas. Efterföljande anrop som sker under uppspelningen hoppas över och utdata spelas upp i Orchestrator-logiken.

Om du bara vill skriva loggar vid icke-omuppspelnings körningar kan du skriva ett villkors uttryck för att endast logga om flaggan "spelar upp" är `false` . Överväg exemplet ovan, men den här gången med repetitions kontroller.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

Från och med Durable Functions 2,0 har .NET Orchestrator-funktioner även möjlighet att skapa en `ILogger` som automatiskt filtrerar ut logg instruktioner under uppspelningen. Denna automatiska filtrering görs med hjälp av [ILogger-API: et (IDurableOrchestrationContext. CreateReplaySafeLogger)](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durablecontextextensions.createreplaysafelogger) .

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log = context.CreateReplaySafeLogger(log);
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

> [!NOTE]
> Föregående C#-exempel är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    if not context.is_replaying:
        logging.info("Calling F1.")
    yield context.call_activity("F1")
    if not context.is_replaying:
        logging.info("Calling F2.")
    yield context.call_activity("F2")
    if not context.is_replaying:
        logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

Med de tidigare nämnda ändringarna ser loggen ut så här:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Anpassad status

Med anpassad Orchestration-status kan du ange ett anpassat status värde för din Orchestrator-funktion. Den här anpassade statusen visas sedan för externa klienter via [http-status frågans API](durable-functions-http-api.md#get-instance-status) eller via SPRÅKspecifika API-anrop. Den anpassade Orchestration-statusen möjliggör bättre övervakning av Orchestrator-funktioner. Till exempel kan Orchestrator-funktions koden anropa API: et "ange anpassad status" för att uppdatera förloppet för en tids krävande åtgärd. En klient, till exempel en webb sida eller ett annat externt system, kan sedan regelbundet fråga HTTP-status frågans API: er för mer information om förloppet. Exempel kod för att ange ett anpassat status värde i en Orchestrator-funktion anges nedan:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SetStatusTest")]
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

> [!NOTE]
> Föregående C#-exempel är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    # ...do work...

    # update the status of the orchestration with some arbitrary data
    custom_status = {'completionPercentage': 90.0, 'status': 'Updating database records'}
    context.set_custom_status(custom_status)
    # ...do more work...

    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

Medan dirigeringen körs kan externa klienter hämta den anpassade statusen:

```http
GET /runtime/webhooks/durabletask/instances/instance123?code=XYZ

```

Klienterna får följande svar:

```json
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> Den anpassade statusen för nytto lasten är begränsad till 16 KB UTF-16 JSON-text eftersom den måste kunna få plats i en Azure Table Storage-kolumn. Du kan använda extern lagring om du behöver större nytto Last.

## <a name="debugging"></a>Felsökning

Azure Functions stöder fel söknings funktions kod direkt och samma stöd vidarebefordrar till Durable Functions, oavsett om de körs i Azure eller lokalt. Det finns dock några beteenden som kan vara medvetna om vid fel sökning:

* **Spela upp**: Orchestrator-funktioner [upprepas](durable-functions-orchestrations.md#reliability) regelbundet när nya indata tas emot. Det här beteendet innebär att en enskild *logisk* körning av en Orchestrator-funktion kan resultera i att samma Bryt punkt används flera gånger, särskilt om den är inställd tidigt i funktions koden.
* **Väntar**: när ett `await` påträffas i en Orchestrator-funktion, ger den kontrollen tillbaka till Dispatchern för den varaktiga aktivitets ramverket. Om det är första gången ett visst `await` har påträffats, återupptas *inte* den associerade uppgiften. Eftersom aktiviteten aldrig återupptas går det inte att stega *över* await (F10 i Visual Studio). Att stega över fungerar bara när en uppgift spelas upp.
* **Timeout för meddelande meddelande**: Durable Functions internt använder Kömeddelanden för att köra funktioner för Orchestrator, aktivitet och entitet. I en miljö med flera virtuella datorer kan en annan virtuell dator Hämta meddelandet, vilket resulterar i dubbla körningar om du bryter mot fel sökningen under längre tids perioder. Det här beteendet finns även för vanliga funktioner för kö-utlösare, men det är viktigt att du pekar på den här kontexten eftersom köerna är en implementerings detalj.
* **Stoppa och starta**: meddelanden i varaktiga funktioner kvarstår mellan fel söknings sessioner. Om du slutar felsöka och avslutar den lokala värd processen medan en varaktig funktion körs, kan den funktionen köras igen automatiskt i en framtida felsökningssession. Det här beteendet kan vara förvirrande när det inte förväntas. Att rensa alla meddelanden från [interna lagrings köer](durable-functions-perf-and-scale.md#internal-queue-triggers) mellan fel söknings sessioner är en teknik för att undvika det här beteendet.

> [!TIP]
> Om du ställer in Bryt punkter i Orchestrator-funktioner, och om du bara vill bryta vid icke-omuppspelningar, kan du ange en villkorlig Bryt punkt som bara bryts om värdet "' spelas upp ' är `false` .

## <a name="storage"></a>Storage

Durable Functions lagrar som standard tillstånd i Azure Storage. Detta innebär att du kan kontrol lera statusen för dina dirigeringar med hjälp av verktyg som [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

![Azure Storage Explorer skärm bild](./media/durable-functions-diagnostics/storage-explorer.png)

Detta är användbart för fel sökning eftersom du ser exakt vilket tillstånd ett dirigering kan vara i. Meddelanden i köerna kan också undersökas för att ta reda på vilket arbete som väntar (eller fastnat i vissa fall).

> [!WARNING]
> Även om det är praktiskt att se körnings historiken i Table Storage, Undvik att ta något beroende på den här tabellen. Den kan ändras när Durable Functions tillägget utvecklas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om övervakning i Azure Functions](../functions-monitoring.md)
