---
title: Använda klient biblioteket för Elastic Database med Entity Framework
description: Använda Elastic Database klient bibliotek och Entity Framework för att koda databaser
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/04/2019
ms.openlocfilehash: 48d43cb2d3c51194d0708a2b9b739a0ee87843d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793406"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Elastic Database klient bibliotek med Entity Framework
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Det här dokumentet visar ändringarna i ett Entity Framework program som behövs för att integrera med [Elastic Database-verktygen](elastic-scale-introduction.md). Fokus är att skapa [Shard kart hantering](elastic-scale-shard-map-management.md) och [data beroende routning](elastic-scale-data-dependent-routing.md) med den Entity Framework **koden första** metoden. Själv studie kursen för att börja med en [ny databas](/ef/ef6/modeling/code-first/workflows/new-database) för EF fungerar som ett exempel i det här dokumentet. Exempel koden som medföljer detta dokument är en del av de elastiska databas verktygen i Visual Studio Code-exempel.

## <a name="downloading-and-running-the-sample-code"></a>Hämta och köra exempel koden

Så här hämtar du koden för den här artikeln:

* Visual Studio 2012 eller senare krävs.
* Ladda ned de [elastiska DB-verktygen för Azure SQL-Entity Framework integrations exempel](https://github.com/Azure/elastic-db-tools/). Packa upp exemplet på en plats som du väljer.
* Starta Visual Studio.
* I Visual Studio väljer du fil-> öppna projekt/lösning.
* I dialog rutan **öppna projekt** navigerar du till det exempel som du laddade ned och väljer **EntityFrameworkCodeFirst. SLN** för att öppna exemplet.

Om du vill köra exemplet måste du skapa tre tomma databaser i Azure SQL Database:

* Shard Map Manager-databas
* Shard 1-databas
* Shard 2-databas

När du har skapat dessa databaser fyller du i plats innehavarna i **program. cs** med Server namnet, databas namnen och dina autentiseringsuppgifter för att ansluta till databaserna. Bygg lösningen i Visual Studio. I Visual Studio hämtas nödvändiga NuGet-paket för klient biblioteket för elastiska databaser, Entity Framework och tillfälligt fel hantering som en del av bygg processen. Se till att återställa NuGet-paket är aktiverat för din lösning. Du kan aktivera den här inställningen genom att högerklicka på lösnings filen i Visual Studio-Solution Explorer.

## <a name="entity-framework-workflows"></a>Entity Framework arbets flöden

Entity Framework utvecklare förlitar sig på något av följande fyra arbets flöden för att bygga program och se till att beständigheten för program objekt:

* **Kod först (ny databas)**: ef-utvecklaren skapar modellen i program koden och sedan skapar EF databasen från den.
* **Kod först (befintlig databas)**: utvecklaren tillåter att EF genererar program koden för modellen från en befintlig databas.
* **Modell först**: utvecklaren skapar modellen i EF-designern och sedan skapar EF databasen från modellen.
* **Databasen först**: utvecklaren använder EF-verktyg för att härleda modellen från en befintlig databas.

Alla dessa metoder förlitar sig på DbContext-klassen för transparent hantering av databas anslutningar och databas schema för ett program. Olika konstruktörer i DbContext-Bask Lassen tillåter olika nivåer av kontroll över skapande av anslutningar, databas start och schema skapande. Utmaningarna uppstår främst från det faktum att databas anslutnings hanteringen från EF överlappar anslutnings hanterings funktionerna i de data beroende routningsgränssnitt som tillhandahålls av klient biblioteket för Elastic Database.

## <a name="elastic-database-tools-assumptions"></a>Antaganden om elastiska databas verktyg

För term definitioner, se [Elastic Database tools-ordlista](elastic-scale-glossary.md).

Med klient biblioteket för Elastic Database definierar du partitioner för dina program data som kallas shardletar. Shardletar identifieras av en horisontell partitionering-nyckel och mappas till vissa databaser. Ett program kan ha så många databaser som behövs och distribuera shardletar för att tillhandahålla tillräckligt med kapacitet eller prestanda för aktuella affärs behov. Mappningen av horisontell partitionering-nyckel värden till databaserna lagras av en Shard-mappning från klient-API: erna för Elastic Database. Den här funktionen kallas **Shard Map Management** eller SMM för kort. Shard-kartan fungerar också som koordinator för databas anslutningar för begär Anden som har en horisontell partitionering-nyckel. Den här funktionen kallas för **data beroende routning**.

Shard Map Manager skyddar användare från inkonsekventa vyer till shardlet-data som kan uppstå när samtidiga shardlet hanterings åtgärder (till exempel att flytta data från en Shard till en annan) sker. Det gör du genom att Shard Maps som hanteras av klient biblioteks koordinatorn databas anslutningar för ett program. Detta gör att Shard-kart funktionen automatiskt avdödar en databas anslutning när Shard hanterings åtgärder kan påverka shardlet som anslutningen har skapats för. Den här metoden måste integreras med några av EFs funktioner, till exempel skapa nya anslutningar från en befintlig för att kontrol lera om det finns några databas förekomster. I allmänhet har vår iakttagelse att de vanliga DbContext-konstruktörerna fungerar som tillförlitliga för stängda databas anslutningar som kan klonas på ett säkert sätt för EF-arbete. Design principen för Elastic Database är i stället endast till att endast Service Broker-öppna anslutningar. Det kan tänkas att stängning av en anslutning som upprättas av klient biblioteket innan du överlämnar det till EF-DbContext kan lösa problemet. Men genom att stänga anslutningen och förlita dig på EF för att öppna den igen, så är det en i föregående fall verifierings-och konsekvens kontrollerna som utförs av biblioteket. Funktionen migreringar i EF använder dock dessa anslutningar för att hantera det underliggande databasschemat på ett sätt som är transparent för programmet. Vi rekommenderar att du behåller och kombinerar alla dessa funktioner från både klient biblioteket för Elastic Database och EF i samma program. I följande avsnitt beskrivs dessa egenskaper och krav i detalj.

## <a name="requirements"></a>Krav

När du arbetar med både klient biblioteket för Elastic Database och Entity Framework API: er, vill du behålla följande egenskaper:

* **Skala ut**: om du vill lägga till eller ta bort databaser från data nivån i shardade-programmet vid behov för programmets kapacitets krav. Det innebär att du styr skapandet och borttagningen av databaser och använder de Elastic Database Shard Map Manager-API: erna för att hantera databaser och mappningar av shardletar.
* **Konsekvens**: programmet använder horisontell partitionering och använder funktionerna för data beroende routning i klient biblioteket. För att undvika skador eller fel frågeresultat sammanställs anslutningarna via Shard Map Manager. Detta behåller även validering och konsekvens.
* **Första koden**: för att bevara bekvämligheten med den första paradigmen för EFS kod. I kod först mappas klasser i programmet transparent till de underliggande databas strukturerna. Program koden interagerar med DbSets som maskerar de flesta aspekter som ingår i den underliggande databas bearbetningen.
* **Schema**: Entity Framework hanterar det inledande skapandet av databas schema och efterföljande schema utveckling genom migreringar. Genom att behålla dessa funktioner är det enkelt att anpassa din app när data utvecklas.

Följande rikt linjer ger information om hur du uppfyller dessa krav för att koda de första programmen med elastiska databas verktyg.

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Data beroende routning med EF DbContext

Databas anslutningar med Entity Framework hanteras vanligt vis via underklasser i **DbContext**. Skapa dessa underklasser genom att härleda från **DbContext**. Det är här du definierar din **DbSets** som implementerar de databasbaserade samlingarna av CLR-objekt för ditt program. I samband med data beroende routning kan du identifiera flera användbara egenskaper som inte nödvändigt vis är för andra EF-kodade första program scenarier:

* Databasen finns redan och har registrerats i Shard-kartan för elastisk databas.
* Schemat för programmet har redan distribuerats till databasen (förklaras nedan).
* Data beroende routnings anslutningar till databasen har sammanställts av Shard-kartan.

Så här integrerar du **DbContexts** med data beroende routning för utskalning:

1. Skapa fysiska databas anslutningar via de elastiska databas klient gränssnitten i Shard Map Manager.
2. Bryt anslutningen till underklassen **DbContext**
3. Överför anslutningen till **DbContext** -bas klasserna för att säkerställa att all bearbetning på EF-sidan också sker.

I följande kod exempel visas den här metoden. (Den här koden finns också i det tillhör ande Visual Studio-projektet)

```csharp
public class ElasticScaleContext<T> : DbContext
{
public DbSet<Blog> Blogs { get; set; }
...

    // C'tor for data-dependent routing. This call opens a validated connection
    // routed to the proper shard by the shard map manager.
    // Note that the base class c'tor call fails for an open connection
    // if migrations need to be done and SQL credentials are used. This is the reason for the
    // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
    public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
        : base(CreateDDRConnection(shardMap, shardingKey, connectionStr),
        true /* contextOwnsConnection */)
    {
    }

    // Only static methods are allowed in calls into base class c'tors.
    private static DbConnection CreateDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
    {
        // No initialization
        Database.SetInitializer<ElasticScaleContext<T>>(null);

        // Ask shard map to broker a validated connection for the given key
        SqlConnection conn = shardMap.OpenConnectionForKey<T>
                            (shardingKey, connectionStr, ConnectionOptions.Validate);
        return conn;
    }
```

## <a name="main-points"></a>Huvud punkter

* En ny konstruktor ersätter Standardkonstruktorn i underklassen DbContext
* Den nya konstruktorn tar de argument som krävs för data beroende routning via klient biblioteket för Elastic Database:
  
  * Shard-kartan för att få åtkomst till data beroende routningsgränssnitt,
  * horisontell partitionering-nyckeln för att identifiera shardlet,
  * en anslutnings sträng med autentiseringsuppgifterna för den data beroende routning anslutningen till Shard.
* Anropet till Bask Lassens konstruktor tar en inblick i en statisk metod som utför alla steg som krävs för data beroende routning.
  
  * Den använder OpenConnectionForKey-anropet av klient gränssnitten för Elastic Database på Shard-kartan för att upprätta en öppen anslutning.
  * Shard-kartan skapar den öppna anslutningen till Shard som innehåller shardlet för den aktuella horisontell partitionering-nyckeln.
  * Den här öppna anslutningen skickas tillbaka till Bask lass konstruktorn för DbContext för att indikera att den här anslutningen ska användas av EF i stället för att skapa en ny anslutning automatiskt. På så sätt har anslutningen taggats av klient-API: t för elastisk databas så att den kan garantera konsekvens under Shard Map Management-åtgärder.

Använd den nya konstruktorn för DbContext-underklassen i stället för Standardkonstruktorn i koden. Här är ett exempel:

```csharp
// Create and save a new blog.

Console.Write("Enter a name for a new blog: ");
var name = Console.ReadLine();

using (var db = new ElasticScaleContext<int>(
                        sharding.ShardMap,  
                        tenantId1,  
                        connStrBldr.ConnectionString))
{
    var blog = new Blog { Name = name };
    db.Blogs.Add(blog);
    db.SaveChanges();

    // Display all Blogs for tenant 1
    var query = from b in db.Blogs
                orderby b.Name
                select b;
    …
}
```

Den nya konstruktorn öppnar anslutningen till Shard som innehåller data för shardlet som identifieras av värdet för **tenantid1**. Koden i det **använda** blocket förblir oförändrad för att få åtkomst till **DbSet** för Bloggar som använder EF på Shard för **tenantid1**. Detta ändrar semantiken för koden i det använda blocket, så att alla databas åtgärder nu är begränsade till den enda Shard där **tenantid1** hålls. En LINQ-fråga över bloggen **DbSet** skulle till exempel bara returnera Bloggar som lagras på den aktuella Shard, men inte de som lagras på andra Shards.  

### <a name="transient-faults-handling"></a>Hantering av tillfälliga fel

Microsoft patterns & Practices-teamet publicerade det [tillfälliga fel hanterings program blocket](/previous-versions/msp-n-p/dn440719(v=pandp.60)). Biblioteket används med elastisk skalnings klient bibliotek i kombination med EF. Se dock till att eventuella tillfälliga undantag returneras till en plats där du kan se till att den nya konstruktorn används efter ett tillfälligt fel så att alla nya anslutnings försök görs med hjälp av konstruktörer som du har justerat. Annars är en anslutning till rätt Shard inte garanterad och det finns inga garantier att anslutningen upprätthålls eftersom ändringar i Shard-mappningen sker.

Följande kod exempel visar hur en princip för SQL-återförsök kan användas runt de nya **DbContext** -underklassens konstruktörer:

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString))
        {
                var blog = new Blog { Name = name };
                db.Blogs.Add(blog);
                db.SaveChanges();
        …
        }
    });
