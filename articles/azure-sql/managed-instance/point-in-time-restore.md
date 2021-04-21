---
title: Återställning till tidpunkt (PITR)
titleSuffix: Azure SQL Managed Instance
description: Återställ en databas på Azure SQL Managed Instance till en tidigare tidpunkt.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 4c116b378c72d87641157fc453d65e46be9f43ec
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787176"
---
# <a name="restore-a-database-in-azure-sql-managed-instance-to-a-previous-point-in-time"></a>Återställa en databas Azure SQL Managed Instance till en tidigare tidpunkt
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Använd återställning till tidpunkt (PITR) för att skapa en databas som en kopia av en annan databas från en tidigare tidpunkt. Den här artikeln beskriver hur du gör en återställning till en tidpunkt av en databas i Azure SQL Managed Instance.

Återställning till tidpunkt är användbart i återställningsscenarier, till exempel incidenter som orsakas av fel, felaktigt inlästa data eller borttagning av viktiga data. Du kan också använda den bara för testning eller granskning. Säkerhetskopierade filer sparas i 7 till 35 dagar, beroende på dina databasinställningar.

Återställning till tidpunkt kan återställa en databas:

- från en befintlig databas.
- från en borttagna databas.
- till samma SQL Managed Instance, eller till en annan SQL Managed Instance. 

## <a name="limitations"></a>Begränsningar

Återställning till tidpunkt till SQL Managed Instance har följande begränsningar:

- När du återställer från en instans av SQL Managed Instance till en annan måste båda instanserna finnas i samma prenumeration och region. Återställning mellan regioner och prenumerationer stöds inte för närvarande.
- Det går inte att återställa en hel SQL Managed Instance tidpunkt. Den här artikeln förklarar bara vad som är möjligt: återställning till tidpunkt av en databas som finns på SQL Managed Instance.

> [!WARNING]
> Tänk på lagringsstorleken för dina SQL Managed Instance. Beroende på storleken på de data som ska återställas kan instanslagringen ta slut. Om det inte finns tillräckligt med utrymme för återställda data använder du en annan metod.

I följande tabell visas scenarier för återställning till tidpunkt för SQL Managed Instance:

|           |Återställa befintlig databas till samma instans av SQL Managed Instance| Återställa en befintlig databas till en annan SQL Managed Instance|Återställa bort ignorerad databas till samma SQL Managed Instance|Återställa bort ignorerad databas till en annan SQL Managed Instance|
|:----------|:----------|:----------|:----------|:----------|
|**Azure-portalen**| Ja|Inga |Ja|Inga|
|**Azure CLI**|Ja |Ja |Inga|Inga|
|**PowerShell**| Ja|Ja |Ja|Ja|

## <a name="restore-an-existing-database"></a>Återställa en befintlig databas

Återställ en befintlig databas till samma SQL Managed Instance med Azure Portal, PowerShell eller Azure CLI. Om du vill återställa en databas SQL Managed Instance en annan databas använder du PowerShell eller Azure CLI så att du kan ange egenskaperna för SQL Managed Instance och resursgruppen. Om du inte anger de här parametrarna återställs databasen till den befintliga SQL Managed Instance som standard. Den Azure Portal stöder för närvarande inte återställning till en annan SQL Managed Instance.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Gå till SQL Managed Instance och välj den databas som du vill återställa.
3. Välj **Återställ** på databassidan:

    ![Återställa en databas med hjälp av Azure Portal](./media/point-in-time-restore/restore-database-to-mi.png)

