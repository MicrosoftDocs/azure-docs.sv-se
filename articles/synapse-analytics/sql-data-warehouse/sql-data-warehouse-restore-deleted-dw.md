---
title: Återställa en borttagen dedikerad SQL-pool (tidigare SQL DW)
description: Guide för att återställa en borttagen dedikerad SQL-pool i Azure Synapse Analytics.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7264791654bf1b646338f0d429930b63f0cc3a06
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96449921"
---
# <a name="restore-a-deleted-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Återställa en borttagen dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics

I den här artikeln får du lära dig att återställa en dedikerad SQL-pool (tidigare SQL DW) med antingen Azure Portal eller PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verifiera din DTU-kapacitet.** Varje dedikerad SQL-pool (tidigare SQL DW) finns på en [logisk SQL-Server](../../azure-sql/database/logical-servers.md) (till exempel myserver.Database.Windows.net) som har en standard-DTU-kvot.  Kontrol lera att servern har tillräckligt med den återstående DTU-kvoten för databasen som återställs. Information om hur du beräknar DTU krävs eller begär mer DTU finns i [begär en ändring av DTU-kvot](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Återställa ett borttaget informations lager via PowerShell

Om du vill återställa en borttagen dedikerad SQL-pool (tidigare SQL DW) använder du cmdleten [restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Om motsvarande server också har tagits bort kan du inte återställa det data lagret.

1. Innan du börjar ska du se till att [installera Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Öppna PowerShell.
3. Anslut till ditt Azure-konto och lista alla prenumerationer som är kopplade till ditt konto.
4. Välj den prenumeration som innehåller den borttagna dedikerade SQL-poolen (tidigare SQL DW) som ska återställas.
5. Hämta det angivna borttagna informations lagret.
6. Återställa den borttagna dedikerade SQL-poolen (tidigare SQL DW)
    1. Om du vill återställa den borttagna dedikerade SQL-poolen (tidigare SQL DW) till en annan server, se till att ange det andra Server namnet.  Den här servern kan också finnas i en annan resurs grupp och region.
    1. Om du vill återställa till en annan prenumeration använder du knappen [Flytta](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) för att flytta servern till en annan prenumeration.
7. Kontrol lera att det återställda informations lagret är online.
8. När återställningen har slutförts kan du konfigurera det återställda data lagret genom att följa [konfiguration av databasen efter återställningen](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Återställa en borttagen databas med hjälp av Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Navigera till servern som det borttagna data lagret finns på.
3. Välj ikonen **borttagna databaser** i innehålls förteckningen.

    ![Borttagna databaser](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Välj den borttagna Azure Synapse-analys som du vill återställa.

    ![Välj Borttagna databaser](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Ange ett nytt **databas namn** och klicka på **OK**

    ![Ange databas namn](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Nästa steg

- [Återställa en befintlig dedikerad SQL-pool (tidigare SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Återställa från en dedikerad geo-säkerhetskopia SQL-pool (tidigare SQL DW)](sql-data-warehouse-restore-from-geo-backup.md)
