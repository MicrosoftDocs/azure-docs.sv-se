---
title: Skapa en elastisk jobb agent med PowerShell (för hands version)
description: Lär dig hur du skapar en elastisk jobbagent med PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
ms.date: 10/21/2020
ms.openlocfilehash: 95e9ef340328bb4c1835e966cc9c3019bca88c09
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368837"
---
# <a name="create-an-elastic-job-agent-using-powershell-preview"></a>Skapa en elastisk jobb agent med PowerShell (för hands version)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Elastiska jobb (för hands version)](job-automation-overview.md) aktiverar körning av ett eller flera Transact-SQL-skript (T-SQL) parallellt över flera databaser.

I den här självstudien får du lära dig de steg som krävs för att köra en fråga över flera databaser:

> [!div class="checklist"]
> * Skapa en elastiskt jobbagent
> * Skapa autentiseringsuppgifter för jobbet så att det kan köra skript på sina mål
> * Definiera mål (servrar, elastiska pooler, databaser, shard-kartor) som du vill köra jobbet mot
> * Skapa autentiseringsuppgifter för databasen i måldatabaserna så att agenten kan ansluta och köra jobb
> * Skapa ett jobb
> * Lägg till jobbsteg i ett jobb
> * Starta körningen av ett jobb
> * Övervaka ett jobb

## <a name="prerequisites"></a>Förutsättningar

Den uppgraderade versionen av Elastic Database-jobb har en ny uppsättning PowerShell-cmdlets som kan användas under migreringen. Dessa nya cmdletar överför alla befintliga autentiseringsuppgifter för jobb, mål (inklusive databaser, servrar, anpassade samlingar), jobb utlösare, jobb scheman, jobb innehåll och jobb till en ny elastisk jobb agent.

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>Installera de senaste elastiska jobb-cmdletarna

Om du inte redan har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Installera **AZ. SQL** -modulen för att hämta de senaste cmdletarna för elastiska jobb. Kör följande kommandon i PowerShell med administratörsbehörighet.

```powershell
# installs the latest PackageManagement and PowerShellGet packages
Find-Package PackageManagement | Install-Package -Force
Find-Package PowerShellGet | Install-Package -Force

# Restart your powershell session with administrative access

# Install and import the Az.Sql module, then confirm
Install-Module -Name Az.Sql
Import-Module Az.Sql

Get-Module Az.Sql
```

Förutom **AZ. SQL** -modulen kräver den här kursen även *SQLServer* PowerShell-modulen. Mer information finns i avsnittet om att [installera SQL Server PowerShell-modulen](/sql/powershell/download-sql-server-ps-module).

## <a name="create-required-resources"></a>Skapa nödvändiga resurser

