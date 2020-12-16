---
title: Återställa en befintlig dedikerad SQL-pool (tidigare SQL DW)
description: Instruktions guide för att återställa en befintlig dedikerad SQL-pool i Azure Synapse Analytics.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2ce552a13592c9d26ef70575f98b0b76ecc454ff
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592000"
---
# <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Återställa en befintlig dedikerad SQL-pool (tidigare SQL DW)

I den här artikeln får du lära dig hur du återställer en befintlig dedikerad SQL-pool (tidigare SQL DW) med hjälp av Azure Portal och PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verifiera din DTU-kapacitet.** Varje pool finns på en [logisk SQL-Server](../../azure-sql/database/logical-servers.md) (till exempel myserver.Database.Windows.net) som har en standard-DTU-kvot. Kontrol lera att servern har tillräckligt med den återstående DTU-kvoten för databasen som återställs. Information om hur du beräknar DTU krävs eller begär mer DTU finns i [begär en ändring av DTU-kvot](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Innan du börjar

1. Se till att [installera Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Ha en befintlig återställnings punkt som du vill återställa från. Om du vill skapa en ny återställning går [du till självstudien för att skapa en ny användardefinierad återställnings punkt](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-powershell"></a>Återställa en befintlig dedikerad SQL-pool (tidigare SQL DW) via PowerShell

Om du vill återställa en befintlig dedikerad SQL-pool (tidigare SQL DW) från en återställnings punkt använder du PowerShell-cmdleten [restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

1. Öppna PowerShell.

2. Anslut till ditt Azure-konto och lista alla prenumerationer som är kopplade till ditt konto.

3. Välj den prenumeration som innehåller den databas som ska återställas.

4. Lista över återställnings punkterna för den dedikerade SQL-poolen (tidigare SQL DW).

5. Välj önskad återställnings punkt med hjälp av RestorePointCreationDate.

6. Återställ den dedikerade SQL-poolen (tidigare SQL DW) till önskad återställnings punkt med hjälp av [restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell-cmdlet.

    1. Om du vill återställa den dedikerade SQL-poolen (tidigare SQL DW) till en annan server, måste du ange det andra Server namnet.  Den här servern kan också finnas i en annan resurs grupp och region.
    2. Om du vill återställa till en annan prenumeration använder du knappen flytta för att flytta servern till en annan prenumeration.

7. Kontrol lera att den återställda dedikerade SQL-poolen (tidigare SQL DW) är online.

8. När återställningen har slutförts kan du konfigurera den återställda dedikerade SQL-poolen (tidigare SQL DW) genom att följa [konfigurationen av databasen efter återställningen](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

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

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different server
#$TargetResourceGroupName = $Database.ResourceGroupName # for restoring to different server in same resourcegroup 
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-the-azure-portal"></a>Återställa en befintlig dedikerad SQL-pool (tidigare SQL DW) via Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Navigera till den dedikerade som du vill återställa från.
3. Överst på bladet översikt väljer du **Återställ**.

    ![ Återställa översikt](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Välj antingen **automatiska återställnings punkter** eller **användardefinierade återställnings punkter**. Om den dedikerade SQL-poolen (tidigare SQL DW) inte har några automatiska återställnings punkter väntar du några timmar eller skapar en användardefinierad återställnings punkt innan du återställer. För User-Defined återställnings punkter väljer du en befintlig eller skapar en ny. För **Server** kan du välja en server i en annan resurs grupp och region eller skapa en ny. När du har angett alla parametrar klickar du på **Granska + Återställ**.

    ![Automatiska återställningspunkter](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Nästa steg

- [Återställa en borttagen dedikerad SQL-pool (tidigare SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
- [Återställa från en dedikerad geo-säkerhetskopia SQL-pool (tidigare SQL DW)](sql-data-warehouse-restore-from-geo-backup.md)
