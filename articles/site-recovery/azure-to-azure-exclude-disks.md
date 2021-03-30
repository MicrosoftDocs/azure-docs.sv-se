---
title: Undanta virtuella Azure-diskar från replikering med Azure Site Recovery och Azure PowerShell
description: Lär dig hur du undantar diskar av virtuella Azure-datorer under Azure Site Recovery med hjälp av Azure PowerShell.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: a21460279420c46b11c43615ae5ecc7bfa81de4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135803"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Undanta diskar från PowerShell-replikering av virtuella Azure-datorer

Den här artikeln beskriver hur du undantar diskar när du replikerar virtuella Azure-datorer. Du kan utesluta diskar för att optimera bandbredden för förbrukad replikering eller de resurser på mål sidan som dessa diskar använder. Den här funktionen är för närvarande endast tillgänglig via Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar:

- Se till att du förstår [arkitekturen och komponenterna för katastrof återställning](azure-to-azure-architecture.md).
- Granska [support kraven](azure-to-azure-support-matrix.md) för alla komponenter.
- Kontrol lera att du har AzureRm PowerShell-modulen "AZ". Information om hur du installerar eller uppdaterar PowerShell finns i [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).
- Se till att du har skapat ett Recovery Services-valv och skyddade virtuella datorer minst en gång. Om du inte har gjort det följer du processen för att [Konfigurera haveri beredskap för virtuella Azure-datorer med hjälp av Azure PowerShell](azure-to-azure-powershell.md).
- [Läs den här artikeln](azure-to-azure-enable-replication-added-disk.md)om du vill ha information om hur du lägger till diskar till en virtuell Azure-dator som är aktive rad för replikering.

## <a name="why-exclude-disks-from-replication"></a>Varför undanta diskar från replikering
Du kan behöva utesluta diskar från replikeringen eftersom:

- Den virtuella datorn har nått [Azure Site Recovery gränser för att replikera data ändrings takten](./azure-to-azure-support-matrix.md).

- De data som är indelade på den uteslutna disken är inte viktiga eller behöver inte replikeras.

- Du vill spara lagrings-och nätverks resurser genom att inte replikera data.

## <a name="how-to-exclude-disks-from-replication"></a>Så här undantar du diskar från replikering

I vårt exempel replikerar vi en virtuell dator som har ett operativ system och tre data diskar i regionen USA, östra till regionen USA, västra 2. Namnet på den virtuella datorn är *AzureDemoVM*. Vi undantar disk 1 och behåller diskar 2 och 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Hämta information om de virtuella datorer som ska replikeras

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Hämta information om den virtuella datorns diskar. Den här informationen kommer att användas senare när du startar replikering av den virtuella datorn.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Replikera en virtuell Azure-dator

I följande exempel förutsätter vi att du redan har ett cache Storage-konto, replikeringsprincip och mappningar. Om du inte har dessa saker följer du processen vid [Konfigurera haveri beredskap för virtuella Azure-datorer med hjälp av Azure PowerShell](azure-to-azure-powershell.md).

Replikera en virtuell Azure-dator med *hanterade diskar*.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication.

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

När åtgärden starta-replikeringen lyckades replikeras VM-data till återställnings regionen.

Du kan gå till Azure Portal och se de replikerade virtuella datorerna under replikerade objekt.

Replikeringen börjar genom att en kopia av de replikerade diskarna för den virtuella datorn i återställnings regionen dirigeras. Den här fasen kallas fasen inledande-replikering.

När den inledande replikeringen har slutförts flyttas replikeringen vidare till fasen differentiell-synkronisering. Den virtuella datorn är i det här läget skyddad. Välj den skyddade virtuella datorn för att se om några diskar är uteslutna.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att [köra ett redundanstest](site-recovery-test-failover-to-azure.md).
