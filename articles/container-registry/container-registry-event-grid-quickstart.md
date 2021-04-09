---
title: Snabb start – skicka händelser till Event Grid
description: I den här snabb starten aktiverar du Event Grid händelser för behållar registret och skickar sedan container image push och ta bort händelser till ett exempel program.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: b4038bb232c14278e838f2c34706dcc103def7e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919671"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Snabb start: skicka händelser från privat container Registry till Event Grid

Azure Event Grid är en helt hanterad tjänst för händelse dirigering som tillhandahåller enhetlig händelse förbrukning med en publicerings prenumerations modell. I den här snabb starten använder du Azure CLI för att skapa ett behållar register, prenumererar på register händelser och distribuerar sedan ett exempel webb program för att ta emot händelserna. Slutligen utlöser du behållar avbildning `push` och `delete` händelser och visar händelse nytto lasten i exempel programmet.

När du har slutfört stegen i den här artikeln visas händelser som skickas från behållar registret till Event Grid i exempel-webbappen:

![Webbläsaren återger exempel webb programmet med tre mottagna händelser][sample-app-01]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Azure CLI-kommandona i den här artikeln är formaterade för **bash** -gränssnittet. Om du använder ett annat gränssnitt som PowerShell eller kommando tolken kan du behöva justera rad fortsättnings tecken eller variabla tilldelnings rader i enlighet med detta. I den här artikeln används variabler för att minimera mängden kommando redigering som krävs.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resurs grupp är en logisk behållare där du distribuerar och hanterar dina Azure-resurser. Följande [AZ Group Create][az-group-create] -kommando skapar en resurs grupp med namnet *myResourceGroup* i regionen *östra* . Ange ett annat värde om du vill använda ett annat namn för resurs gruppen `RESOURCE_GROUP_NAME` .

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Sedan distribuerar du ett behållar register till resurs gruppen med följande kommandon. Innan du kör kommandot [AZ ACR Create][az-acr-create] anger `ACR_NAME` du ett namn för registret. Namnet måste vara unikt inom Azure och är begränsat till 5-50 alfanumeriska tecken.

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

Du bör se exempel appen som återges utan att några händelse meddelanden visas:

![En webbläsare som visar ett exempel på en webbapp utan händelser som visas][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Prenumerera på register händelser

I Event Grid prenumererar du på ett *ämne* för att berätta vilka händelser du vill spåra och var de ska skickas. Följande [AZ eventgrid Event-Subscription Create-][az-eventgrid-event-subscription-create] kommandot prenumererar på det behållar register som du skapade och anger webbappens URL som den slut punkt som den ska skicka händelser till. Miljövariablerna som du har fyllt i tidigare avsnitt återanvänds här, så inga ändringar krävs.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

När prenumerationen är klar bör du se utdata som liknar följande:

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

## <a name="trigger-registry-events"></a>Utlös register händelser

Nu när exempel appen är igång och du har prenumererat på ditt register med Event Grid är du redo att generera vissa händelser. I det här avsnittet använder du ACR-uppgifter för att bygga och skicka en behållar avbildning till registret. ACR-aktiviteter är en funktion i Azure Container Registry som gör att du kan bygga behållar avbildningar i molnet, utan att du behöver Docker-motorn installerad på den lokala datorn.

### <a name="build-and-push-image"></a>Bygga och push-avbildning

Kör följande Azure CLI-kommando för att bygga en behållar avbildning från innehållet i en GitHub-lagringsplats. Som standard skickar ACR-aktiviteter automatiskt en korrekt skapad avbildning till registret, vilket genererar `ImagePushed` händelsen.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

Du bör se utdata som liknar följande medan ACR-aktiviteterna skapas och sedan pushar avbildningen. Följande exempel på utdata har trunkerats för det kortfattat.

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

Kontrol lera att den inbyggda avbildningen finns i registret genom att köra följande kommando för att Visa taggarna i databasen "min avbildning":

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

Taggen "v1" för den avbildning som du har skapat ska visas i utdata, ungefär så här:

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>Ta bort avbildningen

Nu ska du generera en `ImageDeleted` händelse genom att ta bort avbildningen med kommandot [AZ ACR databas Delete][az-acr-repository-delete] :

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Du bör se utdata som liknar följande och ber om bekräftelse för att ta bort manifestet och de associerade avbildningarna:

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>Visa register händelser

Nu har du skickat en avbildning till registret och sedan tagit bort den. Navigera till Event Grid Viewer-webbappen och se både `ImageDeleted` och `ImagePushed` . Du kan också se en prenumerations validerings händelse som genererats genom att köra kommandot i avsnittet [Prenumerera på register händelser](#subscribe-to-registry-events) .

Följande skärm bild visar exempel appen med de tre händelserna och `ImageDeleted` händelsen expanderas för att visa dess information.

![En webbläsare som visar exempel appen med ImagePushed-och ImageDeleted-händelser][sample-app-03]

Grattis! Om du ser `ImagePushed` -och- `ImageDeleted` händelserna skickar registret händelser till Event Grid och event Grid vidarebefordrar dessa händelser till webbappens slut punkt.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med de resurser som du skapade i den här snabb starten kan du ta bort dem med följande Azure CLI-kommando. När du tar bort en resurs grupp tas alla resurser som den innehåller bort permanent.

**Varning**! den här åtgärden kan inte ångras. Se till att du inte längre behöver någon av resurserna i gruppen innan du kör kommandot.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Event Grid-händelseschema

Du hittar Azure Container Registry händelse meddelandets schema referens i Event Grid-dokumentationen:

[Azure Event Grid händelse schema för Container Registry](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat ett behållar register, skapat en avbildning med ACR-aktiviteter, tagit bort den och har förbrukat registrets händelser från Event Grid med ett exempel program. Gå sedan till självstudien om ACR tasks och lär dig mer om att skapa behållar avbildningar i molnet, inklusive automatiserade versioner av bas avbildnings uppdatering:

> [!div class="nextstepaction"]
> [Bygg behållar avbildningar i molnet med ACR-uppgifter](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
