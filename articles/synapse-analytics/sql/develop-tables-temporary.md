---
title: Använda temporära tabeller i Synapse SQL
description: Grundläggande vägledning för att använda temporära tabeller i Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 8513df83196b3521a749515c6bb22caad7d255b7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816051"
---
# <a name="temporary-tables-in-synapse-sql"></a>Temporära tabeller i Synapse SQL

Den här artikeln innehåller grundläggande vägledning för att använda temporära tabeller och beskriver principerna för temporära tabeller på sessionsnivå i Synapse SQL. 

Både de dedikerade SQL-poolerna och serverlösa SQL-poolresurser kan använda temporära tabeller. Serverlös SQL-pool har begränsningar som beskrivs i slutet av den här artikeln. 

## <a name="temporary-tables"></a>Temporära tabeller

Temporära tabeller är användbara vid bearbetning av data, särskilt under omvandling där de mellanliggande resultaten är tillfälliga. Med Synapse SQL finns temporära tabeller på sessionsnivå.  De är bara synliga för den session där de skapades. Därför tas de bort automatiskt när sessionen loggas ut. 

## <a name="temporary-tables-in-dedicated-sql-pool"></a>Temporära tabeller i dedikerad SQL-pool

I den dedikerade SQL-poolresursen erbjuder temporära tabeller en prestandaförmån eftersom deras resultat skrivs till lokal lagring i stället för fjärrlagring.

### <a name="create-a-temporary-table"></a>Skapa en tillfällig tabell

Temporära tabeller skapas genom att tabellnamnet föregås av `#` ett .  Exempel:

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

Temporära tabeller kan också skapas med `CTAS` en med exakt samma metod:

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
> `CTAS` är ett kraftfullt kommando och har den extra fördelen att det är effektivt att använda transaktionsloggutrymmet. 
> 
> 

### <a name="drop-temporary-tables"></a>Ta bort temporära tabeller

När en ny session skapas bör det inte finnas några temporära tabeller.  Men om du anropar samma lagrade procedur som skapar en tillfällig procedur med samma namn, för att säkerställa att dina instruktioner lyckas, använder du en enkel kontroll före `CREATE TABLE` förekomsten med  `DROP` : 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

För kodningskonsekvens är det en bra idé att använda det här mönstret för både tabeller och temporära tabeller.  Det är också en bra idé att använda för `DROP TABLE` att ta bort temporära tabeller när du är klar med dem.  

I utveckling av lagrade procedurer är det vanligt att se att drop-kommandona paketeras tillsammans i slutet av en procedur för att se till att dessa objekt rensas.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularize-code"></a>Modularisera kod

Temporära tabeller kan användas var som helst i en användarsession. Den här funktionen kan sedan utnyttjas för att hjälpa dig modularisera programkoden.  För att demonstrera genererar följande lagrade procedur DDL för att uppdatera all statistik i databasen efter statistiknamn:

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
FROM    #stats_ddl
;
GO
```

I det här skedet är den enda åtgärden som har inträffat att skapa en lagrad procedur som genererar #stats_ddl temporär tabell.  Den lagrade proceduren #stats_ddl om den redan finns. Den här listrutan ser till att den inte misslyckas om den körs mer än en gång i en session.  

Eftersom det inte finns någon i slutet av den lagrade proceduren, förblir den skapade tabellen och kan läsas utanför den lagrade proceduren när den lagrade `DROP TABLE` proceduren har slutförts.  

Till skillnad från andra SQL Server databaser Synapse SQL du använda den tillfälliga tabellen utanför proceduren som skapade den.  De temporära tabeller som skapas via dedikerad SQL-pool kan användas **var** som helst i sessionen. Därför har du mer modulär och hanterbar kod, som du ser i exemplet nedan:

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

### <a name="temporary-table-limitations"></a>Tillfälliga tabellbegränsningar

Dedikerad SQL-pool har några implementeringsbegränsningar för temporära tabeller:

- Endast temporära tabeller med sessionsomfång stöds.  Globala temporära tabeller stöds inte.
- Vyer kan inte skapas i temporära tabeller.
- Temporära tabeller kan bara skapas med hash- eller resursallokeringsdistribution.  Distribution av replikerade temporära tabeller stöds inte. 

## <a name="temporary-tables-in-serverless-sql-pool"></a>Temporära tabeller i en serverlös SQL-pool

Temporära tabeller i serverlös SQL-pool stöds men deras användning är begränsad. De kan inte användas i frågor som är målfiler. 

Du kan till exempel inte ansluta till en tillfällig tabell med data från filer i lagring. Antalet temporära tabeller är begränsat till 100 och deras totala storlek är begränsad till 100 MB.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar tabeller finns i artikeln Designing tables using the Synapse SQL resources (Utforma tabeller med [hjälp Synapse SQL resurser).](develop-tables-overview.md)

