---
title: Skapa och uppdatera statistik för tabeller
description: Rekommendationer och exempel för att skapa och uppdatera frågor om optimering av tabeller i en dedikerad SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3ade41c51cbb8065734e8957cfc8b9f0c22b2df3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673374"
---
# <a name="table-statistics-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Tabell statistik för dedikerad SQL-pool i Azure Synapse Analytics

I den här artikeln hittar du rekommendationer och exempel för att skapa och uppdatera statistik för att optimera frågor i tabeller i dedikerad SQL-pool.

## <a name="why-use-statistics"></a>Varför använda statistik

Den mer dedikerade SQL-poolen vet om dina data, desto snabbare kan den köra frågor mot den. När du har läst in data i en dedikerad SQL-pool är insamling av statistik för dina data ett av de viktigaste sakerna du kan göra för att optimera dina frågor.

Den dedikerade SQL-poolens fråga optimering är en kostnads baserad optimering. Den Jämför kostnaden för olika fråge planer och väljer sedan planen med den lägsta kostnaden. I de flesta fall väljer den den plan som ska köras snabbast.

Om optimeringen t. ex. beräknar att det datum då frågan filtreras på kommer att returnera en rad kommer den att välja en plan. Om den beräknar att det valda datumet returnerar 1 000 000 rader, returneras ett annat schema.

## <a name="automatic-creation-of-statistic"></a>Automatisk generering av statistik

När alternativet databas AUTO_CREATE_STATISTICS är på analyserar dedicerad SQL-pool inkommande användar frågor för saknad statistik.

Om statistik saknas skapar Query Optimering statistik för enskilda kolumner i frågeuttrycket eller kopplings villkor för att förbättra kardinalitet uppskattningar för frågeplan.

> [!NOTE]
> Automatisk generering av statistik är för närvarande aktiverat som standard.

Du kan kontrol lera om din dedikerade SQL-pool har AUTO_CREATE_STATISTICS konfigurerad genom att köra följande kommando:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Om din dedikerade SQL-pool inte har AUTO_CREATE_STATISTICS konfigurerad rekommenderar vi att du aktiverar den här egenskapen genom att köra följande kommando:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Dessa uttryck utlöser automatisk skapande av statistik:

- SELECT
- INFOGA-VÄLJ
- CTAS
- UPDATE
- DELETE
- FÖRKLARA när den innehåller en koppling eller förekomsten av ett predikat identifieras

> [!NOTE]
> Automatisk generering av statistik skapas inte för temporära eller externa tabeller.

Automatisk generering av statistik görs synkront så att du kan få en försämrad frågeresultat om kolumnerna saknar statistik. Hur lång tid det tar att skapa statistik för en enskild kolumn beror på tabellens storlek.

För att undvika mätbar prestanda försämring bör du se till att statistik har skapats först genom att köra benchmark-arbetsbelastningen innan du profileringen av systemet.

> [!NOTE]
> När du skapar statistik loggas [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) under en annan användar kontext.

När automatisk statistik skapas, kommer de att ha formen: _WA_Sys_<8 siffer kolumn-ID i hex>_<8 siffror tabell-ID i hex>. Du kan visa statistik som redan har skapats genom att köra [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) -kommandot:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Table_name är namnet på den tabell som innehåller den statistik som ska visas. Den här tabellen kan inte vara en extern tabell. Målet är namnet på mål indexet, statistiken eller kolumnen som statistik information ska visas för.

## <a name="update-statistics"></a>Uppdatera statistik

Ett bra tips är att uppdatera statistik för datum kolumner varje dag då nya datum läggs till. Varje gången nya rader läses in i den dedikerade SQL-poolen läggs nya inläsnings datum eller transaktions datum till. Dessa tillägg ändrar data distributionen och gör statistiken inaktuell.

Statistik för en land/region-kolumn i en kund tabell kanske aldrig behöver uppdateras eftersom fördelning av värden normalt inte ändras. Förutsatt att fördelningen är konstant mellan kunderna, kommer nya rader i tabell variationen inte att ändra data distributionen.

Men om din dedikerade SQL-pool endast innehåller ett land/region och du hämtar data från ett nytt land/region, vilket resulterar i att data från flera länder/regioner lagras, måste du uppdatera statistik i kolumnen land/region.

Följande är rekommendationer om uppdaterings statistik:

|||
|-|-|
| **Frekvens för statistik uppdateringar**  | Försiktigt: varje dag </br> När du har läst in eller omvandlat dina data |
| **Sampling** |  Färre än 1 000 000 000 rader, Använd standard sampling (20 procent). </br> Med fler än 1 000 000 000 rader använder du samplingen av två procent. |

En av de första frågorna för att fråga när du felsöker en fråga är **"är statistiken uppdaterad?"**

