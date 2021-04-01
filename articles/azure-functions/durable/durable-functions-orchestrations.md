---
title: Varaktiga Orchestrationer – Azure Functions
description: Introduktion till Orchestration-funktionen för Azure Durable Functions.
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: ba314963058389e171601407ff00411049eecd45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97845430"
---
# <a name="durable-orchestrations"></a>Varaktiga dirigeringar

Durable Functions är en utökning av [Azure Functions](../functions-overview.md). Du kan använda en *Orchestrator-funktion* för att dirigera körningen av andra varaktiga funktioner i en Function-app. Orchestrator-funktioner har följande egenskaper:

* Orchestrator Functions definierar funktions arbets flöden med procedur koden. Inga deklarativ scheman eller designers behövs.
* Orchestrator-funktioner kan anropa andra varaktiga funktioner synkront och asynkront. Utdata från anropade funktioner kan sparas på ett tillförlitligt sätt till lokala variabler.
* Orchestrator-funktioner är tåliga och tillförlitliga. Körnings förloppet markeras automatiskt när funktionen "väntar" eller "ger". Det lokala tillståndet förloras aldrig när processen återanvänds eller den virtuella datorn startas om.
* Orchestrator-funktioner kan vara tids krävande. Den totala livs längd för en *Dirigerings instans* kan vara sekunder, dagar, månader eller aldrig slut.

Den här artikeln ger en översikt över Orchestrator-funktionerna och hur de kan hjälpa dig att lösa olika utmaningar med att utveckla appar. Om du inte redan är bekant med de typer av funktioner som är tillgängliga i en Durable Functions app kan du läsa artikeln [varaktiga funktions typer](durable-functions-types-features-overview.md) först.

## <a name="orchestration-identity"></a>Orchestration-identitet

Varje *instans* av en dirigering har en instans identifierare (kallas även ett *instans-ID*). Som standard är varje instans-ID ett automatiskt genererat GUID. Instans-ID: n kan dock också vara valfritt sträng värde som skapats av användare. Varje Dirigerings instans-ID måste vara unikt inom en [aktivitets nav](durable-functions-task-hubs.md).

Följande är några regler om instans-ID: n:

* Instans-ID: n måste vara mellan 1 och 256 tecken.
* Instans-ID: n får inte börja med `@` .
* Instans-ID: n får inte innehålla `/` `\` tecknen,, `#` eller `?` .
* Instans-ID: n får inte innehålla kontroll tecken.

> [!NOTE]
> Vi rekommenderar vanligt vis att använda automatiskt genererade instans-ID när det är möjligt. Användardefinierade instans-ID: n är avsedda för scenarier där det finns en en-till-en-mappning mellan en Dirigerings instans och en extern programspecifik entitet, t. ex. en inköps order eller ett dokument.

