---
title: SQL-autentisering
description: Lär dig mer om SQL-autentisering i Azure Synapse Analytics.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 505c0de5a508bd97b10091451116ec3670a20493
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677547"
---
# <a name="sql-authentication"></a>SQL-autentisering

Azure Synapse Analytics har två SQL Forms-faktorer som gör att du kan styra resursanvändningen. Den här artikeln förklarar hur de två form faktorerna styr användarautentisering.

Du kan använda två typer av auktorisering för att auktorisera till Synapse SQL:

- Azure Active Directory-auktorisering
- SQL-auktorisering

Med Azure Active Directory kan du ha en enda plats för användar hantering. SQL-auktorisering gör att äldre program kan använda Synapse SQL på ett välbekant sätt.

## <a name="administrative-accounts"></a>Administrativa konton

Det finns två administrativa konton (**Serveradministratör** och **Active Directory-administratör**) som fungerar som administratörer. Om du vill identifiera administratörs kontona för din SQL-Server öppnar du Azure Portal och navigerar till fliken Egenskaper i din Synapse SQL.

![SQL-serveradministratörer](./media/sql-authentication/sql-admins.png)

- **Serveradministratör**

  När du skapar en Azure Synapse-analys måste du namnge en **inloggning för Server administratör**. SQL Server skapar kontot som en inloggning i huvuddatabasen. Det här kontot ansluter med hjälp av SQL Server-autentisering (användarnamn och lösenord). Endast ett av dessa konton kan finnas.

- **Azure Active Directory-administratör**

  Ett Azure Active Directory-konto, antingen ett enskilt eller säkerhetsgruppkonto, kan också konfigureras som en administratör. Det är valfritt att konfigurera en Azure AD-administratör, men en Azure AD-administratör **måste** konfigureras om du vill använda Azure AD-konton för att ansluta till Synapse SQL.

Administratörs kontona för **Server administratören** och **Azure AD** har följande egenskaper:

- Är de enda konton som kan ansluta automatiskt till alla SQL Database på servern. (För att kunna ansluta till en användardatabas måste andra konton antingen vara ägare till databasen eller ha ett användarkonto i databasen.)
- Dessa konton går in i användardatabaser som användaren `dbo` och de har alla behörigheter i användardatabaserna. (Ägaren till en användardatabas går också in i databasen som användaren `dbo`.)
- Ange inte `master` databasen som `dbo` användare och har begränsad behörighet i Master.
- Är **inte** medlemmar i den `sysadmin` fasta Server rollen standard SQL Server, vilket inte är tillgängligt i SQL Database.  
- Kan skapa, ändra och släppa databaser, inloggningar, användare i huvud servrar och IP-brandvägg på server nivå.
- Kan lägga till och ta bort medlemmar `dbmanager` i `loginmanager` rollerna och.
- Kan visa `sys.sql_logins` system tabellen.

## <a name="serverless-sql-pool"></a>[SQL-pool utan Server](#tab/serverless)

Om du vill hantera användare som har åtkomst till en server utan SQL-pool kan du använda instruktionerna nedan.

Om du vill skapa en inloggning till en server lös SQL-pool använder du följande syntax:

