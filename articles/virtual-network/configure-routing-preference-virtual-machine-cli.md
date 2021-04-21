---
title: Konfigurera routningsinställning för en virtuell dator – Azure CLI
description: Lär dig hur du skapar en virtuell dator med en offentlig IP-adress med val av routningsinställning med hjälp av Azure-kommandoradsgränssnittet (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: b9155c3114d5a5a1b8729351dc189bc1e5c22369
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764485"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>Konfigurera routningsinställningen för en virtuell dator med Hjälp av Azure CLI

Den här artikeln visar hur du konfigurerar routningsinställningen för en virtuell dator. Internetbunden trafik från den virtuella datorn dirigeras via Internetleverantörens nätverk när du väljer **Internet som** alternativ för routningsinställning. Standardroutning är via Microsofts globala nätverk.

Den här artikeln visar hur du skapar en virtuell dator med en offentlig IP-adress som är inställd på att dirigera trafik via det offentliga Internet med hjälp av Azure CLI.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
1. Om du använder Cloud Shell går du vidare till steg 2. Öppna en kommandosession och logga in på Azure med `az login` .
2. Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp i Azure-regionen USA, östra:

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress
För att komma åt dina virtuella datorer från Internet måste du skapa en offentlig IP-adress. Skapa en offentlig IP-adress med [az network public-ip create](/cli/azure/network/public-ip). I följande exempel skapas en offentlig IP-adress av routningsinställningstypen *Internet* i regionen *USA,* östra:

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

## <a name="create-network-resources"></a>Skapa nätverksresurser

Innan du distribuerar en virtuell dator måste du skapa stödresurser – nätverkssäkerhetsgrupp, virtuellt nätverk och virtuellt nätverkskort.

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverkssäkerhetsgrupp för reglerna som styr inkommande och utgående kommunikation i ditt virtuella nätverk med [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create)

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). I följande exempel skapas ett virtuellt nätverk med *namnet myVNET* med undernäten *mySubNet*:

```azurecli
# Create a virtual network
az network vnet create \
--name myVNET \
--resource-group MyResourceGroup \
--location eastus

# Create a subnet
az network vnet subnet create \
--name mySubNet \
--resource-group MyResourceGroup \
--vnet-name myVNET \
--address-prefixes "10.0.0.0/24" \
--network-security-group myNSG
```

### <a name="create-a-nic"></a>Skapa ett nätverkskort

Skapa ett virtuellt nätverkskort för den virtuella datorn med [az network nic create](/cli/azure/network/nic#az_network_nic_create). I följande exempel skapas ett virtuellt nätverkskort som kopplas till den virtuella datorn.

```azurecli-interactive
# Create a NIC
az network nic create \
--name mynic  \
--resource-group MyResourceGroup \
--network-security-group myNSG  \
--vnet-name myVNET \
--subnet mySubNet  \
--private-ip-address-version IPv4 \
--public-ip-address MyRoutingPrefIP
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell Windows Server 2019-dator och de nödvändiga virtuella nätverkskomponenterna om de inte redan finns.

```azurecli
az vm create \
--name myVM \
--resource-group MyResourceGroup \
--nics mynic \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username myUserName
```

## <a name="clean-up-resources"></a>Rensa resurser

När resurserna inte behövs längre kan du använda [az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen och alla relaterade resurser den innehåller:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [routningsinställningar i offentliga IP-adresser.](routing-preference-overview.md)
- Läs mer om [offentliga IP-adresser](./public-ip-addresses.md#public-ip-addresses) i Azure.
- Läs mer om [inställningar för offentliga IP-adresser.](virtual-network-public-ip-address.md#create-a-public-ip-address)
