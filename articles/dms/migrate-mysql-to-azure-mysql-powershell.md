---
title: 'PowerShell: Kör offlinemigrering från MySQL-databas för att Azure Database for MySQL med DMS'
titleSuffix: Azure Database Migration Service
description: Lär dig hur du migrerar en lokal MySQL-databas till Azure Database for MySQL med hjälp av Azure Database Migration Service via PowerShell-skript.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: 424443288dddcb09d15b7d00ffe9a2840f602959
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819784"
---
# <a name="migrate-mysql-to-azure-database-for-mysql-offline-with-powershell--azure-database-migration-service"></a>Migrera MySQL för att Azure Database for MySQL offline med PowerShell-& Azure Database Migration Service

I den här artikeln migrerar du en MySQL-databas som återställts till en lokal instans till Azure Database for MySQL med hjälp av offlinemigreringsfunktionerna i Azure Database Migration Service via Microsoft Azure PowerShell. Artikeln dokumenterar en samling PowerShell-skript som kan köras i följd för att utföra offlinemigrering av MySQL-databasen till Azure.

> [!NOTE]
> För närvarande går det inte att köra fullständig databasmigrering med hjälp av modulen Az.DataMigration. Under tiden tillhandahålls PowerShell-exempelskriptet "som det är" som använder [DMS Rest-API:et](https://docs.microsoft.com/rest/api/datamigration/tasks/get) och gör att du kan automatisera migreringen. Det här skriptet kommer att ändras eller bli inaktuellt när officiellt stöd har lagts till i modulen Az.DataMigration och Azure CLI. 

> [!IMPORTANT]
> Onlinemigreringsscenariot "MySQL till Azure Database for MySQL" ersätts med ett parallelliserat och mycket effektivt offlinemigreringsscenario från den 1 juni 2021. För onlinemigrering kan du använda det här nya erbjudandet tillsammans med [datareplikering](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication). Du kan också använda verktyg med öppen källkod som [MyDumper/MyLoader](https://centminmod.com/mydumper.html) med datareplikering för onlinemigrering. 

Artikeln hjälper till att automatisera scenariot där käll- och måldatabasnamn kan vara samma eller olika och som en del av migreringen måste antingen alla eller några av tabellerna i måldatabasen migreras med samma namn och tabellstruktur. Även om artiklarna förutsätter att källan är en MySQL-databasinstans och att målet är Azure Database for MySQL, kan den användas för att migrera från en Azure Database for MySQL till en annan genom att bara ändra källserverns namn och autentiseringsuppgifter. Migrering från mySQL-servrar av lägre version (v5.6 och senare) till högre versioner stöds också.

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]

I den här artikeln kan du se hur du:
> [!div class="checklist"]
>
> * Migrera databasschema.
> * Skapa en resursgrupp.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt i en Azure Database Migration Service instans.
> * Konfigurera migreringsprojektet så att det använder offlinemigreringsfunktionerna för MySQL.
> * Köra migreringen.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra de här stegen behöver du:

* Ha ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free)
* Ha en lokal MySQL-databas med version 5.6 eller senare. Om inte laddar du ned och installerar [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6 eller högre.
* [Skapa en instans i Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Se artikeln Använda [MySQL Workbench](../mysql/connect-workbench.md) för att ansluta och fråga efter data för information om hur du ansluter och skapar en databas med hjälp av Workbench-programmet. Den Azure Database for MySQL versionen ska vara lika med eller högre än den lokala MySQL-versionen . Till exempel kan MySQL 5.7 migrera till Azure Database for MySQL 5.7 eller uppgraderas till 8.  
* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager-distributionsmodellen, som ger plats-till-plats-anslutning till dina lokala källservrar med hjälp av [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Mer information om hur du skapar ett [virtuellt nätverk finns i Virtual Network-dokumentationen](../virtual-network/index.yml)och i synnerhet snabbstartsartiklarna med stegvisa detaljer.

    > [!NOTE]
    > Om du använder ExpressRoute med nätverks-peering till Microsoft under installationen [](../virtual-network/virtual-network-service-endpoints-overview.md) av virtuellt nätverkNät lägger du till *Microsoft.Sql-tjänstslutpunkten* i det undernät där tjänsten ska etableras. Den här konfigurationen är nödvändig Azure Database Migration Service saknar Internetanslutning.

* Kontrollera att reglerna för nätverkssäkerhetsgruppen för det virtuella nätverket inte blockerar den utgående porten 443 för ServiceTag för lagring och AzureMonitor. Mer information om NSG-trafikfiltrering för virtuella nätverk finns i artikeln [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper.](../virtual-network/virtual-network-vnet-plan-design-arm.md)
* Öppna Windows-brandväggen för att tillåta anslutningar från Virtual Network för Azure Database Migration Service att komma åt källans MySQL-server, som som standard är TCP-port 3306.
* När du använder en brandväggsinstallation framför dina källdatabaser kan du behöva lägga till brandväggsregler för att tillåta anslutningar från Virtual Network för Azure Database Migration Service att få åtkomst till källdatabaserna för migrering.
* Skapa en brandväggsregel på servernivå eller konfigurera [VNET-tjänstslutpunkter](../mysql/howto-manage-vnet-using-portal.md) för måldatabaser Azure Database for MySQL tillåta Virtual Network för Azure Database Migration Service åtkomst till måldatabaserna. [](../azure-sql/database/firewall-configure.md)
* MySQL-källan måste vara på en MySQL Community Edition som stöds. Om du vill kontrollera vilken version av MySQL-instansen du har går du till verktyget MySQL eller MySQL Workbench och kör följande kommando:

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL stöder endast InnoDB-tabeller. Om du vill konvertera MyISAM-tabeller till InnoDB kan du läsa artikeln [Konvertera tabeller från MyISAM till InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)
* Användaren måste ha behörighet att läsa data i källdatabasen.
* Guiden använder PowerShell v7.1 med PSEdition Core som kan installeras enligt [installationsguiden](/powershell/scripting/install/installing-powershell?view=powershell-7.1&preserve-view=true)
* Ladda ned och installera följande moduler från PowerShell-galleriet med hjälp [av Install-Module PowerShell-cmdleten](/powershell/module/powershellget/Install-Module); se till att öppna PowerShell-kommandofönstret med kör som administratör:
    * Az.Resources
    * Az.Network
    * Az.DataMigration

```powershell
Install-Module Az.Resources
Install-Module Az.Network
Install-Module Az.DataMigration
Import-Module Az.Resources
Import-Module Az.Network
Import-Module Az.DataMigration
```

## <a name="migrate-database-schema"></a>Migrera databasschema

För att överföra alla databasobjekt som tabellscheman, index och lagrade procedurer måste vi extrahera schemat från källdatabasen och tillämpa det på måldatabasen. Du kan extrahera schema med mysqldump med parametern `--no-data`. För detta behöver du en dator som kan ansluta till både MySQL-källdatabasen och Azure Database for MySQL.

Om du vill exportera schemat med mysqldump kör du följande kommando:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Exempel:

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

Kör följande kommando för att importera Azure Database for MySQL målkatalogen:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Exempel:

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

Om du har främmande nycklar i schemat hanteras den parallella datainläsningen under migreringen av migreringsuppgiften. Du behöver inte släppa främmande nycklar under schemamigrering.

Om du har utlösare i databasen framtvingar den dataintegritet i målet före fullständig datamigrering från källan. Rekommendationen är att inaktivera utlösare för alla tabeller i målet under migreringen och sedan aktivera utlösare när migreringen är klar.

Kör följande skript i MySQL Workbench på måldatabasen för att extrahera släpp-utlösarskriptet och lägga till utlösarskriptet.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Kör den genererade utlösarfrågan (DropQuery-kolumnen) i resultatet för att släppa utlösare i måldatabasen. Lägg till utlösarfrågan kan sparas för att användas när datamigrering har slutförts.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Logga in på din Microsoft Azure prenumeration

Använd [PowerShell-kommandot Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) för att logga in på din Azure-prenumeration med hjälp av PowerShell, enligt anvisningarna i artikeln [Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps).

Följande skript anger standardprenumerationen för PowerShell-session efter inloggning och skapar en hjälpfunktion för loggning av formaterade konsolloggar.

```powershell
[string] $SubscriptionName = "mySubscription"
$ErrorActionPreference = "Stop";

Connect-AzAccount
Set-AzContext -Subscription $SubscriptionName
$global:currentSubscriptionId = (Get-AzContext).Subscription.Id;

function LogMessage([string] $Message, [bool] $IsProcessing = $false) {
    if ($IsProcessing) {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Yellow
    }
    else {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Green
    }    
}
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

Registrering av resursprovidern behöver bara göras i varje Azure-prenumeration en gång. Utan registreringen kommer du inte att kunna skapa en instans av **Azure Database Migration Service**.

Registrera resursprovidern med kommandot [Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider) Följande skript registrerar den resursprovider som krävs för **Azure Database Migration Service**

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataMigration
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en resursgrupp innan du skapar DMS-resurser.

Skapa en resursgrupp med hjälp av [kommandot New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup)

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i regionen USA, västra *2* under standardprenumerationen *mySubscription*.

```powershell
# Get the details of resource group
[string] $Location = "westus2"
[string] $ResourceGroupName = "myResourceGroup"

$resourceGroup = Get-AzResourceGroup -Name $ResourceGroupName
if (-not($resourceGroup)) {
    LogMessage -Message "Creating resource group $ResourceGroupName..." -IsProcessing $true
    $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
    LogMessage -Message "Created resource group - $($resourceGroup.ResourceId)."
}
else { LogMessage -Message "Resource group $ResourceGroupName exists." }
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Skapa en instans av Azure Database Migration Service

Du kan skapa en ny instans Azure Database Migration Service med kommandot [New-AzDataMigrationService.](/powershell/module/az.datamigration/new-azdatamigrationservice) Det här kommandot förväntar sig följande obligatoriska parametrar:
* *Namn på Azure-resursgrupp.* Du kan använda [kommandot New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) för att skapa en Azure-resursgrupp som tidigare visat och ange dess namn som en parameter.
* *Tjänstnamn*. Sträng som motsvarar det önskade unika tjänstnamnet för Azure Database Migration Service 
* *Plats*. Anger platsen för tjänsten. Ange en azure-datacenterplats, till exempel USA, västra eller Sydostasien
* *SKU*. Den här parametern motsvarar DMS SKU-namnet. Det SKU-namn som stöds *för närvarande Standard_1vCore*, *Standard_2vCores*, *Standard_4vCores*, *Premium_4vCores*.
* *Identifierare för virtuellt undernät.* Du kan använda [kommandot Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) för att hämta information om ett undernät. 

Följande skript förväntar sig att det virtuella *nätverket myVirtualNetwork* finns med ett undernät med namnet *default* och skapar sedan en Database Migration Service med namnet *myDmService* under resursgruppen som skapades i **steg 3** och i samma region.

```powershell
# Get a reference to the DMS service - Create if not exists
[string] $VirtualNetworkName = "myVirtualNetwork"
[string] $SubnetName = "default"
[string] $ServiceName = "myDmService"

$dmsServiceResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$ResourceGroupName/providers/Microsoft.DataMigration/services/$ServiceName"
$dmsService = Get-AzResource -ResourceId $dmsServiceResourceId -ErrorAction SilentlyContinue

# Create Azure DMS service if not existing
# Possible values for SKU currently are Standard_1vCore,Standard_2vCores,Standard_4vCores,Premium_4vCores
if (-not($dmsService)) {   
    $virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VirtualNetworkName
    if (-not ($virtualNetwork)) { throw "ERROR: Virtual Network $VirtualNetworkName does not exists" }

    $subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $virtualNetwork -Name $SubnetName
    if (-not ($subnet)) { throw "ERROR: Virtual Network $VirtualNetworkName does not contains Subnet $SubnetName" }

    LogMessage -Message "Creating Azure Data Migration Service $ServiceName..." -IsProcessing $true
    $dmsService = New-AzDataMigrationService `
        -ResourceGroupName $ResourceGroupName `
        -Name $ServiceName `
        -Location $resourceGroup.Location `
        -Sku Premium_4vCores `
        -VirtualSubnetId $Subnet.Id
    
    $dmsService = Get-AzResource -ResourceId $dmsServiceResourceId
    LogMessage -Message "Created Azure Data Migration Service - $($dmsService.ResourceId)."
}
else { LogMessage -Message "Azure Data Migration Service $ServiceName exists." }
```

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När du har Azure Database Migration Service en instans skapar du ett migreringsprojekt. Ett migreringsprojekt anger vilken typ av migrering som måste göras.

Följande skript skapar ett migreringsprojekt med namnet *myfirstmysqlofflineproject* för offlinemigrering från MySQL till Azure Database for MySQL under den Database Migration Service-instans som skapades i **steg 4** och i samma region.

```powershell
# Get a reference to the DMS project - Create if not exists
[string] $ProjectName = "myfirstmysqlofflineproject"

$dmsProjectResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($dmsService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($dmsService.Name)/projects/$projectName"
$dmsProject = Get-AzResource -ResourceId $dmsProjectResourceId -ErrorAction SilentlyContinue

# Create Azure DMS Project if not existing
if (-not($dmsProject)) {
    LogMessage -Message "Creating Azure DMS project $projectName for MySQL migration ..." -IsProcessing $true

    $newProjectProperties = @{"sourcePlatform" = "MySQL"; "targetPlatform" = "AzureDbForMySQL" }
    $dmsProject = New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $dmsService.Location `
        -ResourceId $dmsProjectResourceId `
        -Properties $newProjectProperties `
        -Force

    LogMessage -Message "Created Azure DMS project $projectName - $($dmsProject.ResourceId)."
}
else { LogMessage -Message "Azure DMS project $projectName exists." }
```

## <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Skapa ett databasanslutningsinfoobjekt för käll- och målanslutningarna

När du har skapat migreringsprojektet skapar du anslutningsinformationen för databasen. Den här anslutningsinformationen används för att ansluta till käll- och målservrarna under migreringsprocessen.

Följande skript tar servernamnet, användarnamnet och lösenordet för käll- och målinstanserna för MySQL och skapar anslutningsinformationsobjekten. Skriptet uppmanar användaren att ange lösenordet för källan och mySQL-målinstanserna. För tyst skript kan autentiseringsuppgifterna hämtas från Azure Key Vault. 

```powershell
# Initialize the source and target database server connections
[string] $SourceServerName = "13.66.136.192"
[string] $SourceUserName = "docadmin@mysqlserver"
[securestring] $SourcePassword = Read-Host "Enter MySQL Source Server Password" -AsSecureString

[string] $TargetServerName = "migdocdevwus2mysqlsstrgt.mysql.database.azure.com"
[string] $TargetUserName = "docadmin@migdocdevwus2mysqlsstrgt"
[securestring] $TargetPassword = Read-Host "Enter MySQL Target Server Password" -AsSecureString

function InitConnection(
    [string] $ServerName,
    [string] $UserName,
    [securestring] $Password) {
    $connectionInfo = @{
        "dataSource"             = "";
        "serverName"             = "";
        "port"                   = 3306;
        "userName"               = "";
        "password"               = "";
        "authentication"         = "SqlAuthentication";
        "encryptConnection"      = $true;
        "trustServerCertificate" = $true;
        "additionalSettings"     = "";
        "type"                   = "MySqlConnectionInfo" 
    }

    $connectionInfo.dataSource = $ServerName;
    $connectionInfo.serverName = $ServerName;
    $connectionInfo.userName = $UserName;
    $connectionInfo.password = (ConvertFrom-SecureString -AsPlainText $password).ToString();
    $connectionInfo;
}

# Initialize the source and target connections
LogMessage -Message "Initializing source and target connection objects ..." -IsProcessing $true
$sourceConnInfo = InitConnection `
    $SourceServerName `
    $SourceUserName `
    $SourcePassword;

$targetConnInfo = InitConnection `
    $TargetServerName `
    $TargetUserName `
    $TargetPassword;

LogMessage -Message "Source and target connection object initialization complete."
```

## <a name="extract-the-list-of-table-names-from-the-target-database"></a>Extrahera listan över tabellnamn från måldatabasen

Databastabelllistan kan extraheras med hjälp av en migreringsuppgift och anslutningsinformation. Tabelllistan extraheras från både källdatabasen och måldatabasen så att korrekt mappning och verifiering kan göras. 

Följande skript tar namnen på käll- och måldatabaserna och extraherar sedan tabelllistan från databaserna med hjälp av migreringsuppgiften *GetUserTablesMySql.* 

```powershell
# Run scenario to get the tables from the target database to build
# the migration table mapping
[string] $TargetDatabaseName = "migtargetdb"
[string] $SourceDatabaseName = "migsourcedb"

function RunScenario([object] $MigrationService, 
    [object] $MigrationProject, 
    [string] $ScenarioTaskName, 
    [object] $TaskProperties, 
    [bool] $WaitForScenario = $true) {
    # Check if the scenario task already exists, if so remove it
    LogMessage -Message "Removing scenario if already exists..." -IsProcessing $true
    Remove-AzDataMigrationTask `
        -ResourceGroupName $MigrationService.ResourceGroupName `
        -ServiceName $MigrationService.Name `
        -ProjectName $MigrationProject.Name `
        -TaskName $ScenarioTaskName `
        -Force;

    # Start the new scenario task using the provided properties
    LogMessage -Message "Initializing scenario..." -IsProcessing $true
    New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $MigrationService.Location `
        -ResourceId "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($MigrationService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($MigrationService.Name)/projects/$($MigrationProject.Name)/tasks/$($ScenarioTaskName)" `
        -Properties $TaskProperties `
        -Force | Out-Null;
    
    LogMessage -Message "Waiting for $ScenarioTaskName scenario to complete..." -IsProcessing $true
    if ($WaitForScenario) {
        $progressCounter = 0;
        do {
            if ($null -ne $scenarioTask) {
                Start-Sleep 10;
            }

            # Get calls can time out and will return a cancellation exception in that case
            $scenarioTask = Get-AzDataMigrationTask `
                -ResourceGroupName $MigrationService.ResourceGroupName `
                -ServiceName $MigrationService.Name `
                -ProjectName $MigrationProject.Name `
                -TaskName $ScenarioTaskName `
                -Expand `
                -ErrorAction Ignore;

            Write-Progress -Activity "Scenario Run $ScenarioTaskName  (Marquee Progress Bar)" `
                -Status $scenarioTask.ProjectTask.Properties.State `
                -PercentComplete $progressCounter
            
            $progressCounter += 10;
            if ($progressCounter -gt 100) { $progressCounter = 10 }
        }
        while (($null -eq $scenarioTask) -or ($scenarioTask.ProjectTask.Properties.State -eq "Running") -or ($scenarioTask.ProjectTask.Properties.State -eq "Queued"))
    }
    Write-Progress -Activity "Scenario Run $ScenarioTaskName" `
        -Status $scenarioTask.ProjectTask.Properties.State `
        -Completed
                 
    # Now get it using REST APIs so we can expand the output  
    LogMessage -Message "Getting expanded task results ..." -IsProcessing $true  
    $psToken = (Get-AzAccessToken -ResourceUrl https://management.azure.com).Token;
    $token = ConvertTo-SecureString -String $psToken -AsPlainText -Force;
    $taskResource = Invoke-RestMethod `
        -Method GET `
        -Uri "https://management.azure.com$($scenarioTask.ProjectTask.Id)?api-version=2018-03-31-preview&`$expand=output" `
        -ContentType "application/json" `
        -Authentication Bearer `
        -Token $token;
    
    $taskResource.properties;
}

# create the get table task properties by initializing the connection and 
# database name
$getTablesTaskProperties = @{
    "input"    = @{
        "connectionInfo"    = $null;
        "selectedDatabases" = $null;
    };
    "taskType" = "GetUserTablesMySql";
};

LogMessage -Message "Running scenario to get the list of tables from the target database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $targetConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($TargetDatabaseName);
# Create a name for the task
$getTableTaskName = "$($TargetDatabaseName)GetUserTables"
# Get the list of tables from the source
$getTargetTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getTargetTablesTask)) { throw "ERROR: Could not get target database $TargetDatabaseName table information." }
LogMessage -Message "List of tables from the target database acquired."

LogMessage -Message "Running scenario to get the list of tables from the source database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $sourceConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($SourceDatabaseName);
# Create a name for the task
$getTableTaskName = "$($SourceDatabaseName)GetUserTables"
# Get the list of tables from the source
$getSourceTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getSourceTablesTask)) { throw "ERROR: Could not get source database $SourceDatabaseName table information." }
LogMessage -Message "List of tables from the source database acquired."

