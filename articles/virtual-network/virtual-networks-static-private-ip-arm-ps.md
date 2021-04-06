---
title: Skapa en virtuell dator med en statisk privat IP-adress – Azure PowerShell
description: Lär dig hur du skapar en virtuell dator med en privat IP-adress med hjälp av PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 43c2e2c8905ca60e59caed3fdf1b4920f509fd88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98783792"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Skapa en virtuell dator med en statisk privat IP-adress med hjälp av PowerShell

Du kan skapa en virtuell dator (VM) med en statisk privat IP-adress. Tilldela en statisk privat IP-adress, i stället för en dynamisk adress, om du vill välja vilken adress från ett undernät som är tilldelad en virtuell dator. Läs mer om [statiska privata IP-adresser](./public-ip-addresses.md#allocation-method). Om du vill ändra en privat IP-adress som tilldelats till en befintlig virtuell dator från dynamisk till statisk, eller om du vill arbeta med offentliga IP-adresser, se [Lägg till, ändra eller ta bort IP-](virtual-network-network-interface-addresses.md)adresser.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Du kan utföra följande steg från din lokala dator eller med hjälp av Azure Cloud Shell. Se till att du har [Azure PowerShell installerat](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json)om du vill använda din lokala dator. Om du vill använda Azure Cloud Shell väljer du **testa det** i det övre högra hörnet i alla kommando rutor som följer. Cloud Shell signerar dig till Azure.

1. Om du använder Cloud Shell går du vidare till steg 2. Öppna en kommando-session och logga in på Azure med `Connect-AzAccount` .
2. Skapa en resursgrupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resurs grupp i Azure-regionen USA, östra:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Skapa en under näts konfiguration och ett virtuellt nätverk med kommandona [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) och [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) :

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. Skapa ett nätverks gränssnitt i det virtuella nätverket och tilldela en privat IP-adress från under nätet till nätverks gränssnittet med kommandona [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) och [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) :

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. Skapa en VM-konfiguration med [New-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig)och skapa sedan den virtuella datorn med [New-AzVM](/powershell/module/az.Compute/New-azVM). När du uppmanas till det anger du ett användar namn och lösen ord som ska användas som inloggnings uppgifter för den virtuella datorn:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Även om du kan lägga till inställningar för privata IP-adresser i operativ systemet rekommenderar vi att du inte gör det förrän du har läst [Lägg till en privat IP-adress till ett operativ system](virtual-network-network-interface-addresses.md#private).
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> För att få åtkomst till den virtuella datorn från Internet måste du tilldela en offentlig IP-adress till den virtuella datorn. Du kan också ändra en dynamisk privat IP-adresstilldelning till en statisk tilldelning. Mer information finns i [Lägg till eller ändra IP-adresser](virtual-network-network-interface-addresses.md). Dessutom rekommenderar vi att du begränsar nätverks trafiken till den virtuella datorn genom att associera en nätverks säkerhets grupp till nätverks gränssnittet, under nätet som du skapade nätverks gränssnittet i, eller båda. Mer information finns i [Hantera nätverks säkerhets grupper](manage-network-security-group.md).

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla resurser som den innehåller:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [privata IP-adresser](./private-ip-addresses.md) och tilldela en [statisk privat IP-adress](virtual-network-network-interface-addresses.md#add-ip-addresses) till en virtuell Azure-dator.
- Lär dig mer om att skapa virtuella [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -och [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -datorer.
