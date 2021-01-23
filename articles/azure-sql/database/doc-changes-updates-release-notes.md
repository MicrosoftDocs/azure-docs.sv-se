---
title: Nyheter
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Lär dig mer om nya funktioner och dokumentations förbättringar för Azure SQL Database & SQL-hanterad instans.
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: sstein
ms.openlocfilehash: 607b588d3371b20c2b3fa9854e27a7ccdfe2e551
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703775"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>Vad är nytt i Azure SQL Database & SQL-hanterad instans?
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Den här artikeln innehåller Azure SQL Database-och Azure SQL-hanterade instans funktioner som för närvarande finns i en offentlig för hands version. Uppdateringar och förbättringar för SQL Database och SQL-hanterad instans finns i [SQL Database & SQL-hanterade instans tjänst uppdateringar](https://azure.microsoft.com/updates/?product=sql-database). Uppdateringar och förbättringar av andra Azure-tjänster finns i [tjänste uppdateringar](https://azure.microsoft.com/updates).

## <a name="whats-new"></a>Nyheter

Dokumentation för Azure SQL Database och Azure SQL-hanterad instans har delats in i separata avsnitt. Vi har även uppdaterat hur vi refererar till en hanterad instans från *Azure SQL Database Hanterad instans* till en *Azure SQL-hanterad instans*.

Vi har gjort detta eftersom vissa funktioner och funktioner varierar kraftigt mellan en enda databas och en hanterad instans, och den har blivit allt svårare att förklara komplexa olika delarna mellan Azure SQL Database och Azure SQL-hanterad instans i enskilda delade artiklar.

Den här informationen mellan olika Azure SQL-produkter bör förenkla och effektivisera processen med att arbeta med SQL Server databas motorn i Azure, oavsett om det är en enda hanterad databas i Azure SQL Database, en fullständigt fullfjädrade hanterad instans som är värd för flera databaser i Azure SQL-hanterad instans eller den välkända lokala SQL Server-produkten som finns på en virtuell dator i Azure.

Tänk på att det här är ett pågående arbete och att alla artiklar ännu inte har uppdaterats. Dokumentation för t. ex. Transact-SQL-uttryck (T-SQL), lagrade procedurer och många funktioner som delas mellan Azure SQL Database och Azure SQL-hanterad instans har inte slutförts ännu, så vi tackar dig för ditt tålamod när vi fortsätter att klargöra innehållet. 

Den här tabellen ger en snabb jämförelse mellan ändringen i terminologin: 


|**Ny giltighetsperiod**  | **Föregående term**  |**Förklaring** |
|---------|---------|---------|
|**Hanterad Azure SQL-instans** | Azure SQL Database *hanterad instans*| Azure SQL Managed instance är sin egen produkt i Azure SQL-serien, i stället för bara ett distributions alternativ i Azure SQL Database. | 
|**Azure SQL Database**|Azure SQL Database *enskild databas*| Om inget annat uttryckligen anges innehåller produkt namnet Azure SQL Database både enkla databaser och databaser som distribueras till en elastisk pool. |
|**Azure SQL Database**|Azure SQL Database *elastisk pool*| Om inget annat uttryckligen anges innehåller produkt namnet Azure SQL Database både enkla databaser och databaser som distribueras till en elastisk pool.  |
|**Azure SQL Database** |Azure SQL Database | Även om villkoret förblir detsamma gäller det nu bara för distributioner av en enskild databas och elastisk pool, och omfattar inte hanterade instanser. |
| **Azure SQL**| Ej tillämpligt | Detta avser en familj av SQL Server databas motor produkter som är tillgängliga i Azure: Azure SQL Database, Azure SQL-hanterad instans och SQL Server på virtuella Azure-datorer. | 

## <a name="features-in-public-preview"></a>Funktioner i offentlig för hands version

### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

| Funktion | Information |
| ---| --- |
| Accelererad databas återställning med enkla databaser och elastiska pooler | Mer information finns i [accelererad databas återställning](../accelerated-database-recovery.md).|
| Dataidentifiering och -klassificering  |Mer information finns i [Azure SQL Database och Azure Synapse Analytics data discovery & klassificering](data-discovery-and-classification-overview.md).|
| Elastic Database-jobb (för hands version) | Mer information finns i [skapa, konfigurera och hantera elastiska jobb](elastic-jobs-overview.md). |
| Elastiska frågor | Mer information finns i [Översikt över elastiska frågor](elastic-query-overview.md). |
| Elastiska transaktioner | [Distribuerade transaktioner över moln databaser](elastic-transactions-overview.md). |
| Frågeredigeraren i Azure Portal |Mer information finns i [använda Azure Portal SQL-Frågeredigeraren för att ansluta och fråga efter data](connect-query-portal.md).|
| R Services/Machine Learning med enkla databaser och elastiska pooler |Mer information finns i [Machine Learning Services i Azure SQL Database](/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|SQL-analys|Mer information finns i [Azure SQL-analys](../../azure-monitor/insights/azure-sql.md).|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Hanterad Azure SQL-instans](#tab/managed-instance)

| Funktion | Information |
| ---| --- |
| <a href="/azure/azure-sql/database/elastic-transactions-overview">Distribuerade transaktioner</a> | Distribuerade transaktioner över hanterade instanser. |
| <a href="/azure/sql-database/sql-database-instance-pools">Instanspooler</a> | Ett bekvämt och kostnads effektivt sätt att migrera mindre SQL-instanser till molnet. |
| <a href="/en-gb/sql/t-sql/statements/create-login-transact-sql">Azure AD server-huvudobjekt på instans nivå (inloggningar)</a> | Skapa inloggningar på instans nivå med hjälp av instruktionen <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Skapa inloggning från extern provider</a> . |
| [Transaktionell replikering](../managed-instance/replication-transactional-overview.md) | Replikera ändringarna från dina tabeller till andra databaser i SQL-hanterad instans, SQL Database eller SQL Server. Eller uppdatera dina tabeller när några rader ändras i andra instanser av SQL-hanterad instans eller SQL Server. Mer information finns i [Konfigurera replikering i Azure SQL-hanterad instans](../managed-instance/replication-between-two-instances-configure-tutorial.md). |
| Hotidentifiering |Mer information finns i [Konfigurera hot identifiering i Azure SQL Managed instance](../managed-instance/threat-detection-configure.md).|
| Långsiktig kvarhållning av säkerhetskopior | Mer information finns i [Konfigurera långsiktig säkerhets kopiering i Azure SQL Managed instance](../managed-instance/long-term-backup-retention-configure.md), som för närvarande finns i begränsad offentlig för hands version. | 

---

## <a name="new-features"></a>Nya funktioner

### <a name="sql-managed-instance-h2-2019-updates"></a>SQL-hanterad instans H2 2019 uppdateringar

- [Konfiguration av tjänstestyrt undernät](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) är ett säkert och bekvämt sätt att hantera under näts konfiguration där du styr data trafiken medan SQL-hanterad instans säkerställer det oavbrutna flödet av hanterings trafik.
- [Transparent datakryptering (TDE) med Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) gör det möjligt för ett BYOK-scenario för data skydd i vila och gör det möjligt för organisationer att separera hanterings uppgifter för nycklar och data.
- Med [grupper för automatisk redundans](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) kan du replikera alla databaser från den primära instansen till en sekundär instans i en annan region.
- Med [globala spårnings flaggor](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/) kan du konfigurera beteende för SQL-hanterad instans.

### <a name="sql-managed-instance-h1-2019-updates"></a>SQL-hanterad instans H1 2019 uppdateringar

Följande funktioner är aktiverade i distributions modellen SQL-hanterad instans i H1 2019:
  - Support för prenumerationer med <a href="/azure/azure-sql/managed-instance/resource-limits"> Azures månatliga kredit för Visual Studio-prenumeranter </a> och ökade [regionala gränser](../managed-instance/resource-limits.md#regional-resource-limitations).
  - Stöd för <a href="/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> sharepoint 2016 och sharepoint 2019 </a> och <a href="/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central. </a>
  - Skapa en hanterad instans med <a href="/azure/azure-sql/managed-instance/scripts/create-powershell-azure-resource-manager-template">sortering på instans nivå</a> och en <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">tidszon</a> som du väljer.
  - Hanterade instanser skyddas nu med [inbyggd brand vägg](../managed-instance/management-endpoint-verify-built-in-firewall.md).
  - Konfigurera SQL-hanterad instans så att den använder [offentliga slut punkter](../managed-instance/public-endpoint-configure.md), [åsidosätter proxy](connectivity-architecture.md#connection-policy) -anslutning för att få bättre nätverks prestanda, <a href="https://aka.ms/four-cores-sql-mi-update"> 4 virtuella kärnor på Gen5-maskinvarukompatibilitet</a> eller <a href="/azure/azure-sql/database/automated-backups-overview">Konfigurera kvarhållning av säkerhets kopior upp till 35 dagar</a> för återställning vid tidpunkter. [Långsiktig kvarhållning av säkerhets kopior](long-term-retention-overview.md#sql-managed-instance-support) (upp till 10 år) är för närvarande en begränsad offentlig för hands version.  
  - Med nya funktioner kan du <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">geo-återställa databasen till ett annat data Center med hjälp av PowerShell</a>, [byta namn på databas](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [ta bort virtuellt kluster](../managed-instance/virtual-cluster-delete.md).
  - Den nya inbyggda [rollen instans deltagar roll](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) möjliggör separering av tull (SOD) efterlevnad av säkerhets principer och efterlevnad av företags standarder.
  - SQL-hanterad instans finns i följande Azure Government regioner till GA (US Gov, Texas, US Gov, Arizona) och i Kina, norra 2 och Kina, östra 2. Den är också tillgänglig i följande offentliga regioner: Australien, centrala, Australien, centrala, södra Frankrike, södra Förenade Arabemiraten Central, Förenade Arabemiraten Nord, Sydafrika, norra, södra Afrika, västra.

## <a name="known-issues"></a>Kända problem

|Problem  |Datum identifierat  |Status  |Åtgärds datum  |
|---------|---------|---------|---------|
|[Procedur sp_send_dbmail kan tillfälligt Miss lyckas när @query parametern används](#procedure-sp_send_dbmail-may-transiently-fail-when--parameter-is-used)|Jan 2021|Har en lösning||
|[Distribuerade transaktioner kan utföras efter borttagning av hanterade instanser från Server förtroende grupp](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|Okt 2020|Har en lösning||
|[Distribuerade transaktioner kan inte utföras efter skalnings åtgärden för hanterade instanser](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|Okt 2020|Har en lösning||
|[bulk INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) / [OpenRowSet](/sql/t-sql/functions/openrowset-transact-sql?view=sql-server-ver15) i Azure SQL och `BACKUP` / `RESTORE` instruktionen i en hanterad instans kan inte använda Azure AD-hantera identitet för att autentisera till Azure Storage|Sep 2020|Har en lösning||
|[Tjänstens huvud namn kan inte komma åt Azure AD och AKV](#service-principal-cannot-access-azure-ad-and-akv)|Aug 2020|Har en lösning||
|[Återställning av manuell säkerhets kopiering utan kontroll summa kan Miss kopie ras](#restoring-manual-backup-without-checksum-might-fail)|Maj 2020|Matchat|Juni 2020|
|[Agenten slutar svara vid ändring, inaktive ring eller aktivering av befintliga jobb](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|Maj 2020|Matchat|Juni 2020|
|[Behörigheter för resurs gruppen gäller inte för SQL-hanterad instans](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|Feb 2020|Matchat|Nov 2020|
|[Begränsning av manuell redundans via portalen för failover-grupper](#limitation-of-manual-failover-via-portal-for-failover-groups)|Jan 2020|Har en lösning||
|[SQL Agent-roller behöver uttryckliga EXECUTE-behörigheter för icke-sysadmin-inloggningar](#in-memory-oltp-memory-limits-are-not-applied)|Dec 2019|Har en lösning||
|[SQL Agent-jobb kan avbrytas efter omstart av agent processen](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dec 2019|Matchat|Mar 2020|
|[Azure AD-inloggningar och användare stöds inte i SSDT](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|Nov 2019|Ingen lösning||
|[Minnes gränser för minnes intern OLTP tillämpas inte](#in-memory-oltp-memory-limits-are-not-applied)|Okt 2019|Har en lösning||
|[Ett fel fel returnerades vid försök att ta bort en fil som inte är tom](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Okt 2019|Har en lösning||
|[Ändra tjänst nivå och skapa instans åtgärder blockeras av pågående databas återställning](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Sep 2019|Har en lösning||
|[Resource Governor på Affärskritisk tjänst nivå kan behöva konfigureras om efter en redundansväxling](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Sep 2019|Har en lösning||
|[Service Broker dialog rutor mellan databaser måste initieras om efter uppgraderingen av service nivå](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|Aug 2019|Har en lösning||
|[Personifiering av Azure AD-inloggnings typer stöds inte](#impersonation-of-azure-ad-login-types-is-not-supported)|Jul 2019|Ingen lösning||
|[@query parametern stöds inte i sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Apr 2019|Matchat|Jan 2021|
|[Transaktionsreplikering måste konfigureras om efter GEO-redundans](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|Ingen lösning||
|[Tillfällig databas används under återställnings åtgärden](#temporary-database-is-used-during-restore-operation)||Har en lösning||
|[TEMPDB-strukturen och innehållet har skapats på nytt](#tempdb-structure-and-content-is-re-created)||Ingen lösning||
|[Överskrida lagrings utrymme med små databasfiler](#exceeding-storage-space-with-small-database-files)||Har en lösning||
|[GUID-värden som visas i stället för databas namn](#guid-values-shown-instead-of-database-names)||Har en lösning||
|[Fel loggarna är inte beständiga](#error-logs-arent-persisted)||Ingen lösning||
|[Transaktions omfånget på två databaser inom samma instans stöds inte](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Har en lösning|Mar 2020|
|[CLR-moduler och länkade servrar kan ibland inte referera till en lokal IP-adress](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Har en lösning||
|Databasens konsekvens kontrol leras inte med DBCC CHECKDB efter återställningen av databasen från Azure Blob Storage.||Matchat|Nov 2019|
|Det går inte att återställa punkt-i-tid från Affärskritisk nivå till Generell användning nivå om käll databasen innehåller minnesbaserade OLTP-objekt.||Matchat|Okt 2019|
|Funktionen Database mail med externa (icke-Azure) e-postservrar som använder säker anslutning||Matchat|Okt 2019|
|Inneslutna databaser stöds inte i SQL-hanterad instans||Matchat|Aug 2019|

### <a name="procedure-sp_send_dbmail-may-transiently-fail-when-query-parameter-is-used"></a>Procedur sp_send_dbmail kan tillfälligt Miss lyckas när @query parametern används

Proceduren sp_send_dbmail kan tillfälligt Miss lyckas när `@query` parametern används. När det här problemet inträffar Miss lyckas varje andra körning av proceduren sp_send_dbmail med fel `Msg 22050, Level 16, State 1` och meddelande `Failed to initialize sqlcmd library with error number -2147467259` . Om du vill kunna se det här felet korrekt ska proceduren anropas med standardvärdet 0 för parametern `@exclude_query_output` , annars kommer felet inte att spridas.
Det här problemet orsakas av en känd bugg som är relaterad till hur sp_send_dbmail använder personifiering och anslutningspoolen.
Undvik det här problemet genom att skicka e-post till en logik för omprövning som förlitar sig på en utdataparameter `@mailitem_id` . Om körningen Miss lyckas kommer parametervärdet att vara NULL, vilket indikerar att sp_send_dbmail ska anropas en gång för att kunna skicka ett e-postmeddelande. Här är ett exempel på den här omprövnings logiken.
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

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>Distribuerade transaktioner kan utföras efter borttagning av hanterade instanser från Server förtroende grupp

[Server förtroende grupper](../managed-instance/server-trust-group-overview.md) används för att upprätta förtroende mellan hanterade instanser som är nödvändiga för att köra [distribuerade transaktioner](./elastic-transactions-overview.md). När du har tagit bort hanterade instanser från gruppen Server förtroende eller tagit bort gruppen kan du fortfarande köra distribuerade transaktioner. Det finns en lösning som du kan använda för att se till att distribuerade transaktioner är inaktiverade och att manuell redundansväxling på den hanterade instansen har [startats](../managed-instance/user-initiated-failover.md) .

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>Distribuerade transaktioner kan inte utföras efter skalnings åtgärden för hanterade instanser

Skalnings åtgärder för hanterade instanser som inkluderar ändring av tjänst nivå eller antal virtuella kärnor kommer att återställa grupp inställningar för Server förtroende på Server delen och inaktivera [distribuerade transaktioner](./elastic-transactions-overview.md). Som en lösning kan du ta bort och skapa en ny [Server förtroende grupp](../managed-instance/server-trust-group-overview.md) på Azure Portal.

### <a name="bulk-insert-and-backuprestore-statements-cannot-use-managed-identity-to-access-azure-storage"></a>BULK INSERT-och BACKUP/Restore-instruktioner kan inte använda hanterad identitet för att komma åt Azure Storage

Mass infognings-, säkerhets kopierings-och återställnings instruktioner och OpenRowSet-funktionen kan inte använda `DATABASE SCOPED CREDENTIAL` med hanterad identitet för att autentisera till Azure Storage. Som en lösning kan du växla till autentisering med delad åtkomst-signatur. Följande exempel fungerar inte på Azure SQL (både databasen och den hanterade instansen):

```sql
CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/curriculum', CREDENTIAL= msi_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

**Lösning**: Använd [signaturen för delad åtkomst för att autentisera till lagring](/sql/t-sql/statements/bulk-insert-transact-sql?view=sql-server-ver15#f-importing-data-from-a-file-in-azure-blob-storage).

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>Tjänstens huvud namn kan inte komma åt Azure AD och AKV

I vissa fall kan det finnas ett problem med tjänstens huvud namn som används för att få åtkomst till Azure AD-och Azure Key Vault-tjänster (AKV). Därför påverkar det här problemet användningen av Azure AD-autentisering och transparent databas kryptering (TDE) med SQL-hanterad instans. Detta kan uppstå som ett tillfälligt anslutnings problem eller inte kan köra instruktioner som till exempel skapa inloggning/användare från extern PROVIDER eller köra som inloggning/användare. Att ställa in TDE med kundhanterad nyckel på en ny Azure SQL-hanterad instans kanske inte fungerar i vissa fall.

**Lösning**: förhindra att det här problemet uppstår på SQL-hanterad instans innan du kör några uppdaterings kommandon, eller om du redan har drabbats av det här problemet efter uppdaterings kommandon, går du till Azure Portal, åtkomst till SQL Managed instance [Active Directory admin-bladet](./authentication-aad-configure.md?tabs=azure-powershell#azure-portal). Kontrol lera om du kan se fel meddelandet "en hanterad instans behöver ett tjänst huvud namn för att få åtkomst till Azure Active Directory. Klicka här om du vill skapa ett huvud namn för tjänsten. Om du har påträffat det här fel meddelandet klickar du på det och följer de stegvisa instruktionerna som angavs tills felet har åtgärd ATS.

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>Återställning av manuell säkerhets kopiering utan kontroll summa kan Miss kopie ras

I vissa fall kan manuell säkerhets kopiering av databaser som gjorts på en hanterad instans utan kontroll Summa kanske inte återställas. I sådana fall kan du försöka återställa säkerhets kopieringen igen tills du är klar.

**Lösning**: gör manuella säkerhets kopieringar av databaser på hanterade instanser med kontroll Summa aktiverat.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>Agenten slutar svara vid ändring, inaktive ring eller aktivering av befintliga jobb

I vissa fall kan det hända att agenten slutar svara om du ändrar, inaktiverar eller aktiverar ett befintligt jobb. Problemet begränsas automatiskt vid identifiering, vilket leder till en omstart av agent processen.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>Behörigheter för resurs gruppen gäller inte för SQL-hanterad instans

När Azure-rollen SQL-hanterad instans deltagare används i en resurs grupp (RG), tillämpas den inte på SQL-hanterad instans och har ingen effekt.

**Lösning**: Konfigurera en SQL-hanterad instans deltagar roll för användare på prenumerations nivå.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Begränsning av manuell redundans via portalen för failover-grupper

Om en failover-grupp sträcker sig över instanser i olika Azure-prenumerationer eller resurs grupper kan inte manuell redundans initieras från den primära instansen i gruppen för redundans.

**Lösning**: starta redundans via portalen från den geo-sekundära instansen.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL Agent-roller behöver uttryckliga EXECUTE-behörigheter för icke-sysadmin-inloggningar

Om icke-sysadmin-inloggningar läggs till i alla [fasta databas roller för SQL-agenten](/sql/ssms/agent/sql-server-agent-fixed-database-roles), finns det ett problem i vilket EXPLICITa körnings behörigheter måste beviljas till de Master-lagrade procedurerna för att dessa inloggningar ska fungera. Om det här problemet uppstår nekades fel meddelandet "KÖRNINGs behörigheten för objektet <object_name> (Microsoft SQL Server, fel: 229)" visas.

**Lösning**: när du lägger till inloggningar till en fast databas roll för SQL-Agent (SQLAgentUserRole, SQLAgentReaderRole eller SQLAgentOperatorRole) för varje inloggnings objekt som har lagts till i rollerna kör du skriptet nedan för att uttryckligen bevilja kör behörighet till de lagrade procedurerna i listan.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>SQL Agent-jobb kan avbrytas efter omstart av agent processen

**(Löst i mars 2020)** SQL-agenten skapar en ny session varje gång ett jobb startas, vilket gradvis ökar minnes användningen. För att undvika att den interna minnes gränsen uppnås, vilket skulle blockera körning av schemalagda jobb, startas agent processen om när minnes användningen når tröskelvärdet. Det kan leda till avbrott i körningen av jobb som körs vid tidpunkten för omstart.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Minnes gränser för minnes intern OLTP tillämpas inte

Den Affärskritisk tjänst nivån kommer inte att tillämpa [högsta minnes gränser för minnesoptimerade objekt](../managed-instance/resource-limits.md#in-memory-oltp-available-space) i vissa fall. SQL-hanterad instans kan göra att arbets belastningen kan använda mer minne för minnes intern OLTP-åtgärder, vilket kan påverka instansens tillgänglighet och stabilitet. InMemory OLTP-frågor som når gränserna kanske inte kommer att Miss genast. Det här problemet kommer snart att åtgärdas. Frågor som använder mer minnes-till-minne OLTP-minne kommer att Miss Miss kort om de når [gränserna](../managed-instance/resource-limits.md#in-memory-oltp-available-space).

**Lösning**: [övervaka minnes intern OLTP-lagring](../in-memory-oltp-monitor-space.md) med [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) för att säkerställa att arbets belastningen inte använder mer än det tillgängliga minnet. Öka de minnes gränser som beror på antalet virtuella kärnor eller optimera arbets belastningen för att använda mindre minne.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Ett fel fel returnerades vid försök att ta bort en fil som inte är tom

SQL Server-och SQL-hanterad instans [låter inte en användare släppa en fil som inte är tom](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Om du försöker ta bort en data fil som inte är tom med en `ALTER DATABASE REMOVE FILE` instruktion `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` returneras inte felet omedelbart. SQL-hanterad instans fortsätter att försöka släppa filen och åtgärden kommer att Miss par efter 30 minuter `Internal server error` .

**Lösning**: ta bort innehållet i filen med hjälp av `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` kommandot. Om det här är den enda filen i fil gruppen måste du ta bort data från tabellen eller partitionen som är kopplade till den här fil gruppen innan du krymper filen och eventuellt läsa in dessa data till en annan tabell/partition.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Ändra tjänst nivå och skapa instans åtgärder blockeras av pågående databas återställning

Kontinuerlig `RESTORE` instruktion, migrering av Datamigrerings tjänsten och inbyggd återställning av tidpunkt kommer att blockera uppdatering av en tjänst nivå eller ändra storlek på den befintliga instansen och skapa nya instanser tills återställnings processen har slutförts. 

Återställnings processen blockerar dessa åtgärder på hanterade instanser och instans-pooler i samma undernät där återställnings processen körs. Instanserna i instans pooler påverkas inte. Det går inte att skapa eller ändra tjänst nivå åtgärder utan tids gräns. De kommer att fortsätta när återställnings processen har slutförts eller avbrutits.

**Lösning**: vänta tills återställnings processen har slutförts, eller Avbryt återställnings processen om åtgärden för att skapa eller uppdatera service-nivå har högre prioritet.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Resource Governor på Affärskritisk tjänst nivå kan behöva konfigureras om efter en redundansväxling

[Resource Governor](/sql/relational-databases/resource-governor/resource-governor) funktionen som gör att du kan begränsa de resurser som är tilldelade till användarens arbets belastning kan felaktigt klassificera viss användar arbets belastning efter en redundansväxling eller en användarinitierad ändring av tjänst nivån (till exempel ändringen av Max vCore eller maximum instans lagrings storlek).

**Lösning**: kör `ALTER RESOURCE GOVERNOR RECONFIGURE` regelbundet eller som en del av ett SQL Agent-jobb som kör SQL-aktiviteten när instansen startar om du använder [Resource Governor](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>Service Broker dialog rutor mellan databaser måste initieras om efter uppgraderingen av service nivå

Service Broker dialog rutor mellan databaser slutar att leverera meddelanden till tjänsterna i andra databaser efter åtgärden ändra tjänst nivå. Meddelandena går *inte förlorade* och de finns i avsändar kön. Om du ändrar virtuella kärnor eller instans lagrings storlek i SQL-hanterad instans `service_broke_guid` ändras värdet i [sys. Databass](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) -vyn för alla databaser. Alla `DIALOG` skapade med en [BEGIN-dialog](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) -instruktion som refererar till tjänst utjämnare i andra databaser kommer att sluta leverera meddelanden till mål tjänsten.

**Lösning**: stoppa alla aktiviteter som använder Service Broker dialog samtal mellan databaser innan du uppdaterar en tjänst nivå och initierar om dem efteråt. Om det finns återstående meddelanden som inte har levererats efter en ändring av en tjänst nivå läser du meddelandena från käll kön och skickar dem igen till målkön.

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>Personifiering av Azure AD-inloggnings typer stöds inte

Personifiering med `EXECUTE AS USER` eller `EXECUTE AS LOGIN` av följande Azure Active Directory (Azure AD)-huvud konton stöds inte:
-   Azure AD-användare med alias. Följande fel returneras i det här fallet: `15517` .
- Azure AD-inloggningar och användare baserat på Azure AD-program eller tjänstens huvud namn. Följande fel returneras i det här fallet: `15517` och `15406` .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query parametern stöds inte i sp_send_db_mail

`@query`Parametern i [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) proceduren fungerar inte.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Transaktionsreplikering måste konfigureras om efter GEO-redundans

Om transaktionell replikering har Aktiver ATS för en databas i en grupp för automatisk redundans, måste SQL-hanterad instans administratör rensa alla publikationer på den gamla primära servern och konfigurera om dem på den nya primära efter det att en redundansväxling till en annan region inträffar. Mer information finns i [replikering](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication).

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>Azure AD-inloggningar och användare stöds inte i SSDT

SQL Server Data Tools har inte fullständigt stöd för inloggningar och användare i Azure AD.

### <a name="temporary-database-is-used-during-restore-operation"></a>Tillfällig databas används under återställnings åtgärden

När en databas återställs i SQL-hanterad instans, skapar återställnings tjänsten först en tom databas med det önskade namnet för att allokera namnet på instansen. Efter en stund kommer den här databasen att tas bort och återställning av den faktiska databasen kommer att startas. 

Databasen som är i *återställnings* tillstånd kommer temporärt att ha ett slumpmässigt GUID-värde i stället för namn. Det tillfälliga namnet kommer att ändras till det önskade namnet som anges i `RESTORE` instruktionen när återställnings processen har slutförts. 

I den inledande fasen kan en användare komma åt den tomma databasen och till och med skapa tabeller eller läsa in data i den här databasen. Den här tillfälliga databasen kommer att tas bort när återställnings tjänsten startar den andra fasen.

**Lösning**: få inte åtkomst till den databas som du återställer förrän du ser att återställningen har slutförts.

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB-strukturen och innehållet har skapats på nytt

`tempdb`Databasen delas alltid upp i 12 datafiler och fil strukturen kan inte ändras. Den maximala storleken per fil kan inte ändras och nya filer kan inte läggas till i `tempdb` . `Tempdb` återskapas alltid som en tom databas när instansen startar eller växlar över och eventuella ändringar som görs i bevaras `tempdb` inte.

### <a name="exceeding-storage-space-with-small-database-files"></a>Överskrida lagrings utrymme med små databasfiler

`CREATE DATABASE`, `ALTER DATABASE ADD FILE` -och- `RESTORE DATABASE` instruktioner kan Miss Miss kan uppstå på grund av att instansen kan uppnå gränsen för Azure Storage.

Varje Generell användning instans av SQL-hanterad instans har upp till 35 TB lagring reserverat för Azure Premium-disk utrymme. Varje databas fil placeras på en separat fysisk disk. Disk storlekar kan vara 128 GB, 256 GB, 512 GB, 1 TB eller 4 TB. Oanvänt utrymme på disken debiteras inte, men den totala summan av storleken på Azure Premium-diskar får inte överstiga 35 TB. I vissa fall kan en hanterad instans som inte behöver 8 TB totalt överskrida gränsen på 35 TB Azure på lagrings storleken på grund av intern fragmentering.

En Generell användning instans av SQL-hanterad instans kan till exempel ha en stor fil som är 1,2 TB i storleken på en 4 TB-disk. Det kan också finnas 248 filer som är 1 GB vardera och som placeras på separata 128 GB-diskar. I det här exemplet:

- Den totala allokerade disk lagrings storleken är 1 x 4 TB + 248 x 128 GB = 35 TB.
- Det totala reserverade utrymmet för databaser på instansen är 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Det här exemplet illustrerar att under vissa omständigheter, på grund av en specifik distribution av filer, kan en instans av SQL-hanterad instans uppnå gränsen på 35 TB som är reserverad för en ansluten Azure Premium-disk, om du inte kan vänta på det.

I det här exemplet fortsätter befintliga databaser att fungera och kan växa utan problem så länge nya filer inte läggs till. Det går inte att skapa eller återställa nya databaser eftersom det inte finns tillräckligt med utrymme för nya disk enheter, även om den totala storleken på alla databaser inte når gränsen för instans storlek. Det fel som returneras i detta fall är inte klart.

Du kan [identifiera antalet återstående filer](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) med hjälp av systemvyer. Om du når den här gränsen kan du försöka att [tömma och ta bort några av de mindre filerna med hjälp av DBCC SHRINKFILE-instruktionen](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) eller växla till [Affärskritisks nivån, som inte har den här gränsen](../managed-instance/resource-limits.md#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>GUID-värden som visas i stället för databas namn

Flera systemvyer, prestanda räknare, fel meddelanden, XEvents och fel logg poster visar GUID-databas identifierare i stället för de faktiska databas namnen. Använd inte dessa GUID-identifierare eftersom de ersätts med faktiska databas namn i framtiden.

**Lösning**: Använd sys. Databass-vyn för att matcha det faktiska databas namnet från det fysiska databas namnet, som anges i form av GUID-databas identifierare:

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Fel loggarna är inte beständiga

Fel loggar som är tillgängliga i SQL Managed instance är inte bestående och deras storlek ingår inte i den maximala lagrings gränsen. Fel loggar kan raderas automatiskt om redundansväxlingen sker. Det kan finnas luckor i fel logg historiken eftersom SQL-hanterad instans har flyttats flera gånger på flera virtuella datorer.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Transaktions omfånget på två databaser inom samma instans stöds inte

**(Löst i mars 2020)** `TransactionScope` Klassen i .net fungerar inte om två frågor skickas till två databaser inom samma instans under samma transaktions omfång:

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

**Lösning (behövs inte sedan mars 2020)**: Använd [SQLConnection. ChangeDatabase (sträng)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) om du vill använda en annan databas i en anslutnings kontext i stället för att använda två anslutningar.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR-moduler och länkade servrar kan ibland inte referera till en lokal IP-adress

CLR-moduler i SQL-hanterad instans och länkade servrar eller distribuerade frågor som refererar till en aktuell instans kan ibland inte matcha IP-adressen för en lokal instans. Det här felet är ett tillfälligt problem.

**Lösning**: Använd kontext anslutningar i en CLR-modul om möjligt.

## <a name="updates"></a>Uppdateringar

En lista över SQL Database uppdateringar och förbättringar finns i [SQL Database service updates](https://azure.microsoft.com/updates/?product=sql-database).

Uppdateringar och förbättringar av alla Azure-tjänster finns i [tjänste uppdateringar](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Bidra med innehåll

Information om hur du bidrar till Azure SQL-dokumentationen finns i [Guide för dokument bidrag](/contribute/).