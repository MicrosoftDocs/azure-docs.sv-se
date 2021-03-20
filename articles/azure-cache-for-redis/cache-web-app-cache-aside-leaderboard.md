---
title: 'Självstudie: skapa en webbapp (cache-undan) – Azure cache för Redis'
description: Lär dig hur du skapar en webbapp med Azure cache för Redis som använder cache-undan-mönstret.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc
ms.date: 03/30/2018
ms.openlocfilehash: 90e60044e227ea1a18ea032d302b29abda1ea2e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92536852"
---
# <a name="tutorial-create-a-cache-aside-leaderboard-on-aspnet"></a>Självstudiekurs: Skapa en cache-aside-resultattavla för ASP.NET

I den här självstudien uppdaterar du ASP.NET-webbappen *ContosoTeamStats* som har skapats i [ASP.NET-snabbstarten för Azure Cache for Redis](cache-web-app-howto.md), så att den inkluderar en resultattavla som använder [cache-aside-mönstret](/azure/architecture/patterns/cache-aside) med Azure Cache for Redis. Exempelprogrammet som visar en lista med teamstatistik från en databas och olika sätt att använda Azure Cache for Redis för att lagra och hämta data från cacheminnet. När du slutför självstudien har du en webbapp som läser och skriver till en databas, samt som är optimerad med Azure Cache for Redis och värdbaserad i Azure.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbättra dataflödet och minskar databasbelastningen genom att lagra och hämta data med Azure Cache for Redis.
> * Använd en Redis-sorterad uppsättning till att hämta de översta fem teamen.
> * Etablera Azure-resurserna för programmet med en Resource Manager-mall.
> * Publicera programmet till Azure med Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här självstudien behöver du följande:

