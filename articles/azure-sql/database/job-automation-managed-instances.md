---
title: Jobb automatisering med SQL Agent-jobb i Azure SQL-hanterad instans
description: Automatiserings alternativ för att köra Transact-SQL-skript (T-SQL) i Azure SQL-hanterad instans
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
dev_langs:
- TSQL
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 02/01/2021
ms.openlocfilehash: 6ba1ba9d8c809a805fe8691baf96350b70ec6a51
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691900"
---
# <a name="automate-management-tasks-using-sql-agent-jobs-in-azure-sql-managed-instance"></a>Automatisera hanterings uppgifter med hjälp av SQL Agent-jobb i Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Med hjälp av [SQL Server Agent](/sql/ssms/agent/sql-server-agent) i SQL Server-och [SQL-hanterad instans](../../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)kan du skapa och schemalägga jobb som regelbundet kan köras mot en eller flera databaser för att köra Transact-SQL-frågor (T-SQL) och utföra underhålls aktiviteter. Den här artikeln introducerade SQL-Agent för SQL-hanterad instans.

> [!Note]
> SQL-agenten är inte tillgänglig i Azure SQL Database-eller Azure Synapse-analys. I stället rekommenderar vi [jobb automatisering med elastiska jobb](job-automation-overview.md).

### <a name="sql-agent-job-limitations-in-azure-sql-managed-instance"></a>Begränsningar för SQL Agent-jobb i Azure SQL-hanterad instans

Det är värt att notera skillnaderna mellan SQL-agenten som finns i SQL Server och som en del av SQL-hanterad instans. Mer information om vilka funktions skillnader som stöds mellan SQL Server och SQL-hanterad instans finns i [Azure SQL Managed instance T-SQL-skillnader från SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent). 

Några av SQL Agent-funktionerna som är tillgängliga i SQL Server stöds inte i SQL-hanterad instans:

- Inställningar för SQL Agent är skrivskyddade. 
    - Den system lagrade proceduren `sp_set_agent_properties` stöds inte i SQL-hanterad instans.
- Aktivering/inaktive ring av SQL Agent stöds inte för närvarande i SQL-hanterad instans. SQL Agent körs alltid.
- Aviseringar stöds delvis:
  - Personsökare stöds inte.
  - NetSend stöds inte.
  - Aviseringar stöds inte.
- Proxyservrar stöds inte.
- EventLog stöds inte.
- Jobb schema utlösare baserat på en inaktiv processor stöds inte.

## <a name="when-to-use-sql-agent-jobs"></a>När du ska använda SQL Agent-jobb 

Det finns flera scenarier när du kan använda SQL Agent-jobb:

- Automatisera hanterings uppgifter och Schemalägg dem att köras varje vardag, efter timmar osv.
  - Distribuera schemaändringar, hantering av autentiseringsuppgifter, insamling av prestandadata eller insamling av telemetri för klientorganisation (kund).
  - Uppdatera referensdata (information som är gemensam över alla databaser), läsa in data från Azure Blob-lagring.
  - Vanliga underhålls aktiviteter, inklusive DBCC CHECKDB, för att säkerställa data integritet eller index underhåll för att förbättra frågans prestanda. Konfigurera jobb att köras över en samling databaser regelbundet, till exempel vid tidpunkter för låg belastning.
  - Samla in frågeresultat från en uppsättning databaser till en central tabell regelbundet. Prestandafrågor kan kontinuerligt köras och konfigureras för att utlösa ytterligare åtgärder som ska utföras.
- Samla in data för rapportering
  - Samla in data från en samling databaser till en enda mål tabell.
  - Kör databearbetningsfrågor med längre körningstid över en stor uppsättning databaser, till exempel insamling av kundtelemetri. Resultaten samlas till en enskild måltabell för ytterligare analys.
- Dataflyttar
  - Skapa jobb som replikerar ändringar som gjorts i dina databaser till andra databaser eller samla in uppdateringar som gjorts i fjärrdatabaser och tillämpa ändringar i databasen.
  - Skapa jobb som läser in data från eller till databaser med hjälp av SQL Server Integration Services (SSIS).

## <a name="sql-agent-jobs-in-azure-sql-managed-instance"></a>SQL Agent-jobb i Azure SQL-hanterad instans

