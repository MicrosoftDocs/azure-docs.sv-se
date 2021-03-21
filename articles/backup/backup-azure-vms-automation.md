---
title: Säkerhetskopiera och återställa virtuella Azure-datorer med PowerShell
description: Beskriver hur du säkerhetskopierar och återställer virtuella Azure-datorer med hjälp av Azure Backup med PowerShell
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: f59c18aecf577bc7f7d0b1360dd36504305af893
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633197"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Säkerhetskopiera och återställa virtuella Azure-datorer med PowerShell

Den här artikeln beskriver hur du säkerhetskopierar och återställer en virtuell Azure-dator i ett [Azure Backup](backup-overview.md) Recovery Services valv med hjälp av PowerShell-cmdletar.

I den här artikeln lär du dig hur du:

> [!div class="checklist"]
>
> * Skapa ett Recovery Services valv och ange valv kontexten.
> * Definierar en säkerhetskopieringspolicy
> * applicerar säkerhetskopieringspolicyn för att skydda flera virtuella datorer
> * Utlös ett säkerhets kopierings jobb på begäran för de skyddade virtuella datorerna innan du kan säkerhetskopiera (eller skydda) en virtuell dator måste du slutföra [kraven](backup-azure-arm-vms-prepare.md) för att förbereda din miljö för att skydda dina virtuella datorer.

## <a name="before-you-start"></a>Innan du börjar

