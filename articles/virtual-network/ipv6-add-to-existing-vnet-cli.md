---
title: Lägg till IPv6 i ett IPv4-program i Azure Virtual Network – Azure CLI
titlesuffix: Azure Virtual Network
description: Den här artikeln visar hur du distribuerar IPv6-adresser till ett befintligt program i Azure Virtual Network med Azure CLI.
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
ms.openlocfilehash: 9a321687a755f8a3d6e6d9139138d61c58764ef4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932606"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>Lägg till IPv6 i ett IPv4-program i Azure Virtual Network – Azure CLI

Den här artikeln visar hur du lägger till IPv6-adresser i ett program som använder IPv4 offentlig IP-adress i ett virtuellt Azure-nätverk för en Standard Load Balancer med hjälp av Azure CLI. Uppgradering på plats innehåller ett virtuellt nätverk och undernät, en Standard Load Balancer med konfigurationer för IPv4 + IPV6-klient, virtuella datorer med nätverkskort som har en IPv4 + IPv6-konfiguration, nätverks säkerhets grupp och offentliga IP-adresser.

## <a name="prerequisites"></a>Förutsättningar

- I den här artikeln förutsätter vi att du har distribuerat en Standard Load Balancer enligt beskrivningen i [snabb start: skapa en standard Load Balancer-Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Den här artikeln kräver version 2.0.28 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-ipv6-addresses"></a>Skapa IPv6-adresser

Skapa en offentlig IPv6-adress med [AZ Network Public-IP Create](/cli/azure/network/public-ip) för din standard Load Balancer. I följande exempel skapas en offentlig IPv6-IP-adress med namnet *PublicIP_v6* i resurs gruppen *myResourceGroupSLB* :

```azurecli-interactive
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Konfigurera IPv6-belastnings Utjämnings klient del

Konfigurera belastningsutjämnaren med den nya IPv6 IP-adressen med [AZ Network lb frontend-IP Create](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create) enligt följande:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>Konfigurera backend-poolen för IPv6-belastningsutjämnare

Skapa backend-poolen för nätverkskort med IPv6-adresser med [AZ Network lb Address-pool Create](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create) enligt följande:

```azurecli-interactive
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>Konfigurera IPv6-belastnings Utjämnings regler

Skapa IPv6-belastnings Utjämnings regler med [AZ Network lb Rule Create](/cli/azure/network/lb/rule#az-network-lb-rule-create).

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

## <a name="add-ipv6-address-ranges"></a>Lägg till IPv6-adressintervall

Lägg till IPv6-adressintervall till det virtuella nätverket och under nätet som är värd för belastningsutjämnaren enligt följande:

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

## <a name="add-ipv6-configuration-to-nics"></a>Lägg till IPv6-konfiguration till nätverkskort

Konfigurera nätverkskorten för virtuella datorer med en IPv6-adress med [AZ Network NIC IP-config Create](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-create) enligt följande:

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

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visa ett virtuellt IPv6-nätverk med dubbla stackar i Azure Portal

Du kan visa det virtuella IPv6-nätverket med dubbla stackar i Azure Portal på följande sätt:
1. Skriv *myVnet* i portalens Sök fält.
2. När **myVnet** visas i Sök resultaten väljer du det. Då startas **översikts** sidan för det virtuella nätverket med dubbla stackar med namnet *myVNet*. Det virtuella nätverket med dubbla stackar visar de tre nätverkskorten med både IPv4-och IPv6-konfigurationer som finns i det dubbla stack-undernätet med namnet *mitt undernät*.

  ![IPv6-virtuellt nätverk med dubbla stackar i Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive
az group delete --name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du uppdaterat en befintlig Standard Load Balancer med en IP-konfiguration för IPv4-frontend till en konfiguration med dubbla stackar (IPv4 och IPv6). Du har också lagt till IPv6-konfigurationer till nätverkskorten för de virtuella datorerna i backend-poolen. Mer information om IPv6-stöd i Azure Virtual Networks finns i [Vad är IPv6 för Azure Virtual Network?](ipv6-overview.md)
