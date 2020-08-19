---
title: Azure Application insikter om ASP.NET Core program | Microsoft Docs
description: Övervaka ASP.NET Core webb program för tillgänglighet, prestanda och användning.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 81d7ab38080aac941bce1f5d1dd17145f8075036
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518092"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights för ASP.NET Core program

I den här artikeln beskrivs hur du aktiverar Application Insights för ett [ASP.net Core](/aspnet/core) program. När du har slutfört instruktionerna i den här artikeln får Application Insights Samla in begär Anden, beroenden, undantag, prestanda räknare, pulsslag och loggar från ditt ASP.NET Core-program.

Exemplet som vi ska använda här är ett [MVC-program](/aspnet/core/tutorials/first-mvc-app) som är mål `netcoreapp3.0` . Du kan använda dessa instruktioner för alla ASP.NET Core-program. Om du använder [Worker-tjänsten](/aspnet/core/fundamentals/host/hosted-services#worker-service-template)kan du använda instruktionerna [här](./worker-service.md).

## <a name="supported-scenarios"></a>Scenarier som stöds

[Application Insights SDK för ASP.net Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) kan övervaka dina program oavsett var eller hur de körs. Om ditt program körs och har nätverks anslutning till Azure, kan telemetri samlas in. Application Insights övervakning stöds överallt där .NET Core stöds. Support omfattar:
* **Operativ system**: Windows, Linux eller Mac.
* **Värd metod**: i processen eller utanför processen.
* **Distributions metod**: Ramverks beroende eller fristående.
* **Webb server**: IIS (Internet Information Server) eller Kestrel.
* **Värd plattform**: Web Apps funktionen i Azure App Service, Azure VM, Docker, Azure Kubernetes service (AKS) och så vidare.
* **.Net Core runtime-version**: 1. xx, 2. xx eller 3. xx
* **IDE**: Visual Studio, vs Code eller kommando rad.

> [!NOTE]
> ASP.NET Core 3. X kräver [Application Insights 2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) eller senare.

## <a name="prerequisites"></a>Förutsättningar

- Ett fungerande ASP.NET Core program. Om du behöver skapa ett ASP.NET Core program följer du den här [ASP.net Core själv studie kursen](/aspnet/core/getting-started/).
- En giltig Application Insights Instrumentation-nyckel. Den här nyckeln krävs för att skicka telemetri till Application Insights. Om du behöver skapa en ny Application Insights resurs för att hämta en Instrumentation-nyckel, se [skapa en Application Insights resurs](./create-new-resource.md).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Aktivera Application Insights telemetri på Server sidan (Visual Studio)

För Visual Studio för Mac använder du den [manuella vägledningen](#enable-application-insights-server-side-telemetry-no-visual-studio). Endast Windows-versionen av Visual Studio stöder den här proceduren.

1. Öppna ditt projekt i Visual Studio.

    > [!TIP]
    > Om du vill kan du konfigurera käll kontroll för ditt projekt så att du kan spåra alla ändringar som Application Insights gör. Om du vill aktivera käll kontroll väljer du **fil**  >  **Lägg till i käll kontroll**.

2. Välj **projekt**  >  **Lägg till Application Insights Telemetry**.

3. Välj **Kom igång**. Detta urvals text kan variera beroende på vilken version av Visual Studio du har. Vissa tidigare versioner använder en **kostnads fri start** -knapp i stället.

4. Välj din prenumeration. Välj sedan **resurs**  >  **register**.

5. När du har lagt till Application Insights i projektet kontrollerar du att du använder den senaste stabila versionen av SDK. Gå till **Project**  >  **Hantera NuGet-paket**  >  **Microsoft. ApplicationInsights. AspNetCore**. Om du behöver väljer du **Uppdatera**.

     ![Skärm bild som visar var du kan välja Application Insightss paketet för uppdatering](./media/asp-net-core/update-nuget-package.png)

6. Om du har följt det valfria tipset och lagt till projektet i käll kontrollen går du till **Visa**  >  **team Explorer**-  >  **ändringar**. Välj sedan varje fil för att se en diff-vy av ändringarna som gjorts av Application Insights telemetri.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Aktivera Application Insights telemetri på Server sidan (inga Visual Studio)

1. Installera [Application Insights SDK NuGet-paketet för ASP.net Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Vi rekommenderar att du alltid använder den senaste stabila versionen. Hämta fullständiga versions anteckningar för SDK: n på [GitHub-lagrings platsen med öppen källkod](https://github.com/Microsoft/ApplicationInsights-dotnet/releases).

    I följande kod exempel visas de ändringar som ska läggas till i projekt `.csproj` filen.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. Lägg till i- `services.AddApplicationInsightsTelemetry();` `ConfigureServices()` metoden i `Startup` klassen, som i det här exemplet:

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. Konfigurera Instrumentation-tangenten.

    Även om du kan ange Instrumentation-nyckeln som ett argument för `AddApplicationInsightsTelemetry` , rekommenderar vi att du anger Instrumentation-nyckeln i konfigurationen. Följande kod exempel visar hur du anger en Instrumentation-nyckel i `appsettings.json` . Se till att `appsettings.json` kopieras till rotmappen för programmet under publiceringen.

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    Alternativt kan du ange Instrumentation-nyckeln i någon av följande miljövariabler:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Exempel:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    * `APPINSIGHTS_INSTRUMENTATIONKEY` används vanligt vis i [Azure Web Apps](./azure-web-apps.md?tabs=net), men kan även användas på alla platser där SDK stöds. (Om du gör en kod lös övervakning av webbappar krävs det här formatet om du inte använder anslutnings strängar.)

    Du kan nu även använda [anslutnings strängar](./sdk-connection-string.md?tabs=net)i stället för att ställa in Instrumentation-nycklar.

    > [!NOTE]
    > En Instrumentation-nyckel som anges i kod WINS över miljövariabeln `APPINSIGHTS_INSTRUMENTATIONKEY` , som vinner över andra alternativ.

### <a name="user-secrets-and-other-configuration-providers"></a>Användar hemligheter och andra konfigurations leverantörer

Om du vill lagra Instrumentation-nyckeln i ASP.NET Core användar hemligheter eller hämta den från en annan Konfigurationsprovider, kan du använda överlagringen med en `Microsoft.Extensions.Configuration.IConfiguration` parameter. Till exempel `services.AddApplicationInsightsTelemetry(Configuration);`.

## <a name="run-your-application"></a>Köra ditt program

Kör programmet och gör begär anden till det. Telemetri ska nu flöda till Application Insights. Application Insights SDK samlar automatiskt in inkommande webb förfrågningar till ditt program, tillsammans med följande telemetri.

### <a name="live-metrics"></a>Live Metrics

[Live-mått](./live-stream.md) kan användas för att snabbt kontrol lera om Application Insights övervakning har kon figurer ATS korrekt. Det kan ta några minuter innan telemetri börjar visas i portalen och i analysen, men Live-mått visar processor användningen för den process som körs i nära real tid. Det kan också visa andra telemetri som begär Anden, beroenden, spår osv.

### <a name="ilogger-logs"></a>ILogger-loggar

Loggar som släpps via `ILogger` allvarlighets grad `Warning` eller större samlas in automatiskt. Följ [ILogger-dokument](ilogger.md#control-logging-level) för att anpassa vilka logg nivåer som samlas in av Application Insights.

### <a name="dependencies"></a>Beroenden

Beroende insamling är aktiverat som standard. [Den här](asp-net-dependencies.md#automatically-tracked-dependencies) artikeln förklarar de beroenden som samlas in automatiskt och som även innehåller steg för manuell spårning.

### <a name="performance-counters"></a>Prestandaräknare

Stöd för [prestanda räknare](./web-monitor-performance.md) i ASP.net Core är begränsat:

* SDK-versioner 2.4.1 och senare samlar in prestanda räknare om programmet körs i Azure Web Apps (Windows).
* SDK-versioner 2.7.1 och senare samlar in prestanda räknare om programmet körs i Windows och mål `NETSTANDARD2.0` eller senare.
* För program som är riktade till .NET Framework har alla versioner av SDK stöd för prestanda räknare.
* SDK-versioner 2.8.0 och senare stöder CPU/minnes räknare i Linux. Ingen annan räknare stöds i Linux. Det rekommenderade sättet att hämta system räknare i Linux (och andra miljöer som inte kommer från Windows) är att använda [EventCounters](#eventcounter)

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` är aktive rad som standard och den samlar in en standard uppsättning räknare från .NET Core 3. X-appar. Självstudien om [EventCounter](eventcounters.md) visar en lista över standard uppsättningen med insamlade räknare. Den innehåller också anvisningar om hur du anpassar listan.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Aktivera telemetri på klient sidan för webb program

Föregående steg räcker för att hjälpa dig att börja samla in telemetri på Server sidan. Om programmet har komponenter på klient sidan följer du nästa steg för att börja samla in [användnings telemetri](./usage-overview.md).

1. I `_ViewImports.cshtml` lägger du till insprutning:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. I `_Layout.cshtml` infogar du i `HtmlHelper` slutet av `<head>` avsnittet men före andra skript. Om du vill rapportera anpassad JavaScript-telemetri från sidan, infogar du den efter det här kodfragmentet:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
Du kan också använda `FullScript` är tillgänglig från och med `ScriptBody` SDK v 2.14. Använd detta om du behöver styra `<script>` taggen för att ange en säkerhets princip för innehåll:

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

De `.cshtml` fil namn som refereras tidigare är från en standard MVC-Programmall. Slutligen, om du vill aktivera övervakning på klient sidan för ditt program, måste JavaScript-kodfragmentet visas i `<head>` avsnittet på varje sida i programmet som du vill övervaka. Du kan utföra det här målet för den här program mal len genom att lägga till JavaScript-kodfragmentet i `_Layout.cshtml` . 

Om projektet inte innehåller `_Layout.cshtml` kan du fortfarande lägga till [övervakning på klient sidan](./website-monitoring.md). Du kan göra detta genom att lägga till JavaScript-kodfragmentet i en motsvarande fil som styr `<head>` alla sidor i din app. Eller så kan du lägga till kodfragmentet på flera sidor, men den här lösningen är svår att underhålla och vi rekommenderar vanligt vis inte.

## <a name="configure-the-application-insights-sdk"></a>Konfigurera Application Insights SDK

Du kan anpassa Application Insights SDK för ASP.NET Core om du vill ändra standard konfigurationen. Användare av Application Insights ASP.NET SDK kan vara bekanta med att ändra konfigurationen genom att använda `ApplicationInsights.config` eller genom att ändra `TelemetryConfiguration.Active` . Du ändrar konfigurationen på olika sätt för ASP.NET Core. Lägg till ASP.NET Core SDK i programmet och konfigurera den med hjälp av ASP.NET Core inbyggd [beroende inmatning](/aspnet/core/fundamentals/dependency-injection). Gör nästan alla konfigurations ändringar i `ConfigureServices()` -metoden för din `Startup.cs` klass, om du inte är inriktad på annat sätt. I följande avsnitt finns mer information.

> [!NOTE]
> I ASP.NET Core program stöds inte ändring av konfiguration genom att ändra `TelemetryConfiguration.Active` .

### <a name="using-applicationinsightsserviceoptions"></a>Använda ApplicationInsightsServiceOptions

Du kan ändra några vanliga inställningar genom att skicka `ApplicationInsightsServiceOptions` till `AddApplicationInsightsTelemetry` , som i det här exemplet:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
}
```

Fullständig lista över inställningar i `ApplicationInsightsServiceOptions`

|Inställning | Beskrivning | Standardvärde
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | Aktivera/inaktivera `PerformanceCounterCollectionModule` | true
|EnableRequestTrackingTelemetryModule   | Aktivera/inaktivera `RequestTrackingTelemetryModule` | true
|EnableEventCounterCollectionModule   | Aktivera/inaktivera `EventCounterCollectionModule` | true
|EnableDependencyTrackingTelemetryModule   | Aktivera/inaktivera `DependencyTrackingTelemetryModule` | true
|EnableAppServicesHeartbeatTelemetryModule  |  Aktivera/inaktivera `AppServicesHeartbeatTelemetryModule` | true
|EnableAzureInstanceMetadataTelemetryModule   |  Aktivera/inaktivera `AzureInstanceMetadataTelemetryModule` | true
|EnableQuickPulseMetricStream | Aktivera/inaktivera LiveMetrics-funktionen | true
|EnableAdaptiveSampling | Aktivera/inaktivera adaptiv sampling | true
|EnableHeartbeat | Funktionen Aktivera/inaktivera pulsslag, som regelbundet (15 min standard) skickar ett anpassat mått med namnet "HeartbeatState" med information om körnings miljön som .NET-version, Azure-miljö information, om tillämpligt, osv. | true
|AddAutoCollectedMetricExtractor | Aktivera/inaktivera AutoCollectedMetrics Extractor, som är en TelemetryProcessor som skickar församlade mått om begär Anden/beroenden innan provtagning sker. | true
|RequestCollectionOptions.TrackExceptions | Aktivera/inaktivera rapportering av ohanterad undantags spårning i modulen för begärans insamling. | falskt i netstandard 2.0 (eftersom undantag spåras med ApplicationInsightsLoggerProvider), annars sant.

Se de [konfigurerbara inställningarna i `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) för den senaste listan.

### <a name="sampling"></a>Samling

Application Insights SDK för ASP.NET Core stöder både fast priss ätt och anpassningsbar sampling. Adaptiv sampling är aktiverat som standard. 

Mer information finns i [Konfigurera adaptiv sampling för ASP.net Core program](./sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Lägger till TelemetryInitializers

Använd [telemetri-initierare](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) när du vill utöka telemetri med ytterligare information.

Lägg till alla nya `TelemetryInitializer` i `DependencyInjection` behållaren enligt följande kod. SDK: n hämtar automatiskt de `TelemetryInitializer` som läggs till i `DependencyInjection` behållaren.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>Tar bort TelemetryInitializers

Telemetri-initierare finns som standard. Om du vill ta bort alla eller vissa telemetri-initierare använder du följande exempel kod *när* du har anropat `AddApplicationInsightsTelemetry()` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // Remove a specific built-in telemetry initializer
    var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                        (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
    if (tiToRemove != null)
    {
        services.Remove(tiToRemove);
    }

    // Remove all initializers
    // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
    services.RemoveAll(typeof(ITelemetryInitializer));
}
```

### <a name="adding-telemetry-processors"></a>Lägga till telemetri-processorer

Du kan lägga till anpassade telemetri-processorer i `TelemetryConfiguration` med hjälp av tilläggs metoden `AddApplicationInsightsTelemetryProcessor` i `IServiceCollection` . Du använder telemetri-processorer i [avancerade filtrerings scenarier](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer). Använd följande exempel.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddApplicationInsightsTelemetry();
    services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

    // If you have more processors:
    services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
}
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurera eller ta bort standard TelemetryModules

Application Insights använder telemetri-moduler för att automatiskt samla in användbar telemetri om vissa arbets belastningar utan manuell spårning av användaren.

Följande moduler för automatisk insamling är aktiverade som standard. Dessa moduler är ansvariga för automatisk insamling av telemetri. Du kan inaktivera eller konfigurera dem för att ändra deras standard beteende.

* `RequestTrackingTelemetryModule` – Samlar in RequestTelemetry från inkommande webb förfrågningar.
* `DependencyTrackingTelemetryModule` -Samlar in [DependencyTelemetry](./asp-net-dependencies.md) från utgående HTTP-anrop och SQL-anrop.
* `PerformanceCollectorModule` -Samlar in Windows-PerformanceCounters.
* `QuickPulseTelemetryModule` – Samlar in telemetri för visning i Live Metrics-portalen.
* `AppServicesHeartbeatTelemetryModule` – Samlar in hjärtat-taktslag (som skickas som anpassade mått), om Azure App Service miljö där programmet finns.
* `AzureInstanceMetadataTelemetryModule` – Samlar in hjärtat-taktslag (som skickas som anpassade mått), om Azure VM-miljön där programmet finns.
* `EventCounterCollectionModule` – Samlar in [EventCounters.](eventcounters.md) Den här modulen är en ny funktion som är tillgänglig i SDK-version 2.8.0 och senare.

Om du vill konfigurera standard `TelemetryModule` använder du tilläggs metoden `ConfigureTelemetryModule<T>` på `IServiceCollection` , som du ser i följande exempel.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // The following configures DependencyTrackingTelemetryModule.
    // Similarly, any other default modules can be configured.
    services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
            {
                module.EnableW3CHeadersInjection = true;
            });

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Clear();
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

Från och med 2.12.2-versionen, [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) innehåller ett enkelt alternativ för att inaktivera standardmodulerna.

### <a name="configuring-a-telemetry-channel"></a>Konfigurera en telemetri kanal

Standard [kanalen för telemetri](./telemetry-channels.md) är `ServerTelemetryChannel` . Du kan åsidosätta det som visas i följande exempel.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>Inaktivera telemetri dynamiskt

Om du vill inaktivera telemetri villkorligt och dynamiskt kan du lösa `TelemetryConfiguration` instansen med ASP.net Core beroende injektions behållare var som helst i koden och ange `DisableTelemetry` flaggan på den.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

Ovanstående förhindrar inte att automatiska insamlings moduler samlar in telemetri. Endast sändningen av telemetri till Application Insights inaktive ras med ovanstående metod. Om en viss modul för automatisk insamling inte är önskvärd, är det bäst att [ta bort modulen telemetri](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="does-application-insights-support-aspnet-core-3x"></a>Stöder Application Insights ASP.NET Core 3. X?

Ja. Uppdatera till [Application Insights SDK för ASP.net Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.8.0 eller senare. Äldre versioner av SDK stöder inte ASP.NET Core 3. X.

Om du använder Visual Studio-baserade instruktioner [härifrån kan du](#enable-application-insights-server-side-telemetry-visual-studio)också uppdatera till den senaste versionen av Visual Studio 2019 (16.3.0) till onboarding. Tidigare versioner av Visual Studio stöder inte automatisk onboarding för ASP.NET Core 3. X-appar.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hur kan jag spåra telemetri som inte samlas in automatiskt?

Hämta en instans av `TelemetryClient` med hjälp av konstruktorn för konstruktorn och anropa metoden som krävs `TrackXXX()` . Vi rekommenderar inte att du skapar nya `TelemetryClient` instanser i ett ASP.net Core program. En singleton-instans av `TelemetryClient` är redan registrerad i `DependencyInjection` behållaren, som delas `TelemetryConfiguration` med en rest av Telemetrin. Att skapa en ny `TelemetryClient` instans rekommenderas endast om den behöver en konfiguration som är separat från resten av Telemetrin.

I följande exempel visas hur du spårar ytterligare telemetri från en kontrollant.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

Mer information om anpassad data rapportering i Application Insights finns i [Application Insights anpassade mått-API-referens](./api-custom-events-metrics.md). En liknande metod kan användas för att skicka anpassade mått till Application Insights med GetMetric- [API: et](./get-metric.md).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Vissa Visual Studio-mallar använde tilläggs metoden UseApplicationInsights () på IWebHostBuilder för att aktivera Application Insights. Är den här användningen fortfarande giltig?

Även om tilläggs metoden `UseApplicationInsights()` fortfarande stöds är den deklarerad som föråldrad i Application Insights SDK-version 2.8.0 och senare. Den kommer att tas bort i nästa huvud version av SDK. Det rekommenderade sättet att aktivera Application Insights telemetri är att använda `AddApplicationInsightsTelemetry()` eftersom det ger överbelastningar för att kontrol lera viss konfiguration. Dessutom `services.AddApplicationInsightsTelemetry()` är det enda sättet att aktivera Application Insights i ASP.net Core 3. X-appar.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Jag distribuerar mitt ASP.NET Core-program till Web Apps. Bör jag fortfarande aktivera Application Insights tillägget från Web Apps?

Om SDK installeras på bygg tid enligt den här artikeln behöver du inte aktivera [Application Insights-tillägget](./azure-web-apps.md) från App Service portalen. Även om tillägget är installerat kommer det att stängas av när det upptäcker att SDK redan har lagts till i programmet. Om du aktiverar Application Insights från tillägget behöver du inte installera och uppdatera SDK. Men om du aktiverar Application Insights genom att följa anvisningarna i den här artikeln har du mer flexibilitet eftersom:

   * Application Insights telemetri fortsätter att fungera i:
       * Alla operativ system, inklusive Windows, Linux och Mac.
       * Alla publicerings lägen, inklusive oberoende eller Ramverks beroende.
       * Alla mål ramverk, inklusive den fullständiga .NET Framework.
       * Alla värd alternativ, inklusive Web Apps, virtuella datorer, Linux, behållare, Azure Kubernetes service och icke-Azure-värd.
       * Alla .NET Core-versioner, inklusive för hands versioner.
   * Du kan se telemetri lokalt när du felsöker från Visual Studio.
   * Du kan spåra ytterligare anpassad telemetri med hjälp av `TrackXXX()` API: et.
   * Du har fullständig kontroll över konfigurationen.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Kan jag aktivera Application Insights övervakning med hjälp av verktyg som Statusövervakare?

Nej. [Statusövervakare](./monitor-performance-live-website-now.md) och [statusövervakare v2](./status-monitor-v2-overview.md) stöder för närvarande endast ASP.NET 4. x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Är Application Insights automatiskt aktiverat för mitt ASP.NET Core 2,0-program?

`Microsoft.AspNetCore.All`2,0-Metapackage ingår Application Insights SDK (version 2.1.0). Om du kör programmet i Visual Studio-felsökaren aktiverar Visual Studio Application Insights och visar telemetri lokalt i IDE-objektet. Telemetri skickades inte till Application Insights tjänsten om inte en instrument nyckel angavs. Vi rekommenderar att du följer anvisningarna i den här artikeln för att aktivera Application Insights, även för 2,0-appar.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Fungerar alla funktioner om jag kör mitt program i Linux?

Ja. Funktions stödet för SDK är detsamma i alla plattformar, med följande undantag:

* SDK: n samlar in [händelse räknare](./eventcounters.md) på Linux eftersom [prestanda räknare](./performance-counters.md) endast stöds i Windows. De flesta mått är desamma.
* Trots `ServerTelemetryChannel` att är aktiverat som standard, om programmet körs i Linux eller MacOS, skapar kanalen inte automatiskt en lokal lagringsmapp för att hålla telemetri tillfälligt om det uppstår nätverks problem. På grund av den här begränsningen försvinner telemetri när det uppstår tillfälliga nätverks-eller Server problem. Undvik det här problemet genom att konfigurera en lokal mapp för kanalen:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Stöds det här SDK: n för de nya mallarna för .NET Core 3. X Worker service mal len?

Detta SDK kräver `HttpContext` och fungerar därför inte i några icke-http-program, inklusive .net Core 3. X Worker service-programmen. Se [det här](worker-service.md) dokumentet för att aktivera Application Insights i sådana program med hjälp av den nyligen utgivna Microsoft. ApplicationInsights. WorkerService SDK.

## <a name="open-source-sdk"></a>SDK för öppen källkod

* [Läs och bidra till koden](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates).

De senaste uppdateringarna och fel korrigeringarna [finns i viktig information](./release-notes.md).

## <a name="next-steps"></a>Nästa steg

* [Utforska användar flöden](./usage-flows.md) för att förstå hur användare navigerar i din app.
* [Konfigurera en ögonblicks bild samling](./snapshot-debugger.md) för att se status för källkod och variabler vid det tillfälle då ett undantag genereras.
* [Använd API: et](./api-custom-events-metrics.md) för att skicka egna händelser och mått för en detaljerad vy över appens prestanda och användning.
* Använd [tillgänglighets test](./monitor-web-app-availability.md) för att kontrol lera att din app ständigt är i hela världen.
* [Beroende inmatning i ASP.NET Core](/aspnet/core/fundamentals/dependency-injection)

