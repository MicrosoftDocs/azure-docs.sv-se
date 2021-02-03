---
title: 'Självstudie: Använd dynamisk konfiguration av program konfiguration i ASP.NET Core'
titleSuffix: Azure App Configuration
description: I den här självstudien lär du dig att dynamiskt uppdatera konfigurationsdata för ASP.NET Core-appar
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/1/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: e894caddd037738645a37ccb43265a24d56e8afe
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493811"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Självstudie: Använd dynamisk konfiguration i en ASP.NET Core app

ASP.NET Core har ett anslutnings Bart konfigurations system som kan läsa konfigurations data från en mängd olika källor. Den kan hantera ändringar dynamiskt utan att ett program har startats om. ASP.NET Core stöder bindning av konfigurations inställningar till starkt skrivna .NET-klasser. Den infogar dem i koden med hjälp av de olika `IOptions<T>` mönstren. Ett av dessa mönster, i synnerhet `IOptionsSnapshot<T>` , laddar automatiskt om programmets konfiguration när underliggande data ändras. Du kan mata in `IOptionsSnapshot<T>` i kontrollanter i ditt program för att få åtkomst till den senaste konfiguration som lagras i Azure App Configuration.

Du kan också konfigurera appens konfiguration ASP.NET Core klient bibliotek för att uppdatera en uppsättning konfigurations inställningar dynamiskt med hjälp av ett mellanprogram. Konfigurations inställningarna uppdateras med konfigurations lagret varje gång så länge webbappen tar emot begär Anden.

App-konfigurationen cachelagrar automatiskt varje inställning för att undvika för många anrop till konfigurations arkivet. Uppdaterings åtgärden väntar tills det cachelagrade värdet för en inställning upphör att gälla för att uppdatera den inställningen, även när dess värde ändras i konfigurations arkivet. Standardvärdet för förfallo tid för cache är 30 sekunder. Du kan åsidosätta den här förfallo tiden, om det behövs.

Den här självstudien visar hur du kan implementera dynamiska konfigurationsuppdateringar i koden. Den bygger på den webbapp som introducerades i snabbstarterna. Innan du fortsätter måste du först [skapa en ASP.net Core-app med app-konfigurationen](./quickstart-aspnet-core-app.md) .

Du kan använda valfri kod redigerare för att utföra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ som är tillgängligt på Windows-, MacOS-och Linux-plattformarna.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera ditt program för att uppdatera konfigurationen som svar på ändringar i ett konfigurations lager för appar.
> * Mata in den senaste konfigurationen i dina programs kontrollanter.

## <a name="prerequisites"></a>Förutsättningar

Om du vill göra den här själv studie kursen installerar du [.net Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Innan du fortsätter måste du först [skapa en ASP.net Core-app med app-konfigurationen](./quickstart-aspnet-core-app.md) .

## <a name="add-a-sentinel-key"></a>Lägg till en kontroll nyckel

En *kontroll nyckel* är en särskild nyckel som används för att signalera när konfigurationen har ändrats. Din app övervakar kontroll nyckeln för ändringar. När en ändring identifieras uppdaterar du alla konfigurations värden. Den här metoden minskar det totala antalet begär Anden som görs av appen till app-konfigurationen, jämfört med övervakning av alla nycklar för ändringar.

1. I Azure Portal väljer du **Configuration Explorer > skapa > nyckel värde**.
1. För **nyckel** anger du *TestApp: Settings (inställningar: Sentinel*). För **värde** anger du 1. Lämna **etikett** och **innehålls typ** tom.
1. Välj **Använd**.

> [!NOTE]
> Om du inte använder en kontroll nyckel måste du registrera varje nyckel som du vill se manuellt.

## <a name="reload-data-from-app-configuration"></a>Läsa in data på nytt från App Configuration

1. Lägg till en referens till `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet-paketet genom att köra följande kommando:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Öppna *program.cs* och uppdatera `CreateWebHostBuilder` metoden för att lägga till- `config.AddAzureAppConfiguration()` metoden.

   #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```   
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
                });
            })
            .UseStartup<Startup>();
    ```
    ---

    `ConfigureRefresh`Metoden används för att ange inställningarna som används för att uppdatera konfigurations data med appens konfigurations Arkiv när en uppdatering aktive ras. `refreshAll`Parametern till `Register` metoden indikerar att alla konfigurations värden ska uppdateras om kontroll nyckeln ändras.

    `SetCacheExpiration`Metoden åsidosätter också förfallo tiden för standardcachen på 30 sekunder, vilket anger en tid på 5 minuter i stället. Detta minskar antalet begär Anden som görs till app-konfigurationen.

    > [!NOTE]
    > I test syfte kanske du vill minska förfallo tiden för cachen.

    Om du vill utlösa en uppdaterings åtgärd måste du konfigurera en uppdatering mellanprogram för programmet för att uppdatera konfigurations data när någon ändring sker. Du får se hur du gör detta i ett senare steg.

2. Lägg till en *Settings.cs* -fil i katalogen controllers som definierar och implementerar en ny `Settings` klass. Ersätt namn området med namnet på ditt projekt. 

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. Öppna *startup.cs* och Använd `IServiceCollection.Configure<T>` i- `ConfigureServices` metoden för att binda konfigurations data till `Settings` klassen.

    #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
        services.AddAzureAppConfiguration();
    }
    ```
    ---
    > [!Tip]
    > Mer information om alternativ mönster när du läser konfigurations värden finns [i alternativ mönster i ASP.net Core](/aspnet/core/fundamentals/configuration/options?view=aspnetcore-3.1).

