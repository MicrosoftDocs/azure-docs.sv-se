---
title: Extern autentisering från ACR-uppgift
description: Konfigurera en Azure Container Registry Task (ACR Task) för att läsa Docker Hub autentiseringsuppgifter som lagras i ett Azure-nyckelvalv med hjälp av en hanterad identitet för Azure-resurser.
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 16404f9244818d91c5333eb5eec5944bfdd9df98
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781207"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Extern autentisering i en ACR-uppgift med hjälp av en Azure-hanterad identitet 

I en [ACR-uppgift](container-registry-tasks-overview.md)kan du aktivera [en hanterad identitet för Azure-resurser.](container-registry-tasks-authentication-managed-identity.md) Uppgiften kan använda identiteten för att få åtkomst till andra Azure-resurser, utan att behöva ange eller hantera autentiseringsuppgifter. 

I den här artikeln får du lära dig hur du aktiverar en hanterad identitet i en uppgift som har åtkomst till hemligheter som lagras i ett Azure-nyckelvalv. 

För att skapa Azure-resurser kräver den här artikeln att du kör Azure CLI version 2.0.68 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

## <a name="scenario-overview"></a>Översikt över scenario

Exempelaktiviteten läser upp Docker Hub som lagras i ett Azure-nyckelvalv. Autentiseringsuppgifterna är för ett Docker Hub-konto med skrivbehörighet (push) till en privat Docker Hub lagringsplats. Om du vill läsa autentiseringsuppgifterna konfigurerar du uppgiften med en hanterad identitet och tilldelar lämplig behörighet till den. Uppgiften som är associerad med identiteten skapar en avbildning och loggar in Docker Hub för att push-skicka avbildningen till den privata lagringsplatsen. 

Det här exemplet visar steg som använder antingen en användar-tilldelad eller system tilldelad hanterad identitet. Ditt val av identitet beror på organisationens behov.

I ett verkligt scenario kan ett företag publicera avbildningar till en privat lagring Docker Hub som en del av en byggprocess. 

## <a name="prerequisites"></a>Förutsättningar

Du behöver ett Azure-containerregister där du kör uppgiften. I den här artikeln heter det här registret *myregistry*. Ersätt med ditt eget registernamn i senare steg.

Om du inte redan har ett Azure-containerregister kan du gå [till Snabbstart: Skapa ett privat containerregister med hjälp av Azure CLI.](container-registry-get-started-azure-cli.md) Du behöver inte push-skicka avbildningar till registret ännu.

Du behöver också en privat lagringsplats i Docker Hub och ett Docker Hub konto med behörighet att skriva till lagringsplatsen. I det här exemplet heter den här *lagringsplatsen hubuser/hubrepo*. 

## <a name="create-a-key-vault-and-store-secrets"></a>Skapa ett nyckelvalv och lagra hemligheter

Om du behöver skapa du först en resursgrupp med namnet *myResourceGroup* på *platsen eastus* med följande [az group create-kommando:][az-group-create]

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Använd kommandot [az keyvault create][az-keyvault-create] för att skapa ett nyckelvalv. Se till att ange ett unikt nyckelvalvsnamn. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Lagra de nödvändiga Docker Hub autentiseringsuppgifter i nyckelvalvet med hjälp av [kommandot az keyvault secret set.][az-keyvault-secret-set] I dessa kommandon skickas värdena i miljövariabler:

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

I ett verkligt scenario skulle hemligheter troligen ställas in och underhållas i en separat process.

## <a name="define-task-steps-in-yaml-file"></a>Definiera uppgiftssteg i YAML-fil

Stegen för den här exempelaktiviteten definieras i en [YAML-fil](container-registry-tasks-reference-yaml.md). Skapa en fil med `dockerhubtask.yaml` namnet i en lokal arbetskatalog och klistra in följande innehåll. Se till att ersätta namnet på nyckelvalvet i filen med namnet på ditt nyckelvalv.

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