```

## <a name="build-table-mapping-based-on-user-configuration"></a>Skapa tabellmappning baserat på användarkonfiguration

Som en del av att konfigurera migreringsuppgiften skapar du en mappning mellan käll- och måltabellerna. Mappningen är på namnnivån för tabellen, men antagandet är att tabellstrukturen (kolumnantal, kolumnnamn, datatyper osv.) för de mappade tabellerna är exakt densamma.

Följande skript skapar en mappning baserat på mål- och källtabelllistan som extraherades i **steg 7.** Vid partiell databelastning kan användaren ange en lista med tabeller för att filtrera bort tabellerna. Om inga användarindata anges mappas alla måltabeller. Skriptet kontrollerar också om det finns en tabell med samma namn i källan eller inte. Om tabellnamnet inte finns i källan ignoreras måltabellen för migrering. 

```powershell
# Create the source to target table map
# Optional table settings
# DEFAULT: $IncludeTables = $null => include all tables for migration
# DEFAULT: $ExcludeTables = $null => exclude no tables from migration
# Exclude list has higher priority than include list
# Array of qualified source table names which should be migrated
[string[]] $IncludeTables = @("migsourcedb.coupons", "migsourcedb.daily_cash_sheets");
[string[]] $ExcludeTables = $null;

LogMessage -Message "Creating the table map based on the user input and database table information ..." `
    -IsProcessing $true

$targetTables = $getTargetTablesTask.Output.DatabasesToTables."$TargetDatabaseName";
$sourceTables = $getSourceTablesTask.Output.DatabasesToTables."$SourceDatabaseName";
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]';

$schemaPrefixLength = $($SourceDatabaseName + ".").Length;
$tableMappingError = $false
foreach ($srcTable in $sourceTables) {
    # Removing the database name prefix from the table name so that comparison
    # can be done in cases where database name given are different
    $tableName = $srcTable.Name.Substring($schemaPrefixLength, `
            $srcTable.Name.Length - $schemaPrefixLength)

    # In case the table is part of exclusion list then ignore the table
    if ($null -ne $ExcludeTables -and $ExcludeTables -contains $srcTable.Name) {
        continue;
    }

    # Either the include list is null or the table is part of the include list then add it in the mapping
    if ($null -eq $IncludeTables -or $IncludeTables -contains $srcTable.Name) {
        # Check if the table exists in the target. If not then log TABLE MAPPING ERROR
        if (-not ($targetTables | Where-Object { $_.name -ieq "$($TargetDatabaseName).$tableName" })) {
            $tableMappingError = $true
            Write-Host "TABLE MAPPING ERROR: $($targetTables.name) does not exists in target." -ForegroundColor Red
            continue;
        }  

        $tableMap.Add("$($SourceDatabaseName).$tableName", "$($TargetDatabaseName).$tableName");
    }     
}

