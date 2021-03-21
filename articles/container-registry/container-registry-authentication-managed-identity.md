---
title: Autentisera med hanterad identitet
description: Ge till gång till avbildningar i ditt privata behållar register med hjälp av en användar tilldelad eller systemtilldelad hanterad Azure-identitet.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: e6c0d21f7bdefa94241655225589a52c02110f70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041475"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Använd en Azure-hanterad identitet för att autentisera till ett Azure Container Registry 

Använd en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) för att autentisera till ett Azure Container Registry från en annan Azure-resurs, utan att behöva ange eller hantera autentiseringsuppgifter för registret. Du kan till exempel konfigurera en användardefinierad eller systemtilldelad hanterad identitet på en virtuell Linux-dator för att få åtkomst till behållar avbildningar från behållar registret, så enkelt som du använder ett offentligt register.

I den här artikeln får du lära dig mer om hanterade identiteter och hur du:

> [!div class="checklist"]
> * Aktivera en användardefinierad eller systemtilldelad identitet på en virtuell Azure-dator
> * Ge identitets åtkomst till ett Azure Container Registry
> * Använd den hanterade identiteten för att komma åt registret och hämta en behållar avbildning 

Den här artikeln kräver att du kör Azure CLI-version 2.0.55 eller senare för att skapa Azure-resurser. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