* Självstudien fortsätter där du slutade i [ASP.NET-snabbstarten för Azure Cache for Redis](cache-web-app-howto.md). Följ snabbstarten först, om du inte redan har gjort det.
* Installera [Visual Studio 2019](https://www.visualstudio.com/downloads/) med följande arbets belastningar:
    * ASP.NET och webbutveckling
    * Azure Development
    * .NET Desktop Development med SQL Server Express LocalDB eller [SQL Server 2017 Express Edition](https://www.microsoft.com/sql-server/sql-server-editions-express).

## <a name="add-a-leaderboard-to-the-project"></a>Lägga till en resultattavla i projektet

I det här avsnittet av kursen konfigurerar du *ContosoTeamStats*-projektet med en resultattavla som rapporterar statistik för vinster, förluster och oavgjort för en lista med fiktiva team.

### <a name="add-the-entity-framework-to-the-project"></a>Lägga till Entity Framework i projektet

1. Öppna i Visual Studio den *ContosoTeamStats*-lösning som du skapade i [ASP.NET-snabbstarten för Azure Cache for Redis](cache-web-app-howto.md).
2. Klicka på **Verktyg > NuGet Package Manager > Package Manager Console**.
3. Kör följande kommando via fönstret för **Package Manager-konsolen** för att installera EntityFramework:

    ```powershell
    Install-Package EntityFramework
    ```

Mer information om det här paketet finns i NuGet-paketet [EntityFramework](https://www.nuget.org/packages/EntityFramework/).

### <a name="add-the-team-model"></a>Lägga till Team-modellen

1. Högerklicka på **Modeller** i **Solution Explorer** och välj **Lägg till**, **Klass**.

1. Ange `Team` som klassnamn och klicka på **Lägg till**.

    ![Lägg till modellklass](./media/cache-web-app-cache-aside-leaderboard/cache-model-add-class-dialog.png)

1. Ersätt `using`-satserna överst i filen *Team.cs* med följande `using`-satser:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```

1. Ersätt definitionen av `Team`-klassen med följande kodfragment som innehåller en uppdaterad `Team`-klassdefinition samt vissa andra Entity Framework-hjälpklasser. Den här kursen använder kod först-metoden med Entity Framework. Med den här metoden kan Entity Framework skapa databasen utifrån din kod. Mer information om den första kodmetoden för Entity Framework som används i den här självstudien finns i [Kod först till en ny databas](/ef/ef6/modeling/code-first/workflows/new-database).

    ```csharp
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }

        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();

            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }

    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }

        public DbSet<Team> Teams { get; set; }
    }

    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };

            Team.PlayGames(teams);

            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }

    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```

1. I **Solution Explorer** dubbelklickar du på **Web.config** för att öppna det.

    ![Web.config](./media/cache-web-app-cache-aside-leaderboard/cache-web-config.png)

1. Lägg till följande `connectionStrings`-avsnitt inuti avsnittet `configuration`. Namnet på anslutningssträngen måste matcha namnet på klassen för Entity Framework-databasens kontext som är `TeamContext`.

    Den här anslutnings strängen förutsätter att du uppfyller [kraven](#prerequisites) och installerat SQL Server Express LocalDB, som är en del av arbets belastningen *.net Desktop Development* installerat med Visual Studio 2019.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    I följande exempel visas det nya `connectionStrings`-avsnittet efter `configSections` inuti `configuration`-avsnittet:

    ```xml
    <configuration>
        <configSections>
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
        </configSections>
        <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
        </connectionStrings>
        ...
    ```

### <a name="add-the-teamscontroller-and-views"></a>Lägga till TeamsController och vyer

1. Skapa projektet i Visual Studio. 

1. I **Solution Explorer** högerklickar du på mappen **Kontrollanter**. Klicka sedan på **Lägg till** och på **Kontrollant**.

1. Välj **MVC 5-kontrollant med vyer, med hjälp av Entity Framework** och klicka på **Lägg till**. Om du får ett fel när du klickar på **Lägg till**, kontrollerar du att du har skapat projektet först.

    ![Lägga till en kontrollantklass](./media/cache-web-app-cache-aside-leaderboard/cache-add-controller-class.png)

1. Välj **Team (ContosoTeamStats.Models)** från listrutan **Modellklass**. Välj **TeamContext (ContosoTeamStats.Models)** från listrutan **Datakontextklass**. Skriv `TeamsController` i textrutan **Kontrollant** (om den inte automatiskt har fyllts i). Klicka på **Lägg till** för att skapa kontrollantklassen och lägga till standardvyerna.

    ![Konfigurera kontrollanten](./media/cache-web-app-cache-aside-leaderboard/cache-configure-controller.png)

1. I **Solution Explorer** expanderar du **Global.asax** och dubbelklickar på **Global.asax.cs** för att öppna den.

    ![Global.asax.cs](./media/cache-web-app-cache-aside-leaderboard/cache-global-asax.png)

1. Lägg till följande två `using`-satser längst upp i filen under de andra `using`-satserna:

    ```csharp
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```

1. Lägg till följande kodrad i slutet av metoden `Application_Start`:

    ```csharp
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```

1. I **Solution Explorer** expanderar du `App_Start` och dubbelklickar på `RouteConfig.cs`.

    ![RouteConfig.cs](./media/cache-web-app-cache-aside-leaderboard/cache-RouteConfig-cs.png)

1. I `RegisterRoutes`-metoden ersätter du `controller = "Home"` i `Default`-routningen med `controller = "Teams"` som visas i följande exempel:

    ```csharp
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```

### <a name="configure-the-layout-view"></a>Konfigurera vyn Layout

1. I **Solution Explorer** expanderar du mappen **Vyer** och sedan mappen **Delad**. Dubbelklicka på **_Layout.cshtml**. 

    ![_Layout.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml.png)

1. Ändra innehållet i `title`-elementet och ersätt `My ASP.NET Application` med `Contoso Team Stats` enligt följande exempel:

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```

