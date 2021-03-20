---
title: Resurshantering i kompakta elastiska pooler
description: Hantera beräknings resurser i Azure SQL Database elastiska pooler med många databaser.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: sstein
ms.date: 09/16/2020
ms.openlocfilehash: 40b6c5a86184860cf3e7a9840f980706485ae977
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572244"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Resurshantering i kompakta elastiska pooler
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database [elastiska pooler](./elastic-pool-overview.md) är en kostnads effektiv lösning för att hantera många databaser med varierande resursanvändning. Alla databaser i en elastisk pool delar samma allokering av resurser, till exempel processor, minne, arbets trådar, lagrings utrymme, tempdb, i antagandet att **endast en del av databaserna i poolen kommer att använda beräknings resurser vid en viss tidpunkt**. Detta antagande innebär att elastiska pooler kan vara kostnads effektiva. I stället för att betala för alla resurser som varje enskild databas kan behöva, betalar kunderna för en mycket mindre uppsättning resurser, som delas mellan alla databaser i poolen.

## <a name="resource-governance"></a>Resursstyrning

Resurs delning kräver att systemet noggrant styr resursanvändningen för att minimera den "störningarnas granne"-inverkan, där en databas med hög resursanvändning påverkar andra databaser i samma elastiska pool. På samma gång måste systemet tillhandahålla tillräckligt med resurser för funktioner som hög tillgänglighet och haveri beredskap (HADR), säkerhets kopiering och återställning, övervakning, Frågearkivet, automatisk justering osv. för att fungera på ett tillförlitligt sätt.

Azure SQL Database uppnår dessa mål genom att använda flera metoder för resurs styrning, inklusive Windows- [jobb objekt](/windows/win32/procthread/job-objects) för resurs styrning på processnivå, Windows [hanteraren för fil server resurser (FSRM)](/windows-server/storage/fsrm/fsrm-overview) för lagrings kvot hantering, och en ändrad och utökad version av SQL Server [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) att implementera resurs styrning i SQL Database.

Det primära design målet för elastiska pooler är att vara kostnads effektivt. Av den anledningen tillåter systemet avsiktligt kunderna att skapa _kompakta_ pooler, som är pooler med antalet databaser som närmar sig eller maximalt tillåts, men med en måttlig allokering av beräknings resurser. Av samma skäl reserverar systemet inte alla resurser som kan behövas för interna processer, men resurs delning mellan interna processer och användar arbets belastningar tillåts.

Med den här metoden kan kunder använda kompakta elastiska pooler för att uppnå adekvat prestanda och större besparingar. Men om arbets belastningen mot många databaser i en kompakt pool är tillräckligt intensiv blir resurs konkurrens markant. Resurs konkurrens minskar prestanda för användar arbets belastning och kan påverka interna processer negativt.

> [!IMPORTANT]
> I kompakta pooler med många aktiva databaser kan det vara omöjligt att öka antalet databaser i poolen upp till de maximalt dokumenterade för [DTU](resource-limits-dtu-elastic-pools.md) -och [vCore](resource-limits-vcore-elastic-pools.md) elastiska pooler.
>
> Antalet databaser som kan placeras i kompakta pooler utan att orsaka resurs konkurrens-och prestanda problem beror på antalet aktiva och aktuella aktiva databaser, och om resursförbrukning av användar arbets belastningar i varje databas. Det här antalet kan ändras över tid då användar arbets belastningarna ändras.

När resurs konkurrens uppstår i en kompakt, förpackad pool kan kunder välja en eller flera av följande åtgärder för att minimera den:

- Finjustera fråge arbets belastningen för att minska resurs förbrukningen eller sprida resursförbrukning över flera databaser över tid.
- Minska poolens densitet genom att flytta några databaser till en annan pool, eller genom att göra dem till fristående databaser.
- Skala upp poolen för att få fler resurser.

