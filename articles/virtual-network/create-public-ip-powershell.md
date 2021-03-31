---
title: Skapa en offentlig IP-Azure PowerShell
description: Lär dig hur du skapar en offentlig IP-adress med hjälp av Azure PowerShell
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: f81e76e32bc0a50b945c54cdfcac0575540484bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102502458"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-powershell"></a>Snabb start: skapa en offentlig IP-adress med hjälp av Azure PowerShell

Den här artikeln visar hur du skapar en offentlig IP-adressresurs med hjälp av Azure PowerShell. Mer information om vilka resurser som kan kopplas till, skillnaden mellan Basic-och standard-SKU och annan relaterad information finns i [offentliga IP-adresser](./public-ip-addresses.md).  I det här exemplet kommer vi bara att fokusera på IPv4-adresser. Mer information om IPv6-adresser finns i [IPv6 för Azure VNet](./ipv6-overview.md).

## <a name="prerequisites"></a>Förutsättningar

- Azure PowerShell installerat lokalt eller Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln version 5.4.1 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resurs grupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) med namnet **myResourceGroup** på **eastus2** -platsen.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'

New-AzResourceGroup -Name $rg -Location $loc
```
## <a name="create-public-ip"></a>Skapa offentlig IP

---
# <a name="standard-sku---using-zones"></a>[**Standard-SKU – använda zoner**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>Följande kommando fungerar för AZ. Network module version 4.5.0 eller senare.  Mer information om de PowerShell-moduler som används för närvarande finns i PowerShellGet- [dokumentationen](/powershell/module/powershellget/).

Använd [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) för att skapa en standard zon som REDUNDANT offentlig IP-adress med namnet **myStandardZRPublicIP** i **myResourceGroup**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZRPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 1,2,3

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```
> [!IMPORTANT]
> För AZ. Network-moduler som är äldre än 4.5.0 kör du kommandot ovan utan att ange en zon parameter för att skapa en zon-redundant IP-adress. 
>

Använd följande kommando för att skapa en standard zonindelade offentlig IP-adress i Zon 2 med namnet **myStandardZonalPublicIP** i **myResourceGroup**:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZonalPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 2

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```

Observera att ovanstående alternativ för zoner endast är giltiga val i regioner med [Tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**Standard-SKU – inga zoner**](#tab/option-create-public-ip-standard)

>[!NOTE]
>Följande kommando fungerar för AZ. Network module version 4.5.0 eller senare.  Mer information om de PowerShell-moduler som används för närvarande finns i PowerShellGet- [dokumentationen](/powershell/module/powershellget/).

Använd [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) för att skapa en offentlig standard-IP-adress som en icke-zonindelade resurs med namnet **myStandardPublicIP** i **myResourceGroup**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardPublicIP'
$sku = 'Standard'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```

Det här valet är giltigt i alla regioner och är standard valet för offentliga standard-IP-adresser i regioner utan [Tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="basic-sku"></a>[**Grundläggande SKU**](#tab/option-create-public-ip-basic)

Använd [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) för att skapa en grundläggande statisk offentlig IP-adress med namnet **myBasicPublicIP** i **myResourceGroup**.  Grundläggande offentliga IP-adresser har inte konceptet tillgänglighets zoner.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myBasicPublicIP'
$sku = 'Basic'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```
Om det är acceptabelt att IP-adressen kan ändras över tid kan du välja **dynamisk** IP-tilldelning genom att ändra AllocationMethod till "dynamisk".

---

## <a name="additional-information"></a>Ytterligare information 

Mer information om de enskilda variablerna ovan finns i [hantera offentliga IP-adresser](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Nästa steg
- Associera en [offentlig IP-adress till en virtuell dator](./associate-public-ip-address-vm.md#azure-portal)
- Läs mer om [offentliga IP-adresser](./public-ip-addresses.md#public-ip-addresses) i Azure.
- Läs mer om [Inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address).