En Dirigerings instans-ID är en obligatorisk parameter för de flesta [instans hanterings åtgärder](durable-functions-instance-management.md). De är också viktiga för diagnostik, som att [söka igenom Orchestration-spårnings data](durable-functions-diagnostics.md#application-insights) i Application Insights för fel sökning eller analys. Därför rekommenderar vi att du sparar genererade instans-ID: n till en extern plats (till exempel en databas eller i program loggar) där de lätt kan refereras senare.

## <a name="reliability"></a>Tillförlitlighet

Orchestrator-funktioner upprätthåller sin körnings status på ett tillförlitligt sätt med hjälp av design mönstret för [händelse källor](/azure/architecture/patterns/event-sourcing) . I stället för att direkt lagra det aktuella läget för en dirigering använder det varaktiga aktivitets ramverket en skrivskyddad lagrings plats för att registrera en fullständig serie åtgärder som funktionen dirigerar. En skrivskyddad lagrings plats har många fördelar jämfört med "dumpning", fullständig körnings status. Fördelarna är ökad prestanda, skalbarhet och svars tider. Du får också eventuell konsekvens för transaktions data och fullständig gransknings historik och historik. Gransknings historiken har stöd för pålitliga kompenserande åtgärder.

Durable Functions använder händelse källa transparent. I bakgrunden, `await` ger operatorn (C#) eller `yield` (Java Script/python) i en Orchestrator-funktion kontrollen av Orchestrator-tråden tillbaka till den varaktiga aktivitets Ramverks Dispatchern. Dispatchern genomför sedan alla nya åtgärder som Orchestrator-funktionen schemalägger (till exempel anropa en eller flera underordnade funktioner eller schemalägga en varaktig timer) till lagringen. Åtgärden för att utföra transparent tillägg i körnings historiken för Orchestration-instansen. Historiken lagras i en lagrings tabell. Inchecknings åtgärden lägger sedan till meddelanden i en kö för att schemalägga det faktiska arbetet. I det här läget kan Orchestrator-funktionen tas bort från minnet.

När en Orchestration-funktion får mer arbete (till exempel om ett svarsmeddelande tas emot eller om en varaktig timer upphör att gälla), aktiverar Orchestrator och kör om hela funktionen från början för att återskapa det lokala läget. Om koden försöker anropa en funktion (eller något annat asynkront arbete) under uppspelningen kan du se körnings historiken för den aktuella dirigeringen. Om den finner att [aktivitets funktionen](durable-functions-types-features-overview.md#activity-functions) redan har körts och ger ett resultat spelas den upp i resultatet och Orchestrator-koden fortsätter att köras. Repetitionen fortsätter tills funktions koden är avslutad eller tills den har schemalagt nytt asynkront arbete.

> [!NOTE]
> För att uppspelnings mönstret ska fungera korrekt och tillförlitligt måste Orchestrator-funktions koden vara *deterministisk*. Mer information om kod begränsningar för Orchestrator Functions finns i artikeln om [begränsningar för Orchestrator-funktions kod](durable-functions-code-constraints.md) .

> [!NOTE]
> Om en Orchestrator-funktion genererar logg meddelanden kan uppspelnings beteendet orsaka att dubbla logg meddelanden genereras. I avsnittet om [loggning](durable-functions-diagnostics.md#app-logging) kan du läsa mer om varför det här problemet uppstår och hur du kan kringgå det.

## <a name="orchestration-history"></a>Orchestration-historik

Beteendet för händelse-källa i det ständiga aktivitets ramverket är nära kopplad till den Orchestrator-funktions kod som du skriver. Anta att du har en funktion för aktivitets länkning i Orchestrator, som följande Orchestrator-funktion:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    result1 = yield context.call_activity('SayHello', "Tokyo")
    result2 = yield context.call_activity('SayHello', "Seattle")
    result3 = yield context.call_activity('SayHello', "London")
    return [result1, result2, result3]

main = df.Orchestrator.create(orchestrator_function)
```
---

I varje `await` (C#) eller `yield` (JavaScript/python)-instruktion, checkpointrar det varaktiga aktivitets ramverket körnings status för funktionen i en viss varaktig lagrings Server del (vanligt vis Azure Table Storage). Det här är det tillstånd som kallas för *Orchestration-historiken*.

### <a name="history-table"></a>Historik tabell

I allmänhet är det ständiga aktivitets ramverket följande vid varje kontroll punkt:

1. Sparar körnings historik i Azure Storage tabeller.
2. Köa meddelanden för funktioner som Orchestrator vill anropa.
3. Köa meddelanden för själva Orchestrator till &mdash; exempel meddelanden med varaktig timer.

När kontroll punkten har slutförts är Orchestrator-funktionen kostnads fri att tas bort från minnet tills det finns mer arbete att göra.

> [!NOTE]
> Azure Storage tillhandahåller inte några transaktions garantier mellan att spara data i tabell lagring och köer. För att hantera fel använder Durable Functions lagringsprovider de *slutliga konsekvens* mönstren. Dessa mönster ser till att inga data går förlorade om anslutningen bryts eller bryts i mitten av en kontroll punkt.

Vid slut för ande ser historiken för funktionen som visas tidigare ut ungefär så här: följande tabell i Azure Table Storage (förkortat för illustration):

| PartitionKey (InstanceId)                     | Typ             | Timestamp               | Indata | Name             | Resultat                                                    | Status |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852 Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362 Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201 Z |       |                  | "" "Hej Tokyo!" "                                        |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232 Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763 Z |       |                  | "" "Hej Seattle!" "                                      |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919 Z |       |                  | "" "Hej London!" "                                       |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032 Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044 Z |       |                  |                                                           |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044 Z |       |                  | "[" "Hello Tokyo!", "" Hej Seattle! "," "Hej London!" "]" | Slutförd           |

Några anmärkningar om kolumn värden:

* **PartitionKey**: innehåller ett instans-ID för dirigeringen.
* **EventType**: representerar händelsens typ. Kan vara en av följande typer:
  * **OrchestrationStarted**: Orchestrator-funktionen återupptogs från en await eller körs för första gången. `Timestamp`Kolumnen används för att fylla på det deterministiska värdet för `CurrentUtcDateTime` API: erna (.net), `currentUtcDateTime` (Java Script) och `current_utc_datetime` (python).
  * **ExecutionStarted**: Orchestrator-funktionen startade körning för första gången. Den här händelsen innehåller också funktions ingången i `Input` kolumnen.
  * **TaskScheduled**: en aktivitets funktion har schemalagts. Namnet på aktivitets funktionen samlas in i `Name` kolumnen.
  * **TaskCompleted**: en aktivitets funktion har slutförts. Resultatet av funktionen finns i `Result` kolumnen.
  * **TimerCreated**: en varaktig timer skapades. `FireAt`Kolumnen innehåller den schemalagda UTC-tid då timern upphör att gälla.
  * **TimerFired**: en varaktig timer har utlösts.
  * **Händelse aktive rad**: en extern händelse skickades till Orchestration-instansen. `Name`Kolumnen samlar in namnet på händelsen och `Input` kolumnen fångar in händelsens nytto Last.
  * **OrchestratorCompleted**: Orchestrator-funktionen förväntades.
  * **ContinueAsNew**: Orchestrator-funktionen har slutförts och startats om med nytt tillstånd. `Result`Kolumnen innehåller värdet, som används som indatamängden i den omstartade instansen.
  * **ExecutionCompleted**: Orchestrator-funktionen kördes (eller misslyckades). Utdata från funktionen eller fel informationen lagras i `Result` kolumnen.
* **Timestamp**: UTC-tidsstämpeln för historik händelsen.
* **Namn**: namnet på den funktion som anropades.
* **Inmatade**: den JSON-formaterade indatamängden för funktionen.
* **Resultat**: resultatet av funktionen; det vill säga dess retur värde.

> [!WARNING]
> Även om det är användbart som ett fel söknings verktyg ska du inte göra något beroende av den här tabellen. Den kan ändras när Durable Functions tillägget utvecklas.

Varje gång funktionen återupptas från en `await` (C#) eller `yield` (Java Script/python), kör det ständiga aktivitets ramverket om Orchestrator-funktionen från grunden. Vid varje omkörning kontaktas körnings historiken för att avgöra om den aktuella asynkrona åtgärden har ägt rum.  Om åtgärden utfördes, spelar ramverket om resultatet av åtgärden direkt och fortsätter med nästa `await` (C#) eller `yield` (Java Script/python). Den här processen fortsätter tills hela historiken har spelats upp. När den aktuella historiken har spelats upp, kommer de lokala variablerna att återställas till sina tidigare värden.

## <a name="features-and-patterns"></a>Funktioner och mönster

I nästa avsnitt beskrivs funktionerna och mönstren i Orchestrator functions.

### <a name="sub-orchestrations"></a>Sub-orkestreringar

Orchestrator-funktioner kan anropa aktivitets funktioner, men även andra Orchestrator-funktioner. Du kan till exempel bygga ett större dirigering från ett bibliotek med Orchestrator-funktioner. Du kan också köra flera instanser av en Orchestrator-funktion parallellt.

Mer information och exempel finns i artikeln [underordnad](durable-functions-sub-orchestrations.md) .

### <a name="durable-timers"></a>Varaktiga timers

Orchestration kan schemalägga *varaktiga timers* för att implementera fördröjningar eller konfigurera tids gräns hantering för asynkrona åtgärder. Använd varaktiga timers i Orchestrator-funktioner i stället för `Thread.Sleep` och `Task.Delay` (C#) eller `setTimeout()` och `setInterval()` (Java Script) eller `time.sleep()` (python).

Mer information och exempel finns i artikeln [varaktiga timers](durable-functions-timers.md) .

### <a name="external-events"></a>Externa händelser

Orchestrator-funktioner kan vänta på att externa händelser uppdaterar en Orchestration-instans. Den här Durable Functions funktionen är ofta användbar för att hantera en mänsklig interaktion eller andra externa återanrop.

Mer information och exempel finns i artikeln [externa händelser](durable-functions-external-events.md) .

### <a name="error-handling"></a>Felhantering

Orchestrator-funktioner kan använda fel hanterings funktionerna i programmeringsspråket. Befintliga mönster som `try` / `catch` stöds i Orchestration-kod.

Orchestrator-funktioner kan också lägga till principer för återförsök för de aktiviteter eller under-Orchestrator-funktioner som de anropar. Om en aktivitet eller en underordnad Orchestrator-funktion Miss lyckas med ett undantag, kan den angivna återförsöks principen automatiskt fördröja och försöka köra igen till ett visst antal gånger.

> [!NOTE]
> Om det finns ett ohanterat undantag i en Orchestrator-funktion, kommer Orchestration-instansen att slutföras i ett `Failed` tillstånd. Det går inte att göra ett nytt Dirigerings instans försök när det har misslyckats.

Mer information och exempel finns i artikeln [fel hantering](durable-functions-error-handling.md) .

### <a name="critical-sections-durable-functions-2x-currently-net-only"></a>Kritiska avsnitt (Durable Functions 2. x, endast för närvarande .NET)

Orchestration-instanser är entrådade, så det är inte nödvändigt att bekymra dig om tävlings villkor *i* ett Orchestration. Dock är det möjligt att dirigera villkor när dirigeringar interagerar med externa system. För att minska tävlings förhållandena när du interagerar med externa system kan Orchestrator-funktioner definiera *kritiska avsnitt* med hjälp av en `LockAsync` metod i .net.

Följande exempel kod visar en Orchestrator-funktion som definierar ett kritiskt avsnitt. Den anger det kritiska avsnittet med hjälp av `LockAsync` metoden. Den här metoden kräver att en eller flera referenser till en [varaktig entitet](durable-functions-entities.md)skickas, vilket varaktigt hanterar lås status. Endast en enda instans av den här dirigeringen kan köra koden i det kritiska avsnittet i taget.

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

`LockAsync`Erhåller det varaktiga låset (s) och returnerar ett `IDisposable` som avslutar det kritiska avsnittet när det tas bort. Det här `IDisposable` resultatet kan användas tillsammans med ett `using` block för att få en syntaktisk representation av det kritiska avsnittet. När en Orchestrator-funktion anger ett kritiskt avsnitt, kan endast en instans köra det här blocket. Andra instanser som försöker att ange det kritiska avsnittet kommer att blockeras tills den föregående instansen avslutar det kritiska avsnittet.

Funktionen kritiskt avsnitt är också användbar för samordning av ändringar i varaktiga entiteter. Mer information om viktiga avsnitt finns i avsnittet [beständiga entiteter "entity Coordination"](durable-functions-entities.md#entity-coordination) .

> [!NOTE]
> Kritiska avsnitt är tillgängliga i Durable Functions 2,0 och senare. För närvarande implementerar endast .NET-dirigeringar den här funktionen.

### <a name="calling-http-endpoints-durable-functions-2x"></a>Anropar HTTP-slutpunkter (Durable Functions 2. x)

Orchestrator-funktioner tillåts inte i/O, enligt beskrivningen i [Orchestrator-funktionens kod begränsningar](durable-functions-code-constraints.md). Den typiska lösningen för den här begränsningen är att omsluta all kod som behöver göra I/O i en aktivitets funktion. Dirigering som interagerar med externa system använder ofta aktivitets funktioner för att göra HTTP-anrop och returnera resultatet till dirigeringen.

# <a name="c"></a>[C#](#tab/csharp)

För att förenkla detta vanliga mönster kan Orchestrator-funktioner använda `CallHttpAsync` -metoden för att anropa HTTP-API: er direkt.

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const url = context.df.getInput();
    var res = yield context.df.callHttp("GET", url);
    if (res.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    res = yield context.call_http('GET', url)
    if res.status_code >= 400:
        # handing of error code goes here
```
---

Förutom stöd för grundläggande fråge-/svars mönster stöder metoden automatisk hantering av vanliga asynkrona HTTP 202-avsöknings mönster och stöder även autentisering med externa tjänster med [hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md).

Mer information och detaljerade exempel finns i artikeln om [http-funktioner](durable-functions-http-features.md) .

> [!NOTE]
> Anrop av HTTP-slutpunkter direkt från Orchestrator-funktioner är tillgängligt i Durable Functions 2,0 och senare.

### <a name="passing-multiple-parameters"></a>Skicka flera parametrar

Det går inte att skicka flera parametrar till en aktivitets funktion direkt. Rekommendationen är att skicka i en matris med objekt eller sammansatta objekt.

# <a name="c"></a>[C#](#tab/csharp)

I .NET kan du också använda [ValueTuple](/dotnet/csharp/tuples) -objekt. Följande exempel använder nya funktioner i [ValueTuple](/dotnet/csharp/tuples) som lagts till med [C# 7](/dotnet/csharp/whats-new/csharp-7#tuples):

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] IDurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="orchestrator"></a>Orchestrator

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const location = {
        city: "Seattle",
        state: "WA"
    };
    const weather = yield context.df.callActivity("GetWeather", location);

    // ...
};
```

#### <a name="getweather-activity"></a>`GetWeather` Aktivitet

```javascript
module.exports = async function (context, location) {
    const {city, state} = location; // destructure properties into variables

    // ...
};
```

# <a name="python"></a>[Python](#tab/python)

#### <a name="orchestrator"></a>Orchestrator

```python
from collections import namedtuple
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    Location = namedtuple('Location', ['city', 'state'])
    location = Location(city='Seattle', state= 'WA')

    weather = yield context.call_activity("GetWeather", location)

    # ...

```
#### <a name="getweather-activity"></a>`GetWeather` Aktivitet

```python
from collections import namedtuple

Location = namedtuple('Location', ['city', 'state'])

def main(location: Location) -> str:
    city, state = location
    return f"Hello {city}, {state}!"
```

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Begränsningar för orkestrerarkod](durable-functions-code-constraints.md)
