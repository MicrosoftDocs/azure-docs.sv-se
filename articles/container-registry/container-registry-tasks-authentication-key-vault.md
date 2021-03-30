---
title: Extern autentisering från ACR-aktivitet
description: Konfigurera en Azure Container Registry aktivitet (ACR Task) för att läsa autentiseringsuppgifter för Docker Hub lagrade i ett Azure Key Vault med hjälp av en hanterad identitet för Azure-resurser.
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 0bc43f958a14016146160a06372af0b36a9fff75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86058137"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Extern autentisering i en ACR-aktivitet med hjälp av en Azure-hanterad identitet 

I en [ACR-uppgift](container-registry-tasks-overview.md)kan du [Aktivera en hanterad identitet för Azure-resurser](container-registry-tasks-authentication-managed-identity.md). Uppgiften kan använda identiteten för att få åtkomst till andra Azure-resurser, utan att behöva ange eller hantera autentiseringsuppgifter. 

I den här artikeln får du lära dig hur du aktiverar en hanterad identitet i en aktivitet som har åtkomst till hemligheter lagrade i ett Azure Key Vault. 

Den här artikeln kräver att du kör Azure CLI-version 2.0.68 eller senare för att skapa Azure-resurser. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

## <a name="scenario-overview"></a>Översikt över scenario

I exempel uppgiften läses Docker hubbens autentiseringsuppgifter som lagras i ett Azure Key Vault. Autentiseringsuppgifterna är för ett Docker Hub-konto med behörighet att skriva (push) till en privat Docker Hub-lagringsplats. Om du vill läsa autentiseringsuppgifterna konfigurerar du aktiviteten med en hanterad identitet och tilldelar den behörigheten. Uppgiften som är associerad med identiteten skapar en avbildning och loggar in i Docker Hub för att skicka avbildningen till den privata lagrings platsen. 

Det här exemplet visar steg som använder antingen en användardefinierad eller systemtilldelad hanterad identitet. Ditt val av identitet beror på organisationens behov.

I ett verkligt scenario kan ett företag publicera avbildningar till en privat lagrings platsen i Docker-hubben som en del av en build-process. 

## <a name="prerequisites"></a>Förutsättningar

Du behöver ett Azure Container Registry som du kör aktiviteten i. I den här *artikeln kallas registret för registret.* Ersätt med ditt eget register namn i senare steg.

Om du inte redan har ett Azure Container Registry, se [snabb start: skapa ett privat behållar register med hjälp av Azure CLI](container-registry-get-started-azure-cli.md). Du behöver inte skicka avbildningar till registret ännu.

Du behöver också ett privat lager i Docker Hub och ett Docker Hub-konto med behörighet att skriva till lagrings platsen. I det här exemplet heter den här lagrings platsen *hubuser/hubrepo*. 

## <a name="create-a-key-vault-and-store-secrets"></a>Skapa ett nyckel valv och lagra hemligheter

Först, om du behöver, skapa en resurs grupp med namnet *myResourceGroup* på den *östra* platsen med följande [AZ Group Create][az-group-create] -kommando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Använd kommandot [AZ-valv][az-keyvault-create] för att skapa ett nyckel valv. Se till att ange ett unikt nyckel valvs namn. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Lagra nödvändiga autentiseringsuppgifter för Docker Hub i nyckel valvet med hjälp av kommandot [AZ Key Vault Secret set][az-keyvault-secret-set] . I de här kommandona skickas värdena i miljövariabler:

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

I ett verkligt scenario skulle hemligheter troligt vis ställas in och behållas i en separat process.

## <a name="define-task-steps-in-yaml-file"></a>Definiera uppgifts steg i YAML-filen

Stegen för den här exempel uppgiften definieras i en [yaml-fil](container-registry-tasks-reference-yaml.md). Skapa en fil med namnet `dockerhubtask.yaml` i en lokal arbets katalog och klistra in följande innehåll. Se till att ersätta Key Vault-namnet i filen med namnet på ditt nyckel valv.

```yml
version: v1.1.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: bash echo '{{.Secrets.password}}' | docker login --username '{{.Secrets.username}}' --password-stdin 
# Build image
  - build: -t {{.Values.PrivateRepo}}:$ID https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:$ID
```

Uppgifts stegen gör följande:

* Hantera hemliga autentiseringsuppgifter för att autentisera med Docker Hub.
* Autentisera med Docker Hub genom att skicka hemligheterna till `docker login` kommandot.
* Bygg en avbildning med hjälp av ett Dockerfile i exempel på [Azure-samples/ACR-tasks](https://github.com/Azure-Samples/acr-tasks.git) lagrings platsen.
* Push-överför avbildningen till den privata Docker Hub-lagringsplatsen.


## <a name="option-1-create-task-with-user-assigned-identity"></a>Alternativ 1: Skapa uppgift med användardefinierad identitet

Stegen i det här avsnittet skapar en uppgift och aktiverar en användardefinierad identitet. Om du vill aktivera en tilldelad identitet i stället, se [Alternativ 2: Skapa uppgift med systemtilldelad identitet](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Skapa uppgift

Skapa uppgiften *dockerhubtask* genom att köra följande kommando för [AZ ACR Task Create][az-acr-task-create] . Aktiviteten körs utan en käll kods kontext och kommandot refererar till filen `dockerhubtask.yaml` i arbets katalogen. `--assign-identity`Parametern skickar resurs-ID: t för den tilldelade identiteten. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]


### <a name="grant-identity-access-to-key-vault"></a>Bevilja identitets åtkomst till nyckel valv

Kör följande [AZ-nyckel valv set-princip][az-keyvault-set-policy] kommando för att ange en åtkomst princip i nyckel valvet. I följande exempel kan identiteten läsa hemligheter från nyckel valvet. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

Fortsätt att [köra aktiviteten manuellt](#manually-run-the-task).

## <a name="option-2-create-task-with-system-assigned-identity"></a>Alternativ 2: Skapa uppgift med systemtilldelad identitet

Stegen i det här avsnittet skapar en uppgift och aktiverar en tilldelad identitet. Om du vill aktivera en användardefinierad identitet i stället, se [alternativ 1: Skapa uppgift med användardefinierad identitet](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Skapa uppgift

Skapa uppgiften *dockerhubtask* genom att köra följande kommando för [AZ ACR Task Create][az-acr-task-create] . Aktiviteten körs utan en käll kods kontext och kommandot refererar till filen `dockerhubtask.yaml` i arbets katalogen. `--assign-identity`Parametern utan värde aktiverar den systemtilldelade identiteten för uppgiften.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="grant-identity-access-to-key-vault"></a>Bevilja identitets åtkomst till nyckel valv

Kör följande [AZ-nyckel valv set-princip][az-keyvault-set-policy] kommando för att ange en åtkomst princip i nyckel valvet. I följande exempel kan identiteten läsa hemligheter från nyckel valvet. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>Kör uppgiften manuellt

Om du vill kontrol lera att den aktivitet där du har aktiverat en hanterad identitet har körts manuellt utlöser du uppgiften med kommandot [AZ ACR Task Run][az-acr-task-run] . `--set`Parametern används för att skicka det privata lagrings platsen-namnet till uppgiften. I det här exemplet är plats hållarens lagrings platsen namn *hubuser/hubrepo*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

När aktiviteten körs visar utdata lyckad autentisering till Docker Hub och avbildningen har skapats och skickats till den privata lagrings platsen:

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

Om du vill bekräfta att avbildningen har överförts söker du efter taggen ( `cf24` i det här exemplet) i den privata Docker Hub-lagrings platsen.

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
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
