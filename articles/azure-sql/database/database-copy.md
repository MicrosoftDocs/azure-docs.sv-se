---
title: Kopiera en databas
description: Skapa en transaktions konsekvent kopia av en befintlig databas i Azure SQL Database antingen på samma server eller på en annan server.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sashan
ms.reviewer: ''
ms.date: 10/30/2020
ms.openlocfilehash: 7f053b1984a2d838deb14bacd10cdc071e19d8a1
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035146"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>Kopiera en transaktions konsekvent kopia av en databas i Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database tillhandahåller flera metoder för att skapa en kopia av en befintlig [databas](single-database-overview.md) på samma server eller en annan server. Du kan kopiera en databas med hjälp av Azure Portal, PowerShell, Azure CLI eller T-SQL.

## <a name="overview"></a>Översikt

En databas kopia är en transaktions konsekvent ögonblicks bild av käll databasen vid en tidpunkt efter att kopierings förfrågan har initierats. Du kan välja samma server eller en annan server för kopieringen. Du kan också välja att behålla säkerhets kopians redundans, tjänst nivå och beräknings storlek för käll databasen, eller använda en annan redundans för lagring och/eller beräknings storlek i samma eller en annan tjänst nivå. När kopieringen är klar blir den en fullständigt fungerande, oberoende databas. Inloggningar, användare och behörigheter i den kopierade databasen hanteras oberoende av käll databasen. Kopian skapas med hjälp av tekniken för geo-replikering. När replikseeding är klar avslutas geo-replikeringslänken automatiskt. Alla krav för användning av geo-replikering gäller för databaskopieringsåtgärden. Mer information finns i [Översikt över aktiv geo-replikering](active-geo-replication-overview.md) .

> [!NOTE]
> Azure SQL Database konfigurerbar redundans för säkerhets kopiering är för närvarande tillgängligt i offentlig för hands version i södra Brasilien och är allmänt tillgängligt i Sydostasien Azure-region. I för hands versionen stöds inte databas kopiering till en server i en annan Azure-region om käll databasen har skapats med lokalt redundant eller zon-redundant lagring för säkerhets kopiering. 

## <a name="logins-in-the-database-copy"></a>Inloggningar i databas kopian

När du kopierar en databas till samma server kan samma inloggningar användas på båda databaserna. Säkerhets objekt som du använder för att kopiera databasen blir databas ägaren på den nya databasen.

När du kopierar en databas till en annan server blir det säkerhets objekt som initierade kopierings åtgärden på mål servern ägare till den nya databasen.

Oavsett mål servern kopieras alla databas användare, deras behörigheter och deras säkerhets identifierare (sid) till databas kopian. Om du använder [inneslutna databas användare](logins-create-manage.md) för data åtkomst säkerställer du att den kopierade databasen har samma användarautentiseringsuppgifter, så att när kopieringen är klar kan du omedelbart komma åt den med samma autentiseringsuppgifter.

