---
title: Typer av problem med frågans prestanda i Azure SQL Database
description: I den här artikeln får du lära dig mer om typer av problem med frågans prestanda i Azure SQL Database och hur du identifierar och löser frågor med följande problem
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: troubleshooting
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: 4d0f5404a64eae99ced0dd797954ba042b50060f
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217234"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Identifierbara typer av flaskhalsar för frågeprestanda i Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

När du försöker lösa en prestanda Flask hals börjar du med att bestämma om flask halsen inträffar när frågan är i ett körnings tillstånd eller väntar. Olika lösningar gäller beroende på det här beslutet. Använd följande diagram för att förstå de faktorer som kan orsaka ett problem som rör körning eller ett väntande problem. Problem och lösningar som rör varje typ av problem beskrivs i den här artikeln.

Du kan använda Azure SQL Database [intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) eller SQL Server [DMV: er](database/monitoring-with-dmvs.md) för att identifiera dessa typer av prestanda Flask halsar.

![Arbets belastnings status](./media/identify-query-performance-issues/workload-states.png)

**Körnings problem**: problem med att köra är i allmänhet relaterade till kompileringsfel som resulterar i en icke-optimal frågeplan eller körnings problem relaterade till otillräckliga eller överanvända resurser.
**Väntande-relaterade problem**: väntande problem är i allmänhet relaterade till:

- Lås (blockerar)
- I/O
- Konkurrens för användning av TempDB
- Minnes tilldelning väntar

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Kompileringsfel som resulterar i ett underoptimerat frågeplan

Ett underoptimerat schema som genereras av SQL Query Optimering kan vara orsaken till långsamma frågor. SQL Query Optimering kan producera ett underoptimerat schema på grund av ett saknat index, inaktuell statistik, en felaktig uppskattning av antalet rader som ska bearbetas eller en felaktig uppskattning av det nödvändiga minnet. Om du vet att frågan kördes snabbare tidigare eller på en annan instans kan du jämföra de faktiska körnings planerna för att se om de är olika.