# In case of any table mapping errors identified, throw an error and stop the process
if ($tableMappingError) { throw "ERROR: One or more table mapping errors were identified. Please see previous messages." }
# In case no tables are in the mapping then throw error
if ($tableMap.Count -le 0) { throw "ERROR: Could not create table mapping." }
LogMessage -Message "Migration table mapping created for $($tableMap.Count) tables."
```

## <a name="create-and-configure-the-migration-task-inputs"></a>Skapa och konfigurera indata för migreringsuppgiften

När du har skapat tabellmappningen skapar du indata för migreringsuppgiften av typen *Migrate.MySql.AzureDbForMySql* och konfigurerar egenskaperna.

Följande skript skapar migreringsuppgiften och anger anslutningar, databasnamn och tabellmappning.

```powershell
# Create and configure the migration scenario based on the connections
# and the table mapping
$offlineMigTaskProperties = @{
    "input"    = @{
        "sourceConnectionInfo"  = $null;
        "targetConnectionInfo"  = $null;
        "selectedDatabases"     = $null;
        "optionalAgentSettings" = @{
            "EnableCacheBatchesInMemory"         = $true;
            "DisableIncrementalRowStatusUpdates" = $true;
        };
        "startedOn"             = $null;
    };
    "taskType" = "Migrate.MySql.AzureDbForMySql";
};
$offlineSelectedDatabase = @{
    "name"               = $null;
    "targetDatabaseName" = $null;
    "tableMap"           = $null;
};

