---
title: 'Övervaka prestanda med DMV: er'
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Lär dig hur du identifierar och diagnostiserar vanliga prestanda problem med hjälp av dynamiska hanterings vyer för att övervaka Microsoft Azure SQL Database och Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 03/15/2021
ms.openlocfilehash: 5c0de2c1589bfa495ab6ad287b998c403041674c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592174"
---
# <a name="monitoring-microsoft-azure-sql-database-and-azure-sql-managed-instance-performance-using-dynamic-management-views"></a>Övervaka prestanda för Microsoft Azure SQL Database och Azure SQL Managed Instance med hjälp av dynamiska hanteringsvyer
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Microsoft Azure SQL Database och Azure SQL-hanterad instans gör det möjligt för en delmängd av vyer för dynamisk hantering att diagnostisera prestanda problem, vilket kan orsakas av blockerade eller långvariga frågor, resurs Flask halsar, dåliga fråge planer och så vidare. Den här artikeln innehåller information om hur du identifierar vanliga prestanda problem med hjälp av dynamiska Management views.

Microsoft Azure SQL Database och Azure SQL Managed instance stöder delvis tre kategorier av vyer för dynamisk hantering:

- Databasbaserade vyer för dynamisk hantering.
- Vyer för dynamisk hantering i körnings läge.
- Transaktionsskyddade vyer för dynamisk hantering.

