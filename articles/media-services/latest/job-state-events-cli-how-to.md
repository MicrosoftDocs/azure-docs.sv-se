---
title: Övervaka Azure Media Services händelser med Event Grid
description: Den här artikeln visar hur du prenumererar på Event Grid för att övervaka Azure Media Services händelser med hjälp av Azure CLI.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: e01083b8618e4751770ed69aa4b1c20f8a072d88
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092322"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Skapa och övervaka Media Services händelser med Event Grid med Azure CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Event Grid är en händelsetjänst för molnet. Den här tjänsten använder [händelse prenumerationer](../../event-grid/concepts.md#event-subscriptions) för att dirigera händelse meddelanden till prenumeranter. Media Services händelser innehåller all information du behöver för att svara på ändringar i dina data. Du kan identifiera en Media Services-händelse eftersom egenskapen eventType börjar med "Microsoft. Media.". Mer information finns i [Media Services händelse scheman](media-services-event-schemas.md).

I den här artikeln använder du Azure CLI för att prenumerera på händelser för ditt Azure Media Services-konto. Sedan utlöser du händelser för att visa resultatet. Normalt kan du skicka händelser till en slutpunkt som bearbetar informationen om händelsen och utför åtgärder. I den här artikeln skickar du händelserna till en webbapp som samlar in och visar meddelandena.

## <a name="prerequisites"></a>Förutsättningar

- En aktiv Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- Installera och använd CLI lokalt – du måste ha Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

    För närvarande fungerar inte alla [Media Services v3 CLI](/cli/azure/ams)-kommandon i Azure Cloud Shell. Vi rekommenderar att du använder CLI lokalt.

- [Skapa ett Media Services-konto](./create-account-howto.md).

    Se till att komma ihåg de värden som du använde för resursgruppens namn och namnet på Media Services-kontot.

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på händelser för Media Services kontot ska vi skapa slut punkten för händelse meddelandet. Slutpunkten utför vanligtvis åtgärder baserat på informationen om händelsen. I den här artikeln distribuerar du en [fördefinierad webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar händelse meddelandena. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

1. Välj **Deploy to Azure** (Distribuera till Azure) för att distribuera lösningen till din prenumeration. Ange parametervärdena i Azure Portal.

   [![Bild som visar en knapp med etiketten "distribuera till Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)

1. Det kan ta några minuter att slutföra distributionen. Efter distributionen har slutförts kan du visa webbappen för att kontrollera att den körs. I en webbläsare navigerar du till: `https://<your-site-name>.azurewebsites.net`

Om du växlar till webbplatsen "Azure Event Grid Viewer" visas inga händelser än.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Ange Azure-prenumeration

Med följande kommando anger du ID för den Azure-prenumeration som du vill använda för Media Services-kontot. Du kan se en lista över prenumerationer som du har åtkomst till under [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Prenumerera på Media Services händelser

Du prenumererar på en artikel för att berätta Event Grid vilka händelser du vill spåra. I följande exempel prenumererar Media Services kontot som du skapade och skickar URL: en från den webbplats som du skapade som slut punkt för händelse aviseringen. 

Ersätt `<event_subscription_name>` med ett unikt namn för din händelse prenumeration. För `<resource_group_name>` och `<ams_account_name>` använder du de värden som du använde när du skapade Media Services-kontot. För anger du `<endpoint_URL>` URL: en för din webbapp och lägger till `api/updates` den på Start sidans URL. Genom att ange slut punkten när du prenumererar Event Grid hanterar routning av händelser till den slut punkten. 

1. Hämta resurs-ID

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Exempel:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Prenumerera på händelserna

    ```azurecli
    az eventgrid event-subscription create \
    --source-resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Exempel:

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Du kan få varning om validering av hand skakning. Vänta några minuter så bör hand skakningen verifieras.

Nu ska vi utlösa händelser för att se hur Event Grid distribuerar meddelandet till din slut punkt.

## <a name="send-an-event-to-your-endpoint"></a>Skicka en händelse till din slutpunkt

Du kan utlösa händelser för Media Services kontot genom att köra ett kodnings jobb. Du kan följa [den här snabb](stream-files-dotnet-quickstart.md) starten för att koda en fil och börja skicka händelser. 

Visa ditt webbprogram igen och observera att en händelse för verifieringen av prenumerationen har skickats till den. Händelserutnätet skickar valideringshändelsen så att slutpunkten kan bekräfta att den vill ta emot händelsedata. Slut punkten måste anges `validationResponse` till `validationCode` . Mer information finns i [Event Grid säkerhet och autentisering](../../event-grid/security-authentication.md). Du kan visa webb program koden för att se hur den validerar prenumerationen.

> [!TIP]
> Välj ögonikonen för att utöka informationen om händelsen. Uppdatera inte sidan om du vill visa alla händelser.

![Visa prenumerationshändelse](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Nästa steg

[Ladda upp, koda och strömma](stream-files-tutorial-with-api.md)