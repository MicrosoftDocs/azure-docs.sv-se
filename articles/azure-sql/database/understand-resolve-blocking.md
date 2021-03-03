---
title: Förstå och lösa problem med Azure SQL-blockering
titleSuffix: Azure SQL Database
description: En översikt över Azure SQL Database-specifika avsnitt om blockering och fel sökning.
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 2/24/2021
ms.openlocfilehash: b829d7045ac520cfe908c3c8809ae17702d6175d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691441"
---
# <a name="understand-and-resolve-azure-sql-database-blocking-problems"></a>Förstå och lösa Azure SQL Database spärrnings problem
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="objective"></a>Mål

Artikeln beskriver blockering i Azure SQL-databaser och visar hur du felsöker och löser blockering. 

I den här artikeln refererar termen anslutning till en enkel inloggad session av databasen. Varje anslutning visas som ett sessions-ID (SPID) eller session_id i många DMV: er. Vart och ett av dessa SPID kallas ofta en process, även om det inte är en separat process kontext i den vanliga meningen. Varje SPID består i stället av de server resurser och data strukturer som krävs för att betjäna förfrågningarna för en enskild anslutning från en viss klient. Ett enda klient program kan ha en eller flera anslutningar. Från Azure SQL Database perspektiv finns det ingen skillnad mellan flera anslutningar från ett enda klient program på en enskild klient dator och flera anslutningar från flera klient program eller flera klient datorer. de är atomiska. En anslutning kan blockera en annan anslutning, oavsett käll klient.

> [!NOTE]
> **Det här innehållet fokuserar på Azure SQL Database.** Azure SQL Database baseras på den senaste stabila versionen av Microsoft SQL Server-databasmotorn, så mycket av innehållet påminner om fel söknings alternativ och verktyg kan skilja sig. Mer information om blockering i SQL Server finns [förstå och lösa SQL Server blockera problem](/troubleshoot/sql/performance/understand-resolve-blocking).

## <a name="understand-blocking"></a>Förstå blockering 
 
Blockering är en oundviklig och avsiktlig egenskap för alla relationella databas hanterings system (RDBMS) med lås-baserad samtidighet. Som tidigare nämnts inträffar det i SQL Server när en session har ett lås på en angiven resurs och ett andra SPID försöker hämta en motstridig lås typ på samma resurs. Normalt är den tidsram för vilken det första SPID låser resursen liten. När den ägande sessionen släpper låset, är den andra anslutningen kostnads fri att förvärva sitt eget lås på resursen och fortsätta bearbetningen. Detta är normalt beteendet och kan inträffa många gånger under en dag utan märkbar påverkan på system prestanda.

Tids-och transaktions kontexten för en fråga bestämmer hur länge dess lås hålls och hur länge de påverkar andra frågor. Om frågan inte körs inom en transaktion (och inga lås tips används), lagras bara låsen för SELECT-instruktioner på en resurs vid den tidpunkt då den faktiskt läses, inte under frågan. För INSERT-, UPDATE-och DELETE-instruktioner hålls låsen under frågan, både för data konsekvens och för att tillåta att frågan återställs vid behov.

För frågor som körs inom en transaktion bestäms hur länge låsen hålls av typ av fråga, isolerings nivå för transaktionen och om lås tips används i frågan. En beskrivning av hur du låser, låser tips och transaktions isolerings nivåer finns i följande artiklar:

