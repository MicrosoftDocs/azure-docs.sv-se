---
title: 'CLI: Distribuera privat slut punkt för webb program med Azure CLI'
description: Lär dig hur du använder Azure CLI för att distribuera privat slut punkt för din webbapp
author: ericgre
ms.assetid: a56faf72-7237-41e7-85ce-da8346f2bcaa
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/06/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 21f937d95c6cd0dafa27daae50d7a74648af7bdc
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006114"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-cli"></a>Skapa en App Service-app och distribuera privat slut punkt med hjälp av Azure CLI

Det här exempel skriptet skapar en app i App Service med dess relaterade resurser och distribuerar sedan en privat slut punkt.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - I den här självstudien krävs version 2.0.28 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa en resurs måste du skapa en resurs grupp som värd för webbappen, Virtual Network och andra nätverks komponenter. Skapa en resursgrupp med [az group create](/cli/azure/group). I det här exemplet skapas en resurs grupp med namnet *myResourceGroup* på *francecentral* -platsen:

```azurecli-interactive
az group create --name myResourceGroup --location francecentral 
```

## <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

Du måste skapa en App Service plan som värd för din webbapp.
Skapa en App Service plan med [AZ AppService plan Create](/cli/azure/appservice/plan#az-appservice-plan-create).
I det här exemplet skapas App Service plan med namnet *myAppServicePlan* på *francecentral* -platsen med *P1V2* SKU och endast en anställd: 

```azurecli-interactive
az appservice plan create \
--name myAppServicePlan \
--resource-group myResourceGroup \
--location francecentral \
--sku P1V2 \
--number-of-workers 1
```

## <a name="create-a-web-app"></a>Skapa en webbapp

Nu när du har ett App Services plan kan du distribuera en webbapp.
Skapa en webbapp med [AZ AppService plan Create] (/CLI/Azure/webapp? View = Azure-CLI-senaste # AZ-webapp-Create.
I det här exemplet skapas en webbapp med namnet *mySiteName* i planen med namnet *myAppServicePlan*

```azurecli-interactive
az webapp create \
--name mySiteName \
--resource-group myResourceGroup \
--plan myAppServicePlan
```

## <a name="create-a-vnet"></a>Skapa ett virtuellt nätverk

Skapa en Virtual Network med [AZ Network VNet Create](/cli/azure/network/vnet). I det här exemplet skapas en standard Virtual Network med namnet *myVNet* med ett undernät med namnet *undernät*:

```azurecli-interactive
az network vnet create \
--name myVNet \
--resource-group myResourceGroup \
--location francecentral \
--address-prefixes 10.8.0.0/16 \
--subnet-name mySubnet \
--subnet-prefixes 10.8.100.0/24
```

## <a name="configure-the-subnet"></a>Konfigurera under nätet 

Du måste uppdatera under nätet för att inaktivera nätverks principer för privata slut punkter. Uppdatera en under näts konfiguration med namnet *mitt undernät* med [AZ Network VNet Subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
--name mySubnet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--disable-private-endpoint-network-policies true
```

## <a name="create-the-private-endpoint"></a>Skapa den privata slut punkten

Skapa den privata slut punkten för din webbapp med [AZ Network Private-slutpunkt Create](/cli/azure/network/private-endpoint). I det här exemplet skapas en privat slut punkt med namnet *myPrivateEndpoint* i VNet- *MyVNet* i under *nätet under nätet med en* anslutning med namnet *myConnectionName* till resurs-ID: t för min webbapp/Subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/Sites/myWebApp, grupp parametern är *platser* för webbapp. 

```azurecli-interactive
az network private-endpoint create \
--name myPrivateEndpoint \
--resource-group myResourceGroup \
--vnet-name myVNet \
--subnet mySubnet \
--connection-name myConnectionName \
--private-connection-resource-id /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp \
--group-id sites
```

## <a name="configure-the-private-zone"></a>Konfigurera den privata zonen

I slutet måste du skapa en privat DNS-zon med namnet *privatelink.azurewebsites.net* länkad till VNet för att matcha DNS-namnet på webbappen.


```azurecli-interactive
az network private-dns zone create \
--name privatelink.azurewebsites.net \
--resource-group myResourceGroup

az network private-dns link vnet create \
--name myDNSLink \
--resource-group myResourceGroup \
--registration-enabled false \
--virtual-network myVNet \
--zone-name privatelink.azurewebsites.net

az network private-endpoint dns-zone-group create \
--name myZoneGroup \
--resource-group myResourceGroup \
--endpoint-name myPrivateEndpoint \
--private-dns-zone privatelink.azurewebsites.net \
--zone-name privatelink.azurewebsites.net
```






[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>Nästa steg

- Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
- Fler App Service CLI-skriptexempel finns i [dokumentationen för Azure App Service](../samples-cli.md).
