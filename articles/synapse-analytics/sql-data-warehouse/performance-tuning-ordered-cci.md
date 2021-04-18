---
title: Prestandajustering med grupperade kolumnlagringsindex
description: Rekommendationer och överväganden som du bör känna till när du använder sorterade grupperade columnstore-index för att förbättra frågeprestanda i dedikerade SQL-pooler.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/13/2021
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ab94a83a64ca9770f0c216ddf42145b262629c6d
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599000"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Prestandajustering med grupperade kolumnlagringsindex  

När användare frågar en columnstore-tabell i en dedikerad SQL-pool kontrollerar optimeraren de lägsta och högsta värdena som lagras i varje segment.  Segment som ligger utanför gränserna för fråge predikatet läses inte från disk till minne.  En fråga kan få snabbare prestanda om antalet segment som ska läsas och deras totala storlek är litet.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Sorterat kontra icke-ordnat grupperat kolumnlagringsindex

Som standard skapar en intern komponent (indexbyggare) ett icke-ordnat grupperat columnstore-index (CCI) för varje tabell som skapas utan ett indexalternativ.  Data i varje kolumn komprimeras till ett separat CCI-radgruppsegment.  Det finns metadata i varje segments värdeintervall, så segment som ligger utanför gränserna för fråge predikatet läses inte från disken under frågekörningen.  CCI erbjuder den högsta nivån av datakomprimering och minskar storleken på segment att läsa så att frågor kan köras snabbare. Men eftersom indexverktyget inte sorterar data innan de komprimeras till segment kan segment med överlappande värdeintervall uppstå, vilket gör att frågor läser fler segment från disken och tar längre tid att slutföra.  

När du skapar en ordnad CCI sorterar den dedikerade SQL-poolmotorn befintliga data i minnet efter ordernyckel(erna) innan indexverktyget komprimerar dem till indexsegment.  Med sorterade data minskar segment överlappande så att frågor får ett effektivare segmenteliminering och därmed snabbare prestanda eftersom antalet segment som ska läsas från disken är mindre.  Om alla data kan sorteras i minnet samtidigt kan du undvika segment överlappande data.  På grund av stora tabeller i informationslager sker det här scenariot inte ofta.  

Om du vill kontrollera segmentintervallen för en kolumn kör du följande kommando med tabellnamnet och kolumnnamnet:

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id;


```

> [!NOTE] 
> I en ordnad CCI-tabell sorteras nya data som kommer från samma batch med DML eller data inläsningsåtgärder i den batchen, det finns ingen global sortering över alla data i tabellen.  Användare kan återskapa den sorterade CCI:en för att sortera alla data i tabellen.  I dedikerad SQL-pool är columnstore-indexet REBUILD en offline-åtgärd.  För en partitionerad tabell görs REBUILD en partition i taget.  Data i partitionen som återskapas är "offline" och otillgängliga tills REBUILD har slutförts för den partitionen. 

## <a name="query-performance"></a>Frågeprestanda

En frågas prestandavinst från en ordnad CCI beror på frågemönstren, storleken på data, hur väl data sorteras, den fysiska strukturen för segment samt den DWU och resursklass som valts för frågekörningen.  Användarna bör granska alla dessa faktorer innan de väljer ordningskolumnerna när de utformar en ordnad CCI-tabell.

Frågor med alla dessa mönster körs vanligtvis snabbare med sorterad CCI.  
1. Frågorna har likhets-, olikhets- eller intervall predikat
1. Predikatkolumnerna och de ordnade CCI-kolumnerna är desamma.  
1. Predikatkolumnerna används i samma ordning som kolumnordningen för ordnade CCI-kolumner.  
 
I det här exemplet har tabell T1 ett grupperat columnstore-index sorterat i sekvensen Col_C, Col_B och Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A);

```

Prestanda för fråga 1 kan dra större nytta av sorterad CCI än de andra tre frågorna. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>Prestanda vid inläsning av data

Prestanda vid inläsning av data till en ordnad CCI-tabell liknar en partitionerad tabell.  Att läsa in data i en ordnad CCI-tabell kan ta längre tid än en icke-ordnad CCI-tabell på grund av datasorteringsåtgärden, men frågorna kan köras snabbare efteråt med sorterad CCI.  

Här är ett exempel på en prestandajämförelse av inläsning av data i tabeller med olika scheman.

