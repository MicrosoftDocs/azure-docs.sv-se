---
title: Använd Event Grid för ändrings meddelanden för konfigurations data för appar
description: Lär dig hur du använder Azure App konfigurations händelse prenumerationer för att skicka ändrings händelser för nyckel värden till en webb slut punkt
services: azure-app-configuration
author: AlexandraKemperMS
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 03/04/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: c188a4b7fe8e9223faa1cdeb52ae01ed83b94d84
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549787"
---
# <a name="use-event-grid-for-app-configuration-data-change-notifications"></a>Använd Event Grid för ändrings meddelanden för konfigurations data för appar

I den här artikeln får du lära dig hur du konfigurerar Azure App konfigurations händelse prenumerationer för att skicka ändrings händelser för nyckel värden till en webb slut punkt. Azure App konfigurations användare kan prenumerera på händelser som genereras när nyckel värden ändras. Dessa händelser kan utlösa Webhooks, Azure Functions, Azure Storage köer eller andra händelse hanterare som stöds av Azure Event Grid. Normalt kan du skicka händelser till en slutpunkt som bearbetar informationen om händelsen och utför åtgärder. Men för att enkelt beskriva den här artikeln kan skicka du händelser till en webbapp som samlar in och visar meddelanden.

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/). Du kan också använda Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här artikeln att du kör den senaste versionen av Azure CLI (2.0.70 eller senare). Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Om du inte använder Cloud Shell måste du först logga in med `az login`.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Event Grid-ämnen är Azure-resurser och måste placeras i en Azure-resursgrupp. Resursgruppen är en logisk samling där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). 

I följande exempel skapas en resurs grupp med namnet `<resource_group_name>` på den *västra* platsen.  Ersätt `<resource_group_name>` med ett unikt namn för din resursgrupp.

```azurecli-interactive
az group create --name <resource_group_name> --location westus
```

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

Ersätt `<appconfig_name>` med ett unikt namn för ditt konfigurations Arkiv och `<resource_group_name>` med den resurs grupp som du skapade tidigare. Namnet måste vara unikt eftersom det används som ett DNS-namn.

```azurecli-interactive
az appconfig create \
  --name <appconfig_name> \
  --location westus \
  --resource-group <resource_group_name> \
  --sku free
```

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på ämnet ska vi ska slutpunkten för händelsemeddelandet. Slutpunkten utför vanligtvis åtgärder baserat på informationen om händelsen. För att förenkla den här snabbstarten kan du distribuera en [förskapad webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar meddelanden om händelser. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

Ersätt `<your-site-name>` med ett unikt namn för din webbapp. Webbappnamnet måste vara unikt eftersom det är en del av DNS-posten.

```azurecli-interactive
$sitename=<your-site-name>

az deployment group create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

Det kan ta några minuter att slutföra distributionen. Efter distributionen har slutförts kan du visa webbappen för att kontrollera att den körs. I en webbläsare navigerar du till: `https://<your-site-name>.azurewebsites.net`

Webbplatsen bör visas utan några meddelanden.

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store"></a>Prenumerera på appens konfigurations Arkiv

Du prenumererar på ett ämne därför att du vill ange för Event Grid vilka händelser du vill följa och vart du vill skicka händelserna. I följande exempel prenumererar du på den app-konfiguration som du skapade och skickar URL: en från din webbapp som slut punkt för händelse avisering. Ersätt `<event_subscription_name>` med ett namn för din händelseprenumeration. För `<resource_group_name>` och `<appconfig_name>` använder du det värde du skapade tidigare.

Slutpunkten för ditt webbprogram måste innehålla suffixet `/api/updates/`.

```azurecli-interactive
appconfigId=$(az appconfig show --name <appconfig_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $appconfigId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

Visa ditt webbprogram igen och observera att en händelse för verifieringen av prenumerationen har skickats till den. Välj ögonikonen för att utöka informationen om händelsen. Händelserutnätet skickar valideringshändelsen så att slutpunkten kan bekräfta att den vill ta emot händelsedata. Webbappen inkluderar kod för att verifiera prenumerationen.

![Visa prenumerationshändelse](./media/quickstarts/event-grid/view-subscription-event.png)

## <a name="trigger-an-app-configuration-event"></a>Utlös en konfigurations händelse för appen

Nu ska vi utlösa en händelse och se hur Event Grid distribuerar meddelandet till slutpunkten. Skapa ett nyckel värde med hjälp av `<appconfig_name>` från tidigare.

```azurecli-interactive
az appconfig kv set --name <appconfig_name> --key Foo --value Bar --yes
```

Du har utlöst händelsen och Event Grid skickade meddelandet till den slutpunkt som du konfigurerade när du prenumererade. Visa din webbapp om du vill se händelsen som du har skickat.

```json
[{
  "id": "deb8e00d-8c64-4b6e-9cab-282259c7674f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/eventDemoGroup/providers/microsoft.appconfiguration/configurationstores/{appconfig-name}",
  "subject": "https://{appconfig-name}.azconfig.io/kv/Foo",
  "data": {
    "key": "Foo",
    "etag": "a1LIDdNEIV6wCnfv3xaip7fMXD3"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T18:59:54Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="clean-up-resources"></a>Rensa resurser
Om du planerar att fortsätta arbeta med den här program konfigurationen och händelse prenumerationen ska du inte rensa resurserna som du skapade i den här artikeln. Om du inte planerar att fortsätta kan du använda kommandona nedan för att ta bort alla resurser som har skapats i den här artikeln.

Ersätt `<resource_group_name>` med resursgruppen du skapade ovan.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar ämnen och händelse prenumerationer kan du läsa mer om nyckel värdes händelser och vilka Event Grid kan hjälpa dig:

- [Reagerar på Key-Value händelser](concept-app-configuration-event.md)
- [Om Event Grid](../event-grid/overview.md)
- [Azure Event Grid hanterare](../event-grid/event-handlers.md)
