---
title: Design vägledning för replikerade tabeller
description: Rekommendationer för att utforma replikerade tabeller i Synapse SQL-pool
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7dcb884d8eafdfa5218e96d63f62a5d462d20cf8
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679938"
---
# <a name="design-guidance-for-using-replicated-tables-in-synapse-sql-pool"></a>Design Guide för att använda replikerade tabeller i Synapse SQL-pool

Den här artikeln innehåller rekommendationer för att utforma replikerade tabeller i Synapse SQL-poolens schema. Använd dessa rekommendationer för att förbättra prestandan för frågor genom att minska data flytt och fråga efter komplexitet.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med koncepten för data distribution och data förflyttning i SQL-poolen.  Mer information finns i [arkitektur](massively-parallel-processing-mpp-architecture.md) artikeln.

Som en del av tabell designen förstår du så mycket som möjligt av dina data och hur data efter frågas.  Överväg till exempel följande frågor:

- Hur stor är tabellen?
- Hur ofta uppdateras tabellen?
- Har jag fakta-och dimensions tabeller i en SQL-pool?

## <a name="what-is-a-replicated-table"></a>Vad är en replikerad tabell?

En replikerad tabell innehåller en fullständig kopia av den tillgängliga tabellen på varje Compute-nod. När du replikerar en tabell behöver du inte överföra data mellan beräkningsnoder före en koppling eller aggregering. Eftersom tabellen har flera kopior fungerar replikerade tabeller bäst när tabell storleken är mindre än 2 GB komprimerad.  2 GB är inte en hård gräns.  Om data är statiska och inte ändras kan du replikera större tabeller.

Följande diagram visar en replikerad tabell som är tillgänglig på varje Compute-nod. I SQL-poolen kopieras den replikerade tabellen fullständigt till en distributions databas på varje Compute-nod.

![Replikerad tabell](./media/design-guidance-for-replicated-tables/replicated-table.png "Replikerad tabell")  

Replikerade tabeller fungerar bra för dimensions tabeller i ett stjärn schema. Dimensions tabeller är vanligt vis kopplade till fakta tabeller som distribueras annorlunda än dimensions tabellen.  Dimensioner är vanligt vis en storlek som gör det möjligt att lagra och underhålla flera kopior. Dimensioner lagrar beskrivande data som ändras långsamt, t. ex. kundnamn och adress samt produkt information. Dataens långsammaste föränderliga natur leder till mindre underhåll av den replikerade tabellen.

Överväg att använda en replikerad tabell när:

- Tabell storleken på disken är mindre än 2 GB, oavsett antalet rader. Du kan använda [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) kommandot för att hitta storleken på en tabell: `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')` .
- Tabellen används i kopplingar som annars kräver data förflyttning. När du kopplar ihop tabeller som inte är distribuerade i samma kolumn, till exempel en hash-distribuerad tabell till en Round Robin-tabell, krävs data flytt för att slutföra frågan.  Om en av tabellerna är liten bör du tänka på en replikerad tabell. Vi rekommenderar att du använder replikerade tabeller i stället för Round-Robin-tabeller i de flesta fall. Använd [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)om du vill visa åtgärder för data förflyttning i fråge planer.  BroadcastMoveOperation är den typiska data flytt åtgärden som kan elimineras med hjälp av en replikerad tabell.  

Replikerade tabeller kanske inte ger bästa prestanda för frågor när:

- Tabellen har ofta Infoga-, uppdaterings-och borttagnings åtgärder. DML-åtgärder (Data Manipulation Language) kräver en återskapning av den replikerade tabellen. Återskapande av ofta kan orsaka sämre prestanda.
- SQL-poolen skalas ofta. Skalning av en SQL-pool ändrar antalet datornoder, vilket innebär att den replikerade tabellen återskapas.
- Tabellen har ett stort antal kolumner, men data åtgärder använder vanligt vis bara ett litet antal kolumner. I det här scenariot, i stället för att replikera hela tabellen, kan det vara mer effektivt att distribuera tabellen och sedan skapa ett index på kolumnerna som används ofta. När en fråga kräver data förflyttning flyttas endast data för de begärda kolumnerna i SQL-poolen.

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Använda replikerade tabeller med enkla frågenoder

Innan du väljer att distribuera eller replikera en tabell bör du tänka på vilka typer av frågor du planerar att köra mot tabellen. När det är möjligt

- Använd replikerade tabeller för frågor med enkla frågenoder, till exempel likhet eller olikhet.
- Använd distribuerade tabeller för frågor med komplexa frågenoder, till exempel gilla eller inte gilla.

CPU-intensiva frågor fungerar bäst när arbetet är fördelat på alla Compute-noder. Till exempel fungerar frågor som kör beräkningar på varje rad i en tabell bättre på distribuerade tabeller än replikerade tabeller. Eftersom en replikerad tabell lagras fullständigt på varje Compute-nod körs en processor intensiv fråga mot en replikerad tabell mot hela tabellen på varje Compute-nod. Den extra beräkningen kan sänka prestandan för frågor.

Den här frågan har till exempel ett komplext predikat.  Den körs snabbare när data finns i en distribuerad tabell i stället för en replikerad tabell. I det här exemplet kan data fördelas med resursallokering.

