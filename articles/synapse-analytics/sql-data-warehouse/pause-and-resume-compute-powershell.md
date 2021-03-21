---
title: 'Snabb start: pausa och återuppta beräkning i dedikerad SQL-pool (tidigare SQL DW) med Azure PowerShell'
description: Du kan använda Azure PowerShell för att pausa och återuppta en dedikerad SQL-pool (tidigare SQL DW). beräknings resurser.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 03/20/2019
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse, devx-track-azurepowershell
ms.openlocfilehash: 74c30a843ef5edd54c7cd19f3fd49acfe782f488
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602187"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Snabb start: pausa och återuppta beräkning i dedikerad SQL-pool (tidigare SQL DW) med Azure PowerShell

Du kan använda Azure PowerShell för att pausa och återuppta dedikerad SQL-pool (tidigare SQL DW) Compute-resurser.
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Den här snabb starten förutsätter att du redan har en särskild SQL-pool (tidigare SQL DW) som du kan pausa och återuppta. Om du behöver skapa en, kan du använda [skapa och ansluta – Portal](create-data-warehouse-portal.md) för att skapa en dedikerad SQL-pool (tidigare SQL DW) som kallas **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

Om du vill se vilken prenumeration du använder kör du [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Om du behöver använda en annan prenumeration än standardinställningen kör du [set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-dedicated-sql-pool-formerly-sql-dw-information"></a>Leta upp dedikerad SQL-pool (tidigare SQL DW) information

Leta upp databas namnet, Server namnet och resurs gruppen för den dedikerade SQL-poolen (tidigare SQL DW) som du planerar att pausa och återuppta.

Följ de här stegen för att hitta plats information för din dedikerade SQL-pool (tidigare SQL DW):

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Klicka på **Azure Synapse Analytics (tidigare SQL DW)** på den vänstra sidan i Azure Portal.
1. Välj **mySampleDataWarehouse** på sidan **Azure Synapse Analytics (tidigare SQL DW)** . SQL-poolen öppnas.

    ![Servernamn och resursgrupp](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Skriv ner den dedikerade SQL-poolen (tidigare SQL DW), som är databas namnet. Anteckna även servernamnet och resursgruppen.
1. Använd endast den första delen av Server namnet i PowerShell-cmdletar. I föregående bild är det fullständiga Server namnet sqlpoolservername.database.windows.net. Vi använder **sqlpoolservername** som server namn i PowerShell-cmdleten.

## <a name="pause-compute"></a>Pausa beräkning

För att spara kostnader kan du pausa och återuppta beräknings resurser på begäran. Om du till exempel inte använder databasen under natten och på helger, kan du pausa den under dessa tider och återuppta den under dagen.

>[!NOTE]
>Det kostar inget att beräkna resurser när databasen har pausats. Men du kan fortsätta att debiteras för lagring.

Använd cmdleten [suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) om du vill pausa en databas. I följande exempel pausas en SQL-pool med namnet **mySampleDataWarehouse** som finns på en server med namnet **sqlpoolservername**. Servern finns i en Azure-resurs grupp med namnet **myResourceGroup**.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

I följande exempel hämtas databasen till $database-objektet. Sedan rör det objektet för att [pausa-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Resultaten lagras i objektet resultDatabase. Det slutliga kommandot visar resultatet.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Återuppta beräkning

Använd cmdleten [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) för att starta en databas. I följande exempel startas en databas med namnet **mySampleDataWarehouse** som finns på en server med namnet **sqlpoolservername**. Servern finns i en Azure-resurs grupp med namnet **myResourceGroup**.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

I nästa exempel hämtas databasen till $database-objektet. Sedan rör det objektet för att [återuppta-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) och lagrar resultaten i $resultDatabase. Det slutliga kommandot visar resultatet.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>Kontrol lera status för SQL-programåtgärden

Använd cmdleten [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/Get-AzSqlDatabaseActivity?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) för att kontrol lera status för den DEDIKERADe SQL-poolen (tidigare SQL DW).

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för data lager enheter och data som lagras i din dedikerade SQL-pool (tidigare SQL DW). Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagringen ska du pausa beräkningen.
- Om du vill ta bort framtida avgifter kan du ta bort SQL-poolen.

Följ dessa steg för att rensa resurser enligt dina önskemål.

1. Logga in på [Azure Portal](https://portal.azure.com)och klicka på SQL-poolen.

    ![Rensa resurser](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Om du vill pausa beräkningarna klickar du på knappen **Pausa**. När SQL-poolen har pausats visas en **Start** -knapp.  Klicka på **Starta** om du vill återuppta beräkningarna.

3. Om du vill ta bort SQL-poolen så att du inte debiteras för beräkning eller lagring klickar du på **ta bort**.

4. Om du vill ta bort den SQL-Server som du har skapat klickar du på **sqlpoolservername.Database.Windows.net** och sedan på **ta bort**.  Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

5. Om du vill ta bort resursgruppen klickar du på **myResourceGroup** och sedan på **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om SQL-poolen fortsätter du till artikeln [Läs in data i dedikerad SQL-pool (tidigare SQL DW)](./load-data-from-azure-blob-storage-using-copy.md) . Mer information om hur du hanterar Compute-funktioner finns i artikeln [hantera beräknings översikt](sql-data-warehouse-manage-compute-overview.md) .