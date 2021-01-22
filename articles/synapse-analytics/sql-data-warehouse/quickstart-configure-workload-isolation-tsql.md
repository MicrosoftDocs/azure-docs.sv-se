---
title: 'Snabb start: Konfigurera arbets belastnings isolering-T-SQL'
description: Använd T-SQL för att konfigurera arbets belastnings isolering.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/27/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9d69c1708e73ad7ce5610a0683835e9f304c3f54
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679762"
---
# <a name="quickstart-configure-workload-isolation-in-a-dedicated-sql-pool-using-t-sql"></a>Snabb start: Konfigurera arbets belastnings isolering i en dedikerad SQL-pool med hjälp av T-SQL

I den här snabb starten skapar du snabbt en arbets belastnings grupp och en klassificerare för att reservera resurser för data inläsning. Arbets belastnings gruppen kommer att allokera 20% av system resurserna till data belastningarna.  Klassificeringen av arbets belastningen tilldelar förfrågningar till arbets belastnings gruppen för data inläsningar.  Med 20% isolering för data inläsningar är de garanterat resurser för att nå service avtal.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!NOTE]
> Att skapa en Synapse SQL-instans i Azure Synapse Analytics kan resultera i en ny fakturerbar tjänst.  Mer information finns i [priser för Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Förutsättningar

Den här snabb starten förutsätter att du redan har en Synapse SQL-instans i Azure Synapse och att du har behörighet att kontrol lera databasen. Om du behöver skapa ett använder du [skapa och Anslut – Portal](create-data-warehouse-portal.md) för att skapa en dedikerad SQL-pool med namnet **mySampleDataWarehouse**.

## <a name="create-login-for-dataloads"></a>Skapa inloggning för DataLoads

Skapa en inloggning för SQL Server autentisering i `master` databasen med hjälp av [create login](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) for ' ELTLogin'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Skapa användare

[Skapa användare](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), "ELTLogin", i mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Skapa en arbets belastnings grupp

Skapa en [arbets belastnings grupp](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för DataLoads med 20% isolering.

```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5)
;
```

## <a name="create-a-workload-classifier"></a>Skapa en klassificering för arbets belastning

Skapa en [arbets belastnings klassificerare](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att mappa ELTLogin till arbets belastnings gruppen DataLoads.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Visa befintliga arbets belastnings grupper och klassificerare och kör tids värden

```sql
--Workload groups
SELECT * FROM
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>Rensa resurser

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Du debiteras för data lager enheter och data som lagras i din dedikerade SQL-pool. Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagrings utrymmet kan du pausa beräkningen när du inte använder den dedikerade SQL-poolen. Genom att pausa beräkning debiteras du bara för data lagring. När du är redo att arbeta med data återupptar du beräkningen.
- Om du vill ta bort framtida avgifter kan du ta bort den dedikerade SQL-poolen.

## <a name="next-steps"></a>Nästa steg

- Nu har du skapat en arbets belastnings grupp. Kör några frågor som ELTLogin för att se hur de fungerar. Se [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att visa frågor och arbets belastnings gruppen som tilldelats.
- Mer information om Synapse SQL-arbetsbelastnings hantering finns i hantering av [arbets belastning](sql-data-warehouse-workload-management.md) och [arbets belastnings isolering](sql-data-warehouse-workload-isolation.md).
