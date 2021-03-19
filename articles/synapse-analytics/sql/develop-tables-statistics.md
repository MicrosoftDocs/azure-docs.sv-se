---
title: Skapa och uppdatera statistik med Azure Synapse SQL-resurser
description: Rekommendationer och exempel för att skapa och uppdatera statistik för att optimera frågor i Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/19/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 450a089c6cc1c77ac26cb0aa339277d5c49b41c8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594792"
---
# <a name="statistics-in-synapse-sql"></a>Statistik i Synapse SQL

I den här artikeln finns rekommendationer och exempel för att skapa och uppdatera statistik för optimering av frågor med hjälp av SQL-Synapse: dedikerad SQL-pool och Server lös SQL-pool.

## <a name="statistics-in-dedicated-sql-pool"></a>Statistik i dedikerad SQL-pool

### <a name="why-use-statistics"></a>Varför använda statistik

Den mer dedikerade SQL-poolen känner till om dina data, desto snabbare kan den köra frågor. Efter inläsning av data i en dedikerad SQL-pool är insamling av statistik för dina data ett av de viktigaste saker som du kan göra för att optimera frågor.  

Den dedikerade SQL-poolens fråga optimering är en kostnads baserad optimering. Den Jämför kostnaden för olika fråge planer och väljer sedan planen med den lägsta kostnaden. I de flesta fall väljer den den plan som ska köras snabbast.

Om optimeringen t. ex. beräknar att det datum då frågan filtreras på kommer att returnera en rad, väljs en plan. Om den beräknar att det valda datumet returnerar 1 000 000 rader, returneras ett annat schema.

### <a name="automatic-creation-of-statistics"></a>Automatisk generering av statistik

Den dedikerade SQL-poolen analyserar inkommande användar frågor för saknad statistik när alternativet databas AUTO_CREATE_STATISTICS är inställt på `ON` .  Om statistik saknas skapar Query Optimering statistik för enskilda kolumner i frågesyntaxen eller kopplings villkoret. 

Den här funktionen används för att förbättra beräkningar av kardinalitet för frågeplan.

> [!IMPORTANT]
> Automatisk generering av statistik är för närvarande aktiverat som standard.

Du kan kontrol lera om ditt informations lager har AUTO_CREATE_STATISTICS konfigurerat genom att köra följande kommando:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Om informations lagret inte har AUTO_CREATE_STATISTICS aktiverat, rekommenderar vi att du aktiverar den här egenskapen genom att köra följande kommando:

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
> Automatisk generering av statistik genereras inte i temporära eller externa tabeller.

Automatisk generering av statistik görs synkront. Det innebär att du kan få en försämrad frågeresultat om kolumnerna saknar statistik. Hur lång tid det tar att skapa statistik för en enskild kolumn beror på tabellens storlek.

För att undvika mätbar prestanda försämring bör du se till att statistik har skapats först genom att köra benchmark-arbetsbelastningen innan du profileringen av systemet.

> [!NOTE]
> När du skapar statistik loggas [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true) under en annan användar kontext.

När automatisk statistik skapas, kommer de att ha formen: _WA_Sys_<8 siffer kolumn-ID i hex>_<8 siffror tabell-ID i hex>. Du kan visa redan skapade statistik genom att köra [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) -kommandot:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Table_name är namnet på den tabell som innehåller den statistik som ska visas, vilket inte kan vara en extern tabell. Målet är namnet på mål indexet, statistiken eller kolumnen som statistik information ska visas för.

### <a name="update-statistics"></a>Uppdatera statistik

Ett bra tips är att uppdatera statistik för datum kolumner varje dag då nya datum läggs till. Varje gången nya rader läses in i data lagret läggs nya inläsnings datum eller transaktions datum till. Dessa tillägg ändrar data distributionen och gör statistiken inaktuell.

