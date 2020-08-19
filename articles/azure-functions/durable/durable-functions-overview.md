---
title: Översikt över Durable Functions – Azure
description: Introduktion till Durable Functions-tillägget för Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 03/12/2020
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: adf58b667d17393fc905fbf31261530fce88d9f8
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272356"
---
# <a name="what-are-durable-functions"></a>Vad är Durable Functions?

*Durable Functions* är en utökning av [Azure Functions](../functions-overview.md) som låter dig skriva tillstånds känsliga funktioner i en server lös beräknings miljö. Med tillägget kan du definiera tillstånds känsliga arbets flöden genom att skriva [*Orchestrator-funktioner*](durable-functions-orchestrations.md) och tillstånds känsliga entiteter genom att skriva [*enhets funktioner*](durable-functions-entities.md) med hjälp av Azure Functions Programmerings modell. I bakgrunden hanterar tillägget tillstånd, kontroll punkter och omstarter åt dig, så att du kan fokusera på affärs logiken.

## <a name="supported-languages"></a><a name="language-support"></a>Språk som stöds

Durable Functions stöder för närvarande följande språk:

* **C#**: både [förkompilerade klassbibliotek](../functions-dotnet-class-library.md) och [C#-skript](../functions-reference-csharp.md).
* **JavaScript**: stöds endast för version 2.x av Azure Functions-körningen. Kräver version 1.7.0 av Durable Functions-tillägget eller en senare version. 
* **Python**: kräver version 1.8.5 av Durable Functions-tillägget eller en senare version. 
* **F#**: både förkompilerade klassbibliotek och F#-skript. F#-skriptet stöds endast för version 1.x av Azure Functions-körningen.
* **PowerShell**: stöd för Durable Functions finns för närvarande i en offentlig för hands version. Stöds endast för version 3. x av Azure Functions Runtime och PowerShell 7. Kräver version 2.2.2 av Durable Functions-tillägget eller en senare version. Endast följande mönster stöds för närvarande: [funktion länkning](#chaining), [fläkt-ut/fläkt-in](#fan-in-out), [asynkrona http API: er](#async-http).

Durable Functions har som mål att stödja alla [Azure Functions-språk](../supported-languages.md). I [Durable Functions-problemlistan](https://github.com/Azure/azure-functions-durable-extension/issues) finns senaste status för arbetet med att stödja ytterligare språk.

Precis som Azure Functions finns det mallar som hjälper dig att utveckla Durable Functions med [Visual studio 2019](durable-functions-create-first-csharp.md), [Visual studio Code](quickstart-js-vscode.md)och [Azure Portal](durable-functions-create-portal.md).

## <a name="application-patterns"></a>Programmönster

Det huvudsakliga användningsfallet för Durable Functions är att förenkla komplexa, tillståndskänsliga koordinationskrav i serverlösa program. I följande avsnitt beskrivs typiska program mönster som kan dra nytta av Durable Functions:

* [Funktionslänkning](#chaining)
* [Förgrening](#fan-in-out)
* [Asynkrona HTTP-API:er](#async-http)
* [Övervakning](#monitoring)
* [Mänsklig interaktion](#human)
* [Aggregator (tillstånds känsliga enheter)](#aggregator)

### <a name="pattern-1-function-chaining"></a><a name="chaining"></a>Mönster #1: funktions länkning

I funktions kedje mönstret körs en sekvens med funktioner i en speciell ordning. I det här mönstret tillämpas utdata från en funktion på indatan för en annan funktion.

![Ett diagram över funktions kedje mönstret](./media/durable-functions-concepts/function-chaining.png)

Du kan använda Durable Functions för att implementera funktions länknings mönstret på ett koncist sätt som visas i följande exempel.

I det här exemplet är värdena `F1` , `F2` , `F3` och `F4` namnen på andra funktioner i samma Function-app. Du kan implementera kontroll flödet med hjälp av vanliga kodnings konstruktioner. Koden körs uppifrån och ned. Koden kan omfatta befintliga semantiska språk kontroll flöde, t. ex. villkor och slingor. Du kan inkludera fel hanterings logik i `try` / `catch` / `finally` block.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

Du kan använda- `context` parametern för att anropa andra funktioner efter namn, pass parametrar och returnera funktions resultat. Varje gång koden anropar `await` , visar Durable Functions Framework förloppet för den aktuella funktions instansen. Om processen eller den virtuella datorn återvinns mitt i körningen fortsätter funktions instansen från föregående `await` anrop. Mer information finns i nästa avsnitt, mönster #2: fläkt ut/fläkt i.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    try {
        const x = yield context.df.callActivity("F1");
        const y = yield context.df.callActivity("F2", x);
        const z = yield context.df.callActivity("F3", y);
        return    yield context.df.callActivity("F4", z);
    } catch (error) {
        // Error handling or compensation goes here.
    }
});
```

Du kan använda `context.df` objektet för att anropa andra funktioner efter namn, pass parametrar och returnera funktions resultat. Varje gång koden anropar `yield` , visar Durable Functions Framework förloppet för den aktuella funktions instansen. Om processen eller den virtuella datorn återvinns mitt i körningen fortsätter funktions instansen från föregående `yield` anrop. Mer information finns i nästa avsnitt, mönster #2: fläkt ut/fläkt i.

> [!NOTE]
> `context`Objektet i Java Script representerar hela [funktions kontexten](../functions-reference-node.md#context-object). Kom åt Durable Functions kontexten med hjälp av `df` egenskapen i huvud kontexten.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    x = yield context.call_activity("F1", None)
    y = yield context.call_activity("F2", x)
    z = yield context.call_activity("F3", y)
    result = yield context.call_activity("F4", z)
    return result


main = df.Orchestrator.create(orchestrator_function)
```

Du kan använda `context` objektet för att anropa andra funktioner efter namn, pass parametrar och returnera funktions resultat. Varje gång koden anropar `yield` , visar Durable Functions Framework förloppet för den aktuella funktions instansen. Om processen eller den virtuella datorn återvinns mitt i körningen fortsätter funktions instansen från föregående `yield` anrop. Mer information finns i nästa avsnitt, mönster #2: fläkt ut/fläkt i.

> [!NOTE]
> `context`Objektet i python representerar Orchestration-kontexten. Få åtkomst till huvud Azure Functions kontexten med hjälp av `function_context` egenskapen i Orchestration-kontexten.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

$X = Invoke-ActivityFunction -FunctionName 'F1'
$Y = Invoke-ActivityFunction -FunctionName 'F2' -Input $X
$Z = Invoke-ActivityFunction -FunctionName 'F3' -Input $Y
Invoke-ActivityFunction -FunctionName 'F4' -Input $Z
```

Du kan använda `Invoke-ActivityFunction` kommandot för att anropa andra funktioner efter namn, pass parametrar och returnera funktions resultat. Varje gång koden anropas `Invoke-ActivityFunction` utan `NoWait` växeln, visar Durable Functions Framework förloppet för den aktuella funktions instansen. Om processen eller den virtuella datorn återvinns mitt i körningen fortsätter funktions instansen från föregående `Invoke-ActivityFunction` anrop. Mer information finns i nästa avsnitt, mönster #2: fläkt ut/fläkt i.

---

### <a name="pattern-2-fan-outfan-in"></a><a name="fan-in-out"></a>Mönster #2: fläkt ut/fläkt i

I mönstret fläkt ut/fläkt i, kör du flera funktioner parallellt och väntar sedan på att alla funktioner ska slutföras. Ofta utförs en del sammansättnings arbete på resultaten som returneras från funktionerna.

![Ett diagram över fläkt-och fläkt mönstret](./media/durable-functions-concepts/fan-out-fan-in.png)

Med normala funktioner kan du använda funktionen Skicka flera meddelanden till en kö. Fanning tillbaka i är mycket mer utmanande. I en normal funktion skriver du kod för att spåra när funktionen för köade funktioner har slutförts och sedan lagra funktions resultat.

Durable Functions-tillägget hanterar det här mönstret med relativt enkel kod:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

Fläkt arbetet distribueras till flera instanser av `F2` funktionen. Arbetet spåras med hjälp av en dynamisk lista med aktiviteter. `Task.WhenAll` anropas för att vänta tills alla anropade funktioner har slutförts. Sedan `F2` aggregeras funktionen utdata från den dynamiska uppgifts listan och skickas till `F3` funktionen.

Den automatiska kontroll punkten som sker vid `await` anropet `Task.WhenAll` innebär att en eventuell halvvägs krasch eller omstart inte kräver att en redan slutförd aktivitet startas om.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

Fläkt arbetet distribueras till flera instanser av `F2` funktionen. Arbetet spåras med hjälp av en dynamisk lista med aktiviteter. `context.df.Task.all` API anropas för att vänta tills alla anropade funktioner har slutförts. Sedan `F2` aggregeras funktionen utdata från den dynamiska uppgifts listan och skickas till `F3` funktionen.

Den automatiska kontroll punkten som sker vid `yield` anropet `context.df.Task.all` innebär att en eventuell halvvägs krasch eller omstart inte kräver att en redan slutförd aktivitet startas om.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    # Get a list of N work items to process in parallel.
    work_batch = yield context.call_activity("F1", None)

    parallel_tasks = [ context.call_activity("F2", b) for b in work_batch ]
    
    outputs = yield context.task_all(parallel_tasks)

    # Aggregate all N outputs and send the result to F3.
    total = sum(outputs)
    yield context.call_activity("F3", total)


main = df.Orchestrator.create(orchestrator_function)
```

Fläkt arbetet distribueras till flera instanser av `F2` funktionen. Arbetet spåras med hjälp av en dynamisk lista med aktiviteter. `context.task_all` API anropas för att vänta tills alla anropade funktioner har slutförts. Sedan `F2` aggregeras funktionen utdata från den dynamiska uppgifts listan och skickas till `F3` funktionen.

Den automatiska kontroll punkten som sker vid `yield` anropet `context.task_all` innebär att en eventuell halvvägs krasch eller omstart inte kräver att en redan slutförd aktivitet startas om.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

# Get a list of work items to process in parallel.
$WorkBatch = Invoke-ActivityFunction -FunctionName 'F1'

$ParallelTasks =
    foreach ($WorkItem in $WorkBatch) {
        Invoke-ActivityFunction -FunctionName 'F2' -Input $WorkItem -NoWait
    }

$Outputs = Wait-ActivityFunction -Task $ParallelTasks

# Aggregate all outputs and send the result to F3.
$Total = ($Outputs | Measure-Object -Sum).Sum
Invoke-ActivityFunction -FunctionName 'F3' -Input $Total
```

Fläkt arbetet distribueras till flera instanser av `F2` funktionen. Observera användningen av `NoWait` växeln på `F2` funktions anropet: med den här växeln kan Orchestrator fortsätta att anropa `F2` utan att aktiviteten slutförs. Arbetet spåras med hjälp av en dynamisk lista med aktiviteter. `Wait-ActivityFunction`Kommandot anropas för att vänta tills alla anropade funktioner har slutförts. Sedan `F2` aggregeras funktionen utdata från den dynamiska uppgifts listan och skickas till `F3` funktionen.

Den automatiska kontroll punkten som sker vid `Wait-ActivityFunction` anropet garanterar att en eventuell halvvägs krasch eller omstart inte kräver att en redan slutförd aktivitet startas om.

---

> [!NOTE]
> I sällsynta fall är det möjligt att en krasch kan inträffa i fönstret när en aktivitets funktion har slutförts, men innan slutförandet sparas i Orchestration-historiken. Om detta inträffar kommer aktivitets funktionen att köras igen från början efter att processen återställts.

### <a name="pattern-3-async-http-apis"></a><a name="async-http"></a>Mönster #3: asynkrona HTTP API: er

Det asynkrona HTTP API-mönstret löser problemet med att koordinera tillstånden för långvariga åtgärder med externa klienter. Ett vanligt sätt att implementera det här mönstret är att ha en HTTP-slutpunkt som utlöser tids krävande åtgärder. Omdirigera sedan klienten till en status slut punkt som klienten avsöker för att lära sig när åtgärden har slutförts.

![Ett diagram över HTTP API-mönstret](./media/durable-functions-concepts/async-http-api.png)

Durable Functions tillhandahåller **inbyggt stöd** för det här mönstret, vilket fören klar eller till och med att ta bort koden som du behöver skriva för att interagera med tids krävande funktions körningar. Till exempel visar Durable Functions snabb starts exempel ([C#](durable-functions-create-first-csharp.md) och [Java Script](quickstart-js-vscode.md)) ett enkelt rest-kommando som du kan använda för att starta nya Orchestrator Function-instanser. När en instans startar visar tillägget webhook HTTP-API: er som frågar Orchestrator-funktionens status. 

I följande exempel visas REST-kommandon för att starta en Orchestrator och fråga dess status. För tydlighetens skull utelämnas vissa protokoll detaljer från exemplet.

```
> curl -X POST https://myfunc.azurewebsites.net/api/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Eftersom Durable Functions runtime hanterar tillstånd för dig behöver du inte implementera din egen status spårnings funktion.

Durable Functions-tillägget visar inbyggda HTTP-API: er som hanterar långvariga dirigeringar. Du kan också implementera det här mönstret själv genom att använda dina egna funktions utlösare (till exempel HTTP, kö eller Azure Event Hubs) och [Dirigerings klientens bindning](durable-functions-bindings.md#orchestration-client). Du kan till exempel använda ett Queue-meddelande för att utlösa avslutning. Eller så kan du använda en HTTP-utlösare som skyddas av en Azure Active Directory autentiseringsprincip i stället för inbyggda HTTP-API: er som använder en genererad nyckel för autentisering.

Mer information finns i artikeln om [http-funktioner](durable-functions-http-features.md) , som förklarar hur du kan exponera asynkrona, långvariga processer över HTTP med Durable Functions-tillägget.

### <a name="pattern-4-monitor"></a><a name="monitoring"></a>Mönster #4: övervaka

Övervaknings mönstret avser en flexibel, återkommande process i ett arbets flöde. Ett exempel är att avsöker tills vissa villkor är uppfyllda. Du kan använda en vanlig [timer-utlösare](../functions-bindings-timer.md) för att hantera ett grundläggande scenario, till exempel ett periodiskt rensnings jobb, men dess intervall är statiskt och hanteringen av instans livstider blir komplex. Du kan använda Durable Functions för att skapa flexibla upprepnings intervall, hantera aktivitets livs längder och skapa flera övervaknings processer från ett enda dirigering.

Ett exempel på ett övervaknings mönster är att byta till det tidigare asynkrona HTTP API-scenariot. I stället för att exponera en slut punkt för en extern klient för att övervaka en långvarig åtgärd, förbrukar den tids krävande övervakaren en extern slut punkt och väntar sedan på en tillstånds ändring.

![Ett diagram över övervaknings mönstret](./media/durable-functions-concepts/monitor.png)

I några få kodrader kan du använda Durable Functions för att skapa flera Övervakare som ser godtyckliga slut punkter. Övervakarna kan avsluta körningen när ett villkor uppfylls, eller så kan en annan funktion använda den varaktiga Orchestration-klienten för att avsluta övervakarna. Du kan ändra en övervakares `wait` intervall baserat på ett speciellt villkor (till exempel exponentiell backoff.) 

Följande kod implementerar en grundläggande Övervakare:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInterval = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    job = json.loads(context.get_input())
    job_id = job["jobId"]
    polling_interval = job["pollingInterval"]
    expiry_time = job["expiryTime"]

    while context.current_utc_datetime < expiry_time:
        job_status = yield context.call_activity("GetJobStatus", job_id)
        if job_status == "Completed":
            # Perform an action when a condition is met.
            yield context.call_activity("SendAlert", job_id)
            break

        # Orchestration sleeps until this time.
        next_check = context.current_utc_datetime + timedelta(seconds=polling_interval)
        yield context.create_timer(next_check)

    # Perform more work here, or let the orchestration end.


main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Övervakaren stöds för närvarande inte i PowerShell.

---

När en begäran tas emot skapas en ny Dirigerings instans för jobb-ID: t. Instansen avsöker en status tills ett villkor uppfylls och loopen avslutas. En varaktig timer styr avsöknings intervallet. Sedan kan du utföra mer arbete, eller så kan dirigeringen avslutas. När `nextCheck` överskrider `expiryTime` övervakaren avslutas övervakaren.

### <a name="pattern-5-human-interaction"></a><a name="human"></a>Mönster #5: mänsklig interaktion

Många automatiserade processer omfattar viss typ av mänsklig interaktion. Att involvera människor i en automatiserad process är knepigt eftersom människor inte är hög tillgängliga och svarar som moln tjänster. En automatiserad process kan tillåta den här interaktionen genom att använda tids gränser och kompensations logik.

En godkännande process är ett exempel på en affärs process som involverar mänsklig interaktion. Godkännande från en chef kan krävas för en utgifts rapport som överstiger en viss rabatt. Om chefen inte godkänner utgifts rapporten inom 72 timmar (kanske chefen gick på semestern), används en eskalering för att få godkännande från någon annan (kanske chefens chef).

![Ett diagram över mänsklig interaktions mönster](./media/durable-functions-concepts/approval.png)

Du kan implementera mönstret i det här exemplet med hjälp av en Orchestrator-funktion. Orchestrator använder en [varaktig timer](durable-functions-timers.md) för att begära godkännande. Orchestrator eskalerar om timeout inträffar. Orchestrator väntar på en [extern händelse](durable-functions-external-events.md), till exempel ett meddelande som genereras av en mänsklig interaktion.

Följande exempel skapar en godkännande process för att demonstrera de mänskliga interaktions mönstren:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

Ring om du vill skapa en varaktig timer `context.CreateTimer` . Meddelandet tas emot av `context.WaitForExternalEvent` . Sedan `Task.WhenAny` uppmanas du att bestämma om du vill eskalera (tids gräns inträffar först) eller bearbeta godkännandet (godkännandet tas emot före tids gränsen).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Ring om du vill skapa en varaktig timer `context.df.createTimer` . Meddelandet tas emot av `context.df.waitForExternalEvent` . Sedan `context.df.Task.any` uppmanas du att bestämma om du vill eskalera (tids gräns inträffar först) eller bearbeta godkännandet (godkännandet tas emot före tids gränsen).

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    yield context.call_activity("RequestApproval", None)

    due_time = context.current_utc_datetime + timedelta(hours=72)
    durable_timeout_task = context.create_timer(due_time)
    approval_event_task = context.wait_for_external_event("ApprovalEvent")

    winning_task = yield context.task_any([approval_event_task, durable_timeout_task])

    if approval_event_task == winning_task:
        durable_timeout_task.cancel()
        yield context.call_activity("ProcessApproval", approval_event_task.result)
    else:
        yield context.call_activity("Escalate", None)


main = df.Orchestrator.create(orchestrator_function)
```

Ring om du vill skapa en varaktig timer `context.create_timer` . Meddelandet tas emot av `context.wait_for_external_event` . Sedan `context.task_any` uppmanas du att bestämma om du vill eskalera (tids gräns inträffar först) eller bearbeta godkännandet (godkännandet tas emot före tids gränsen).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Mänsklig interaktion stöds för närvarande inte i PowerShell.

---

En extern klient kan leverera händelse meddelandet till en väntande Orchestrator-funktion med hjälp av [inbyggda http-API: er](durable-functions-http-api.md#raise-event):

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

En händelse kan också aktive ras med den beständiga Orchestration-klienten från en annan funktion i samma Function-app:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


async def main(client: str):
    durable_client = df.DurableOrchestrationClient(client)
    is_approved = True
    await durable_client.raise_event(instance_id, "ApprovalEvent", is_approved)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Mänsklig interaktion stöds för närvarande inte i PowerShell.

---

### <a name="pattern-6-aggregator-stateful-entities"></a><a name="aggregator"></a>Mönster #6: aggregator (tillstånds känsliga entiteter)

Det sjätte mönstret är att sammanställa händelse data under en viss tids period till en enda, adresser bar *entitet*. I det här mönstret kan de data som sammanställs komma från flera källor, levereras i batchar eller vara spridda över långa tids perioder. Aggregator kan behöva vidta åtgärder för händelse data när de anländer, och externa klienter kan behöva fråga de sammanställda data.

![Aggregator-diagram](./media/durable-functions-concepts/aggregator.png)

Det är svårt att försöka implementera det här mönstret med normala, tillstånds lösa funktioner är att samtidighets kontroll blir en enorm utmaning. Du behöver inte bara bekymra dig om flera trådar som ändrar samma data samtidigt. du måste också bekymra dig om att se till att Aggregator bara körs på en enda virtuell dator i taget.

Du kan använda [varaktiga entiteter](durable-functions-entities.md) för att enkelt implementera det här mönstret som en enda funktion.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            ctx.SetState(currentValue + amount);
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }
}
```

Varaktiga entiteter kan också modelleras som klasser i .NET. Den här modellen kan vara användbar om listan över åtgärder är fast och blir stor. Följande exempel är en motsvarande implementering av `Counter` entiteten med hjälp av .NET-klasser och-metoder.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

# <a name="python"></a>[Python](#tab/python)

Varaktiga entiteter stöds för närvarande inte i python.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Det finns för närvarande inte stöd för varaktiga entiteter i PowerShell.

---

Klienter kan köa *åtgärder* för (kallas även "signalering") en entitets funktion som använder [enhets klient bindningen](durable-functions-bindings.md#entity-client).

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [DurableClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

> [!NOTE]
> Dynamiskt genererade proxyservrar är också tillgängliga i .NET för att signalera enheter på ett typ säkert sätt. Förutom att signalera kan klienter även fråga efter status för en entitets funktion med hjälp av [typ säkra metoder](durable-functions-bindings.md#entity-client-usage) i Dirigerings klient bindningen.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

Varaktiga entiteter stöds för närvarande inte i python.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Det finns för närvarande inte stöd för varaktiga entiteter i PowerShell.

---

Enhets funktioner är tillgängliga i [Durable Functions 2,0](durable-functions-versions.md) och senare för C# och Java Script.

## <a name="the-technology"></a>Tekniken

Durable Functions-tillägget är byggt ovanpå det [varaktiga aktivitets ramverket](https://github.com/Azure/durabletask)i bakgrunden, ett bibliotek med öppen källkod på GitHub som används för att skapa arbets flöden i kod. Precis som Azure Functions är Server lös utvecklingen av Azure WebJobs, Durable Functions är Server lös utvecklingen av det varaktiga aktivitets ramverket. Microsoft och andra organisationer använder det tåliga aktivitets ramverket i stor utsträckning för att automatisera verksamhets kritiska processer. Det är en naturlig anpassning för den serverbaserade Azure Functionss miljön.

## <a name="code-constraints"></a>Kod begränsningar

För att tillhandahålla pålitliga och långvariga körnings garantier har Orchestrator-funktioner en uppsättning kodnings regler som måste följas. Mer information finns i artikeln om [begränsningar för Orchestrator-funktions kod](durable-functions-code-constraints.md) .

## <a name="billing"></a>Fakturering

Durable Functions debiteras samma som Azure Functions. Mer information finns i [prissättning för Azure Functions](https://azure.microsoft.com/pricing/details/functions/). När du kör Orchestrator Functions i Azure Functions [förbruknings planen](../functions-scale.md#consumption-plan)finns det några fakturerings beteenden som du bör känna till. Mer information om dessa funktioner finns i artikeln [Durable Functions fakturering](durable-functions-billing.md) .

## <a name="jump-right-in"></a>Kom igång snabbt

Du kan komma igång med Durable Functions på mindre än 10 minuter genom att slutföra någon av dessa språkspecifika snabbstartsguider:

* [C# med Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript med hjälp av Visual Studio Code](quickstart-js-vscode.md)
* [Python med Visual Studio Code](quickstart-python-vscode.md)

I båda snabbstarterna skapar du och testar en beständig ”hello world”-funktion lokalt. Du publicerar sedan funktionskoden till Azure. Den funktion som du skapar orkestrerar och kedjar samman anrop till andra funktioner.

## <a name="learn-more"></a>Läs mer

Följande video visar fördelarna med Durable Functions:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

En mer djupgående diskussion om Durable Functions och den underliggande tekniken finns i följande video (den fokuserar på .NET, men begreppen gäller även för andra språk som stöds):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Eftersom Durable Functions är ett avancerat tillägg för [Azure Functions](../functions-overview.md) är det inte lämpligt för alla program. En jämförelse med andra Azure-orkestreringstekniker finns i avsnittet om att [jämföra Azure Functions med Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Durable Functions funktions typer och funktioner](durable-functions-types-features-overview.md)
