---
title: Konfigurera Dirigerings inställningar för en offentlig IP-adress – Azure PowerShell
titlesuffix: Azure Virtual Network
description: Lär dig hur du konfigurerar cirkulations inställningar för en offentlig IP-adress med hjälp av Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 5fac791247dbb785e087ac21f3fd5532064c6c8a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667891"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-powershell"></a>Konfigurera cirkulations inställningar för en offentlig IP-adress med hjälp av Azure PowerShell

Den här artikeln visar hur du konfigurerar inställningar för routning via Internet LEVERANTÖRs nätverk (**Internet** alternativ) för en offentlig IP-adress med hjälp av Azure PowerShell. När du har skapat den offentliga IP-adressen kan du associera den med följande Azure-resurser för inkommande och utgående trafik till Internet:

* Virtuell dator
* Skaluppsättning för virtuella datorer
* Azure Kubernetes Service (AKS)
* Belastningsutjämnare mot Internet
* Application Gateway
* Azure Firewall

Som standard är cirkulations inställningen för offentlig IP-adress inställd på Microsofts globala nätverk för alla Azure-tjänster och kan associeras med valfri Azure-tjänst.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell module version 6.9.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurepowershell
$rg = New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-public-ip-with-internet-routing-preference"></a>Skapa en offentlig IP-adress med inställningar för Internet routning

Följande kommando skapar en ny offentlig IP-adress med en inställnings typ för routning som *Internet* i Azure-regionen *USA, östra* :

```azurepowershell
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

Du kan associera ovanstående skapade offentliga IP-adress med en virtuell [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -eller [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -dator. Använd avsnittet CLI på sidan självstudie: [associera en offentlig IP-adress till en virtuell dator](associate-public-ip-address-vm.md#azure-cli) för att koppla den offentliga IP-adressen till den virtuella datorn. Du kan också associera den offentliga IP-adressen som skapats ovan med en [Azure Load Balancer](../load-balancer/load-balancer-overview.md), genom att tilldela den till konfiguration av belastningsutjämnarens **klient** del. Den offentliga IP-adressen fungerar som en belastningsutjämnad virtuell IP-adress (VIP).

## <a name="clean-up-resources"></a>Rensa resurser

Om den inte längre behövs kan du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen, den virtuella datorn och alla relaterade resurser.

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [cirkulations inställningar i offentliga IP-adresser](routing-preference-overview.md).
- [Konfigurera Dirigerings inställningar för en virtuell dator med hjälp av Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md).
