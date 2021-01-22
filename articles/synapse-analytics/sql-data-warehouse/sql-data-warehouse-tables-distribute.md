---
title: Design rikt linjer för distribuerade tabeller
description: Rekommendationer för att utforma hash-distribuerade och resursallokering-tabeller med resursallokering med dedikerad SQL-pool i Azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 5aefe869041d9fff8112b6aa380961ca6568ae0b
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673577"
---
# <a name="guidance-for-designing-distributed-tables-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Vägledning för att utforma distribuerade tabeller med dedikerad SQL-pool i Azure Synapse Analytics

Rekommendationer för att designa distribuerade hash-och resursallokering-tabeller i dedikerade SQL-pooler.

Den här artikeln förutsätter att du är bekant med koncepten för data distribution och data förflyttning i dedikerad SQL-pool.  Mer information finns i [Azure Synapse Analytics-arkitekturen](massively-parallel-processing-mpp-architecture.md).

## <a name="what-is-a-distributed-table"></a>Vad är en distribuerad tabell?

En distribuerad tabell visas som en enskild tabell, men raderna lagras i stället för 60-distributioner. Raderna distribueras med en hash-eller Round-Robin-algoritm.  

**Hash-distribuerade tabeller** ger bättre prestanda i stora fakta tabeller och fokuserar på den här artikeln. **Round-Robin-tabeller** är användbara för att förbättra inläsnings hastigheten. Dessa design alternativ har en betydande inverkan på att förbättra frågor och läsa in prestanda.

Ett annat alternativ för tabell lagring är att replikera en liten tabell över alla Compute-noder. Mer information finns i [design guide för replikerade tabeller](design-guidance-for-replicated-tables.md). Om du snabbt vill välja bland de tre alternativen går du till distribuerade tabeller i [tabellerna översikt](sql-data-warehouse-tables-overview.md).

Som en del av tabell designen förstår du så mycket som möjligt av dina data och hur data efter frågas.  Överväg till exempel följande frågor:

- Hur stor är tabellen?
- Hur ofta uppdateras tabellen?
- Har jag fakta-och dimensions tabeller i en dedikerad SQL-pool?

### <a name="hash-distributed"></a>Hash distribuerad

