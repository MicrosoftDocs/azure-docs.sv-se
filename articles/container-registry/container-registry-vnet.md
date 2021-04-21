---
title: Begränsa åtkomst med hjälp av en tjänstslutpunkt
description: Begränsa åtkomsten till ett Azure-containerregister med hjälp av en tjänstslutpunkt i ett virtuellt Azure-nätverk. Åtkomst till tjänstslutpunkter är en funktion på Premium-tjänstnivån.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 8a67a011c75a192df9ad3460458fd766b5ec1ec1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773478"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Begränsa åtkomsten till ett containerregister med hjälp av en tjänstslutpunkt i ett virtuellt Azure-nätverk

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) tillhandahåller säkra, privata nätverk för dina Azure-resurser och lokala resurser. Med [en tjänstslutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) kan du skydda containerregistrets offentliga IP-adress till endast ditt virtuella nätverk. Den här slutpunkten ger trafiken en optimal väg till resursen via Azure-stamnätverket. Identiteterna för det virtuella nätverket och undernätet överförs också med varje begäran.

Den här artikeln visar hur du konfigurerar en tjänstslutpunkt för containerregister (förhandsversion) i ett virtuellt nätverk. 

> [!IMPORTANT]
> Azure Container Registry stöder nu [Azure Private Link](container-registry-private-link.md), vilket gör att privata slutpunkter från ett virtuellt nätverk kan placeras i ett register. Privata slutpunkter kan nås inifrån det virtuella nätverket med hjälp av privata IP-adresser. Vi rekommenderar att du använder privata slutpunkter i stället för tjänstslutpunkter i de flesta nätverksscenarier.

Du kan konfigurera en slutpunkt för registertjänsten på **tjänstnivån** Premium-containerregister. Information om registertjänstnivåer och begränsningar finns i [Azure Container Registry tjänstnivåer](container-registry-skus.md).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Framtida utveckling av tjänstslutpunkter för Azure Container Registry inte planeras för närvarande. Vi rekommenderar att du [använder privata slutpunkter i](container-registry-private-link.md) stället.
* Du kan inte använda Azure Portal för att konfigurera tjänstslutpunkter i ett register.
* Endast ett [Azure Kubernetes Service](../aks/intro-kubernetes.md) eller en virtuell [Azure-dator](../virtual-machines/linux/overview.md) kan användas som värd för att komma åt ett containerregister med hjälp av en tjänstslutpunkt. *Andra Azure-tjänster, Azure Container Instances tjänster stöds inte.*
* Tjänstslutpunkter för Azure Container Registry stöds inte i Azure US Government-molnet eller Azure China-molnet.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Förutsättningar

* Om du vill använda Azure CLI-stegen i den här artikeln krävs Azure CLI version 2.0.58 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

* Om du inte redan har ett containerregister skapar du ett (Premium-nivå krävs) och push-pushar en exempelavbildning, till `hello-world` exempel Docker Hub. Du kan till exempel använda [Azure Portal][quickstart-portal] eller [Azure CLI för][quickstart-cli] att skapa ett register. 

* Om du vill begränsa registeråtkomsten med hjälp av en tjänstslutpunkt i en annan Azure-prenumeration registrerar du resursprovidern för Azure Container Registry i prenumerationen. Exempel:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>Konfigurera nätverksåtkomst för registret

I det här avsnittet konfigurerar du containerregistret så att det tillåter åtkomst från ett undernät i ett virtuellt Azure-nätverk. Steg tillhandahålls med hjälp av Azure CLI.

### <a name="add-a-service-endpoint-to-a-subnet"></a>Lägga till en tjänstslutpunkt i ett undernät

När du skapar en virtuell dator skapar Azure som standard ett virtuellt nätverk i samma resursgrupp. Namnet på det virtuella nätverket baseras på namnet på den virtuella datorn. Om du till exempel ger den virtuella datorn namnet *myDockerVM* är standardnamnet för det virtuella nätverket *myDockerVMVNET*, med ett undernät med namnet *myDockerVMSubnet*. Kontrollera detta med kommandot [az network vnet list:][az-network-vnet-list]

