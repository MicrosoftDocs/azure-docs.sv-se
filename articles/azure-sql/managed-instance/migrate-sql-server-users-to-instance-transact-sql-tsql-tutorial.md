---
title: Migrera SQL Server Windows-användare och grupper till SQL-hanterad instans med T-SQL
description: Lär dig mer om hur du migrerar Windows-användare och grupper i en SQL Server instans till en Azure SQL-hanterad instans
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: f2dd34ab7c6ee5be26836e4abb86960605ee44ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84708694"
---
# <a name="tutorial-migrate-windows-users-and-groups-in-a-sql-server-instance-to-azure-sql-managed-instance-using-t-sql-ddl-syntax"></a>Självstudie: migrera Windows-användare och grupper i en SQL Server instans till en Azure SQL-hanterad instans med hjälp av en T-SQL DDL-syntax
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

> [!NOTE]
> Den syntax som används för att migrera användare och grupper till SQL-hanterad instans i den här artikeln finns i **offentlig för hands version**.

Den här artikeln tar dig igenom processen för att migrera dina lokala Windows-användare och-grupper i din SQL Server till Azure SQL-hanterad instans med hjälp av T-SQL-syntaxen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> - Skapa inloggningar för SQL Server
> - Skapa en test databas för migrering
> - Skapa inloggningar, användare och roller
> - Säkerhetskopiera och Återställ databasen till SQL-hanterad instans (MI)
> - Migrera användare manuellt till MI med ALTER USER syntax
> - Testa autentisering med de nya mappade användarna

## <a name="prerequisites"></a>Förutsättningar

Följande krav gäller för att slutföra den här självstudien:

- Windows-domänen är federerad med Azure Active Directory (Azure AD).
- Åtkomst till Active Directory för att skapa användare/grupper.
- En befintlig SQL Server i din lokala miljö.
- En befintlig SQL-hanterad instans. Se [snabb start: skapa en SQL-hanterad instans](instance-create-quickstart.md).
  - En `sysadmin` i SQL-hanterad instans måste användas för att skapa Azure AD-inloggningar.
- [Skapa en Azure AD-administratör för SQL-hanterad instans](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).
- Du kan ansluta till din SQL-hanterade instans i nätverket. Mer information finns i följande artiklar:
  - [Anslut ditt program till Azure SQL Managed Instance](connect-application-instance.md)
  - [Snabb start: Konfigurera en punkt-till-plats-anslutning till en Azure SQL-hanterad instans från den lokala platsen](point-to-site-p2s-configure.md)
  - [Konfigurera offentlig slut punkt i Azure SQL-hanterad instans](public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>DDL-syntax för T-SQL

Nedan finns T-SQL DDL-syntaxen som används för att stödja migrering av Windows-användare och grupper från en SQL Server instans till SQL-hanterad instans med Azure AD-autentisering.

```sql
-- For individual Windows users with logins
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com];

--For individual groups with logins
ALTER USER [domainName\groupName] WITH LOGIN=[groupName]
```

## <a name="arguments"></a>Argument

_Namn_</br>
Anger användarens domän namn.

_Användar_</br>
Anger namnet på den användare som identifierats i databasen.

_= loginName \@ domainName.com_</br>
Mappar om en användare till Azure AD-inloggningen

_Namn_</br>
Anger namnet på den grupp som identifierats i databasen.

## <a name="part-1-create-logins-in-sql-server-for-windows-users-and-groups"></a>Del 1: skapa inloggningar i SQL Server för Windows-användare och-grupper

> [!IMPORTANT]
> Följande syntax skapar en användare och en grupp inloggning i din SQL Server. Du måste kontrol lera att användaren och gruppen finns i din Active Directory (AD) innan du kör syntaxen nedan. </br> </br>
> Användare: testUser1, testGroupUser </br>
> Grupp: migrering-testGroupUser måste tillhöra migreringstabellen i AD

Exemplet nedan skapar en inloggning i SQL Server för ett konto med namnet _testUser1_ under domän _aadsqlmi_.

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases

use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;
go;

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser].
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;
go;


-- Check logins were created
select * from sys.server_principals;
go;
```

Skapa en databas för det här testet.

```sql
-- Create a database called [migration]
create database migration
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>Del 2: skapa Windows-användare och grupper och Lägg sedan till roller och behörigheter

Använd följande syntax för att skapa test användaren.

```sql
use migration;  
go

-- Create Windows user [aadsqlmi\testUser1] with login
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1];
go
```

Kontrol lera användar behörigheterna:

```sql
-- Check the user in the Metadata
select * from sys.database_principals;
go

-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions;
go
```

Skapa en roll och tilldela din test användare till den här rollen:

```sql
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole;
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole;
go

alter role UserMigrationRole add member [aadsqlmi\testUser1];
go
```

Använd följande fråga för att Visa användar namn som tilldelats en speciell roll:

```sql
-- Display user name assigned to a specific role
SELECT DP1.name AS DatabaseRoleName,
   isnull (DP2.name, 'No members') AS DatabaseUserName
 FROM sys.database_role_members AS DRM
 RIGHT OUTER JOIN sys.database_principals AS DP1
   ON DRM.role_principal_id = DP1.principal_id
 LEFT OUTER JOIN sys.database_principals AS DP2
   ON DRM.member_principal_id = DP2.principal_id
WHERE DP1.type = 'R'
ORDER BY DP1.name;
```

Använd följande syntax för att skapa en grupp. Lägg sedan till gruppen i rollen `db_owner` .

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration];
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration';
go

--Check the db_owner role for 'aadsqlmi\migration' group
select is_rolemember('db_owner', 'aadsqlmi\migration')
go
-- Output  ( 1 means YES)
```

Skapa en test tabell och Lägg till data med hjälp av följande syntax:

```sql
-- Create a table and add data
create table test ( a int, b int);
go

