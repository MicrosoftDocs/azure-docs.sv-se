---
title: Ändra tillgänglighets uppsättning för virtuella datorer
description: Lär dig hur du ändrar tillgänglighets uppsättningen för den virtuella datorn med hjälp av Azure PowerShell.
ms.service: virtual-machines
author: cynthn
ms.topic: how-to
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: 54f59a052132826897cfbc8dda59bc73fb6ad8d9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200574"
---
# <a name="change-the-availability-set-for-a-vm"></a>Ändra tillgänglighetsuppsättning för en virtuell dator
Följande steg beskriver hur du ändrar tillgänglighets uppsättningen för en virtuell dator med hjälp av Azure PowerShell. En virtuell dator kan bara läggas till i en tillgänglighets uppsättning när den skapas. Om du vill ändra tillgänglighets uppsättningen måste du ta bort och sedan återskapa den virtuella datorn. 

Den här artikeln gäller både virtuella Linux-och Windows-datorer.

Den här artikeln testades senast 2/12/2019 med hjälp av [Azure Cloud Shell](https://shell.azure.com/powershell) och [AZ PowerShell-modulens](/powershell/azure/install-az-ps) version 1.2.0.

I det här exemplet kontrol leras inte om den virtuella datorn är kopplad till en belastningsutjämnare. Om den virtuella datorn är kopplad till en belastningsutjämnare måste du uppdatera skriptet för att hantera det fallet. 


## <a name="change-the-availability-set"></a>Ändra tillgänglighets uppsättning 

Följande skript innehåller ett exempel på hur du samlar in nödvändig information, tar bort den ursprungliga virtuella datorn och sedan återskapar den i en ny tillgänglighets uppsättning.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM. 
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
 
# For a Linux VM, change the last parameter from -Windows to -Linux 
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
    {
            Add-AzVMNetworkInterface `
               -VM $newVM `
               -Id $nic.Id -Primary
               }
           else
               {
                 Add-AzVMNetworkInterface `
                -VM $newVM `
                 -Id $nic.Id 
                }
      }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>Nästa steg

Lägg till ytterligare lagrings utrymme i den virtuella datorn genom att lägga till ytterligare en [datadisk](attach-managed-disk-portal.md).
