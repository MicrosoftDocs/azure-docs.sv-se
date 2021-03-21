---
title: Använda PowerShell för att säkerhetskopiera DPM-arbetsbelastningar
description: Lär dig hur du distribuerar och hanterar Azure Backup för Data Protection Manager (DPM) med PowerShell
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 176cbffe5152462055c4ffdb2367cf9c0ab97c1f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90968297"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Distribuera och hantera säkerhetskopiering till Azure för DPM-servrar (Data Protection Manager) med PowerShell

Den här artikeln visar hur du använder PowerShell för att konfigurera Azure Backup på en DPM-server och för att hantera säkerhets kopiering och återställning.

## <a name="setting-up-the-powershell-environment"></a>Konfigurera PowerShell-miljön

Innan du kan använda PowerShell för att hantera säkerhets kopieringar från Data Protection Manager till Azure måste du ha rätt miljö i PowerShell. I början av PowerShell-sessionen kontrollerar du att du kör följande kommando för att importera rätt moduler och gör att du kan referera till DPM-cmdlets korrekt:

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Installation och registrering

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Börja med att [Hämta den senaste Azure PowerShell](/powershell/azure/install-az-ps).

Följande konfigurations-och registrerings uppgifter kan automatiseras med PowerShell:

* skapar ett Recovery Services-valv
* Installera Azure Backup Agent
* Registrera med Azure Backup tjänsten
* Nätverks inställningar
* Krypteringsinställningar

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Följande steg vägleder dig genom att skapa ett Recovery Services-valv. Ett Recovery Services-valv skiljer sig från ett säkerhets kopierings valv.