SQL Agent-jobb körs av SQL-agenttjänsten som fortsätter att användas för uppgifts automatisering i SQL Server och SQL-hanterad instans. 

SQL Agent-jobb är en angiven serie med T-SQL-skript mot databasen. Använd jobb för att definiera en administrativ uppgift som kan köras en eller flera gånger och övervakas lyckad eller misslyckad status. 

Ett jobb kan köras på en lokal server eller på flera fjärrservrar. SQL Agent-jobb är en intern databas motor komponent som körs i SQL-hanterad instans tjänst.

Det finns flera viktiga begrepp vad gäller SQL Agent-jobb:

- **Jobbsteg** är en uppsättning med ett eller flera steg som ska köras i jobbet. För varje jobbsteg kan du definiera strategi för återförsök och vilken åtgärd som ska vidtas om jobbsteget lyckas eller misslyckas.
- **Scheman** definierar när jobbet ska köras.
- Med **aviseringar** kan du definiera regler som ska användas för att meddela operatörer via e-post när jobbet har slutförts.

### <a name="sql-agent-job-steps"></a>Jobb steg för SQL Agent

SQL Agent-jobbsteg är sekvenser med åtgärder som SQL Agent ska köra. Varje steg har följande steg som ska köras om steget lyckas eller misslyckas, antalet återförsök i fall av fel.

Med SQL-agenten kan du skapa olika typer av jobb, t. ex. Transact-SQL-jobb, som kör en enskild Transact-SQL-batch mot databasen, eller operativ systemets kommando/PowerShell-steg som kan köra ett anpassat OS-skript, [SSIS jobb steg](../../data-factory/how-to-invoke-ssis-package-managed-instance-agent.md) som gör det möjligt att läsa in data med SSIS- [körning eller med](../managed-instance/replication-transactional-overview.md) hjälp av de steg som kan användas för att läsa

> [!Note]
> Mer information om hur du använder Azure SSIS Integration Runtime med SSISDB som hanteras av Azure SQL Managed instance finns i [använda Azure SQL Managed instance med SQL Server Integration Services (SSIS) i Azure Data Factory](/../azure/data-factory/how-to-use-sql-managed-instance-with-ir.md).

[Transaktionsreplikering](../managed-instance/replication-transactional-overview.md) kan replikera ändringarna från dina tabeller till andra databaser i Azure SQL Managed Instance, Azure SQL Database eller SQL Server. Mer information finns i [Konfigurera replikering i Azure SQL-hanterad instans](../../azure-sql/managed-instance/replication-between-two-instances-configure-tutorial.md). 

Andra typer av jobb-steg stöds inte för närvarande i SQL-hanterad instans, inklusive:

- Jobbsteg för sammanslagen replikering stöds inte.
- Köläsare stöds inte.
- Analysis Services stöds inte
 
### <a name="sql-agent-job-schedules"></a>Jobb scheman för SQL-Agent

Ett schema anger när ett jobb körs. Fler än ett jobb kan köras på samma schema, och fler än ett schema kan tillämpas på samma jobb.

Ett schema kan definiera följande villkor för den tid då ett jobb körs:

- När SQL Server Agent startar. Jobbet aktiveras efter varje redundans.
- En gång vid ett specifikt datum och en specifik tid, vilket är användbart för fördröjd körning av ett jobb.
- Enligt ett återkommande schema.

> [!Note]
> SQL-hanterad instans har för närvarande inte möjlighet att starta ett jobb när processorn är inaktiv.

### <a name="sql-agent-job-notifications"></a>Meddelanden om SQL Agent-jobb

Med SQL Agent-jobb kan du få meddelanden när jobbet har slutförts eller misslyckats. Du kan ta emot aviseringar via e-post.

Om den inte redan är aktive rad måste du först konfigurera [Database Mail funktionen](/sql/relational-databases/database-mail/database-mail) på Azure SQL-hanterad instans:

```sql
GO
EXEC sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
EXEC sp_configure 'Database Mail XPs', 1;
GO
RECONFIGURE
```