Statistik för en land-eller region kolumn i en kund tabell kanske aldrig behöver uppdateras eftersom fördelning av värden normalt inte ändras. Förutsatt att fördelningen är konstant mellan kunderna, kommer nya rader i tabell variationen inte att ändra data distributionen.

Om informations lagret bara innehåller ett land eller en region och du hämtar data från ett nytt land eller en annan region måste du uppdatera statistik i kolumnen land eller region.

Följande är rekommendationer för att uppdatera statistik:

|Typ|Rekommendation|
|-|-|
| **Frekvens för statistik uppdateringar**  | Försiktigt: varje dag </br> När du har läst in eller omvandlat dina data |
| **Sampling** |  Färre än 1 000 000 000 rader, Använd standard sampling (20 procent). </br> Med fler än 1 000 000 000 rader använder du samplingen av två procent. |

### <a name="determine-last-statistics-update"></a>Fastställ senaste statistik uppdatering

En av de första frågorna för att fråga när du felsöker en fråga är **"är statistiken uppdaterad?"**

Den här frågan är inte en som kan besvaras av data åldern. Ett uppdaterat statistik objekt kan vara gammalt om ingen material ändring har gjorts i underliggande data. När antalet rader har ändrats huvudsakligen, eller om en material ändring i fördelning av värden för en kolumn inträffar, *är det dags* att uppdatera statistik.

Det finns ingen dynamisk hanterings vy att avgöra om data i tabellen har ändrats sedan den senaste tids statistiken uppdaterades. Att veta ålder på din statistik kan ge dig en del av bilden. 

Du kan använda följande fråga för att avgöra när statistiken senast uppdaterades i varje tabell.

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

**Datum kolumner** i ett data lager, till exempel vanligt vis behöver ofta förekommande statistik uppdateringar. Varje gången nya rader läses in i data lagret läggs nya inläsnings datum eller transaktions datum till. Dessa tillägg ändrar data distributionen och gör statistiken inaktuell.

Statistik i en köns kolumn i en kund tabell kanske aldrig behöver uppdateras. Förutsatt att fördelningen är konstant mellan kunderna, kommer nya rader i tabell variationen inte att ändra data distributionen.

Men om ditt informations lager bara innehåller en kön och ett nytt krav resulterar i flera kön måste du uppdatera statistiken i kön kön. 

Mer information hittar du i [statistik](/sql/relational-databases/statistics/statistics) artikeln.

### <a name="implement-statistics-management"></a>Implementera statistik hantering

Det är ofta en bra idé att utöka data inläsnings processen så att statistiken uppdateras i slutet av belastningen. Data inläsningen är när tabeller ofta ändrar storlek, fördelning av värden eller både och. Därför är inläsnings processen en logisk plats för att implementera vissa hanterings processer.

Följande GUID-principer används för att uppdatera din statistik under inläsnings processen:

- Se till att varje inläst tabell har minst ett uppdaterat statistik objekt. Den här processen uppdaterar informationen i tabell storlek (antal rader och sid antal) som en del av statistiken uppdatera.
- Fokusera på kolumner som ingår i JOIN-, GROUP BY-, ORDER BY-och DISTINCT-satser.
- Överväg att uppdatera "ascending Key"-kolumner som transaktions datum oftare eftersom dessa värden inte ingår i statistik histogrammet.
- Överväg att uppdatera statiska distributions kolumner mindre ofta.
- Kom ihåg att varje statistik objekt uppdateras i följd. Att bara implementera `UPDATE STATISTICS <TABLE_NAME>` är inte alltid idealiskt, särskilt för breda tabeller med massor av statistik objekt.

