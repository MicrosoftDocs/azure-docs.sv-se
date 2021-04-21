---
title: Så här skickar du Azure SignalR Service-händelser till Event Grid
description: En guide som visar hur du aktiverar Event Grid-händelser för dina SignalR Service och sedan skickar anslutna/frånkopplade klientanslutningshändelser till ett exempelprogram.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: 65fb54dbfc5158ef8cc2b488f267c92f601502f6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784613"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Skicka händelser från Azure SignalR Service till Event Grid

Azure Event Grid är en fullständigt hanterad tjänst för händelsedirigering som tillhandahåller enhetlig händelseförbrukning med hjälp av en pub-sub-modell. I den här guiden använder du Azure CLI för att skapa en Azure SignalR Service, prenumerera på anslutningshändelser och sedan distribuera ett exempelwebbprogram för att ta emot händelserna. Slutligen kan du ansluta och koppla från och se händelsenyttolasten i exempelprogrammet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Azure CLI-kommandona i den här artikeln är formaterade för **Bash-gränssnittet.** Om du använder ett annat gränssnitt som PowerShell eller kommandotolken kan du behöva justera rad fortsättningstecken eller variabeltilldelningsrader därefter. Den här artikeln använder variabler för att minimera mängden kommandoredigering som krävs.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där du distribuerar och hanterar dina Azure-resurser. Följande [az group create-kommando][az-group-create] skapar en resursgrupp med *namnet myResourceGroup* i *regionen eastus.* Om du vill använda ett annat namn för resursgruppen anger du `RESOURCE_GROUP_NAME` till ett annat värde.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Skapa en Azure SignalR Service

Distribuera sedan en Azure Signalr Service till resursgruppen med följande kommandon.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

När SignalR Service har skapats returnerar Azure CLI utdata som liknar följande:

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

## <a name="create-an-event-endpoint"></a>Skapa en händelseslutpunkt

I det här avsnittet använder du en Resource Manager som finns på en GitHub-lagringsplats för att distribuera en förbyggd exempelwebbapp till Azure App Service. Senare prenumererar du på registrets Event Grid händelser och anger den här appen som slutpunkten som händelserna skickas till.

Om du vill distribuera exempelappen `SITE_NAME` anger du ett unikt namn för webbappen och kör följande kommandon. Platsnamnet måste vara unikt i Azure eftersom det utgör en del av webbappens fullständigt kvalificerade domännamn (FQDN). I ett senare avsnitt går du till appens FQDN i en webbläsare för att visa registrets händelser.

```azurecli-interactive
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

När distributionen har lyckats (det kan ta några minuter) öppnar du en webbläsare och går till webbappen för att kontrollera att den körs:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Prenumerera på registerhändelser

I Event Grid prenumererar du på ett *ämne för* att berätta vilka händelser du vill spåra och var de ska skickas. Följande [az eventgrid event-subscription create-kommando][az-eventgrid-event-subscription-create] prenumererar på den Azure SignalR Service som du skapade och anger webbappens URL som den slutpunkt som den ska skicka händelser till. Miljövariablerna som du fylla i i tidigare avsnitt återanvänds här, så inga ändringar krävs.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

När prenumerationen har slutförts bör du se utdata som liknar följande:

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

## <a name="trigger-registry-events"></a>Utlösa registerhändelser

Växla till tjänstläget till `Serverless Mode` och konfigurera en klientanslutning till SignalR Service. Du kan använda [serverlöst exempel](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) som referens.

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

## <a name="view-registry-events"></a>Visa registerhändelser

Nu har du anslutit en klient till SignalR Service. Gå till webbappen Event Grid Viewer så bör du se en `ClientConnectionConnected` händelse. Om du avslutar klienten visas även en `ClientConnectionDisconnected` händelse.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create
[az-group-create]: /cli/azure/group#az_group_create
