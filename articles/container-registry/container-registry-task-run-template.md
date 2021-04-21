---
title: Snabb uppgiftskörning med mall
description: Köa en ACR-uppgiftskörning för att skapa en avbildning Azure Resource Manager en mall
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: af7bebc311f81bb489fcc8be419f167ff6f9460a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781243"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>Köra ACR-uppgifter med Resource Manager mallar

[ACR-uppgifter](container-registry-tasks-overview.md) är en uppsättning funktioner i Azure Container Registry som hjälper dig att hantera och ändra containeravbildningar under hela containerns livscykel. 

Den här artikeln Azure Resource Manager exempelmallen för att köa en snabb uppgiftskörning, ungefär som du kan skapa manuellt med [kommandot az acr build.][az-acr-build]

En Resource Manager mall för att köa en uppgiftskörning är användbar i automatiseringsscenarier och utökar funktionerna i `az acr build` . Exempel:

* Använd en mall för att skapa ett containerregister och omedelbart köa en uppgiftskörning för att skapa och push-skicka en containeravbildning
* Skapa eller aktivera ytterligare resurser som du kan använda i en snabb uppgiftskörning, till exempel en hanterad identitet för Azure-resurser

## <a name="limitations"></a>Begränsningar

* Du måste ange en fjärrkontext, till exempel en GitHub-lagringsplats [som källplats](container-registry-tasks-overview.md#context-locations) för din uppgiftskörning. Du kan inte använda en lokal källkontext.
* För uppgiftskörningar med en hanterad identitet tillåts *endast en användar tilldelad* hanterad identitet.

## <a name="prerequisites"></a>Förutsättningar

* **GitHub-konto** – Skapa ett https://github.com konto på om du inte redan har ett. 
* **Exempeldatabas för förk:** För de uppgiftsexempel som visas här använder du GitHub-användargränssnittet för att förföra följande exempeldatabas till ditt GitHub-konto: https://github.com/Azure-Samples/acr-build-helloworld-node . Den här lagringsplatsen innehåller Dockerfile-exempel och källkod för att skapa små containeravbildningar.

## <a name="example-create-registry-and-queue-task-run"></a>Exempel: Skapa körning av register- och köaktivitet

I det här exemplet används [en exempelmall](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild) för att skapa ett containerregister och köa en uppgiftskörning som skapar och push-erar en avbildning. 

### <a name="template-parameters"></a>Mallparametrar

I det här exemplet anger du värden för följande mallparametrar:

|Parameter  |Värde  |
|---------|---------|
|registryName     |Unikt namn på registret som har skapats         |
|repository     |Måldatabas för build-uppgift        |
|taskRunName     |Namnet på uppgiftskörningen, som anger avbildningstagg |
|sourceLocation     |Fjärrkontext för bygguppgiften, till exempel https://github.com/Azure-Samples/acr-build-helloworld-node . Dockerfile i lagringsplatsens rot skapar en containeravbildning för en liten Node.js webbapp. Om du vill kan du använda din föring av lagringsplatsen som byggkontext.         |

### <a name="deploy-the-template"></a>Distribuera mallen

Distribuera mallen med kommandot [az deployment group][az-deployment-group-create] create. Det här exemplet skapar och push-pushar *avbildningen helloworld-node:testrun* till ett register med namnet *mycontainerregistry*.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuild/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=testrun \
    sourceLocation=https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
 ```

Föregående kommando skickar parametrarna på kommandoraden. Om du vill skickar du dem i en [parameterfil](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Verifiera distributionen

När distributionen är klar kontrollerar du att avbildningen har skapats genom att [köra az acr repository show-tags][az-acr-repository-show-tags]:

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Utdata:

```console
Result
--------
testrun
```

### <a name="view-run-log"></a>Visa körningslogg

Om du vill visa information om aktivitetskörningen kan du visa körningsloggen.

Hämta först körnings-ID:t [med az acr task list-runs][az-acr-task-list-runs]
```azurecli
az acr task list-runs \
  --registry mycontainerregistry --output table
```

Utdata liknar följande:

```console
RUN ID    TASK    PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ------  ----------  ---------  ---------  --------------------  ----------
ca1               linux       Succeeded  Manual     2020-03-23T17:54:28Z  00:00:48
```

Kör [az acr task logs för][az-acr-task-logs] att visa aktivitetskörningsloggar för körnings-ID:t, i det här fallet *ca1*:

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

Utdata visar aktivitetskörningsloggen.

Du kan också visa aktivitetskörningsloggen i Azure Portal. 

1. Navigera till containerregistret
2. Under **Tjänster** väljer du **Uppgifter**  >  **Kör**.
3. Välj körnings-ID, i det här fallet *ca1*. 

Portalen visar aktivitetskörningsloggen.

## <a name="example-task-run-with-managed-identity"></a>Exempel: Uppgiftskörning med hanterad identitet

Använd en [exempelmall för](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity) att köa en uppgiftskörning som möjliggör en användar tilldelad hanterad identitet. Under uppgiftskörningen autentiserar identiteten för att hämta en avbildning från ett annat Azure-containerregister. 

Det här scenariot liknar autentisering [mellan register i en ACR-uppgift med hjälp av en Azure-hanterad identitet](container-registry-tasks-cross-registry-authentication.md). En organisation kan till exempel ha ett centraliserat register med basavbildningar som används av flera utvecklingsteam.

### <a name="prepare-base-registry"></a>Förbereda basregistret

I demonstrationssyfte skapar du ett separat containerregister som basregister och push-Node.js en basavbildning som hämtas från Docker Hub.

1. Skapa ett andra containerregister, till exempel *mybaseregistry*, för att lagra basavbildningar.
1. Hämta `node:9-alpine` avbildningen Docker Hub, tagga den för ditt basregister och skicka den till basregistret:

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>Skapa en ny Dockerfile

Skapa en Dockerfile som hämtar basavbildningen från ditt basregister. Utför följande steg i din lokala föring av GitHub-lagringsplatsen, till exempel `https://github.com/myGitHubID/acr-build-helloworld-node.git` .

1. I GitHub-användargränssnittet väljer du **Skapa ny fil.**
1. Ge filen namnet *Dockerfile-test* och klistra in följande innehåll. Ersätt ditt registernamn med *mybaseregistry*.
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. Välj **Commit new file (Genomför ny fil).**

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Ge identiteter pull-behörigheter till basregistret

Ge den hanterade identiteten behörighet att hämta från *basregistret, mybaseregistry*.

Använd kommandot [az acr show][az-acr-show] för att hämta resurs-ID:t för basregistret och lagra det i en variabel:

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

Använd kommandot [az role assignment create][az-role-assignment-create] för att tilldela identiteten acrpull-rollen till basregistret. Den här rollen har endast behörighet att hämta avbildningar från registret.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>Mallparametrar

I det här exemplet anger du värden för följande mallparametrar:

|Parameter  |Värde  |
|---------|---------|
|registryName     |Namnet på registret där avbildningen har skapats  |
|repository     |Måldatabas för build-uppgift        |
|taskRunName     |Namnet på aktivitetskörningen, som anger avbildningstagg |
|userAssignedIdentity |Resurs-ID för användar tilldelad identitet aktiverad i uppgiften|
|customRegistryIdentity | Klient-ID för användartilldeerad identitet som aktiverats i uppgiften, som används för att autentisera med anpassat register |
|customRegistry |Inloggningsservernamnet för det anpassade registret som används i uppgiften, till exempel *mybaseregistry.azurecr.io*|
|sourceLocation     |Fjärrkontext för build-uppgiften, till exempel *https://github.com/ \<your-GitHub-ID\> /acr-build-helloworld-node.* |
|dockerFilePath | Sökväg till Dockerfile i fjärrkontexten som används för att skapa avbildningen. |

### <a name="deploy-the-template"></a>Distribuera mallen

Distribuera mallen med kommandot [az deployment group][az-deployment-group-create] create. Det här exemplet skapar och push-pushar *avbildningen helloworld-node:testrun* till ett register med namnet *mycontainerregistry*. Basavbildningen hämtas från *mybaseregistry.azurecr.io*.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=basetask \
    userAssignedIdentity=$resourceID \
    customRegistryIdentity=$clientID \
    sourceLocation=https://github.com/<your-GitHub-ID>/acr-build-helloworld-node.git#main \
    dockerFilePath=Dockerfile-test \
    customRegistry=mybaseregistry.azurecr.io
```

Föregående kommando skickar parametrarna på kommandoraden. Om du vill skickar du dem i en [parameterfil](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Verifiera distributionen

När distributionen är klar kontrollerar du att avbildningen har skapats genom att [köra az acr repository show-tags][az-acr-repository-show-tags]:

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Utdata:

```console
Result
--------
basetask
```

### <a name="view-run-log"></a>Visa körningslogg

Om du vill visa körningsloggen går du till [stegen i föregående avsnitt.](#view-run-log)

## <a name="next-steps"></a>Nästa steg

 * Se fler mallexempel på [ACR GitHub-lagringsplatsen](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment).
 * Mer information om mallegenskaper finns i mallreferensen för [uppgiftskörningar](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) och [uppgifter.](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks)


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-logs]: /cli/azure/acr/task#az_acr_task_logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
