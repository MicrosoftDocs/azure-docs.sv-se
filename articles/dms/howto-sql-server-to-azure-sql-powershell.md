---
title: 'PowerShell: Migrera SQL Server till SQL Database'
titleSuffix: Azure Database Migration Service
description: Lär dig att migrera en databas från SQL Server till Azure SQL Database med hjälp av Azure PowerShell med Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 3152fe0bf77b73e593ac61efb5f386827bdc96ef
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643724"
---
# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-azure-powershell"></a>Migrera en SQL Server databas till Azure SQL Database med Azure PowerShell

I den här artikeln migrerar du **Adventureworks2012** -databasen som återställs till en lokal instans av SQL Server 2016 eller senare för Azure SQL Database med hjälp av Microsoft Azure PowerShell. Du kan migrera databaser från en SQL Server instans till Azure SQL Database genom att använda `Az.DataMigration` modulen i Microsoft Azure PowerShell.

I den här artikeln kan du se hur du:
> [!div class="checklist"]
>
> * Skapa en resursgrupp.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsjobb i en Azure Database Migration Service-instans.
> * Köra migreringen.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra de här stegen:

* [SQL Server 2016 eller senare](https://www.microsoft.com/sql-server/sql-server-downloads) (vilken utgåva som helst)
* För att aktivera TCP/IP-protokollet, som är inaktiverat som standard med SQL Server Express installation. Aktivera TCP/IP-protokollet genom att följa artikeln [Aktivera eller inaktivera ett Server nätverks protokoll](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Konfigurera Windows- [brandväggen för åtkomst till databas motorn](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* En Azure SQL Database-instans. Du kan skapa en Azure SQL Database-instans genom att följa informationen i artikeln [skapa en databas i Azure SQL Database i Azure Portal](../azure-sql/database/single-database-create-quickstart.md).
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 eller senare.
* För att skapa en Microsoft Azure Virtual Network med hjälp av Azure Resource Manager distributions modell, som förser Azure Database Migration Service med plats-till-plats-anslutning till dina lokala käll servrar genom att använda antingen [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* För att slutföra utvärderingen av din lokala databas och schema migrering med hjälp av Data Migration Assistant som beskrivs i artikeln [utföra en utvärdering av SQL Server migrering](/sql/dma/dma-assesssqlonprem)
* Hämta och installera modulen AZ. data migration från PowerShell-galleriet med hjälp av [PowerShell-cmdleten Install-module](/powershell/module/powershellget/Install-Module); Se till att öppna PowerShell-Kommandotolken med kör som administratör.
* För att säkerställa att autentiseringsuppgifterna som används för att ansluta till käll SQL Servers instansen har behörigheten [kontroll Server](/sql/t-sql/statements/grant-server-permissions-transact-sql) .
* För att säkerställa att de autentiseringsuppgifter som används för att ansluta till målet för Azure SQL DB-instansen har kontroll databasen behörighet för mål Azure SQL Database-databaser.
* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnads fritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Logga in på din Microsoft Azure prenumeration

Använd anvisningarna i artikeln Logga in [med Azure PowerShell](/powershell/azure/authenticate-azureps) för att logga in på din Azure-prenumeration med hjälp av PowerShell.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en resurs grupp innan du kan skapa en virtuell dator.

Skapa en resurs grupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) .

I följande exempel skapas en resurs grupp med namnet *myResourceGroup* i regionen *östra* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Skapa en instans av Azure Database Migration Service

Du kan skapa en ny instans av Azure Database Migration Service med hjälp av `New-AzDataMigrationService` cmdleten. Denna cmdlet förväntar sig följande obligatoriska parametrar:

* *Namn på Azure-resurs gruppen*. Du kan använda kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) för att skapa en Azure-resurs grupp som tidigare visas och ange dess namn som en parameter.
* *Tjänst namn*. Sträng som motsvarar det önskade unika tjänst namnet för Azure Database Migration Service 
* *Plats*. Anger tjänstens plats. Ange en plats för Azure Data Center, t. ex. USA, västra eller Sydostasien
* *SKU*. Den här parametern motsvarar DMS SKU-namnet. SKU-namnet som stöds för närvarande är *GeneralPurpose_4vCores*.
* *ID för virtuell undernät*. Du kan använda cmdleten [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) för att skapa ett undernät. 

I följande exempel skapas en tjänst med namnet *MyDMS* i resurs gruppen *MyDMSResourceGroup* som finns i regionen *USA, östra* med ett virtuellt nätverk med namnet *MyVNET* och undernät som kallas för *undernät*.

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

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Skapa ett objekt för databas anslutnings information för käll-och mål anslutningarna

Du kan skapa ett informations objekt för databas anslutning med hjälp av `New-AzDmsConnInfo` cmdleten. Denna cmdlet förväntar sig följande parametrar:

* *ServerType*. Vilken typ av databas anslutning som begärdes, till exempel SQL, Oracle eller MySQL. Använd SQL för SQL Server och Azure SQL.
* *Data källa*. Namnet eller IP-adressen för en SQL Server instans eller Azure SQL Database.
* *AuthType*. Autentiseringstypen för anslutning, som kan vara antingen SqlAuthentication eller WindowsAuthentication.
* Parametern *TrustServerCertificate* anger ett värde som anger om kanalen är krypterad och kringgår certifikat kedjan för att verifiera förtroende. Värdet kan vara sant eller falskt.

I följande exempel skapas objektet anslutnings information för käll SQL Server som kallas MySourceSQLServer med SQL-autentisering:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Nästa exempel visar hur du skapar anslutnings information för en server med namnet SQLAzureTarget med SQL-autentisering:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Ange databaser för migreringsjobbet

Skapa en lista med `AzDataMigrationDatabaseInfo` objekt som anger databaser som en del av Azure Database migration Project som kan anges som parameter för att skapa projektet. Cmdleten `New-AzDataMigrationDatabaseInfo` kan användas för att skapa AzDataMigrationDatabaseInfo. 

I följande exempel skapas `AzDataMigrationDatabaseInfo` Project för **AdventureWorks2016** -databasen och läggs till i listan som ska anges som parameter för att skapa projekt.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Skapa ett projekt objekt

Slutligen kan du skapa ett Azure Database migration-projekt med namnet *MyDMSProject* som finns i *östra USA* och sedan `New-AzDataMigrationProject` lägga till de tidigare skapade käll-och mål anslutningarna och listan över databaser som ska migreras.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Skapa och starta en migrerings uppgift

Slutligen skapar du och startar Azure Database migration-aktiviteten. För migrering av Azure Database krävs information om autentiseringsuppgifter för både källa och mål och lista över databas tabeller som ska migreras utöver den information som redan finns med i projektet som en förutsättning.

### <a name="create-credential-parameters-for-source-and-target"></a>Skapa Credential-parametrar för källa och mål

Anslutnings säkerhets referenser kan skapas som ett [PSCredential](/dotnet/api/system.management.automation.pscredential) -objekt.

I följande exempel visas hur du skapar *PSCredential* -objekt för både käll-och mål anslutningar som ger lösen ord som String-variabler *$sourcePassword* och *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Skapa en tabell karta och välj käll-och mål parametrar för migrering

En annan parameter som krävs för migrering är mappning av tabeller från källa till mål som ska migreras. Skapa en ord lista med tabeller som innehåller en mappning mellan käll-och mål tabeller för migrering. Följande exempel illustrerar mappning mellan käll-och mål tabeller personal schema för AdventureWorks 2016-databasen.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

Nästa steg är att välja käll-och mål databaserna och tillhandahålla tabell mappning för att migrera som en parameter med hjälp av `New-AzDmsSelectedDB` cmdleten, som du ser i följande exempel:

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-the-migration-task-and-start-it"></a>Skapa migreringen och starta den

Använd `New-AzDataMigrationTask` cmdleten för att skapa och starta en migrering. Denna cmdlet förväntar sig följande parametrar:

* *TaskType*. Typ av migrering som ska skapas för SQL Server till Azure SQL Database *MigrateSqlServerSqlDb* förväntas. 
* *Resurs grupps namn*. Namnet på den Azure-resurs grupp där aktiviteten ska skapas.
* *ServiceName*. Azure Database Migration Service instans där du vill skapa uppgiften.
* *ProjectName*. Namnet på Azure Database Migration Service projektet som uppgiften ska skapas i. 
* *Aktivitets* namn. Namn på den uppgift som ska skapas. 
* *SourceConnection*. AzDmsConnInfo-objekt som representerar käll SQL Server anslutning.
* *TargetConnection*. AzDmsConnInfo-objekt som representerar mål Azure SQL Database anslutning.
* *SourceCred*. [PSCredential](/dotnet/api/system.management.automation.pscredential) -objekt för att ansluta till käll servern.
* *TargetCred*. [PSCredential](/dotnet/api/system.management.automation.pscredential) -objekt för att ansluta till mål servern.
* *SelectedDatabase*. AzDataMigrationSelectedDB-objekt som representerar käll-och mål databas mappning.
* *SchemaValidation*. (valfritt, växel parameter) Efter migreringen utför en jämförelse av schema informationen mellan källa och mål.
* *DataIntegrityValidation*. (valfritt, växel parameter) Efter migreringen utför en kontroll summa som baseras på data integritet mellan källa och mål.
* *QueryAnalysisValidation*. (valfritt, växel parameter) Efter migreringen utför en snabb och intelligent fråga-analys genom att hämta frågor från käll databasen och köra dem i målet.

I följande exempel skapas och startas en migrerings aktivitet med namnet myDMSTask:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
```

I följande exempel skapas och startas samma migrerings aktivitet som ovan, men utför även alla tre valideringar:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -SchemaValidation `
  -DataIntegrityValidation `
  -QueryAnalysisValidation `
```

## <a name="monitor-the-migration"></a>Övervaka migreringen

Du kan övervaka migreringen som körs genom att fråga egenskapen state för aktiviteten så som visas i följande exempel:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>Tar bort DMS-instansen

När migreringen är klar kan du ta bort Azure DMS-instansen:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-step"></a>Nästa steg

* Läs igenom vägledningen för migrering i Microsoft [Database migration guide](https://datamigration.microsoft.com/).