Mer information finns i [beräkning av kardinalitet](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics"></a>Exempel: skapa statistik

I de här exemplen visas hur du använder olika alternativ för att skapa statistik. De alternativ som du använder för varje kolumn beror på egenskaperna för dina data och hur kolumnen kommer att användas i frågor.

#### <a name="create-single-column-statistics-with-default-options"></a>Skapa en statistik med en kolumn med standard alternativ

Om du vill skapa statistik för en kolumn anger du ett namn för statistik objekt och kolumn namn.
I den här syntaxen används alla standard alternativ. Som standard får en dedikerad SQL-pool **20 procent** av tabellen när den skapar statistik.

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name]);
```

Exempel:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Skapa en statistik med en kolumn genom att undersöka varje rad

Standard samplings frekvensen på 20 procent räcker för de flesta situationer. Du kan dock justera samplings frekvensen. Använd följande syntax för att sampla den fullständiga tabellen:

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name])
    WITH FULLSCAN;
```

Exempel:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Skapa en statistik med en kolumn genom att ange prov storleken

Ett annat alternativ är att ange exempel storleken i procent:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Skapa statistik med en kolumn på bara några av raderna

Du kan också skapa statistik för en del av raderna i tabellen, vilket kallas för en filtrerad statistik.

Du kan till exempel använda filtrerad statistik när du planerar att fråga en speciell partition i en stor partitionerad tabell. Genom att skapa statistik enbart på partitionsalternativ förbättras noggrannheten i statistiken. Du får också en förbättring av frågans prestanda.

I det här exemplet skapas statistik för ett värde intervall. Värdena kan enkelt definieras för att matcha värde intervallet i en partition.

```sql
CREATE STATISTICS stats_col1
    ON table1(col1)
    WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> För att fråga optimeringen ska överväga att använda filtrerad statistik när den väljer den distribuerade frågan måste frågan passa inuti definitionen av statistik objekt. I föregående exempel måste frågans WHERE-sats ange col1-värden mellan 2000101 och 20001231.

#### <a name="create-single-column-statistics-with-all-the-options"></a>Skapa statistik med en kolumn med alla alternativ

Du kan också kombinera alternativen tillsammans. I följande exempel skapas ett filtrerat statistik objekt med en anpassad exempel storlek:

```sql
CREATE STATISTICS stats_col1
    ON table1 (col1)
    WHERE col1 > '2000101' AND col1 < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Fullständig referens finns i [skapa statistik](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true).

#### <a name="create-multi-column-statistics"></a>Skapa statistik för flera kolumner

Använd föregående exempel, men ange fler kolumner för att skapa ett statistik objekt med flera kolumner.

> [!NOTE]
> Histogrammet, som används för att uppskatta antalet rader i frågeresultatet, är bara tillgängligt för den första kolumnen som anges i statistik objekt definitionen.

I det här exemplet är histogrammet i *produkt \_ kategorin*. Statistik över kolumner beräknas för *produkt \_ kategori* och *produkt \_ sub_category*:

