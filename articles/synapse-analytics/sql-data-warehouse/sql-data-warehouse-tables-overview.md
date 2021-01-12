---
title: Designa tabeller
description: Introduktion till utformning av tabeller med dedikerad SQL-pool i Azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/15/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 2a8cfbe75925ddc49f6fa3205fafdd1c2203b472
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115641"
---
# <a name="design-tables-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Design tabeller med dedikerad SQL-pool i Azure Synapse Analytics

Den här artikeln innehåller grundläggande introduktions begrepp för att utforma tabeller i dedikerad SQL-pool.

## <a name="determine-table-category"></a>Bestäm tabell kategori

Ett [stjärn schema](https://en.wikipedia.org/wiki/Star_schema) ordnar data i fakta-och dimensions tabeller. Vissa tabeller används för integrering eller mellanlagring av data innan de flyttas till en fakta-eller dimensions tabell. När du skapar en tabell bestämmer du om tabell data tillhör en fakta-, dimensions-eller integrations tabell. Det här beslutet informerar lämplig tabell struktur och distribution.

- **Fakta tabeller** innehåller kvantitativa data som vanligt vis genereras i ett transaktions system och sedan läses in i den DEDIKERADe SQL-poolen. En detalj handel genererar till exempel försäljnings transaktioner varje dag och läser sedan in data i en särskild fakta tabell för SQL-pool för analys.

- **Dimensions tabeller** innehåller attribut data som kan ändras, men som ofta ändras sällan. Till exempel lagras en kunds namn och adress i en dimensions tabell och uppdateras bara när kundens profil ändras. För att minimera storleken på en stor fakta tabell behöver kundens namn och adress inte finnas i varje rad i en fakta tabell. I stället kan fakta tabellen och dimensions tabellen dela ett kund-ID. En fråga kan ansluta till de två tabellerna för att associera en kunds profil och transaktioner.

- **Integrerings tabeller** tillhandahåller en plats för integrering eller mellanlagring av data. Du kan skapa en integrations tabell som en vanlig tabell, en extern tabell eller en temporär tabell. Du kan till exempel läsa in data till en mellanlagringsplats, utföra omvandlingar för data i mellanlagringen och sedan infoga data i en produktions tabell.

## <a name="schema-and-table-names"></a>Schema-och tabell namn

Scheman är ett bra sätt att gruppera tabeller, som används på liknande sätt tillsammans.  Om du migrerar flera databaser från en lokal-lösning till en dedikerad SQL-pool, fungerar det bäst att migrera alla fakta-, dimensions-och integrations tabeller till ett schema i en dedikerad SQL-pool.

Du kan till exempel lagra alla tabeller i den dedikerade SQL-poolen [informations lagret wideworldimportersdw](/sql/sample/world-wide-importers/database-catalog-wwi-olap?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) -exempel i ett schema med namnet WWI. Följande kod skapar ett [användardefinierat schema](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) med namnet WWI.

```sql
CREATE SCHEMA wwi;
```

Om du vill visa en tabells struktur i en dedikerad SQL-pool kan du använda fakta, dim och int som prefix till tabell namnen. I följande tabell visas några av schema-och tabell namnen för informations lagret wideworldimportersdw.  

| Informations lagret wideworldimportersdw-tabell  | Tabell typ | Dedikerad SQL-pool |
|:-----|:-----|:------|:-----|
| City | Dimension | WWI. DimCity |
| Beställning | Fakta | WWI. FactOrder |

## <a name="table-persistence"></a>Tabell persistence

Tabeller lagrar data antingen permanent i Azure Storage, temporärt i Azure Storage eller i ett externt data lager till en dedikerad SQL-pool.

### <a name="regular-table"></a>Vanlig tabell

En vanlig tabell lagrar data i Azure Storage som en del av en dedikerad SQL-pool. Tabellen och data behålls oavsett om en session är öppen.  I följande exempel skapas en vanlig tabell med två kolumner.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Temporär tabell

Det finns bara en temporär tabell under sessionen. Du kan använda en temporär tabell för att hindra andra användare från att se tillfälliga resultat och också minska behovet av rensning.  

Temporära tabeller använder lokal lagring för att erbjuda snabba prestanda.  Mer information finns i  [temporära tabeller](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Extern tabell

En extern tabell pekar på data som finns i Azure Storage BLOB eller Azure Data Lake Store. När det används tillsammans med CREATE TABLE som SELECT-instruktion, importerar data till en dedikerad SQL-pool genom att välja från en extern tabell.

Därför är externa tabeller användbara för att läsa in data. En inläsnings kurs finns i [använda PolyBase för att läsa in data från Azure Blob Storage](./load-data-from-azure-blob-storage-using-copy.md).

## <a name="data-types"></a>Datatyper

Dedikerad SQL-pool stöder de vanligaste data typerna. En lista över data typer som stöds finns i [data typer i CREATE TABLE referens](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes) i CREATE TABLE-instruktionen. Vägledning om hur du använder data typer finns i [data typer](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Distribuerade tabeller

En grundläggande funktion i en dedikerad SQL-pool är hur den kan lagra och arbeta i tabeller över [distributioner](massively-parallel-processing-mpp-architecture.md#distributions).  Dedikerad SQL-pool stöder tre metoder för att distribuera data: Round-Robin (standard), hash och replikeras.

### <a name="hash-distributed-tables"></a>Hash-distribuerade tabeller

En hash-distribuerad tabell distribuerar rader baserat på värdet i kolumnen distribution. En hash-distribuerad tabell är utformad för att uppnå höga prestanda för frågor i stora tabeller. Det finns flera faktorer att tänka på när du väljer en distributions kolumn.

Mer information finns i [design rikt linjer för distribuerade tabeller](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Replikerade tabeller

En replikerad tabell innehåller en fullständig kopia av tabellen som är tillgänglig på varje Compute-nod. Frågor körs snabbt på replikerade tabeller eftersom kopplingar i replikerade tabeller inte kräver data förflyttning. Replikeringen kräver extra lagring, men är inte praktisk för stora tabeller.

Mer information finns i [design guide för replikerade tabeller](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Round-Robin-tabeller

En Round-Robin-tabell distribuerar tabell rader jämnt över alla distributioner. Raderna distribueras slumpmässigt. Det går snabbt att läsa in data i en Round Robin-tabell.  Tänk på att frågor kan kräva mer data förflyttning än andra distributions metoder.

Mer information finns i [design rikt linjer för distribuerade tabeller](sql-data-warehouse-tables-distribute.md).

### <a name="common-distribution-methods-for-tables"></a>Vanliga distributions metoder för tabeller

Tabell kategorin bestämmer ofta vilka alternativ som ska väljas för att distribuera tabellen.

| Tabell kategori | Rekommenderat distributions alternativ |
|:---------------|:--------------------|
| Fakta           | Använd hash-distribution med grupperat columnstore-index. Prestanda förbättras när två hash-tabeller kopplas till samma distributions kolumn. |
| Dimension      | Använd replikerad för mindre tabeller. Om tabellerna är för stora för att lagras på varje Compute-nod använder du hash-distribuerad. |
| Mellanlagring        | Använd Round Robin för mellanlagrings tabellen. Belastningen med CTAS är snabbt. När data finns i mellanlagringsdatabasen använder du infoga... Välj om du vill flytta data till produktions tabeller. |

## <a name="table-partitions"></a>Table-partitioner

En partitionerad tabell lagrar och utför åtgärder på tabell rader enligt data intervall. En tabell kan till exempel partitioneras per dag, månad eller år. Du kan förbättra frågans prestanda via partition Eli minering, vilket begränsar en sökning till data i en partition. Du kan också underhålla data genom att byta partition. Eftersom data i SQL-poolen redan har distribuerats kan det ta lång tid att köra för många partitioner. Mer information finns i [rikt linjer för partitionering](sql-data-warehouse-tables-partition.md).  När partitionen växlar till diskpartitioner som inte är tomma bör du överväga att använda alternativet TRUNCATE_TARGET i [Alter Table](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) -instruktionen om befintliga data ska trunkeras. Koden nedan växlar i de transformerade dagliga data till SalesFact för att skriva över befintliga data.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>Columnstore-index

Dedicerad SQL-pool lagrar som standard en tabell som ett grupperat columnstore-index. Den här typen av data lagring uppnår hög data komprimering och frågans prestanda i stora tabeller.  

Det grupperade columnstore-indexet är vanligt vis det bästa valet, men i vissa fall är ett grupperat index eller en heap lämplig lagrings struktur.  

> [!TIP]
> En heap-tabell kan vara särskilt användbar vid inläsning av tillfälliga data, till exempel en mellanlagringsplats som omvandlas till en slutlig tabell.

En lista över columnstore-funktioner finns i [Vad är nytt för columnstore-index](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). För att förbättra prestanda för columnstore-index, se [maximera radgrupps-kvalitet för columnstore-index](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Statistik

I frågans optimering används statistik på kolumn nivå när planen för att köra en fråga skapas.

För att förbättra prestanda för frågor, är det viktigt att ha statistik för enskilda kolumner, särskilt kolumner som används i frågor till kopplingar. Att [skapa statistik](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic) sker automatiskt.  

Uppdaterings statistik sker inte automatiskt. Uppdatera statistik när ett stort antal rader har lagts till eller ändrats. Uppdatera till exempel statistik efter en belastning. Mer information finns i [statistik vägledning](sql-data-warehouse-tables-statistics.md).

## <a name="primary-key-and-unique-key"></a>Primär nyckel och unik nyckel

PRIMÄR nyckel stöds bara om både icke-KLUSTRad och inte framtvingad används.  En unik begränsning stöds endast med inte TVINGAd användning.  Kontrol lera [tabell begränsningar för dedikerad SQL-pool](sql-data-warehouse-table-constraints.md).

## <a name="commands-for-creating-tables"></a>Kommandon för att skapa tabeller

Du kan skapa en tabell som en ny tom tabell. Du kan också skapa och fylla i en tabell med resultatet av en SELECT-instruktion. Följande är T-SQL-kommandon för att skapa en tabell.

| T-SQL-uttryck | Description |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | Skapar en tom tabell genom att definiera alla tabell kolumner och alternativ. |
| [SKAPA EXTERN TABELL](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | Skapar en extern tabell. Definitionen av tabellen lagras i en dedikerad SQL-pool. Tabell data lagras i Azure Blob Storage eller Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | Fyller i en ny tabell med resultatet av en SELECT-instruktion. Tabell kolumnerna och data typerna baseras på Select Statement-resultatet. För att importera data, kan den här instruktionen välja från en extern tabell. |
| [SKAPA EXTERN TABELL SOM VÄLJ](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | Skapar en ny extern tabell genom att exportera resultatet av en SELECT-instruktion till en extern plats.  Platsen är antingen Azure Blob Storage eller Azure Data Lake Store. |

## <a name="aligning-source-data-with-dedicated-sql-pool"></a>Justera källdata med dedikerad SQL-pool

Dedikerade tabeller för SQL-pooler fylls genom att läsa in data från en annan data källa. Om du vill utföra en lyckad inläsning måste antalet och data typerna för kolumnerna i käll data justeras mot tabell definitionen i den dedikerade SQL-poolen. Att hämta data så att de justeras kan vara den svåraste delen av att utforma dina tabeller.

Om data kommer från flera data lager läser du in data i den dedikerade SQL-poolen och lagrar dem i en integrations tabell. När data finns i integrations tabellen kan du använda kraften i en dedikerad SQL-pool för att utföra omvandlings åtgärder. När data har för berett kan du infoga dem i produktions tabeller.

## <a name="unsupported-table-features"></a>Tabell funktioner som inte stöds

Dedikerad SQL-pool har stöd för många, men inte alla, av tabell funktionerna som erbjuds av andra databaser.  I följande lista visas några av tabell funktionerna som inte stöds i dedikerad SQL-pool:

- Sekundär nyckel, kontrol lera [tabell begränsningar](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Beräknade kolumner](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Indexerade vyer](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Sequence](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Glesa kolumner](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- Surrogat nycklar. Implementera med [identitet](sql-data-warehouse-tables-identity.md).
- [Synonymer](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Utlösare](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Unika index](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Användardefinierade typer](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="table-size-queries"></a>Tabell storleks frågor

Ett enkelt sätt att identifiera utrymme och rader som används av en tabell i varje 60-distribution är att använda [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Att använda DBCC-kommandon kan dock vara ganska begränsade.  Vyer för dynamisk hantering (DMV: er) visar mer information än DBCC-kommandon. Börja med att skapa den här vyn:

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count]
    + nps.[row_overflow_used_page_count]
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count]
 - (nps.[reserved_page_count] - nps.[used_page_count])
 - ([in_row_data_page_count]
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
WHERE pn.[type] = 'COMPUTE'
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT *
FROM size
;
```

### <a name="table-space-summary"></a>Sammanfattning av tabell utrymme

Den här frågan returnerar raderna och utrymmet efter tabell.  Det gör att du kan se vilka tabeller som är de största tabellerna och om de är resursallokering, replikerade eller hash-distribuerade.  För hash-distribuerade tabeller visar frågan distributions kolumnen.  

```sql
SELECT
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM
    dbo.vTableSizes
GROUP BY
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Tabell utrymme efter distributions typ

```sql
SELECT
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Tabell utrymme efter index typ

```sql
SELECT
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Sammanfattning av distributions utrymme

```sql
SELECT
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Nästa steg

När du har skapat tabellerna för din dedikerade SQL-pool är nästa steg att läsa in data i tabellen.  En inläsnings kurs finns i [inläsning av data till dedikerad SQL-pool](load-data-wideworldimportersdw.md).