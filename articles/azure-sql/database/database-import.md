---
title: Importera en BACPAC-fil för att skapa en databas i Azure SQL Database
description: Skapa en ny databas i Azure SQL Database eller Azure SQL-hanterad instans från en BACPAC-fil.
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
ms.openlocfilehash: d59f08fc061bace55a7cd3d774380046055828ae
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618618"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Snabb start: importera en BACPAC-fil till en databas i Azure SQL Database eller Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Du kan importera en SQL Server-databas till Azure SQL Database eller SQL-hanterad instans med hjälp av en [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) -fil. Du kan importera data från en BACPAC-fil lagrad i Azure Blob Storage (endast standardlagring) eller från lokal lagring på en lokal plats. För att maximera importhastigheten genom att ge fler och snabbare resurser ska du skala databasen till en högre tjänstnivå och beräkningsstorlek under importprocessen. Du kan sedan skala ned när importen har slutförts.

> [!NOTE]
> Den importerade databasens kompatibilitetsnivå baseras på käll databasens kompatibilitetsnivå.

> [!IMPORTANT]
> När du har importerat databasen kan du välja att köra databasen på dess aktuella kompatibilitetsnivå (nivå 100 för AdventureWorks2008R2-databasen) eller på en högre nivå. Mer information om effekterna av och alternativ för att köra en databas på en specifik kompatibilitetsnivå finns i [Ändra databasens kompatibilitetsnivå](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). I [Ändra konfiguration av databasomfång](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) finns även information om ytterligare databasnivåinställningar som rör kompatibilitetsnivåer.

## <a name="using-azure-portal"></a>Använda Azure Portal

Titta på den här videon för att se hur du importerar från en BACPAC-fil i Azure Portal eller fortsätta läsa nedan:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

