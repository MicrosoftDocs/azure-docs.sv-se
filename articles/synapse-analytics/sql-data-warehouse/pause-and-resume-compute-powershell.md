---
title: 'Snabbstart: Pausa och återuppta beräkning i dedikerad SQL-pool (tidigare SQL DW) med Azure PowerShell'
description: Du kan använda Azure PowerShell för att pausa och återuppta dedikerad SQL-pool (tidigare SQL DW). beräkningsresurser.
services: synapse-analytics
author: gaursa
ms.author: gaursa
manager: craigg
ms.reviewer: igorstan
ms.date: 03/20/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: b204132a49a8790b35cc99af8eebf465fd90f041
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536372"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Snabbstart: Pausa och återuppta beräkning i dedikerad SQL-pool (tidigare SQL DW) med Azure PowerShell

Du kan använda Azure PowerShell för att pausa och återuppta dedikerade beräkningsresurser för SQL-pooler (tidigare SQL DW).
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Den här snabbstarten förutsätter att du redan har en dedikerad SQL-pool (tidigare SQL DW) som du kan pausa och återuppta. Om du behöver skapa en kan du använda Skapa och ansluta [– portalen](create-data-warehouse-portal.md) för att skapa en dedikerad SQL-pool (tidigare SQL DW) med namnet **mySampleDataWarehouse.**

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen [med kommandot Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

Om du vill se vilken prenumeration du använder kör [du Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Om du behöver använda en annan prenumeration än standardprenumerationen kör [du Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-dedicated-sql-pool-formerly-sql-dw-information"></a>Leta upp dedikerad information om SQL-pool (tidigare SQL DW)

Leta upp databasnamnet, servernamnet och resursgruppen för den dedikerade SQL-poolen (tidigare SQL DW) som du planerar att pausa och återuppta.

Följ de här stegen för att hitta platsinformation för din dedikerade SQL-pool (tidigare SQL DW):

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Klicka **Azure Synapse Analytics (tidigare SQL DW)** på vänster sida i Azure Portal.
1. Välj **mySampleDataWarehouse** på **Azure Synapse Analytics (tidigare SQL DW).** SQL-poolen öppnas.

    ![Servernamn och resursgrupp](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Skriv ned namnet på den dedikerade SQL-poolen (tidigare SQL DW), som är databasnamnet. Anteckna även servernamnet och resursgruppen.
1. Använd bara den första delen av servernamnet i PowerShell-cmdletarna. I föregående bild är det fullständiga servernamnet sqlpoolservername.database.windows.net. Vi använder **sqlpoolservername** som servernamn i PowerShell-cmdleten.

## <a name="pause-compute"></a>Pausa beräkning

För att spara kostnader kan du pausa och återuppta beräkningsresurser på begäran. Om du till exempel inte använder databasen under natten och på helger kan du pausa den under dessa tider och återuppta den under dagen.

>[!NOTE]
>Beräkningsresurser debiteras inte när databasen pausas. Du fortsätter dock att debiteras för lagring.

Om du vill pausa en databas använder [du cmdleten Suspend-AzSqlDatabase.](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) I följande exempel pausas en SQL-pool med **namnet mySampleDataWarehouse** på en server med namnet **sqlpoolservername**. Servern finns i en Azure-resursgrupp med namnet **myResourceGroup.**

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

I följande exempel hämtas databasen till det $database objektet. Sedan kommer objektet att ledas [till Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Resultaten lagras i objektet resultDatabase. Det sista kommandot visar resultatet.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Återuppta beräkning

Starta en databas med cmdleten [Resume-AzSqlDatabase.](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) I följande exempel startas en databas med **namnet mySampleDataWarehouse** på en server med namnet **sqlpoolservername**. Servern finns i en Azure-resursgrupp med namnet **myResourceGroup.**

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

I nästa exempel hämtas databasen till det $database objektet. Den leder sedan objektet till [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) och lagrar resultatet i $resultDatabase. Det sista kommandot visar resultatet.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>Kontrollera status för sql-poolåtgärden

Om du vill kontrollera statusen för din dedikerade SQL-pool (tidigare SQL DW) använder du cmdleten [Get-AzSqlDatabaseActivity.](/powershell/module/az.sql/Get-AzSqlDatabaseActivity?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för informationslagerenheter och data som lagras i din dedikerade SQL-pool (tidigare SQL DW). Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagringen pausar du beräkningen.
- Om du vill ta bort framtida avgifter kan du ta bort SQL-poolen.

Följ dessa steg för att rensa resurser enligt dina önskemål.

1. Logga in på [Azure Portal](https://portal.azure.com)och klicka på DIN SQL-pool.

    ![Rensa resurser](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Om du vill pausa beräkningarna klickar du på knappen **Pausa**. När SQL-poolen har pausats visas knappen **Start.**  Klicka på **Starta** om du vill återuppta beräkningarna.

3. Om du vill ta bort SQL-poolen så att du inte debiteras för beräkning eller lagring klickar du på **Ta bort.**

4. Om du vill ta bort SQL-servern som du **skapade klickar sqlpoolservername.database.windows.net** och sedan på Ta **bort.**  Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

5. Om du vill ta bort resursgruppen klickar du på **myResourceGroup** och sedan på **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om SQL-pool fortsätter du till [artikeln Läsa in data till dedikerad SQL-pool (tidigare SQL DW).](./load-data-from-azure-blob-storage-using-copy.md) Mer information om hur du hanterar beräkningsfunktioner finns i artikeln [Hantera beräkningsöversikt.](sql-data-warehouse-manage-compute-overview.md)
