---
title: 'Snabbstart: Skapa en arbetsbelastningsklassare – portalen'
description: Använd Azure Portal för att skapa en arbetsbelastningsklassare med hög prioritet.
services: synapse-analytics
author: ronortloff
ms.author: rortloff
manager: craigg
ms.reviewer: jrasnick
ms.date: 05/04/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- azure-synapse
- mode-portal
ms.openlocfilehash: 6f1997a80e66d1ca7928afc02acf92f376fc3376
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534274"
---
# <a name="quickstart-create-a-dedicated-sql-pool-workload-classifier-using-the-azure-portal"></a>Snabbstart: Skapa en dedikerad arbetsbelastningsklassare för SQL-pool med hjälp av Azure Portal

I den här snabbstarten skapar du en [arbetsbelastningsklassare](sql-data-warehouse-workload-classification.md) för att tilldela frågor till en arbetsbelastningsgrupp.  Klassificeraren tilldelar begäranden från `ELTLogin` SQL-användaren till `DataLoads` arbetsbelastningsgruppen.   Följ [självstudien Snabbstart: Konfigurera arbetsbelastningsisolering](quickstart-configure-workload-isolation-portal.md) för att skapa `DataLoads` arbetsbelastningsgruppen.  Den här självstudien skapar en arbetsbelastnings klassificerare med WLM_LABEL för att ytterligare klassificera begäranden korrekt.  Klassificeraren tilldelar även `HIGH` [arbetsbelastnings](sql-data-warehouse-workload-importance.md) prioritet till dessa begäranden.


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.


## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

> [!NOTE]
> Att skapa en dedikerad SQL-poolinstans Azure Synapse Analytics kan resultera i en ny fakturerbar tjänst.  Mer information finns i [Azure Synapse Analytics prissättning.](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)

## <a name="prerequisites"></a>Förutsättningar

Den här snabbstarten förutsätter att du redan har en dedikerad SQL-poolinstans som du har behörighet för CONTROL DATABASE. Om du behöver skapa en använder du Skapa och [anslut – portalen](create-data-warehouse-portal.md) för att skapa en dedikerad SQL-pool med namnet **mySampleDataWarehouse.**
<br><br>
Det finns en `DataLoads` arbetsbelastningsgrupp.  Se [självstudien Snabbstart: Konfigurera arbetsbelastningsisolering](quickstart-configure-workload-isolation-portal.md) för att skapa arbetsbelastningsgruppen.
<br><br>
>[!IMPORTANT] 
>Din dedikerade SQL-pool måste vara online för att konfigurera arbetsbelastningshantering. 


## <a name="create-a-login-for-eltlogin"></a>Skapa en inloggning för ELTLogin

