---
title: Indexerings tabeller
description: Rekommendationer och exempel för indexering av tabeller i dedikerad SQL-pool.
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
ms.openlocfilehash: fabbdf330d43737ffa85379f9cc4d5ac59c4a734
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673526"
---
# <a name="indexing-dedicated-sql-pool-tables-in-azure-synapse-analytics"></a>Indexera dedikerade SQL pool tabeller i Azure Synapse Analytics

Rekommendationer och exempel för indexering av tabeller i dedikerad SQL-pool.

## <a name="index-types"></a>Indextyper

Dedikerad SQL-pool erbjuder flera indexerings alternativ, inklusive [grupperade columnstore-index](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [grupperade index och icke-grupperade index](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)och ett icke-index-alternativ som också kallas [heap](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  

Information om hur du skapar en tabell med ett index finns i dokumentationen för [CREATE TABLE (dedikerad SQL-pool)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

## <a name="clustered-columnstore-indexes"></a>Grupperade columnstore-index

Dedicerad SQL-pool skapar som standard ett grupperat columnstore-index när inga index alternativ anges för en tabell. Grupperade columnstore-tabeller ger både den högsta nivån av data komprimering och bästa övergripande prestanda för frågor.  Grupperade columnstore-tabeller avsevärt vanligt vis grupperade index eller heap-tabeller och är vanligt vis det bästa valet för stora tabeller.  Därför är grupperat columnstore det bästa sättet att starta när du är osäker på hur du kan indexera tabellen.  

Om du vill skapa en grupperad columnstore-tabell ska du helt enkelt ange ett GRUPPERat COLUMNSTORE-INDEX i WITH-satsen eller lämna WITH-satsen avstängd:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Det finns några scenarier där grupperat columnstore inte kan vara ett lämpligt alternativ:

- Columnstore-tabeller stöder inte varchar (max), nvarchar (max) och varbinary (max). Betrakta heap eller grupperat index i stället.
- Columnstore-tabeller kan vara mindre effektiva för tillfälliga data. Betrakta heap och kanske till och med temporära tabeller.
- Små tabeller med färre än 60 000 000 rader. Beakta heap-tabeller.

## <a name="heap-tables"></a>Heap-tabeller

När du tillfälligt ställer in data i en dedikerad SQL-pool, kan det hända att du använder en heap-tabell för att göra den övergripande processen snabbare. Detta beror på att belastningar till heaps är snabbare än att indexera tabeller och i vissa fall kan den efterföljande läsningen göras från cachen.  Om du bara läser in data för att mellanlagra dem innan du kör fler transformationer går det mycket snabbare att läsa in tabellen till heap-tabellen än att läsa in data till en grupperad columnstore-tabell. Dessutom går det snabbare att läsa in data till en [temporär tabell](sql-data-warehouse-tables-temporary.md) än att läsa in en tabell till permanent lagring.  När data har lästs in kan du skapa index i tabellen för snabbare frågans prestanda.  

Kluster columnstore-tabeller börjar uppnå optimal komprimering när det finns fler än 60 000 000 rader.  För små uppslags tabeller, mindre än 60 000 000 rader, Överväg att använda HEAP eller grupperat index för snabbare frågeresultat. 

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

## <a name="clustered-and-nonclustered-indexes"></a>Klustrade och grupperade index

Grupperade index kan avsevärt grupperade columnstore-tabeller när en enskild rad behöver hämtas snabbt. För frågor där en enkel eller väldigt liten rad ökning krävs för att utföra extrema hastigheter, Överväg ett kluster index eller ett grupperat sekundärt index. Nack delen med att använda ett grupperat index är att bara frågor som förmånen är de som använder ett mycket selektivt filter i kolumnen grupperat index. För att förbättra filter i andra kolumner kan ett grupperat index läggas till i andra kolumner. Varje index som läggs till i en tabell lägger dock till både utrymme och bearbetnings tid som ska läsas in.

Skapa en grupperad index tabell genom att helt enkelt ange GRUPPERat INDEX i WITH-satsen:

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

Grupperade columnstore-tabeller ordnas i data i segment.  Att ha hög segment kvalitet är avgörande för att uppnå optimala prestanda för frågor i en columnstore-tabell.  Segment kvaliteten kan mätas med antalet rader i en komprimerad radgrupp.  Segment kvaliteten är mest optimal om det finns minst 100 000 rader per komprimerad radgrupp och får prestanda som antalet rader per rad grupp metod 1 048 576 rader, vilket är de flesta rader som en rad grupp kan innehålla.

Du kan skapa och använda vyn nedan för att beräkna de genomsnittliga raderna per rad grupp och identifiera eventuella under-optimala kluster columnstore-index.  Den sista kolumnen i den här vyn genererar ett SQL-uttryck som kan användas för att återskapa index.

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

Nu när du har skapat vyn kan du köra den här frågan för att identifiera tabeller med rad grupper med färre än 100 000 rader. Naturligtvis kanske du vill öka tröskelvärdet för 100 000 om du vill ha mer optimalt segment kvalitet.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

När du har kört frågan kan du börja titta på data och analysera resultaten. I den här tabellen beskrivs vad du letar efter i rad grupps analysen.

| Kolumn | Så här använder du dessa data |
| --- | --- |
| [table_partition_count] |Om tabellen är partitionerad kan du förväntas se att antalet öppna rad grupper är högre. Varje partition i distributionen kan i teorin ha en öppen rad grupp som är kopplad till den. Sätt faktorn i din analys. En liten tabell som har partitionerats kan optimeras genom att partitionering tas bort helt eftersom detta skulle förbättra komprimeringen. |
| [row_count_total] |Totalt antal rader för tabellen. Du kan till exempel använda det här värdet för att beräkna procent andelen av rader i det komprimerade läget. |
| [row_count_per_distribution_MAX] |Om alla rader är jämnt fördelade blir det här värdet mål antalet rader per distribution. Jämför det här värdet med compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Totalt antal rader i columnstore-format för tabellen. |
| [COMPRESSED_rowgroup_rows_AVG] |Om det genomsnittliga antalet rader är betydligt mindre än det maximala antalet rader för en radgrupp, kan du överväga att använda CTAS eller ALTER INDEX Rebuild för att komprimera om data |
| [COMPRESSED_rowgroup_count] |Antalet rad grupper i columnstore-format. Om det här talet är mycket högt i förhållande till tabellen är det en indikator för att columnstore-densiteten är låg. |
| [COMPRESSED_rowgroup_rows_DELETED] |Rader raderas logiskt i columnstore-format. Om talet är högt relaterat till tabell storlek kan du överväga att återskapa partitionen eller återskapa indexet eftersom det tar bort dem fysiskt. |
| [COMPRESSED_rowgroup_rows_MIN] |Använd det här tillsammans med kolumnen medel och MAX för att förstå värde intervallet för rad grupperna i ditt columnstore. Ett lågt tal över inläsnings tröskeln (102 400 per partitions justering) innebär att optimeringar är tillgängliga i data inläsningen |
| [COMPRESSED_rowgroup_rows_MAX] |Som ovan |
| [OPEN_rowgroup_count] |Öppna rad grupper är normala. Det skulle rimlig förväntas en öppen rad grupp per tabell distribution (60). För stora tal föreslås data inläsning mellan partitioner. Kontrol lera partitionerings strategin för att se till att den är ljud |
| [OPEN_rowgroup_rows] |Varje rad grupp kan ha 1 048 576 rader med högst. Använd det här värdet för att se hur alla öppna rad grupper för närvarande är |
| [OPEN_rowgroup_rows_MIN] |Öppna grupper visar att data antingen trickle läsas in i tabellen eller att den tidigare inläsningen spiller över återstående rader i den här rad gruppen. Använd de minsta, högsta, medel kolumnerna för att se hur mycket data som är söt i öppna rad grupper. För små tabeller kan det vara 100% av alla data! I vilket fall ändrar INDEX återuppbyggnad för att tvinga data att columnstore-data. |
| [OPEN_rowgroup_rows_MAX] |Som ovan |
| [OPEN_rowgroup_rows_AVG] |Som ovan |
| [CLOSED_rowgroup_rows] |Titta på de stängda rad grupps raderna som en Sanity-kontroll. |
| [CLOSED_rowgroup_count] |Antalet stängda rad grupper bör vara lågt om alla visas. Stängda rad grupper kan konverteras till komprimerade rad grupper med hjälp av ALTER INDEX... Omorganisera kommando. Detta är dock normalt inte obligatoriskt. Stängda grupper konverteras automatiskt till columnstore-gruppgrupper efter bakgrunden "tupel/process". |
| [CLOSED_rowgroup_rows_MIN] |Stängda rad grupper bör ha en mycket hög fyllnings hastighet. Om fyllnings hastigheten för en stängd rad grupp är låg, krävs ytterligare analys av columnstore. |
| [CLOSED_rowgroup_rows_MAX] |Som ovan |
| [CLOSED_rowgroup_rows_AVG] |Som ovan |
| [Rebuild_Index_SQL] |SQL för att återskapa columnstore-index för en tabell |

## <a name="causes-of-poor-columnstore-index-quality"></a>Causes of poor columnstore index quality (Orsaker till låg columnstore-indexkvalitet)

Om du har identifierat tabeller med dålig segment kvalitet vill du identifiera rotor saken.  Nedan visas några andra vanliga orsaker till dålig segment kvalitet:

1. Minnes belastning när index har skapats
2. Hög mängd DML-åtgärder
3. Små eller trickle inläsnings åtgärder
4. För många partitioner

Dessa faktorer kan orsaka ett columnstore-index som är betydligt mindre än de optimala 1 000 000 raderna per rad grupp. De kan också göra att rader går till delta-rad gruppen i stället för en komprimerad radgrupp.

### <a name="memory-pressure-when-index-was-built"></a>Minnes belastning när index har skapats

Antalet rader per komprimerad radgrupp är direkt relaterat till radens bredd och mängden minne som är tillgängligt för bearbetning av rad gruppen.  När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  Därför är det bästa sättet att ge sessionen som skriver till dina columnstore-index-tabeller åtkomst till så mycket minne som möjligt.  Eftersom det finns en kompromiss mellan minne och samtidighet beror rikt linjerna för den rätt minnesallokering på data i varje rad i tabellen, de data lager enheter som allokeras till systemet och antalet samtidiga platser som du kan ge till sessionen som skriver data till din tabell.

### <a name="high-volume-of-dml-operations"></a>Hög mängd DML-åtgärder

En stor mängd DML-åtgärder som uppdaterar och raderar rader kan leda till ineffektivitet i columnstore. Detta gäller särskilt när majoriteten av raderna i en rad grupp ändras.

- När du tar bort en rad från en komprimerad rad grupp markeras bara raden som borttagen. Raden finns kvar i den komprimerade rad gruppen tills partitionen eller tabellen har återskapats.
- Om du infogar en rad läggs raden till i en intern rowstore-tabell som kallas delta rad grupp. Den infogade raden konverteras inte till columnstore förrän delta-rad gruppen är full och har marker ATS som stängd. Rad grupper stängs när de når Max kapaciteten på 1 048 576 rader.
- Uppdatering av en rad i columnstore-format bearbetas som logisk borttagning och sedan en infogning. Den infogade raden kan lagras i delta arkivet.

Batch-uppdatering och infognings åtgärder som överskrider Mass tröskelvärdet på 102 400 rader per partitionerad distribution går direkt till columnstore-formatet. Men om du antar en jämn fördelning måste du ändra fler än 6 144 000 rader i en enda åtgärd för att detta ska inträffa. Om antalet rader för en angiven partitions justerad distribution är mindre än 102 400, går raderna till delta Store och stannar där tills tillräckliga rader har infogats eller ändrats för att stänga rad gruppen eller så har indexet återskapats.

### <a name="small-or-trickle-load-operations"></a>Små eller trickle inläsnings åtgärder

Små belastningar som flödar till en dedikerad SQL-pool kallas ibland även trickle-belastningar. De representerar vanligt vis en nära konstant data ström som matas in av systemet. Men eftersom den här data strömmen är nära kontinuerlig, är volymen av rader inte särskilt stor. Oftare än att data är betydligt under tröskelvärdet som krävs för direkt inläsning till columnstore-format.

I dessa fall är det ofta bättre att fylla i data först i Azure Blob Storage och låta dem ackumuleras innan de läses in. Den här tekniken kallas ofta för *Micro batching*.

### <a name="too-many-partitions"></a>För många partitioner

En annan sak att överväga är effekten av partitionering i dina grupperade columnstore-tabeller.  Före partitionering delar den dedikerade SQL-poolen redan dina data i 60-databaser.  Om du partitionerar ytterligare delas dina data upp.  Om du partitionerar dina data måste du ta hänsyn till att **varje** partition behöver minst 1 000 000 rader för att dra nytta av ett grupperat columnstore-index.  Om du partitionerar din tabell i 100 partitioner behöver din tabell minst 6 000 000 000 rader för att dra nytta av ett grupperat columnstore-index (60-distributioner *100 partitioner* 1 000 000 rader). Om din 100-partitionstabell inte har 6 000 000 000-rader kan du antingen minska antalet partitioner eller överväga att använda en heap-tabell i stället.

När dina tabeller har lästs in med vissa data följer du stegen nedan för att identifiera och återskapa tabeller med under optimalt grupperade columnstore-index.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Återskapa index för att förbättra segment kvaliteten

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Steg 1: identifiera eller skapa en användare som använder rätt resurs klass

Ett snabbt sätt att snabbt förbättra segment kvaliteten är att återskapa indexet.  SQL som returnerades av vyn ovan returnerar ett instruktionen ALTER INDEX Rebuild som kan användas för att återskapa index. När du återskapar indexen bör du se till att du allokerar tillräckligt med minne till sessionen som återskapar indexet.  Det gör du genom att öka resurs klassen för en användare som har behörighet att återskapa indexet för den här tabellen till det rekommenderade minimivärdet.

Nedan visas ett exempel på hur du allokerar mer minne till en användare genom att öka deras resurs klass. Information om hur du arbetar med resurs klasser finns i [resurs klasser för hantering av arbets belastning](resource-classes-for-workload-management.md).

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Steg 2: återskapa grupperade columnstore-index med högre resurs klass användare

Logga in som användaren från steg 1 (t. ex. LoadUser), som nu använder en högre resurs klass och kör ALTER INDEX-instruktionerna. Se till att användaren har behörigheten Ändra för tabellerna där indexet återskapas. I de här exemplen visas hur du återskapar hela columnstore-indexet eller hur du återskapar en enda partition. I stora tabeller är det mer praktiskt att återskapa index för en enda partition i taget.

Alternativt kan du i stället för att återskapa indexet kopiera tabellen till en ny tabell [med hjälp av CTAs](sql-data-warehouse-develop-ctas.md). Vilket sätt är bäst? För stora mängder data är CTAS ofta snabbare än [Alter index](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). För mindre data volymer är det enklare att använda ALTER INDEX och du behöver inte byta ut tabellen.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Återskapande av ett index i en dedikerad SQL-pool är en offline-åtgärd.  Mer information om hur du återskapar index finns i avsnittet ALTER INDEX Rebuild i [columnstore-index defragmentering](/sql/relational-databases/indexes/columnstore-indexes-defragmentation?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)och [ändra index](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Steg 3: kontrol lera att den klustrade columnstore-segmentets kvalitet har förbättrats

Kör frågan som identifierade tabellen med dålig segment kvalitet och kontrol lera att segment kvaliteten har förbättrats.  Om segment kvaliteten inte förbättrades kan det bero på att raderna i tabellen är extra breda.  Överväg att använda en högre resurs klass eller DWU när du återskapar dina index.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Återskapa index med CTAS och partition växling

I det här exemplet används instruktionen [CREATE TABLE as Select (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) och partition byte för att återskapa en table-partition.

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

Mer information om att skapa partitioner på nytt med hjälp av CTAS finns i [använda partitioner i dedikerad SQL-pool](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar tabeller finns i [utveckla tabeller](sql-data-warehouse-tables-overview.md).
