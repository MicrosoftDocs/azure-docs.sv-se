---
title: Automatisk säkerhets kopiering v2 för SQL Server 2016/2017 virtuella Azure-datorer | Microsoft Docs
description: I den här artikeln förklaras funktionen för automatisk säkerhets kopiering för SQL Server 2016/2017-virtuella datorer som körs på Azure. Den här artikeln är speciell för virtuella datorer som använder Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f41614d54dc4320f683f406b2882a7b388bb4c3d
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358426"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Automatisk säkerhets kopiering v2 för Azure Virtual Machines (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [SQL Server 2014](automated-backup-sql-2014.md)
> * [SQL Server 2016 +](automated-backup.md)

Automatisk säkerhets kopiering v2 konfigurerar automatiskt [hanterad säkerhets kopiering till Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) för alla befintliga och nya databaser på en virtuell Azure-dator som kör SQL Server 2016 eller senare versioner av standard, Enterprise eller Developer. På så sätt kan du konfigurera regelbundna säkerhets kopieringar av databaser som använder varaktiga Azure Blob Storage. Automatisk säkerhets kopiering v2 beror på [SQL Server infrastruktur som tjänst (IaaS) Agent tillägg](sql-server-iaas-agent-extension-automate-management.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Förutsättningar
Om du vill använda automatisk säkerhets kopiering v2 granskar du följande krav:

**Operativ system**:

- Windows Server 2012 R2 eller senare

**SQL Server version/utgåva**:

- SQL Server 2016 eller högre: Developer, standard eller Enterprise

> [!NOTE]
> För SQL Server 2014, se [Automatisk säkerhets kopiering för SQL Server 2014](automated-backup-sql-2014.md).

**Databas konfiguration**:

- Mål _användar_ databaser måste använda den fullständiga återställnings modellen. System databaser behöver inte använda den fullständiga återställnings modellen. Om du kräver att logg säkerhets kopior ska vidtas för modellen eller MSDB måste du dock använda den fullständiga återställnings modellen. Mer information om effekten av den fullständiga återställnings modellen för säkerhets kopieringar finns i [säkerhets kopiering under den fullständiga återställnings modellen](/previous-versions/sql/sql-server-2008-r2/ms190217(v=sql.105)). 
- SQL Server VM har registrerats med SQL IaaS agent-tillägget i [fullständigt hanterings läge](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full). 
-  Automatisk säkerhets kopiering förlitar sig på det fullständiga [SQL Server IaaS agent-tillägget](sql-server-iaas-agent-extension-automate-management.md). Automatiserad säkerhets kopiering stöds därför bara för mål databaser från standard instansen eller en namngiven instans. Om det inte finns någon standard instans och flera namngivna instanser, Miss lyckas SQL IaaS-tillägget och den automatiserade säkerhets kopieringen kommer inte att fungera. 

## <a name="settings"></a>Inställningar
I följande tabell beskrivs de alternativ som kan konfigureras för automatisk säkerhets kopiering v2. De faktiska konfigurations stegen varierar beroende på om du använder Azure Portal-eller Azure Windows PowerShell-kommandon.

### <a name="basic-settings"></a>Grundläggande inställningar

| Inställning | Intervall (standard) | Beskrivning |
| --- | --- | --- |
| **Automatisk säkerhetskopiering** | Aktivera/inaktivera (inaktive rad) | Aktiverar eller inaktiverar automatisk säkerhets kopiering för en virtuell Azure-dator som kör SQL Server 2016/2017 Developer, standard eller Enterprise. |
| **Kvarhållningsperiod** | 1-30 dagar (30 dagar) | Antalet dagar att behålla säkerhets kopior. |
| **Lagrings konto** | Azure Storage-konto | Ett Azure Storage-konto som ska användas för att lagra automatiska säkerhetskopieringsfiler i Blob Storage. En behållare skapas på den här platsen för att lagra alla säkerhetskopieringsfiler. Fil namns konventionen för säkerhets kopiering innehåller datum, tid och databas-GUID. |
| **Kryptering** |Aktivera/inaktivera (inaktive rad) | Aktiverar eller inaktiverar kryptering. När kryptering är aktiverat finns de certifikat som används för att återställa säkerhets kopian på det angivna lagrings kontot. Den använder samma **automatiska säkerhets kopierings** behållare med samma namngivnings konvention. Om lösen ordet ändras genereras ett nytt certifikat med det lösen ordet, men det gamla certifikatet kvarstår för att återställa tidigare säkerhets kopior. |
| **Lösenord** |Lösen ords text | Ett lösen ord för krypterings nycklar. Det här lösen ordet krävs endast om kryptering är aktiverat. För att kunna återställa en krypterad säkerhets kopia måste du ha rätt lösen ord och relaterat certifikat som användes när säkerhets kopieringen gjordes. |

### <a name="advanced-settings"></a>Avancerade inställningar

| Inställning | Intervall (standard) | Beskrivning |
| --- | --- | --- |
| **Säkerhets kopior av system databasen** | Aktivera/inaktivera (inaktive rad) | När den här funktionen är aktive rad säkerhets kopie ras även system databaser: Master, MSDB och Model. För MSDB-och modell databaserna kontrollerar du att de är i fullständigt återställnings läge om du vill att logg säkerhets kopior ska vidtas. Logg säkerhets kopior tas aldrig för huvud. Och inga säkerhets kopior tas för TempDB. |
| **Schema för säkerhets kopiering** | Manuellt/automatiserat (automatiserat) | Som standard bestäms schemat för säkerhets kopiering automatiskt utifrån logg tillväxten. Schema för manuell säkerhets kopiering gör att användaren kan ange tids perioden för säkerhets kopieringar. I det här fallet sker säkerhets kopieringar endast med den angivna frekvensen och under den angivna tids perioden för en given dag. |
| **Frekvens för fullständig säkerhets kopiering** | Varje dag/varje vecka | Frekvens för fullständiga säkerhets kopieringar. I båda fallen börjar fullständiga säkerhets kopieringar under nästa schemalagda tids period. När du väljer varje vecka kan säkerhets kopieringar omfatta flera dagar tills alla databaser har säkerhetskopierats. |
| **Start tid för fullständig säkerhets kopiering** | 00:00 – 23:00 (01:00) | Start tiden för en dag under vilken fullständiga säkerhets kopieringar kan ske. |
| **Tids period för fullständig säkerhets kopiering** | 1 – 23 timmar (1 timme) | Varaktigheten för tids perioden för en specifik dag under vilken fullständiga säkerhets kopieringar kan äga rum. |
| **Logg säkerhets kopierings frekvens** | 5 – 60 minuter (60 minuter) | Frekvens för säkerhets kopiering av loggar. |

## <a name="understanding-full-backup-frequency"></a>Förstå fullständig säkerhets kopierings frekvens
Det är viktigt att förstå skillnaden mellan dagliga och veckovis fullständiga säkerhets kopieringar. Tänk på följande två exempel scenarier.

### <a name="scenario-1-weekly-backups"></a>Scenario 1: vecko Visa säkerhets kopior
Du har ett SQL Server VM som innehåller ett antal stora databaser.

På måndag aktiverar du automatisk säkerhets kopiering v2 med följande inställningar:

- Schema för säkerhets kopiering: **manuell**
- Frekvens för fullständig säkerhets kopiering: **varje vecka**
- Start tid för fullständig säkerhets kopiering: **01:00**
- Tids period för fullständig säkerhets kopiering: **1 timme**

Det innebär att nästa tillgängliga säkerhets kopierings fönster är tisdag kl. 1 timme. Vid detta tillfälle börjar den automatiska säkerhets kopieringen säkerhetskopiera dina databaser en i taget. I det här scenariot är dina databaser tillräckligt stora för att fullständiga säkerhets kopieringar ska slutföras för de första par databaserna. Men efter en timme har alla databaser inte säkerhetskopierats.

När detta inträffar börjar den automatiserade säkerhets kopieringen säkerhetskopiera de återstående databaserna nästa dag, onsdag vid 1 timme i en timme. Om inte alla databaser har säkerhetskopierats under den tiden, försöker den igen nästa dag. Detta fortsätter tills alla databaser har säkerhetskopierats.

När den når tisdag igen börjar den automatiska säkerhets kopieringen säkerhetskopiera alla databaser igen.

Det här scenariot visar att automatisk säkerhets kopiering endast fungerar inom den angivna tids perioden och att varje databas säkerhets kopie ras en gång per vecka. Detta visar också att det är möjligt för säkerhets kopieringar att omfatta flera dagar i fall där det inte går att slutföra alla säkerhets kopieringar på en enda dag.

### <a name="scenario-2-daily-backups"></a>Scenario 2: dagliga säkerhets kopieringar
Du har ett SQL Server VM som innehåller ett antal stora databaser.

På måndag aktiverar du automatisk säkerhets kopiering v2 med följande inställningar:

- Schema för säkerhets kopiering: manuell
- Frekvens för fullständig säkerhets kopiering: varje dag
- Start tid för fullständig säkerhets kopiering: 22:00
- Tids period för fullständig säkerhets kopiering: 6 timmar

Det innebär att nästa tillgängliga säkerhets kopierings fönster är måndag vid 10 PM i 6 timmar. Vid detta tillfälle börjar den automatiska säkerhets kopieringen säkerhetskopiera dina databaser en i taget.

Sedan, på tisdag vid 10 i 6 timmar, startar fullständiga säkerhets kopieringar av alla databaser.

> [!IMPORTANT]
> När du schemalägger dagliga säkerhets kopieringar rekommenderar vi att du schemalägger ett brett tids fönster så att alla databaser kan säkerhets kopie ras inom den här tiden. Detta är särskilt viktigt i de fall där du har en stor mängd data som ska säkerhets kopie ras.

## <a name="configure-new-vms"></a>Konfigurera nya virtuella datorer

Använd Azure Portal för att konfigurera automatisk säkerhets kopiering v2 När du skapar en ny SQL Server 2016-eller 2017-virtuell dator i distributions modellen för Resource Manager.

På fliken **SQL Server inställningar** väljer du **Aktivera** under **Automatisk säkerhets kopiering**. Följande Azure Portal skärm bild visar inställningarna för **Automatisk säkerhets kopiering i SQL** .

![Konfiguration av automatisk säkerhets kopiering i SQL i Azure Portal](./media/automated-backup/automated-backup-blade.png)

> [!NOTE]
> Automatisk säkerhets kopiering v2 är inaktiverat som standard.

## <a name="configure-existing-vms"></a>Konfigurera befintliga virtuella datorer

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

För befintliga SQL Server virtuella datorer går du till [resursen SQL Virtual Machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) och väljer sedan **säkerhets kopieringar** för att konfigurera dina automatiserade säkerhets kopior.

![Automatisk SQL-säkerhetskopiering för befintliga virtuella datorer](./media/automated-backup/sql-server-configuration.png)


När du är färdig klickar du på knappen **tillämpa** längst ned på sidan **säkerhets kopierings** inställningar för att spara ändringarna.

Om du aktiverar automatisk säkerhets kopiering för första gången konfigurerar Azure SQL Server IaaS-agenten i bakgrunden. Under den här tiden kanske Azure Portal inte visar att automatisk säkerhets kopiering har kon figurer ATS. Vänta några minuter på att agenten ska installeras, konfigureras. Efter det kommer Azure Portal att återspegla de nya inställningarna.

## <a name="configure-with-powershell"></a>Konfigurera med PowerShell

Du kan använda PowerShell för att konfigurera automatisk säkerhets kopiering v2. Innan du börjar måste du:

- [Hämta och installera den senaste Azure PowerShell](https://aka.ms/webpi-azps).
- Öppna Windows PowerShell och associera det med ditt konto med kommandot **Connect-AzAccount** .

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-server-iaas-extension"></a>Installera SQL Server IaaS-tillägget
Om du etablerade en SQL Server virtuell dator från Azure Portal bör SQL Server IaaS-tillägget redan vara installerat. Du kan avgöra om den är installerad för den virtuella datorn genom att anropa **Get-AzVM-** kommandot och undersöka **tillägg** -egenskapen.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Om tillägget SQL Server IaaS-agent är installerat ska du se det som "Sqliaasagent är" eller "SQLIaaSExtension". **ProvisioningState** för tillägget ska också Visa "lyckades". 

Om den inte är installerad eller om den inte har kon figurer ATS kan du installera den med följande kommando. Förutom namnet på den virtuella datorn och resurs gruppen måste du också ange den region (**$region**) som den virtuella datorn finns i.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region 
```

### <a name="verify-current-settings"></a><a id="verifysettings"></a> Verifiera aktuella inställningar
Om du har aktiverat automatisk säkerhets kopiering under etableringen kan du använda PowerShell för att kontrol lera den aktuella konfigurationen. Kör kommandot **Get-AzVMSqlServerExtension** och granska egenskapen **AutoBackupSettings** :

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Du bör få utdata som liknar följande:

```
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

Om resultatet visar att **Aktivera** är inställt på **falskt** måste du aktivera automatisk säkerhets kopiering. Den goda nyheten är att du aktiverar och konfigurerar automatisk säkerhets kopiering på samma sätt. Mer information finns i nästa avsnitt.

> [!NOTE] 
> Om du kontrollerar inställningarna direkt efter att du har gjort en ändring är det möjligt att du får tillbaka de gamla konfigurations värdena. Vänta några minuter och kontrol lera inställningarna igen för att se till att ändringarna har tillämpats.

### <a name="configure-automated-backup-v2"></a>Konfigurera automatisk säkerhets kopiering v2
Du kan använda PowerShell för att aktivera automatisk säkerhets kopiering samt ändra konfiguration och beteende när som helst. 

Börja med att välja eller skapa ett lagrings konto för de säkerhetskopierade filerna. Följande skript väljer ett lagrings konto eller skapar det om det inte redan finns.

```powershell
$storage_accountname = "yourstorageaccount"
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region } 
```

> [!NOTE]
> Automatisk säkerhets kopiering stöder inte lagring av säkerhets kopior i Premium Storage, men det kan ta säkerhets kopior från virtuella dator diskar som använder Premium Storage.

Använd sedan kommandot **New-AzVMSqlServerAutoBackupConfig** för att aktivera och konfigurera inställningarna för automatisk säkerhets kopiering v2 för lagring av säkerhets kopior i Azure Storage-kontot. I det här exemplet är säkerhets kopiorna inställda för att behållas i 10 dagar. Säkerhets kopieringar av system databaser är aktiverade. Fullständiga säkerhets kopieringar är schemalagda för varje vecka med ett tidsfönster som börjar på 20:00 i två timmar. Logg säkerhets kopior är schemalagda för var 30: e minut. Det andra kommandot, **set-AzVMSqlServerExtension**, uppdaterar den angivna virtuella Azure-datorn med de här inställningarna.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS-agenten. 

Om du vill aktivera kryptering ändrar du det tidigare skriptet för att skicka parametern **EnableEncryption** tillsammans med ett lösen ord (säker sträng) för parametern **CertificatePassword** . Följande skript aktiverar de automatiska säkerhets kopierings inställningarna i föregående exempel och lägger till kryptering.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Bekräfta [konfigurationen för automatisk säkerhets kopiering](#verifysettings)för att kontrol lera att dina inställningar tillämpas.

### <a name="disable-automated-backup"></a>Inaktivera automatisk säkerhets kopiering
Om du vill inaktivera automatisk säkerhets kopiering kör du samma skript utan parametern **-Enable** till kommandot **New-AzVMSqlServerAutoBackupConfig** . Avsaknad av parametern **-Enable** signalerar kommandot för att inaktivera funktionen. Precis som vid installationen kan det ta flera minuter att inaktivera automatisk säkerhets kopiering.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Exempelskript
Följande skript innehåller en uppsättning variabler som du kan anpassa för att aktivera och konfigurera automatisk säkerhets kopiering för den virtuella datorn. I så fall kan du behöva anpassa skriptet baserat på dina krav. Du skulle till exempel behöva göra ändringar om du vill inaktivera säkerhets kopieringen av system databaser eller aktivera kryptering.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL Server IaaS Extension 

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Övervakning

Om du vill övervaka automatisk säkerhets kopiering på SQL Server 2016/2017 har du två huvud alternativ. Eftersom den automatiska säkerhets kopieringen använder funktionen SQL Server hanterad säkerhets kopiering, gäller samma övervaknings tekniker för båda.

Först kan du söka efter statusen genom att anropa [msdb. managed_backup. sp_get_backup_diagnostics](/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Eller en fråga av tabell värdes funktionen [msdb. managed_backup. fn_get_health_status](/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) .

Ett annat alternativ är att dra nytta av den inbyggda Database Mail funktionen för meddelanden.

1. Anropa den lagrade proceduren [msdb. managed_backup. sp_set_parameter](/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) för att tilldela en e-postadress till **SSMBackup2WANotificationEmailIds** -parametern. 
1. Aktivera [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) för att skicka e-postmeddelanden från den virtuella Azure-datorn.
1. Använd SMTP-servern och användar namnet för att konfigurera Database Mail. Du kan konfigurera Database Mail i SQL Server Management Studio eller med Transact-SQL-kommandon. Mer information finns i [Database mail](/sql/relational-databases/database-mail/database-mail).
1. [Konfigurera SQL Server Agent att använda Database mail](/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Kontrol lera att SMTP-porten tillåts både via den lokala VM-brandväggen och nätverks säkerhets gruppen för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg
Automatisk säkerhets kopiering v2 konfigurerar hanterad säkerhets kopiering på virtuella Azure-datorer. Det är därför viktigt att [granska dokumentationen för hanterad säkerhets kopiering](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) för att förstå beteendet och konsekvenserna.

Du hittar ytterligare vägledning för säkerhets kopiering och återställning av SQL Server på virtuella Azure-datorer i följande artikel: [säkerhets kopiering och återställning för SQL Server på Azure Virtual Machines](backup-restore.md).

Information om andra tillgängliga Automation-uppgifter finns [SQL Server IaaS agent Extension](sql-server-iaas-agent-extension-automate-management.md).

Mer information om att köra SQL Server på virtuella Azure-datorer finns i [Översikt över SQL Server på Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).