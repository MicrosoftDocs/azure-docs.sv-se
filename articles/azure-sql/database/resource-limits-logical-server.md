---
title: Resurs begränsningar för logiska servrar i Azure
description: Den här artikeln innehåller en översikt över resurs gränserna för den logiska servern i Azure som används av Azure SQL Database och Azure Synapse Analytics. Den innehåller också information om vad som händer när dessa resurs gränser nåtts eller överskrids.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 03/25/2021
ms.openlocfilehash: 5e95bc50a74413389bd2583beb90128b3fd0810a
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543524"
---
# <a name="resource-limits-for-azure-sql-database-and-azure-synapse-analytics-servers"></a>Resurs gränser för Azure SQL Database-och Azure Synapse Analytics-servrar
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Den här artikeln innehåller en översikt över resurs gränserna för den [logiska servern](logical-servers.md) som används av Azure SQL Database och Azure Synapse Analytics. Den innehåller information om vad som händer när dessa resurs gränser nåtts eller överskrids och beskriver de resurs styrnings metoder som används för att genomdriva dessa gränser.

> [!NOTE]
> För Azure SQL-hanterade instans gränser, se [resurs gränser för hanterade instanser](../managed-instance/resource-limits.md).

## <a name="maximum-resource-limits"></a>Högsta antal resurs gränser

| Resurs | Gräns |
| :--- | :--- |
| Databaser per logisk server | 5000 |
| Standard antal logiska servrar per prenumeration i en region | 20 |
| Högsta antal logiska servrar per prenumeration i en region | 200 |  
| Kvot för DTU/eDTU per logisk server | 54 000 |  
| vCore kvot per logisk server | 540 |
| Högsta antal pooler per logisk server | Begränsas av antalet DTU: er eller virtuella kärnor. Om varje pool till exempel är 1000 DTU: er, kan en server stödja 54-pooler.|
|||

> [!IMPORTANT]
> När antalet databaser närmar sig gränsen per logisk server kan följande inträffa:
>
> - Ökande svars tid för att köra frågor mot huvud databasen.  Detta inkluderar vyer av statistik över resursutnyttjande, till exempel `sys.resource_stats` .
> - Ökande svars tid i hanterings åtgärder och åter givning av Portal synvinklar som innefattar att räkna upp databaser på servern.

> [!NOTE]
> För att få mer DTU/eDTU-kvot, vCore kvot eller flera logiska servrar än standard beloppet skickar du en ny supportbegäran i Azure Portal. Mer information finns i [begäran om kvot ökning för Azure SQL Database](quota-increase-request.md).

### <a name="storage-size"></a>Lagrings storlek

För resurs lagrings storlekar för enskilda databaser, referera till antingen [DTU-baserade resurs gränser](resource-limits-dtu-single-databases.md) eller [vCore resurs gränser](resource-limits-vcore-single-databases.md) för lagrings storleks gränser per pris nivå.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Vad händer när databas resurs gränser nås

### <a name="compute-cpu"></a>Beräknings-CPU

När databas beräknings processor användningen blir hög ökar svars tiden för frågor och frågor kan till och med ta lång tid. Under dessa villkor kan frågor placeras i kö av tjänsten och de tillhandahålls resurser för att köras när resurser blir kostnads fria.
När du räknar med hög beräknings användning är följande alternativ för minskning:

- Öka beräknings storleken för databasen eller den elastiska poolen för att tillhandahålla databasen med fler beräknings resurser. Se [skala resurser för enkel databas](single-database-scale.md) och [skala elastiska pooler](elastic-pool-scale.md).
- Optimera frågor för att minska användningen av CPU-resurser för varje fråga. Mer information finns i avsnittet om [frågeoptimering och frågetips](performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

När databas utrymmet som används når den maximala storleks gränsen, infogas och uppdateras databasen som ökar data storleken och klienterna får ett [fel meddelande](troubleshoot-common-errors-issues.md). SELECT-och DELETE-instruktioner fortsätter att fungera.

När du ska räkna med hög användnings utrymme är alternativen för minskning:

- Öka den maximala storleken på databasen eller den elastiska poolen eller lägga till mer lagrings utrymme. Se [skala resurser för enkel databas](single-database-scale.md) och [skala elastiska pooler](elastic-pool-scale.md).
- Om databasen finns i en elastisk pool kan du eventuellt flytta databasen utanför poolen så att lagrings utrymmet inte delas med andra databaser.
- Krymp en databas för att frigöra outnyttjat utrymme. Mer information finns i [Hantera fil utrymme i Azure SQL Database](file-space-manage.md).
- Kontrol lera om det går att använda högt utrymme på grund av en ökning i storleken på det beständiga versions arkivet (PVS). PVS är en del av varje databas och används för att implementera  [accelererad databas återställning](../accelerated-database-recovery.md). För att fastställa aktuell PVS-storlek, se [fel sökning av PVS](/sql/relational-databases/accelerated-database-recovery-management#troubleshooting). En vanlig orsak till stor PVS-storlek är en transaktion som är öppen under en längre tid (timmar), vilket förhindrar rensning av äldre versioner i PVS.

### <a name="sessions-and-workers-requests"></a>Sessioner och arbetare (begär Anden)

Det högsta antalet sessioner och arbets tagare bestäms av tjänst nivån och beräknings storleken (DTU: er/eDTU: er eller virtuella kärnor). Nya begär Anden avvisas när sessioner eller arbets gränser nås och klienter får ett fel meddelande. Även om antalet tillgängliga anslutningar kan styras av programmet är antalet samtidiga arbetare ofta svårare att uppskatta och kontrol lera. Detta gäller särskilt under belastnings perioder när databas resurs gränser har nåtts och arbets grupper registreras på grund av längre körnings frågor, stora spärrnings kedjor eller alltför lång frågans parallellitet.

När du räknar med hög arbets belastning eller arbets belastning, är alternativ för minskning följande:

- Öka tjänst nivån eller beräknings storleken för databasen eller den elastiska poolen. Se [skala resurser för enkel databas](single-database-scale.md) och [skala elastiska pooler](elastic-pool-scale.md).
- Optimering av frågor för att minska resursutnyttjande för varje fråga om orsaken till ökad arbets belastning beror på konkurrens för beräknings resurser. Mer information finns i avsnittet om [frågeoptimering och frågetips](performance-guidance.md#query-tuning-and-hinting).
- Minska inställningen för [MAXDOP](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (maximal grad av parallellitet).
- Optimera fråge arbets belastningen för att minska antalet förekomster och varaktighet för frågans blockering. Mer information finns i [förstå och lösa problem med att blockera Azure SQL](understand-resolve-blocking.md).

### <a name="memory"></a>Minne

Till skillnad från andra resurser (CPU, arbetare, lagring) som når minnes gränsen påverkar inte frågans prestanda negativt, och orsakar inga fel och fel. Som det beskrivs i detalj i [arkitektur guiden för minnes hantering](/sql/relational-databases/memory-management-architecture-guide), använder SQL Server Database Engine ofta allt tillgängligt minne, efter design. Minne används främst för cachelagring av data för att undvika dyrare lagrings åtkomst. Den högre minnes användningen förbättrar därför vanligt vis frågornas prestanda på grund av snabbare läsningar från minnet, i stället för långsammare läsningar från lagringen.

När databas motorn startades, när arbets belastningen börjar läsa data från lagring cachelagrar databas motorn aggressivt data i minnet. Efter den här inledande rampering-perioden är det vanligt och förväntas se till att `avg_memory_usage_percent` `avg_instance_memory_percent` kolumnerna i [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) vara nära eller lika med 100%, särskilt för databaser som inte är inaktiva och som inte ryms i minnet.

Förutom Datacachen används minnet i andra komponenter i databas motorn. När det finns efter frågan på minnet och allt tillgängligt minne har använts av Datacachen, krymper databas motorn dynamiskt datacache-storlek för att göra minnet tillgängligt för andra komponenter och kommer att utöka Datacachen dynamiskt när andra komponenter frigör minne.

I sällsynta fall kan en tillräckligt mycket krävande arbets belastning orsaka ett otillräckligt minnes tillstånd, vilket leder till minnes brist. Detta kan ske på valfri minnes användnings nivå mellan 0% och 100%. Detta är mer troligt vid mindre beräknings storlekar som har proportionellt mindre minnes gränser och/eller med arbets belastningar som använder mer minne för bearbetning av frågor, till exempel i [kompakta elastiska pooler](elastic-pool-resource-management.md).

När du ska räkna ut minnes fel visas följande alternativ för minskning:
- Öka tjänst nivån eller beräknings storleken för databasen eller den elastiska poolen. Se [skala resurser för enkel databas](single-database-scale.md) och [skala elastiska pooler](elastic-pool-scale.md).
- Optimera frågor och konfiguration för att minska minnes användningen. Vanliga lösningar beskrivs i följande tabell.

|Lösning|Description|
| :----- | :----- |
|Minska storleken på minnes bidrag|Mer information om minnes bidrag finns i blogg inlägget om att [förstå SQL Server minnes tilldelning](https://techcommunity.microsoft.com/t5/sql-server/understanding-sql-server-memory-grant/ba-p/383595) . En vanlig lösning för att undvika alltför stora minnes bidrag håller [statistiken](/sql/relational-databases/statistics/statistics) uppdaterad. Detta resulterar i mer exakta uppskattningar av minnes förbrukning av frågemotor, vilket undviker onödigt stora minnes bidrag.</br></br>I databaser som använder kompatibilitetsnivå 140 och senare, kan databas motorn automatiskt justera minnes tilldelningens storlek med hjälp av [återkoppling i batch-läge](/sql/relational-databases/performance/intelligent-query-processing#batch-mode-memory-grant-feedback). I databaser som använder kompatibilitetsnivå 150 och senare, använder databas motorn samma [återkoppling i rad läge](/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)för att ge fler vanliga frågor om rad läge. Den här inbyggda funktionen hjälper till att undvika minnes brists fel på grund av onödigt stora minnes bidrag.|
|Minska storleken på cachen för frågeplan|Databas motorn cachelagrar fråge planer i minnet för att undvika att kompilera en frågeplan för varje frågekörningen. För att undvika överdriven storlek av frågor som orsakas av programplaner som bara används en gång, aktiverar du den OPTIMIZE_FOR_AD_HOC_WORKLOADS [databas-omfångs konfigurationen](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).|
|Minska storleken på Lås minnet|Databas motorn använder minne för [Lås](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#Lock_Engine). Undvik, om möjligt, stora transaktioner som kan förvärva ett stort antal lås och orsaka minnes förbrukning med hög lock.|


## <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Resursförbrukning efter användar arbets belastningar och interna processer

PROCESSOR-och minnes förbrukning av användar arbets belastningar i varje databas rapporteras i [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) och [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) vyer, i `avg_cpu_percent` och `avg_memory_usage_percent` kolumner. För elastiska pooler rapporteras resursförbrukning på resurs nivå i vyn [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) . CPU-förbrukningen för användar arbets belastningen rapporteras också via `cpu_percent` Azure Monitors mått, för [enskilda databaser](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases) och [elastiska pooler](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) på Poolnivå.

Azure SQL Database kräver beräknings resurser för att implementera grundläggande tjänst funktioner, till exempel hög tillgänglighet och haveri beredskap, säkerhets kopiering och återställning av databasen, övervakning, Frågearkivet, automatisk justering osv. Systemet tar undan en viss begränsad del av de övergripande resurserna för dessa interna processer med hjälp av [resurs styrnings](#resource-governance) metoder, vilket gör resten av resurser tillgängliga för användar arbets belastningar. Vid tillfällen då interna processer inte använder beräknings resurser gör systemet dem tillgängliga för användar arbets belastningar.

Total processor-och minnes förbrukning för användar arbets belastningar och interna processer rapporteras i [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) och [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) vyer, i `avg_instance_cpu_percent` och `avg_instance_memory_percent` kolumner. Dessa data rapporteras även via `sqlserver_process_core_percent` och `sqlserver_process_memory_percent` Azure Monitor mått för [enskilda databaser](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases) och [elastiska pooler](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) på Poolnivå.

En mer detaljerad analys av den senaste resurs förbrukningen för användar arbets belastningar och interna processer rapporteras i [sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) och [sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) vyer. Mer information om resurspooler och arbets belastnings grupper som refereras till i dessa vyer finns i [resurs styrning](#resource-governance). De här vyerna rapporterar om resursutnyttjande av användar arbets belastningar och speciella interna processer i de associerade resurspooler och arbets belastnings grupper.

I samband med prestanda övervakning och fel sökning är det viktigt att överväga både **användar-CPU-förbrukning** ( `avg_cpu_percent` , `cpu_percent` ) och **Total CPU-förbrukning** för användar arbets belastningar och interna processer ( `avg_instance_cpu_percent` , `sqlserver_process_core_percent` ).

**Användningen av användar-CPU** beräknas som en procent andel av användarnas arbets belastnings gränser i varje tjänst mål. **Användarens CPU-användning** vid 100% indikerar att användarens arbets belastning har nått gränsen för tjänst målet. Men när den **totala CPU-förbrukningen** når 70-100% intervall, är det möjligt att se utökning av användarens arbets belastnings-och svars fördröjning, även om rapporterad **användar-CPU-förbrukning** fortfarande är betydligt lägre än 100%. Detta är mer troligt när du använder mindre tjänst mål med en måttlig allokering av beräknings resurser, men relativt intensiv användar arbets belastningar, till exempel i [kompakta elastiska pooler](elastic-pool-resource-management.md). Detta kan också inträffa med mindre tjänst mål när interna processer tillfälligt kräver ytterligare resurser, till exempel när du skapar en ny replik av databasen.

När den **totala CPU-förbrukningen** är hög är alternativ för minskning detsamma som tidigare nämnts och omfattar ökning av tjänst mål och/eller användar arbets belastnings optimering.

## <a name="resource-governance"></a>Resursstyrning

Om du vill framtvinga resurs gränserna använder Azure SQL Database en resurs styrnings implementering som baseras på SQL Server [Resource Governor](/sql/relational-databases/resource-governor/resource-governor), ändrad och utökad att köras i Azure SQL Database. I SQL Database ger flera [resurspooler](/sql/relational-databases/resource-governor/resource-governor-resource-pool) och [arbets belastnings grupper](/sql/relational-databases/resource-governor/resource-governor-workload-group), med resurs gränser inställda på både pool-och grupp nivåer, en [bal anse rad databas som en tjänst](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). Användarens arbets belastning och interna arbets belastningar klassificeras till separata resurspooler och arbets belastnings grupper. Användarens arbets belastning på de primära och läsbara sekundära replikerna, inklusive geo-replikeringar, klassificeras i `SloSharedPool1` resurspoolen och `UserPrimaryGroup.DBId[N]` arbets belastnings gruppen, där `N` står för databas-ID-värdet. Det finns dessutom flera resurspooler och arbets belastnings grupper för olika interna arbets belastningar.

Förutom att använda Resource Governor för att styra resurser inom SQL-processen, Azure SQL Database även Windows- [jobb objekt](/windows/win32/procthread/job-objects) för resurs styrning på processnivå och Windows [hanteraren för fil server resurser (FSRM)](/windows-server/storage/fsrm/fsrm-overview) för lagrings kvot hantering.

Azure SQL Database resurs styrning är hierarkiskt beslagen. Från uppifrån och ned tillämpas gränser på operativ Systems nivå och på lagrings volym nivå med hjälp av mekanismer för styrning av operativ system resurser och Resource Governor, sedan på resurspoolen med Resource Governor och sedan på arbets belastnings grupps nivån med Resource Governor. Resurs styrnings gränser som gäller för den aktuella databasen eller elastisk pool visas i vyn [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) .

### <a name="data-io-governance"></a>Data IO-styrning

Data IO-styrning är en process i Azure SQL Database som används för att begränsa både Läs-och skriv fysisk IO mot datafiler i en databas. IOPS-gränser ställs in för varje service nivå för att minimera effekterna "störnings Grans kraft", för att tillhandahålla skälighet i tjänsten för flera innehavare och för att hålla sig inom funktionerna i den underliggande maskin varan och lagringen.

För enskilda databaser tillämpas gränser för arbets belastnings grupper för alla lagrings-i/o mot databasen, medan gränserna för resurspooler gäller för alla lagrings-i/o-databaser i samma dedikerade SQL-pool, inklusive `tempdb` databasen. För elastiska pooler gäller begränsningarna för arbets belastnings gruppen för varje databas i poolen, medan gränsen för resurspooler gäller hela den elastiska poolen, inklusive `tempdb` databasen, som delas mellan alla databaser i poolen. I allmänhet är det inte säkert att gränserna för resurspooler kan uppnås av arbets belastningen mot en databas (antingen en eller en pool), eftersom gränserna för arbets belastnings gruppen är lägre än begränsningen för resurspool och begränsar IOPS/genomflöde tidigare. Poolens gränser kan dock nås av den kombinerade arbets belastningen mot flera databaser i samma pool.

Om en fråga till exempel genererar 1000 IOPS utan någon IO-resurs styrning, men den högsta IOPS-gränsen för arbets belastnings gruppen är inställd på 900 IOPS, kan inte frågan generera mer än 900 IOPS. Men om max antalet IOPS för resurspoolen är inställt på 1500 IOPS och total i/o från alla arbets belastnings grupper som är associerade med resurspoolen överskrider 1500 IOPS, kan i/o för samma fråga minskas under arbets gruppens gräns på 900 IOPS.

Värdena för IOPS och data flöde som returnerades av [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) Visa fungerar som gränser/versaler, inte som garantier. Dessutom garanterar resurs styrningen inte någon speciell lagrings fördröjning. Den bästa tillgängliga svars tiden, IOPS och data flödet för en specifik användar arbets belastning är beroende inte bara av gränser för i/o-resursens styrning, utan även på den kombination av IO-storlek som används och på funktionerna i det underliggande lagrings utrymmet. SQL Database använder IOs som varierar i storlek mellan 512 KB och 4 MB. I syfte att framtvinga IOPS-gränser redovisas varje IO oavsett storlek, med undantag för databaser med datafiler i Azure Storage. I så fall redovisas IOs som är större än 256 KB som flera 256-KB-IOs för att justera med Azure Storage IO-redovisning.

För Basic-, standard-och Generell användning-databaser, som använder datafiler i Azure Storage, `primary_group_max_io` kan det hända att värdet inte kan nås om en databas inte har tillräckligt med datafiler för att ackumulera antalet IOPS, eller om data inte fördelas jämnt över filer, eller om prestanda nivån för underliggande blobbar begränsar IOPS/data flödet under resurs styrnings gränserna. På samma sätt `primary_max_log_rate` kan det hända att värdet inte kan nås av en arbets belastning på grund av IOPS-gränsen på den underliggande Azure Storage-blobben, med en liten logg-iOS som genereras av frekventa transaktions skrivningar. För databaser som använder Azure Premium Storage använder Azure SQL Database tillräckligt stora lagrings blobbar för att få nödvändiga IOPS/data flöden, oavsett databas storlek. För större databaser skapas flera datafiler för att öka total IOPS/data flödes kapacitet.

Värdena för resursutnyttjande, till exempel `avg_data_io_percent` och `avg_log_write_percent` , som rapporteras i vyerna [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database),  [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)och [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) , beräknas som procent andelar av maximala resurs styrnings gränser. När andra faktorer än resurs styrningen begränsar IOPS/data flödet, är det möjligt att se att utökningar av IOPS/genom strömning och fördröjning ökar när arbets belastningen ökar, även om rapporterat resursutnyttjande är lägre än 100%.

Om du vill se läsa och skriva IOPS, data flöde och svars tid per databas fil använder du funktionen [sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) . Den här funktionen delar alla IO: t i databasen, inklusive Background IO som inte redovisas mot `avg_data_io_percent` , men använder IOPS och data flödet för den underliggande lagringen och kan påverka den observerade lagrings fördröjningen. Funktions ytorna ytterligare svars tid som kan introduceras av i/o-resursens styrning för läsningar och skrivningar, i `io_stall_queued_read_ms` `io_stall_queued_write_ms` respektive kolumner.

### <a name="transaction-log-rate-governance"></a>Hastighets styrning för transaktions logg

Styrning av transaktions logg hastighet är en process i Azure SQL Database som används för att begränsa hög förbruknings frekvens för arbets belastningar som Mass infogning, SELECT INTO och indexe build. Dessa gränser spåras och framtvingas på den andra nivån till frekvensen för genereringen av logg poster, vilket begränsar data flödet, oavsett hur många IOs som kan utfärdas mot datafiler.  Taxan för transaktions logg skapande skalas linjärt upp till en punkt som är maskin vara beroende av, med den högsta logg frekvensen som tillåts som 96 MB/s med vCore inköps modell.

> [!NOTE]
> Faktiska fysiska IOs till transaktionsloggfiler är inte reglerade eller begränsade.

Logg taxan ställs in så att de kan uppnås och hanteras i flera olika scenarier, medan det övergripande systemet kan underhålla sin funktionalitet med minimerad påverkan på användar belastningen. Styrning av logg hastighet säkerställer att säkerhets kopior av transaktions loggar stannar inom publicerings service avtal.  Denna styrning förhindrar också en alltför lång efter släpning på sekundära repliker.

När logg poster skapas utvärderas och utvärderas varje åtgärd för om den ska fördröjas för att upprätthålla den högsta önskade logg frekvensen (MB/s per sekund). Fördröjningarna läggs inte till när logg posterna töms på lagringen, i takt med att logg takts styrningen används under själva genereringen av logg hastighet.

De faktiska taxan för logg skapande som påförs vid körning kan också påverkas av feedback-mekanismer, vilket tillfälligt minskar de tillåtna logg priserna så att systemet kan stabiliseras. Hantering av logg fil utrymme, Undvik att köra i slut på logg utrymmes villkor och replikering av tillgänglighets grupper kan tillfälligt minska de totala system gränserna.

Trafikstyrningen för logg takts trafik sker via följande vänte lägen (visas i [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) och [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) vyer):

| Wait-typ | Kommentarer |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Databas begränsning |
| POOL_LOG_RATE_GOVERNOR | Begränsning av pool |
| INSTANCE_LOG_RATE_GOVERNOR | Begränsning på instans nivå |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Feedback-kontroll, fysisk replikering för tillgänglighets grupp i Premium/Affärskritisk inte hålla sig uppdaterad |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Feedback-kontroll, begränsa priser för att undvika ett slut på logg utrymmes villkor |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | Feedback-kontroll för geo-replikering, begränsa logg takten för att undvika hög data latens och otillgänglig geo-sekundära|
|||

När du påträffar en logg hastighets gräns som hindrar önskad skalbarhet, bör du överväga följande alternativ:

- Skala upp till en högre service nivå för att få maximalt 96 MB/s-logg hastighet, eller växla till en annan tjänst nivå. Den [storskaliga](service-tier-hyperscale.md) Service nivån ger 100 MB/s logg taxa oavsett vilken service nivå som valts.
- Om data som läses in är tillfälliga, till exempel mellanlagring av data i en ETL-process, kan den läsas in i tempdb (som är minimalt loggad).
- För analys scenarier läser du in i en klustrad columnstore-tabell. Detta minskar den nödvändiga logg frekvensen på grund av komprimering. Den här tekniken ökar processor användningen och gäller endast för data uppsättningar som drar nytta av klustrade columnstore-index.

### <a name="storage-space-governance"></a>Lagrings utrymmes styrning

I Premium-och Affärskritisk tjänst nivåer lagras data och transaktionsloggfiler på den lokala SSD-volymen på den dator som är värd för databasen eller elastisk pool. Detta ger hög IOPS och data flöde och låg IO-latens. Storleken på den här lokala volymen beror på maskin varu funktioner och är begränsad. På en specifik dator används lokalt volym utrymme av kund databaser, inklusive `tempdb` operativ system, hanterings program, övervaknings data, loggar osv. När databaser skapas, tas bort och ökar/minskar utrymmes användningen, varierar användningen av lokalt utrymme på en dator över tid. 

Om systemet upptäcker att det lediga utrymmet på en dator är lågt och en databas eller elastisk pool riskerar att få slut på utrymme, flyttas databasen eller den elastiska poolen till en annan dator med tillräckligt ledigt utrymme, vilket ger en större gräns för det konfigurerade tjänst målet. Den här flyttningen sker på ett sätt som är online, på samma sätt som en databas skalnings åtgärd och har en liknande [påverkan](single-database-scale.md#impact), inklusive en kort (sekunder) redundansväxling i slutet av åtgärden. Den här redundansväxlingen avslutar öppna anslutningar och återställer transaktioner, vilket potentiellt påverkar program som använder databasen vid detta tillfälle.

Eftersom data fysiskt kopieras till en annan dator kan det krävas en stor del av att flytta större databaser. Under den tiden ökar risken för att utrymmet ökar om det finns en stor användar databas eller elastisk pool, eller om `tempdb` databasen ökar mycket snabbt. Systemet initierar databas flyttning på ett balanserat sätt för att förhindra fel på disk utrymme och för att undvika onödig redundans.

## <a name="next-steps"></a>Nästa steg

- Information om allmänna Azure-gränser finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Information om DTU: er och eDTU: er finns i [DTU: er och eDTU: er](purchasing-models.md#dtu-based-purchasing-model).
- Information om tempdb-storleks gränser finns [i tempdb i Azure SQL Database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).