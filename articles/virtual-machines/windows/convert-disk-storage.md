---
title: Konvertera Managed disks Storage mellan standard-och Premium SSD genom att använda Azure PowerShell
description: Så här konverterar du Azure Managed disks från standard till Premium eller Premium till standard med hjälp av Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 13159e527fac76a1a79118e9363b94904935a2be
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807503"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Uppdatera lagrings typen för en hanterad disk

Det finns fyra disk typer av Azure Managed disks: Azure Ultra disks, Premium SSD, standard SSD och standard-HDD. Du kan växla mellan de tre GA disk typerna (Premium SSD, standard SSD och standard-HDD) utifrån dina prestanda behov. Du kan inte byta från eller till en Ultra disk, du måste distribuera en ny.

Den här funktionen stöds inte för ohanterade diskar. Men du kan enkelt [konvertera en ohanterad disk till en hanterad disk](convert-unmanaged-to-managed-disks.md) för att kunna växla mellan disk typer.

 

## <a name="prerequisites"></a>Förutsättningar

* Eftersom konverteringen kräver en omstart av den virtuella datorn (VM) bör du schemalägga migreringen av disk lagringen under ett redan befintligt underhålls fönster.
* Om disken är ohanterad måste du först [konvertera den till en hanterad disk](convert-unmanaged-to-managed-disks.md) så att du kan växla mellan lagrings alternativen.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Växla alla hanterade diskar för en virtuell dator mellan Premium och standard

I det här exemplet visas hur du konverterar alla diskar från standard till Premium Storage eller från Premium till standard lagring. Om du vill använda Premium Managed disks måste den virtuella datorn använda en [VM-storlek](../sizes.md) som har stöd för Premium Storage. Det här exemplet växlar också till en storlek som har stöd för Premium Storage:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Växla mellan de enskilda hanterade diskarna mellan standard och Premium

För din arbets belastning för utveckling/testning kanske du vill ha en blandning av standard-och Premium-diskar för att minska kostnaderna. Du kan välja att bara uppgradera de diskar som behöver bättre prestanda. Det här exemplet visar hur du konverterar en enskild virtuell dator disk från standard till Premium Storage eller från Premium till standard lagring. Om du vill använda Premium Managed disks måste den virtuella datorn använda en [VM-storlek](../sizes.md) som har stöd för Premium Storage. I det här exemplet visas också hur du växlar till en storlek som har stöd för Premium Storage:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Konvertera Managed disks från standard till Premium i Azure Portal

Gör så här:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj den virtuella datorn i listan över **virtuella datorer** i portalen.
3. Om den virtuella datorn inte är stoppad väljer du **stoppa** längst upp i **översikts** fönstret för den virtuella datorn och väntar på att den virtuella datorn ska stoppas.
3. I fönstret för den virtuella datorn väljer du **diskar** på menyn.
4. Välj den disk som du vill konvertera.
5. Välj **konfiguration** på menyn.
6. Ändra **konto typen** från **standard HDD** till **Premium SSD**.
7. Klicka på **Spara** och Stäng disk fönstret.

Disk typs konverteringen är omedelbar. Du kan starta den virtuella datorn efter konverteringen.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Växla Managed disks mellan Standard HDD och Standard SSD 

Det här exemplet visar hur du konverterar en enskild virtuell dator disk från Standard HDD till Standard SSD eller från Standard SSD till Standard HDD:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Nästa steg

Gör en skrivskyddad kopia av en virtuell dator med hjälp av en [ögonblicks bild](snapshot-copy-managed-disk.md).