4. På **sidan Återställ** väljer du tidpunkt för det datum och den tid som du vill återställa databasen till.
5. Välj **Bekräfta** för att återställa databasen. Den här åtgärden startar återställningsprocessen, som skapar en ny databas och fyller den med data från den ursprungliga databasen vid den angivna tidpunkten. Mer information om återställningsprocessen finns i [Återställningstid.](../database/recovery-using-backups.md#recovery-time)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du inte redan har Azure PowerShell kan du [gå till Installera Azure PowerShell modulen](/powershell/azure/install-az-ps).

Om du vill återställa databasen med hjälp av PowerShell anger du dina värden för parametrarna i följande kommando. Kör sedan kommandot:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Om du vill återställa databasen till SQL Managed Instance anger du även namnen på målresursgruppen och SQL Managed Instance:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Mer information finns i [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du inte redan har installerat Azure CLI kan du gå [till Installera Azure CLI.](/cli/azure/install-azure-cli)

Om du vill återställa databasen med hjälp av Azure CLI anger du dina värden för parametrarna i följande kommando. Kör sedan kommandot:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Om du vill återställa databasen till SQL Managed Instance anger du även namnen på målresursgruppen och SQL Managed Instance:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

En detaljerad förklaring av tillgängliga parametrar finns i [CLI-dokumentationen för att återställa en databas i en SQL Managed Instance](/cli/azure/sql/midb#az_sql_midb_restore).

---

## <a name="restore-a-deleted-database"></a>Återställa en borttagen databas

Du kan återställa en borttagna databas med hjälp av PowerShell eller Azure Portal. Om du vill återställa en borttagna databas till samma instans använder du antingen Azure Portal eller PowerShell. Om du vill återställa en borttagna databas till en annan instans använder du PowerShell. 

### <a name="portal"></a>Portalen 


Om du vill återställa en hanterad databas med Azure Portal öppnar du SQL Managed Instance översiktssidan och väljer **Borttagna databaser.** Välj en borttagna databas som du vill återställa och ange namnet på den nya databasen som ska skapas med data som återställs från säkerhetskopian.

  ![Skärmbild av återställning av borttagna Azure SQL-instansdatabas](./media/point-in-time-restore/restore-deleted-sql-managed-instance-annotated.png)

.. /.. /sql-database

### <a name="powershell"></a>PowerShell

Om du vill återställa en databas till samma instans uppdaterar du parametervärdena och kör sedan följande PowerShell-kommando: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -FromPointInTimeBackup -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Om du vill återställa databasen till SQL Managed Instance anger du även namnen på målresursgruppen och SQL Managed Instance:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlinstanceDatabase -FromPointInTimeBackup -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Skriva över en befintlig databas

Om du vill skriva över en befintlig databas måste du:

1. Ta bort den befintliga databas som du vill skriva över.
2. Byt namn på den återställda databasen till namnet på den databas som du tog bort.

### <a name="drop-the-original-database"></a>Ta bort den ursprungliga databasen

Du kan ta bort databasen med hjälp Azure Portal, PowerShell eller Azure CLI.

Du kan också ta bort databasen genom att ansluta till SQL Managed Instance direkt, starta SQL Server Management Studio (SSMS) och sedan köra följande Transact-SQL-kommando (T-SQL):

```sql
DROP DATABASE WorldWideImporters;
```

Använd någon av följande metoder för att ansluta till databasen i SQL Managed Instance:

- [SSMS/Azure Data Studio via en virtuell Azure-dator](./connect-vm-instance-configure.md)
- [Punkt-till-plats](./point-to-site-p2s-configure.md)
- [Offentlig slutpunkt](./public-endpoint-configure.md)

# <a name="portal"></a>[Portal](#tab/azure-portal)

I Azure Portal väljer du databasen från SQL Managed Instance och väljer sedan Ta **bort.**

   ![Ta bort en databas med hjälp av Azure Portal](./media/point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd följande PowerShell-kommando för att ta bort en befintlig databas från en SQL Managed Instance:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande Azure CLI-kommando för att ta bort en befintlig databas från en SQL Managed Instance:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Ändra det nya databasnamnet så att det matchar det ursprungliga databasnamnet

Anslut direkt till SQL Managed Instance och starta SQL Server Management Studio. Kör sedan följande Transact-SQL-fråga (T-SQL). Frågan ändrar namnet på den återställda databasen till namnet på den bort ignorerade databasen som du vill skriva över.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Använd någon av följande metoder för att ansluta till databasen i SQL Managed Instance:

- [Virtuell Azure-dator](./connect-vm-instance-configure.md)
- [Punkt-till-plats](./point-to-site-p2s-configure.md)
- [Offentlig slutpunkt](./public-endpoint-configure.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer [om automatiska säkerhetskopieringar.](../database/automated-backups-overview.md)
