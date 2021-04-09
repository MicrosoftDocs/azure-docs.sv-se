---
title: Partitionerings tabeller
description: Rekommendationer och exempel för att använda Table-partitioner i dedikerad SQL-pool
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 8a59c24100b433719ccfd3a9ea1b6a676695d381
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98673442"
---
# <a name="partitioning-tables-in-dedicated-sql-pool"></a>Partitionerings tabeller i dedikerad SQL-pool

Rekommendationer och exempel för att använda Table-partitioner i dedikerad SQL-pool.

## <a name="what-are-table-partitions"></a>Vad är Table-partitioner?

Med Table partitions kan du dela upp data i mindre grupper av data. I de flesta fall skapas Table partitions i en datum kolumn. Partitionering stöds i alla dedikerade tabell typer för SQL-pooler. inklusive grupperat columnstore, grupperat index och heap. Partitionering stöds också på alla distributions typer, inklusive både hash-och resursallokering-distribution.  

Partitionering kan dra nytta av data underhåll och frågans prestanda. Vare sig det gäller både eller bara en är beroende av hur data läses in och om samma kolumn kan användas för båda syfte, eftersom partitionering bara kan göras på en kolumn.

### <a name="benefits-to-loads"></a>Fördelar med att läsa in

Den främsta fördelen med partitionering i dedikerad SQL-pool är att förbättra effektiviteten och prestandan vid inläsning av data genom att använda borttagning av partition, växling och sammanslagning. I de flesta fall är data partitionerade i en datum kolumn som är nära knutna till den ordning som data läses in i SQL-poolen. En av de största fördelarna med att använda partitioner för att underhålla data är att undvika transaktions loggning. Även om du helt enkelt infogar, uppdaterar eller tar bort data är det enklaste sättet, med lite tanke och ansträngning, att använda partitionering under inläsnings processen och förbättra prestanda avsevärt.

Partition växling kan användas för att snabbt ta bort eller ersätta en del av en tabell.  En försäljnings fakta tabell kan till exempel innehålla endast data för de senaste 36 månaderna. I slutet av varje månad tas den äldsta månaden av försäljnings data bort från tabellen.  Dessa data kan tas bort med hjälp av en Delete-instruktion för att ta bort data för den äldsta månaden. 

Att ta bort en stor mängd data rad för rad med en Delete-instruktion kan dock ta för lång tid, samt skapa risken för stora transaktioner som tar lång tid att återställa om något går fel. En mer optimal metod är att släppa den äldsta data partitionen. Det kan ta flera sekunder att ta bort enskilda rader och det kan ta flera sekunder att ta bort en hel partition.

### <a name="benefits-to-queries"></a>Fördelar med frågor

Partitionering kan också användas för att förbättra prestanda för frågor. En fråga som tillämpar ett filter på partitionerade data kan begränsa sökningen till endast de kvalificerade partitionerna. Den här filtrerings metoden kan undvika en fullständig tabells ökning och bara genomsöka en mindre delmängd data. Med introduktionen av grupperade columnstore-index, är de stora prestanda fördelarna mindre fördelaktiga, men i vissa fall kan det vara en fördel med frågor. 

Om till exempel försäljnings fakta tabellen är partitionerad i 36 månader med fältet försäljnings datum kan frågor som filtrerar på försäljnings datumet hoppa över sökning i partitioner som inte matchar filtret.

## <a name="sizing-partitions"></a>Storleksändra partitioner

Medan partitionering kan användas för att förbättra prestanda för vissa scenarier, kan en tabell med **för många** partitioner försämra prestanda under vissa omständigheter.  Dessa problem gäller särskilt för grupperade columnstore-tabeller. 

För att partitionering ska vara till hjälp är det viktigt att förstå när du ska använda partitionering och antalet partitioner som ska skapas. Det finns ingen fast snabb regel för hur många partitioner som är för många, beroende på dina data och hur många partitioner som du läser in samtidigt. Ett lyckat partitionerings schema har vanligt vis till hundratals partitioner, inte tusentals.

