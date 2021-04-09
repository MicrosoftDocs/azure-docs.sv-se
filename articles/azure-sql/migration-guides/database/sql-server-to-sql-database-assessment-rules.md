---
title: Utvärderings regler för SQL Server att Azure SQL Database migrering
description: Utvärderings regler för att identifiera problem med käll SQL Servers instansen som måste åtgärdas innan du migrerar till Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: c407b62b5f0308fbb6d9ff7223a3554ca09592db
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027218"
---
# <a name="assessment-rules-for-sql-server-to-azure-sql-database-migration"></a>Utvärderings regler för SQL Server att Azure SQL Database migrering
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Migreringsverktyg verifierar din käll SQL Server instans genom att köra ett antal utvärderings regler för att identifiera problem som måste åtgärdas innan du migrerar SQL Server-databasen till Azure SQL Database. 

Den här artikeln innehåller en lista över regler som används för att utvärdera möjligheten att migrera SQL Server databasen till Azure SQL Database. 


## <a name="bulk-insert"></a>Mass infogning<a id="BulkInsert"></a>

**Title: BULK INSERT med en BLOB-datakälla som inte är Azure-datakälla stöds inte i Azure SQL Database.**   
**Kategori**: problem   

**Beteckning**   
Azure SQL Database kan inte komma åt fil resurser eller Windows-mappar. Se avsnittet "påverkade objekt" för den speciella användningen av BULK INSERT-uttryck som inte refererar till en Azure-blob. Objekt med BULK INSERT där källan inte är Azure Blob Storage fungerar inte efter migrering till Azure SQL Database. 


**Rekommenderade**   
Du måste konvertera BULK INSERT-instruktioner som använder lokala filer eller fil resurser för att använda filer från Azure Blob Storage i stället för att migrera till Azure SQL Database. Du kan också migrera till SQL Server på en virtuell Azure-dator.

## <a name="compute-clause"></a>Compute-sats<a id="ComputeClause"></a>

**Title: Compute-satsen upphör att gälla och har tagits bort.**   
**Kategori**: varning   

**Beteckning**   
Compute-satsen genererar summor som visas som ytterligare sammanfattnings kolumner i slutet av resultat uppsättningen. Den här satsen stöds dock inte längre i Azure SQL Database. 


**Rekommenderade**   
T-SQL-modulen måste skrivas om med SAMMANSLAGNINGs operatorn i stället. Koden nedan visar hur beräkning kan ersättas med sammanslagning: 

```sql 
USE AdventureWorks 
GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP; 
```

