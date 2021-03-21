---
title: Aktivitets nav i Durable Functions – Azure
description: Läs om vad en aktivitets hubb är i Durable Functions-tillägget för Azure Functions. Lär dig hur du konfigurerar aktivitets nav.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 26234039c77601bc1d29beeebd3fcb8461d6d6c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009525"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Aktivitets nav i Durable Functions (Azure Functions)

En *aktivitets hubb* i [Durable Functions](durable-functions-overview.md) är en logisk behållare för Azure Storage resurser som används för dirigering. Orchestrator-och aktivitets funktioner kan bara samverka med varandra när de tillhör samma aktivitets nav.

Om flera Functions-appar delar ett lagrings konto, *måste* varje funktions app konfigureras med ett separat aktivitets nav namn. Ett lagrings konto kan innehålla flera aktivitets nav. Följande diagram illustrerar en aktivitets hubb per Function-app i delade och dedikerade lagrings konton.

![Diagram som visar delade och dedikerade lagrings konton.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage resurser

En aktivitets hubb består av följande lagrings resurser:

* En eller flera kontroll köer.
* En kö för arbets objekt.
* En historik tabell.
* Tabell med en instans.
* En lagrings behållare som innehåller en eller flera blobar för lån.
* En lagrings behållare som innehåller stora nytto Last meddelanden, om tillämpligt.

Alla dessa resurser skapas automatiskt i standard Azure Storages kontot när Orchestrator-, Entity-eller aktivitets funktioner körs eller är schemalagda att köras. I artikeln om [prestanda och skala](durable-functions-perf-and-scale.md) förklaras hur dessa resurser används.

## <a name="task-hub-names"></a>Namn på uppgifts hubb

Aktivitets nav identifieras med ett namn som följer dessa regler:

* Innehåller bara alfanumeriska tecken
* Börjar med en bokstav
* Får bestå av minst 3 tecken, högst 45 tecken

Namnet på aktivitets navet deklareras i *host.js* filen, som visas i följande exempel:

### <a name="hostjson-functions-20"></a>host.jspå (Functions 2,0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

### <a name="hostjson-functions-1x"></a>host.jspå (Functions 1. x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Aktivitets nav kan också konfigureras med hjälp av appinställningar, som du ser i följande `host.json` exempel fil:

### <a name="hostjson-functions-10"></a>host.jspå (Functions 1,0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.jspå (Functions 2,0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

Aktivitets navets namn anges till värdet för `MyTaskHub` appens inställning. Följande `local.settings.json` visar hur du definierar `MyTaskHub` inställningen som `samplehubname` :

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Följande kod visar hur du skriver en funktion som använder [Dirigerings klient bindningen](durable-functions-bindings.md#orchestration-client) för att arbeta med en aktivitets hubb som är konfigurerad som en app-inställning:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [DurableClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    object eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> Föregående C#-exempel är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Egenskapen aktivitets nav i `function.json` filen anges via appens inställning:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

# <a name="python"></a>[Python](#tab/python)

Egenskapen aktivitets nav i `function.json` filen anges via appens inställning:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

Namn på uppgifts hubbar måste börja med en bokstav och får bara bestå av bokstäver och siffror. Om inget värde anges används ett standard namn för aktivitets navet som visas i följande tabell:

| Beständig tilläggs version | Namn på standard aktivitets hubb |
| - | - |
| 2x | När det distribueras i Azure härleds aktivitets hubbens namn från namnet på Function- _appen_. När du kör utanför Azure är standard namnet på aktivitets navet `TestHubName` . |
| 1.x | Standard namnet för aktivitets hubben för alla miljöer är `DurableFunctionsHub` . |

Mer information om skillnaderna mellan tilläggs versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

> [!NOTE]
> Namnet är det som skiljer en aktivitets hubb från en annan när det finns flera aktivitets nav i ett delat lagrings konto. Om du har flera Function-appar som delar ett delat lagrings konto måste du uttryckligen konfigurera olika namn för varje aktivitets nav i *host.jspå* filer. Annars konkurrerar flera Function-appar med varandra för meddelanden, vilket kan resultera i odefinierat beteende, inklusive dirigeringar som förväntas "fastnar" i `Pending` `Running` läget eller.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du hanterar Orchestration-versioner](durable-functions-versioning.md)
