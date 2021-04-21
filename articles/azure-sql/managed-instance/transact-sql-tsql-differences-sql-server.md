---
title: T-SQL-skillnader mellan SQL Server & Azure SQL Managed Instance
description: I den här artikeln beskrivs skillnaderna i Transact-SQL (T-SQL) mellan en Azure SQL Managed Instance och SQL Server.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: reference
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, bonova, danil
ms.date: 3/16/2021
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: f744b718919a6da75b2064efdc163ef4618b5a7c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815908"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>T-SQL-skillnader mellan SQL Server & Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här artikeln sammanfattar och förklarar skillnaderna i syntax och beteende mellan Azure SQL Managed Instance och SQL Server. 


SQL Managed Instance ger hög kompatibilitet med SQL Server databasmotorn och de flesta funktioner stöds i en SQL Managed Instance.

![Enkel migrering från SQL Server](./media/transact-sql-tsql-differences-sql-server/migration.png)

Det finns vissa PaaS-begränsningar som introduceras i SQL Managed Instance och vissa beteendeändringar jämfört med SQL Server. Skillnaderna är indelade i följande kategorier: <a name="Differences"></a>

- [Tillgängligheten](#availability) omfattar skillnaderna i [Always On-tillgänglighetsgrupper](#always-on-availability-groups) och [säkerhetskopieringar.](#backup)
- [Säkerheten](#security) omfattar skillnaderna i [granskning,](#auditing) [certifikat,](#certificates) [autentiseringsuppgifter,](#credential)kryptografiska [providers,](#cryptographic-providers)inloggningar och användare [samt](#logins-and-users) [tjänstnyckeln och huvudnyckeln för tjänsten.](#service-key-and-service-master-key)
- [Konfigurationen](#configuration) omfattar skillnaderna i [buffertpooltillägg,](#buffer-pool-extension) [sortering,](#collation)kompatibilitetsnivåer, [](#compatibility-levels)databasspegling, [](#database-options)databasalternativ , [SQL Server Agent](#sql-server-agent)och [tabellalternativ](#tables). [](#database-mirroring)
- [Funktionerna omfattar](#functionalities) [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [distribuerade](#distributed-transactions)transaktioner, [utökade](#extended-events) [händelser,](#external-libraries)externa bibliotek, filström och [FileTable,](#filestream-and-filetable) [fulltexts-semantisk](#full-text-semantic-search) [sökning,](#linked-servers)länkade servrar , [PolyBase](#polybase) [,](#replication)replikering, [RESTORE](#restore-statement), [Service Broker,](#service-broker)lagrade procedurer, funktioner och [utlösare.](#stored-procedures-functions-and-triggers)
- [Miljöinställningar](#Environment) som virtuella nätverk och undernätskonfigurationer.

De flesta av dessa funktioner är arkitekturbegränsningar och representerar tjänstfunktioner.

Tillfälliga kända problem som identifieras i SQL Managed Instance och kommer att åtgärdas i framtiden beskrivs på sidan [med information om den här versionen.](../database/doc-changes-updates-release-notes.md)

## <a name="availability"></a>Tillgänglighet

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>AlwaysOn-tillgänglighetsgrupper

[Hög tillgänglighet](../database/high-availability-sla.md) är inbyggt SQL Managed Instance och kan inte styras av användarna. Följande instruktioner stöds inte:

- [SKAPA SLUTPUNKT ... FÖR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [SKAPA TILLGÄNGLIGHETSGRUPP](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ÄNDRA TILLGÄNGLIGHETSGRUPP](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [TA BORT TILLGÄNGLIGHETSGRUPP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- [SET HADR-satsen](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) i [ALTER DATABASE-instruktionen](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

SQL Managed Instance har automatiska säkerhetskopieringar så att användarna kan skapa fullständiga `COPY_ONLY` databassäkerhetskopior. Differentiella säkerhetskopior, loggsäkerhetskopior och säkerhetskopior av ögonblicksbilder stöds inte.

- Med en SQL Managed Instance kan du bara backa upp en instansdatabas till ett Azure Blob Storage-konto:
  - Endast `BACKUP TO URL` stöds.
  - `FILE`Enheter `TAPE` för , och säkerhetskopiering stöds inte.
- De flesta av de `WITH` allmänna alternativen stöds.
  - `COPY_ONLY` är obligatoriskt.
  - `FILE_SNAPSHOT` stöds inte.
  - Bandalternativ: `REWIND` `NOREWIND` , , och `UNLOAD` stöds `NOUNLOAD` inte.
  - Loggspecifika alternativ: `NORECOVERY` `STANDBY` , `NO_TRUNCATE` och stöds inte.

Begränsningar: 

- Med en SQL Managed Instance kan du säkerhetskopiera en instansdatabas till en säkerhetskopia med upp till 32 remsor, vilket är tillräckligt för databaser upp till 4 TB om komprimering av säkerhetskopior används.
- Du kan inte köra `BACKUP DATABASE ... WITH COPY_ONLY` på en databas som är krypterad med TDE (service managed transparent datakryptering). Tjänst-hanterad TDE tvingar säkerhetskopior att krypteras med en intern TDE-nyckel. Nyckeln kan inte exporteras, så du kan inte återställa säkerhetskopian. Använd automatiska säkerhetskopieringar och återställning till tidpunkt, eller använd [kund-hanterad TDE (BYOK) i](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key) stället. Du kan också inaktivera kryptering på databasen.
- Interna säkerhetskopior som tas på en hanterad instans kan inte återställas till en SQL Server. Det beror på att Managed Instance har en högre intern databasversion jämfört med någon version av SQL Server.
- Den maximala stripe-storleken för säkerhetskopiering med `BACKUP` hjälp av kommandot i SQL Managed Instance är 195 GB, vilket är den maximala blobstorleken. Öka antalet strimlar i säkerhetskopieringskommandot för att minska den enskilda stripe-storleken och hålla dig inom den här gränsen.

    > [!TIP]
    > För att komma runt den här begränsningen kan du göra följande när du SQL Server en databas från en lokal miljö eller på en virtuell dator:
    >
    > - Backa upp `DISK` till i stället för att backa upp till `URL` .
    > - Ladda upp de säkerhetskopierade filerna till Blob Storage.
    > - Återställ till SQL Managed Instance.
    >
    > Kommandot i SQL Managed Instance stöder större blobstorlekar i säkerhetskopieringsfilerna eftersom en annan blobtyp används för lagring av de `Restore` uppladdade säkerhetskopieringsfilerna.

Information om säkerhetskopieringar med T-SQL finns i [BACKUP](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Säkerhet

### <a name="auditing"></a>Granskning

De viktigaste skillnaderna mellan granskning i Microsoft Azure SQL och SQL Server är:

- Med SQL Managed Instance fungerar granskning på servernivå. `.xel`Loggfilerna lagras i Azure Blob Storage.
- Med Azure SQL Database fungerar granskning på databasnivå. `.xel`Loggfilerna lagras i Azure Blob Storage.
- Med SQL Server, lokalt eller på virtuella datorer fungerar granskning på servernivå. Händelser lagras i filsystem eller Windows-händelseloggar.
 
XEvent-granskning i SQL Managed Instance Azure Blob Storage-mål. Fil- och Windows-loggar stöds inte.

De viktigaste skillnaderna i `CREATE AUDIT` syntaxen för granskning i Azure Blob Storage är:

- En ny syntax `TO URL` tillhandahålls som du kan använda för att ange URL:en för Azure Blob Storage-containern där filerna `.xel` placeras.
- Syntaxen `TO FILE` stöds inte eftersom SQL Managed Instance inte kan komma åt Windows-filresurser.

Mer information finns i: 

- [SKAPA SERVERGRANSKNING](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Granskning](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certifikat

SQL Managed Instance kan inte komma åt filresurser och Windows-mappar, så följande begränsningar gäller:

- Filen `CREATE FROM` / `BACKUP TO` stöds inte för certifikat.
- Certifikatet `CREATE` / `BACKUP` från `FILE` / `ASSEMBLY` stöds inte. Privata nyckelfiler kan inte användas. 

Se [SKAPA CERTIFIKAT och](/sql/t-sql/statements/create-certificate-transact-sql) [SÄKERHETSKOPIERA CERTIFIKAT.](/sql/t-sql/statements/backup-certificate-transact-sql) 
 
**Lösning:** I stället för att skapa en säkerhetskopia av ett certifikat och återställa säkerhetskopian hämtar du certifikatets binära innehåll och privata nyckel, lagrar det som [.sql-fil](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)och skapar från binärt :

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Autentiseringsuppgift

Endast Azure Key Vault och `SHARED ACCESS SIGNATURE` identiteter stöds. Windows-användare stöds inte.

Se [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql) och [ALTER CREDENTIAL](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Kryptografiska providers

SQL Managed Instance kan inte komma åt filer, så kryptografiska providers kan inte skapas:

- `CREATE CRYPTOGRAPHIC PROVIDER` stöds inte. Se [SKAPA KRYPTOGRAFIPROVIDER.](/sql/t-sql/statements/create-cryptographic-provider-transact-sql)
- `ALTER CRYPTOGRAPHIC PROVIDER` stöds inte. Se ALTER CRYPTOGRAPHIC PROVIDER ( [ÄNDRA KRYPTOGRAFIPROVIDER).](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)

### <a name="logins-and-users"></a>Inloggningar och användare

- SQL-inloggningar som skapats `FROM CERTIFICATE` med hjälp av , och `FROM ASYMMETRIC KEY` `FROM SID` stöds. Se [SKAPA INLOGGNING.](/sql/t-sql/statements/create-login-transact-sql)
- Azure Active Directory (Azure AD) serverhuvudnamn (inloggningar) som skapats med syntaxen [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) eller CREATE USER FROM [LOGIN [Azure AD Login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true) stöds. Dessa inloggningar skapas på servernivå.

    SQL Managed Instance stöder Azure AD-databashuvudnamn med syntaxen `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` . Den här funktionen kallas även för användare av inneslutna databaser i Azure AD.

- Windows-inloggningar som skapats `CREATE LOGIN ... FROM WINDOWS` med syntaxen stöds inte. Använd Azure Active Directory inloggningar och användare.
- Den Azure AD-användare som skapade instansen har obegränsad [administratörsbehörighet.](../database/logins-create-manage.md)
- Icke-administratörsanvändare på Azure AD-databasnivå kan skapas med hjälp av `CREATE USER ... FROM EXTERNAL PROVIDER` syntaxen. Se [SKAPA ANVÄNDARE... FRÅN EXTERN PROVIDER](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).
- Azure AD-serverhuvudnamn (inloggningar) stöder SQL-funktioner inom SQL Managed Instance enda. Funktioner som kräver interaktion mellan instanser, oavsett om de finns i samma Azure AD-klientorganisation eller olika klienter, stöds inte för Azure AD-användare. Exempel på sådana funktioner är:

  - SQL-transaktionsreplikering.
  - Länkserver.

- Det går inte att ange en Azure AD-inloggning som mappats till en Azure AD-grupp som databasägare.
- Personifiering av Azure AD-huvudnamn på servernivå med hjälp av andra Azure AD-huvudnamn stöds, till exempel [EXECUTE AS-satsen.](/sql/t-sql/statements/execute-as-transact-sql) EXECUTE AS-begränsningar är:

  - EXECUTE AS USER stöds inte för Azure AD-användare när namnet skiljer sig från inloggningsnamnet. Ett exempel är när användaren skapas via syntaxen CREATE USER [myAadUser] FROM LOGIN [ ] och personifiering görs via john@contoso.com EXEC AS USER = _myAadUser_. När du skapar en **ANVÄNDARE** från ett Azure AD-serverhuvudkonto (inloggning) anger du user_name som samma login_name från **LOGIN**.
  - Endast de SQL Server huvudnamn (inloggningar) som ingår i rollen kan utföra följande åtgärder som riktar sig mot `sysadmin` Azure AD-huvudnamn:

    - KÖRA SOM ANVÄNDARE
    - KÖRA SOM INLOGGNING

  - För att personifiera en användare med EXECUTE AS-instruktionen måste användaren mappas direkt till Azure AD-serverhuvudnamnet (inloggning). Användare som är medlemmar i Azure AD-grupper som är mappade till Azure AD-serverhuvudnamn kan i praktiken inte personifieras med EXECUTE AS-instruktionen, även om anroparen har den personifierade behörigheten för det angivna användarnamnet.

- Databasexport/-import med bacpac-filer stöds för Azure AD-användare i SQL Managed Instance antingen [med SSMS V18.4](/sql/ssms/download-sql-server-management-studio-ssms)eller [ senare ellerSQLPackage.exe](/sql/tools/sqlpackage-download).
  - Följande konfigurationer stöds med hjälp av bacpac-databasfilen: 
    - Exportera/importera en databas mellan olika hanterade instanser inom samma Azure AD-domän.
    - Exportera en databas från SQL Managed Instance och importera till SQL Database inom samma Azure AD-domän. 
    - Exportera en databas från SQL Database importera till SQL Managed Instance inom samma Azure AD-domän.
    - Exportera en databas från SQL Managed Instance och importera till SQL Server (version 2012 eller senare).
      - I den här konfigurationen skapas alla Azure AD-användare som SQL Server databashuvudnamn (användare) utan inloggningar. Typen av användare visas som och `SQL` visas som `SQL_USER` i sys.database_principals). Deras behörigheter och roller finns kvar i SQL Server metadata och kan användas för personifiering. De kan dock inte användas för att komma åt och logga in på SQL Server med sina autentiseringsuppgifter.

- Endast huvudkontoinloggningen på servernivå, som skapas av SQL Managed Instance-etableringsprocessen, medlemmar i serverrollerna, till exempel eller , eller andra inloggningar med behörigheten ALTER ANY LOGIN på servernivå kan skapa `securityadmin` Azure AD-serverhuvudnamn (inloggningar) i huvuddatabasen `sysadmin` för SQL Managed Instance.
- Om inloggningen är ett SQL-huvudnamn kan endast inloggningar som ingår i rollen använda create-kommandot för att `sysadmin` skapa inloggningar för ett Azure AD-konto.
- Azure AD-inloggningen måste vara medlem i en Azure AD i samma katalog som används för Azure SQL Managed Instance.
- Azure AD-serverhuvudnamn (inloggningar) visas i Object Explorer börjar med SQL Server Management Studio 18.0 förhandsversion 5.
- Överlappande Azure AD-serverhuvudnamn (inloggningar) med ett Azure AD-administratörskonto tillåts. Azure AD-serverhuvudnamn (inloggningar) har företräde framför Azure AD-administratören när du löser huvudnamnet och tillämpar behörigheter på SQL Managed Instance.
- Under autentiseringen tillämpas följande sekvens för att matcha autentiseringsobjekt:

    1. Om Azure AD-kontot finns som direkt mappat till Azure AD-serverhuvudkontot (inloggning), som finns i sys.server_principals som typen "E", beviljar du åtkomst och tillämpar behörigheter för Azure AD-serverhuvudkontot (inloggning).
    2. Om Azure AD-kontot är medlem i en Azure AD-grupp som är mappad till Azure AD-serverhuvudkontot (inloggning), som finns i sys.server_principals som typ "X", beviljar du åtkomst och tillämpar behörigheter för Azure AD-gruppinloggningen.
    3. Om Azure AD-kontot är en särskild portalkonfigurerad Azure AD-administratör för SQL Managed Instance, som inte finns i SQL Managed Instance-systemvyer, tillämpar du särskilda fasta behörigheter för Azure AD-administratören för SQL Managed Instance (äldre läge).
    4. Om Azure AD-kontot finns som direkt mappat till en Azure AD-användare i en databas, som finns i sys.database_principals som typen "E", beviljar du åtkomst och tillämpar behörigheter för Azure AD-databasanvändaren.
    5. Om Azure AD-kontot är medlem i en Azure AD-grupp som är mappad till en Azure AD-användare i en databas, som finns i sys.database_principals som typen "X", beviljar du åtkomst och tillämpar behörigheter för Azure AD-gruppinloggningen.
    6. Om en Azure AD-inloggning mappas till antingen ett Azure AD-användarkonto eller ett Azure AD-gruppkonto, som matchar användaren som autentiserar, tillämpas alla behörigheter från den här Azure AD-inloggningen.

### <a name="service-key-and-service-master-key"></a>Tjänstnyckel och huvudnyckel för tjänsten

- [Säkerhetskopiering av](/sql/t-sql/statements/backup-master-key-transact-sql) huvudnyckel stöds inte (hanteras av SQL Database tjänsten).
- [Återställning av huvudnyckel](/sql/t-sql/statements/restore-master-key-transact-sql) stöds inte (hanteras av SQL Database tjänsten).
- [Säkerhetskopiering av huvudnyckeln](/sql/t-sql/statements/backup-service-master-key-transact-sql) för tjänsten stöds inte (hanteras av SQL Database tjänsten).
- [Återställning av huvudnyckeln](/sql/t-sql/statements/restore-service-master-key-transact-sql) för tjänsten stöds inte (hanteras av SQL Database tjänsten).

## <a name="configuration"></a>Konfiguration

### <a name="buffer-pool-extension"></a>Buffertpooltillägg

- [Buffertpooltillägg](/sql/database-engine/configure-windows/buffer-pool-extension) stöds inte.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` stöds inte. Se [ÄNDRA SERVERKONFIGURATION.](/sql/t-sql/statements/alter-server-configuration-transact-sql)

### <a name="collation"></a>Sortering

Standardinstansar sorteras och `SQL_Latin1_General_CP1_CI_AS` kan anges som en parameter för att skapa. Se [Sorteringar.](/sql/t-sql/statements/collations)

### <a name="compatibility-levels"></a>Efterlevnadsnivåer

- Kompatibilitetsnivåer som stöds är 100, 110, 120, 130, 140 och 150.
- Kompatibilitetsnivåer under 100 stöds inte.
- Standardkompatibilitetsnivån för nya databaser är 140. För återställda databaser förblir kompatibilitetsnivån oförändrad om den var 100 och högre.

Se [ALTER DATABASE Compatibility Level (Ändra databaskompatibilitetsnivå).](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)

### <a name="database-mirroring"></a>Databasspegling

Databasspegling stöds inte.

- `ALTER DATABASE SET PARTNER` - `SET WITNESS` och -alternativ stöds inte.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` stöds inte.

Mer information finns i ALTER DATABASE SET PARTNER and SET WITNESS and CREATE ENDPOINT ... [(ÄNDRA DATABASUPPSÄTTNINGSPARTNER och ANGE VITTNE](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) [OCH SKAPA SLUTPUNKT... FÖR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Databasalternativ

- Flera loggfiler stöds inte.
- Minnesbaserade objekt stöds inte på den Generell användning tjänstnivån. 
- Det finns en gräns på 280 filer per Generell användning instans, vilket innebär högst 280 filer per databas. Både data och loggfiler på Generell användning nivå räknas mot den här gränsen. [Den Affärskritisk stöder 32 767 filer per databas](./resource-limits.md#service-tier-characteristics).
- Databasen får inte innehålla filgrupper som innehåller filströmsdata. Återställningen misslyckas om .bak innehåller `FILESTREAM` data. 
- Varje fil placeras i Azure Blob Storage. I/O och dataflöde per fil beror på storleken på varje enskild fil.

#### <a name="create-database-statement"></a>CREATE DATABASE-instruktion

Följande begränsningar gäller för `CREATE DATABASE` :

- Filer och filgrupper kan inte definieras. 
- Alternativet `CONTAINMENT` stöds inte. 
- `WITH` -alternativ stöds inte. 
   > [!TIP]
   > Som en lösning kan du använda `ALTER DATABASE` efter för att ange `CREATE DATABASE` databasalternativ för att lägga till filer eller ange inneslutning. 

- Alternativet `FOR ATTACH` stöds inte.
- Alternativet `AS SNAPSHOT OF` stöds inte.

Mer information finns i [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>ALTER DATABASE-instruktion

Vissa filegenskaper kan inte anges eller ändras:

- En filsökväg kan inte anges i `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL-instruktionen. Ta `FILENAME` bort från skriptet eftersom SQL Managed Instance automatiskt placerar filerna. 
- Ett filnamn kan inte ändras med hjälp av `ALTER DATABASE` -instruktionen.

Följande alternativ anges som standard och kan inte ändras:

- `MULTI_USER`
- `ENABLE_BROKER`
- `AUTO_CLOSE OFF`

Följande alternativ kan inte ändras:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Vissa `ALTER DATABASE` instruktioner (till exempel [SET CONTAINMENT](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#converting-a-database-to-partially-contained-using-transact-sql)) kan misslyckas tillfälligt, till exempel under den automatiserade säkerhetskopieringen av databasen eller direkt efter att en databas har skapats. I det här `ALTER DATABASE` fallet bör ett återfall göras. Mer information om relaterade felmeddelanden finns i [avsnittet Kommentarer.](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&tabs=sqlpool&view=azuresqldb-mi-current#remarks-2)

Mer information finns i [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server-agent

- Att aktivera och inaktivera SQL Server Agent stöds för närvarande inte i SQL Managed Instance. SQL Agent körs alltid.
- Jobbschemautlösare som baseras på en inaktiv PROCESSOR stöds inte.
- SQL Server Agent är skrivskyddade. Proceduren `sp_set_agent_properties` stöds inte i SQL Managed Instance. 
- Jobb
  - T-SQL-jobbsteg stöds.
  - Följande replikeringsjobb stöds:
    - Transaktionsloggläsare
    - Ögonblicksbild
    - Distributör
  - SSIS-jobbsteg stöds.
  - Andra typer av jobbsteg stöds inte för närvarande:
    - Steget för sammanslagningsreplikeringsjobbet stöds inte. 
    - Köläsare stöds inte. 
    - Kommandogränssnittet stöds inte ännu.
  - SQL Managed Instance kan inte komma åt externa resurser, till exempel nätverksresurser via robocopy. 
  - SQL Server Analysis Services stöds inte.
- Meddelanden stöds delvis.
- E-postavisering stöds, även om det kräver att du konfigurerar en Database Mail profil. SQL Server Agent kan bara använda en Database Mail profil och den måste anropas `AzureManagedInstance_dbmail_profile` . 
  - Pager stöds inte.
  - NetSend stöds inte.
  - Aviseringar stöds inte ännu.
  - Proxys stöds inte.
- EventLog stöds inte.
- Användaren måste mappas direkt till Azure AD-serverhuvudkonto (inloggning) för att skapa, ändra eller köra SQL Agent-jobb. Användare som inte är direkt mappade, till exempel användare som tillhör en Azure AD-grupp som har behörighet att skapa, ändra eller köra SQL Agent-jobb, kommer inte att kunna utföra dessa åtgärder på ett effektivt sätt. Detta beror på managed instance-personifiering och [EXECUTE AS-begränsningar.](#logins-and-users)
- Funktionen Multi Server Administration för master-/måljobb (MSX/TSX) stöds inte.

Information om SQL Server Agent finns i [SQL Server Agent](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tables

Följande tabelltyper stöds inte:

- [Filestream](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) (Polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (stöds inte bara på Generell användning nivå)

Information om hur du skapar och ändrar tabeller finns i [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) och [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funktioner

### <a name="bulk-insert--openrowset"></a>Massinfogning/OPENROWSET

SQL Managed Instance kan inte komma åt filresurser och Windows-mappar, så filerna måste importeras från Azure Blob Storage:

- `DATASOURCE` krävs i kommandot `BULK INSERT` när du importerar filer från Azure Blob Storage. Se [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` krävs i funktionen `OPENROWSET` när du läser innehållet i en fil från Azure Blob Storage. Se [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` kan användas för att läsa data Azure SQL Database, Azure SQL Managed Instance eller SQL Server instanser. Andra källor som Oracle-databaser eller Excel-filer stöds inte.

### <a name="clr"></a>Clr

En SQL Managed Instance kan inte komma åt filresurser och Windows-mappar, så följande begränsningar gäller:

- Endast `CREATE ASSEMBLY FROM BINARY` stöds. Se [SKAPA SAMMANSÄTTNING FRÅN BINÄR](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` stöds inte. Se [SKAPA SAMMANSÄTTNING FRÅN FIL](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` kan inte referera till filer. Se [ALTER ASSEMBLY (ÄNDRA SAMMANSÄTTNING).](/sql/t-sql/statements/alter-assembly-transact-sql)

### <a name="database-mail-db_mail"></a>Database Mail (db_mail)
 - `sp_send_dbmail` kan inte skicka bifogade filer med @file_attachments parametern . Lokalt filsystem och externa resurser eller Azure Blob Storage är inte tillgängliga från den här proceduren.
 - Se kända problem som rör `@query` parameter och autentisering.
 
### <a name="dbcc"></a>Dbcc

Odokumenterade DBCC-instruktioner som är aktiverade i SQL Server stöds inte i SQL Managed Instance.

- Endast ett begränsat antal globala spårningsflaggor stöds. Sessionsnivå `Trace flags` stöds inte. Se [Spårningsflaggor.](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) och [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) fungerar med det begränsade antalet globala spårningsflaggor.
- [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) med alternativ REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST och REPAIR_REBUILD kan inte användas eftersom databasen inte kan anges i läge – se `SINGLE_USER` ALTER DATABASE [differences](#alter-database-statement). Potentiella fel i databasen hanteras av Azure-supportteamet. Kontakta Azure-supporten om det finns tecken på att databasen är skadad.

### <a name="distributed-transactions"></a>Distribuerade transaktioner

Partiellt stöd [för distribuerade transaktioner är](../database/elastic-transactions-overview.md) för närvarande i offentlig förhandsversion. Distribuerade transaktioner stöds under följande villkor (alla måste vara uppfyllda):
* alla transaktionsdeltagare är Azure SQL hanterade instanser som ingår i [serverförtroendegruppen](./server-trust-group-overview.md).
* transaktioner initieras antingen från .NET (TransactionScope-klassen) eller Transact-SQL.

Azure SQL Managed Instance stöder för närvarande inte andra scenarier som regelbundet stöds av MSDTC lokalt eller i Azure Virtual Machines.

### <a name="extended-events"></a>Extended Events

Vissa Windows-specifika mål för Extended Events (XEvents) stöds inte:

- Målet `etw_classic_sync` stöds inte. Lagra `.xel` filer i Azure Blob Storage. Läs mer i [Målet etw_classic_sync](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Målet `event_file` stöds inte. Lagra `.xel` filer i Azure Blob Storage. Läs mer i [Målet event_file](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Externa bibliotek

Externa R- och Python-bibliotek i databasen stöds i begränsad offentlig förhandsversion. Se [Machine Learning Services i Azure SQL Managed Instance (förhandsversion)](machine-learning-services-overview.md).

### <a name="filestream-and-filetable"></a>Filestream och FileTable

- Filströmsdata stöds inte.
- Databasen får inte innehålla filgrupper med `FILESTREAM` data.
- `FILETABLE` stöds inte.
- Tabeller kan inte ha `FILESTREAM` typer.
- Följande funktioner stöds inte:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Mer information finns i [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) och [FileTables](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Semantisk fulltextsökning

[Semantisk](/sql/relational-databases/search/semantic-search-sql-server) sökning stöds inte.

### <a name="linked-servers"></a>Länkade servrar

Länkade servrar i SQL Managed Instance har stöd för ett begränsat antal mål:

- Mål som stöds är SQL Managed Instance, SQL Database, Azure Synapse [SQL-serverlösa](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) och dedikerade pooler och SQL Server instanser. 
- Distribuerade skrivbara transaktioner är endast möjliga mellan hanterade instanser. Mer information finns i [Distribuerade transaktioner.](../database/elastic-transactions-overview.md) MS DTC stöds dock inte.
- Mål som inte stöds är filer, Analysis Services och andra RDBMS. Försök att använda inbyggd CSV-import från Azure Blob Storage med eller som ett alternativ för filimport eller läsa in filer med hjälp av en `BULK INSERT` `OPENROWSET` [serverlös SQL-pool i Azure Synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/).

Åtgärder: 

- [Skrivtransaktioner mellan](../database/elastic-transactions-overview.md) instanser stöds endast för hanterade instanser.
- `sp_dropserver` stöds för att ta bort en länkad server. Se [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- Funktionen `OPENROWSET` kan endast användas för att köra frågor på SQL Server instanser. De kan antingen hanteras, lokalt eller på virtuella datorer. Se [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- Funktionen `OPENDATASOURCE` kan endast användas för att köra frågor på SQL Server instanser. De kan antingen hanteras, lokalt eller på virtuella datorer. Endast `SQLNCLI` värdena `SQLNCLI11` , och stöds som `SQLOLEDB` en provider. Ett exempel är `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Se [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Länkade servrar kan inte användas för att läsa filer (Excel, CSV) från nätverksresurser. Försök att [använda BULK INSERT,](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) som läser CSV-filer från Azure Blob Storage eller en länkad server som refererar till en [serverlös SQL-pool i Synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/). Spåra dessa begäranden på [SQL Managed Instance feedback-objekt](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Den enda tillgängliga typen av extern källa är RDBMS (i offentlig förhandsversion) för att Azure SQL databas, Azure SQL hanterad instans och Azure Synapse-pool. Du kan använda en extern tabell som refererar till en [serverlös SQL-pool i Synapse Analytics](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/) som en lösning för externa Polybase-tabeller som läser direkt från Azure Storage. I Azure SQL hanterad instans kan du använda länkade servrar till en [serverlös SQL-pool](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) i Synapse Analytics eller SQL Server för att läsa Azure Storage-data.
Information om PolyBase finns i [PolyBase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikering

- Replikeringstyper för ögonblicksbilder och dubbelriktade stöds. Sammanslagningsreplikering, peer-to-peer-replikering och uppdaterbara prenumerationer stöds inte.
- [Transaktionsreplikering](replication-transactional-overview.md) är tillgängligt för offentlig förhandsversion på SQL Managed Instance med vissa begränsningar:
    - Alla typer av replikeringsdeltagare (utgivare, distributör, pull-prenumerant och push-prenumerant) kan placeras på SQL Managed Instance, men utgivaren och distributören måste antingen vara både i molnet eller både lokalt.
    - SQL Managed Instance kan kommunicera med de senaste versionerna av SQL Server. Mer information [finns i versionsmatrisen](replication-transactional-overview.md#supportability-matrix) som stöds.
    - Transaktionsreplikering har [vissa ytterligare nätverkskrav.](replication-transactional-overview.md#requirements)

Mer information om hur du konfigurerar transaktionsreplikering finns i följande självstudier:
- [Replikering mellan en SQL MI-utgivare och SQL MI-prenumerant](replication-between-two-instances-configure-tutorial.md)
- [Replikering mellan en SQL MI-utgivare, SQL MI-distributör och en SQL Server prenumerant](replication-two-instances-and-sql-server-configure-tutorial.md)

### <a name="restore-statement"></a>RESTORE-instruktion 

- Syntax som stöds:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Syntax som inte stöds:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Källa: 
  - `FROM URL` (Azure Blob Storage) är det enda alternativ som stöds.
  - `FROM DISK`/`TAPE`/backup-enheten stöds inte.
  - Säkerhetskopieringsuppsättningar stöds inte.
- `WITH` -alternativ stöds inte. Återställningsförsök `WITH` som , , `DIFFERENTIAL` `STATS` `REPLACE` osv. misslyckas.
- `ASYNC RESTORE`: Återställningen fortsätter även om klientanslutningen bryts. Om anslutningen har tagits bort kan du kontrollera statusen för en återställningsåtgärd och för en CREATE- och `sys.dm_operation_status` DROP-databas. Se [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Följande databasalternativ anges eller åsidosätts och kan inte ändras senare: 

- `NEW_BROKER` om den a broker inte är aktiverad i .bak-filen. 
- `ENABLE_BROKER` om den a broker inte är aktiverad i .bak-filen. 
- `AUTO_CLOSE=OFF` om en databas i .bak-filen har `AUTO_CLOSE=ON` . 
- `RECOVERY FULL` om en databas i .bak-filen har `SIMPLE` eller `BULK_LOGGED` återställningsläge.
- En minnesoptimerad filgrupp läggs till och kallas XTP om den inte fanns i .bak-källfilen. 
- Alla befintliga minnesoptimerade filgrupper har bytt namn till XTP. 
- `SINGLE_USER` alternativen `RESTRICTED_USER` och konverteras till `MULTI_USER` .

Begränsningar: 

- Säkerhetskopior av de skadade databaserna kan återställas beroende på typen av skada, men automatiska säkerhetskopieringar tas inte förrän det skadade har åtgärdats. Kontrollera att du kör på `DBCC CHECKDB` källfilen och SQL Managed Instance säkerhetskopiering `WITH CHECKSUM` för att förhindra det här problemet.
- Återställning av fil för en databas som innehåller begränsningar som beskrivs i det här dokumentet (till exempel eller objekt) kan inte `.BAK` `FILESTREAM` `FILETABLE` återställas på SQL Managed Instance.
- `.BAK` filer som innehåller flera säkerhetskopieringsuppsättningar kan inte återställas. 
- `.BAK` filer som innehåller flera loggfiler kan inte återställas.
- Säkerhetskopior som innehåller databaser som är större än 8 TB, aktiva minnesbaserade OLTP-objekt eller antal filer som överstiger 280 filer per instans kan inte återställas på en Generell användning-instans. 
- Säkerhetskopior som innehåller databaser som är större än 4 TB eller minnes minnesbaserade [](resource-limits.md) OLTP-objekt med en total storlek som är större än den storlek som beskrivs i resursgränser kan inte återställas på Affärskritisk instans.
Information om återställningsutsatser finns i [RESTORE-instruktioner.](/sql/t-sql/statements/restore-statements-transact-sql)

 > [!IMPORTANT]
 > Samma begränsningar gäller för inbyggd återställning till tidpunkt. Till exempel kan Generell användning databas som är större än 4 TB inte återställas på Affärskritisk instansen. Affärskritisk en databas med minnes minnes in memory OLTP-filer eller fler än 280 filer kan inte återställas på en Generell användning instans.

### <a name="service-broker"></a>Service Broker

Utbyte av service broker-meddelanden mellan instanser stöds endast mellan Azure SQL hanterade instanser:

- `CREATE ROUTE`: Du kan inte använda med `CREATE ROUTE` något annat namn än eller DNS för en annan `ADDRESS` `LOCAL` SQL Managed Instance.
- `ALTER ROUTE`: Du kan inte använda med `ALTER ROUTE` något annat namn än eller DNS för en annan `ADDRESS` `LOCAL` SQL Managed Instance.

Transportsäkerhet stöds, dialogsäkerhet är inte:
- `CREATE REMOTE SERVICE BINDING`stöds inte.

Service Broker är aktiverat som standard och kan inte inaktiveras. Följande ALTER DATABSE-alternativ stöds inte:
- `ENABLE_BROKER`
- `DISABLE_BROKER`

### <a name="stored-procedures-functions-and-triggers"></a>Lagrade procedurer, funktioner och utlösare

- `NATIVE_COMPILATION` stöds inte på den Generell användning nivån.
- Följande [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) alternativ stöds inte: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
  - `scan for startup procs`
- `sp_execute_external_scripts` stöds inte. Se [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` stöds inte. Se [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` stöds inte, och detta inkluderar `sp_addextendedproc` och `sp_dropextendedproc` . Den här funktionen stöds inte eftersom den är på en utfasningsväg för SQL Server. Mer information finns i Utökade [lagrade procedurer.](/sql/relational-databases/extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming)
- `sp_attach_db`, `sp_attach_single_file_db` och `sp_detach_db` stöds inte. Se [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)och [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Systemfunktioner och variabler

Följande variabler, funktioner och vyer returnerar olika resultat:

- `SERVERPROPERTY('EngineEdition')` returnerar värdet 8. Den här egenskapen identifierar unikt SQL Managed Instance. Se [SERVEREGENSKAPER.](/sql/t-sql/functions/serverproperty-transact-sql)
- `SERVERPROPERTY('InstanceName')` returnerar NULL eftersom begreppet instans som det finns för SQL Server inte gäller för SQL Managed Instance. Se [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` returnerar ett fullständigt DNS-"anslutningsbart" namn, till exempel my-managed-instance.wcus17662feb9ce98.database.windows.net. Se [ @SERVERNAME @](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` returnerar ett fullständigt DNS-"anslutningsbart" namn, till exempel för `myinstance.domain.database.windows.net` egenskaperna "name" och "data_source". Se [SYS. SERVRAR](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` returnerar NULL eftersom begreppet tjänst som det finns för SQL Server inte gäller för SQL Managed Instance. Se [ @SERVICENAME @](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` stöds. Det returnerar NULL om Azure AD-inloggningen inte finns sys.sysinloggningar. Se [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` stöds inte. Fel data returneras, vilket är ett tillfälligt känt problem. Se [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Miljöbegränsningar

### <a name="subnet"></a>Undernät
-  Du kan inte placera några andra resurser (till exempel virtuella datorer) i det undernät där du har distribuerat SQL Managed Instance. Distribuera dessa resurser med ett annat undernät.
- Undernätet måste ha tillräckligt många tillgängliga [IP-adresser.](connectivity-architecture-overview.md#network-requirements) Minimum är att ha minst 32 IP-adresser i undernätet.
- Antalet virtuella kärnor och typer av instanser som du kan distribuera i en region har vissa [begränsningar och begränsningar.](resource-limits.md#regional-resource-limitations)
- Det finns [en nätverkskonfiguration](connectivity-architecture-overview.md#network-requirements) som måste tillämpas på undernätet.

### <a name="vnet"></a>VNET
- VNet kan distribueras med hjälp av resursmodell – klassisk modell för VNet stöds inte.
- När en SQL Managed Instance har skapats stöds inte SQL Managed Instance eller VNet till en annan resursgrupp eller prenumeration.
- För SQL Managed Instances som finns i virtuella kluster som skapas före 2020-09-22 global peering inte. [](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) Du kan ansluta till dessa resurser via ExpressRoute eller VNet-till-VNet via VNet-gatewayer.

### <a name="failover-groups"></a>Redundansgrupper
Systemdatabaser replikeras inte till den sekundära instansen i en redundansgrupp. Scenarier som är beroende av objekt från systemdatabaserna är därför omöjliga på den sekundära instansen såvida inte objekten skapas manuellt på den sekundära instansen.

### <a name="tempdb"></a>Tempdb
- Den maximala filstorleken `tempdb` för får inte vara större än 24 GB per kärna på en Generell användning nivå. Den maximala `tempdb` storleken på en Affärskritisk-nivå begränsas av den SQL Managed Instance lagringsstorleken. `Tempdb` loggfilens storlek är begränsad till 120 GB Generell användning lagringsnivå. Vissa frågor kan returnera ett fel om de behöver mer än 24 GB per kärna i eller om de producerar mer än `tempdb` 120 GB loggdata.
- `Tempdb` delas alltid upp i 12 datafiler: 1 primär, kallas även master, datafil och 11 icke-primära datafiler. Filstrukturen kan inte ändras och nya filer kan inte läggas till i `tempdb` . 
- [Minnesoptimerade `tempdb` metadata](/sql/relational-databases/databases/tempdb-database?view=sql-server-ver15&preserve-view=true#memory-optimized-tempdb-metadata), en ny SQL Server databasfunktion i minnet från 2019, stöds inte.
- Objekt som skapas i modelldatabasen kan inte skapas automatiskt i efter en omstart eller redundans eftersom inte får sin `tempdb` `tempdb` ursprungliga objektlista från modelldatabasen. Du måste skapa objekt i `tempdb` manuellt efter varje omstart eller redundans.

### <a name="msdb"></a>MSDB

Följande MSDB-scheman i SQL Managed Instance måste ägas av deras respektive fördefinierade roller:

- Allmänna roller
  - TargetServersRole
- [Databasroller har åtgärdats](/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15&preserve-view=true)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [DatabaseMail-roller:](/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15&preserve-view=true#DBProfile)
  - DatabaseMailUserRole
- [Roller för integreringstjänster:](/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15&preserve-view=true)
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> Om du ändrar fördefinierade rollnamn, schemanamn och schemaägare av kunder påverkas tjänstens normala drift. Alla ändringar som görs i dessa återställs till de fördefinierade värdena så snart som de har identifierats, eller vid nästa tjänstuppdatering senast för att säkerställa normal tjänstdrift.

### <a name="error-logs"></a>Felloggar

SQL Managed Instance utförlig information i felloggarna. Det finns många interna systemhändelser som loggas i felloggen. Använd en anpassad procedur för att läsa felloggar som filtrerar bort vissa irrelevanta poster. Mer information finns i [SQL Managed Instance – sp_readmierrorlog](/archive/blogs/sqlcat/azure-sql-db-managed-instance-sp_readmierrorlog) eller [SQL Managed Instance(förhandsversion)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) för Azure Data Studio.

## <a name="next-steps"></a>Nästa steg

- Mer information om SQL Managed Instance finns i [Vad är SQL Managed Instance?](sql-managed-instance-paas-overview.md)
- En lista över funktioner och jämförelser finns i Azure SQL Managed Instance [jämförelse av funktioner.](../database/features-comparison.md)
- Information om lanseringsuppdateringar och kända problem finns [i SQL Managed Instance viktiga information](../database/doc-changes-updates-release-notes.md)
- En snabbstart som visar hur du skapar en ny SQL Managed Instance finns i [Skapa en SQL Managed Instance](instance-create-quickstart.md).
