---
title: 'Övervaka din dedikerade arbets belastning i SQL-poolen med DMV: er'
description: 'Lär dig hur du övervakar arbets belastningen för en dedikerad SQL-pool i Azure Synapse Analytics och kör frågor med hjälp av DMV: er.'
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/24/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: 62064eaae6aa7fb3438845170497035473227d30
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685216"
---
# <a name="monitor-your-azure-synapse-analytics-dedicated-sql-pool-workload-using-dmvs"></a>Övervaka arbets belastningen för Azure Synapse Analytics-dedikerad SQL-pool med DMV: er

Den här artikeln beskriver hur du använder DMV: er (Dynamic Management views) för att övervaka din arbets belastning, inklusive att undersöka frågekörningen i SQL-poolen.

## <a name="permissions"></a>Behörigheter

Om du vill fråga DMV: er i den här artikeln måste du antingen **Visa databas tillstånd** eller **kontroll** behörighet. Det är vanligt vis att beviljande **VISNINGS databas status** är den rekommenderade behörigheten eftersom det är mycket mer restriktivt.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Övervaka anslutningar

Alla inloggningar till data lagret loggas till [sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Denna DMV innehåller de senaste 10 000 inloggningarna.  Session_id är den primära nyckeln och tilldelas sekventiellt för varje ny inloggning.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Övervaka frågekörningen

Alla frågor som körs på SQL-poolen loggas till [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Denna DMV innehåller de senaste 10 000 frågorna som kördes.  Request_id identifierar unikt varje fråga och är den primära nyckeln för denna DMV.  Request_id tilldelas sekventiellt för varje ny fråga och föregås av QID, som står för fråge-ID.  Om du frågar denna DMV för en specifik session_id visas alla frågor för en specifik inloggning.

> [!NOTE]
> Lagrade procedurer använder flera ID: n för begäran.  ID för begäran tilldelas i sekventiell ordning.

Här följer några steg som du följer för att undersöka körnings planer och tids perioder för en viss fråga.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>STEG 1: identifiera den fråga som du vill undersöka

```sql
-- Monitor active queries
SELECT *
FROM sys.dm_pdw_exec_requests
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 *
FROM sys.dm_pdw_exec_requests
ORDER BY total_elapsed_time DESC;

```

I föregående frågeresultat **noterar du fråge-ID** för frågan som du vill undersöka.

Frågor i tillståndet **Suspended** kan placeras i kö på grund av ett stort antal aktiva körnings frågor. Dessa frågor visas också i [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) väntar på fråga med en typ av UserConcurrencyResourceType. Information om samtidiga gränser finns i [minnes-och samtidiga gränser](memory-concurrency-limits.md) eller [resurs klasser för hantering av arbets belastning](resource-classes-for-workload-management.md). Frågor kan också vänta på andra orsaker till exempel för objekt lås.  Om din fråga väntar på en resurs, se [undersöka frågor som väntar på resurser](#monitor-waiting-queries) ytterligare i den här artikeln.

För att förenkla sökningen av en fråga i [sys.dm_pdw_exec_requestss](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) tabellen använder du [etikett](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att tilldela en kommentar till frågan, som kan visas i vyn sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>STEG 2: Undersök frågeplan

Använd förfrågnings-ID: t för att hämta frågans distribuerade SQL-plan (DSQL) från [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

När en DSQL-plan tar längre tid än förväntat, kan orsaken vara en komplex plan med många DSQL-steg eller bara ett steg tar lång tid.  Om planen är många steg med flera flyttnings åtgärder bör du överväga att optimera tabell distributionerna för att minska data flytten. [Tabellen distributions](sql-data-warehouse-tables-distribute.md) artikel förklarar varför data måste flyttas för att lösa en fråga. Artikeln beskriver också några distributions strategier för att minimera data flytten.

Om du vill undersöka ytterligare information om ett enda steg, *operation_type* kolumnen i steget för långvarig fråga och notera **steg indexet**:

* Fortsätt med steg 3 för **SQL-åtgärder**: OnOperation, RemoteOperation, ReturnOperation.
* Fortsätt med steg 4 för **data förflyttnings åtgärder**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>STEG 3: Undersök SQL på de distribuerade databaserna

Använd förfrågnings-ID och steg index för att hämta information från [sys.dm_pdw_sql_requests](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), som innehåller körnings information för steget fråga på alla distribuerade databaser.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

När steget körs kan [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) användas för att hämta den SQL Server beräknade planen från SQL Server plan-cachen för det steg som körs på en viss distribution.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>STEG 4: Undersök data förflyttning på de distribuerade databaserna

Använd förfrågnings-ID och steg index för att hämta information om ett steg för data förflyttning som körs på varje distribution från [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Markera kolumnen *total_elapsed_time* om du vill se om en viss distribution tar betydligt längre tid än andra för data förflyttning.
* För den tids krävande distributionen kontrollerar du kolumnen *rows_processed* för att se om antalet rader som flyttas från den distributionen är betydligt större än andra. I så fall kan det hända att den här sökningen visar skevning av underliggande data. En orsak till datasnedingen är att distribueras i en kolumn med många NULL-värden (vars rader kommer att alla hamnar i samma fördelning). Förhindra långsamma frågor genom att undvika distribution på dessa typer av kolumner eller filtrera din fråga för att eliminera NULL-värden när det är möjligt. 

Om frågan körs kan du använda [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att hämta den SQL Server beräknade planen från SQL Server plan-cachen för det SQL-steg som körs för närvarande i en viss distribution.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Övervaka väntande frågor

Om du upptäcker att din fråga inte gör något, eftersom den väntar på en resurs, är här en fråga som visar alla resurser som en fråga väntar på.

```sql
-- Find queries
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Om frågan väntar på resurser från en annan fråga blir statusen **AcquireResources**.  Om frågan har alla nödvändiga resurser kommer statusen att **beviljas**.

## <a name="monitor-tempdb"></a>Övervaka tempdb

TempDB används för att lagra mellanliggande resultat under frågekörningen. Hög användning av tempdb-databasen kan leda till långsam frågans prestanda. För varje DW100c som kon figurer ATS tilldelas 399 GB tempdb-utrymme (DW1000c skulle ha 3,99 TB av det totala tempdb-utrymmet).  Nedan visas tips för att övervaka tempdb-användning och för att minska tempdb-användning i dina frågor.

### <a name="monitoring-tempdb-with-views"></a>Övervaka tempdb med vyer

Om du vill övervaka tempdb-användningen installerar du först vyn [Microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) från [Microsoft Toolkit för SQL-poolen](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring). Du kan sedan köra följande fråga för att se tempdb-användning per nod för alla utförda frågor:

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa'
ORDER BY sr.request_id;
```

Om du har en fråga som förbrukar en stor mängd minne eller har fått ett fel meddelande som rör allokering av tempdb, kan det bero på en mycket stor [CREATE TABLE som Select (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) eller [insert Select](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) -instruktionen som körs och som inte fungerar i den slutliga data flytt åtgärden. Detta kan vanligt vis identifieras som en ShuffleMove-åtgärd i den distribuerade fråge planen direkt före den slutliga INFOGNINGen.  Använd [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att övervaka ShuffleMove-åtgärder.

Den vanligaste lösningen är att bryta din CTAS eller infoga SELECT-instruktion i flera load-instruktioner så att data volymen inte överskrider tempdb-gränsen på 1 TB per nod. Du kan också skala klustret till en större storlek som kommer att sprida tempdb-storleken mellan fler noder som minskar tempdb på varje enskild nod.

Förutom CTAS och INSERT SELECT-instruktioner, kan stora, komplexa frågor som körs med otillräckligt minne spill i tempdb som orsakar att frågor inte kan köras.  Överväg att köra med en större [resurs klass](resource-classes-for-workload-management.md) för att undvika spill i tempdb.

## <a name="monitor-memory"></a>Övervaka minne

Minnet kan vara rotor saken för långsam prestanda och minnes problem. Överväg att skala ditt informations lager om du upptäcker SQL Server minnes användningen når gränserna under frågekörningen.

Följande fråga returnerar SQL Server minnes användning och minnes belastning per nod:

```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB,
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```

## <a name="monitor-transaction-log-size"></a>Övervaka transaktions logg storlek

Följande fråga returnerar transaktions logg storleken för varje distribution. Om en av loggfilerna når 160 GB bör du överväga att skala upp instansen eller begränsa transaktions storleken.

```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id
FROM sys.dm_pdw_nodes_os_performance_counters
WHERE
instance_name like 'Distribution_%'
AND counter_name = 'Log File(s) Used Size (KB)'
```

## <a name="monitor-transaction-log-rollback"></a>Övervaka återställning av transaktions logg

Om dina frågor inte fungerar eller tar lång tid att gå vidare kan du kontrol lera och övervaka om du har några transaktioner att återställa.

```sql
-- Monitor rollback
SELECT
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="monitor-polybase-load"></a>Övervaka PolyBase-belastning

Följande fråga ger en ungefärlig uppskattning av förloppet för inläsningen. Frågan visar bara filer som bearbetas just nu.

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>Nästa steg

Mer information om DMV: er finns i [systemvyer](../sql/reference-tsql-system-views.md).
