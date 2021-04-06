---
title: Distribuera behållar grupp till Azure Virtual Network
description: Lär dig hur du distribuerar en behållar grupp till ett nytt eller befintligt virtuellt Azure-nätverk med hjälp av kommando rads gränssnittet i Azure.
ms.topic: article
ms.date: 07/02/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: b791d3f37809c2eca53f5a3cd34f7c44dd11ce40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028887"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Distribuera containerinstanser i ett virtuellt Azure-nätverk

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) ger säker, privat nätverk för dina Azure-resurser och lokala resurser. Genom att distribuera behållar grupper till ett virtuellt Azure-nätverk kan dina behållare kommunicera säkert med andra resurser i det virtuella nätverket.

Den här artikeln visar hur du använder kommandot [AZ container Create][az-container-create] i Azure CLI för att distribuera behållar grupper till antingen ett nytt virtuellt nätverk eller ett befintligt virtuellt nätverk. 

För nätverks scenarier och begränsningar, se [scenarier och resurser för virtuella nätverk för Azure Container instances](container-instances-virtual-network-concepts.md).

> [!IMPORTANT]
> Distribution av container grupper till ett virtuellt nätverk är allmänt tillgängligt för Linux-behållare i de flesta regioner där Azure Container Instances är tillgängligt. Mer information finns i [regioner och resurs tillgänglighet][container-regions]. 

Exemplen i den här artikeln är formaterade för bash-gränssnittet. Om du föredrar ett annat gränssnitt, till exempel PowerShell eller kommando tolken, justerar du rad fortsättnings tecknen efter behov.


## <a name="deploy-to-new-virtual-network"></a>Distribuera till nytt virtuellt nätverk

Om du vill distribuera till ett nytt virtuellt nätverk och låta Azure Skapa nätverks resurserna automatiskt, anger du följande när du kör [AZ container Create][az-container-create]:

* Virtuellt nätverksnamn
* Adressprefix för virtuellt nätverk i CIDR-format
* Namn på undernät
* Undernätsprefixets i CIDR-format

Prefixen för det virtuella nätverket och under nätet anger adress utrymmen för det virtuella nätverket respektive undernät. Dessa värden representeras i CIDR-notation (Classless Inter-Domain routing), till exempel `10.0.0.0/16` . Mer information om hur du arbetar med undernät finns i [lägga till, ändra eller ta bort ett virtuellt nätverks under nät](../virtual-network/virtual-network-manage-subnet.md).

När du har distribuerat din första behållar grupp med den här metoden kan du distribuera till samma undernät genom att ange det virtuella nätverket och under nät namnen, eller nätverks profilen som Azure skapar automatiskt åt dig. Eftersom Azure delegerar under nätet till Azure Container Instances kan du *bara* distribuera behållar grupper till under nätet.

### <a name="example"></a>Exempel

Följande kommando för [AZ container Create][az-container-create] anger inställningar för ett nytt virtuellt nätverk och undernät. Ange namnet på en resurs grupp som har skapats i en region där distributioner av container grupper i ett virtuellt nätverk är [tillgängliga](container-instances-region-availability.md). Det här kommandot distribuerar den offentliga Microsoft [ACI-HelloWorld-][aci-helloworld] behållaren som kör en liten Node.js-webbserver som betjänar en statisk webb sida. I nästa avsnitt ska du distribuera en andra behållar grupp till samma undernät och testa kommunikationen mellan de två behållar instanserna.

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

När du distribuerar till ett nytt virtuellt nätverk med hjälp av den här metoden kan distributionen ta några minuter medan nätverks resurserna skapas. Efter den första distributionen slutförs ytterligare distributioner av container grupper till samma undernät snabbare.

## <a name="deploy-to-existing-virtual-network"></a>Distribuera till befintligt virtuellt nätverk

Så här distribuerar du en behållar grupp till ett befintligt virtuellt nätverk:

1. Skapa ett undernät i det befintliga virtuella nätverket, Använd ett befintligt undernät där en behållar grupp redan har distribuerats eller Använd ett befintligt undernät som töms av *alla* andra resurser
1. Distribuera en behållar grupp med [AZ container Create][az-container-create] och ange något av följande:
   * Namn på virtuellt nätverk och undernät
   * Resurs-ID för virtuellt nätverk och resurs-ID för under nätet som tillåter användning av ett virtuellt nätverk från en annan resurs grupp
   * Namn eller ID för nätverks profil, som du kan hämta med [AZ Network Profile List][az-network-profile-list]

### <a name="example"></a>Exempel

