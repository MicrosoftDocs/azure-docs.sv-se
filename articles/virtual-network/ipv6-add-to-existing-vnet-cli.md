---
title: Lägga till IPv6 i ett IPv4-program i ett virtuellt Azure-nätverk – Azure CLI
titlesuffix: Azure Virtual Network
description: Den här artikeln visar hur du distribuerar IPv6-adresser till ett befintligt program i ett virtuellt Azure-nätverk med Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 5835ea4d80f9c4111b76672facc4a0250ae0079a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769867"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>Lägga till IPv6 i ett IPv4-program i ett virtuellt Azure-nätverk – Azure CLI

Den här artikeln visar hur du lägger till IPv6-adresser i ett program som använder en offentlig IP-adress för IPv4 i ett virtuellt Azure-nätverk för en Standard Load Balancer med Azure CLI. Uppgraderingen på plats innehåller ett virtuellt nätverk och undernät, en Standard Load Balancer med IPv4 + IPV6-konfigurationer, virtuella datorer med nätverkskort som har IPv4 + IPv6-konfigurationer, nätverkssäkerhetsgrupp och offentliga IP-adresser.

## <a name="prerequisites"></a>Förutsättningar

- Den här artikeln förutsätter att du har distribuerat Standard Load Balancer enligt beskrivningen i [Snabbstart: Skapa en Standard Load Balancer – Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Den här artikeln kräver version 2.0.28 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-ipv6-addresses"></a>Skapa IPv6-adresser

Skapa en offentlig IPv6-adress med [az network public-ip create](/cli/azure/network/public-ip) för Standard Load Balancer. I följande exempel skapas en offentlig IP-adress för IPv6 *PublicIP_v6* i *resursgruppen myResourceGroupSLB:*

```azurecli-interactive
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Konfigurera IPv6-lastbalanserare i frontend

Konfigurera lastbalanseraren med den nya IPv6 [IP-adressen med az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) på följande sätt:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>Konfigurera en IPv6-lastbalanserare för en backend-pool

Skapa backend-poolen för nätverkskort med IPv6-adresser med [az network lb address-pool create](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create) på följande sätt:

```azurecli-interactive
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>Konfigurera regler för IPv6-lastbalanserare

Skapa regler för IPv6-lastbalanserare [med az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create).

```azurecli-interactive
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>Lägga till IPv6-adressintervall

Lägg till IPv6-adressintervall i det virtuella nätverket och undernätet som är värd för lastbalanseraren enligt följande:

```azurecli-interactive
az network vnet update \
--name myVnet  \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "fd00:db8:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "fd00:db8:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>Lägga till IPv6-konfiguration till nätverkskort

Konfigurera de virtuella datorernas nätverkskort med en IPv6-adress med [az network nic ip-config create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) på följande sätt:

```azurecli-interactive
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visa virtuella IPv6-nätverk med dubbla stackar i Azure Portal

Du kan visa det virtuella nätverket med dubbla IPv6-stackar Azure Portal följande:
1. I portalens sökfält anger du *myVnet*.
2. När **myVnet** visas i sökresultatet väljer du det. Då startas översiktssidan **för** det virtuella nätverket med dubbla stackar med namnet *myVNet*. Det virtuella nätverket med dubbla stackar visar de tre nätverkskorten med både IPv4- och IPv6-konfigurationer som finns i undernätet med dubbla stackar med namnet *mySubnet.*

  ![Virtuellt IPv6-nätverk med dubbla stackar i Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive
az group delete --name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du uppdaterat en befintlig Standard Load Balancer med en IP-konfiguration för IPv4-frontend till en dubbel stackkonfiguration (IPv4 och IPv6). Du har också lagt till IPv6-konfigurationer till nätverkskorten för de virtuella datorerna i serverpoolen. Mer information om IPv6-stöd i virtuella Azure-nätverk finns [i Vad är IPv6 för Azure Virtual Network?](ipv6-overview.md)
