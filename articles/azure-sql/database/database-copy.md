---
title: Kopiera en databas
description: Skapa en transaktionellt konsekvent kopia av en befintlig databas Azure SQL Database på samma server eller en annan server.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sashan
ms.reviewer: wiassaf
ms.date: 03/10/2021
ms.openlocfilehash: b7084ef045d14b9715c41bb9ffa483d1f2f7bedf
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865161"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>Kopiera en transaktionellt konsekvent kopia av en databas i Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database flera metoder för att skapa en kopia av en [befintlig databas](single-database-overview.md) på samma server eller en annan server. Du kan kopiera en databas med Azure Portal, PowerShell, Azure CLI eller T-SQL.

## <a name="overview"></a>Översikt

En databaskopia är en transaktionsmässigt konsekvent ögonblicksbild av källdatabasen från och med en tidpunkt efter att kopieringsbegäran har initierats. Du kan välja samma server eller en annan server för kopian. Du kan också välja att behålla redundans för säkerhetskopiering, tjänstnivå och beräkningsstorlek för källdatabasen, eller använda en annan lagringsredundans för säkerhetskopiering och/eller beräkningsstorlek inom samma eller en annan tjänstnivå. När kopieringen är klar blir den en helt funktionell, oberoende databas. Inloggningar, användare och behörigheter i den kopierade databasen hanteras oberoende av källdatabasen. Kopian skapas med hjälp av geo-replikeringstekniken. När replikseeding är klar avslutas geo-replikeringslänken automatiskt. Alla krav för användning av geo-replikering gäller för databaskopieringsåtgärden. Mer [information finns i Översikt över aktiv geo-replikering.](active-geo-replication-overview.md)

> [!NOTE]
> Azure SQL Database Redundans för konfigurerbar säkerhetskopieringslagring är för närvarande tillgänglig i offentlig förhandsversion i Brasilien, södra och är allmänt tillgänglig endast i Sydostasien Azure-regionen. Om källdatabasen i förhandsversionen har skapats med lokalt redundant eller zonredundant redundans för säkerhetskopiering stöds inte databaskopiering till en server i en annan Azure-region. 

## <a name="logins-in-the-database-copy"></a>Inloggningar i databaskopian

När du kopierar en databas till samma server kan samma inloggningar användas på båda databaserna. Säkerhetsobjekt som du använder för att kopiera databasen blir databasägaren för den nya databasen.

När du kopierar en databas till en annan server blir det säkerhetsobjekt som initierade kopieringen på målservern ägare till den nya databasen.

Oavsett målserver kopieras alla databasanvändare, deras behörigheter och deras säkerhetsidentifierare (SID) till databaskopian. Om [du använder inneslutna](logins-create-manage.md) databasanvändare för dataåtkomst ser du till att den kopierade databasen har samma användarautentiseringsuppgifter, så att du omedelbart kan komma åt den med samma autentiseringsuppgifter när kopieringen är klar.