* [Läs mer](backup-azure-recovery-services-vault-overview.md) om Recovery Services-valv.
* [Granska](backup-architecture.md#architecture-built-in-azure-vm-backup) arkitekturen för säkerhets kopiering av virtuella Azure-datorer, [Läs om](backup-azure-vms-introduction.md) säkerhets kopierings processen och [Granska](backup-support-matrix-iaas.md) support, begränsningar och krav.
* Granska PowerShell-objektcachen för Recovery Services.

## <a name="recovery-services-object-hierarchy"></a>Recovery Services objektets hierarki

Objektets hierarki sammanfattas i följande diagram.

![Recovery Services objektets hierarki](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Granska referens referensen för [cmdleten](/powershell/module/az.recoveryservices/) **AZ. RecoveryServices** i Azure-biblioteket.

## <a name="set-up-and-register"></a>Konfigurera och registrera

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Så här börjar du:

1. [Ladda ned den senaste versionen av PowerShell](/powershell/azure/install-az-ps)

2. Hitta Azure Backup PowerShell-cmdlet: ar som är tillgängliga genom att skriva följande kommando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Alias och cmdletar för Azure Backup, Azure Site Recovery och Recovery Services valvet visas. Följande bild är ett exempel på vad du ser. Det är inte den fullständiga listan över cmdletar.

    ![lista över Recovery Services](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Logga in på ditt Azure-konto med **Connect-AzAccount**. Den här cmdleten hämtar en webb sida där du uppmanas att ange dina autentiseringsuppgifter för ditt konto:

    * Alternativt kan du inkludera dina kontoautentiseringsuppgifter som en parameter i cmdleten **Connect-AzAccount** med hjälp av parametern **-Credential** .
    * Om du är en CSP-partner som arbetar för en klient kan du ange kunden som en klient, genom att använda sitt tenantID eller klientens primära domän namn. Exempel: **Connect-AzAccount-Tenant "fabrikam.com"**

4. Associera den prenumeration som du vill använda med kontot, eftersom ett konto kan ha flera prenumerationer:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Om du använder Azure Backup för första gången måste du använda cmdleten **[register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** för att registrera Azure Recovery Service-providern med din prenumeration.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Du kan kontrol lera att leverantörerna har registrerats med hjälp av följande kommandon:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    I kommandoutdata ska **RegistrationState** ändras till **registrerad**. Om inte, kör du bara **[register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** -cmdleten igen.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Följande steg vägleder dig genom att skapa ett Recovery Services-valv. Ett Recovery Services-valv skiljer sig från ett säkerhets kopierings valv.

1. Recovery Services valvet är en Resource Manager-resurs, så du måste placera den i en resurs grupp. Du kan använda en befintlig resurs grupp eller skapa en resurs grupp med cmdleten **[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)** . När du skapar en resurs grupp anger du namn och plats för resurs gruppen.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Använd cmdleten [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) för att skapa Recovery Services-valvet. Se till att ange samma plats för valvet som används för resurs gruppen.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Ange vilken typ av lagrings redundans som ska användas. Du kan använda [Lokalt Redundant lagring (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage), [geo-REDUNDANT lagring (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)eller [zon-redundant lagring (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage). I följande exempel visas alternativet **-BackupStorageRedundancy** för *testvault* som är inställt på den här **inställningen.**

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata. Därför är det lämpligt att lagra säkerhets kopierings Recovery Services valvet i en variabel.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Visa valv i en prenumeration

Om du vill visa alla valv i prenumerationen använder du [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault):

```powershell
Get-AzRecoveryServicesVault
```

Utdata liknar följande exempel, Observera att den associerade ResourceGroupName och platsen tillhandahålls.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="back-up-azure-vms"></a>Säkerhetskopiera virtuella Azure-datorer

Använd ett Recovery Services valv för att skydda dina virtuella datorer. Innan du tillämpar skyddet ställer du in valv kontexten (typen av data som skyddas i valvet) och kontrollerar skydds principen. Skydds principen är schemat när säkerhets kopierings jobben körs och hur länge varje ögonblicks bild av säkerhets kopian bevaras.

### <a name="set-vault-context"></a>Ange valv kontext

Innan du aktiverar skydd på en virtuell dator använder du [set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext) för att ange valv kontexten. När valvet sammanhang är inställt gäller det alla efterkommande cmdletar. I följande exempel anges valv kontexten för valvet, *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Hämta valv-ID: t

Vi planerar att föråldra inställningen för valv kontext i enlighet med Azure PowerShell rikt linjer. I stället kan du lagra eller hämta valv-ID: t och skicka det till relevanta kommandon. Så om du inte har angett valv kontexten eller vill ange kommandot som ska köras för ett visst valv, måste du skicka valv-ID: t till "-vaultID" till alla relevanta kommandon, enligt följande:

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

Eller

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>Ändra inställningar för lagrings replikering

Använd [set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) -kommandot för att ställa in Storage Replication-konfigurationen för valvet på LRS/GRS

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> Lagrings redundans kan bara ändras om det inte finns några säkerhets kopierings objekt skyddade i valvet.

### <a name="create-a-protection-policy"></a>Skapa en skydds princip

När du skapar ett Recovery Services-valv medföljer standardskydd och principer för kvarhållning. Principen för standardskydd utlöser ett säkerhetsjobb varje dag vid en viss tidpunkt. Principen för standardskydd håller kvar den dagliga återställningspunkten i 30 dagar. Du kan använda standard principen för att snabbt skydda den virtuella datorn och redigera principen senare med annan information.

Använd **[Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** för att visa de skydds principer som är tillgängliga i valvet. Du kan använda denna cmdlet för att hämta en princip eller för att visa de principer som är kopplade till en arbets belastnings typ. I följande exempel hämtas principer för arbets belastnings typ, AzureVM.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

Utdata ser ut ungefär så här:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Tids zonen för fältet BackupTime i PowerShell är UTC. Men när tiden för säkerhets kopieringen visas i Azure Portal justeras tiden till din lokala tidszon.
>
>

En säkerhets kopierings skydds princip är associerad med minst en bevarande princip. En bevarande princip definierar hur länge en återställnings punkt ska sparas innan den tas bort.

* Använd [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) för att Visa standard bevarande principen.
* På samma sätt kan du använda [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) för att hämta standard schema principen.
* Cmdlet [: en New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) skapar ett PowerShell-objekt som innehåller information om säkerhets kopierings principen.
* Schema-och bevarande princip objekt används som indata till New-AzRecoveryServicesBackupProtectionPolicy-cmdleten.

Som standard definieras en start tid i objektet Schemalägg princip. Använd följande exempel för att ändra start tiden till önskad start tid. Den önskade start tiden ska också vara i UTC-tid. I följande exempel antas den önskade start tiden vara 01:00 AM UTC för dagliga säkerhets kopieringar.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Du behöver bara ange start tiden i 30 minuter. I exemplet ovan kan det bara vara "01:00:00" eller "02:30:00". Start tiden får inte vara "01:15:00"

I följande exempel lagras schema principen och bevarande principen i variabler. Exemplet använder variablerna för att definiera parametrarna när du skapar en skydds princip, *NewPolicy*.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

Utdata ser ut ungefär så här:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Aktivera skydd

När du har definierat skydds principen måste du ändå aktivera principen för ett objekt. Använd [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) för att aktivera skydd. Att aktivera skydd kräver två objekt – objektet och principen. När principen har associerats med valvet utlöses arbets flödet för säkerhets kopiering vid den tidpunkt som anges i princip schemat.

> [!IMPORTANT]
> När du använder PowerShell för att aktivera säkerhets kopiering för flera virtuella datorer samtidigt, måste du se till att det inte finns fler än 100 virtuella datorer kopplade till en enda princip. Det här är en [rekommenderad metod](./backup-azure-vm-backup-faq.yml#is-there-a-limit-on-number-of-vms-that-can-be-associated-with-the-same-backup-policy). PowerShell-klienten blockerar för närvarande inte uttryckligen om det finns fler än 100 virtuella datorer, men kontrollen är planerad att läggas till i framtiden.

I följande exempel aktive ras skyddet för objektet, V2VM, med hjälp av principen NewPolicy. Exemplen varierar beroende på om den virtuella datorn är krypterad och vilken typ av kryptering.

Aktivera skydd på **icke-krypterade Resource Manager-virtuella datorer**:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Om du vill aktivera skydd på krypterade virtuella datorer (krypterade med BEK och KEK) måste du ge Azure Backup tjänst behörighet att läsa nycklar och hemligheter från nyckel valvet.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Om du vill aktivera skydd på **krypterade virtuella datorer (endast krypterat med Bek)** måste du ge Azure Backup tjänst behörighet att läsa hemligheter från nyckel valvet.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Om du använder Azure Government molnet använder du värdet `ff281ffe-705c-4f53-9f37-a40e6f2c68f3` för parametern **servicePrincipalName** i cmdleten [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) .
>

Om du vill selektivt säkerhetskopiera några diskar och utesluta andra som nämns i [dessa scenarier](selective-disk-backup-restore.md#scenarios)kan du konfigurera skyddet och endast säkerhetskopiera de relevanta diskarna som dokumenteras [här](selective-disk-backup-restore.md#enable-backup-with-powershell).

## <a name="monitoring-a-backup-job"></a>Övervaka ett säkerhets kopierings jobb

Du kan övervaka långvariga åtgärder, till exempel säkerhets kopierings jobb, utan att använda Azure Portal. Använd cmdleten [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) för att hämta status för ett pågående jobb. Denna cmdlet hämtar säkerhets kopierings jobben för ett visst valv och det valvet anges i valv kontexten. Följande exempel hämtar status för ett pågående jobb som en matris och lagrar status i $joblist-variabeln.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress" -VaultId $targetVault.ID
$joblist[0]
```

Utdata ser ut ungefär så här:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

I stället för att avsöka de här jobben för slut för ande, vilket är onödig ytterligare kod, använder du [wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) -cmdlet: en. Den här cmdleten pausar körningen tills antingen jobbet har slutförts eller tills det angivna tids gräns värdet har uppnåtts.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Hantera säkerhetskopior av virtuella Azure-datorer

### <a name="modify-a-protection-policy"></a>Ändra en skydds princip

Ändra skydds principen genom att använda [set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) för att ändra SchedulePolicy-eller Retention Policy-objekten.

#### <a name="modifying-scheduled-time"></a>Ändra schemalagd tid

När du skapar en skydds princip tilldelas den en start tid som standard. I följande exempel visas hur du ändrar Start tiden för en skydds princip.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that you want to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>Ändrar kvarhållning

I följande exempel ändras återställnings punktens kvarhållning till 365 dagar.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Konfigurera omedelbar kvarhållning av ögonblicks bilder

> [!NOTE]
> Från Azure PowerShell version 1.6.0 och senare, kan en uppdatering av återställnings perioden för ögonblicks bilder av ögonblicks bilder i principen med hjälp av PowerShell

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

Standardvärdet är 2. Du kan ange ett värde med minst 1 och högst 5. För veckovis säkerhets kopierings principer är perioden inställt på 5 och kan inte ändras.

#### <a name="creating-azure-backup-resource-group-during-snapshot-retention"></a>Skapar Azure Backup resurs grupp under kvarhållning av ögonblicks bilder

> [!NOTE]
> Från Azure PowerShell version 3.7.0 och senare, kan en skapa och redigera resurs gruppen som skapats för att lagra ögonblicks bilder.

Mer information om regler för skapande av resurs grupper och annan relevant information finns i [Azure Backup resurs grupp för Virtual Machines](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines) dokumentation.

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -name "DefaultPolicyForVMs"
$bkpPol.AzureBackupRGName="Contosto_"
$bkpPol.AzureBackupRGNameSuffix="ForVMs"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

### <a name="exclude-disks-for-a-protected-vm"></a>Exkludera diskar för en skyddad virtuell dator

Med Azure VM Backup kan du selektivt utesluta eller ta med diskar som är användbara i [dessa scenarier](selective-disk-backup-restore.md#scenarios). Om den virtuella datorn redan skyddas av Azure VM backup och om alla diskar säkerhets kopie ras, kan du ändra skyddet för att selektivt inkludera eller exkludera diskar som anges [här](selective-disk-backup-restore.md#modify-protection-for-already-backed-up-vms-with-powershell).

### <a name="trigger-a-backup"></a>Utlösa en säkerhets kopia

Använd [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) för att utlösa ett säkerhets kopierings jobb. Om det är den första säkerhets kopian, är det en fullständig säkerhets kopia. Efterföljande säkerhets kopieringar tar en stegvis kopia. I följande exempel utförs en VM-säkerhetskopiering i 60 dagar.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Utdata ser ut ungefär så här:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Tids zonen för fälten StartTime och slut tid i PowerShell är UTC. Men när tiden visas i Azure Portal justeras tiden till din lokala tidszon.
>
>

### <a name="change-policy-for-backup-items"></a>Ändra princip för säkerhets kopierings objekt

Du kan antingen ändra den befintliga principen eller ändra principen för det säkerhetskopierade objektet från Policy1 till Policy2. Om du vill växla principer för ett säkerhetskopierat objekt hämtar du den relevanta principen och säkerhetskopierar objektet och använder kommandot [Enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) med säkerhets kopierings objekt som parameter.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

Kommandot väntar tills den konfigurerade säkerhets kopieringen har slutförts och returnerar följande utdata.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Sluta skydda

#### <a name="retain-data"></a>Behålla data

Om du vill stoppa skyddet kan du använda PowerShell-cmdleten [disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) . Detta stoppar schemalagda säkerhets kopieringar men data som säkerhets kopie ras tills nu kvarhålls för alltid.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Ta bort säkerhetskopieringsdata

Om du vill ta bort lagrade säkerhets kopierings data fullständigt i valvet lägger du till flaggan-RemoveRecoveryPoints eller växlar till [skydds kommandot Disable (inaktivera)](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Återställa en virtuell Azure-dator

Det finns en viktig skillnad mellan att återställa en virtuell dator med hjälp av Azure Portal och återställa en virtuell dator med hjälp av PowerShell. Med PowerShell slutförs återställnings åtgärden när diskarna och konfigurations informationen från återställnings punkten har skapats. Återställnings åtgärden skapar inte den virtuella datorn. Information om hur du skapar en virtuell dator från disk finns i avsnittet [skapa den virtuella datorn från återställda diskar](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Om du inte vill återställa hela den virtuella datorn, men vill återställa eller återställa några filer från en virtuell Azure-säkerhetskopiering, se [avsnittet fil återställning](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> Återställnings åtgärden skapar inte den virtuella datorn.
>
>

Följande bild visar attributhierarkin från RecoveryServicesVault ned till BackupRecoveryPoint.

![Recovery Services objektets hierarki som visar BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Om du vill återställa säkerhetskopierade data identifierar du det säkerhetskopierade objektet och den återställnings punkt som innehåller tidpunkts data. Använd [restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) för att återställa data från valvet till ditt konto.

De grundläggande stegen för att återställa en virtuell Azure-dator är:

* Välj VM.
* Välj en återställnings punkt.
* Återställa diskarna.
* Skapa den virtuella datorn från lagrade diskar.

### <a name="select-the-vm-when-restoring-files"></a>Välj den virtuella datorn (när du återställer filer)

Om du vill hämta PowerShell-objektet som identifierar rätt säkerhets kopierings objekt, startar du från behållaren i valvet och arbetar på ditt sätt nedåt i-objekt-hierarkin. Om du vill välja den behållare som representerar den virtuella datorn använder du cmdleten [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) och pipe till [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) -cmdlet: en.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-files"></a>Välj en återställnings punkt (när du återställer filer)

Använd cmdleten [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) för att visa en lista över alla återställnings punkter för säkerhets kopierings objekt. Välj sedan den återställnings punkt som ska återställas. Om du är osäker på vilken återställnings punkt som ska användas, är det en bra idé att välja den senaste RecoveryPointType = AppConsistent-punkten i listan.

I följande skript är variabeln **$RP** en matris med återställnings punkter för det valda säkerhets kopierings objektet, från de senaste sju dagarna. Matrisen sorteras i omvänd ordning med den senaste återställnings punkten vid index 0. Använd standard PowerShell-matrisering för att välja återställnings punkt. I exemplet väljer $rp [0] den senaste återställnings punkten.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

Utdata ser ut ungefär så här:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>Återställa diskarna

Använd cmdleten [restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) för att återställa ett säkerhets kopierings objekts data och konfiguration till en återställnings punkt. När du har identifierat en återställnings punkt använder du den som värde för parametern **-RecoveryPoint** . I exemplet ovan var **$RP [0]** den återställnings punkt som ska användas. I följande exempel kod är **$RP [0]** den återställnings punkt som används för att återställa disken.

Så här återställer du diskar och konfigurations information:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Återställa hanterade diskar

> [!NOTE]
> Om den virtuella datorn har Managed disks och du vill återställa dem som Managed disks, har vi introducerat kapaciteten från Azure PowerShell RM-modul v-6.7.0. och senare.
>
>

Ange ytterligare en parameter **TargetResourceGroupName** för att ange rg som de hanterade diskarna ska återställas till.

> [!IMPORTANT]
> Vi rekommenderar starkt att du använder **TargetResourceGroupName** -parametern för att återställa hanterade diskar eftersom den resulterar i betydande prestanda förbättringar. Om den här parametern inte anges kan du inte dra nytta av funktionen för omedelbar återställning och återställnings åtgärden kommer att vara långsammare i jämförelse. Om syftet är att återställa hanterade diskar som ohanterade diskar, ska du inte ange den här parametern och göra avsikten tydlig genom att ange `-RestoreAsUnmanagedDisks` parametern. `-RestoreAsUnmanagedDisks`Parametern är tillgänglig från Azure PowerShell 3.7.0 och senare. I framtida versioner är det obligatoriskt att ange någon av dessa parametrar för den rätta återställnings upplevelsen.
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

**VMConfig.JS** filen kommer att återställas till lagrings kontot och de hanterade diskarna kommer att återställas till den angivna mål-rg.

Utdata ser ut ungefär så här:

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Använd [wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) -cmdlet: en för att vänta tills återställnings jobbet har slutförts.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

När återställnings jobbet har slutförts använder du cmdleten [Get-AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) för att hämta information om återställnings åtgärden. Egenskapen JobDetails innehåller den information som behövs för att återskapa den virtuella datorn.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

#### <a name="restore-selective-disks"></a>Återställ selektiva diskar

En användare kan selektivt återställa få diskar i stället för hela den säkerhetskopierade uppsättningen. Ange de nödvändiga diskarna som parameter för att bara återställa dem i stället för hela uppsättningen som dokumenteras [här](selective-disk-backup-restore.md#restore-selective-disks-with-powershell).

> [!IMPORTANT]
> En måste selektivt säkerhetskopiera diskar för att selektivt återställa diskar. Mer information finns [här](selective-disk-backup-restore.md#selective-disk-restore).

När du har återställt diskarna går du till nästa avsnitt för att skapa den virtuella datorn.

#### <a name="restore-disks-to-a-secondary-region"></a>Återställa diskar till en sekundär region

Om återställning mellan regioner är aktiverat i valvet som du har skyddat dina virtuella datorer med, replikeras säkerhetskopierade data till den sekundära regionen. Du kan använda säkerhets kopierings data för att utföra en återställning. Utför följande steg för att utlösa en återställning i den sekundära regionen:

1. [Hämta det valv-ID](#fetch-the-vault-id) som dina virtuella datorer skyddas med.
1. Välj [rätt säkerhets kopierings objekt som ska återställas](#select-the-vm-when-restoring-files).
1. Välj lämplig återställnings punkt i den sekundära regionen som du vill använda för att utföra återställningen.

    Slutför det här steget genom att köra det här kommandot:

    ```powershell
    $rp=Get-AzRecoveryServicesBackupRecoveryPoint -UseSecondaryRegion -Item $backupitem -VaultId $targetVault.ID
    $rp=$rp[0]
    ```

1. Kör cmdleten [restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) med `-RestoreToSecondaryRegion` parametern för att utlösa en återställning i den sekundära regionen.

    Slutför det här steget genom att köra det här kommandot:

    ```powershell
    $restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -VaultLocation $targetVault.Location -RestoreToSecondaryRegion -RestoreOnlyOSDisk
    ```

    Utdatan blir något som liknar följande exempel:

    ```output
    WorkloadName     Operation             Status              StartTime                 EndTime          JobID
    ------------     ---------             ------              ---------                 -------          ----------
    V2VM             CrossRegionRestore   InProgress           4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
    ```

1. Kör cmdleten [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) med `-UseSecondaryRegion` parametern för att övervaka återställnings jobbet.

    Slutför det här steget genom att köra det här kommandot:

    ```powershell
    Get-AzRecoveryServicesBackupJob -From (Get-Date).AddDays(-7).ToUniversalTime() -To (Get-Date).ToUniversalTime() -UseSecondaryRegion -VaultId $targetVault.ID
    ```

    Utdatan blir något som liknar följande exempel:

    ```output
    WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
    ------------     ---------            ------               ---------                 -------                   -----
    V2VM             CrossRegionRestore   InProgress           2/8/2021 4:24:57 PM                                 2d071b07-8f7c-4368-bc39-98c7fb2983f7
    ```

## <a name="replace-disks-in-azure-vm"></a>Ersätt diskar i Azure VM

Gör så här för att ersätta diskarna och konfigurations informationen:

* Steg 1: [Återställ diskarna](backup-azure-vms-automation.md#restore-the-disks)
* Steg 2: [Koppla från datadisk med PowerShell](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-powershell)
* Steg 3: [koppla datadisk till virtuell Windows-dator med PowerShell](../virtual-machines/windows/attach-disk-ps.md)

## <a name="create-a-vm-from-restored-disks"></a>Skapa en virtuell dator från återställda diskar

När du har återställt diskarna kan du använda följande steg för att skapa och konfigurera den virtuella datorn från disk.

> [!NOTE]
>
> 1. AzureAz-modulen 3.0.0 eller högre krävs. <br>
> 2. För att skapa krypterade virtuella datorer från återställda diskar måste din Azure-roll ha behörighet att utföra åtgärden, **Microsoft. nyckel valv/valv/distribuera/åtgärd**. Om rollen inte har den här behörigheten skapar du en anpassad roll med den här åtgärden. Mer information finns i [Azure-anpassade roller](../role-based-access-control/custom-roles.md). <br>
> 3. När du har återställt diskarna kan du nu hämta en distributionsmall som du kan använda direkt för att skapa en ny virtuell dator. Du behöver inte andra PowerShell-cmdletar för att skapa hanterade/ohanterade virtuella datorer som är krypterade/okrypterade.<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>Skapa en virtuell dator med hjälp av distributions mal len

Den resulterande jobb informationen ger den mall-URI som kan frågas och distribueras.

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

Mallen är inte direkt tillgänglig eftersom den finns under en kunds lagrings konto och den aktuella behållaren. Vi behöver den fullständiga URL: en (tillsammans med en tillfällig SAS-token) för att få åtkomst till den här mallen.

1. Extrahera först mallnamnet från templateBlobURI. Formatet anges nedan. Du kan använda delnings åtgärden i PowerShell för att extrahera det sista mallnamnet från den här URL: en.

    ```http
    https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
    ```

2. Sedan kan den fullständiga webb adressen genereras enligt beskrivningen [här](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-powershell#provide-sas-token-during-deployment).

    ```powershell
    Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
    $templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
    ```

3. Distribuera mallen för att skapa en ny virtuell dator enligt beskrivningen [här](../azure-resource-manager/templates/deploy-powershell.md).

    ```powershell
    New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI
    ```

### <a name="create-a-vm-using-the-config-file"></a>Skapa en virtuell dator med hjälp av konfigurations filen

I följande avsnitt beskrivs de steg som krävs för att skapa en virtuell dator med hjälp av "VMConfig"-filen.

> [!NOTE]
> Vi rekommenderar starkt att du använder distributions mal len som beskrivs ovan för att skapa en virtuell dator. Det här avsnittet (punkterna 1-6) kommer snart att vara inaktuellt.

1. Fråga om de återställda disk egenskaperna för jobb informationen.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Ange Azure Storage-kontexten och Återställ JSON-konfigurationsfilen.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Använd JSON-konfigurationsfilen för att skapa VM-konfigurationen.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Koppla operativ system disk och data diskar. Det här steget innehåller exempel för olika hanterade och krypterade VM-konfigurationer. Använd det exempel som passar din VM-konfiguration.

    * **Icke-hanterade och icke-krypterade virtuella datorer** – Använd följande exempel för icke-hanterade och icke-krypterade virtuella datorer.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
            $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Icke-hanterade och krypterade virtuella datorer med Azure AD (endast Bek)** – för icke-hanterade, krypterade virtuella datorer med Azure AD (krypterad med Bek), måste du återställa hemligheten till nyckel valvet innan du kan ansluta diskar. Mer information finns i [återställa en krypterad virtuell dator från en Azure Backup återställnings punkt](backup-azure-restore-key-secret.md). Följande exempel visar hur du kopplar OS-och data diskar för krypterade virtuella datorer. När du ställer in OS-disken måste du ange relevant OS-typ.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Icke-hanterade och krypterade virtuella datorer med Azure AD (Bek och KEK)** – för icke-hanterade, krypterade virtuella datorer med Azure AD (krypterad med Bek och KEK), återställer du nyckeln och hemligheten till nyckel valvet innan du kopplar diskarna. Mer information finns i [återställa en krypterad virtuell dator från en Azure Backup återställnings punkt](backup-azure-restore-key-secret.md). Följande exempel visar hur du kopplar OS-och data diskar för krypterade virtuella datorer.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Icke-hanterade och krypterade virtuella datorer utan Azure AD (endast Bek)** – för icke-hanterade, krypterade virtuella datorer utan Azure AD (krypteras endast med Bek), om det **inte går** att återställa källan till nyckel valvet med hjälp av proceduren i [återställa en icke-krypterad virtuell dator från en Azure Backup återställnings punkt](backup-azure-restore-key-secret.md). Kör sedan följande skript för att ange krypterings information för den återställda OS-blobben (det här steget krävs inte för en data-BLOB). $Dekurl kan hämtas från det återställda nyckel valvet.

    Följande skript behöver bara köras när käll nyckel valvet/hemligheten inte är tillgänglig.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    När **hemligheterna är tillgängliga** och krypterings informationen också har ställts in på OS-blobben ansluter du diskarna med hjälp av skriptet som anges nedan.

    Om käll nyckel valvet/hemligheterna redan är tillgängliga, behöver skriptet ovan inte köras.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Icke-hanterade och krypterade virtuella datorer utan Azure AD (Bek och KEK)** – för icke-hanterade, krypterade virtuella datorer utan Azure AD (krypterade med Bek & KEK), om käll nyckel **valv/nyckel/hemlighet inte är tillgängliga** återställa nyckeln och hemligheterna till nyckel valvet med hjälp av proceduren i [återställa en icke-krypterad virtuell dator från en Azure Backup återställnings punkt](backup-azure-restore-key-secret.md). Kör sedan följande skript för att ange krypterings information för den återställda OS-blobben (det här steget krävs inte för en data-BLOB). $Dekurl och $kekurl kan hämtas från det återställda nyckel valvet.

    Skriptet nedan måste köras endast när käll nyckel valvet/nyckeln/hemligheten inte är tillgänglig.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    När **nyckeln/hemligheterna är tillgängliga** och krypterings informationen har angetts på OS-blobben ansluter du diskarna med hjälp av skriptet som anges nedan.

    Om käll nyckel valvet/nyckeln/hemligheterna är tillgängliga, behöver skriptet ovan inte köras.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Hanterade och icke-krypterade virtuella datorer** – för hanterade icke-krypterade virtuella datorer, koppla de återställda hanterade diskarna. Detaljerad information finns i [koppla en datadisk till en virtuell Windows-dator med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **Hanterade och krypterade virtuella datorer med Azure AD (endast Bek)** – för hanterade krypterade virtuella datorer med Azure AD (endast krypterad med Bek) ansluter du de återställda hanterade diskarna. Detaljerad information finns i [koppla en datadisk till en virtuell Windows-dator med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **Hanterade och krypterade virtuella datorer med Azure AD (Bek och KEK)** – för hanterade krypterade virtuella datorer med Azure AD (krypterad med Bek och KEK) kopplar du tillbaka de återställda hanterade diskarna. Detaljerad information finns i [koppla en datadisk till en virtuell Windows-dator med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **Hanterade och krypterade virtuella datorer utan Azure AD (endast Bek)** – för hanterade, krypterade virtuella datorer utan Azure AD (krypteras endast med Bek), om det **inte** går att återställa hemligheterna för nyckel valv med hjälp av proceduren i [återställa en icke-krypterad virtuell dator från en Azure Backup återställnings punkt](backup-azure-restore-key-secret.md). Kör sedan följande skript för att ange krypterings information på den återställda OS-disken (det här steget krävs inte för en data disk). $Dekurl kan hämtas från det återställda nyckel valvet.

    Skriptet nedan måste köras endast när käll nyckel valvet/hemligheten inte är tillgänglig.  

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    När hemligheterna är tillgängliga och krypterings informationen har ställts in på operativ system disken, kan du se [koppla en datadisk till en virtuell Windows-dator med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **Hanterade och krypterade virtuella datorer utan Azure AD (Bek och KEK)** – för hanterade, krypterade virtuella datorer utan Azure AD (krypteras med hjälp av Bek & KEK), om det **inte finns några tillgängliga käll nyckel valv/nyckel/hemlighet** för att återställa nyckeln och hemligheterna med hjälp av proceduren i [återställa en icke-krypterad virtuell dator från en Azure Backup återställnings punkt](backup-azure-restore-key-secret.md). Kör sedan följande skript för att ange krypterings information på den återställda OS-disken (det här steget krävs inte för data diskar). $Dekurl och $kekurl kan hämtas från det återställda nyckel valvet.

    Följande skript behöver bara köras när käll nyckel valvet/nyckeln/hemligheten inte är tillgänglig.

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $encryptionSettingsElement.KeyEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndKeyReference
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.KeyEncryptionKey.KeyUrl = $kekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    När nyckeln/hemligheterna är tillgängliga och krypterings informationen har ställts in på operativ system disken, kan du se [koppla en datadisk till en virtuell Windows-dator med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md).

5. Ange nätverks inställningarna.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Skapa den virtuella datorn.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Push-ADE-tillägg.
   Om ADE-tilläggen inte skickas markeras data diskarna som okrypterade, så det är obligatoriskt att utföra stegen nedan:

   * **För virtuell dator med Azure AD** – Använd följande kommando för att manuellt aktivera kryptering för data diskarna  

     **Endast BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK och KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **För virtuell dator utan Azure AD** – Använd följande kommando för att manuellt aktivera kryptering för data diskarna.

     Om du under kommando körningen frågar efter AADClientID måste du uppdatera Azure PowerShell.

     **Endast BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK och KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> Se till att ta bort JASON-filerna som skapats som en del av disk processen för återställning av krypterade virtuella datorer manuellt.

## <a name="restore-files-from-an-azure-vm-backup"></a>Återställa filer från en virtuell Azure-säkerhetskopiering

Förutom att återställa diskar kan du också återställa enskilda filer från en virtuell Azure-säkerhetskopiering. Funktionen Återställ filer ger åtkomst till alla filer i en återställnings punkt. Hantera filerna via Utforskaren som du skulle använda för normala filer.

De grundläggande stegen för att återställa en fil från en Azure VM-säkerhetskopiering är:

* Välj den virtuella datorn
* Välj en återställnings punkt
* Montera diskarna för återställnings punkt
* Kopiera de filer som krävs
* Demontera disken

### <a name="select-the-vm-when-restoring-the-vm"></a>Välj den virtuella datorn (när du återställer den virtuella datorn)

Om du vill hämta PowerShell-objektet som identifierar rätt säkerhets kopierings objekt, startar du från behållaren i valvet och arbetar på ditt sätt nedåt i-objekt-hierarkin. Om du vill välja den behållare som representerar den virtuella datorn använder du cmdleten [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) och pipe till [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) -cmdlet: en.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-the-vm"></a>Välj en återställnings punkt (när du återställer den virtuella datorn)

Använd cmdleten [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) för att visa en lista över alla återställnings punkter för säkerhets kopierings objekt. Välj sedan den återställnings punkt som ska återställas. Om du är osäker på vilken återställnings punkt som ska användas, är det en bra idé att välja den senaste RecoveryPointType = AppConsistent-punkten i listan.

I följande skript är variabeln **$RP** en matris med återställnings punkter för det valda säkerhets kopierings objektet, från de senaste sju dagarna. Matrisen sorteras i omvänd ordning med den senaste återställnings punkten vid index 0. Använd standard PowerShell-matrisering för att välja återställnings punkt. I exemplet väljer $rp [0] den senaste återställnings punkten.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

Utdata ser ut ungefär så här:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>Montera diskarna för återställnings punkt

Använd cmdleten [Get-AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) för att hämta skriptet för att montera alla diskar i återställnings punkten.

> [!NOTE]
> Diskarna monteras som iSCSI-anslutna diskar till den dator där skriptet körs. Monteringen sker omedelbart och du debiteras inte några avgifter.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

Utdata ser ut ungefär så här:

```output
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Kör skriptet på den dator där du vill återställa filerna. Om du vill köra skriptet måste du ange det angivna lösen ordet. När diskarna har anslutits använder du Utforskaren i Windows för att bläddra bland de nya volymerna och filerna. Mer information finns i säkerhets kopierings artikeln, [återställa filer från säkerhets kopian av virtuella Azure-datorer](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Demontera diskarna

När de filer som krävs har kopierats använder du [disable-AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) för att demontera diskarna. Se till att demontera diskarna så att åtkomsten till filerna för återställnings punkten tas bort.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>Nästa steg

Om du föredrar att använda PowerShell för att kommunicera med dina Azure-resurser kan du läsa artikeln PowerShell, [distribuera och hantera säkerhets kopiering för Windows Server](backup-client-automation.md). Om du hanterar DPM-säkerhetskopieringar kan du läsa artikeln [distribuera och hantera säkerhets kopiering för DPM](backup-dpm-automation.md).
