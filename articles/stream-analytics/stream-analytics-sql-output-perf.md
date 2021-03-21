---
title: Öka data flödes prestandan till Azure SQL Database från Azure Stream Analytics
description: Lär dig mer om att lägga till data i SQL Azure från Azure Stream Analytics och få högre Skriv data flödes nivåer.
author: chetanmsft
ms.author: chetang
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 8baa33c8d9622ff76db04345f5c6c465f026e261
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020238"
---
# <a name="increase-throughput-performance-to-azure-sql-database-from-azure-stream-analytics"></a>Öka data flödes prestandan till Azure SQL Database från Azure Stream Analytics

Den här artikeln beskriver tips för att få bättre Skriv data flödes prestanda när du läser in data i Azure SQL Database med Azure Stream Analytics.

SQL-utdata i Azure Stream Analytics stöder skrivning parallellt som ett alternativ. Med det här alternativet kan du [helt parallella](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) jobb topologier, där flera utgående partitioner skrivs till mål tabellen parallellt. Att aktivera det här alternativet i Azure Stream Analytics kanske inte räcker för att uppnå högre data flöden, eftersom det är beroende av databas konfigurationen och tabell schemat. Valet av index, kluster nyckel, index fyllnings faktor och komprimering påverkar tiden för att läsa in tabeller. Mer information om hur du optimerar din databas för att förbättra frågor och läsa in prestanda utifrån interna benchmarks finns i [SQL Database prestanda vägledning](../azure-sql/database/performance-guidance.md). Sortering av skrivningar är inte garanterat vid skrivning parallellt till SQL Database.

