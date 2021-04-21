---
title: Distribuera en containergrupp till ett virtuellt Azure-nätverk
description: Lär dig hur du distribuerar en containergrupp till ett nytt eller befintligt virtuellt Azure-nätverk med hjälp av Azure-kommandoradsgränssnittet.
ms.topic: article
ms.date: 07/02/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 44be66957aa745179ffe4cd00db75f1d47237dfc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771055"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Distribuera containerinstanser i ett virtuellt Azure-nätverk

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) tillhandahåller säkra, privata nätverk för dina Azure-resurser och lokala resurser. Genom att distribuera containergrupper till ett virtuellt Azure-nätverk kan dina containrar kommunicera säkert med andra resurser i det virtuella nätverket.

Den här artikeln visar hur du använder [kommandot az container create][az-container-create] i Azure CLI för att distribuera containergrupper till antingen ett nytt virtuellt nätverk eller ett befintligt virtuellt nätverk. 

Nätverksscenarier och begränsningar finns i [Scenarier och resurser för virtuella nätverk för Azure Container Instances](container-instances-virtual-network-concepts.md).

> [!IMPORTANT]
> Distribution av containergrupper till ett virtuellt nätverk är allmänt tillgängligt för Linux-containrar, i de flesta regioner där Azure Container Instances är tillgänglig. Mer information finns i [Regioner och resurstillgänglighet.][container-regions] 

Exemplen i den här artikeln är formaterade för Bash-gränssnittet. Om du föredrar ett annat gränssnitt, till exempel PowerShell eller kommandotolken, justerar du rad fortsättningstecken därefter.


## <a name="deploy-to-new-virtual-network"></a>Distribuera till nytt virtuellt nätverk

Om du vill distribuera till ett nytt virtuellt nätverk och azure skapar nätverksresurserna åt dig automatiskt anger du följande när du kör [az container create][az-container-create]:

* Virtuellt nätverksnamn
* Adressprefix för virtuellt nätverk i CIDR-format
* Namn på undernät
* Adressprefix för undernät i CIDR-format

Adressprefixen för det virtuella nätverket och undernätet anger adressutrymmena för det virtuella nätverket respektive undernätet. Dessa värden representeras i CIDR-notationen (Classless Inter-Domain Routing), till exempel `10.0.0.0/16` . Mer information om hur du arbetar med undernät finns i [Lägga till, ändra eller ta bort ett undernät för virtuellt nätverk.](../virtual-network/virtual-network-manage-subnet.md)

När du har distribuerat din första containergrupp med den här metoden kan du distribuera till samma undernät genom att ange det virtuella nätverket och undernätsnamnen, eller nätverksprofilen som Azure skapar automatiskt åt dig. Eftersom Azure delegerar undernätet till Azure Container Instances kan du endast *distribuera* containergrupper till undernätet.

### <a name="example"></a>Exempel

Följande az [container create-kommando][az-container-create] anger inställningar för ett nytt virtuellt nätverk och undernät. Ange namnet på en resursgrupp som har skapats i en region där distributioner av containergrupper i ett virtuellt nätverk är [tillgängliga.](container-instances-region-availability.md) Det här kommandot distribuerar den offentliga Microsoft [aci-helloworld-containern][aci-helloworld] som kör en liten Node.js som betjänar en statisk webbsida. I nästa avsnitt distribuerar du en andra containergrupp till samma undernät och testar kommunikationen mellan de två containerinstanserna.

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

När du distribuerar till ett nytt virtuellt nätverk med den här metoden kan distributionen ta några minuter medan nätverksresurserna skapas. Efter den första distributionen slutförs ytterligare distributioner av containergrupper till samma undernät snabbare.

## <a name="deploy-to-existing-virtual-network"></a>Distribuera till befintligt virtuellt nätverk

Så här distribuerar du en containergrupp till ett befintligt virtuellt nätverk:

1. Skapa ett undernät i ditt befintliga virtuella nätverk, använd ett befintligt undernät där en containergrupp redan har distribuerats eller använd ett befintligt undernät som töms *på alla* andra resurser
1. Distribuera en containergrupp med [az container create][az-container-create] och ange något av följande:
   * Namn på virtuellt nätverk och undernätsnamn
   * Resurs-ID för virtuellt nätverk och resurs-ID för undernät, vilket gör att du kan använda ett virtuellt nätverk från en annan resursgrupp
   * Nätverksprofilens namn eller ID, som du kan hämta med [hjälp av az network profile list][az-network-profile-list]

### <a name="example"></a>Exempel

I följande exempel distribueras en andra containergrupp till samma undernät som skapades tidigare och verifierar kommunikationen mellan de två containerinstanserna.

