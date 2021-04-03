---
title: Hantera fel i Durable Functions – Azure
description: Lär dig hur du hanterar fel i Durable Functions-tillägget för Azure Functions.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 023f9dfcc421935c3f7515e847108925d5e5521e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673655"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Hantera fel i Durable Functions (Azure Functions)

Varaktiga funktions dirigering implementeras i kod och kan använda programmeringsspråkets inbyggda fel hanterings funktioner. Det finns egentligen inga nya koncept som du behöver lära dig för att lägga till fel hantering och kompensation till dina dirigeringar. Det finns dock några beteenden som du bör vara medveten om.

## <a name="errors-in-activity-functions"></a>Fel i aktivitets funktioner

Alla undantag som genereras i en aktivitets funktion konverteras tillbaka till Orchestrator-funktionen och utlöstes som `FunctionFailedException` . Du kan skriva fel hantering och kompensations kod som passar dina behov i Orchestrator-funktionen.

Anta till exempel följande Orchestrator-funktion som överför fonder från ett konto till ett annat:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> Föregående C#-exempel är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    transfer_details = context.get_input()

    yield context.call_activity('DebitAccount', {
         'account': transfer_details['sourceAccount'],
         'amount' : transfer_details['amount']
    })

    try:
        yield context.call_activity('CreditAccount', {
                'account': transfer_details['destinationAccount'],
                'amount': transfer_details['amount'],
            })
    except:
        yield context.call_activity('CreditAccount', {
            'account': transfer_details['sourceAccount'],
            'amount': transfer_details['amount']
        })

main = df.Orchestrator.create(orchestrator_function)
```

---

Om det första funktions anropet för **CreditAccount** Miss lyckas kompenserar Orchestrator-funktionen genom att kreditera pengarna tillbaka till käll kontot.

## <a name="automatic-retry-on-failure"></a>Automatiskt försök vid fel

När du anropar aktivitets funktioner eller under Orchestration-funktioner kan du ange en princip för automatisk återförsök. Följande exempel försöker anropa en funktion upp till tre gånger och väntar 5 sekunder mellan varje nytt försök:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> Föregående C#-exempel är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    first_retry_interval_in_milliseconds = 5000
    max_number_of_attempts = 3

    retry_options = df.RetryOptions(first_retry_interval_in_milliseconds, max_number_of_attempts)

    yield context.call_activity_with_retry('FlakyFunction', retry_options)

main = df.Orchestrator.create(orchestrator_function)
```

---

Aktivitets funktions anropet i föregående exempel tar en parameter för att konfigurera en princip för automatisk återförsök. Det finns flera alternativ för att anpassa principen för automatiskt återförsök:

* **Maximalt antal försök**: maximalt antal återförsök.
* **Första** återförsöksintervall: vänte tiden innan det första försöket.
* **Backoff-koefficient**: den koefficient som används för att fastställa frekvensen av ökningen av backoff. Standardvärdet är 1.
* **Högsta återförsöksintervall**: den maximala vänte tiden mellan återförsök.
* **Timeout för återförsök**: maximal tid att spendera på nya försök. Standard beteendet är att försöka på obestämd tid.
* **Referens**: en användardefinierad motringning kan anges för att avgöra om en funktion ska provas igen. 

> [!NOTE]
> Användardefinierade återanrop stöds inte för närvarande av Durable Functions i Java Script ( `context.df.RetryOptions` ).


## <a name="function-timeouts"></a>Funktions tids gränser

Du kanske vill överge ett funktions anrop i en Orchestrator-funktion om det tar för lång tid att slutföra. Det är ett bra sätt att göra detta idag genom att skapa en [beständig timer](durable-functions-timers.md) med `context.CreateTimer` (.net), `context.df.createTimer` (Java Script) eller `context.create_timer` (python) tillsammans med `Task.WhenAny` (.net), `context.df.Task.any` (Java Script) eller `context.task_any` (python), som i följande exempel:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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
> Föregående C#-exempel är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
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
    deadline = context.current_utc_datetime + timedelta(seconds = 30)
    
    activity_task = context.call_activity('FlakyFunction')
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any(activity_task, timeout_task)
    if winner == activity_task:
        timeout_task.cancel()
        return True
    else:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!NOTE]
> Den här mekanismen avslutar egentligen inte körningen av aktivitets funktionen. I stället tillåter det bara att Orchestrator-funktionen ignorerar resultatet och går vidare. Mer information finns i dokumentationen för [timers](durable-functions-timers.md#usage-for-timeout) .

## <a name="unhandled-exceptions"></a>Ohanterade undantag

Om en Orchestrator-funktion Miss lyckas med ett ohanterat undantag loggas detaljerna för undantaget och instansen slutförs med en `Failed` status.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Eternal-dirigeringar](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Lär dig hur du diagnostiserar problem](durable-functions-diagnostics.md)
