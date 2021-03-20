---
title: Singleton för Durable Functions – Azure
description: Använda singleton i Durable Functions-tillägget för Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: azfuncdf
ms.openlocfilehash: 50ed473d61dff19f41f77a79513c0ddab521e56f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91325750"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Singleton-Dirigerare i Durable Functions (Azure Functions)

För bakgrunds jobb måste du ofta se till att endast en instans av en viss Orchestrator körs i taget. Du kan se till att den här typen av singleton-beteende i [Durable Functions](durable-functions-overview.md) genom att tilldela ett särskilt instans-ID till en Orchestrator när du skapar den.

## <a name="singleton-example"></a>Singleton-exempel

I följande exempel visas en funktion för HTTP-utlösare som skapar en singleton-dirigering av bakgrunds jobb. Koden ser till att det bara finns en instans för ett angivet instans-ID.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists or an existing one stopped running(completed/failed/terminated).
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Completed 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Failed 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        // An instance with the specified ID doesn't exist or an existing one stopped running, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists or an existing one still running, don't create one.
        return new HttpResponseMessage(HttpStatusCode.Conflict)
        {
            Content = new StringContent($"An instance with ID '{instanceId}' already exists."),
        };
    }
}
```

> [!NOTE]
> Föregående C#-kod är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `OrchestrationClient` attribut i stället för `DurableClient` attributet och du måste använda `DurableOrchestrationClient` parameter typen i stället för `IDurableOrchestrationClient` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**function.json**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists or an existing one stopped running(completed/failed/terminated).
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance 
        || existingInstance.runtimeStatus == "Completed" 
        || existingInstance.runtimeStatus == "Failed" 
        || existingInstance.runtimeStatus == "Terminated") {
        // An instance with the specified ID doesn't exist or an existing one stopped running, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists or an existing one still running, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

# <a name="python"></a>[Python](#tab/python)

**function.json**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**__init__. py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    instance_id = req.route_params['instanceId']
    function_name = req.route_params['functionName']

    existing_instance = await client.get_status(instance_id)

    if existing_instance != None or existing_instance.runtime_status in ["Completed", "Failed", "Terminated"]:
        event_data = req.get_body()
        instance_id = await client.start_new(function_name, instance_id, event_data)
        logging.info(f"Started orchestration with ID = '{instance_id}'.")
        return client.create_check_status_response(req, instance_id)
    else:
        return {
            'status': 409,
            'body': f"An instance with ID '${instance_id}' already exists"
        }

```

---

Instans-ID: n är som standard slumpmässigt genererade GUID. I föregående exempel skickas instans-ID: t i flödes data från URL: en. Kod anropen `GetStatusAsync` (C#), `getStatus` (Java Script) eller `get_status` (python) för att kontrol lera om en instans med det angivna ID: t redan körs. Om ingen sådan instans körs skapas en ny instans med detta ID.

> [!NOTE]
> Det finns ett möjligt konkurrens villkor i det här exemplet. Om två instanser av **HttpStartSingle** körs samtidigt, rapporterar båda funktions anropen att de lyckas, men endast en Dirigerings instans kommer att starta. Detta kan ha oönskade sido effekter, beroende på dina behov. Därför är det viktigt att se till att det inte finns två begär Anden som kan köra den här utlösnings funktionen samtidigt.

Implementerings informationen för Orchestrator-funktionen spelar ingen roll. Det kan vara en vanlig Orchestrator-funktion som startar och slutförs, eller så kan det vara en som kör för alltid (det vill säga ett [Eternal-Orchestration](durable-functions-eternal-orchestrations.md)). Den viktiga punkten är att det bara finns en instans som körs i taget.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om de ursprungliga HTTP-funktionerna i Orchestration](durable-functions-http-features.md)
