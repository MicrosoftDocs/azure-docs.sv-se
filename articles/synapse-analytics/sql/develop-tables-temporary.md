---
title: Använda temporära tabeller i Synapse SQL
description: Grundläggande rikt linjer för att använda temporära tabeller i Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 06faa1da71331c299245a93af96166880e7732de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96451783"
---
# <a name="temporary-tables-in-synapse-sql"></a>Temporära tabeller i Synapse SQL

Den här artikeln innehåller grundläggande information om hur du använder temporära tabeller och visar principerna för temporära tabeller på sessionsläge i Synapse SQL. 

Både den dedikerade SQL-poolen och resurserna för Server lös SQL-pool kan använda temporära tabeller. SQL-poolen utan server har begränsningar som beskrivs i slutet av den här artikeln. 

## <a name="temporary-tables"></a>Temporära tabeller

Temporära tabeller är användbara vid bearbetning av data, särskilt under omvandling där de mellanliggande resultaten är tillfälliga. Med Synapse SQL finns temporära tabeller på sessions nivå.  De är bara synliga för den session där de skapades. De tas då bort automatiskt när sessionen loggar ut. 

## <a name="temporary-tables-in-dedicated-sql-pool"></a>Temporära tabeller i dedikerad SQL-pool

I den dedikerade SQL-poolen ger temporära tabeller en prestanda förmån eftersom deras resultat skrivs till lokala platser i stället för Fjärrlagring.

### <a name="create-a-temporary-table"></a>Skapa en tillfällig tabell

Temporära tabeller skapas genom att prefixet för ditt tabell namn används `#` .  Exempel:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

Temporära tabeller kan också skapas med en `CTAS` med exakt samma metod:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
```

> [!NOTE]
> `CTAS` är ett kraftfullt kommando och har den extra fördelen att vara effektiv i användningen av transaktions logg utrymme. 
> 
> 

### <a name="drop-temporary-tables"></a>Släpp temporära tabeller

Inga temporära tabeller bör finnas när en ny session skapas.  Men om du anropar samma lagrade procedur som skapar en tillfällig med samma namn, så att du kan se till att dina `CREATE TABLE` instruktioner lyckas, kan du använda en enkel för hands kontroll med  `DROP` : 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

För att koda konsekvens är det en bra idé att använda det här mönstret för både tabeller och temporära tabeller.  Det är också en bra idé att använda `DROP TABLE` för att ta bort temporära tabeller när du är klar med dem.  

Vid utveckling av lagrade procedurer är det vanligt att se Drop-kommandon som samlats ihop i slutet av en procedur för att se till att dessa objekt rensas.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularize-code"></a>Modularize-kod

Temporära tabeller kan användas var som helst i en användarsession. Den här funktionen kan sedan utnyttjas för att hjälpa dig att modularize program koden.  För att demonstrera, genererar följande lagrade procedur DDL för att uppdatera all statistik i databasen efter statistik namn:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

I det här skedet är den enda åtgärd som har inträffat skapandet av en lagrad procedur som genererar #stats_ddl temporära tabellen.  Den lagrade proceduren släpper #stats_ddl om den redan finns. Den här minskningen ser till att den inte fungerar om den körs mer än en gång i en session.  

Eftersom det inte finns någon `DROP TABLE` i slutet av den lagrade proceduren, är den skapade tabellen kvar och kan läsas utanför den lagrade proceduren när den lagrade proceduren har slutförts.  

Till skillnad från andra SQL Server-databaser låter Synapse SQL dig använda den temporära tabellen utanför proceduren som skapade den.  De temporära tabeller som skapas via dedikerad SQL-pool kan användas **var som helst** i sessionen. Därför har du mer modulär och hanterbar kod, som visas i exemplet nedan:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

### <a name="temporary-table-limitations"></a>Tillfälligt tabell begränsningar

Dedikerad SQL-pool har några implementerings begränsningar för temporära tabeller:

- Endast tillfälliga tabeller med sessions definitions område stöds.  Globala temporära tabeller stöds inte.
- Det går inte att skapa vyer för temporära tabeller.
- Temporära tabeller kan bara skapas med hash-eller resursallokering-distribution.  Replikerad temporär tabell distribution stöds inte. 

## <a name="temporary-tables-in-serverless-sql-pool"></a>Temporära tabeller i SQL-poolen utan Server

Temporära tabeller i SQL-poolen utan Server stöds men deras användning är begränsad. De kan inte användas i frågor som målfil. 

Du kan till exempel inte ansluta till en temporär tabell med data från filer i lagrings utrymmet. Antalet temporära tabeller är begränsat till 100 och den totala storleken är begränsad till 100 MB.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar tabeller finns i avsnittet [utforma tabeller med hjälp av artikeln SYNAPSE SQL-resurser](develop-tables-overview.md) .

