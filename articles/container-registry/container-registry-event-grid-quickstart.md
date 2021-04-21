---
title: Snabbstart – Skicka händelser till Event Grid
description: I den här snabbstarten aktiverar du Event Grid händelser för containerregistret och skickar sedan push-meddelanden och borttagningshändelser för containeravbildningar till ett exempelprogram.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 43dea2640c9c9445ea464205f6c586bc1e486206
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784033"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Snabbstart: Skicka händelser från privat containerregister till Event Grid

Azure Event Grid är en fullständigt hanterad tjänst för händelsedirigering som tillhandahåller enhetlig händelseförbrukning med hjälp av en publicera-prenumerera-modell. I den här snabbstarten använder du Azure CLI för att skapa ett containerregister, prenumerera på registerhändelser och sedan distribuera ett exempelwebbprogram för att ta emot händelserna. Slutligen utlöser du `push` containeravbildning och händelser och visar `delete` händelsenyttolasten i exempelprogrammet.

När du har slutfört stegen i den här artikeln visas händelser som skickas från ditt containerregister Event Grid visas i exempelwebbappen:

![Webbläsare som återger exempelwebbappen med tre mottagna händelser][sample-app-01]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Azure CLI-kommandona i den här artikeln är formaterade för **Bash-gränssnittet.** Om du använder ett annat gränssnitt som PowerShell eller kommandotolken kan du behöva justera rad fortsättningstecken eller variabeltilldelningsrader därefter. Den här artikeln använder variabler för att minimera mängden kommandoredigering som krävs.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där du distribuerar och hanterar dina Azure-resurser. Följande [az group create-kommando][az-group-create] skapar en resursgrupp med *namnet myResourceGroup* i *regionen eastus.* Om du vill använda ett annat namn för resursgruppen anger du `RESOURCE_GROUP_NAME` till ett annat värde.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Distribuera sedan ett containerregister till resursgruppen med följande kommandon. Innan du kör kommandot [az acr create][az-acr-create] anger `ACR_NAME` du till ett namn för registret. Namnet måste vara unikt i Azure och är begränsat till 5–50 alfanumeriska tecken.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

När registret har skapats returnerar Azure CLI utdata som liknar följande:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-08-16T20:02:46.569509+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```

## <a name="create-an-event-endpoint"></a>Skapa en händelseslutpunkt

I det här avsnittet använder du en Resource Manager som finns på en GitHub-lagringsplats för att distribuera ett förbyggt exempelwebbprogram till Azure App Service. Senare prenumererar du på registrets Event Grid och anger den här appen som slutpunkten som händelserna skickas till.

Om du vill distribuera exempelappen `SITE_NAME` anger du ett unikt namn för webbappen och kör följande kommandon. Webbplatsnamnet måste vara unikt i Azure eftersom det utgör en del av det fullständigt kvalificerade domännamnet (FQDN) för webbappen. I ett senare avsnitt går du till appens FQDN i en webbläsare för att visa registrets händelser.

```azurecli-interactive
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

När distributionen har lyckats (det kan ta några minuter) öppnar du en webbläsare och går till webbappen för att kontrollera att den körs:

`http://<your-site-name>.azurewebsites.net`

Du bör se exempelappen renderad utan att några händelsemeddelanden visas:

