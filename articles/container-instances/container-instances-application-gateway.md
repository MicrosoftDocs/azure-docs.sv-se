---
title: Statisk IP-adress för container grupp
description: Skapa en behållar grupp i ett virtuellt nätverk och Använd en Azure Application Gateway för att exponera en statisk IP-adress för klient delen i en behållares webbapp
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 0131780fdb04a71837d5ae9bf5498bf2bd499f8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98035061"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Exponera en statisk IP-adress för en behållar grupp

Den här artikeln visar ett sätt att exponera en statisk, offentlig IP-adress för en [behållar grupp](container-instances-container-groups.md) med hjälp av en Azure [Application Gateway](../application-gateway/overview.md). Följ de här stegen när du behöver en statisk start punkt för en extern, container app som körs i Azure Container Instances. 

I den här artikeln använder du Azure CLI för att skapa resurser för det här scenariot:

* Ett virtuellt Azure-nätverk
* En behållar grupp distribuerat [i det virtuella nätverket som är](container-instances-vnet.md) värd för en liten webbapp
* En Programgateway med en offentlig IP-adress för klient delen, en lyssnare som är värd för en webbplats på gatewayen och en väg till behållare gruppen för Server delen

Så länge programgatewayen körs och behållar gruppen visar en stabil privat IP-adress i nätverkets delegerade undernät, är behållar gruppen tillgänglig på den här offentliga IP-adressen.

> [!NOTE]
> Azure debiterar för en Programgateway baserat på den tid som gatewayen är etablerad och tillgänglig, samt den mängd data som den bearbetar. Se [prissättning](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Skapa det virtuella nätverket

I ett typiskt fall kanske du redan har ett virtuellt Azure-nätverk. Om du inte har ett kan du skapa en som visas med följande exempel kommandon. Det virtuella nätverket behöver separata undernät för programgatewayen och behållar gruppen.

Skapa en Azure-resurs grupp om du behöver det. Exempel:

```azureci
az group create --name myResourceGroup --location eastus
```

Skapa ett virtuellt nätverk med kommandot [AZ Network VNet Create][az-network-vnet-create] . Det här kommandot skapar *myAGSubnet* -undernätet i nätverket.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Använd kommandot [AZ Network VNet Subnet Create][az-network-vnet-subnet-create] för att skapa ett undernät för gruppen Server dels behållare. Här heter den namnet *myACISubnet*.

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Använd kommandot [AZ Network Public-IP Create][az-network-public-ip-create] för att skapa en statisk offentlig IP-resurs. I ett senare steg är den här adressen konfigurerad som klient delen av programgatewayen.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Skapa containergrupp

Kör följande [AZ container Create][az-container-create] för att skapa en behållar grupp i det virtuella nätverk som du konfigurerade i föregående steg. 

Gruppen distribueras i *myACISubnet* -undernätet och innehåller en enda instans med namnet *AppContainer* som hämtar `aci-helloworld` avbildningen. Som du ser i andra artiklar i dokumentationen packar den här avbildningen en liten webbapp som skrivits i Node.js som hanterar en statisk HTML-sida. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

När den har distribuerats tilldelas behållar gruppen en privat IP-adress i det virtuella nätverket. Kör till exempel följande [AZ container show][az-container-show] -kommando för att hämta gruppens IP-adress:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

Utdata liknar följande: `10.0.2.4`.

För användning i ett senare steg sparar du IP-adressen i en miljö variabel:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

> [!IMPORTANT]
> Om behållar gruppen har stoppats, startats eller startats om, kan behållar gruppens privata IP-adress komma att ändras. Om detta händer måste du uppdatera konfigurationen för programgatewayen.

## <a name="create-application-gateway"></a>Skapa Application Gateway

Skapa en Application Gateway i det virtuella nätverket genom att följa stegen i snabb starten för [Application Gateway](../application-gateway/quick-create-cli.md). Följande [AZ Network Application-Gateway Create-][az-network-application-gateway-create] kommando skapar en gateway med en offentlig IP-adress för klient delen och en väg till behållar gruppen. Mer information om Gateway-inställningar finns i [Application Gateway-dokumentationen](../application-gateway/index.yml) .

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


Det kan ta upp till 15 minuter för Azure att skapa programgatewayen. 

## <a name="test-public-ip-address"></a>Testa offentlig IP-adress
  
Nu kan du testa åtkomst till webbappen som körs i behållar gruppen bakom programgatewayen.

Kör kommandot [AZ Network Public-IP show][az-network-public-ip-show] för att hämta gatewayens offentliga IP-adress:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

Utdata är en offentlig IP-adress som liknar: `52.142.18.133` .

Om du vill visa den aktiva webbappen när den har kon figurer ATS navigerar du till gatewayens offentliga IP-adress i webbläsaren. Lyckad åtkomst liknar:

![Skärmbild från webbläsaren som visar ett program som körs i en instans av Azure-containern](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Nästa steg

* Se en [snabb starts mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) för att skapa en behållar grupp med en WordPress-container instans som en backend-server bakom en Programgateway.
* Du kan också konfigurera en Programgateway med ett certifikat för SSL-avslutning. Se [översikten](../application-gateway/ssl-overview.md) och [självstudien](../application-gateway/create-ssl-portal.md).
* Beroende på ditt scenario bör du överväga att använda andra lösningar för belastnings utjämning i Azure med Azure Container Instances. Använd till exempel [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) för att distribuera trafik över flera behållar instanser och över flera regioner. Se det här [blogg inlägget](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