Den här frågan är inte en som kan besvaras av data åldern. Ett uppdaterat statistik objekt kan vara gammalt om ingen material ändring har gjorts i underliggande data. När antalet rader har ändrats huvudsakligen, eller om det finns en väsentlig ändring i fördelning av värden för en kolumn, *är det dags* att uppdatera statistik. 

Det finns ingen dynamisk hanterings vy för att avgöra om data i tabellen har ändrats sedan den senaste tids statistiken uppdaterades.  Följande två frågor kan hjälpa dig att avgöra om din statistik är inaktuell.

**Fråga 1:**  Ta reda på skillnaden mellan antalet rader från statistiken (**stats_row_count**) och det faktiska antalet rader (**actual_row_count**). 

```sql
select 
objIdsWithStats.[object_id], 
actualRowCounts.[schema], 
actualRowCounts.logical_table_name, 
statsRowCounts.stats_row_count, 
actualRowCounts.actual_row_count,
row_count_difference = CASE
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN actualRowCounts.actual_row_count - statsRowCounts.stats_row_count
    ELSE statsRowCounts.stats_row_count - actualRowCounts.actual_row_count
END,
percent_deviation_from_actual = CASE
    WHEN actualRowCounts.actual_row_count = 0 THEN statsRowCounts.stats_row_count
    WHEN statsRowCounts.stats_row_count = 0 THEN actualRowCounts.actual_row_count
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (actualRowCounts.actual_row_count - statsRowCounts.stats_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
    ELSE CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (statsRowCounts.stats_row_count - actualRowCounts.actual_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
END
from
(
    select distinct object_id from sys.stats where stats_id > 1
) objIdsWithStats
left join
(
    select object_id, sum(rows) as stats_row_count from sys.partitions group by object_id
) statsRowCounts
on objIdsWithStats.object_id = statsRowCounts.object_id 
left join
(
    SELECT sm.name [schema] ,
    tb.name logical_table_name ,
    tb.object_id object_id ,
    SUM(rg.row_count) actual_row_count
    FROM sys.schemas sm
    INNER JOIN sys.tables tb ON sm.schema_id = tb.schema_id
    INNER JOIN sys.pdw_table_mappings mp ON tb.object_id = mp.object_id
    INNER JOIN sys.pdw_nodes_tables nt ON nt.name = mp.physical_name
    INNER JOIN sys.dm_pdw_nodes_db_partition_stats rg
    ON rg.object_id = nt.object_id
    AND rg.pdw_node_id = nt.pdw_node_id
    AND rg.distribution_id = nt.distribution_id
    WHERE 1 = 1
    GROUP BY sm.name, tb.name, tb.object_id
) actualRowCounts
on objIdsWithStats.object_id = actualRowCounts.object_id

```

**Fråga 2:** Ta reda på din statistiks ålder genom att kontrol lera den senaste gången din statistik uppdaterades i varje tabell. 

> [!NOTE]
> Om det finns en väsentlig förändring i fördelningen av värden för en kolumn bör du uppdatera statistiken, oavsett när de uppdaterades senast.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

**Datum kolumner** i en dedikerad SQL-pool, till exempel, behöver ofta ofta förekommande statistik uppdateringar. Varje gången nya rader läses in i den dedikerade SQL-poolen läggs nya inläsnings datum eller transaktions datum till. Dessa tillägg ändrar data distributionen och gör statistiken inaktuell.

Det kan hända att statistik i en köns kolumn i en kund tabell aldrig behöver uppdateras. Förutsatt att fördelningen är konstant mellan kunderna, kommer nya rader i tabell variationen inte att ändra data distributionen.

Om din dedikerade SQL-pool bara innehåller en kön och ett nytt krav resulterar i flera kön måste du uppdatera statistiken i kön kön.