Här följer några konfigurationer i varje tjänst som kan hjälpa till att förbättra det totala data flödet i din lösning.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Ärv partitionering** – det här alternativet för konfiguration av SQL-utdata gör att du kan ärva partitionerings schemat i föregående fråge steg eller indata. Med den här inställningen aktive rad, skriver du till en disk-baserad tabell och har en [helt parallell](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topologi för ditt jobb, vilket förväntar dig att se bättre data flöden. Den här partitionen sker redan automatiskt för många andra [utdata](stream-analytics-parallelization.md#partitions-in-inputs-and-outputs). Tabell låsning (TABLOCK) är också inaktiverat för Mass infogningar som görs med det här alternativet.

> [!NOTE] 
> Om det finns fler än 8 inpartitioner är det inte säkert att du väljer att ärva schemat för inpartitionering. Den övre gränsen har observerats i en tabell med en enda identitets kolumn och ett grupperat index. I det här fallet kan du använda [till](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 i frågan för att uttryckligen ange antalet utgående skrivare. Dina observationer kan variera beroende på schemat och valet av index.

- **Batchstorlek** – med konfiguration av SQL-utdata kan du ange den maximala batchstorleken i en Azure Stream Analytics SQL-utdata baserat på typen av mål tabell/arbets belastning. Batchstorlek är det maximala antalet poster som skickas med varje Mass infognings transaktion. I grupperade columnstore-index kan batch-storlekarna runt [100 000](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) tillåta mer parallellisering, minimal loggning och lås optimeringar. I diskbaserade tabeller kan 10K (standard) eller lägre värde vara optimalt för din lösning, eftersom högre batch-storlekar kan utlösa lås eskalering under Mass infogningar.

- **Justering av indatameddelande av meddelanden** – om du har optimerat med Ärv partitionering och batchstorlek, ökar antalet indata-händelser per meddelande per partition, vilket gör att du kan överföra ditt Skriv-genomflöde ytterligare. Med justering av indata-meddelanden kan batchstorleken i Azure Stream Analytics vara upp till den angivna batchstorleken, vilket förbättrar data flödet. Detta kan uppnås med hjälp av [komprimering](stream-analytics-define-inputs.md) eller ökande storlekar för indatamängds meddelanden i EventHub eller BLOB.

## <a name="sql-azure"></a>SQL Azure

- **Partitionerade tabeller och index** – med hjälp av en [partitionerad](/sql/relational-databases/partitions/partitioned-tables-and-indexes) SQL-tabell och partitionerade index i tabellen med samma kolumn som din partitionsnyckel (till exempel PartitionID) kan du avsevärt minska inblandning mellan partitioner under skrivningar. För en partitionerad tabell måste du skapa en [partitions funktion](/sql/t-sql/statements/create-partition-function-transact-sql) och ett [PARTITIONSSCHEMA](/sql/t-sql/statements/create-partition-scheme-transact-sql) på den primära fil gruppen. Detta kommer också att öka tillgängligheten för befintliga data medan nya data läses in. Loggens IO-gräns kan uppnås baserat på antalet partitioner som kan ökas genom att du uppgraderar SKU: n.

- **Undvik unika nyckel överträdelser** – om du får [varnings meddelanden om flera nyckel](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) överträdelser i Azure Stream Analytics aktivitets loggen, kontrollerar du att jobbet inte påverkas av unika begränsnings överträdelser som sannolikt kommer att inträffa under återställnings fall. Detta kan undvikas genom att ange alternativet för att [Ignorera \_ duplicera- \_ nyckeln](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) i dina index.

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory och In-Memory tabeller

- **InMemory-tabell som temporär tabell** – [i-minnes tabeller](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) möjliggör mycket data inläsning av data, men data måste anpassas i minnet. Benchmarks Visa Mass inläsning från en InMemory-tabell till en diskbaserad tabell är ungefär 10 gånger snabbare än att infoga direkt Mass infogning med hjälp av en enda skrivare i den diskbaserade tabellen med en identitets kolumn och ett grupperat index. Om du vill dra nytta av den här Mass infogningen måste du konfigurera ett [kopierings jobb med Azure Data Factory](../data-factory/connector-azure-sql-database.md) som kopierar data från InMemory-tabellen till den diskbaserade tabellen.

## <a name="avoiding-performance-pitfalls"></a>Undvika prestanda fall GRO par
Mass infogning av data är mycket snabbare än att läsa in data med enskilda infogningar eftersom den upprepade omkostnaderna vid överföring av data, parsning av INSERT-instruktionen, körning av instruktionen och utfärdande av transaktions poster undviks. I stället används en mer effektiv sökväg i lagrings motorn för att strömma data. Installations kostnaden för den här sökvägen är dock mycket högre än en enda INSERT-instruktion i en diskbaserad tabell. Bryt punkten är vanligt vis runt 100 rader, utöver vilken Mass inläsning är nästan alltid mer effektivt. 

Om frekvensen inkommande händelser är låg kan det enkelt skapa batchgrupper som är mindre än 100 rader, vilket gör Mass infogningen ineffektiv och använder för mycket disk utrymme. Du kan undvika den här begränsningen genom att utföra någon av följande åtgärder:
* Skapa en i stället för [utlösare](/sql/t-sql/statements/create-trigger-transact-sql) för att använda enkel infogning för varje rad.
* Använd en In-Memory temporär tabell enligt beskrivningen i föregående avsnitt.

Ett annat sådant scenario uppstår när du skriver till ett icke-grupperat columnstore-index (NCCI), där mindre Mass infogningar kan skapa för många segment, vilket kan krascha indexet. I det här fallet är rekommendationen att använda ett grupperat columnstore-index i stället.

## <a name="summary"></a>Sammanfattning

I sammanfattning, med funktionen för partitionerade utdata i Azure Stream Analytics för SQL-utdata, bör parallellisering av jobbet med en partitionerad tabell i SQL Azure ge dig betydande data flödes förbättringar. Genom att använda Azure Data Factory för att dirigera data förflyttning från en In-Memory tabell till diskbaserade tabeller kan du ange hur stor data flödes vinster är. Om möjligt kan det också vara en större faktor att förbättra meddelande tätheten för att förbättra det totala data flödet.