![Stapeldiagram som visar prestandajämförelsen vid inläsning av data i tabeller med olika scheman.](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Här är ett exempel på en jämförelse av frågeprestanda mellan CCI och sorterad CCI.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Minska segment överlappande

Antalet överlappande segment beror på storleken på de data som ska sorteras, tillgängligt minne och max grad av parallellitet (MAXDOP) när CCI skapas. Nedan visas alternativ för att minska segment överlappande när du skapar sorterade CCI.

- Använd xlargerc-resursklassen på en högre DWU för att tillåta mer minne för datasortering innan indexverktyget komprimerar data i segment.  I ett indexsegment går det inte att ändra den fysiska platsen för data.  Det finns ingen datasortering inom ett segment eller mellan segment.  

- Skapa sorterad CCI med MAXDOP = 1.  Varje tråd som används för att skapa sorterade CCI-data fungerar på en delmängd av data och sorterar dem lokalt.  Det finns ingen global sortering över data sorterade efter olika trådar.  Att använda parallella trådar kan minska tiden för att skapa en ordnad CCI, men genererar mer överlappande segment än att använda en enda tråd.  För närvarande stöds maxdop-alternativet endast för att skapa en ordnad CCI-tabell med hjälp CREATE TABLE AS SELECT.  Att skapa en ordnad CCI via CREATE INDEX eller CREATE TABLE kommandon stöder inte alternativet MAXDOP. Exempel:

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- Sortera data i förväg efter sorteringsnyckel(er) innan du läser in dem i tabeller.

Här är ett exempel på en ordnad CCI-tabellfördelning som har noll segment som överlappar enligt rekommendationerna ovan. Den sorterade CCI-tabellen skapas i en DWU1000c-databas via CTAS från en heap-tabell på 20 GB med MAXDOP 1 och xlargerc.  CCI sorteras på en BIGINT-kolumn utan dubbletter.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Skapa sorterad CCI på stora tabeller

Att skapa en ordnad CCI är en offline-åtgärd.  För tabeller utan partitioner är data inte tillgängliga för användare förrän den ordnade CCI-skapandeprocessen har slutförts.   Eftersom motorn skapar den sorterade CCI-partitionen efter partition för partition för partitioner för partitioner, kan användarna fortfarande komma åt data i partitioner där det inte pågår någon ordnad CCI-generering.   Du kan använda det här alternativet för att minimera avbrottstiden när CCI skapas i stora tabeller: 

1.    Skapa partitioner på den stora måltabellen (kallas för Table_A).
2.    Skapa en tom sorterad CCI-tabell (kallas Table_B) med samma tabell- och partitionsschema som tabell A.
3.    Växla en partition från Tabell A till Tabell B.
4.    Kör ALTER INDEX <Ordered_CCI_Index> ON <Table_B> REBUILD PARTITION = <Partition_ID> på Tabell B för att återskapa den inkopplade partitionen.  
5.    Upprepa steg 3 och 4 för varje partition i Table_A.
6.    När alla partitioner växlas från Table_A till Table_B och har återskapats tar du bort Table_A och byter namn Table_B till Table_A. 

>[!TIP]
> För en dedikerad SQL-pooltabell med en ordnad CCI sorterar ALTER INDEX REBUILD om data med hjälp av tempdb. Övervaka tempdb under återskapningsåtgärder. Om du behöver mer tempdb-utrymme skalar du upp poolen. Skala ned igen när indexet har återskapats.
>
> För en dedikerad SQL-pooltabell med en ordnad CCI sorterar ALTER INDEX REORGANIZE inte om data. Använd ALTER INDEX REBUILD om du vill använda data.
>
> Mer information om ordnat CCI-underhåll finns [i Optimera grupperade kolumnlagringsindex.](sql-data-warehouse-tables-index.md#optimizing-clustered-columnstore-indexes)

## <a name="examples"></a>Exempel

**A. Så här söker du efter sorterade kolumner och ordningstal:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0;
```

**B. Om du vill ändra kolumnordningstal lägger du till eller tar bort kolumner från orderlistan eller ändrar från CCI till sorterad CCI:**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON dbo.InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON);
```

## <a name="next-steps"></a>Nästa steg

Fler utvecklingstips finns i [utvecklingsöversikten.](sql-data-warehouse-overview-develop.md)
