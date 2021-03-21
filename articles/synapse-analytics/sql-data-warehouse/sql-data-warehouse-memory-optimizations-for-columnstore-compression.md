---
title: Förbättra columnstore-indexets prestanda för dedikerad SQL-pool
description: Minska minnes kraven eller öka det tillgängliga minnet för att maximera antalet rader i varje radgrupps i en dedikerad SQL-pool.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/22/2019
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: b15f71698af8c340b58f3399390857790313b8ac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585493"
---
# <a name="maximizing-rowgroup-quality-for-columnstore-indexes-in-dedicated-sql-pool"></a>Maximera radgrupps-kvalitet för columnstore-index i dedikerad SQL-pool 

Radgrupps-kvaliteten bestäms av antalet rader i en radgrupps. Att öka det tillgängliga minnet kan maximera antalet rader ett columnstore-index komprimeras till varje radgrupps.  Använd dessa metoder för att förbättra komprimerings hastigheten och fråga om prestanda för columnstore-index.

## <a name="why-the-rowgroup-size-matters"></a>Varför radgrupps storlek

Eftersom ett columnstore-index skannar en tabell genom att söka igenom kolumn segmenten i enskilda högkvalitativa, ökar antalet rader i varje radgrupps, vilket ökar frågans prestanda.

När högkvalitativa har ett stort antal rader förbättrar data komprimeringen vilket innebär att det finns mindre data att läsa från disken.