insert into test values (1,10)
go

-- Check the table values
select * from test;
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-sql-managed-instance"></a>Del 3: säkerhetskopiera och återställa den enskilda användar databasen till SQL-hanterad instans

Skapa en säkerhets kopia av migrerings databasen med hjälp av artikeln [Kopiera databaser med säkerhets kopiering och återställning](/sql/relational-databases/databases/copy-databases-with-backup-and-restore), eller Använd följande syntax:

```sql
use master;
go
backup database migration to disk = 'C:\Migration\migration.bak';
go
```

Följ vår [snabb start: Återställ en databas till en SQL-hanterad instans](restore-sample-database-quickstart.md).

## <a name="part-4-migrate-users-to-sql-managed-instance"></a>Del 4: migrera användare till SQL-hanterad instans

Kör kommandot ALTER USER för att slutföra migreringsprocessen på SQL-hanterad instans.

1. Logga in på din SQL-hanterade instans med Azure AD-administratörskontot för SQL-hanterad instans. Skapa sedan din Azure AD-inloggning i den SQL-hanterade instansen med hjälp av följande syntax. Mer information finns i [Självstudier: SQL-hanterad instans säkerhet i Azure SQL Database med hjälp av Azure AD server-Huvudkonton (inloggningar)](aad-security-configure-tutorial.md).

    ```sql
    use master
    go

    -- Create login for AAD user [testUser1@aadsqlmi.net]
    create login [testUser1@aadsqlmi.net] from external provider
    go

    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net]
    create login [migration] from external provider
    go

    --Check the two new logins
    select * from sys.server_principals
    go
    ```

1. Kontrol lera migreringen för rätt databas, tabell och huvud namn.

    ```sql
    -- Switch to the database migration that is already restored for MI
    use migration;
    go

    --Check if the restored table test exist and contain a row
    select * from test;
    go

    -- Check that the SQL on-premises Windows user/group exists  
    select * from sys.database_principals;
    go
    -- the old user aadsqlmi\testUser1 should be there
    -- the old group aadsqlmi\migration should be there
    ```

1. Använd ALTER USER syntax för att mappa den lokala användaren till Azure AD-inloggningen.

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net];
    go

    -- Check the principal
    select * from sys.database_principals;
    go
    -- New user testUser1@aadsqlmi.net should be there instead
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    -- Check a specific role
    -- Display Db user name assigned to a specific role
    SELECT DP1.name AS DatabaseRoleName,
    isnull (DP2.name, 'No members') AS DatabaseUserName
    FROM sys.database_role_members AS DRM
    RIGHT OUTER JOIN sys.database_principals AS DP1
    ON DRM.role_principal_id = DP1.principal_id
    LEFT OUTER JOIN sys.database_principals AS DP2
    ON DRM.member_principal_id = DP2.principal_id
    WHERE DP1.type = 'R'
    ORDER BY DP1.name;
    ```

1. Använd ALTER USER syntax för att mappa den lokala gruppen till Azure AD-inloggningen.

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration];
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals;
    go

    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    --Check the db_owner role for 'aadsqlmi\migration' user
    select is_rolemember('db_owner', 'migration')
    go
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>Del 5: testa Azure AD-användare eller-grupp-autentisering

Testa autentisering till SQL-hanterad instans med användaren som tidigare mappades till Azure AD-inloggningen med hjälp av syntaxen ALTER USER.

1. Logga in på den federerade virtuella datorn med din Azure SQL Managed instance-prenumeration som `aadsqlmi\testUser1`
1. Använd SQL Server Management Studio (SSMS) och logga in på din SQL-hanterade instans med **Active Directory integrerad** autentisering, ansluta till databasen `migration` .
    1. Du kan också logga in med hjälp av testUser1@aadsqlmi.net autentiseringsuppgifterna med alternativet SSMS **Active Directory – universellt med MFA-support**. I det här fallet kan du dock inte använda mekanismen för enkel inloggning och du måste ange ett lösen ord. Du behöver inte använda en federerad virtuell dator för att logga in på din SQL-hanterade instans.
1. Som en del av **roll medlemmen kan du välja från** `test` tabellen

    ```sql
    Select * from test  --  and see one row (1,10)
    ```

Testa autentisering till en SQL-hanterad instans med en medlem i en Windows-grupp `migration` . Användaren `aadsqlmi\testGroupUser` måste ha lagts till i gruppen `migration` innan migreringen.

1. Logga in på den federerade virtuella datorn med din Azure SQL Managed instance-prenumeration som `aadsqlmi\testGroupUser`
1. Med hjälp av SSMS med **Active Directory integrerad** autentisering ansluter du till Azure SQL-hanterade instans servern och databasen `migration`
    1. Du kan också logga in med hjälp av testGroupUser@aadsqlmi.net autentiseringsuppgifterna med alternativet SSMS **Active Directory – universellt med MFA-support**. I det här fallet kan du dock inte använda mekanismen för enkel inloggning och du måste ange ett lösen ord. Du behöver inte använda en federerad virtuell dator för att logga in på din SQL-hanterade instans.
1. Som en del av `db_owner` rollen kan du skapa en ny tabell.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```

> [!NOTE]
> På grund av ett känt design problem för Azure SQL Database kommer ett skapa en tabell-instruktion som körs som en medlem i en grupp att Miss Miss kan följande fel meddelande visas: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> Den aktuella lösningen är att skapa en tabell med ett befintligt schema i fallet ovan <dbo. New>

## <a name="next-steps"></a>Nästa steg

[Självstudie: Migrera SQL Server till Azure SQL-hanterad instans offline med DMS](../../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)