```sql
CREATE STATISTICS stats_2cols
    ON table1 (product_category, product_sub_category)
    WHERE product_category > '2000101' AND product_category < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Eftersom det finns en korrelation mellan *produkt \_ kategori* och *produkt \_ under \_ kategori*, kan ett statistik objekt med flera kolumner vara användbart om dessa kolumner används samtidigt.

#### <a name="create-statistics-on-all-columns-in-a-table"></a>Skapa statistik för alla kolumner i en tabell

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

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Använd en lagrad procedur för att skapa statistik för alla kolumner i en databas

SQL-poolen har ingen system lagrad procedur som motsvarar sp_create_stats i SQL Server. Den här lagrade proceduren skapar ett enda kolumn statistik objekt på varje kolumn i databasen som inte redan har statistik.

I följande exempel får du hjälp att komma igång med databas designen. Det är kostnads fritt att anpassa den efter dina behov:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default, 2 Fullscan, 3 Sample
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

Anropa den här proceduren för att skapa statistik för alla kolumner i tabellen med en fullscan:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Om du vill skapa exempel statistik för alla kolumner i tabellen anger du 3 och exemplet procent. I proceduren nedan används en samplings frekvens på 20 procent.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>Exempel: uppdatera statistik

Om du vill uppdatera statistiken kan du:

- Uppdatera ett statistik objekt. Ange namnet på det statistik objekt som du vill uppdatera.
- Uppdatera alla statistik objekt i en tabell. Ange namnet på tabellen i stället för ett enskilt statistik objekt.

#### <a name="update-one-specific-statistics-object"></a>Uppdatera ett enskilt statistik objekt

Använd följande syntax för att uppdatera ett enskilt statistik objekt:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Exempel:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Genom att uppdatera vissa statistik objekt kan du minimera den tid och de resurser som krävs för att hantera statistik. Den här åtgärden kräver viss idé att välja de bästa statistik objekt som ska uppdateras.

#### <a name="update-all-statistics-on-a-table"></a>Uppdatera all statistik för en tabell

En enkel metod för att uppdatera alla statistik objekt i en tabell är:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Exempel:

```sql
UPDATE STATISTICS dbo.table1;
```

UPDATE STATISTICS-instruktionen är enkel att använda. Kom bara ihåg att den uppdaterar *all* statistik i tabellen, vilket kräver mer arbete än vad som behövs. 

Om prestanda inte är ett problem är den här metoden det enklaste och mest kompletta sättet att garantera att statistiken är aktuell.

> [!NOTE]
> När du uppdaterar all statistik i en tabell gör en dedikerad SQL-pool en sökning för att sampla tabellen för varje statistik objekt. Om tabellen är stor och har många kolumner och många statistik, kan det vara mer effektivt att uppdatera individuell statistik utifrån behov.

En implementering av en `UPDATE STATISTICS` procedur finns i [temporära tabeller](develop-tables-temporary.md). Implementerings metoden skiljer sig något från föregående `CREATE STATISTICS` procedur, men resultatet är detsamma.
Fullständig syntax finns i [Uppdatera statistik](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true).

### <a name="statistics-metadata"></a>Metadata för statistik

Det finns flera systemvyer och funktioner som du kan använda för att hitta information om statistik. Du kan till exempel se om ett statistik objekt är inaktuellt genom att använda funktionen STATS_DATE (). Med STATS_DATE () kan du se när statistiken senast skapades eller uppdaterades.

#### <a name="catalog-views-for-statistics"></a>Katalogvyer för statistik

Dessa system visningar innehåller information om statistik:

| Katalogvy | Description |
|:--- |:--- |
| [sys. columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) |En rad för varje kolumn. |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) |En rad för varje objekt i databasen. |
| [sys. schema](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) |En rad för varje schema i databasen. |
| [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) |En rad för varje statistik objekt. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) |En rad för varje kolumn i objektet statistik. Länkar tillbaka till sys. columns. |
| [sys. tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?view=azure-sqldw-latest&preserve-view=true) |En rad för varje tabell (inklusive externa tabeller). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?view=azure-sqldw-latest&preserve-view=true) |En rad för varje datatyp. |

#### <a name="system-functions-for-statistics"></a>System funktioner för statistik

Dessa system funktioner är användbara när du arbetar med statistik:

| System funktion | Description |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Datum då statistik objekt senast uppdaterades. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Sammanfattnings nivå och detaljerad information om distributionen av värden som förstås av statistik objekt. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Kombinera statistik kolumner och funktioner i en vy

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
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

### <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS ()-exempel

DBCC SHOW_STATISTICS () visar data som lagras i ett statistik objekt. Dessa data ingår i tre delar:

- Huvud
- Densitets vektor
- Histogram

Rubriken är metadata om statistiken. Histogrammet visar fördelningen av värden i den första nyckel kolumnen i statistik-objektet. 

Täthets vektorn mäter en jämförelse mellan kolumner. Dedikerad SQL-pool beräknar kardinalitet uppskattningar med alla data i statistik-objektet.

#### <a name="show-header-density-and-histogram"></a>Visa rubrik, densitet och histogram

Det här enkla exemplet visar alla tre delarna i ett statistik objekt:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Exempel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Visa en eller flera delar av DBCC SHOW_STATISTICS ()

Om du bara vill visa vissa delar använder du `WITH` satsen och anger vilka delar du vill se:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
    WITH stat_header, histogram, density_vector
```

