---
title: Använd PowerShell för att säkerhetskopiera Windows Server till Azure
description: I den här artikeln lär du dig hur du använder PowerShell för att konfigurera Azure Backup på Windows Server eller en Windows-klient och hur du hanterar säkerhets kopiering och återställning.
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: 582d8123f16b2d5a543d862b8eb3e45895087e4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90987095"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Distribuera och hantera säkerhetskopiering till Azure för Windows Server/Windows-klient med hjälp av PowerShell

Den här artikeln visar hur du använder PowerShell för att konfigurera Azure Backup på Windows Server eller en Windows-klient och hur du hanterar säkerhets kopiering och återställning.

## <a name="install-azure-powershell"></a>Installera Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kom igång genom att [installera den senaste versionen av PowerShell](/powershell/azure/install-az-ps).

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Följande steg vägleder dig genom att skapa ett Recovery Services-valv. Ett Recovery Services-valv skiljer sig från ett säkerhets kopierings valv.

1. Om du använder Azure Backup för första gången måste du använda cmdleten **register-AzResourceProvider** för att registrera Azure Recovery Service-providern med din prenumeration.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Recovery Services valvet är en Azure Resource Manager resurs, så du måste placera den i en resurs grupp. Du kan använda en befintlig resurs grupp eller skapa en ny. När du skapar en ny resurs grupp anger du namn och plats för resurs gruppen.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. Använd cmdleten **New-AzRecoveryServicesVault** för att skapa det nya valvet. Se till att ange samma plats för valvet som används för resurs gruppen.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Ange vilken typ av lagrings redundans som ska användas. Du kan använda [Lokalt Redundant lagring (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage), [geo-REDUNDANT lagring (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) eller [Zone-redundant lagring (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage). I följande exempel visas alternativet **-BackupStorageRedundancy** för *testVault* som är inställt på den här **inställningen.**

   > [!TIP]
   > Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata. Därför är det lämpligt att lagra säkerhets kopierings Recovery Services valvet i en variabel.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Visa valv i en prenumeration

Använd **Get-AzRecoveryServicesVault** för att visa listan över alla valv i den aktuella prenumerationen. Du kan använda det här kommandot för att kontrol lera att ett nytt valv har skapats, eller att se vilka valv som är tillgängliga i prenumerationen.

Kör kommandot, **Get-AzRecoveryServicesVault** och alla valv i prenumerationen visas.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Installera Azure Backup Agent

Innan du installerar Azure Backup Agent måste du hämta installations programmet och presentera det på Windows Server. Du kan hämta den senaste versionen av installations programmet från [Microsoft Download Center](https://aka.ms/azurebackup_agent) eller från Recovery Services valvets instrument panels sida. Spara installations programmet på en lättillgänglig plats som `C:\Downloads\*` .

Du kan också använda PowerShell för att hämta hämtaren:

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Installera agenten genom att köra följande kommando i en upphöjd PowerShell-konsol:

```powershell
MARSAgentInstaller.exe /q
```

Detta installerar agenten med alla standard alternativ. Installationen tar några minuter i bakgrunden. Om du inte anger alternativet */nu* öppnas fönstret **Windows Update** i slutet av installationen för att söka efter uppdateringar. När agenten har installerats visas den i listan över installerade program.

Om du vill se en lista över installerade program går du till **kontroll panelen**  >  **program**  >  **program och funktioner**.

![Agent installerad](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Installationsalternativ

Om du vill se alla tillgängliga alternativ via kommando raden använder du följande kommando:

```powershell
MARSAgentInstaller.exe /?
```

De tillgängliga alternativen är:

| Alternativ | Information | Standardvärde |
| --- | --- | --- |
| /q |Tyst installation |- |
| /p: "plats" |Sökväg till installationsmappen för Azure Backup agenten. |C:\Program\Microsoft Azure Recovery Services agent |
| /s: "plats" |Sökväg till cache-mappen för Azure Backup agenten. |C:\Program\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Anmäl dig till Microsoft Update |- |
| /nu |Sök inte efter uppdateringar när installationen är klar |- |
| /d |Avinstallerar Microsoft Azure Recovery Services agent |- |
| /ph |Proxyvärd |- |
| /po |Proxy-värdens Port nummer |- |
| /pu |Användar namn för proxy-värd |- |
| /pw |Lösen ord för proxy |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Registrera en Windows Server-eller Windows-klientdator i ett Recovery Services-valv

När du har skapat Recovery Services-valvet laddar du ned den senaste agenten och autentiseringsuppgifterna för valvet och lagrar det på en lämplig plats som C:\Downloads.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

### <a name="registering-using-the-ps-az-module"></a>Registrera via PS AZ-modulen

> [!NOTE]
> Ett fel med generering av valv certifikat har åtgärd ATS i AZ 3.5.0-versionen. Använd AZ 3.5.0 Release version eller senare för att ladda ned ett valv certifikat.

I den senaste AZ-modulen i PowerShell, på grund av underliggande plattforms begränsningar, krävs ett självsignerat certifikat för att ladda ned valvets autentiseringsuppgifter. I följande exempel visas hur du anger ett självsignerat certifikat och laddar ned autentiseringsuppgifterna för valvet.

```powershell
$dt = $(Get-Date).ToString("M-d-yyyy")
$cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -FriendlyName 'test-vaultcredentials' -subject "Windows Azure Tools" -KeyExportPolicy Exportable -NotAfter $(Get-Date).AddHours(48) -NotBefore $(Get-Date).AddHours(-24) -KeyProtection None -KeyUsage None -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2") -Provider "Microsoft Enhanced Cryptographic Provider v1.0"
$certficate = [convert]::ToBase64String($cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx))
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault -Path $CredsPath -Certificate $certficate
```

På Windows Server-eller Windows-klientdatorn kör du cmdleten [Start-OBRegistration](/powershell/module/msonlinebackup/start-obregistration) för att registrera datorn med valvet.
Detta, och andra cmdletar som används för säkerhets kopiering, kommer från MSONLINE-modulen, vilken MARS-AgentInstaller har lagts till som en del av installations processen.

Agentens installations program uppdaterar inte $Env:P SModulePath-variabeln. Det innebär att automatisk inläsning av modulen Miss lyckas. Du kan lösa detta genom att göra följande:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Alternativt kan du läsa in modulen manuellt i skriptet på följande sätt:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

När du läser in en online backup-cmdletar registrerar du autentiseringsuppgifterna för valvet:

```powershell
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false
```

```Output
CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : testvault
Region              : WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Använd inte relativa sökvägar för att ange filen med valvets autentiseringsuppgifter. Du måste ange en absolut sökväg som inmatad till cmdleten.
>
>

## <a name="networking-settings"></a>Nätverks inställningar

När anslutningen till Windows-datorn till Internet är via en proxyserver, kan proxyinställningarna också tillhandahållas agenten. I det här exemplet finns det ingen proxyserver, så vi rensar explicit all proxy-relaterad information.

Bandbredds användningen kan också styras med alternativen för `work hour bandwidth` och `non-work hour bandwidth` för en specifik uppsättning dagar i veckan.

Att ange proxy-och bandbredds information görs med cmdleten [set-OBMachineSetting](/powershell/module/msonlinebackup/set-obmachinesetting) :

```powershell
Set-OBMachineSetting -NoProxy
```

```Output
Server properties updated successfully.
```

```powershell
Set-OBMachineSetting -NoThrottle
```

```Output
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Krypteringsinställningar

De säkerhets kopierings data som skickas till Azure Backup krypteras för att skydda data sekretessen. Krypterings lösen frasen är "Password" för att dekryptera data vid tidpunkten för återställningen.

Du måste skapa en säkerhets-PIN-kod genom att välja **generera** under **Inställningar**  >  **Egenskaper**  >  **säkerhet PIN-kod** i avsnittet **Recovery Services Vault** i Azure Portal.

>[!NOTE]
> Det går bara att skapa en säkerhets kod via Azure Portal.

Använd sedan detta som `generatedPIN` i kommandot:

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> Se till att lösen Frass informationen är säker och säker när den har kon figurer ATS. Du kan inte återställa data från Azure utan den här lösen frasen.
>
>

## <a name="back-up-files-and-folders"></a>Säkerhetskopiera filer och mappar

Alla säkerhets kopior från Windows-servrar och-klienter till Azure Backup regleras av en princip. Principen omfattar tre delar:

1. Ett **schema för säkerhets kopiering** som anger när säkerhets kopieringar måste utföras och synkroniseras med tjänsten.
2. Ett **bevarande schema** som anger hur länge återställnings punkterna i Azure ska bevaras.
3. En **specifikation för fil inkludering/undantag** som avgör vad som ska säkerhets kopie ras.

I det här dokumentet, eftersom vi automatiserar säkerhets kopieringen, förutsätter vi att inget har kon figurer ATS. Vi börjar med att skapa en ny säkerhets kopierings princip med cmdleten [New-OBPolicy](/powershell/module/msonlinebackup/new-obpolicy) .

```powershell
$NewPolicy = New-OBPolicy
```

För tillfället är principen Tom och andra cmdletar behövs för att definiera vilka objekt som ska tas med eller undantas, när säkerhets kopieringarna ska köras och var säkerhets kopian ska lagras.

### <a name="configuring-the-backup-schedule"></a>Konfigurera schema för säkerhets kopiering

Den första av de tre delarna i en princip är schema för säkerhets kopiering, som skapas med cmdleten [New-OBSchedule](/powershell/module/msonlinebackup/new-obschedule) . Säkerhets kopierings schemat definierar när säkerhets kopieringar måste göras. När du skapar ett schema måste du ange två indataparametrar:

* **Vecko dagar** som säkerhets kopieringen ska köras. Du kan köra säkerhets kopierings jobbet på bara en dag eller varje dag i veckan, eller en kombination av mellan.
* **Tidpunkter på dagen** då säkerhets kopieringen ska köras. Du kan definiera upp till tre olika tidpunkter på dagen då säkerhets kopieringen ska utlösas.

Du kan till exempel konfigurera en säkerhets kopierings princip som körs vid 4PM varje lördag och söndag.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

Schemat för säkerhets kopiering måste vara associerat med en princip och detta kan åstadkommas med hjälp av cmdleten [set-OBSchedule](/powershell/module/msonlinebackup/set-obschedule) .

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```

### <a name="configuring-a-retention-policy"></a>Konfigurera en bevarande princip

Bevarande principen definierar hur länge återställnings punkter som skapas från säkerhets kopierings jobb ska behållas. När du skapar en ny bevarande princip med cmdleten [New-OBRetentionPolicy](/powershell/module/msonlinebackup/new-obretentionpolicy) kan du ange antalet dagar som säkerhets kopierings platserna ska behållas med Azure Backup. I exemplet nedan anges en bevarande princip på sju dagar.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

Bevarande principen måste vara kopplad till huvud principen med cmdleten [set-OBRetentionPolicy](/powershell/module/msonlinebackup/set-obretentionpolicy):

```powershell
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="including-and-excluding-files-to-be-backed-up"></a>Inkludera och exkludera filer som ska säkerhets kopie ras

Ett `OBFileSpec` objekt definierar de filer som ska inkluderas och exkluderas i en säkerhets kopia. Detta är en uppsättning regler som omfattar de skyddade filerna och mapparna på en dator. Du kan ha så många fil-eller undantags regler som krävs och associera dem med en princip. När du skapar ett nytt OBFileSpec-objekt kan du:

* Ange de filer och mappar som ska ingå
* Ange de filer och mappar som ska undantas
* Ange rekursiv säkerhets kopiering av data i en mapp (eller) om endast filerna på den översta nivån i den angivna mappen ska säkerhets kopie ras.

Den senare uppnås med hjälp av flaggan-NonRecursive i kommandot New-OBFileSpec.

I exemplet nedan säkerhetskopierar vi volym C: och D: och utesluter binärfilerna för operativ systemet i Windows-mappen och eventuella temporära mappar. För att göra det skapar vi två filspecifikationer med cmdleten [New-OBFileSpec](/powershell/module/msonlinebackup/new-obfilespec) , en för inkludering och en för undantag. När fil specifikationen har skapats är de kopplade till principen med cmdleten [Add-OBFileSpec](/powershell/module/msonlinebackup/add-obfilespec) .

```powershell
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

```powershell
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="applying-the-policy"></a>Tillämpar principen

Objektet är nu komplett och har ett associerat schema för säkerhets kopiering, bevarande princip och en lista över undantagna filer. Den här principen kan nu bekräftas för Azure Backup att använda. Innan du tillämpar den nyligen skapade principen kontrollerar du att det inte finns några befintliga säkerhets kopierings principer kopplade till servern med hjälp av cmdleten [Remove-OBPolicy](/powershell/module/msonlinebackup/remove-obpolicy) . Om du tar bort principen uppmanas du att bekräfta. Använd flaggan med cmdleten om du vill hoppa över bekräftelsen `-Confirm:$false` .

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Bekräfta att principobjektet görs med cmdleten [set-OBPolicy](/powershell/module/msonlinebackup/set-obpolicy) . Detta kommer också att be om bekräftelse. Använd flaggan med cmdleten om du vill hoppa över bekräftelsen `-Confirm:$false` .

```powershell
Set-OBPolicy -Policy $NewPolicy
```

```Output
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

Du kan visa information om den befintliga säkerhets kopierings principen med hjälp av cmdleten [Get-OBPolicy](/powershell/module/msonlinebackup/get-obpolicy) . Du kan öka detalj nivån ytterligare med cmdleten [Get-OBSchedule](/powershell/module/msonlinebackup/get-obschedule) för säkerhets kopierings schema och [Get-OBRetentionPolicy](/powershell/module/msonlinebackup/get-obretentionpolicy) -cmdlet: en för bevarande principerna

```powershell
Get-OBPolicy | Get-OBSchedule
```

```Output
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing
```

```powershell
Get-OBPolicy | Get-OBRetentionPolicy
```

```Output
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing
```

```powershell
Get-OBPolicy | Get-OBFileSpec
```

```Output
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-on-demand-backup"></a>Utföra en säkerhets kopiering på begäran

När en säkerhets kopierings princip har angetts sker säkerhets kopieringarna enligt schemat. Det går också att utlösa en säkerhets kopiering på begäran med cmdleten [Start-OBBackup](/powershell/module/msonlinebackup/start-obbackup) :

```powershell
Get-OBPolicy | Start-OBBackup
```

```Output
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="back-up-windows-server-system-state-in-mars-agent"></a>Säkerhetskopiera Windows Server System State i MARS-agenten

I det här avsnittet beskrivs PowerShell-kommandot för att ställa in system tillstånd i MARS-agenten

### <a name="schedule"></a>Schema

```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>Kvarhållning

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>Konfigurera schema och kvarhållning

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>Verifiera principen

```powershell
Get-OBSystemStatePolicy
 ```

## <a name="restore-data-from-azure-backup"></a>Återställa data från Azure Backup

Det här avsnittet vägleder dig genom stegen för att automatisera återställning av data från Azure Backup. Detta omfattar följande steg:

1. Välj käll volym
2. Välj en säkerhets kopierings punkt som ska återställas
3. Ange ett objekt som ska återställas
4. Utlös återställnings processen

### <a name="picking-the-source-volume"></a>Plocka käll volymen

Om du vill återställa ett objekt från Azure Backup måste du först identifiera källan för objektet. Eftersom vi kör kommandona i kontexten för en Windows Server eller en Windows-klient har datorn redan identifierats. Nästa steg när du identifierar källan är att identifiera den volym som innehåller den. En lista över volymer eller källor som säkerhets kopie ras från den här datorn kan hämtas genom att köra cmdleten [Get-OBRecoverableSource](/powershell/module/msonlinebackup/get-obrecoverablesource) . Det här kommandot returnerar en matris med alla källor som säkerhets kopie ras från den här servern/klienten.

```powershell
$Source = Get-OBRecoverableSource
$Source
```

```Output
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Välja en säkerhets kopierings punkt som du vill återställa från

Du hämtar en lista över säkerhets kopierings punkter genom att köra cmdleten [Get-OBRecoverableItem](/powershell/module/msonlinebackup/get-obrecoverableitem) med lämpliga parametrar. I vårt exempel väljer vi den senaste säkerhets kopierings punkten för käll volymen *C:* och använder den för att återställa en viss fil.

```powershell
$Rps = Get-OBRecoverableItem $Source[0]
$Rps
```

```Output

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/16/2019 7:00:19 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :
```

Objektet `$Rps` är en matris med säkerhets kopierings punkter. Det första elementet är den senaste punkten och det N:te elementet är den äldsta punkten. Vi använder för att välja den senaste punkten `$Rps[0]` .

### <a name="specifying-an-item-to-restore"></a>Ange ett objekt som ska återställas

Om du vill återställa en enskild fil anger du fil namnet i förhållande till rot volymen. Om du till exempel vill hämta C:\Test\Cat.job kör du följande kommando.

```powershell
$Item = New-OBRecoverableItem $Rps[0] "Test\cat.jpg" $FALSE
$Item
```

```Output
IsDir                : False
ItemNameFriendly     : C:\Test\cat.jpg
ItemNameGuid         :
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : cat.jpg
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM

```

### <a name="triggering-the-restore-process"></a>Utlösa återställnings processen

För att utlösa återställnings processen måste du först ange återställnings alternativen. Detta kan göras med hjälp av cmdleten [New-OBRecoveryOption](/powershell/module/msonlinebackup/new-obrecoveryoption) . I det här exemplet antar vi att vi vill återställa filerna till *C:\Temp*. Vi antar också att vi vill hoppa över filer som redan finns i målmappen *C:\Temp*. Använd följande kommando för att skapa ett sådant återställnings alternativ:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Utlös nu återställnings processen med kommandot [Start-OBRecovery](/powershell/module/msonlinebackup/start-obrecovery) på den valda `$Item` från utdata från `Get-OBRecoverableItem` cmdleten:

```powershell
Start-OBRecovery -RecoverableItem $Item -RecoveryOption $RecoveryOption
```

```Output
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```

## <a name="uninstalling-the-azure-backup-agent"></a>Avinstallera Azure Backup Agent

Du kan avinstallera Azure Backup agenten genom att använda följande kommando:

```powershell
.\MARSAgentInstaller.exe /d /q
```

Om du avinstallerar agentens binärfiler från datorn kan det ta några konsekvenser att tänka på:

* Den tar bort fil filtret från datorn och spårningen av ändringar stoppas.
* All princip information tas bort från datorn, men princip informationen fortsätter att lagras i tjänsten.
* Alla säkerhets kopierings scheman tas bort och inga ytterligare säkerhets kopieringar görs.

Data som lagras i Azure förblir dock kvar och bevaras enligt inställningarna för bevarande princip. Äldre punkter föråldras automatiskt.

## <a name="remote-management"></a>Fjärrhantering

All hantering runt Azure Backup Agent, principer och data källor kan göras via en fjärr anslutning via PowerShell. Datorn som ska fjärrhanteras måste förberedas på rätt sätt.

Som standard konfigureras WinRM-tjänsten för manuell start. Start typen måste vara inställd på *Automatisk* och tjänsten måste startas. För att verifiera att WinRM-tjänsten körs, ska värdet för egenskapen status *köras*.

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

PowerShell ska konfigureras för fjärr kommunikation.

```powershell
Enable-PSRemoting -Force
```

```Output
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.
```

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force
```

Datorn kan nu hanteras via fjärr anslutning från installationen av agenten. Till exempel kopierar följande skript agenten till fjärrdatorn och installerar den.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Backup för Windows Server/-klienten:

* [Introduktion till Azure Backup](./backup-overview.md)
* [Säkerhetskopiera Windows-servrar](backup-windows-with-mars-agent.md)
