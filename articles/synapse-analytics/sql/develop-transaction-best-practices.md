---
title: Optimera transaktioner för dedikerad SQL-pool
description: Lär dig hur du optimerar prestandan för transaktions koden i en dedikerad SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 908f047a22491d50337f51c0a6dce7f2db8a2ebc
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97026820"
---
# <a name="optimize-transactions-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Optimera transaktioner med dedikerad SQL-pool i Azure Synapse Analytics 

Lär dig hur du optimerar prestandan för transaktions koden i en dedikerad SQL-pool och minimerar risken för långa återställningar.

## <a name="transactions-and-logging"></a>Transaktioner och loggning

Transaktioner är en viktig komponent i en Relations databas motor. Dedikerad SQL-pool använder transaktioner under data modifieringen. Dessa transaktioner kan vara explicita eller implicita. Ett enda INSERT-, UPDATE-och DELETE-uttryck är exempel på implicita transaktioner. Explicita transaktioner använder påbörja omstart, genomför omlägg eller Återställ omlastning. Explicita transaktioner används vanligt vis när flera ändrings instruktioner måste knytas ihop till en enda atomisk enhet.

Dedikerad SQL-pool genomför ändringar i databasen med transaktions loggar. Varje distribution har sin egen transaktions logg. Transaktions logg skrivningar är automatiskt. Ingen konfiguration krävs. Men även om den här processen garanterar Skriv åtgärden, introduceras en omkostnader i systemet. Du kan minimera den här effekten genom att skriva en transaktions effektiv kod. Transaktions effektiv kod i stort sett hamnar i två kategorier.

* Använd minimala loggnings konstruktioner när det är möjligt
* Bearbeta data med hjälp av omfångs batchar för att undvika att transaktioner med lång tid körs
* Använd ett partition växlings mönster för stora ändringar av en specifik partition

## <a name="minimal-vs-full-logging"></a>Minimal vs. fullständig loggning

Till skillnad från fullständigt loggade åtgärder, som använder transaktions loggen för att hålla reda på varje rad ändring, är minimalt inloggade åtgärder fortfarande spår över omfattnings tilldelningar och metadata ändringar. Därför innebär minimal loggning att endast logga den information som krävs för att återställa transaktionen efter ett haveri eller för en explicit begäran (återföring av återföring). Eftersom mycket mindre information spåras i transaktions loggen fungerar en minimalt loggad åtgärd bättre än en fullständigt loggad åtgärd på liknande sätt. Dessutom, eftersom färre skrivningar går till transaktions loggen, genereras en mycket mindre mängd loggdata och därför är mer I/O-effektivt.

Transaktionernas säkerhets gränser gäller endast fullständigt loggade åtgärder.

> [!NOTE]
> Minimalt inloggade åtgärder kan delta i explicita transaktioner. När alla ändringar i tilldelnings strukturerna spåras är det möjligt att återställa minimalt inloggade åtgärder.

## <a name="minimally-logged-operations"></a>Minimalt loggade åtgärder

Följande åtgärder kan vara minimalt loggade:

* CREATE TABLE som SELECT ([CTAs](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json))
* Infoga.. SELECT
* CREATE INDEX
* ÄNDRA INDEX ÅTERSKAPA
* DROP INDEX
* TRUNCATE TABLE
* DROP TABLE
* ÄNDRA PARTITION FÖR TABELL VÄXEL

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> Interna data förflyttnings åtgärder (t. ex. BROADCAST och blandad) påverkas inte av transaktionens säkerhets gräns.

## <a name="minimal-logging-with-bulk-load"></a>Minimal loggning med Mass inläsning

CTAS och infoga... SELECT är både Mass inläsnings åtgärder. Båda påverkas dock av mål tabell definitionen och är beroende av inläsnings scenariot. I följande tabell förklaras när Mass åtgärder är fullständigt eller minimalt loggade:  

| Primärt index | Läs in scenario | Loggnings läge |
| --- | --- | --- |
| Heap |Valfri |**Minimal** |
| Grupperat index |Tom mål tabell |**Minimal** |
| Grupperat index |Inlästa rader överlappar inte befintliga sidor i mål |**Minimal** |
| Grupperat index |Inlästa rader överlappar befintliga sidor i målet |Fullständig |
| Grupperat columnstore-index |Batchstorlek >= 102 400 per partition-justerad distribution |**Minimal** |
| Grupperat columnstore-index |Batchstorlek < 102 400 per partition anpassad distribution |Fullständig |

Det är värt att notera att alla skrivningar för att uppdatera sekundära eller icke-grupperade index alltid är fullständigt loggade.

> [!IMPORTANT]
> Dedikerad SQL-pool har 60-distributioner. Detta förutsätter att alla rader är jämnt distribuerade och landningar i en enda partition, men din batch måste innehålla 6 144 000 rader eller större för att få minimal inloggning vid skrivning till ett grupperat columnstore-index. Om tabellen är partitionerad och de rader som infogas sträcker sig över diskpartitioner, behöver du 6 144 000 rader per partition, förutsatt att du har till och med data distribution. Varje partition i varje distribution måste vara oberoende av gränsen på 102 400 rader för att infogningen ska vara minimalt inloggad i distributionen.

Inläsning av data i en icke-tom tabell med ett grupperat index kan ofta innehålla en blandning av fullständigt loggade och minimalt loggade rader. Ett grupperat index är ett balanserat träd (b-Tree) av sidor. Om sidan som skrivs till redan innehåller rader från en annan transaktion, kommer dessa skrivningar att loggas fullständigt. Men om sidan är tom loggas skrivningen till sidan av minimalt.

## <a name="optimize-deletes"></a>Optimera borttagningar

TA bort är en fullständigt loggad åtgärd.  Om du behöver ta bort en stor mängd data i en tabell eller partition är det ofta mer meningsfullt för `SELECT` de data som du vill behålla, vilket kan köras som en minimalt loggad åtgärd.  Om du vill välja data skapar du en ny tabell med [CTAs](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).  När du har skapat använder du [rename](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att byta ut den gamla tabellen med den tabell som skapats nyligen.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimize-updates"></a>Optimera uppdateringar

UPPDATERINGEN är en fullständigt loggad åtgärd.  Om du behöver uppdatera ett stort antal rader i en tabell eller partition, kan det ofta vara mycket mer effektivt att använda en minimalt loggad åtgärd, till exempel [CTAs](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) .

I exemplet nedan har en fullständig tabell uppdatering konverterats till en CTAS så att minimal loggning är möjlig.

I det här fallet lägger vi retroaktivt till ett rabatt belopp för försäljningen i tabellen:

```sql
--Step 01. Create a new table containing the "Update".
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS
SELECT
    [ProductKey]  
,    [OrderDateKey]
,    [DueDateKey]  
,    [ShipDateKey]
,    [CustomerKey]
,    [PromotionKey]
,    [CurrencyKey]
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber]
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> Att återskapa stora tabeller kan dra nytta av dedikerade funktioner för arbets belastnings hantering i SQL-pooler. Mer information finns i [resurs klasser för hantering av arbets belastning](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="optimize-with-partition-switching"></a>Optimera med partition växling

Om det står i stor skala i storskaliga ändringar i en [Table partition](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), är ett mönster för partitions växlingen begripligt. Om data ändringen är betydande och sträcker sig över flera partitioner, uppnås samma resultat om du går igenom partitionerna.

Stegen för att utföra en partitionsnyckel är följande:

1. Skapa en tom out-partition
2. Utför uppdateringen som en CTAS
3. Växla ut befintliga data till ut-tabellen
4. Växla till nya data
5. Rensa data

För att hjälpa till att identifiera de partitioner som ska växlas, skapar du följande hjälp procedur.  

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id]
                                                AND i.[index_id]        = p.[index_id]
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id]
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Med den här proceduren maximeras kod åter användning och du får ett exempel på partition växling mer kompakt.

Följande kod visar de steg som nämndes tidigare för att få en fullständig partitions växlings rutin.

```sql
--Create a partitioned aligned empty table to switch out the data
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT
    [ProductKey]  
,    [OrderDateKey]
,    [DueDateKey]  
,    [ShipDateKey]
,    [CustomerKey]
,    [PromotionKey]
,    [CurrencyKey]
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber]
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Minimera loggning med små batchar

För stora data ändringar kan det vara bra att dela upp åtgärden i segment eller batchar för att begränsa arbets enheten.

En följande kod är ett arbets exempel. Batchstorleken har angetts till ett trivialt nummer för att markera tekniken. I verkligheten skulle batchstorleken vara betydligt större.

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Guide för paus och skalning

Med Azure Synapse Analytics kan du [pausa, återuppta och skala](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) din DEDIKERADe SQL-pool på begäran. 

När du pausar eller skalar en dedikerad SQL-pool är det viktigt att förstå att alla pågående transaktioner avbryts omedelbart. orsaka att eventuella öppna transaktioner återställs. 

Om din arbets belastning har utfärdat en tids krävande och ofullständig data ändring innan paus-eller skalnings åtgärden, måste det här arbetet utföras. Detta kan påverka tiden det tar att pausa eller skala din dedikerade SQL-pool. 

> [!IMPORTANT]
> Både `UPDATE` och `DELETE` är fullständigt loggade och därmed kan dessa åtgärder för att ångra/upprepa ta betydligt längre tid än motsvarande minimalt loggade åtgärder.

Det bästa scenariot är att låta ändringar i Flight-datatransaktionerna slutföras innan du pausar eller skalar din dedikerade SQL-pool. Det här scenariot är dock inte alltid praktiskt. Överväg något av följande alternativ för att minska risken för en lång återställning:

* Skriv över tids krävande åtgärder med [CTAs](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)
* Bryt åtgärden i segment. körs på en delmängd av raderna

## <a name="next-steps"></a>Nästa steg

Mer information om isolerings nivåer och transaktionella gränser finns i [transaktioner i dedikerad SQL-pool](develop-transactions.md) .  En översikt över andra bästa metoder finns i [metod tips för SQL-pool](best-practices-sql-pool.md).
