---
title: SQL-hanterad instans säkerhet med Azure AD server-Huvudkonton (inloggningar)
description: Lär dig mer om tekniker och funktioner för att skydda den hanterade Azure SQL-instansen och Använd Azure AD server-Huvudkonton (inloggningar)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: 50544507f9d83c216bb6c18e004c5ce7ad1ca346
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639842"
---
# <a name="tutorial-security-in-azure-sql-managed-instance-using-azure-ad-server-principals-logins"></a>Självstudie: säkerhet i Azure SQL-hanterad instans med hjälp av Azure AD server-huvudobjekt (inloggningar)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL-hanterad instans innehåller nästan alla säkerhetsfunktioner som den senaste SQL Server (Enterprise Edition) databas motorn har:

- Begränsa åtkomsten i en isolerad miljö
- Använd autentiseringsmekanismer som kräver identitet: Azure Active Directory (Azure AD) och SQL-autentisering
- Använda auktorisering med rollbaserade medlemskap och behörigheter
- Aktivera säkerhetsfunktioner

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> - Skapa ett Azure AD server-huvudobjekt (inloggning) för en hanterad instans
> - Bevilja behörigheter till Azure AD-serverhuvudkonton (inloggningar) i en hanterad instans
> - Skapa Azure AD-användare från Azure AD-serverhuvudkonton (inloggningar)
> - Tilldela behörigheter till Azure AD-användare och hantera databassäkerhet
> - Använda personifiering med Azure AD-användare
> - Använda frågor över flera databaser med Azure AD-användare
> - Läs om säkerhetsfunktioner som hotskydd, granskning, datamaskering och kryptering

Mer information finns i [Översikt över Azure SQL Managed instance](sql-managed-instance-paas-overview.md). 

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här självstudien behöver du följande:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- En hanterad instans
  - Följ den här artikeln: [snabb start: skapa en hanterad instans](instance-create-quickstart.md)
- Kunna komma åt din hanterade instans och [ha etablerat en Azure AD-administratör för den hanterade instansen](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). Mer information finns i:
  - [Ansluta program till en hanterad instans](connect-application-instance.md)
  - [Anslutnings arkitektur för SQL-hanterad instans](connectivity-architecture-overview.md)
  - [Konfigurera och hantera Azure Active Directory-autentisering med SQL](../database/authentication-aad-configure.md)

## <a name="limit-access"></a>Begränsa åtkomst 

Hanterade instanser kan nås via en privat IP-adress. På samma sätt som en isolerad SQL Server-miljö behöver program eller användare åtkomst till SQL Managed instance Network (VNet) innan en anslutning kan upprättas. Mer information finns i [ansluta ditt program till SQL-hanterad instans](connect-application-instance.md).

Det är också möjligt att konfigurera en tjänst slut punkt på en hanterad instans, vilket möjliggör offentliga anslutningar på samma sätt som för Azure SQL Database.
Mer information finns i [Konfigurera offentlig slut punkt i Azure SQL-hanterad instans](public-endpoint-configure.md).

> [!NOTE]
> Även om du har aktiverat tjänstens slut punkter gäller inte [Azure SQL Database brand Väggs regler](../database/firewall-configure.md) . Den hanterade Azure SQL-instansen har sin egen [inbyggda brand vägg](management-endpoint-verify-built-in-firewall.md) för att hantera anslutningen.

## <a name="create-an-azure-ad-server-principal-login-using-ssms"></a>Skapa ett Azure AD server-huvudobjekt (inloggning) med SSMS

