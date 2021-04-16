---
title: Förbättra prestanda för kolumnlagringsindex för dedikerad SQL-pool
description: Minska minneskraven eller öka det tillgängliga minnet för att maximera antalet rader i varje radgrupp i dedikerad SQL-pool.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/22/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 1336359bdd0768ba1d1554554d266cacfb483a43
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566519"
---
# <a name="maximizing-rowgroup-quality-for-columnstore-indexes-in-dedicated-sql-pool"></a>Maximera radgruppskvaliteten för kolumnlagringsindex i dedikerad SQL-pool 

Radgruppskvaliteten bestäms av antalet rader i en radgrupp. Genom att öka det tillgängliga minnet kan du maximera antalet rader som ett columnstore-index komprimerar till varje radgrupp.  Använd dessa metoder för att förbättra komprimeringsfrekvensen och frågeprestanda för kolumnlagringsindex.

## <a name="why-the-rowgroup-size-matters"></a>Varför storleken på radgruppen är viktig

Eftersom ett columnstore-index genomsöker en tabell genom att genomsöka kolumnsegment i enskilda radgrupper, förbättrar maximerat antalet rader i varje radgrupp frågeprestanda.

När radgrupper har ett stort antal rader förbättras datakomprimeringen, vilket innebär att det finns mindre data att läsa från disken.

