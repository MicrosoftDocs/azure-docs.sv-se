---
title: Analysera din arbets belastning för dedikerad SQL-pool
description: Tekniker för att analysera frågans prioritering för dedikerad SQL-pool i Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 14c3ad30bac7cec4c11822d825323bb9db2ba440
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96454532"
---
# <a name="analyze-your-workload-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Analysera din arbets belastning för dedikerad SQL-pool i Azure Synapse Analytics

Tekniker för att analysera din dedikerade arbets belastning i SQL-poolen i Azure Synapse Analytics. 

## <a name="resource-classes"></a>Resursklasser

Dedikerad SQL-pool tillhandahåller resurs klasser för att tilldela system resurser till frågor.  Mer information om resurs klasser finns i [resurs klasser & hantering av arbets belastning](resource-classes-for-workload-management.md).  Frågor väntar om resurs klassen som tilldelas till en fråga behöver fler resurser än vad som för närvarande är tillgängligt.

## <a name="queued-query-detection-and-other-dmvs"></a>Avkänning av köade frågor och andra DMV: er

Du kan använda `sys.dm_pdw_exec_requests` DMV för att identifiera frågor som väntar i en concurrency-kö. Frågor som väntar på en concurrency-kortplats har statusen **inaktive** rad.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

Roller för arbets belastnings hantering kan visas med `sys.database_principals` .

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Följande fråga visar vilken roll som varje användare är tilldelad till.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

Dedikerad SQL-pool har följande vänte typer:

* **LocalQueriesConcurrencyResourceType**: frågor som ligger utanför samtidighets fack ramverket. DMV-frågor och system funktioner som `SELECT @@VERSION` är exempel på lokala frågor.
* **UserConcurrencyResourceType**: frågor som är i ramverket för samtidighets fack. Frågor mot slut användar tabeller representerar exempel som använder den här resurs typen.
* **DmsConcurrencyResourceType**: väntar på data förflyttnings åtgärder.
* **BackupConcurrencyResourceType**: det här väntar indikerar att en databas säkerhets kopie ras. Det maximala värdet för den här resurs typen är 1. Om flera säkerhets kopieringar har begärts samtidigt, den andra kön. I allmänhet rekommenderar vi att du tar en stund mellan flera ögonblicks bilder på 10 minuter.

`sys.dm_pdw_waits`DMV kan användas för att se vilka resurser en begäran väntar på.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

`sys.dm_pdw_resource_waits`DMV visar wait-information för en specifik fråga. Resursens vänte tid mäter den tid som väntar på att resurser ska tillhandahållas. Vänte tiden för signalen är den tid det tar för de underliggande SQL-servrarna att schemalägga frågan till processorn.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```

Du kan också använda `sys.dm_pdw_resource_waits` DMV-beräkningen av hur många samtidiga samtidiga platser som har beviljats.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

`sys.dm_pdw_wait_stats`DMV kan användas för historisk trend analys av väntande.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar databas användare och säkerhet finns i [skydda en dedikerad SQL-pool (tidigare SQL DW)](sql-data-warehouse-overview-manage-security.md). Mer information om hur större resurs klasser kan förbättra grupperade columnstore-index finns i [Återskapa index för att förbättra segment kvaliteten](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).
