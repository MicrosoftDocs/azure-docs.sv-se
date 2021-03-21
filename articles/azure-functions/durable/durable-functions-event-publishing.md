---
title: Durable Functions publicering till Azure Event Grid (förhands granskning)
description: Lär dig hur du konfigurerar automatisk Azure Event Grid publicering för Durable Functions.
ms.topic: conceptual
ms.date: 04/25/2020
ms.openlocfilehash: 44df100a5c794abf918a09dea0f94d30ddf916d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102175965"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions publicering till Azure Event Grid (förhands granskning)

Den här artikeln visar hur du konfigurerar Durable Functions att publicera livs cykel händelser för dirigering (till exempel skapade, slutförda och misslyckade) till ett anpassat [Azure Event Grid-ämne](../../event-grid/overview.md).

Här följer några scenarier där den här funktionen är användbar:

* **DevOps-scenarier som blå/gröna distributioner**: du kanske vill veta om några aktiviteter körs innan du implementerar [distributions strategin sida vid sida](durable-functions-versioning.md#side-by-side-deployments).

* **Stöd för avancerad övervakning och diagnostik**: du kan hålla koll på dirigerings status information i ett externt arkiv som är optimerat för frågor, till exempel Azure SQL Database eller Azure Cosmos dB.

* **Tids krävande bakgrunds aktivitet**: om du använder Durable Functions för en tids krävande bakgrunds aktivitet hjälper den här funktionen dig att känna till aktuell status.

## <a name="prerequisites"></a>Förutsättningar

* Installera [Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) i ditt Durable Functions-projekt.
* Installera [Azure Storage emulator](../../storage/common/storage-use-emulator.md) (endast Windows) eller Använd ett befintligt Azure Storage-konto.
* Installera [Azure CLI](/cli/azure/) eller Använd [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Skapa ett anpassat Event Grid-ämne

Skapa ett Event Grid ämne för att skicka händelser från Durable Functions. Följande instruktioner visar hur du skapar ett ämne med hjälp av Azure CLI. Du kan också skapa ämnet med [hjälp av PowerShell](../../event-grid/custom-event-quickstart-powershell.md) eller med [hjälp av Azure Portal](../../event-grid/custom-event-quickstart-portal.md).

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot `az group create`. Azure Event Grid stöder för närvarande inte alla regioner. Information om vilka regioner som stöds finns i [Översikt över Azure Event Grid](../../event-grid/overview.md).

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Skapa en anpassat ämne

Ett Event Grid ämne innehåller en användardefinierad slut punkt som du kan använda för att publicera din händelse. Ersätt `<topic_name>` med ett unikt namn för ditt ämne. Ämnes namnet måste vara unikt eftersom det blir en DNS-post.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Hämta slut punkten och nyckeln

Hämta slut punkten för ämnet. Ersätt `<topic_name>` med det namn som du har valt.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Hämta ämnes nyckeln. Ersätt `<topic_name>` med det namn som du har valt.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Nu kan du skicka händelser till ämnet.

## <a name="configure-event-grid-publishing"></a>Konfigurera Event Grid publicering

Leta upp filen i Durable Functions-projektet `host.json` .

### <a name="durable-functions-1x"></a>Durable Functions 1. x

Lägg till `eventGridTopicEndpoint` och `eventGridKeySettingName` i en `durableTask` egenskap.

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Durable Functions 2. x

Lägg till ett `notifications` avsnitt i `durableTask` filens egenskap och Ersätt `<topic_name>` med det namn som du har valt. Om `durableTask` egenskaperna eller `extensions` inte finns skapar du dem som det här exemplet:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "notifications": {
        "eventGrid": {
          "topicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
          "keySettingName": "EventGridKey"
        }
      }
    }
  }
}
```

De möjliga Azure Event Grid konfigurations egenskaperna finns i [host.jsi dokumentationen](../functions-host-json.md#durabletask). När du har konfigurerat `host.json` filen skickar din Function-app livs cykel händelser till Event Grid ämnet. Den här åtgärden startar när du kör din Function-app både lokalt och i Azure.

Ange appens inställning för ämnes nyckeln i Funktionsapp och `local.settings.json` . Följande JSON är ett exempel på `local.settings.json` för lokal fel sökning. Ersätt `<topic_key>` med ämnes nyckeln.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Om du använder Storage- [emulatorn](../../storage/common/storage-use-emulator.md) (endast Windows) kontrollerar du att den fungerar. Det är en bra idé att köra `AzureStorageEmulator.exe clear all` kommandot innan du kör.

Om du använder ett befintligt Azure Storage-konto ersätter du `UseDevelopmentStorage=true` `local.settings.json` med dess anslutnings sträng.

## <a name="create-functions-that-listen-for-events"></a>Skapa funktioner som lyssnar efter händelser

Med hjälp av Azure Portal skapar du en annan Function-app för att lyssna efter händelser som publicerats av din Durable Functions-app. Det är bäst att hitta det i samma region som Event Grid ämnet.

### <a name="create-an-event-grid-trigger-function"></a>Skapa en Event Grid trigger-funktion

1. I din Function-app väljer du **Functions** och väljer sedan **+ Lägg till** 

   :::image type="content" source="./media/durable-functions-event-publishing/function-add-function.png" alt-text="Lägg till en funktion i Azure Portal." border="true":::

1. Sök efter **Event Grid** och välj sedan mallen **Azure Event Grid utlösare** . 

    :::image type="content" source="./media/durable-functions-event-publishing/function-select-event-grid-trigger.png" alt-text="Välj händelse rutnätets utlösare mall i Azure Portal." border="true":::

1. Ge den nya utlösaren ett namn och välj sedan **skapa funktion**.

    :::image type="content" source="./media/durable-functions-event-publishing/function-name-event-grid-trigger.png" alt-text="Namnge Event Grid-utlösaren i Azure Portal." border="true":::


    En funktion med följande kod skapas:

    # <a name="c-script"></a>[C#-skript](#tab/csharp-script)

    ```csharp
    #r "Newtonsoft.Json"
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(JObject eventGridEvent, ILogger log)
    {
        log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
    }
    ```

   # <a name="javascript"></a>[JavaScript](#tab/javascript)

   ```javascript
   module.exports = async function(context, eventGridEvent) {
       context.log(typeof eventGridEvent);
       context.log(eventGridEvent);
   }
   ```

---

### <a name="add-an-event-grid-subscription"></a>Lägg till en Event Grid-prenumeration

Nu kan du lägga till en Event Grid-prenumeration för Event Grid avsnittet som du har skapat. Mer information finns [i begrepp i Azure Event Grid](../../event-grid/concepts.md).

1. I den nya funktionen väljer du **integration** och väljer sedan **Event Grid utlösare (eventGridEvent)**. 

    :::image type="content" source="./media/durable-functions-event-publishing/eventgrid-trigger-link.png" alt-text="Välj länken Event Grid utlösare." border="true":::

1. Välj **skapa Event Grid Beskrivning**.

    :::image type="content" source="./media/durable-functions-event-publishing/create-event-grid-subscription.png" alt-text="Skapa Event Grid prenumerationen." border="true":::

1. Namnge din händelse prenumeration och välj ämnes typen **Event Grid ämnen** . 

1. Välj prenumerationen. Välj sedan den resurs grupp och resurs som du skapade för Event Grid avsnittet. 

1. Välj **Skapa**.

    :::image type="content" source="./media/durable-functions-event-publishing/event-grid-subscription-details.png" alt-text="Skapa en Event Grid-prenumeration." border="true":::

Nu är du redo att ta emot livs cykel händelser.

## <a name="run-durable-functions-app-to-send-the-events"></a>Kör Durable Functions app för att skicka händelserna

I Durable Functions-projektet som du konfigurerade tidigare startar du fel sökning på den lokala datorn och påbörjar ett dirigering. Appen publicerar Durable Functions livs cykel händelser till Event Grid. Kontrol lera att Event Grid utlöser lyssnings funktionen som du skapade genom att kontrol lera loggarna i Azure Portal.

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Händelseschema

I följande lista beskrivs schemat för livs cykel händelser:

* **`id`**: Unik identifierare för händelsen Event Grid.
* **`subject`**: Sökväg till händelsens ämne. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` är `Running` ,, `Completed` `Failed` och `Terminated` .  
* **`data`**: Durable Functions vissa parametrar.
  * **`hubName`**: [TaskHub](durable-functions-task-hubs.md) namn.
  * **`functionName`**: Orchestrator-funktions namn.
  * **`instanceId`**: Durable Functions instanceId.
  * **`reason`**: Ytterligare data som är associerade med spårnings händelsen. Mer information finns i [diagnostik i Durable functions (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**: Dirigerings körnings status. Körs, slutförd, misslyckades, avbröts.
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: Tidpunkt (UTC).
* **`dataVersion`**: Version av livs cykelns händelse schema.
* **`metadataVersion`**: Metadata-version.
* **`topic`**: Resurs för Event Grid-ämne.

## <a name="how-to-test-locally"></a>Testa lokalt

Om du vill testa lokalt kan du läsa [Azure Function Event Grid utlösa lokal fel sökning](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om instans hantering i Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Lär dig mer om versions hantering i Durable Functions](durable-functions-versioning.md)
