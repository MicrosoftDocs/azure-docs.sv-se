---
title: Hantera och övervaka arbets belastnings prioritet i dedikerad SQL-pool
description: Lär dig hur du hanterar och övervakar den dedikerade SQL-poolen för förfrågnings nivå för Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 12b75ad3746cd0f54e27e474e0fd13bb0bba0e05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685250"
---
# <a name="manage-and-monitor-workload-importance-in-dedicated-sql-pool-for-azure-synapse-analytics"></a>Hantera och övervaka arbets belastnings prioritet i dedikerad SQL-pool för Azure Synapse Analytics

Hantera och övervaka dedikerad SQL-pool begär nivå prioritet i Azure Synapse med hjälp av DMV: er och katalogfiler.

## <a name="monitor-importance"></a>Övervaknings prioritet

Övervaka prioritet med kolumnen ny prioritet i vyn [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) dynamisk hantering.
Övervaknings frågan nedan visar sändnings tid och start tid för frågor. Granska sändnings tiden och start tiden tillsammans med viktig information för att se hur viktigt det är att schemalägga.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Använd katalogvyer för att se mer i hur frågor schemaläggs.

## <a name="manage-importance-with-catalog-views"></a>Hantera prioritet med katalogvyer

Vyn sys.workload_management_workload_classifiers katalog innehåller information om klassificerare. Om du vill utesluta systemdefinierade klassificerare som mappar till resurs klasser kör du följande kod:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

I vyn katalog [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)finns information om de parametrar som används för att skapa klassificeraren.  Frågan nedan visar att ExecReportsClassifier skapades i ```membername``` parametern för värden med ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![frågeresultat](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

För att under lätta fel sökningen av fel klassificering rekommenderar vi att du tar bort mappningar av resurs klass roller när du skapar arbets belastnings klassificerare. Koden nedan returnerar befintliga roll medlemskap i resurs klass. Kör sp_droprolemember för varje ```membername``` returnerad från motsvarande resurs klass.
Nedan visas ett exempel på att söka efter förekomster innan du släpper en arbets belastnings klassificering:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Nästa steg

- Mer information om klassificering finns i avsnittet om [arbets belastnings klassificering](sql-data-warehouse-workload-classification.md).
- Mer information om prioritet finns i [arbets belastnings prioritet](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Gå till konfigurera arbets belastnings prioritet](sql-data-warehouse-how-to-configure-workload-importance.md)
