---
title: Selektiv säkerhets kopiering och återställning av diskar för virtuella Azure-datorer
description: I den här artikeln lär du dig mer om säkerhets kopiering och återställning av selektiva diskar med den virtuella Azure-datorn säkerhets kopierings lösning.
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: e82c959dc63222e8565243cc9ac805283cab6617
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501844"
---
# <a name="selective-disk-backup-and-restore-for-azure-virtual-machines"></a>Selektiv säkerhets kopiering och återställning av diskar för virtuella Azure-datorer

Azure Backup stöder säkerhets kopiering av alla diskar (operativ system och data) i en virtuell dator tillsammans med säkerhets kopierings lösningen för virtuella datorer. Nu kan du använda säkerhets kopierings-och återställnings funktionen selektiva diskar för att säkerhetskopiera en delmängd av data diskarna i en virtuell dator. Detta ger en effektiv och kostnads effektiv lösning för dina säkerhets kopierings-och återställnings behov. Varje återställnings punkt innehåller bara de diskar som ingår i säkerhets kopierings åtgärden. Detta gör att du kan ha en delmängd av diskarna som återställs från den aktuella återställnings punkten under återställnings åtgärden. Detta gäller både för återställning från ögonblicks bilder och valvet.

## <a name="scenarios"></a>Scenarier

Den här lösningen är användbar särskilt i följande scenarier:

1. Om du har viktiga data som ska säkerhets kopie ras på bara en disk eller en del av diskarna och inte vill säkerhetskopiera resten av diskarna som är anslutna till en virtuell dator för att minimera kostnaderna för lagring av säkerhets kopior.
2. Om du har andra säkerhets kopierings lösningar för en del av din virtuella dator eller dina data. Om du t. ex. säkerhetskopierar dina databaser eller data med en annan lösning för säkerhets kopiering av arbets belastning och vill använda säkerhets kopiering av Azure VM-nivå för resten av data eller diskarna för att skapa ett effektivt och stabilt system med hjälp av bästa tillgängliga funktioner.

Med PowerShell eller Azure CLI kan du konfigurera en selektiv säkerhets kopiering av den virtuella Azure-datorn.  Med hjälp av ett skript kan du inkludera eller exkludera data diskar med deras LUN-nummer.  För närvarande är möjligheten att konfigurera säkerhets kopiering av selektiva diskar via Azure Portal begränsad till alternativet för att **säkerhetskopiera OS** -diskar. Så du kan konfigurera säkerhets kopiering av din virtuella Azure-dator med operativ system disk och undanta alla data diskar som är anslutna till den.

>[!NOTE]
> OS-disken läggs som standard till i säkerhets kopian av den virtuella datorn och kan inte uteslutas.

## <a name="using-azure-cli"></a>Använda Azure CLI

Se till att du använder AZ CLI version 2.0.80 eller högre. Du kan hämta CLI-versionen med det här kommandot:

```azurecli
az --version
```

Logga in på det prenumerations-ID där Recovery Services valvet och den virtuella datorn finns:

```azurecli
az account set -s {subscriptionID}
```

>[!NOTE]
>Endast **resourcegroup** -namnet (inte objektet) som motsvarar valvet krävs i varje kommando nedan.

### <a name="configure-backup-with-azure-cli"></a>Konfigurera säkerhets kopiering med Azure CLI

Under åtgärden konfigurera skydd måste du ange disk List inställningen med en   /  parameter för inkludering av **undantag** , vilket ger LUN-numren för diskarna som ska tas med eller undantas i säkerhets kopieringen.

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting exclude --diskslist 0 1
```

Om den virtuella datorn inte finns i samma resurs grupp som valvet refererar **ResourceGroup** till resurs gruppen där valvet skapades. I stället för namnet på den virtuella datorn anger du det VM-ID: t som anges nedan.

```azurecli
az backup protection enable-for-vm  --resource-group {ResourceGroup} --vault-name {vaultname} --vm $(az vm show -g VMResourceGroup -n MyVm --query id --output tsv) --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

### <a name="modify-protection-for-already-backed-up-vms-with-azure-cli"></a>Ändra skydd för redan säkerhetskopierade virtuella datorer med Azure CLI

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting exclude --diskslist {LUN number(s) separated by space}
```

### <a name="backup-only-os-disk-during-configure-backup-with-azure-cli"></a>Säkerhetskopiera endast en operativ system disk under Konfigurera säkerhets kopiering med Azure CLI

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --exclude-all-data-disks
```