Exempel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1)
    WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>Skillnader mellan DBCC SHOW_STATISTICS ()

`DBCC SHOW_STATISTICS()` är mer strikt implementerad i dedikerad SQL-pool jämfört med SQL Server:

- Funktioner som inte har dokumenterats stöds inte.
- Det går inte att använda Stats_stream.
- Det går inte att ansluta resultat för vissa del mängder av statistik data. STAT_HEADER till exempel koppla DENSITY_VECTOR.
- NO_INFOMSGS kan inte ställas in för meddelande under tryckning.
- Hakparenteser runt statistik namn kan inte användas.
- Det går inte att använda kolumn namn för att identifiera statistik objekt.
- Anpassad fel 2767 stöds inte.


## <a name="statistics-in-serverless-sql-pool"></a>Statistik i SQL-pool utan Server

Statistik skapas per viss kolumn för viss data uppsättning (lagrings Sök väg).

> [!NOTE]
> Det går inte att skapa statistik för LOB-kolumner.

### <a name="why-use-statistics"></a>Varför använda statistik

Ju mer Server lös SQL-poolen vet om dina data, desto snabbare kan den köra frågor mot den. Insamling av statistik för dina data är ett av de viktigaste sakerna du kan göra för att optimera dina frågor. 

Den serverbaserade SQL-poolens fråga optimering är en kostnads baserad optimering. Den Jämför kostnaden för olika fråge planer och väljer sedan planen med den lägsta kostnaden. I de flesta fall väljer den den plan som ska köras snabbast. 

Om optimeringen t. ex. beräknar att det datum då frågan filtreras på kommer att returnera en rad kommer den att välja en plan. Om den beräknar att det valda datumet returnerar 1 000 000 rader, returneras ett annat schema.

### <a name="automatic-creation-of-statistics"></a>Automatisk generering av statistik

SQL-pool utan server analyserar inkommande användar frågor för saknad statistik. Om statistik saknas skapar Query Optimering statistik för enskilda kolumner i frågeuttrycket eller kopplings villkor för att förbättra kardinalitet uppskattningar för frågeplan.

SELECT-instruktionen utlöser automatisk skapande av statistik.

> [!NOTE]
> Automatisk generering av statistik är aktive rad för Parquet-filer. För CSV-filer måste du skapa statistik manuellt tills automatisk generering av CSV-filer stöds.

Automatisk generering av statistik görs synkront så att du kan få en försämrad frågeresultat om kolumnerna saknar statistik. Hur lång tid det tar att skapa statistik för en enskild kolumn beror på storleken på de riktade filerna.

### <a name="manual-creation-of-statistics"></a>Manuell skapande av statistik

Med Server lös SQL-pool kan du skapa statistik manuellt. För CSV-filer måste du skapa statistik manuellt eftersom automatisk generering av statistik inte är aktive rad för CSV-filer. 

I följande exempel finns anvisningar om hur du manuellt skapar statistik.

### <a name="update-statistics"></a>Uppdatera statistik

Ändringar av data i filer, ta bort och lägga till filer resulterar i ändringar i data distributionen och gör statistiken inaktuell. I så fall måste statistik uppdateras.

SQL-poolen utan server återskapar automatiskt statistiken om data ändras markant. Varje gång statistik skapas skapas även det aktuella läget för data uppsättningen: fil Sök vägar, storlekar, senaste ändrings datum.

När statistiken är inaktuella skapas nya. Algoritmen går igenom data och jämför dem med data uppsättningens aktuella tillstånd. Om storleken på ändringarna är större än det angivna tröskelvärdet tas gammal statistik bort och återskapas över den nya data uppsättningen.

