---
title: Utforska .NET-spårnings loggar med ILogger Azure Application-insikter
description: Exempel på användning av Azure Application Insights ILogger-providern med ASP.NET Core-och konsol program.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: e7d0bd6f7d93eac944e0d53a8e898463bf9887f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592276"
---
# <a name="applicationinsightsloggerprovider-for-microsoftextensionlogging"></a>ApplicationInsightsLoggerProvider för Microsoft. extension. logging

Den här artikeln visar hur du använder `ApplicationInsightsLoggerProvider` för att avbilda `ILogger` loggar i-konsolen och ASP.net Core program.
Mer information finns i [Logga in ASP.net Core](/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Core program

`ApplicationInsightsLoggerProvider` är aktive rad som standard för ASP.NET Core program när ApplicationInsights har kon figurer ATS med [kod](./asp-net-core.md) eller [kod lös](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring) metod.

Endast *varnings* -och över `ILogger` loggar (från alla [kategorier](/aspnet/core/fundamentals/logging/#log-category)) skickas till Application Insights som standard. Men du kan [anpassa det här beteendet](./asp-net-core.md#how-do-i-customize-ilogger-logs-collection). Ytterligare steg krävs för att avbilda ILogger-loggar från **program. cs** eller **startup. cs**. (Mer information finns i [fånga ILogger-loggar från startup. CS och program. cs i ASP.net Core-program](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Använd följande steg om du bara vill använda `ApplicationInsightsLoggerProvider` utan någon annan Application Insights övervakning.

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

Koden i steg 2 konfigureras `ApplicationInsightsLoggerProvider` . Följande kod visar en exempel kontroll klass som använder `ILogger` för att skicka loggar. Loggarna samlas in av Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Avbilda ILogger-loggar från startup. CS och program. cs i ASP.NET Core appar

> [!NOTE]
> I ASP.NET Core 3,0 och senare går det inte längre att mata `ILogger` in i Start. CS och program. cs. Mer https://github.com/aspnet/Announcements/issues/353 information finns i.

`ApplicationInsightsLoggerProvider` kan samla in loggar från tidiga i program starten. Även om ApplicationInsightsLoggerProvider aktive ras automatiskt i Application Insights (från och med version 2.7.1) har den inte någon Instrumentation-nyckel konfigurerad till senare i pipelinen. Därför kommer endast loggar från **Controller**/other-klasser att samlas in. Om du vill avbilda varje logg som börjar med **program. cs** och **startup. cs** , måste du uttryckligen aktivera en Instrumentation-nyckel för ApplicationInsightsLoggerProvider. Dessutom är *TelemetryConfiguration* inte helt konfigurerat när du loggar från **program. cs** eller **startup. cs** . Dessa loggar har en minimal konfiguration som använder [InMemoryChannel](./telemetry-channels.md), ingen [sampling](./sampling.md)och inga standard- [initierare eller-processorer för telemetri](./api-filtering-sampling.md).

I följande exempel demonstreras den här funktionen med **program. cs** och **startup. cs**.

#### <a name="example-programcs"></a>Exempel program. CS

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

#### <a name="example-startupcs"></a>Exempel start. CS

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

Microsoft. ApplicationInsights. ASPNET SDK-versioner innan 2.7.1 stödde en Logging-provider som nu är föråldrad. Den här providern har Aktiver ATS via **AddApplicationInsights ()-** tilläggs metoden för ILoggerFactory. Vi rekommenderar att du migrerar till den nya providern, vilket innebär två steg:

1. Ta bort anropet *ILoggerFactory. AddApplicationInsights ()* från metoden **Startup.Configurera ()** för att undvika dubbel loggning.
2. Tillämpa eventuella filtrerings regler i koden eftersom de inte kommer att respekteras av den nya providern. Överlagringar av *ILoggerFactory. AddApplicationInsights ()* tog minsta LogLevel-eller filter funktioner. Med den nya providern är filtrering en del av själva loggnings ramverket. Den är inte gjord av Application Insights leverantören. Därför bör alla filter som anges via *ILoggerFactory. AddApplicationInsights ()* tas bort. Och filtrerings regler bör tillhandahållas genom att följa anvisningarna för [kontroll loggnings nivå](#control-logging-level) . Om du använder *appsettings.jspå* för att filtrera loggningen fortsätter den att fungera med den nya providern, eftersom båda använder samma provider-alias, *ApplicationInsights*.

Du kan fortfarande använda den gamla providern. (Den tas bara bort i en större versions ändring till 3. *XX*.) men vi rekommenderar att du migrerar till den nya providern av följande anledningar:

- Den tidigare providern saknar stöd för [logg omfattningar](/aspnet/core/fundamentals/logging#log-scopes). I den nya providern läggs egenskaper från definitions området automatiskt till som anpassade egenskaper till den insamlade Telemetrin.
- Loggar kan nu fångas mycket tidigare i program start pipelinen. Loggar från **program** -och **Start** klasserna kan nu fångas.
- Med den nya providern görs filtrering på själva Ramverks nivån. Du kan filtrera loggar till Application Insights-providern på samma sätt som för andra leverantörer, inklusive inbyggda providers, t. ex. konsol, fel sökning och så vidare. Du kan också använda samma filter för flera providers.
- I ASP.NET Core (2,0 och senare) är det rekommenderade sättet att  [Aktivera loggnings leverantörer](https://github.com/aspnet/Announcements/issues/255) att använda tilläggs metoder på ILoggingBuilder i **program. cs** .

> [!Note]
> Den nya providern är tillgänglig för program som är riktade till netstandard 2.0 eller senare. Från [Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) -version 2.14.0 och senare är en ny provider också tillgänglig för program som är riktade till .NET Framework NET461 eller senare. Om programmet är inriktat över äldre .NET Core-versioner, t. ex. .NET core 1,1 eller om den är riktad mot .NET Framework mindre än NET46, fortsätter du att använda den gamla providern.

## <a name="console-application"></a>Konsol program

> [!NOTE]
> Det finns ett nytt Application Insights SDK som heter [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) som kan användas för att aktivera Application Insights (ILogger och annan Application Insights telemetri) för alla konsol program. Vi rekommenderar att du använder det här paketet och tillhör ande instruktioner [härifrån.](./worker-service.md)

Om du bara vill använda ApplicationInsightsLoggerProvider utan någon annan Application Insights övervakning använder du följande steg.

Paket installerade:

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

I det här exemplet används det fristående paketet `Microsoft.Extensions.Logging.ApplicationInsights` . Som standard använder den här konfigurationen den "minimala" TelemetryConfiguration för att skicka data till Application Insights. Minimalt innebär att InMemoryChannel är den kanal som används. Det finns ingen sampling och ingen standard TelemetryInitializers. Det här beteendet kan åsidosättas för ett konsol program, vilket visas i följande exempel.

Installera det här ytterligare paketet:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

I följande avsnitt visas hur du åsidosätter standard-TelemetryConfiguration med hjälp av metoden **services.Configurera \<TelemetryConfiguration> ()** . Det här exemplet ställer in `ServerTelemetryChannel` och sampling. Den lägger till en anpassad ITelemetryInitializer i TelemetryConfiguration.

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

## <a name="control-logging-level"></a>Kontrol lera loggnings nivå

`ILogger` har en inbyggd mekanism för att tillämpa [logg filtrering](/aspnet/core/fundamentals/logging#log-filtering). På så sätt kan du styra de loggar som skickas till varje registrerad Provider, inklusive Application Insights leverantören. Filtreringen kan göras antingen i konfigurationen (vanligt vis med hjälp av en *appsettings.jspå* fil) eller i kod.

I följande exempel visas hur du använder filter regler i `ApplicationInsightsLoggerProvider` .

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Skapa filter regler i konfigurationen med appsettings.jspå

För ApplicationInsightsLoggerProvider är providerns alias `ApplicationInsights` . I följande avsnitt av *appsettings.jspå* instrueras loggnings leverantörer vanligt vis att logga på nivå *varningen* och ovan. Den åsidosätter sedan de `ApplicationInsightsLoggerProvider` till logg kategorier som börjar med "Microsoft" på nivå *fel* och högre.

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

### <a name="create-filter-rules-in-code"></a>Skapa filter regler i kod

Följande kodfragment konfigurerar loggar för *Varning* och över från alla kategorier och för *fel* och över från kategorier som börjar med "Microsoft" att skickas till `ApplicationInsightsLoggerProvider` . Den här konfigurationen är samma som i föregående avsnitt i *appsettings.jspå*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="logging-scopes"></a>Loggnings omfång

`ApplicationInsightsLoggingProvider` stöder [logg omfattningar](/aspnet/core/fundamentals/logging#log-scopes) och omfång är aktiverade som standard.

Om omfånget är av typen `IReadOnlyCollection<KeyValuePair<string,object>>` , läggs varje nyckel/värde-par i samlingen till i Application Insights-telemetri som anpassade egenskaper. I exemplet nedan kommer loggar att samlas in som `TraceTelemetry` och kommer att ha ("MyKey", "attributvärde") i egenskaper.

```csharp
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
    {
        _logger.LogError("An example of an Error level message");
    }
```

Om någon annan typ används som omfång kommer de att lagras under egenskapen "omfång" i Application Insights-telemetri. I exemplet nedan `TraceTelemetry` har en egenskap som kallas "omfång" som innehåller omfattningen.

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Vilka är de gamla och nya versionerna av ApplicationInsightsLoggerProvider?

[Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) innehöll en inbyggd ApplicationInsightsLoggerProvider (Microsoft. ApplicationInsights. AspNetCore. logging. ApplicationInsightsLoggerProvider), som har Aktiver ATS genom **ILoggerFactory** tilläggs metoder. Den här providern har marker ATS som föråldrad från version 2.7.1. Den tas bort helt i nästa större versions ändring. 2.6.1-paketet [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) är inte föråldrat. Det krävs för att aktivera övervakning av begär Anden, beroenden och så vidare.

Det föreslagna alternativet är det nya fristående paketet [Microsoft. Extensions. logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), som innehåller förbättrade ApplicationInsightsLoggerProvider (Microsoft. Extensions. logging. ApplicationInsights. ApplicationInsightsLoggerProvider) och tilläggs metoder på ILoggerBuilder för att aktivera det.

[Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.1 tar ett beroende på det nya paketet och aktiverar ILogger-avbildning automatiskt.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Varför visas vissa ILogger-loggar två gånger i Application Insights?

Duplicering kan inträffa om du har den äldre (nu föråldrade) versionen av ApplicationInsightsLoggerProvider aktive rad genom att anropa `AddApplicationInsights` `ILoggerFactory` . Kontrol lera att **konfigurations** metoden har följande och ta bort den:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Om du får dubbel loggning när du felsöker från Visual Studio, anger `EnableDebugLogger` du  *falskt* i koden som aktiverar Application Insights, enligt följande. Den här dupliceringen och korrigeringen är bara relevant när du felsöker programmet.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Jag har uppdaterat till [Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.1 och loggar från ILogger samlas in automatiskt. Vill du Hur gör jag för att inaktivera funktionen helt?

Se avsnittet [kontrol lera loggnings nivå](#control-logging-level) för att se hur du filtrerar loggar i allmänhet. Om du vill inaktivera ApplicationInsightsLoggerProvider använder du `LogLevel.None` :

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

Application Insights fångar in och skickar ILogger-loggar med samma TelemetryConfiguration som används för alla andra telemetri. Men det finns ett undantag. Som standard är TelemetryConfiguration inte helt konfigurerat när du loggar från **program. cs** eller **startup. cs**. Loggar från de här platserna har inte standard konfigurationen, så de kör inte alla TelemetryInitializers och TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Jag använder det fristående paketet Microsoft. Extensions. logging. ApplicationInsights och jag vill logga ytterligare anpassad telemetri manuellt. Hur gör jag?

När du använder det fristående paketet `TelemetryClient` matas det inte in i di-behållaren, så du måste skapa en ny instans av `TelemetryClient` och använda samma konfiguration som loggarna använder, som följande kod visar. Detta säkerställer att samma konfiguration används för all anpassad telemetri och telemetri från ILogger.

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
> Om du använder Microsoft. ApplicationInsights. AspNetCore-paketet för att aktivera Application Insights ändrar du koden så att den blir `TelemetryClient` direkt i konstruktorn. Ett exempel finns i [vanliga frågor och svar](./asp-net-core.md#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Vilken Application Insights telemetri skapas från ILogger-loggar? Eller var kan jag se ILogger-loggar i Application Insights?

ApplicationInsightsLoggerProvider fångar ILogger-loggar och skapar TraceTelemetry från dem. Om ett undantags objekt skickas till `Log` metoden i `ILogger` skapas *ExceptionTelemetry* i stället för TraceTelemetry. Dessa telemetri-objekt finns på samma platser som andra TraceTelemetry eller ExceptionTelemetry för Application Insights, inklusive Portal, analys eller Visual Studio Local-felsökning.

Om du föredrar att alltid skicka TraceTelemetry, använder du det här kodfragmentet: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Jag har inte installerat SDK och jag använder tillägget Azure Web Apps för att aktivera Application Insights för mina ASP.NET Core-program. Hur gör jag för att använder du den nya providern? 

Application Insights tillägget i Azure Web Apps använder den nya providern. Du kan ändra filtrerings reglerna i *appsettings.js* filen för programmet.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Jag använder det fristående paketet Microsoft. Extensions. logging. ApplicationInsights och aktiverar Application Insights provider genom att anropa **Builder. AddApplicationInsights ("iKey")**. Finns det något alternativ för att hämta en Instrumentation-nyckel från konfigurationen?


Ändra program. CS och appsettings.jspå följande sätt:

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

Den här koden krävs bara när du använder en fristående Logging-Provider. Vid regelbunden Application Insights övervakning läses instrument knappen in automatiskt från konfigurations Sök vägen *ApplicationInsights: InstrumentationKey*. Appsettings.jspå bör se ut så här:

   ```json
   {
     "ApplicationInsights":
       {
           "InstrumentationKey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Nästa steg

Läs mer om:

* [Logga in ASP.NET Core](/aspnet/core/fundamentals/logging)
* [.NET-spårnings loggar i Application Insights](./asp-net-trace-logs.md)

