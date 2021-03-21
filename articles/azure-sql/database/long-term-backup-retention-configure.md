---
title: 'Azure SQL Database: hantera långsiktig kvarhållning av säkerhets kopior'
description: Lär dig hur du lagrar och återställer automatiserade säkerhets kopieringar för Azure SQL Database i Azure Storage (i upp till 10 år) med hjälp av Azure Portal och PowerShell
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/16/2020
ms.openlocfilehash: fad19d360f7c476ba71a9bbe00b58387b92f8ac4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690572"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Hantera Azure SQL Database långsiktig kvarhållning av säkerhets kopior
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Med Azure SQL Database kan du ange en [långsiktig bevarande princip för säkerhets kopior](long-term-retention-overview.md) (brv) för att automatiskt behålla säkerhets kopior i separata Azure Blob Storage-behållare i upp till 10 år. Du kan sedan återställa en databas med hjälp av de här säkerhets kopiorna med hjälp av Azure Portal eller PowerShell. Principer för långsiktig kvarhållning stöds också för [Azure SQL-hanterad instans](../managed-instance/long-term-backup-retention-configure.md).

## <a name="using-the-azure-portal"></a>Använda Azure Portal

I följande avsnitt visas hur du använder Azure Portal för att ange långsiktiga bevarande principer, hantera tillgängliga långsiktiga säkerhets kopior och återställa från en tillgänglig säkerhets kopia.

### <a name="configure-long-term-retention-policies"></a>Konfigurera principer för långsiktig kvarhållning

Du kan konfigurera SQL Database att [behålla automatiserade säkerhets kopieringar](long-term-retention-overview.md) under en längre tid än kvarhållningsperioden för din tjänst nivå.

1. I Azure Portal navigerar du till servern och väljer sedan **säkerhets kopieringar**. Välj fliken **bevarande principer** för att ändra inställningarna för kvarhållning av säkerhets kopior.

   ![miljö för bevarande principer](./media/long-term-backup-retention-configure/ltr-policies-tab.png)

2. På fliken bevarande principer väljer du de databaser på vilka du vill ange eller ändra bevarande principer för långsiktig säkerhets kopiering. Omarkerade databaser kommer inte att påverkas.

   ![Välj databas för att konfigurera bevarande principer för säkerhets kopior](./media/long-term-backup-retention-configure/ltr-policies-tab-configure.png)