Om du använder inloggningar på servernivå för dataåtkomst och kopierar databasen till en annan server kanske inte den inloggningsbaserade åtkomsten fungerar. Detta kan inträffa eftersom inloggningarna inte finns på målservern, eller på grund av att deras lösenord och säkerhetsidentifierare (SID) skiljer sig åt. Information om hur du hanterar inloggningar när du kopierar en databas till en annan server finns i [hantera Azure SQL Database säkerhet efter haveri beredskap](active-geo-replication-security-configure.md). När kopierings åtgärden till en annan server lyckas och innan andra användare mappas om, är det bara inloggningen som är kopplad till databas ägaren eller så kan Server administratören logga in på den kopierade databasen. Information om hur du löser inloggningar och upprättar data åtkomst när kopieringen är klar finns i [lösa inloggningar](#resolve-logins).

## <a name="copy-using-the-azure-portal"></a>Kopiera med Azure Portal

Om du vill kopiera en databas med hjälp av Azure Portal öppnar du sidan för databasen och klickar sedan på **Kopiera**.

   ![Databaskopia](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>Kopiera med PowerShell eller Azure CLI

Använd följande exempel för att kopiera en databas.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För PowerShell använder du cmdleten [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) .

> [!IMPORTANT]
> PowerShell-modulen Azure Resource Manager (RM) stöds fortfarande av Azure SQL Database, men all framtida utveckling är för AZ. SQL-modulen. AzureRM-modulen kommer att fortsätta att ta emot fel korrigeringar fram till minst december 2020.  Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell AZ-modulen](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Databas kopieringen är en asynkron åtgärd, men mål databasen skapas direkt efter att begäran har godkänts. Om du vill avbryta kopieringen medan du fortfarande pågår släpper du mål databasen med hjälp av cmdleten [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) .

Ett fullständigt exempel på PowerShell-skript finns i [Kopiera en databas till en ny server](scripts/copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Databas kopieringen är en asynkron åtgärd, men mål databasen skapas direkt efter att begäran har godkänts. Om du vill avbryta kopieringen medan du fortfarande pågår släpper du mål databasen med kommandot [AZ SQL DB Delete](/cli/azure/sql/db#az-sql-db-delete) .

* * *

## <a name="copy-using-transact-sql"></a>Kopiera med Transact-SQL

Logga in på huvud databasen med Server Administratörs inloggning eller inloggning som skapade databasen som du vill kopiera. För att databas kopieringen ska lyckas måste inloggningar som inte är Server administratör vara medlemmar i `dbmanager` rollen. Mer information om inloggningar och anslutning till-servern finns i [Hantera inloggningar](logins-create-manage.md).

Starta kopieringen av käll databasen med [create-databasen... SOM en kopia av](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true#copy-a-database) instruktionen. T-SQL-instruktionen fortsätter att köras tills databas kopierings åtgärden har slutförts.

> [!NOTE]
> Om du avslutar T-SQL-instruktionen avbryts inte databas kopierings åtgärden. Avsluta åtgärden genom att släppa mål databasen.
>

> [!IMPORTANT]
> Välj redundans för säkerhets kopiering när du använder T-SQL CREATE DATABASE... Det finns inte stöd för att kopiera kommandot ännu. 

### <a name="copy-to-the-same-server"></a>Kopiera till samma server

Logga in på huvud databasen med Server Administratörs inloggning eller inloggning som skapade databasen som du vill kopiera. För att kopieringen av databasen ska lyckas måste inloggningar som inte är Server administratör vara medlemmar i `dbmanager` rollen.

Detta kommando kopierar Databas1 till en ny databas med namnet Databas2 på samma server. Beroende på databasens storlek kan kopierings åtgärden ta lite tid att slutföra.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-an-elastic-pool"></a>Kopiera till en elastisk pool

Logga in på huvud databasen med Server Administratörs inloggning eller inloggning som skapade databasen som du vill kopiera. För att kopieringen av databasen ska lyckas måste inloggningar som inte är Server administratör vara medlemmar i `dbmanager` rollen.

Detta kommando kopierar Databas1 till en ny databas med namnet Databas2 i en elastisk pool med namnet pool1. Beroende på databasens storlek kan kopierings åtgärden ta lite tid att slutföra.

Databas1 kan vara en enskild databas eller en databas i en pool. Kopiering mellan olika nivåer stöds, men vissa kopior på flera nivåer kommer inte att lyckas. Du kan till exempel kopiera en enkel eller elastisk standard databas till en generell användnings pool, men du kan inte kopiera en standard elastisk databas till en Premium-pool. 

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE "Database2"
   AS COPY OF "Database1"
   (SERVICE_OBJECTIVE = ELASTIC_POOL( name = "pool1" ) ) ;
   ```

### <a name="copy-to-a-different-server"></a>Kopiera till en annan server

Logga in på huvud databasen på mål servern där den nya databasen ska skapas. Använd en inloggning som har samma namn och lösen ord som databas ägaren till käll databasen på käll servern. Inloggningen på mål servern måste också vara medlem i `dbmanager` rollen eller vara Server Administratörs inloggning.

Detta kommando kopierar Databas1 på Server1 till en ny databas med namnet Databas2 på Server2. Beroende på databasens storlek kan kopierings åtgärden ta lite tid att slutföra.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Båda servrarnas brand väggar måste konfigureras för att tillåta inkommande anslutning från IP-adressen för klienten som utfärdar T-SQL CREATE-databasen... SOM en kopia av kommandot.

### <a name="copy-to-a-different-subscription"></a>Kopiera till en annan prenumeration

Du kan använda stegen i avsnittet [Kopiera en SQL Database till en annan server](#copy-to-a-different-server) för att kopiera databasen till en server i en annan prenumeration med hjälp av T-SQL. Se till att du använder en inloggning som har samma namn och lösen ord som databas ägaren till käll databasen. Dessutom måste inloggningen vara medlem i `dbmanager` rollen eller en Server administratör på både käll-och mål servrar.

```sql
Step# 1
Create login and user in the master database of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx'
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo]
GO

Step# 2
Create the user in the source database and grant dbowner permission to the database.

CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo]
GO
exec sp_addrolemember 'db_owner','loginname'
GO

Step# 3
Capture the SID of the user “loginname” from master database

SELECT [sid] FROM sysusers WHERE [name] = 'loginname'

Step# 4
Connect to Destination server.
Create login and user in the master database, same as of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx', SID = [SID of loginname login on source server]
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo]
GO
exec sp_addrolemember 'dbmanager','loginname'
GO

Step# 5
Execute the copy of database script from the destination server using the credentials created

CREATE DATABASE new_database_name
AS COPY OF source_server_name.source_database_name
```

> [!NOTE]
> [Azure Portal](https://portal.azure.com), PowerShell och Azure CLI stöder inte databas kopiering till en annan prenumeration.

> [!TIP]
> Databas kopiering med T-SQL stöder kopiering av en databas från en prenumeration i en annan Azure-klient. Detta stöds endast när du använder en SQL-autentisering för inloggning för att logga in på mål servern.

## <a name="monitor-the-progress-of-the-copying-operation"></a>Övervaka förloppet för kopierings åtgärden

Övervaka kopierings processen genom att skicka frågor till vyerna [sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys.dm_database_copies](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)och [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) . När kopieringen pågår är kolumnen **state_desc** i vyn sys. Databass för den nya databasen som **kopia.**

* Om kopieringen Miss lyckas är kolumnen **state_desc** i vyn sys. Databass för den nya databasen **misstänkt**. Kör DROP-instruktionen på den nya databasen och försök igen senare.
* Om kopieringen lyckas anges kolumnen **state_desc** i vyn sys. Databass för den nya databasen som **online**. Kopieringen är klar och den nya databasen är en vanlig databas som kan ändras oberoende av käll databasen.

> [!NOTE]
> Om du väljer att avbryta kopieringen medan den pågår kör du [Drop Database](/sql/t-sql/statements/drop-database-transact-sql) -instruktionen på den nya databasen.

> [!IMPORTANT]
> Om du behöver skapa en kopia med ett betydligt mindre tjänst mål än källan, kanske mål databasen inte har tillräckligt med resurser för att slutföra initierings processen och det kan leda till att kopieringen Miss lyckas. I det här scenariot använder du en geo-Restore-begäran för att skapa en kopia på en annan server och/eller en annan region. Mer information finns i [återställa en Azure SQL Database med hjälp av databas säkerhets kopior](recovery-using-backups.md#geo-restore) .

## <a name="azure-roles-to-manage-database-copy"></a>Azure-roller för att hantera databas kopiering

Om du vill skapa en databas kopia måste du ha följande roller

* Prenumerations ägare eller
* SQL Server deltagar roll eller
* Anpassad roll för käll-och mål databaserna med följande behörighet:

   Microsoft. SQL/Servers/databaser/läsa Microsoft. SQL/Servers/databaser/skriva

Om du vill avbryta en databas kopia måste du ha följande roller

* Prenumerations ägare eller
* SQL Server deltagar roll eller
* Anpassad roll för käll-och mål databaserna med följande behörighet:

   Microsoft. SQL/Servers/databaser/läsa Microsoft. SQL/Servers/databaser/skriva

Om du vill hantera databas kopieringen med hjälp av Azure Portal måste du också ha följande behörigheter:

   Microsoft. Resources/Subscriptions/resurser/Läs Microsoft. Resources/Subscriptions/Resources/Write Microsoft. Resources/Deployments/Read Microsoft. Resources/Deployments/Write Microsoft. Resources/operationstatuses/Read

Om du vill se åtgärder under distributioner i resurs gruppen på portalen, måste du ha följande ytterligare Azure-roller för åtgärder i flera olika resurs leverantörer, inklusive SQL-åtgärder:

   Microsoft. Resources/Subscriptions/ResourceGroups/Deployments/Operations/Read Microsoft. Resources/Subscriptions/ResourceGroups/distributions/operationstatuses/Read

## <a name="resolve-logins"></a>Lösa inloggningar

När den nya databasen är online på mål servern använder du instruktionen [Alter User](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current&preserve-view=true) för att mappa om användarna från den nya databasen till inloggningar på mål servern. Information om hur du löser överblivna användare finns i [Felsöka överblivna användare](/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server). Se även [hur du hanterar Azure SQL Database säkerhet efter haveri beredskap](active-geo-replication-security-configure.md).

Alla användare i den nya databasen behåller de behörigheter som de hade i käll databasen. Den användare som initierade databas kopian blir databas ägaren till den nya databasen. När kopieringen är klar och innan andra användare mappas om kan bara databas ägaren logga in på den nya databasen.

Information om hur du hanterar användare och inloggningar när du kopierar en databas till en annan server finns i [hantera Azure SQL Database säkerhet efter haveri beredskap](active-geo-replication-security-configure.md).

## <a name="database-copy-errors"></a>Databas kopierings fel

Följande fel kan uppstå när du kopierar en databas i Azure SQL Database. Mer information finns i [Kopiera en Azure SQL Database](database-copy.md).

| Felkod | Allvarlighetsgrad | Beskrivning |
| ---:| ---:|:--- |
| 40635 |16 |Klienten med IP-adressen%. &#x2a;LS är tillfälligt inaktive rad. |
| 40637 |16 |Skapa databas kopiering har inaktiverats för tillfället. |
| 40561 |16 |Databas kopieringen misslyckades. Käll-eller mål databasen finns inte. |
| 40562 |16 |Databas kopieringen misslyckades. Käll databasen har släppts. |
| 40563 |16 |Databas kopieringen misslyckades. Mål databasen har släppts. |
| 40564 |16 |Databas kopieringen misslyckades på grund av ett internt fel. Släpp mål databasen och försök igen. |
| 40565 |16 |Databas kopieringen misslyckades. Högst 1 samtidiga databas kopiering från samma källa tillåts. Släpp mål databasen och försök igen senare. |
| 40566 |16 |Databas kopieringen misslyckades på grund av ett internt fel. Släpp mål databasen och försök igen. |
| 40567 |16 |Databas kopieringen misslyckades på grund av ett internt fel. Släpp mål databasen och försök igen. |
| 40568 |16 |Databas kopieringen misslyckades. Käll databasen är inte tillgänglig. Släpp mål databasen och försök igen. |
| 40569 |16 |Databas kopieringen misslyckades. Mål databasen är inte tillgänglig. Släpp mål databasen och försök igen. |
| 40570 |16 |Databas kopieringen misslyckades på grund av ett internt fel. Släpp mål databasen och försök igen senare. |
| 40571 |16 |Databas kopieringen misslyckades på grund av ett internt fel. Släpp mål databasen och försök igen senare. |

## <a name="next-steps"></a>Nästa steg

* Information om inloggningar finns i [Hantera inloggningar](logins-create-manage.md) och [Hantera Azure SQL Database säkerhet efter haveri beredskap](active-geo-replication-security-configure.md).
* Information om hur du exporterar en databas finns i [Exportera databasen till en BACPAC](database-export.md).
