---
title: 'PowerShell: Migrera SQL Server till SQL-hanterad instans online'
titleSuffix: Azure Database Migration Service
description: Lär dig att migrera online från SQL Server till Azure SQL-hanterad instans med Azure PowerShell och Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 7f09db2e1f98d48e91dfea2642969ff4ca360967
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697831"
---
# <a name="migrate-sql-server-to-sql-managed-instance-online-with-powershell--azure-database-migration-service"></a>Migrera SQL Server till SQL-hanterad instans online med PowerShell & Azure Database Migration Service

I den här artikeln migrerar du **Adventureworks2016** -databasen som återställs till en lokal instans av SQL Server 2005 eller senare till en Azure SQL SQL SQL-hanterad instans med hjälp av Microsoft Azure PowerShell. Du kan migrera databaser från en SQL Server instans till en SQL-hanterad instans med hjälp av `Az.DataMigration` modulen i Microsoft Azure PowerShell.

I den här artikeln kan du se hur du:
> [!div class="checklist"]
>
> * Skapa en resursgrupp.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsjobb i en instans av Azure Database Migration Service.
> * Kör migreringen online.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Den här artikeln innehåller steg för en online-migrering, men det är också möjligt att migrera [offline](howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md).


## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra de här stegen:

* [SQL Server 2016 eller senare](https://www.microsoft.com/sql-server/sql-server-downloads) (vilken utgåva som helst).
* En lokal kopia av **AdventureWorks2016** -databasen som är tillgänglig för nedladdning [här](/sql/samples/adventureworks-install-configure).
* För att aktivera TCP/IP-protokollet, som är inaktiverat som standard med SQL Server Express installation. Aktivera TCP/IP-protokollet genom att följa artikeln [Aktivera eller inaktivera ett Server nätverks protokoll](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Konfigurera Windows- [brandväggen för åtkomst till databas motorn](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En SQL-hanterad instans. Du kan skapa en SQL-hanterad instans genom att följa detalj i artikeln [skapa en ASQL-hanterad instans](../azure-sql/managed-instance/instance-create-quickstart.md).
* Hämta och installera [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 eller senare.
* En Microsoft Azure Virtual Network som skapats med hjälp av Azure Resource Manager distributions modell, som förser Azure Database Migration Service med plats-till-plats-anslutning till dina lokala käll servrar genom att antingen använda [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* En slutförd utvärdering av din lokala databas och schema-migrering med hjälp av Data Migration Assistant, enligt beskrivningen i artikeln [utföra en utvärdering av SQL Server migrering](/sql/dma/dma-assesssqlonprem).
* Hämta och installera `Az.DataMigration` modulen (version 0.7.2 eller senare) från PowerShell-galleriet med hjälp av [PowerShell-cmdleten Install-module](/powershell/module/powershellget/Install-Module).
* För att säkerställa att autentiseringsuppgifterna som används för att ansluta till käll SQL Server instans har behörigheten [kontroll Server](/sql/t-sql/statements/grant-server-permissions-transact-sql) .
* För att säkerställa att de autentiseringsuppgifter som används för att ansluta till målets SQL-hanterade instans har behörigheten kontroll databas på SQL-hanterade instans databaser.

    > [!IMPORTANT]
    > För online-migreringar måste du redan ha konfigurerat dina Azure Active Directory autentiseringsuppgifter. Mer information finns i artikeln [använda portalen för att skapa ett Azure AD-program och tjänstens huvud namn som har åtkomst till resurser](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resurs grupp är en logisk behållare där Azure-resurser distribueras och hanteras.

Skapa en resurs grupp med hjälp av [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) kommandot.

I följande exempel skapas en resurs grupp med namnet *myResourceGroup* i regionen *USA, östra* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-dms"></a>Skapa en instans av DMS

Du kan skapa en ny instans av Azure Database Migration Service med hjälp av `New-AzDataMigrationService` cmdleten.
Denna cmdlet förväntar sig följande obligatoriska parametrar:

* *Namn på Azure-resurs gruppen*. Du kan använda [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) kommandot för att skapa en Azure-resurs grupp som tidigare visas och ange dess namn som en parameter.
* *Tjänst namn*. Sträng som motsvarar det önskade unika tjänst namnet för Azure Database Migration Service.
* *Plats*. Anger tjänstens plats. Ange en plats för Azure Data Center, till exempel USA, västra eller Sydostasien.
* *SKU*. Den här parametern motsvarar DMS SKU-namnet. SKU-namn som stöds för närvarande är *Basic_1vCore* *Basic_2vCores* *GeneralPurpose_4vCores*.
* *ID för virtuell undernät*. Du kan använda cmdleten [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) för att skapa ett undernät.

I följande exempel skapas en tjänst med namnet *MyDMS* i resurs gruppen *MyDMSResourceGroup* som finns i regionen *USA, östra* med ett virtuellt nätverk med namnet *MyVNET* och ett undernät med namnet *mitt undernät*.


```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När du har skapat en Azure Database Migration Service-instans skapar du ett migreringsjobb. Ett Azure Database Migration Service-projekt kräver anslutnings information för både käll-och mål instanserna, samt en lista över databaser som du vill migrera som en del av projektet.
Definiera anslutnings strängar för käll-och mål anslutningar.

Följande skript definierar käll SQL Server anslutnings information: 

```powershell
# Source connection properties
$sourceDataSource = "<mysqlserver.domain.com/privateIP of source SQL>"
$sourceUserName = "domain\user"
$sourcePassword = "mypassword"
```

Följande skript definierar anslutnings informationen för SQL-hanterad instans för SQL: 

```powershell
# Target MI connection properties
$targetMIResourceId = "/subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Sql/managedInstances/<myMI>"
$targetUserName = "<user>"
$targetPassword = "<password>"
```



### <a name="define-source-and-target-database-mapping"></a>Definiera käll-och mål databas mappning

Tillhandahåll databaser som ska migreras i det här migreringsjobbet

Följande skript mappar käll databasen till respektive nya databas på den SQL-hanterade SQL-instansen med det angivna namnet.

```powershell
# Selected databases (Source database name to target database name mapping)
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name>", "<target database name> ")
```

För flera databaser lägger du till listan över databaser i skriptet ovan med följande format:

```powershell
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name1>", "<target database name1> ")
$selectedDatabasesMap.Add("<source  database name2>", "<target database name2> ")
```

### <a name="create-dms-project"></a>Skapa DMS-projekt

Du kan skapa ett Azure Database Migration Service-projekt i DMS-instansen.

```powershell
# Create DMS project
$project = New-AzDataMigrationProject `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -Location $dmsLocation `
  -SourceType SQL `
  -TargetType SQLMI `

# Create selected databases object
$selectedDatabases = @();
foreach ($sourceDbName in $selectedDatabasesMap.Keys){
    $targetDbName = $($selectedDatabasesMap[$sourceDbName])
    $selectedDatabases += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
      -Name $sourceDbName `
      -TargetDatabaseName $targetDbName `
      -BackupFileShare $backupFileShare `
}
```



### <a name="create-a-backup-fileshare-object"></a>Skapa ett objekt för säkerhets kopiering av FileShare

Skapa nu ett FileShare-objekt som representerar den lokala SMB-nätverks resurs som Azure Database Migration Service kan ta säkerhets kopior av käll databasen med hjälp av New-AzDmsFileShare-cmdleten.

```powershell
# SMB Backup share properties
$smbBackupSharePath = "\\shareserver.domain.com\mybackup"
$smbBackupShareUserName = "domain\user"
$smbBackupSharePassword = "<password>"

# Create backup file share object
$smbBackupSharePasswordSecure = ConvertTo-SecureString -String $smbBackupSharePassword -AsPlainText -Force
$smbBackupShareCredentials = New-Object System.Management.Automation.PSCredential ($smbBackupShareUserName, $smbBackupSharePasswordSecure)
$backupFileShare = New-AzDmsFileShare -Path $smbBackupSharePath -Credential $smbBackupShareCredentials
```

### <a name="define-the-azure-storage"></a>Definiera Azure Storage 

Välj Azure Storage behållare som ska användas för migrering: 

```powershell
# Storage resource id
$storageAccountResourceId = "/subscriptions/<subscriptionname>/resourceGroups/<rg>/providers/Microsoft.Storage/storageAccounts/<mystorage>"
```


### <a name="configure-azure-active-directory-app"></a>Konfigurera Azure Active Directory app

Ange nödvändig information för Azure Active Directory för en migrering online SQL-hanterad instans: 

```powershell
# AAD properties
$AADAppId = "<appid-guid>"
$AADAppKey = "<app-key>"

# Create AAD object
$AADAppKeySecure = ConvertTo-SecureString $AADAppKey -AsPlainText -Force
$AADApp = New-AzDmsAadApp -ApplicationId $AADAppId -AppKey $AADAppKeySecure
```


## <a name="create-and-start-a-migration-task"></a>Skapa och starta en migrerings uppgift

Skapa och starta sedan en Azure Database Migration Service aktivitet. Anropa källan och målet med variabler och lista de databas tabeller som ska migreras: 


```powershell
# Managed Instance online migration properties
$dmsTaskName = "testmigration1"

# Create source connection info
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource $sourceDataSource `
  -AuthType WindowsAuthentication `
  -TrustServerCertificate:$true
$sourcePasswordSecure = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCredentials = New-Object System.Management.Automation.PSCredential ($sourceUserName, $sourcePasswordSecure)

# Create target connection info
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI `
    -MiResourceId $targetMIResourceId
$targetPasswordSecure = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCredentials = New-Object System.Management.Automation.PSCredential ($targetUserName, $targetPasswordSecure)
```

I följande exempel skapas och startas en migrering online: 

```powershell
# Create DMS migration task
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -TaskName $dmsTaskName `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCredentials `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCredentials `
  -SelectedDatabase  $selectedDatabases `
  -BackupFileShare $backupFileShare `
  -AzureActiveDirectoryApp $AADApp `
  -StorageResourceId $storageAccountResourceId
```

Mer information finns i [New-AzDataMigrationTask](/powershell/module/az.datamigration/new-azdatamigrationtask).

## <a name="monitor-the-migration"></a>Övervaka migreringen

Utför följande uppgifter för att övervaka migreringen.

### <a name="check-the-status-of-task"></a>Kontrol lera status för uppgiften

```powershell
# Get migration task status details
$migTask = Get-AzDataMigrationTask `
                    -ResourceGroupName $dmsResourceGroupName `
                    -ServiceName $dmsServiceName `
                    -ProjectName $dmsProjectName `
                    -Name $dmsTaskName `
                    -ResultType DatabaseLevelOutput `
                    -Expand

# Task state will be either of 'Queued', 'Running', 'Succeeded', 'Failed', 'FailedInputValidation' or 'Faulted'
$taskState = $migTask.ProjectTask.Properties.State

# Display task state
$taskState | Format-List
```

Använd följande för att hämta en lista över fel:-

```powershell
# Get task errors
$taskErrors = $migTask.ProjectTask.Properties.Errors

# Display task errors
foreach($taskError in $taskErrors){
    $taskError |  Format-List
}


# Get database level details
$databaseLevelOutputs = $migTask.ProjectTask.Properties.Output

# Display database level details
foreach($databaseLevelOutput in $databaseLevelOutputs){

    # This is the source database name.
    $databaseName = $databaseLevelOutput.SourceDatabaseName;

    Write-Host "=========="
    Write-Host "Start migration details for database " $databaseName
    # This is the status for that database - It will be either of:
    # INITIAL, FULL_BACKUP_UPLOADING, FULL_BACKUP_UPLOADED, LOG_FILES_UPLOADING,
    # CUTOVER_IN_PROGRESS, CUTOVER_INITIATED, CUTOVER_COMPLETED, COMPLETED, CANCELLED, FAILED
    $databaseMigrationState = $databaseLevelOutput.MigrationState;

    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo
        
    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo

    # Details about last Currently active/most recent backups. This contains file names, LSN, backup date, etc 
    $databaseActiveBackpusets = $databaseLevelOutput.ActiveBackupSets

    # Display info
    $databaseLevelOutput | Format-List

    Write-Host "Currently active/most recent backupset details:"
    $databaseActiveBackpusets  | select BackupStartDate, BackupFinishedDate, FirstLsn, LastLsn -ExpandProperty ListOfBackupFiles | Format-List

    Write-Host "Last restored backupset details:"
    $databaseLastRestoredBackupFiles  | Format-List

    Write-Host "End migration details for database " $databaseName
    Write-Host "=========="
}
```

## <a name="performing-the-cutover"></a>Utföra start punkt 

Med en online-migrering utförs en fullständig säkerhets kopiering och återställning av databaserna och sedan fortsätter återställningen av transaktions loggarna som lagras i BackupFileShare.

När databasen i en Azure SQL-hanterad instans uppdateras med senaste data och är synkroniserad med käll databasen, kan du utföra en start punkt.

I följande exempel utförs cutover\migration. Användarna anropar det här kommandot efter eget gottfinnande.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Tar bort instansen av Azure Database Migration Service

När migreringen är klar kan du ta bort Azure Database Migration Service-instansen:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Ytterligare resurser

Information om ytterligare migrerings scenarier (käll-/mål par) finns i Microsoft [Database migration guide](https://datamigration.microsoft.com/).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om Azure Database Migration Service i artikeln [Vad är Azure Database migration service?](./dms-overview.md).