Skapa en SQL Server inloggning i databasen `master` med CREATE [LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för `ELTLogin` .

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user-and-grant-permissions"></a>Skapa användare och bevilja behörigheter

När inloggningen har skapats måste en användare skapas i databasen.  Använd [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att skapa SQL-användaren i `ELTRole` **mySampleDataWarehouse**.  Eftersom vi ska testa klassificeringen under den här självstudien beviljar `ELTLogin` du behörigheter **till mySampleDataWarehouse**. 

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
GRANT CONTROL ON DATABASE::mySampleDataWarehouse TO ELTLogin 
END
;
```

## <a name="configure-workload-classification"></a>Konfigurera arbetsbelastningsklassificering
Med klassificering kan du dirigera begäranden, baserat på en uppsättning regler, till en arbetsbelastningsgrupp.  I [självstudien Snabbstart: Konfigurera arbetsbelastningsisolering](quickstart-configure-workload-isolation-portal.md) skapade vi `DataLoads` arbetsbelastningsgruppen.  Nu ska du skapa en arbetsbelastnings klassificerare för att dirigera frågor till `DataLoads` arbetsbelastningsgruppen.


1.  Gå till sidan **mySampleDataWarehouse dedikerad** SQL-pool.
3.  Välj **Arbetsbelastningshantering.**

    ![Klicka på Meny](./media/quickstart-create-a-workload-classifier-portal/menu.png)

4.  Välj **Inställningar & klassificerare** till höger i `DataLoads` arbetsbelastningsgruppen.

    ![Klicka på Skapa](./media/quickstart-create-a-workload-classifier-portal/settings-classifiers.png)

5. Välj  **Inte konfigurerad** under kolumnen Klassificerare.
6. Välj **+ Lägg till klassificerare.**

    ![Klicka på Lägg till](./media/quickstart-create-a-workload-classifier-portal/add-wc.png)

7.  Ange `ELTLoginDataLoads` som **Namn.**
8.  Ange `ELTLogin` för **Medlem**.
9.  Välj `High` för **Begärande prioritet.**  *Valfri*, normal prioritet är standard.
10. Ange `fact_loads` för **Etikett.**
11. Välj **Lägg till**.
12. Välj **Spara**.

    ![Klicka på Konfiguration](./media/quickstart-create-a-workload-classifier-portal/config-wc.png)

## <a name="verify-and-test-classification"></a>Verifiera och testa klassificering
Kontrollera [sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql?view=azure-sqldw-latest&preserve-view=true) katalogvyn för att verifiera att `ELTLoginDataLoads` klassificeraren finns.

```sql
SELECT * FROM sys.workload_management_workload_classifiers WHERE name = 'ELTLoginDataLoads'
```

Kontrollera [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest&preserve-view=true) katalogvyn för att verifiera klassificerarinformationen.

```sql
SELECT c.[name], c.group_name, c.importance, cd.classifier_type, cd.classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ELTLoginDataLoads'
```

Kör följande instruktioner för att testa klassificering.  Se till att du är ``ELTLogin`` ansluten som och används i ``Label`` frågan.
```sql
CREATE TABLE factstaging (ColA int)
INSERT INTO factstaging VALUES(0)
INSERT INTO factstaging VALUES(1)
INSERT INTO factstaging VALUES(2)
GO

CREATE TABLE testclassifierfact WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT * FROM factstaging
OPTION (LABEL='fact_loads')
```

Kontrollera `CREATE TABLE` instruktionen som klassificerats till `DataLoads` arbetsbelastningsgruppen med hjälp `ELTLoginDataLoads` av arbetsbelastningsklassificeraren.
```sql 
SELECT TOP 1 request_id, classifier_name, group_name, resource_allocation_percentage, submit_time, [status], [label], command 
FROM sys.dm_pdw_exec_requests 
WHERE [label] = 'fact_loads'
ORDER BY submit_time DESC
```

## <a name="clean-up-resources"></a>Rensa resurser

Så här tar du bort `ELTLoginDataLoads` arbetsbelastnings klassificeraren som skapades i den här självstudien:

1. Klicka på **1 klassificerare** till höger i `DataLoads` arbetsbelastningsgruppen.

    ![Klicka på Ta bort](./media/quickstart-create-a-workload-classifier-portal/delete-wc.png)

2. Klicka på **Klassificerare.**
3. Klicka på till **`...`** höger om `ELTLoginDataLoads` arbetsbelastnings klassificeraren.
4. Klicka på Ta **bort.**
5. Klicka på **Spara.**

    ![Klicka på Spara](./media/quickstart-create-a-workload-classifier-portal/delete-save-wc.png)

Du debiteras för informationslagerenheter och data som lagras i din dedikerade SQL-pool. Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagringen kan du pausa beräkningen när du inte använder din dedikerade SQL-pool. Genom att pausa beräkningen debiteras du bara för datalagring. När du är redo att arbeta med data återupptar du beräkningen.
- Om du vill ta bort framtida avgifter kan du ta bort din dedikerade SQL-pool.

Följ dessa steg för att rensa resurser.

1. Logga in på [Azure Portal](https://portal.azure.com), välj din dedikerade SQL-pool.

    ![Rensa resurser](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Om du vill pausa beräkningen väljer du **knappen Pausa.** När den dedikerade SQL-poolen har pausats visas knappen **Start.**  Om du vill återuppta beräkningen väljer du **Starta**.

3. Om du vill ta bort din dedikerade SQL-pool så att du inte debiteras för beräkning eller lagring väljer du **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Övervaka din arbetsbelastning med hjälp Azure Portal mått för övervakning.  Mer [information finns i Hantera och övervaka arbetsbelastningshantering.](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
