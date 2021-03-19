---
title: Hantera säkerhets kopior av Azure-filresurser med PowerShell
description: Lär dig hur du använder PowerShell för att hantera och övervaka Azure-filresurser som har säkerhetskopierats av tjänsten Azure Backup.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: e2f07e56fb9a8715b1b53165ab5f4b45b4e20ccb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89000234"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>Hantera säkerhets kopior av Azure-filresurser med PowerShell

Den här artikeln beskriver hur du använder Azure PowerShell för att hantera och övervaka Azure-filresurser som säkerhets kopie ras av Azure Backups tjänsten.

> [!WARNING]
> Kontrol lera att PowerShell-versionen har uppgraderats till den lägsta versionen för "AZ. RecoveryServices 2.6.0" för AFS-säkerhetskopieringar. Mer information finns i [avsnittet](backup-azure-afs-automation.md#important-notice-backup-item-identification) disponera kravet på den här ändringen.

## <a name="modify-the-protection-policy"></a>Ändra skydds principen

Om du vill ändra principen som används för att säkerhetskopiera Azure-filresursen använder du [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Ange relevant säkerhets kopierings objekt och den nya säkerhets kopierings principen.

I följande exempel ändras skydds principen för **testAzureFS** från **dailyafs** till **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>Spåra säkerhets kopierings-och återställnings jobb

Säkerhets kopierings-och återställnings åtgärder på begäran returnerar ett jobb tillsammans med ett ID, som visas när du [kör en säkerhets kopiering på begäran](backup-azure-afs-automation.md#trigger-an-on-demand-backup). Använd cmdleten [Get-AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) för att spåra jobb förlopp och information.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```

## <a name="stop-protection-on-a-file-share"></a>Avbryta skyddet av en filresurs

Det finns två sätt att sluta skydda Azure-filresurser:

* Stoppa alla framtida säkerhets kopierings jobb och *ta bort* alla återställnings punkter
* Stoppa alla framtida säkerhets kopierings jobb men *lämna* återställnings punkterna

Det kan finnas en kostnad för att lämna återställnings punkterna i lagret, eftersom de underliggande ögonblicks bilderna som skapats av Azure Backup bevaras. Men fördelen med att lämna återställnings punkterna är att du kan återställa fil resursen senare, om så önskas. Information om kostnaden för att lämna återställnings punkterna finns i [pris informationen](https://azure.microsoft.com/pricing/details/storage/files/). Om du väljer att ta bort alla återställnings punkter kan du inte återställa fil resursen.

## <a name="stop-protection-and-retain-recovery-points"></a>Stoppa skyddet och behåll återställnings punkter

Använd cmdleten [disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) för att stoppa skyddet när du behåller data.

I följande exempel slutar skydd för *afsfileshare* -filresursen men behåller alla återställnings punkter:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

Jobb-ID-attributet i utdata motsvarar jobb-ID: t för jobbet som skapats av säkerhets kopierings tjänsten för åtgärden "stoppa skydd". Använd cmdleten [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) för att spåra jobbets status.

## <a name="stop-protection-without-retaining-recovery-points"></a>Stoppa skyddet utan att behålla återställnings punkter

Om du vill stoppa skyddet utan att behålla återställnings punkter använder du cmdleten [disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) och lägger till parametern **-RemoveRecoveryPoints** .

I följande exempel stoppas skyddet för *afsfileshare* -filresursen utan att återställnings punkterna bevaras:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om](manage-afs-backup.md) att hantera säkerhets kopior av Azure-filresurser i Azure Portal.
