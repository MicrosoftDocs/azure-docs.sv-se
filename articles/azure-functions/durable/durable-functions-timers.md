---
title: Timers i Durable Functions – Azure
description: Lär dig hur du implementerar varaktiga timers i Durable Functions-tillägget för Azure Functions.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: bb91f205a9b83b0b4b410644ef6c0fcbbf60876a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91876455"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Timers i Durable Functions (Azure Functions)

[Durable Functions](durable-functions-overview.md) tillhandahåller *varaktiga timers* för användning i Orchestrator-funktioner för att implementera fördröjningar eller ställa in timeout för asynkrona åtgärder. Varaktiga timers ska användas i Orchestrator-funktioner i stället för `Thread.Sleep` och `Task.Delay` (C#) eller `setTimeout()` och `setInterval()` (Java Script) eller `time.sleep()` (python).

Du skapar en varaktig timer genom att anropa `CreateTimer` (.net)-metoden eller `createTimer` (JavaScript)-metoden i [bindningen för Orchestration-utlösaren](durable-functions-bindings.md#orchestration-trigger). Metoden returnerar en uppgift som slutförs vid en angiven tidpunkt.

## <a name="timer-limitations"></a>Timer-begränsningar

När du skapar en timer som upphör att gälla 4:30 PM kommer det underliggande ständiga aktivitets ramverket att köa ett meddelande som bara är synligt på 4:30 PM. När du kör i Azure Functions förbruknings planen ser det nyligen synliga timer-meddelandet till att Function-appen aktive ras på en lämplig virtuell dator.

> [!NOTE]
> * Från och med [version 2.3.0](https://github.com/Azure/azure-functions-durable-extension/releases/tag/v2.3.0) av det varaktiga tillägget är de varaktiga tidsintervallen obegränsade. I tidigare versioner av tillägget är varaktiga timers begränsade till sju dagar. När du använder en tidigare version och behöver en fördröjning längre än sju dagar, använder du timer-API: erna i en `while` slinga för att simulera denna fördröjning.
> * Använd alltid i `CurrentUtcDateTime` stället för `DateTime.UtcNow` i .net eller i `currentUtcDateTime` stället för `Date.now` eller `Date.UTC` i Java Script när du beräknar brand tiden för varaktiga timers. Mer information finns i artikeln om [begränsningar för Orchestrator-funktions kod](durable-functions-code-constraints.md) .

## <a name="usage-for-delay"></a>Användning för fördröjning

I följande exempel visas hur du använder varaktiga timers för att fördröja körningar. Exemplet utfärdar ett fakturerings meddelande varje dag i 10 dagar.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> Föregående exempel mål i C# Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    for i in range(0, 9):
        deadline = context.current_utc_datetime + timedelta(days=1)
        yield context.create_timer(deadline)
        yield context.call_activity("SendBillingEvent")

main = df.Orchestrator.create(orchestrator_function)
```
---

> [!WARNING]
> Undvik oändlig loop i Orchestrator-funktioner. Information om hur du på ett säkert och effektivt sätt implementerar oändliga upprepnings scenarier finns i [Eternal-dirigeringar](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Användning för timeout

Det här exemplet illustrerar hur du använder varaktiga timers för att implementera tids gränser.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> Föregående exempel mål i C# Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    deadline = context.current_utc_datetime + timedelta(seconds=30)
    activity_task = context.call_activity("GetQuote")
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any([activity_task, timeout_task])
    if winner == activity_task:
        timeout_task.cancel()
        return True
    elif winner == timeout_task:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!WARNING]
> Använd ett `CancellationTokenSource` (.net) eller anropa `cancel()` det returnerade `TimerTask` (Java Script) för att avbryta en varaktig timer om din kod inte väntar på att den ska slutföras. Det varaktiga aktivitets ramverket ändrar inte dirigeringens status till slutförd förrän alla utestående aktiviteter har slutförts eller avbrutits.

Den här avbrotts mekanismen avslutar inte aktivitets funktionen eller under Orchestration-körningar. I stället tillåter det bara att Orchestrator-funktionen ignorerar resultatet och går vidare. Om din Function-app använder förbruknings planen debiteras du fortfarande för all tid och minne som används av funktionen övergivna aktiviteter. Som standard har funktioner som körs i förbruknings planen en tids gräns på fem minuter. Om den här gränsen överskrids återvinns Azure Functionss värden för att stoppa all körning och förhindra en uppräknings situation. [Tids gränsen för funktionen kan konfigureras](../functions-host-json.md#functiontimeout).

Ett mer djupgående exempel på hur du implementerar tids gränser i Orchestrator-funktioner finns i artikeln om [tids gränser för mänsklig interaktion &-telefon verifiering](durable-functions-phone-verification.md) .

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du höjer och hanterar externa händelser](durable-functions-external-events.md)