LogMessage -Message "Preparing migration scenario configuration ..." -IsProcessing $true

# Select the database to be migrated
$offlineSelectedDatabase.name = $SourceDatabaseName;
$offlineSelectedDatabase.tableMap = New-Object PSObject -Property $tableMap;
$offlineSelectedDatabase.targetDatabaseName = $TargetDatabaseName;

# Set connection info and the database mapping
$offlineMigTaskProperties.input.sourceConnectionInfo = $sourceConnInfo;
$offlineMigTaskProperties.input.targetConnectionInfo = $targetConnInfo;
$offlineMigTaskProperties.input.selectedDatabases = @($offlineSelectedDatabase);
$offlineMigTaskProperties.input.startedOn = [System.DateTimeOffset]::UtcNow.ToString("O");
```

## <a name="configure-performance-tuning-parameters"></a>Konfigurera prestandajusteringsparametrar

Enligt PowerShell-modulen finns det några valfria parametrar som kan justeras baserat på miljön. Dessa parametrar kan användas för att förbättra migreringsuppgiftens prestanda. Alla dessa parametrar är valfria och deras standardvärde är NULL.

> [!NOTE]
> Följande prestandakonfigurationer har visat ett ökat dataflöde under migreringen på Premium SKU.
> * WriteDataRangeBatchTaskCount = 12
> * DelayProgressUpdatesInStorageInterval = 30 sekunder
> * ThrottleQueryTableDataRangeTaskAtBatchCount = 36

Följande skript tar användarvärdena för parametrarna och anger parametrarna i egenskaperna för migreringsuppgiften.

```powershell
# Setting optional parameters from fine tuning the data transfer rate during migration
# DEFAULT values for all the configurations is $null
LogMessage -Message "Adding optional migration performance tuning configuration ..." -IsProcessing $true
# Partitioning settings
# Optional setting that configures the maximum number of parallel reads on tables located on the source database.
[object] $DesiredRangesCount = 4
# Optional setting that configures that size of the largest batch that will be committed to the target server.
[object] $MaxBatchSizeKb = 4096
# Optional setting that configures the minimum number of rows in each batch written to the target.
[object] $MinBatchRows = $null
# Task count settings
# Optional setting that configures the number of databases that will be prepared for migration in parallel.
[object] $PrepareDatabaseForBulkImportTaskCount = $null
# Optional setting that configures the number of tables that will be prepared for migration in parallel.
[object] $PrepareTableForBulkImportTaskCount = $null
# Optional setting that configures the number of threads available to read ranges on the source.
[object] $QueryTableDataRangeTaskCount = 8
# Optional setting that configures the number of threads available to write batches to the target.
[object] $WriteDataRangeBatchTaskCount = 12
# Batch cache settings
# Optional setting that configures how much memory will be used to cache batches in memory before reads on the source are throttled.
[object] $MaxBatchCacheSizeMb = $null
# Optional setting that configures the amount of available memory at which point reads on the source will be throttled.
[object] $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb = $null
# Optional setting that configures the number of batches cached in memory that will trigger read throttling on the source.
[object] $ThrottleQueryTableDataRangeTaskAtBatchCount = 36
# Performance settings
# Optional setting that configures the delay between updates of result objects in Azure Table Storage.
[object] $DelayProgressUpdatesInStorageInterval = "00:00:30"

