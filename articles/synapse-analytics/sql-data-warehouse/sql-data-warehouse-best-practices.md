---
title: Metod tips för dedikerad SQL-pool (tidigare SQL DW)
description: Rekommendationer och metod tips för att utveckla lösningar för dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: d62ea594916dbf5656569b7e1a3afe9ee81d8f89
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685301"
---
# <a name="best-practices-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Metod tips för dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics

Den här artikeln är en samling av bästa metoder som hjälper dig att uppnå optimala prestanda från din [dedikerade SQL-pool (tidigare SQL DW)](sql-data-warehouse-overview-what-is.md) -distribution.  Syftet med den här artikeln är att ge dig grundläggande vägledning och fokusera på viktiga områden i fokus.  

## <a name="reduce-cost-with-pause-and-scale"></a>Minska kostnaderna genom att pausa och skala

Läs mer om att minska kostnaderna genom att pausa och skala i [Hantera beräkning](sql-data-warehouse-manage-compute-overview.md).

## <a name="maintain-statistics"></a>Underhålla statistik

Dedikerad SQL-pool (tidigare SQL DW) kan konfigureras för att automatiskt identifiera och skapa statistik för kolumner.  De fråge planer som skapats av optimeringen är bara lika lämpliga som tillgängliga statistik.  

Vi rekommenderar att du aktiverar AUTO_CREATE_STATISTICS för dina databaser och håller statistiken uppdaterad dagligen eller efter varje belastning för att säkerställa att statistik för kolumner som används i dina frågor alltid är uppdaterade.

Om du tycker att det tar för lång tid att uppdatera all statistik, kanske du vill försöka bli mer selektiv om vilka kolumner som behöver frekventa statistik uppdateringar. Du kanske till exempel vill uppdatera datumkolumner, där nya värden kan läggas till, varje dag.

> [!TIP]
> Du får störst nytta genom att ha uppdaterad statistik för kolumner som ingår i kopplingar, kolumner som används i WHERE-satsen och kolumner som finns i GROUP BY.

