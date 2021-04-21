---
title: Hanterad identitet i ACR-uppgift
description: Aktivera en hanterad identitet för Azure-resurser i en Azure Container Registry-uppgift så att uppgiften får åtkomst till andra Azure-resurser, inklusive andra privata containerregister.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: 19d63861a98884ff2f5103946c19e2226c4b14b7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781171"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Använda en Azure-hanterad identitet i ACR-uppgifter 

Aktivera en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) i en [ACR-uppgift](container-registry-tasks-overview.md)så att aktiviteten kan komma åt andra Azure-resurser, utan att behöva ange eller hantera autentiseringsuppgifter. Du kan till exempel använda en hanterad identitet för att aktivera ett uppgiftssteg för att hämta eller push-skicka containeravbildningar till ett annat register.

I den här artikeln får du lära dig hur du använder Azure CLI för att aktivera en användar tilldelad eller system tilldelad hanterad identitet på en ACR-uppgift. Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI. Om du vill använda den lokalt krävs version 2.0.68 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

I illustrationssyfte använder exempelkommandona i den här artikeln [az acr task create][az-acr-task-create] för att skapa en grundläggande avbildningsbygguppgift som möjliggör en hanterad identitet. Exempelscenarier för åtkomst till skyddade resurser från en ACR-uppgift med hjälp av en hanterad identitet finns i:

* [Autentisering mellan register](container-registry-tasks-cross-registry-authentication.md)
* [Få åtkomst till externa resurser med hemligheter lagrade i Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Varför ska jag använda en hanterad identitet?

En hanterad identitet för Azure-resurser ger valda Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan konfigurera en ACR-uppgift med en hanterad identitet så att uppgiften kan komma åt andra skyddade Azure-resurser utan att skicka autentiseringsuppgifter i uppgiftsstegen.

Hanterade identiteter är av två typer:

* *Användar-tilldelade identiteter*, som du kan tilldela till flera resurser och spara så länge du vill. Användar tilldelade identiteter finns för närvarande i förhandsversion.

* En *system tilldelad identitet*, som är unik för en specifik resurs, till exempel en ACR-uppgift och som varar under resursens livslängd.

Du kan aktivera antingen eller båda identitetstyperna i en ACR-uppgift. Bevilja identiteten åtkomst till en annan resurs, precis som alla säkerhetsobjekt. När aktiviteten körs använder den identiteten för att komma åt resursen i alla uppgiftssteg som kräver åtkomst.

## <a name="steps-to-use-a-managed-identity"></a>Steg för att använda en hanterad identitet

Följ de här avancerade stegen för att använda en hanterad identitet med en ACR-uppgift.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (Valfritt) Skapa en användar tilldelad identitet

Om du planerar att använda en användar tilldelad identitet använder du en befintlig identitet eller skapar identiteten med hjälp av Azure CLI eller andra Azure-verktyg. Använd till exempel kommandot [az identity create.][az-identity-create] 

Om du planerar att endast använda en system tilldelad identitet hoppar du över det här steget. Du skapar en system tilldelad identitet när du skapar ACR-uppgiften.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Aktivera identitet för en ACR-uppgift

När du skapar en ACR-uppgift kan du välja att aktivera en användar tilldelad identitet, en system tilldelad identitet eller båda. Du kan till exempel skicka `--assign-identity` parametern när du kör [kommandot az acr task create][az-acr-task-create] i Azure CLI.

Om du vill aktivera en system tilldelad identitet skickar `--assign-identity` du utan värde eller `assign-identity [system]` . Följande exempelkommando skapar en Linux-uppgift från en offentlig GitHub-lagringsplats som skapar avbildningen och aktiverar `hello-world` en system tilldelad hanterad identitet:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Om du vill aktivera en användar tilldelad identitet `--assign-identity` skickar du med ett värde för identitetens resurs-ID.  Följande exempelkommando skapar en Linux-uppgift från en offentlig GitHub-lagringsplats som skapar avbildningen och aktiverar `hello-world` en användar tilldelad hanterad identitet:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

Du kan hämta identitetens resurs-ID genom att köra [kommandot az identity show.][az-identity-show] Resurs-ID:t för *ID:t myUserAssignedIdentity* i resursgruppen *myResourceGroup* har följande format: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Ge identiteten behörighet att komma åt andra Azure-resurser

Bevilja identiteten behörighet att komma åt andra Azure-resurser beroende på kraven för din uppgift. Exempel:

* Tilldela den hanterade identiteten en roll med pull, push och pull eller andra behörigheter till ett målcontainerregister i Azure. En fullständig lista över registerroller finns i [Azure Container Registry och behörigheter](container-registry-roles.md). 
* Tilldela den hanterade identiteten en roll för att läsa hemligheter i ett Azure-nyckelvalv.

Använd [Azure CLI eller](../role-based-access-control/role-assignments-cli.md) andra Azure-verktyg för att hantera rollbaserad åtkomst till resurser. Kör till exempel kommandot [az role assignment create][az-role-assignment-create] för att tilldela identiteten en roll till resursen. 

I följande exempel tilldelas en hanterad identitet behörighet att hämta från ett containerregister. Kommandot anger *huvudnamns-ID* för uppgiftsidentiteten och *resurs-ID* för målregistret.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (Valfritt) Lägg till autentiseringsuppgifter för uppgiften

Om uppgiften behöver autentiseringsuppgifter för att hämta eller skicka avbildningar till ett annat anpassat register, eller för att få åtkomst till andra resurser, lägger du till autentiseringsuppgifter för uppgiften. Kör kommandot [az acr task credential add][az-acr-task-credential-add] för att lägga till autentiseringsuppgifter och skicka parametern för att ange att `--use-identity` identiteten kan komma åt autentiseringsuppgifterna. 

Om du till exempel vill lägga till autentiseringsuppgifter för en system tilldelad identitet för autentisering med Azure Container Registry *targetregistry* skickar du `use-identity [system]` :

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Om du vill lägga till autentiseringsuppgifter för en användar tilldelad identitet för autentisering med *registrets targetregistry* skickar du med ett värde för `use-identity` klient-ID:t för identiteten.  Exempel:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Du kan hämta klient-ID för identiteten genom att köra [kommandot az identity show.][az-identity-show] Klient-ID:t är ett GUID i formen `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` .

### <a name="5-run-the-task"></a>5. Kör uppgiften

När du har konfigurerat en uppgift med en hanterad identitet kör du uppgiften. Om du till exempel vill testa en av uppgifterna som skapats i den här artikeln utlöser du den manuellt med [kommandot az acr task run.][az-acr-task-run] Om du har konfigurerat ytterligare automatiserade aktivitetsutlösare körs aktiviteten när den utlöses automatiskt.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du aktiverar och använder en användar tilldelad eller system tilldelad hanterad identitet på en ACR-uppgift. Scenarier för att komma åt skyddade resurser från en ACR-uppgift med hjälp av en hanterad identitet finns i:

* [Autentisering mellan register](container-registry-tasks-cross-registry-authentication.md)
* [Få åtkomst till externa resurser med hemligheter som lagras i Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[azure-cli-install]: /cli/azure/install-azure-cli
