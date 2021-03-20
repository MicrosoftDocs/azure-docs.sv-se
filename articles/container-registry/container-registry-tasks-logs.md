---
title: Visa aktivitets körnings loggar – aktiviteter
description: Visa och hantera körnings loggar som genereras av ACR-aktiviteter.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: b2a10d4a3a2746acf38445673af994c6317c77de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93027184"
---
# <a name="view-and-manage-task-run-logs"></a>Visa och hantera aktivitets körnings loggar

Varje aktivitet som körs i [Azure Container Registry uppgifter](container-registry-tasks-overview.md) genererar loggfiler som du kan kontrol lera om aktivitets stegen har körts. 

Den här artikeln förklarar hur du visar och hanterar körnings loggar för aktiviteter.

## <a name="view-streamed-logs"></a>Visa strömmade loggar

När du utlöser en uppgift manuellt strömmas loggens utdata direkt till-konsolen. När du till exempel utlöser en aktivitet manuellt med hjälp av kommandot [AZ ACR build](/cli/azure/acr#az-acr-build), [AZ ACR Run](/cli/azure/acr#az-acr-run)eller [AZ ACR Task Run](/cli/azure/acr/task#az-acr-task-run) , ser du loggen utdata strömmad till-konsolen. 

Följande exempel på [AZ ACR-körning](/cli/azure/acr#az-acr-run) utlöser manuellt en aktivitet som kör en behållare som hämtas från samma register:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Strömmad logg:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>Visa lagrade loggar 

Azure Container Registry lagrar kör loggar för alla aktiviteter. Du kan visa lagrade körnings loggar i Azure Portal. Du kan också använda kommandot [AZ ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) för att visa en vald logg. Som standard behålls loggar i 30 dagar.

Om en aktivitet utlöses automatiskt, till exempel vid en uppdatering av en käll kod, är det *enda* sättet att Visa körnings loggarna genom att använda lagrade loggar. Automatiska aktivitets utlösare inkluderar käll kods incheckningar eller pull-begäranden, bas avbildnings uppdateringar och timer-utlösare.

Så här visar du körnings loggar i portalen:

1. Navigera till behållar registret.
1. I **tjänster** väljer du **aktiviteter**  >  **körs**.
1. Välj ett **körnings-ID** för att Visa körnings status och körnings loggar. Loggen innehåller samma information som en strömmande logg, om en sådan skapas.

![Visa aktivitets körning inloggnings Portal](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Om du vill visa en logg med hjälp av Azure CLI kör du [AZ ACR-aktivitets loggar](/cli/azure/acr/task#az-acr-task-logs) och anger ett körnings-ID, ett uppgifts namn eller en angiven avbildning som skapas av en build-uppgift. Om ett aktivitets namn anges visar kommandot loggen för den senaste skapade körningen.

I följande exempel visas loggen för funktionen kör med ID *CF4*:

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Alternativ logg lagring

Du kanske vill lagra aktivitets körnings loggar i ett lokalt fil system eller använda en alternativ lagrings lösning som Azure Storage.

Du kan till exempel skapa en lokal *tasklogs* -katalog och dirigera om utdata från [AZ ACR aktivitets loggar](/cli/azure/acr/task#az-acr-task-logs) till en lokal fil:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

Du kan också spara lokala loggfiler till Azure Storage. Använd till exempel [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md), [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md)eller andra metoder för att ladda upp filer till ett lagrings konto.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Container Registry uppgifter](container-registry-tasks-overview.md)


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
