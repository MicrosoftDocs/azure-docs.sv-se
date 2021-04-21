---
title: Visa aktivitetskörningsloggar – Uppgifter
description: Visa och hantera körningsloggar som genereras av ACR-uppgifter.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: ce5f33853be2aa48bcfd1916c7f8b94b9702f38c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781063"
---
# <a name="view-and-manage-task-run-logs"></a>Visa och hantera aktivitetskörningsloggar

Varje uppgift som körs [Azure Container Registry genererar](container-registry-tasks-overview.md) loggutdata som du kan granska för att avgöra om aktivitetsstegen har körts. 

Den här artikeln förklarar hur du visar och hanterar aktivitetskörningsloggar.

## <a name="view-streamed-logs"></a>Visa strömmade loggar

När du utlöser en uppgift manuellt strömmas loggutdata direkt till konsolen. När du till exempel utlöser en uppgift manuellt med hjälp av kommandot [az acr build](/cli/azure/acr#az_acr_build), [az acr run](/cli/azure/acr#az_acr_run)eller az [acr task run](/cli/azure/acr/task#az_acr_task_run) kan du se loggutdata strömmas till konsolen. 

Följande exempelkommando [az acr run](/cli/azure/acr#az_acr_run) utlöser manuellt en uppgift som kör en container som hämtas från samma register:

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

Azure Container Registry lagrar körningsloggar för alla uppgifter. Du kan visa lagrade körningsloggar i Azure Portal. Du kan också använda [kommandot az acr task logs](/cli/azure/acr/task#az_acr_task_logs) för att visa en vald logg. Som standard bevaras loggar i 30 dagar.

Om en uppgift utlöses automatiskt, till exempel av en källkodsuppdatering, är åtkomst till lagrade loggar det enda *sättet* att visa körningsloggarna. Automatiska uppgiftsutlösare omfattar källkodsin commits eller pull-begäranden, uppdateringar av basavbildningar och timerutlösare.

Så här visar du körningsloggar i portalen:

1. Gå till containerregistret.
1. I **Tjänster** väljer du **Uppgifter**  >  **kör**.
1. Välj ett **körnings-ID** för att visa körningsstatus och körningsloggar. Loggen innehåller samma information som en strömmad logg om en sådan genereras.

![Visa inloggningsportalen för uppgiftskörning](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Om du vill visa en logg med hjälp av Azure CLI kör du [az acr task logs](/cli/azure/acr/task#az_acr_task_logs) och anger ett körnings-ID, ett aktivitetsnamn eller en specifik avbildning som skapas av en bygguppgift. Om ett aktivitetsnamn anges visar kommandot loggen för den senaste skapade körningen.

I följande exempel visas loggen för körningen med ID *cf4*:

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Alternativ logglagring

Du kanske vill lagra aktivitetskörningsloggar i ett lokalt filsystem eller använda en alternativ arkiveringslösning, till exempel Azure Storage.

Skapa till exempel en lokal *tasklogs-katalog* och omdirigera utdata från [az acr task logs](/cli/azure/acr/task#az_acr_task_logs) till en lokal fil:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

Du kan också spara lokala loggfiler i Azure Storage. Du kan till exempel använda [Azure CLI,](../storage/blobs/storage-quickstart-blobs-cli.md) [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md)eller andra metoder för att ladda upp filer till ett lagringskonto.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Container Registry-uppgifter](container-registry-tasks-overview.md)


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
