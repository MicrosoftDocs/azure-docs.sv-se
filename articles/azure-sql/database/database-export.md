---
title: Exportera en Azure SQL Database till en BACPAC-fil (Azure Portal)
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Exportera en databas till en BACPAC-fil med hjälp av Azure Portal.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
author: stevestein
ms.custom: sqldbrb=2
ms.author: sstein
ms.reviewer: ''
ms.date: 01/11/2021
ms.topic: how-to
ms.openlocfilehash: 14854f839d6dfe3c8a08a4a1453fd78e389fe8d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568740"
---
# <a name="export-to-a-bacpac-file---azure-sql-database-and-azure-sql-managed-instance"></a>Exportera till en BACPAC-fil – Azure SQL Database och Azure SQL-hanterad instans

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

När du behöver exportera en databas för arkivering eller för att flytta till en annan plattform kan du exportera databasens schema och data till en [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#Anchor_4) -fil. En BACPAC-fil är en ZIP-fil med en utökning av BACPAC som innehåller metadata och data från databasen. En BACPAC-fil kan lagras i Azure Blob Storage eller i lokal lagring på en lokal plats och senare importeras tillbaka till Azure SQL Database, Azure SQL-hanterad instans eller en SQL Server instans.

## <a name="considerations"></a>Överväganden

- För att en export ska kunna utföras konsekvent måste du se till att ingen Skriv aktivitet inträffar under exporten eller att du exporterar från en [transaktions konsekvent kopia](database-copy.md) av databasen.
- Om du exporterar till Blob Storage är den maximala storleken för en BACPAC-fil 200 GB. För att arkivera en större BACPAC-fil, exportera till lokal lagring.
- Det finns inte stöd för att exportera en BACPAC-fil till Azure Premium Storage med de metoder som beskrivs i den här artikeln.
- Det finns för närvarande inte stöd för lagring bakom en brand vägg.
- Lagrings fil namnet eller indatavärdet för StorageURI måste vara mindre än 128 tecken långt och får inte sluta med "." och får inte innehålla specialtecken som blank steg eller "<, >, *,%, &,:, \, /,?". 
- Om export åtgärden överskrider 20 timmar kan den avbrytas. Om du vill öka prestanda under exporten kan du:

  - Öka din beräknings storlek tillfälligt.
  - Upphöra med alla Läs-och skriv aktiviteter under exporten.
  - Använd ett [grupperat index](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described) med värden som inte är null på alla stora tabeller. Utan grupperade index kan en export Miss lyckas om det tar längre tid än 6-12 timmar. Detta beror på att export tjänsten måste slutföra en tabells ökning för att försöka Exportera hela tabellen. Ett bra sätt att avgöra om dina tabeller är optimerade för export är att köra **DBCC SHOW_STATISTICS** och se till att *RANGE_HI_KEY* inte är null och att dess värde har en bra distribution. Mer information finns i [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql).

> [!NOTE]
> BACPACs är inte avsedda att användas för säkerhets kopierings-och återställnings åtgärder. Azure skapar automatiskt säkerhets kopior för varje användar databas. Mer information finns i [Översikt över verksamhets kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md) och [SQL Database säkerhets kopieringar](automated-backups-overview.md).

## <a name="the-azure-portal"></a>Azure Portal

Det finns för närvarande inte stöd för att exportera en BACPAC av en databas från en [Azure SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md) med hjälp av Azure Portal. Använd SQL Server Management Studio eller SQLPackage i stället.

> [!NOTE]
> Datorer som bearbetar import-/export begär Anden som skickas via Azure Portal eller PowerShell måste lagra BACPAC-filen och temporära filer som genereras av Data-Tier Application Framework (DacFX). Det disk utrymme som krävs varierar kraftigt mellan databaser med samma storlek och kan kräva disk utrymme upp till 3 gånger databasens storlek. Datorer som kör import/export-begäran har bara 450GB lokalt disk utrymme. Därför kan vissa begär Anden Miss lyckas med felet `There is not enough space on the disk` . I det här fallet är lösningen att köra sqlpackage.exe på en dator med tillräckligt lokalt disk utrymme. Vi rekommenderar att du använder [SqlPackage](#sqlpackage-utility) för att importera/exportera databaser som är större än 150 GB för att undvika det här problemet.

1. Om du vill exportera en databas med hjälp av [Azure Portal](https://portal.azure.com)öppnar du sidan för din databas och klickar på **Exportera** i verktygsfältet.

   ![Skärm bild som visar knappen Exportera.](./media/database-export/database-export1.png)

2. Ange BACPAC-filnamn, Välj ett befintligt Azure Storage-konto och container för exporten och ange sedan lämpliga autentiseringsuppgifter för åtkomst till käll databasen. En SQL **Server Admin-inloggning** krävs här även om du är Azure-administratören, eftersom det är en Azure-administratör som inte motsvarar administratörs behörighet i Azure SQL Database eller Azure SQL-hanterad instans.

    ![Databas export](./media/database-export/database-export2.png)

3. Klicka på **OK**.

4. Du övervakar förloppet för export åtgärden genom att öppna sidan för servern som innehåller den databas som exporteras. Under **Inställningar** och klicka sedan på **import/export-historik**.

   ![Exportera historik](./media/database-export/export-history.png)

## <a name="sqlpackage-utility"></a>SQLPackage-verktyg

Om du vill exportera en databas i SQL Database med hjälp av kommando rads verktyget [SqlPackage](/sql/tools/sqlpackage) , se [Exportera parametrar och egenskaper](/sql/tools/sqlpackage#export-parameters-and-properties). SQLPackage-verktyget levereras med de senaste versionerna av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) och [SQL Server Data Tools för Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt), eller så kan du ladda ned den senaste versionen av [SQLPackage](https://www.microsoft.com/download/details.aspx?id=53876) direkt från Microsoft Download Center.

Vi rekommenderar att du använder SQLPackage-verktyget för skalning och prestanda i de flesta produktions miljöer. En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files) (på engelska).

Det här exemplet visar hur du exporterar en databas med hjälp av SqlPackage.exe med Active Directory Universal Authentication:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

De senaste versionerna av SQL Server Management Studio innehåller en guide för att exportera en databas i Azure SQL Database eller en SQL-hanterad instans databas till en BACPAC-fil. Se [Exportera ett data skikts program](/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="powershell"></a>PowerShell

> [!NOTE]
> [Azure SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md) stöder för närvarande inte export av en databas till en BACPAC-fil med hjälp av Azure PowerShell. Om du vill exportera en hanterad instans till en BACPAC-fil använder du SQL Server Management Studio eller SQLPackage.

Använd cmdleten [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) för att skicka en begäran om att exportera databas till tjänsten Azure SQL Database. Det kan ta en stund att slutföra export åtgärden, beroende på databasens storlek.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Om du vill kontrol lera status för export förfrågan använder du cmdleten [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . Om du kör det omedelbart efter det att begäran vanligt vis returnerar **status: pågår**. När du ser **status:** exporten har slutförts.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```
## <a name="cancel-the-export-request"></a>Avbryt export förfrågan

Använd [databas åtgärderna-Cancel-API](/rest/api/sql/databaseoperations/cancel) eller kommandot PowerShell [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/Stop-AzSqlDatabaseActivity), här ett exempel på PowerShell-kommando.

```cmd
Stop-AzSqlDatabaseActivity -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -OperationId $Operation.OperationId
```

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om långsiktig kvarhållning av säkerhets kopior av en enskild databas och databaser i pooler som ett alternativ till att exportera en databas för Arkiv lag ring, se [långsiktig kvarhållning av säkerhets kopior](long-term-retention-overview.md). Du kan använda SQL Agent-jobb för att schemalägga säkerhets kopieringar av [kopierade databaser](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) som ett alternativ till långsiktig kvarhållning av säkerhets kopior.
- En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files) (på engelska).
- Mer information om hur du importerar en BACPAC till en SQL Server-databas finns i [Importera en BACPAC till en SQL Server-databas](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database).
- Information om hur du exporterar en BACPAC från en SQL Server-databas finns i [Exportera ett data skikts program](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Information om hur du migrerar en databas med hjälp av tjänsten data migration finns i [Migrera från SQL Server till Azure SQL Database offline med DMS](../../dms/tutorial-sql-server-to-azure-sql.md).
- Om du exporterar från SQL Server som en inledning för migrering till Azure SQL Database, se [Migrera en SQL Server databas till Azure SQL Database](migrate-to-database-from-sql-server.md).
- Information om hur du hanterar och delar lagrings nycklar och signaturer för delad åtkomst på ett säkert sätt finns i [Azure Storage Security guide](../../storage/blobs/security-recommendations.md).