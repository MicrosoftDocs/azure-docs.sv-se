---
title: Begränsa åtkomst med hjälp av en tjänst slut punkt
description: Begränsa åtkomsten till ett Azure Container Registry med hjälp av en tjänst slut punkt i ett virtuellt Azure-nätverk. Åtkomst till tjänst slut punkten är en funktion i Premium service-nivån.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: c49595ee4ee79aef264a87dd48bccd03f3d4f5a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773903"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Begränsa åtkomsten till ett behållar register med hjälp av en tjänst slut punkt i ett virtuellt Azure-nätverk

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) ger säker, privat nätverk för dina Azure-resurser och lokala resurser. Med en [tjänst slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) kan du skydda behållar registrets offentliga IP-adress till endast det virtuella nätverket. Den här slut punkten ger trafiken en optimal väg till resursen över Azure stamnät nätverket. Identiteterna för det virtuella nätverket och under nätet skickas också med varje begäran.

Den här artikeln visar hur du konfigurerar en tjänst slut punkt för behållar registret (för hands version) i ett virtuellt nätverk. 

> [!IMPORTANT]
> Azure Container Registry stöder nu [Azures privata länk](container-registry-private-link.md), vilket möjliggör att privata slut punkter från ett virtuellt nätverk placeras i ett register. Privata slut punkter är tillgängliga i det virtuella nätverket med hjälp av privata IP-adresser. Vi rekommenderar att du använder privata slut punkter i stället för tjänst slut punkter i de flesta nätverks scenarier.

Konfiguration av en slut punkt för en Registry-tjänst är tillgänglig i tjänst nivån **Premium** container Registry. Information om nivåer och gränser för register tjänster finns i [Azure Container Registry tjänst nivåer](container-registry-skus.md).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Framtida utveckling av tjänst slut punkter för Azure Container Registry planeras inte för tillfället. Vi rekommenderar att du använder [privata slut punkter](container-registry-private-link.md) i stället.
* Du kan inte använda Azure Portal för att konfigurera tjänstens slut punkter i ett register.
* Endast ett [Azure Kubernetes service-](../aks/intro-kubernetes.md) kluster eller en virtuell Azure- [dator](../virtual-machines/linux/overview.md) kan användas som en värd för att få åtkomst till ett behållar register med hjälp av en tjänst slut punkt. *Andra Azure-tjänster, inklusive Azure Container Instances, stöds inte.*
* Tjänst slut punkter för Azure Container Registry stöds inte i Azure-molnet för amerikanska myndigheter eller Azure Kina.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Förutsättningar

* För att kunna använda Azure CLI-stegen i den här artikeln krävs Azure CLI version 2.0.58 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

* Om du inte redan har ett behållar register kan du skapa en (Premium-nivå krävs) och skicka en exempel avbildning, till exempel `hello-world` från Docker Hub. Använd till exempel [Azure Portal][quickstart-portal] eller [Azure CLI][quickstart-cli] för att skapa ett register. 

* Om du vill begränsa register åtkomsten med hjälp av en tjänst slut punkt i en annan Azure-prenumeration registrerar du resurs leverantören för Azure Container Registry i den prenumerationen. Exempel:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>Konfigurera nätverks åtkomst för registret

I det här avsnittet konfigurerar du behållar registret för att tillåta åtkomst från ett undernät i ett virtuellt Azure-nätverk. Du får anvisningar med hjälp av Azure CLI.

### <a name="add-a-service-endpoint-to-a-subnet"></a>Lägga till en tjänst slut punkt i ett undernät