### <a name="backup-only-os-disk-during-modify-protection-with-azure-cli"></a>Säkerhetskopiera endast OS-disk under ändra skydd med Azure CLI

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --exclude-all-data-disks
```

### <a name="restore-disks-with-azure-cli"></a>Återställa diskar med Azure CLI

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --diskslist {LUN number of the disk(s) to be restored}
```

### <a name="restore-only-os-disk-with-azure-cli"></a>Återställa endast OS-diskar med Azure CLI

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} } --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-only-osdisk
```

### <a name="get-protected-item-to-get-disk-exclusion-details-with-azure-cli"></a>Hämta skyddat objekt för att hämta information om disk undantag med Azure CLI

```azurecli
az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

Det finns ytterligare en **diskExclusionProperties** -parameter som har lagts till i det skyddade objektet enligt nedan:

```azurecli
"extendedProperties": {
      "diskExclusionProperties": {
        "diskLunList": [
          0,
          1
        ],
        "isInclusionList": true
      }
```

### <a name="get-backup-job-with-azure-cli"></a>Hämta säkerhets kopierings jobb med Azure CLI

```azurecli
az backup job show --vault-name {vaultname} --resource-group {resourcegroup} -n {BackupJobID}
```

Med det här kommandot får du information om de säkerhetskopierade diskarna och exkluderade diskar enligt nedan:

```output
   "Backed-up disk(s)": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b; diskextest_DataDisk_0; diskextest_DataDisk_1",  "Backup Size": "0 MB",
   "Excluded disk(s)": "diskextest_DataDisk_2",
```

### <a name="list-recovery-points-with-azure-cli"></a>Lista återställnings punkter med Azure CLI

```azurecli
az backup recoverypoint list --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM
```

Detta ger information om antalet diskar som är anslutna och säkerhets kopie ras på den virtuella datorn.

```azurecli
      "recoveryPointDiskConfiguration": {
        "excludedDiskList": null,
        "includedDiskList": null,
        "numberOfDisksAttachedToVm": 4,
        "numberOfDisksIncludedInBackup": 3
};
```

### <a name="get-recovery-point-with-azure-cli"></a>Hämta återställnings punkt med Azure CLI

```azurecli
az backup recoverypoint show --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -n {recoverypointID}
```

Varje återställnings punkt innehåller information om de inkluderade och exkluderade diskarna:

```azurecli
  "recoveryPointDiskConfiguration": {
      "excludedDiskList": [
        {
          "lun": 2,
          "name": "diskextest_DataDisk_2"
        }
      ],
      "includedDiskList": [
        {
          "lun": -1,
          "name": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b"
        },
        {
          "lun": 0,
          "name": "diskextest_DataDisk_0"
        },
        {
          "lun": 1,
          "name": "diskextest_DataDisk_1"
        }
      ],
      "numberOfDisksAttachedToVm": 4,
      "numberOfDisksIncludedInBackup": 3
```

### <a name="remove-disk-exclusion-settings-and-get-protected-item-with-azure-cli"></a>Ta bort inställningar för disk undantag och hämta skyddat objekt med Azure CLI

```azurecli
az backup protection update-for-vm --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting resetexclusionsettings

az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

När du kör de här kommandona visas `"diskExclusionProperties": null` .

## <a name="using-powershell"></a>Använda PowerShell

Se till att du använder Azure PowerShell version 3.7.0 eller senare.

Under den Konfigurera skydds åtgärden måste du ange disk List inställningen med en parameter för inkludering/uteslutning, vilket ger LUN-numren för diskarna som ska tas med eller undantas i säkerhets kopian.

### <a name="enable-backup-with-powershell"></a>Aktivera säkerhets kopiering med PowerShell

Exempel:

```azurepowershell
$disks = ("0","1")
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "rg-p-recovery_vaults" -Name "rsv-p-servers"
Get-AzRecoveryServicesBackupProtectionPolicy
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "P-Servers"
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -InclusionDisksList $disks -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExclusionDisksList $disks -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-configure-backup-with-powershell"></a>Säkerhetskopiera endast OS-disk under Konfigurera säkerhets kopiering med PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="get-backup-item-object-to-be-passed-in-modify-protection-with-powershell"></a>Hämta objektet för säkerhets kopiering som ska skickas i ändra skydd med PowerShell

```azurepowershell
$item= Get-AzRecoveryServicesBackupItem -BackupManagementType "AzureVM" -WorkloadType "AzureVM" -VaultId $targetVault.ID -FriendlyName "V2VM"
```