function AddOptionalSetting($optionalAgentSettings, $settingName, $settingValue) {
    # If no value specified for the setting, don't bother adding it to the input
    if ($null -eq $settingValue) {
        return;
    }

    # Add a new property to the JSON object to capture the setting which will be customized
    $optionalAgentSettings | add-member -MemberType NoteProperty -Name $settingName -Value $settingValue
}

# Set any optional settings in the input based on parameters to this cmdlet
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DesiredRangesCount" $DesiredRangesCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchSizeKb" $MaxBatchSizeKb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MinBatchRows" $MinBatchRows;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareDatabaseForBulkImportTaskCount" $PrepareDatabaseForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareTableForBulkImportTaskCount" $PrepareTableForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "QueryTableDataRangeTaskCount" $QueryTableDataRangeTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "WriteDataRangeBatchTaskCount" $WriteDataRangeBatchTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchCacheSizeMb" $MaxBatchCacheSizeMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb" $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtBatchCount" $ThrottleQueryTableDataRangeTaskAtBatchCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DelayProgressUpdatesInStorageInterval" $DelayProgressUpdatesInStorageInterval;
```

## <a name="creating-and-running-the-migration-task"></a>Skapa och köra migreringsuppgiften

När du har konfigurerat indata för uppgiften skapas och körs uppgiften på agenten. Skriptet utlöser uppgiftskörningen och väntar på att migreringen ska slutföras.

Följande skript anropar den konfigurerade migreringsuppgiften och väntar på att den ska slutföras.

```powershell
# Running the migration scenario
[string] $TaskName = "mysqlofflinemigrate"