```

**SqlDatabaseUtils. SqlRetryPolicy** i koden ovan definieras som en **SqlDatabaseTransientErrorDetectionStrategy** med ett antal återförsök på 10 och 5 sekunders vänte tid mellan återförsök. Den här metoden liknar vägledningen för EF-och användarinitierade transaktioner (se [begränsningar med att försöka köra körnings strategier (EF6 och senare)](/ef/ef6/fundamentals/connection-resiliency/retry-logic). Båda situationerna kräver att programmet styr omfattningen som det tillfälliga undantaget returnerar: för att antingen öppna transaktionen igen eller (som visas), måste du återskapa kontexten från rätt konstruktor som använder klient biblioteket för Elastic Database.

Behovet av att kontrol lera var tillfälliga undantag gör oss tillbaka i räckvidden innebär också att den inbyggda **SqlAzureExecutionStrategy** som medföljer EF används. **SqlAzureExecutionStrategy** skulle öppna en anslutning igen men inte använda **OpenConnectionForKey** och därmed kringgå all verifiering som utförs som en del av anropet **OpenConnectionForKey** . I stället använder kod exemplet den inbyggda **DefaultExecutionStrategy** som också levereras med EF. Till skillnad från **SqlAzureExecutionStrategy** fungerar den korrekt i kombination med principen för återförsök från tillfälligt fel hantering. Körnings principen anges i klassen **ElasticScaleDbConfiguration** . Observera att vi beslutade att inte använda **DefaultSqlExecutionStrategy** eftersom det föreslår användning av **SqlAzureExecutionStrategy** om tillfälliga undantag uppstår, vilket skulle leda till fel beteende som diskuteras. Mer information om de olika principerna för återförsök och EF finns i [anslutnings återhämtning i EF](/ef/ef6/fundamentals/connection-resiliency/retry-logic).

#### <a name="constructor-rewrites"></a>Omskrivning av konstruktor

Kod exemplen ovan illustrerar standardvärdet för konstruktörer som krävs för ditt program för att kunna använda data beroende routning med Entity Framework. Följande tabell generaliserar den här metoden med andra konstruktorer.

| Aktuell konstruktor | Omskriven konstruktor för data | Base-konstruktor | Kommentarer |
| --- | --- | --- | --- |
| Kontext () |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection, bool) |Anslutningen måste vara en funktion i Shard-mappningen och den data beroende Dirigerings nyckeln. Du måste efter-pass skapa automatisk anslutning av EF, och i stället använda Shard-kartan för att hantera anslutningen. |
| Kontext (sträng) |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection, bool) |Anslutningen är en funktion i Shard-mappningen och den data beroende Dirigerings nyckeln. Ett fast databas namn eller en anslutnings sträng fungerar inte när de skickas genom Shard-mappningen. |
| DbCompiledModel (kontext) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |Anslutningen skapas för den angivna Shard-kartan och horisontell partitionering-nyckeln med den angivna modellen. Den kompilerade modellen överförs till bas-c'tor. |
| Kontext (DbConnection, bool) |ElasticScaleContext (ShardMap, TKey, bool) |DbContext (DbConnection, bool) |Anslutningen måste härledas från Shard-mappningen och nyckeln. Den kan inte tillhandahållas som indata (om inte indata redan användes med Shard-mappningen och nyckeln). Det booleska värdet skickas. |
| Kontext (sträng, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |Anslutningen måste härledas från Shard-mappningen och nyckeln. Den kan inte tillhandahållas som indata (om inte indata användes av Shard-mappningen och nyckeln). Den kompilerade modellen överförs. |
| Kontext (ObjectContext, bool) |ElasticScaleContext (ShardMap, TKey, ObjectContext, bool) |DbContext (ObjectContext, bool) |Den nya konstruktorn måste vara säker på att alla anslutningar i ObjectContext som skickas till en inkommande trafik dirigeras om till en anslutning som hanteras av elastisk skalning. En detaljerad diskussion om ObjectContexts ligger utanför det här dokumentets omfattning. |
| Kontext (DbConnection, DbCompiledModel, bool) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel, bool) |DbContext (DbConnection, DbCompiledModel, bool); |Anslutningen måste härledas från Shard-mappningen och nyckeln. Det går inte att ange anslutningen som indata (om inte indata redan användes med Shard-mappningen och nyckeln). Modell och boolesk skickas till konstruktorn för Bask Lassen. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Shard schema distribution genom EF-migreringar

Automatisk schema hantering är en bekvämlighet som tillhandahålls av Entity Framework. I samband med program som använder elastiska databas verktyg vill du behålla den här funktionen för att automatiskt etablera schemat till nyskapade Shards när databaserna läggs till i shardade-programmet. Det främsta användnings fallet är att öka kapaciteten på data nivån för shardade-program med EF. Att förlita dig på EF: s funktioner för schema hantering minskar databas administrationens ansträngning med ett shardade-program som bygger på EF.

Schema distribution via EF-migreringar fungerar bäst på **öppna anslutningar**. Detta är i motsats till scenariot för data oberoende routning som förlitar sig på den öppna anslutningen som finns i klient-API: t för Elastic Database. En annan skillnad är konsekvens kravet: det är önskvärt att säkerställa konsekvensen för alla data beroende vägvals anslutningar för att skydda mot samtidiga Shard-mappningar, men det är inte problem med den inledande schema distributionen till en ny databas som ännu inte har registrerats i Shard-mappningen och som ännu inte har allokerats för att innehålla shardletar. Du kan därför förlita dig på vanliga databas anslutningar för det här scenariot, till skillnad från data beroende routning.  

Detta leder till en metod där schema distribution via EF-migreringen är nära kopplad till registreringen av den nya databasen som en Shard i programmets Shard-karta. Detta är beroende av följande krav:

* Databasen har redan skapats.
* Databasen är tom – den innehåller inget användar schema och inga användar data.
* Databasen kan inte nås via Elastic Database client-API: er för data beroende routning.

Med dessa krav på plats kan du skapa en vanlig icke-öppen **SQLConnection** för att starta EF-migreringar för schema distribution. I följande kod exempel visas den här metoden.

```csharp
// Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
// and kick off EF initialization of the database to deploy schema

