---
title: Autentisering mellan register från ACR-aktivitet
description: Konfigurera en Azure Container Registry aktivitet (ACR Task) för att få åtkomst till ett annat privat Azure Container Registry med hjälp av en hanterad identitet för Azure-resurser
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 0e8e2690113167ad68ef1fc0bbef322491997c76
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251156"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Autentisering mellan register i en ACR-aktivitet med hjälp av en Azure-hanterad identitet 

I en [ACR-uppgift](container-registry-tasks-overview.md)kan du [Aktivera en hanterad identitet för Azure-resurser](container-registry-tasks-authentication-managed-identity.md). Uppgiften kan använda identiteten för att få åtkomst till andra Azure-resurser, utan att behöva ange eller hantera autentiseringsuppgifter. 

I den här artikeln får du lära dig hur du aktiverar en hanterad identitet i en uppgift för att hämta en avbildning från ett register som skiljer sig från det som används för att köra uppgiften.

Den här artikeln kräver att du kör Azure CLI-version 2.0.68 eller senare för att skapa Azure-resurser. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

## <a name="scenario-overview"></a>Översikt över scenario

Exempel aktiviteten hämtar en bas avbildning från ett annat Azure Container Registry för att bygga och skicka en program avbildning. Om du vill hämta bas avbildningen konfigurerar du aktiviteten med en hanterad identitet och tilldelar den behörigheten. 

Det här exemplet visar steg som använder antingen en användardefinierad eller systemtilldelad hanterad identitet. Ditt val av identitet beror på organisationens behov.

I ett verkligt scenario kan en organisation underhålla en uppsättning grundläggande avbildningar som används av alla utvecklings grupper för att bygga sina program. De här bas avbildningarna lagras i ett företags register, där varje utvecklings team har bara pull-rättigheter. 

## <a name="prerequisites"></a>Förutsättningar

I den här artikeln behöver du två Azure Container-register:

* Du använder det första registret för att skapa och köra ACR-uppgifter. I den här *artikeln kallas registret för registret.* 
* Det andra registret är värd för en bas avbildning som används för aktiviteten för att bygga en avbildning. I den här artikeln heter det andra registret *mybaseregistry*. 

Ersätt med dina egna register namn i senare steg.

Om du inte redan har de nödvändiga Azure Container register, se [snabb start: skapa ett privat behållar register med hjälp av Azure CLI](container-registry-get-started-azure-cli.md). Du behöver inte skicka avbildningar till registret ännu.

## <a name="prepare-base-registry"></a>Förbered grundläggande register

I demonstrations syfte kan du som en engångs åtgärd köra [AZ ACR import] [AZ-ACR-import] för att importera en offentlig Node.js-avbildning från Docker Hub till ditt bas register. I praktiken kan ett annat team eller en process i organisationen underhålla avbildningar i bas registret.

```azurecli
az acr import --name mybaseregistry \
  --source docker.io/library/node:15-alpine \
  --image baseimages/node:15-alpine 
```

## <a name="define-task-steps-in-yaml-file"></a>Definiera uppgifts steg i YAML-filen

Stegen för det här exemplet för [flera steg](container-registry-tasks-multi-step.md) definieras i en yaml- [fil](container-registry-tasks-reference-yaml.md). Skapa en fil med namnet `helloworldtask.yaml` i din lokala arbets katalog och klistra in följande innehåll. Uppdatera värdet för `REGISTRY_NAME` i build-steget med Server namnet för bas registret.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

I build-steget används `Dockerfile-app` filen i lagrings platsen [Azure-samples/ACR-build-HelloWorld-Node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) för att bygga en avbildning. `--build-arg`Refererar till bas registret för att hämta bas avbildningen. När den har skapats skickas avbildningen till registret som används för att köra uppgiften.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Alternativ 1: Skapa uppgift med användardefinierad identitet

Stegen i det här avsnittet skapar en uppgift och aktiverar en användardefinierad identitet. Om du vill aktivera en tilldelad identitet i stället, se [Alternativ 2: Skapa uppgift med systemtilldelad identitet](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Skapa uppgift

Skapa uppgiften *helloworldtask* genom att köra följande kommando för [AZ ACR Task Create][az-acr-task-create] . Aktiviteten körs utan en käll kods kontext och kommandot refererar till filen `helloworldtask.yaml` i arbets katalogen. `--assign-identity`Parametern skickar resurs-ID: t för den tilldelade identiteten. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Ge Identity pull-behörighet till bas registret

I det här avsnittet ger du hanterade identitets behörigheter att hämta från bas registret, *mybaseregistry*.

Använd kommandot [AZ ACR show][az-acr-show] för att hämta resurs-ID för bas registret och lagra det i en variabel:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Använd kommandot [AZ roll tilldelning skapa][az-role-assignment-create] för att tilldela identiteten `acrpull` rollen till bas registret. Den här rollen har endast behörigheter för att hämta avbildningar från registret.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

Fortsätt att [lägga till autentiseringsuppgifter för mål registret i aktiviteten](#add-target-registry-credentials-to-task).

## <a name="option-2-create-task-with-system-assigned-identity"></a>Alternativ 2: Skapa uppgift med systemtilldelad identitet

Stegen i det här avsnittet skapar en uppgift och aktiverar en tilldelad identitet. Om du vill aktivera en användardefinierad identitet i stället, se [alternativ 1: Skapa uppgift med användardefinierad identitet](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Skapa uppgift

Skapa uppgiften *helloworldtask* genom att köra följande kommando för [AZ ACR Task Create][az-acr-task-create] . Aktiviteten körs utan en käll kods kontext och kommandot refererar till filen `helloworldtask.yaml` i arbets katalogen. `--assign-identity`Parametern utan värde aktiverar den systemtilldelade identiteten för uppgiften. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Ge Identity pull-behörighet till bas registret

I det här avsnittet ger du hanterade identitets behörigheter att hämta från bas registret, *mybaseregistry*.

Använd kommandot [AZ ACR show][az-acr-show] för att hämta resurs-ID för bas registret och lagra det i en variabel:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Använd kommandot [AZ roll tilldelning skapa][az-role-assignment-create] för att tilldela identiteten `acrpull` rollen till bas registret. Den här rollen har endast behörigheter för att hämta avbildningar från registret.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Lägg till autentiseringsuppgifter för mål registret i uppgiften

Använd nu kommandot [AZ ACR Task Credential Add][az-acr-task-credential-add] för att aktivera uppgiften att autentisera med bas registret med identitetens autentiseringsuppgifter. Kör kommandot som motsvarar den typ av hanterade identitet som du aktiverade i uppgiften. Om du har aktiverat en användardefinierad identitet skickar du `--use-identity` med klient-ID: t för identiteten. Om du har aktiverat en systemtilldelad identitet skickar du `--use-identity [system]` .

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

## <a name="manually-run-the-task"></a>Kör uppgiften manuellt

Om du vill kontrol lera att den aktivitet där du har aktiverat en hanterad identitet har körts manuellt utlöser du uppgiften med kommandot [AZ ACR Task Run][az-acr-task-run] . 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Om aktiviteten körs ser utdata ut ungefär så här:

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

Kör kommandot [AZ ACR-lagringsplatsen show-Tags][az-acr-repository-show-tags] för att kontrol lera att avbildningen har skapats och skickats till *registret*:

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Exempel på utdata:

```console
cf10
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du [aktiverar en hanterad identitet i en ACR-aktivitet](container-registry-tasks-authentication-managed-identity.md).
* Se [yaml-referensen ACR tasks](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