Det första Azure AD server-huvudobjektet (inloggning) kan skapas av standard kontot för SQL-administratörer (icke-Azure AD) som är a `sysadmin` , eller Azure AD-administratören för den hanterade instans som skapades under etablerings processen. Mer information finns i [etablera en Azure Active Directory administratör för SQL-hanterad instans](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

I följande artiklar finns exempel på hur du ansluter till SQL-hanterad instans:

- [Snabb start: Konfigurera virtuell Azure-dator för att ansluta till SQL-hanterad instans](connect-vm-instance-configure.md)
- [Snabb start: Konfigurera en punkt-till-plats-anslutning till SQL-hanterad instans från lokal plats](point-to-site-p2s-configure.md)

1. Logga in på den hanterade instansen med ett standard konto för SQL-inloggning (icke-Azure AD) som är en `sysadmin` Azure AD-administratör för SQL-hanterad instans med hjälp av [SQL Server Management Studio](point-to-site-p2s-configure.md#connect-with-ssms).

2. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.

3. Använd följande syntax i frågefönstret för att skapa en inloggning för ett lokalt Azure AD-konto:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Det här exemplet skapar en inloggning för kontot nativeuser@aadsqlmi.onmicrosoft.com.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Välj **Kör** i verktygsfältet för att skapa inloggningen.

5. Kontrollera den nyligen tillagda inloggningen genom att köra följande T-SQL-kommando:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![Skärm bild av fliken resultat i S M S Object Explorer visar namnet, principal_id, sid, typ och type_desc för den nyligen tillagda inloggningen.](./media/aad-security-configure-tutorial/native-login.png)

Mer information finns i [SKAPA INLOGGNING](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

## <a name="grant-permissions-to-create-logins"></a>Bevilja behörighet att skapa inloggningar

För att andra Azure AD-serverhuvudkonton (inloggningar) ska kunna skapas måste SQL Server-roller eller -behörigheter tilldelas till huvudkontot (SQL eller Azure AD).

### <a name="sql-authentication"></a>SQL-autentisering

- Om inloggningen är ett SQL-huvud kan endast inloggningar som är en del av `sysadmin` rollen använda kommandot Skapa för att skapa inloggningar för ett Azure AD-konto.

### <a name="azure-ad-authentication"></a>Azure AD-autentisering

- För att ge det nyligen skapade Azure AD-serverhuvudkontot (inloggning) möjlighet att skapa andra inloggningar för andra Azure AD-användare, grupper eller program beviljar du inloggningen serverrollen `sysadmin` eller `securityadmin`.
- Som minst måste behörigheten **ALTER ANY LOGIN** ges till Azure AD-serverhuvudkontot (inloggning) för att skapa andra Azure AD-serverhuvudkonton (inloggningar).
- Som standard är standard behörigheten som beviljats nyligen skapade Azure AD server-Huvudkonton (inloggningar) i huvud servern: **Anslut SQL** och **Visa alla databaser**.
- Serverrollen `sysadmin` kan ges till många Azure AD-serverhuvudkonton (inloggningar) i en hanterad instans.

Så här lägger du till inloggningen till serverrollen `sysadmin`:

1. Logga in på den hanterade instansen igen eller Använd den befintliga anslutningen till Azure AD-administratören eller SQL-huvudobjektet som är en `sysadmin` .

1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.

1. Bevilja Azure AD-serverhuvudkontot (inloggning) serverrollen `sysadmin` med hjälp av följande T-SQL-syntax:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    I följande exempel beviljas serverrollen `sysadmin` till inloggningen nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>Skapa ytterligare Azure AD-serverhuvudkonton (inloggningar) med hjälp av SSMS

När Azure AD-serverhuvudkontot (inloggning) har skapats och har getts `sysadmin`-privilegier kan den inloggningen skapa ytterligare inloggningar med hjälp av satsen **FROM EXTERNAL PROVIDER** (Från extern provider) med **CREATE LOGIN** (Skapa inloggning).

1. Anslut till den hanterade instansen med Azure AD-serverhuvudkontot (inloggning) med hjälp av SQL Server Management Studio. Ange värd namnet för SQL-hanterad instans. Det finns tre alternativ att välja mellan när du loggar in med ett Azure AD-konto för autentisering i SSMS:

   - Active Directory – Universell med stöd för MFA
   - Active Directory – lösenord
   - Active Directory – integrerad </br>

     ![Skärm bild av dialog rutan Anslut till server i s M S med Active Directory-Universal with MFA-stöd som marker ATS i list rutan för autentisering.](./media/aad-security-configure-tutorial/ssms-login-prompt.png)

     Mer information finns i [Universal Authentication (SSMS-stöd för Multi-Factor Authentication)](../database/authentication-mfa-ssms-overview.md).

1. Välj **Active Directory – Universell med stöd för MFA**. Detta öppnar ett Multi-Factor Authentication inloggnings fönster. Logga in med ditt Azure AD-lösenord.

    ![Skärm bild av Multi-Factor Authentication inloggnings fönstret med markören i fältet ange lösen ord.](./media/aad-security-configure-tutorial/mfa-login-prompt.png)

1. I SSMS **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.
1. Använd följande syntax i frågefönstret för att skapa en inloggning för ytterligare ett Azure AD-konto:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    I det här exemplet skapas en inloggning för Azure AD bob@aadsqlmi.net -användaren, vars domän aadsqlmi.net är federerad med Azure ad aadsqlmi.onmicrosoft.com-domänen.

    Kör följande T-SQL-kommando. Federerade Azure AD-konton är ersättningar för SQL-hanterade instanser för lokala Windows-inloggningar och användare.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Skapa en databas i den hanterade instansen med hjälp av syntaxen [create Database](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current&preserve-view=true) . Den här databasen används för att testa användarinloggningar i nästa avsnitt.
    1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.
    1. Använd följande syntax i frågefönstret för att skapa en databas med namnet **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Skapa en SQL-hanterad instans inloggning för en grupp i Azure AD. Gruppen måste finnas i Azure AD innan du kan lägga till inloggningen till SQL-hanterad instans. Läs [Skapa en basgrupp och lägga till medlemmar med hjälp av Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Skapa en grupp _mygroup_ och lägg till medlemmar i den här gruppen.

1. Öppna ett nytt frågefönster i SQL Server Management Studio.

    Det här exemplet förutsätter att det finns en grupp som heter min _grupp_ i Azure AD. Kör följande kommando:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Testa att logga in på den hanterade instansen med den nyligen skapade inloggningen eller gruppen. Öppna en ny anslutning till den hanterade instansen och använd den nya inloggningen som autentisering.
1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga** för den nya anslutningen.
1. Kontrollera serverbehörigheter för det nyligen skapade Azure AD-serverhuvudkontot (inloggning) genom att köra följande kommando:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Azure AD-gäst användare stöds för inloggningar med SQL-hanterade instanser, endast när de läggs till som en del av en Azure AD-grupp. En Azure AD-gäst användare är ett konto som bjuds in till den Azure AD-instans som den hanterade instansen tillhör, från en annan Azure AD-instans. Till exempel joe@contoso.com kan du lägga till (Azure AD-konto) eller steve@outlook.com (Microsoft-konto) i en grupp i Azure AD aadsqlmi-instansen. När användarna har lagts till i en grupp kan en inloggning skapas i **huvud** databasen för SQL-hanterad instans för gruppen med hjälp av syntaxen för att **Skapa inloggning** . Gäst användare som är medlemmar i den här gruppen kan ansluta till den hanterade instansen med hjälp av sina aktuella inloggningar (till exempel joe@contoso.com eller steve@outlook.com ).

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login"></a>Skapa en Azure AD-användare från Azure AD server-huvudobjektet (inloggning)

Auktorisering till enskilda databaser fungerar ungefär på samma sätt i SQL-hanterad instans som med databaser i SQL Server. En användare kan skapas från en befintlig inloggning i en databas och få behörigheter för databasen eller läggas till i en databasroll.

Nu när vi har skapat en databas som heter **MyMITestDB**, och en inloggning som endast har standardbehörigheterna, är nästa steg att skapa en användare från den inloggningen. För tillfället kan inloggningen ansluta till den hanterade instansen och se alla databaser, men inte interagera med databaserna. Om du loggar in med Azure AD-kontot som har standardbehörigheterna och försöker expandera den nyligen skapade databasen visas följande fel:

![Skärm bild av ett fel meddelande från S S M Object Explorer som läser "det går inte att komma åt databasen MyMITestDB. (ObjectExplorer)".](./media/aad-security-configure-tutorial/ssms-db-not-accessible.png)

Läs mer om hur du beviljar databasbehörigheter i [Getting Started with Database Engine Permissions](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions) (Komma igång med behörigheter för databasmotorn).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Skapa en Azure AD-användare och skapa en exempeltabell

1. Logga in på den hanterade instansen med ett `sysadmin`-konto i SQL Server Management Studio.
1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.
1. I frågefönstret använder du följande syntax för att skapa en Azure AD-användare från ett Azure AD-serverhuvudkonto (inloggning):

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    I följande exempel skapas en användare bob@aadsqlmi.net från inloggningen bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Det finns även stöd för att skapa en Azure AD-användare från ett Azure AD-serverhuvudkonto (inloggning) som är en grupp.

    I följande exempel skapas en inloggning för Azure _AD Group-gruppen som finns_ i din Azure AD-instans.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Alla användare som tillhör *mygroup* kan komma åt databasen **MyMITestDB**.

    > [!IMPORTANT]
    > När du skapar en **USER** (Användare) från ett Azure AD-serverhuvudkonto (inloggning) anger du samma user_name som login_name från **LOGIN** (Inloggning).

    Mer information finns i [SKAPA ANVÄNDARE](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

1. Skapa en testtabell med följande T-SQL-kommando i ett nytt frågefönster:

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. Skapa en anslutning i SSMS med användaren som har skapats. Lägg märke till att du inte kan se tabellen **TestTable** som skapades av `sysadmin` tidigare. Vi behöver ge användaren behörigheter att läsa data från databasen.

1. Du kan kontrollera användarens aktuella behörighet genom att köra följande kommando:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Lägga till användare till roller på databasnivå

För att användaren ska kunna se data i databasen ger vi användaren [roller på databasnivå](/sql/relational-databases/security/authentication-access/database-level-roles).

1. Logga in på den hanterade instansen med ett `sysadmin`-konto i SQL Server Management Studio.

1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.

1. Bevilja Azure AD-användaren `db_datareader`-databasrollen med hjälp av följande T-SQL-syntax:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    I följande exempel får användaren bob@aadsqlmi.net och gruppen _mygroup_`db_datareader` behörigheter för **MyMITestDB**-databasen:

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Kontrol lera att Azure AD-användaren som skapades i databasen finns genom att köra följande kommando:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Skapa en ny anslutning till den hanterade instansen med användaren som lades till i rollen `db_datareader`.
1. Expandera databasen i **Object Explorer** du vill se tabellen.

    ![Skärm bild från Object Explorer i S S M S visar mappstrukturen för tabeller i MyMITestDB. Dbo. Mappen TestTable är markerad.](./media/aad-security-configure-tutorial/ssms-test-table.png)

1. Öppna ett nytt frågefönster och kör följande SELECT-sats:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Kan du se data från tabellen? Du bör se de kolumner som returneras.

    ![Skärm bild av fliken resultat i S M S Object Explorer visar tabell kolumn rubrikerna AccountNum, ort, namn och tillstånd.](./media/aad-security-configure-tutorial/ssms-test-table-query.png)

## <a name="impersonate-azure-ad-server-level-principals-logins"></a>Personifiera Azure AD-säkerhetsobjekt på server nivå (inloggningar)

SQL-hanterad instans stöder personifiering av Azure AD-säkerhetsobjekt på server nivå (inloggningar).

### <a name="test-impersonation"></a>Testa personifiering

1. Logga in på den hanterade instansen med ett `sysadmin`-konto i SQL Server Management Studio.

1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.

1. Kör följande kommando i frågefönstret för att skapa en lagrad procedur:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Använd följande kommando för att se att den användare som du personifierar när du kör den lagrade proceduren är **bob \@ aadsqlmi.net**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Testa personifieringen med hjälp av instruktionen KÖRA SOM INLOGGNING:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Endast SQL-huvudkonton på servernivå (inloggningar) som ingår i rollen `sysadmin` kan utföra följande åtgärder som riktar in sig på Azure AD-huvudkonton:
>
> - KÖRA SOM ANVÄNDARE
> - KÖRA SOM INLOGGNING

## <a name="use-cross-database-queries"></a>Använda frågor över databaser

Databasöverskridande frågor stöds för Azure AD-konton med Azure AD-serverhuvudkonton (inloggningar). Vi måste skapa en annan databas och tabell för att testa en databasöverskridande fråga med en Azure AD-grupp. Du kan hoppa över att skapa en annan databas och tabell om det redan finns en.

1. Logga in på den hanterade instansen med ett `sysadmin`-konto i SQL Server Management Studio.
1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.
1. I frågefönstret använder du följande kommando för att skapa en databas med namnet **MyMITestDB2** och en tabell med namnet **TestTable2**:

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. I ett nytt frågefönster kör du följande kommando för att skapa användaren _mygroup_ i den nya databasen **MyMITestDB2**, och beviljar SELECT-behörigheter för databasen för _mygroup_:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Logga in på den hanterade instansen med SQL Server Management Studio som medlem _i gruppen Azure AD Group._ Öppna ett nytt frågefönster och kör instruktionen SELECT över flera databaser:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Du bör se tabellresultaten från **TestTable2**.

## <a name="additional-supported-scenarios"></a>Ytterligare scenarier som stöds

- SQL Agent-hantering och -jobbkörningar stöds för Azure AD-serverhuvudkonton (inloggningar).
- Åtgärder för databassäkerhetskopiering och -återställning kan köras av Azure AD-serverhuvudkonton (inloggningar).
- [Granskning](auditing-configure.md) av alla instruktioner som är relaterade till Azure AD-serverhuvudkonton (inloggningar) och autentiseringshändelser.
- Dedikerad administratörsanslutning för Azure AD-serverhuvudkonton (inloggningar) som är medlemmar i serverrollen `sysadmin`.
- Azure AD server-Huvudkonton (inloggningar) stöds med hjälp av verktyget [SQLCMD](/sql/tools/sqlcmd-utility) och [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) .
- Inloggningsutlösare stöds för inloggningshändelser som kommer från Azure AD-serverhuvudkonton (inloggningar).
- Service Broker och DBMail kan konfigureras med hjälp av Azure AD-serverhuvudkonton (inloggningar).

## <a name="next-steps"></a>Nästa steg

### <a name="enable-security-features"></a>Aktivera säkerhetsfunktioner

Se artikeln [SQL-hanterad instans säkerhetsfunktioner](sql-managed-instance-paas-overview.md#security-features) för en omfattande lista över olika sätt att skydda databasen. Följande säkerhetsfunktioner diskuteras:

- [SQL-hanterad instans granskning](auditing-configure.md)
- [Alltid krypterad](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Hotidentifiering](threat-detection-configure.md)
- [Dynamisk data maskning](/sql/relational-databases/security/dynamic-data-masking)
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security)
- [Transparent data kryptering (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>SQL-hanterade instans funktioner

En fullständig översikt över funktioner för SQL-hanterad instans finns i:

> [!div class="nextstepaction"]
> [SQL-hanterade instans funktioner](sql-managed-instance-paas-overview.md)