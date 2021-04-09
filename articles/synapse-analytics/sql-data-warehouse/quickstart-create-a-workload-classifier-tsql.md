---
title: 'Snabb start: skapa en arbets belastnings klassificering-T-SQL'
description: Använd T-SQL för att skapa en klassificering för arbets belastning med hög prioritet.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: e757c8047bf6d634ab6d7cbc8963087c0eccc46a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98677376"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Snabb start: skapa en arbets belastnings klassificering med T-SQL

I den här snabb starten skapar du snabbt en arbets belastnings klassificering med stor betydelse för organisationens VD. Den här arbets belastnings klassificeraren gör det möjligt för VD-frågor att prioriteras över andra frågor med lägre prioritet i kön.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!NOTE]
> Att skapa en dedikerad instans av SQL-poolen i Azure Synapse Analytics kan resultera i en ny fakturerbar tjänst.  Mer information finns i [priser för Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Förutsättningar

Den här snabb starten förutsätter att du redan har skapat en dedikerad SQL-pool i Azure Synapse Analytics och att du har behörighet att kontrol lera databasen. Om du behöver skapa ett använder du [skapa och Anslut – Portal](create-data-warehouse-portal.md) för att skapa en dedikerad SQL-pool med namnet **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Skapa inloggning för TheCEO

Skapa en inloggning för SQL Server autentisering i `master` databasen med hjälp av [create login](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) for ' TheCEO '.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Skapa användare

[Skapa användare](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), "TheCEO", i mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Skapa en klassificering för arbets belastning

Skapa en [arbets belastnings klassificering](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för "TheCEO" med hög prioritet.

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Visa befintliga klassificerare

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Rensa resurser

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Du debiteras för data lager enheter och data som lagras i din dedikerade SQL-pool. Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagrings utrymmet kan du pausa beräkningen när du inte använder den dedikerade SQL-poolen. Genom att pausa beräkning debiteras du bara för data lagring. När du är redo att arbeta med data återupptar du beräkningen.
- Om du vill ta bort framtida avgifter kan du ta bort den dedikerade SQL-poolen.

Rensa resurserna genom att följa dessa steg.

1. Logga in på [Azure Portal](https://portal.azure.com)och välj din dedikerade SQL-pool.

    ![Rensa resurser](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Om du vill pausa beräkningen väljer du knappen **pausa** . När den dedikerade SQL-poolen har pausats visas en **Start** -knapp.  Om du vill återuppta beräkningen väljer du **Start**.

3. Om du vill ta bort den dedikerade SQL-poolen så att du inte debiteras för beräkning eller lagring väljer du **ta bort**.

## <a name="next-steps"></a>Nästa steg

- Du har nu skapat en arbets belastnings klassificering. Kör några frågor som TheCEO för att se hur de fungerar. Se [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att visa frågor och tilldelade prioritet.
- Mer information om dedikerad arbets belastnings hantering i SQL-poolen finns i avsnittet om [arbets belastnings prioritet](sql-data-warehouse-workload-importance.md) och [belastnings klassificering](sql-data-warehouse-workload-classification.md).
- Se instruktions artiklar för att [Konfigurera arbets belastnings prioritet](sql-data-warehouse-how-to-configure-workload-importance.md) och hur du [hanterar och övervakar arbets belastnings hantering](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