[Azure Portal](https://portal.azure.com) har *bara* stöd för att skapa en enda databas i Azure SQL Database och *endast* från en BACPAC-fil som lagras i Azure Blob Storage.

Om du vill migrera en databas till en [Azure SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md) från en BACPAC-fil, använder du SQL Server Management Studio eller SQLPackage med hjälp av Azure Portal eller Azure PowerShell stöds inte för närvarande.

> [!NOTE]
> Datorer som bearbetar import-/export begär Anden som skickas via Azure Portal eller PowerShell måste lagra BACPAC-filen och temporära filer som genereras av Data-Tier Application Framework (DacFX). Det disk utrymme som krävs varierar kraftigt mellan databaser med samma storlek och kan kräva disk utrymme upp till 3 gånger databasens storlek. Datorer som kör import/export-begäran har bara 450GB lokalt disk utrymme. Därför kan vissa begär Anden Miss lyckas med felet `There is not enough space on the disk` . I det här fallet är lösningen att köra sqlpackage.exe på en dator med tillräckligt lokalt disk utrymme. Vi rekommenderar att du använder SqlPackage för att importera/exportera databaser som är större än 150 GB för att undvika det här problemet.

1. Om du vill importera från en BACPAC-fil till en ny databas med hjälp av Azure Portal öppnar du lämplig server sida och väljer sedan **Importera databas** i verktygsfältet.  

   ![Databas import1](./media/database-import/sql-server-import-database.png)

1. Välj lagrings kontot och behållaren för BACPAC-filen och välj sedan den BACPAC-fil som du vill importera från.

1. Ange den nya databas storleken (vanligt vis samma som ursprung) och ange mål SQL Server autentiseringsuppgifter. En lista över möjliga värden för en ny databas i Azure SQL Database finns i [skapa databas](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Databas import2](./media/database-import/sql-server-import-database-settings.png)

1. Klicka på **OK**.

1. Om du vill övervaka en import status öppnar du sidan Server för databasen och väljer **import/export-historik** under **Inställningar**. När det är klart har importen statusen **slutförd** .

   ![Import status för databas](./media/database-import/sql-server-import-database-history.png)

1. Kontrol lera att databasen är Live på servern genom att välja **SQL-databaser** och kontrol lera att den nya databasen är **online**.

## <a name="using-sqlpackage"></a>Använda SqlPackage

Om du vill importera en SQL Server-databas med hjälp av kommando rads verktyget [SqlPackage](/sql/tools/sqlpackage) , se [import parametrar och egenskaper](/sql/tools/sqlpackage#import-parameters-and-properties). [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) och [SQL Server Data Tools för Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt) inkluderar SqlPackage. Du kan också hämta de senaste [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) från Microsoft Download Center. 

För skalning och prestanda rekommenderar vi att du använder SqlPackage i de flesta produktions miljöer snarare än att använda Azure Portal. En SQL Server kund expert team blogg om hur du migrerar med hjälp av `BACPAC` filer finns i [migrera från SQL Server till Azure SQL Database använda BACPAC-filer](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

DTU-baserad etablerings modell stöder Select Database maximum size-värden för varje nivå. [Använd något av de värden som stöds](/sql/t-sql/statements/create-database-transact-sql)när du importerar en databas. 

Följande SqlPackage-kommando importerar **AdventureWorks2008R2** -databasen från lokal lagring till en logisk SQL-Server med namnet **mynewserver20170403**. Den skapar en ny databas med namnet **myMigratedDatabase** med en **Premium** service-nivå och ett **P6** -tjänst mål. Ändra värdena efter behov för din miljö.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Om du vill ansluta till Azure SQL Database från bakom en företags brand vägg måste brand väggen ha port 1433 öppen. Om du vill ansluta till SQL-hanterad instans måste du ha en [punkt-till-plats-anslutning](../managed-instance/point-to-site-p2s-configure.md) eller en Express Route-anslutning.

Det här exemplet visar hur du importerar en databas med hjälp av SqlPackage med Active Directory Universal Authentication.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>Använda PowerShell

> [!NOTE]
> [En SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md) stöder för närvarande inte migrering av en databas till en instans databas från en BACPAC-fil med hjälp av Azure PowerShell. Använd SQL Server Management Studio eller SQLPackage om du vill importera till en SQL-hanterad instans.

> [!NOTE]
> De datorer som bearbetar import/export-begäranden som skickas via portalen eller PowerShell måste lagra BACPAC-filen och temporära filer som genereras av Data-Tier Application Framework (DacFX). Det disk utrymme som krävs varierar kraftigt mellan databaser med samma storlek och kan ta upp till tre gånger från databasens storlek. Datorer som kör import/export-begäran har bara 450GB lokalt disk utrymme. Som ett resultat kan vissa förfrågningar Miss lyckas med fel meddelandet "det finns inte tillräckligt med disk utrymme på disken". I det här fallet är lösningen att köra sqlpackage.exe på en dator med tillräckligt lokalt disk utrymme. När du importerar/exporterar databaser som är större än 150 GB använder du SqlPackage för att undvika det här problemet.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> PowerShell-modulen för Azure Resource Manager (RM) stöds fortfarande, men all framtida utveckling är för AZ. SQL-modulen. AzureRM-modulen kommer att fortsätta att ta emot fel korrigeringar fram till minst december 2020.  Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell AZ-modulen](/powershell/azure/new-azureps-module-az).

Använd cmdleten [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) för att skicka en begäran om att importera databasen till Azure. Det kan ta en stund att slutföra importen, beroende på databasens storlek. DTU-baserad etablerings modell stöder Select Database maximum size-värden för varje nivå. [Använd något av de värden som stöds](/sql/t-sql/statements/create-database-transact-sql)när du importerar en databas. 

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

Du kan använda cmdleten [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) för att kontrol lera importens förlopp. Att köra cmdleten direkt efter att begäran vanligt vis returneras `Status: InProgress` . Importen är klar när du ser `Status: Succeeded` .

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

Använd kommandot [AZ-SQL-DB-import](/cli/azure/sql/db#az-sql-db-import) för att skicka en begäran om att importera databasen till Azure. Det kan ta en stund att slutföra importen, beroende på databasens storlek. DTU-baserad etablerings modell stöder Select Database maximum size-värden för varje nivå. [Använd något av de värden som stöds](/sql/t-sql/statements/create-database-transact-sql)när du importerar en databas. 

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
> Ett annat skript exempel finns i [Importera en databas från en BACPAC-fil](scripts/import-from-bacpac-powershell.md).

## <a name="cancel-the-import-request"></a>Avbryt import förfrågan

Använd [databas åtgärderna-Cancel-API](https://docs.microsoft.com/rest/api/sql/databaseoperations/cancel) eller kommandot PowerShell [Stop-AzSqlDatabaseActivity](https://docs.microsoft.com/powershell/module/az.sql/Stop-AzSqlDatabaseActivity), här ett exempel på PowerShell-kommando.

```cmd
Stop-AzSqlDatabaseActivity -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -OperationId $Operation.OperationId
```


## <a name="limitations"></a>Begränsningar

- Import till en databas i elastisk pool stöds inte. Du kan importera data till en enstaka databas och sedan flytta databasen till en elastisk pool.
- Import-/exportguiden fungerar inte när Tillåt åtkomst till Azure-tjänster är inställt på av. Du kan dock lösa problemet genom att manuellt köra sqlpackage.exe från en virtuell Azure-dator eller utföra exporten direkt i din kod med hjälp av DACFx-API: et.
- Import stöder inte att du anger en redundans för säkerhets kopiering när du skapar en ny databas och skapar med standard redundansen för Geo-redundant lagring. För att undvika detta måste du först skapa en tom databas med önskad redundans för säkerhets kopiering med Azure Portal eller PowerShell och sedan importera BACPAC till den tomma databasen. 

> [!NOTE]
> Azure SQL Database konfigurerbar redundans för säkerhets kopiering är för närvarande endast tillgängligt i den allmänt tillgängliga för hands versionen i Sydostasien Azure-region.

## <a name="import-using-wizards"></a>Importera med hjälp av guider

Du kan också använda de här guiderna.

- [Guiden Importera data nivå program i SQL Server Management Studio](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server import-och export guiden](/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Nästa steg

- Information om hur du ansluter till och frågar en databas i Azure SQL Database finns i [snabb start: Azure SQL Database: använd SQL Server Management Studio för att ansluta till och fråga efter data](connect-query-ssms.md).
- En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (på engelska).
- En beskrivning av hela processen för migrering av SQL Server databasen, inklusive prestanda rekommendationer, finns i [SQL Server Database migration till Azure SQL Database](migrate-to-database-from-sql-server.md).
- Information om hur du hanterar och delar lagrings nycklar och signaturer för delad åtkomst på ett säkert sätt finns i [Azure Storage Security guide](../../storage/blobs/security-recommendations.md).
