---
title: Skapa en virtuell dator med en statisk offentlig IP-adress – PowerShell | Microsoft Docs
description: Skapa en virtuell dator (VM) med en statisk offentlig IP-adress med hjälp av PowerShell. Statiska offentliga IP-adresser är adresser som aldrig ändras.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: allensu
ms.openlocfilehash: 047a6db82e65c97deba5270d181f72315a67e82c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791264"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Skapa en virtuell dator med en statisk offentlig IP-adress med hjälp av PowerShell


Du kan skapa en virtuell dator med en statisk offentlig IP-adress. Med en offentlig IP-adress kan du kommunicera med en virtuell dator från Internet. Tilldela en statisk offentlig IP-adress i stället för en dynamisk adress för att säkerställa att adressen aldrig ändras. Läs mer om [statiska offentliga IP-adresser](./public-ip-addresses.md#allocation-method). Om du vill ändra en offentlig IP-adress som tilldelats till en befintlig virtuell dator från dynamisk till statisk, eller om du vill arbeta med privata IP-adresser, se [Lägg till, ändra eller ta bort IP-](virtual-network-network-interface-addresses.md)adresser. Offentliga IP-adresser har en [nominell avgift](https://azure.microsoft.com/pricing/details/ip-addresses)och det finns en [gräns](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) för hur många offentliga IP-adresser som du kan använda per prenumeration.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Du kan utföra följande steg från din lokala dator eller med hjälp av Azure Cloud Shell. Se till att du har [Azure PowerShell installerat](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json)om du vill använda din lokala dator. Om du vill använda Azure Cloud Shell väljer du **testa det** i det övre högra hörnet i alla kommando rutor som följer. Cloud Shell signerar dig till Azure.

1. Om du använder Cloud Shell går du vidare till steg 2. Öppna en kommando-session och logga in på Azure med `Connect-AzAccount` .
2. Skapa en resursgrupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resurs grupp i Azure-regionen USA, östra:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Skapa en virtuell dator med kommandot [New-AzVM](/powershell/module/az.Compute/New-azVM) . `-AllocationMethod "Static"`Alternativet tilldelar den virtuella datorn en statisk offentlig IP-adress. I följande exempel skapas en virtuell Windows Server-dator med en statisk, grundläggande SKU offentlig IP-adress med namnet *myPublicIpAddress*. När du uppmanas till det anger du ett användar namn och lösen ord som ska användas som inloggnings uppgifter för den virtuella datorn:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Om den offentliga IP-adressen måste vara en standard-SKU måste du [skapa en offentlig IP-adress](virtual-network-public-ip-address.md#create-a-public-ip-address), [skapa ett nätverks gränssnitt](virtual-network-network-interface.md#create-a-network-interface), [tilldela den offentliga IP-adressen till nätverks gränssnittet](virtual-network-network-interface-addresses.md#add-ip-addresses)och sedan [skapa en virtuell dator med nätverks gränssnittet](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm)i separata steg. Lär dig mer om [offentliga IP-adresser SKU: er](./public-ip-addresses.md#sku). Om den virtuella datorn ska läggas till i backend-poolen för en offentlig Azure Load Balancer måste SKU: n för den virtuella datorns offentliga IP-adress matcha SKU: n för den offentliga IP-adressen för belastningsutjämnaren. Mer information finns i [Azure Load Balancer](../load-balancer/skus.md).

4. Visa den offentliga IP-adress som tilldelats och bekräfta att den har skapats som en statisk adress med [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Azure tilldelade en offentlig IP-adress från adresser som används i den region där du skapade den virtuella datorn i. Du kan ladda ned listan över intervall (prefix) för [offentliga](https://www.microsoft.com/download/details.aspx?id=56519) Azure-moln och för Azure-moln för [amerikanska myndigheter](https://www.microsoft.com/download/details.aspx?id=57063), [Kina](https://www.microsoft.com/download/details.aspx?id=57062) eller [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Ändra inte inställningarna för IP-adress i den virtuella datorns operativ system. Operativ systemet är inte medvetna om offentliga Azure-IP-adresser. Även om du kan lägga till inställningar för privata IP-adresser i operativ systemet rekommenderar vi att du inte gör det om det inte behövs, och inte förrän du har läst [lägga till en privat IP-adress till ett operativ system](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla resurser som den innehåller:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [offentliga IP-adresser](./public-ip-addresses.md#public-ip-addresses) i Azure
- Läs mer om [Inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Läs mer om [privata IP-adresser](./private-ip-addresses.md) och tilldelning av en [statisk privat IP-adress](virtual-network-network-interface-addresses.md#add-ip-addresses) till en virtuell Azure-dator
- Lär dig mer om att skapa virtuella [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -och [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -datorer