Mer information: utgångna [databas motor funktioner i SQL Server ](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="change-data-capture-cdc"></a>Registrering av ändrings data (CDC)<a id="CDC"></a>

**Title: insamlingen av ändrings data (CDC) stöds inte i Azure SQL Database**   
**Kategori**: problem   


**Beteckning**   
CDC (Change data Capture) stöds inte i Azure SQL Database. Utvärdera om Ändringsspårning kan användas i stället.  Du kan också migrera till en Azure SQL-hanterad instans eller SQL Server på Azure Virtual Machines. 


**Rekommenderade**   
CDC (Change data Capture) stöds inte i Azure SQL Database. Utvärdera om Ändringsspårning kan användas i stället eller om du vill migrera till en Azure SQL-hanterad instans.

Mer information: [Aktivera ändrings spårning i Azure SQL](https://social.technet.microsoft.com/wiki/contents/articles/2976.azure-sql-how-to-enable-change-tracking.aspx)

## <a name="clr-assemblies"></a>CLR-sammansättningar<a id="ClrAssemblies"></a>

**Title: SQL CLR-sammansättningar stöds inte i Azure SQL Database**   
**Kategori**: problem   


**Beteckning**   
Azure SQL Database stöder inte SQL CLR-sammansättningar. 


**Rekommenderade**   
Det finns för närvarande inget sätt att uppnå detta i Azure SQL Database. De rekommenderade alternativa lösningarna kräver program kod och databas ändringar för att endast använda sammansättningar som stöds av Azure SQL Database. Du kan också migrera till en Azure SQL-hanterad instans eller SQL Server på en virtuell Azure-dator

Mer information: [Transact-SQL-skillnader som inte stöds i SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)

## <a name="cryptographic-provider"></a>Kryptografiprovider<a id="CryptographicProvider"></a>

**Title: en användning av en KRYPTOGRAFIPROVIDER eller en ALTER CRYPTOGRAPHIC PROVIDER hittades, vilket inte stöds i Azure SQL Database**   
**Kategori**: problem   

**Beteckning**   
Azure SQL Database stöder inte KRYPTOGRAFISKa PROVIDER-instruktioner eftersom det inte går att komma åt filer. Se avsnittet påverkade objekt för de olika användnings områdena för KRYPTOGRAFIPROVIDERs. Objekt med `CREATE CRYPTOGRAPHIC PROVIDER` eller `ALTER CRYPTOGRAPHIC PROVIDER` fungerar inte korrekt efter migrering till Azure SQL Database.  


**Rekommenderade**   
Granska objekt med `CREATE CRYPTOGRAPHIC PROVIDER` eller `ALTER CRYPTOGRAPHIC PROVIDER` . Ta bort användningen av dessa funktioner i alla sådana objekt som krävs. Du kan också migrera till SQL Server på en virtuell Azure-dator

## <a name="cross-database-references"></a>Kors databas referenser<a id="CrossDataseReferences"></a>

**Title: frågor mellan databaser stöds inte i Azure SQL Database**   
**Kategori**: problem   

**Beteckning**   
Databaser på den här servern använder frågor över databaser, som inte stöds i Azure SQL Database. 


**Rekommenderade**   
Azure SQL Database stöder inte kors databas frågor. Följande åtgärder rekommenderas: 
- Migrera beroende databaser till Azure SQL Database och Använd Elastic Databases fråga (för närvarande i för hands version) för att fråga över Azure SQL-databaser. 
- Flytta de beroende data uppsättningarna från andra databaser till databasen som migreras. 
- Migrera till en Azure SQL-hanterad instans.
- Migrera till SQL Server på den virtuella Azure-datorn. 

Mer information: [kontrol lera Azure SQL Database elastisk databas fråga (förhands granskning)](../../database/elastic-query-overview.md)

## <a name="database-compatibility"></a>Databasens kompatibilitet<a id="DbCompatLevelLowerThan100"></a>

**Title: Azure SQL Database stöder inte kompatibilitetsnivån under 100.**   
**Kategori**: varning   

**Beteckning**   
Kompatibilitetsnivån för databaser är ett värdefullt verktyg som hjälper dig i databas modernisering, genom att tillåta att SQL Server databas motorn uppgraderas, samtidigt som du behåller funktions status för att ansluta program genom att underhålla samma kompatibilitetsnivå som för hands versionen av databasen. Azure SQL Database stöder inte kompatibilitetsnivån under 100. 


**Rekommenderade**   
Utvärdera om programfunktionerna är intakta när kompatibilitetsnivån för databas uppgraderas till 100 på Azure SQL-hanterad instans. Du kan också migrera till SQL Server på en virtuell Azure-dator

## <a name="database-mail"></a>Database-mail<a id="DatabaseMail"></a>

**Title: Database Mail stöds inte i Azure SQL Database.**   
**Kategori**: varning   

**Beteckning**   
Den här servern använder Database Mail-funktionen, som inte stöds i Azure SQL Database.


**Rekommenderade**   
Överväg att migrera till en Azure SQL-hanterad instans som stöder Database Mail.  Du kan också överväga att använda Azure Functions och SendGrid för att utföra e-postfunktioner på Azure SQL Database.

Mer information: [skicka e-post från Azure SQL Database med Azure Functions-skript](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)


## <a name="database-principal-alias"></a>Databasens huvud namn<a id="DatabasePrincipalAlias"></a>

**Title: SYS. DATABASE_PRINCIPAL_ALIASES upphör att gälla och har tagits bort.**   
**Kategori**: problem   

**Beteckning**   
SYS. DATABASE_PRINCIPAL_ALIASES upphör att gälla och har tagits bort i Azure SQL Database.  


**Rekommenderade**   
Använd roller i stället för alias.

Mer information: utgångna [databas motor funktioner i SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK alternativ<a id="DisableDefCNSTCHK"></a>

**Title: SET-alternativet DISABLE_DEF_CNST_CHK upphör att gälla och har tagits bort.**   
**Kategori**: problem   

**Beteckning**   
SET option DISABLE_DEF_CNST_CHK upphör att gälla och har tagits bort i Azure SQL Database.  


Mer information: utgångna [databas motor funktioner i SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW-tips<a id="FastFirstRowHint"></a>

**Title: FASTFIRSTROW-frågetipset upphör att gälla och har tagits bort.**   
**Kategori**: varning   

**Beteckning**   
FASTFIRSTROW-frågetipset har avbrutits och har tagits bort i Azure SQL Database.  


**Rekommenderade**   
I stället för FASTFIRSTROW-frågetipset använder du alternativ (FAST n).

Mer information: utgångna [databas motor funktioner i SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>-<a id="FileStream"></a>

**Title: FILESTREAM stöds inte i Azure SQL Database**   
**Kategori**: problem   

**Beteckning**   
FILESTREAM-funktionen, som gör att du kan lagra ostrukturerade data, till exempel text dokument, bilder och videor i NTFS-filsystem, stöds inte i Azure SQL Database. 


**Rekommenderade**   
Ladda upp de ostrukturerade filerna till Azure Blob Storage och lagra metadata som är relaterade till dessa filer (namn, typ, URL-plats, lagrings nyckel osv.) i Azure SQL Database. Du kan behöva skapa en ny tekniker för ditt program för att aktivera strömmande blobbar till och från Azure SQL Database. Du kan också migrera till SQL Server på en virtuell Azure-dator.

Mer information: [strömma blobbar till och från Azure SQL-bloggen](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)


## <a name="linked-server"></a>Länkad server<a id="LinkedServer"></a>

**Title: länkade Server funktioner stöds inte i Azure SQL Database**   
**Kategori**: problem   

**Beteckning**   
Länkade servrar gör det möjligt för SQL Server Database Engine att köra kommandon mot OLE DB data källor utanför instansen av SQL Server. 


**Rekommenderade**   
Azure SQL Database stöder inte länkade Server funktioner. Följande åtgärder rekommenderas för att eliminera behovet av länkade servrar: 
- Identifiera beroende data uppsättningar från fjärranslutna SQL-servrar och Överväg att flytta dem till den databas som migreras. 
- Migrera beroende databaser till Azure och Använd Elastic Database fråga (för hands version) för att fråga över databaser i Azure SQL Database. 

Mer information: [kontrol lera Azure SQL Database elastisk fråga (förhands granskning)](../../database/elastic-query-overview.md) 

## <a name="ms-dtc"></a>MS DTC<a id="MSDTCTransactSQL"></a>

**Title: det går inte att starta DISTRIBUERAd transaktion i Azure SQL Database.**   
**Kategori**: problem   

**Beteckning**   
Distribuerad transaktion som startas av Transact SQL BEGIN DISTRIBUTEd TRANSACTION och hanteras av Microsoft Distributed Transaction Coordinator (MS DTC) stöds inte i Azure SQL Database.  


**Rekommenderade**   
Avsnittet granska påverkade objekt i Azure Migrate för att se alla objekt som använder BEGIN utspridda-transaktion. Överväg att migrera deltagar databaserna till den Azure SQL-hanterade instans där distribuerade transaktioner över flera instanser stöds (för närvarande i för hands version). Du kan också migrera till SQL Server på en virtuell Azure-dator.

Mer information: [transaktioner över flera servrar för Azure SQL-hanterad instans ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="openrowset-bulk"></a>OpenRowSet (bulk)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Title: OpenRowSet som används i Mass åtgärder med icke-Azure Blob Storage data källa stöds inte i Azure SQL Database.**   
**Kategori**: problem   

**Beskrivning** OpenRowSet stöder Mass åtgärder via en inbyggd Mass leverantör som gör det möjligt för data från en fil att läsas och returneras som en rad uppsättning. OpenRowSet med icke-Azure Blob Storage-datakälla stöds inte i Azure SQL Database.


**Rekommenderade**   
Azure SQL Database kan inte komma åt fil resurser och Windows-mappar, så filerna måste importeras från Azure Blob Storage. Därför stöds endast data källan för BLOB Type i OpenRowSet-funktionen. Du kan också migrera till SQL Server på en virtuell Azure-dator

Mer information: [lösa skillnader i Transact-SQL vid migrering till SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="openrowset-provider"></a>OpenRowSet (Provider)<a id="OpenRowsetWithSQLAndNonSQLProvider"></a>

**Title: OpenRowSet med SQL eller icke-SQL-providern stöds inte i Azure SQL Database.**   
**Kategori**: problem   

**Beteckning**   
OpenRowSet med SQL eller icke-SQL-Provider är ett alternativ till att få åtkomst till tabeller i en länkad server och som är en engångs ad hoc-metod för att ansluta och komma åt fjärrdata med hjälp av OLE DB. OpenRowSet med SQL eller icke-SQL-providern stöds inte i Azure SQL Database.


**Rekommenderade**   
Azure SQL Database stöder endast OpenRowSet för import från Azure Blob Storage. Du kan också migrera till SQL Server på en virtuell Azure-dator

Mer information: [lösa skillnader i Transact-SQL vid migrering till SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="non-ansi-left-outer-join"></a>Icke-ANSI vänster yttre koppling<a id="NonANSILeftOuterJoinSyntax"></a>

**Title: icke-ANSI-stil vänster yttre koppling upphör att gälla och har tagits bort.**   
**Kategori**: varning   

**Beteckning**   
Vänster yttre koppling i icke-ANSI-format upphör att gälla och har tagits bort i Azure SQL Database. 


**Rekommenderade**   
Använd ANSI Join-syntax.

Mer information: utgångna [databas motor funktioner i SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="non-ansi-right-outer-join"></a>Yttre koppling från icke-ANSI-höger<a id="NonANSIRightOuterJoinSyntax"></a>

**Title: icke-ANSI-stil höger yttre koppling är invecklad och har tagits bort.**   
**Kategori**: varning   

**Beteckning**   
Icke-ANSI-stil höger yttre koppling är invecklad och har tagits bort i Azure SQL Database. 


**Rekommenderade**   
Använd ANSI Join-syntax.

Mer information: utgångna [databas motor funktioner i SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="next-column"></a>Nästa kolumn<a id="NextColumn"></a>

**Rubrik: tabeller och kolumner som heter nästa leder till ett fel i Azure SQL Database.**   
**Kategori**: problem   

**Beteckning**   
Tabeller eller kolumner som heter NEXT har identifierats. Sekvenser, som introducerades i Microsoft SQL Server, använder ANSI-standardfunktionen NEXT VALUE FOR. Om en tabell eller kolumn heter nästa och kolumnen har ett alias som värde, och om ANSI-standarden som utelämnas, kan resulterande instruktion orsaka ett fel.  


**Rekommenderade**   
Skriv om-instruktioner för att inkludera ANSI-standarden som nyckelord när en tabell eller kolumn aliasas. Till exempel, när en kolumn heter nästa och kolumnen har ett alias som värde, `SELECT NEXT VALUE FROM TABLE` kommer frågan att orsaka ett fel och skrivas om som Välj nästa som värde från tabellen. När en tabell heter nästa och tabellen har ett alias som värde, `SELECT Col1 FROM NEXT VALUE` kommer frågan att orsaka ett fel och bör skrivas om som `SELECT Col1 FROM NEXT AS VALUE` .

## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Title: äldre stil RAISERROR-anropen ska ersättas med moderna motsvarigheter.**   
**Kategori**: varning   

**Beteckning**   
RAISERROR-anrop som exemplet nedan kallas äldre format eftersom de inte innehåller kommatecken och parentesen. `RAISERROR 50001 'this is a test'`. Den här metoden för att anropa RAISERROR upphör att gälla och tas bort i Azure SQL Database. 


**Rekommenderade**   
Skriv om instruktionen med den aktuella RAISERROR-syntaxen eller utvärdera om den moderna metoden `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` är genomförbar.

Mer information: utgångna [databas motor funktioner i SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="server-audits"></a>Server granskningar<a id="ServerAudits"></a>

**Rubrik: Använd Azure SQL Database gransknings funktioner för att ersätta server granskningar**   
**Kategori**: varning   

**Beteckning**   
Server granskningar stöds inte i Azure SQL Database.


**Rekommenderade**   
Överväg att Azure SQL Database gransknings funktioner för att ersätta server revisioner.  Azure SQL stöder granskning och funktionerna är mer omfattande än SQL Server. Azure SQL Database kan granska olika databas åtgärder och händelser, inklusive: åtkomst till data, schema ändringar (DDL), data ändringar (DML), konton, roller och behörigheter (DCL, säkerhets undantag. Azure SQL Database granskning ökar en organisations möjlighet att få djupgående insikt i händelser och ändringar som sker i databasen, inklusive uppdateringar och frågor mot data. Du kan också migrera till en Azure SQL-hanterad instans eller SQL Server på en virtuell Azure-dator.

Mer information: [granskning för Azure SQL Database ](../../database/auditing-overview.md)

## <a name="server-credentials"></a>Autentiseringsuppgifter för Server<a id="ServerCredentials"></a>

**Title: serverns begränsade autentiseringsuppgifter stöds inte i Azure SQL Database**   
**Kategori**: varning   

**Beteckning**   
En autentiseringsuppgift är en post som innehåller den autentiseringsinformation (autentiseringsuppgifter) som krävs för att ansluta till en resurs utanför SQL Server. Azure SQL Database stöder autentiseringsuppgifter för databasen, men inte de som skapats i SQL Servers området.   


**Rekommenderade**   
Azure SQL Database stöder referenser till databasens omfång. Konvertera serverns begränsade autentiseringsuppgifter till databasens begränsade autentiseringsuppgifter. Du kan också migrera till en Azure SQL-hanterad instans eller SQL Server på en virtuell Azure-dator

Mer information: [skapa databasens begränsade autentiseringsuppgifter](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)

## <a name="service-broker"></a>Service Broker<a id="ServiceBroker"></a>

**Title: Service Broker-funktionen stöds inte i Azure SQL Database**   
**Kategori**: problem   

**Beteckning**   
SQL Server Service Broker tillhandahåller inbyggt stöd för meddelanden och köer i SQL Server databas motor. Service Broker funktionen stöds inte i Azure SQL Database.


**Rekommenderade**   
Service Broker funktionen stöds inte i Azure SQL Database. Överväg att migrera till en Azure SQL-hanterad instans som stöder Service Broker inom samma instans. Du kan också migrera till SQL Server på en virtuell Azure-dator. 

## <a name="server-scoped-triggers"></a>Utlösare för Server omfång<a id="ServerScopedTriggers"></a>

**Title: utlösare för Server omfång stöds inte i Azure SQL Database**   
**Kategori**: varning   

**Beteckning**   
En utlösare är en särskild typ av lagrad procedur som körs som svar på en viss åtgärd i en tabell som infogning, borttagning eller uppdatering av data. Server-begränsade utlösare stöds inte i Azure SQL Database. Azure SQL Database stöder inte följande alternativ för utlösare: för inloggning, kryptering, med Lägg till, inte för replikering, externt namn alternativ (det finns inget stöd för extern metod), alla SERVER alternativ (DDL-utlösare), Utlös vid en INLOGGNINGs händelse (inloggnings utlösare), Azure SQL Database stöder inte CLR-utlösare.


**Rekommenderade**   
Använd trigger på databas nivå i stället. Du kan också migrera till en Azure SQL-hanterad instans eller SQL Server på en virtuell Azure-dator

Mer information: [lösa skillnader i Transact-SQL vid migrering till SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="sql-agent-jobs"></a>SQL Agent-jobb<a id="AgentJobs"></a>

**Rubrik: SQL Server Agent jobb är inte tillgängliga i Azure SQL Database**   
**Kategori**: varning   

**Beteckning**   
SQL Server Agent är en Microsoft Windows-tjänst som kör schemalagda administrativa uppgifter, som kallas jobb i SQL Server. SQL Server Agent-jobb är inte tillgängliga i Azure SQL Database. 


**Rekommendation** Använd elastiska jobb (förhands granskning) som ersätter SQL Server Agent jobb i Azure SQL Database. Elastic Database-jobb för Azure SQL Database gör att du kan köra T-SQL-skript som sträcker sig över flera databaser samtidigt som du försöker igen och ger dig slutliga garantier för slutförandet. Du kan också överväga att migrera till en hanterad Azure SQL-instans eller SQL Server på Azure Virtual Machines.

Mer information: [komma igång med Elastic Database-jobb (för hands version) ](../../database/elastic-jobs-overview.md)

## <a name="sql-database-size"></a>SQL Database storlek<a id="SQLDBDatabaseSize"></a>

**Title: Azure SQL Database har inte stöd för större databas storlekar än 100 TB.**   
**Kategori**: problem   

**Beteckning**   
Databasens storlek är större än den högsta tillåtna storleken på 100 TB. 


**Rekommenderade**   
Utvärdera om data kan arkiveras eller komprimeras eller shardades i flera databaser. Du kan också migrera till SQL Server på en virtuell Azure-dator. 

Mer information: [vCore Resource Limits](../../database/resource-limits-vcore-single-databases.md) 

## <a name="sql-mail"></a>SQL-e-post<a id="SqlMail"></a>

**Title: SQL Mail har avbrutits.**   
**Kategori**: varning   

**Beteckning**   
SQL Mail har avbrutits och tagits bort i Azure SQL Database.


**Rekommenderade**   
Överväg att migrera till en hanterad Azure SQL-instans eller SQL Server på Azure Virtual Machines och använda Database Mail.

Mer information: utgångna [databas motor funktioner i SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>

**Title: identifierade instruktioner som refererar till borttagna system lagrade procedurer som inte är tillgängliga i Azure SQL Database.**   
**Kategori**: varning   

**Beteckning**   
Följande system och utökade lagrade procedurer kan inte användas i Azure SQL Database –,,,,, `sp_dboption` `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` `sp_activedirectory_start` .


**Rekommenderade**    
Ta bort referenser till system procedurer som inte stöds och som har tagits bort i Azure SQL Database.

Mer information: utgångna [databas motor funktioner i SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="trace-flags"></a>Spårnings flaggor<a id="TraceFlags"></a>

**Title: Azure SQL Database stöder inte spårnings flaggor**   
**Kategori**: varning   

**Beteckning**   
Spårnings flaggor används för att tillfälligt ange specifika Server egenskaper eller inaktivera ett visst beteende. Spårnings flaggor används ofta för att diagnostisera prestanda problem eller felsöka lagrade procedurer eller komplexa dator system. Azure SQL Database stöder inte spårnings flaggor. 


**Rekommenderade**   
Avsnittet granska påverkade objekt i Azure Migrate för att se alla spårnings flaggor som inte stöds i Azure SQL Database och utvärdera om de kan tas bort. Du kan också migrera till en Azure SQL-hanterad instans som stöder ett begränsat antal globala spårnings flaggor eller SQL Server på en virtuell Azure-dator.

Mer information: [lösa skillnader i Transact-SQL vid migrering till SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="windows-authentication"></a>Windows-autentisering<a id="WindowsAuthentication"></a>

**Title: databas användare som mappas med Windows-autentisering (integrerad säkerhet) stöds inte i Azure SQL Database.**   
**Kategori**: varning   

**Beteckning**   
Azure SQL Database stöder två typer av autentisering 
- SQL-autentisering: använder ett användar namn och lösen ord 
- Azure Active Directory autentisering: använder identiteter som hanteras av Azure Active Directory och stöds för hanterade och integrerade domäner. 

Databas användare som mappas med Windows-autentisering (integrerad säkerhet) stöds inte i Azure SQL Database. 



**Rekommenderade**   
Federera den lokala Active Directory med Azure Active Directory. Windows-identiteten kan sedan ersättas med motsvarande Azure Active Directory identiteter. Du kan också migrera till SQL Server på en virtuell Azure-dator.

Mer information: [SQL Database säkerhets funktioner](../../database/security-overview.md#authentication)

## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Title: xp_cmdshell stöds inte i Azure SQL Database.**   
**Kategori**: problem   

**Beteckning**   
xp_cmdshell som skapar ett Windows Command Shell och skickar i en sträng för körning stöds inte i Azure SQL Database. 


**Rekommenderade**   
Avsnittet granska påverkade objekt i Azure Migrate för att se alla objekt med xp_cmdshell och utvärdera om referensen till xp_cmdshell eller det påverkade objektet kan tas bort. Överväg också att utforska Azure Automation som levererar molnbaserad automatiserings-och konfigurations tjänst. Du kan också migrera till SQL Server på en virtuell Azure-dator. 

## <a name="next-steps"></a>Nästa steg

Information om hur du börjar migrera SQL Server till Azure SQL Database finns i [SQL Server till SQL Database migration guide](sql-server-to-sql-database-guide.md).

- För en matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering samt särskilda uppgifter, se [tjänst och verktyg för datamigrering](../../../dms/dms-tools-matrix.md).

- Mer information om SQL Database finns i:
   - [Översikt över Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Kostnad för total ägande kostnad för Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Mer information om ramverket och implementerings cykeln för molnbaserad migrering finns i
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metod tips för kostnads-och storleks arbets belastningar migreras till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Information om hur du bedömer program åtkomst lagret finns i [Data Access Migration Toolkit (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Mer information om hur du utför data åtkomst Layer A/B-testning finns [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
