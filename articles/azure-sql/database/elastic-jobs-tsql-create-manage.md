---
title: Skapa och hantera Elastic Database jobb (för hands version) med Transact-SQL (T-SQL)
description: Kör skript över flera databaser med Elastic Database Job agent med hjälp av Transact-SQL (T-SQL).
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
dev_langs:
- TSQL
ms.topic: how-to
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
ms.date: 02/01/2021
ms.openlocfilehash: 11b94ba5bcedf56f0115b8730dc58f808aff5c58
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100371608"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs-preview"></a>Använd Transact-SQL (T-SQL) för att skapa och hantera Elastic Database jobb (förhands granskning)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här artikeln innehåller många exempel scenarier som hjälper dig att komma igång med elastiska jobb med T-SQL.

I exemplen används [lagrade procedurer](#job-stored-procedures) och [vyer](#job-views) som är tillgängliga i [*jobb databasen*](job-automation-overview.md#elastic-job-database).

Transact-SQL (T-SQL) används för att skapa, konfigurera, köra och hantera jobb. Det går inte att skapa en elastisk jobb agent i T-SQL, så du måste först skapa en *elastisk jobb agent* med hjälp av portalen eller [PowerShell](elastic-jobs-powershell-create.md#create-the-elastic-job-agent).

## <a name="create-a-credential-for-job-execution"></a>Skapa en autentiseringsuppgift för jobb körning

Autentiseringsuppgiften används för att ansluta till mål databaserna för skript körning. Autentiseringsuppgiften behöver rätt behörigheter, på de databaser som anges av mål gruppen, för att kunna köra skriptet. När du använder en logisk grupp medlem för [SQL Server](logical-servers.md) och/eller grupp medlemmar, rekommenderar vi starkt att du skapar en autentiseringsuppgift för att uppdatera autentiseringsuppgifterna innan du expanderar servern och/eller poolen vid jobb körningen. Databasens begränsade autentiseringsuppgifter skapas på jobb agents databasen. Samma autentiseringsuppgift måste användas för att *skapa en inloggning* och *skapa en användare från inloggning för att ge behörighet för inloggnings databasen* på mål databaserna.

```sql
--Connect to the new job database specified when creating the Elastic Job agent

-- Create a database master key if one does not already exist, using your own password.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';  
  
-- Create two database scoped credentials.  
-- The credential to connect to the Azure SQL logical server, to execute jobs
CREATE DATABASE SCOPED CREDENTIAL job_credential WITH IDENTITY = 'job_credential',
    SECRET = '<EnterStrongPasswordHere>';
GO
-- The credential to connect to the Azure SQL logical server, to refresh the database metadata in server
CREATE DATABASE SCOPED CREDENTIAL refresh_credential WITH IDENTITY = 'refresh_credential',
    SECRET = '<EnterStrongPasswordHere>';
GO
```

## <a name="create-a-target-group-servers"></a>Skapa en mål grupp (servrar)

I följande exempel visas hur du kör ett jobb mot alla databaser på en server.  
Anslut till [*jobb databasen*](job-automation-overview.md#elastic-job-database) och kör följande kommando:

```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1';

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = 'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name = 'refresh_credential', --credential required to refresh the databases in a server
@server_name = 'server1.database.windows.net';

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```

## <a name="exclude-an-individual-database"></a>Undanta en enskild databas

I följande exempel visas hur du kör ett jobb mot alla databaser på en server, förutom databasen med namnet *MappingDB*.  
Anslut till [*jobb databasen*](job-automation-overview.md#elastic-job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup';
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name = N'refresh_credential', --credential required to refresh the databases in a server
@server_name = N'London.database.windows.net';
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name = N'refresh_credential', --credential required to refresh the databases in a server
@server_name = 'server2.database.windows.net';
GO

--Exclude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name = N'MappingDB';
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```

## <a name="create-a-target-group-pools"></a>Skapa en mål grupp (pooler)

I följande exempel visas hur du riktar in alla databaser i en eller flera elastiska pooler.  
Anslut till [*jobb databasen*](job-automation-overview.md#elastic-job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup';

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
@target_group_name = 'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name = 'refresh_credential', --credential required to refresh the databases in a server
@server_name = 'server1.database.windows.net',
@elastic_pool_name = 'ElasticPool-1';

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```

## <a name="deploy-new-schema-to-many-databases"></a>Distribuera nytt schema till många databaser

I följande exempel visas hur du distribuerar nytt schema till alla databaser.  
Anslut till [*jobb databasen*](job-automation-overview.md#elastic-job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name = 'CreateTableTest', @description = 'Create Table Test';

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name = 'CreateTableTest',
@command = N'IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name = 'job_credential',
@target_group_name = 'PoolGroup';
```

## <a name="data-collection-using-built-in-parameters"></a>Data insamling med inbyggda parametrar

I många scenarier för data insamling kan det vara praktiskt att inkludera vissa av dessa skript-variabler för att få hjälp med att bearbeta resultatet av jobbet.

- $ (job_name)
- $ (job_id)
- $ (job_version)
- $ (step_id)
- $ (step_name)
- $ (job_execution_id)
- $ (job_execution_create_time)
- $ (target_group_name)

Om du till exempel vill gruppera alla resultat från samma jobb körning tillsammans använder du *$ (job_execution_id)* som du ser i följande kommando:

```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```

## <a name="monitor-database-performance"></a>Övervaka databasprestanda

I följande exempel skapas ett nytt jobb för att samla in prestanda data från flera databaser.

Som standard skapar jobb agenten output-tabellen för att lagra returnerade resultat. Därför har databasens huvud namn som är associerat med autentiseringsuppgifterna för utdata minst följande behörigheter: `CREATE TABLE` på-databasen, `ALTER` ,, `SELECT` `INSERT` , `DELETE` i utdatatabellen eller schemat, och `SELECT` i vyn [sys. indexs](/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql) .

Om du vill skapa tabellen manuellt i förväg måste du ha följande egenskaper:

1. Kolumner med rätt namn och data typer för resultat uppsättningen.
2. Ytterligare kolumn för internal_execution_id med data typen UniqueIdentifier.
3. Ett grupperat index med namnet `IX_<TableName>_Internal_Execution_ID` i kolumnen internal_execution_id.
4. Alla behörigheter som anges ovan förutom `CREATE TABLE` behörighet för databasen.

Anslut till [*jobb databasen*](job-automation-overview.md#elastic-job-database) och kör följande kommandon:

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name = 'ResultsJob',
@command = N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name = 'job_credential',
@target_group_name = 'PoolGroup',
@output_type = 'SqlDatabase',
@output_credential_name = 'job_credential',
@output_server_name = 'server1.database.windows.net',
@output_database_name = '<resultsdb>',
@output_table_name = '<resutlstable>';

--Create a job to monitor pool performance

--Connect to the job database specified when creating the job agent

-- Add a target group containing Elastic Job database
EXEC jobs.sp_add_target_group 'ElasticJobGroup';

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = 'ElasticJobGroup',
@target_type = 'SqlDatabase',
@server_name = 'server1.database.windows.net',
@database_name = 'master';

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name = 'ResultsPoolsJob',
@description = 'Demo: Collection Performance data from all pools',
@schedule_interval_type = 'Minutes',
@schedule_interval_count = 15;

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsPoolsJob',
@command=N'declare @now datetime
DECLARE @startTime datetime
DECLARE @endTime datetime
DECLARE @poolLagMinutes datetime
DECLARE @poolStartTime datetime
DECLARE @poolEndTime datetime
SELECT @now = getutcdate ()
SELECT @startTime = dateadd(minute, -15, @now)
SELECT @endTime = @now
SELECT @poolStartTime = dateadd(minute, -30, @startTime)
SELECT @poolEndTime = dateadd(minute, -30, @endTime)

SELECT elastic_pool_name , end_time, elastic_pool_dtu_limit, avg_cpu_percent, avg_data_io_percent, avg_log_write_percent, max_worker_percent, max_session_percent,
        avg_storage_percent, elastic_pool_storage_limit_mb FROM sys.elastic_pool_resource_stats
        WHERE end_time > @poolStartTime and end_time <= @poolEndTime;
'),
@credential_name = 'job_credential',
@target_group_name = 'ElasticJobGroup',
@output_type = 'SqlDatabase',
@output_credential_name = 'job_credential',
@output_server_name = 'server1.database.windows.net',
@output_database_name = 'resultsdb',
@output_table_name = 'resutlstable';
```

## <a name="view-job-definitions"></a>Visa jobb definitioner

I följande exempel visas hur du visar aktuella jobb definitioner.  
Anslut till [*jobb databasen*](job-automation-overview.md#elastic-job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

-- View all jobs
SELECT * FROM jobs.jobs;

-- View the steps of the current version of all jobs
SELECT js.* FROM jobs.jobsteps js
JOIN jobs.jobs j
  ON j.job_id = js.job_id AND j.job_version = js.job_version;

-- View the steps of all versions of all jobs
SELECT * FROM jobs.jobsteps;
```

## <a name="begin-unplanned-execution-of-a-job"></a>Påbörja oplanerad körning av ett jobb

I följande exempel visas hur du startar ett jobb direkt.  
Anslut till [*jobb databasen*](job-automation-overview.md#elastic-job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

-- Execute the latest version of a job
EXEC jobs.sp_start_job 'CreateTableTest';

-- Execute the latest version of a job and receive the execution id
declare @je uniqueidentifier;
exec jobs.sp_start_job 'CreateTableTest', @job_execution_id = @je output;
select @je;

select * from jobs.job_executions where job_execution_id = @je;

-- Execute a specific version of a job (e.g. version 1)
exec jobs.sp_start_job 'CreateTableTest', 1;
```

## <a name="schedule-execution-of-a-job"></a>Schemalägg körning av ett jobb

I följande exempel visas hur du schemalägger ett jobb för framtida körning.  
Anslut till [*jobb databasen*](job-automation-overview.md#elastic-job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name = 'ResultsJob',
@enabled=1,
@schedule_interval_type = 'Minutes',
@schedule_interval_count = 15;
```

## <a name="monitor-job-execution-status"></a>Övervaka jobb körnings status

I följande exempel visas hur du visar information om körnings status för alla jobb.  
Anslut till [*jobb databasen*](job-automation-overview.md#elastic-job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named 'ResultsPoolJob'
SELECT * FROM jobs.job_executions
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC;

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC;

--View all execution statuses for job named 'ResultsPoolsJob'
SELECT * FROM jobs.job_executions
WHERE job_name = 'ResultsPoolsJob'
ORDER BY start_time DESC;

-- View all active executions
SELECT * FROM jobs.job_executions
WHERE is_active = 1
ORDER BY start_time DESC;
```

## <a name="cancel-a-job"></a>Avbryta ett jobb

I följande exempel visas hur du avbryter ett jobb.  
Anslut till [*jobb databasen*](job-automation-overview.md#elastic-job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

-- View all active executions to determine job execution id
SELECT * FROM jobs.job_executions
WHERE is_active = 1 AND job_name = 'ResultPoolsJob'
ORDER BY start_time DESC;
GO

-- Cancel job execution with the specified job execution id
EXEC jobs.sp_stop_job '01234567-89ab-cdef-0123-456789abcdef';
```

## <a name="delete-old-job-history"></a>Ta bort gammal jobb historik

I följande exempel visas hur du tar bort jobb historik före ett visst datum.  
Anslut till [*jobb databasen*](job-automation-overview.md#elastic-job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job's executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00';

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Ta bort ett jobb och alla jobb historik

I följande exempel visas hur du tar bort ett jobb och alla relaterade jobb historik.  
Anslut till [*jobb databasen*](job-automation-overview.md#elastic-job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob';

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="job-stored-procedures"></a>Lagrade procedurer för jobb

Följande lagrade procedurer finns i [jobb databasen](job-automation-overview.md#elastic-job-database).

|Lagrad procedur  |Beskrivning  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     Lägger till ett nytt jobb.    |
|[sp_update_job](#sp_update_job)    |      Uppdaterar ett befintligt jobb.   |
|[sp_delete_job](#sp_delete_job)     |      Tar bort ett befintligt jobb.   |
|[sp_add_jobstep](#sp_add_jobstep)    |    Lägger till ett steg i ett jobb.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     Uppdaterar ett jobb steg.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     Tar bort ett jobb steg.    |
|[sp_start_job](#sp_start_job)    |  Startar körning av ett jobb.       |
|[sp_stop_job](#sp_stop_job)     |     Stoppar en jobb körning.   |
|[sp_add_target_group](#sp_add_target_group)    |     Lägger till en mål grupp.    |
|[sp_delete_target_group](#sp_delete_target_group)     |    Tar bort en mål grupp.     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    Lägger till en databas eller grupp med databaser i en mål grupp.     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     Tar bort en mål grupps medlem från en mål grupp.    |
|[sp_purge_jobhistory](#sp_purge_jobhistory)    |    Tar bort historik poster för ett jobb.     |

### <a name="sp_add_job"></a><a name="sp_add_job"></a>sp_add_job

Lägger till ett nytt jobb.
  
#### <a name="syntax"></a>Syntax  
  
```syntaxsql
[jobs].sp_add_job [ @job_name = ] 'job_name'  
  [ , [ @description = ] 'description' ]
  [ , [ @enabled = ] enabled ]
  [ , [ @schedule_interval_type = ] schedule_interval_type ]  
  [ , [ @schedule_interval_count = ] schedule_interval_count ]
  [ , [ @schedule_start_time = ] schedule_start_time ]
  [ , [ @schedule_end_time = ] schedule_end_time ]
  [ , [ @job_id = ] job_id OUTPUT ]
```

#### <a name="arguments"></a>Argument  

[ **\@ job_name =** ] "job_name"  
Namnet på jobbet. Namnet måste vara unikt och får inte innehålla procent andelen (%) jokerteck. job_name är nvarchar (128), utan standardvärdet.

[ **\@ Description =** ] ' Beskrivning '  
Jobbets beskrivning. beskrivningen är nvarchar (512), med standardvärdet NULL. Om Beskrivning utelämnas används en tom sträng.

[ **\@ Enabled =** ] aktiverat  
Om jobbets schema är aktiverat. Enabled är bit, med standardvärdet 0 (inaktive rad). Om 0, är jobbet inte aktiverat och körs inte enligt sitt schema. Det kan dock köras manuellt. Om 1, kommer jobbet att köras enligt sitt schema och kan också köras manuellt.

[ **\@ schedule_interval_type =**] schedule_interval_type  
Värdet anger när jobbet ska köras. schedule_interval_type är nvarchar (50) med standardvärdet en gång och kan vara något av följande värden:

- En gång,
- Minuter,
- Timmar,
- "Dagar",
- Veckor,
- Månaden

[ **\@ schedule_interval_count =** ] schedule_interval_count  
Antalet schedule_interval_count perioder som inträffar mellan varje jobb körning. schedule_interval_count är int, med standardvärdet 1. Värdet måste vara större än eller lika med 1.

[ **\@ schedule_start_time =** ] schedule_start_time  
Det datum då jobb körningen kan börja. schedule_start_time är DATETIME2, med standardvärdet 0001-01-01 00:00:00.0000000.

[ **\@ schedule_end_time =** ] schedule_end_time  
Det datum då jobb körningen kan stoppas. schedule_end_time är DATETIME2, med standardvärdet 9999-12-31 11:59:59.0000000.

[ **\@ job_id =** ] job_id utdata  
Jobb identifierings numret som tilldelats jobbet om det har skapats. job_id är en utgående variabel av typen UniqueIdentifier.

#### <a name="return-code-values"></a>Retur kod värden

0 (lyckades) eller 1 (haveri)

#### <a name="remarks"></a>Kommentarer

sp_add_job måste köras från den jobb agent databas som angavs när jobb agenten skapades.
När sp_add_job har körts för att lägga till ett jobb kan sp_add_jobstep användas för att lägga till steg som utför aktiviteterna för jobbet. Jobbets första versions nummer är 0, vilket kommer att ökas till 1 när det första steget läggs till.

#### <a name="permissions"></a>Behörigheter

Som standard kan medlemmar i den fasta Server rollen sysadmin köra den här lagrade proceduren. De begränsar en användare till att bara kunna övervaka jobb, kan du ge användaren en del av följande databas roll i den jobb agent databas som anges när du skapar jobb agenten:

- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

### <a name="sp_update_job"></a><a name="sp_update_job"></a>sp_update_job

Uppdaterar ett befintligt jobb.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_update_job [ @job_name = ] 'job_name'  
  [ , [ @new_name = ] 'new_name' ]
  [ , [ @description = ] 'description' ]
  [ , [ @enabled = ] enabled ]
  [ , [ @schedule_interval_type = ] schedule_interval_type ]  
  [ , [ @schedule_interval_count = ] schedule_interval_count ]
  [ , [ @schedule_start_time = ] schedule_start_time ]
  [ , [ @schedule_end_time = ] schedule_end_time ]
```

#### <a name="arguments"></a>Argument

[ **\@ job_name =** ] "job_name"  
Namnet på det jobb som ska uppdateras. job_name är nvarchar (128).

[ **\@ NEW_NAME =** ] "NEW_NAME"  
Det nya namnet på jobbet. new_name är nvarchar (128).

[ **\@ Description =** ] ' Beskrivning '  
Jobbets beskrivning. beskrivningen är nvarchar (512).

[ **\@ Enabled =** ] aktiverat  
Anger om jobbets schema är aktiverat (1) eller inte aktiverat (0). Aktive rad är bit.

[ **\@ schedule_interval_type =** ] schedule_interval_type  
Värdet anger när jobbet ska köras. schedule_interval_type är nvarchar (50) och kan vara något av följande värden:

- En gång,
- Minuter,
- Timmar,
- "Dagar",
- Veckor,
- Månaden

[ **\@ schedule_interval_count =** ] schedule_interval_count  
Antalet schedule_interval_count perioder som inträffar mellan varje jobb körning. schedule_interval_count är int, med standardvärdet 1. Värdet måste vara större än eller lika med 1.

[ **\@ schedule_start_time =** ] schedule_start_time  
Det datum då jobb körningen kan börja. schedule_start_time är DATETIME2, med standardvärdet 0001-01-01 00:00:00.0000000.

[ **\@ schedule_end_time =** ] schedule_end_time  
Det datum då jobb körningen kan stoppas. schedule_end_time är DATETIME2, med standardvärdet 9999-12-31 11:59:59.0000000.

#### <a name="return-code-values"></a>Retur kod värden

0 (lyckades) eller 1 (haveri)

#### <a name="remarks"></a>Kommentarer

När sp_add_job har körts för att lägga till ett jobb kan sp_add_jobstep användas för att lägga till steg som utför aktiviteterna för jobbet. Jobbets första versions nummer är 0, vilket kommer att ökas till 1 när det första steget läggs till.

#### <a name="permissions"></a>Behörigheter

Som standard kan medlemmar i den fasta Server rollen sysadmin köra den här lagrade proceduren. De begränsar en användare till att bara kunna övervaka jobb, kan du ge användaren en del av följande databas roll i den jobb agent databas som anges när du skapar jobb agenten:

- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

### <a name="sp_delete_job"></a><a name="sp_delete_job"></a>sp_delete_job

Tar bort ett befintligt jobb.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
  [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argument

[ **\@ job_name =** ] "job_name"  
Namnet på det jobb som ska tas bort. job_name är nvarchar (128).

[ **\@ Force =** ] Force  
Anger om du vill ta bort om jobbet har pågående körningar och avbryta alla pågående körningar (1) eller om körningen av jobb pågår (0). Force är bit.

#### <a name="return-code-values"></a>Retur kod värden

0 (lyckades) eller 1 (haveri)

#### <a name="remarks"></a>Kommentarer

Jobb historiken tas bort automatiskt när ett jobb tas bort.

#### <a name="permissions"></a>Behörigheter

Som standard kan medlemmar i den fasta Server rollen sysadmin köra den här lagrade proceduren. De begränsar en användare till att bara kunna övervaka jobb, kan du ge användaren en del av följande databas roll i den jobb agent databas som anges när du skapar jobb agenten:

- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

### <a name="sp_add_jobstep"></a><a name="sp_add_jobstep"></a>sp_add_jobstep

Lägger till ett steg i ett jobb.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_add_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] step_name ]
     [ , [ @command_type = ] 'command_type' ]
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]
     [ , [ @retry_attempts = ] retry_attempts ]
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]
     [ , [ @output_type = ] 'output_type' ]
     [ , [ @output_credential_name = ] 'output_credential_name' ]
     [ , [ @output_subscription_id = ] 'output_subscription_id' ]
     [ , [ @output_resource_group_name = ] 'output_resource_group_name' ]
     [ , [ @output_server_name = ] 'output_server_name' ]
     [ , [ @output_database_name = ] 'output_database_name' ]
     [ , [ @output_schema_name = ] 'output_schema_name' ]
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argument

[ **\@ job_name =** ] "job_name"  
Namnet på det jobb som steget ska läggas till i. job_name är nvarchar (128).

[ **\@ step_id =** ] step_id  
Sekvensens identifierings nummer för jobb steget. Steg identifierings nummer börjar med 1 och ökar utan luckor. Om ett befintligt steg redan har det här ID: t kommer det här steget och alla följande steg ha sina ID: n, så att det här nya steget kan infogas i sekvensen. Om detta inte anges tilldelas step_id automatiskt den sista i sekvensen av steg. step_id är en int.

[ **\@ step_name =** ] step_name  
Namnet på steget. Måste anges, förutom det första steget i ett jobb (för bekvämlighet) har standard namnet ' JobStep '. step_name är nvarchar (128).

[ **\@ command_type =** ] "command_type"  
Den typ av kommando som utförs av den här Jobstep. command_type är nvarchar (50), med standardvärdet TSql, vilket innebär att @command_type parameterns värde är ett T-SQL-skript.

Om det anges måste värdet vara TSql.

[ **\@ command_source =** ] "command_source"  
Den typ av plats där kommandot lagras. command_source är nvarchar (50), med standardvärdet infogat, vilket innebär att @command_source parameterns värde är den litterala texten för kommandot.

Om det här alternativet anges måste värdet vara infogat.

[ **\@ Command =** ] "kommando"  
Kommandot måste vara ett giltigt T-SQL-skript och körs sedan av det här jobb steget. kommandot är nvarchar (max), med standardvärdet NULL.

[ **\@ credential_name =** ] "credential_name"  
Namnet på den databasbaserade autentiseringsuppgiften som lagras i den här jobb kontroll databasen som används för att ansluta till varje mål databas i mål gruppen när det här steget körs. credential_name är nvarchar (128).

[ **\@ target_group_name =** ] Target-GROUP_NAME  
Namnet på den mål grupp som innehåller mål databaserna som jobb steget ska köras på. target_group_name är nvarchar (128).

[ **\@ initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Fördröjningen före det första försöket, om jobb steget Miss lyckas vid det första körnings försöket. initial_retry_interval_seconds är int, med standardvärdet 1.

[ **\@ maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maximal fördröjning mellan nya försök. Om fördröjningen mellan återförsök skulle växa större än det här värdet, är det ett tak för detta värde i stället. maximum_retry_interval_seconds är int, med standardvärdet 120.

[ **\@ retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Multiplikatorn som ska användas för fördröjningen för nya försök om flera jobb körnings försök Miss lyckas. Om det första återförsöket hade en fördröjning på 5 sekunder och backoff-multiplikatorn är 2,0, kommer den andra återförsöket att ha en fördröjning på 10 sekunder och det tredje försöket kommer att ha en fördröjning på 20 sekunder. retry_interval_backoff_multiplier är Real, med standardvärdet 2,0.

[ **\@ retry_attempts =** ] retry_attempts  
Antalet gånger som körningen ska göras om det första försöket Miss lyckas. Om retry_attempts svärdet till exempel är 10, kommer det att finnas 1 inledande försök och 10 nya försök, vilket ger totalt 11 försök. Om det slutliga försöket Miss lyckas avslutas jobb körningen med en livs cykel som misslyckades. retry_attempts är int, med standardvärdet 10.

[ **\@ step_timeout_seconds =** ] step_timeout_seconds  
Den maximala tids mängd som tillåts för steget att köra. Om den här tiden överskrids, kommer jobb körningen att avslutas med en livs cykel av stängningsåtgärd. step_timeout_seconds är int, med standardvärdet 43 200 sekunder (12 timmar).

[ **\@ output_type =** ] "output_type"  
Om detta inte är null skrivs den typ av mål som kommandots första resultat uppsättning skrivs till. output_type är nvarchar (50), med standardvärdet NULL.

Om det anges måste värdet vara SqlDatabase.

[ **\@ output_credential_name =** ] "output_credential_name"  
Om detta inte är null, namnet på databasen som används för att ansluta till mål databasen för utdata. Måste anges om output_type är lika med SqlDatabase. output_credential_name är nvarchar (128) med standardvärdet NULL.

[ **\@ output_subscription_id =** ] "output_subscription_id"  
Beskrivning av behov.

[ **\@ output_resource_group_name =** ] "output_resource_group_name"  
Beskrivning av behov.

[ **\@ output_server_name =** ] "output_server_name"  
Om detta inte är null är det fullständigt kvalificerade DNS-namnet för den server som innehåller mål databasen för utdata. Måste anges om output_type är lika med SqlDatabase. output_server_name är nvarchar (256), med standardvärdet NULL.

[ **\@ output_database_name =** ] "output_database_name"  
Om detta inte är null, namnet på databasen som innehåller mål tabellen för utdata. Måste anges om output_type är lika med SqlDatabase. output_database_name är nvarchar (128), med standardvärdet NULL.

[ **\@ output_schema_name =** ] "output_schema_name"  
Om detta inte är null, namnet på det SQL-schema som innehåller mål tabellen för utdata. Om output_type är lika med SqlDatabase är standardvärdet dbo. output_schema_name är nvarchar (128).

[ **\@ output_table_name =** ] "output_table_name"  
Om detta inte är null skrivs namnet på den tabell som kommandots första resultat uppsättning ska skrivas till. Om tabellen inte redan finns skapas den baserat på schemat för den returnerade resultat uppsättningen. Måste anges om output_type är lika med SqlDatabase. output_table_name är nvarchar (128) med standardvärdet NULL.

[ **\@ job_version =** ] job_version utdata  
Utdataparameter som ska tilldelas det nya jobb versions numret. job_version är int.

[ **\@ max_parallelism =** ] max_parallelism utdata  
Den högsta nivån av parallellitet per elastisk pool. Om det här alternativet är inställt begränsas jobb steget så att det bara körs på maximalt antal databaser per elastisk pool. Detta gäller för varje elastisk pool som antingen ingår direkt i mål gruppen eller finns i en server som ingår i mål gruppen. max_parallelism är int.

#### <a name="return-code-values"></a>Retur kod värden

0 (lyckades) eller 1 (haveri)

#### <a name="remarks"></a>Kommentarer

När sp_add_jobstep lyckas ökas jobbets aktuella versions nummer. Nästa gången jobbet körs kommer den nya versionen att användas. Om jobbet körs för tillfället kommer den här körningen inte att innehålla det nya steget.

#### <a name="permissions"></a>Behörigheter

Som standard kan medlemmar i den fasta Server rollen sysadmin köra den här lagrade proceduren. De begränsar en användare till att bara kunna övervaka jobb, kan du ge användaren en del av följande databas roll i den jobb agent databas som anges när du skapar jobb agenten:  

- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

### <a name="sp_update_jobstep"></a><a name="sp_update_jobstep"></a>sp_update_jobstep

Uppdaterar ett jobb steg.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_update_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]
     [ , [ @new_id = ] new_id ]
     [ , [ @new_name = ] 'new_name' ]
     [ , [ @command_type = ] 'command_type' ]
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]
     [ , [ @retry_attempts = ] retry_attempts ]
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]
     [ , [ @output_type = ] 'output_type' ]
     [ , [ @output_credential_name = ] 'output_credential_name' ]
     [ , [ @output_server_name = ] 'output_server_name' ]
     [ , [ @output_database_name = ] 'output_database_name' ]
     [ , [ @output_schema_name = ] 'output_schema_name' ]
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argument

[ **\@ job_name =** ] "job_name"  
Namnet på det jobb som steget tillhör. job_name är nvarchar (128).

[ **\@ step_id =** ] step_id  
Identifierings numret för det jobb steg som ska ändras. Antingen step_id eller step_name måste anges. step_id är en int.

[ **\@ step_name =** ] "step_name"  
Namnet på steget som ska ändras. Antingen step_id eller step_name måste anges. step_name är nvarchar (128).

[ **\@ new_id =** ] new_id  
Det nya sekvens identifierings numret för jobb steget. Steg identifierings nummer börjar med 1 och ökar utan luckor. Om ett steg sorteras om, kommer andra steg att numreras om automatiskt.

[ **\@ NEW_NAME =** ] "NEW_NAME"  
Det nya namnet på steget. new_name är nvarchar (128).

[ **\@ command_type =** ] "command_type"  
Den typ av kommando som utförs av den här Jobstep. command_type är nvarchar (50), med standardvärdet TSql, vilket innebär att @command_type parameterns värde är ett T-SQL-skript.

Om det anges måste värdet vara TSql.

[ **\@ command_source =** ] "command_source"  
Den typ av plats där kommandot lagras. command_source är nvarchar (50), med standardvärdet infogat, vilket innebär att @command_source parameterns värde är den litterala texten för kommandot.

Om det här alternativet anges måste värdet vara infogat.

[ **\@ Command =** ] "kommando"  
Kommandona måste vara giltiga T-SQL-skript och körs sedan av det här jobb steget. kommandot är nvarchar (max), med standardvärdet NULL.

[ **\@ credential_name =** ] "credential_name"  
Namnet på den databasbaserade autentiseringsuppgiften som lagras i den här jobb kontroll databasen som används för att ansluta till varje mål databas i mål gruppen när det här steget körs. credential_name är nvarchar (128).

[ **\@ target_group_name =** ] Target-GROUP_NAME  
Namnet på den mål grupp som innehåller mål databaserna som jobb steget ska köras på. target_group_name är nvarchar (128).

[ **\@ initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Fördröjningen före det första försöket, om jobb steget Miss lyckas vid det första körnings försöket. initial_retry_interval_seconds är int, med standardvärdet 1.

[ **\@ maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maximal fördröjning mellan nya försök. Om fördröjningen mellan återförsök skulle växa större än det här värdet, är det ett tak för detta värde i stället. maximum_retry_interval_seconds är int, med standardvärdet 120.

[ **\@ retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Multiplikatorn som ska användas för fördröjningen för nya försök om flera jobb körnings försök Miss lyckas. Om det första återförsöket hade en fördröjning på 5 sekunder och backoff-multiplikatorn är 2,0, kommer den andra återförsöket att ha en fördröjning på 10 sekunder och det tredje försöket kommer att ha en fördröjning på 20 sekunder. retry_interval_backoff_multiplier är Real, med standardvärdet 2,0.

[ **\@ retry_attempts =** ] retry_attempts  
Antalet gånger som körningen ska göras om det första försöket Miss lyckas. Om retry_attempts svärdet till exempel är 10, kommer det att finnas 1 inledande försök och 10 nya försök, vilket ger totalt 11 försök. Om det slutliga försöket Miss lyckas avslutas jobb körningen med en livs cykel som misslyckades. retry_attempts är int, med standardvärdet 10.

[ **\@ step_timeout_seconds =** ] step_timeout_seconds  
Den maximala tids mängd som tillåts för steget att köra. Om den här tiden överskrids, kommer jobb körningen att avslutas med en livs cykel av stängningsåtgärd. step_timeout_seconds är int, med standardvärdet 43 200 sekunder (12 timmar).

[ **\@ output_type =** ] "output_type"  
Om detta inte är null skrivs den typ av mål som kommandots första resultat uppsättning skrivs till. Om du vill återställa värdet för output_type tillbaka till NULL anger du värdet för parametern till (tom sträng). output_type är nvarchar (50), med standardvärdet NULL.

Om det anges måste värdet vara SqlDatabase.

[ **\@ output_credential_name =** ] "output_credential_name"  
Om detta inte är null, namnet på databasen som används för att ansluta till mål databasen för utdata. Måste anges om output_type är lika med SqlDatabase. Om du vill återställa värdet för output_credential_name tillbaka till NULL anger du värdet för parametern till (tom sträng). output_credential_name är nvarchar (128) med standardvärdet NULL.

[ **\@ output_server_name =** ] "output_server_name"  
Om detta inte är null är det fullständigt kvalificerade DNS-namnet för den server som innehåller mål databasen för utdata. Måste anges om output_type är lika med SqlDatabase. Om du vill återställa värdet för output_server_name tillbaka till NULL anger du värdet för parametern till (tom sträng). output_server_name är nvarchar (256), med standardvärdet NULL.

[ **\@ output_database_name =** ] "output_database_name"  
Om detta inte är null, namnet på databasen som innehåller mål tabellen för utdata. Måste anges om output_type är lika med SqlDatabase. Om du vill återställa värdet för output_database_name tillbaka till NULL anger du värdet för parametern till (tom sträng). output_database_name är nvarchar (128), med standardvärdet NULL.

[ **\@ output_schema_name =** ] "output_schema_name"  
Om detta inte är null, namnet på det SQL-schema som innehåller mål tabellen för utdata. Om output_type är lika med SqlDatabase är standardvärdet dbo. Om du vill återställa värdet för output_schema_name tillbaka till NULL anger du värdet för parametern till (tom sträng). output_schema_name är nvarchar (128).

[ **\@ output_table_name =** ] "output_table_name"  
Om detta inte är null skrivs namnet på den tabell som kommandots första resultat uppsättning ska skrivas till. Om tabellen inte redan finns skapas den baserat på schemat för den returnerade resultat uppsättningen. Måste anges om output_type är lika med SqlDatabase. Om du vill återställa värdet för output_server_name tillbaka till NULL anger du värdet för parametern till (tom sträng). output_table_name är nvarchar (128) med standardvärdet NULL.

[ **\@ job_version =** ] job_version utdata  
Utdataparameter som ska tilldelas det nya jobb versions numret. job_version är int.

[ **\@ max_parallelism =** ] max_parallelism utdata  
Den högsta nivån av parallellitet per elastisk pool. Om det här alternativet är inställt begränsas jobb steget så att det bara körs på maximalt antal databaser per elastisk pool. Detta gäller för varje elastisk pool som antingen ingår direkt i mål gruppen eller finns i en server som ingår i mål gruppen. Om du vill återställa värdet för max_parallelism tillbaka till null anger du värdet för parametern till-1. max_parallelism är int.

#### <a name="return-code-values"></a>Retur kod värden

0 (lyckades) eller 1 (haveri)

#### <a name="remarks"></a>Kommentarer

Pågående körningar av jobbet kommer inte att påverkas. När sp_update_jobstep lyckas ökas jobbets versions nummer. Nästa gången jobbet körs kommer den nya versionen att användas.

#### <a name="permissions"></a>Behörigheter

Som standard kan medlemmar i den fasta Server rollen sysadmin köra den här lagrade proceduren. De begränsar en användare till att bara kunna övervaka jobb, kan du ge användaren en del av följande databas roll i den jobb agent databas som anges när du skapar jobb agenten:

- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare

### <a name="sp_delete_jobstep"></a><a name="sp_delete_jobstep"></a>sp_delete_jobstep

Tar bort ett jobb steg från ett jobb.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argument

[ **\@ job_name =** ] "job_name"  
Namnet på det jobb som steget kommer att tas bort från. job_name är nvarchar (128), utan standardvärdet.

[ **\@ step_id =** ] step_id  
Identifierings numret för det jobb steg som ska tas bort. Antingen step_id eller step_name måste anges. step_id är en int.

[ **\@ step_name =** ] "step_name"  
Namnet på steget som ska tas bort. Antingen step_id eller step_name måste anges. step_name är nvarchar (128).

[ **\@ job_version =** ] job_version utdata  
Utdataparameter som ska tilldelas det nya jobb versions numret. job_version är int.

#### <a name="return-code-values"></a>Retur kod värden

0 (lyckades) eller 1 (haveri)

#### <a name="remarks"></a>Kommentarer

Pågående körningar av jobbet kommer inte att påverkas. När sp_update_jobstep lyckas ökas jobbets versions nummer. Nästa gången jobbet körs kommer den nya versionen att användas.

De andra jobb stegen numreras om automatiskt så att de fyller Lucken från det borttagna jobb steget.

#### <a name="permissions"></a>Behörigheter

Som standard kan medlemmar i den fasta Server rollen sysadmin köra den här lagrade proceduren. De begränsar en användare till att bara kunna övervaka jobb, kan du ge användaren en del av följande databas roll i den jobb agent databas som anges när du skapar jobb agenten:

- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

### <a name="sp_start_job"></a><a name="sp_start_job"></a>sp_start_job

Startar körning av ett jobb.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_start_job [ @job_name = ] 'job_name'
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]
```

#### <a name="arguments"></a>Argument

[ **\@ job_name =** ] "job_name"  
Namnet på det jobb som steget kommer att tas bort från. job_name är nvarchar (128), utan standardvärdet.

[ **\@ job_execution_id =** ] job_execution_id utdata  
Utdataparameter som ska tilldelas jobb körningens ID. job_version är uniqueidentifier.

#### <a name="return-code-values"></a>Retur kod värden

0 (lyckades) eller 1 (haveri)

#### <a name="remarks"></a>Kommentarer

Inga.

#### <a name="permissions"></a>Behörigheter

Som standard kan medlemmar i den fasta Server rollen sysadmin köra den här lagrade proceduren. De begränsar en användare till att bara kunna övervaka jobb, kan du ge användaren en del av följande databas roll i den jobb agent databas som anges när du skapar jobb agenten:

- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

### <a name="sp_stop_job"></a><a name="sp_stop_job"></a>sp_stop_job

Stoppar en jobb körning.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```

#### <a name="arguments"></a>Argument

[ **\@ job_execution_id =** ] job_execution_id  
Identifierings numret för jobb körningen som ska stoppas. job_execution_id är uniqueidentifier och standardvärdet är NULL.

#### <a name="return-code-values"></a>Retur kod värden

0 (lyckades) eller 1 (haveri)

#### <a name="remarks"></a>Kommentarer

Inga.

#### <a name="permissions"></a>Behörigheter

Som standard kan medlemmar i den fasta Server rollen sysadmin köra den här lagrade proceduren. De begränsar en användare till att bara kunna övervaka jobb, kan du ge användaren en del av följande databas roll i den jobb agent databas som anges när du skapar jobb agenten:

- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

### <a name="sp_add_target_group"></a><a name="sp_add_target_group"></a>sp_add_target_group

Lägger till en mål grupp.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```

#### <a name="arguments"></a>Argument

[ **\@ target_group_name =** ] "target_group_name"  
Namnet på den mål grupp som ska skapas. target_group_name är nvarchar (128), utan standardvärdet.

[ **\@ target_group_id =** ] Target_group_id att ange det identifierings nummer för mål gruppen som har tilldelats jobbet om det har skapats. target_group_id är en utgående variabel av typen UniqueIdentifier, med standardvärdet NULL.

#### <a name="return-code-values"></a>Retur kod värden

0 (lyckades) eller 1 (haveri)

#### <a name="remarks"></a>Kommentarer

Mål grupper ger ett enkelt sätt att rikta ett jobb till en samling databaser.

#### <a name="permissions"></a>Behörigheter

Som standard kan medlemmar i den fasta Server rollen sysadmin köra den här lagrade proceduren. De begränsar en användare till att bara kunna övervaka jobb, kan du ge användaren en del av följande databas roll i den jobb agent databas som anges när du skapar jobb agenten:

- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

### <a name="sp_delete_target_group"></a><a name="sp_delete_target_group"></a>sp_delete_target_group

Tar bort en mål grupp.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```

#### <a name="arguments"></a>Argument

[ **\@ target_group_name =** ] "target_group_name"  
Namnet på den mål grupp som ska tas bort. target_group_name är nvarchar (128), utan standardvärdet.

#### <a name="return-code-values"></a>Retur kod värden

0 (lyckades) eller 1 (haveri)

#### <a name="remarks"></a>Kommentarer

Inga.

#### <a name="permissions"></a>Behörigheter

Som standard kan medlemmar i den fasta Server rollen sysadmin köra den här lagrade proceduren. De begränsar en användare till att bara kunna övervaka jobb, kan du ge användaren en del av följande databas roll i den jobb agent databas som anges när du skapar jobb agenten:

- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

### <a name="sp_add_target_group_member"></a><a name="sp_add_target_group_member"></a>sp_add_target_group_member

Lägger till en databas eller grupp med databaser i en mål grupp.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] 'membership_type' ]
        [ , [ @target_type = ] 'target_type' ]
        [ , [ @refresh_credential_name = ] 'refresh_credential_name' ]
        [ , [ @server_name = ] 'server_name' ]
        [ , [ @database_name = ] 'database_name' ]
        [ , [ @elastic_pool_name = ] 'elastic_pool_name' ]
        [ , [ @shard_map_name = ] 'shard_map_name' ]
        [ , [ @target_id = ] 'target_id' OUTPUT ]
```

#### <a name="arguments"></a>Argument

[ **\@ target_group_name =** ] "target_group_name"  
Namnet på den mål grupp som medlemmen ska läggas till i. target_group_name är nvarchar (128), utan standardvärdet.

[ **\@ membership_type =** ] "membership_type"  
Anger om mål grupps medlemmen ska tas med eller undantas. target_group_name är nvarchar (128), med standardvärdet include. Giltiga värden för membership_type är include eller exclude.

[ **\@ target_type =** ] "target_type"  
Typ av mål databas eller samling av databaser, inklusive alla databaser på en server, alla databaser i en elastisk pool, alla databaser i en Shard-karta eller en enskild databas. target_type är nvarchar (128), utan standardvärdet. Giltiga värden för target_type är SqlServer, SqlElasticPool, SqlDatabase eller SqlShardMap.

[ **\@ refresh_credential_name =** ] "refresh_credential_name"  
Namnet på databasens begränsade autentiseringsuppgifter. refresh_credential_name är nvarchar (128), utan standardvärdet.

[ **\@ server_name =** ] "server_name"  
Namnet på den server som ska läggas till i den angivna mål gruppen. server_name ska anges när target_type är SqlServer. server_name är nvarchar (128), utan standardvärdet.

[ **\@ database_name =** ] "database_name"  
Namnet på databasen som ska läggas till i den angivna mål gruppen. database_name ska anges när target_type är ' SqlDatabase '. database_name är nvarchar (128), utan standardvärdet.

[ **\@ elastic_pool_name =** ] "elastic_pool_name"  
Namnet på den elastiska pool som ska läggas till i den angivna mål gruppen. elastic_pool_name ska anges när target_type är ' SqlElasticPool '. elastic_pool_name är nvarchar (128), utan standardvärdet.

[ **\@ shard_map_name =** ] "shard_map_name"  
Namnet på Shard som ska läggas till i den angivna mål gruppen. elastic_pool_name ska anges när target_type är ' SqlShardMap '. shard_map_name är nvarchar (128), utan standardvärdet.

[ **\@ target_id =** ] target_group_id utdata  
Det mål-ID-nummer som tilldelats mål grupps medlemmen om den skapades i mål gruppen. target_id är en utgående variabel av typen UniqueIdentifier, med standardvärdet NULL.
Returnera kod värden 0 (lyckades) eller 1 (haveri)

#### <a name="remarks"></a>Kommentarer

Ett jobb körs på alla enskilda databaser inom en server eller i en elastisk pool vid tidpunkten för körningen, när en server eller elastisk pool ingår i mål gruppen.

#### <a name="permissions"></a>Behörigheter

Som standard kan medlemmar i den fasta Server rollen sysadmin köra den här lagrade proceduren. De begränsar en användare till att bara kunna övervaka jobb, kan du ge användaren en del av följande databas roll i den jobb agent databas som anges när du skapar jobb agenten:

- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

#### <a name="examples"></a>Exempel

I följande exempel läggs alla databaser i London-och NewYork-servrarna till i grupp servrarna som underhåller kund information. Du måste ansluta till jobb databasen som anges när du skapar jobb agenten, i det här fallet ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs;
GO

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group @target_group_name =  N'Servers Maintaining Customer Information';
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'refresh_credential', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net';
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'refresh_credential', --credential required to refresh the databases in server
@server_name=N'NewYork.database.windows.net';
GO

--View the recently added members to the target group
SELECT * FROM [jobs].target_group_members WHERE target_group_name= N'Servers Maintaining Customer Information';
GO
```

### <a name="sp_delete_target_group_member"></a><a name="sp_delete_target_group_member"></a>sp_delete_target_group_member

Tar bort en mål grupps medlem från en mål grupp.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
   [ , [ @target_id = ] 'target_id']
```

#### <a name="arguments"></a>Argument

[ @target_group_name =] target_group_name  
Namnet på den mål grupp som mål grupps medlemmen ska tas bort från. target_group_name är nvarchar (128), utan standardvärdet.

[ @target_id =] target_id  
 Det mål-ID-nummer som tilldelats den mål grupps medlem som ska tas bort. target_id är en uniqueidentifier med standardvärdet NULL.

#### <a name="return-code-values"></a>Retur kod värden

0 (lyckades) eller 1 (haveri)

#### <a name="remarks"></a>Kommentarer

Mål grupper ger ett enkelt sätt att rikta ett jobb till en samling databaser.

#### <a name="permissions"></a>Behörigheter

Som standard kan medlemmar i den fasta Server rollen sysadmin köra den här lagrade proceduren. De begränsar en användare till att bara kunna övervaka jobb, kan du ge användaren en del av följande databas roll i den jobb agent databas som anges när du skapar jobb agenten:

- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

#### <a name="examples"></a>Exempel

I följande exempel tar vi bort London-servern från grupp servrarna som underhåller kund information. Du måste ansluta till jobb databasen som anges när du skapar jobb agenten, i det här fallet ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ;
GO

-- Retrieve the target_id for a target_group_members
declare @tid uniqueidentifier
SELECT @tid = target_id FROM [jobs].target_group_members WHERE target_group_name = 'Servers Maintaining Customer Information' and server_name = 'London.database.windows.net';

-- Remove a target group member of type server
EXEC jobs.sp_delete_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_id = @tid;
GO
```

### <a name="sp_purge_jobhistory"></a><a name="sp_purge_jobhistory"></a>sp_purge_jobhistory

Tar bort historik poster för ett jobb.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argument

[ **\@ job_name =** ] "job_name"  
Namnet på det jobb som historik posterna ska tas bort för. job_name är nvarchar (128), med standardvärdet NULL. Antingen job_id eller job_name måste anges, men det går inte att ange båda.

[ **\@ job_id =** ] job_id  
 Jobb identifierings numret för jobbet för de poster som ska tas bort. job_id är uniqueidentifier, med standardvärdet NULL. Antingen job_id eller job_name måste anges, men det går inte att ange båda.

[ **\@ oldest_date =** ] oldest_date  
 Den äldsta posten som ska sparas i historiken. oldest_date är DATETIME2, med standardvärdet NULL. När oldest_date anges tar sp_purge_jobhistory bara bort poster som är äldre än det angivna värdet.

#### <a name="return-code-values"></a>Retur kod värden

0 (lyckades) eller 1 (haveri)

#### <a name="remarks"></a>Kommentarer

Mål grupper ger ett enkelt sätt att rikta ett jobb till en samling databaser.

#### <a name="permissions"></a>Behörigheter

Som standard kan medlemmar i den fasta Server rollen sysadmin köra den här lagrade proceduren. De begränsar en användare till att bara kunna övervaka jobb, kan du ge användaren en del av följande databas roll i den jobb agent databas som anges när du skapar jobb agenten:

- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

#### <a name="examples"></a>Exempel

I följande exempel läggs alla databaser i London-och NewYork-servrarna till i grupp servrarna som underhåller kund information. Du måste ansluta till jobb databasen som anges när du skapar jobb agenten, i det här fallet ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```

## <a name="job-views"></a>Vyer för jobb

Följande vyer är tillgängliga i [jobb databasen](job-automation-overview.md#elastic-job-database).

|Visa  |Beskrivning  |
|---------|---------|
|[job_executions](#job_executions-view)     |  Visar jobb körnings historik.      |
|[utskrifts](#jobs-view)     |   Visar alla jobb.      |
|[job_versions](#job_versions-view)     |   Visar alla jobb versioner.      |
|[jobbsteg](#jobsteps-view)     |     Visar alla steg i den aktuella versionen av varje jobb.    |
|[jobstep_versions](#jobstep_versions-view)     |     Visar alla steg i alla versioner av varje jobb.    |
|[target_groups](#target_groups-view)     |      Visar alla mål grupper.   |
|[target_group_members](#target_group_members-view)     |   Visar alla medlemmar i alla mål grupper.      |

### <a name="job_executions-view"></a><a name="job_executions-view"></a>job_executions vy

[Jobs]. [job_executions]

Visar jobb körnings historik.

|Kolumnnamn | Datatyp | Beskrivning |
|---------|---------|---------|
|**job_execution_id** | uniqueidentifier | Unikt ID för en instans av jobb körningen.
|**job_name** | nvarchar (128) | Jobbets namn.
|**job_id** | uniqueidentifier | Jobbets unika ID.
|**job_version** | int | Version av jobbet (uppdateras automatiskt varje gången jobbet ändras).
|**step_id** |int | Unikt (för detta jobb) ID för steget. NULL anger att detta är den överordnade jobb körningen.
|**is_active** | bit | Anger om informationen är aktiv eller inaktiv. 1 anger aktiva jobb och 0 anger inaktiv.
|**–** | nvarchar (50) | Värde som anger jobbets status: "skapad", "pågår", "misslyckades", "lyckades", "hoppade över", "SucceededWithSkipped"|
|**create_time**| datetime2 (7) | Datum och tid då jobbet skapades.
|**start_time** | datetime2 (7) | Datum och tid då jobbet startades. NULL om jobbet ännu inte har körts.
|**end_time** | datetime2 (7) | Datum och tid då jobbet slutfördes. NULL om jobbet ännu inte har körts eller inte har körts ännu.
|**current_attempts** | int | Antal gånger steget försökte utföra åtgärden igen. Överordnat jobb kommer att bli 0, underordnade jobb körningar är 1 eller större baserat på körnings principen.
|**current_attempt_start_time** | datetime2 (7) | Datum och tid då jobbet startades. NULL anger att detta är den överordnade jobb körningen.
|**last_message** | nvarchar(max) | Jobb-eller steg historik meddelande.
|**target_type** | nvarchar (128) | Typ av mål databas eller samling av databaser, inklusive alla databaser på en server, alla databaser i en elastisk pool eller en databas. Giltiga värden för target_type är SqlServer, SqlElasticPool eller SqlDatabase. NULL anger att detta är den överordnade jobb körningen.
|**target_id** | uniqueidentifier | Unikt ID för mål grupps medlemmen.  NULL anger att detta är den överordnade jobb körningen.
|**target_group_name** | nvarchar (128) | Mål gruppens namn. NULL anger att detta är den överordnade jobb körningen.
|**target_server_name** | nvarchar (256)  | Namnet på den server som finns i mål gruppen. Anges endast om target_type är SqlServer. NULL anger att detta är den överordnade jobb körningen.
|**target_database_name** | nvarchar (128) | Namnet på den databas som finns i mål gruppen. Anges bara när target_type är ' SqlDatabase '. NULL anger att detta är den överordnade jobb körningen.

### <a name="jobs-view"></a>jobb visning

[Jobs]. utskrifts

Visar alla jobb.

|Kolumnnamn | Datatyp |Beskrivning|
|------|------|-------|
|**job_name** | nvarchar (128) | Jobbets namn.|
|**job_id**| uniqueidentifier |Jobbets unika ID.|
|**job_version** |int |Version av jobbet (uppdateras automatiskt varje gången jobbet ändras).|
|**beteckning** |nvarchar (512)| Beskrivning av jobbet. Aktive rad bit: anger om jobbet är aktiverat eller inaktiverat. 1 anger aktiverade jobb och 0 anger inaktiverade jobb.|
|**schedule_interval_type**|nvarchar (50) |Värde som anger när jobbet ska köras: "en gång", "minuter", "timmar", "dagar", "veckor", "månader"
|**schedule_interval_count**|int|Antalet schedule_interval_type perioder som inträffar mellan varje jobb körning.|
|**schedule_start_time**|datetime2 (7)|Datum och tid då jobbet senast startades.|
|**schedule_end_time**|datetime2 (7)|Datum och tid då jobbet senast slutfördes.|

### <a name="job_versions-view"></a><a name="job_versions-view"></a>job_versions vy

[Jobs]. [job_versions]

Visar alla jobb versioner.

|Kolumnnamn|Datatyp|Beskrivning|
|------|------|-------|
|**job_name**|nvarchar (128)|Jobbets namn.|
|**job_id**|uniqueidentifier|Jobbets unika ID.|
|**job_version**|int|Version av jobbet (uppdateras automatiskt varje gången jobbet ändras).|

### <a name="jobsteps-view"></a>jobbsteg vy

[Jobs]. jobbsteg

Visar alla steg i den aktuella versionen av varje jobb.

|Kolumnnamn|Datatyp|Beskrivning|
|------|------|-------|
|**job_name**|nvarchar (128)|Jobbets namn.|
|**job_id**|uniqueidentifier|Jobbets unika ID.|
|**job_version**|int|Version av jobbet (uppdateras automatiskt varje gången jobbet ändras).|
|**step_id**|int|Unikt (för detta jobb) ID för steget.|
|**step_name**|nvarchar (128)|Unikt (för detta jobb) namn för steget.|
|**command_type**|nvarchar (50)|Typ av kommando som ska köras i jobb steget. För v1 måste värdet vara lika med och standardvärdet är "TSql".|
|**command_source**|nvarchar (50)|Kommandots plats. För v1 är ' inline ' standard och endast accepterat värde.|
|**kommandoprompt**|nvarchar(max)|De kommandon som ska köras av elastiska jobb via command_type.|
|**credential_name**|nvarchar (128)|Namnet på databasens begränsade autentiseringsuppgifter som används för att köra jobbet.|
|**target_group_name**|nvarchar (128)|Mål gruppens namn.|
|**target_group_id**|uniqueidentifier|Unikt ID för mål gruppen.|
|**initial_retry_interval_seconds**|int|Fördröjningen före det första försöket. Standardvärdet är 1.|
|**maximum_retry_interval_seconds**|int|Maximal fördröjning mellan nya försök. Om fördröjningen mellan återförsök skulle växa större än det här värdet, är det ett tak för detta värde i stället. Standardvärdet är 120.|
|**retry_interval_backoff_multiplier**|real|Multiplikatorn som ska användas för fördröjningen för nya försök om flera jobb körnings försök Miss lyckas. Standardvärdet är 2,0.|
|**retry_attempts**|int|Antalet återförsök som ska användas om det här steget Miss lyckas. Standardvärdet är 10, vilket innebär att inga nya försök görs.|
|**step_timeout_seconds**|int|Tiden i minuter mellan nya försök. Standardvärdet är 0, vilket anger ett intervall på 0 minuter.|
|**output_type**|nvarchar (11)|Kommandots plats. I den aktuella för hands versionen är infogad som standard och endast accepterat värde.|
|**output_credential_name**|nvarchar (128)|Namnet på de autentiseringsuppgifter som ska användas för att ansluta till mål servern för att lagra resultat uppsättningen.|
|**output_subscription_id**|uniqueidentifier|Unikt ID för prenumerationen på mål-server\database för resultat uppsättningen från frågekörningen.|
|**output_resource_group_name**|nvarchar (128)|Resurs grupp namn där mål servern finns.|
|**output_server_name**|nvarchar (256)|Namnet på mål servern för resultat uppsättningen.|
|**output_database_name**|nvarchar (128)|Namnet på mål databasen för resultat uppsättningen.|
|**output_schema_name**|nvarchar(max)|Namnet på mål schemat. Standardvärdet är dbo, om inget anges.|
|**output_table_name**|nvarchar(max)|Namnet på tabellen där resultat uppsättningen från frågeresultaten ska lagras. Tabellen skapas automatiskt baserat på schemat för resultat uppsättningen om den inte redan finns. Schemat måste matcha schemat för resultat mängden.|
|**max_parallelism**|int|Det maximala antalet databaser per elastisk pool som jobb steget ska köras vid en viss tidpunkt. Standardvärdet är NULL, vilket innebär ingen gräns. |

### <a name="jobstep_versions-view"></a><a name="jobstep_versions-view"></a>jobstep_versions vy

[Jobs]. [jobstep_versions]

Visar alla steg i alla versioner av varje jobb. Schemat är identiskt med [jobbsteg](#jobsteps-view).

### <a name="target_groups-view"></a><a name="target_groups-view"></a>target_groups vy

[Jobs]. [target_groups]

Visar en lista över alla mål grupper.

|Kolumnnamn|Datatyp|Beskrivning|
|-----|-----|-----|
|**target_group_name**|nvarchar (128)|Namnet på mål gruppen, en samling databaser.
|**target_group_id**|uniqueidentifier|Unikt ID för mål gruppen.

### <a name="target_group_members-view"></a><a name="target_group_members-view"></a>target_group_members vy

[Jobs]. [target_group_members]

Visar alla medlemmar i alla mål grupper.

|Kolumnnamn|Datatyp|Beskrivning|
|-----|-----|-----|
|**target_group_name**|nvarchar (128|Namnet på mål gruppen, en samling databaser. |
|**target_group_id**|uniqueidentifier|Unikt ID för mål gruppen.|
|**membership_type**|int|Anger om mål grupps medlemmen tas med eller undantas i mål gruppen. Giltiga värden för target_group_name är include eller exclude.|
|**target_type**|nvarchar (128)|Typ av mål databas eller samling av databaser, inklusive alla databaser på en server, alla databaser i en elastisk pool eller en databas. Giltiga värden för target_type är SqlServer, SqlElasticPool, SqlDatabase eller SqlShardMap.|
|**target_id**|uniqueidentifier|Unikt ID för mål grupps medlemmen.|
|**refresh_credential_name**|nvarchar (128)|Namnet på den databas omfattnings information som används för att ansluta till mål grupps medlemmen.|
|**subscription_id**|uniqueidentifier|Unikt ID för prenumerationen.|
|**resource_group_name**|nvarchar (128)|Namnet på resurs gruppen där mål grupps medlemmen finns.|
|**server_name**|nvarchar (128)|Namnet på den server som finns i mål gruppen. Anges endast om target_type är SqlServer. |
|**database_name**|nvarchar (128)|Namnet på den databas som finns i mål gruppen. Anges bara när target_type är ' SqlDatabase '.|
|**elastic_pool_name**|nvarchar (128)|Namnet på den elastiska poolen som finns i mål gruppen. Anges bara när target_type är ' SqlElasticPool '.|
|**shard_map_name**|nvarchar (128)|Namnet på Shard Maps som finns i mål gruppen. Anges bara när target_type är ' SqlShardMap '.|

## <a name="resources"></a>Resurser

- ![Ämnes länks ikon](/sql/database-engine/configure-windows/media/topic-link.gif "Ämnes länk ikon") [konventioner för Transact-SQL-syntax](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  

## <a name="next-steps"></a>Nästa steg

- [Skapa och hantera elastiska jobb med PowerShell](elastic-jobs-powershell-create.md)
- [Auktorisering och behörigheter](/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