Om du använder inloggningar på servernivå för dataåtkomst och kopierar databasen till en annan server kanske inte den inloggningsbaserade åtkomsten fungerar. Detta kan inträffa eftersom inloggningarna inte finns på målservern, eller på grund av att deras lösenord och säkerhetsidentifierare (SID) skiljer sig åt. Mer information om hur du hanterar inloggningar när du kopierar en databas till en annan server finns i Hantera [Azure SQL Database säkerhet efter haveriberedskap.](active-geo-replication-security-configure.md) När kopieringen till en annan server lyckas och innan andra användare mappas om kan endast den inloggning som är associerad med databasägaren eller serveradministratören logga in på den kopierade databasen. Information om hur du löser inloggningar och upprättar dataåtkomst när kopieringen är klar finns i [Lösa inloggningar.](#resolve-logins)

## <a name="copy-using-the-azure-portal"></a>Kopiera med Azure Portal

Om du vill kopiera en databas Azure Portal databasen öppnar du sidan för databasen och klickar sedan på **Kopiera.**

   ![Databaskopia](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>Kopiera med PowerShell eller Azure CLI

Använd följande exempel för att kopiera en databas.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För PowerShell använder du cmdleten [New-AzSqlDatabaseCopy.](/powershell/module/az.sql/new-azsqldatabasecopy)

> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager (RM) stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. AzureRM-modulen fortsätter att ta emot felkorrigeringar fram till åtminstone december 2020.  Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är betydligt identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Databaskopian är en asynkron åtgärd, men måldatabasen skapas omedelbart efter att begäran har godkänts. Om du behöver avbryta kopieringsåtgärden medan den pågår släpper du måldatabasen med cmdleten [Remove-AzSqlDatabase.](/powershell/module/az.sql/new-azsqldatabase)

Ett fullständigt PowerShell-exempelskript finns i [Kopiera en databas till en ny server.](scripts/copy-database-to-new-server-powershell.md)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Databaskopian är en asynkron åtgärd, men måldatabasen skapas omedelbart efter att begäran har godkänts. Om du behöver avbryta kopieringsåtgärden medan den fortfarande pågår släpper du måldatabasen med kommandot [az sql db delete.](/cli/azure/sql/db#az_sql_db_delete)

* * *

## <a name="copy-using-transact-sql"></a>Kopiera med Transact-SQL

Logga in på huvuddatabasen med serveradministratörsinloggningen eller inloggningen som skapade databasen som du vill kopiera. För att databaskopiering ska lyckas måste inloggningar som inte är serveradministratör vara medlemmar i `dbmanager` rollen. Mer information om inloggningar och anslutning till servern finns i [Hantera inloggningar.](logins-create-manage.md)

Börja kopiera källdatabasen med [CREATE DATABASE ... SOM COPY OF-instruktion.](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true#copy-a-database) T-SQL-instruktionen fortsätter att köras tills databaskopieringsåtgärden har slutförts.

> [!NOTE]
> Om du avslutar T-SQL-instruktionen avslutas inte databaskopieringsåtgärden. Om du vill avsluta åtgärden tar du bort måldatabasen.
>

> [!IMPORTANT]
> Välja redundans för säkerhetskopieringslagring när du använder T-SQL CREATE DATABASE ... AS COPY OF-kommandot stöds inte ännu. 

### <a name="copy-to-the-same-server"></a>Kopiera till samma server

Logga in på huvuddatabasen med serveradministratörsinloggningen eller inloggningen som skapade databasen som du vill kopiera. För att databaskopiering ska lyckas måste inloggningar som inte är serveradministratör vara medlemmar i `dbmanager` rollen.

Det här kommandot kopierar Database1 till en ny databas med namnet Database2 på samma server. Beroende på databasens storlek kan kopieringsåtgärden ta lite tid att slutföra.

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-an-elastic-pool"></a>Kopiera till en elastisk pool

Logga in på huvuddatabasen med serveradministratörsinloggningen eller inloggningen som skapade databasen som du vill kopiera. För att databaskopiering ska lyckas måste inloggningar som inte är serveradministratör vara medlemmar i `dbmanager` rollen.

Det här kommandot kopierar Database1 till en ny databas med namnet Database2 i en elastisk pool med namnet pool1. Beroende på databasens storlek kan kopieringsåtgärden ta lite tid att slutföra.

Database1 kan vara en enkel databas eller en pooldatabas. Kopiering mellan olika nivåpooler stöds, men vissa kopior mellan nivåer kommer inte att lyckas. Du kan till exempel kopiera en enkel eller elastisk standard-databas till en pool för generell användning, men du kan inte kopiera en elastisk standard-databas till en Premium-pool. 

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE "Database2"
   AS COPY OF "Database1"
   (SERVICE_OBJECTIVE = ELASTIC_POOL( name = "pool1" ) );
   ```

### <a name="copy-to-a-different-server"></a>Kopiera till en annan server

Logga in på målserverns huvuddatabas där den nya databasen ska skapas. Använd en inloggning som har samma namn och lösenord som databasägaren för källdatabasen på källservern. Inloggningen på målservern måste också vara medlem i rollen `dbmanager` eller vara serveradministratörens inloggning.

Det här kommandot kopierar Database1 på server1 till en ny databas med namnet Database2 på server2. Beroende på databasens storlek kan kopieringsåtgärden ta lite tid att slutföra.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Båda servrarna brandväggar måste konfigureras för att tillåta inkommande anslutning från IP-adressen för den klient som utfärdar T-SQL CREATE DATABASE ... SOM KOPIA AV kommandot.

### <a name="copy-to-a-different-subscription"></a>Kopiera till en annan prenumeration

Du kan använda stegen i avsnittet [Kopiera en SQL Database](#copy-to-a-different-server) till en annan server för att kopiera databasen till en server i en annan prenumeration med hjälp av T-SQL. Se till att du använder en inloggning som har samma namn och lösenord som databasägaren för källdatabasen. Dessutom måste inloggningen vara medlem i rollen eller `dbmanager` en serveradministratör på både käll- och målservrar.

```sql
--Step# 1
--Create login and user in the master database of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx'
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 2
--Create the user in the source database and grant dbowner permission to the database.

CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE db_owner ADD MEMBER loginname;
GO

--Step# 3
--Capture the SID of the user "loginname" from master database

SELECT [sid] FROM sysusers WHERE [name] = 'loginname';

--Step# 4
--Connect to Destination server.
--Create login and user in the master database, same as of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx', SID = [SID of loginname login on source server];
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 5
--Execute the copy of database script from the destination server using the credentials created

CREATE DATABASE new_database_name
AS COPY OF source_server_name.source_database_name;
```

> [!NOTE]
> Databasen [Azure Portal,](https://portal.azure.com)PowerShell och Azure CLI stöder inte databaskopiering till en annan prenumeration.

> [!TIP]
> Databaskopiering med T-SQL stöder kopiering av en databas från en prenumeration i en annan Azure-klientorganisation. Detta stöds endast när du använder en SQL-autentiseringsinloggning för att logga in på målservern.

## <a name="monitor-the-progress-of-the-copying-operation"></a>Övervaka kopieringsåtgärdens förlopp

Övervaka kopieringsprocessen genom att fråga [sys.databases,](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) [sys.dm_database_copies](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)och [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) vyer. Medan kopieringen pågår anges **state_desc** sys.databases-vyn för den nya databasen till **COPYING (KOPIERA).**

* Om kopieringen misslyckas anges **state_desc** i vyn sys.databases för den nya databasen till **SUSPECT**. Kör DROP-instruktionen på den nya databasen och försök igen senare.
* Om kopieringen lyckas anges **state_desc** i vyn sys.databases för den nya databasen till **ONLINE.** Kopieringen är klar och den nya databasen är en vanlig databas som kan ändras oberoende av källdatabasen.

> [!NOTE]
> Om du vill avbryta kopieringen medan den pågår kör du [DROP DATABASE-instruktionen](/sql/t-sql/statements/drop-database-transact-sql) på den nya databasen.

> [!IMPORTANT]
> Om du behöver skapa en kopia med ett betydligt mindre tjänstmål än källan, kanske måldatabasen inte har tillräckligt med resurser för att slutföra seeding-processen och det kan leda till att kopieringsåtgärden misslyckas. I det här scenariot använder du en begäran om geo-återställning för att skapa en kopia på en annan server och/eller i en annan region. Se [Återställa en Azure SQL Database med hjälp av databassäkerhetskopior](recovery-using-backups.md#geo-restore) för mer information.

## <a name="azure-rbac-roles-and-permissions-to-manage-database-copy"></a>Azure RBAC-roller och behörigheter för att hantera databaskopiering

Om du vill skapa en databaskopia måste du ha följande roller

* Prenumerationsägare eller
* SQL Server rollen deltagare eller
* Anpassad roll på käll- och måldatabaserna med följande behörighet:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Om du vill avbryta en databaskopia måste du ha följande roller

* Prenumerationsägare eller
* SQL Server rollen deltagare eller
* Anpassad roll på käll- och måldatabaserna med följande behörighet:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Om du vill hantera databaskopiering Azure Portal databasen måste du också ha följande behörigheter:

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

Om du vill se åtgärderna under distributioner i resursgruppen på portalen, åtgärder över flera resursproviders, inklusive SQL-åtgärder, behöver du följande ytterligare behörigheter:

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="resolve-logins"></a>Lösa inloggningar

När den nya databasen är online på målservern använder du [ALTER USER-instruktionen](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current&preserve-view=true) för att mappa om användarna från den nya databasen till inloggningar på målservern. Information om hur du löser överblivna användare finns [i Felsöka överblivna användare.](/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server) Se även [Hantera Azure SQL Database efter haveriberedskap.](active-geo-replication-security-configure.md)

Alla användare i den nya databasen behåller de behörigheter som de hade i källdatabasen. Den användare som initierade databaskopian blir databasägare för den nya databasen. När kopieringen har lyckats och innan andra användare mappas om kan endast databasägaren logga in på den nya databasen.

Mer information om hur du hanterar användare och inloggningar när du kopierar en databas till en annan server finns i Hantera [Azure SQL Database säkerhet efter haveriberedskap.](active-geo-replication-security-configure.md)

## <a name="database-copy-errors"></a>Fel vid databaskopiering

Följande fel kan uppstå vid kopiering av en databas i Azure SQL Database. Mer information finns i [Kopiera en Azure SQL Database](database-copy.md).

| Felkod | Allvarlighetsgrad | Beskrivning |
| ---:| ---:|:--- |
| 40635 |16 |Klienten med IP-adressen %.&#x2a;ls är tillfälligt inaktiverad. |
| 40637 |16 |Skapa databaskopia är för närvarande inaktiverat. |
| 40561 |16 |Databaskopiering misslyckades. Käll- eller måldatabasen finns inte. |
| 40562 |16 |Databaskopiering misslyckades. Källdatabasen har tagits bort. |
| 40563 |16 |Databaskopiering misslyckades. Måldatabasen har tagits bort. |
| 40564 |16 |Databaskopian misslyckades på grund av ett internt fel. Ta bort måldatabasen och försök igen. |
| 40565 |16 |Databaskopiering misslyckades. Högst 1 samtidig databaskopiering från samma källa tillåts. Ta bort måldatabasen och försök igen senare. |
| 40566 |16 |Databaskopiering misslyckades på grund av ett internt fel. Ta bort måldatabasen och försök igen. |
| 40567 |16 |Databaskopiering misslyckades på grund av ett internt fel. Ta bort måldatabasen och försök igen. |
| 40568 |16 |Databaskopiering misslyckades. Källdatabasen har blivit otillgänglig. Ta bort måldatabasen och försök igen. |
| 40569 |16 |Databaskopiering misslyckades. Måldatabasen har blivit otillgänglig. Ta bort måldatabasen och försök igen. |
| 40570 |16 |Databaskopiering misslyckades på grund av ett internt fel. Ta bort måldatabasen och försök igen senare. |
| 40571 |16 |Databaskopiering misslyckades på grund av ett internt fel. Ta bort måldatabasen och försök igen senare. |

## <a name="next-steps"></a>Nästa steg

* Information om inloggningar finns i [Hantera inloggningar och Hantera Azure SQL Database](logins-create-manage.md) säkerhet efter [haveriberedskap.](active-geo-replication-security-configure.md)
* Information om hur du exporterar en [databas finns i Exportera databasen till en BACPAC.](database-export.md)
