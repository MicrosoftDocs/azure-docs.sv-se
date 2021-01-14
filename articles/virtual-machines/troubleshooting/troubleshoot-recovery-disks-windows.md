---
title: Använd en virtuell Windows-Felsökning med Azure PowerShell | Microsoft Docs
description: Lär dig hur du felsöker problem med virtuella Windows-datorer i Azure genom att ansluta OS-disken till en virtuell återställnings dator med Azure PowerShell
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: fbdccc2efb72626d1974f4cd81be9d2ef4c522d4
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201594"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell återställnings dator med hjälp av Azure PowerShell
Om din virtuella Windows-dator (VM) i Azure påträffar ett start-eller disk fel kan du behöva utföra fel söknings stegen på själva disken. Ett vanligt exempel är en misslyckad program uppdatering som förhindrar att den virtuella datorn kan starta. Den här artikeln beskriver hur du använder Azure PowerShell för att ansluta disken till en annan virtuell Windows-dator för att åtgärda eventuella fel och sedan reparera den ursprungliga virtuella datorn. 

> [!Important]
> Skripten i den här artikeln gäller bara för de virtuella datorer som använder [hanterad disk](../managed-disks-overview.md). 

 

## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Vi kan nu använda Azure PowerShell för att ändra OS-disk för en virtuell dator. Vi behöver inte längre ta bort och återskapa den virtuella datorn.

Så här ser felsökningsprocessen ut:

1. Stoppa den virtuella datorn som påverkas.
2. Skapa en ögonblicks bild från den virtuella datorns OS-disk.
3. Skapa en disk från ögonblicks bilden av OS-disken.
4. Anslut disken som en datadisk till en virtuell återställnings dator.
5. Anslut till den virtuella återställnings datorn. Redigera filer eller kör eventuella verktyg för att åtgärda problem på den kopierade OS-disken.
6. Demontera och koppla från disk från virtuell återställnings dator.
7. Ändra OS-disken för den berörda virtuella datorn.

Du kan automatisera steg 1, 2, 3, 4, 6 och 7 genom att använda kommandona för reparation av virtuella datorer. Mer dokumentation och instruktioner finns i [Reparera en virtuell Windows-dator med hjälp av reparations kommandona för virtuella Azure-datorer](repair-windows-vm-using-azure-virtual-machine-repair-commands.md).

Kontrol lera att du har [den senaste Azure PowerShell](/powershell/azure/) installerat och loggat in på din prenumeration:

```powershell
Connect-AzAccount
```

I följande exempel ersätter du parameter namnen med dina egna värden. 

## <a name="determine-boot-issues"></a>Fastställa start problem
Du kan visa en skärm bild av den virtuella datorn i Azure för att hjälpa till med fel sökning av start problem. Den här skärm bilden kan hjälpa dig att identifiera varför en virtuell dator inte kan starta. I följande exempel hämtas skärm bilden från den virtuella Windows-datorn som heter `myVM` i resurs gruppen med namnet `myResourceGroup` :

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Granska skärm bilden för att ta reda på varför den virtuella datorn inte kan starta. Observera eventuella fel meddelanden eller angivna fel koder.

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

I följande exempel stoppas den virtuella datorn med namnet `myVM` från resurs gruppen med namnet `myResourceGroup` :

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Vänta tills den virtuella datorn har tagits bort innan du bearbetar nästa steg.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Skapa en ögonblicks bild från den virtuella datorns OS-disk

