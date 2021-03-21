---
title: Appar för flera klient organisationer med RLS och Elastic Database-verktyg
description: Använd Elastic Database-verktyg med säkerhet på radnivå för att bygga ett program med en mycket skalbar data nivå.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 6d753a90f2a4cb19c9f3933d007fb3d378af6d81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793219"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Program med flera klienter med elastiska databas verktyg och säkerhet på radnivå
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Elastiska databas verktyg](elastic-scale-get-started.md) och [säkerhet på radnivå (RLS)][rls] för att möjliggöra skalning av data nivån för ett program med flera klienter med Azure SQL Database. Tillsammans hjälper dessa tekniker dig att bygga ett program som har en mycket skalbar data nivå. Data nivån stöder Shards för flera innehavare och använder **ADO.net SqlClient** eller **Entity Framework**. Mer information finns i [design mönster för SaaS-program med flera innehavare med Azure SQL Database](./saas-tenancy-app-design-patterns.md).

- **Elastiska databas verktyg** gör det möjligt för utvecklare att skala ut data nivån med standard horisontell partitionering-metoder, med hjälp av .NET-bibliotek och Azure-tjänstmallar. Genom att hantera Shards med hjälp av [klient biblioteket för Elastic Database][s-d-elastic-database-client-library] kan du automatisera och effektivisera många av de infrastruktur-uppgifter som vanligt vis är kopplade till horisontell partitionering.
- **Säkerhet på radnivå** gör att utvecklare säkert kan lagra data för flera klienter i samma databas. RLS säkerhets principer filtrera bort rader som inte tillhör klienten som kör en fråga. Att centralisera filter logiken i databasen fören klar underhållet och minskar risken för ett säkerhets fel. Alternativet att förlita dig på all klient kod för att genomdriva säkerheten är riskabelt.

Genom att använda dessa funktioner tillsammans kan ett program lagra data för flera klienter i samma Shard-databas. IT kostar mindre per klient när klienterna delar en databas. Samma program kan också erbjuda sina Premium-klienter möjlighet att betala för sina egna dedikerade Shard för en klient. En fördel med en isolering med enskild klient är att garantera prestanda. Det finns ingen annan klient som konkurrerar för resurser i en databas för en enda klient.

Målet är att använda [data beroende](elastic-scale-data-dependent-routing.md) API: er för klient bibliotekets elastiska databas för att automatiskt ansluta varje klient till rätt Shard-databas. Endast ett Shard innehåller ett visst TenantId-värde för den angivna innehavaren. TenantId är *nyckeln horisontell partitionering*. När anslutningen har upprättats säkerställer en säkerhets princip för RLS i databasen att den angivna innehavaren endast har åtkomst till de data rader som innehåller sitt TenantId.

> [!NOTE]
> Klient-ID: n kan bestå av mer än en kolumn. För att under lätta är den här diskussionen att vi har informerat ett TenantId i en kolumn.

![Blogga appens arkitektur][1]

## <a name="download-the-sample-project"></a>Hämta exempel projektet

### <a name="prerequisites"></a>Förutsättningar