* [Låsning i databasmotorn](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [Anpassa låsning och rad version](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#customizing-locking-and-row-versioning)
* [Lås lägen](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_modes)
* [Lås kompatibilitet](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_compatibility)
* [Transaktioner](/sql/t-sql/language-elements/transactions-transact-sql)

När du låser och blockerar är kvar på platsen där det finns en skadlig effekt på system prestandan beror det på någon av följande orsaker:

* Ett SPID innehåller lås på en uppsättning resurser under en längre tid innan de släpps. Den här typen av blockering löser sig självt över tid, men kan leda till försämrade prestanda.

* Ett SPID innehåller lås på en uppsättning resurser och släpper aldrig ut dem. Den här typen av blockering löser inte sig själv och förhindrar åtkomst till de berörda resurserna på obestämd tid.

I det första scenariot kan situationen vara mycket flytande eftersom olika SPID orsakar blockering av olika resurser över tid, vilket skapar ett flytt mål. Dessa situationer är svåra att felsöka med hjälp av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) för att begränsa problemet med enskilda frågor. Den andra situationen resulterar däremot i ett konsekvent tillstånd som kan vara lättare att diagnostisera.

## <a name="applications-and-blocking"></a>Program och blockering

Det kan vara en tendens att fokusera på Server sidans justerings-och plattforms problem när ett blockerande problem uppstår. Den uppmärksamhet som betalats ut till databasen kanske inte leder till en lösning och kan absorbera tiden och energin bättre riktad mot att undersöka klient programmet och de frågor som skickas. Oavsett vilken Synlighets nivå som programmet exponerar för databas anrop som görs, kräver ett blockerande problem ofta både granskning av de exakta SQL-uttryck som skickas av programmet och programmets exakta beteende för annullering av frågor, anslutnings hantering, hämtning av alla resultat rader och så vidare. Om utvecklings verktyget inte tillåter explicit kontroll över anslutnings hantering, fråga om tids gräns, resultat hämtning och så vidare, kan det hända att blockerande problem inte kan matchas. Den här potentialen bör kontrol leras noga innan du väljer ett program utvecklings verktyg för Azure SQL Database, särskilt för prestanda känsliga OLTP-miljöer. 

Var uppmärksam på databas prestanda under design-och konstruktions fasen av databasen och programmet. I synnerhet bör resurs förbrukningen, isolerings nivån och längden på transaktions Sök vägen utvärderas för varje fråga. Varje fråga och transaktion bör vara så lätt som möjligt. Disciplinen för hantering av anslutnings hantering måste utnyttjas, utan det kan programmet förefalla ha acceptabel prestanda till ett lågt antal användare, men prestandan kan försämras avsevärt om antalet användare skalas upp. 

Med rätt program-och fråge design kan Azure SQL Database stödja flera tusentals samtidiga användare på en enskild server, med lite spärr.

> [!Note]
> Mer information om program utveckling finns i [Felsöka anslutnings problem och andra fel med Azure SQL Database och Azure SQL-hanterad instans](troubleshoot-common-errors-issues.md) och [hantering av tillfälliga fel](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling).

## <a name="troubleshoot-blocking"></a>Felsöka blockering

Oavsett vilken spärr situation vi befinner dig i är metoden för fel söknings låsning densamma. Dessa logiska separationer anger vad som kommer att diktera resten av sammansättningen i den här artikeln. Konceptet är att hitta huvud blocket och identifiera vad frågan gör och varför den blockeras. När den problematiska frågan har identifierats (det vill säga vad som håller på att låsas för den längre perioden) är nästa steg att analysera och avgöra varför blockeringen sker. När vi har förstått varför kan vi göra ändringar genom att designa om frågan och transaktionen.

Steg i fel sökning:

1. Identifiera den huvudsakliga spärrnings sessionen (huvud Blocker)

2. Hitta frågan och transaktionen som orsakar blockeringen (vad håller sig lås under en längre period)

3. Analysera/förstå varför den långvariga blockeringen inträffar

4. Lös blockerings problem genom att designa om fråga och transaktion

Nu ska vi ta en titt på hur du kan hitta den huvudsakliga spärrnings sessionen med en lämplig data insamling.

## <a name="gather-blocking-information"></a>Samla in information om blockering

En databas administratör kan använda SQL-skript som kontinuerligt övervakar statusen för att låsa och blockera i Azure SQL-databasen för att motverka svårigheter att felsöka blockerade problem. Det finns två metoder för att samla in dessa data. 

Det första är att fråga dynamiska hanterings objekt (DMOs) och lagra resultaten för jämförelse över tid. Vissa objekt som refereras i den här artikeln är DMV: er (Dynamic Management views) och vissa är DMFs (Dynamic Management Functions). Den andra metoden är att använda XEvents för att avbilda vad som körs. 


## <a name="gather-information-from-dmvs"></a>Samla in information från DMV: er

Att referera till DMV: er för att felsöka blockering har målet att identifiera SPID (sessions-ID) i huvudet i den blockerande kedjan och SQL-instruktionen. Sök efter skadelidande SPID som blockeras. Om något SPID blockeras av en annan SPID undersöker du SPID som äger resursen (blockerings-SPID). Är den ägaren SPID som blockeras också? Du kan gå till kedjan för att hitta huvud blocket och undersöka varför den håller på att vara låst.

Kom ihåg att köra vart och ett av dessa skript i Azure SQL-databasen.

* Kommandona sp_who och sp_who2 är äldre kommandon för att visa alla aktuella sessioner. DMV-sys.dm_exec_sessions returnerar mer data i en resultat mängd som är lättare att fråga och filtrera. Du hittar sys.dm_exec_sessions i kärnan av andra frågor. 

* Om du redan har identifierat en viss session kan du använda `DBCC INPUTBUFFER(<session_id>)` för att hitta den sista instruktionen som skickades av en session. Liknande resultat kan returneras med sys.dm_exec_input_buffer dynamisk hanterings funktion (DMF) i en resultat uppsättning som är enklare att fråga och filtrera, vilket ger session_id och request_id. Om du till exempel vill returnera den senaste frågan som skickats in session_id 66 och request_id 0:

```sql
SELECT * FROM sys.dm_exec_input_buffer (66,0);
```

* Referera till sys.dm_exec_requests och referera till kolumnen blocking_session_id. När blocking_session_id = 0 blockeras ingen session. Även om sys.dm_exec_requests visar en lista över begär Anden som för närvarande körs, visas alla anslutningar (aktiva eller inte) i sys.dm_exec_sessions. Bygg vidare på den här gemensamma kopplingen mellan sys.dm_exec_requests och sys.dm_exec_sessions i nästa fråga.

* Kör den här exempel frågan för att hitta aktiva körnings frågor och den aktuella SQL-satsen text eller inmatad buffert med hjälp av [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) eller [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) DMV: er. Om data som returneras av `text` fältet sys.dm_exec_sql_text är null körs inte frågan för tillfället. I så fall `event_info` kommer fältet för sys.dm_exec_input_buffer innehålla den sista kommando strängen som skickas till SQL-motorn. Den här frågan kan också användas för att identifiera sessioner som blockerar andra sessioner, inklusive en lista över session_ids som blockeras per session_id. 

```sql
WITH cteBL (session_id, blocking_these) AS 
(SELECT s.session_id, blocking_these = x.blocking_these FROM sys.dm_exec_sessions s 
CROSS APPLY    (SELECT isnull(convert(varchar(6), er.session_id),'') + ', '  
                FROM sys.dm_exec_requests as er
                WHERE er.blocking_session_id = isnull(s.session_id ,0)
                AND er.blocking_session_id <> 0
                FOR XML PATH('') ) AS x (blocking_these)
)
SELECT s.session_id, blocked_by = r.blocking_session_id, bl.blocking_these
, batch_text = t.text, input_buffer = ib.event_info, * 
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
INNER JOIN cteBL as bl on s.session_id = bl.session_id
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) t
OUTER APPLY sys.dm_exec_input_buffer(s.session_id, NULL) AS ib
WHERE blocking_these is not null or r.blocking_session_id > 0
ORDER BY len(bl.blocking_these) desc, r.blocking_session_id desc, r.session_id;
```

* Kör den här mer avancerade exempel frågan, som tillhandahålls av Microsoft Support, för att identifiera huvudet på en spärr kedja för flera sessioner, inklusive frågetexten för de sessioner som ingår i en spärr kedja.

```sql
WITH cteHead ( session_id,request_id,wait_type,wait_resource,last_wait_type,is_user_process,request_cpu_time
,request_logical_reads,request_reads,request_writes,wait_time,blocking_session_id,memory_usage
,session_cpu_time,session_reads,session_writes,session_logical_reads
,percent_complete,est_completion_time,request_start_time,request_status,command
,plan_handle,sql_handle,statement_start_offset,statement_end_offset,most_recent_sql_handle
,session_status,group_id,query_hash,query_plan_hash) 
AS ( SELECT sess.session_id, req.request_id, LEFT (ISNULL (req.wait_type, ''), 50) AS 'wait_type'
    , LEFT (ISNULL (req.wait_resource, ''), 40) AS 'wait_resource', LEFT (req.last_wait_type, 50) AS 'last_wait_type'
    , sess.is_user_process, req.cpu_time AS 'request_cpu_time', req.logical_reads AS 'request_logical_reads'
    , req.reads AS 'request_reads', req.writes AS 'request_writes', req.wait_time, req.blocking_session_id,sess.memory_usage
    , sess.cpu_time AS 'session_cpu_time', sess.reads AS 'session_reads', sess.writes AS 'session_writes', sess.logical_reads AS 'session_logical_reads'
    , CONVERT (decimal(5,2), req.percent_complete) AS 'percent_complete', req.estimated_completion_time AS 'est_completion_time'
    , req.start_time AS 'request_start_time', LEFT (req.status, 15) AS 'request_status', req.command
    , req.plan_handle, req.[sql_handle], req.statement_start_offset, req.statement_end_offset, conn.most_recent_sql_handle
    , LEFT (sess.status, 15) AS 'session_status', sess.group_id, req.query_hash, req.query_plan_hash
    FROM sys.dm_exec_sessions AS sess
    LEFT OUTER JOIN sys.dm_exec_requests AS req ON sess.session_id = req.session_id
    LEFT OUTER JOIN sys.dm_exec_connections AS conn on conn.session_id = sess.session_id 
    )
, cteBlockingHierarchy (head_blocker_session_id, session_id, blocking_session_id, wait_type, wait_duration_ms,
wait_resource, statement_start_offset, statement_end_offset, plan_handle, sql_handle, most_recent_sql_handle, [Level])
AS ( SELECT head.session_id AS head_blocker_session_id, head.session_id AS session_id, head.blocking_session_id
    , head.wait_type, head.wait_time, head.wait_resource, head.statement_start_offset, head.statement_end_offset
    , head.plan_handle, head.sql_handle, head.most_recent_sql_handle, 0 AS [Level]
    FROM cteHead AS head
    WHERE (head.blocking_session_id IS NULL OR head.blocking_session_id = 0)
    AND head.session_id IN (SELECT DISTINCT blocking_session_id FROM cteHead WHERE blocking_session_id != 0)
    UNION ALL
    SELECT h.head_blocker_session_id, blocked.session_id, blocked.blocking_session_id, blocked.wait_type,
    blocked.wait_time, blocked.wait_resource, h.statement_start_offset, h.statement_end_offset,
    h.plan_handle, h.sql_handle, h.most_recent_sql_handle, [Level] + 1
    FROM cteHead AS blocked
    INNER JOIN cteBlockingHierarchy AS h ON h.session_id = blocked.blocking_session_id and h.session_id!=blocked.session_id --avoid infinite recursion for latch type of blocking
    WHERE h.wait_type COLLATE Latin1_General_BIN NOT IN ('EXCHANGE', 'CXPACKET') or h.wait_type is null
    )
SELECT bh.*, txt.text AS blocker_query_or_most_recent_query 
FROM cteBlockingHierarchy AS bh 
OUTER APPLY sys.dm_exec_sql_text (ISNULL ([sql_handle], most_recent_sql_handle)) AS txt;
```

* Om du vill fånga långvariga eller ej allokerade transaktioner använder du en annan uppsättning DMV: er för att visa aktuella öppna transaktioner, inklusive [sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql), [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql), [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql)och sys.dm_exec_sql_text. Det finns flera DMV: er som är kopplade till spårnings transaktioner, se fler [DMV: er för transaktioner](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql) här. 

```sql
SELECT [s_tst].[session_id],
[database_name] = DB_NAME (s_tdt.database_id),
[s_tdt].[database_transaction_begin_time], 
[sql_text] = [s_est].[text] 
FROM sys.dm_tran_database_transactions [s_tdt]
INNER JOIN sys.dm_tran_session_transactions [s_tst] ON [s_tst].[transaction_id] = [s_tdt].[transaction_id]
INNER JOIN sys.dm_exec_connections [s_ec] ON [s_ec].[session_id] = [s_tst].[session_id]
CROSS APPLY sys.dm_exec_sql_text ([s_ec].[most_recent_sql_handle]) AS [s_est];
```

* Referens [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) som finns på nivån tråd/aktivitet i SQL. Detta returnerar information om vilken SQL-wait-typ som begäran har för närvarande. Precis som sys.dm_exec_requests returneras bara aktiva begär anden av sys.dm_os_waiting_tasks. 

> [!Note]
> Mer information om väntande typer, inklusive sammanställd väntande statistik över tid, finns i [sys.DM_DB_WAIT_STATS](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)DMV. Den här DMV returnerar den sammanlagda wait-statistiken för den aktuella databasen.

* Använd [sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql) DMV för mer detaljerad information om vilka lås som har placerats av frågor. Denna DMV kan returnera stora mängder data på en produktions SQL Server och är användbar för att diagnostisera vilka lås som för närvarande hålls. 

På grund av den inre kopplingen på sys.dm_os_waiting_tasks begränsar följande fråga utdata från sys.dm_tran_locks till just nu blockerade begär Anden, deras vänte status och deras Lås:

```sql
SELECT table_name = schema_name(o.schema_id) + '.' + o.name
, wt.wait_duration_ms, wt.wait_type, wt.blocking_session_id, wt.resource_description
, tm.resource_type, tm.request_status, tm.request_mode, tm.request_session_id
FROM sys.dm_tran_locks AS tm
INNER JOIN sys.dm_os_waiting_tasks as wt ON tm.lock_owner_address = wt.resource_address
LEFT OUTER JOIN sys.partitions AS p on p.hobt_id = tm.resource_associated_entity_id
LEFT OUTER JOIN sys.objects o on o.object_id = p.object_id or tm.resource_associated_entity_id = o.object_id
WHERE resource_database_id = DB_ID()
AND object_name(p.object_id) = '<table_name>';
```

* Med DMV: er kan du lagra frågeresultaten över tiden och tillhandahålla data punkter som gör att du kan granska blockeringen under ett visst tidsintervall för att identifiera bestående blockerade eller trender. 

## <a name="gather-information-from-extended-events"></a>Samla in information från utökade händelser

Förutom ovanstående information är det ofta nödvändigt att samla in en spårning av aktiviteterna på servern för att noggrant undersöka ett blockerande problem på Azure SQL Database. Om en session exempelvis kör flera instruktioner i en transaktion, visas bara den sista instruktionen som skickades. Ett av de tidigare instruktionerna kan dock vara orsaken till att låsen fortfarande hålls. En spårning gör att du kan se alla kommandon som körs av en session i den aktuella transaktionen.

Det finns två sätt att samla in spårningar i SQL Server. Utökade händelser (XEvents) och profiler spårar. Men [SQL Server profiler](/sql/tools/sql-server-profiler/sql-server-profiler) är föråldrad spårnings teknik som inte stöds för Azure SQL Database. [Utökade händelser](/sql/relational-databases/extended-events/extended-events) är den nyare spårnings tekniken som ger mer mångsidighet och mindre påverkan på det observerade systemet, och dess gränssnitt är integrerat i SQL Server Management Studio (SSMS). 

Se dokumentet som förklarar hur du använder [guiden Ny session för utökade händelser](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) i SSMS. För Azure SQL-databaser innehåller SSMS dock en undermapp för utökade händelser under varje databas i Object Explorer. Använd en session med utökade händelser för att avbilda följande användbara händelser: 

-   Kategori fel:
    -   Åt
    -   Error_reported  
    -   Execution_warning

-   Kategori varningar: 
    -   Missing_join_predicate

-   Kategori körning:
    -   Rpc_completed
    -   Rpc_starting 
    -   Sql_batch_completed
    -   Sql_batch_starting

-   Låsa
    -   Lock_deadlock

-   Session
    -   Existing_connection
    -   Inloggning
    -   Utloggning

## <a name="identify-and-resolve-common-blocking-scenarios"></a>Identifiera och lösa vanliga spärrnings scenarier

Genom att undersöka ovanstående information kan du fastställa orsaken till de flesta spärrnings problem. Resten av den här artikeln är en beskrivning av hur du använder den här informationen för att identifiera och lösa några vanliga spärrnings scenarier. Den här diskussionen förutsätter att du har använt blockerade skript (refererad tidigare) för att samla in information om blockerade SPID och har insamlat program aktivitet med hjälp av en XEvent-session.

## <a name="analyze-blocking-data"></a>Analysera data som blockeras 

* Undersök resultatet av DMV: er-sys.dm_exec_requests och sys.dm_exec_sessions för att fastställa huvudena för spärrnings kedjorna med blocking_these och session_id. Detta kommer att tydligt identifiera vilka förfrågningar som blockeras och som blockerar. Titta närmare på de sessioner som blockeras och blockeras. Finns det en gemensam eller rot till spärrnings kedjan? De delar antagligen en gemensam tabell och en eller flera av sessionerna som ingår i en spärr kedja utför en Skriv åtgärd. 

* Granska utdata från DMV: er-sys.dm_exec_requests och sys.dm_exec_sessions för information om SPID: t i huvudet i den blockerande kedjan. Leta efter följande fält: 

    -    `sys.dm_exec_requests.status`  
    Den här kolumnen visar status för en viss begäran. Normalt indikerar statusen vilande att SPID har slutfört körningen och väntar på att programmet ska skicka en annan fråga eller batch. En körbara eller kör status anger att SPID för närvarande bearbetar en fråga. Följande tabell innehåller korta förklaringar av de olika status värdena.

    | Status | Innebörd |
    |:-|:-|
    | Bakgrund | SPID kör en bakgrunds aktivitet, till exempel död läges identifiering, logg skrivare eller kontroll punkt. |
    | Sätt | SPID körs inte för närvarande. Detta indikerar vanligt vis att SPID väntar på ett kommando från programmet. |
    | Körs | SPID körs för närvarande på en Scheduler. |
    | Körbara | SPID är i körbara-kön för en Scheduler och väntar på att hämta Scheduler Time. |
    | Inaktiverad | SPID väntar på en resurs, till exempel ett lås eller en spärr. | 
                       
    -    `sys.dm_exec_sessions.open_transaction_count`  
    Det här fältet visar antalet öppna transaktioner i den här sessionen. Om det här värdet är större än 0, är SPID i en öppen transaktion och kan hålla lås som har förvärv ATS av vilken instruktion som helst i transaktionen.

    -    `sys.dm_exec_requests.open_transaction_count`  
    På samma sätt visar det här fältet antalet öppna transaktioner i denna begäran. Om det här värdet är större än 0, är SPID i en öppen transaktion och kan hålla lås som har förvärv ATS av vilken instruktion som helst i transaktionen.

    -   `sys.dm_exec_requests.wait_type`, `wait_time` och `last_wait_type`  
    Om  `sys.dm_exec_requests.wait_type`   är null väntar inte begäran på något och  `last_wait_type`   värdet anger sist  `wait_type`   att begäran påträffades. Mer information om  `sys.dm_os_wait_stats` och en beskrivning av de vanligaste vänte typerna finns i [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).  `wait_time`   Värdet kan användas för att avgöra om begäran gör förlopp. När en fråga mot sys.dm_exec_requestss tabellen returnerar ett värde i  `wait_time`   kolumnen som är mindre än  `wait_time`   värdet från en föregående fråga om sys.dm_exec_requests, indikerar detta att det tidigare låset hämtades och släpptes och väntar nu på ett nytt lås (förutsatt att det inte är noll `wait_time` ). Detta kan verifieras genom att jämföra `wait_resource`   sys.dm_exec_requests-utdata som visar den resurs som begäran väntar på.

    -   `sys.dm_exec_requests.wait_resource` Det här fältet anger den resurs som en blockerad begäran väntar på. I följande tabell visas vanliga  `wait_resource`   format och deras innebörd:

    | Resurs | Format | Exempel | Förklaring | 
    |:-|:-|:-|:-|
    | Tabell | DatabaseID: ObjectID: IndexID | FLIK: 5:261575970:1 | I det här fallet är databas-ID 5 pubs-exempel databasen och objekt-ID 261575970 är rubrik tabellen och 1 är det grupperade indexet. |
    | Sida | DatabaseID: FileID: PageID | SIDA: 5:1:104 | I det här fallet är databas-ID 5 pubs, fil-ID 1 är den primära data filen och sidan 104 är en sida som tillhör tabellen titlar. För att identifiera object_id sidan tillhör, använder du den dynamiska hanterings funktionen [sys.dm_db_page_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql), skickar DatabaseID, FileId, PageId från `wait_resource` . | 
    | Nyckel | DatabaseID: Hobt_id (hash-värde för index nyckel) | NYCKEL: 5:72057594044284928 (3300a4f361aa) | I det här fallet är databas-ID 5 pubs, Hobt_ID 72057594044284928 motsvarar index_id 2 för object_id 261575970 (rubrik tabell). Använd vyn sys. partitions-katalog för att koppla hobt_id till en viss index_id och object_id. Det finns inget sätt att avhasha index nyckelns hash-värde för ett speciellt nyckel värde. |
    | Rad | DatabaseID: FileID: PageID: fack (rad) | RID: 5:1:104:3 | I det här fallet är databas-ID 5 pubs, fil-ID 1 är den primära data filen, sidan 104 är en sida som tillhör tabellen titlar och fack 3 anger radens position på sidan. |
    | Kompileringsfel  | DatabaseID: FileID: PageID: fack (rad) | RID: 5:1:104:3 | I det här fallet är databas-ID 5 pubs, fil-ID 1 är den primära data filen, sidan 104 är en sida som tillhör tabellen titlar och fack 3 anger radens position på sidan. |

    -    `sys.dm_tran_active_transactions`[Sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql) DMV innehåller information om öppna transaktioner som kan kopplas till andra DMV: er för en fullständig bild av transaktioner som väntar på genomförande eller återställning. Använd följande fråga för att returnera information om öppna transaktioner som är kopplade till andra DMV: er, inklusive [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql). Överväg att använda en transaktions aktuella status, `transaction_begin_time` och andra situations data för att utvärdera om det kan vara en källa till spärrning.

    ```sql
    SELECT tst.session_id, [database_name] = db_name(s.database_id)
    , tat.transaction_begin_time
    , transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime()) 
    , transaction_type = CASE tat.transaction_type  WHEN 1 THEN 'Read/write transaction'
                                                    WHEN 2 THEN 'Read-only transaction'
                                                    WHEN 3 THEN 'System transaction'
                                                    WHEN 4 THEN 'Distributed transaction' END
    , input_buffer = ib.event_info, tat.transaction_uow     
    , transaction_state  = CASE tat.transaction_state    
                WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                WHEN 1 THEN 'The transaction has been initialized but has not started.'
                WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                WHEN 4 THEN 'The commit process has been initiated on the distributed transaction.'
                WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                WHEN 6 THEN 'The transaction has been committed.'
                WHEN 7 THEN 'The transaction is being rolled back.'
                WHEN 8 THEN 'The transaction has been rolled back.' END 
    , transaction_name = tat.name, request_status = r.status
    , azure_dtc_state = CASE tat.dtc_state 
                        WHEN 1 THEN 'ACTIVE'
                        WHEN 2 THEN 'PREPARED'
                        WHEN 3 THEN 'COMMITTED'
                        WHEN 4 THEN 'ABORTED'
                        WHEN 5 THEN 'RECOVERED' END
    , tst.is_user_transaction, tst.is_local
    , session_open_transaction_count = tst.open_transaction_count  
    , s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
    FROM sys.dm_tran_active_transactions tat 
    INNER JOIN sys.dm_tran_session_transactions tst  on tat.transaction_id = tst.transaction_id
    INNER JOIN Sys.dm_exec_sessions s on s.session_id = tst.session_id 
    LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
    CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib;
    ```

    -   Andra kolumner

        De återstående kolumnerna i [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) och [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) kan ge inblick i roten av ett problem också. Deras användbarhet varierar beroende på problemets omständigheter. Du kan till exempel kontrol lera om problemet bara uppstår från vissa klienter (hostname) i vissa nätverks bibliotek (net_library) när den senaste batch som skickades av ett SPID var `last_request_start_time` i sys.dm_exec_sessions, hur länge en begäran kördes med `start_time` i sys.dm_exec_requests och så vidare.


## <a name="common-blocking-scenarios"></a>Vanliga spärrnings scenarier

Tabellen nedan mappar vanliga symtom till troliga orsaker.  

`wait_type` `open_transaction_count` Kolumnerna, och `status` refererar till information som returneras av [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql), och andra kolumner kan returneras av [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql). "Matchar?" kolumnen anger om blockeringen ska matchas separat eller inte, eller om sessionen ska avlivas via `KILL` kommandot. Mer information finns i [Kill (Transact-SQL)](/sql/t-sql/language-elements/kill-transact-sql).

| Scenario | Waittype | Open_Tran | Status | Löser? | Andra symptom |  
|:-|:-|:-|:-|:-|:-|--|
| 1 | INTE NULL | >= 0 | körbara | Ja, när frågan har slutförts. | I sys.dm_exec_sessions kommer **läsa**, **cpu_time** och/eller **memory_usage** kolumner att öka med tiden. Varaktigheten för frågan blir hög när den har slutförts. |
| 2 | NULL | \>0,0 | sätt | Nej, men SPID kan stoppas. | En Attention-signal kan ses i den utökade Händelsesessionen för denna SPID, vilket indikerar att tids gränsen för frågor är slut eller Avbryt har inträffat. |
| 3 | NULL | \>= 0 | körbara | Nej. Kommer inte att lösas förrän klienten hämtar alla rader eller stänger anslutningen. SPID kan stoppas, men det kan ta upp till 30 sekunder. | Om open_transaction_count = 0 och SPID innehåller Lås medan transaktions isolerings nivån är standard (Läs COMMMITTED) är detta en sannolik orsak. |  
| 4 | Det varierar | \>= 0 | körbara | Nej. Kommer inte att lösas förrän klienten avbryter frågor eller stänger anslutningar. SPID kan stoppas, men det kan ta upp till 30 sekunder. | Kolumnen **hostname** i sys.DM_EXEC_SESSIONS för SPID vid en spärrnings kedja är samma som ett av SPID: t som blockeras. |  
| 5 | NULL | \>0,0 | ånger | Ja. | En Attention-signal kan visas i den utökade händelse-sessionen för denna SPID, vilket indikerar att tids gränsen för frågor är slut eller Avbryt har inträffat, eller att en återställnings instruktion har utfärdats. |  
| 6 | NULL | \>0,0 | sätt | Slutligen. När Windows NT fastställer att sessionen inte längre är aktiv, kommer Azure SQL Database anslutningen att brytas. | `last_request_start_time`Värdet i sys.dm_exec_sessions är mycket tidigare än den aktuella tiden. |

Följande scenarier kommer att utökas i dessa scenarier. 

1.  Blockering som orsakas av en fråga som normalt körs med en lång körnings tid

    **Lösning**: lösningen på den här typen av spärrnings problem är att söka efter sätt att optimera frågan. Den här klassen av spärrnings problem kan faktiskt vara ett prestanda problem och kräver att du använder den som sådan. Information om hur du felsöker en speciell långsam fråga finns i [så här felsöker du långsamma frågor som körs på SQL Server](/troubleshoot/sql/performance/troubleshoot-slow-running-queries). Mer information finns i [övervaka och justera för prestanda](/sql/relational-databases/performance/monitor-and-tune-for-performance). 

    Rapporter från [frågearkivet](/sql/relational-databases/performance/best-practice-with-the-query-store) i SSMS är också ett starkt rekommenderat och värdefullt verktyg för att identifiera de mest kostsamma frågorna, under optimala körnings planer. Läs även avsnittet [intelligent prestanda](intelligent-insights-overview.md) i Azure Portal för Azure SQL Database, inklusive [query Performance Insight](query-performance-insight-use.md).

    Om du har en lång körnings fråga som blockerar andra användare och inte kan optimeras bör du överväga att flytta den från en OLTP-miljö till ett dedikerat rapporterings system, en [synkron skrivskyddad replik av databasen](read-scale-out.md).

1.  Blockering orsakad av en vilande SPID som har en ej allokerad transaktion

    Den här typen av blockering kan ofta identifieras med ett SPID som är i vilo läge eller väntar på ett kommando, men vars transaktions kapslings nivå ( `@@TRANCOUNT` , `open_transaction_count` från sys.dm_exec_requests) är större än noll. Detta kan inträffa om programmet upplever en fråge tids gräns eller utfärdar ett avbrott utan att även utfärda det nödvändiga antalet återställnings-och/eller COMMIT-instruktioner. När ett SPID tar emot en fråge-timeout eller Avbryt, kommer den aktuella frågan och batchen att avslutas, men inte automatiskt återställa eller bekräfta transaktionen. Programmet ansvarar för detta, eftersom Azure SQL Database inte kan anta att en hel transaktion måste återställas på grund av att en fråga avbryts. Frågan timeout eller Avbryt visas som en varnings händelse för SPID i Extended Event-sessionen.

    För att demonstrera en obekräftad explicit transaktion, utfärda följande fråga:

    ```sql
    CREATE TABLE #test (col1 INT);
    INSERT INTO #test SELECT 1;
    BEGIN TRAN
    UPDATE #test SET col1 = 2 where col1 = 1;
    ```

    Kör sedan den här frågan i samma fönster:
    ```sql
    SELECT @@TRANCOUNT;
    ROLLBACK TRAN
    DROP TABLE #test;
    ```

    Utdata från den andra frågan anger att transaktions kapslings nivån är en. Alla lås som har köpts i transaktionen hålls kvar tills transaktionen genomfördes eller återställdes. Om program explicit öppnar och genomför transaktioner kan en kommunikation eller ett annat fel lämna sessionen och dess transaktion i öppet tillstånd. 

    Använd skriptet ovan baserat på sys.dm_tran_active_transactions för att identifiera transaktioner som inte är allokerade.

    **Lösningar**:

     -   Dessutom kan den här typen av blockerande problem också vara ett prestanda problem och kräva att du kan använda den som sådan. Om körnings tiden för frågan kan minskas inträffar inte frågan timeout eller Avbryt. Det är viktigt att programmet kan hantera timeout-och avbrotts scenarier, men du kan också dra nytta av att undersöka frågans prestanda. 
     
     -    Program måste hantera transaktions kapslings nivåer korrekt, eller så kan de orsaka ett spärrnings problem när frågan har avbrutits på det här sättet. Tänk också på följande:  

            *    Kör följande fel i klient programmets fel hanterare `IF @@TRANCOUNT > 0 ROLLBACK TRAN` , även om klient programmet inte tror att en transaktion är öppen. Det krävs en sökning efter öppna transaktioner, eftersom en lagrad procedur som anropas under batchen kunde starta en transaktion utan klient programmets kunskap. Vissa villkor, till exempel att avbryta frågan, förhindrar att proceduren körs förbi den aktuella instruktionen, så även om proceduren har logik för att kontrol lera `IF @@ERROR <> 0` och avbryta transaktionen utförs inte återställnings koden i sådana fall.  
            *    Om anslutningspoolen används i ett program som öppnar anslutningen och kör ett litet antal frågor innan anslutningen till poolen skickas tillbaka till poolen, t. ex. ett webbaserat program, kan tillfälliga inaktive ring av anslutningspoolen minimera problemet tills klient programmet har ändrats för att hantera felen på lämpligt sätt. Genom att inaktivera anslutningspoolen, orsakar en anslutning av anslutningen en fysisk från koppling av Azure SQL Database anslutningen, vilket leder till att servern återställer eventuella öppna transaktioner.  
            *    Används `SET XACT_ABORT ON` för anslutningen eller i alla lagrade procedurer som påbörjar transaktioner och som inte rensas efter ett fel. Om ett körnings fel inträffar avbryts den här inställningen eventuella öppna transaktioner och retur kontroll skickas till klienten. Mer information hittar du i [SET XACT_ABORT (Transact-SQL)](/sql/t-sql/statements/set-xact-abort-transact-sql).
    > [!NOTE]
    > Anslutningen återställs inte förrän den återanvänds från anslutningspoolen, så det är möjligt att en användare kan öppna en transaktion och sedan släppa anslutningen till anslutningspoolen, men den kan inte återanvändas i flera sekunder, medan transaktionen förblir öppen. Om anslutningen inte återanvänds avbryts transaktionen när tids gränsen för anslutningen uppnåddes och tas bort från anslutningspoolen. Därför är det optimalt att klient programmet kan avbryta transaktioner i sin fel hanterare eller använda `SET XACT_ABORT ON` för att undvika denna möjliga fördröjning.

    > [!CAUTION]
    > Följande `SET XACT_ABORT ON` är T-SQL-uttryck som följer en instruktion som gör att ett fel inte körs. Detta kan påverka det avsedda flödet av befintlig kod.

1.  Blockering orsakad av ett SPID vars motsvarande klient program inte hämtade alla resultat rader till slut för ande

    När du har skickat en fråga till servern måste alla program omedelbart hämta alla resultat rader för att kunna slutföras. Om ett program inte hämtar alla resultat rader, kan låsen vara kvar i tabellerna och blockera andra användare. Om du använder ett program som transparent skickar SQL-uttryck till servern måste programmet hämta alla resultat rader. Om det inte är (och om det inte kan konfigureras för det) kanske du inte kan lösa blockerings problemet. Du kan undvika problemet genom att begränsa dåligt ansvars program till rapporter eller databas för besluts support.
    
    > [!NOTE]
    > Se [rikt linjer för omprövnings logik](./troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) för program som ansluter till Azure SQL Database. 
    
    **Lösning**: programmet måste skrivas om för att hämta alla rader i resultatet till slut för ande. Detta gäller inte för att använda [förskjutning och hämta i order by-satsen](/sql/t-sql/queries/select-order-by-clause-transact-sql#using-offset-and-fetch-to-limit-the-rows-returned) i en fråga för att utföra växling på Server sidan.

1.  Blockering som orsakas av ett SPID som är i återställnings läge

    En data ändrings fråga som har stoppats eller avbrutits utanför en användardefinierad transaktion, kommer att återställas. Detta kan också inträffa som en sido effekt av klientens nätverks session från koppling, eller när en begäran har valts som död läge. Detta kan ofta identifieras genom att observera resultatet av sys.dm_exec_requests, vilket kan indikera återställnings **kommandot** och **kolumnen percent_complete** kan visa förloppet. 

    Tack vare en [accelererad databas återställnings funktion](../accelerated-database-recovery.md) som introducerades i 2019 bör långa återställningar vara ovanliga.
    
    **Lösning**: vänta tills SPID har återställt de ändringar som har gjorts. 

    Undvik den här situationen genom att inte utföra stora batch-skrivningar eller skapa index eller underhålls åtgärder under upptagna timmar på OLTP-system. Om möjligt kan du utföra sådana åtgärder under perioder med låg aktivitet.

1.  Blockering som orsakas av en överbliven anslutning

    Om klient programmet sväller fel eller om klient datorn startas om, kan sessionen till servern inte omedelbart avbrytas under vissa förhållanden. Från Azure SQL Database perspektiv förefaller klienten fortfarande vara närvarande och eventuella lås som har förvärv ATS kan fortfarande behållas. Mer information finns i [så här felsöker du överblivna anslutningar i SQL Server](/sql/t-sql/language-elements/kill-transact-sql#remarks). 

    **Lösning**: om klient programmet har kopplats från utan att behöva rensa resurserna kan du avsluta SPID med hjälp av `KILL` kommandot. `KILL`Kommandot tar SPID-värdet som inmatade. Om du till exempel vill avsluta SPID 99 utfärdar du följande kommando:

    ```sql
    KILL 99
    ```

## <a name="see-also"></a>Se även

* [Övervakning och prestandajustering för Azure SQL Database och Azure SQL Managed Instance](./monitor-tune-overview.md)
* [Övervaka prestanda med hjälp av Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [Guide för transaktionslåsning och radversionshantering](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [ANGE ISOLERINGS NIVÅ FÖR TRANSAKTION](/sql/t-sql/statements/set-transaction-isolation-level-transact-sql)
* [Snabbstart: Extended Events i SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
* [Intelligent Insights att använda AI för att övervaka och felsöka databas prestanda](intelligent-insights-overview.md)

## <a name="learn-more"></a>Läs mer

* [Azure SQL Database: förbättra prestanda justering med automatisk justering](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Improving-Performance-Tuning-with-Automatic-Tuning)
* [Förbättra Azure SQL Database prestanda med automatisk justering](https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning)
* [Leverera konsekventa prestanda med Azure SQL](/learn/modules/azure-sql-performance/)
* [Fel sökning av anslutnings problem och andra fel med Azure SQL Database och Azure SQL-hanterad instans](troubleshoot-common-errors-issues.md)
* [Hantering av tillfälliga fel](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)
