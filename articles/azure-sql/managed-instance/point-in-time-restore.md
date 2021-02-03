---
title: Återställning av tidpunkt (PITR)
titleSuffix: Azure SQL Managed Instance
description: Återställ en databas på en hanterad Azure SQL-instans till en tidigare tidpunkt.
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
ms.openlocfilehash: d3414cb31192211c1663a84e1541f56b63674660
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525406"
---
# <a name="restore-a-database-in-azure-sql-managed-instance-to-a-previous-point-in-time"></a>Återställa en databas i en Azure SQL-hanterad instans till en tidigare tidpunkt
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Använd tidpunkts återställning (PITR) för att skapa en databas som en kopia av en annan databas från en tidigare tidpunkt. Den här artikeln beskriver hur du utför en tidpunkts återställning av en databas i Azure SQL Managed instance.

Återställning efter tidpunkt är användbart i återställnings scenarier, t. ex. incidenter orsakade av fel, felaktiga inlästa data eller borttagning av viktiga data. Du kan också använda det bara för testning eller granskning. Säkerhetskopierade filer sparas i 7 till 35 dagar, beroende på dina databas inställningar.

Återställning av tidpunkter kan återställa en databas:

- från en befintlig databas.
- från en borttagen databas.
- till samma SQL-hanterade instans eller till en annan SQL-hanterad instans. 

## <a name="limitations"></a>Begränsningar

Tidpunkts återställning till SQL-hanterad instans har följande begränsningar:

- När du återställer från en instans av SQL-hanterad instans till en annan måste båda instanserna finnas i samma prenumeration och region. Återställning mellan regioner och över prenumerationer stöds inte för närvarande.
- Det går inte att återställa punkt-till-tid för en hel SQL-hanterad instans. Den här artikeln förklarar bara vad som är möjligt: återställning vid olika tidpunkter av en databas som finns på SQL-hanterad instans.

> [!WARNING]
> Tänk på lagrings storleken för din SQL-hanterade instans. Beroende på storleken på de data som ska återställas kan du få slut på instans lagring. Om det inte finns tillräckligt med utrymme för återställda data kan du använda en annan metod.

I följande tabell visas tidpunkter för återställnings scenarier för SQL-hanterad instans:

|           |Återställ befintlig databas till samma instans av SQL-hanterad instans| Återställa en befintlig databas till en annan SQL-hanterad instans|Återställ utelämnad databas till samma SQL-hanterade instans|Återställ utelämnad databas till en annan SQL-hanterad instans|
|:----------|:----------|:----------|:----------|:----------|
|**Azure-portalen**| Ja|Inga |Ja|Inga|
|**Azure CLI**|Ja |Ja |Inga|Inga|
|**PowerShell**| Ja|Ja |Ja|Ja|

## <a name="restore-an-existing-database"></a>Återställa en befintlig databas

Återställ en befintlig databas till samma SQL-hanterade instans med hjälp av Azure Portal, PowerShell eller Azure CLI. Om du vill återställa en databas till en annan SQL-hanterad instans använder du PowerShell eller Azure CLI så att du kan ange egenskaperna för SQL-hanterad instans och resurs grupp. Om du inte anger dessa parametrar kommer databasen att återställas till den befintliga SQL-hanterade instansen som standard. Azure Portal har för närvarande inte stöd för återställning till en annan SQL-hanterad instans.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Gå till din SQL-hanterade instans och välj den databas som du vill återställa.
3. Välj **Återställ** på databas sidan:

    ![Återställa en databas med hjälp av Azure Portal](./media/point-in-time-restore/restore-database-to-mi.png)