När du skapar en virtuell dator skapar Azure som standard ett virtuellt nätverk i samma resurs grupp. Namnet på det virtuella nätverket baseras på namnet på den virtuella datorn. Om du till exempel namnger din virtuella dator *myDockerVM* är det virtuella standard nätverks namnet *myDockerVMVNET*, med ett undernät som heter *myDockerVMSubnet*. Verifiera detta genom att använda kommandot [AZ Network VNet List][az-network-vnet-list] :

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
    "Subnet&quot;: &quot;myDockerVMSubnet"
  }
]
```

Använd kommandot [AZ Network VNet Subnet Update][az-network-vnet-subnet-update] för att lägga till en tjänst slut punkt för **Microsoft. ContainerRegistry** i under nätet. Ersätt namnen på ditt virtuella nätverk och undernät i följande kommando:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Använd kommandot [AZ Network VNet Subnet show][az-network-vnet-subnet-show] för att hämta resurs-ID för under nätet. Du behöver detta i ett senare steg för att konfigurera en regel för nätverks åtkomst.

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

### <a name="change-default-network-access-to-registry"></a>Ändra standard nätverks åtkomst till registret

Som standard tillåter ett Azure Container Registry anslutningar från värdar i alla nätverk. Om du vill begränsa åtkomsten till ett valt nätverk ändrar du standard åtgärden till neka åtkomst. Ersätt namnet på registret i följande [AZ ACR Update][az-acr-update] -kommando:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Lägg till nätverks regel i registret

Använd kommandot [AZ ACR Network-Rule Add][az-acr-network-rule-add] för att lägga till en nätverks regel i registret som tillåter åtkomst från den virtuella datorns undernät. Ersätt namnet på behållar registret och resurs-ID: t för under nätet i följande kommando: 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>Verifiera åtkomst till registret

Efter några minuter efter att konfigurationen uppdaterats, verifiera att den virtuella datorn har åtkomst till behållar registret. Skapa en SSH-anslutning till den virtuella datorn och kör kommandot [AZ ACR login][az-acr-login] för att logga in i registret. 

```bash
az acr login --name mycontainerregistry
```

Du kan utföra register åtgärder som att köra `docker pull` för att hämta en exempel avbildning från registret. Ersätt en bild och ett tagg värde som är lämpligt för ditt register, med prefixet för inloggnings Server namnet för registret (alla gemener):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker hämtar avbildningen till den virtuella datorn.

Det här exemplet visar att du kan komma åt den privata behållar registret via nätverks åtkomst regeln. Registret kan dock inte nås från en inloggnings värd som inte har någon konfigurerad nätverks åtkomst regel. Om du försöker logga in från en annan värd med hjälp av `az acr login` kommandot eller `docker login` kommandot ser utdata ut ungefär så här:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Återställ standard register åtkomst

Om du vill återställa registret för att tillåta åtkomst som standard, tar du bort eventuella nätverks regler som är konfigurerade. Ange sedan standard åtgärden för att tillåta åtkomst. 

### <a name="remove-network-rules"></a>Ta bort nätverks regler

Om du vill se en lista över nätverks regler som har kon figurer ATS för registret kör du följande kommando för [AZ ACR Network-Rule List][az-acr-network-rule-list] :

```azurecli
az acr network-rule list --name mycontainerregistry 
```

För varje regel som har kon figurer ATS kör du kommandot [AZ ACR Network-Rule Remove Remove][az-acr-network-rule-remove] för att ta bort den. Exempel:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="allow-access"></a>Tillåt åkomst

Ersätt namnet på registret i följande [AZ ACR Update][az-acr-update] -kommando:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat alla Azure-resurser i samma resurs grupp och inte längre behöver dem, kan du välja att ta bort resurserna med hjälp av ett enda [AZ Group Delete](/cli/azure/group) -kommando:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du begränsar åtkomsten till ett register med en privat slut punkt i ett virtuellt nätverk finns i [Konfigurera Azures privata länk för ett Azure Container Registry](container-registry-private-link.md).
* Om du behöver konfigurera åtkomst regler för registret bakom en klient brand vägg, se [Konfigurera regler för åtkomst till ett Azure Container Registry bakom en brand vägg](container-registry-firewall-access-rules.md).


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
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
