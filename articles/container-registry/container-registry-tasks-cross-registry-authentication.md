---
title: Autentisering mellan register från ACR-uppgift
description: Konfigurera en Azure Container Registry task (ACR Task) för att få åtkomst till ett annat privat Azure-containerregister med hjälp av en hanterad identitet för Azure-resurser
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: a9b70a44de0cfccb9a61bc24575281e440db6e32
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781132"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Autentisering mellan register i en ACR-uppgift med hjälp av en Azure-hanterad identitet 

I en [ACR-uppgift](container-registry-tasks-overview.md)kan du aktivera [en hanterad identitet för Azure-resurser.](container-registry-tasks-authentication-managed-identity.md) Uppgiften kan använda identiteten för att få åtkomst till andra Azure-resurser, utan att behöva ange eller hantera autentiseringsuppgifter. 

I den här artikeln får du lära dig hur du aktiverar en hanterad identitet i en uppgift för att hämta en avbildning från ett annat register än det som användes för att köra uppgiften.

För att skapa Azure-resurser kräver den här artikeln att du kör Azure CLI version 2.0.68 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

## <a name="scenario-overview"></a>Översikt över scenario

Exempelaktiviteten hämtar en basavbildning från ett annat Azure-containerregister för att skapa och push-skicka en programavbildning. Om du vill hämta basavbildningen konfigurerar du uppgiften med en hanterad identitet och tilldelar lämplig behörighet till den. 

Det här exemplet visar steg med hjälp av antingen en användar-tilldelad eller system tilldelad hanterad identitet. Ditt val av identitet beror på organisationens behov.

I ett verkligt scenario kan en organisation ha en uppsättning basavbildningar som används av alla utvecklingsteam för att skapa sina program. Dessa basavbildningar lagras i ett företagsregister, där varje utvecklingsteam endast har pull-rättigheter. 

## <a name="prerequisites"></a>Förutsättningar

För den här artikeln behöver du två Azure-containerregister:

* Du använder det första registret för att skapa och köra ACR-uppgifter. I den här artikeln heter det här registret *myregistry*. 
* Det andra registret är värd för en basavbildning som används för uppgiften att skapa en avbildning. I den här artikeln heter det andra registret *mybaseregistry*. 

Ersätt med dina egna registernamn i senare steg.

Om du inte redan har de Azure-containerregister som behövs kan du gå till [Snabbstart: Skapa ett privat containerregister med hjälp av Azure CLI.](container-registry-get-started-azure-cli.md) Du behöver inte push-skicka avbildningar till registret ännu.

## <a name="prepare-base-registry"></a>Förbereda basregistret

I demonstrationssyfte kör du som en enda åtgärd [az acr import][az-acr-import] för att importera en offentlig Node.js-avbildning från Docker Hub till ditt basregister. I praktiken kan ett annat team eller en annan process i organisationen underhålla avbildningar i basregistret.

```azurecli
az acr import --name mybaseregistry \
  --source docker.io/library/node:15-alpine \
  --image baseimages/node:15-alpine 
```

## <a name="define-task-steps-in-yaml-file"></a>Definiera uppgiftssteg i YAML-fil

Stegen för den här [exempeluppgiften med flera steg](container-registry-tasks-multi-step.md) definieras i en [YAML-fil](container-registry-tasks-reference-yaml.md). Skapa en fil med `helloworldtask.yaml` namnet i din lokala arbetskatalog och klistra in följande innehåll. Uppdatera värdet för `REGISTRY_NAME` i build-steget med servernamnet för ditt basregister.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git#main -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

Build-steget använder filen `Dockerfile-app` i [lagringsplatsen Azure-Samples/acr-build-helloworld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) för att skapa en avbildning. Refererar `--build-arg` till basregistret för att hämta basavbildningen. När avbildningen har skapats push-skickas den till det register som används för att köra uppgiften.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Alternativ 1: Skapa uppgift med användar tilldelad identitet

