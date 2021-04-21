---
title: Importera en BACPAC-fil för att skapa en databas i Azure SQL Database
description: Skapa en ny databas i Azure SQL Database eller Azure SQL Managed Instance från en BACPAC-fil.
services: sql-database
ms.service: sql-db-mi
ms.subservice: migrate
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/29/2020
ms.openlocfilehash: eddcab2c0a34ef437e4f2f1e2203fee9065133a4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781891"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Snabbstart: Importera en BACPAC-fil till en databas i Azure SQL Database eller Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Du kan importera en SQL Server databas till Azure SQL Database eller SQL Managed Instance med hjälp av en [BACPAC-fil.](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) Du kan importera data från en BACPAC-fil lagrad i Azure Blob Storage (endast standardlagring) eller från lokal lagring på en lokal plats. För att maximera importhastigheten genom att ge fler och snabbare resurser ska du skala databasen till en högre tjänstnivå och beräkningsstorlek under importprocessen. Du kan sedan skala ned när importen har slutförts.

> [!NOTE]
> Kompatibilitetsnivån för den importerade databasen baseras på källdatabasens kompatibilitetsnivå.

> [!IMPORTANT]
> När du har importerat databasen kan du välja att använda databasen på den aktuella kompatibilitetsnivån (nivå 100 för databasen AdventureWorks2008R2) eller på en högre nivå. Mer information om effekterna av och alternativ för att köra en databas på en specifik kompatibilitetsnivå finns i [Ändra databasens kompatibilitetsnivå](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). I [Ändra konfiguration av databasomfång](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) finns även information om ytterligare databasnivåinställningar som rör kompatibilitetsnivåer.

## <a name="using-azure-portal"></a>Använda Azure Portal

Titta på den här videon för att se hur du importerar från en BACPAC-fil i Azure Portal eller fortsätta att läsa nedan:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