```azurecli
az network vnet list \
  --resource-group myResourceGroup \
  --query "[].{Name: name, Subnet: subnets[0].name}"
```

Utdata:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

Använd kommandot [az network vnet subnet update för][az-network-vnet-subnet-update] att lägga till en **Microsoft.ContainerRegistry-tjänstslutpunkt** i undernätet. Ersätt namnen på ditt virtuella nätverk och undernät med följande kommando:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Använd kommandot [az network vnet subnet show][az-network-vnet-subnet-show] för att hämta resurs-ID för undernätet. Du behöver detta i ett senare steg för att konfigurera en regel för nätverksåtkomst.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Utdata:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="change-default-network-access-to-registry"></a>Ändra standardnätverksåtkomst till registret

Som standard tillåter ett Azure-containerregister anslutningar från värdar i alla nätverk. Om du vill begränsa åtkomsten till ett valt nätverk ändrar du standardåtgärden till neka åtkomst. Ersätt namnet på registret i följande [az acr update-kommando:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Lägga till nätverksregel i registret

Använd kommandot [az acr network-rule add][az-acr-network-rule-add] för att lägga till en nätverksregel i registret som tillåter åtkomst från den virtuella datorns undernät. Ersätt containerregistrets namn och resurs-ID för undernätet med följande kommando: 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>Verifiera åtkomsten till registret

När du har väntat några minuter på att konfigurationen ska uppdateras kontrollerar du att den virtuella datorn har åtkomst till containerregistret. Skapa en SSH-anslutning till den virtuella datorn och kör [kommandot az acr login][az-acr-login] för att logga in i registret. 

```bash
az acr login --name mycontainerregistry
```

Du kan utföra registeråtgärder som att köra för `docker pull` att hämta en exempelavbildning från registret. Ersätt en avbildning och taggvärde som är lämpligt för ditt register, med prefixet registerinloggningsserverns namn (endast gemener):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker hämtar avbildningen till den virtuella datorn.

Det här exemplet visar att du kan komma åt det privata containerregistret via nätverksåtkomstregeln. Registret kan dock inte nås från en inloggningsvärd som inte har en konfigurerad nätverksåtkomstregel. Om du försöker logga in från en annan värd med `az acr login` kommandot eller kommandot ser `docker login` utdata ut ungefär så här:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Återställa standardåtkomst till registret

Om du vill återställa registret så att det tillåter åtkomst som standard tar du bort alla nätverksregler som har konfigurerats. Ange sedan standardåtgärden för att tillåta åtkomst. 

### <a name="remove-network-rules"></a>Ta bort nätverksregler

Om du vill se en lista över nätverksregler som har konfigurerats för registret kör du följande [kommando az acr network-rule list:][az-acr-network-rule-list]

```azurecli
az acr network-rule list --name mycontainerregistry 
```

För varje regel som har konfigurerats kör du kommandot [az acr network-rule remove][az-acr-network-rule-remove] för att ta bort den. Exempel:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="allow-access"></a>Tillåt åkomst

Ersätt namnet på registret med följande [az acr update-kommando:][az-acr-update]
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat alla Azure-resurser i samma resursgrupp och inte längre behöver dem kan du ta bort resurserna med ett enda [az group delete-kommando:](/cli/azure/group)

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du begränsar åtkomsten till ett register med hjälp av en privat slutpunkt i ett virtuellt nätverk finns i [Konfigurera Azure Private Link för ett Azure-containerregister.](container-registry-private-link.md)
* Om du behöver konfigurera regler för registeråtkomst bakom en klientbrandvägg kan du se Konfigurera regler för åtkomst till [ett Azure-containerregister bakom en brandvägg.](container-registry-firewall-access-rules.md)


<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az_acr_network_rule_add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az_acr_network_rule_remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az_acr_network_rule_list
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-list]: /cli/azure/network/vnet/#az_network_vnet_list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
