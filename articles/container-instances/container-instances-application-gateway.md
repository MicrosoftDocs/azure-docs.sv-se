---
title: Statisk IP-adress för containergrupp
description: Skapa en containergrupp i ett virtuellt nätverk och använd en Azure-programgateway för att exponera en statisk IP-adress på serversidan för en containerwebbapp
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: de9e06b457a9ea5485fe268bd2b7cf206f0a6c0e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790949"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Exponera en statisk IP-adress för en containergrupp

Den här artikeln visar ett sätt att exponera en statisk, offentlig IP-adress för [en containergrupp med](container-instances-container-groups.md) hjälp av en Azure [Application Gateway](../application-gateway/overview.md). Följ dessa steg när du behöver en statisk startpunkt för en externt riktad containerapp som körs i Azure Container Instances. 

I den här artikeln använder du Azure CLI för att skapa resurserna för det här scenariot:

* Ett virtuellt Azure-nätverk
* En containergrupp som distribueras [i det virtuella nätverket som](container-instances-vnet.md) är värd för en liten webbapp
* En programgateway med en offentlig IP-adress för frontend, en lyssnare som ska vara värd för en webbplats på gatewayen och en väg till containergruppen i backend

Så länge programgatewayen körs och containergruppen exponerar en stabil privat IP-adress i nätverkets delegerade undernät är containergruppen tillgänglig på den här offentliga IP-adressen.

> [!NOTE]
> Azure debiterar för en programgateway baserat på hur lång tid som gatewayen har etablerats och är tillgänglig, samt mängden data som den bearbetar. Se [prissättning](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Skapa det virtuella nätverket

I ett typiskt fall kanske du redan har ett virtuellt Azure-nätverk. Om du inte har något kan du skapa ett som visas med följande exempelkommandon. Det virtuella nätverket behöver separata undernät för programgatewayen och containergruppen.

Om du behöver en resursgrupp skapar du en Azure-resursgrupp. Exempel:

```azureci
az group create --name myResourceGroup --location eastus
```

Skapa ett virtuellt nätverk med [kommandot az network vnet][az-network-vnet-create] create. Det här kommandot skapar *undernätet myAGSubnet* i nätverket.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Använd kommandot [az network vnet subnet create][az-network-vnet-subnet-create] för att skapa ett undernät för backend-containergruppen. Här heter den *myACISubnet*.

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Använd kommandot [az network public-ip create för att][az-network-public-ip-create] skapa en statisk offentlig IP-resurs. I ett senare steg konfigureras den här adressen som programgatewayens frontend.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Skapa containergrupp

Kör följande az [container create för][az-container-create] att skapa en containergrupp i det virtuella nätverk som du konfigurerade i föregående steg. 

Gruppen distribueras i undernätet *myACISubnet* och innehåller en enda instans med namnet *appcontainer* som hämtar avbildningen. `aci-helloworld` Som du ser i andra artiklar i dokumentationen paketerar den här avbildningen en liten webbapp som skrivits i Node.js fungerar som en statisk HTML-sida. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

När containergruppen har distribuerats tilldelas den en privat IP-adress i det virtuella nätverket. Kör till exempel följande [az container show-kommando][az-container-show] för att hämta gruppens IP-adress:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

Utdata liknar följande: `10.0.2.4`.

Spara IP-adressen i en miljövariabel för användning i ett senare steg:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

> [!IMPORTANT]
> Om containergruppen stoppas, startas eller startas om kan containergruppens privata IP-adress ändras. Om detta inträffar måste du uppdatera konfigurationen av programgatewayen.

## <a name="create-application-gateway"></a>Skapa Application Gateway

Skapa en programgateway i det virtuella nätverket genom att följa stegen i [snabbstarten för Application Gateway.](../application-gateway/quick-create-cli.md) Följande kommando [az network application-gateway create][az-network-application-gateway-create] skapar en gateway med en offentlig IP-adress för frontend och en väg till containergruppen i backend. Se Application Gateway [för](../application-gateway/index.yml) mer information om gatewayinställningarna.

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
  
Nu kan du testa åtkomsten till webbappen som körs i containergruppen bakom programgatewayen.

Kör kommandot [az network public-ip show för][az-network-public-ip-show] att hämta gatewayens offentliga IP-adress på frontend-sidan:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

Utdata är en offentlig IP-adress som liknar: `52.142.18.133` .

Om du vill visa webbappen som körs när den har konfigurerats navigerar du till gatewayens offentliga IP-adress i webbläsaren. Lyckad åtkomst liknar följande:

![Skärmbild från webbläsaren som visar ett program som körs i en instans av Azure-containern](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Nästa steg

* Se en [snabbstartsmall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) för att skapa en containergrupp med en WordPress-containerinstans som en serverserver bakom en programgateway.
* Du kan också konfigurera en programgateway med ett certifikat för SSL-avslutning. Se [översikten](../application-gateway/ssl-overview.md) och [självstudien](../application-gateway/create-ssl-portal.md).
* Beroende på ditt scenario bör du överväga att använda andra Azure-lösningar för belastningsutjämning med Azure Container Instances. Använd till exempel [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) för att distribuera trafik över flera containerinstanser och över flera regioner. Se det [här blogginlägget.](https://aaronmsft.com/posts/azure-container-instances/)

[az-network-vnet-create]:  /cli/azure/network/vnet#az_network_vnet_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az_network_public_ip_show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az_network-application-gateway-create
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
