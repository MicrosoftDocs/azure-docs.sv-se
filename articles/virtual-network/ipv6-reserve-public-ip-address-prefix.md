---
title: Reservera offentliga IPv6-adresser och adress intervall i ett virtuellt Azure-nätverk
titlesuffix: Azure Virtual Network
description: Lär dig hur du reserverar offentliga IPv6-adresser och adress intervall i ett virtuellt Azure-nätverk.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: ac79e1eb5c4f7448dc17804cd8aac3cba582497e
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509951"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Reservera offentligt IPv6-adressprefix
Med IPv6 för Azure Virtual Network (VNet) kan du vara värd för program i Azure med IPv6-och IPv4-anslutning både i ett virtuellt nätverk och till och från Internet. Förutom att reservera enskilda IPv6-adresser kan du reservera sammanhängande intervall av Azure IPv6-adresser (kallas även IP-prefix) för din användning. I den här artikeln beskrivs hur du skapar offentliga IP-adresser och adress intervall för IPv6 med hjälp av Azure PowerShell och CLI.


## <a name="create-a-single-reserved-ipv6-public-ip"></a>Skapa en enda reserverad IPv6 offentlig IP-adress

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

Du kan skapa en enda reserverad (statisk) IPv6-IP-adress med Azure PowerShell med [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) på följande sätt:

```azurepowershell
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Använda Azure CLI

 Du kan skapa en enda reserverad (statisk) IPv6 offentlig IP-adress Azure CLI med [AZ Network Public-IP Create](/cli/azure/network/public-ip) enligt följande:

```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Skapa ett reserverat IPv6-prefix (intervall)

Om du vill reservera ett IPv6-prefix lägger du till IP-adresserna för IPv6 till samma kommando som används för att skapa IPv4-prefix. Följande kommandon skapar ett prefix av storlek/125 (8 IPv6-adresser).

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

Du kan skapa en offentlig IPv6-adress med hjälp av Azure CLI med [AZ Network Public-IP Create](/powershell/module/az.network/new-azpublicipprefix) enligt följande:
```azurepowershell
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Använda Azure CLI

Du kan skapa en offentlig IPv6-adress med hjälp av Azure CLI på följande sätt:

```azurecli
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>Allokera en offentlig IP-adress från ett reserverat IPv6-prefix

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

 Du skapar en offentlig IP-IP-adress från ett reserverat prefix genom att lägga till `-PublicIpPrefix` argumentet när du skapar den offentliga IP-adressen med Azure PowerShell. I följande exempel förutsätts att ett prefix har skapats och lagrats i en PowerShell-variabel med namnet: *$MyOwnIPv 6prefix*.

```azurepowershell
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Använda Azure CLI

I följande exempel förutsätts att ett prefix har skapats och lagrats i en CLI-variabel med namnet: *IPv6PrefixWestUS*.

```azurecli
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [IPv6-](ipv6-public-ip-address-prefix.md)adressprefix.
- Läs mer om [IPv6-adresser](ipv6-overview.md).