LogMessage -Message "Running data migration scenario ..." -IsProcessing $true
$summary = @{
    "SourceServer"   = $SourceServerName;
    "SourceDatabase" = $SourceDatabaseName;
    "TargetServer"   = $TargetServerName;
    "TargetDatabase" = $TargetDatabaseName;
    "TableCount"     = $tableMap.Count;
    "StartedOn"      = $offlineMigTaskProperties.input.startedOn;
}

Write-Host "Job Summary:" -ForegroundColor Yellow
Write-Host $(ConvertTo-Json $summary) -ForegroundColor Yellow

$migrationResult = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $TaskName `
    -TaskProperties $offlineMigTaskProperties

LogMessage -Message "Migration completed with status - $($migrationResult.state)"
#Checking for any errors or warnings captured by the task during migration
$dbLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "DatabaseLevelOutput" } 
$migrationLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "MigrationLevelOutput" }
if ($dbLevelResult.exceptionsAndWarnings) {
    Write-Host "Following database errors were captured: $($dbLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationLevelResult.exceptionsAndWarnings) {
    Write-Host "Following migration errors were captured: $($migrationLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationResult.errors.details) {
    Write-Host "Following task level migration errors were captured: $($migrationResult.errors.details)" -ForegroundColor Red
}
```