```sql
CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
-- or
CREATE LOGIN Mary@domainname.net FROM EXTERNAL PROVIDER;
```
När inloggningen finns kan du skapa användare i de enskilda databaserna inom den serverbaserade slut punkten för SQL-poolen och bevilja dessa användare nödvändiga behörigheter. Om du vill skapa en användning kan du använda följande syntax:
```sql
CREATE USER Mary FROM LOGIN Mary;
-- or
CREATE USER Mary FROM LOGIN Mary@domainname.net;
-- or
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

När inloggningen och användaren har skapats kan du använda den vanliga SQL Server syntaxen för att bevilja rättigheter.

## <a name="sql-pool"></a>[SQL-pool](#tab/provisioned)

### <a name="administrator-access-path"></a>Åtkomstväg för administratör

När brandväggen på servernivå är korrekt konfigurerad kan **SQL-serveradministratören** och **Azure Active Directory-administratören** ansluta med hjälp av klientverktyg som SQL Server Management Studio eller SQL Server Data Tools. Endast de senaste verktygen innehåller alla funktioner och möjligheter. 

Följande diagram visar en typisk konfiguration för de två administratörs kontona:
 
![konfiguration av de två administrations kontona](./media/sql-authentication/1sql-db-administrator-access.png)

Vid användning av en öppen port i brandväggen på servernivå kan administratörer ansluta till en SQL Database.

### <a name="database-creators"></a>Databasskapare

En av dessa administrativa roller är **DBManager** -rollen. Medlemmar i den här rollen kan skapa nya databaser. För att använda den här rollen skapar du en användare i `master`-databasen och lägger sedan till användaren i **dbmanager**-databasrollen. 

Om du vill skapa en databas måste användaren vara en användare baserad på en SQL Server inloggning i `master` databasen eller innesluten databas användare baserat på en Azure Active Directory användare.

1. Anslut till databasen med ett administratörs konto `master` .
2. Skapa en inloggning för SQL Server autentisering med hjälp av instruktionen [create login](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true) . Exempel på instruktion:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Använd ett starkt lösenord när du skapar en inloggning eller en oberoende databasanvändare. Mer information finns i [Starka lösenord](/sql/relational-databases/security/strong-passwords?view=azure-sqldw-latest&preserve-view=true).

   För att förbättra prestandan cachelagras inloggningar (huvudnamn på servernivå) tillfälligt på databasnivån. Information om hur du uppdaterar autentiseringscache finns i [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql?view=azure-sqldw-latest&preserve-view=true).

3. Skapa en databas användare med hjälp av instruktionen [create User](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest&preserve-view=true) . Användaren kan vara en Azure Active Directory autentisering som innehåller databas användare (om du har konfigurerat din miljö för Azure AD-autentisering) eller en SQL Server autentisering som innehåller en databas användare eller en SQL Server autentisering som är baserad på en SQL Server autentisering (skapades i föregående steg). Exempel på uttryck:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Lägg till den nya användaren i **DBManager** -databas rollen i `master` med hjälp av metoden [Sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=azure-sqldw-latest&preserve-view=true) (Observera att [Alter Role](/sql/t-sql/statements/alter-role-transact-sql?view=azure-sqldw-latest&preserve-view=true) -instruktionen inte stöds i SQL-etableringen). Exempel på instruktioner:

   ```sql
   EXEC sp_addrolemember 'dbmanager', 'Mary'; 
   EXEC sp_addrolemember 'dbmanager', 'mike@contoso.com]'; 
   ```

   > [!NOTE]
   > Dbmanager är en databasroll i en huvuddatabas, så du kan bara lägga till en databasanvändare i dbmanager-rollen. Du kan inte lägga till en inloggning på servernivå till databasnivårollen.

5. Vid behov konfigurerar du en brandväggsregel så att den nya användaren kan ansluta. (Den nya användaren kan omfattas av en befintlig brandväggsregel.)

Användaren kan nu ansluta till `master` databasen och kan skapa nya databaser. Det konto som skapar databasen blir ägare till databasen.

### <a name="login-managers"></a>Inloggningshanterare

Den andra administrativa rollen är inloggningshanterare-rollen. Medlemmar i den här rollen kan skapa nya inloggningar i huvuddatabasen. Om du vill kan du slutföra samma steg (skapa en inloggning och användare och lägga till en användare i rollen **loginmanager**) så att en användare kan skapa nya inloggningar i huvuddatabasen. Vanligt vis är inloggningar inte nödvändiga eftersom Microsoft rekommenderar att du använder inneslutna databas användare, som autentiseras på databas nivå i stället för att använda användare baserat på inloggningar. Mer information finns i [Användare av oberoende databas – göra databasen portabel](/sql/relational-databases/security/contained-database-users-making-your-database-portable?view=azure-sqldw-latest&preserve-view=true).

---

## <a name="non-administrator-users"></a>Användare som är icke-administratörer

Icke-administratörskonton behöver vanligt vis inte åtkomst till huvud databasen. Skapa oberoende databasanvändare på databasnivå med hjälp av instruktionen [SKAPA ANVÄNDARE (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql). 

Användaren kan vara en Azure Active Directory-autentiserad oberoende databasanvändare (om du har konfigurerat din miljö för Azure AD-autentisering), eller en SQL Server-autentiserad oberoende databasanvändare, eller en SQL Server-autentiserad användare baserad på en SQL Server-autentiserad inloggning (skapad i föregående steg.)  

För att skapa användare, anslut till databasen och köra instruktioner som liknar följande exempel:

```sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Initialt kan endast en av administratörerna eller ägaren av databasen skapa användare. För att auktorisera ytterligare användare som ska kunna skapa nya användare: Ge den valda användaren `ALTER ANY USER`-behörighet genom att använda en instruktion som:

```sql
GRANT ALTER ANY USER TO Mary;
```

Om du vill ge fler användare fullständig kontroll över databasen gör du dem till medlem i den **db_owner** fasta databas rollen.

Använd instruktionen i Azure SQL Database eller Synapse server utan Server `ALTER ROLE` .

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

Använd [EXEC sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=azure-sqldw-latest&preserve-view=true)i dedikerad SQL-pool.

```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```

> [!NOTE]
> En vanlig orsak till att skapa en databas användare baserat på en Server inloggning är för användare som behöver åtkomst till flera databaser. Eftersom inneslutna databas användare är enskilda entiteter, behåller varje databas sin egen användare och det egna lösen ordet. Detta kan medföra att användaren måste komma ihåg varje lösen ord för varje databas, och det kan bli untenable när du behöver ändra flera lösen ord för många databaser. Men när du använder SQL Server inloggningar och hög tillgänglighet (aktiva geo-replikering och failover-grupper) måste SQL Server inloggningar anges manuellt på varje server. Annars kommer databas användaren inte längre att mappas till Server inloggningen efter en redundansväxling och kommer inte att kunna komma åt databasen efter redundansväxlingen. 

Mer information om hur du konfigurerar inloggningar för geo-replikering finns i  [Konfigurera och hantera Azure SQL Database säkerhet för geo-återställning eller redundans](../../azure-sql/database/active-geo-replication-security-configure.md).

### <a name="configuring-the-database-level-firewall"></a>Konfigurera brandvägg på databasnivå

Bästa praxis är att icke-administratörer bara har åtkomst genom brandväggen till de databaser som de använder. Istället för att auktorisera deras IP-adresser genom brandväggen på servernivå och ge dem åtkomst till alla databaser, använd instruktionen [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database?view=azure-sqldw-latest&preserve-view=true) för att konfigurera brandväggen på databasnivå. Brandväggen på databasnivå kan inte konfigureras via portalen.

### <a name="non-administrator-access-path"></a>Åtkomstväg för icke-administratör

När brandväggen på databasnivå är korrekt konfigurerad kan databasanvändare ansluta med klientverktyg som SQL Server Management Studio eller SQL Server Data Tools. Endast de senaste verktygen innehåller alla funktioner och möjligheter. I följande diagram visas en typisk åtkomstväg för en icke-administratör.

