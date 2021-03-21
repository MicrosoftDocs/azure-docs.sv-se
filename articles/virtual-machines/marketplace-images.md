---
title: Ange information om inköps prenumerations information via Azure PowerShell
description: Lär dig hur du anger information om inköps plan för Azure Marketplace när du skapar bilder i ett delat avbildnings Galleri.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/07/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 9df1f6b02e6572c8f2153016c0142912e24fcfe8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562545"
---
# <a name="supply-azure-marketplace-purchase-plan-information-when-creating-images"></a>Ange information om inköps plan för Azure Marketplace när du skapar bilder

Om du skapar en avbildning i ett delat galleri med en källa som ursprungligen skapades från en Azure Marketplace-avbildning, kan du behöva hålla koll på inköps planens information. Den här artikeln visar hur du hittar information om inköps planer för en virtuell dator. Använd sedan informationen när du skapar en avbildnings definition. Vi kan också använda informationen från avbildnings definitionen för att förenkla leverans av inköps Plans informationen när du skapar en virtuell dator för en avbildning.

Mer information om hur du hittar och använder Marketplace-avbildningar finns i [hitta och använda Azure Marketplace-avbildningar](./windows/cli-ps-findimage.md).


## <a name="get-the-source-vm-information"></a>Hämta information om den virtuella käll datorn
Om du fortfarande har den ursprungliga virtuella datorn kan du hämta plan namn, utgivare och produkt information från den med hjälp av Get-AzVM. I det här exemplet får du en virtuell dator med namnet *myVM* i resurs gruppen *myResourceGroup* och sedan visas information om inköps planen för den virtuella datorn.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

## <a name="create-the-image-definition"></a>Skapa avbildnings definitionen

Hämta det avbildnings galleri som du vill använda för att lagra avbildningen. Du kan visa alla gallerier först.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Skapa sedan variabler för galleriet som du vill använda. I det här exemplet skapar vi en variabel som heter `$gallery` for *Gallery* i resurs gruppen *myGalleryRG* .

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myGalleryRG
```

Skapa avbildnings definitionen med  `-PurchasePlanPublisher` `-PurchasePlanProduct` parametrarna, och `-PurchasePlanName` .

```azurepowershell-interactive
 $imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Linux `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU' `
   -PurchasePlanPublisher $vm.Plan.Publisher `
   -PurchasePlanProduct $vm.Plan.Product `
   -PurchasePlanName  $vm.Plan.Name
```

Skapa sedan avbildnings versionen med hjälp av [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). Du kan skapa en avbildnings version från en [virtuell dator](image-version-vm-powershell.md#create-an-image-version), en [hanterad avbildning](image-version-managed-image-powershell.md#create-an-image-version), [VHD\snapshot](image-version-snapshot-powershell.md#create-an-image-version)eller [en annan avbildnings version](image-version-another-gallery-powershell.md#create-the-image-version). 


## <a name="create-the-vm"></a>Skapa den virtuella datorn

När du går till skapa en virtuell dator från avbildningen kan du använda informationen från avbildnings definitionen för att skicka utgivar informationen med hjälp av [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan).


```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "mySIGPubVM"
$location = "West Central US"
$vmName = "mySIGPubVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version. Set-AZVMPlan is used to pass the plan information in for the VM.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2   | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Set-AzVMPlan `
     -Publisher $imageDefinition.PurchasePlan.Publisher `
     -Product $imageDefinition.PurchasePlan.Product `
     -Name $imageDefinition.PurchasePlan.Name | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create the virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hittar och använder Marketplace-avbildningar finns i [hitta och använda Azure Marketplace-avbildningar](./windows/cli-ps-findimage.md).
