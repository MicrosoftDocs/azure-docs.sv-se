---
title: Utforska .NET-spårningsloggar med ILogger – Azure Application Insights
description: Exempel på hur du använder Azure Application Insights ILogger-providern med ASP.NET Core- och Console-program.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: a4781e3f0208d355c06df506bab3b0a3dd457078
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568598"
---
# <a name="applicationinsightsloggerprovider-for-microsoftextensionlogging"></a>ApplicationInsightsLoggerProvider för Microsoft.Extension.Logging

Den här artikeln visar hur du använder `ApplicationInsightsLoggerProvider` för att samla in loggar i konsolen ASP.NET `ILogger` Core-program.
Mer loggning finns i [Logga in ASP.NET Core](/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Core-program

`ApplicationInsightsLoggerProvider`är aktiverat som standard för ASP.NET [Core-program](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring) när ApplicationInsights har konfigurerats med [kod](./asp-net-core.md) eller kod mindre.

Endast *varningsloggar* `ILogger` och loggar ovan [(från alla](/aspnet/core/fundamentals/logging/#log-category)kategorier) skickas till Application Insights som standard. Men du kan [anpassa det här beteendet](./asp-net-core.md#how-do-i-customize-ilogger-logs-collection). Ytterligare steg krävs för att samla in ILogger-loggar **från Program.cs** eller **Startup.cs**. (Se [Samla in ILogger-loggar från Startup.cs och Program.cs i ASP.NET Core-program](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Om du bara vill använda `ApplicationInsightsLoggerProvider` utan någon annan Application Insights övervakning använder du följande steg.

1. Installera NuGet-paketet:

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
    </ItemGroup>
   ```

1. Ändra `Program.cs` på det sätt som visas här:

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("put-actual-ikey-here");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

Koden i steg 2 konfigurerar `ApplicationInsightsLoggerProvider` . Följande kod visar ett exempel på en Controller-klass som använder för `ILogger` att skicka loggar. Loggarna avbildas av Application Insights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Samla in ILogger-loggar från Startup.cs och Program.cs i ASP.NET Core-appar

> [!NOTE]
> I ASP.NET Core 3.0 och senare går det inte längre att `ILogger` mata in i Startup.cs och Program.cs. Mer https://github.com/aspnet/Announcements/issues/353 information finns i .

`ApplicationInsightsLoggerProvider` kan samla in loggar tidigt i programstarten. Även om ApplicationInsightsLoggerProvider aktiveras automatiskt i Application Insights (från och med version 2.7.1), har den ingen instrumenteringsnyckel konfigurerad förrän senare i pipelinen. Därför kommer endast loggar från **Kontrollant**/andra klasser att avbildas. Om du vill avbilda varje logg som börjar med **Program.cs** och **Startup.cs** måste du uttryckligen aktivera en instrumenteringsnyckel för ApplicationInsightsLoggerProvider. Dessutom är *TelemetryConfiguration* inte helt konfigurerat när du loggar från **Program.cs** eller **Startup.cs.** Så dessa loggar har en minsta konfiguration som använder [InMemoryChannel,](./telemetry-channels.md)ingen [sampling](./sampling.md)och inga [standard-telemetriinitierare eller processorer](./api-filtering-sampling.md).

I följande exempel visas den här funktionen med **Program.cs** och **Startup.cs**.

#### <a name="example-programcs"></a>Exempelprogram.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Exempel på Startup.cs

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrera från den gamla ApplicationInsightsLoggerProvider

Microsoft.ApplicationInsights.AspNet SDK-versioner före 2.7.1 hade stöd för en loggningsprovider som nu är föråldrad. Den här providern aktiverades **via tilläggsmetoden AddApplicationInsights()** i ILoggerFactory. Vi rekommenderar att du migrerar till den nya providern, vilket omfattar två steg:

1. Ta bort *anropet ILoggerFactory.AddApplicationInsights()* från **Startup.Configure()-metoden** för att undvika dubbel loggning.
2. Tillämpa eventuella filtreringsregler i koden igen eftersom de inte kommer att respekteras av den nya providern. Överlagringar *av ILoggerFactory.AddApplicationInsights() tog* minimala LogLevel- eller filterfunktioner. Med den nya providern är filtrering en del av själva loggningsramverket. Det görs inte av Application Insights providern. Därför bör alla filter som tillhandahålls via *ILoggerFactory.AddApplicationInsights()-överlagringar* tas bort. Filtreringsregler bör tillhandahållas genom att följa anvisningarna [för kontrollloggningsnivå.](#control-logging-level) Om du *använderappsettings.jspå* för att filtrera loggning fortsätter det att fungera med den nya providern eftersom båda använder samma provideralias, *ApplicationInsights*.

Du kan fortfarande använda den gamla providern. (Den tas bara bort i en större versionsändring till 3. *xx*.) Men vi rekommenderar att du migrerar till den nya providern av följande skäl:

- Den tidigare providern saknar stöd för [loggomfattningar](/aspnet/core/fundamentals/logging#log-scopes). I den nya providern läggs egenskaper från omfånget automatiskt till som anpassade egenskaper i den insamlade telemetrin.
- Loggar kan nu avbildas mycket tidigare i programmets startpipeline. Loggar från **klasserna Program** **och** Start kan nu hämtas.
- Med den nya providern görs filtreringen på själva ramverksnivån. Du kan filtrera loggar till Application Insights-providern på samma sätt som för andra leverantörer, inklusive inbyggda providers som Console, Debug och så vidare. Du kan också använda samma filter för flera providers.
- I ASP.NET Core (2.0 och senare) är [](https://github.com/aspnet/Announcements/issues/255) det rekommenderade sättet att aktivera loggningsproviders att använda tilläggsmetoder på ILoggingBuilder **i själva Program.cs.**

> [!Note]
> Den nya providern är tillgänglig för program som har NETSTANDARD2.0 som mål eller senare. Från [och med Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.14.0 och senare finns även en ny provider tillgänglig för program som riktar .NET Framework NET461 eller senare. Om ditt program riktar in sig på äldre .NET Core-versioner, till exempel .NET Core 1.1 eller om det riktar sig mot .NET Framework mindre än NET46, fortsätter du att använda den gamla providern.

## <a name="console-application"></a>Konsolprogram

> [!NOTE]
> Det finns en ny Application Insights SDK som heter [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) som kan användas för att aktivera Application Insights (ILogger och annan Application Insights-telemetri) för alla konsolprogram. Vi rekommenderar att du använder det här paketet och tillhörande instruktioner [här.](./worker-service.md)

Om du bara vill använda ApplicationInsightsLoggerProvider utan någon annan Application Insights övervakning använder du följande steg.

Installerade paket:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        logger.LogInformation("Logger is working");

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

I det här exemplet används det fristående paketet `Microsoft.Extensions.Logging.ApplicationInsights` . Som standard använder den här konfigurationen telemetryConfiguration som "minimalt" för att skicka data till Application Insights. Minimalt innebär att InMemoryChannel är den kanal som används. Det finns ingen sampling och inga standardtelemetriinitialiserare. Det här beteendet kan åsidosättas för ett konsolprogram, vilket visas i följande exempel.

Installera det här extra paketet:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

Följande avsnitt visar hur du åsidosätter standardmetoden TelemetryConfiguration med **hjälpservices.Configure \<TelemetryConfiguration> ().** Det här exemplet uppsättningar `ServerTelemetryChannel` och sampling. Den lägger till en anpassad ITelemetryInitializer i TelemetryConfiguration.

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );
    
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Kontrollloggningsnivå

`ILogger` har en inbyggd mekanism för att tillämpa [loggfiltrering](/aspnet/core/fundamentals/logging#log-filtering). På så sätt kan du styra loggarna som skickas till varje registrerad provider, inklusive Application Insights providern. Filtreringen kan göras antingen i konfigurationen (vanligtvis genom att använda *enappsettings.jsfil)* eller i kod.

I följande exempel visas hur du tillämpar filterregler på `ApplicationInsightsLoggerProvider` .

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Skapa filterregler i konfigurationen med appsettings.jspå

För ApplicationInsightsLoggerProvider är provideraliaset `ApplicationInsights` . I följande avsnitt av *appsettings.jspå* loggningsproviders vanligtvis att logga på nivå *Varning* och högre. Den åsidosätter sedan `ApplicationInsightsLoggerProvider` för att logga kategorier som börjar med "Microsoft" på nivå *Fel* och högre.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Skapa filterregler i kod

Följande kodfragment konfigurerar  loggar för Varning och  högre från alla kategorier och för Fel och högre från kategorier som börjar med "Microsoft" som ska skickas till `ApplicationInsightsLoggerProvider` . Den här konfigurationen är samma som i föregående avsnitt i *appsettings.jspå*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="logging-scopes"></a>Loggningsomfång

`ApplicationInsightsLoggingProvider` stöder [loggomfång och](/aspnet/core/fundamentals/logging#log-scopes) omfång är aktiverade som standard.

Om omfånget är av typen läggs varje nyckel/värde-par i samlingen till `IReadOnlyCollection<KeyValuePair<string,object>>` i Application Insights-telemetrin som anpassade egenskaper. I exemplet nedan avbildas loggar som och har `TraceTelemetry` ("MyKey", "MyValue") i egenskaperna.

```csharp
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
    {
        _logger.LogError("An example of an Error level message");
    }
```

Om någon annan typ används som Omfång lagras de under egenskapen "Omfång" i Application Insights-telemetri. I exemplet nedan har en `TraceTelemetry` egenskap med namnet "Omfång" som innehåller omfånget.

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Vilka är de gamla och nya versionerna av ApplicationInsightsLoggerProvider?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) innehöll en inbyggd ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), som aktiverades via **tilläggsmetoderna ILoggerFactory.** Den här providern är markerad som föråldrad från version 2.7.1. Den tas bort helt i nästa större versionsändring. Själva [paketet Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) är inte inaktuellt. Det krävs för att aktivera övervakning av begäranden, beroenden och så vidare.

Det föreslagna alternativet är det nya fristående paketet [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), som innehåller en förbättrad ApplicationInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) och tilläggsmetoder på ILoggerBuilder för att aktivera det.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.1 är beroende av det nya paketet och aktiverar ILogger capture automatiskt.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Varför visas vissa ILogger-loggar två gånger i Application Insights?

Duplicering kan inträffa om du har den äldre (nu föråldrade) versionen av ApplicationInsightsLoggerProvider aktiverad genom att anropa `AddApplicationInsights` på `ILoggerFactory` . Kontrollera om metoden **Konfigurera** har följande och ta bort den:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Om du får dubbel loggning när du felsöker från Visual Studio anger du till falskt i koden som `EnableDebugLogger` aktiverar Application Insights, enligt följande.  Den här dupliceringen och korrigeringen är bara relevant när du felsöker programmet.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Jag har uppdaterat [till Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.1 och loggar från ILogger hämtas automatiskt. Hur gör jag för att inaktivera den här funktionen helt?

Se avsnittet [Kontrollloggningsnivå](#control-logging-level) för att se hur du filtrerar loggar i allmänhet. Om du vill inaktivera ApplicationInsightsLoggerProvider använder du `LogLevel.None` :

**I kod:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**I konfiguration:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Varför har vissa ILogger-loggar inte samma egenskaper som andra?

Application Insights samlar in och skickar ILogger-loggar med samma TelemetryConfiguration som används för alla andra telemetri. Men det finns ett undantag. Som standard är TelemetryConfiguration inte helt konfigurerat när du loggar från **Program.cs** eller **Startup.cs**. Loggar från dessa platser har inte standardkonfigurationen, så de kör inte alla TelemetryInitializers och TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Jag använder det fristående paketet Microsoft.Extensions.Logging.ApplicationInsights och vill logga ytterligare anpassad telemetri manuellt. Hur gör jag det?

När du använder det fristående paketet matas inte in i DI-containern, så du måste skapa en ny instans av och använda samma konfiguration som loggningsprovidern använder, som följande `TelemetryClient` `TelemetryClient` kod visar. Detta säkerställer att samma konfiguration används för all anpassad telemetri samt telemetri från ILogger.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Om du använder Microsoft.ApplicationInsights.AspNetCore-paketet för att aktivera Application Insights ändrar du den här koden för att `TelemetryClient` komma direkt i konstruktorn. Ett exempel finns i dessa [vanliga frågor och svar.](./asp-net-core.md#frequently-asked-questions)


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Vilken Application Insights typ av telemetri produceras från ILogger-loggar? Eller var kan jag se ILogger-loggar i Application Insights?

ApplicationInsightsLoggerProvider samlar in ILogger-loggar och skapar TraceTelemetry från dem. Om ett Undantagsobjekt skickas till `Log` metoden på `ILogger` skapas *ExceptionTelemetry* i stället för TraceTelemetry. Dessa telemetriobjekt finns på samma platser som andra TraceTelemetry eller ExceptionTelemetry för Application Insights, inklusive portal, analys eller Visual Studio lokal felsökare.

Om du föredrar att alltid skicka TraceTelemetry använder du det här kodfragmentet: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Jag har inte SDK installerat och jag använder Tillägget Azure Web Apps för att aktivera Application Insights för mina ASP.NET Core-program. Hur gör jag för att du använda den nya providern? 

Tillägget Application Insights i Azure Web Apps använder den nya providern. Du kan ändra filtreringsreglerna i *appsettings.jspå* filen för ditt program.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Jag använder det fristående paketet Microsoft.Extensions.Logging.ApplicationInsights och aktiverar Application Insights providern genom att anropa **Builder. AddApplicationInsights("ikey")**. Finns det något alternativ för att hämta en instrumentationsnyckel från konfigurationen?


Ändra Program.cs och appsettings.jspå enligt följande:

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   Relevant avsnitt från `appsettings.json` :

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Den här koden krävs bara när du använder en fristående loggningsprovider. För regelbunden Application Insights övervakning läses instrumenteringsnyckeln in automatiskt från *konfigurationssökvägen ApplicationInsights: InstrumentationKey*. Appsettings.jsska se ut så här:

   ```json
   {
     "ApplicationInsights":
       {
           "InstrumentationKey":"putrealikeyhere"
       }
   }
   ```

> [!IMPORTANT]
> Nya **Azure-regioner kräver** användning av anslutningssträngar i stället för instrumentationsnycklar. [Anslutningssträngen](./sdk-connection-string.md?tabs=net) identifierar den resurs som du vill associera dina telemetridata med. Du kan också ändra de slutpunkter som din resurs ska använda som mål för din telemetri. Du måste kopiera anslutningssträngen och lägga till den i programmets kod eller i en miljövariabel.

## <a name="next-steps"></a>Nästa steg

Läs mer om:

* [Logga in ASP.NET Core](/aspnet/core/fundamentals/logging)
* [.NET-spårningsloggar i Application Insights](./asp-net-trace-logs.md)