4. Uppdatera `Configure` metoden, Lägg till `UseAzureAppConfiguration` mellanprogram för att tillåta att konfigurations inställningarna som registreras för uppdatering uppdateras medan den ASP.net Core webbappen fortsätter att ta emot begär Anden.


    #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```
    ---
    
    Mellanprogram använder den uppdaterings konfiguration som anges i `AddAzureAppConfiguration` -metoden i `Program.cs` för att utlösa en uppdatering för varje begäran som tas emot av ASP.net Core webbappen. För varje begäran utlöses en uppdaterings åtgärd och klient biblioteket kontrollerar om det cachelagrade värdet för den registrerade konfigurations inställningen har upphört att gälla. Om den har gått ut uppdateras den.

    > [!NOTE]
    > För att säkerställa att konfigurationen uppdateras ska du lägga till mellanprogram så tidigt som det är lämpligt för din begäran-pipeline så att den inte kortas av något annat mellan program i ditt program.

## <a name="use-the-latest-configuration-data"></a>Använda senaste konfigurationsdata

1. Öppna *HomeController.cs* i katalogen controllers och Lägg till en referens i `Microsoft.Extensions.Options` paketet.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Uppdatera `HomeController` klassen som ska tas emot `Settings` via beroende insprutning och använd dess värden.

 #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
```
#### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
```
#### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
```
---



3. Öppna *index. cshtml* i vyerna > Home Directory och ersätt innehållet med följande skript:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i kommando gränssnittet:

    ```console
        dotnet build
    ```

1. När skapandet har slutförts kör du följande kommando för att köra webbappen lokalt:

    ```console
        dotnet run
    ```

1. Öppna ett webbläsarfönster och gå till den URL som visas i `dotnet run` utdata.

    ![Starta snabb starts app lokalt](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **alla resurser** och välj den instans av app Configuration Store som du skapade i snabb starten.

1. Välj **Configuration Explorer** och uppdatera värdena för följande nycklar:

    | Tangent | Värde |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Data från Azure App Configuration – nu med live-uppdateringar! |
    | TestApp: inställningar: Sentinel | 2 |

1. Uppdatera webbläsarsidan för att visa de nya konfigurationsinställningarna. Du kan behöva uppdatera mer än en gång för att ändringarna ska reflekteras, eller ändra den automatiska uppdaterings hastigheten till mindre än 5 minuter. 

    ![Starta uppdaterad snabb starts app lokalt](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat ASP.NET Core-webbappen för dynamisk uppdatering av konfigurations inställningar från App-konfigurationen. Fortsätt till nästa självstudie om du vill lära dig hur du använder en Azure-hanterad identitet för att effektivisera åtkomsten till app-konfigurationen.

> [!div class="nextstepaction"]
> [Hanterad identitets integrering](./howto-integrate-azure-managed-service-identity.md)