I följande exempel distribueras en andra behållar grupp till samma undernät som skapats tidigare och verifierar kommunikationen mellan de två behållar instanserna.

Börja med att hämta IP-adressen för den första behållar gruppen som du har distribuerat, *AppContainer*:

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

Utdata visar behållar gruppens IP-adress i det privata under nätet. Exempel:

```console
10.0.0.4
```

Ange nu `CONTAINER_GROUP_IP` till den IP-adress som du hämtade med `az container show` kommandot och kör följande `az container create` kommando. Den här andra behållaren, *commchecker*, kör en Alpine Linux-baserad avbildning och körs `wget` mot den första behållar gruppens privata undernät-IP-adress.

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

När den här andra behållar distributionen har slutförts hämtar du dess loggar så att du kan se resultatet av `wget` kommandot som körs:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Om den andra behållaren har kommunicerat med det första, ser utdata ut ungefär så här:

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

Logg resultatet bör visa att det `wget` gick att ansluta och hämta index filen från den första behållaren med dess privata IP-adress på det lokala under nätet. Nätverks trafiken mellan de två behållar grupperna fanns kvar i det virtuella nätverket.

### <a name="example---yaml"></a>Exempel – YAML

Du kan också distribuera en behållar grupp till ett befintligt virtuellt nätverk med hjälp av en YAML-fil, en [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)eller en annan programmerings metod, till exempel med python SDK. 

Om du till exempel använder en YAML-fil kan du distribuera till ett virtuellt nätverk med ett undernät delegerat till Azure Container Instances. Ange följande egenskaper:

* `ipAddress`: Inställningarna för den privata IP-adressen för behållar gruppen.
  * `ports`: Portarna som ska öppnas, om det finns några.
  * `protocol`: Protokollet (TCP eller UDP) för den öppnade porten.
* `networkProfile`: Nätverks inställningar för det virtuella nätverket och under nätet.
  * `id`: Fullständig Resource Manager-resurs-ID för `networkProfile` .

Om du vill hämta ID för nätverks profilen kör du kommandot [AZ Network Profile List][az-network-profile-list] och anger namnet på den resurs grupp som innehåller det virtuella nätverket och det delegerade under nätet.

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

Exempel på utdata:

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

När du har nätverks profil-ID: t kopierar du följande YAML till en ny fil med namnet *VNet-Deploy-ACI. yaml*. `networkProfile`Ersätt `id` värdet med det ID som du precis hämtade under och spara sedan filen. Den här YAML skapar en behållar grupp med namnet *appcontaineryaml* i ditt virtuella nätverk.

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

Distribuera behållar gruppen med kommandot [AZ container Create][az-container-create] , och ange namnet på yaml-filen för `--file` parametern:

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

När distributionen har slutförts kör du kommandot [AZ container show][az-container-show] för att visa dess status. Exempel på utdata:

```console
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="delete-container-instances"></a>Ta bort behållar instanser

När du är klar med de behållar instanser som du har skapat tar du bort dem med följande kommandon:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Ta bort nätverks resurser

Den här funktionen kräver för närvarande flera ytterligare kommandon för att ta bort nätverks resurserna som du skapade tidigare. Om du använde exempel kommandona i föregående avsnitt i den här artikeln för att skapa ditt virtuella nätverk och undernät kan du använda följande skript för att ta bort dessa nätverks resurser. Skriptet förutsätter att din resurs grupp innehåller ett enda virtuellt nätverk med en enda nätverks profil.

Ange `RES_GROUP` variabeln till namnet på den resurs grupp som innehåller det virtuella nätverk och undernät som ska tas bort innan du kör skriptet. Uppdatera namnet på det virtuella nätverket om du inte använde det `aci-vnet` föreslagna namnet tidigare. Skriptet är formaterat för bash-gränssnittet. Om du föredrar ett annat gränssnitt, t. ex. PowerShell eller kommando tolken, måste du justera variabel tilldelning och-åtkomst på motsvarande sätt.

> [!WARNING]
> Det här skriptet tar bort resurser! Den tar bort det virtuella nätverket och alla undernät som det innehåller. Se till att du inte längre behöver *någon* av resurserna i det virtuella nätverket, inklusive eventuella undernät som den innehåller, innan du kör skriptet. **De här resurserna kan inte återställas när de har** tagits bort.

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

Om du vill distribuera ett nytt virtuellt nätverk, undernät, nätverks profil och behållar grupp med hjälp av en Resource Manager-mall, se [skapa en Azure Container Group med VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
[container-regions]: container-instances-region-availability.md