Du får ett exempel på hur du konfigurerar e-postkontot som ska användas för att skicka e-postaviseringar. Tilldela kontot till e-postprofilen som kallas `AzureManagedInstance_dbmail_profile` . Om du vill skicka e-post med hjälp av SQL Agent-jobb i SQL-hanterad instans måste det finnas en profil som måste anropas `AzureManagedInstance_dbmail_profile` . Annars kommer SQL-hanterad instans inte att kunna skicka e-post via SQL-agenten. Se följande exempel:

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)';

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.';

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

Testa Database Mail-konfigurationen via T-SQL med den [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) systemet lagrad procedur:

```sql
DECLARE @body VARCHAR(4000) = 'The email is sent from ' + @@SERVERNAME;
EXEC msdb.dbo.sp_send_dbmail  
    @profile_name = 'AzureManagedInstance_dbmail_profile',  
    @recipients = 'ADD YOUR EMAIL HERE',  
    @body = 'Add some text',  
    @subject = 'Azure SQL Instance - test email';  
```

Du kan meddela operatören att något har hänt med dina SQL Agent-jobb. En operatör definierar kontakt information för en person som ansvarar för underhåll av en eller flera instanser i SQL-hanterad instans. Ibland tilldelas operatörs ansvar en individ.

I system med flera instanser i SQL-hanterad instans eller SQL Server kan många personer dela operatörs ansvar. En operatör innehåller ingen säkerhetsinformation och definierar inte ett säkerhetsobjekt. Vi rekommenderar att en operatör inte är en person vars ansvar kan ändras, men en e-postdistributions grupp.   

Du kan [skapa operatörer](/sql/relational-databases/system-stored-procedures/sp-add-operator-transact-sql) med SQL Server Management Studio (SSMS) eller Transact-SQL-skriptet som visas i följande exempel:

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'AzureSQLTeam',
    @enabled=1,
    @email_address=N'AzureSQLTeamn@contoso.com';
```

Bekräfta att e-postmeddelandet lyckades eller misslyckades via [Database mail-loggen](/sql/relational-databases/database-mail/database-mail-log-and-audits) i SSMS.

Du kan sedan [ändra ett SQL Agent-jobb](/sql/relational-databases/system-stored-procedures/sp-update-job-transact-sql) och tilldela operatörer som ska meddelas via e-post om jobbet slutförs, Miss lyckas eller lyckas med SSMS eller följande Transact-SQL-skript:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'AzureSQLTeam';
```

### <a name="sql-agent-job-history"></a>Jobb historik för SQL Agent

Med Azure SQL Managed instance kan du för närvarande inte ändra några egenskaper för SQL-agenten eftersom de lagras i de underliggande register värdena. Det innebär att alternativ för att justera agentens bevarande princip för poster i jobb historik är fasta enligt standardvärdet 1000 poster och max 100 historik per jobb.

### <a name="sql-agent-fixed-database-role-membership"></a>SQL Agent Fixed Database Role-medlemskap

Om användare som är länkade till icke-sysadmin-inloggningar läggs till i någon av de tre fasta databas rollerna för SQL-agenten i msdb-systemdatabasen, finns det ett problem i vilket explicita KÖRNINGs behörigheter måste beviljas till de Master-lagrade procedurerna för att dessa inloggningar ska fungera. Om det här problemet uppstår nekades fel meddelandet "KÖRNINGs behörigheten för objektet <object_name> (Microsoft SQL Server, fel: 229)" visas. 

När du lägger till användare till en fast databas roll för SQL-Agent (SQLAgentUserRole, SQLAgentReaderRole eller SQLAgentOperatorRole) i msdb, för var och en av användarens inloggningar som har lagts till i dessa roller, kör du skriptet nedan för att uttryckligen bevilja kör behörighet till de systemlagrade procedurerna i listan. I det här exemplet förutsätts att användar namnet och inloggnings namnet är desamma. 

```sql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

## <a name="learn-more"></a>Läs mer

- [Vad är en hanterad Azure SQL-instans?](../managed-instance/sql-managed-instance-paas-overview.md)
- [Vad är nytt i Azure SQL Database & SQL-hanterad instans?](../../azure-sql/database/doc-changes-updates-release-notes.md?tabs=managed-instance)
- [Azure SQL Managed instance T-SQL-skillnader från SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)
- [Jämförelse av funktioner: Azure SQL Database och Azure SQL-hanterad instans](../../azure-sql/database/features-comparison.md)