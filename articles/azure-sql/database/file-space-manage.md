---
title: Azure SQL Database fil utrymmes hantering
description: Den här sidan beskriver hur du hanterar fil utrymme med enkla databaser i Azure SQL Database och innehåller kod exempel för hur du avgör om du behöver krympa en enskild databas eller en databas i poolen samt hur du utför en databas krympnings åtgärd.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, sstein
ms.date: 12/22/2020
ms.openlocfilehash: 7bb754b892715adffc6ead99f3d866f9f9d8af9b
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99096499"
---
# <a name="manage-file-space-for-databases-in-azure-sql-database"></a>Hantera fil utrymme för databaser i Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här artikeln beskrivs olika typer av lagrings utrymme för databaser i Azure SQL Database och steg som kan vidtas när det allokerade fil utrymmet måste hanteras explicit.

> [!NOTE]
> Den här artikeln gäller inte för Azure SQL-hanterade instanser.

## <a name="overview"></a>Översikt

Med Azure SQL Database finns det arbets belastnings mönster där allokeringen av underliggande datafiler för databaser kan bli större än mängden använda data sidor. Den här situationen kan uppstå när mängden utnyttjat utrymme ökar och data därefter raderas. Orsaken till detta beror på att det allokerade fil utrymmet inte frigörs automatiskt när data tas bort.

I följande scenarier kan det vara nödvändigt att övervaka användningen av filutrymmet och att krympa datafiler:

- Tillåt datatillväxt i en elastisk pool när filutrymmet som tilldelats dess databaser når poolens maxstorlek.
- Tillåt att maxstorleken för en enskild databas eller elastisk pool minskas.
- Tillåt att en enskild databas eller elastisk pool ändras till en annan tjänstnivå eller prestandanivå med en mindre maxstorlek.

### <a name="monitoring-file-space-usage"></a>Övervaka fil utrymmes användning

De flesta mått för lagrings utrymme som visas i Azure Portal och följande API: er mäter endast storleken på data sidor som används:

