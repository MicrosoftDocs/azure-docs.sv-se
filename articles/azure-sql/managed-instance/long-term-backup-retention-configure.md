---
title: 'Azure SQL-hanterad instans: långsiktig kvarhållning av säkerhets kopior (PowerShell)'
description: Lär dig hur du lagrar och återställer automatiserade säkerhets kopieringar på separata Azure Blob Storage-behållare för en Azure SQL-hanterad instans med PowerShell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 04/29/2020
ms.openlocfilehash: a5a2ff85395a55bcd4e8405e2eb60c6a4645818c
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833447"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Hantera Azure SQL Managed instance långsiktig kvarhållning av säkerhets kopior (PowerShell)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

I Azure SQL-hanterad instans kan du konfigurera en [långsiktig bevarande princip för säkerhets kopior](../database/long-term-retention-overview.md#sql-managed-instance-support) (brv) som en begränsad offentlig för hands versions funktion. På så sätt kan du automatiskt behålla säkerhets kopior av databasen i separata Azure Blob Storage-behållare i upp till 10 år. Du kan sedan återställa en databas med dessa säkerhets kopior med PowerShell.

   > [!IMPORTANT]
   > BRV för hanterade instanser är för närvarande i begränsad för hands version och är tillgängligt för EA-och CSP-prenumerationer i fall. Om du vill begära registrering skapar du ett [support ärende för Azure](https://azure.microsoft.com/support/create-ticket/). Välj tekniskt problem för tjänsten Välj SQL Database Hanterad instans och för problem typen väljer du **säkerhets kopiering, återställning och affärs kontinuitet/långsiktig kvarhållning av säkerhets kopior**. I din begäran kan du ange det tillstånd som du vill ska registreras i den begränsade offentliga för hands versionen av LTR för hanterad instans.

Följande avsnitt visar hur du använder PowerShell för att konfigurera långsiktig kvarhållning av säkerhets kopior, Visa säkerhets kopior i Azure SQL-lagring och återställa från en säkerhets kopia i Azure SQL Storage.

## <a name="azure-roles-to-manage-long-term-retention"></a>Azure-roller för att hantera långsiktig kvarhållning

För **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** och **restore-AzSqlInstanceDatabase** måste du ha en av följande roller:

- Prenumerations ägarens roll eller
- Rollen hanterad instans deltagare eller
- Anpassad roll med följande behörigheter:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

För **Remove-AzSqlInstanceDatabaseLongTermRetentionBackup** måste du ha en av följande roller:

- Prenumerations ägarens roll eller
- Anpassad roll med följande behörighet:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> Rollen hanterad instans deltagare har inte behörighet att ta bort LTR-säkerhetskopieringar.

Azure RBAC-behörigheter kan beviljas i antingen *prenumerations* -eller *resurs grupps* omfång. Men för att komma åt LTR-säkerhetskopieringar som tillhör en utelämnad instans måste behörigheten beviljas i *prenumerations* omfånget för den instansen.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

## <a name="create-an-ltr-policy"></a>Skapa en LTR-princip

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount

Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W' 
    YearlyRetention = 'P5Y' 
    WeekOfYear = '16'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

## <a name="view-ltr-policies"></a>Visa LTR-principer

Det här exemplet visar hur du visar en lista över LTR-principer i en instans för en enskild databas

```powershell
# gets the current version of LTR policy for a database
$LTRPolicies = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup
}
Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy 
```

Det här exemplet visar hur du listar LTR-principerna för alla databaser på en instans

```powershell
# gets the current version of LTR policy for all of the databases on an instance

$Databases = Get-AzSqlInstanceDatabase -ResourceGroupName $resourceGroup -InstanceName $instanceName

$LTRParams = @{
    InstanceName = $instanceName
    ResourceGroupName = $resourceGroup
}

foreach($database in $Databases.Name){
    Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRParams  -DatabaseName $database
 }
```

## <a name="clear-an-ltr-policy"></a>Rensa en LTR-princip

Det här exemplet visar hur du tar bort en LTR-princip från en databas

```powershell
# remove the LTR policy from a database
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    RemovePolicy = $true
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

## <a name="view-ltr-backups"></a>Visa LTR-säkerhetskopieringar

Det här exemplet visar hur du visar en lista över säkerhets kopieringar i en instans.

```powershell

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$LTRBackupParam = @{
    Location = $instance.Location 
    DatabaseState = 'Live'
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# only list the latest LTR backup for each database
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    OnlyLatestPerDatabase = $true
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 
```

## <a name="delete-ltr-backups"></a>Ta bort LTR-säkerhetskopieringar

Det här exemplet visar hur du tar bort en LTR-säkerhetskopiering från listan över säkerhets kopior.

```powershell
# remove the earliest backup
# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Borttagning av LTR-säkerhetskopiering går inte att ångra. Om du vill ta bort en LTR-säkerhetskopiering när instansen har tagits bort måste du ha behörighet som prenumerations omfång. Du kan ställa in aviseringar om varje borttagning i Azure Monitor genom filtrering för åtgärd, tar bort en säkerhets kopia av långsiktig kvarhållning. Aktivitets loggen innehåller information om vem och när du har gjort begäran. Mer information finns i [skapa aktivitets logg aviseringar](../../azure-monitor/platform/alerts-activity-log.md) .

## <a name="restore-from-ltr-backups"></a>Återställa från LTR-säkerhetskopieringar

Det här exemplet visar hur du återställer från en LTR-säkerhetskopiering. Obs! det här gränssnittet ändrades inte, men parametern resurs-ID kräver nu resurs-ID: t för säkerhets kopiering.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbname
    OnlyLatestPerDatabase = $true
}
$ltrBackup = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 

$RestoreLTRParam = @{
    TargetInstanceName          = $instanceName 
    TargetResourceGroupName     = $resourceGroup 
    TargetInstanceDatabaseName  = $dbName
    FromLongTermRetentionBackup = $true
    ResourceId                  = $ltrBackup.ResourceId 
}
Restore-AzSqlInstanceDatabase @RestoreLTRParam
```

> [!IMPORTANT]
> Om du vill återställa från en LTR-säkerhetskopiering när instansen har tagits bort, måste du ha behörighet som är begränsad till prenumerationen på instansen och den prenumerationen måste vara aktiv. Du måste också utelämna den valfria-ResourceGroupName-parametern.

> [!NOTE]
> Här kan du ansluta till den återställda databasen med hjälp av SQL Server Management Studio för att utföra nödvändiga åtgärder, till exempel för att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller för att ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet. Se tidpunkt [för återställning](../database/recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Nästa steg

- Mer information om tjänstgenererade automatiska säkerhetskopior finns i avsnittet om [automatiska säkerhetskopior](../database/automated-backups-overview.md)
- Mer information om långsiktig kvarhållning av säkerhetskopior finns i avsnittet om [långsiktig kvarhållning av säkerhetskopior](../database/long-term-retention-overview.md)
