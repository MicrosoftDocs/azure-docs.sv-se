---
title: Övervaka arbetsbelastning – Azure Portal
description: Övervaka Synapse SQL med hjälp av Azure Portal
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 4f4c50588a67e2e69d0975c9f4414242ecf23617
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568275"
---
# <a name="monitor-workload---azure-portal"></a>Övervaka arbetsbelastning – Azure Portal

I den här artikeln beskrivs hur du använder Azure Portal för att övervaka din arbetsbelastning. Detta omfattar att konfigurera Azure Monitor loggar för att undersöka frågekörnings- och arbetsbelastningstrender med hjälp av Log Analytics [för Synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- SQL-pool: Vi samlar in loggar för en SQL-pool. Om du inte har en SQL-pool etablerad kan du läsa anvisningarna i [Skapa en SQL-pool.](./load-data-from-azure-blob-storage-using-copy.md)

## <a name="create-a-log-analytics-workspace"></a>Skapa en Log Analytics-arbetsyta

Gå till bläddringsbladet för Log Analytics-arbetsytor och skapa en arbetsyta

![Log Analytics-arbetsytor](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Skärmbild som visar Log Analytics-arbetsytor där du kan välja Lägg till.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Skärmbild som visar Log Analytics-arbetsytan där du kan ange värden.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Mer information om arbetsytor finns i följande [dokumentation.](../../azure-monitor/logs/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)

## <a name="turn-on-resource-logs"></a>Aktivera resursloggar

Konfigurera diagnostikinställningar för att generera loggar från SQL-poolen. Loggar består av telemetrivyer som motsvarar de vanligaste DMV:erna för prestandafelsökning. För närvarande stöds följande vyer:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

![Aktivera resursloggar](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Loggar kan skickas till Azure Storage, Stream Analytics eller Log Analytics. För den här självstudien väljer du Log Analytics.

![Ange loggar](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Köra frågor mot Log Analytics

Gå till Log Analytics-arbetsytan där du kan göra följande:

- Analysera loggar med hjälp av loggfrågor och spara frågor för återanvändning
- Spara frågor för återanvändning
- Skapa loggaviseringar
- Fästa frågeresultat på en instrumentpanel

Mer information om funktionerna i loggfrågor finns i följande [dokumentation.](/azure/data-explorer/kusto/query/?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json)

![Redigeringsprogram för Log Analytics-arbetsyta](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Frågor om Log Analytics-arbetsyta](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Exempel på loggfrågor

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

Nu när du har konfigurerat Azure Monitor-loggar kan du anpassa [Azure-instrumentpaneler så](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) att de kan dela i hela teamet.