1. I avsnittet `body` lägger du till följande nya `Html.ActionLink`-uttryck för *Contoso Team Stats* under länken för *Azure Cache for Redis-test*.

    ```csharp
    @Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
    ```

    ![Kodändringar](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml-code.png)

1. Tryck på **Ctrl+F5** för att skapa och köra programmet. Den här versionen av programmet läser resultaten direkt från databasen. Observera att åtgärderna **Skapa nytt**, **Redigera**, **Information** och **Ta bort** har lagts till automatiskt till programmet av **MVC 5-kontrollanten med vyer, med hjälp av Entity Framework**-autogenerering. I nästa avsnitt i självstudien lägger du till Azure Cache for Redis för att optimera dataåtkomst och ange ytterligare funktioner i programmet.

    ![Startprogram](./media/cache-web-app-cache-aside-leaderboard/cache-starter-application.png)

## <a name="configure-the-app-for-azure-cache-for-redis"></a>Konfigurera appen för Azure Cache for Redis

I det här avsnittet av självstudien konfigurerar du exempelprogrammet till att lagra och hämta Contoso-teamets statistik från en Azure Cache for Redis-instans med hjälp av cacheklienten [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

### <a name="add-a-cache-connection-to-the-teams-controller"></a>Lägga till en cacheanslutning till Teams Controller

Du har redan installerat *StackExchange.Redis*-klientbibliotekspaketet i snabbstarten. Du har också redan konfigurerat *CacheConnection*-appinställningen som ska användas lokalt och med den publicerade apptjänsten. Använd samma klientbibliotek och *CacheConnection*-information i *TeamsController*.

1. I **Solution Explorer** expanderar du mappen **Kontrollanter** och sedan dubbelklickar du på filen **TeamController.cs** för att öppna den.

    ![Teamkontrollant](./media/cache-web-app-cache-aside-leaderboard/cache-teamscontroller.png)

1. Lägg till följande två `using`-satser i **TeamsController.cs**:

    ```csharp
    using System.Configuration;
    using StackExchange.Redis;
    ```

1. Lägg till följande två egenskaper i klassen `TeamsController`:

    ```csharp
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

### <a name="update-the-teamscontroller-to-read-from-the-cache-or-the-database"></a>Uppdatera TeamsController för att läsa från cachen eller databasen

I det här exemplet kan teamstatistik hämtas från databasen eller från cachen. Teamstatistik lagras i cacheminnet som en serialiserad `List<Team>` och även som en sorterad uppsättning med Redis-datatyper. När du hämtar objekt från en sorterad uppsättning hämtar du vissa, alla eller frågar efter vissa objekt. I det här exemplet får du fråga i den sorterade uppsättningen efter de översta 5 team som rangordnas efter antalet WINS.

Du behöver inte spara teamstatistiken i olika format i cacheminnet för att kunna använda Azure Cache for Redis. Den här självstudien använder flera olika format för att visa några av de olika sätt och olika datatyper som du kan använda för att cachelagra data.

1. Lägg till följande två `using`-satser längst upp i filen `TeamsController.cs` tillsammans med de andra `using`-satserna:

    ```csharp
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

1. Ersätt den befintliga implementeringen av metoden `public ActionResult Index()` med följande implementering:

    ```csharp
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```

1. Lägg till följande tre metoder till `TeamsController`-klassen för att implementera åtgärdstyperna `playGames`, `clearCache` och `rebuildDB` från switch-satsen som lades till i föregående kodfragment.

    `PlayGames`-metoden uppdaterar teamstatistiken genom att simulera en spelsäsong, spara resultaten i databasen och rensa inaktuella data från cachen.

    ```csharp
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    `RebuildDB`-metoden återinitierar databasen med en standarduppsättning av team, genererar statistik för dem och rensar inaktuella data från cachen.

    ```csharp
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    `ClearCachedTeams`-metoden tar bort cachelagrad teamstatistik från cachen.

    ```csharp
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    }
    ```

1. Lägg till följande fyra metoder till `TeamsController`-klassen för att implementera olika sätt att hämta teamstatistik från cacheminnet och databasen. Var och en av dessa metoder returnerar en `List<Team>` som visas i vyn.

    `GetFromDB`-metoden läser teamstatistiken från databasen.
    ```csharp
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t;

        return results.ToList<Team>();
    }
    ```

    `GetFromList`-metoden läser teamstatistiken från cachen som en serialiserad `List<Team>`. Om statistiken inte finns i cachen sker en cachemiss. För en cachemiss läses teamstatistiken från databasen och lagras sedan i cacheminnet för nästa begäran. I det här exemplet används JSON.NET-serialisering för att serialisera .NET-objekt till och från cacheminnet. Mer information finns i [Så här arbetar du med .NET-objekt i Azure Cache for Redis](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

    ```csharp
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    `GetFromSortedSet`-metoden läser teamstatistiken från en cachelagrad sorterad uppsättning. Om det finns en cachemiss, läses teamstatistiken från databasen och lagras i cacheminnet som en sorterad uppsättning.

    ```csharp
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    `GetFromSortedSetTop5`-metoden läser de översta fem teamen från den cachelagrade sorterade uppsättningen. Den startar genom att kontrollera cacheminnet efter förekomsten av `teamsSortedSet`-nyckeln. Om den här nyckeln inte finns, anropas `GetFromSortedSet`-metoden för att läsa teamstatistiken och lagra den i cacheminnet. Därefter efterfrågas den cachelagrade sorterade uppsättningens översta fem team som returneras.

    ```csharp
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Uppdatera metoderna Skapa, Redigera och Ta bort till att arbeta med cacheminnet

Den autogenererade kod som skapats som en del av det här exemplet innehåller metoder för att lägga till, redigera och ta bort team. Varje gång ett team läggs till, redigeras eller tas bort, blir data i cacheminnet inaktuell. I det här avsnittet ändrar du dessa tre metoder för att ta bort de cachelagrade teamen så att cachen uppdateras.

1. Bläddra till `Create(Team team)`-metoden i `TeamsController`-klassen. Lägg till ett anrop till metoden `ClearCachedTeams` enligt följande exempel:

    ```csharp
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```

2. Bläddra till `Edit(Team team)`-metoden i `TeamsController`-klassen. Lägg till ett anrop till metoden `ClearCachedTeams` enligt följande exempel:

    ```csharp
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```

3. Bläddra till `DeleteConfirmed(int id)`-metoden i `TeamsController`-klassen. Lägg till ett anrop till metoden `ClearCachedTeams` enligt följande exempel:

    ```csharp
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```

### <a name="add-caching-methods-to-the-teams-index-view"></a>Lägga till cachelagringsmetoder i teamindexvyn

1. I **Solution Explorer** expanderar du mappen **Vyer** och sedan mappen **Team**. Dubbelklicka på **Index.cshtml**.

    ![Index.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-views-teams-index-cshtml.png)

1. Leta efter följande paragrafelement i den övre delen av filen:

    ![Åtgärdstabell](./media/cache-web-app-cache-aside-leaderboard/cache-teams-index-table.png)

    Den här länken skapar ett nytt team. Ersätt paragrafelementet med följande tabell. Den här tabellen innehåller åtgärdslänkar för att skapa ett nytt team, spela en ny spelsäsong, rensa cacheminnet, hämta teamen från cachen i olika format, hämta teamen från databasen och återskapa databasen med ny exempeldata.

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>
    </table>
    ```

1. Bläddra till slutet av filen **index. cshtml** och Lägg till följande- `tr` element så att det är den sista raden i den sista tabellen i filen:

    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
    Den här raden visar värdet för `ViewBag.Msg` som innehåller en statusrapport om den aktuella åtgärden. `ViewBag.Msg` ställs in när du klickar på någon av åtgärdslänkarna i föregående steg.

    ![Statusmeddelande](./media/cache-web-app-cache-aside-leaderboard/cache-status-message.png)

1. Tryck på **F6** för att skapa projektet.

## <a name="run-the-app-locally"></a>Köra appen lokalt

Kör programmet lokalt på din dator för att verifiera funktionerna som har lagts till för att stödja teamen.

I det här testet körs både programmet och databasen lokalt. Men Azure Cache for Redis fjärrhanteras i Azure. Därför kommer cachen troligen underprestera något. För bästa prestanda bör klientprogrammet och Azure Cache for Redis-instansen finnas på samma plats. I nästa avsnitt distribuerar du alla resurser till Azure för att se den förbättrade prestandan från att använda en cache.

Köra appen lokalt:

1. Tryck på **CTRL + F5** för att köra programmet.

    ![App som körs lokalt](./media/cache-web-app-cache-aside-leaderboard/cache-local-application.png)

1. Testa de nya metoderna som har lagts till i vyn. Eftersom cachen är fjärrbaserad i dessa tester har databasen något högre prestanda än cachen.

## <a name="publish-and-run-in-azure"></a>Publicera och köra i Azure

### <a name="provision-a-database-for-the-app"></a>Etablera en databas för appen

I det här avsnittet ska du etablera en ny databas i SQL Database för appen att använda medan den är värd i Azure.

1. Klicka på [Skapa en resurs](https://portal.azure.com/) längst upp till vänster i **Azure Portal**.

1. På sidan **Nytt** klickar du på **Databaser** > **SQL Database**.

1. Använd följande inställningar för den nya SQL-databasen:

   | Inställning       | Föreslaget värde | Beskrivning |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Databasnamn** | *ContosoTeamsDatabase* | För giltiga databas namn, se [databas identifierare](/sql/relational-databases/databases/database-identifiers). |
   | **Prenumeration** | *Din prenumeration*  | Välj samma prenumeration som du använde för att skapa cachen och vara värd för apptjänsten. |
   | **Resursgrupp**  | *TestResourceGroup* | Klicka på **Använda befintlig** och använd samma resursgrupp där du har placerat din cache och apptjänst. |
   | **Välj källa** | **Tom databas** | Börja med en tom databas. |

1. Under **Server** klickar du på **Konfigurera nödvändiga inställningar** > **Skapa en ny server** och ange följande information och klicka sedan på knappen **Välj**:

   | Inställning       | Föreslaget värde | Beskrivning |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Servernamn** | Valfritt globalt unikt namn | Giltiga servernamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming). |
   | **Inloggning för serveradministratör** | Valfritt giltigt namn | För giltiga inloggnings namn, se [databas identifierare](/sql/relational-databases/databases/database-identifiers). |
   | **Lösenord** | Valfritt giltigt lösenord | Lösenordet måste innehålla minst 8 tecken och måste innehålla tecken från tre av följande kategorier: versaler, gemener, siffror och icke-alfanumeriska tecken. |
   | **Plats** | *East US* | Välj samma region där du skapade cachen och apptjänsten. |

1. Klicka på **Fäst på instrumentpanelen** och sedan på **Skapa** för att skapa den nya databasen och servern.

1. När den nya databasen har skapats klickar du på **Visa databasanslutningssträngar** och kopierar **ADO.NET**-anslutningssträngen.

    ![Visa anslutningssträngar](./media/cache-web-app-cache-aside-leaderboard/cache-show-connection-strings.png)

1. Navigera till apptjänsten i Azure Portal och klicka på **Programinställningar** och sedan på **Lägg till ny anslutningssträng** under avsnittet Anslutningssträngar.

1. Lägg till en ny anslutningssträng med namnet *TeamContext* så att det matchar Entity Framework-databaskontextklassen. Klistra in anslutningssträngen för den nya databasen som värde. Se till att ersätta följande platshållare i anslutningssträngen och klicka på **Spara**:

    | Platshållare | Föreslaget värde |
    | --- | --- |
    | *{ditt_användarnamn}* | Använd **Server Administratörs inloggning** för den server som du nyss skapade. |
    | *{ditt_lösenord}* | Använd lösen ordet för den server som du nyss skapade. |

    Genom att lägga till användarnamnet och lösenordet som en programinställning inkluderas inte ditt användarnamn och lösenord i koden. Den här metoden skyddar dessa autentiseringsuppgifter.

### <a name="publish-the-application-updates-to-azure"></a>Publicera programuppdateringar i Azure

I det här steget i självstudien publicerar du programuppdateringarna i Azure för att köra det i molnet.

1. Högerklicka på projektet **ContosoTeamStats** i Visual Studio och välj **Publicera**.

    ![Publicera](./media/cache-web-app-cache-aside-leaderboard/cache-publish-app.png)

2. Klicka på **Publicera** för att använda samma publiceringsprofil som du skapade i snabbstarten.

3. När publiceringen är klar startar Visual Studio appen i standardwebbläsaren.

    ![Cachen har lagts till](./media/cache-web-app-cache-aside-leaderboard/cache-added-to-application.png)

    I följande tabell beskrivs varje åtgärdslänk från exempelprogrammet:

    | Åtgärd | Beskrivning |
    | --- | --- |
    | Skapa Ny |Skapa ett nytt team. |
    | Spela säsong |Spela en spelsäsong, uppdatera teamstatistiken och avmarkera inaktuell teamdata från cachen. |
    | Rensa cacheminne |Rensa teamstatistik från cachen. |
    | Lista från cache |Hämta teamstatistik från cachen. Om det finns en cachemiss läser du in statistiken från databasen och sparar den till cachen till nästa gång. |
    | Sorterad uppsättning från cachen |Hämtar teamstatistiken från cachen med en sorterad uppsättning. Om det finns en cachemiss läser du in statistiken från databasen och sparar den till cachen med en sorterad uppsättning. |
    | Översta 5 teamen från cachen |Hämtar de översta fem teamen från cachen med en sorterad uppsättning. Om det finns en cachemiss läser du in statistiken från databasen och sparar den till cachen med en sorterad uppsättning. |
    | Läsa in från databas |Hämtar teamstatistiken från databasen. |
    | Återskapa databas |Återskapa databasen och öppna den igen med exempelteamdata. |
    | Redigera/Information/Ta bort |Redigera ett team, visa information om ett team, ta bort ett team. |

Klicka på några åtgärder och experimentera med att hämta data från olika källor. Observera skillnaderna mellan den tid det tar för att slutföra de olika sätten att hämta data från databasen och cachen.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med självstudiens exempelprogram kan du ta bort Azure-resurserna som användes för att spara kostnad och resurser. Alla dina resurser bör finnas i samma resursgrupp. Du kan ta bort dem tillsammans i en enda åtgärd genom att ta bort resursgruppen. Anvisningarna för det här avsnittet används en resursgrupp med namnet *TestResources*.

> [!IMPORTANT]
> Det går inte att ångra borttagningen av en resursgrupp och att resursgruppen och alla resurser i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat resurserna som värd för det här exemplet i en befintlig resursgrupp som innehåller resurser som du vill behålla, kan du ta bort varje resurs separat från deras respektive blad.
>

1. Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Resursgrupper**.
2. Skriv namnet på din resursgrupp i textrutan **Filtrera objekt...**.
3. Klicka på **...** till höger om resursgruppen och klicka på **Ta bort resursgrupp**.

    ![Ta bort](./media/cache-web-app-cache-aside-leaderboard/cache-delete-resource-group.png)

4. Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på din resursgrupp för att bekräfta och klicka på **Ta bort**.

    Efter en liten stund tas resursgruppen och resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Så här skalar du Azure Cache for Redis](./cache-how-to-scale.md)