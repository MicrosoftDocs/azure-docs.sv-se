---
title: 'CLI: Distribuera privat slutpunkt för webbapp med Azure CLI'
description: Lär dig hur du använder Azure CLI för att distribuera privat slutpunkt för din webbapp
author: ericgre
ms.assetid: a56faf72-7237-41e7-85ce-da8346f2bcaa
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/06/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 3c8d0927c3fb74c52e54ceb5ff8ba5c0361c4f46
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787861"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-cli"></a>Skapa en App Service-app och distribuera privat slutpunkt med Hjälp av Azure CLI

Det här exempelskriptet skapar en app App Service med dess relaterade resurser och distribuerar sedan en privat slutpunkt.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Den här självstudien kräver version 2.0.28 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa en resurs måste du skapa en resursgrupp som ska vara värd för webbappen, Virtual Network och andra nätverkskomponenter. Skapa en resursgrupp med [az group create](/cli/azure/group). I det här exemplet skapas en resursgrupp *med namnet myResourceGroup* på *platsen francecentral:*

```azurecli-interactive
az group create --name myResourceGroup --location francecentral 
```

## <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

Du måste skapa en App Service som värd för webbappen.
Skapa en App Service plan med [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create).
Det här exemplet App Service plan med namnet *myAppServicePlan* på platsen *frankrike, centrala* med *SKU:n P1V2* och endast en arbetsroll: 

```azurecli-interactive
az appservice plan create \
--name myAppServicePlan \
--resource-group myResourceGroup \
--location francecentral \
--sku P1V2 \
--number-of-workers 1
```

## <a name="create-a-web-app"></a>Skapa en webbapp

Nu när du har en App Service kan du distribuera en webbapp.
Skapa en webbapp med [az appservice plan create](/cli/azure/webapp#az_webapp_create.
I det här exemplet skapas en webbapp med *namnet mySiteName* i planen *med namnet myAppServicePlan*

```azurecli-interactive
az webapp create \
--name mySiteName \
--resource-group myResourceGroup \
--plan myAppServicePlan
```

## <a name="create-a-vnet"></a>Skapa ett virtuellt nätverk

Skapa en Virtual Network med [az network vnet create](/cli/azure/network/vnet). I det här exemplet skapas en Virtual Network med *namnet myVNet* med ett undernät med namnet *mySubnet*:

```azurecli-interactive
az network vnet create \
--name myVNet \
--resource-group myResourceGroup \
--location francecentral \
--address-prefixes 10.8.0.0/16 \
--subnet-name mySubnet \
--subnet-prefixes 10.8.100.0/24
```

## <a name="configure-the-subnet"></a>Konfigurera undernätet 

Du måste uppdatera undernätet för att inaktivera nätverksprinciper för privata slutpunkter. Uppdatera en undernätskonfiguration med *namnet mySubnet* med [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update):

```azurecli-interactive
az network vnet subnet update \
--name mySubnet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--disable-private-endpoint-network-policies true
```

## <a name="create-the-private-endpoint"></a>Skapa den privata slutpunkten

Skapa den privata slutpunkten för webbappen med [az network private-endpoint create](/cli/azure/network/private-endpoint). I det här exemplet skapas en privat slutpunkt med namnet  *myPrivateEndpoint* i det virtuella nätverket *myVNet* i undernätet *mySubnet* med en anslutning med namnet *myConnectionName* till resurs-ID:t för min webbapp /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp. Gruppparametern är platser för webbapptypen. 

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

I slutet måste du skapa en privat DNS-zon med *namnet privatelink.azurewebsites.net* länkad till det virtuella nätverket för att matcha DNS-namnet på webbappen.


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
