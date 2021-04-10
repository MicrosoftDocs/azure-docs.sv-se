---
title: Bästa metoder för utveckling för Synapse SQL
description: Rekommendationer och metod tips du bör känna till när du utvecklar för Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 479b20bcb0803d5483d139939da627d53ceccb49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667618"
---
# <a name="development-best-practices-for-synapse-sql"></a>Bästa metoder för utveckling för Synapse SQL

I den här artikeln beskrivs vägledning och bästa praxis när du utvecklar din lösning för data lager. 

## <a name="dedicated-sql-pool-development-best-practices"></a>Bästa metoder för utveckling av dedikerade SQL-pooler

### <a name="reduce-cost-with-pause-and-scale"></a>Minska kostnaderna genom att pausa och skala

Läs mer om att minska kostnaderna genom att pausa och skala i [Hantera beräkning](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Underhålla statistik

Se till att du uppdaterar statistiken dagligen eller efter varje belastning.  Det finns alltid kompromisser mellan prestanda och kostnaden för att skapa och uppdatera statistik. Om du tycker att det tar för lång tid att underhålla all statistik bör du vara mer selektiv om vilka kolumner som har statistik eller vilka kolumner som behöver uppdateras ofta.  

Du kanske till exempel vill uppdatera datum kolumner, där nya värden kan läggas till varje dag. 

> [!NOTE]
> Du får störst nytta genom att ha statistik över kolumner som ingår i kopplingar, kolumner som används i WHERE-satsen och kolumner som finns i GROUP BY.

Se även [Hantera tabell statistik](develop-tables-statistics.md), [skapa statistik](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true), [Uppdatera statistik](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true).

### <a name="hash-distribute-large-tables"></a>Hash-distribuera stora tabeller

Tabeller distribueras som standard med resursallokering (Round Robin). Den här funktionen gör det enkelt för användarna att börja skapa tabeller utan att behöva bestämma hur deras tabeller ska distribueras.  Round Robin-tabeller kan fungera tillräckligt för vissa arbets belastningar. Men i de flesta fall kommer en distributions kolumn att fungera mycket bättre.  

Det vanligaste exemplet på när en tabell som distribueras med en kolumn presterar avsevärt mycket bättre än en resursallokeringstabell är när två stora faktatabeller kopplas.  

Om du till exempel har en tabell med order som distribuerats av order_id och en transaktions tabell, som också distribueras av order_id, och du ansluter tabellen Order till tabellen transaktioner på order_id, blir den här frågan en direkt fråga. 

Det innebär att vi eliminerar åtgärder för data förflyttning.  Färre steg innebär en snabbare fråga.  Mindre dataflyttning gör också att frågor körs snabbare.

> [!TIP]
> När du läser in en distribuerad tabell ser du till att inkommande data inte sorteras baserat på distributionsnyckeln eftersom det gör att inläsningarna tar längre tid.  

Se följande länkar om du vill ha mer information om hur du väljer en distributions kolumn kan förbättra prestanda samt definiera en distribuerad tabell i WITH-satsen i instruktionen CREATE TABLEs.

Se även [tabell översikt](develop-tables-overview.md), [tabell distribution](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [val av tabell distribution](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)och [CREATE TABLE som Välj](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true).

### <a name="do-not-over-partition"></a>Överpartitionera inte
När du partitionerar data kan det vara effektivt att underhålla dina data genom att byta partitionering eller optimera genomsökningar med hjälp av partition Eli minering, så att för många partitioner kan sakta ned dina frågor.  Ofta är en partitionerings strategi för hög granularitet som fungerar bra på SQL Server kanske inte fungerar bra på en dedikerad SQL-pool.  

> [!NOTE]
> Ofta är en partitionerings strategi för hög granularitet som fungerar bra på SQL Server kanske inte fungerar bra på en dedikerad SQL-pool.  

För många partitioner kan också minska effektiviteten i grupperade columnstore-index om varje partition har färre än 1 miljoner rader. Dedikerad SQL-pool partitionerar dina data till 60-databaser. 

Om du skapar en tabell med 100 partitioner blir resultatet 6000 partitioner.  Alla arbetsbelastningar är olika så det bästa rådet är att experimentera med partitioneringen för att se vad som fungerar bäst med din arbetsbelastning.  

Ett alternativ att överväga är att använda en kornig het som är lägre än vad som kan ha fungerat i SQL Server.  Överväg exempelvis att använda veckovisa eller månadsvisa partitioneringar i stället för dagliga.

Se även [tabell partitionering](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="minimize-transaction-sizes"></a>Minimera transaktionsstorlekar

INSERT-, UPDATE- och DELETE-instruktioner körs i en transaktion och när de misslyckas måste de återställas.  Du kan minimera risken för en lång återställning genom att minska transaktionsstorlekarna om det går.  Du kan göra det genom att dela upp INSERT-, UPDATE- och DELETE-instruktioner i flera delar.  

Om du till exempel har en INFOGNING som du förväntar dig att ta 1 timme, kan du dela upp den i fyra delar så att du kan förkorta varje körning till 15 minuter.

> [!TIP]
> Dra nytta av minimal loggning, t.ex. med CTAS, TRUNCATE, DROP TABLE eller INSERT, för att tömma tabeller och minska risken för återställning.  

Ett annat sätt att eliminera återställningar är att använda åtgärder med endast metadata, t.ex. med partitionsväxling, för datahantering.  

Till exempel kan du, i stället för att köra en DELETE-instruktion för att ta bort alla rader i en tabell där order_date var i oktober 2001, partitionera dina data månadsvis och sedan byta ut partitionen med data mot en tom partition från en annan tabell (se ALTER TABLE-exemplen).  

För opartitionerade tabeller bör du överväga att använda en CTAS för att skriva de data som du vill behålla i en tabell i stället för att använda ta bort.  Om en CTAS tar samma tids period, är det en mycket säkrare åtgärd att köra eftersom den har minimal transaktions loggning och kan avbrytas snabbt om det behövs.

Se även [förstå transaktioner](develop-transactions.md), [optimera transaktioner](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [tabell partitionering](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [truncate Table](/sql/t-sql/statements/truncate-table-transact-sql?view=azure-sqldw-latest&preserve-view=true), [ändra tabell](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)och [Skapa tabell som Select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="use-the-smallest-possible-column-size"></a>Använda minsta möjliga kolumnstorlek

När du definierar DDL: en med den minsta data typen som kommer att ge stöd för dina data förbättras frågans prestanda. Den här åtgärden är särskilt viktig för kolumnerna CHAR och VARCHAR.  

Om det längsta värdet i en kolumn är 25 tecken definierar du kolumnen som VARCHAR(25).  Undvik att definiera alla teckenkolumner med en stor standardlängd.  Definiera också kolumner som VARCHAR om det är allt som behövs i stället för att använda NVARCHAR.

Se även [tabell översikt](develop-tables-overview.md), [tabell data typer](develop-tables-data-types.md)och [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true).

### <a name="optimize-clustered-columnstore-tables"></a>Optimera grupperade columnstore-tabeller

Grupperade columnstore-index är ett av de mest effektiva sätten att lagra data i en dedikerad SQL-pool.  Som standard skapas tabeller i dedikerad SQL-pool som grupperade ColumnStore-objekt.  

Det är viktigt att segmentkvaliteten är bra för att uppnå bästa prestanda med frågor mot columnstore-tabeller.  När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  

Segmentkvaliteten kan mätas utifrån antalet rader i en komprimerad radgrupp.  Se artikeln [orsaker till dåliga kolumn kvalitets](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) -och [tabell index](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) för att få stegvisa instruktioner för att identifiera och förbättra segment kvaliteten för grupperade columnstore-tabeller.  

Eftersom ett columnstore-segment av hög kvalitet är viktigt är det en bra idé att använda användar-ID: n som finns i resurs klassen medel eller stor för att läsa in data. Genom att använda lägre [data lager enheter](resource-consumption-models.md) kan du tilldela en större resurs klass till din inläsnings användare.

Eftersom columnstore-tabeller vanligt vis inte skickar data till ett komprimerat columnstore-segment förrän det finns fler än 1 000 000 rader per tabell, och varje dedikerad SQL-adresspool är partitionerad i 60-tabeller, kommer columnstore-tabeller inte att dra nytta av en fråga om tabellen har fler än 60 000 000 rader.  

> [!TIP]
> För tabeller med färre än 60 000 000 rader är det inte säkert att ett columnstore-index är den optimala lösningen.  

Om du partitionerar dina data måste du dessutom tänka på att varje partition måste ha 1 000 000 rader för att dra nytta av ett grupperat columnstore-index.  Om en tabell har 100 partitioner måste den ha minst 6 000 000 000 rader för att kunna dra nytta av ett lager för grupperade kolumner (60-distributioner *100 partitioner* 1 000 000 rader).  

Om din tabell inte har 6 000 000 000 rader kan du antingen minska antalet partitioner eller överväga att använda en heap-tabell i stället.  Det kan också vara värt att experimentera för att se om bättre prestanda kan uppnås med hjälp av en heap-tabell med sekundära index i stället för en columnstore-tabell.

När du kör frågor mot en columnstore-tabell körs frågorna snabbare om du bara väljer de kolumner som du behöver.  

Se även [tabell index](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [columnstore-index, guide](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true), [Återskapa columnstore-index](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality).

## <a name="serverless-sql-pool-development-best-practices"></a>Metod tips för utveckling av SQL-pooler utan Server

### <a name="general-considerations"></a>Generella saker att tänka på

Med Server lös SQL-pool kan du söka efter filer i dina Azure Storage-konton. Den har inte funktioner för lokal lagring eller inmatning, vilket innebär att alla filer som fråge målen är externa för SQL-poolen utan server. Allt som rör läsning av filer från lagring kan därför påverka frågans prestanda.

### <a name="colocate-azure-storage-account-and-serverless-sql-pool"></a>Samplacera Azure Storage konto och SQL-pool utan Server

Du kan minimera svars tiden genom att samplacera ditt Azure Storage-konto och slut punkten för SQL-poolen utan server. Lagrings konton och slut punkter som tillhandahålls när arbets ytan skapas befinner sig i samma region.

För optimala prestanda bör du kontrol lera att de finns i samma region om du har åtkomst till andra lagrings konton med en server lös SQL-pool. Annars ökar svars tiden för data överföring från fjärrregionen till slut punktens region.

### <a name="azure-storage-throttling"></a>Azure Storage begränsning

Flera program och tjänster kan komma åt ditt lagrings konto. När den kombinerade IOPS eller data flödet som genereras av program, tjänster och arbets belastningen på den serverbaserade SQL-poolen överskrider lagrings kontots gränser, sker lagrings begränsningen. När lagrings begränsningen inträffar finns det en betydande negativ inverkan på frågans prestanda.

När begränsningen har identifierats har SQL-poolen utan Server en inbyggd hantering av det här scenariot. SQL-poolen utan Server gör begär anden till lagringen långsammare tills begränsningen har lösts. 

För optimal frågekörning rekommenderar vi dock att du inte överbelastar lagrings kontot med andra arbets belastningar under frågekörningen.

### <a name="prepare-files-for-querying"></a>Förbered filer för frågor

Om möjligt kan du förbereda filer för bättre prestanda:

- Konvertera CSV till Parquet – Parquet är kolumn format. Eftersom den är komprimerad har den mindre fil storlekar än CSV-filer med samma data, och SQL-poolen utan server behöver mindre tid och lagrings begär Anden för att kunna läsa den.
- Om en fråga är riktad mot en enda stor fil kan du dra nytta av att dela upp den på flera mindre filer.
- Försök att behålla CSV-filens storlek under 10 GB.
- Det är önskvärt att ha lika stora filer för en enskild OpenRowSet-sökväg eller en extern tabell plats.
- Partitionera dina data genom att lagra partitioner i olika mappar eller fil namn – kontrol lera [Använd fil namn och fil Sök väg funktioner för att ange specifika partitioner](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Använd funktionerna fileinfo och filepath till att fokusera på specifika partitioner

Data är ofta ordnade i partitioner. Du kan instruera Server lös SQL-poolen att fråga specifika mappar och filer. Detta minskar antalet filer och mängden data som frågan behöver läsa och bearbeta. 

Därför kommer du att få bättre prestanda. Mer information finns i funktioner för [fil namn](query-data-storage.md#filename-function) och fil [Sök väg](query-data-storage.md#filepath-function) och exempel på hur du [frågar efter vissa filer](query-specific-files.md).

Om dina data i lagringen inte är partitionerade bör du överväga att partitionera dem så att du kan använda dessa funktioner för att optimera frågor som riktar sig mot dessa filer.

När du [frågar partitionerade Apache Spark för externa Azure Synapse-tabeller](develop-storage-files-spark-tables.md) från en server lös SQL-pool, kommer frågan automatiskt att endast rikta in de filer som behövs.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Använd CETAS för att förbättra frågornas prestanda och kopplingar

[CETAS](develop-tables-cetas.md) är en av de viktigaste funktionerna som finns i SQL-poolen utan server. CETAS är en parallell åtgärd som skapar externa tabellens metadata och exporterar resultatet av URVALs frågan till en uppsättning filer i ditt lagrings konto.

Du kan använda CETAS för att lagra ofta använda delar av frågor som kopplade referens tabeller till en ny uppsättning filer. Senare kan du koppla till den här enskilda externa tabellen i stället för att upprepa vanliga kopplingar i flera frågor. 

När CETAS genererar Parquet-filer skapas statistik automatiskt när den första frågan riktar sig till den här externa tabellen och du får bättre prestanda.

### <a name="next-steps"></a>Nästa steg

Om du behöver information som inte anges i den här artikeln använder du funktionen **Sök efter dokument** till vänster på den här sidan för att söka i alla dokument i SQL-poolen.  På [sidan Microsoft Q&en fråga för Azure Synapse Analytics](/answers/topics/azure-synapse-analytics.html) kan du ställa frågor till andra användare och till produkt gruppen för Azure Synapse Analytics. Vi övervakar aktivt detta forum för att kontrollera att dina frågor besvaras antingen av en annan användare eller av någon av oss.  

Om du föredrar att ställa dina frågor på Stack Overflow har vi också ett [Azure Synapse Analytics Stack Overflow-forum](https://stackoverflow.com/questions/tagged/azure-sqldw).