I följande exempel skapas en ögonblicks bild med namnet `mySnapshot` från operativ system disken för den virtuella datorn med namnet "myVM". 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```

En ögonblicks bild är en fullständig skrivskyddad kopia av en virtuell hård disk. Den kan inte kopplas till en virtuell dator. I nästa steg ska vi skapa en disk från den här ögonblicks bilden.

## <a name="create-a-disk-from-the-snapshot"></a>Skapa en disk från ögonblicks bilden

Det här skriptet skapar en hanterad disk med namnet `newOSDisk` från ögonblicks bilden med namnet `mysnapshot` .  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType = 'Standard_LRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
Nu har du en kopia av den ursprungliga OS-disken. Du kan montera disken till en annan virtuell Windows-dator i fel söknings syfte.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>Anslut disken till en annan virtuell Windows-dator för fel sökning

Nu kopplar vi kopian av den ursprungliga OS-disken till en virtuell dator som en data disk. Med den här processen kan du korrigera konfigurations fel eller granska ytterligare program-eller systemloggfiler på disken. I följande exempel kopplas disken `newOSDisk` till den virtuella datorn med namnet `RecoveryVM` .

> [!NOTE]
> För att du ska kunna ansluta disken måste kopian av den ursprungliga OS-disken och den virtuella återställnings datorn vara på samma plats.

```powershell
$rgName = "myResourceGroup"
$vmName = "RecoveryVM"
$location = "eastus" 
$dataDiskName = "newOSDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Anslut till den virtuella återställnings datorn och åtgärda problem på den anslutna disken

1. RDP till den virtuella återställnings datorn med lämpliga autentiseringsuppgifter. Följande exempel laddar ned RDP-anslutningssträngen för den virtuella datorn med namnet `RecoveryVM` i resurs gruppen med namnet `myResourceGroup` och laddar ned den till `C:\Users\ops\Documents`

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Data disken måste identifieras och bifogas automatiskt. Visa listan över anslutna volymer för att fastställa enhets beteckningen enligt följande:

    ```powershell
    Get-Disk
    ```

    Följande exempel på utdata visar disken som är ansluten till en disk **2**. (Du kan också använda `Get-Volume` för att Visa enhets beteckningen):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

När kopian av den ursprungliga OS-disken har monterats kan du utföra eventuella underhålls-och fel söknings steg efter behov. När du har åtgärdat problemen fortsätter du med följande steg.

## <a name="unmount-and-detach-original-os-disk"></a>Demontera och koppla från ursprunglig OS-disk
När dina fel har åtgärd ATS avmonterar du och kopplar från den befintliga disken från den virtuella återställnings datorn. Du kan inte använda disken med någon annan virtuell dator förrän lånet som ansluter disken till den virtuella återställnings datorn har släppts.

1. Från RDP-sessionen avmonterar du data disken på den virtuella återställnings datorn. Du behöver disk numret från föregående `Get-Disk` cmdlet. Använd `Set-Disk` för att ange disken som offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Bekräfta att disken nu har angetts som offline med hjälp av `Get-Disk` igen. Följande exempel på utdata visar att disken nu har angetts som offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Avsluta RDP-sessionen. Från Azure PowerShell-sessionen tar du bort disken med namnet `newOSDisk` från den virtuella datorn med namnet "RecoveryVM".

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Ändra OS-disken för den berörda virtuella datorn

Du kan använda Azure PowerShell för att växla OS-diskarna. Du behöver inte ta bort och återskapa den virtuella datorn.

Det här exemplet stoppar den virtuella datorn med namnet `myVM` och tilldelar disken namnet `newOSDisk` som den nya OS-disken. 

```powershell
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>Verifiera och aktivera startdiagnostik

I följande exempel aktive ras Diagnostic-tillägget på den virtuella datorn som heter `myVMDeployed` i resurs gruppen med namnet `myResourceGroup` :

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till din virtuella dator kan du läsa [FELSÖKA RDP-anslutningar till en virtuell Azure-dator](troubleshoot-rdp-connection.md). Problem med att komma åt program som körs på den virtuella datorn finns i [Felsöka problem med program anslutningen på en virtuell Windows-dator](troubleshoot-app-connection.md).

Mer information om hur du använder Resource Manager finns i [Azure Resource Manager översikt](../../azure-resource-manager/management/overview.md).
