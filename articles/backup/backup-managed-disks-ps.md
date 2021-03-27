---
title: Säkerhetskopiera Azure Managed Disks med Azure PowerShell
description: Lär dig hur du säkerhetskopierar Azure-Managed Disks med Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 2e286128185c1ac7fe4861a9b06de52e10d4139a
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630591"
---
# <a name="back-up-azure-managed-disks-using-azure-powershell"></a>Säkerhetskopiera Azure Managed Disks med Azure PowerShell

I den här artikeln förklaras hur du säkerhetskopierar [Azure Managed disk](../virtual-machines/managed-disks-overview.md) med Azure PowerShell.

I den här artikeln får du lära dig att:

- Skapa ett säkerhets kopierings valv

- Skapa en säkerhetskopieringspolicy

- Konfigurera en säkerhets kopia av en Azure-disk

- Köra ett säkerhets kopierings jobb på begäran

Information om tillgänglighet för Azure disk backup, stödda scenarier och begränsningar finns i [support mat ris](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Skapa ett säkerhets kopierings valv

Ett säkerhets kopierings valv är en lagrings enhet i Azure som innehåller säkerhets kopierings data för olika nyare arbets belastningar som Azure Backup stöder, till exempel Azure Database for PostgreSQL-servrar och Azure-diskar. Säkerhets kopierings valv gör det enkelt att organisera dina säkerhets kopierings data samtidigt som du minimerar hanterings kostnaderna. Säkerhets kopierings valv baseras på Azure Resource Manager modell för Azure, som tillhandahåller förbättrade funktioner för att skydda säkerhetskopierade data.

Innan du skapar ett säkerhets kopierings valv väljer du lagrings redundans för data i valvet. Fortsätt sedan att skapa säkerhets kopierings valvet med den lagrings redundansen och platsen. I den här artikeln ska vi skapa ett säkerhets kopierings valv "TestBkpVault" i regionen "väst" under resurs gruppen "testBkpVaultRG". Använd kommandot [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) för att skapa ett säkerhets kopierings valv. Lär dig mer om att [skapa ett säkerhets kopierings valv](./backup-vault-overview.md#create-a-backup-vault).

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore
New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

När du har skapat valvet ska vi skapa en säkerhets kopierings princip för att skydda Azure-diskar.

## <a name="create-a-backup-policy"></a>Skapa en säkerhets kopierings princip

Om du vill förstå de inre komponenterna i en säkerhets kopierings princip för säkerhets kopiering av Azure-diskar hämtar du princip mal len med kommandot [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true). Det här kommandot returnerar en standard princip mal len för en specifik DataSource-typ. Använd den här princip mal len för att skapa en ny princip.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDisk
$policyDefn | fl


DatasourceType : {Microsoft.Compute/disks}
ObjectType     : BackupPolicy
PolicyRule     : {BackupHourly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Princip mal len består av en utlösare (som bestämmer vad som utlöser säkerhets kopieringen) och en livs cykel (som avgör när säkerhets kopian ska tas bort/kopieras/flyttas). I Azure disk Backup är standardvärdet för utlösare en schemalagd Tim utlösare för var fjärde timme (PT4H) och för att behålla varje säkerhets kopia i 7 dagar.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2020-04-05T13:00:00+00:00/PT4H}
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P7D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

Azure disk Backup erbjuder flera säkerhets kopior per dag. Om du behöver mer frekventa säkerhets kopieringar väljer du säkerhets kopierings frekvensen varje **timme** med möjlighet att göra säkerhets kopior med intervaller var fjärde, 6, 8 eller 12 timmar. Säkerhets kopiorna schemaläggs utifrån det **tidsintervall** som valts. Om du till exempel väljer **var fjärde timme** så tas säkerhets kopiorna ungefär i intervallet var fjärde timme, så att säkerhets kopiorna distribueras jämnt över dagen. Om det räcker med en sådan säkerhets kopiering kan du välja den **dagliga** säkerhets kopierings frekvensen. I frekvensen för daglig säkerhets kopiering kan du ange tiden på dagen då dina säkerhets kopieringar görs. Det är viktigt att notera att tiden på dagen anger start tiden för säkerhets kopieringen och inte tidpunkten då säkerhets kopieringen slutförts. Tiden som krävs för att slutföra säkerhets kopierings åtgärden beror på olika faktorer, inklusive storleken på disken och omsättnings takten mellan på varandra följande säkerhets kopieringar. Säkerhets kopiering av Azure-diskar är dock en agent utan agent som använder [stegvisa ögonblicks bilder](../virtual-machines/disks-incremental-snapshots.md), vilket inte påverkar produktions programmets prestanda.

   >[!NOTE]
   > Även om det valda valvet kan ha inställningen för global redundans, stöder Azure disk backup endast Snapshot-datalager. Alla säkerhets kopior lagras i en resurs grupp i din prenumeration och kopieras inte till lagringen för säkerhets kopierings valv.

Mer information om hur du skapar en princip finns i dokumentet [säkerhets kopierings princip för Azure-disk](backup-managed-disks.md#create-backup-policy) .

Om du vill redigera Tim frekvensen eller kvarhållningsperioden använder du kommandona [Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-5.7.0&preserve-view=true) och/eller [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-5.7.0&preserve-view=true) . När principobjektet har alla önskade värden fortsätter du med att skapa en ny princip från principobjektet med hjälp av kommandot [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name diskBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
diskBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$diskBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "diskBkpPolicy"
```

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering

När valvet och principen har skapats finns det 3 kritiska punkter som användaren behöver tänka på för att skydda en Azure-disk.

### <a name="key-entities-involved"></a>Viktiga enheter som ingår

#### <a name="disk-to-be-protected"></a>Disk som ska skyddas

Hämta ARM-ID för disken som ska skyddas. Detta fungerar som identifierare för disken. Vi kommer att använda ett exempel på en disk med namnet "PSTestDisk" i resurs gruppen "diskrg" under en annan prenumeration.

```azurepowershell-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/PSTestDisk"
```

#### <a name="snapshot-resource-group"></a>Resurs grupp för ögonblicks bild

Disk ögonblicks bilderna lagras i en resurs grupp i din prenumeration. Som en rikt linje rekommenderar vi att du skapar en dedikerad resurs grupp som ett ögonblicks bild data lager som ska användas av den Azure Backup tjänsten. Genom att ha en dedikerad resurs grupp kan du begränsa åtkomst behörigheterna för resurs gruppen, vilket ger säkerhet och enkel hantering av säkerhets kopierings data. Observera ARM-ID: t för den resurs grupp där du vill placera disk ögonblicks bilderna

```azurepowershell-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Backup-valv

Säkerhets kopierings valven kräver behörigheter på disk och resurs gruppen för ögonblicks bilder för att kunna utlösa ögonblicks bilder och hantera deras livs cykel. Den systemtilldelade hanterade identiteten för valvet används för att tilldela sådana behörigheter. Använd kommandot [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault?view=azps-5.7.0&preserve-view=true) för att aktivera systemtilldelad hanterad identitet för Recovery Services-valvet.

### <a name="assign-permissions"></a>Tilldela behörigheter

Användaren behöver tilldela några behörigheter via RBAC till valv (representeras av valv-MSI) och relevant disk och/eller disk RG. Dessa kan utföras via portalen eller PowerShell. Alla relaterade behörigheter beskrivs i punkterna 1, 2, 3 i [det här avsnittet](backup-managed-disks.md#configure-backup).

### <a name="prepare-the-request"></a>Förbered begäran

När alla relevanta behörigheter har angetts utförs konfigurationen av säkerhets kopieringen i två steg. Först förbereder vi den relevanta begäran genom att använda det relevanta valvet, principen, disk-och ögonblicks bilds resurs gruppen med kommandot [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) . Sedan skickar vi begäran om att skydda disken med kommandot [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) .

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDisk -DatasourceLocation $TestBkpvault.Location -PolicyId $diskBkpPol[0].Id -DatasourceId $DiskId 
$instance.Property.PolicyInfo.PolicyParameter.DataStoreParametersList[0].ResourceGroupId = $snapshotrg
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166
```

## <a name="run-an-on-demand-backup"></a>Köra en säkerhetskopiering på begäran

Hämta relevant säkerhets kopierings instans där användaren vill utlösa en säkerhets kopia med hjälp av [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true)

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

Du kan ange en bevarande regel när du aktiverar säkerhets kopiering. Om du vill visa bevarande reglerna i principen navigerar du genom objektet princip för kvarhållning av regler. I exemplet nedan visas regeln med namnet default och vi kommer att använda den regeln för säkerhets kopiering på begäran

```azurepowershell-interactive
$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Utlös en säkerhets kopiering på begäran med kommandot [Backup-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true) .

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="tracking-jobs"></a>Spårnings jobb

Spåra alla jobb med kommandot [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) . Du kan visa en lista över alla jobb och hämta information om ett visst jobb.

Du kan också använda AZ. ResourceGraph för att spåra alla jobb i alla säkerhets kopierings valv. Använd kommandot [search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) för att hämta det relevanta jobbet som kan finnas i alla säkerhets kopierings valv.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Nästa steg

- [Återställa Azure-Managed Disks med Azure PowerShell](restore-managed-disks-ps.md)