När du skapar partitioner i **grupperade columnstore** -tabeller är det viktigt att fundera över hur många rader som tillhör varje partition. För optimal komprimering och prestanda för grupperade columnstore-tabeller behövs minst 1 000 000 rader per distribution och partition. Innan partitionerna skapas delar den dedikerade SQL-poolen redan varje tabell i 60-distribuerade databaser. 

Alla partitioner som läggs till i en tabell är utöver de distributioner som skapats i bakgrunden. I det här exemplet, om försäljnings fakta tabellen innehöll 36 månader, och eftersom en dedikerad SQL-pool har 60 distributioner, ska försäljnings fakta tabellen innehålla 60 000 000 rader per månad eller 2 100 000 000 rader när alla månader är ifyllda. Om en tabell innehåller färre än det rekommenderade lägsta antalet rader per partition bör du överväga att använda färre partitioner för att öka antalet rader per partition. 

Mer information finns i [indexerings](sql-data-warehouse-tables-index.md) artikeln, som innehåller frågor som kan bedöma kvaliteten på kluster columnstore-index.

## <a name="syntax-differences-from-sql-server"></a>Skillnader i syntaxen från SQL Server

Dedikerad SQL-pool gör det möjligt att definiera partitioner som är enklare än SQL Server. Partitionering och scheman används inte i dedicerade SQL-pooler eftersom de är i SQL Server. I stället behöver du bara identifiera partitionerade kolumner och gränserna. 