Mer information om högkvalitativa finns i [instruktions guide för columnstore-index](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="target-size-for-rowgroups"></a>Mål storlek för högkvalitativa

För bästa prestanda för frågor är målet att maximera antalet rader per radgrupps i ett columnstore-index. En radgrupps kan ha högst 1 048 576 rader.

Det går inte att använda maximalt antal rader per radgrupps. Columnstore-index uppnår höga prestanda när högkvalitativa har minst 100 000 rader.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Högkvalitativa kan bli putsad under komprimering

Ibland finns det inte tillräckligt med minne för att komprimera alla rader som har angetts för varje radgrupps under en återuppbyggnad av Mass inläsning eller columnstore-index. När minnes trycket är närvarande, trimmar columnstore-index radgrupps storlekarna så att komprimeringen kan lyckas.

Om det inte finns tillräckligt med minne för att komprimera minst 10 000 rader till varje radgrupps genereras ett fel.

Mer information om Mass inläsning finns i [Mass inläsning till ett grupperat columnstore-index](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="how-to-monitor-rowgroup-quality"></a>Så här övervakar du radgrupps-kvalitet

DMV-sys.dm_pdw_nodes_db_column_store_row_group_physical_stats ([sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) innehåller den visnings definition som matchar SQL DB) som visar användbar information, till exempel antalet rader i högkvalitativa och orsaken till trimning, om det trimmades.

Du kan skapa följande vy som ett praktiskt sätt att fråga denna DMV för att få information om radgrupps trimning.

```sql
create view dbo.vCS_rg_physical_stats
as
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]
)
select *
from cte;
```

Trim_reason_desc anger om radgrupps har trimmats (trim_reason_desc = NO_TRIM betyder det att det inte finns någon trimning och rad grupp är av optimal kvalitet). Följande trimnings orsaker visar för tidig trimning av radgrupps:

- BULKLOAD: den här trimnings orsaken används när den inkommande gruppen med rader för inläsningen hade färre än 1 000 000 rader. Motorn skapar komprimerade rad grupper om fler än 100 000 rader infogas (i stället för att infoga i delta arkivet), men anger trimnings orsaken till BULKLOAD. I det här scenariot kan du öka batch-belastningen för att ta med fler rader. Du kan också utvärdera om partitionerings schema för att se till att det inte är för detaljerat eftersom rad grupper inte kan spänna över partitionerings gränser.
- MEMORY_LIMITATION: om du vill skapa rad grupper med 1 000 000 rader krävs en viss mängd arbets minne av motorn. När det tillgängliga minnet för inläsnings sessionen är mindre än det nödvändiga arbets minnet, så får rad grupper för tidigt trimning. I följande avsnitt beskrivs hur du beräknar nödvändigt minne och allokerar mer minne.
- DICTIONARY_SIZE: den här trimnings orsaken indikerar att radgrupps trimning utfördes eftersom det fanns minst en sträng kolumn med breda och/eller höga kardinalation-strängar. Ord listans storlek är begränsad till 16 MB i minnet och när den här gränsen nås är rad gruppen komprimerad. Om du stöter på den här situationen bör du överväga att isolera den problematiska kolumnen i en separat tabell.

## <a name="how-to-estimate-memory-requirements"></a>Beräkna minnes krav

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

Det maximala minne som krävs för att komprimera en radgrupps är ungefär

- 72 MB +
- \#rader \* \# kolumner \* 8 byte +
- \#rader \* \# korta-sträng-kolumner \* 32 byte +
- \#långa sträng kolumner \* 16 MB för komprimerings ord lista

> [!NOTE]
> Short-String-columns använder sträng data typer för <= 32 byte och Long-String-columns använder sträng data typer för > 32 byte.

Långa strängar komprimeras med en komprimerings metod som är utformad för komprimering av text. Den här komprimerings metoden använder en *ord lista* för att lagra text mönster. Den maximala storleken för en ord lista är 16 MB. Det finns bara en ord lista för varje lång sträng kolumn i radgrupps.

En djupgående Beskrivning av kraven för columnstore-minnet finns i skalning för videon [dedikerad SQL-pool: konfiguration och vägledning](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Sätt att minska minnes kraven

Använd följande tekniker för att minska minnes kraven för att komprimera högkvalitativa till columnstore-index.

### <a name="use-fewer-columns"></a>Använd färre kolumner

Om möjligt kan du utforma tabellen med färre kolumner. När en radgrupps komprimeras till columnstore-filen komprimerar columnstore-indexet varje kolumn segment separat.

Det innebär att minnes kraven för att komprimera en radgrupps ökar när antalet kolumner ökar.

### <a name="use-fewer-string-columns"></a>Använd färre sträng kolumner

Kolumner med sträng data typer kräver mer minne än data typerna numeriska och datum. Överväg att minska minnes kraven genom att ta bort sträng kolumner från fakta tabeller och placera dem i mindre dimensions tabeller.

Ytterligare minnes krav för sträng komprimering:

- Sträng data typer på upp till 32 tecken kan kräva 32 ytterligare byte per värde.
- Sträng data typer med mer än 32 tecken komprimeras med hjälp av ordboks metoder.  Varje kolumn i radgrupps kan kräva upp till ytterligare 16 MB för att skapa ord listan.

### <a name="avoid-over-partitioning"></a>Undvik överpartitionering

Columnstore-index skapar en eller flera högkvalitativa per partition. För dedikerad SQL-pool i Azure Synapse Analytics ökar antalet partitioner snabbt eftersom data distribueras och varje distribution är partitionerad.

Om tabellen har för många partitioner kanske det inte finns tillräckligt med rader för att fylla i högkvalitativa. Bristen på rader skapar inte minnes belastning under komprimeringen. Men det leder till högkvalitativa som inte uppnår prestanda för bästa columnstore-fråga.

Ett annat skäl att undvika överpartitionering är att det finns en minnes belastning för att läsa in rader i ett columnstore-index i en partitionerad tabell.

Under en belastning kan många partitioner ta emot inkommande rader, som lagras i minnet tills varje partition har tillräckligt med rader som ska komprimeras. Om du har för många partitioner skapas ytterligare minnes belastning.

### <a name="simplify-the-load-query"></a>Förenkla inläsnings frågan

Databasen delar minnes tilldelningen för en fråga bland alla operatorer i frågan. När en inläsnings fråga har komplexa sorteringar och kopplingar, minskas mängden tillgängligt minne för komprimering.

Utforma inläsnings frågan för att endast fokusera på inläsning av frågan. Om du behöver köra transformeringar av data kan du köra dem separat från inläsnings frågan. Du kan till exempel mellanlagra data i en heap-tabell, köra transformeringarna och läsa in mellanlagringsplatsen i columnstore-indexet.

> [!TIP]
> Du kan också läsa in data först och sedan använda MPP-systemet för att transformera data.

### <a name="adjust-maxdop"></a>Justera MAXDOP

Varje distribution komprimerar högkvalitativa till columnstore parallellt när det finns fler än en processor kärna tillgänglig per distribution.

För parallellitet krävs ytterligare minnes resurser, vilket kan leda till minnes belastning och radgrupps trimning.

Du kan minska minnes belastningen genom att använda MAXDOP-frågetipset för att tvinga inläsnings åtgärden att köras i serie läge i varje distribution.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Sätt att allokera mer minne

DWU storlek och användar resurs klass avgör hur mycket minne som är tillgängligt för en användar fråga.

Om du vill öka minnes tilldelningen för en inläsnings fråga kan du antingen öka antalet DWU: er eller öka resurs klassen.

- Om du vill öka DWU: er kan du läsa mer i [Hur gör jag för att skalnings prestanda?](quickstart-scale-compute-portal.md)
- Information om hur du ändrar resurs klassen för en fråga finns i avsnittet [ändra en användar resurs klass exempel](resource-classes-for-workload-management.md#change-a-users-resource-class).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan förbättra prestandan för dedikerad SQL-pool finns i [Översikt över prestanda](cheat-sheet.md).
