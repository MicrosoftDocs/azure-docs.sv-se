---
title: Skapa en virtuell dator från en specialiserad avbildning
description: Skapa en virtuell dator med hjälp av en specialiserad avbildning i ett galleri för delade avbildningar.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 6b728591aeb330c7b2af8ca5dc4770147761b5b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552294"
---
# <a name="create-a-vm-using-a-specialized-image"></a>Skapa en virtuell dator med hjälp av en specialiserad avbildning 

Skapa en virtuell dator från en specialiserad avbildnings version som lagras i ett delat avbildnings Galleri. Om du vill skapa en virtuell dator med en generaliserad avbildnings version, se [skapa en virtuell dator med en generaliserad avbildning](vm-generalized-image-version-powershell.md).

När du har en specialiserad avbildnings version kan du skapa en eller flera nya virtuella datorer med cmdleten [New-AzVM](/powershell/module/az.compute/new-azvm) . 

I det här exemplet använder vi bild Definitions-ID: t för att se till att den nya virtuella datorn kommer att använda den senaste versionen av en avbildning. Du kan också använda en speciell version med hjälp av avbildningens versions-ID för `Set-AzVMSourceImage -Id` . Om du till exempel vill använda bild versionen *1.0.0* typ: `Set-AzVMSourceImage -Id "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` . 

Tänk på att om du använder en angiven avbildnings version kan Automation inte köras om den angivna avbildnings versionen inte är tillgänglig eftersom den har tagits bort eller tagits bort från regionen. Vi rekommenderar att du använder bild Definitions-ID: t för att skapa din nya virtuella dator, om inte en speciell avbildnings version krävs.

Ersätt resurs namn efter behov i det här exemplet. 


```azurepowershell-interactive

# Create some variables for the new VM.

$resourceGroup = "mySIGSpecializedRG"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition


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

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2 | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig

```

## <a name="attach-the-data-disk"></a>Koppla data disken
Om avbildningen innehåller en datadisk måste du koppla data disken till den virtuella datorn.

```azurepowershell-interactive

$vm = Get-AzVM -Name $vmName -ResourceGroupName $resourceGroup 

$lun = $imageVersion.StorageProfile.DataDiskImages.Lun

Add-AzVMDataDisk `
   -CreateOption FromImage `
   -SourceImageUri $imageversion.Id `
   -Lun $lun `
   -Caching $imageVersion.StorageProfile.DataDiskImages.HostCaching `
   -DiskSizeInGB $imageVersion.StorageProfile.DataDiskImages.SizeInGB `
   -VM $vm

```


## <a name="next-steps"></a>Nästa steg
[Azure Image Builder (för hands version)](./image-builder-overview.md) kan hjälpa dig att automatisera avbildnings versionen, du kan även använda den för att uppdatera och [skapa en ny avbildnings version från en befintlig avbildnings version](./linux/image-builder-gallery-update-image-version.md). 

Du kan också skapa en delad resurs för avbildnings galleriet med hjälp av mallar. Det finns flera tillgängliga Azure snabb starts mallar: 

- [Skapa ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en avbildningsdefinition i ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en avbildningsversion i ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från avbildningsversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Mer information om delade avbildnings gallerier finns i [översikten](./shared-image-galleries.md). Om du stöter på problem, se [Felsöka delade avbildnings gallerier](troubleshooting-shared-images.md).