Hämta först IP-adressen för den första containergruppen som du distribuerade, *appcontainer:*

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

Utdata visar IP-adressen för containergruppen i det privata undernätet. Exempel:

```console
10.0.0.4
```

Ange nu `CONTAINER_GROUP_IP` till den IP-adress som du hämtade `az container show` med kommandot och kör följande `az container create` kommando. Den här andra *containern, commchecker,* kör en Alpine Linux-baserad avbildning och körs mot den första `wget` containergruppens privata undernäts IP-adress.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-address>

az container create \
  --resource-group myResourceGroup \
  --name commchecker \
  --image alpine:3.5 \
  --command-line "wget $CONTAINER_GROUP_IP" \
  --restart-policy never \
  --vnet aci-vnet \
  --subnet aci-subnet
```

När den här andra containerdistributionen har slutförts hämtar du dess loggar så att du kan se utdata `wget` från kommandot som den körde:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Om den andra containern kommunicerade med den första ser utdata ut ungefär så här:

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

Loggutdata bör visa att kunde ansluta och ladda ned indexfilen från den första containern med dess `wget` privata IP-adress i det lokala undernätet. Nätverkstrafiken mellan de två containergrupperna fanns kvar i det virtuella nätverket.

### <a name="example---yaml"></a>Exempel – YAML

Du kan också distribuera en containergrupp till ett befintligt virtuellt nätverk med hjälp av en YAML-fil, [en Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)eller en annan programmatisk metod som med Python SDK. 

När du till exempel använder en YAML-fil kan du distribuera till ett virtuellt nätverk med ett undernät som delegerats till Azure Container Instances. Ange följande egenskaper:

* `ipAddress`: Inställningarna för privata IP-adresser för containergruppen.
  * `ports`: Portarna som ska öppnas, om några.
  * `protocol`: Protokollet (TCP eller UDP) för den öppna porten.
* `networkProfile`: Nätverksinställningar för det virtuella nätverket och undernätet.
  * `id`: Den fullständiga Resource Manager resurs-ID för `networkProfile` .

Hämta nätverksprofilens ID genom att köra kommandot [az network profile list][az-network-profile-list] och ange namnet på den resursgrupp som innehåller ditt virtuella nätverk och delegerade undernät.

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

Exempel på utdata:

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

När du har nätverksprofils-ID:t kopierar du följande YAML till en ny fil med namnet *vnet-deploy-aci.yaml*. Under `networkProfile` ersätter du `id` värdet med ID:t som du precis hämtade och sparar sedan filen. Den här YAML-koden skapar en *containergrupp med namnet appcontaineryaml* i ditt virtuella nätverk.

```YAML
apiVersion: '2019-12-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Distribuera containergruppen med kommandot [az container create][az-container-create] och ange YAML-filnamnet för `--file` parametern:

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

När distributionen är klar kör du [kommandot az container show][az-container-show] för att visa dess status. Exempel på utdata:

```console
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="delete-container-instances"></a>Ta bort containerinstanser

När du är klar med de containerinstanser som du skapade tar du bort dem med följande kommandon:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Ta bort nätverksresurser

Den här funktionen kräver för närvarande flera ytterligare kommandon för att ta bort nätverksresurserna som du skapade tidigare. Om du använde exempelkommandona i föregående avsnitt i den här artikeln för att skapa ditt virtuella nätverk och undernät kan du använda följande skript för att ta bort dessa nätverksresurser. Skriptet förutsätter att resursgruppen innehåller ett enda virtuellt nätverk med en enda nätverksprofil.

Innan du kör skriptet anger du variabeln till namnet på resursgruppen som innehåller det virtuella nätverket och `RES_GROUP` undernätet som ska tas bort. Uppdatera namnet på det virtuella nätverket om du inte använder det `aci-vnet` namn som föreslogs tidigare. Skriptet är formaterat för Bash-gränssnittet. Om du föredrar ett annat gränssnitt, till exempel PowerShell eller kommandotolken, måste du justera variabeltilldelning och åtkomstorer därefter.

> [!WARNING]
> Det här skriptet tar bort resurser! Den tar bort det virtuella nätverket och alla undernät som det innehåller. Se till att du inte längre behöver *några* av resurserna i det virtuella nätverket, inklusive eventuella undernät som det innehåller, innan du kör det här skriptet. När de här **resurserna har tagits bort kan de inte återställas.**

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar ett nytt virtuellt nätverk, undernät, nätverksprofil och containergrupp med hjälp av en Resource Manager mall finns i Skapa en [Azure-containergrupp med VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-network-vnet-create]: /cli/azure/network/vnet#az_network_vnet_create
[az-network-profile-list]: /cli/azure/network/profile#az_network_profile_list
[container-regions]: container-instances-region-availability.md
