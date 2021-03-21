---
title: Återställa Azure Files med PowerShell
description: I den här artikeln får du lära dig hur du återställer Azure Files med hjälp av Azure Backup-tjänsten och PowerShell.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 60c9848e12de80bcafe4553a9e8f3e27e8876d41
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021392"
---
# <a name="restore-azure-files-with-powershell"></a>Återställa Azure Files med PowerShell

I den här artikeln förklaras hur du återställer en hel fil resurs eller vissa filer från en återställnings punkt som skapats av tjänsten [Azure Backup](backup-overview.md) med Azure PowerShell.

Du kan återställa en hel fil resurs eller vissa filer på resursen. Du kan återställa till den ursprungliga platsen eller till en annan plats.

> [!WARNING]
> Kontrol lera att PowerShell-versionen har uppgraderats till den lägsta versionen för "AZ. RecoveryServices 2.6.0" för AFS-säkerhetskopieringar. Mer information finns i [avsnittet](backup-azure-afs-automation.md#important-notice-backup-item-identification) disponera kravet för den här ändringen.

>[!NOTE]
>Azure Backup stöder nu återställning av flera filer eller mappar till den ursprungliga eller alternativa platsen med hjälp av PowerShell. Läs mer i [det här avsnittet](#restore-multiple-files-or-folders-to-original-or-alternate-location) av dokumentet.

## <a name="fetch-recovery-points"></a>Hämta återställnings punkter

Använd [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) för att visa en lista över alla återställnings punkter för det säkerhetskopierade objektet.

I följande skript:

* Variabeln **$RP** är en matris av återställnings punkter för det valda säkerhets kopierings objektet från de senaste sju dagarna.
* Matrisen sorteras i omvänd ordning med den senaste återställnings punkten vid index **0**.
* Använd standard PowerShell-matrisering för att välja återställnings punkt.
* I exemplet väljer **$RP [0]** den senaste återställnings punkten.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"
$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID
$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0] | fl
```

Utdata ser ut ungefär så här.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

När du har valt den relevanta återställnings punkten återställer du fil resursen eller filen till den ursprungliga platsen eller till en annan plats.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Återställa en Azure-filresurs till en annan plats

Använd [restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) för att återställa till den valda återställnings punkten. Ange följande parametrar för att identifiera den alternativa platsen:

* **TargetStorageAccountName**: det lagrings konto som det säkerhetskopierade innehållet återställs till. Mål lagrings kontot måste finnas på samma plats som valvet.
* **TargetFileShareName**: de fil resurser i mål lagrings kontot som det säkerhetskopierade innehållet återställs till.
* **TargetFolder**: mappen under den fil resurs som data återställs till. Om det säkerhetskopierade innehållet ska återställas till en rotmapp, ger du målmappens värden som en tom sträng.
* **ResolveConflict**: instruktion om det finns en konflikt med återställda data. Accepterar **överskrivning** eller **Skip**.

Kör cmdleten med parametrarna på följande sätt:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Kommandot returnerar ett jobb med ett ID som ska spåras, som du ser i följande exempel.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Återställa en Azure-fil till en annan plats

Använd [restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) för att återställa till den valda återställnings punkten. Ange de här parametrarna för att identifiera den alternativa platsen och för att unikt identifiera den fil som du vill återställa.

* **TargetStorageAccountName**: det lagrings konto som det säkerhetskopierade innehållet återställs till. Mål lagrings kontot måste finnas på samma plats som valvet.
* **TargetFileShareName**: de fil resurser i mål lagrings kontot som det säkerhetskopierade innehållet återställs till.
* **TargetFolder**: mappen under den fil resurs som data återställs till. Om det säkerhetskopierade innehållet ska återställas till en rotmapp, ger du målmappens värden som en tom sträng.
* **SourceFilePath**: den absoluta sökvägen till filen som ska återställas inom fil resursen, som en sträng. Den här sökvägen är samma sökväg som används i PowerShell-cmdleten **Get-AzStorageFile** .
* **SourceFileType**: om en katalog eller en fil är markerad. Accepterar **katalog** eller **fil**.
* **ResolveConflict**: instruktion om det finns en konflikt med återställda data. Accepterar **överskrivning** eller **Skip**.

De ytterligare parametrarna (SourceFilePath och SourceFileType) är bara relaterade till den enskilda filen som du vill återställa.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Det här kommandot returnerar ett jobb med ett ID som ska spåras, som du ser i föregående avsnitt.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Återställa Azure-filresurser och filer till den ursprungliga platsen

När du återställer till en ursprunglig plats behöver du inte ange parametrar för mål och mål. Endast **ResolveConflict** måste anges.

### <a name="overwrite-an-azure-file-share"></a>Skriv över en Azure-filresurs

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

### <a name="overwrite-an-azure-file"></a>Skriv över en Azure-fil

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Återställa flera filer eller mappar till en ursprunglig eller alternativ plats

Använd kommandot [restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) genom att skicka sökvägen till alla filer eller mappar som du vill återställa som ett värde för parametern **MultipleSourceFilePath** .

### <a name="restore-multiple-files"></a>Återställa flera filer

I följande skript försöker vi återställa *FileSharePage.png* och *MyTestFile.txt* filer.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("FileSharePage.png", "MyTestFile.txt")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType File -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

### <a name="restore-multiple-directories"></a>Återställa flera kataloger

I följande skript försöker vi återställa *zrs1_restore* och *återställa* kataloger.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("Restore","zrs1_restore")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType Directory -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

Resultatet blir något som liknar:

```output
WorkloadName         Operation         Status          StartTime                EndTime       JobID
------------         ---------         ------          ---------                -------       -----
azurefiles           Restore           InProgress      4/5/2020 8:01:24 AM                    cd36abc3-0242-44b1-9964-0a9102b74d57
```

Om du vill återställa flera filer eller mappar till en annan plats använder du skripten ovan genom att ange de parameter värden som är relaterade till mål platsen, enligt beskrivningen ovan i [återställa en Azure-fil till en annan plats](#restore-an-azure-file-to-an-alternate-location).

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om](restore-afs.md) att återställa Azure Files i Azure Portal.
