---
title: Snabb körning av uppgift med mall
description: Köa en ACR-uppgift köra för att skapa en avbildning med hjälp av en Azure Resource Manager mall
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: 6e8023c088ac328c2b6e95fccd0230c4d40325c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98916073"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>Köra ACR-uppgifter med Resource Manager-mallar

[ACR-aktiviteter](container-registry-tasks-overview.md) är en uppsättning funktioner i Azure Container Registry som hjälper dig att hantera och ändra behållar avbildningar över livs cykeln för behållare. 

Den här artikeln visar Azure Resource Manager mall-exempel för att köa en snabb uppgifts körning, som liknar en som du kan skapa manuellt med kommandot [AZ ACR build][az-acr-build] .

En Resource Manager-mall för att köa en uppgifts körning är användbar i automatiserings scenarier och utökar funktionerna i `az acr build` . Exempel:

* Använd en mall för att skapa ett behållar register och omedelbart köa en aktivitets körning för att skapa och skicka en behållar avbildning
* Skapa eller aktivera ytterligare resurser som du kan använda i en snabb uppgifts körning, till exempel en hanterad identitet för Azure-resurser

## <a name="limitations"></a>Begränsningar

* Du måste ange en fjärran sluten kontext, till exempel en GitHub-lagrings platsen som [käll plats](container-registry-tasks-overview.md#context-locations) för aktivitets körningen. Du kan inte använda en lokal käll kontext.
* För uppgifter som körs med hjälp av en hanterad identitet tillåts endast en hanterad identitet som *tilldelats av användare* .

## <a name="prerequisites"></a>Förutsättningar

* **GitHub-konto** – skapa ett konto på https://github.com om du inte redan har ett. 
* **Exempel databas för förgrening** – för de uppgifts exempel som visas här använder du GITHUB-gränssnittet för att förgrena följande exempel lager till ditt GitHub-konto: https://github.com/Azure-Samples/acr-build-helloworld-node . Den här lagrings platsen innehåller exempel-Dockerfiles och käll kod för att bygga små behållar avbildningar.

## <a name="example-create-registry-and-queue-task-run"></a>Exempel: Skapa registret och köa uppgifts körning

I det här exemplet används en [exempel-mall](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild) för att skapa ett behållar register och köa en aktivitets körning som skapar och push-överför en avbildning. 

### <a name="template-parameters"></a>Mallparametrar

I det här exemplet anger du värden för följande mallparametrar:

|Parameter  |Värde  |
|---------|---------|
|registryName     |Unikt namn på register som skapas         |
|repository     |Mål databas för bygg uppgift        |
|taskRunName     |Namnet på uppgifts körningen, som anger en bildtagg |
|sourceLocation     |Fjärrkontext för build-uppgiften, till exempel https://github.com/Azure-Samples/acr-build-helloworld-node . Dockerfile i lagrings platsen-roten skapar en behållar avbildning för en liten Node.js-webbapp. Om du vill kan du använda din förgrening av lagrings platsen som bygg kontext.         |

### <a name="deploy-the-template"></a>Distribuera mallen

Distribuera mallen med kommandot [AZ Deployment Group Create][az-deployment-group-create] . I det här exemplet skapas och pushar avbildningen *HelloWorld-node: testrun* till ett register med namnet *mycontainerregistry*.

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

Föregående kommando skickar parametrarna på kommando raden. Om du vill kan du skicka dem i en [parameter fil](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Verifiera distributionen

När distributionen har slutförts kontrollerar du att avbildningen har skapats genom att köra [AZ ACR-lagringsplats show-Tags][az-acr-repository-show-tags]:

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

### <a name="view-run-log"></a>Visa körnings logg

Visa körnings loggen om du vill visa information om aktivitets körningen.

Börja med att hämta uppgifts listan för körnings-ID med [AZ ACR-körningar][az-acr-task-list-runs]
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

Kör [AZ-ACR][az-acr-task-logs] för att Visa aktivitets körnings loggar för körnings-ID: t, i det här fallet *CA1*:

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

Utdata visar loggen för aktivitets körning.

Du kan också Visa aktivitets körnings loggen i Azure Portal. 

1. Navigera till behållar registret
2. Under **tjänster** väljer du **aktiviteter**  >  **körs**.
3. Välj körnings-ID, i det här fallet *CA1*. 

Portalen visar körnings loggen för aktiviteten.

## <a name="example-task-run-with-managed-identity"></a>Exempel: aktivitets körning med hanterad identitet

Använd en [exempel mall](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity) för att köa en uppgifts körning som aktiverar en användardefinierad hanterad identitet. Under uppgifts körningen autentiserar identiteten att hämta en avbildning från ett annat Azure Container Registry. 

Det här scenariot liknar [autentisering mellan register i en ACR-aktivitet med hjälp av en Azure-hanterad identitet](container-registry-tasks-cross-registry-authentication.md). En organisation kan till exempel ha ett centraliserat register med bas avbildningar som används av flera utvecklings team.

### <a name="prepare-base-registry"></a>Förbered grundläggande register

I demonstrations syfte skapar du ett separat behållar register som bas register och skickar en Node.js bas avbildning som hämtas från Docker Hub.

1. Skapa ett andra behållar register, till exempel *mybaseregistry*, för att lagra bas avbildningar.
1. Hämta `node:9-alpine` avbildningen från Docker Hub, tagga den för ditt bas register och skicka den till bas registret:

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>Skapa ny Dockerfile

Skapa en Dockerfile som hämtar bas avbildningen från bas registret. Utför följande steg i din lokala förgrening av GitHub-lagrings platsen, till exempel `https://github.com/myGitHubID/acr-build-helloworld-node.git` .

1. I GitHub-ANVÄNDARGRÄNSSNITTET väljer du **Skapa ny fil**.
1. Ge filen namnet *Dockerfile-test* och klistra in följande innehåll. Ersätt ditt register namn för *mybaseregistry*.
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. Välj **genomför ny fil**.

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Ge Identity pull-behörighet till bas registret

Ge hanterade identitets behörigheter för att hämta från bas registret, *mybaseregistry*.

Använd kommandot [AZ ACR show][az-acr-show] för att hämta resurs-ID för bas registret och lagra det i en variabel:

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

Använd kommandot [AZ roll tilldelning skapa][az-role-assignment-create] för att tilldela identiteten rollen Acrpull till bas registret. Den här rollen har endast behörigheter för att hämta avbildningar från registret.

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
|registryName     |Namn på registret där avbildningen skapas  |
|repository     |Mål databas för bygg uppgift        |
|taskRunName     |Namnet på uppgifts körningen, som anger en bildtagg |
|userAssignedIdentity |Resurs-ID för användardefinierad identitet som har Aktiver ATS i uppgiften|
|customRegistryIdentity | Klient-ID för användardefinierad identitet som är aktive rad i uppgiften och som används för att autentisera med anpassat register |
|customRegistry |Inloggnings Server namnet för det anpassade registret som används i uppgiften, till exempel *mybaseregistry.azurecr.io*|
|sourceLocation     |Fjärran sluten kontext för build-uppgiften, till exempel *https://github.com/ \<your-GitHub-ID\> /ACR-build-HelloWorld-Node.* |
|dockerFilePath | Sökväg till Dockerfile i fjärrkontexten som används för att bygga avbildningen. |

### <a name="deploy-the-template"></a>Distribuera mallen

Distribuera mallen med kommandot [AZ Deployment Group Create][az-deployment-group-create] . I det här exemplet skapas och pushar avbildningen *HelloWorld-node: testrun* till ett register med namnet *mycontainerregistry*. Bas avbildningen hämtas från *mybaseregistry.azurecr.io*.

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

Föregående kommando skickar parametrarna på kommando raden. Om du vill kan du skicka dem i en [parameter fil](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Verifiera distributionen

När distributionen har slutförts kontrollerar du att avbildningen har skapats genom att köra [AZ ACR-lagringsplats show-Tags][az-acr-repository-show-tags]:

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

### <a name="view-run-log"></a>Visa körnings logg

Information om hur du visar körnings loggen finns i stegen i [föregående avsnitt](#view-run-log).

## <a name="next-steps"></a>Nästa steg

 * Se fler mall exempel i [ACR GitHub-lagrings platsen](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment).
 * Mer information om mallegenskaper finns i mallen referens för [aktivitets körningar](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) och [uppgifter](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks).


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-logs]: /cli/azure/acr/task#az-acr-task-logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
