---
title: Återställa en dedikerad SQL-pool från en geo-säkerhetskopiering
description: Instruktions guide för geo-återställning av en dedikerad SQL-pool i Azure Synapse Analytics
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
ms.openlocfilehash: 4683bd84873506483209f4a0eb3751a1b163ed48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96449884"
---
# <a name="geo-restore-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Geo-Återställ en dedikerad SQL-pool i Azure Synapse Analytics

I den här artikeln får du lära dig att återställa din dedikerade SQL-pool (tidigare SQL DW) från en geo-säkerhetskopiering via Azure Portal och PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verifiera din DTU-kapacitet.** Varje dedikerad SQL-pool (tidigare SQL DW) finns på en [logisk SQL-Server](../../azure-sql/database/logical-servers.md) (till exempel myserver.Database.Windows.net) som har en standard-DTU-kvot. Kontrol lera att SQL Server har tillräckligt med den återstående DTU-kvoten för databasen som återställs. Information om hur du beräknar DTU krävs eller begär mer DTU finns i [begär en ändring av DTU-kvot](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Återställa från en geografisk Azure-region via PowerShell

Om du vill återställa från en geo-säkerhets kopiering använder du cmdleten [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) och [restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

> [!NOTE]
> Du kan utföra en geo-återställning till Gen2! Det gör du genom att ange en Gen2-ServiceObjectiveName (t. ex. DW1000 **c**) som en valfri parameter.
>

1. Innan du börjar ska du se till att [installera Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Öppna PowerShell.
3. Anslut till ditt Azure-konto och lista alla prenumerationer som är kopplade till ditt konto.
4. Välj den prenumeration som innehåller det data lager som ska återställas.
5. Hämta det data lager som du vill återställa.
6. Skapa en begäran om återställning av data lagret.
7. Kontrol lera statusen för det geo-återställda informations lagret.
8. Information om hur du konfigurerar ditt informations lager när återställningen har slutförts finns i [Konfigurera din databas efter återställning]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

Den återställda databasen kommer att TDEs om käll databasen är TDE-aktive rad.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Återställa från en geografisk region i Azure via Azure Portal

Följ stegen som beskrivs nedan för att återställa en dedikerad SQL-pool (tidigare SQL DW) från en geo-säkerhets kopiering:

1. Logga in på ditt [Azure Portal](https://portal.azure.com/) -konto.
1. Sök efter **dedikerade SQL-pooler (tidigare SQL DW)**.

   ![Ny DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

1. Klicka på Lägg till och fyll i informationen som begärs på fliken **grundläggande** och klicka på **Nästa: ytterligare inställningar**.

   ![Grundläggande inställningar](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

1. Om du vill **använda befintlig data** parameter väljer du **säkerhets kopiering** och väljer lämplig säkerhets kopia från rullnings List alternativen. Klicka på **Granska + Skapa**.

   ![säkerhetskopiering](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

1. När data lagret har återställts kontrollerar du att **statusen** är online.

## <a name="next-steps"></a>Nästa steg

- [Återställa en befintlig dedikerad SQL-pool (tidigare SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Återställa en borttagen dedikerad SQL-pool (tidigare SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