Du måste skicka ovanstående **$item** -objektet till--- **-parametern i** följande cmdletar.

### <a name="modify-protection-for-already-backed-up-vms-with-powershell"></a>Ändra skydd för redan säkerhetskopierade virtuella datorer med PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -InclusionDisksList[Strings] -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ExclusionDisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-modify-protection-with-powershell"></a>Säkerhetskopiera endast OS-disk under ändra skydd med PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="reset-disk-exclusion-setting-with-powershell"></a>Återställ disk undantags inställningen med PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ResetExclusionSettings -VaultId $targetVault.ID
```

### <a name="restore-selective-disks-with-powershell"></a>Återställa selektiva diskar med PowerShell

```azurepowershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $item -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreDiskList [$disks]
```

### <a name="restore-only-os-disk-with-powershell"></a>Återställa endast OS-diskar med PowerShell

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreOnlyOSDisk
```

## <a name="using-the-azure-portal"></a>Använda Azure Portal

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

Med hjälp av Azure Portal kan du Visa de inkluderade och exkluderade diskarna i informations fönstret för säkerhets kopiering av virtuella datorer och informations fönstret för säkerhets kopierings jobbet.  När du väljer återställnings punkt att återställa från under återställning kan du Visa de säkerhetskopierade diskarna i återställnings punkten.

Här kan du Visa de inkluderade och exkluderade diskarna för en virtuell dator i portalen i informations fönstret för säkerhets kopiering av virtuell dator:

![Visa inkluderade och exkluderade diskar från informations fönstret för säkerhets kopiering](./media/selective-disk-backup-restore/backup-details.png)

Här kan du Visa de inkluderade och exkluderade diskarna i en säkerhets kopia från jobb informations fönstret:

![Visa inkluderade och exkluderade diskar från fönstret jobb information](./media/selective-disk-backup-restore/job-details.png)

Här kan du Visa de säkerhetskopierade diskarna under återställningen, när du väljer den återställnings punkt som du vill återställa från:

![Visa säkerhetskopierade diskar under återställningen](./media/selective-disk-backup-restore/during-restore.png)

Konfigurering av de selektiva diskarna för säkerhets kopiering för en virtuell dator via Azure Portal är begränsad till alternativet för att **säkerhetskopiera OS** -diskar. Använd PowerShell eller Azure CLI om du vill använda säkerhets kopiering av selektiva diskar på redan en säkerhets kopia av en virtuell dator eller för avancerad inkludering eller uteslutning av vissa data diskar för en virtuell dator.

>[!NOTE]
>Om data sträcker sig över flera diskar ser du till att alla beroende diskar ingår i säkerhets kopian. Om du inte säkerhetskopierar alla beroende diskar i en volym skapas inte volymen som omfattar vissa icke-säkerhetskopierade diskar.

### <a name="backup-os-disk-only-in-the-azure-portal"></a>Säkerhetskopiera OS-disken endast i Azure Portal

När du aktiverar säkerhets kopiering med Azure Portal kan du välja alternativet **säkerhetskopiera endast operativ system disk** . Så du kan konfigurera säkerhets kopiering av din virtuella Azure-dator med OS-disk och undanta alla data diskar som är anslutna till den.

![Konfigurera säkerhets kopiering enbart för operativ system disken](./media/selective-disk-backup-restore/configure-backup-operating-system-disk.png)

## <a name="using-azure-rest-api"></a>Använda Azure REST API

Du kan konfigurera virtuella Azure-säkerhetskopieringar med några valda diskar eller så kan du ändra en befintlig VM-skydd så att det innehåller/undantar några diskar som dokumenteras [här](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup).

## <a name="selective-disk-restore"></a>Selektiv disk återställning

Selektiv disk återställning är en extra funktion som du får när du aktiverar funktionen för att säkerhetskopiera selektiva diskar. Med den här funktionen kan du återställa selektiva diskar från alla diskar som har säkerhetskopierats i en återställnings punkt. Det är mer effektivt och hjälper till att spara tid i scenarier där du vet vilka diskar som behöver återställas.

- OS-disken ingår som standard i säkerhets kopiering och återställning av virtuella datorer och kan inte uteslutas.
- Selektiv disk återställning stöds bara för återställnings punkter som skapats efter att funktionen disk undantag har Aktiver ATS.
- Säkerhets kopiering med inställningen disk exkludera **på** stöder bara alternativet **disk återställning** . **Återställning av virtuella datorer** eller **ersätta befintliga** återställnings alternativ stöds inte i det här fallet.