Manuell statistik deklareras aldrig som inaktuell.

> [!NOTE]
> Automatisk rekreation av statistik är aktive rad för Parquet-filer. För CSV-filer måste du släppa och skapa statistik manuellt tills automatisk generering av CSV-filer stöds. Se exemplen nedan om hur du släpper och skapar statistik.

En av de första frågorna för att fråga när du felsöker en fråga är **"är statistiken uppdaterad?"**

När antalet rader har ändrats huvudsakligen, eller om det finns en väsentlig förändring i en kolumns fördelning, *är det dags* att uppdatera statistik.

> [!NOTE]
> Om det finns en väsentlig förändring i fördelningen av värden för en kolumn bör du uppdatera statistiken, oavsett när de uppdaterades senast.

### <a name="implement-statistics-management"></a>Implementera statistik hantering

Du kanske vill utöka din datapipeline för att se till att statistiken uppdateras när data ändras markant genom tillägg, borttagning eller ändring av filer.

Följande GUID-principer finns för att uppdatera din statistik:

- Kontrol lera att data uppsättningen har minst ett statistik objekt uppdaterat. Den här uppdaterings storleken (antal rader och sid antal) som en del av statistiken.
- Fokusera på kolumner som ingår i WHERE-, JOIN-, GROUP BY-, ORDER BY-och DISTINCT-satser.
- Uppdatera "ascending Key"-kolumner som transaktions datum oftare eftersom dessa värden inte ingår i statistik histogrammet.
- Uppdatera statiska distributions kolumner mindre ofta.