Även om syntaxen för partitionering kan skilja sig något från SQL Server, är de grundläggande begreppen desamma. SQL Server och dedikerad SQL-pool stöder en partitions kolumn per tabell som kan ha en intervall partition. Mer information om partitionering finns i [partitionerade tabeller och index](/sql/relational-databases/partitions/partitioned-tables-and-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

I följande exempel används instruktionen [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att partitionera tabellen FactInternetSales i kolumnen OrderDateKey:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Migrera partitionering från SQL Server

Så här migrerar du SQL Server partitions definitioner till dedikerad SQL-pool helt enkelt:

- Eliminera SQL Server [partition schema](/sql/t-sql/statements/create-partition-scheme-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).
- Lägg till [partition funktions](/sql/t-sql/statements/create-partition-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) definitionen i CREATE TABLE.

Om du migrerar en partitionerad tabell från en SQL Server instans kan följande SQL hjälpa dig att ta reda på antalet rader i varje partition. Tänk på att om samma partitionerings kornig het används i en dedikerad SQL-pool, minskar antalet rader per partition med en faktor på 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="partition-switching"></a>Partition växling

Dedikerad SQL-pool stöder partitions delning, sammanslagning och växling. Var och en av dessa funktioner utförs med instruktionen [Alter Table](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

Om du vill byta partitioner mellan två tabeller måste du se till att partitionerna överensstämmer med deras respektive gränser och att tabell definitionerna matchar. Eftersom kontroll begränsningar inte är tillgängliga för att genomdriva värde intervallet i en tabell måste käll tabellen innehålla samma partition gränser som mål tabellen. Om partitionernas gränser inte är samma, kommer partitionsuppsättningen att Miss Miss sen eftersom metadata för partitionen inte kommer att synkroniseras.

### <a name="how-to-split-a-partition-that-contains-data"></a>Dela en partition som innehåller data

Den mest effektiva metoden att dela en partition som redan innehåller data är att använda en `CTAS` instruktion. Om den partitionerade tabellen är ett grupperat columnstore-kluster måste Table-partitionen vara tom innan den kan delas.

I följande exempel skapas en partitionerad columnstore-tabell. Den infogar en rad i varje partition:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);
```

Följande fråga hittar antalet rader genom att använda `sys.partitions` vyn katalog:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Följande delade kommando får ett fel meddelande:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

MSG 35346, Level 15, State 1, rad 44 SPLIT-satsen för ALTER PARTITION-Instruktionen misslyckades eftersom partitionen inte är tom. Det går bara att dela tomma partitioner i när ett columnstore-index finns i tabellen. Överväg att inaktivera columnstore-indexet innan du utfärdar ALTER PARTITION-instruktionen och återskapa sedan columnstore-indexet när ALTER PARTITION har slutförts.

Du kan dock använda `CTAS` för att skapa en ny tabell för att lagra data.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

När partitionens gränser är justerade tillåts en växel. Käll tabellen lämnas då till en tom partition som du sedan kan dela.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Allt som återstår är att justera data till de nya gränserna med `CTAS` och sedan växla tillbaka data till huvud tabellen.

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

När du har slutfört förflyttningen av data är det en bra idé att uppdatera statistiken i mål tabellen. Uppdaterings statistik säkerställer att statistiken korrekt visar den nya distributionen av data i sina respektive partitioner.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Läs in nya data i partitioner som innehåller data i ett steg

Att läsa in data i partitioner med partitionering är ett bekvämt sätt att mellanlagra nya data i en tabell som inte är synlig för användarna.  Det kan vara svårt att utnyttja upptagna system för att hantera den låsning som är kopplad till partition växling.  

Om du vill ta bort befintliga data i en partition måste du `ALTER TABLE` använda dem för att växla ut data.  En annan `ALTER TABLE` krävdes för att växla till nya data.  

I dedicerad SQL-pool `TRUNCATE_TARGET` stöds alternativet i `ALTER TABLE` kommandot.  Med `TRUNCATE_TARGET` `ALTER TABLE` kommandot skriver över befintliga data i partitionen med nya data.  Nedan visas ett exempel som använder `CTAS` för att skapa en ny tabell med befintliga data, infogar nya data och sedan växlar alla data tillbaka till mål tabellen och skriver över befintliga data.

```sql
CREATE TABLE [dbo].[FactInternetSales_NewSales]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

INSERT INTO dbo.FactInternetSales_NewSales
VALUES (1,20000101,2,2,2,2,2,2);

ALTER TABLE dbo.FactInternetSales_NewSales SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2 WITH (TRUNCATE_TARGET = ON);  
```

### <a name="table-partitioning-source-control"></a>Käll kontroll för tabell partitionering

Om du vill undvika din tabell definition från **rusting** i ditt käll kontroll system kan du tänka på följande sätt:

1. Skapa tabellen som en partitionerad tabell men utan partitionerings värden

    ```sql
    CREATE TABLE [dbo].[FactInternetSales]
    (
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
    )
    WITH
    (   CLUSTERED COLUMNSTORE INDEX
    ,   DISTRIBUTION = HASH([ProductKey])
    ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES () )
    )
    ;
    ```

1. `SPLIT` tabellen som en del av distributions processen:

    ```sql
     -- Create a table containing the partition boundaries

    CREATE TABLE #partitions
    WITH
    (
        LOCATION = USER_DB
    ,   DISTRIBUTION = HASH(ptn_no)
    )
    AS
    SELECT  ptn_no
    ,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
    FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
    ) a
    ;

     -- Iterate over the partition boundaries and split the table

    DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
    ,       @i INT = 1                                 --iterator for while loop
    ,       @q NVARCHAR(4000)                          --query
    ,       @p NVARCHAR(20)     = N''                  --partition_number
    ,       @s NVARCHAR(128)    = N'dbo'               --schema
    ,       @t NVARCHAR(128)    = N'FactInternetSales' --table
    ;

    WHILE @i <= @c
    BEGIN
        SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
        SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

        -- PRINT @q;
        EXECUTE sp_executesql @q;
        SET @i+=1;
    END

     -- Code clean-up

    DROP TABLE #partitions;
    ```

Med den här metoden är koden i käll kontrollen fortfarande statisk och partitionernas gränser kan vara dynamiska. utvecklas med SQL-poolen över tid.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar tabeller finns i artiklarna om [tabell översikt](sql-data-warehouse-tables-overview.md).