![Alternativet för att återställa VM och ersätta befintligt är inte tillgängligt under återställnings åtgärden](./media/selective-disk-backup-restore/options-not-available.png)

## <a name="limitations"></a>Begränsningar

Säkerhets kopierings funktionen selektiva diskar stöds inte för klassiska virtuella datorer och krypterade virtuella datorer. Därför stöds inte virtuella Azure-datorer som är krypterade med Azure Disk Encryption (ADE) med BitLocker för kryptering av Windows VM och funktionen dm-crypt för virtuella Linux-datorer stöds inte.

Återställnings alternativen för att **skapa en ny virtuell dator** och **ersätta befintliga** stöds inte för den virtuella dator där funktionen selektiva diskar säkerhets kopiering är aktive rad.

Azure VM Backup stöder för närvarande inte virtuella datorer med extremt diskar eller delade diskar kopplade till dem. Selektiv disk säkerhets kopiering kan inte användas i sådana fall, vilket utesluter disken och säkerhets kopian av den virtuella datorn.

## <a name="billing"></a>Fakturering

Säkerhets kopiering av virtuella Azure-datorer följer den befintliga pris sättnings modellen, som beskrivs i detalj [här](https://azure.microsoft.com/pricing/details/backup/).

**Skyddad instans (PI) kostnad** beräknas enbart för operativ system disken om du väljer att säkerhetskopiera med alternativet **endast operativ system disk** .  Om du konfigurerar säkerhets kopiering och väljer minst en datadisk kommer PI-kostnaden att beräknas för alla diskar som är anslutna till den virtuella datorn. **Lagrings kostnaden för säkerhets kopiering** beräknas utifrån enbart de diskar som ingår och så att du kan spara pengar på lagrings kostnaderna. **Ögonblicks bildens kostnad** beräknas alltid för alla diskar i den virtuella datorn (både inkluderade och exkluderade diskar).

Om du har valt funktionen för återställning av kors region (CRR) gäller [CRR-priset](https://azure.microsoft.com/pricing/details/backup/) på lagrings kostnaden för säkerhets kopieringen när disken har exkluderats.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-is-protected-instance-pi-cost-calculated-for-only-os-disk-backup-in-windows-and-linux"></a>Hur beräknas en skyddad instans (PI) endast för säkerhets kopiering av OS-diskar i Windows och Linux?

PI-kostnaden beräknas baserat på den faktiska (använda) storleken på den virtuella datorn.

- För Windows: använt utrymmes beräkning baseras på den enhet där operativ systemet lagras (vanligt vis C:).
- För Linux: använt utrymmes beräkning baseras på den enhet där rot fil systemet (/) är monterat.

### <a name="i-have-configured-only-os-disk-backup-why-is-the-snapshot-happening-for-all-the-disks"></a>Jag har bara konfigurerat säkerhets kopiering av operativ system diskar, varför sker ögonblicks bilden för alla diskar?

Med funktionerna selektiv säkerhets kopiering av disk kan du spara pengar på säkerhets kopierings valv lagrings kostnader genom att skärp de inkluderade diskarna som ingår i säkerhets kopieringen. Ögonblicks bilden tas dock för alla diskar som är anslutna till den virtuella datorn. Ögonblicks bildens kostnad beräknas alltid för alla diskar i den virtuella datorn (både inkluderade och exkluderade diskar). Mer information finns i [fakturering](#billing).

### <a name="i-cant-configure-backup-for-the-azure-virtual-machine-by-excluding-ultra-disk-or-shared-disks-attached-to-the-vm"></a>Jag kan inte konfigurera säkerhets kopiering för den virtuella Azure-datorn genom att utesluta Ultra disk eller delade diskar som är kopplade till den virtuella datorn

Funktionen selektiv disk säkerhets kopiering är en funktion som finns ovanpå säkerhets kopierings lösningen för virtuella Azure-datorer. Azure VM Backup stöder för närvarande inte virtuella datorer med en mycket disk eller delad disk ansluten till dem.

## <a name="next-steps"></a>Nästa steg

- [Stödmatris för säkerhetskopiering av virtuella Azure-datorer](backup-support-matrix-iaas.md)
- [Vanliga frågor och svar – säkerhetskopiera virtuella Azure-datorer](backup-azure-vm-backup-faq.yml)