Detaljerad information om vyer för dynamisk hantering finns i [vyer och funktioner i dynamisk hantering (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

## <a name="monitor-with-sql-insights"></a>Övervaka med SQL Insights

[Azure Monitor SQL Insights](../../azure-monitor/insights/sql-insights-overview.md) är ett verktyg för att övervaka Azure SQL-hanterade instanser, Azure SQL-databaser och SQL Server instanser i virtuella Azure SQL-datorer. Den här tjänsten använder en fjärragent för att samla in data från DMV: er (Dynamic Management views) och dirigera data till Azure Log Analytics, där de kan övervakas och analyseras. Du kan visa dessa data från [Azure Monitor](../../azure-monitor/overview.md) i angivna vyer eller komma åt loggdata direkt för att köra frågor och analysera trender. Om du vill börja använda Azure Monitor SQL Insights, se [Aktivera SQL Insights](../../azure-monitor/insights/sql-insights-enable.md).

## <a name="permissions"></a>Behörigheter

I Azure SQL Database kräver en fråga till en dynamisk hanterings vy **Visa databas tillstånds** behörigheter. Behörigheten **Visa databas tillstånd** returnerar information om alla objekt i den aktuella databasen.
Om du vill ge behörigheten **Visa databas tillstånd** till en speciell databas användare kör du följande fråga:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

I en Azure SQL-hanterad instans kräver en förfrågan till vyn dynamisk hantering att **Visa Server tillstånds** behörigheter. Mer information finns i [vyer för dynamisk hantering i system](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views#required-permissions).

I en instans av SQL Server och i Azure SQL-hanterad instans, returnerar Dynamic Management views information om Server tillstånd. I Azure SQL Database returnerar de bara information om din aktuella logiska databas.

Den här artikeln innehåller en samling av DMV-frågor som du kan köra med hjälp av SQL Server Management Studio eller Azure Data Studio för att identifiera följande typer av frågor om prestanda:

- [Identifiera frågor som rör överdriven CPU-förbrukning](#identify-cpu-performance-issues)
- [PAGELATCH_ * och WRITE_LOG väntar på IO-Flask halsar](#identify-io-performance-issues)
- [PAGELATCH_ * väntar på att bytTempDB konkurrens orsakades](#identify-tempdb-performance-issues)
- [RESOURCE_SEMAHPORE väntar på att vänta på minnes tilldelnings problem](#identify-memory-grant-wait-performance-issues)
- [Identifiera databas-och objekt storlekar](#calculating-database-and-objects-sizes)
- [Hämtar information om aktiva sessioner](#monitoring-connections)
- [Hämta information om resursanvändning för hela systemet och databasen](#monitor-resource-use)
- [Hämtar information om frågans prestanda](#monitoring-query-performance)

## <a name="identify-cpu-performance-issues"></a>Identifiera problem med processorprestanda

Om CPU-förbrukningen är över 80% under längre tid, bör du tänka på följande fel söknings steg:

### <a name="the-cpu-issue-is-occurring-now"></a>CPU-problemet uppstår nu

Om problemet inträffar just nu finns det två möjliga scenarier:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Många enskilda frågor som sammantaget förbrukar hög CPU

Använd följande fråga för att identifiera Top-frågans hash-värden:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM (SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
    FROM (SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
                CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
    GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Tids krävande frågor som använder CPU körs fortfarande

Använd följande fråga för att identifiera dessa frågor:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
    CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>PROCESSOR problemet inträffade tidigare

Om problemet inträffade tidigare och du vill utföra rotor Saks analys, använder du [query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Användare med databas åtkomst kan använda T-SQL för att fråga efter Query Store-data. Standardkonfigurationer för Query Store använder en kornig het på 1 timme. Använd följande fråga för att titta på aktivitet för frågor med hög CPU-användning. Den här frågan returnerar de högsta 15 processor krävande frågorna. Kom ihåg att ändra `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()` :

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

När du har identifierat de problematiska frågorna är det dags att finjustera frågorna för att minska processor användningen.  Om du inte har tid att justera frågorna kan du också välja att uppgradera databasens service nivå mål för att lösa problemet.

## <a name="identify-io-performance-issues"></a>Identifiera problem med IO-prestanda

När du har problem med I/O-prestanda är följande väntetyper de som oftast associeras med I/O-problem:

- `PAGEIOLATCH_*`

  För data filens IO-problem (inklusive `PAGEIOLATCH_SH` , `PAGEIOLATCH_EX` , `PAGEIOLATCH_UP` ).  Om vänte typens namn har **IO** i det, pekar den på ett i/o-problem. Om det inte finns någon **IO** i väntan på sid låsning, pekar den på en annan typ av problem (till exempel tempdb-konkurrens).

- `WRITE_LOG`

  För transaktions loggens IO-problem.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Om IO-problemet inträffar just nu

Använd [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) eller [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) för att se `wait_type` och `wait_time` .

#### <a name="identify-data-and-log-io-usage"></a>Identifiera data-och logg-i/o-användning

Använd följande fråga för att identifiera data-och logg-i/o-användning. Om data eller logg-i/o är över 80% innebär det att användarna har använt tillgängligt IO för SQL Database tjänst nivån.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Om du har nått IO-gränsen har du två alternativ:

- Alternativ 1: Uppgradera beräkningsstorleken eller tjänstnivån
- Alternativ 2: identifiera och finjustera de frågor som förbrukar de flesta i/o.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Visa buffert-relaterad IO med Query Store

För alternativ 2 kan du använda följande fråga mot Frågearkivet för buffert-relaterad IO för att visa de senaste två timmarna för spårade aktiviteter:

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>Visa total logg-IO för WRITELOG vänta

Om wait-typen är `WRITELOG` använder du följande fråga för att visa total logg-IO enligt instruktion:

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>Identifiera `tempdb` prestanda problem

När du identifierar i/o-prestanda problem är de vanligaste vänte typerna som är associerade med `tempdb` problem `PAGELATCH_*` (inte `PAGEIOLATCH_*` ). Vänta dock `PAGELATCH_*` inte alltid att du har `tempdb` konkurrens.  Det kan också bero på konkurrens om en datasida med användarobjekt på grund av konkurrerande begäranden som görs mot samma datasida. Om du vill bekräfta `tempdb` konkurrens ytterligare använder du [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) för att bekräfta att wait_resource svärdet börjar med `2:x:y` där 2 är `tempdb` databas-ID: `x` t, är fil-ID och `y` är sid-ID.  

För tempdb-konkurrens är en vanlig metod att minska eller skriva om program kod som förlitar sig på `tempdb` .  Vanliga `tempdb` användnings områden är:

- Temporära tabeller
- Tabellvariabler
- Tabellvärdesparametrar
- Användning av versions lager (kopplat till tids krävande transaktioner)
- Frågor som har frågeplaner som använder sorteringar, hash-kopplingar och buffertar

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Vanligaste frågor som använder tabell variabler och temporära tabeller

Använd följande fråga för att identifiera de vanligaste frågorna som använder Table-variabler och temporära tabeller:

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>Identifiera tids krävande transaktioner

Använd följande fråga för att identifiera tids krävande transaktioner. Tids krävande transaktioner förhindrar rensning av versions arkiv.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>Identifiera problem med väntande minnes tilldelnings prestanda

Om den övre vänte typen är `RESOURCE_SEMAHPORE` och du inte har ett problem med hög CPU-användning kan det uppstå ett väntande minnes tilldelnings problem.

### <a name="determine-if-a-resource_semahpore-wait-is-a-top-wait"></a>Ta reda på om ett `RESOURCE_SEMAHPORE` väntetillstånd väntar

Använd följande fråga för att avgöra om ett `RESOURCE_SEMAHPORE` väntetillstånd väntar

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identify-high-memory-consuming-statements"></a>Identifiera höga minnes krävande uttryck

Använd följande fråga för att identifiera höga minnes krävande uttryck:

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>Identifiera de 10 främsta aktiva minnes beviljarna

Använd följande fråga för att identifiera de 10 viktigaste aktiva minnes beviljarna:

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>Beräkna databas- och objektstorlekar

Följande fråga returnerar databasens storlek (i MB):

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

Följande fråga returnerar storleken på enskilda objekt (i MB) i databasen:

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Övervaka anslutningar

Du kan använda vyn [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql) för att hämta information om de anslutningar som upprättats till en bestämd Server och en hanterad instans samt information om varje anslutning. Dessutom är vyn [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) användbar när du hämtar information om alla aktiva användar anslutningar och interna uppgifter.

Följande fråga hämtar information om den aktuella anslutningen:

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Om du har behörigheten **Visa databas tillstånd** för databasen när du kör **sys.dm_exec_requests** och **sys.dm_exec_sessions vyer**, ser du alla pågående sessioner på databasen. annars visas bara den aktuella sessionen.

## <a name="monitor-resource-use"></a>Övervaka resursanvändning

Du kan övervaka Azure SQL Database resursanvändning med [SQL Database Query Performance Insight](query-performance-insight-use.md). För Azure SQL Database och Azure SQL-hanterad instans kan du övervaka med hjälp av [query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

Du kan också övervaka användningen med följande vyer:

- Azure SQL Database: [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- Azure SQL-hanterad instans: [sys.server_resource_stats](/sql/relational-databases/system-catalog-views/sys-server-resource-stats-azure-sql-database)
- Både Azure SQL Database och Azure SQL-hanterad instans: [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)

### <a name="sysdm_db_resource_stats"></a>sys.dm_db_resource_stats

Du kan använda vyn [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) i varje databas. I vyn **sys.dm_db_resource_stats** visas senaste resurs användnings data i förhållande till tjänst nivån. Genomsnitts procent andelen för CPU, data-IO, logg skrivningar och minne registreras var 15: e sekund och bevaras i 1 timme.

Eftersom den här vyn ger en mer detaljerad titt på resursanvändningen använder **sys.dm_db_resource_stats** First för all analys av aktuella tillstånd eller fel sökning. Den här frågan visar till exempel den genomsnittliga och högsta resursanvändning som används för den aktuella databasen under den senaste timmen:

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

För andra frågor, se exemplen i [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database).

### <a name="sysserver_resource_stats"></a>sys.server_resource_stats

Du kan använda [sys.server_resource_stats](/sql/relational-databases/system-catalog-views/sys-server-resource-stats-azure-sql-database) för att returnera processor användning, IO och lagrings data för en hanterad Azure SQL-instans. Data samlas in och sammanställs inom fem minuters intervall. Det finns en rad för var 15: e sekunds rapportering. De data som returneras inkluderar CPU-användning, lagrings storlek, i/o-användning och SKU för hanterade instanser. Historiska data behålls i cirka 14 dagar.

```sql
DECLARE @s datetime;  
DECLARE @e datetime;  
SET @s= DateAdd(d,-7,GetUTCDate());  
SET @e= GETUTCDATE();  
SELECT resource_name, AVG(avg_cpu_percent) AS Average_Compute_Utilization
FROM sys.server_resource_stats
WHERE start_time BETWEEN @s AND @e  
GROUP BY resource_name  
HAVING AVG(avg_cpu_percent) >= 80;
```

### <a name="sysresource_stats"></a>sys.resource_stats

[Sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) -vyn i **huvud** databasen har ytterligare information som kan hjälpa dig att övervaka databasens prestanda på den aktuella tjänst nivån och beräknings storleken. Data samlas in var 5: e minut och bevaras i cirka 14 dagar. Den här vyn är användbar för en längre historisk analys av hur databasen använder resurser.

I följande diagram visas användningen av CPU-resurser för en Premium-databas med P2 Compute-storlek för varje timme under en vecka. Det här diagrammet startar på en måndag, visar fem arbets dagar och visar sedan en helg, om det händer mycket mindre i programmet.

![Användning av databas resurser](./media/monitoring-with-dmvs/sql_db_resource_utilization.png)

Den här databasen har för närvarande en hög processor belastning på bara över 50 procent CPU-användning i förhållande till P2 Compute-storlek (kulminera på tisdag). Om CPU är den dominerande faktorn i programmets resurs profil, kan du bestämma att P2 är rätt beräknings storlek för att garantera att arbets belastningen alltid passar. Om du förväntar dig att ett program ska växa över tid, är det en bra idé att ha en extra resurs-buffert så att programmet aldrig når prestanda nivå gränsen. Om du ökar beräknings storleken kan du undvika kund synliga fel som kan uppstå när en databas inte har tillräckligt med ström för att bearbeta begär Anden på ett effektivt sätt, särskilt i svars känsliga miljöer. Ett exempel är en databas som stöder ett program som målar webb sidor baserat på resultaten av databas anrop.

Andra program typer kan tolka samma graf på olika sätt. Om ett program till exempel försöker bearbeta löne data varje dag och har samma diagram, kan den här typen av "batch job"-modell fungera i en beräknings storlek på P1. Beräknings storleken P1 har 100 DTU: er jämfört med 200 DTU: er vid P2 Compute-storlek. Beräknings storleken P1 ger hälften av prestandan för P2 Compute-storlek. Därför är 50 procent av CPU-användningen i P2 lika med 100 procent CPU-användning i P1. Om programmet inte har några tids gränser kanske det inte spelar någon roll om ett jobb tar 2 timmar eller 2,5 timmar att slutföras, om det blir klart idag. Ett program i den här kategorin kan förmodligen använda en beräknings storlek i P1. Du kan dra nytta av det faktum att det finns tids perioder under dagen då resurs användningen är lägre, så att alla "stora toppar" kan spilla i en av troughs senare under dagen. Beräknings storleken P1 kan vara lämplig för den typen av program (och spara pengar), så länge som jobben kan slutföras i tid varje dag.

Databas motorn visar förbrukad resursinformation för varje aktiv databas i vyn **sys.resource_stats** i **huvud** databasen på varje server. Data i tabellen sammanställs i intervall om 5 minuter. På tjänst nivåerna Basic, standard och Premium kan data ta mer än 5 minuter visas i tabellen, så dessa data är mer användbara för historiska analyser i stället för i nära real tids analys. Fråga vyn **sys.resource_stats** om du vill se den senaste historiken för en databas och kontrol lera om den reservation du valde har levererat den prestanda du önskar när du behöver.

> [!NOTE]
> På Azure SQL Database måste du vara ansluten till **huvud** databasen för att fråga **sys.resource_stats** i följande exempel.

Det här exemplet visar hur data i den här vyn exponeras:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC;
```

![Vyn sys.resource_stats katalog](./media/monitoring-with-dmvs/sys_resource_stats.png)

I nästa exempel visas olika sätt som du kan använda vyn **sys.resource_stats** Catalog för att få information om hur databasen använder resurser:

1. Om du vill titta på den senaste vecko resurs användningen för databasen userdb1 kan du köra den här frågan:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Om du vill utvärdera hur bra arbets belastningen passar för beräknings storleken måste du öka detalj nivån för varje aspekt av resurs måtten: CPU, läsningar, skrivningar, antal arbetare och antal sessioner. Här är en reviderad fråga som använder **sys.resource_stats** för att rapportera genomsnitts-och max värden för dessa resurs mått:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. Med den här informationen om medelvärdet och Max värdet för varje resurs mått kan du utvärdera hur bra arbets belastningen passar in i den beräknade storlek du väljer. Vanligt vis ger genomsnitts värden från **sys.resource_stats** en lämplig bas linje som du kan använda mot mål storleken. Det bör vara ditt primära Mät märke. Till exempel kanske du använder standard tjänst nivån med S2-beräknings storlek. Genomsnittlig användning i procent för CPU-och IO-läsningar och skrivningar är under 40 procent, det genomsnittliga antalet arbetare är lägre än 50 och det genomsnittliga antalet sessioner under 200. Din arbets belastning kan få plats i S1-Compute-storlek. Det är enkelt att se om din databas passar för arbets-och sessionsgränser. Om du vill se om en databas passar i en lägre beräknings storlek med avseende på CPU, läsning och skrivning, dividerar du DTU-numret för den lägre beräknings storleken med DTU-numret för din aktuella beräknings storlek och multiplicerar sedan resultatet med 100:

    `S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40`

    Resultatet är den relativa prestanda skillnaden mellan de två beräknings storlekarna i procent. Om din resurs använder inte överskrider den här mängden kan din arbets belastning få plats i den lägre beräknings storleken. Men du måste titta på alla intervall med resurs användnings värden och fastställa i procent hur ofta databas arbets belastningen skulle få plats i den lägre beräknings storleken. Följande fråga utvärderar anpassnings procent per resurs dimension baserat på tröskelvärdet på 40 procent som vi beräknade i det här exemplet:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Baserat på databas tjänst nivån kan du bestämma om din arbets belastning passar i den lägre beräknings storleken. Om databasens arbets belastnings mål är 99,9 procent och föregående fråga returnerar värden som är större än 99,9 procent för alla tre resurs dimensionerna, kommer din arbets belastning antagligen att passa den lägre beräknings storleken.

    När du tittar på den här procent andelen får du också information om huruvida du bör flytta till nästa högre beräknings storlek för att uppfylla ditt mål. Till exempel visar userdb1 följande CPU-användning för den senaste veckan:

   | Genomsnittlig CPU-procent | Högsta processor procent |
   | --- | --- |
   | 24,5 |100,00 |

    Den genomsnittliga CPU: n är ungefär ett kvartal av gränsen för beräknings storlek, vilket skulle passa in i databasens beräknings storlek. Men det maximala värdet visar att databasen når gränsen för beräknings storleken. Behöver du flytta till nästa högre beräknings storlek? Titta på hur många gånger din arbets belastning når 100 procent och jämför den sedan med databasens arbets belastnings mål.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Om frågan returnerar ett värde som är mindre än 99,9 procent för någon av de tre resurs dimensionerna kan du antingen flytta till nästa högre beräknings storlek eller använda program justerings tekniker för att minska belastningen på databasen.

4. Den här övningen tar också hänsyn till din planerade arbets belastnings ökning i framtiden.

För elastiska pooler kan du övervaka individuella databaser i poolen med de tekniker som beskrivs i det här avsnittet. Men du kan också övervaka poolen som helhet. Mer information finns i [Övervaka och hantera en elastisk pool](elastic-pool-overview.md).

### <a name="maximum-concurrent-requests"></a>Maximalt antal samtidiga förfrågningar

Om du vill se antalet samtidiga förfrågningar kör du den här Transact-SQL-frågan på databasen:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R;
```

Om du vill analysera arbets belastningen för en SQL Server databas ändrar du den här frågan för att filtrera på den databas som du vill analysera. Om du till exempel har en lokal databas med namnet databas, returnerar denna Transact-SQL-fråga antalet samtidiga begär anden i databasen:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R
INNER JOIN sys.databases D ON D.database_id = R.database_id
AND D.name = 'MyDatabase';
```

Detta är bara en ögonblicks bild vid en enskild tidpunkt. För att få en bättre förståelse för arbets belastningen och krav för samtidiga begär Anden måste du samla in många prover över tid.

### <a name="maximum-concurrent-logins"></a>Maximalt antal samtidiga inloggningar

Du kan analysera dina användar-och program mönster för att få en uppfattning om inloggnings frekvensen. Du kan också köra verkliga belastningar i en test miljö för att se till att du inte träffar detta eller andra gränser vi diskuterar i den här artikeln. Det finns inte en enstaka fråga eller dynamisk hanterings vy (DMV) som kan visa dig antal samtidiga inloggningar eller historik.

Om flera klienter använder samma anslutnings sträng, autentiserar tjänsten varje inloggning. Om 10 användare samtidigt ansluter till en databas med samma användar namn och lösen ord, blir det 10 samtidiga inloggningar. Den här gränsen gäller endast för varaktigheten för inloggningen och autentiseringen. Om samma 10 användare ansluter till databasen sekventiellt, skulle antalet samtidiga inloggningar aldrig vara större än 1.

> [!NOTE]
> För närvarande gäller inte den här gränsen för databaser i elastiska pooler.

### <a name="maximum-sessions"></a>Maximalt antal sessioner

Om du vill se antalet aktuella aktiva sessioner kör du den här Transact-SQL-frågan på databasen:

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections;
```

Om du analyserar en SQL Server arbets belastning ändrar du frågan så att den fokuserar på en speciell databas. Den här frågan hjälper dig att fastställa möjliga sessions behov för databasen om du överväger att flytta den till Azure.

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections C
INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
INNER JOIN sys.databases D ON (D.database_id = S.database_id)
WHERE D.name = 'MyDatabase';
```

De här frågorna returnerar ett antal tidpunkter. Om du samlar flera prover över tid har du den bästa förståelsen av din sessions användning.

Du kan hämta historisk statistik för sessioner genom att fråga [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) och granska kolumnen **active_session_count** .

## <a name="monitoring-query-performance"></a>Övervaknings frågans prestanda

Långsamma eller tids krävande frågor kan använda stora system resurser. Det här avsnittet visar hur du använder vyer för dynamisk hantering för att identifiera några vanliga problem med frågans prestanda.

### <a name="finding-top-n-queries"></a>Hitta de N främsta frågorna

I följande exempel returneras information om de fem främsta frågorna som rangordnas med Genomsnittlig CPU-tid. I det här exemplet sammanställs frågorna utifrån deras fråge-hash, så att logiskt likvärdiga frågor grupperas efter deras ackumulerade resursförbrukning.

```sql
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
     MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
FROM sys.dm_exec_query_stats AS QS
CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Övervaka blockerade frågor

Långsamma eller långvariga frågor kan bidra till överdriven resurs förbrukning och vara en följd av blockerade frågor. Orsaken till blockeringen kan vara dåligt program design, dåliga fråge planer, avsaknad av användbara index och så vidare. Du kan använda vyn sys.dm_tran_locks för att hämta information om den aktuella lås aktiviteten i databasen. Exempel kod finns i [sys.dm_tran_locks (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql). Mer information om fel sökning av blockering finns i [förstå och lösa problem med att blockera Azure SQL](understand-resolve-blocking.md).

### <a name="monitoring-query-plans"></a>Övervaknings fråge planer

En ineffektiv frågeplan kan också öka CPU-förbrukningen. I följande exempel används vyn [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql) för att avgöra vilken fråga som använder den mest kumulativa processorn.

```sql
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Se även

[Introduktion till Azure SQL Database och Azure SQL-hanterad instans](sql-database-paas-overview.md)