- API: er för Azure Resource Manager baserade mått, inklusive PowerShell [Get-Metrics](/powershell/module/az.monitor/get-azmetric)
- T-SQL: [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

Följande API: er mäter dock också storleken på utrymmet som allokerats för databaser och elastiska pooler:

- T-SQL:  [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Krympa datafiler

Azure SQL Database krymper inte automatiskt datafiler för att frigöra oanvänt allokerat utrymme på grund av den potentiella påverkan av databas prestanda.  Kunder kan dock krympa datafiler via självbetjäning när de väljer genom att följa stegen som beskrivs i [frigör oanvänt allokerat utrymme](#reclaim-unused-allocated-space).

> [!NOTE]
> Till skillnad från datafiler krymper Azure SQL Database automatiskt loggfiler eftersom åtgärden inte påverkar databasens prestanda.

## <a name="understanding-types-of-storage-space-for-a-database"></a>Förstå typer av lagrings utrymme för en databas

Det är viktigt att förstå följande lagrings utrymmes mängder för att hantera fil utrymmet för en databas.

|Databas antal|Definition|Kommentarer|
|---|---|---|
|**Använt data utrymme**|Mängden utrymme som används för att lagra databas data på 8 KB-sidor.|Normalt ökar utrymmet som används (minskar) vid infogningar (rader). I vissa fall ändras inte utrymmet som används vid infogningar eller borttagningar beroende på mängden och mönstret för data som ingår i åtgärden och eventuell fragmentering. Att till exempel ta bort en rad från alla data sidor minskar inte nödvändigt vis det utrymme som används.|
|**Allokerat data utrymme**|Mängden formaterat fil utrymme som är tillgängligt för lagring av databas data.|Mängden utrymme som allokeras växer automatiskt, men minskar aldrig efter borttagningarna. Det här beteendet säkerställer att framtida infogningar är snabbare eftersom utrymmet inte behöver formateras om.|
|**Allokerat data utrymme men oanvänt**|Skillnaden mellan mängden allokerat data utrymme och använt data utrymme.|Den här kvantiteten representerar den maximala mängden ledigt utrymme som kan frigöras genom att krympa datafiler för databasen.|
|**Maximal datastorlek**|Den maximala mängd utrymme som kan användas för att lagra databas data.|Mängden allokerat data utrymme kan inte växa utöver data Max storleken.|

Följande diagram illustrerar förhållandet mellan de olika typerna av lagrings utrymme för en databas.

![lagrings utrymmes typer och-relationer](./media/file-space-manage/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Fråga en enskild databas om lagrings utrymmes information

Följande frågor kan användas för att fastställa antalet lagrings utrymmen för en enskild databas.  

### <a name="database-data-space-used"></a>Använt databas data utrymme

Ändra följande fråga för att returnera mängden databas data utrymme som används.  Enheter i frågeresultatet är i MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC;
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Allokerat och oanvänt utrymme för databas data utrymme

Använd följande fråga för att returnera mängden allokerat utrymme för databas data och mängden oanvänt utrymme som allokerats.  Enheter i frågeresultatet är i MB.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB,
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS';
```

### <a name="database-data-max-size"></a>Maximal storlek för databas data

Ändra följande fråga för att returnera den maximala storleken för databas data.  Enheter i frågeresultatet anges i byte.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes;
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Förstå typer av lagrings utrymme för en elastisk pool

Det är viktigt att förstå följande lagrings utrymmes mängder för att hantera fil utrymmet för en elastisk pool.

|Kvantitet för elastisk pool|Definition|Kommentarer|
|---|---|---|
|**Använt data utrymme**|En summering av data utrymmet som används av alla databaser i den elastiska poolen.||
|**Allokerat data utrymme**|En summering av det data utrymme som allokerats av alla databaser i den elastiska poolen.||
|**Allokerat data utrymme men oanvänt**|Skillnaden mellan mängden allokerat data utrymme och data utrymme som används av alla databaser i den elastiska poolen.|Den här kvantiteten representerar den maximala mängden utrymme som allokerats för den elastiska poolen som kan frigöras genom att krympa datafiler för databasen.|
|**Maximal datastorlek**|Den maximala mängden data utrymme som kan användas av den elastiska poolen för alla dess databaser.|Utrymmet som allokerats för den elastiska poolen får inte överskrida den högsta tillåtna storleken för elastisk pool.  Om det här tillståndet inträffar kan allokerat utrymme som inte används frigöras genom att krympa datafiler för databasen.|

> [!NOTE]
> Fel meddelandet "den elastiska poolen har nått sin lagrings gräns" anger att databas objekt har allokerats tillräckligt med utrymme för att uppfylla lagrings gränsen för elastisk pool, men det kan finnas oanvänt utrymme i tilldelningen av data utrymme. Överväg att öka den elastiska poolens lagrings gräns eller som en kortsiktig lösning, vilket frigör data utrymme med hjälp av avsnittet [**återta oanvänt allokerat utrymme**](#reclaim-unused-allocated-space) nedan. Du bör också vara medveten om den potentiella negativa prestanda effekten vid krympning av databasfiler, se avsnittet [**Återskapa index**](#rebuild-indexes) nedan.

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Fråga en elastisk pool för lagrings utrymmes information

Följande frågor kan användas för att fastställa det tillgängliga lagringsutrymmet för en elastisk pool.  

### <a name="elastic-pool-data-space-used"></a>Använt data utrymme för elastisk pool

Ändra följande fråga för att returnera mängden elastiskt data utrymme för elastisk pool.  Enheter i frågeresultatet är i MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC;
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Allokerat data utrymme för elastisk pool och oanvänt allokerat utrymme

Ändra följande exempel för att returnera en tabell som visar utrymmet allokerat och oanvänt allokerat utrymme för varje databas i en elastisk pool. Tabellen beställer databaser från de databaserna med störst mängd oanvänt allokerat utrymme till minsta mängd oanvänt allokerat utrymme.  Enheter i frågeresultatet är i MB.  

Frågeresultatet för att bestämma hur mycket utrymme som allokeras för varje databas i poolen kan läggas till tillsammans för att fastställa det totala utrymmet som allokerats för den elastiska poolen. Allokerat utrymme för elastisk pool får inte överskrida den maximala storleken för elastisk pool.  

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. AzureRM-modulen kommer att fortsätta att ta emot fel korrigeringar fram till minst december 2020. Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell AZ-modulen](/powershell/azure/new-azureps-module-az).

PowerShell-skriptet kräver SQL Server PowerShell-modul – se [Ladda ned PowerShell-modulen](/sql/powershell/download-sql-server-ps-module) för att installera.

```powershell
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$poolName = "<poolName>"
$userName = "<userName>"
$password = "<password>"

# get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# for each database in the elastic pool, get space allocated in MB and space allocated unused in MB
foreach ($database in $databasesInPool) {
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn -Database $database.DatabaseName `
            -Username $userName -Password $password -Query $sqlCommand)
}

# display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort -Property DatabaseDataSpaceAllocatedUnusedInMB -Descending | Format-Table
```

Följande skärm bild är ett exempel på utdata från skriptet:

![exempel på allokerat utrymme för elastisk pool och oanvänt allokerat utrymme](./media/file-space-manage/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Maximal storlek för elastisk pool

Ändra följande T-SQL-fråga för att returnera den senaste registrerade elastiska poolens data Max storlek.  Enheter i frågeresultatet är i MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC;
```

## <a name="reclaim-unused-allocated-space"></a>Frigör oanvänt allokerat utrymme

> [!NOTE]
> Krympnings kommandon påverkar databas prestanda vid körning, och om möjligt bör köras under perioder med låg belastning.

### <a name="dbcc-shrink"></a>DBCC-krympning

När databaser har identifierats för att frigöra oanvänt allokerat utrymme ändrar du namnet på databasen i följande kommando för att krympa datafilerna för varje databas.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1');
```

Krympnings kommandon påverkar databas prestanda vid körning, och om möjligt bör köras under perioder med låg belastning.  

Du bör också vara medveten om den potentiella negativa prestanda effekten vid krympning av databasfiler, se avsnittet [**Återskapa index**](#rebuild-indexes) nedan.

Mer information om det här kommandot finns i [SHRINKDATABASE](/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

### <a name="auto-shrink"></a>Automatisk krympning

Du kan också aktivera automatisk krympning för en databas.  Automatisk krympning minskar fil hanterings komplexiteten och är mindre påverkan på databas prestanda än `SHRINKDATABASE` eller `SHRINKFILE` .  Automatisk krympning kan vara särskilt användbart för att hantera elastiska pooler med många databaser.  Den automatiska krympningen kan dock vara mindre effektiv när du frigör fil utrymme än `SHRINKDATABASE` och `SHRINKFILE` .
Som standard inaktive ras automatisk krympning som rekommenderas för de flesta databaser. Mer information finns i [överväganden för AUTO_SHRINK](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-auto_shrink).

Om du vill aktivera automatisk krympning ändrar du namnet på databasen i följande kommando.

```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON;
```

Mer information om det här kommandot finns i alternativ för [databas uppsättning](/sql/t-sql/statements/alter-database-transact-sql-set-options) .

### <a name="rebuild-indexes"></a>Återskapa index

När datafilerna i databasen krymps kan index bli fragmenterade och förlora prestanda optimerings effektivitet. Om prestanda försämringen inträffar bör du överväga att återskapa databas index. Mer information om fragmentering och återuppbyggnad av index finns i [omorganisera och återskapa index](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Nästa steg

- Information om Max storlek för databas finns i:
  - [Azure SQL Database vCore-baserade inköps modell gränser för en enskild databas](resource-limits-vcore-single-databases.md)
  - [Resursbegränsningar för enskilda databaser som använder den DTU-baserade inköpsmodellen](resource-limits-dtu-single-databases.md)
  - [Azure SQL Database vCore-baserade inköps modell gränser för elastiska pooler](resource-limits-vcore-elastic-pools.md)
  - [Resurs begränsningar för elastiska pooler med hjälp av den DTU-baserade inköps modellen](resource-limits-dtu-elastic-pools.md)
- Mer information om `SHRINKDATABASE` kommandot finns i [SHRINKDATABASE](/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).
- Mer information om fragmentering och återuppbyggnad av index finns i [omorganisera och återskapa index](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).