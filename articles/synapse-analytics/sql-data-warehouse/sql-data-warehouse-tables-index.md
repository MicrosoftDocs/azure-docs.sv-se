---
title: Indexera tabeller
description: Rekommendationer och exempel för indexering av tabeller i dedikerad SQL-pool.
services: synapse-analytics
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/16/2021
author: XiaoyuMSFT
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 58f3eed8b16ff3ed02c6dfac6dc7d72ebb4ca374
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599986"
---
# <a name="indexing-dedicated-sql-pool-tables-in-azure-synapse-analytics"></a>Indexera dedikerade SQL-pooltabeller i Azure Synapse Analytics

Rekommendationer och exempel för indexering av tabeller i dedikerad SQL-pool.

## <a name="index-types"></a>Indextyper

Dedikerad SQL-pool erbjuder flera indexeringsalternativ, inklusive grupperade [columnstore-index,](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)klustrade [index](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)och icke-klusterindex och ett icke-indexalternativ som även kallas [heap](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  

Information om hur du skapar en tabell med ett index [finns i CREATE TABLE (dedikerad SQL-pool)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) dokumentation.

## <a name="clustered-columnstore-indexes"></a>Grupperade columnstore-index

Som standard skapar dedikerad SQL-pool ett grupperat columnstore-index när inga indexalternativ anges i en tabell. Grupperade columnstore-tabeller erbjuder både den högsta nivån av datakomprimering och bästa övergripande frågeprestanda.  Grupperade columnstore-tabeller överträffar vanligtvis klustrade index eller heap-tabeller och är vanligtvis det bästa valet för stora tabeller.  Av dessa skäl är klustrad columnstore den bästa platsen att börja på när du är osäker på hur du indexerar tabellen.  

Om du vill skapa en klustrad columnstore-tabell anger du bara CLUSTERED COLUMNSTORE INDEX i WITH-satsen eller lämnar WITH-satsen av:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Det finns några scenarier där grupperat columnstore kanske inte är ett bra alternativ:

- Columnstore-tabeller stöder inte varchar(max), nvarchar(max) och varbinary(max). Överväg heap eller grupperat index i stället.
- Columnstore-tabeller kan vara mindre effektiva för tillfälliga data. Överväg heap och kanske till och med temporära tabeller.
- Små tabeller med mindre än 60 miljoner rader. Överväg heap-tabeller.

## <a name="heap-tables"></a>Heap-tabeller

När du tillfälligt använder data i en dedikerad SQL-pool kan det hända att den övergripande processen går snabbare om du använder en heap-tabell. Det beror på att inläsningar till heaps är snabbare än indextabeller och i vissa fall kan efterföljande läsningar göras från cachen.  Om du bara läser in data för att mellanlägga dem innan du kör fler transformationer går det mycket snabbare att läsa in tabellen till heap-tabellen än att läsa in data till en klustrad columnstore-tabell. Dessutom läses inläsningen av data [till en temporär tabell](sql-data-warehouse-tables-temporary.md) snabbare än att läsa in en tabell till permanent lagring.  Efter datainläsning kan du skapa index i tabellen för snabbare frågeprestanda.  

Klusterkolumnlagringstabeller börjar uppnå optimal komprimering när det finns fler än 60 miljoner rader.  För små uppslagstabeller, mindre än 60 miljoner rader, bör du överväga att använda HEAP eller grupperat index för snabbare frågeprestanda. 

Om du vill skapa en heap-tabell anger du bara HEAP i WITH-satsen:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Grupperade och icke-klusterindex

Klustrade index kan prestera bättre än klustrade columnstore-tabeller när en enskild rad snabbt behöver hämtas. För frågor där en enda eller mycket få rader sökning krävs för att utföra med extrem hastighet kan du överväga ett grupperat index eller icke-kluster sekundärt index. Nackdelen med att använda ett grupperat index är att endast frågor som drar nytta av är de som använder ett mycket selektivt filter på den grupperade indexkolumnen. För att förbättra filtret på andra kolumner kan ett icke-clustered-index läggas till i andra kolumner. Varje index som läggs till i en tabell lägger dock till både utrymme och bearbetningstid för inbelastningar.

Om du vill skapa en klustrad indextabell anger du bara CLUSTERED INDEX i WITH-satsen:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Om du vill lägga till ett icke-grupperat index i en tabell använder du följande syntax:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Optimera grupperade columnstore-index

Grupperade columnstore-tabeller är ordnade i data i segment.  Det är viktigt att ha hög segmentkvalitet för att uppnå optimala frågeprestanda i en columnstore-tabell.  Segmentkvaliteten kan mätas med antalet rader i en komprimerad radgrupp.  Segmentkvaliteten är mest optimal där det finns minst 100 000 rader per komprimerad radgrupp och bättre prestanda eftersom antalet rader per radgrupp närmar sig 1 048 576 rader, vilket är de flesta rader som en radgrupp kan innehålla.

Nedanstående vy kan skapas och användas i systemet för att beräkna de genomsnittliga raderna per radgrupp och identifiera eventuella icke-optimala klusterkolumnlagringsindex.  Den sista kolumnen i den här vyn genererar en SQL-instruktion som kan användas för att återskapa dina index.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Nu när du har skapat vyn kör du den här frågan för att identifiera tabeller med radgrupper med färre än 100 000 rader. Naturligtvis kanske du vill öka tröskelvärdet på 100 000 om du vill ha en bättre segmentkvalitet.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

När du har kört frågan kan du börja titta på data och analysera dina resultat. I den här tabellen förklaras vad du ska leta efter i din radgruppsanalys.

| Kolumn | Så här använder du dessa data |
| --- | --- |
| [table_partition_count] |Om tabellen är partitionerad kan du förvänta dig att se högre antal öppna radgrupper. Varje partition i fördelningen kan i teorin ha en öppen radgrupp kopplad till den. Ta med detta i din analys. En liten tabell som har partitionerats kan optimeras genom att partitioneringen tas bort helt och hållet eftersom detta skulle förbättra komprimeringen. |
| [row_count_total] |Totalt antal rader för tabellen. Du kan till exempel använda det här värdet för att beräkna procentandelen rader i komprimerat tillstånd. |
| [row_count_per_distribution_MAX] |Om alla rader distribueras jämnt skulle det här värdet vara målantalet rader per distribution. Jämför det här värdet med compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Totalt antal rader i columnstore-format för tabellen. |
| [COMPRESSED_rowgroup_rows_AVG] |Om det genomsnittliga antalet rader är betydligt mindre än det maximala antalet rader för en radgrupp bör du överväga att använda CTAS eller ALTER INDEX REBUILD för att komprimera data igen |
| [COMPRESSED_rowgroup_count] |Antal radgrupper i columnstore-format. Om det här talet är mycket högt i förhållande till tabellen är det en indikator om att kolumnlagringsdensiteten är låg. |
| [COMPRESSED_rowgroup_rows_DELETED] |Rader tas bort logiskt i columnstore-format. Om antalet är högt i förhållande till tabellstorleken bör du överväga att återskapa partitionen eller återskapa indexet eftersom detta tar bort dem fysiskt. |
| [COMPRESSED_rowgroup_rows_MIN] |Använd detta tillsammans med kolumnerna AVG och MAX för att förstå intervallet med värden för radgrupperna i ditt columnstore. Ett lågt tal över belastningströskeln (102 400 per partitionsjusterad distribution) tyder på att optimeringar är tillgängliga i databelastningen |
| [COMPRESSED_rowgroup_rows_MAX] |Som ovan |
| [OPEN_rowgroup_count] |Öppna radgrupper är normala. Man förväntar sig rimligen en OPEN-radgrupp per tabelldistribution (60). För många tal tyder på data inläsning över partitioner. Dubbelkolla partitioneringsstrategin för att kontrollera att den är bra |
| [OPEN_rowgroup_rows] |Varje radgrupp kan ha högst 1 048 576 rader. Använd det här värdet om du vill se hur fullständiga de öppna radgrupperna är för närvarande |
| [OPEN_rowgroup_rows_MIN] |Öppna grupper anger att data antingen läses in i tabellen eller att den tidigare belastningen spills över återstående rader i den här radgruppen. Använd kolumnerna MIN, MAX, AVG för att se hur mycket data som finns i ÖPPNA radgrupper. För små tabeller kan det vara 100 % av alla data! I så fall kan ALTER INDEX REBUILD tvinga data till columnstore. |
| [OPEN_rowgroup_rows_MAX] |Som ovan |
| [OPEN_rowgroup_rows_AVG] |Som ovan |
| [CLOSED_rowgroup_rows] |Titta på de stängda radgruppraderna som en sanitetskontroll. |
| [CLOSED_rowgroup_count] |Antalet stängda radgrupper bör vara lågt om några alls visas. Stängda radgrupper kan konverteras till komprimerade radgrupper med hjälp av ALTER INDEX ... Omorganisera kommandot. Detta krävs dock normalt inte. Stängda grupper konverteras automatiskt till columnstore-radgrupper med hjälp av bakgrundsprocessen "tuppel mover". |
| [CLOSED_rowgroup_rows_MIN] |Stängda radgrupper bör ha en mycket hög fyllningsfrekvens. Om fyllningsfrekvensen för en stängd radgrupp är låg krävs ytterligare analys av columnstore. |
| [CLOSED_rowgroup_rows_MAX] |Som ovan |
| [CLOSED_rowgroup_rows_AVG] |Som ovan |
| [Rebuild_Index_SQL] |SQL för att återskapa columnstore-index för en tabell |

## <a name="impact-of-index-maintenance"></a>Effekt av indexunderhåll

Kolumnen i `Rebuild_Index_SQL` vyn `vColumnstoreDensity` innehåller en `ALTER INDEX REBUILD` -instruktion som kan användas för att återskapa dina index. När du återskapar indexen måste du allokera tillräckligt med minne till sessionen som återskapar indexet. Det gör du genom att öka [resursklassen för](resource-classes-for-workload-management.md) en användare som har behörighet att återskapa indexet i den här tabellen till det rekommenderade minimivärdet. Ett exempel finns i Återskapa [index för att förbättra segmentkvaliteten senare](#rebuilding-indexes-to-improve-segment-quality) i den här artikeln.

För en tabell med ett ordnat grupperat `ALTER INDEX REBUILD` columnstore-index sorterar om data med tempdb. Övervaka tempdb under återskapningsåtgärder. Om du behöver mer tempdb-utrymme skalar du upp databaspoolen. Skala ned igen när indexet har återskapats.

För en tabell med ett ordnat grupperat `ALTER INDEX REORGANIZE` kolumnlagringsindex sorteras inte data på nytt. Om du vill sortera om data använder du `ALTER INDEX REBUILD` .

Mer information om sorterade grupperade kolumnlagringsindex finns i [Prestandajustering med ordnat grupperat columnstore-index.](performance-tuning-ordered-cci.md)

## <a name="causes-of-poor-columnstore-index-quality"></a>Causes of poor columnstore index quality (Orsaker till låg columnstore-indexkvalitet)

Om du har identifierat tabeller med dålig segmentkvalitet vill du identifiera rotorsaken.  Nedan visas några andra vanliga orsaker till dålig segmentkvalitet:

1. Minnestryck när indexet skapades
2. Hög volym av DML-åtgärder
3. Små eller knepiga inläsningsåtgärder
4. För många partitioner

Dessa faktorer kan göra att ett columnstore-index har betydligt mindre än de optimala 1 miljoner raderna per radgrupp. De kan också göra att rader går till deltaradgruppen i stället för en komprimerad radgrupp.

### <a name="memory-pressure-when-index-was-built"></a>Minnestryck när indexet skapades

Antalet rader per komprimerad radgrupp är direkt relaterat till bredden på raden och mängden tillgängligt minne för att bearbeta radgruppen.  När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  Därför är det bästa sättet att ge sessionen som skriver till kolumnlagringsindextabellerna åtkomst till så mycket minne som möjligt.  Eftersom det finns en avvägning mellan minne och samtidighet beror riktlinjerna för rätt minnesallokering på data i varje rad i tabellen, de informationslagerenheter som allokeras till systemet och antalet samtidighetsplatser som du kan ge till den session som skriver data till tabellen.

### <a name="high-volume-of-dml-operations"></a>Hög volym av DML-åtgärder

En stor mängd DML-åtgärder som uppdaterar och tar bort rader kan leda till ineffektivitet i kolumnlagringen. Detta gäller särskilt när de flesta raderna i en radgrupp ändras.

- Om du tar bort en rad från en komprimerad radgrupp, markerar du bara raden som borttagna logiskt. Raden finns kvar i den komprimerade radgruppen tills partitionen eller tabellen återskapas.
- När en rad infogas läggs raden till i en intern rowstore-tabell som kallas deltaradgrupp. Den infogade raden konverteras inte till columnstore förrän deltaradgruppen är full och har markerats som stängd. Radgrupper stängs när de når maxkapaciteten på 1 048 576 rader.
- Uppdatering av en rad i columnstore-format bearbetas som en logisk borttagning och sedan en infogning. Den infogade raden kan lagras i deltalagret.

Batchbaserade uppdaterings- och infogningsåtgärder som överskrider masströskelvärdet på 102 400 rader per partitionsjusterad distribution går direkt till columnstore-formatet. Om vi antar en jämn fördelning skulle du dock behöva ändra fler än 6,144 miljoner rader i en enda åtgärd för att detta ska ske. Om antalet rader för en viss partitionsjusterad distribution är mindre än 102 400 går raderna till deltalagret och stannar där tills tillräckligt många rader har infogats eller ändrats för att stänga radgruppen eller om indexet har återskapats.

### <a name="small-or-trickle-load-operations"></a>Små eller knepiga inläsningsåtgärder

Små belastningar som flödar till dedikerad SQL-pool kallas ibland för svårare belastningar. De representerar vanligtvis en nästan konstant dataström som matas in av systemet. Men eftersom den här dataströmmen nästan är kontinuerlig är mängden rader inte särskilt stor. Oftast ligger data betydligt under tröskelvärdet som krävs för en direkt inläsning till columnstore-format.

I sådana situationer är det ofta bättre att först landa data i Azure Blob Storage och låta dem ackumuleras före inläsningen. Den här tekniken kallas ofta *mikrobatchbearbetning.*

### <a name="too-many-partitions"></a>För många partitioner

En annan sak att tänka på är effekten av partitionering på dina grupperade columnstore-tabeller.  Före partitionering delar dedikerad SQL-pool redan in dina data i 60 databaser.  Partitionering delar upp dina data ytterligare.  Om du partitionera dina data bör du tänka **på** att varje partition behöver minst 1 miljon rader för att dra nytta av ett grupperat columnstore-index.  Om du partitionera tabellen i 100 partitioner behöver tabellen minst 6 miljarder rader för att kunna dra nytta av ett grupperat columnstore-index (60 *distributioner 100 partitioner* 1 miljon rader). Om din tabell med 100 partitioner inte har 6 miljarder rader kan du antingen minska antalet partitioner eller överväga att använda en heap-tabell i stället.

När dina tabeller har lästs in med vissa data följer du stegen nedan för att identifiera och återskapa tabeller med icke-optimala grupperade kolumnlagringsindex.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Återskapa index för att förbättra segmentkvaliteten

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Steg 1: Identifiera eller skapa användare som använder rätt resursklass

Ett snabbt sätt att omedelbart förbättra segmentkvaliteten är att återskapa indexet.  SQL som returneras av vyn ovan returnerar en ALTER INDEX REBUILD-instruktion som kan användas för att återskapa dina index. När du återskapar indexen måste du se till att allokera tillräckligt med minne till sessionen som återskapar ditt index. Det gör du genom att öka resursklassen för en användare som har behörighet att återskapa indexet i den här tabellen till det rekommenderade minimivärdet.

Nedan visas ett exempel på hur du allokerar mer minne till en användare genom att öka deras resursklass. Information om hur du arbetar med resursklasser finns [i Resursklasser för arbetsbelastningshantering.](resource-classes-for-workload-management.md)

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser';
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Steg 2: Återskapa grupperade columnstore-index med användare med högre resursklass

Logga in som användaren från steg 1 (LoadUser), som nu använder en högre resursklass och kör ALTER INDEX-isatserna. Se till att den här användaren har ALTER-behörighet till de tabeller där indexet återskapas. De här exemplen visar hur du återskapar hela kolumnlagringsindexet eller hur du återskapar en enskild partition. För stora tabeller är det mer praktiskt att återskapa index med en enda partition i taget.

I stället för att återskapa indexet kan du kopiera tabellen till en ny tabell med [hjälp av CTAS](sql-data-warehouse-develop-ctas.md). Vilket sätt är bäst? För stora mängder data är CTAS vanligtvis snabbare än [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). För mindre mängder data är ALTER INDEX enklare att använda och kräver inte att du växlar ut tabellen.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5;
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE);
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE);
```

Att återskapa ett index i en dedikerad SQL-pool är en offlineåtgärd.  Mer information om hur du återskapar index finns i avsnittet ALTER INDEX REBUILD i [Columnstore Indexes Defragmentation](/sql/relational-databases/indexes/columnstore-indexes-defragmentation?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)och [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Steg 3: Kontrollera att segmentkvaliteten för grupperade kolumnlager har förbättrats

Kör om frågan som identifierade en tabell med dålig segmentkvalitet och kontrollera att segmentkvaliteten har förbättrats.  Om segmentkvaliteten inte förbättras kan det vara så att raderna i tabellen är extra breda.  Överväg att använda en högre resursklass eller DWU när du återskapar dina index.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Återskapa index med CTAS och partitionsväxling

I det här exemplet [används CREATE TABLE CTAS-instruktionen (AS SELECT) och](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) partitionsväxling för att återskapa en tabellpartition.

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
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
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Switch IN the rebuilt data with TRUNCATE_TARGET option
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2 WITH (TRUNCATE_TARGET = ON);
```

Mer information om hur du återskapar partitioner med CTAS finns i [Använda partitioner i dedikerad SQL-pool.](sql-data-warehouse-tables-partition.md)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar tabeller finns i [Utveckla tabeller.](sql-data-warehouse-tables-overview.md)