Den [Azure Portal endast](https://portal.azure.com) *stöd* för att skapa en enkel databas i Azure SQL Database och *endast* från en BACPAC-fil som lagras i Azure Blob Storage.

Om du vill migrera en [databas till Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) från en BACPAC-fil använder du SQL Server Management Studio eller SQLPackage, med hjälp av Azure Portal eller Azure PowerShell stöds inte för närvarande.

> [!NOTE]
> Datorer som bearbetar import-/exportbegäranden som skickas via Azure Portal eller PowerShell måste lagra BACPAC-filen samt temporära filer som genereras av Data-Tier Application Framework (DacFX). Diskutrymmet som krävs varierar avsevärt mellan databaser med samma storlek och kan kräva diskutrymme upp till 3 gånger databasens storlek. Datorer som kör import-/exportbegäran har bara 450 GB lokalt diskutrymme. Därför kan vissa begäranden misslyckas med felet `There is not enough space on the disk` . I det här fallet är lösningen att köra sqlpackage.exe på en dator med tillräckligt med lokalt diskutrymme. Vi rekommenderar att du använder SqlPackage för att importera/exportera databaser som är större än 150 GB för att undvika det här problemet.

1. Om du vill importera från en BACPAC-fil till en ny enkel databas med hjälp av Azure Portal öppnar du lämplig serversida och väljer sedan **Importera databas i verktygsfältet.**  

   ![Databasimport1](./media/database-import/sql-server-import-database.png)

1. Välj lagringskontot och containern för BACPAC-filen och välj sedan den BACPAC-fil som du vill importera från.

1. Ange den nya databasstorleken (vanligtvis samma som ursprunget) och ange SQL Server autentiseringsuppgifter. En lista över möjliga värden för en ny databas i Azure SQL Database finns i [Skapa databas](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true).

   ![Databasimport2](./media/database-import/sql-server-import-database-settings.png)

1. Klicka på **OK**.

1. Om du vill övervaka förloppet för en import öppnar du databasens serversida och **väljer** **Importera/exportera historik** under Inställningar. När importen lyckas har den **statusen Slutförd.**

   ![Importstatus för databas](./media/database-import/sql-server-import-database-history.png)

1. Kontrollera att databasen är live på servern genom att välja **SQL-databaser** och kontrollera att den nya databasen är **Online.**

## <a name="using-sqlpackage"></a>Använda SqlPackage

Information om hur du SQL Server en databas med [hjälp av kommandoradsverktyget SqlPackage](/sql/tools/sqlpackage) finns i [importera parametrar och egenskaper](/sql/tools/sqlpackage#import-parameters-and-properties). [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) och [SQL Server Data Tools för Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt) sqlpackage. Du kan också ladda ned den [senaste Versionen av SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) från Microsoft Download Center. 

För skalning och prestanda rekommenderar vi att du använder SqlPackage i de flesta produktionsmiljöer i stället för att använda Azure Portal. En blogg SQL Server Customer Advisory Team om att migrera med hjälp av filer finns i Migrera från SQL Server till `BACPAC` [Azure SQL Database med BACPAC Files](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

Den DTU-baserade etableringsmodellen har stöd för att välja värden för maximal databasstorlek för varje nivå. När du importerar en databas [använder du något av dessa värden som stöds.](/sql/t-sql/statements/create-database-transact-sql) 

Följande SqlPackage-kommando importerar **AdventureWorks2008R2-databasen** från lokal lagring till en logisk SQL-server med namnet **mynewserver20170403**. Den skapar en ny databas med namnet **myMigratedDatabase** med en **Premium-tjänstnivå** och ett **P6-tjänstmål.** Ändra dessa värden efter behov för din miljö.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Om du vill Azure SQL Database en brandvägg bakom en företagsbrandvägg måste brandväggen ha port 1433 öppen. Om du vill SQL Managed Instance anslutning måste du ha en [punkt-till-plats-anslutning](../managed-instance/point-to-site-p2s-configure.md) eller en Express Route-anslutning.

Det här exemplet visar hur du importerar en databas med hjälp av SqlPackage med Active Directory Universal Authentication.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>Använda PowerShell

> [!NOTE]
> [En SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) stöder för närvarande inte migrering av en databas till en instansdatabas från en BACPAC-fil med Azure PowerShell. Om du vill importera till en SQL Managed Instance använder du SQL Server Management Studio eller SQLPackage.

> [!NOTE]
> Datorerna som bearbetar import-/exportbegäranden som skickas via portalen eller Powershell måste lagra bacpac-filen samt temporära filer som genereras av Data-Tier Application Framework (DacFX). Diskutrymmet som krävs varierar avsevärt mellan databaser med samma storlek och kan ta upp till 3 gånger av databasstorleken. Datorer som kör import/export-begäran har bara 450 GB lokalt diskutrymme. Därför kan vissa begäranden misslyckas med felet "Det finns inte tillräckligt med utrymme på disken". I det här fallet är lösningen att köra sqlpackage.exe på en dator med tillräckligt med lokalt diskutrymme. Använd SqlPackage för att undvika det här problemet när du importerar/exporterar databaser som är större än 150 GB.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager (RM) stöds fortfarande, men all framtida utveckling är för Az.Sql-modulen. AzureRM-modulen fortsätter att ta emot felkorrigeringar fram till åtminstone december 2020.  Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är betydligt identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

Använd [cmdleten New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) för att skicka en importdatabasbegäran till Azure. Beroende på databasens storlek kan importen ta lite tid att slutföra. Den DTU-baserade etableringsmodellen stöder utvalda värden för maximal databasstorlek för varje nivå. När du importerar en databas [använder du något av dessa värden som stöds.](/sql/t-sql/statements/create-database-transact-sql) 

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

Du kan använda cmdleten [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) för att kontrollera importens förlopp. Om du kör cmdleten omedelbart efter begäran returneras vanligtvis `Status: InProgress` . Importen är klar när du ser `Status: Succeeded` .

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd kommandot [az-sql-db-import för](/cli/azure/sql/db#az_sql_db_import) att skicka en importdatabasbegäran till Azure. Beroende på databasens storlek kan importen ta lite tid att slutföra. Den DTU-baserade etableringsmodellen har stöd för att välja värden för maximal databasstorlek för varje nivå. När du importerar en databas [använder du något av dessa värden som stöds.](/sql/t-sql/statements/create-database-transact-sql) 

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> Ett annat skriptexempel finns i [Importera en databas från en BACPAC-fil.](scripts/import-from-bacpac-powershell.md)

## <a name="cancel-the-import-request"></a>Avbryta importbegäran

Använd kommandot [Database Operations - Cancel API](/rest/api/sql/databaseoperations/cancel) eller Powershell [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/Stop-AzSqlDatabaseActivity), här är ett exempel på powershell-kommando.

```cmd
Stop-AzSqlDatabaseActivity -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -OperationId $Operation.OperationId
```


## <a name="limitations"></a>Begränsningar

- Import till en databas i elastisk pool stöds inte. Du kan importera data till en enstaka databas och sedan flytta databasen till en elastisk pool.
- Import Export Service fungerar inte när Tillåt åtkomst till Azure-tjänster är inställt på AV. Du kan dock komma runt problemet genom att manuellt köra sqlpackage.exe en virtuell Azure-dator eller utföra exporten direkt i koden med hjälp av DACFx-API:et.
- Import stöder inte att du anger redundans för lagring av säkerhetskopior när du skapar en ny databas och skapar med standardredundans för geo-redundant lagring av säkerhetskopior. Du kan komma runt problemet genom att först skapa en tom databas med önskad redundans för säkerhetskopieringslagring med hjälp Azure Portal eller PowerShell och sedan importera BACPAC till den här tomma databasen. 

> [!NOTE]
> Azure SQL Database Redundans för konfigurerbar säkerhetskopieringslagring är för närvarande tillgänglig i offentlig förhandsversion Sydostasien Endast i Azure-regionen.

## <a name="import-using-wizards"></a>Importera med hjälp av guider

Du kan också använda de här guiderna.

- [Guiden Importera datanivåprogram i SQL Server Management Studio](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server import- och exportguiden.](/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)

## <a name="next-steps"></a>Nästa steg

- Information om hur du ansluter till och frågar en databas i Azure SQL Database finns i [Snabbstart: Azure SQL Database: Använda SQL Server Management Studio](connect-query-ssms.md)för att ansluta till och fråga efter data .
- En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (på engelska).
- En diskussion om hela migreringsprocessen SQL Server databasen, inklusive prestandarekommendationer, finns i [SQL Server databasmigrering för att Azure SQL Database](migrate-to-database-from-sql-server.md).
- Information om hur du hanterar och delar lagringsnycklar och signaturer för delad åtkomst på ett säkert sätt [finns i Azure Storage säkerhetsguide](../../storage/blobs/security-recommendations.md).