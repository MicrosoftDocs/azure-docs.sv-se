---
title: Under dirigering för Durable Functions – Azure
description: Så här anropar du dirigeringar från dirigering i Durable Functions-tillägget för Azure Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 5625bc2ddfa4b6f527ca16f19f33d257a1834d4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85340809"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Under dirigering i Durable Functions (Azure Functions)

Förutom att anropa aktivitets funktioner kan Orchestrator-funktioner anropa andra Orchestrator-funktioner. Du kan till exempel bygga ett större dirigering av ett bibliotek med mindre Orchestrator-funktioner. Du kan också köra flera instanser av en Orchestrator-funktion parallellt.

En Orchestrator-funktion kan anropa en annan Orchestrator-funktion med hjälp av- `CallSubOrchestratorAsync` eller `CallSubOrchestratorWithRetryAsync` -metoderna i .net, eller `callSubOrchestrator` eller- `callSubOrchestratorWithRetry` metoderna i Java Script. Artikeln [fel hantering &s kompensation](durable-functions-error-handling.md#automatic-retry-on-failure) innehåller mer information om automatiskt återförsök.

Under Orchestrator-funktioner fungerar precis som aktivitets funktioner från anroparens perspektiv. De kan returnera ett värde, utlösa ett undantag och kan förväntas av den överordnade Orchestrator-funktionen. 

> [!NOTE]
> Under dirigering stöds för närvarande i .NET och Java Script.

## <a name="example"></a>Exempel

I följande exempel illustreras ett IoT-scenario ("Sakernas Internet") där det finns flera enheter som måste tillhandahållas. Följande funktion representerar det etablerings arbets flöde som måste utföras för varje enhet:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

---

Den här Orchestrator-funktionen kan användas som-är för en-off-enhets etablering eller kan ingå i ett större Dirigerings steg. I det senare fallet kan den överordnade Orchestrator-funktionen schemalägga instanser av `DeviceProvisioningOrchestration` med hjälp av `CallSubOrchestratorAsync` (.net) eller `callSubOrchestrator` (Java Script) API.

Här är ett exempel som visar hur du kör flera Orchestrator-funktioner parallellt.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

> [!NOTE]
> Föregående C#-exempel är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

---

> [!NOTE]
> Under dirigering måste definieras i samma Function-app som den överordnade samordningen. Om du behöver anropa och vänta på dirigeringar i en annan Function-app, bör du överväga att använda det inbyggda stödet för HTTP API: er och HTTP 202-avsökningens konsument mönster. Mer information finns i avsnittet om [http-funktioner](durable-functions-http-features.md) .

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du anger en anpassad Dirigerings status](durable-functions-custom-orchestration-status.md)