Se även [Hantera tabell statistik](sql-data-warehouse-tables-statistics.md), [skapa statistik](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)och [Uppdatera statistik](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Använda DMV:er för att övervaka och optimera frågor

Dedikerad SQL-pool (tidigare SQL DW) har flera DMV: er som kan användas för att övervaka frågekörningen.  Den [övervaka din arbets belastning med hjälp av DMV: er](sql-data-warehouse-manage-monitor.md) -artikel information steg-för-steg-instruktioner om hur du granskar information om en körnings fråga.  

För att snabbt hitta frågor i dessa DMV:er kan det vara bra att använda alternativet LABEL med dina frågor.

Se även [övervaka arbets belastningen med DMV: er](sql-data-warehouse-manage-monitor.md), [etikett](sql-data-warehouse-develop-label.md), [alternativ](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)och [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="tune-query-performance-with-new-product-enhancements"></a>Justera prestanda för frågor med nya produkt förbättringar

- [Prestandajustering med materialiserade vyer](performance-tuning-materialized-views.md)
- [Prestandajustering med grupperade kolumnlagringsindex](performance-tuning-ordered-cci.md)
- [Prestandajustering med cachelagring av resultatuppsättningar](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>Gruppera INSERT-satser i batchar

En engångs inläsning till en liten tabell med en INSERT-instruktion eller till och med en regelbunden omlastning av en sökning kan fungera bra för dina behov med en instruktion som `INSERT INTO MyLookup VALUES (1, 'Type 1')` .  

Men om du behöver läsa in flera tusen eller flera miljoner rader under en dag kanske du märker att singleton-infogningar med INSERT inte hänger med.  I så fall utvecklar du i stället dina processer så att de skriver till en fil och så att en annan process regelbundet körs och läser in filen.

Se även [Infoga](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Använda PolyBase för att snabbt läsa in och exportera data

Dedikerad SQL-pool (tidigare SQL DW) stöder inläsning och export av data via flera verktyg, inklusive Azure Data Factory, PolyBase och BCP.  För små datamängder där prestanda inte är viktigt räcker alla verktygen för dina behov.  Om du däremot läser in eller exporterar stora mängder data eller om snabba prestanda krävs är PolyBase det bästa valet.  

PolyBase är utformat för att utnyttja systemets distribuerade natur och kommer att läsa in och exportera data storlekarna snabbare än andra verktyg.  PolyBase-inläsningar kan utföras med hjälp av CTAS eller INSERT INTO.   

> [!TIP]
> CTAS minimerar transaktionsloggningen och är det snabbaste sättet att läsa in data.

Azure Data Factory stöder även PolyBase-belastningar och kan uppnå liknande prestanda som CTAS.  PolyBase stöder olika filformat, inklusive Gzip-filer.  
  
> [!NOTE]
> För att maximera data flödet när du använder gzip-textfiler kan du dela upp filer i 60 eller fler filer för att maximera din belastnings parallellt.  För snabbare totalt genomflöde bör du överväga att använda samtidig inläsning av data.

Se även [läsa in data](design-elt-data-loading.md), [Guide för att använda PolyBase](guidance-for-loading-data.md), [dedikerad SQL-pool, läsa in mönster och strategier](/archive/blogs/sqlcat/), [läsa in data med Azure Data Factory]( ../../data-factory/load-azure-sql-data-warehouse.md), [Flytta data med Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md), [Skapa externt fil format](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)och [Skapa tabell som Select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="load-then-query-external-tables"></a>Läsa in och sedan fråga externa tabeller

Även om Polybase, även kallat externa tabeller, kan vara det snabbaste sättet att läsa in data, är det inte optimalt för frågor. PolyBase-tabeller stöder för närvarande endast Azure Blob-filer och Azure Data Lake lagring. Dessa filer har inte några beräkningsresurser som backar upp dem.  

Därför kan dedikerad SQL-pool inte avlasta detta arbete och måste därför läsa hela filen genom att läsa in den på tempdb för att kunna läsa data.  Om du har flera frågor som ska köras mot dessa data är det därför bättre att läsa in dem en gång och låta frågorna använda den lokala tabellen.

Se även [Guide för att använda PolyBase](guidance-for-loading-data.md).

## <a name="hash-distribute-large-tables"></a>Hash-distribuera stora tabeller

Tabeller distribueras som standard med resursallokering (Round Robin).  Detta gör det enkelt för användarna att börja skapa tabeller utan att de behöver bestämma hur tabellerna ska distribueras.  Resursallokeringstabeller kan prestera bra för vissa arbetsbelastningar, men i de flesta fall blir prestanda bättre om du väljer en distributionskolumn.  

Det vanligaste exemplet på när en tabell som distribueras med en kolumn presterar avsevärt mycket bättre än en resursallokeringstabell är när två stora faktatabeller kopplas.  

Om du till exempel har en ordertabell, som distribueras efter order_id, och en transaktionstabell, som också distribueras efter order_id, och du kopplar ordertabellen till transaktionstabellen baserat på order_id, så blir den här frågan en direktfråga, vilket innebär att vi eliminerar dataflyttningsåtgärder.  Färre steg innebär en snabbare fråga.  Mindre dataflyttning gör också att frågor körs snabbare.  

> [!TIP]
> När du läser in en distribuerad tabell ser du till att inkommande data inte sorteras baserat på distributionsnyckeln eftersom det gör att inläsningarna tar längre tid.  

I följande länkar finns mer information om hur du väljer en distributions kolumn som kan förbättra prestanda samt hur du definierar en distribuerad tabell i WITH-satsen i CREATE TABLE-instruktionen.

Se även [tabell översikt](sql-data-warehouse-tables-overview.md), [tabell distribution](sql-data-warehouse-tables-distribute.md), [val av tabell distribution](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [CREATE TABLE som Välj](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="do-not-over-partition"></a>Överpartitionera inte

När du partitionerar data kan det vara effektivt att underhålla dina data genom att byta partitionering eller optimera genomsökningar med hjälp av partition Eli minering, så att för många partitioner kan sakta ned dina frågor.  Ofta är en partitionerings strategi för hög granularitet som fungerar bra på SQL Server kanske inte fungerar bra i dedikerad SQL-pool (tidigare SQL DW).  

För många partitioner kan också minska effektiviteten i grupperade columnstore-index om varje partition har färre än 1 miljoner rader.  Tänk på att när du skapar en tabell med 100 partitioner i bakgrunden, är dedikerad SQL-pool partitioner dina data till 60 databaser, så om du skapar en tabell med partitioner, resulterar detta faktiskt i 6000-partitioner.  

Alla arbetsbelastningar är olika så det bästa rådet är att experimentera med partitioneringen för att se vad som fungerar bäst med din arbetsbelastning.  Överväg att använda lägre granularitet än vad som har fungerat i SQL Server.  Överväg exempelvis att använda veckovisa eller månadsvisa partitioneringar i stället för dagliga.

Se även [tabell partitionering](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Minimera transaktionsstorlekar

INSERT-, UPDATE- och DELETE-instruktioner körs i en transaktion och när de misslyckas måste de återställas.  Du kan minimera risken för en lång återställning genom att minska transaktionsstorlekarna om det går.  Du kan göra det genom att dela upp INSERT-, UPDATE- och DELETE-instruktioner i flera delar.  

Om du till exempel har en INFOGNING som du förväntar dig att ta 1 timme, om möjligt, kan du dela upp den i fyra delar som varje körs på 15 minuter.  Använd särskilda minimala loggnings fall, t. ex. CTAS, TRUNKERA, släpp tabell eller infoga i tomma tabeller, för att minska återställnings risken.  

Ett annat sätt att eliminera återställningar är att använda åtgärder med endast metadata, t.ex. med partitionsväxling, för datahantering.  I stället för att exempelvis köra en DELETE-instruktion för att ta bort alla rader i en tabell där order_date var i oktober 2001, kan du partitionera dina data varje månad och sedan stänga av partitionen med data för en tom partition från en annan tabell (se [Alter Table](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) -exempel).  

För opartitionerade tabeller bör du överväga att använda en CTAS för att skriva de data som du vill behålla i en tabell i stället för att använda ta bort.  Om en CTAS tar samma tids period, är det en mycket säkrare åtgärd att köra eftersom den har minimal transaktions loggning och kan avbrytas snabbt om det behövs.

Se även [förstå transaktioner](sql-data-warehouse-develop-transactions.md), [optimera transaktioner](sql-data-warehouse-develop-best-practices-transactions.md), [tabell partitionering](sql-data-warehouse-tables-partition.md), [truncate Table](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ändra tabell](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)och [Skapa tabell som Select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="reduce-query-result-sizes"></a>Minska resultat storlekarna för frågan

Det här steget hjälper dig att undvika problem på klient sidan som orsakas av resultat av stor fråga.  Du kan redigera frågan om du vill minska antalet returnerade rader. Med vissa verktyg för att skapa frågor kan du lägga till syntaxen "Top N" i varje fråga.  Du kan också CETAS från frågeresultatet till en temporär tabell och sedan använda PolyBase-export för bearbetning av äldre versioner.

## <a name="use-the-smallest-possible-column-size"></a>Använda minsta möjliga kolumnstorlek

När du definierar DDL: en med den minsta data typen som kommer att ge stöd för dina data förbättras frågans prestanda.  Den här metoden är särskilt viktig för kolumner av typen CHAR och VARCHAR.  

Om det längsta värdet i en kolumn är 25 tecken definierar du kolumnen som VARCHAR(25).  Undvik att definiera alla teckenkolumner med en stor standardlängd.  Definiera också kolumner som VARCHAR när det är allt som krävs i stället för att använda NVARCHAR.

Se även [tabell översikt](sql-data-warehouse-tables-overview.md), [tabell data typer](sql-data-warehouse-tables-data-types.md) [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="use-temporary-heap-tables-for-transient-data"></a>Använda tillfälliga heap-tabeller för tillfälliga data

När du är tillfälligt inleder data kan det hända att du använder en heap-tabell för att göra den övergripande processen snabbare.  Om du bara läser in data för att mellanlagra dem innan du kör fler transformationer går det mycket snabbare om du läser in tabellen till en heap-tabell än om du läser in data till en grupperad columnstore-tabell.  

Om du läser in data till en temporär tabell går inläsningen dessutom mycket fortare än om du läser in en tabell till permanent lagring.  Temporära tabeller börjar med "#" och är bara tillgängliga för den session som skapade den, så de kan bara fungera i begränsade scenarier.

Heap-tabeller definieras i WITH-satsen i en CREATE TABLE-instruktion.  Om du använder en temporär tabell måste du också komma ihåg att skapa statistik för den temporära tabellen.

Se även [temporära tabeller](sql-data-warehouse-tables-temporary.md), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [CREATE TABLE som Välj](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="optimize-clustered-columnstore-tables"></a>Optimera grupperade columnstore-tabeller

Grupperade columnstore-index är ett av de mest effektiva sätten att lagra data i en dedikerad SQL-pool.  Som standard skapas tabeller i dedikerad SQL-pool som grupperade ColumnStore-objekt.  Det är viktigt att segmentkvaliteten är bra för att uppnå bästa prestanda med frågor mot columnstore-tabeller.  

När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  Segmentkvaliteten kan mätas utifrån antalet rader i en komprimerad radgrupp.  Se [orsaker till dålig kolumn kvalitet för columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) i [tabellen tabell index](sql-data-warehouse-tables-index.md) för stegvisa instruktioner för att identifiera och förbättra segment kvaliteten för grupperade columnstore-tabeller.  

Eftersom ett columnstore-segment av hög kvalitet är viktigt är det en bra idé att använda användar-ID: n som finns i resurs klassen medel eller stor för att läsa in data. Genom att använda lägre [data lager enheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) kan du tilldela en större resurs klass till din inläsnings användare.

Eftersom det vanligt vis inte går att skicka data till ett komprimerat columnstore-segment i columnstore-tabeller förrän det finns fler än 1 000 000 rader per tabell och varje dedikerad SQL-adresspool partitioneras till 60-tabeller, som en tumregel, kommer columnstore-tabeller inte att dra nytta av en fråga om tabellen har fler än 60 000 000 rader.  För tabeller med färre än 60 miljoner rader kanske det inte tjänar något till att ha ett columnstore-index.  Men det skadar inte heller.  

Om du partitionerar data bör du dessutom tänka på att varje partition måste innehålla 1 miljon rader för att kunna dra nytta av ett grupperat columnstore-index.  Om en tabell har 100 partitioner måste den ha minst 6 000 000 000 rader för att kunna dra nytta av ett lager för grupperade kolumner (60-distributioner *100 partitioner* 1 000 000 rader).  

Om din tabell inte innehåller 6 miljarder rader i det här exemplet minskar du antalet partitioner eller överväger att använda en heap-tabell i stället.  Det kan också vara värt att experimentera och se om du kan förbättra prestanda med en heap-tabell med sekundära index i stället för en columnstore-tabell.

> [!TIP]
> När du kör frågor mot en columnstore-tabell körs frågorna snabbare om du bara väljer de kolumner som du behöver.  

Se även [Table indexes](sql-data-warehouse-tables-index.md) (Tabellindex), [Columnstore indexes guide](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (Guide för columnstore-index), [Rebuilding columnstore indexes](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality) (Återskapa columnstore-index)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Använda en större resursklass för att förbättra frågeprestanda

Dedikerad SQL-pool använder resurs grupper som ett sätt att allokera minne till frågor.  Från rutan är alla användare tilldelade till den lilla resurs klassen, vilket ger 100 MB minne per distribution.  Eftersom det alltid är 60 distributioner och varje distribution tilldelas minst 100 MB, är den totala minnesallokeringen i systemet 6 000 MB, eller strax under 6 GB.  

Vissa frågor, t.ex. stora kopplingar eller inläsningar till grupperade columnstore-tabeller, kan dra nytta av större minnesallokeringar.  Vissa frågor, som rena genomsökningar, ger ingen förmån.  Att använda större resurs klasser minskar dock samtidighet, så du bör ta hänsyn till den här effekten innan du flyttar alla dina användare till en stor resurs klass.

Se även [resurs klasser för hantering av arbets belastning](resource-classes-for-workload-management.md).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Använda den mindre resursklassen för bättre prestanda vid samtidig körning

Om du märker att användar frågor verkar ha lång fördröjning, kan det bero på att användarna körs i större resurs klasser och att de använder många samtidiga aktivitets platser som orsakar andra frågor att köa.  Du kan se om användarnas frågor köas genom att köra `SELECT * FROM sys.dm_pdw_waits` för att se om några rader returneras.

Se även [resurs klasser för hantering av arbets belastning](resource-classes-for-workload-management.md), [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="other-resources"></a>Andra resurser

Information om vanliga problem och lösningar finns i vår [felsökningsartikel](sql-data-warehouse-troubleshoot.md).

Om du inte hittar det du letar efter i den här artikeln kan du prova att använda "Sök efter dokument" på vänster sida av den här sidan för att söka i alla Azure Synapse-dokument.  På [sidan Microsoft Q&en fråga för Azure Synapse](/answers/topics/azure-synapse-analytics.html) kan du skicka frågor till andra användare och till produkt gruppen för Azure Synapse. Vi övervakar aktivt detta forum för att kontrollera att dina frågor besvaras antingen av en annan användare eller av någon av oss.  

Om du föredrar att ställa dina frågor på Stack Overflow har vi också ett [Azure Synapse Stack Overflow-forum](https://stackoverflow.com/questions/tagged/azure-sqldw).

Använd feedback-sidan för [Azure-Synapse](https://feedback.azure.com/forums/307516-sql-data-warehouse) för att skapa funktions begär Anden.  Genom att skicka in dina egna önskemål eller rösta fram andras förfrågningar hjälper du oss att prioritera funktioner.