1. Om du använder Azure Backup för första gången måste du använda cmdleten **register-AzResourceProvider** för att registrera Azure Recovery Service-providern med din prenumeration.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Recovery Services valvet är en ARM-resurs, så du måste placera den i en resurs grupp. Du kan använda en befintlig resurs grupp eller skapa en ny. När du skapar en ny resurs grupp anger du namn och plats för resurs gruppen.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Använd cmdleten **New-AzRecoveryServicesVault** för att skapa ett nytt valv. Se till att ange samma plats för valvet som används för resurs gruppen.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Ange vilken typ av lagrings redundans som ska användas. Du kan använda [Lokalt Redundant lagring (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage), [geo-REDUNDANT lagring (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)eller [zon-redundant lagring (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage). I följande exempel visas alternativet **BackupStorageRedundancy** för *testVault*  som är inställt **på en**.

   > [!TIP]
   > Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata. Därför är det lämpligt att lagra säkerhets kopierings Recovery Services valvet i en variabel.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Visa valv i en prenumeration

Använd **Get-AzRecoveryServicesVault** för att visa listan över alla valv i den aktuella prenumerationen. Du kan använda det här kommandot för att kontrol lera att ett nytt valv har skapats, eller att se vilka valv som är tillgängliga i prenumerationen.

Kör kommandot, get-AzRecoveryServicesVault och alla valv i prenumerationen visas.

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

## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Installera Azure Backup-agenten på en DPM-server

Innan du installerar Azure Backup Agent måste du hämta installations programmet och presentera det på Windows Server. Du kan hämta den senaste versionen av installations programmet från [Microsoft Download Center](https://aka.ms/azurebackup_agent) eller från Recovery Services valvets instrument panels sida. Spara installations programmet på en lättillgänglig plats som `C:\Downloads\*` .

Installera agenten genom att köra följande kommando i en upphöjd PowerShell-konsol **på DPM-servern**:

```powershell
MARSAgentInstaller.exe /q
```

Detta installerar agenten med alla standard alternativ. Installationen tar några minuter i bakgrunden. Om du inte anger alternativet */nu* öppnas fönstret **Windows Update** i slutet av installationen för att söka efter uppdateringar.

Agenten visas i listan över installerade program. Om du vill se en lista över installerade program går du till **kontroll panelen**  >  **program**  >  **program och funktioner**.

![Agent installerad](./media/backup-dpm-automation/installed-agent-listing.png)

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

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Registrera DPM på ett Recovery Services-valv

När du har skapat Recovery Services-valvet laddar du ned den senaste agenten och autentiseringsuppgifterna för valvet och lagrar det på en lämplig plats som C:\Downloads.

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

På DPM-servern kör du cmdleten [Start-OBRegistration](/powershell/module/msonlinebackup/start-obregistration) för att registrera datorn med valvet.

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Inledande konfigurations inställningar

När DPM-servern har registrerats med Recovery Services-valvet börjar den med standard prenumerations inställningarna. Dessa prenumerations inställningar omfattar nätverk, kryptering och mellanlagringsområdet. Om du vill ändra prenumerations inställningarna måste du först hämta en referens till de befintliga (standard) inställningarna med cmdleten [Get-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/get-dpmcloudsubscriptionsetting) :

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Alla ändringar görs i det här lokala PowerShell-objektet ```$setting```  och det fullständiga objektet allokeras till DPM och Azure Backup att spara dem med cmdleten [set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) . Du måste använda ```–Commit``` flaggan för att se till att ändringarna är sparade. Inställningarna tillämpas inte och används av Azure Backup om det inte allokeras.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Nätverk

Om anslutningen till DPM-datorn till Azure Backup tjänsten på Internet är via en proxyserver, bör inställningarna för proxyservern tillhandahållas för lyckade säkerhets kopieringar. Detta görs med hjälp av ```-ProxyServer``` och ```-ProxyPort``` ```-ProxyUsername``` och ```ProxyPassword``` parametrarna med cmdleten [set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) . I det här exemplet finns det ingen proxyserver, så vi rensar explicit all proxy-relaterad information.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Bandbredds användningen kan också styras med alternativ för ```-WorkHourBandwidth``` och ```-NonWorkHourBandwidth``` för en specifik uppsättning dagar i veckan. I det här exemplet ställer vi inte in någon begränsning.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Konfigurera mellanlagringsområdet

Azure Backup-agenten som körs på DPM-servern behöver tillfällig lagring för data som återställs från molnet (lokalt mellanlagringsområde). Konfigurera mellanlagringsområdet med cmdleten [set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) och ```-StagingAreaPath``` parametern.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

I exemplet ovan kommer mellanlagringsområdet att ställas in på *C:\StagingArea* i PowerShell-objektet ```$setting``` . Kontrol lera att den angivna mappen redan finns, eller att det inte går att utföra den slutliga incheckningen av prenumerations inställningarna.

### <a name="encryption-settings"></a>Krypteringsinställningar

De säkerhets kopierings data som skickas till Azure Backup krypteras för att skydda data sekretessen. Krypterings lösen frasen är "Password" för att dekryptera data vid tidpunkten för återställningen. Det är viktigt att hålla denna information säker och säker när den har kon figurer ATS.

I exemplet nedan konverterar det första kommandot strängen ```passphrase123456789``` till en säker sträng och tilldelar den skyddade strängen till variabeln med namnet ```$Passphrase``` . det andra kommandot anger den säkra strängen i ```$Passphrase``` som lösen ord för att kryptera säkerhets kopior.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Se till att lösen Frass informationen är säker och säker när den har kon figurer ATS. Du kommer inte att kunna återställa data från Azure utan den här lösen frasen.
>
>

Nu bör du ha gjort alla nödvändiga ändringar i ```$setting``` objektet. Kom ihåg att genomföra ändringarna.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Skydda data till Azure Backup

I det här avsnittet lägger du till en produktions server i DPM och skyddar sedan data till den lokala DPM-lagringen och Azure Backup. I exemplen visar vi hur du säkerhetskopierar filer och mappar. Det går lätt att utöka logiken för att säkerhetskopiera alla data källor som stöds av DPM. Alla säkerhets kopior av DPM styrs av en skydds grupp (PG) med fyra delar:

1. **Grupp medlemmar** är en lista över alla skydds bara objekt (även kallade *data källor* i DPM) som du vill skydda i samma skydds grupp. Du kanske till exempel vill skydda virtuella produktions datorer i en skydds grupp och SQL Server databaser i en annan skydds grupp eftersom de kan ha olika säkerhets kopierings krav. Innan du kan säkerhetskopiera alla data källor på en produktions server måste du kontrol lera att DPM-agenten är installerad på servern och hanteras av DPM. Följ stegen för [att installera DPM-agenten](/system-center/dpm/deploy-dpm-protection-agent) och länka den till rätt DPM-server.
2. **Data skydds metod** anger mål platser för säkerhets kopiering – band, disk och moln. I vårt exempel kommer vi att skydda data på den lokala disken och i molnet.
3. Ett **schema för säkerhets kopiering** som anger när säkerhets kopieringar måste utföras och hur ofta data ska synkroniseras mellan DPM-servern och produktions servern.
4. Ett **bevarande schema** som anger hur länge återställnings punkterna i Azure ska bevaras.

### <a name="creating-a-protection-group"></a>Skapa en skydds grupp

Börja med att skapa en ny skydds grupp med hjälp av cmdleten [New-DPMProtectionGroup](/powershell/module/dataprotectionmanager/new-dpmprotectiongroup) .

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Cmdleten ovan kommer att skapa en skydds grupp med namnet *ProtectGroup01*. En befintlig skydds grupp kan också ändras senare för att lägga till säkerhets kopia i Azure-molnet. Men för att göra ändringar i skydds gruppen – nya eller befintliga – behöver vi en referens till ett *ändrings* Bart objekt med hjälp av cmdleten [Get-DPMModifiableProtectionGroup](/powershell/module/dataprotectionmanager/get-dpmmodifiableprotectiongroup) .

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Lägger till grupp medlemmar i skydds gruppen

Varje DPM-agent känner till listan över data källor på den server som den är installerad på. Om du vill lägga till en data källa i skydds gruppen måste DPM-agenten först skicka en lista över data källorna tillbaka till DPM-servern. En eller flera data källor väljs sedan och läggs till i skydds gruppen. De PowerShell-steg som krävs för att uppnå detta är:

1. Hämta en lista över alla servrar som hanteras av DPM via DPM-agenten.
2. Välj en enskild server.
3. Hämta en lista över alla data källor på servern.
4. Välj en eller flera data källor och Lägg till dem i skydds gruppen

Listan över servrar där DPM-agenten är installerad och hanteras av DPM-servern erhålls med cmdleten [Get-DPMProductionServer](/powershell/module/dataprotectionmanager/get-dpmproductionserver) . I det här exemplet ska vi filtrera och konfigurera PowerShell med namnet *productionserver01* för säkerhets kopiering.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains "productionserver01"}
```

Nu ska du hämta listan över data källor med ```$server``` hjälp av cmdleten [Get-DPMDatasource](/powershell/module/dataprotectionmanager/get-dpmdatasource) . I det här exemplet ska vi filtrera på den volym `D:\` som vi vill konfigurera för säkerhets kopiering. Den här data källan läggs sedan till i skydds gruppen med hjälp av cmdleten [Add-DPMChildDatasource](/powershell/module/dataprotectionmanager/add-dpmchilddatasource) . Kom ihåg att använda objektet *ändrings* bara skydds grupp ```$MPG``` för att göra tilläggen.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains "D:\" }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Upprepa det här steget så många gånger som krävs, tills du har lagt till alla valda data källor i skydds gruppen. Du kan också börja med bara en data källa och slutföra arbets flödet för att skapa skydds gruppen och vid ett senare tillfälle lägga till fler data källor i skydds gruppen.

### <a name="selecting-the-data-protection-method"></a>Välja data skydds metod

När data källorna har lagts till i skydds gruppen är nästa steg att ange skydds metoden med cmdleten [set-DPMProtectionType](/powershell/module/dataprotectionmanager/set-dpmprotectiontype) . I det här exemplet är skydds gruppen konfigurerad för lokal disk och moln säkerhets kopiering. Du måste också ange den data källa som du vill skydda till molnet med hjälp av cmdleten [Add-DPMChildDatasource](/powershell/module/dataprotectionmanager/add-dpmchilddatasource) med flaggan-online.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Ställer in kvarhållningsintervall

Ange kvarhållning för säkerhets kopierings platserna med cmdleten [set-DPMPolicyObjective](/powershell/module/dataprotectionmanager/set-dpmpolicyobjective) . Det kan verka konstigt att ställa in kvarhållning innan säkerhets kopierings schemat har definierats, men med ```Set-DPMPolicyObjective``` cmdleten anges automatiskt ett standard schema för säkerhets kopiering som sedan kan ändras. Det är alltid möjligt att ställa in schemat för säkerhets kopiering först och bevarande principen efter.

I exemplet nedan anger cmdleten bevarande parametrarna för disk säkerhets kopieringar. Detta kommer att behålla säkerhets kopior i 10 dagar och synkronisera data var 6: e timme mellan produktions servern och DPM-servern. Den ```SynchronizationFrequencyMinutes``` definierar inte hur ofta en säkerhets kopierings punkt skapas, men hur ofta data kopieras till DPM-servern.  Den här inställningen förhindrar att säkerhets kopior blir för stora.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

För säkerhets kopieringar till Azure (DPM refererar till dem som säkerhets kopiering online) kan kvarhållningsintervallet konfigureras för [långsiktig kvarhållning med ett farfar-gfs (-fader-son Scheme)](backup-azure-backup-cloud-as-tape.md). Det innebär att du kan definiera en sammansatt bevarande princip som omfattar dagliga, veckovis, månatliga och årliga bevarande principer. I det här exemplet skapar vi en matris som representerar det komplexa bevarande schema som vi vill ha och sedan konfigurerar kvarhållningsintervallet med cmdleten [set-DPMPolicyObjective](/powershell/module/dataprotectionmanager/set-dpmpolicyobjective) .

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Ange schema för säkerhets kopiering

DPM ställer in ett standard schema för säkerhets kopiering automatiskt om du anger skydds målet med hjälp av ```Set-DPMPolicyObjective``` cmdleten. Om du vill ändra standard scheman använder du cmdleten [Get-DPMPolicySchedule](/powershell/module/dataprotectionmanager/get-dpmpolicyschedule) följt av cmdleten [set-DPMPolicySchedule](/powershell/module/dataprotectionmanager/set-dpmpolicyschedule) .

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

I exemplet ovan ```$onlineSch``` är en matris med fyra element som innehåller det befintliga Online Protection-schemat för skydds gruppen i gfs-schemat:

1. ```$onlineSch[0]``` innehåller det dagliga schemat
2. ```$onlineSch[1]``` innehåller vecko schemat
3. ```$onlineSch[2]``` innehåller månads schema
4. ```$onlineSch[3]``` innehåller års schema

Så om du behöver ändra vecko schema måste du referera till ```$onlineSch[1]``` .

### <a name="initial-backup"></a>Den första säkerhetskopieringen

När du säkerhetskopierar en data källa för första gången, måste DPM skapa en inledande replik som skapar en fullständig kopia av data källan som ska skyddas på DPM-replikens volym. Den här aktiviteten kan antingen schemaläggas en angiven tid eller aktive ras manuellt, med hjälp av cmdleten [set-DPMReplicaCreationMethod](/powershell/module/dataprotectionmanager/set-dpmreplicacreationmethod) med parametern ```-NOW``` .

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Ändra storleken på DPM-repliken & återställnings punkt volym

Du kan också ändra storleken på DPM-replik volym och skugg kopierings volym med cmdleten [set-DPMDatasourceDiskAllocation](/powershell/module/dataprotectionmanager/set-dpmdatasourcediskallocation) som i följande exempel: Get-DatasourceDiskAllocation-Datasource $DS Set-DatasourceDiskAllocation-DataSource $DS-ProtectionGroup $MPG-manual-ReplicaArea (2 GB)-ShadowCopyArea (2 GB)

### <a name="committing-the-changes-to-the-protection-group"></a>Genomför ändringarna i skydds gruppen

Slutligen måste ändringarna bekräftas innan DPM kan utföra säkerhets kopieringen enligt den nya skydds grupps konfigurationen. Detta kan uppnås med cmdleten [set-DPMProtectionGroup](/powershell/module/dataprotectionmanager/set-dpmprotectiongroup) .

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>Visa säkerhets kopierings platserna

Du kan använda cmdleten [Get-DPMRecoveryPoint](/powershell/module/dataprotectionmanager/get-dpmrecoverypoint) för att hämta en lista över alla återställnings punkter för en data källa. I det här exemplet kommer vi att:

* Hämta alla PGs på DPM-servern och lagrats i en matris ```$PG```
* Hämta de data källor som motsvarar ```$PG[0]```
* Hämta alla återställnings punkter för en data källa.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Återställa skyddade data i Azure

Att återställa data är en kombination av ett ```RecoverableItem``` objekt och ett ```RecoveryOption``` objekt. I föregående avsnitt fick vi en lista över säkerhets kopierings punkter för en data källa.

I exemplet nedan visar vi hur du återställer en virtuell Hyper-V-dator från Azure Backup genom att kombinera säkerhets kopierings punkter med målet för återställning. Det här exemplet innehåller:

* Skapa ett återställnings alternativ med cmdleten  [New-DPMRecoveryOption](/powershell/module/dataprotectionmanager/new-dpmrecoveryoption) .
* Hämtar matrisen med säkerhets kopierings punkter med ```Get-DPMRecoveryPoint``` cmdleten.
* Väljer en säkerhets kopierings punkt att återställa från.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation "C:\VMRecovery"

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Kommandona kan enkelt utökas för alla typer av data källor.

## <a name="next-steps"></a>Nästa steg

* Mer information om DPM för Azure Backup finns i [Introduktion till DPM-säkerhetskopiering](backup-azure-dpm-introduction.md)