Mer information finns i allmän vägledning för [statistik](/sql/relational-databases/statistics/statistics?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="implementing-statistics-management"></a>Implementera statistik hantering

Det är ofta en bra idé att utöka data inläsnings processen så att statistiken uppdateras i slutet av belastningen för att undvika/minimera blockeringen eller resurs konkurrens mellan samtidiga frågor.  

Data inläsningen är när tabeller ofta ändrar storlek och/eller deras fördelning av värden. Data inläsning är en logisk plats för att implementera vissa hanterings processer.

Följande GUID-principer finns för att uppdatera din statistik:

- Se till att varje inläst tabell har minst ett uppdaterat statistik objekt. Detta uppdaterar informationen i tabell storlek (antal rader och sid antal) som en del av statistiken uppdatera.
- Fokusera på kolumner som ingår i JOIN-, GROUP BY-, ORDER BY-och DISTINCT-satser.
- Överväg att uppdatera "ascending Key"-kolumner som transaktions datum oftare, eftersom dessa värden inte kommer att ingå i statistik histogrammet.
- Överväg att uppdatera statiska distributions kolumner mindre ofta.
- Kom ihåg att varje statistik objekt uppdateras i följd. Att bara implementera `UPDATE STATISTICS <TABLE_NAME>` är inte alltid idealiskt, särskilt för breda tabeller med massor av statistik objekt.

Mer information finns i [beräkning av kardinalitet](/sql/relational-databases/performance/cardinality-estimation-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="examples-create-statistics"></a>Exempel: skapa statistik

I de här exemplen visas hur du använder olika alternativ för att skapa statistik. De alternativ som du använder för varje kolumn beror på egenskaperna för dina data och hur kolumnen kommer att användas i frågor.

### <a name="create-single-column-statistics-with-default-options"></a>Skapa en statistik med en kolumn med standard alternativ

Om du vill skapa statistik för en kolumn anger du ett namn för statistik objekt och kolumn namn.

I den här syntaxen används alla standard alternativ. Som standard samplas **20 procent** av tabellen när du skapar statistik.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Exempel:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Skapa en statistik med en kolumn genom att undersöka varje rad

Standard samplings frekvensen på 20 procent räcker för de flesta situationer. Du kan dock justera samplings frekvensen.

Använd följande syntax för att sampla den fullständiga tabellen:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Exempel:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Skapa en statistik med en kolumn genom att ange prov storleken

Du kan också ange exempel storleken i procent:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Skapa statistik med en kolumn på bara några av raderna

Du kan också skapa statistik på en del av raderna i tabellen. Detta kallas för en filtrerad statistik.

Du kan till exempel använda filtrerad statistik när du planerar att fråga en speciell partition i en stor partitionerad tabell. Genom att skapa statistik enbart på partitionsalternativ förbättras noggrannheten i statistiken, och därför förbättras frågans prestanda.

I det här exemplet skapas statistik för ett värde intervall. Värdena kan enkelt definieras för att matcha värde intervallet i en partition.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> För att fråga optimeringen ska överväga att använda filtrerad statistik när den väljer den distribuerade frågan måste frågan passa inuti definitionen av statistik objekt. I föregående exempel måste frågans WHERE-sats ange col1-värden mellan 2000101 och 20001231.

### <a name="create-single-column-statistics-with-all-the-options"></a>Skapa statistik med en kolumn med alla alternativ

Du kan också kombinera alternativen tillsammans. I följande exempel skapas ett filtrerat statistik objekt med en anpassad exempel storlek:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Fullständig referens finns i [skapa statistik](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="create-multi-column-statistics"></a>Skapa statistik för flera kolumner

Använd föregående exempel, men ange fler kolumner för att skapa ett statistik objekt med flera kolumner.

> [!NOTE]
> Histogrammet, som används för att uppskatta antalet rader i frågeresultatet, är bara tillgängligt för den första kolumnen som anges i statistik objekt definitionen.

I det här exemplet är histogrammet i *produkt \_ kategorin*. Statistik över kolumner beräknas för *produkt \_ kategori* och *produkt \_ sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Eftersom det finns en korrelation mellan *produkt \_ kategori* och *produkt \_ under \_ kategori*, kan ett statistik objekt med flera kolumner vara användbart om dessa kolumner används samtidigt.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Skapa statistik för alla kolumner i en tabell

Ett sätt att skapa statistik är att utfärda kommandon för att skapa statistik när tabellen har skapats:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-sql-pool"></a>Använd en lagrad procedur för att skapa statistik för alla kolumner i en SQL-pool

Den dedikerade SQL-poolen har ingen lagrad system procedur som motsvarar sp_create_stats i SQL Server. Den här lagrade proceduren skapar ett enda kolumn statistik objekt på varje kolumn i en SQL-pool som inte redan har statistik.

I följande exempel får du hjälp att komma igång med SQL-poolens design. Det är kostnads fritt att anpassa den efter dina behov.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Om du vill skapa statistik för alla kolumner i tabellen med hjälp av standardvärdena kör du den lagrade proceduren.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Anropa den här proceduren om du vill skapa statistik för alla kolumner i tabellen med hjälp av en fullscan.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Om du vill skapa exempel statistik för alla kolumner i tabellen anger du 3 och exemplet procent. I den här proceduren används 20 procents samplings frekvens.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

## <a name="examples-update-statistics"></a>Exempel: uppdatera statistik

Om du vill uppdatera statistiken kan du:

- Uppdatera ett statistik objekt. Ange namnet på det statistik objekt som du vill uppdatera.
- Uppdatera alla statistik objekt i en tabell. Ange namnet på tabellen i stället för ett enskilt statistik objekt.

### <a name="update-one-specific-statistics-object"></a>Uppdatera ett enskilt statistik objekt

Använd följande syntax för att uppdatera ett enskilt statistik objekt:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Exempel:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Genom att uppdatera vissa statistik objekt kan du minimera den tid och de resurser som krävs för att hantera statistik. Detta kräver en del tanke på att välja de bästa statistik objekt som ska uppdateras.

### <a name="update-all-statistics-on-a-table"></a>Uppdatera all statistik för en tabell

En enkel metod för att uppdatera alla statistik objekt i en tabell är:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Exempel:

```sql
UPDATE STATISTICS dbo.table1;
```

UPDATE STATISTICS-instruktionen är enkel att använda. Kom bara ihåg att den uppdaterar *all* statistik i tabellen och kan därför utföra mer arbete än vad som behövs. Om prestanda inte är ett problem är det här det enklaste och mest kompletta sättet att garantera att statistiken är aktuell.

> [!NOTE]
> När du uppdaterar all statistik i en tabell gör en dedikerad SQL-pool en sökning för att sampla tabellen för varje statistik objekt. Om tabellen är stor och har många kolumner och många statistik, kan det vara mer effektivt att uppdatera individuell statistik utifrån behov.

En implementering av en `UPDATE STATISTICS` procedur finns i [temporära tabeller](sql-data-warehouse-tables-temporary.md). Implementerings metoden skiljer sig något från föregående `CREATE STATISTICS` procedur, men resultatet är detsamma.

Fullständig syntax finns i [Uppdatera statistik](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="statistics-metadata"></a>Metadata för statistik

Det finns flera systemvyer och funktioner som du kan använda för att hitta information om statistik. Du kan till exempel se om ett statistik objekt kan vara inaktuellt genom att använda funktionen statistik datum för att se när statistiken senast skapades eller uppdaterades.

### <a name="catalog-views-for-statistics"></a>Katalogvyer för statistik

Dessa system visningar innehåller information om statistik:

| Katalogvy | Beskrivning |
|:--- |:--- |
| [sys. columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |En rad för varje kolumn. |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |En rad för varje objekt i databasen. |
| [sys. schema](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |En rad för varje schema i databasen. |
| [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |En rad för varje statistik objekt. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |En rad för varje kolumn i objektet statistik. Länkar tillbaka till sys. columns. |
| [sys. tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |En rad för varje tabell (inklusive externa tabeller). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |En rad för varje datatyp. |

### <a name="system-functions-for-statistics"></a>System funktioner för statistik

Dessa system funktioner är användbara när du arbetar med statistik:

| System funktion | Beskrivning |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Datum då statistik objekt senast uppdaterades. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Sammanfattnings nivå och detaljerad information om distributionen av värden som förstås av statistik objekt. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Kombinera statistik kolumner och funktioner i en vy

I den här vyn visas kolumner som relaterar till statistik och resultat från STATS_DATE ()-funktionen tillsammans.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS ()-exempel

DBCC SHOW_STATISTICS () visar data som lagras i ett statistik objekt. Dessa data ingår i tre delar:

- Huvud
- Densitets vektor
- Histogram

Metadata för sidhuvudet om statistiken. Histogrammet visar fördelningen av värden i den första nyckel kolumnen i statistik-objektet. Täthets vektorn mäter en jämförelse mellan kolumner.

> [!NOTE]
> Dedikerad SQL-pool beräknar kardinalitet uppskattningar med alla data i statistik-objektet.

### <a name="show-header-density-and-histogram"></a>Visa rubrik, densitet och histogram

Det här enkla exemplet visar alla tre delarna i ett statistik objekt:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Exempel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Visa en eller flera delar av DBCC SHOW_STATISTICS ()

Om du bara vill visa vissa delar använder du `WITH` satsen och anger vilka delar du vill se:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Exempel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>Skillnader mellan DBCC SHOW_STATISTICS ()

DBCC SHOW_STATISTICS () är mer strikt implementerad i dedikerad SQL-pool jämfört med SQL Server:

- Det finns inte stöd för icke-dokumenterade funktioner.
- Det går inte att använda Stats_stream.
- Det går inte att ansluta resultat för vissa del mängder av statistik data. STAT_HEADER till exempel koppla DENSITY_VECTOR.
- NO_INFOMSGS kan inte ställas in för meddelande under tryckning.
- Hakparenteser runt statistik namn kan inte användas.
- Det går inte att använda kolumn namn för att identifiera statistik objekt.
- Anpassad fel 2767 stöds inte.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan förbättra prestanda för frågor finns i [övervaka din arbets belastning](sql-data-warehouse-manage-monitor.md)
