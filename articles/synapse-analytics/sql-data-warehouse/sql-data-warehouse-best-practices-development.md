---
title: Bästa metoder för utveckling
description: Rekommendationer och metod tips du bör känna till när du utvecklar lösningar för Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bc101e6bb743b9290593937a4d1bac656430b438
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685318"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Bästa metoder för utveckling för Synapse SQL-pool

I den här artikeln beskrivs vägledning och bästa praxis när du utvecklar din lösning för SQL-pooler.

## <a name="tune-query-performance-with-new-product-enhancements"></a>Justera prestanda för frågor med nya produkt förbättringar

- [Prestandajustering med materialiserade vyer](performance-tuning-materialized-views.md)
- [Prestandajustering med grupperade kolumnlagringsindex](performance-tuning-ordered-cci.md)
- [Prestandajustering med cachelagring av resultatuppsättningar](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Minska kostnaderna genom att pausa och skala

Mer information om hur du minskar kostnaderna genom att pausa och skala finns i artikeln [Hantera beräkning](sql-data-warehouse-manage-compute-overview.md) .

## <a name="maintain-statistics"></a>Underhålla statistik

SQL-poolen kan konfigureras för att automatiskt identifiera och skapa statistik för kolumner.  De fråge planer som skapats av optimeringen är bara lika lämpliga som tillgängliga statistik.  

Vi rekommenderar att du aktiverar AUTO_CREATE_STATISTICS för dina databaser och håller statistiken uppdaterad dagligen eller efter varje belastning för att säkerställa att statistik för kolumner som används i dina frågor alltid är uppdaterade.

Om du tycker att det tar för lång tid att uppdatera all statistik, kanske du vill försöka bli mer selektiv om vilka kolumner som behöver frekventa statistik uppdateringar. Du kanske till exempel vill uppdatera datumkolumner, där nya värden kan läggas till, varje dag.

> [!TIP]
> Du får störst nytta genom att ha uppdaterad statistik för kolumner som ingår i kopplingar, kolumner som används i WHERE-satsen och kolumner som finns i GROUP BY.

Se även [Hantera tabell statistik](sql-data-warehouse-tables-statistics.md), [skapa statistik](sql-data-warehouse-tables-statistics.md)och [Uppdatera statistik](sql-data-warehouse-tables-statistics.md#update-statistics).

## <a name="hash-distribute-large-tables"></a>Hash-distribuera stora tabeller

Tabeller distribueras som standard med resursallokering (Round Robin).  Den här designen gör det enkelt för användarna att komma igång med att skapa tabeller utan att behöva bestämma hur deras tabeller ska distribueras.  

Resursallokeringstabeller kan prestera bra för vissa arbetsbelastningar, men i de flesta fall blir prestanda bättre om du väljer en distributionskolumn.  Det vanligaste exemplet på när en tabell som distribueras med en kolumn presterar avsevärt mycket bättre än en resursallokeringstabell är när två stora faktatabeller kopplas.  

Om du till exempel har en ordertabell, som distribueras efter order_id, och en transaktionstabell, som också distribueras efter order_id, och du kopplar ordertabellen till transaktionstabellen baserat på order_id, så blir den här frågan en direktfråga, vilket innebär att vi eliminerar dataflyttningsåtgärder.  Färre steg innebär en snabbare fråga.  Mindre dataflyttning gör också att frågor körs snabbare.  

När du läser in en distribuerad tabell ser du till att inkommande data inte sorteras baserat på distributionsnyckeln eftersom det gör att inläsningarna tar längre tid.  De artiklar som följer ger ytterligare information om hur du kan förbättra prestanda genom att välja en distributions kolumn och definiera en distribuerad tabell i WITH-satsen i instruktionen CREATE TABLEs.

Se även [tabell översikt](sql-data-warehouse-tables-overview.md), [tabell distribution](sql-data-warehouse-tables-distribute.md), [val av tabell distribution](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service), [CREATE TABLE](sql-data-warehouse-tables-overview.md)och [CREATE TABLE som Välj](sql-data-warehouse-develop-ctas.md)

## <a name="do-not-over-partition"></a>Överpartitionera inte

När du partitionerar data kan det vara effektivt att underhålla dina data genom att byta partitionering eller optimera genomsökningar med hjälp av partition Eli minering, så att för många partitioner kan sakta ned dina frågor.  

Ofta fungerar en partitionerings strategi för hög granularitet som fungerar bra på SQL Server kanske inte fungerar bra på SQL-poolen.  För många partitioner kan också minska effektiviteten i grupperade columnstore-index om varje partition har färre än 1 miljoner rader.  

Tänk på att när du skapar en tabell med 100 partitioner i 60 bakgrunden, så resulterar det i att du skapar en tabell med partitioner. Detta leder faktiskt till 6000-partitioner.  Alla arbetsbelastningar är olika så det bästa rådet är att experimentera med partitioneringen för att se vad som fungerar bäst med din arbetsbelastning.  

> [!TIP]
> Överväg att använda en lägre kornig het än vad som kan ha fungerat i SQL Server.  Överväg exempelvis att använda veckovisa eller månadsvisa partitioneringar i stället för dagliga.

Se även [tabell partitionering](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Minimera transaktionsstorlekar

INSERT-, UPDATE- och DELETE-instruktioner körs i en transaktion och när de misslyckas måste de återställas.  Du kan minimera risken för en lång återställning genom att minska transaktionsstorlekarna om det går.  Du kan göra det genom att dela upp INSERT-, UPDATE- och DELETE-instruktioner i flera delar.  

Om du t. ex. har en INFOGNING, som du förväntar dig att ta 1 timme, om möjligt, kan du dela upp den i fyra delar, som var och en körs på 15 minuter.  Använd särskilda minimala loggnings fall, t. ex. CTAS, TRUNKERA, släpp tabell eller infoga i tomma tabeller, för att minska återställnings risken.  

Ett annat sätt att eliminera återställningar är att använda åtgärder med endast metadata, t.ex. med partitionsväxling, för datahantering.  Till exempel kan du, i stället för att köra en DELETE-instruktion för att ta bort alla rader i en tabell där order_date var i oktober 2001, partitionera dina data månadsvis och sedan byta ut partitionen med data mot en tom partition från en annan tabell (se ALTER TABLE-exemplen).  

För opartitionerade tabeller bör du överväga att använda en CTAS för att skriva de data som du vill behålla i en tabell i stället för att använda ta bort.  Om en CTAS tar samma tids period, är det en mycket säkrare åtgärd att köra eftersom den har minimal transaktions loggning och kan avbrytas snabbt om det behövs.

Se även [förstå transaktioner](sql-data-warehouse-develop-transactions.md), [optimera transaktioner](sql-data-warehouse-develop-best-practices-transactions.md), [tabell partitionering](sql-data-warehouse-tables-partition.md), [truncate Table](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ändra tabell](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)och [Skapa tabell som Select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="use-the-smallest-possible-column-size"></a>Använda minsta möjliga kolumnstorlek

När du definierar DDL: en med den minsta data typen som kommer att ge stöd för dina data förbättras frågans prestanda.  Den här metoden är särskilt viktig för kolumnerna CHAR och VARCHAR.  

Om det längsta värdet i en kolumn är 25 tecken definierar du kolumnen som VARCHAR(25).  Undvik att definiera alla teckenkolumner med en stor standardlängd.  Definiera också kolumner som VARCHAR när det är allt som krävs i stället för att använda NVARCHAR.

Se även [tabell översikt](sql-data-warehouse-tables-overview.md), [tabell data typer](sql-data-warehouse-tables-data-types.md)och [CREATE TABLE](sql-data-warehouse-tables-overview.md).

## <a name="optimize-clustered-columnstore-tables"></a>Optimera grupperade columnstore-tabeller

Grupperade columnstore-index är ett av de mest effektiva sätten att lagra data i SQL-poolen.  Som standard skapas tabeller i SQL-poolen som grupperade ColumnStore-tabeller.  

> [!NOTE]
> För att uppnå optimala prestanda för frågor i columnstore-tabeller är det viktigt att ha bra segment kvalitet.  

När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  Segmentkvaliteten kan mätas utifrån antalet rader i en komprimerad radgrupp.  

Se [orsaker till dålig kolumn kvalitet för columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) i [tabellen tabell index](sql-data-warehouse-tables-index.md) för stegvisa instruktioner för att identifiera och förbättra segment kvaliteten för grupperade columnstore-tabeller.  

Eftersom ett columnstore-segment av hög kvalitet är viktigt är det en bra idé att använda användar-ID: n som finns i resurs klassen medel eller stor för att läsa in data. Genom att använda lägre [data lager enheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) kan du tilldela en större resurs klass till din inläsnings användare.

Eftersom columnstore-tabeller vanligt vis inte skickar data till ett komprimerat columnstore-segment förrän det finns fler än 1 000 000 rader per tabell och varje SQL-adresspool är partitionerad i 60-tabeller, kommer columnstore-tabeller inte att dra nytta av en fråga om tabellen har fler än 60 000 000 rader.  

För en tabell med färre än 60 000 000 rader kanske det inte är någon mening att ha ett columnstore-index.  Men det skadar inte heller.  

Om du partitionerar data bör du dessutom tänka på att varje partition måste innehålla 1 miljon rader för att kunna dra nytta av ett grupperat columnstore-index.  Om en tabell har 100 partitioner måste den ha minst 6 000 000 000 rader för att kunna dra nytta av ett lager för grupperade kolumner (60-distributioner *100 partitioner* 1 000 000 rader).  

Om din tabell inte innehåller 6 miljarder rader i det här exemplet minskar du antalet partitioner eller överväger att använda en heap-tabell i stället.  Det kan också vara värt att experimentera och se om du kan förbättra prestanda med en heap-tabell med sekundära index i stället för en columnstore-tabell.

> [!TIP]
> När du kör frågor mot en columnstore-tabell körs frågorna snabbare om du bara väljer de kolumner som du behöver.  

Se även [tabell index](sql-data-warehouse-tables-index.md), [columnstore-index, guide](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)och [Återskapa columnstore-index](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="next-steps"></a>Nästa steg

Om du inte hittar det du letar efter i den här artikeln kan du prova att använda "Sök efter dokument" på vänster sida av den här sidan för att söka i alla Azure Synapse-dokument.  

På [sidan Microsoft Q&en fråga för Azure Synapse](/answers/topics/azure-synapse-analytics.html) kan du skicka frågor till andra användare och till produkt gruppen för Azure Synapse.  Vi övervakar aktivt detta forum för att kontrollera att dina frågor besvaras antingen av en annan användare eller av någon av oss.  

Om du föredrar att ställa dina frågor på Stack Overflow har vi också ett [Azure Synapse Analytics Stack Overflow-forum](https://stackoverflow.com/questions/tagged/azure-sqldw).

Använd feedback-sidan för [Azure-Synapse](https://feedback.azure.com/forums/307516-sql-data-warehouse) för att skapa funktions begär Anden.  Genom att skicka in dina egna önskemål eller rösta fram andras förfrågningar hjälper du oss att prioritera funktioner.