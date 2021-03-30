---
title: Säkerhetskopiera en Azure-filresurs med hjälp av PowerShell
description: I den här artikeln får du lära dig hur du säkerhetskopierar en Azure Files fil resurs med hjälp av Azure Backup tjänsten och PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 948931764769bc967b88e7942b7e8384b0f93dff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87076999"
---
# <a name="back-up-an-azure-file-share-by-using-powershell"></a>Säkerhetskopiera en Azure-filresurs med hjälp av PowerShell

Den här artikeln beskriver hur du använder Azure PowerShell för att säkerhetskopiera en Azure Files fil resurs via ett [Azure Backup](backup-overview.md) Recovery Services valv.

Den här artikeln förklarar hur du:

> [!div class="checklist"]
>
> * Konfigurera PowerShell och registrera Recovery Services-providern.
> * Skapa ett Recovery Services-valv.
> * Konfigurera säkerhets kopiering för en Azure-filresurs.
> * Kör ett säkerhets kopierings jobb.

## <a name="before-you-start"></a>Innan du börjar

* [Läs mer](backup-azure-recovery-services-vault-overview.md) om Recovery Services-valv.
* Granska referens referensen för [cmdleten](/powershell/module/az.recoveryservices) AZ. RecoveryServices i Azure-biblioteket.
* Granska följande PowerShell-platshierarki för Recovery Services:

  ![Recovery Services objektets hierarki](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## <a name="set-up-powershell"></a>Konfigurera PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Konfigurera PowerShell på följande sätt:

1. [Ladda ned den senaste versionen av Azure PowerShell](/powershell/azure/install-az-ps).

    > [!NOTE]
    > Den lägsta PowerShell-version som krävs för säkerhets kopiering av Azure-filresurser är AZ. RecoveryServices 2.6.0. Med den senaste versionen, eller minst den lägsta versionen, kan du undvika problem med befintliga skript. Installera den lägsta versionen med hjälp av följande PowerShell-kommando:
    >
    > ```powershell
    > Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
    > ```

2. Hitta PowerShell-cmdletar för Azure Backup med hjälp av det här kommandot:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Granska alias och cmdlets för Azure Backup, Azure Site Recovery och Recovery Services valvet. Här är ett exempel på vad du kan se. Det är inte en fullständig lista över cmdletar.

    ![Lista över Recovery Services-cmdletar](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Logga in på ditt Azure-konto med **Connect-AzAccount**.
5. På webb sidan som visas uppmanas du att ange dina autentiseringsuppgifter för kontot.

    Du kan också inkludera dina kontoautentiseringsuppgifter som en parameter i cmdleten **Connect-AzAccount** med hjälp av **-Credential**.

    Om du är en CSP-partner som arbetar för en klients räkning anger du kunden som en klient. Använd klient-ID eller klientens primära domän namn. Ett exempel är **Connect-AzAccount-Tenant "fabrikam.com"**.

6. Associera den prenumeration som du vill använda med kontot, eftersom ett konto kan ha flera prenumerationer:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Om du använder Azure Backup för första gången ska du använda cmdleten **register-AzResourceProvider** för att registrera Azure Recovery Services-providern med din prenumeration:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verifiera att providern har registrerats:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. I kommandot utdata kontrollerar du att **RegistrationState** ändras till **registrerad**. Om den inte gör det kör du cmdleten **register-AzResourceProvider** igen.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Recovery Services valvet är en Resource Manager-resurs, så du måste placera den i en resurs grupp. Du kan använda en befintlig resurs grupp, eller så kan du skapa en resurs grupp med hjälp av cmdleten **New-AzResourceGroup** . När du skapar en resurs grupp anger du namn och plats för den.

Följ de här stegen för att skapa ett Recovery Services-valv:

1. Om du inte har en befintlig resurs grupp skapar du en ny med hjälp av cmdleten [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . I det här exemplet skapar vi en resurs grupp i regionen USA, västra:

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

1. Använd cmdleten [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) för att skapa valvet. Ange samma plats för valvet som du använde för resurs gruppen.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Visa valv i en prenumeration

Om du vill visa alla valv i prenumerationen använder du [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault):

```powershell
Get-AzRecoveryServicesVault
```

Utdata ser ut ungefär så här. Observera att utdata tillhandahåller den associerade resurs gruppen och platsen.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Ange valv kontext

Lagra Valve-objektet i en variabel och ange valv kontexten.

Många Azure Backup-cmdletar kräver att Recovery Services Vault-objektet är inmatat, så det är praktiskt att lagra valvet i en variabel.

Valvets sammanhang är typen av data som skyddas i valvet. Ange den med [set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext). När kontexten har angetts gäller den för alla efterföljande cmdletar.

I följande exempel anges valv kontexten för **testvault**:

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Hämta valv-ID: t

Vi planerar att föråldra kontext inställningen för valvet i enlighet med Azure PowerShell rikt linjer. I stället kan du lagra eller hämta valv-ID: t och skicka det till relevanta kommandon. Om du inte har angett valv kontexten eller om du vill ange kommandot som ska köras för ett visst valv, måste du skicka valv-ID `-vaultID` : t till alla relevanta kommandon enligt följande:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Konfigurera en säkerhetskopieringspolicy

En säkerhets kopierings princip anger schemat för säkerhets kopior och hur länge återställnings punkter för säkerhets kopiering ska behållas.

En säkerhets kopierings princip är associerad med minst en bevarande princip. En bevarande princip definierar hur länge en återställnings punkt ska sparas innan den tas bort. Du kan konfigurera säkerhets kopior med daglig, veckovis, månatlig eller årlig kvarhållning.

Här följer några cmdletar för säkerhets kopierings principer:

* Visa standard kvarhållning av säkerhets kopierings principer med hjälp av [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject).
* Visa schemat för säkerhets kopierings policy med hjälp av [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject).
* Skapa en ny säkerhets kopierings princip med hjälp av [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy). Du anger schema-och bevarande princip objekt som inmatade.

Som standard definieras en start tid i objektet Schemalägg princip. Använd följande exempel för att ändra start tiden till önskad start tid. Den önskade start tiden måste vara i UTC-tid (Universal Coordinated Time). Exemplet förutsätter att den önskade start tiden är 01:00 AM UTC för dagliga säkerhets kopieringar.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Du måste ange start tiden i högst 30 minuter. I föregående exempel kan det bara vara "01:00:00" eller "02:30:00". Start tiden får inte vara "01:15:00".

I följande exempel lagras schema principen och bevarande principen i variabler. Den använder sedan variablerna som parametrar för en ny princip (**NewAFSPolicy**). **NewAFSPolicy** tar en daglig säkerhets kopiering och behåller den i 30 dagar.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

De utdata som genereras liknar följande:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Aktivera säkerhets kopiering

När du har definierat säkerhets kopierings principen kan du aktivera skydd för Azure-filresursen med hjälp av principen.

### <a name="retrieve-a-backup-policy"></a>Hämta en princip för säkerhets kopiering

Du hämtar relevant princip objekt med hjälp av [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy). Använd den här cmdleten för att visa de principer som associeras med en arbets belastnings typ eller för att hämta en speciell princip.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Hämta en princip för en arbets belastnings typ

I följande exempel hämtas principer för **migreringsåtgärden** för arbets belastnings typ:

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

De utdata som genereras liknar följande:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Tids zonen för fältet **BackupTime** i POWERSHELL är UTC. När tiden för säkerhets kopieringen visas i Azure Portal justeras tiden till din lokala tidszon.

#### <a name="retrieve-a-specific-policy"></a>Hämta en speciell princip

Följande princip hämtar säkerhets kopierings principen med namnet **dailyafs**:

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-protection-and-apply-the-policy"></a>Aktivera skydd och tillämpa principen

Aktivera skydd med hjälp av [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). När principen är associerad med valvet utlöses säkerhets kopieringarna enligt princip schemat.

I följande exempel aktive ras skyddet för Azure-filresurs- **testAzureFileShare** i Storage Account **testStorageAcct**, med principen **dailyafs**:

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Kommandot väntar tills konfigurations skydds jobbet är klart och ger utdata som liknar följande exempel:

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

Mer information om hur du hämtar en lista över fil resurser för ett lagrings konto finns i [den här artikeln](/powershell/module/az.storage/get-azstorageshare).

## <a name="important-notice-backup-item-identification"></a>Viktigt meddelande: identifiering av säkerhets kopierings objekt

I det här avsnittet beskrivs viktiga förändringar i säkerhets kopiering av Azure-filresurser i förberedelser inför allmän tillgänglighet.

När du aktiverar en säkerhets kopia för Azure-filresurser får användaren ett fil resurs namn som entitetsnamnet och ett säkerhets kopierings objekt skapas. Säkerhetsobjektets namn är en unik identifierare som Azure Backups tjänsten skapar. Vanligt vis är identifieraren ett användarvänligt namn. Men för att hantera scenariot med mjuk borttagning, där en fil resurs kan tas bort och en annan fil resurs kan skapas med samma namn, är den unika identiteten för en Azure-filresurs nu ett ID.

Om du vill veta det unika ID: t för varje objekt kör du kommandot **Get-AzRecoveryServicesBackupItem** med relevanta filter för **backupManagementType** och **WorkloadType** för att hämta alla relevanta objekt. Observera sedan fältet namn i det returnerade PowerShell-objektet/svaret.

Vi rekommenderar att du listar objekt och sedan hämtar deras unika namn från fältet namn i svaret. Använd det här värdet för att filtrera objekten med parametern *Name* . Annars kan du använda *FriendlyName* -parametern för att hämta objektet med dess ID.

> [!IMPORTANT]
> Se till att PowerShell uppgraderas till den lägsta versionen (AZ. RecoveryServices 2.6.0) för säkerhets kopiering av Azure-filresurser. Med den här versionen är *FriendlyName* -filtret tillgängligt för kommandot **Get-AzRecoveryServicesBackupItem** .
>
> Skicka namnet på Azure-filresursen till *FriendlyName* -parametern. Om du skickar namnet på fil resursen till parametern *Name* , genererar den här versionen en varning för att skicka namnet till *FriendlyName* -parametern.
>
> Om du inte installerar den lägsta versionen kan det leda till att befintliga skript inte fungerar. Installera den lägsta versionen av PowerShell med hjälp av följande kommando:
>
>```powershell
>Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
>```

## <a name="trigger-an-on-demand-backup"></a>Utlös en säkerhets kopiering på begäran

Använd [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) för att köra en säkerhets kopiering på begäran för en skyddad Azure-fil resurs:

1. Hämta lagrings kontot från behållaren i valvet som innehåller dina säkerhetskopierade data med hjälp av [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Starta ett säkerhets kopierings jobb genom att hämta information om Azure-filresursen med hjälp av [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Kör en säkerhets kopiering på begäran med hjälp av [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Kör säkerhets kopiering på begäran på följande sätt:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Kommandot returnerar ett jobb med ett ID som ska spåras, som du ser i följande exempel:

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Ögonblicks bilder av Azure-filresurser används när säkerhets kopieringen görs. Vanligt vis slutförs jobbet när kommandot returnerar utdata.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [att säkerhetskopiera Azure Files i Azure Portal](backup-afs.md).
* Se [exempel skriptet på GitHub](https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup) för att använda en Azure Automation Runbook för att schemalägga säkerhets kopieringar.