3. I fönstret **Konfigurera principer** anger du önskad kvarhållningsperiod för säkerhets kopiering varje vecka, månad eller år. Välj en kvarhållningsperiod på 0 om du vill ange att ingen långsiktig kvarhållning av säkerhets kopior ska anges.

   ![fönstret konfigurera principer](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

4. Välj **tillämpa** om du vill tillämpa de valda inställningarna för kvarhållning för alla valda databaser.

> [!IMPORTANT]
> När du aktiverar en långsiktig bevarande princip för säkerhets kopiering kan det ta upp till sju dagar innan den första säkerhets kopieringen blir synlig och tillgänglig för återställning. Mer information om säkerhets kopierings cadance finns i [långsiktig kvarhållning av säkerhets kopior](long-term-retention-overview.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Visa säkerhets kopior och återställning från en säkerhets kopia

Visa säkerhets kopiorna som bevaras för en speciell databas med en LTR-princip och Återställ från dessa säkerhets kopior.

1. I Azure Portal navigerar du till servern och väljer sedan **säkerhets kopieringar**. Om du vill visa tillgängliga säkerhets kopierings säkerhets kopior för en speciell databas väljer du **Hantera** under kolumnen tillgängliga LTR Backups. En ruta visas med en lista över tillgängliga säkerhets kopierings säkerhets kopior för den valda databasen.

   ![tillgängliga säkerhets kopierings upplevelser](./media/long-term-backup-retention-configure/ltr-available-backups-tab.png)

1. I rutan **tillgängliga säkerhets kopieringar** som visas granskar du de tillgängliga säkerhets kopiorna. Du kan välja en säkerhets kopia att återställa från eller ta bort.

   ![Visa tillgängliga LTR-säkerhetskopieringar](./media/long-term-backup-retention-configure/ltr-available-backups-manage.png)

1. Om du vill återställa från en tillgänglig LTR-säkerhetskopiering väljer du den säkerhets kopia som du vill återställa och väljer sedan **Återställ**.

   ![Återställ från tillgänglig LTR-säkerhetskopiering](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)

1. Välj ett namn för den nya databasen och välj sedan **Granska + skapa** för att granska informationen om återställningen. Välj **skapa** för att återställa databasen från den valda säkerhets kopian.

   ![Konfigurera information om återställning](./media/long-term-backup-retention-configure/restore-ltr.png)

1. I verktygsfältet väljer du meddelande ikonen för att visa status för återställnings jobbet.

   ![förlopp för återställningsjobb](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. När återställnings jobbet har slutförts öppnar du sidan **SQL-databaser** för att visa den nyligen återställda databasen.

> [!NOTE]
> Här kan du ansluta till den återställda databasen med hjälp av SQL Server Management Studio för att utföra nödvändiga åtgärder, till exempel [för att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller för att ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet](recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>Använda PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

Följande avsnitt visar hur du använder PowerShell för att konfigurera långsiktig kvarhållning av säkerhets kopior, Visa säkerhets kopior i Azure Storage och återställa från en säkerhets kopia i Azure Storage.

### <a name="azure-roles-to-manage-long-term-retention"></a>Azure-roller för att hantera långsiktig kvarhållning

För **Get-AzSqlDatabaseLongTermRetentionBackup** och **restore-AzSqlDatabase** måste du ha en av följande roller:

- Prenumerations ägarens roll eller
- SQL Server deltagar roll eller
- Anpassad roll med följande behörigheter:

   Microsoft. SQL/locations/longTermRetentionBackups/Read Microsoft. SQL/locations/longTermRetentionServers/longTermRetentionBackups/Read Microsoft. SQL/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Read

För **Remove-AzSqlDatabaseLongTermRetentionBackup** måste du ha en av följande roller:

- Prenumerations ägarens roll eller
- Anpassad roll med följande behörighet:

   Microsoft. SQL/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Delete

> [!NOTE]
> Rollen SQL Server Contributor har inte behörighet att ta bort LTR-säkerhetskopieringar.

Azure RBAC-behörigheter kan beviljas i antingen *prenumerations* -eller *resurs grupps* omfång. Men för att få åtkomst till LTR-säkerhets kopieringar som tillhör en tappad Server måste behörigheten beviljas i *prenumerations* omfånget för den servern.

- Microsoft. SQL/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Delete

### <a name="create-an-ltr-policy"></a>Skapa en LTR-princip

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Visa LTR-principer

Det här exemplet visar hur du visar en lista över LTR-principer inom en server

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
```

### <a name="clear-an-ltr-policy"></a>Rensa en LTR-princip

Det här exemplet visar hur du tar bort en LTR-princip från en databas

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Visa LTR-säkerhetskopieringar

I det här exemplet visas hur du visar en lista över säkerhets kopior på en server.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Ta bort LTR-säkerhetskopieringar

Det här exemplet visar hur du tar bort en LTR-säkerhetskopiering från listan över säkerhets kopior.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Borttagning av LTR-säkerhetskopiering går inte att ångra. Om du vill ta bort en LTR-säkerhetskopiering efter att servern har tagits bort måste du ha behörighet för prenumerations omfång. Du kan ställa in aviseringar om varje borttagning i Azure Monitor genom filtrering för åtgärd, tar bort en säkerhets kopia av långsiktig kvarhållning. Aktivitets loggen innehåller information om vem och när du har gjort begäran. Mer information finns i [skapa aktivitets logg aviseringar](../../azure-monitor/alerts/alerts-activity-log.md) .

### <a name="restore-from-ltr-backups"></a>Återställa från LTR-säkerhetskopieringar

Det här exemplet visar hur du återställer från en LTR-säkerhetskopiering. Obs! det här gränssnittet ändrades inte, men parametern resurs-ID kräver nu resurs-ID: t för säkerhets kopiering.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Om du vill återställa från en LTR-säkerhetskopiering när servern eller resurs gruppen har tagits bort, måste du ha behörighet som är begränsad till serverns prenumeration och den prenumerationen måste vara aktiv. Du måste också utelämna den valfria-ResourceGroupName-parametern.

> [!NOTE]
> Här kan du ansluta till den återställda databasen med hjälp av SQL Server Management Studio för att utföra nödvändiga åtgärder, till exempel för att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller för att ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet. Se tidpunkt [för återställning](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations"></a>Begränsningar
- När du återställer från en LTR-säkerhetskopiering är egenskapen Läs skalning inaktive rad. Om du vill aktivera, Läs skala på den återställda databasen uppdaterar du databasen när den har skapats.
- Du måste ange mål tjänst nivå målet när du återställer från en LTR-säkerhetskopiering, som skapades när databasen fanns i en elastisk pool. 

## <a name="next-steps"></a>Nästa steg

- Mer information om tjänstgenererade automatiska säkerhetskopior finns i avsnittet om [automatiska säkerhetskopior](automated-backups-overview.md)
- Mer information om långsiktig kvarhållning av säkerhetskopior finns i avsnittet om [långsiktig kvarhållning av säkerhetskopior](long-term-retention-overview.md)