Mer information finns i [beräkning av kardinalitet](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>Exempel: skapa statistik för kolumn i OpenRowSet-sökväg

I följande exempel visas hur du använder olika alternativ för att skapa statistik. De alternativ som du använder för varje kolumn beror på egenskaperna för dina data och hur kolumnen kommer att användas i frågor.

> [!NOTE]
> Du kan bara skapa en statistik med en kolumn just nu.
>
> Följande behörigheter krävs för att köra sp_create_openrowset_statistics och sp_drop_openrowset_statistics: administrera Mass åtgärder eller administrera databas Mass åtgärder.

Följande lagrade procedur används för att skapa statistik:

```sql
sys.sp_create_openrowset_statistics [ @stmt = ] N'statement_text'
```

Argument: [ @stmt =] N ' statement_text '-anger en Transact-SQL-instruktion som ska returnera kolumn värden som ska användas för statistik. Du kan använda TABLESAMPLE för att ange exempel på data som ska användas. Om TABLESAMPLE inte anges kommer FULLSCAN att användas.

```syntaxsql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> CSV-sampling fungerar inte just nu, endast FULLSCAN stöds för CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Skapa en statistik med en kolumn genom att undersöka varje rad

Om du vill skapa statistik för en kolumn anger du en fråga som returnerar kolumnen som du behöver statistik för.

Om du inte anger något annat använder Server lös SQL-poolen som standard 100% av de data som anges i data uppsättningen när statistik skapas.

Till exempel för att skapa statistik med standard alternativ (FULLSCAN) för en års kolumn i data uppsättningen baserat på population.csv-filen:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT year
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv'',
        FORMAT = ''CSV'',
        FIELDTERMINATOR ='','',
        ROWTERMINATOR = ''\n''
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
'
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Skapa en statistik med en kolumn genom att ange prov storleken

Du kan ange exempel storleken i procent:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>Exempel: uppdatera statistik

Om du vill uppdatera statistiken måste du släppa och skapa statistik. Följande lagrade procedur används för att släppa statistik:

```sql
sys.sp_drop_openrowset_statistics [ @stmt = ] N'statement_text'
```

> [!NOTE]
> Följande behörigheter krävs för att köra sp_create_openrowset_statistics och sp_drop_openrowset_statistics: administrera Mass åtgärder eller administrera databas Mass åtgärder.

Argument: [ @stmt =] N ' statement_text '-anger samma Transact-SQL-uttryck som används när statistiken skapades.

Om du vill uppdatera statistiken för kolumnen år i data uppsättningen, som baseras på population.csv-filen, måste du släppa och skapa statistik:

```sql
EXEC sys.sp_drop_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>Exempel: skapa statistik för extern tabell kolumn

I följande exempel visas hur du använder olika alternativ för att skapa statistik. De alternativ som du använder för varje kolumn beror på egenskaperna för dina data och hur kolumnen kommer att användas i frågor.

> [!NOTE]
> Du kan bara skapa en statistik med en kolumn just nu.

Om du vill skapa statistik för en kolumn anger du ett namn för statistik objekt och kolumn namn.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

Argument: external_table anger extern tabell som statistik ska skapas i.

FULLSCAN beräknings statistik genom att genomsöka alla rader. FULLSCAN och SAMPLE 100 procent har samma resultat. FULLSCAN kan inte användas med exempel alternativet.

SAMPLE Number procent anger den ungefärliga procent andelen eller antalet rader i tabellen eller den indexerade vyn för frågans optimering som ska användas när statistik skapas. Antalet kan vara mellan 0 och 100.

EXEMPLET kan inte användas med alternativet FULLSCAN.

> [!NOTE]
> CSV-sampling fungerar inte just nu, endast FULLSCAN stöds för CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Skapa en statistik med en kolumn genom att undersöka varje rad

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Skapa en statistik med en kolumn genom att ange prov storleken

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>Exempel: uppdatera statistik

Om du vill uppdatera statistiken måste du släppa och skapa statistik. Släpp statistiken först:

```sql
DROP STATISTICS census_external_table.sState
```

Och skapa statistik:

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

### <a name="statistics-metadata"></a>Metadata för statistik

Det finns flera systemvyer och funktioner som du kan använda för att hitta information om statistik. Du kan till exempel se om ett statistik objekt är inaktuellt genom att använda funktionen STATS_DATE (). Med STATS_DATE () kan du se när statistiken senast skapades eller uppdaterades.

> [!NOTE]
> Statistik för metadata är endast tillgänglig för externa tabell kolumner. Statistik för metadata är inte tillgänglig för OpenRowSet-kolumner.

#### <a name="catalog-views-for-statistics"></a>Katalogvyer för statistik

Dessa system visningar innehåller information om statistik:

| Katalogvy                                                 | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [sys. columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) | En rad för varje kolumn.                                     |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) | En rad för varje objekt i databasen.                     |
| [sys. schema](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) | En rad för varje schema i databasen.                     |
| [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) | En rad för varje statistik objekt.                          |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) | En rad för varje kolumn i objektet statistik. Länkar tillbaka till sys. columns. |
| [sys. tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?view=azure-sqldw-latest&preserve-view=true) | En rad för varje tabell (inklusive externa tabeller).           |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?view=azure-sqldw-latest&preserve-view=true) | En rad för varje datatyp.                                  |

#### <a name="system-functions-for-statistics"></a>System funktioner för statistik

Dessa system funktioner är användbara när du arbetar med statistik:

| System funktion                                              | Description                                  |
| :----------------------------------------------------------- | :------------------------------------------- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Datum då statistik objekt senast uppdaterades. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Kombinera statistik kolumner och funktioner i en vy

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
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   st.[user_created] = 1
;
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan förbättra frågans prestanda för dedikerad SQL-pool finns i [övervaka din arbets belastning](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) och [bästa praxis för dedikerad SQL-pool](best-practices-dedicated-sql-pool.md#maintain-statistics).

För att ytterligare förbättra frågans prestanda för SQL-pool utan server, se [metod tips för Server lös SQL-pool](best-practices-serverless-sql-pool.md)
