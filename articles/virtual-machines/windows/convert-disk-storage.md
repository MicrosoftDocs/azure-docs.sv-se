---
title: Konvertera Managed disks Storage mellan olika disk typer med hjälp av Azure PowerShell
description: Så här konverterar du Azure Managed disks mellan olika disk typer med hjälp av Azure PowerShell.
author: roygara
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.openlocfilehash: e2541607b116159e4f6ec4028c83ecc9a45eded8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550747"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Uppdatera lagrings typen för en hanterad disk

Det finns fyra disk typer av Azure Managed disks: Azure Ultra disks, Premium SSD, standard SSD och standard-HDD. Du kan växla mellan Premium SSD, standard SSD och standard-HDD baserat på dina prestanda behov. Du kan inte byta från eller till en Ultra disk, du måste distribuera en ny.

Den här funktionen stöds inte för ohanterade diskar. Men du kan enkelt [konvertera en ohanterad disk till en hanterad disk](convert-unmanaged-to-managed-disks.md) för att kunna växla mellan disk typer.



## <a name="before-you-begin"></a>Innan du börjar

* Eftersom konverteringen kräver en omstart av den virtuella datorn (VM) bör du schemalägga migreringen av disk lagringen under ett redan befintligt underhålls fönster.
* Om disken är ohanterad måste du först [konvertera den till en hanterad disk](convert-unmanaged-to-managed-disks.md) så att du kan växla mellan lagrings alternativen.

## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Växla alla hanterade diskar för en virtuell dator mellan från ett konto till ett annat

Det här exemplet visar hur du konverterar alla diskar i en virtuell dator till Premium Storage. Men genom att ändra $storageType variabeln i det här exemplet kan du konvertera den virtuella datorns disk typer till standard SSD eller standard-HDD. Om du vill använda Premium Managed disks måste den virtuella datorn använda en [VM-storlek](../sizes.md) som har stöd för Premium Storage. Det här exemplet växlar också till en storlek som har stöd för Premium Storage:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS, StandardSDD_LRS and Premium_LRS based on your scenario
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

För din arbets belastning för utveckling/testning kanske du vill ha en blandning av standard-och Premium-diskar för att minska kostnaderna. Du kan välja att bara uppgradera de diskar som behöver bättre prestanda. Det här exemplet visar hur du konverterar en enskild virtuell dator disk från standard till Premium Storage. Men genom att ändra $storageType variabeln i det här exemplet kan du konvertera den virtuella datorns disk typer till standard SSD eller standard-HDD. Om du vill använda Premium Managed disks måste den virtuella datorn använda en [VM-storlek](../sizes.md) som har stöd för Premium Storage. I det här exemplet visas också hur du växlar till en storlek som har stöd för Premium Storage:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
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

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Växla Managed disks från en typ av disk till en annan

Gör så här:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj den virtuella datorn i listan över **virtuella datorer**.
3. Om den virtuella datorn inte är stoppad väljer du **stanna** överst i **översikts** fönstret för den virtuella datorn och väntar på att den virtuella datorn ska stoppas.
4. I fönstret för den virtuella datorn väljer du **diskar** på menyn.
5. Välj den disk som du vill konvertera.
6. Välj **konfiguration** på menyn.
7. Ändra **konto typen** från den ursprungliga disk typen till den önskade disk typen.
8. Välj **Spara** och Stäng disk fönstret.

Disk typs konverteringen är omedelbar. Du kan starta den virtuella datorn efter konverteringen.

## <a name="next-steps"></a>Nästa steg

Gör en skrivskyddad kopia av en virtuell dator med hjälp av en [ögonblicks bild](snapshot-copy-managed-disk.md).