```sql

SELECT EnglishProductName
FROM DimProduct
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Konvertera befintliga Round Robin-tabeller till replikerade tabeller

Om du redan har en Round-Robin-tabell rekommenderar vi att du konverterar dem till replikerade tabeller om de uppfyller de kriterier som beskrivs i den här artikeln. Replikerade tabeller ger bättre prestanda över Round-Robin-tabeller eftersom de eliminerar behovet av data förflyttning.  En Round-Robin-tabell kräver alltid data förflyttning för kopplingar.

I det här exemplet används [CTAs](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att ändra DimSalesTerritory-tabellen till en replikerad tabell. Det här exemplet fungerar oavsett om DimSalesTerritory är hash-Distributed eller Round-Robin.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]
WITH
  (
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE')

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Exempel på prestanda frågor för resursallokering med resursallokering och replikerad

En replikerad tabell kräver ingen data förflyttning för kopplingar eftersom hela tabellen redan finns på varje Compute-nod. Om dimensions tabellerna har distribuerats med resursallokering, kopierar en koppling dimensions tabellen i fullständig till varje Compute-nod. För att flytta data innehåller frågeuttrycket en åtgärd som kallas BroadcastMoveOperation. Den här typen av data förflyttnings åtgärd saktar ned frågans prestanda och elimineras genom att använda replikerade tabeller. Använd vyn [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) system katalog för att Visa plan steg för frågor.  

I följande fråga mot AdventureWorks `FactInternetSales` -schemat är tabellen till exempel hash-distribuerad. `DimDate`Tabellerna och `DimSalesTerritory` är mindre dimensions tabeller. Den här frågan returnerar den totala försäljningen i Nordamerika för räkenskapsår 2004:

```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```

Vi återskapar `DimDate` och `DimSalesTerritory` som Round-Robin-tabeller. Därför visade frågan följande frågeplan, som har flera sändnings flyttnings åtgärder:

![Frågeplan med resursallokering](./media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg)

Vi återskapar `DimDate` och `DimSalesTerritory` replikerade tabeller och körde frågan igen. Den resulterande frågeplan är mycket kortare och har inga sändnings flyttningar.

![Replikerad frågeplan](./media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg)

## <a name="performance-considerations-for-modifying-replicated-tables"></a>Prestanda överväganden vid ändring av replikerade tabeller

SQL-poolen implementerar en replikerad tabell genom att underhålla en huvud version av tabellen. Den kopierar huvud versionen till den första distributions databasen på varje Compute-nod. När det sker en ändring uppdateras huvud versionen först och sedan återskapas tabellerna på varje Compute-nod. En återskapning av en replikerad tabell omfattar att kopiera tabellen till varje Compute-nod och sedan skapa index.  Till exempel har en replikerad tabell på en DW2000c fem kopior av data.  En huvud kopia och en fullständig kopia på varje Compute-nod.  Alla data lagras i distributions databaser. SQL-poolen använder den här modellen för att stödja snabbare data ändrings instruktioner och flexibla skalnings åtgärder.

Asynkrona återuppbyggnadar utlöses av den första frågan mot den replikerade tabellen efter:

- Data läses in eller ändras
- Synapse SQL-instansen skalas till en annan nivå
- Tabell definitionen har uppdaterats

Återuppbyggnadar krävs inte efter:

- Pausa åtgärd
- Återuppta åtgärd

Återskapning sker inte omedelbart efter att data har ändrats. I stället utlöses omgenereringen första gången en fråga väljs från tabellen.  Frågan som utlöste återställnings läsningarna direkt från huvud versionen av tabellen medan data kopieras asynkront till varje Compute-nod. När data kopieringen är klar fortsätter de efterföljande frågorna att använda huvud versionen av tabellen.  Om en aktivitet sker mot den replikerade tabellen som tvingar fram en ny återskapning, är data kopian ogiltig och nästa SELECT-instruktion kommer att utlösa data som ska kopieras igen.

### <a name="use-indexes-conservatively"></a>Använda index försiktigt

Standard indexerings metoder gäller för replikerade tabeller. SQL-poolen återbygger varje replikerat tabell index som en del av återuppbyggnaden. Använd bara index när prestanda ökningen förväger kostnaden för att återskapa indexen.

### <a name="batch-data-load"></a>Inläsning av batch-data

När du läser in data i replikerade tabeller kan du försöka minimera återställningarna genom att gruppera. Utför alla batch-inläsningar innan SELECT-instruktioner körs.

Exempelvis läser det här inläsnings mönstret data från fyra källor och anropar fyra återuppbyggnadar.

- Läs in från källa 1.
- Välj instruktion utlösare återskapa 1.
- Läs in från källa 2.
- Select Statement triggers build 2.
- Läs in från källa 3.
- Select Statement triggers build 3.
- Läs in från källa 4.
- Select Statement triggers build 4.

Till exempel läser det här inläsnings mönstret data från fyra källor, men anropar bara en återskapning.

- Läs in från källa 1.
- Läs in från källa 2.
- Läs in från källa 3.
- Läs in från källa 4.
- Välj instruktion utlösare återskapa.

### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Återskapa en replikerad tabell efter en batch-inläsning

För att säkerställa konsekvent körning av frågor, kan du överväga att tvinga fram skapandet av de replikerade tabellerna efter en batch-belastning. Annars kommer den första frågan fortfarande använda data förflyttning för att slutföra frågan.

Den här frågan använder [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) DMV för att visa en lista över replikerade tabeller som har ändrats, men inte återskapas.

```sql
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id
  JOIN sys.pdw_table_distribution_properties p
    ON p.object_id = t.object_id
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```

Om du vill utlösa en återskapning kör du följande instruktion på varje tabell i föregående utdata.

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
```

## <a name="next-steps"></a>Nästa steg

Använd någon av följande instruktioner för att skapa en replikerad tabell:

- [CREATE TABLE (SQL-pool)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE som SELECT (SQL-pool)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

En översikt över distribuerade tabeller finns i [distribuerade tabeller](sql-data-warehouse-tables-distribute.md).
