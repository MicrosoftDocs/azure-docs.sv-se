---
title: In-Memory exempel
description: Försök Azure SQL Database In-Memory tekniker med OLTP-och columnstore-exempel.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: b5a1035f8a213a6ce02dd3252ff7d3ddea46faf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92786589"
---
# <a name="in-memory-sample"></a>In-Memory exempel
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Med In-Memory tekniker i Azure SQL Database kan du förbättra programmets prestanda och eventuellt minska kostnaderna för din databas. Genom att använda In-Memory tekniker i Azure SQL Database kan du uppnå prestanda förbättringar med olika arbets belastningar.

I den här artikeln visas två exempel som illustrerar användningen av In-Memory OLTP, samt columnstore-index i Azure SQL Database.

Mer information finns i:

- [Översikt över minnes intern OLTP och användnings scenarier](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) (innehåller referenser till kund fallstudier och information för att komma igång)
- [Dokumentation för In-Memory OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Guide för columnstore-index](/sql/relational-databases/indexes/columnstore-indexes-overview)
- Hybrid transaktions-/analys bearbetning (HTAP), även kallat [drift analys i real tid](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Installera In-Memory OLTP-exemplet

Du kan skapa AdventureWorksLT-exempel databasen med några få klick i [Azure Portal](https://portal.azure.com/). Sedan förklarar stegen i det här avsnittet hur du kan utöka din AdventureWorksLT-databas med In-Memory OLTP-objekt och demonstrera prestanda för delar.

För en mer förenklad, men mer visuellt tilltalande prestanda demonstration för In-Memory OLTP, se:

- Version: [in-memory-OLTP-demo-v 1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Källkod: [InMemory-OLTP-demo-källkod](https://github.com/microsoft/sql-server-samples/tree/master/samples/features/in-memory-database)

### <a name="installation-steps"></a>Installationssteg

1. I [Azure Portal](https://portal.azure.com/)skapar du en Premium-eller affärskritisk-databas på en server. Ange **källan** till AdventureWorksLT-exempel databasen. Detaljerade anvisningar finns [i skapa din första databas i Azure SQL Database](database/single-database-create-quickstart.md).

2. Anslut till databasen med SQL Server Management Studio [(SSMS.exe)](/sql/ssms/download-sql-server-management-studio-ssms).

3. Kopiera [InMemory OLTP Transact-SQL-skriptet](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) till Urklipp. T-SQL-skriptet skapar nödvändiga In-Memory objekt i AdventureWorksLT-exempel databasen som du skapade i steg 1.

4. Klistra in T-SQL-skriptet i SSMS och kör skriptet. `MEMORY_OPTIMIZED = ON`Satsen CREATE TABLE satser är viktiga. Exempel:

```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```

### <a name="error-40536"></a>Fel 40536

Om du får fel 40536 när du kör T-SQL-skriptet kör du följande T-SQL-skript för att kontrol lera om databasen har stöd för minnes intern:

```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```

Resultatet **0** innebär att In-Memory inte stöds och **1** innebär att det stöds. För att diagnosticera problemet kontrollerar du att databasen finns på nivån Premium-tjänst.

### <a name="about-the-created-memory-optimized-items"></a>Om de färdiga minnesoptimerade objekten

**Tabeller**: exemplet innehåller följande minnesoptimerade tabeller:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo. DemoSalesOrderHeaderSeed
- Demo. DemoSalesOrderDetailSeed

Du kan granska minnesoptimerade tabeller genom **Object Explorer** i SSMS. Högerklicka på **tabeller**  >  **filter**  >  **filter inställningar**  >  **är** minnesoptimerade. Värdet är lika med 1.

Du kan också fråga i katalogvyer, till exempel:

```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```

**Internt kompilerad lagrad procedur**: du kan kontrol lera SalesLT.usp_InsertSalesOrder_inmem via en katalog visnings fråga:

```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```

&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Kör exempel-OLTP-arbetsbelastningen

Den enda skillnaden mellan följande två *lagrade procedurer* är att den första proceduren använder minnesoptimerade versioner av tabellerna, medan den andra proceduren använder vanliga disk tabeller:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**

I det här avsnittet får du se hur du använder det praktiska **ostress.exe** verktyget för att köra de två lagrade procedurerna vid stressade nivåer. Du kan jämföra hur lång tid det tar för de två stressarna att slutföras.

När du kör ostress.exe rekommenderar vi att du skickar parameter värden som är utformade för båda följande:

- Kör ett stort antal samtidiga anslutningar med hjälp av-N100.
- Ha varje anslutnings slinga hundratals gånger, genom att använda-R500.

Men du kanske vill starta med mycket mindre värden som-N10 och-R50 för att säkerställa att allt fungerar.

### <a name="script-for-ostressexe"></a>Skript för ostress.exe

I det här avsnittet visas T-SQL-skriptet som är inbäddat i vår ostress.exe kommando rad. Skriptet använder objekt som har skapats av T-SQL-skriptet som du installerade tidigare.

Följande skript infogar en exempel försäljnings order med fem rad objekt i följande minnesoptimerade *tabeller*:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem

```sql
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```

Om du vill göra *_ondisk* versionen av föregående T-SQL-skript för ostress.exe ersätter du båda förekomsterna av den *_inmem* under strängen med *_ondisk*. Dessa ersättningar påverkar namnen på tabeller och lagrade procedurer.

#### <a name="install-rml-utilities-and-ostress"></a>Installera RML-verktyg och `ostress`

Vi rekommenderar att du planerar att köra ostress.exe på en virtuell Azure-dator (VM). Du skapar en [virtuell Azure-dator](https://azure.microsoft.com/documentation/services/virtual-machines/) i samma Azure-geografiska region där din AdventureWorksLT-databas finns. Men du kan köra ostress.exe på din bärbara dator i stället.

På den virtuella datorn, eller på vilken värd du väljer, installerar du RML-verktygen (Replay Markup Language). Verktygen är ostress.exe.

Mer information finns i:

- ostress.exe diskussion i [exempel databasen för In-Memory OLTP](/sql/relational-databases/in-memory-oltp/sample-database-for-in-memory-oltp).
- [Exempel databas för In-Memory OLTP](/sql/relational-databases/in-memory-oltp/sample-database-for-in-memory-oltp).
- [Bloggen för att installera ostress.exe](https://techcommunity.microsoft.com/t5/sql-server-support/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql/ba-p/317910).

<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->

#### <a name="run-the-_inmem-stress-workload-first"></a>Kör *_inmem* stress-arbetsbelastningen först

Du kan använda ett *RML cmd-kommandotolk* -fönster för att köra vår ostress.exe kommando rad. Kommando rads parametrarna dirigerar `ostress` till:

- Kör 100 anslutningar samtidigt (-N100).
- Varje anslutning kör T-SQL-skriptet 50 gånger (-R50).

```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```

Så här kör du föregående ostress.exe kommando rad:

1. Återställ data innehållet i databasen genom att köra följande kommando i SSMS för att ta bort alla data som infogades av tidigare körningar:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Kopiera texten från föregående ostress.exe kommando rad till Urklipp.

3. Ersätt `<placeholders>` för parametrarna-S-U-P-d med rätt verkliga värden.

4. Kör den redigerade kommando raden i ett RML cmd-fönster.

#### <a name="result-is-a-duration"></a>Resultatet är en varaktighet

När du `ostress.exe` är klar skrivs körnings tiden som den sista raden i utdata i RML cmd-fönstret. Till exempel, en kortare test körning senast 1,5 minuter:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`

#### <a name="reset-edit-for-_ondisk-then-rerun"></a>Återställ, redigera för *_ondisk* och kör sedan

När du har fått resultatet från *_inmem* kör utför du följande steg för *_ondisk* kör:

1. Återställ databasen genom att köra följande kommando i SSMS för att ta bort alla data som infogades av föregående körning:

   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Redigera ostress.exe kommando rad för att ersätta alla *_inmem* med *_ondisk*.

3. Kör ostress.exe igen för den andra gången och avbilda varaktighets resultatet.

4. Återställ databasen igen (för att ett ansvarsfullt sätt ta bort vad som kan vara en stor mängd test data).

#### <a name="expected-comparison-results"></a>Förväntade jämförelse resultat

Våra In-Memory-tester har visat att prestanda förbättras med **nio gånger** för den här förenklad-arbetsbelastningen, med `ostress` körning på en virtuell Azure-dator i samma Azure-region som databasen.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Installera In-Memory Analytics-exemplet

I det här avsnittet ska du jämföra IO-och statistik resultaten när du använder ett columnstore-index jämfört med ett traditionellt b-träd index.

För real tids analys på en OLTP-arbets belastning är det ofta bäst att använda ett grupperat columnstore-index. Mer information finns i [columnstore-index som beskrivs](/sql/relational-databases/indexes/columnstore-indexes-overview).

### <a name="prepare-the-columnstore-analytics-test"></a>Förbereda columnstore Analytics-testet

1. Använd Azure Portal för att skapa en ny AdventureWorksLT-databas från exemplet.
   - Använd det exakta namnet.
   - Välj en Premium service-nivå.

2. Kopiera [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) till Urklipp.
   - T-SQL-skriptet skapar nödvändiga In-Memory objekt i AdventureWorksLT-exempel databasen som du skapade i steg 1.
   - Skriptet skapar dimensions tabellen och två fakta tabeller. Fakta tabellerna fylls med 3 500 000 rader.
   - Skriptet kan ta 15 minuter att slutföra.

3. Klistra in T-SQL-skriptet i SSMS och kör skriptet. Nyckelordet **COLUMNSTORE** i **create index** -instruktionen är viktigt, som i:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Ange AdventureWorksLT till kompatibilitetsnivå 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Nivå 130 är inte direkt relaterad till In-Memory funktioner. Men nivå 130 ger vanligt vis snabbare prestanda för frågor än 120.

#### <a name="key-tables-and-columnstore-indexes"></a>Nyckel tabeller och columnstore-index

- dbo. FactResellerSalesXL_CCI är en tabell som har ett grupperat columnstore-index, som har avancerad komprimering på *data* nivån.

- dbo. FactResellerSalesXL_PageCompressed är en tabell som har ett motsvarande vanligt grupperat index, som bara komprimeras på *sidnivå* .

#### <a name="key-queries-to-compare-the-columnstore-index"></a>Viktiga frågor för att jämföra columnstore-indexet

Det finns [flera typer av T-SQL-frågetyper som du kan köra](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql) för att se prestanda förbättringar. I steg 2 i T-SQL-skriptet, bör du tänka på det här paret av frågor. De skiljer sig bara åt på en rad:

- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`

Ett grupperat columnstore-index finns i FactResellerSalesXL \_ CCI-tabellen.

Följande T-SQL script-utdrag skriver ut statistik för IO och tid för frågan för varje tabell.

```sql
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

I en databas med P2-pris nivån kan du förväntar dig nio gånger prestanda vinsten för den här frågan genom att använda det grupperade columnstore-indexet jämfört med det traditionella indexet. Med p15 kan du vänta ungefär 57 gånger prestanda genom att använda columnstore-indexet.

## <a name="next-steps"></a>Nästa steg

- [Snabb start 1: In-Memory OLTP-teknik för snabbare T-SQL-prestanda](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp)

- [Använda In-Memory OLTP i ett befintligt Azure SQL-program](in-memory-oltp-configure.md)

- [Övervaka In-Memory OLTP-lagring](in-memory-oltp-monitor-space.md) för In-Memory OLTP

## <a name="additional-resources"></a>Ytterligare resurser

### <a name="deeper-information"></a>Djupare information

- [Lär dig hur kvorum dubblerar nyckel databasens arbets belastning och sänker DTU med 70% med In-Memory OLTP i Azure SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Minnes intern OLTP i Azure SQL Database blogg inlägg](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Lär dig mer om In-Memory OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)

- [Lär dig mer om columnstore-index](/sql/relational-databases/indexes/columnstore-indexes-overview)

- [Lär dig mer om operativa analyser i real tid](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

- Se [vanliga arbets belastnings mönster och överväganden vid migrering](/previous-versions/dn673538(v=msdn.10)) (som beskriver arbets belastnings mönster där In-Memory OLTP ofta ger avsevärda prestanda vinster)

#### <a name="application-design"></a>Programdesign

- [Minnes intern OLTP (minnes intern optimering)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)

- [Använda In-Memory OLTP i ett befintligt Azure SQL-program](in-memory-oltp-configure.md)

#### <a name="tools"></a>Verktyg

- [Azure-portalen](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)

- [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt)