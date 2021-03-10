---
title: Koppla en datadisk till en virtuell Windows-dator i Azure med hjälp av PowerShell
description: Så här ansluter du en ny eller befintlig datadisk till en virtuell Windows-dator med hjälp av PowerShell med distributions modellen för Resource Manager.
author: roygara
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d5c638a63e4e8dc1a55c07f4bdf713fd36ca6b3d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550883"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Koppla en datadisk till en virtuell Windows-dator med PowerShell

Den här artikeln visar hur du ansluter både nya och befintliga diskar till en virtuell Windows-dator med hjälp av PowerShell. 

Börja med att gå igenom följande tips:

* Storleken på den virtuella datorn styr hur många data diskar du kan koppla. Mer information finns i [storlekar för virtuella datorer](../sizes.md).
* Om du vill använda Premium-SSD behöver du en [Premium Storage-aktiverad VM-typ](../sizes-memory.md), t. ex. den virtuella datorn DS-serien eller GS-serien.

Den här artikeln använder PowerShell i [Azure Cloud Shell](../../cloud-shell/overview.md), som uppdateras kontinuerligt till den senaste versionen. Om du vill öppna Cloud Shell väljer du **testa den** överst i ett kodblock.

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Lägg till en tom datadisk till en virtuell dator

Det här exemplet visar hur du lägger till en tom datadisk till en befintlig virtuell dator.

### <a name="using-managed-disks"></a>Använda hanterade diskar

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Använda hanterade diskar i en tillgänglighets zon

Om du vill skapa en disk i en tillgänglighets zon använder du [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) med `-Zone` parametern. I följande exempel skapas en disk i zon *1*.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>Initiera disken

När du har lagt till en tom disk måste du initiera den. För att initiera disken kan du logga in på en virtuell dator och använda disk hantering. Om du har aktiverat [WinRM](/windows/desktop/winrm/portal) och ett certifikat på den virtuella datorn när du skapade det kan du använda fjärr-PowerShell för att initiera disken. Du kan också använda ett anpassat skript tillägg:

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

Skript filen kan innehålla kod för att initiera diskarna, till exempel:

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Koppla en befintlig datadisk till en virtuell dator

Du kan koppla en befintlig hanterad disk till en virtuell dator som en data disk.

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Nästa steg

Du kan också distribuera hanterade diskar med hjälp av mallar. Mer information finns i [använda Managed disks i Azure Resource Manager mallar](../using-managed-disks-template-deployments.md) eller [snabb starts mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk) för att distribuera flera data diskar.