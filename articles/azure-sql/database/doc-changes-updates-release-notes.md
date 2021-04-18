---
title: Nyheter
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Lär dig mer om de nya funktionerna och dokumentationsförbättringarna för Azure SQL Database & SQL Managed Instance.
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/17/2021
ms.author: sstein
ms.openlocfilehash: 81c306ac2a8a5c00c5d06877974db7e04964c76b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600921"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>Vad är nytt i Azure SQL Database & SQL Managed Instance?
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Den här artikeln Azure SQL Database och Azure SQL Managed Instance funktioner som för närvarande är i allmänt tillgänglig förhandsversion. Mer SQL Database och SQL Managed Instance uppdateringar och förbättringar finns i [SQL Database & SQL Managed Instance tjänstuppdateringar.](https://azure.microsoft.com/updates/?product=sql-database) Uppdateringar och förbättringar av andra Azure-tjänster finns i [Tjänstuppdateringar.](https://azure.microsoft.com/updates)

## <a name="whats-new"></a>Nyheter

Dokumentationen för Azure SQL Database och Azure SQL Managed Instance har delats upp i separata avsnitt. Vi har också uppdaterat hur vi refererar till en hanterad instans från *Azure SQL Database hanterad instans* *till Azure SQL Managed Instance*.

Vi har gjort detta eftersom vissa funktioner varierar avsevärt mellan en enkel databas och en hanterad instans, och det har blivit allt svårare att förklara komplexa nyanser mellan Azure SQL Database och Azure SQL Managed Instance i enskilda delade artiklar.

Detta förtydligande mellan de olika Azure SQL-produkterna bör förenkla och effektivisera processen med att arbeta med SQL Server-databasmotorn i Azure, oavsett om det är en enda hanterad databas i Azure SQL Database, en fullständigt hanterad instans som är värd för flera databaser i Azure SQL Managed Instance eller den välbekanta lokala SQL Server-produkten som finns på en virtuell dator i Azure.

Tänk på att detta är ett pågående arbete och att inte alla artiklar har uppdaterats ännu. Till exempel är dokumentationen för Transact-SQL-instruktioner (T-SQL), lagrade procedurer och många funktioner som delas mellan Azure SQL Database och Azure SQL Managed Instance ännu inte slutförda, så vi tack för din tålamod när vi fortsätter att klargöra innehållet. 

Den här tabellen ger en snabb jämförelse av ändringen i terminologin: 


|**Ny giltighetsperiod**  | **Föregående term**  |**Förklaring** |
|---------|---------|---------|
|**Hanterad Azure SQL-instans** | Azure SQL Database *hanterad instans*| Azure SQL Managed Instance är en egen produkt i Azure SQL familjen, snarare än bara ett distributionsalternativ inom Azure SQL Database. | 
|**Azure SQL Database**|Azure SQL Database enkel *databas*| Om inget annat anges, innehåller produktnamnet Azure SQL Database både enkla databaser och databaser som distribuerats till en elastisk pool. |
|**Azure SQL Database**|Azure SQL Database elastisk *pool*| Om inget annat anges, innehåller produktnamnet Azure SQL Database både enkla databaser och databaser som distribuerats till en elastisk pool.  |
|**Azure SQL Database** |Azure SQL Database | Även om termen förblir densamma gäller den nu bara för distributioner av enkla databaser och elastiska pooler och inkluderar inte hanterade instanser. |
| **Azure SQL**| Ej tillämpligt | Detta avser den familj av SQL Server databasmotorprodukter som är tillgängliga i Azure: Azure SQL Database, Azure SQL Managed Instance och SQL Server på virtuella Azure-datorer. | 

## <a name="features-in-public-preview"></a>Funktioner i offentlig förhandsversion

### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

| Funktion | Information |
| ---| --- |
| Elastiska databasjobb (förhandsversion) | Mer information finns i [Skapa, konfigurera och hantera elastiska jobb.](elastic-jobs-overview.md) |
| Elastiska frågor | Mer information finns i [Översikt över elastiska frågor.](elastic-query-overview.md) |
| Elastiska transaktioner | [Distribuerade transaktioner mellan molndatabaser](elastic-transactions-overview.md). |
| Frågeredigeraren i Azure Portal |Mer information finns i [Använda Azure Portal SQL-frågeredigeraren för att ansluta och fråga efter data.](connect-query-portal.md)|
|SQL-analys|Mer information finns i [Azure SQL-analys](../../azure-monitor/insights/azure-sql.md).|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Hanterad Azure SQL-instans](#tab/managed-instance)

| Funktion | Information |
| ---| --- |
| [Distribuerade transaktioner](/azure/azure-sql/database/elastic-transactions-overview) | Distribuerade transaktioner mellan hanterade instanser. |
| [Instanspooler](/azure/sql-database/sql-database-instance-pools) | Ett praktiskt och kostnadseffektivt sätt att migrera mindre SQL-instanser till molnet. |
| [Azure AD-serverhuvudnamn på instansnivå (inloggningar)](/sql/t-sql/statements/create-login-transact-sql) | Skapa inloggningar på instansnivå med instruktionen [CREATE LOGIN FROM EXTERNAL PROVIDER](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) (SKAPA INLOGGNING FRÅN EXTERN PROVIDER). |
| [Transaktionsreplikering](../managed-instance/replication-transactional-overview.md) | Replikera ändringarna från dina tabeller till andra databaser i SQL Managed Instance, SQL Database eller SQL Server. Eller uppdatera dina tabeller när vissa rader ändras i andra instanser av SQL Managed Instance eller SQL Server. Mer information finns i [Konfigurera replikering i Azure SQL Managed Instance](../managed-instance/replication-between-two-instances-configure-tutorial.md). |
| Hotidentifiering |Mer information finns i [Konfigurera hotidentifiering i Azure SQL Managed Instance](../managed-instance/threat-detection-configure.md).|
| Långsiktig kvarhållning av säkerhetskopior | Mer information finns i [Configure long-term back up retention in Azure SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md), som för närvarande finns i en begränsad offentlig förhandsversion. |

---

## <a name="new-features"></a>Nya funktioner

### <a name="sql-managed-instance-h2-2019-updates"></a>SQL Managed Instance H2 2019-uppdateringar

- [Tjänststödd undernätskonfiguration](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) är ett säkert och praktiskt sätt att hantera undernätskonfigurationen där du styr datatrafik SQL Managed Instance säkerställer det oavbrutna flödet av hanteringstrafik.
- [transparent datakryptering (TDE) med Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) möjliggör ett BYOK-scenario (Bring Your Own Key) för dataskydd i vila och gör att organisationer kan separera hanteringsuppgifter för nycklar och data.
- [Med automatiska redundansgrupper](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) kan du replikera alla databaser från den primära instansen till en sekundär instans i en annan region.
- [Med globala spårningsflaggor](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/) kan du konfigurera SQL Managed Instance beteende.

### <a name="sql-managed-instance-h1-2019-updates"></a>SQL Managed Instance H1 2019-uppdateringar

Följande funktioner är aktiverade i SQL Managed Instance i H1 2019:
  - Stöd för prenumerationer med <a href="/azure/azure-sql/managed-instance/resource-limits">Azure-månadskredit för Visual Studio prenumeranter</a> och ökade [regionala gränser.](../managed-instance/resource-limits.md#regional-resource-limitations)
  - Stöd för <a href="/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 och SharePoint 2019 </a> och <a href="/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central. </a>
  - Skapa en hanterad instans <a href="/azure/azure-sql/managed-instance/scripts/create-powershell-azure-resource-manager-template">med sortering på instansnivå</a> <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">och en tidszon</a> som du väljer.
  - Hanterade instanser skyddas nu med [den inbyggda brandväggen](../managed-instance/management-endpoint-verify-built-in-firewall.md).
  - Konfigurera SQL Managed Instance att använda [](../managed-instance/public-endpoint-configure.md)offentliga slutpunkter, [åsidosätta proxyanslutning](connectivity-architecture.md#connection-policy) för att få bättre nätverksprestanda, 4 virtuella kärnor på <a href="https://aka.ms/four-cores-sql-mi-update">Gen5-maskinvarugenerering</a> eller Konfigurera kvarhållning av säkerhetskopior i upp till <a href="/azure/azure-sql/database/automated-backups-overview">35</a> dagar för återställning till tidpunkt. [Långsiktig kvarhållning av säkerhetskopior](long-term-retention-overview.md) (upp till 10 år) är för närvarande i offentlig förhandsversion.  
  - Med nya funktioner kan du <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">geo-återställa databasen</a>till ett annat datacenter med hjälp av PowerShell, byta [namn på databas](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), ta bort [virtuellt kluster](../managed-instance/virtual-cluster-delete.md).
  - Den nya inbyggda [rollen Instansdeltagare](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) gör det möjligt att separera uppgiftsefterlevnad (SoD) med säkerhetsprinciper och efterlevnad av företagsstandarder.
  - SQL Managed Instance är tillgänglig i följande Azure Government för GA (US Gov, Texas, US Gov, Arizona) och i Kina, norra 2 och Kina, östra 2. Den är också tillgänglig i följande offentliga regioner: Australien, centrala, Australien, centrala 2, Brasilien, södra, Frankrike, södra, Förenade Arabemiraten, centrala, Förenade Arabemiraten, norra, Sydafrika, norra och Sydafrika, västra.

## <a name="known-issues"></a>Kända problem

|Problem  |Identifierat datum  |Status  |Löst datum  |
|---------|---------|---------|---------|
|[Om du ändrar anslutningstypen påverkas inte anslutningar via slutpunkten för redundansgruppen](#changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint)|Januari 2021|Har lösning||
|[Proceduren sp_send_dbmail tillfälligt misslyckas när @query parametern används](#procedure-sp_send_dbmail-may-transiently-fail-when--parameter-is-used)|Januari 2021|Har lösning||
|[Distribuerade transaktioner kan köras när du har tagit bort den hanterade instansen från serverförtroendegruppen](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|Okt 2020|Har lösning||
|[Distribuerade transaktioner kan inte köras efter skalningsåtgärd för hanterad instans](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|Okt 2020|Har lösning||
|[BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) / [OPENROWSET i](/sql/t-sql/functions/openrowset-transact-sql) Azure SQL-instruktionen `BACKUP` / `RESTORE` i managed instance kan inte använda Azure AD-hanterad identitet för att autentisera till Azure Storage|Sep 2020|Har en lösning||
|[Tjänstens huvudnamn kan inte komma åt Azure AD och AKV](#service-principal-cannot-access-azure-ad-and-akv)|Augusti 2020|Har en lösning||
|[Återställning av manuell säkerhetskopiering utan KONTROLLSUMMA kan misslyckas](#restoring-manual-backup-without-checksum-might-fail)|Maj 2020|Matchat|Juni 2020|
|[Agenten slutar svara när du ändrar, inaktiverar eller aktiverar befintliga jobb](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|Maj 2020|Matchat|Juni 2020|
|[Behörigheter för resursgrupp som inte tillämpas på SQL Managed Instance](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|Feb 2020|Matchat|Nov 2020|
|[Begränsning av manuell redundans via portalen för redundansgrupper](#limitation-of-manual-failover-via-portal-for-failover-groups)|Januari 2020|Har en lösning||
|[SQL Agent-roller behöver uttryckliga EXECUTE-behörigheter för icke-sysadmin-inloggningar](#in-memory-oltp-memory-limits-are-not-applied)|Dec 2019|Har en lösning||
|[SQL Agent-jobb kan avbrytas vid omstart av agentprocessen](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dec 2019|Matchat|Mar 2020|
|[Azure AD-inloggningar och användare stöds inte i SSDT](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|Nov 2019|Ingen lösning||
|[Minnesbegränsningar för OLTP-minne tillämpas inte](#in-memory-oltp-memory-limits-are-not-applied)|Okt 2019|Har lösning||
|[Fel fel returnerades vid försök att ta bort en fil som inte är tom](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Okt 2019|Har lösning||
|[Ändra tjänstnivå och skapa instansåtgärder blockeras av pågående databasåterställning](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Sep 2019|Har lösning||
|[Resource Governor på Affärskritisk tjänstnivå kan behöva konfigureras om efter redundans](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Sep 2019|Har lösning||
|[Dialogrutor för Service Broker databasdatabaser måste initieras om efter uppgraderingen av tjänstnivån](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|Augusti 2019|Har lösning||
|[Personifiering av Azure AD-inloggningstyper stöds inte](#impersonation-of-azure-ad-login-types-is-not-supported)|Juli 2019|Ingen lösning||
|[@query parametern stöds inte i sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|April 2019|Matchat|Januari 2021|
|[Transaktionsreplikering måste konfigureras om efter geo-redundans](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|Ingen lösning||
|[Den tillfälliga databasen används under återställningen](#temporary-database-is-used-during-restore-operation)||Har en lösning||
|[TEMPDB-struktur och innehåll skapas på nytt](#tempdb-structure-and-content-is-re-created)||Ingen lösning||
|[Överskrida lagringsutrymme med små databasfiler](#exceeding-storage-space-with-small-database-files)||Har en lösning||
|[GUID-värden visas i stället för databasnamn](#guid-values-shown-instead-of-database-names)||Har en lösning||
|[Felloggar bevaras inte](#error-logs-arent-persisted)||Ingen lösning||
|[Transaktionsomfång för två databaser i samma instans stöds inte](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Har en lösning|Mar 2020|
|[CLR-moduler och länkade servrar kan ibland inte referera till en lokal IP-adress](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Har en lösning||
|Databaskonsekvensen verifierades inte med DBCC CHECKDB efter återställning av databasen Azure Blob Storage.||Matchat|Nov 2019|
|Återställning till tidpunkt av databas från Affärskritisk till Generell användning-nivå lyckas inte om källdatabasen innehåller minnes minnesindelad OLTP-objekt.||Matchat|Okt 2019|
|Databas-e-postfunktion med externa e-postservrar (inte Azure) som använder säker anslutning||Matchat|Okt 2019|
|Inneslutna databaser stöds inte i SQL Managed Instance||Matchat|Augusti 2019|

### <a name="changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint"></a>Om du ändrar anslutningstypen påverkas inte anslutningar via slutpunkten för redundansgruppen

Om en instans ingår i en grupp för automatisk [](https://docs.microsoft.com/azure/azure-sql/managed-instance/connection-types-overview) redundans [,](https://docs.microsoft.com/azure/azure-sql/database/auto-failover-group-overview)så gäller inte ändring av instansens anslutningstyp för de anslutningar som upprättas via slutpunkten för redundansgruppens lyssnare.

**Lösning:** Ta bort och återskapa grupp-afer för automatisk redundans genom att ändra anslutningstypen.

### <a name="procedure-sp_send_dbmail-may-transiently-fail-when-query-parameter-is-used"></a>Proceduren sp_send_dbmail tillfälligt misslyckas när @query parametern används

Proceduren sp_send_dbmail tillfälligt misslyckas när `@query` parametern används. När det här problemet inträffar misslyckas varje sekund sp_send_dbmail med `Msg 22050, Level 16, State 1` felmeddelandet `Failed to initialize sqlcmd library with error number -2147467259` . För att det här felet ska kunna visas korrekt ska proceduren anropas med standardvärdet 0 för parametern , annars sprids `@exclude_query_output` inte felet.
Det här problemet orsakas av ett känt fel som rör hur sp_send_dbmail använder personifiering och anslutningspooler.
Du kan lösa det här problemet genom att omsluta koden för att skicka e-post till en omprövningslogik som förlitar sig på utdataparametern `@mailitem_id` . Om körningen misslyckas blir parametervärdet NULL, vilket anger att sp_send_dbmail anropas en gång till för att skicka ett e-postmeddelande. Här är ett exempel på den här omprövningslogiken.
```sql
CREATE PROCEDURE send_dbmail_with_retry AS
BEGIN
    DECLARE @miid INT
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
        @mailitem_id = @miid OUTPUT

    -- If sp_send_dbmail returned NULL @mailidem_id then retry sending email.
    --
    IF (@miid is NULL)
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
END
```

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>Distribuerade transaktioner kan köras när du har tagit bort hanterad instans från serverförtroendegruppen

[Serverförtroendegrupper](../managed-instance/server-trust-group-overview.md) används för att upprätta förtroende mellan hanterade instanser som är nödvändiga för [att köra distribuerade transaktioner.](./elastic-transactions-overview.md) När du har tagit bort hanterad instans från serverförtroendegruppen eller tagit bort gruppen kanske du fortfarande kan köra distribuerade transaktioner. Det finns en lösning som du kan använda för att vara säker på att distribuerade transaktioner är inaktiverade och att den användarinitierade manuella [redundansen](../managed-instance/user-initiated-failover.md) är initierad på en hanterad instans.

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>Distribuerade transaktioner kan inte köras efter skalningsåtgärd för hanterad instans

Skalningsåtgärder för hanterad instans som inkluderar ändring av tjänstnivå eller antal virtuella kärnor återställer inställningarna för serverförtroendegrupp på serverservern och inaktiverar körning av [distribuerade transaktioner.](./elastic-transactions-overview.md) Som en tillfällig lösning kan du ta bort och [skapa en ny serverförtroendegrupp](../managed-instance/server-trust-group-overview.md) Azure Portal.

### <a name="bulk-insert-and-backuprestore-statements-cannot-use-managed-identity-to-access-azure-storage"></a>BULK INSERT och BACKUP/RESTORE-instruktioner kan inte använda hanterad identitet för att få åtkomst till Azure Storage

Massinfognings-, BACKUP- och RESTORE-instruktioner och OPENROWSET-funktionen kan inte användas `DATABASE SCOPED CREDENTIAL` med hanterad identitet för att autentisera till Azure Storage. Som en tillfällig lösning kan du växla till AUTENTISERING MED SIGNATUR FÖR DELAD ÅTKOMST. Följande exempel fungerar inte på Azure SQL (både databas och hanterad instans):

```sql
CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/curriculum', CREDENTIAL= msi_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

**Lösning:** Använd [signatur för delad åtkomst för att autentisera till lagring.](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage)

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>Tjänstens huvudnamn kan inte komma åt Azure AD och AKV

I vissa fall kan det finnas ett problem med tjänstens huvudnamn som används för åtkomst till Azure AD Azure Key Vault tjänster (AKV). Därför påverkar det här problemet användningen av Azure AD-autentisering och transparent databaskryptering (TDE) med SQL Managed Instance. Detta kan upplevas som ett tillfälligt anslutningsproblem eller att det inte går att köra instruktioner som CREATE LOGIN/USER FROM EXTERNAL PROVIDER (SKAPA INLOGGNING/ANVÄNDARE FRÅN EXTERN PROVIDER) eller EXECUTE AS LOGIN/USER (KÖR SOM INLOGGNING/ANVÄNDARE). Att konfigurera TDE med kund hanterad nyckel på en ny Azure SQL Managed Instance fungerar kanske inte heller i vissa fall.

**Lösning:** Om du vill förhindra att det här problemet uppstår på din SQL Managed Instance innan du kör några uppdateringskommandon, eller om du redan har haft det här problemet efter uppdateringskommandon, går du till Azure Portal, öppnar bladet SQL Managed Instance [Active Directory-administratör.](./authentication-aad-configure.md?tabs=azure-powershell#azure-portal) Kontrollera om du kan se felmeddelandet "Managed Instance needs a Service Principal to access Azure Active Directory. Klicka här för att skapa tjänstens huvudnamn". Om du har påträffat det här felmeddelandet klickar du på det och följer de stegvisa anvisningarna tills felet har lösts.

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>Återställning av manuell säkerhetskopiering utan CHECKSUM kan misslyckas

I vissa fall kan manuell säkerhetskopiering av databaser som har gjorts på en hanterad instans utan CHECKSUM misslyckas med att återställas. I sådana fall kan du försöka återställa säkerhetskopian tills du lyckas.

**Lösning:** Gör manuella säkerhetskopieringar av databaser på hanterade instanser med CHECKSUM aktiverat.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>Agenten slutar svara när du ändrar, inaktiverar eller aktiverar befintliga jobb

I vissa fall kan ändringar, inaktivering eller aktivering av ett befintligt jobb leda till att agenten slutar svara. Problemet åtgärdas automatiskt vid identifiering, vilket resulterar i en omstart av agentprocessen.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>Behörigheter för resursgruppen tillämpas inte på SQL Managed Instance

När Azure SQL Managed Instance rollen Deltagare tillämpas på en resursgrupp (RG) tillämpas den inte på SQL Managed Instance har ingen effekt.

**Lösning:** Konfigurera en SQL Managed Instance för användare på prenumerationsnivå.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Begränsning av manuell redundans via portalen för redundansgrupper

Om en redundansgrupp sträcker sig över instanser i olika Azure-prenumerationer eller resursgrupper kan manuell redundans inte initieras från den primära instansen i redundansgruppen.

**Lösning:** Initiera redundans via portalen från den geo-sekundära instansen.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL Agent-roller behöver uttryckliga EXECUTE-behörigheter för icke-sysadmin-inloggningar

Om icke-sysadmin-inloggningar läggs till i fasta SQL Agent-databasroller finns det ett problem där explicita EXECUTE-behörigheter måste beviljas till de lagrade huvudprocedurerna för att dessa inloggningar ska fungera. [](/sql/ssms/agent/sql-server-agent-fixed-database-roles) Om det här problemet uppstår visas felmeddelandet "Behörigheten EXECUTE nekades på objektet <object_name> (Microsoft SQL Server, Fel: 229)".

**Lösning:** När du lägger till inloggningar till en fast SQL Agent-databasroll (SQLAgentUserRole, SQLAgentReaderRole eller SQLAgentOperatorRole) kör du nedanstående T-SQL-skript för att uttryckligen bevilja EXECUTE-behörigheter till de lagrade procedurer som anges.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>SQL Agent-jobb kan avbrytas vid omstart av agentprocessen

**(Löst i mars 2020)** SQL Agent skapar en ny session varje gång ett jobb startas, vilket gradvis ökar minnesförbrukningen. För att undvika att nå den interna minnesgränsen, vilket skulle blockera körningen av schemalagda jobb, startas agentprocessen om när minnesförbrukningen når tröskelvärdet. Det kan leda till att körningen av jobb avbryts när omstarten körs.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Minnesbegränsningar för OLTP-minne tillämpas inte

Den Affärskritisk-tjänstnivån tillämpar i vissa fall inte korrekt högsta minnesgränser för [minnesoptimerade](../managed-instance/resource-limits.md#in-memory-oltp-available-space) objekt. SQL Managed Instance kan göra det möjligt för arbetsbelastningen att använda mer minne för minnesintensiva OLTP-åtgärder, vilket kan påverka tillgängligheten och stabiliteten för instansen. Minnes minnesbaserade OLTP-frågor som når gränserna misslyckas kanske inte omedelbart. Det här problemet kommer snart att åtgärdas. Frågor som använder mer minnes minnes inkommande OLTP-minne misslyckas tidigare om de når [gränsen](../managed-instance/resource-limits.md#in-memory-oltp-available-space).

**Lösning:** [Övervaka minnesanvändningen av OLTP-lagring](../in-memory-oltp-monitor-space.md) [med hjälp SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) att säkerställa att arbetsbelastningen inte använder mer än det tillgängliga minnet. Öka minnesgränserna som beror på antalet virtuella kärnor eller optimera din arbetsbelastning för att använda mindre minne.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Fel fel returnerades vid försök att ta bort en fil som inte är tom

SQL Server och SQL Managed Instance tillåter [inte att en användare släpper en fil som inte är tom](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Om du försöker ta bort en datafil utan data med hjälp `ALTER DATABASE REMOVE FILE` av en -instruktion `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` returneras inte felet omedelbart. SQL Managed Instance fortsätter att försöka ta bort filen och åtgärden misslyckas efter 30 minuter med `Internal server error` .

**Lösning:** Ta bort innehållet i filen med `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` kommandot . Om det här är den enda filen i filgruppen måste du ta bort data från tabellen eller partitionen som är associerad med den här filgruppen innan du krymper filen och eventuellt läsa in dessa data i en annan tabell/partition.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Ändra tjänstnivå och skapa instansåtgärder blockeras av pågående databasåterställning

Pågående instruktion, datamigreringstjänstens migreringsprocess och inbyggd återställning till tidpunkt blockerar uppdatering av en tjänstnivå eller storleksändring av den befintliga instansen och skapandet av nya instanser tills återställningsprocessen `RESTORE` är klar. 

Återställningsprocessen blockerar dessa åtgärder på de hanterade instanserna och instanspoolerna i samma undernät där återställningsprocessen körs. Instanserna i instanspoolerna påverkas inte. Åtgärder på tjänstnivå för att skapa eller ändra misslyckas inte eller time out. De fortsätter när återställningen har slutförts eller avbrutits.

**Lösning:** Vänta tills återställningsprocessen är klar eller avbryt återställningsprocessen om åtgärden för att skapa eller uppdatera tjänstnivå har högre prioritet.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Resource Governor på Affärskritisk tjänstnivå kan behöva konfigureras om efter redundans

Den [Resource Governor-funktion](/sql/relational-databases/resource-governor/resource-governor) som gör att du kan begränsa de resurser som tilldelats till användararbetsbelastningen kan felaktigt klassificera en viss användararbetsbelastning efter en redundansväxling eller en användarinitierad ändring av tjänstnivån (till exempel ändringen av maximal vCore eller maximal instanslagringsstorlek).

**Lösning:** Kör regelbundet eller som en del av ett SQL Agent-jobb som kör SQL-uppgiften när instansen startar om du `ALTER RESOURCE GOVERNOR RECONFIGURE` använder [Resource Governor](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>Dialogrutor för Service Broker databas över databaser måste initieras om efter uppgraderingen av tjänstnivån

Dialogrutorna för Service Broker databas mellan databaser slutar att leverera meddelanden till tjänsterna i andra databaser efter att åtgärden på tjänstnivå har ändrats. Meddelandena *förloras inte* och de finns i avsändarkön. Ändringar av virtuella kärnor eller instanslagringsstorlek i SQL Managed Instance leder till att ett värde i `service_broke_guid` [vyn sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) ändras för alla databaser. Alla `DIALOG` som skapas med en BEGIN [DIALOG-instruktion](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) som refererar till Service Brokers i en annan databas slutar att leverera meddelanden till måltjänsten.

**Lösning:** Stoppa alla aktiviteter som använder konversationer mellan Service Broker dialogrutor innan du uppdaterar en tjänstnivå och initiera om dem efteråt. Om det finns återstående meddelanden som inte har levererades efter en ändring på tjänstnivån läser du meddelandena från källkön och skickar dem igen till målkön.

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>Personifiering av Azure AD-inloggningstyper stöds inte

Personifiering med `EXECUTE AS USER` eller av följande Azure Active Directory `EXECUTE AS LOGIN` (Azure AD)-huvudnamn stöds inte:
-   Alias för Azure AD-användare. Följande fel returneras i det här fallet: `15517` .
- Azure AD-inloggningar och användare baserat på Azure AD-program eller tjänstens huvudnamn. Följande fel returneras i det här fallet: `15517` och `15406` .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query parametern stöds inte i sp_send_db_mail

Parametern `@query` i [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) här proceduren fungerar inte.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Transaktionsreplikering måste konfigureras om efter geo-redundans

Om transaktionsreplikering har aktiverats för en databas i en automatisk redundansgrupp måste SQL Managed Instance-administratören rensa alla publiceringar på den gamla primära och konfigurera om dem på den nya primära efter en redundans till en annan region. Mer information finns i [Replikering.](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication)

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>Azure AD-inloggningar och användare stöds inte i SSDT

SQL Server Data Tools har inte fullständigt stöd för Azure AD-inloggningar och -användare.

### <a name="temporary-database-is-used-during-restore-operation"></a>Den tillfälliga databasen används under återställningen

När en databas återställs i SQL Managed Instance skapar återställningstjänsten först en tom databas med önskat namn för att allokera namnet på instansen. Efter en stund tas den här databasen bort och återställningen av den faktiska databasen startas. 

Databasen som är i *återställningstillstånd har* tillfälligt ett slumpmässigt GUID-värde i stället för namn. Det tillfälliga namnet ändras till det önskade namn som anges i `RESTORE` -instruktionen när återställningen är klar. 

I den första fasen kan en användare komma åt den tomma databasen och även skapa tabeller eller läsa in data i den här databasen. Den tillfälliga databasen tas bort när återställningstjänsten startar den andra fasen.

**Lösning:** Kom inte åt databasen som du återställer förrän du ser att återställningen har slutförts.

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB-struktur och innehåll skapas på nytt

Databasen `tempdb` är alltid uppdelad i 12 datafiler och filstrukturen kan inte ändras. Den maximala storleken per fil kan inte ändras och nya filer kan inte läggas till i `tempdb` . `Tempdb` skapas alltid igen som en tom databas när instansen startar eller går över, och eventuella ändringar som görs i `tempdb` bevaras inte.

### <a name="exceeding-storage-space-with-small-database-files"></a>Överstiger lagringsutrymme med små databasfiler

`CREATE DATABASE`- `ALTER DATABASE ADD FILE` och `RESTORE DATABASE` -instruktioner kan misslyckas eftersom instansen kan nå Azure Storage gränsen.

Varje Generell användning instans av SQL Managed Instance har upp till 35 TB lagringsutrymme reserverat för Azure Premium-diskutrymme. Varje databasfil placeras på en separat fysisk disk. Diskstorlekarna kan vara 128 GB, 256 GB, 512 GB, 1 TB eller 4 TB. Oanvänd utrymme på disken debiteras inte, men den totala summan av Azure Premium-diskstorlekar får inte överstiga 35 TB. I vissa fall kan en hanterad instans som inte behöver totalt 8 TB överskrida Azure-gränsen på 35 TB för lagringsstorleken på grund av intern fragmentering.

Till exempel kan Generell användning instans SQL Managed Instance en stor fil med storleken 1,2 TB placerad på en disk på 4 TB. Den kan också ha 248 filer på vardera 1 GB som placeras på separata 128 GB diskar. I det här exemplet:

- Den totala allokerade disklagringsstorleken är 1 x 4 TB + 248 x 128 GB = 35 TB.
- Det totala reserverade utrymmet för databaser på instansen är 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Det här exemplet illustrerar att en instans av SQL Managed Instance under vissa omständigheter, på grund av en specifik distribution av filer, kan nå gränsen på 35 TB som är reserverad för en ansluten Azure Premium-disk när du inte förväntar dig det.

I det här exemplet fortsätter befintliga databaser att fungera och kan växa utan problem så länge nya filer inte läggs till. Nya databaser kan inte skapas eller återställas eftersom det inte finns tillräckligt med utrymme för nya diskar, även om den totala storleken för alla databaser inte når instansstorleksgränsen. Felet som returneras i det fallet är inte tydligt.

Du kan [identifiera antalet återstående filer med hjälp](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) av systemvyer. Om du når den här gränsen kan du försöka att tömma och ta bort några av de mindre filerna med [hjälp](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) av DBCC SHRINKFILE-instruktionen eller växla [till Affärskritisk-nivån,](../managed-instance/resource-limits.md#service-tier-characteristics)som inte har den här gränsen .

### <a name="guid-values-shown-instead-of-database-names"></a>GUID-värden visas i stället för databasnamn

Flera systemvyer, prestandaräknare, felmeddelanden, XEvents och felloggposter visar GUID-databasidentifierare i stället för de faktiska databasnamnen. Förlita dig inte på dessa GUID-identifierare eftersom de ersätts med faktiska databasnamn i framtiden.

**Lösning:** Använd sys.databases-vyn för att matcha det faktiska databasnamnet från det fysiska databasnamnet, som anges i form av GUID-databasidentifierare:

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Felloggar bevaras inte

Felloggar som är tillgängliga SQL Managed Instance inte bevaras och deras storlek ingår inte i den maximala lagringsgränsen. Felloggar kan raderas automatiskt om redundans inträffar. Det kan finnas luckor i fellogghistoriken eftersom SQL Managed Instance har flyttats flera gånger på flera virtuella datorer.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Transaktionsomfång för två databaser inom samma instans stöds inte

**(Löst i mars 2020)** Klassen i .NET fungerar inte om två frågor skickas till två databaser inom samma `TransactionScope` instans under samma transaktionsomfång:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**Lösning (behövs inte sedan mars 2020):** Använd [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) för att använda en annan databas i en anslutningskontext i stället för att använda två anslutningar.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR-moduler och länkade servrar kan ibland inte referera till en lokal IP-adress

CLR-moduler SQL Managed Instance och länkade servrar eller distribuerade frågor som refererar till en aktuell instans kan ibland inte matcha IP-adressen för en lokal instans. Det här felet är ett tillfälligt problem.

**Lösning:** Använd kontextanslutningar i en CLR-modul om det är möjligt.

## <a name="updates"></a>Uppdateringar

En lista över SQL Database uppdateringar och förbättringar finns i [SQL Database tjänstuppdateringar.](https://azure.microsoft.com/updates/?product=sql-database)

Uppdateringar och förbättringar av alla Azure-tjänster finns i [Tjänstuppdateringar.](https://azure.microsoft.com/updates)

## <a name="contribute-to-content"></a>Bidra med innehåll

Information om hur du bidrar Azure SQL dokumentationen finns i [Microsofts Docs-deltagarguide](/contribute/).