Förslag på hur du implementerar de senaste två åtgärderna finns i [operativa rekommendationer](#operational-recommendations) längre fram i den här artikeln. Minskning av resurs konkurrens fördelar både användar arbets belastningar och interna processer och gör att systemet på ett tillförlitligt sätt kan underhålla den förväntade Service nivån.

## <a name="monitoring-resource-consumption"></a>Övervaka resursförbrukning

För att undvika prestanda försämring på grund av en resurs konkurrens bör kunder som använder kompakta elastiska pooler proaktivt övervaka resursanvändningen och vidta åtgärder för att få en rimlig åtgärd om en ökad resurs konkurrens börjar påverka arbets belastningarna. Kontinuerlig övervakning är viktigt eftersom resursanvändningen i en pool ändras med tiden, på grund av ändringar i användar arbets belastningen, förändringar i data volymer och distribution, förändringar i poolens densitet och ändringar i Azure SQL Databases tjänsten.

Azure SQL Database tillhandahåller flera mått som är relevanta för den här typen av övervakning. Att överskrida det rekommenderade medelvärdet för varje mått indikerar resurs konkurrens i poolen och bör åtgärdas med hjälp av en av de åtgärder som nämns ovan.

|Måttnamn|Beskrivning|Rekommenderat genomsnitts värde|
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|PROCESSOR användning för SQL-processen som är kopplad till en elastisk pool, mätt av det underliggande operativ systemet. Tillgängligt i vyn [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) i varje databas och i vyn [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) i- `master` databasen. Det här måttet har också spridits till Azure Monitor, där det [heter](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `sqlserver_process_core_percent` , och kan visas i Azure Portal. Det här värdet är detsamma för alla databaser i samma elastiska pool.|Under 70%. Tillfälliga kort toppar upp till 90% kan vara acceptabla.|
|`max_worker_percent`|Användning av [arbets tråd]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) . Anges för varje databas i poolen, samt för själva poolen. Det finns olika gränser för antalet arbets trådar på databas nivå, och på Poolnivå rekommenderas att du övervakar detta mått på båda nivåerna. Tillgängligt i vyn [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) i varje databas och i vyn [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) i- `master` databasen. Det här måttet har också spridits till Azure Monitor, där det [heter](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `workers_percent` , och kan visas i Azure Portal.|Under 80%. Toppar upp till 100% innebär att anslutnings försöken och frågorna Miss lyckas.|
|`avg_data_io_percent`|IOPS-användning för Läs-och skriv fysisk IO. Anges för varje databas i poolen, samt för själva poolen. Det finns olika begränsningar för antalet IOPS på databas nivå och på Poolnivå bör du övervaka detta mått på båda nivåerna. Tillgängligt i vyn [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) i varje databas och i vyn [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) i- `master` databasen. Det här måttet har också spridits till Azure Monitor, där det [heter](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `physical_data_read_percent` , och kan visas i Azure Portal.|Under 80%. Tillfälliga kort toppar upp till 100% kan vara acceptabla.|
|`avg_log_write_percent`|Data flödes användning för transaktions logg skrivnings-i/o. Anges för varje databas i poolen, samt för själva poolen. Det finns olika gränser för logg data flödet på databas nivå och på Poolnivå rekommenderas det att övervaka detta mått på båda nivåerna. Tillgängligt i vyn [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) i varje databas och i vyn [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) i- `master` databasen. Det här måttet har också spridits till Azure Monitor, där det [heter](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `log_write_percent` , och kan visas i Azure Portal. När måttet är nära 100%, alla databas ändringar (Infoga, uppdatera, ta bort, MERGE-instruktioner, Välj... I, BULK INSERT osv.) blir långsammare.|Under 90%. Tillfälliga kort toppar upp till 100% kan vara acceptabla.|
|`oom_per_second`|Takten för minnes belastnings fel (OOM) i en elastisk pool, vilket är en indikation på minnes belastningen. Tillgängligt i vyn [sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) . Se [exempel på exempel](#examples) frågor för att beräkna det här måttet.|0|
|`avg_storage_percent`|Totalt lagrings utrymme som används av data i alla databaser i en elastisk pool. Innehåller inget tomt utrymme i databasfilerna. Tillgängligt i vyn [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) i- `master` databasen. Det här måttet har också spridits till Azure Monitor, där det [heter](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `storage_percent` , och kan visas i Azure Portal.|Under 80%. Kan närma 100% för pooler utan data tillväxt.|
|`avg_allocated_storage_percent`|Totalt lagrings utrymme som används av databasfiler i lagrings utrymmen i alla databaser i en elastisk pool. Inkluderar tomt utrymme i databasfiler. Tillgängligt i vyn [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) i- `master` databasen. Det här måttet har också spridits till Azure Monitor, där det [heter](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `allocated_data_storage_percent` , och kan visas i Azure Portal.|Under 90%. Kan närma 100% för pooler utan data tillväxt.|
|`tempdb_log_used_percent`|Användning av transaktions logg utrymme i `tempdb` databasen. Även om temporära objekt som skapats i en databas inte är synliga i andra databaser i samma elastiska pool, `tempdb` är en delad resurs för alla databaser i samma pool. En tids krävande eller överblivna transaktion i `tempdb` startad från en databas i poolen kan använda en stor del av transaktions loggen och orsaka fel för frågor i andra databaser i samma pool. Härlett från [sys.dm_db_log_space_usage](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) och [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) vyer. Det här måttet genereras också till Azure Monitor och kan visas i Azure Portal. Se [exempel](#examples) för en exempel fråga för att returnera det aktuella värdet för det här måttet.|Under 50%. Tillfälliga toppar upp till 80% är acceptabla.|
|||

Förutom dessa mått ger Azure SQL Database en vy som returnerar faktiska resurs styrnings gränser, samt ytterligare vyer som returnerar statistik för resursutnyttjande på resurspoolen och på arbets belastnings grupps nivå.

|Namn på vy|Beskrivning|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Returnerar de inställningar för konfiguration och kapacitet som används av resurs styrnings mekanismer i den aktuella databasen eller den elastiska poolen.|
|[sys.dm_resource_governor_resource_pools](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Returnerar information om aktuell resurspool, aktuell konfiguration av resurspooler och ackumulerad resurspool.|
|[sys.dm_resource_governor_workload_groups](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Returnerar ackumulerad statistik för arbets belastnings grupper och den aktuella konfigurationen av arbets belastnings gruppen. Den här vyn kan kopplas till sys.dm_resource_governor_resource_pools i `pool_id` kolumnen för att hämta information om resurspooler.|
|[sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Returnerar statistik för användning av resurspool under de senaste 32 minuterna. Varje rad representerar ett 20-sekunders intervall. `delta_`Kolumnerna returnerar ändringen i varje statistik under intervallet.|
|[sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Returnerar användnings statistik för arbets belastnings grupper under de senaste 32 minuterna. Varje rad representerar ett 20-sekunders intervall. `delta_`Kolumnerna returnerar ändringen i varje statistik under intervallet.|
|||

Dessa vyer kan användas för att övervaka resursutnyttjande och felsöka resurs konkurrens i nära real tid. Användarens arbets belastning på de primära och läsbara sekundära replikerna, inklusive geo-replikeringar, klassificeras i `SloSharedPool1` resurspoolen och `UserPrimaryGroup.DBId[N]` arbets belastnings gruppen, där `N` står för databas-ID-värdet.

Förutom övervakning av den aktuella resursutnyttjande kan kunder som använder kompakta pooler underhålla historiska resursutnyttjande i ett separat data lager. Dessa data kan användas i förutsägelse analys för att proaktivt hantera resursutnyttjande baserat på historiska och säsongs trender.

## <a name="operational-recommendations"></a>Operativa rekommendationer

**Lämna tillräckligt med resurs utrymme**. Om resurs konkurrens och prestanda försämring inträffar kan det undvikas att flytta några databaser från den berörda elastiska poolen eller skala upp poolen, som tidigare nämnts. Dessa åtgärder kräver dock att ytterligare beräknings resurser slutförs. I synnerhet för Premium-och Affärskritisk-pooler kräver dessa åtgärder överföring av alla data för de databaser som flyttas, eller för alla databaser i den elastiska poolen om poolen skalas upp. Data överföring är en tids krävande och resurs intensiv åtgärd. Om poolen redan är under hög resurs belastning försämras prestanda ytterligare om den minimerande åtgärden. I extrema fall kanske det inte går att lösa resurs konkurrens via databas flytt eller pool-skalning eftersom de nödvändiga resurserna inte är tillgängliga. I detta fall kan det vara den enda lösningen att tillfälligt minska arbets belastningen för den berörda elastiska poolen.

Kunder som använder kompakta pooler bör noggrant övervaka resursanvändningen, som beskrivs ovan, och vidta åtgärder när måtten fortsätter inom de rekommenderade intervallen och det finns fortfarande tillräckligt med resurser i den elastiska poolen.

Resursutnyttjande beror på flera faktorer som ändras med tiden för varje databas och varje elastisk pool. Att uppnå optimal pris/prestanda-förhållande i kompakta pooler kräver kontinuerlig övervakning och ombalansering, som flyttar databaser från fler använda pooler till mindre använda pooler och skapar nya pooler efter behov för att hantera ökad arbets belastning.

**Flytta inte "heta" databaser**. Om resurs konkurrens på Poolnivå främst orsakas av ett litet antal databaser med hög användning, kan det vara frestande att flytta databaserna till en mindre Använd pool eller göra dem till fristående databaser. Det rekommenderas dock inte att göra detta medan en databas fortfarande används, eftersom flyttnings åtgärden ytterligare försämrar prestanda, både för den databas som flyttas och för hela poolen. I stället måste du antingen vänta till hög användnings nivå eller flytta mindre använda databaser i stället för att minska resurs belastningen på Poolnivå. Men om du flyttar databaser med mycket låg belastning får du inte några fördelar eftersom det inte minskar resursutnyttjande på Poolnivå.

**Skapa nya databaser i en "karantän"-pool**. I scenarier där nya databaser skapas ofta, till exempel program som använder klient organisations-och databas modellen, finns det risk för att en ny databas som finns i en befintlig elastisk pool inte förbrukar stora resurser och påverkar andra databaser och interna processer i poolen. För att minimera den här risken skapar du en separat "karantän" pool med en mycket gott resurs beläggning. Använd den här poolen för nya databaser med ännu okända resurs förbruknings mönster. När en databas har lyckats i den här poolen för en affärs cykel, till exempel en vecka eller en månad, och dess resursförbrukning är känd, kan den flyttas till en pool med tillräckligt med kapacitet för att hantera den här ytterligare resursanvändningen.

**Övervaka både använt och allokerat utrymme**. När allokerat pool utrymme (Total storlek på alla databasfiler i lagringen för alla databaser i en pool) når den maximala storleken på poolen, kan det hända att det uppstår fel på disk. Om det allokerade utrymmet är högt och om det är på spåret för att uppnå maximal poolstorlek, är alternativ för minskning följande:
- Flytta några databaser från poolen för att minska det totala allokerade utrymmet
- [Krymp](file-space-manage.md) databasfiler för att minska det tomma allokerade utrymmet i filer
- Skala upp poolen till ett tjänst mål med en större maximal poolstorlek

Om använt pool utrymme (Total storlek på data i alla databaser i en pool, inte inkluderar tomt utrymme i filer) trender högt och är på spåret för att uppnå maximal poolstorlek, är alternativ för minskning följande:
- Flytta några databaser från poolen för att minska det totala använda utrymmet
- Flytta (arkivera) data utanför databasen eller ta bort data som inte längre behövs
- Implementera [data komprimering](/sql/relational-databases/data-compression/data-compression)
- Skala upp poolen till ett tjänst mål med en större maximal poolstorlek

**Undvik att komprimera servrar**. Azure SQL Database [stöder](./resource-limits-logical-server.md) upp till 5000 databaser per server. Kunder som använder elastiska pooler med tusentals databaser kan överväga att placera flera elastiska pooler på en enskild server, med det totala antalet databaser upp till den gräns som stöds. Men servrar med många tusentals databaser skapar operativa utmaningar. Åtgärder som kräver att du räknar upp alla databaser på en server, till exempel visar databaser i portalen, blir långsammare. Drift fel, till exempel felaktig ändring av inloggningar på server nivå eller brand Väggs regler, påverkar ett större antal databaser. Vid oavsiktlig borttagning av servern krävs hjälp från Microsoft Support för att återställa databaser på den borttagna servern och det kan orsaka ett långvarigt avbrott för alla berörda databaser.

Vi rekommenderar att du begränsar antalet databaser per server till ett lägre antal än det högsta stödda. I många fall är det optimalt att använda upp till 1000-2000 databaser per server. För att minska sannolikheten för borttagning av oavsiktlig server rekommenderar vi att du placerar ett [borttagnings lås](../../azure-resource-manager/management/lock-resources.md) på servern eller resurs gruppen.

Tidigare var vissa scenarier som innebär att flytta databaser i, ut eller mellan elastiska pooler på samma server snabbare än när du flyttar databaser mellan servrar. För närvarande körs alla databaser i samma hastighet oavsett källa och mål server.

## <a name="examples"></a>Exempel

### <a name="monitoring-memory-utilization"></a>Övervaka minnes användning

Den här frågan beräknar `oom_per_second` måttet för varje resurspool under de senaste 32 minuterna. Den här frågan kan köras i alla databaser i en elastisk pool.

```sql
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```

### <a name="monitoring-tempdb-log-space-utilization"></a>Övervaka `tempdb` användning av logg utrymme

Den här frågan returnerar det aktuella värdet för `tempdb_log_used_percent` måttet, vilket visar den relativa användningen av `tempdb` transaktions loggen i förhållande till den maximalt tillåtna storleken. Den här frågan kan köras i alla databaser i en elastisk pool.

```sql
SELECT (lsu.used_log_space_in_bytes / df.log_max_size_bytes) * 100 AS tempdb_log_space_used_percent
FROM tempdb.sys.dm_db_log_space_usage AS lsu
CROSS JOIN (
           SELECT SUM(CAST(max_size AS bigint)) * 8 * 1024. AS log_max_size_bytes
           FROM tempdb.sys.database_files
           WHERE type_desc = N'LOG'
           ) AS df
;
```

## <a name="next-steps"></a>Nästa steg

- En introduktion till elastiska pooler finns i [elastiska pooler, som hjälper dig att hantera och skala flera databaser i Azure SQL Database](./elastic-pool-overview.md).
- Information om hur du justerar frågor för arbets belastningar för att minska resursutnyttjande finns i [övervakning och justering]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index), [övervakning och prestanda justering](./monitor-tune-overview.md).