För att skapa elastiska jobbagenter krävs en databas (S0 eller högre) som kan användas som [jobbdatabas](job-automation-overview.md#elastic-job-database).

Skriptet nedan skapar en ny resursgrupp, server och databas som ska användas som jobbdatabas. Det andra skriptet skapar en andra server med två tomma databaser för att köra jobb mot.

Elastiska jobb har inga särskilda krav för namngivningskonventioner, så du kan använda vilken namnkonvention du vill, förutsatt att de uppfyller något av [kraven för Azure](/azure/architecture/best-practices/resource-naming).

```powershell
# sign in to Azure account
Connect-AzAccount

# create a resource group
Write-Output "Creating a resource group..."
$resourceGroupName = Read-Host "Please enter a resource group name"
$location = Read-Host "Please enter an Azure Region"
$rg = New-AzResourceGroup -Name $resourceGroupName -Location $location
$rg

# create a server
Write-Output "Creating a server..."
$agentServerName = Read-Host "Please enter an agent server name"
$agentServerName = $agentServerName + "-" + [guid]::NewGuid()
$adminLogin = Read-Host "Please enter the server admin name"
$adminPassword = Read-Host "Please enter the server admin password"
$adminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$adminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $adminLogin, $adminPasswordSecure
$agentServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $agentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$agentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$agentServer

# create the job database
Write-Output "Creating a blank database to be used as the Job Database..."
$jobDatabaseName = "JobDatabase"
$jobDatabase = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $agentServerName -DatabaseName $jobDatabaseName -RequestedServiceObjectiveName "S0"
$jobDatabase
```

```powershell
# create a target server and sample databases - uses the same credentials
Write-Output "Creating target server..."
$targetServerName = Read-Host "Please enter a target server name"
$targetServerName = $targetServerName + "-" + [guid]::NewGuid()
$targetServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $targetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set target server firewall rules to allow all Azure IPs
$targetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$targetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$targetServer

# create sample databases to execute jobs against
$db1 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database1"
$db1
$db2 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database2"
$db2
```

### <a name="create-the-elastic-job-agent"></a>Skapa en elastiskt jobbagent

En agent för elastiska jobb är en Azure-resurs för att skapa, köra och hantera jobb. Agenten kör jobb baserat på ett schema eller som ett engångsjobb.

Cmdlet: en **New-AzSqlElasticJobAgent** kräver att det redan finns en databas i Azure SQL Database, så parametrarna *resourceGroupName*, *servername* och *databasename* måste alla peka på befintliga resurser.

```powershell
Write-Output "Creating job agent..."
$agentName = Read-Host "Please enter a name for your new Elastic Job agent"
$jobAgent = $jobDatabase | New-AzSqlElasticJobAgent -Name $agentName
$jobAgent
```

### <a name="create-the-job-credentials"></a>Skapa autentiseringsuppgifter för jobb

Jobb använder de autentiseringsuppgifter som anges i databasen för att ansluta till mål databaserna som anges av mål gruppen vid körning och körning av skript. Dessa databasbegränsade autentiseringsuppgifter används också för att ansluta till huvuddatabasen för att räkna upp alla databaser i en server eller en elastisk pool när någon av dessa används som medlemstyp för målgruppen.

Databasbegränsade autentiseringsuppgifter måste skapas i databasen för jobbet. Alla måldatabaser måste ha en inloggning med tillräcklig behörighet för att slutföra jobbet.

![Autentiseringsuppgifter för elastiska jobb](./media/elastic-jobs-powershell-create/job-credentials.png)

Förutom autentiseringsuppgifterna i avbildningen, observera även tillägget av **beviljandekommando** i följande skript. Dessa behörigheter krävs för skriptet vi valde för det här exempeljobbet. Eftersom exemplet skapar en ny tabell i mål databaserna, behöver varje mål databas rätt behörighet för att kunna köras.

Kör följande skript för att skapa autentiseringsuppgifter för det nödvändiga jobbet (i jobbdatabasen):

```powershell
# in the master database (target server)
# create the master user login, master user, and job user login
$params = @{
  'database' = 'master'
  'serverInstance' =  $targetServer.ServerName + '.database.windows.net'
  'username' = $adminLogin
  'password' = $adminPassword
  'outputSqlErrors' = $true
  'query' = 'CREATE LOGIN masteruser WITH PASSWORD=''password!123'''
}
Invoke-SqlCmd @params
$params.query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @params
$params.query = 'CREATE LOGIN jobuser WITH PASSWORD=''password!123'''
Invoke-SqlCmd @params

# for each target database
# create the jobuser from jobuser login and check permission for script execution
$targetDatabases = @( $db1.DatabaseName, $Db2.DatabaseName )
$createJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$grantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$grantCreateScript = "GRANT CREATE TABLE TO jobuser"

$targetDatabases | % {
  $params.database = $_
  $params.query = $createJobUserScript
  Invoke-SqlCmd @params
  $params.query = $grantAlterSchemaScript
  Invoke-SqlCmd @params
  $params.query = $grantCreateScript
  Invoke-SqlCmd @params
}

# create job credential in Job database for master user
Write-Output "Creating job credentials..."
$loginPasswordSecure = (ConvertTo-SecureString -String 'password!123' -AsPlainText -Force)

$masterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $loginPasswordSecure
$masterCred = $jobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $masterCred

$jobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $loginPasswordSecure
$jobCred = $jobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $jobCred
```

### <a name="define-the-target-databases-to-run-the-job-against"></a>Definiera de mål databaser som jobbet ska köras mot

En [målgrupp](job-automation-overview.md#target-group) utgörs av en eller flera databaser som ett jobbsteg ska köras mot.

Följande fragment skapar två mål grupper: *serverGroup* och *serverGroupExcludingDb2*. *serverGroup* riktar alla databaser som finns på servern vid körningen och *serverGroupExcludingDb2* riktar sig mot alla databaser på servern, förutom *targetDb2*:

```powershell
Write-Output "Creating test target groups..."
# create ServerGroup target group
$serverGroup = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$serverGroup | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName

# create ServerGroup with an exclusion of db2
$serverGroupExcludingDb2 = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -Database $db2.DatabaseName -Exclude
```

### <a name="create-a-job-and-steps"></a>Skapa ett jobb och steg

I det här exemplet definieras ett jobb och två jobb steg för jobbet som ska köras. Det första steget i jobbet (*step1*) skapar en ny tabell (*Step1Table*) i varje databas i målgruppen *ServerGroup*. Det andra steget i jobbet (*step2*) skapar en ny tabell (*Step2Table*) i varje databas utom för *TargetDb2*, eftersom målgruppen som definierades tidigare har angett att den ska undantas.

```powershell
Write-Output "Creating a new job..."
$jobName = "Job1"
$job = $jobAgent | New-AzSqlElasticJob -Name $jobName -RunOnce
$job

Write-Output "Creating job steps..."
$sqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$sqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $serverGroup.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText1
$job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $serverGroupExcludingDb2.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText2
```

### <a name="run-the-job"></a>Kör jobbet

Kör följande kommando direkt för att starta jobbet:

```powershell
Write-Output "Start a new execution of the job..."
$jobExecution = $job | Start-AzSqlElasticJob
$jobExecution
```

Efter varje steg som avslutats korrekt bör du se två nya tabeller i TargetDb1 och bara en ny tabell i TargetDb2:

   ![verifiering av nya tabeller i SSMS](./media/elastic-jobs-powershell-create/job-execution-verification.png)

Du kan också schemalägga att jobbet ska köras senare. Kör följande kommando för att schemalägga ett jobb så att det körs vid en viss tid:

```powershell
# run every hour starting from now
$job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

### <a name="monitor-status-of-job-executions"></a>Övervaka status för jobbkörningar

Följande kodavsnitt hämtar information om jobbkörning:

```powershell
# get the latest 10 executions run
$jobAgent | Get-AzSqlElasticJobExecution -Count 10

# get the job step execution details
$jobExecution | Get-AzSqlElasticJobStepExecution

# get the job target execution details
$jobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

I följande tabell visas möjliga tillstånd för jobb körning:

|Stat|Beskrivning|
|:---|:---|
|**Skapad** | Jobb körningen har precis skapats och pågår ännu inte.|
|**Pågår** | Jobb körningen pågår just nu.|
|**WaitingForRetry** | Det gick inte att slutföra åtgärden för jobb körningen och väntar på att försöka igen.|
|**Brutit** | Jobb körningen har slutförts.|
|**SucceededWithSkipped** | Jobb körningen har slutförts men vissa av dess underordnade hoppades över.|
|**Misslyckades** | Jobb körningen har misslyckats och förbrukat sina återförsök.|
|**Stängningsåtgärd** | Tids gränsen nåddes för jobb körningen.|
|**Avbrutna** | Jobb körningen avbröts.|
|**Överhoppad** | Jobb körningen hoppades över eftersom en annan körning av samma jobb steg redan kördes på samma mål.|
|**WaitingForChildJobExecutions** | Jobb körningen väntar på att de underordnade körningarna ska slutföras.|

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort alla resurser som du har skapat i den här självstudien genom att ta bort resursgruppen.

> [!TIP]
> Om du planerar att fortsätta arbeta med de här jobben rensar du inte resurserna som du skapade i den här artikeln.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien körde du ett Transact-SQL-skript mot en uppsättning databaser. Du har lärt dig att göra följande:

> [!div class="checklist"]
> * Skapa en elastiskt jobbagent
> * Skapa autentiseringsuppgifter för jobbet så att det kan köra skript på sina mål
> * Definiera mål (servrar, elastiska pooler, databaser, shard-kartor) som du vill köra jobbet mot
> * Skapa autentiseringsuppgifter för databasen i måldatabaserna så att agenten kan ansluta och köra jobb
> * Skapa ett jobb
> * Lägga till ett steg i jobbet
> * Starta en körning av jobbet
> * Övervaka jobbet

> [!div class="nextstepaction"]
> [Hantera elastiska jobb med Transact-SQL](elastic-jobs-tsql-create-manage.md)
