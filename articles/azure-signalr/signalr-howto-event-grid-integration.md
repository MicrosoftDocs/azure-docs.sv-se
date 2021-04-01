---
title: Så här skickar du Azure SignalR service-händelser till Event Grid
description: En guide som visar hur du aktiverar Event Grid händelser för signal tjänsten och sedan skickar klient anslutning anslutna/frånkopplade händelser till ett exempel program.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: 84b83c1dd541418c446a89a6f51be668cb41e54e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94562652"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Skicka händelser från Azure SignalR Service till Event Grid

Azure Event Grid är en helt hanterad tjänst för händelse dirigering som tillhandahåller enhetlig händelse förbrukning med en pub-sub-modell. I den här guiden använder du Azure CLI för att skapa en Azure SignalR-tjänst, prenumererar på anslutnings händelser och distribuerar sedan ett exempel webb program för att ta emot händelserna. Slutligen kan du ansluta och koppla från och se händelse nytto lasten i exempel programmet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Azure CLI-kommandona i den här artikeln är formaterade för **bash** -gränssnittet. Om du använder ett annat gränssnitt som PowerShell eller kommando tolken kan du behöva justera rad fortsättnings tecken eller variabla tilldelnings rader i enlighet med detta. I den här artikeln används variabler för att minimera mängden kommando redigering som krävs.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resurs grupp är en logisk behållare där du distribuerar och hanterar dina Azure-resurser. Följande [AZ Group Create][az-group-create] -kommando skapar en resurs grupp med namnet *myResourceGroup* i regionen *östra* . Ange ett annat värde om du vill använda ett annat namn för resurs gruppen `RESOURCE_GROUP_NAME` .

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Skapa en Azure SignalR Service

Distribuera sedan en Azure SignalR-tjänst till resurs gruppen med följande kommandon.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

När signal tjänsten har skapats returnerar Azure CLI utdata som liknar följande:

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>Skapa en händelse slut punkt

I det här avsnittet använder du en Resource Manager-mall som finns på en GitHub-lagringsplats för att distribuera ett fördefinierat exempel webb program till Azure App Service. Senare prenumererar du på registrets Event Grid händelser och anger den här appen som den slut punkt som händelserna ska skickas till.

Distribuera exempel appen genom `SITE_NAME` att ange ett unikt namn för din webbapp och köra följande kommandon. Plats namnet måste vara unikt inom Azure eftersom det ingår i det fullständigt kvalificerade domän namnet (FQDN) för webbappen. I ett senare avsnitt går du till appens FQDN i en webbläsare för att visa dina register händelser.

```azurecli-interactive
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

När distributionen har slutförts (det kan ta några minuter) öppnar du en webbläsare och navigerar till din webbapp för att kontrol lera att den körs:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Prenumerera på register händelser

I Event Grid prenumererar du på ett *ämne* för att berätta vilka händelser du vill spåra och var de ska skickas. Följande [AZ eventgrid Event-Subscription Create-][az-eventgrid-event-subscription-create] kommandot prenumererar på den Azure SignalR-tjänst som du skapade och anger webbappens URL som den slut punkt som den ska skicka händelser till. Miljövariablerna som du har fyllt i tidigare avsnitt återanvänds här, så inga ändringar krävs.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

När prenumerationen är klar bör du se utdata som liknar följande:

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Utlös register händelser

Växla till tjänst läget `Serverless Mode` och konfigurera en klient anslutning till signal tjänsten. Du kan ta [Server lös exempel](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) som referens.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a separate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Visa register händelser

Nu har du anslutit en klient till signal tjänsten. Navigera till Event Grid Viewer-webbappen och se en `ClientConnectionConnected` händelse. Om du avbryter klienten visas även en `ClientConnectionDisconnected` händelse.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