Om du vill konfigurera ett behållar register och skicka en behållar avbildning till den måste du också ha Docker installerat lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på ett [macOS][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-system.

## <a name="why-use-a-managed-identity"></a>Varför ska jag använda en hanterad identitet?

En hanterad identitet för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan konfigurera [vissa Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), inklusive virtuella datorer, med en hanterad identitet. Använd sedan identiteten för att få åtkomst till andra Azure-resurser utan att skicka autentiseringsuppgifter i kod eller skript.

Hanterade identiteter är av två typer:

* *Användarspecifika identiteter*, som du kan tilldela till flera resurser och bevara så länge du vill. Användarspecifika identiteter är för närvarande en för hands version.

* En *Systemhanterad identitet*, som är unik för en viss resurs, till exempel en enskild virtuell dator och varar för den aktuella resursens livs längd.

När du har konfigurerat en Azure-resurs med en hanterad identitet ger du identiteten åtkomst till en annan resurs, precis som alla säkerhets objekt. Du kan till exempel tilldela en hanterad identitet en roll med pull, push och pull eller andra behörigheter till ett privat register i Azure. (En fullständig lista över register roller finns i [Azure Container Registry roller och behörigheter](container-registry-roles.md).) Du kan ge en identitets åtkomst till en eller flera resurser.

Använd sedan identiteten för att autentisera till en [tjänst som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), utan några autentiseringsuppgifter i koden. Om du vill använda identiteten för att komma åt ett Azure Container Registry från en virtuell dator autentiserar du med Azure Resource Manager. Välj hur du vill autentisera med hjälp av den hanterade identiteten, beroende på ditt scenario:

* [Skaffa en Azure AD-åtkomsttoken](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) programmatiskt med http-eller rest-anrop

* Använd [Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md) : er

* [Logga in på Azure CLI eller PowerShell](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) med identiteten. 

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Om du inte redan har ett Azure Container Registry kan du skapa ett register och skicka en exempel behållar avbildning till den. Anvisningar finns i [snabb start: skapa ett privat behållar register med hjälp av Azure CLI](container-registry-get-started-azure-cli.md).

I den här artikeln förutsätter vi att du har `aci-helloworld:v1` behållar avbildningen som lagras i registret. I exemplen används register namnet *myContainerRegistry*. Ersätt med dina egna register-och avbildnings namn i senare steg.

## <a name="create-a-docker-enabled-vm"></a>Skapa en Docker-aktiverad virtuell dator

Skapa en Docker-aktiverad virtuell Ubuntu-dator. Du måste också installera [Azure CLI](/cli/azure/install-azure-cli) på den virtuella datorn. Om du redan har en virtuell Azure-dator kan du hoppa över det här steget för att skapa den virtuella datorn.

Distribuera en standard virtuell Azure-Ubuntu med [AZ VM Create][az-vm-create]. I följande exempel skapas en virtuell dator med namnet *myDockerVM* i en befintlig resurs grupp med namnet *myResourceGroup*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När kommandot har slutförts noterar du det `publicIpAddress` som visas av Azure CLI. Använd den här adressen för att göra SSH-anslutningar till den virtuella datorn.

### <a name="install-docker-on-the-vm"></a>Installera Docker på den virtuella datorn

När den virtuella datorn har körts skapar du en SSH-anslutning till den virtuella datorn. Ersätt *publicIpAddress* med den offentliga IP-adressen för den virtuella datorn.

```bash
ssh azureuser@publicIpAddress
```

Kör följande kommando för att installera Docker på den virtuella datorn:

```bash
sudo apt update
sudo apt install docker.io -y
```

Efter installationen kör du följande kommando för att kontrol lera att Docker körs korrekt på den virtuella datorn:

```bash
sudo docker run -it mcr.microsoft.com/hello-world
```

Utdata:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Installera Azure CLI

Följ stegen i [Installera Azure CLI med apt](/cli/azure/install-azure-cli-apt) för att installera Azure CLI på den virtuella Ubuntu-datorn. I den här artikeln kontrollerar du att du installerar version 2.0.55 eller senare.

Avsluta SSH-sessionen.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Exempel 1: åtkomst med en användardefinierad identitet

### <a name="create-an-identity"></a>Skapa en identitet

Skapa en identitet i din prenumeration med kommandot [AZ Identity Create](/cli/azure/identity#az_identity_create) . Du kan använda samma resurs grupp som du använde tidigare för att skapa behållar registret eller den virtuella datorn, eller en annan.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Om du vill konfigurera identiteten i följande steg använder du kommandot [AZ Identity show] [az_identity_show] för att lagra identitetens resurs-ID och tjänstens huvud namns-ID i variabler.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Eftersom du behöver identitetens ID i ett senare steg när du loggar in på CLI från den virtuella datorn, visar du värdet:

```bash
echo $userID
```

ID: t har formatet:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>Konfigurera den virtuella datorn med identiteten

Följande [AZ för virtuell dator identitet][az-vm-identity-assign] konfigurerar den virtuella Docker-datorn med den användare som tilldelats identiteten:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Bevilja identitets åtkomst till behållar registret

Konfigurera identiteten för att få åtkomst till ditt behållar register. Använd först kommandot [AZ ACR show][az-acr-show] för att hämta resurs-ID: t för registret:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Använd kommandot [AZ Role tilldelning Create][az-role-assignment-create] för att tilldela AcrPull-rollen till registret. Den här rollen ger [pull-behörighet](container-registry-roles.md) till registret. Tilldela ACRPush-rollen för att ge både pull-och push-behörighet.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Använd identiteten för att komma åt registret

SSH till Docker virtuell dator som har kon figurer ATS med identiteten. Kör följande Azure CLI-kommandon med hjälp av Azure CLI installerat på den virtuella datorn.

Börja med att autentisera till Azure CLI med [AZ-inloggning][az-login]med den identitet som du konfigurerade på den virtuella datorn. För `<userID>` ersätter du ID: t för den identitet som du hämtade i föregående steg. 

```azurecli
az login --identity --username <userID>
```

Autentisera sedan till registret med [AZ ACR-inloggning][az-acr-login]. När du använder det här kommandot använder CLI Active Directory token som skapades när du körde `az login` för att enkelt autentisera sessionen med behållar registret. (Beroende på den virtuella datorns konfiguration kan du behöva köra kommandot och Docker-kommandona med `sudo` .)

```azurecli
az acr login --name myContainerRegistry
```

Du bör se ett `Login succeeded` meddelande. Du kan sedan köra `docker` kommandon utan att ange autentiseringsuppgifter. Du kan till exempel köra [Docker pull][docker-pull] för att hämta `aci-helloworld:v1` avbildningen och ange namnet på inloggnings servern för registret. Inloggnings serverns namn består av ditt behållar register namn (alla gemener) följt av `.azurecr.io` – till exempel `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Exempel 2: åtkomst med en tilldelad identitet

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Konfigurera den virtuella datorn med en Systemhanterad identitet

Följande [AZ för den virtuella datorns identitet tilldelas][az-vm-identity-assign] konfigurerar din Docker-VM med en tilldelad identitet:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Använd kommandot [AZ VM show][az-vm-show] för att ange en variabel till värdet för `principalId` (tjänstens huvud namns-ID) för den virtuella datorns identitet, som ska användas i senare steg.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Bevilja identitets åtkomst till behållar registret

Konfigurera identiteten för att få åtkomst till ditt behållar register. Använd först kommandot [AZ ACR show][az-acr-show] för att hämta resurs-ID: t för registret:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Använd kommandot [AZ Role tilldelning Create][az-role-assignment-create] för att tilldela identiteten rollen AcrPull. Den här rollen ger [pull-behörighet](container-registry-roles.md) till registret. Tilldela ACRPush-rollen för att ge både pull-och push-behörighet.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Använd identiteten för att komma åt registret

SSH till Docker virtuell dator som har kon figurer ATS med identiteten. Kör följande Azure CLI-kommandon med hjälp av Azure CLI installerat på den virtuella datorn.

Autentisera först Azure CLI med AZ- [inloggning][az-login]med den systemtilldelade identiteten på den virtuella datorn.

```azurecli
az login --identity
```

Autentisera sedan till registret med [AZ ACR-inloggning][az-acr-login]. När du använder det här kommandot använder CLI Active Directory token som skapades när du körde `az login` för att enkelt autentisera sessionen med behållar registret. (Beroende på den virtuella datorns konfiguration kan du behöva köra kommandot och Docker-kommandona med `sudo` .)

```azurecli
az acr login --name myContainerRegistry
```

Du bör se ett `Login succeeded` meddelande. Du kan sedan köra `docker` kommandon utan att ange autentiseringsuppgifter. Du kan till exempel köra [Docker pull][docker-pull] för att hämta `aci-helloworld:v1` avbildningen och ange namnet på inloggnings servern för registret. Inloggnings serverns namn består av ditt behållar register namn (alla gemener) följt av `.azurecr.io` – till exempel `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```
> [!NOTE]
> Systemtilldelade hanterade tjänst identiteter kan användas för att interagera med ACR: er och App Service kan använda systemtilldelade hanterade tjänst identiteter. Men du kan inte kombinera dessa, eftersom App Service inte kan använda MSI för att kommunicera med en ACR. Det enda sättet är att aktivera admin på ACR och använda admin användar namn/lösen ord.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder hanterade identiteter med Azure Container Registry och hur du:

> [!div class="checklist"]
> * Aktivera en användardefinierad eller systemtilldelad identitet i en virtuell Azure-dator
> * Ge identitets åtkomst till ett Azure Container Registry
> * Använd den hanterade identiteten för att komma åt registret och hämta en behållar avbildning

* Lär dig mer om [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/index.yml).


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-show]: /cli/azure/vm#az-vm-show
[az-vm-identity-assign]: /cli/azure/vm/identity#az-vm-identity-assign
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
