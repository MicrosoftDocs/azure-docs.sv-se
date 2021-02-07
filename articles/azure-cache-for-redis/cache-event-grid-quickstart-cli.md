---
title: 'Snabb start: dirigera Azure cache efter Redis-händelser till webb slut punkt med Azure CLI'
description: Använd Azure Event Grid för att prenumerera på Azure cache för Redis-händelser, utlösa en händelse och visa resultatet.
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 7f33ca0043400962054fabb1aadb1da612fe5426
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806434"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-azure-cli"></a>Snabb start: dirigera Azure cache efter Redis-händelser till webb slut punkt med Azure CLI

Azure Event Grid är en händelsetjänst för molnet. I den här snabb starten använder du Azure CLI för att prenumerera på Azure cache för Redis-händelser, utlösa en händelse och visa resultatet.

Normalt kan du skicka händelser till en slutpunkt som bearbetar informationen om händelsen och utför åtgärder. För att förenkla den här snabb starten kommer du dock att skicka händelser till en webbapp som samlar in och visar meddelandena. När du slutför stegen som beskrivs i den här snabb starten ser du att händelse data har skickats till webbappen.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här snabb starten att du kör den senaste versionen av Azure CLI (2.0.70 eller senare). Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Om du inte använder Cloud Shell måste du först logga in med `az login`.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Event Grid ämnen distribueras som enskilda Azure-resurser och måste tillhandahållas under en Azure-resurs grupp. En resurs grupp är en logisk samling där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). 

I följande exempel skapas en resursgrupp med namnet `<resource_group_name>` på platsen *westcentralus*.  Ersätt `<resource_group_name>` med ett unikt namn för din resursgrupp.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-cache-instance"></a>Skapa en cache-instans

```azurecli-interactive
#/bin/bash

# Create a Basic C0 (256 MB) Azure Cache for Redis instance
az redis create --name <cache_name> --resource-group <resource_group_name> --location westcentralus --sku Basic --vm-size C0
```


## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på ämnet ska vi ska slutpunkten för händelsemeddelandet. Slutpunkten utför vanligtvis åtgärder baserat på informationen om händelsen. För att förenkla den här snabbstarten kan du distribuera en [förskapad webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar meddelanden om händelser. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

Ersätt `<your-site-name>` med ett unikt namn för din webbapp. Webbappnamnet måste vara unikt eftersom det är en del av DNS-posten.

```azurecli-interactive
sitename=<your-site-name>

az group deployment create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

Det kan ta några minuter att slutföra distributionen. Efter distributionen har slutförts kan du visa webbappen för att kontrollera att den körs. I en webbläsare navigerar du till: `https://<your-site-name>.azurewebsites.net`

Webbplatsen bör visas utan några meddelanden.

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-azure-cache-for-redis-instance"></a>Prenumerera på Azure-cachen för Redis-instansen

I det här steget ska du prenumerera på ett ämne för att berätta Event Grid vilka händelser du vill spåra och var de ska skickas. I följande exempel prenumererar du på Azure cache för Redis-instansen som du skapade och skickar URL: en från din webbapp som slut punkt för händelse avisering. Ersätt `<event_subscription_name>` med ett namn för din händelseprenumeration. För `<resource_group_name>` och `<cache_name>` använder du det värde du skapade tidigare.

Slutpunkten för ditt webbprogram måste innehålla suffixet `/api/updates/`.

```azurecli-interactive
cacheId=$(az redis show --name <cache_name> --resource-group <resource_group_name> --query id --subscription <subscription_id>)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --source-resource-id $cacheId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

Visa ditt webbprogram igen och observera att en händelse för verifieringen av prenumerationen har skickats till den. Välj ögonikonen för att utöka informationen om händelsen. Händelserutnätet skickar valideringshändelsen så att slutpunkten kan bekräfta att den vill ta emot händelsedata. Webbappen inkluderar kod för att verifiera prenumerationen.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid Viewer.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Utlösa en händelse från Azure cache för Redis

Nu ska vi utlösa en händelse och se hur Event Grid distribuerar meddelandet till slutpunkten. Nu ska vi exportera data som lagras i Azure-cachen för Redis-instansen. Använd värdena för `{cache_name}` och `{resource_group_name}` du skapade tidigare.

```azurecli-interactive
az redis export  --ids '/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}' \
    --prefix '<prefix_for_exported_files>' \
    --container '<SAS_url>'  
```

Du har utlöst händelsen och Event Grid skickade meddelandet till den slutpunkt som du konfigurerade när du prenumererade. Visa din webbapp om du vill se händelsen som du har skickat.


```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ExportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ExportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ExportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Rensa resurser
Om du planerar att fortsätta arbeta med den här Azure-cachen för Redis-instansen och händelse prenumerationen ska du inte rensa resurserna som skapades i den här snabb starten. Om du inte planerar att fortsätta använder du följande kommando för att ta bort de resurser som du skapade i den här snabb starten.

Ersätt `<resource_group_name>` med resursgruppen du skapade ovan.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar ämnen och händelse prenumerationer kan du läsa mer om Azure cache för Redis-händelser och vilka Event Grid kan hjälpa dig:

- [Reagerar på Azure cache för Redis-händelser](cache-event-grid.md)
- [Om Event Grid](../event-grid/overview.md)
