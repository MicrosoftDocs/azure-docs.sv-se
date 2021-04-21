---
title: Autentisera med hanterad identitet
description: Ge åtkomst till avbildningar i ditt privata containerregister med hjälp av en användar-tilldelad eller system tilldelad hanterad Azure-identitet.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 213f49356fdc2444f8bc2cb4635e96015aff0a61
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781549"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Använda en hanterad Azure-identitet för att autentisera till ett Azure-containerregister 

Använd en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) för att autentisera till ett Azure-containerregister från en annan Azure-resurs, utan att behöva ange eller hantera autentiseringsuppgifter för registret. Du kan till exempel konfigurera en användar tilldelad eller system tilldelad hanterad identitet på en virtuell Linux-dator för att komma åt containeravbildningar från containerregistret, lika enkelt som du använder ett offentligt register. Du kan också konfigurera ett Azure Kubernetes Service [](../aks/use-managed-identity.md) kluster för att använda dess hanterade identitet för att hämta containeravbildningar Azure Container Registry för podddistributioner.

I den här artikeln får du lära dig mer om hanterade identiteter och hur du:

> [!div class="checklist"]
> * Aktivera en användar tilldelad eller system tilldelad identitet på en virtuell Azure-dator
> * Bevilja identiteten åtkomst till ett Azure-containerregister
> * Använda den hanterade identiteten för att komma åt registret och hämta en containeravbildning 

För att skapa Azure-resurserna kräver den här artikeln att du kör Azure CLI version 2.0.55 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