Stegen i det här avsnittet skapar en uppgift och aktiverar en användar tilldelad identitet. Om du vill aktivera en system tilldelad identitet i stället kan du se [Alternativ 2: Skapa uppgift med system tilldelad identitet](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Skapa uppgift

Skapa uppgiften *helloworldtask genom* att köra följande [az acr task create-kommando.][az-acr-task-create] Aktiviteten körs utan källkodskontext och kommandot refererar till filen `helloworldtask.yaml` i arbetskatalogen. Parametern `--assign-identity` skickar resurs-ID:t för den användar tilldelade identiteten. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Ge identiteter pull-behörigheter till basregistret

I det här avsnittet ger du den hanterade identiteten behörighet att hämta från *basregistret, mybaseregistry*.

Använd kommandot [az acr show][az-acr-show] för att hämta resurs-ID:t för basregistret och lagra det i en variabel:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Använd kommandot [az role assignment create][az-role-assignment-create] för att tilldela den identitet som rollen till `acrpull` basregistret. Den här rollen har endast behörighet att hämta avbildningar från registret.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

Fortsätt till Lägg [till autentiseringsuppgifter för målregistret i uppgiften](#add-target-registry-credentials-to-task).

## <a name="option-2-create-task-with-system-assigned-identity"></a>Alternativ 2: Skapa uppgift med system tilldelad identitet

Stegen i det här avsnittet skapar en uppgift och aktiverar en system tilldelad identitet. Om du vill aktivera en användar tilldelad identitet i stället kan du se [Alternativ 1: Skapa uppgift med användar tilldelad identitet.](#option-1-create-task-with-user-assigned-identity) 

### <a name="create-task"></a>Skapa uppgift

Skapa uppgiften *helloworldtask genom* att köra följande [az acr task create-kommando.][az-acr-task-create] Aktiviteten körs utan källkodskontext och kommandot refererar till filen `helloworldtask.yaml` i arbetskatalogen. Parametern `--assign-identity` utan värde aktiverar den system tilldelade identiteten för uppgiften. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Ge identiteter pull-behörigheter till basregistret

I det här avsnittet ger du den hanterade identiteten behörighet att hämta från *basregistret, mybaseregistry*.

Använd kommandot [az acr show][az-acr-show] för att hämta resurs-ID:t för basregistret och lagra det i en variabel:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Använd kommandot [az role assignment create][az-role-assignment-create] för att tilldela den identitet som rollen till `acrpull` basregistret. Den här rollen har endast behörighet att hämta avbildningar från registret.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Lägga till autentiseringsuppgifter för målregistret i en uppgift

Använd nu kommandot [az acr task credential add][az-acr-task-credential-add] för att göra det möjligt för uppgiften att autentisera med basregistret med identitetens autentiseringsuppgifter. Kör kommandot som motsvarar den typ av hanterad identitet som du aktiverade i uppgiften. Om du har aktiverat en användar tilldelad identitet skickar `--use-identity` du med klient-ID:t för identiteten. Om du har aktiverat en system tilldelad identitet skickar du `--use-identity [system]` .

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>Köra uppgiften manuellt

Om du vill kontrollera att aktiviteten där du har aktiverat en hanterad identitet körs manuellt utlöser du uppgiften med [kommandot az acr task run.][az-acr-task-run] 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Om aktiviteten körs korrekt ser utdata ut ungefär så här:

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
15-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 15-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

Kör kommandot [az acr repository show-tags][az-acr-repository-show-tags] för att verifiera att avbildningen har skapats och push-skickats till *myregistry*:

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Exempel på utdata:

```console
cf10
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om [hur du aktiverar en hanterad identitet i en ACR-uppgift.](container-registry-tasks-authentication-managed-identity.md)
* Se [ACR-uppgifter YAML-referens](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[az-group-create]: /cli/azure/group?#az_group_create
