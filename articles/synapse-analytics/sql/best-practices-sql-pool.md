---
title: Metod tips för dedikerade SQL-pooler
description: Rekommendationer och metod tips som du bör känna till när du arbetar med dedikerade SQL-pooler.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 65116cd3bf4c4ffb8f902818b79d48c6ee6b1680
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675059"
---
# <a name="best-practices-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Metod tips för dedikerade SQL-pooler i Azure Synapse Analytics

Den här artikeln innehåller en samling metod tips som hjälper dig att uppnå optimala prestanda för dedikerade SQL-pooler i Azure Synapse Analytics. Nedan hittar du grundläggande vägledning och viktiga områden att fokusera på när du skapar din lösning. I varje avsnitt beskrivs ett begrepp och du kan sedan peka på mer detaljerade artiklar som beskriver konceptet i större djup.

## <a name="dedicated-sql-pools-loading"></a>Dedikerade inläsning av SQL-pooler

Särskilda inläsnings anvisningar för SQL-pooler finns i [rikt linjer för att läsa in data](data-loading-best-practices.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Minska kostnaderna genom att pausa och skala

Mer information om hur du minskar kostnaderna genom att pausa och skala finns i [Hantera beräkning](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="maintain-statistics"></a>Underhålla statistik

Medan SQL Server automatiskt identifierar och skapar eller uppdaterar statistik för kolumner, kräver dedikerade SQL-pooler manuell underhåll av statistik. Du bör underhålla din statistik för att säkerställa att SQL-poolens planer är optimerade.  Kvaliteten på planerna som skapas av optimeraren beror på den tillgängliga statistiken.

> [!TIP]
> Ett enkelt sätt att komma igång med statistik är att skapa exempelstatistik för varje kolumn.  

Det är lika viktigt att uppdatera statistik när viktiga dataändringar görs.  En konservativ metod kan vara att uppdatera statistiken varje dag eller efter varje belastning.  Det finns alltid kompromisser mellan prestanda och kostnaden för att skapa och uppdatera statistik.

Om du vill förkorta tiden för statistik underhåll bör du vara noga med vilka kolumner som har statistik eller behöver den frekventa uppdateringen. Du kanske till exempel vill uppdatera datum kolumner där nya värden kan läggas till varje dag. Fokusera på att ha statistik för kolumner som ingår i kopplingar, kolumner som används i WHERE-satsen och kolumner som finns i GROUP BY.

Du hittar mer information om statistik i [Hantera tabell statistik](develop-tables-statistics.md), [skapa statistik](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true)och [Uppdatera statistik](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) artiklar.

## <a name="group-insert-statements-into-batches"></a>Gruppera INSERT-satser i batchar

En engångs inläsning till en liten tabell med en INSERT-instruktion som `INSERT INTO MyLookup VALUES (1, 'Type 1')` kan vara den bästa metoden beroende på dina behov. Men om du behöver läsa in tusentals eller miljon tals rader under dagen är det troligt att singleton-INFOGNINGar inte är optimala.

Ett sätt att lösa det här problemet är att utveckla en process som skriver till en fil och en annan process för att regelbundet läsa in den här filen. Mer information hittar du i [Infoga](/sql/t-sql/statements/insert-transact-sql?view=azure-sqldw-latest&preserve-view=true) -artikeln.

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Använda PolyBase för att snabbt läsa in och exportera data

SQL-poolen stöder inläsning och export av data via flera verktyg, inklusive Azure Data Factory, PolyBase och BCP.  För små datamängder där prestanda inte är viktigt räcker alla verktygen för dina behov.  

> [!NOTE]
> PolyBase är det bästa valet när du läser in eller exporterar stora data volymer, eller så behöver du snabbare prestanda.

PolyBase-inläsningar kan utföras med hjälp av CTAS eller INSERT INTO. CTAS kommer att minimera transaktions loggningen och är det snabbaste sättet att läsa in dina data. Azure Data Factory stöder även PolyBase-belastningar och kan uppnå prestanda som liknar CTAS. PolyBase stöder olika fil format inklusive gzip-filer.

För att maximera data flödet när du använder gzip-textfiler kan du dela upp filer i 60 eller fler filer för att maximera din belastnings parallellt. För snabbare totalt genomflöde bör du överväga att använda samtidig inläsning av data. Ytterligare information för ämnen som är relevanta för det här avsnittet finns i följande artiklar:

- [Läsa in data](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Guide for using PolyBase](data-loading-best-practices.md) (Guide för att använda PolyBase)
- [Inläsnings mönster och strategier för Azure SQL-poolen](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies)
- [Läs in data med Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Flytta data med Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Läsa in och sedan fråga externa tabeller

PolyBase är inte optimalt för frågor. PolyBase-tabeller för dedikerade SQL-pooler stöder för närvarande endast Azure Blob-filer och Azure Data Lake lagring. De här filerna har inga beräknings resurser som kan återställas. Därför kan dedikerade SQL-pooler inte avlasta detta arbete och måste läsa hela filen genom att läsa in den i tempdb så att den kan läsa data.

Om du har flera frågor för att fråga dessa data är det bättre att läsa in dessa data en gång och låta frågor använda den lokala tabellen. Ytterligare PolyBase-vägledning finns i  [hand boken för att använda PolyBase](data-loading-best-practices.md) -artikeln.

## <a name="hash-distribute-large-tables"></a>Hash-distribuera stora tabeller

Tabeller distribueras som standard med resursallokering (Round Robin).   Med den här standardinställningen är det enkelt för användarna att börja skapa tabeller utan att behöva bestämma hur deras tabeller ska distribueras. Round Robin-tabeller kan fungera tillräckligt för vissa arbets belastningar. I de flesta fall ger en distributions kolumn bättre prestanda.  

Det vanligaste exemplet på en tabell som distribueras av en kolumn som utför en Round Robin-tabell är när två stora fakta tabeller är anslutna.  

Om du till exempel har en tabell med order som distribuerats av order_id och en transaktions tabell också distribueras av order_id, blir den här frågan en direkt fråga när du ansluter tabellen Order till din transaktions tabell på order_id. Åtgärder för data förflyttning elimineras sedan. Färre steg innebär en snabbare fråga. Mindre dataflyttning gör också att frågor körs snabbare.

> [!NOTE]
> När du läser in en distribuerad tabell ska dina inkommande data inte sorteras på distributions nyckeln. Om du gör det blir belastningen långsammare.

I artikel länkarna nedan får du ytterligare information om hur du kan förbättra prestandan genom att välja en distributions kolumn. Du hittar också information om hur du definierar en distribuerad tabell i WITH-satsen i CREATE TABLE-instruktionen:

- [Tabell översikt](develop-tables-overview.md)
- [Table distribution](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Tabelldistribution)
- [Selecting table distribution](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service) (Välja tabelldistribution)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)

## <a name="do-not-over-partition"></a>Överpartitionera inte

När du partitionerar data kan det vara effektivt att underhålla dina data genom att byta partitionering eller optimera genomsökningar med hjälp av partition Eli minering, så att för många partitioner kan sakta ned dina frågor.  Ofta är en partitionerings strategi för hög granularitet som fungerar bra på SQL Server kanske inte fungerar bra på en dedikerad SQL-pool.  

För många partitioner kan minska effektiviteten för grupperade columnstore-index om varje partition har färre än 1 000 000 rader. dedikerade SQL-pooler partitionerar dina data automatiskt i 60-databaser. Om du skapar en tabell med 100 partitioner blir resultatet 6000 partitioner. Varje arbets belastning är annorlunda, så det bästa är att experimentera med partitionering för att se vad som fungerar bäst för din arbets belastning.  

Ett alternativ att överväga är att använda en kornig het som är lägre än det som du har implementerat med SQL Server. Du kan till exempel använda veckovis eller månads Visa partitioner i stället för dagliga partitioner.

Mer information om partitionering finns i artikeln om [tabell partitionering](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="minimize-transaction-sizes"></a>Minimera transaktionsstorlekar

INSERT-, UPDATE-och DELETE-instruktioner körs i en transaktion. När de fungerar måste de återställas. Minimera transaktions storlekarna när det är möjligt för att minska risken för en lång återställning.  Att minimera transaktions storlekar kan göras genom att dela upp INSERT-, UPDATE-och DELETE-uttryck i delar. Om du till exempel har en INFOGNING som du förväntar dig att ta 1 timme, kan du dela upp INFOGAt i fyra delar. Varje körning kommer då att förkortas till 15 minuter.  

> [!TIP]
> Använd särskilda minimala loggnings fall, t. ex. CTAS, TRUNKERA, släpp tabell eller infoga i tomma tabeller för att minska återställnings risken.  

Ett annat sätt att eliminera återställningar är att använda åtgärder med endast metadata, t.ex. med partitionsväxling, för datahantering.  I stället för att exempelvis köra en DELETE-instruktion för att ta bort alla rader i en tabell där order_date var i oktober 2001, kan du partitionera dina data varje månad. Sedan kan du växla ut partitionen med data för en tom partition från en annan tabell (se ALTER TABLE-exempel).  

För opartitionerade tabeller bör du överväga att använda en CTAS för att skriva de data som du vill behålla i en tabell i stället för att använda ta bort.  Om en CTAS tar samma tids period, är det mycket säkrare att köra eftersom det har minimal transaktions loggning och kan avbrytas snabbt om det behövs.

Mer information om innehåll som rör det här avsnittet finns i artiklarna nedan:

- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Förstå transaktioner](develop-transactions.md)
- [Optimizing transactions](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Optimera transaktioner)
- [Tabell partitionering](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="reduce-query-result-sizes"></a>Minska resultat storlekarna för frågan

Genom att minska storleken på frågeresultaten kan du undvika problem på klient sidan som orsakas av stora frågeresultat.  Du kan redigera frågan om du vill minska antalet returnerade rader. Med vissa verktyg för att skapa frågor kan du lägga till syntaxen "Top N" i varje fråga.  Du kan också CETAS från frågeresultatet till en temporär tabell och sedan använda PolyBase-export för bearbetning av äldre versioner.

## <a name="use-the-smallest-possible-column-size"></a>Använda minsta möjliga kolumnstorlek

När du definierar DDL: en ska du använda den minsta data typen som har stöd för dina data så att du kan förbättra frågans prestanda.  Den här rekommendationen är särskilt viktig för kolumnerna CHAR och VARCHAR.  Om det längsta värdet i en kolumn är 25 tecken definierar du kolumnen som VARCHAR(25).  Undvik att definiera alla teckenkolumner med en stor standardlängd.  Definiera dessutom kolumner som VARCHAR om det är allt som behövs i stället för att använda NVARCHAR.

I [tabell översikt](develop-tables-overview.md), [tabell data typer](develop-tables-data-types.md)och [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) artiklar finns en mer detaljerad översikt över viktiga begrepp som är relevanta för ovanstående information.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Använda tillfälliga heap-tabeller för tillfälliga data

När du tillfälligt vilplan av data på dedikerade SQL-pooler gör heap-tabeller vanligt vis den övergripande processen snabbare.  Om du bara läser in data för att mellanlagra dem innan du kör fler transformationer, går det snabbare att läsa in tabellen till en heap-tabell än att läsa in data till en grupperad columnstore-tabell.  

Inläsning av data till en temporär tabell går också mycket snabbare än att läsa in en tabell till permanent lagring.  Temporära tabeller börjar med "#" och är bara tillgängliga för den session som skapade den. De kan därför bara fungera i begränsade scenarier. Heap-tabeller definieras i WITH-satsen i en CREATE TABLE-instruktion.  Om du använder en temporär tabell måste du också komma ihåg att skapa statistik för den temporära tabellen.

Ytterligare vägledning finns i de [temporära tabellerna](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)och [CREATE TABLE som Välj](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) artiklar.

## <a name="optimize-clustered-columnstore-tables"></a>Optimera grupperade columnstore-tabeller

Grupperade columnstore-index är ett av de mest effektiva sätten att lagra data i en dedikerad SQL-pool.  Som standard skapas tabeller i dedikerad SQL-pool som grupperade ColumnStore-objekt.  Det är viktigt att segmentkvaliteten är bra för att uppnå bästa prestanda med frågor mot columnstore-tabeller.  När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  

Segment kvaliteten kan mätas med antalet rader i en komprimerad radgrupp. Se [orsaker till dålig kolumn kvalitet för columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) i [tabellen tabell index](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) för stegvisa instruktioner för att identifiera och förbättra segment kvaliteten för grupperade columnstore-tabeller.  

Eftersom ett columnstore-segment av hög kvalitet är viktigt är det en bra idé att använda användar-ID: n som finns i resurs klassen medel eller stor för att läsa in data. Genom att använda lägre [data lager enheter](resource-consumption-models.md) kan du tilldela en större resurs klass till din inläsnings användare.

Columnstore-tabeller skickar vanligt vis inte data till ett komprimerat columnstore-segment förrän det finns fler än 1 000 000 rader per tabell. Varje dedikerad SQL-pool är partitionerad i 60-tabeller. Därför drar columnstore-tabeller inte en fråga om inte tabellen har fler än 60 000 000 rader.  

> [!TIP]
> För tabeller med färre än 60 000 000 rader är det inte säkert att ett columnstore-index är den optimala lösningen.  

Om du partitionerar dina data måste varje partition ha 1 000 000 rader för att dra nytta av ett grupperat columnstore-index.  För en tabell med 100 partitioner måste den ha minst 6 000 000 000 rader för att kunna dra nytta av ett lager för grupperade kolumner (60-distributioner *100 partitioner* 1 000 000 rader).  

Om tabellen inte innehåller 6 000 000 000 rader har du två huvud alternativ. Du kan antingen minska antalet partitioner eller överväga att använda en heap-tabell i stället.  Det kan också vara värt att experimentera för att se om bättre prestanda kan uppnås med hjälp av en heap-tabell med sekundära index i stället för en columnstore-tabell.

När du kör frågor mot en columnstore-tabell körs frågorna snabbare om du bara väljer de kolumner som du behöver.  Mer information om tabell-och columnstore-index finns i [tabell index](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), columnstore-index- [Guide](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true)och [Återskapa columnstore-index](../sql-data-warehouse/sql-data-warehouse-tables-index.md?view=azure-sqldw-latest&preserve-view=true#rebuilding-indexes-to-improve-segment-quality) -artiklar.

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Använda en större resursklass för att förbättra frågeprestanda

SQL-pooler använder resurs grupper som ett sätt att allokera minne till frågor. Inlednings vis tilldelas alla användare till den lilla resurs klassen, vilket ger 100 MB minne per distribution.  Det finns alltid 60-distributioner. Varje distribution får minst 100 MB. Den totala allokeringen av hela systemet är 6 000 MB, eller bara under 6 GB.  

Vissa frågor, t.ex. stora kopplingar eller inläsningar till grupperade columnstore-tabeller, kan dra nytta av större minnesallokeringar.  Vissa frågor, till exempel rena genomsökningar, ser ingen förmån. Användning av större resurs klasser påverkar samtidighet. Därför bör du vara medveten om dessa fakta innan du flyttar alla dina användare till en stor resurs klass.

Mer information om resurs klasser finns i artikeln [resurs klasser för hantering av arbets belastning](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Använd mindre resurs klass för att öka samtidigheten

Om du ser en lång fördröjning i användar frågorna kan användarna köras i större resurs klasser. Det här scenariot befordrar förbrukningen av samtidiga platser, vilket kan orsaka att andra frågor hamnar i kö.  För att avgöra om användarnas frågor är köade, kör `SELECT * FROM sys.dm_pdw_waits` för att se om några rader returneras.

[Resurs klasserna för hantering av arbets belastning](../sql-data-warehouse/resource-classes-for-workload-management.md) och [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true) artiklar ger dig mer information.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Använda DMV:er för att övervaka och optimera frågor

SQL-pooler har flera DMV: er som du kan använda för att övervaka frågekörningen.  I övervaknings artikeln nedan går vi igenom steg-för-steg-instruktioner om hur du visar information om en körnings fråga.  För att snabbt hitta frågor i dessa DMV:er kan det vara bra att använda alternativet LABEL med dina frågor. Mer detaljerad information finns i artiklarna som följer i listan nedan:

- [Övervaka din arbetsbelastning med DMV:er](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [ETIKETT](develop-label.md)
- [ALTERNATIVET](/sql/t-sql/queries/option-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="next-steps"></a>Nästa steg

Se även [fel söknings](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) artikeln för vanliga problem och lösningar.

Om du behöver information som inte finns med i den här artikeln kan du söka på [sidan Microsoft Q&en fråga för Azure Synapse](/answers/topics/azure-synapse-analytics.html) , där du kan ställa frågor till andra användare och till produkt gruppen för SQL-poolen.  

Vi övervakar aktivt detta forum för att kontrollera att dina frågor besvaras antingen av en annan användare eller av någon av oss.  Om du föredrar att ställa dina frågor på Stack Overflow, har vi också en [Azure SQL-pool Stack Overflow forum](https://stackoverflow.com/questions/tagged/azure-sqldw).

För funktions förfrågningar använder du feedback-sidan för [Azure SQL-poolen](https://feedback.azure.com/forums/307516-sql-data-warehouse) .  Genom att lägga till dina förfrågningar eller upp-röstning andra begär Anden kan vi fokusera på de mest aktuella funktionerna på begäran.