## <a name="deleting-the-database-migration-service"></a>Ta bort Database Migration Service

Samma Database Migration Service kan användas för flera migreringar så att instansen när den har skapats kan användas igen. Om du inte tänker fortsätta att använda Database Migration Service kan du ta bort tjänsten med kommandot [Remove-AzDataMigrationService.](/powershell/module/az.datamigration/remove-azdatamigrationservice?)

Följande skript tar bort den Azure Database Migration Service instansen och dess associerade projekt.

```powershell
Remove-AzDataMigrationService -ResourceId $($dmsService.ResourceId)
```

## <a name="next-steps"></a>Nästa steg

* Information om kända problem och begränsningar när du utför migreringar med DMS finns i artikeln [Vanliga problem – Azure Database Migration Service](./known-issues-troubleshooting-dms.md).
* Information om hur du felsöker anslutningsproblem för källdatabaser när du använder DMS finns i [artikeln Problem med att ansluta källdatabaser.](./known-issues-troubleshooting-dms-source-connectivity.md)
* Information om Azure Database Migration Service finns i artikeln [Vad är Azure Database Migration Service?](./dms-overview.md).
* Information om Azure Database for MySQL finns i artikeln [Vad är Azure Database for MySQL?](../mysql/overview.md).
* Självstudie om hur du använder DMS via portalen finns i artikeln [Självstudie: Migrera MySQL](./tutorial-mysql-azure-mysql-offline-portal.md) till Azure Database for MySQL offline med DMS