Mer information om radgrupper finns i [Guide för kolumnlagringsindex.](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="target-size-for-rowgroups"></a>Målstorlek för radgrupper

För bästa frågeprestanda är målet att maximera antalet rader per radgrupp i ett columnstore-index. En radgrupp kan ha högst 1 048 576 rader.

Det är okej att inte ha det maximala antalet rader per radgrupp. Kolumnlagringsindex ger bra prestanda när radgrupper har minst 100 000 rader.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Radgrupper kan trimmas under komprimering

Under massinläsning eller återskapande av columnstore-index finns ibland inte tillräckligt med minne för att komprimera alla rader som angetts för varje radgrupp. När minnestryck finns trimmar columnstore index radgruppernas storlek så att komprimering till columnstore kan lyckas.

Om det inte finns tillräckligt med minne för att komprimera minst 10 000 rader i varje radgrupp genereras ett fel.

Mer information om massinläsning finns i [Massinläsning till ett grupperat kolumnlagringsindex.](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="how-to-monitor-rowgroup-quality"></a>Så här övervakar du radgruppskvalitet

DMV-sys.dm_pdw_nodes_db_column_store_row_group_physical_stats ([sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) innehåller vydefinitionen som matchar SQL DB) som visar användbar information, till exempel antal rader i radgrupper och orsaken till trimningen, om det fanns trimning.

Du kan skapa följande vy som ett praktiskt sätt att fråga denna DMV för att få information om radgruppstrimning.

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

Den trim_reason_desc anger om radgruppen har trimmats(trim_reason_desc = NO_TRIM antyder att det inte fanns någon trimning och radgruppen har optimal kvalitet). Följande trimningsorsaker indikerar för tidig trimning av radgruppen:

- BULKLOAD: Den här trimningsorsaken används när den inkommande batchen med rader för belastningen hade mindre än 1 miljon rader. Motorn skapar komprimerade radgrupper om det finns fler än 100 000 rader som infogas (till skillnad från infogning i deltalagret) men anger trimningsorsaken till BULKLOAD. I det här scenariot bör du överväga att öka batchbelastningen så att den innehåller fler rader. Utvärdera också partitioneringsschemat för att se till att det inte är för detaljerad eftersom radgrupper inte kan sträcka sig över partitionsgränser.
- MEMORY_LIMITATION: För att skapa radgrupper med 1 miljon rader krävs en viss mängd arbetsminne av motorn. När tillgängligt minne för inläsningssessionen är mindre än det nödvändiga arbetsminnet, trimmas radgrupper i förtid. I följande avsnitt beskrivs hur du beräknar nödvändigt minne och allokerar mer minne.
- DICTIONARY_SIZE: Den här trimningsorsaken anger att radgruppstrimningen inträffade eftersom det fanns minst en strängkolumn med breda strängar och/eller strängar med hög kardinalitet. Ordlistestorleken är begränsad till 16 MB i minnet och när den här gränsen har nåtts komprimeras radgruppen. Om du får problem kan du överväga att isolera den problematiska kolumnen i en separat tabell.

## <a name="how-to-estimate-memory-requirements"></a>Så här beräknar du minneskraven

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

Det maximala minne som krävs för att komprimera en radgrupp är ungefär

- 72 MB +
- \#\* \# radkolumner \* 8 byte +
- \#rader \* \# short-string-columns \* 32 byte +
- \#long-string-columns \* 16 MB för komprimeringsordlista

> [!NOTE]
> Korta strängkolumner använder strängdatatyper på <= 32 byte och långa strängkolumner använder strängdatatyper på > 32 byte.

Långa strängar komprimeras med en komprimeringsmetod som utformats för att komprimera text. Den här komprimeringsmetoden *använder en ordlista* för att lagra textmönster. Den maximala storleken för en ordlista är 16 MB. Det finns bara en ordlista för varje lång strängkolumn i radgruppen.

En detaljerad beskrivning av minneskraven för columnstore finns i videon [Dedicated SQL pool scaling: configuration and guidance (Dedikerad SQL-poolskalning: konfiguration och vägledning).](https://channel9.msdn.com/Events/Ignite/2016/BRK3291)

## <a name="ways-to-reduce-memory-requirements"></a>Sätt att minska minneskraven

Använd följande metoder för att minska minneskraven för att komprimera radgrupper till kolumnlagringsindex.

### <a name="use-fewer-columns"></a>Använda färre kolumner

Om möjligt ska du utforma tabellen med färre kolumner. När en radgrupp komprimeras till columnstore komprimerar columnstore-indexet varje kolumnsegment separat.

Därför ökar minneskraven för att komprimera en radgrupp när antalet kolumner ökar.

### <a name="use-fewer-string-columns"></a>Använda färre strängkolumner

Kolumner med strängdatatyper kräver mer minne än numeriska datatyper och datumdatatyper. Överväg att ta bort strängkolumner från faktatabeller och placera dem i mindre dimensionstabeller för att minska minneskraven.

Ytterligare minneskrav för strängkomprimering:

- Strängdatatyper på upp till 32 tecken kan kräva ytterligare 32 byte per värde.
- Strängdatatyper med mer än 32 tecken komprimeras med hjälp av ordlistemetoder.  Varje kolumn i radgruppen kan kräva upp till ytterligare 16 MB för att skapa ordlistan.

### <a name="avoid-over-partitioning"></a>Undvik överpartitionering

Kolumnlagringsindex skapar en eller flera radgrupper per partition. För dedikerad SQL-pool Azure Synapse Analytics ökar antalet partitioner snabbt eftersom data distribueras och varje distribution partitioneras.

Om tabellen har för många partitioner kanske det inte finns tillräckligt med rader för att fylla radgrupperna. Bristen på rader skapar inte minnestryck under komprimering. Men det leder till radgrupper som inte uppnår bästa prestanda för columnstore-frågor.

Ett annat skäl till att undvika överpartitionering är att det finns en minneskostnad för att läsa in rader i ett columnstore-index på en partitionerad tabell.

Under en inläsning kan många partitioner ta emot de inkommande raderna, som finns i minnet tills varje partition har tillräckligt med rader för att komprimeras. Om du har för många partitioner ökar minnestrycket.

### <a name="simplify-the-load-query"></a>Förenkla belastningsfrågan

Databasen delar minnes grant för en fråga mellan alla operatorer i frågan. När en belastningsfråga har komplexa sorteringar och kopplingar minskas det tillgängliga minnet för komprimering.

Utforma belastningsfrågan så att den bara fokuserar på att läsa in frågan. Om du behöver köra transformationer på data kör du dem separat från inläsningsfrågan. Du kan till exempel mellanlagra data i en heap-tabell, köra transformeringarna och sedan läsa in mellanlagringstabellen i columnstore-indexet.

> [!TIP]
> Du kan också läsa in data först och sedan använda MPP-systemet för att transformera data.

### <a name="adjust-maxdop"></a>Justera MAXDOP

Varje distribution komprimerar radgrupper till columnstore parallellt när det finns fler än en processorkärna tillgänglig per distribution.

Parallellitet kräver ytterligare minnesresurser, vilket kan leda till minnestryck och radgruppstrimning.

Om du vill minska minnesbelastningen kan du använda MAXDOP-frågetipset för att tvinga inläsningsåtgärden att köras i serieläge inom varje distribution.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Sätt att allokera mer minne

DWU-storlek och användarresursklass avgör tillsammans hur mycket minne som är tillgängligt för en användarfråga.

Om du vill öka minnestilldelningen för en belastningsfråga kan du antingen öka antalet DWUs eller öka resursklassen.

- Om du vill öka DWUs, se [Hur gör jag för att skalningsprestanda?](quickstart-scale-compute-portal.md)
- Information om hur du ändrar resursklassen för en fråga finns i [Ändra ett exempel på användarresursklass.](resource-classes-for-workload-management.md#change-a-users-resource-class)

## <a name="next-steps"></a>Nästa steg

Fler sätt att förbättra prestanda för dedikerad SQL-pool finns i [Prestandaöversikt.](cheat-sheet.md)
