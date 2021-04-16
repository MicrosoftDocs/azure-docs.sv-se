---
title: Återställa Azure Managed Disks via Azure PowerShell
description: Lär dig hur du återställer Azure Managed Disks med Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: c6625b43c313d45d4b295dd406e29a2b1d85b387
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520045"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Återställa Azure Managed Disks med Azure PowerShell

Den här artikeln beskriver hur du [återställer Azure Managed Disks](../virtual-machines/managed-disks-overview.md) från en återställningspunkt som skapats av Azure Backup.

För närvarande stöds Original-Location-återställningsalternativet (OLR) genom att ersätta den befintliga källdisken som säkerhetskopiorna gjordes från inte. Du kan återställa från en återställningspunkt för att skapa en ny disk antingen i samma resursgrupp som källdisken som säkerhetskopiorna gjordes från eller i någon annan resursgrupp. Detta kallas för Alternate-Location Recovery (ALR) och hjälper till att behålla både källdisken och den återställda (nya) disken.

I den här artikeln får du lära dig att:

- Återställa för att skapa en ny disk

- Spåra status för återställningsåtgärden

Vi refererar till det befintliga säkerhetskopieringsvalvet "TestBkpVault" under resursgruppen "testBkpVaultRG" i exemplen

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>Återställa för att skapa en ny disk

### <a name="setting-up-permissions"></a>Konfigurera behörigheter

Backup Vault använder hanterad identitet för att få åtkomst till andra Azure-resurser. För att kunna återställa från en säkerhetskopia kräver Backup-valvets hanterade identitet en uppsättning behörigheter för resursgruppen där disken ska återställas.

Säkerhetskopieringsvalvet använder en system tilldelad hanterad identitet, som är begränsad till en per resurs och är kopplad till den här resursens livscykel. Du kan bevilja behörigheter till den hanterade identiteten med hjälp av rollbaserad åtkomstkontroll i Azure (Azure RBAC). Hanterad identitet är ett huvudnamn för tjänsten av en särskild typ som bara kan användas med Azure-resurser. Läs mer om [hanterade identiteter.](../active-directory/managed-identities-azure-resources/overview.md)

Tilldela relevanta behörigheter för valvets system tilldelade hanterade identitet på målresursgruppen där diskarna ska återställas/skapas enligt vad som [anges här.](restore-managed-disks.md#restore-to-create-a-new-disk)

### <a name="fetching-the-relevant-recovery-point"></a>Hämta relevant återställningspunkt

Hämta alla instanser med kommandot [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) och identifiera relevant instans.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

Du kan också använda **kommandot Az.Resourcegraph** och [kommandot Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) för att söka igenom instanser i många valv och prenumerationer.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

När instansen har identifierats hämtar du den relevanta återställningspunkten.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>Förbereda återställningsbegäran

Konstruera ARM-ID:t för den nya disken som ska skapas med målresursgruppen, som behörigheterna har tilldelats enligt beskrivningen ovan [och](#setting-up-permissions)det nödvändiga disknamnet. En disk kan till exempel ha namnet **PSTestDisk2** under en **resursgruppmålgrupp med** en annan prenumeration.

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

Använd kommandot [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) för att förbereda återställningsbegäran med all relevant information.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>Utlösa återställningen

Använd kommandot [Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) för att utlösa återställningen med den begäran som förbereddes ovan.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>Spåra jobb

Spåra alla jobb med kommandot [Get-AzDataProtectionJob.](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) Du kan lista alla jobb och hämta en viss jobbinformation.

Du kan också använda **Az.ResourceGraph för** att spåra alla jobb i alla säkerhetskopieringsvalv. Använd kommandot [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) för att hämta det relevanta jobbet, som kan finnas i alla säkerhetskopieringsvalv.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Nästa steg

- [Vanliga frågor och svar om Azure Disk Backup](disk-backup-faq.yml)