![Åtkomstväg för icke-administratör](./media/sql-authentication/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Grupper och roller

Effektiv åtkomsthantering använder behörigheter tilldelade grupper och roller i stället för enskilda användare.

- När du använder Azure Active Directory-autentisering, lägger du Azure Active Directory-användare i en Azure Active Directory-grupp. Skapa en oberoende databasanvändare för gruppen. Placera en eller flera användare i en [databasrollen](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true) och tilldela sedan [behörigheter](/sql/relational-databases/security/permissions-database-engine?view=azure-sqldw-latest&preserve-view=true) till databasrollen.

- När du använder SQL Server-autentisering kan du skapa inneslutna databasanvändare i databasen. Placera en eller flera användare i en [databasrollen](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true) och tilldela sedan [behörigheter](/sql/relational-databases/security/permissions-database-engine?view=azure-sqldw-latest&preserve-view=true) till databasrollen.

Databasrollerna kan vara de inbyggda rollerna, som **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** och **db_denydatareader**. **db_owner** används ofta för att endast ge fullständig behörighet till några användare. De andra fasta databasrollerna är användbara för att snabbt få en enkel databas i utveckling, men de rekommenderas inte för de flesta produktionsdatabaserna. 

Till exempel ger den fasta databasrollen **db_datareader** läsbehörighet till alla tabeller i databasen, vilket vanligtvis är mer än är absolut nödvändigt. 

Det är mycket bättre att använda instruktionen [create Role](/sql/t-sql/statements/create-role-transact-sql) för att skapa egna användardefinierade databas roller och samtidigt ge varje roll de lägsta behörigheter som krävs för affärs behovet. När en användare är medlem i flera roller sammanställs behörigheterna för alla.

## <a name="permissions"></a>Behörigheter

Det finns över 100 behörigheter som individuellt kan beviljas eller nekas i SQL Database. Många av de här behörigheterna är kapslade. Till exempel inkluderar `UPDATE`-behörighet på ett schema `UPDATE`-behörighet för alla tabeller i schemat. Som i de flesta andra behörighetssystem åsidosätter ett nekande av en behörighet en beviljad. 

På grund av den kapslade karaktären och antalet behörigheter kan det krävas noggranna studier för att designa ett behörighetssystem som korrekt skyddar databasen. 

Börja med listan över behörigheter på [Behörigheter (Databasmotor)](/sql/relational-databases/security/permissions-database-engine) och granska den [stora bilden](/sql/relational-databases/security/media/database-engine-permissions.png) med behörigheter.

### <a name="considerations-and-restrictions"></a>Överväganden och begränsningar

Tänk på följande när du hanterar inloggningar och användare i SQL Database:

- Du måste vara ansluten till **huvud** databasen när du kör `CREATE/ALTER/DROP DATABASE` instruktionerna.
- Databas användaren som motsvarar inloggningen för **Server administratören** kan inte ändras eller tas bort.
- Amerikansk engelska är standardspråket för inloggningen **Serveradministratör**.
- Endast administratörer (inloggningen som **serveradministratör** eller Azure AD-administratör) och medlemmar i databasrollen **dbmanager** i **huvuddatabasen** har behörighet att köra `CREATE DATABASE`- och `DROP DATABASE`-uttrycken.
- Du måste vara ansluten till huvuddatabasen när du kör uttrycket `CREATE/ALTER/DROP LOGIN`. Att använda inloggningar rekommenderas inte. Använd i stället oberoende databasanvändare.
- Du måste ange namnet på databasen i anslutningssträngen för att ansluta till en användardatabas.
- Endast huvudsaklig inloggning på servernivå och medlemmarna i databasrollen **loginmanager** i **huvud** databasen har behörighet att köra uttryck `CREATE LOGIN`, `ALTER LOGIN` och `DROP LOGIN`.
- När du kör `CREATE/ALTER/DROP LOGIN` `CREATE/ALTER/DROP DATABASE` -satserna i ett ADO.NET-program är det inte tillåtet att använda parametriserade kommandon. Mer information finns i [Kommandon och parametrar](/dotnet/framework/data/adonet/commands-and-parameters).
- När du kör uttrycket `CREATE/ALTER/DROP DATABASE` och `CREATE/ALTER/DROP LOGIN`, måste vart och ett av dessa uttryck vara den enda instruktionen i en Transact-SQL-batch. Annars uppstår ett fel. Till exempel kontrollerar följande Transact-SQL huruvida databasen finns. Om den finns anropas ett `DROP DATABASE`-uttryck för att ta bort databasen. Eftersom `DROP DATABASE`-uttrycket inte är det enda uttrycket i batchen, ger körning av följande Transact-SQL-uttryck ett fel.

  ```sql
  IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
  DROP DATABASE [database_name];
  GO
  ```
  
  Använd i stället följande Transact-SQL-uttryck:
  
  ```sql
  DROP DATABASE IF EXISTS [database_name]
  ```

- När du kör uttryck `CREATE USER` med alternativ `FOR/FROM LOGIN`, måste det vara det enda uttrycket i en Transact-SQL-batch.
- När du kör uttryck `ALTER USER` med alternativ `WITH LOGIN`, måste det vara det enda uttrycket i en Transact-SQL-batch.
- För `CREATE/ALTER/DROP` behöver en användare behörighet `ALTER ANY USER` på databasen.
- När ägaren av en databasroll försöker lägga till eller ta bort en annan databasanvändare till eller från databasrollen uppstår följande fel: **Användarens eller rollens ”Namn” finns inte i den här databasen.** Felet beror på att användaren inte är synlig för ägaren. Ge rollägare behörighet `VIEW DEFINITION` på användaren för att lösa problemet. 

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Användare av oberoende databas – göra databasen portabel](/sql/relational-databases/security/contained-database-users-making-your-database-portable).