Uppgiftsstegen gör följande:

* Hantera hemliga autentiseringsuppgifter för att autentisera med Docker Hub.
* Autentisera med Docker Hub genom att skicka hemligheterna till `docker login` kommandot .
* Skapa en avbildning med hjälp av en Dockerfile-exempelfil på [lagringsplatsen Azure-Samples/acr-tasks.](https://github.com/Azure-Samples/acr-tasks.git)
* Push-skicka avbildningen till den Docker Hub lagringsplatsen.


## <a name="option-1-create-task-with-user-assigned-identity"></a>Alternativ 1: Skapa uppgift med användar tilldelad identitet

Stegen i det här avsnittet skapar en uppgift och aktiverar en användar tilldelad identitet. Om du i stället vill aktivera en system tilldelad identitet kan du se [Alternativ 2: Skapa uppgift med system tilldelad identitet.](#option-2-create-task-with-system-assigned-identity) 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Skapa uppgift

Skapa uppgiften *dockerhubtask genom* att köra följande [az acr task create-kommando.][az-acr-task-create] Aktiviteten körs utan källkodskontext och kommandot refererar till filen `dockerhubtask.yaml` i arbetskatalogen. Parametern `--assign-identity` skickar resurs-ID:t för den användar tilldelade identiteten. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]


### <a name="grant-identity-access-to-key-vault"></a>Bevilja identitetsåtkomst till nyckelvalv

Kör följande az [keyvault set-policy-kommando][az-keyvault-set-policy] för att ange en åtkomstprincip för nyckelvalvet. I följande exempel kan identiteten läsa hemligheter från nyckelvalvet. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

Gå vidare [till Kör aktiviteten manuellt.](#manually-run-the-task)

## <a name="option-2-create-task-with-system-assigned-identity"></a>Alternativ 2: Skapa uppgift med system tilldelad identitet

Stegen i det här avsnittet skapar en uppgift och aktiverar en system tilldelad identitet. Om du vill aktivera en användar tilldelad identitet i stället kan du se [Alternativ 1: Skapa uppgift med användar tilldelad identitet.](#option-1-create-task-with-user-assigned-identity) 

### <a name="create-task"></a>Skapa uppgift

Skapa uppgiften *dockerhubtask genom* att köra följande [az acr task create-kommando.][az-acr-task-create] Aktiviteten körs utan källkodskontext och kommandot refererar till filen `dockerhubtask.yaml` i arbetskatalogen. Parametern `--assign-identity` utan värde aktiverar den system tilldelade identiteten för uppgiften.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="grant-identity-access-to-key-vault"></a>Bevilja identitetsåtkomst till nyckelvalv

Kör följande az [keyvault set-policy-kommando][az-keyvault-set-policy] för att ange en åtkomstprincip för nyckelvalvet. I följande exempel kan identiteten läsa hemligheter från nyckelvalvet. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>Köra uppgiften manuellt

Om du vill kontrollera att aktiviteten där du har aktiverat en hanterad identitet körs, utlöser du aktiviteten manuellt med [kommandot az acr task run.][az-acr-task-run] Parametern `--set` används för att skicka namnet på den privata lagringsplatsen till uppgiften. I det här exemplet är platshållarplatsens namn *hubuser/hubrepo*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

När uppgiften körs visar utdata lyckad autentisering till Docker Hub och avbildningen har skapats och push-skickats till den privata lagringsplatsen:

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

Kontrollera att avbildningen push-skickas genom att söka efter taggen ( i det `cf24` här exemplet) i den privata Docker Hub lagringsplatsen.

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
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[az-group-create]: /cli/azure/group?#az_group_create
[az-keyvault-create]: /cli/azure/keyvault?#az_keyvault_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az_keyvault_secret_set
[az-keyvault-set-policy]: /cli/azure/keyvault#az_keyvault_set_policy