En hash-distribuerad tabell distribuerar tabell rader över datornoderna genom att använda en deterministisk hash-funktion för att tilldela varje rad till en [distribution](massively-parallel-processing-mpp-architecture.md#distributions).

![Distribuerad tabell](./media/sql-data-warehouse-tables-distribute/hash-distributed-table.png "Distribuerad tabell")  

Eftersom identiska värden alltid hash till samma distribution har SQL Analytics inbyggd kunskap om rad platser. I dedikerad SQL-pool används den här kunskapen för att minimera data flyttningen under frågor, vilket förbättrar frågans prestanda.

Hash-distribuerade tabeller fungerar bra för stora fakta tabeller i ett stjärn schema. De kan ha ett stort antal rader och har fortfarande höga prestanda. Det finns naturligtvis några design överväganden som hjälper dig att få den prestanda som det distribuerade systemet har utformats för att tillhandahålla. Att välja en lämplig distributions kolumn är en sådan som beskrivs i den här artikeln.

Överväg att använda en hash-distribuerad tabell när:

- Tabell storleken på disken är större än 2 GB.
- Tabellen har ofta Infoga-, uppdaterings-och borttagnings åtgärder.

### <a name="round-robin-distributed"></a>Resursallokering med resursallokering

En fördelad resursallokering-tabell distribuerar tabell rader jämnt över alla distributioner. Tilldelningen av rader till distributioner är slumpmässig. Till skillnad från hash-distribuerade tabeller är rader med samma värden inte garanterade att tilldelas samma distribution.

Det innebär att systemet ibland måste anropa en åtgärd för data förflyttning för att bättre organisera dina data innan de kan lösa en fråga.  Det här extra steget kan sakta ned dina frågor. Om du till exempel ansluter till en Round-Robin-tabell, krävs vanligt vis att reshuffling rader, vilket är en prestanda träff.

Överväg att använda Round-Robin-fördelningen för tabellen i följande scenarier:

- När du ska komma igång som en enkel start punkt eftersom den är standard
- Om det inte finns någon uppenbar kopplings nyckel
- Om det inte finns någon bra kandidat kolumn för hash som distribuerar tabellen
- Om tabellen inte delar en gemensam kopplings nyckel med andra tabeller
- Om kopplingen är mindre viktig än andra kopplingar i frågan
- När tabellen är en tillfällig mellanlagrings tabell

Självstudien [Läs in New York taxidata-data](./load-data-from-azure-blob-storage-using-copy.md#load-the-data-into-your-data-warehouse) ger ett exempel på inläsning av data i en mellanlagrings tabell för resursallokering.

## <a name="choosing-a-distribution-column"></a>Välja en distributions kolumn

En hash-distribuerad tabell har en distributions kolumn som är hash-nyckeln. Följande kod skapar till exempel en hash-distribuerad tabell med ProductKey som distributions kolumn.

```sql
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

Data som lagras i kolumnen distribution kan uppdateras. Uppdateringar av data i kolumnen distribution kan resultera i en data blandnings åtgärd.

Att välja en distributions kolumn är ett viktigt design beslut eftersom värdena i den här kolumnen avgör hur raderna ska distribueras. Det bästa valet beror på flera faktorer och inbegriper vanligt vis kompromisser. När du har valt en distributions kolumn kan du inte ändra den.  

Om du inte väljer den bästa kolumnen första gången kan du använda [CREATE TABLE som Select (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att återskapa tabellen med en annan distributions kolumn.

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Välj en distributions kolumn med data som distribuerar jämnt

För bästa prestanda bör alla distributioner ha ungefär samma antal rader. När en eller flera distributioner har ett oproportionerligt antal rader, slutför vissa distributioner sin del av en parallell fråga innan andra. Eftersom frågan inte kan slutföras förrän alla distributioner har slutfört bearbetningen, är varje fråga bara lika snabb som den långsamma fördelningen.

- Data sned innebär att data inte fördelas jämnt över distributionerna
- Bearbetning av sned innebär att vissa distributioner tar längre tid än andra när du kör parallella frågor. Detta kan inträffa när data har skevas.
  
För att balansera parallell bearbetningen väljer du en distributions kolumn som:

- **Har många unika värden.** Kolumnen kan ha vissa dubblettvärden. Men alla rader med samma värde tilldelas samma distribution. Eftersom det finns 60-distributioner måste kolumnen ha minst 60 unika värden.  Vanligt vis är antalet unika värden mycket större.
- **Innehåller inte NULL-värden eller innehåller bara några NULL-värden.** För ett extrema exempel, om alla värden i kolumnen är NULL, tilldelas alla rader samma distribution. Det innebär att Query-bearbetningen skevas till en distribution och inte drar nytta av parallell bearbetning.
- **Är inte en datum kolumn**. Alla data för samma datum är i samma distribution. Om flera användare är all filtrering på samma datum är det bara 1 av 60-distributionerna som utför allt bearbetnings arbete.

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Välj en distributions kolumn som minimerar data flyttningen

För att få rätt frågor om frågeresultat kan du flytta data från en Compute-nod till en annan. Data flyttning sker ofta när frågor har kopplingar och agg regeringar i distribuerade tabeller. Att välja en distributions kolumn som hjälper till att minimera data flytt är en av de viktigaste strategierna för att optimera prestanda för din dedikerade SQL-pool.

Om du vill minimera data flytten väljer du en distributions kolumn som:

- Används i `JOIN` -,-, `GROUP BY` `DISTINCT` `OVER` -och- `HAVING` satser. När två stora fakta tabeller har frekventa kopplingar, ökar frågans prestanda när du distribuerar båda tabellerna på någon av kopplings kolumnerna.  När en tabell inte används i kopplingar bör du överväga att distribuera tabellen i en kolumn som ofta är i- `GROUP BY` satsen.
- Används *inte* i `WHERE` satser. Detta kan begränsa frågan till att inte köras på alla distributioner.
- Är *inte* en datum kolumn. WHERE-satser filtreras ofta efter datum.  När detta inträffar kan all bearbetning bara köras på några få distributioner.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Vad du gör om ingen av kolumnerna är en bra distributions kolumn

Om ingen av kolumnerna har tillräckligt distinkta värden för en distributions kolumn kan du skapa en ny kolumn som en sammansatt mängd av ett eller flera värden. Använd kolumnen sammansatt distribution som en kopplings kolumn i frågor för att undvika data flyttning under frågekörningen.

När du har konstruerat en hash-distribuerad tabell är nästa steg att läsa in data i tabellen.  Information om hur du läser in vägledning finns i [Översikt över inläsning](design-elt-data-loading.md).

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Så här ser du om distributions kolumnen är ett bra val

När data har lästs in i en hash-distribuerad tabell, kontrollerar du om du vill se hur jämnt raderna fördelas över 60-distributionerna. Raderna per fördelning kan variera upp till 10% utan märkbar påverkan på prestanda.

### <a name="determine-if-the-table-has-data-skew"></a>Ta reda på om tabellen har en data skev

Ett snabbt sätt att söka efter data skevning är att använda [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Följande SQL-kod returnerar antalet tabell rader som lagras i var och en av 60-distributionerna. För balanserade prestanda bör raderna i den distribuerade tabellen spridas jämnt över alla distributioner.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Så här identifierar du vilka tabeller som har fler än 10% data skevning:

1. Skapa vyn dbo. vTableSizes som visas i [översikts artikeln tabeller](sql-data-warehouse-tables-overview.md#table-size-queries) .  
2. Kör följande fråga:

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having (max(row_count * 1.000) - min(row_count * 1.000))/max(row_count * 1.000) >= .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>Kontrol lera Query-planer för data förflyttning

En lämplig distributions kolumn gör att kopplingar och agg regeringar har minimal data förflyttning. Detta påverkar hur kopplingar ska skrivas. För att få minimal data förflyttning för en koppling i två hash-distribuerade tabeller måste en av kopplings kolumnerna vara distributions kolumn.  När två hash-distribuerade tabeller ansluter till en distributions kolumn av samma datatyp kräver inte kopplingen data flytt. Kopplingar kan använda ytterligare kolumner utan att det uppstår någon data förflyttning.

Undvik data flyttning under en koppling:

- Tabellerna som används i kopplingen måste vara hash-distribuerade på **någon** av kolumnerna som ingår i kopplingen.
- Data typerna för kopplings kolumnerna måste matcha mellan båda tabellerna.
- Kolumnerna måste vara kopplade till en Equals-operator.
- Kopplings typen får inte vara en `CROSS JOIN` .

Om du vill se om frågor har data förflyttning kan du titta på frågeplan.  

## <a name="resolve-a-distribution-column-problem"></a>Lösa ett problem med distributions kolumner

Det är inte nödvändigt att lösa alla fall av data skevning. Att distribuera data är en fråga om att hitta rätt balans mellan att minimera data skevningen och data flytt. Det är inte alltid möjligt att minimera både data skevning och data förflyttning. Ibland kan fördelarna med att ha den minsta data flytten överväger effekten av att ha data skevning.

För att avgöra om du bör lösa data skevningen i en tabell, bör du förstå så mycket som möjligt om data volymerna och frågorna i din arbets belastning. Du kan använda stegen i artikeln [fråga övervakning](sql-data-warehouse-manage-monitor.md) om du vill övervaka effekten av skevning för frågans prestanda. Mer specifikt kan du söka efter hur lång tid det tar för stora frågor att slutföra vid enskilda distributioner.

Eftersom du inte kan ändra distributions kolumnen i en befintlig tabell, är det vanligaste sättet att lösa data skevningen att återskapa tabellen med en annan distributions kolumn.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Återskapa tabellen med en ny distributions kolumn

I det här exemplet används [CREATE TABLE som Välj](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att återskapa en tabell med en annan hash-fördelnings kolumn.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

## <a name="next-steps"></a>Nästa steg

Använd någon av följande instruktioner om du vill skapa en distribuerad tabell:

- [CREATE TABLE (dedikerad SQL-pool)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE som SELECT (dedikerad SQL-pool)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)