4. På sidan **Återställ** väljer du punkten för det datum och den tid som du vill återställa databasen till.
5. Välj **Bekräfta** för att återställa databasen. Den här åtgärden startar återställnings processen, som skapar en ny databas och fyller den med data från den ursprungliga databasen vid den angivna tidpunkten. Mer information om återställnings processen finns i [återställnings tid](../database/recovery-using-backups.md#recovery-time).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du inte redan har Azure PowerShell installerat, se [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

Om du vill återställa-databasen med hjälp av PowerShell anger du värdena för parametrarna i följande kommando. Kör sedan kommandot:

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

Om du vill återställa databasen till en annan SQL-hanterad instans anger du även namnen på mål resurs gruppen och mål-SQL Managed-instansen:  

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

Mer information finns i [restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du inte redan har installerat Azure CLI kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Om du vill återställa databasen med hjälp av Azure CLI anger du värdena för parametrarna i följande kommando. Kör sedan kommandot:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Om du vill återställa databasen till en annan SQL-hanterad instans anger du även namnen på mål resurs gruppen och SQL-hanterad instans:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

En detaljerad förklaring av tillgängliga parametrar finns i [CLI-dokumentationen för att återställa en databas i en SQL-hanterad instans](/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore).

---

## <a name="restore-a-deleted-database"></a>Återställa en borttagen databas

Du kan återställa en borttagen databas med hjälp av PowerShell eller Azure Portal. Om du vill återställa en borttagen databas till samma instans använder du antingen Azure Portal eller PowerShell. Om du vill återställa en borttagen databas till en annan instans använder du PowerShell. 

### <a name="portal"></a>Portal 


Om du vill återställa en hanterad databas med Azure Portal öppnar du översikts sidan SQL-hanterad instans och väljer **borttagna databaser**. Välj en borttagen databas som du vill återställa och skriv namnet på den nya databasen som ska skapas med data som återställs från säkerhets kopian.

  ![Skärm bild av Återställ borttagen Azure SQL-instans databas](./media/point-in-time-restore/restore-deleted-sql-managed-instance-annotated.png)

.. /.. /sql-database

### <a name="powershell"></a>PowerShell

Om du vill återställa en databas till samma instans uppdaterar du parameter värden och kör sedan följande PowerShell-kommando: 

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

Om du vill återställa databasen till en annan SQL-hanterad instans anger du även namnen på mål resurs gruppen och mål-SQL Managed-instansen:

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

## <a name="overwrite-an-existing-database"></a>Skriv över en befintlig databas

Om du vill skriva över en befintlig databas måste du:

1. Ta bort den befintliga databasen som du vill skriva över.
2. Byt namn på databasen för den tidpunkt som återställs till namnet på den databas som du har släppt.

### <a name="drop-the-original-database"></a>Ta bort den ursprungliga databasen

Du kan släppa databasen med hjälp av Azure Portal, PowerShell eller Azure CLI.

Du kan också släppa databasen genom att ansluta till SQL-hanterad instans direkt, starta SQL Server Management Studio (SSMS) och sedan köra följande Transact-SQL-kommando (T-SQL):

```sql
DROP DATABASE WorldWideImporters;
```

Använd någon av följande metoder för att ansluta till din databas i SQL-hanterad instans:

- [SSMS/Azure Data Studio via en virtuell Azure-dator](./connect-vm-instance-configure.md)
- [Punkt-till-plats](./point-to-site-p2s-configure.md)
- [Offentlig slutpunkt](./public-endpoint-configure.md)

# <a name="portal"></a>[Portal](#tab/azure-portal)

I Azure Portal väljer du databasen från SQL-hanterad instans och väljer sedan **ta bort**.

   ![Ta bort en databas med hjälp av Azure Portal](./media/point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd följande PowerShell-kommando för att släppa en befintlig databas från en SQL-hanterad instans:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande Azure CLI-kommando för att släppa en befintlig databas från en SQL-hanterad instans:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Ändra det nya databas namnet så att det matchar det ursprungliga databas namnet

Anslut direkt till SQL-hanterad instans och starta SQL Server Management Studio. Kör sedan följande Transact-SQL-fråga (T-SQL). Frågan kommer att ändra namnet på den återställda databasen till den för den borttagna databas som du tänker skriva över.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Använd någon av följande metoder för att ansluta till din databas i SQL-hanterad instans:

- [Virtuell Azure-dator](./connect-vm-instance-configure.md)
- [Punkt-till-plats](./point-to-site-p2s-configure.md)
- [Offentlig slutpunkt](./public-endpoint-configure.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [automatiserade säkerhets kopieringar](../database/automated-backups-overview.md).