![Webbläsare som visar exempelwebbapp utan att händelser visas][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Prenumerera på registerhändelser

I Event Grid prenumererar du på ett *ämne för* att berätta vilka händelser du vill spåra och var de ska skickas. Följande [az eventgrid event-subscription create-kommando][az-eventgrid-event-subscription-create] prenumererar på det containerregister som du skapade och anger webbappens URL som den slutpunkt som den ska skicka händelser till. Miljövariablerna som du fylla i i tidigare avsnitt återanvänds här, så inga ändringar krävs.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

När prenumerationen har slutförts bör du se utdata som liknar följande:

```json
{
  "destination": {
    "endpointBaseUrl": "https://eventgridviewer.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "All"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-acr",
  "labels": null,
  "name": "event-sub-acr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "topic": "/subscriptions/<Subscription ID>/resourceGroups/myresourcegroup/providers/microsoft.containerregistry/registries/myregistry",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Utlösa registerhändelser

Nu när exempelappen är igång och du prenumererar på ditt register med Event Grid är du redo att generera några händelser. I det här avsnittet använder du ACR-uppgifter för att skapa och push-skicka en containeravbildning till registret. ACR-uppgifter är en funktion i Azure Container Registry som gör att du kan skapa containeravbildningar i molnet utan att dockermotorn behöver installeras på den lokala datorn.

### <a name="build-and-push-image"></a>Skapa och push-push-avbildning

Kör följande Azure CLI-kommando för att skapa en containeravbildning från innehållet på en GitHub-lagringsplats. Som standard ACR-uppgifter automatiskt en skapad avbildning till registret, vilket genererar `ImagePushed` händelsen.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

Du bör se utdata som liknar följande när ACR-uppgifter skapar och sedan push-erar avbildningen. Följande exempel på utdata har trunkerats för korthet.

```output
Sending build context to ACR...
Queued a build with build ID: aa2
Waiting for build agent...
2018/08/16 22:19:38 Using acb_vol_27a2afa6-27dc-4ae4-9e52-6d6c8b7455b2 as the home volume
2018/08/16 22:19:38 Setting up Docker configuration...
2018/08/16 22:19:39 Successfully set up Docker configuration
2018/08/16 22:19:39 Logging in to registry: myregistry.azurecr.io
2018/08/16 22:19:55 Successfully logged in
Sending build context to Docker daemon  94.72kB
Step 1/5 : FROM node:9-alpine
...
```

Kontrollera att den skapade avbildningen finns i registret genom att köra följande kommando för att visa taggarna på lagringsplatsen "myimage":

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

Taggen "v1" för den avbildning som du skapade bör visas i utdata, ungefär så här:

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>Ta bort avbildningen

Generera nu en händelse `ImageDeleted` genom att ta bort avbildningen med kommandot az [acr repository delete:][az-acr-repository-delete]

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Du bör se utdata som liknar följande och be om bekräftelse för att ta bort manifestet och tillhörande avbildningar:

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>Visa registerhändelser

Nu har du push-push-lagt till en avbildning i registret och sedan tagit bort den. Gå till webbappen Event Grid Viewer så bör du se både `ImageDeleted` - och `ImagePushed` -händelser. Du kan också se en händelse för prenumerationsverifiering som genereras genom att köra kommandot i [avsnittet Prenumerera på registerhändelser.](#subscribe-to-registry-events)

Följande skärmbild visar exempelappen med de tre händelserna och händelsen `ImageDeleted` expanderas för att visa dess information.

![Webbläsare som visar exempelappen med ImagePushed- och ImageDeleted-händelser][sample-app-03]

Grattis! Om du ser händelserna och skickar ditt register händelser till Event Grid och Event Grid vidarebefordrar dessa händelser till `ImagePushed` `ImageDeleted` webbappens slutpunkt.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med de resurser som du skapade i den här snabbstarten kan du ta bort dem med följande Azure CLI-kommando. När du tar bort en resursgrupp tas alla resurser som den innehåller bort permanent.

**VARNING!** Den här åtgärden går inte att ångra. Se till att du inte längre behöver någon av resurserna i gruppen innan du kör kommandot.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Event Grid-händelseschema

Du hittar schemareferensen Azure Container Registry händelsemeddelanden i Event Grid dokumentationen:

[Azure Event Grid händelseschema för Container Registry](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du ett containerregister, skapade en avbildning med ACR-uppgifter, tog bort den och har förbrukat registrets händelser från Event Grid med ett exempelprogram. Gå sedan vidare till självstudien ACR-uppgifter för att lära dig mer om att skapa containeravbildningar i molnet, inklusive automatiserade byggen vid uppdatering av basavbildningar:

> [!div class="nextstepaction"]
> [Skapa containeravbildningar i molnet med ACR-uppgifter](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create
[az-group-create]: /cli/azure/group#az_group_create
