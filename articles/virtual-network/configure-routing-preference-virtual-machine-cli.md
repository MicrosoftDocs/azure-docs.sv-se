---
title: Konfigurera cirkulations inställningar för en virtuell dator – Azure CLI
description: Lär dig hur du skapar en virtuell dator med en offentlig IP-adress med inställnings alternativet routning med hjälp av Azures kommando rads gränssnitt (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7673a42afa3b85a2aaf3f11f5e9b74fd46d48488
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945083"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>Konfigurera Dirigerings inställningar för en virtuell dator med Azure CLI

Den här artikeln visar hur du konfigurerar cirkulations inställningar för en virtuell dator. Internet bindnings trafik från den virtuella datorn kommer att dirigeras via Internet leverantörens nätverk när du väljer **Internet** som alternativ för routning av inställningar. Standardroutningen är via Microsofts globala nätverk.

Den här artikeln visar hur du skapar en virtuell dator med en offentlig IP-adress som är inställd på att dirigera trafik via det offentliga Internet med Azure CLI.

> [!IMPORTANT]
> Dirigerings inställningen är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
1. Om du använder Cloud Shell går du vidare till steg 2. Öppna en kommando-session och logga in på Azure med `az login` .
2. Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resurs grupp i Azure-regionen USA, östra:

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress
Du måste skapa en offentlig IP-adress för att få åtkomst till dina virtuella datorer från Internet. Skapa en offentlig IP-adress med [az network public-ip create](/cli/azure/network/public-ip). I följande exempel skapas en offentlig IP-adress för Dirigerings inställningar på *Internet* i regionen *USA, östra* :

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

Innan du distribuerar en virtuell dator måste du skapa stöd för nätverks resurser – nätverks säkerhets grupp, virtuellt nätverk och virtuellt nätverkskort.

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverks säkerhets grupp för de regler som ska styra inkommande och utgående kommunikation i ditt VNet med [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). I följande exempel skapas ett virtuellt nätverk med namnet *myVNET* *med undernät för undernät:*

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

Skapa ett virtuellt nätverkskort för den virtuella datorn med [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create). I följande exempel skapas ett virtuellt nätverkskort som kommer att kopplas till den virtuella datorn.

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

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az-vm-create). I följande exempel skapas en Windows Server 2019 VM och de nödvändiga virtuella nätverks komponenterna om de inte redan finns.

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

När resurserna inte behövs längre kan du använda [az group delete](/cli/azure/group#az-group-delete) för att ta bort resursgruppen och alla relaterade resurser den innehåller:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [cirkulations inställningar i offentliga IP-adresser](routing-preference-overview.md).
- Läs mer om [offentliga IP-adresser](./public-ip-addresses.md#public-ip-addresses) i Azure.
- Läs mer om [Inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address).
