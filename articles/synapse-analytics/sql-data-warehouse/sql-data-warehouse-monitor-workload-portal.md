---
title: Övervaka arbets belastning – Azure Portal
description: Övervaka Synapse SQL med Azure Portal
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 3334c5ca300b6dbeb0ad055c7dbd2c15e1c5481a
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677129"
---
# <a name="monitor-workload---azure-portal"></a>Övervaka arbets belastning – Azure Portal

Den här artikeln beskriver hur du använder Azure Portal för att övervaka arbets belastningen. Detta innefattar att ställa in Azure Monitor loggar för att undersöka frågor om körning och arbets belastnings trender med Log Analytics för [SYNAPSE SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
- SQL-pool: vi kommer att samla in loggar för en SQL-pool. Om du inte har en SQL-pool etablerad kan du läsa anvisningarna i [skapa en SQL-pool](./load-data-from-azure-blob-storage-using-copy.md).

## <a name="create-a-log-analytics-workspace"></a>Skapa en Log Analytics-arbetsyta

Navigera till bladet Bläddra för Log Analytics arbets ytor och skapa en arbets yta

![Log Analytics-arbetsytor](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Skärm bild som visar Log Analytics arbets ytor där du kan välja Lägg till.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Skärm bild som visar Log Analytics arbets ytan där du kan ange värden.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Mer information om arbets ytor finns i följande [dokumentation](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace).

## <a name="turn-on-resource-logs"></a>Aktivera resurs loggar

Konfigurera diagnostikinställningar för att generera loggar från SQL-poolen. Loggar består av telemetri-vyer som motsvarar de vanligaste prestanda fel söknings DMV: er. För närvarande stöds följande vyer:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

![Aktivera resurs loggar](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Loggar kan genereras till Azure Storage, Stream Analytics eller Log Analytics. I den här självstudien väljer du Log Analytics.

![Ange loggar](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Köra frågor mot Log Analytics

Navigera till din Log Analytics arbets yta där du kan göra följande:

- Analysera loggar med hjälp av logg frågor och spara frågor för åter användning
- Spara frågor för åter användning
- Skapa loggaviseringar
- Fäst frågeresultat på en instrument panel

Mer information om funktionerna i logg frågor finns i följande [dokumentation](/azure/data-explorer/kusto/query/?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json).

![Redigerare för Log Analytics arbets yta](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Frågor om Log Analytics-arbetsyta](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Exempel på logg frågor

```Kusto
//List all queries
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```

```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart
```

```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits"
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat och konfigurerat Azure Monitor-loggar, [anpassar du Azure-instrumentpaneler](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) så att de delas i hela teamet.