- Identifiera saknade index med någon av följande metoder:

  - Använd [intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#missing-index).
  - [Database Advisor](database/database-advisor-implement-performance-recommendations.md) för databaser med enkel och pool.
  - DMV: er. Det här exemplet visar effekten av ett saknat index, hur du identifierar [saknade index](database/performance-guidance.md#identifying-and-adding-missing-indexes) med hjälp av DMV: er och effekten av att implementera den saknade index rekommendationen.
- Försök att använda [frågetipset](/sql/t-sql/queries/hints-transact-sql-query), [Uppdatera statistik](/sql/t-sql/statements/update-statistics-transact-sql)eller [Återskapa index](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) för att få bättre planer. Aktivera [Automatisk plan korrigering](../azure-sql/database/automatic-tuning-overview.md) i Azure SQL Database för att automatiskt minimera de här problemen.

  Det här [exemplet](database/performance-guidance.md#query-tuning-and-hinting) visar effekten av en underoptimerad frågeplan på grund av en parametriserad fråga, hur du identifierar det här villkoret och hur du använder ett frågeuttryck för att lösa problemet.

- Försök ändra kompatibilitetsnivån för databas och implementera intelligent Query-bearbetning. SQL-frågans optimering kan generera en annan frågeplan beroende på kompatibilitetsnivån för din databas. Högre kompatibilitetsnivå ger mer [intelligenta fråge bearbetnings funktioner](/sql/relational-databases/performance/intelligent-query-processing).

  - Mer information om bearbetning av frågor finns i [arkitektur guide för Query Processing](/sql/relational-databases/query-processing-architecture-guide).
  - Om du vill ändra kompatibilitetsnivån för databaser och läsa mer om skillnaderna mellan kompatibilitetsnivån, se [ändra databas](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
  - Läs mer om beräkning av kardinalitet i [kardinalitet](/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Lösa frågor med under optimala fråge körnings planer

I följande avsnitt lär du dig hur du löser frågor med en underoptimerad körnings plan för frågor.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a> Frågor med PSP-problem (parameter känslig plan)

Ett problem med en parameter känslig plan (PSP) inträffar när Query Optimering genererar en plan för frågekörningen som är optimal endast för ett särskilt parameter värde (eller uppsättning värden) och den cachelagrade planen är inte optimal för parameter värden som används i löpande körningar. Planer som inte är optimala kan sedan orsaka problem med frågans prestanda och försämra det totala data flödet.

Mer information om parameter-och bearbetning av frågor finns i [arkitektur guiden för frågekörning](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Flera lösningar kan minimera PSP-problem. Varje lösning har tillhör ande kompromisser och nack delar:

- Använd frågetipset för att [kompilera](/sql/t-sql/queries/hints-transact-sql-query) om frågan vid varje frågekörningen. Den här lösningen för att kompilera den här lösningen och öka CPU-tiden för bättre plan kvalitet. `RECOMPILE`Alternativet är ofta inte möjligt för arbets belastningar som kräver ett högt data flöde.
- Använd frågetipset [alternativ (Optimize for...)](/sql/t-sql/queries/hints-transact-sql-query) för att åsidosätta det faktiska parametervärdet med ett typiskt parameter värde som ger en plan som är tillräckligt stor för de flesta värde för parameter värden. Det här alternativet kräver en god förståelse av optimala parameter värden och associerade plan egenskaper.
- Använd frågetipset [OPTION (Optimize for UNknown)](/sql/t-sql/queries/hints-transact-sql-query) för att åsidosätta det faktiska parametervärdet och Använd i stället densiteten densitet Vector. Du kan också göra detta genom att samla in inkommande parameter värden i lokala variabler och sedan använda de lokala variablerna i predikat i stället för att använda själva parametrarna. För den här korrigeringen måste den genomsnittliga densiteten vara *tillräckligt hög*.
- Inaktivera parameter identifiering helt och hållet med hjälp av [DISABLE_PARAMETER_SNIFFING](/sql/t-sql/queries/hints-transact-sql-query) -frågetipset.
- Använd [KEEPFIXEDPLAN](/sql/t-sql/queries/hints-transact-sql-query) -frågetipset för att förhindra omkompileringar i cacheminnet. Den här lösningen förutsätter att den tillräckligt höga planen är den som redan finns i cacheminnet. Du kan också inaktivera automatiska statistik uppdateringar för att minska risken för att den bra planen kommer att avlägsnas och en ny dålig plan kompileras.
- Tvinga planen genom att uttryckligen använda [use plan](/sql/t-sql/queries/hints-transact-sql-query) -frågetipset genom att skriva om frågan och lägga till tipset i frågetexten. Eller ange en speciell plan genom att använda Query Store eller genom att aktivera [Automatisk justering](../azure-sql/database/automatic-tuning-overview.md).
- Ersätt den gemensamma proceduren med en kapslad uppsättning procedurer som varje kan användas baserat på villkorlig logik och associerade parameter värden.
- Skapa alternativ för dynamisk sträng körning till en statisk procedur definition.

Mer information om hur du löser PSP-problem finns i följande blogg inlägg:

- [Jag har luktat en parameter](/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor jämfört med dynamiska SQL vs.-procedurer jämfört med plan kvalitet för parametriserade frågor](/archive/blogs/conor_cunningham_msft/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries)
- [Metoder för optimering av SQL-frågor i SQL Server: parameter avlyssning](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Kompilera aktivitet som orsakas av felaktig Parameterisering

När en fråga har litteraler, parameterizes-databas motorn automatiskt uttrycket eller en användare uttryckligen parameterizes satsen för att minska antalet kompileringar. Ett stort antal kompileringar för en fråga med samma mönster men olika litterala värden kan resultera i hög CPU-användning. Om du bara delvis Parameterisera en fråga som fortsätter att ha litteraler, så Parameterisera inte frågan längre i databas motorn.

Här är ett exempel på en delvis parametriserad fråga:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F';
```

I det här exemplet `t1.c1` tar `@p1` , men `t2.c2` fortsätter att ta GUID som literal. I det här fallet `c2` behandlas frågan som en annan fråga och en ny kompilering sker om du ändrar värdet för. För att minska kompileringarna i det här exemplet skulle du också Parameterisera GUID.

Följande fråga visar antalet frågor efter frågans hash för att avgöra om en fråga är korrekt parametriserad:

```sql
SELECT TOP 10
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC;
```

### <a name="factors-that-affect-query-plan-changes"></a>Faktorer som påverkar ändringar i frågeplan

En omkompilering av en frågeplan kan resultera i en genererad frågeplan som skiljer sig från den ursprungliga cachelagrade planen. En befintlig ursprunglig plan kan omkompileras automatiskt av olika anledningar:

- Ändringar i schemat refereras av frågan
- Data ändringar i tabellerna refereras till av frågan
- Frågans kontext alternativ har ändrats

En kompilerad plan kan komma att matas ut från cachen av olika anledningar, t. ex.:

- Instansen startas om
- Konfigurations ändringar för databas omfattning
- Minnesbelastning
- Explicita begär Anden om att rensa cacheminnet

Om du använder ett omkompilerings tips cachelagras inte en plan.

En RECOMPILE (eller ny kompilering efter cache-avtagningen) kan fortfarande resultera i att en frågeplan som är identisk med originalet skapas. När planen ändras från föregående eller ursprungliga plan är dessa förklaringar förmodligen följande:

- **Ändrad fysisk design**: till exempel kan nyligen skapade index effektivt hantera kraven för en fråga. Nya index kan användas i en ny kompilering om Query Optimering bestämmer att det nya indexet är mer optimalt än att använda den data struktur som ursprungligen valdes för den första versionen av frågekörningen. Eventuella fysiska ändringar av de refererade objekten kan leda till ett nytt schema val vid kompileringen.

- **Server resurs skillnader**: när en plan i ett system skiljer sig från planen i ett annat system kan resurs tillgängligheten, till exempel antalet tillgängliga processorer, påverka vilken plan som genereras. Om ett system till exempel har fler processorer kan du välja en parallell plan.

- **Annan statistik**: statistiken som är kopplad till de refererade objekten kan ha ändrats eller vara väsentlig annorlunda än det ursprungliga systemets statistik. Om statistiken ändras och en omkompilering sker, använder Query Optimering statistiken som börjar från när de ändrades. Den reviderade statistikens data distributioner och frekvenser kan skilja sig från de ursprungliga kompileringarna. Dessa ändringar används för att skapa beräkningar av kardinalitet. (*Beräkning av kardinalitet* är antalet rader som förväntas flöda genom det logiska frågeuttrycket.) Ändringar av beräkningar av kardinalitet kan leda till att du väljer olika fysiska operatörer och tillhör ande åtgärder. Även smärre ändringar i statistiken kan resultera i en ändrad frågeplan för frågekörningen.

- **Ändrade kompatibilitetsnivån för databas eller kardinalitet**: ändringar av databasens kompatibilitetsnivå kan möjliggöra nya strategier och funktioner som kan resultera i en annan frågeplan för körning. Utöver kompatibilitetsnivån för databas kan en inaktive rad eller aktive rad spårnings flagga 4199 eller ett ändrat tillstånd för den databasbaserade konfigurationen QUERY_OPTIMIZER_HOTFIXES också påverka val av frågekörning vid kompilering. Spårnings flaggorna 9481 (framtvinga äldre CE) och 2312 (tvinga standard CE) påverkar också planen.

## <a name="resource-limits-issues"></a>Problem med resurs begränsningar

Långsam fråga-prestanda som inte är relaterade till icke-optimala fråge planer och saknade index är allmänt relaterade till otillräckliga eller överanvända resurser. Om frågesträngen är optimal kan frågan (och databasen) söka efter resurs gränserna för databasen, elastisk pool eller hanterad instans. Ett exempel kan vara överskotts logg skrivnings data flöde för Service nivån.

- Identifiera resurs problem med hjälp av Azure Portal: för att se om resurs gränserna är problemet, se [SQL Database resurs övervakning](database/monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring). För enskilda databaser och elastiska pooler, se [Database Advisor prestanda rekommendationer](database/database-advisor-implement-performance-recommendations.md) och [fråga prestanda insikter](database/query-performance-insight-use.md).
- Identifiera resurs gränser med hjälp av [intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)
- Identifiera resurs problem med [DMV: er](database/monitoring-with-dmvs.md):

  - [Sys.dm_db_resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV returnerar CPU, I/O och minnes användning för databasen. Det finns en rad för varje 15-sekunders intervall, även om det inte finns någon aktivitet i databasen. Historiska data bevaras i en timme.
  - [Sys.resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV returnerar CPU-användning och lagrings data för Azure SQL Database. Data samlas in och sammanställs i fem minuters intervall.
  - [Många enskilda frågor som sammantaget förbrukar hög CPU](database/monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)

Om du identifierar problemet som otillräcklig resurs kan du uppgradera resurserna för att öka kapaciteten för din databas för att öka processor kraven. Mer information finns i [skala enkla databas resurser i Azure SQL Database](database/single-database-scale.md) och [skala elastiska pool resurser i Azure SQL Database](database/elastic-pool-scale.md). Information om hur du skalar en hanterad instans finns i [resurs gränser på tjänst nivå](managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="performance-problems-caused-by-increased-workload-volume"></a>Prestanda problem som orsakas av ökad arbets belastnings volym

En ökning av program trafik och arbets belastnings volym kan orsaka ökad CPU-användning. Men du måste vara noga med att diagnostisera det här problemet. När du ser ett högt processor problem kan du svara på dessa frågor för att avgöra om ökningen orsakas av ändringar av arbets belastnings volymen:

- Är frågorna från programmet orsaken till det höga processor problemet?
- För de [vanligaste processor krävande frågorna som du kan identifiera](database/monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past):

  - Är flera körnings planer kopplade till samma fråga? I så fall varför?
  - Kördes körnings tiderna för frågor med samma körnings plan? Ökade körnings antalet? I så fall kan arbets belastnings ökningen troligen orsaka prestanda problem.

I sammanfattning, om körnings planen inte kördes annorlunda men CPU-användningen ökade tillsammans med antalet körningar, är prestanda problemet sannolikt relaterat till en ökad arbets belastning.

Det är inte alltid lätt att identifiera en ändring av arbets belastnings volymer som kör ett CPU-problem. Tänk på följande faktorer:

- **Ändrad resursanvändning**: anta till exempel ett scenario där CPU-användningen ökade till 80 procent under en längre tids period. PROCESSOR användningen innebär däremot inte att arbets belastnings volymen har ändrats. Regressioner i fråge körnings planen och ändringar i data distributionen kan också bidra till mer resursanvändning även om programmet kör samma arbets belastning.

- **Utseendet på en ny fråga**: ett program kan köra en ny uppsättning frågor vid olika tidpunkter.

- **En ökning eller minskning av antalet begär Anden**: det här scenariot är det mest uppenbara måttet för en arbets belastning. Antalet frågor motsvarar inte alltid mer resursutnyttjande. Detta mått är dock fortfarande en betydande signal, förutsatt att andra faktorer är oförändrade.

Använd Intelligent Insights för att upptäcka [arbets belastningen ökar](database/intelligent-insights-troubleshoot-performance.md#workload-increase) och [planerar regressioner](database/intelligent-insights-troubleshoot-performance.md#plan-regression).

## <a name="waiting-related-problems"></a>Väntande-relaterade problem

När du har eliminerat ett underoptimalt schema och *väntande relaterade* problem som är relaterade till körnings problem, är prestanda problemet vanligt vis att frågorna väntar på viss resurs. Väntande-relaterade problem kan orsakas av:

- **Blockerar**:

  En fråga kan innehålla låset på objekt i databasen medan andra försöker komma åt samma objekt. Du kan identifiera blockerade frågor med hjälp av [DMV: er](database/monitoring-with-dmvs.md#monitoring-blocked-queries) eller [intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#locking). Mer information finns i [förstå och lösa problem med att blockera Azure SQL](database/understand-resolve-blocking.md).
- **I/o-problem**

  Frågor kan vänta på att sidorna skrivs till data-eller loggfilerna. I det här fallet kontrollerar du `INSTANCE_LOG_RATE_GOVERNOR` `WRITE_LOG` statistiken, eller `PAGEIOLATCH_*` wait i DMV. Se använda DMV: er för att [identifiera problem med IO-prestanda](database/monitoring-with-dmvs.md#identify-io-performance-issues).
- **TempDB-problem**

  Om arbets belastningen använder temporära tabeller eller om det finns TempDB-spill i planerna kan frågorna ha problem med TempDB-dataflöde. Se använda DMV: er för att [identifiera tempdb-problem](database/monitoring-with-dmvs.md#identify-tempdb-performance-issues).
- **Minnesrelaterade problem**

  Om arbets belastningen inte har tillräckligt med minne kan förväntad släppas, eller så kan frågorna få mindre minne än de behöver. I vissa fall kommer inbyggd intelligens i fråga optimering att åtgärda minnesrelaterade problem. Se använda DMV: er för att [identifiera minnes tilldelnings problem](database/monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues).

### <a name="methods-to-show-top-wait-categories"></a>Metoder för att visa de främsta väntande kategorierna

Dessa metoder används ofta för att visa de översta kategorierna av vänte typer:

- Använd Intelligent Insights för att identifiera frågor med prestanda försämring på grund av [ökad väntan](database/intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)
- Använd [query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) för att hitta väntande statistik för varje fråga över tid. I Query Store kombineras vänte typer i väntande kategorier. Du kan hitta mappningen av vänte kategorier till vänte typer i [sys.query_store_wait_stats](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Använd [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) för att returnera information om alla väntande processer som körs under en fråga. Du kan använda den här aggregerade vyn för att diagnostisera prestanda problem med Azure SQL Database och även med vissa frågor och batchar. Frågor kan vänta på resurser, kön väntar eller externa vänte tid.
- Använd [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) för att returnera information om den kö med aktiviteter som väntar på en resurs.

I scenarier med hög processor kan Query Store och väntande statistik avspegla CPU-användning om:

- Frågor med hög processor användning körs fortfarande.
- Frågor med hög CPU-användning kördes när redundansväxlingen skedde.

DMV: er som spårar Frågearkivet och väntande statistik visar resultat för endast slutförda och uppnådde tids gräns frågor. De visar inga data för körnings instruktioner förrän instruktionerna är slutförda. Använd vyn dynamisk hantering [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) för att spåra för tillfället att köra frågor och den associerade arbets tiden.

> [!TIP]
> Ytterligare verktyg:
>
> - [TigerToolbox vänte tid och lås](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>Nästa steg

[Översikt över SQL Database övervakning och justering](database/monitor-tune-overview.md)