- Använda Visual Studio (2012 eller högre)
- Skapa tre databaser i Azure SQL Database
- Hämta exempel projekt: [elastiska dB-verktyg för Azure SQL-multi-Tenant Shards](https://go.microsoft.com/?linkid=9888163)
  - Fyll i informationen för dina databaser i början av **programmet. cs**

Det här projektet utökar det som beskrivs i [elastiska dB-verktyg för Azure SQL-Entity Framework-integrering](elastic-scale-use-entity-framework-applications-visual-studio.md) genom att lägga till stöd för Shard-databaser med flera innehavare. Projektet skapar ett enkelt konsol program för att skapa Bloggar och inlägg. Projektet innehåller fyra klienter, plus två Shard-databaser för flera innehavare. Den här konfigurationen illustreras i föregående diagram.

Skapa och kör programmet. Den här körningen startar starter av elastiska databas verktyg "Shard Map Manager" och utför följande tester:

1. Skapa en ny blogg med Entity Framework och LINQ och Visa sedan alla bloggar för varje klient
2. Använd ADO.NET SqlClient och Visa alla bloggar för en klient
3. Försök att infoga en blogg för fel klient för att kontrol lera att ett fel har uppstått

Observera att eftersom RLS ännu inte har Aktiver ATS i Shard-databaserna visar var och en av dessa tester ett problem: innehavare kan se Bloggar som inte tillhör dem och programmet hindras inte från att infoga en blogg för fel klient. Resten av den här artikeln beskriver hur du löser problemen genom att tvinga klient isolering med RLS. Det finns två steg:

1. **Program nivå**: ändra program koden så att den alltid anger aktuellt TENANTID i sessionens \_ kontext när du har öppnat en anslutning. Detta exempel projekt anger redan TenantId på det här sättet.
2. **Datanivå**: skapa en säkerhets princip för RLS i varje Shard-databas för att filtrera rader baserat på det TenantId som lagras i sessionen \_ . Skapa en princip för var och en av dina Shard-databaser, annars filtreras inte rader i Shards med flera innehavare.

## <a name="1-application-tier-set-tenantid-in-the-session_context"></a>1. program nivå: Ange TenantId i SESSION- \_ kontexten

Först ansluter du till en Shard-databas med hjälp av API: er för data beroende routning i klient biblioteket för Elastic Database. Programmet måste fortfarande meddela databasen som TenantId använder anslutningen. TenantId talar om vilken säkerhets princip för RLS som rader måste filtreras efter som tillhör andra klienter. Lagra aktuellt TenantId i [sessions- \_ kontexten](/sql/t-sql/functions/session-context-transact-sql) för anslutningen.

Ett alternativ till SESSION \_ Context är att använda [Sammanhangs \_ information](/sql/t-sql/functions/context-info-transact-sql). Men SESSIONs- \_ kontexten är ett bättre alternativ. \_Det är enklare att använda sessions-kontexten, den returnerar null som standard och stöder nyckel/värde-par.

### <a name="entity-framework"></a>Entity Framework

För program som använder Entity Framework är den enklaste metoden att ställa in SESSIONens \_ kontext i ElasticScaleContext-åsidosättningen som beskrivs i [data beroende routning med EF DbContext](elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Skapa och kör en SqlCommand som anger TenantId i sessionen i \_ kontexten till shardingKey som har angetts för anslutningen. Returnera sedan anslutnings utjämning via data beroende routning. På så sätt behöver du bara skriva kod en gång för att ställa in SESSIONs- \_ kontexten.

```csharp
// ElasticScaleContext.cs
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}
// ...
```

Nu anges SESSIONs \_ kontexten automatiskt med det angivna TenantId när ElasticScaleContext anropas:

```csharp
// Program.cs
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);
        foreach (var item in query)
        {
            Console.WriteLine(item.Name);
        }
    }
});
```

### <a name="adonet-sqlclient"></a>ADO.NET SqlClient

För program som använder ADO.NET SqlClient skapar du en wrapper-funktion runt metoden ShardMap. OpenConnectionForKey. Ange att omslutningen automatiskt ska ange TenantId i SESSIONens \_ kontext till det aktuella TenantId innan en anslutning returneras. För att säkerställa att SESSIONs \_ sammanhang alltid är inställt, bör du bara öppna anslutningar med den här funktionen.

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2. datanivå: skapa säkerhets princip på radnivå

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Skapa en säkerhets princip för att filtrera de rader som varje innehavare har åtkomst till

Nu när programmet ställer in en SESSIONSNYCKEL \_ med aktuellt tenantid innan du frågar, kan en RLS-säkerhets princip filtrera frågor och utesluta rader som har ett annat TenantId.

RLS implementeras i Transact-SQL. En användardefinierad funktion definierar åtkomst logiken och en säkerhets princip binder funktionen till valfritt antal tabeller. För det här projektet:

1. Funktionen kontrollerar att programmet är anslutet till databasen och att TenantId som lagras i SESSIONens \_ kontext matchar tenantid för en specifik rad.
    - Programmet är anslutet, i stället för någon annan SQL-användare.

2. Ett FILTER-predikat tillåter att rader som uppfyller TenantId-filtret passerar för URVALs-, UPPDATERINGs-och BORTTAGNINGs frågor.
    - Ett BLOCK-predikat förhindrar att rader som inte uppfyller filtret infogas eller uppdateras.
    - Om SESSION \_ context inte har angetts returnerar funktionen null och inga rader är synliga eller kan infogas.

Om du vill aktivera RLS på alla Shards kör du följande T-SQL med hjälp av antingen Visual Studio (SSDT), SSMS eller PowerShell-skriptet som ingår i projektet. Eller om du använder [Elastic Database jobb](./elastic-jobs-overview.md)kan du automatisera körningen av det här T-SQL på alla Shards.

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO
```

> [!TIP]
> I ett komplext projekt kan du behöva lägga till predikatet i hundratals tabeller, vilket kan vara omständligt. Det finns en hjälp procedur lagrad procedur som automatiskt genererar en säkerhets princip och lägger till ett predikat i alla tabeller i ett schema. Mer information finns i blogg inlägget vid [tillämpa Row-Level säkerhet för alla tabeller – hjälp skript (blogg)](https://techcommunity.microsoft.com/t5/sql-server/apply-row-level-security-to-all-tables-helper-script/ba-p/384360).

Nu när du kör exempel programmet igen ser klienterna bara de rader som hör till dem. Dessutom kan inte programmet infoga rader som tillhör andra innehavare än den som för närvarande är ansluten till Shard-databasen. Appen kan inte heller uppdatera TenantId i de rader som kan visas. Om appen försöker göra något, utlöses en DbUpdateException.

Om du lägger till en ny tabell senare ändrar du säkerhets principen för att lägga till FILTER och blockera predikat i den nya tabellen.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Lägg till standard begränsningar för att automatiskt fylla TenantId för infogningar

Du kan ange en standard begränsning för varje tabell för att automatiskt fylla i TenantId med det värde som lagras i SESSIONens \_ kontext när rader infogas. Ett exempel följer.

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs
    ADD CONSTRAINT df_TenantId_Blogs
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts
    ADD CONSTRAINT df_TenantId_Posts
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO
```

Nu behöver programmet inte ange ett TenantId vid infogning av rader:

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);
        db.SaveChanges();
    }
});
```

> [!NOTE]
> Om du använder standard begränsningar för ett Entity Framework-projekt rekommenderar vi att du *inte* tar med kolumnen TenantId i din EF-datamodell. Den här rekommendationen beror på att Entity Framework frågor automatiskt tillhandahåller standardvärden som åsidosätter standard begränsningar som skapats i T-SQL och som använder SESSION \_ context.
> Om du vill använda standard begränsningar i exempelprojektet, till exempel, bör du ta bort TenantId från DataClasses. CS (och köra Add-Migration i Package Manager-konsolen) och använda T-SQL för att se till att fältet endast finns i databas tabellerna. På så sätt anger EF automatiskt felaktiga standardvärden när data infogas.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>Valfritt Aktivera en *superanvändare* för åtkomst till alla rader

Vissa program kanske vill skapa en *superanvändare* som har åtkomst till alla rader. En superanvändare kan aktivera rapportering för alla klienter på alla Shards. Eller en superanvändare kan utföra delade sammanslagnings åtgärder på Shards som involverar flytt av klient rader mellan databaser.

Om du vill aktivera en superanvändare skapar du en ny SQL-användare ( `superuser` i det här exemplet) i varje Shard-databas. Ändra sedan säkerhets principen med en ny predika-funktion som ger användaren åtkomst till alla rader. En sådan funktion anges härnäst.

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        WHERE
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        )
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>Underhåll

- **Lägger till nya Shards**: kör T-SQL-skriptet för att aktivera RLS på alla nya Shards, annars filtreras inte frågor på dessa Shards.
- **Nya tabeller läggs** till: Lägg till ett filter och blockera predikat i säkerhets principen på alla Shards varje gång som en ny tabell skapas. Andra frågor om den nya tabellen filtreras inte. Det här tillägget kan automatiseras med hjälp av en DDL-utlösare, enligt beskrivningen i [tillämpa Row-Level säkerhet automatiskt på nyligen skapade tabeller (blogg)](https://techcommunity.microsoft.com/t5/SQL-Server/Apply-Row-Level-Security-automatically-to-newly-created-tables/ba-p/384393).

## <a name="summary"></a>Sammanfattning

Elastiska databas verktyg och säkerhet på radnivå kan användas tillsammans för att skala ut ett programs data lager med stöd för både flera innehavare och Shards för en enda klient. Shards för flera innehavare kan användas för att lagra data på ett mer effektivt sätt. Den här effektiviteten uttalas när ett stort antal klienter bara har några rader med data. Shards för en enskild klient kan ge stöd för Premium-klienter som har striktare prestanda och isolerings krav. Mer information finns i [säkerhets referens på radnivå][rls].

## <a name="additional-resources"></a>Ytterligare resurser

- [Vad är en elastisk pool i Azure?](elastic-pool-overview.md)
- [Skala ut med Azure SQL Database](elastic-scale-introduction.md)
- [Designmönster för SaaS-program med flera klientorganisationer med Azure SQL Database](./saas-tenancy-app-design-patterns.md)
- [Autentisering i appar med flera klienter, med hjälp av Azure AD och OpenID Connect](/azure/architecture/multitenant-identity/authenticate)
- [Tailspin Surveys-programmet](/azure/architecture/multitenant-identity/tailspin)

## <a name="questions-and-feature-requests"></a>Frågor och funktions begär Anden

Om du har frågor kan du kontakta oss på [sidan Microsoft Q&en fråga för SQL Database](/answers/topics/azure-sql-database.html). Och Lägg till eventuella funktions förfrågningar till [forumen SQL Database feedback](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: /sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]:elastic-database-client-library.md