Om du vill konfigurera ett containerregister och push-installera en containeravbildning till det måste du också ha Docker installerat lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på ett [macOS][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-system.

## <a name="why-use-a-managed-identity"></a>Varför ska jag använda en hanterad identitet?

Om du inte känner till funktionen för hanterade identiteter för Azure-resurser kan du läsa igenom den här [översikten](../active-directory/managed-identities-azure-resources/overview.md).

När du har ställt in valda Azure-resurser med en hanterad identitet ger du identiteten den åtkomst som du vill ha till en annan resurs, precis som alla säkerhetsobjekt. Du kan till exempel tilldela en hanterad identitet en roll med pull, push och pull eller andra behörigheter till ett privat register i Azure. (En fullständig lista över registerroller finns i [Azure Container Registry roller och behörigheter](container-registry-roles.md).) Du kan ge en identitet åtkomst till en eller flera resurser.

Använd sedan identiteten för att autentisera till alla [tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), utan några autentiseringsuppgifter i koden. Välj hur du vill autentisera med hjälp av den hanterade identiteten, beroende på ditt scenario. Om du vill använda identiteten för att få åtkomst till ett Azure-containerregister från en virtuell dator autentiserar du med Azure Resource Manager. 

> [!NOTE]
> För närvarande kan tjänster som Azure Web App for Containers eller Azure Container Instances inte använda sin hanterade identitet för att autentisera med Azure Container Registry när du drar en containeravbildning för att distribuera själva containerresursen. Identiteten är endast tillgänglig när containern körs. Om du vill distribuera dessa resurser med hjälp Azure Container Registry avbildningar från en tjänst rekommenderas en annan autentiseringsmetod, till exempel [tjänstens](container-registry-auth-service-principal.md) huvudnamn.

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Om du inte redan har ett Azure-containerregister kan du skapa ett register och push-skicka en exempelcontaineravbildning till det. Anvisningar finns i [Snabbstart: Skapa ett privat containerregister med hjälp av Azure CLI.](container-registry-get-started-azure-cli.md)

Den här artikeln förutsätter att du har `aci-helloworld:v1` containeravbildningen lagrad i registret. I exemplen används registernamnet *myContainerRegistry*. Ersätt med dina egna register- och avbildningsnamn i senare steg.

## <a name="create-a-docker-enabled-vm"></a>Skapa en Docker-aktiverad virtuell dator

Skapa en Docker-aktiverad virtuell Ubuntu-dator. Du måste också installera [Azure CLI på](/cli/azure/install-azure-cli) den virtuella datorn. Om du redan har en virtuell Azure-dator kan du hoppa över det här steget för att skapa den virtuella datorn.

Distribuera en virtuell Ubuntu Azure-standarddator med [az vm create][az-vm-create]. I följande exempel skapas en virtuell dator med *namnet myDockerVM* i en befintlig resursgrupp med namnet *myResourceGroup:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När kommandot har slutförts noterar du det som `publicIpAddress` visas av Azure CLI. Använd den här adressen för att göra SSH-anslutningar till den virtuella datorn.

### <a name="install-docker-on-the-vm"></a>Installera Docker på den virtuella datorn

När den virtuella datorn körs gör du en SSH-anslutning till den virtuella datorn. Ersätt *publicIpAddress med* den offentliga IP-adressen för den virtuella datorn.

```bash
ssh azureuser@publicIpAddress
```

Kör följande kommando för att installera Docker på den virtuella datorn:

```bash
sudo apt update
sudo apt install docker.io -y
```

Efter installationen kör du följande kommando för att kontrollera att Docker körs korrekt på den virtuella datorn:

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

Följ stegen i Installera [Azure CLI med apt för](/cli/azure/install-azure-cli-apt) att installera Azure CLI på din virtuella Ubuntu-dator. Se till att du installerar version 2.0.55 eller senare för den här artikeln.

Avsluta SSH-sessionen.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Exempel 1: Åtkomst med en användar tilldelad identitet

### <a name="create-an-identity"></a>Skapa en identitet

Skapa en identitet i din prenumeration med hjälp av [kommandot az identity](/cli/azure/identity#az_identity_create) create. Du kan använda samma resursgrupp som du använde tidigare för att skapa containerregistret, den virtuella datorn eller en annan.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Om du vill konfigurera identiteten i följande steg använder du kommandot [az identity show][az_identity_show] för att lagra identitetens resurs-ID och tjänstens huvudnamns-ID i variabler.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Eftersom du behöver identitetens ID i ett senare steg när du loggar in på CLI från den virtuella datorn visar du värdet:

```bash
echo $userID
```

ID:t är i formuläret:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>Konfigurera den virtuella datorn med identiteten

Följande kommando [az vm identity assign][az-vm-identity-assign] konfigurerar den virtuella Docker-datorn med den användar-tilldelade identiteten:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Bevilja identitetsåtkomst till containerregistret

Konfigurera nu identiteten så att den får åtkomst till containerregistret. Använd först [kommandot az acr show][az-acr-show] för att hämta registrets resurs-ID:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Använd kommandot [az role assignment create][az-role-assignment-create] för att tilldela AcrPull-rollen till registret. Den här rollen [ger pull-behörigheter](container-registry-roles.md) till registret. Om du vill ange både pull- och push-behörigheter tilldelar du rollen ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Använda identiteten för att få åtkomst till registret

SSH till den virtuella Docker-datorn som är konfigurerad med identiteten. Kör följande Azure CLI-kommandon med hjälp av Azure CLI som är installerat på den virtuella datorn.

Först autentiserar du till Azure CLI med [az login][az-login], med hjälp av den identitet som du konfigurerade på den virtuella datorn. För `<userID>` ersätter du ID:t för den identitet som du hämtade i föregående steg. 

```azurecli
az login --identity --username <userID>
```

Autentisera sedan till registret med [az acr login][az-acr-login]. När du använder det här kommandot använder CLI den Active Directory-token som skapades när du körde för att sömlöst autentisera `az login` sessionen med containerregistret. (Beroende på den virtuella datorns konfiguration kan du behöva köra det här kommandot och Docker-kommandon med `sudo` .)

```azurecli
az acr login --name myContainerRegistry
```

Du bör se ett `Login succeeded` meddelande. Du kan sedan köra kommandon `docker` utan att ange autentiseringsuppgifter. Du kan till exempel [köra docker pull][docker-pull] för att hämta `aci-helloworld:v1` avbildningen och ange namnet på inloggningsservern för ditt register. Inloggningsserverns namn består av ditt containerregisternamn (endast gemener) följt `.azurecr.io` av , till exempel `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Exempel 2: Åtkomst med en system tilldelad identitet

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Konfigurera den virtuella datorn med en system hanterad identitet

Följande az [vm identity assign-kommando][az-vm-identity-assign] konfigurerar din virtuella Docker-dator med en system-tilldelad identitet:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Använd kommandot [az vm show][az-vm-show] för att ange en variabel till värdet för (tjänstens huvudnamns-ID) för den virtuella datorns identitet, som `principalId` ska användas i senare steg.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Bevilja identitetsåtkomst till containerregistret

Konfigurera nu identiteten för åtkomst till containerregistret. Använd först [kommandot az acr show][az-acr-show] för att hämta registrets resurs-ID:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Använd kommandot [az role assignment create][az-role-assignment-create] för att tilldela AcrPull-rollen till identiteten. Den här rollen [ger pull-behörigheter](container-registry-roles.md) till registret. Om du vill ange både pull- och push-behörigheter tilldelar du rollen ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Använda identiteten för att få åtkomst till registret

SSH till den virtuella Docker-datorn som har konfigurerats med identiteten. Kör följande Azure CLI-kommandon med hjälp av Azure CLI som är installerat på den virtuella datorn.

Först autentiserar du Azure CLI med [az login][az-login], med hjälp av den system tilldelade identiteten på den virtuella datorn.

```azurecli
az login --identity
```

Autentisera sedan till registret med [az acr login][az-acr-login]. När du använder det här kommandot använder CLI den Active Directory-token som skapades när du körde för att `az login` sömlöst autentisera sessionen med containerregistret. (Beroende på den virtuella datorns konfiguration kan du behöva köra det här kommandot och Docker-kommandon med `sudo` .)

```azurecli
az acr login --name myContainerRegistry
```

Du bör se ett `Login succeeded` meddelande. Du kan sedan köra kommandon `docker` utan att ange autentiseringsuppgifter. Kör till exempel [docker pull för att][docker-pull] hämta avbildningen `aci-helloworld:v1` och ange namnet på inloggningsservern för registret. Inloggningsserverns namn består av containerregistrets namn (endast gemener) följt `.azurecr.io` av , till exempel `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder hanterade identiteter med Azure Container Registry och hur du:

> [!div class="checklist"]
> * Aktivera en användar-tilldelad eller system tilldelad identitet på en virtuell Azure-dator
> * Bevilja identiteten åtkomst till ett Azure-containerregister
> * Använda den hanterade identiteten för att komma åt registret och hämta en containeravbildning

* Läs mer om [hanterade identiteter för Azure-resurser.](../active-directory/managed-identities-azure-resources/index.yml)


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-vm-show]: /cli/azure/vm#az_vm_show
[az-vm-identity-assign]: /cli/azure/vm/identity#az_vm_identity_assign
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-identity-show]: /cli/azure/identity#az_identity_show
[azure-cli]: /cli/azure/install-azure-cli
