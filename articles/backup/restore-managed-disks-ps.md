---
title: Återställa Azure-Managed Disks via Azure PowerShell
description: Lär dig hur du återställer Azure-Managed Disks med hjälp av Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 0ddf552947c39692ea01d0dea7e67f147d754fcc
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630571"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Återställa Azure-Managed Disks med Azure PowerShell

I den här artikeln förklaras hur du återställer [Azure-Managed disks](../virtual-machines/managed-disks-overview.md) från en återställnings punkt som skapats av Azure Backup.

För närvarande stöds inte alternativet Original-Location återställning (OLR) för återställning genom att ersätta den befintliga käll disken där säkerhets kopiorna gjordes. Du kan återställa från en återställnings punkt för att skapa en ny disk antingen i samma resurs grupp som käll disken från vilken säkerhets kopian gjordes eller i någon annan resurs grupp. Detta kallas Alternate-Location återställning (återställning till) och detta hjälper till att hålla både käll disken och den återställda (nya) disken.

I den här artikeln får du lära dig att:

- Återställ för att skapa en ny disk

- Spåra status för återställnings åtgärden

Vi kommer att referera till ett befintligt säkerhets kopierings valv "TestBkpVault" under resurs gruppen "testBkpVaultRG" i exemplen

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>Återställ för att skapa en ny disk

### <a name="setting-up-permissions"></a>Konfigurera behörigheter

Säkerhets kopierings valv använder hanterad identitet för att få åtkomst till andra Azure-resurser. För att återställa från säkerhets kopian kräver säkerhets kopierings valvets hanterade identitet en uppsättning behörigheter för resurs gruppen där disken ska återställas.

Säkerhets kopierings valvet använder en systemtilldelad hanterad identitet, som är begränsad till en per resurs och är kopplad till livs cykeln för den här resursen. Du kan bevilja behörighet till den hanterade identiteten med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC). Hanterad identitet är ett tjänst objekt av en särskild typ som bara kan användas med Azure-resurser. Läs mer om [hanterade identiteter](../active-directory/managed-identities-azure-resources/overview.md).

Tilldela de relevanta behörigheterna för valvets systemtilldelade hanterade identitet i mål resurs gruppen där diskarna ska återställas/skapas enligt vad som anges [här](restore-managed-disks.md#restore-to-create-a-new-disk).

### <a name="fetching-the-relevant-recovery-point"></a>Hämtar relevant återställnings punkt

Hämta alla instanser med kommandot [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) och identifiera den relevanta instansen.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

Du kan också använda **AZ. Resourcegraph** och kommandot [search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) för att söka i flera instanser i många valv och prenumerationer.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

När instansen har identifierats hämtar du relevant återställnings punkt.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>Förbereder återställnings förfrågan

Konstruera ARM-ID: t för den nya disken som ska skapas med mål resurs gruppen, till vilka behörigheter som tilldelats enligt beskrivningen [ovan](#setting-up-permissions), och det nödvändiga disk namnet. En disk kan till exempel ha namnet **PSTestDisk2** under en resurs grupp **targetrg** med en annan prenumeration.

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

Använd kommandot [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) för att förbereda återställnings förfrågan med all relevant information.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>Utlös återställningen

Använd kommandot [Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) för att utlösa återställningen med begäran för beredd ovan.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>Spårnings jobb

Spåra alla jobb med kommandot [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) . Du kan visa en lista över alla jobb och hämta information om ett visst jobb.

Du kan också använda **AZ. ResourceGraph** för att spåra alla jobb i alla säkerhets kopierings valv. Använd kommandot [search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) för att hämta det relevanta jobbet, som kan finnas i alla säkerhets kopierings valv.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Nästa steg

- [Vanliga frågor och svar om Azure disk backup](disk-backup-faq.md)