public void RegisterNewShard(string server, string database, string connStr, int key)
{

    Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database));

    SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr);
    connStrBldr.DataSource = server;
    connStrBldr.InitialCatalog = database;

    // Go into a DbContext to trigger migrations and schema deployment for the new shard.
    // This requires an un-opened connection.
    using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString))
    {
        // Run a query to engage EF migrations
        (from b in db.Blogs
            select b).Count();
    }

    // Register the mapping of the tenant to the shard in the shard map.
    // After this step, data-dependent routing on the shard map can be used

    this.ShardMap.CreatePointMapping(key, shard);
}
```

Det här exemplet visar metoden **RegisterNewShard** som registrerar Shard i Shard-kartan, distribuerar schemat via EF-migreringar och lagrar en mappning av en horisontell partitionering-nyckel till Shard. Den förlitar sig på en konstruktor i **DbContext** -underklassen (**ElasticScaleContext** i exemplet) som tar en SQL-anslutningssträng som indatamängd. Koden för den här konstruktorn är rak framåt, vilket visas i följande exempel:

```csharp
// C'tor to deploy schema and migrations to a new shard
protected internal ElasticScaleContext(string connectionString)
    : base(SetInitializerForConnection(connectionString))
{
}

// Only static methods are allowed in calls into base class c'tors
private static string SetInitializerForConnection(string connectionString)
{
    // You want existence checks so that the schema can get deployed
    Database.SetInitializer<ElasticScaleContext<T>>(
new CreateDatabaseIfNotExists<ElasticScaleContext<T>>());

    return connectionString;
}
```

En kan ha använt den version av konstruktorn som ärvts från Bask Lassen. Men koden måste se till att standard initieraren för EF används vid anslutning. Därför är den korta insikten till den statiska metoden innan du anropar i Bask Lassens konstruktor med anslutnings strängen. Observera att registreringen av Shards ska köras i en annan app-domän eller process för att säkerställa att initierarens inställningar för EF inte står i konflikt.

## <a name="limitations"></a>Begränsningar

De metoder som beskrivs i det här dokumentet medför några begränsningar:

* EF-program som använder **LocalDb** måste först migrera till en vanlig SQL Server databas innan du använder klient biblioteket för Elastic Database. Det går inte att skala ut ett program via horisontell partitionering med elastisk skalning med **LocalDb**. Observera att utvecklingen fortfarande kan använda **LocalDb**.
* Ändringar i programmet som kräver databas schema ändringar måste gå igenom EF-migreringar på alla Shards. Exempel koden för det här dokumentet visar inte hur du gör detta. Överväg att använda Update-Database med en ConnectionString-parameter för att iterera över alla Shards; eller extrahera T-SQL-skriptet för den väntande migreringen med Update-Database med alternativet-skript och Använd T-SQL-skriptet på din Shards.  
* Med en begäran antas det att all bearbetning av databasen finns i en enda Shard som identifieras av horisontell partitionering-nyckeln som anges i begäran. Detta antagande är dock inte alltid sant. Till exempel när det inte går att göra en horisontell partitionering-nyckel tillgänglig. För att åtgärda detta tillhandahåller klient biblioteket **MultiShardQuery** -klassen som implementerar en anslutnings abstraktion för frågor över flera Shards. Inlärning för att använda **MultiShardQuery** i kombination med EF är utanför det här dokumentets omfång

## <a name="conclusion"></a>Slutsats

Genom de steg som beskrivs i det här dokumentet kan EF-program använda klient biblioteks funktionen för elastiska databaser för data beroende routning genom att omväga konstruktörer för de **DbContext** -underklasser som används i programmet EF. Detta begränsar de ändringar som krävs för de platser där **DbContext** -klasser redan finns. Dessutom kan EF-program fortsätta att dra nytta av automatisk schema distribution genom att kombinera stegen som anropar de nödvändiga EF-migreringarna med registreringen av nya Shards och mappningar i Shard-kartan.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png