---
title: Telemetrisampling i Azure Application Insights | Microsoft Docs
description: Hur du håller volymen av telemetri under kontroll.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: ba7892c8afbe8e557c7dcf9aa3bd663f53a5728f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834758"
---
# <a name="sampling-in-application-insights"></a>Sampling i Application Insights

Sampling är en funktion i [Azure Application Insights](./app-insights-overview.md). Det är det rekommenderade sättet att minska telemetritrafik, datakostnader och lagringskostnader, samtidigt som en statistiskt korrekt analys av programdata bevaras. Sampling hjälper dig också att Application Insights att minska begränsningen av telemetrin. Samplingsfiltret väljer objekt som är relaterade, så att du kan navigera mellan objekt när du utför diagnostiska undersökningar.

När måttantal visas i portalen renormaliseras de för att ta hänsyn till sampling. På så sätt minimeras all påverkan på statistiken.

## <a name="brief-summary"></a>Kort sammanfattning

* Det finns tre olika typer av sampling: adaptiv sampling, sampling med fast hastighet och inmatningssampling.
* Adaptiv sampling är aktiverat som standard i alla de senaste versionerna av Application Insights ASP.NET och ASP.NET Core Software Development Kits (SDK). Det används också av [Azure Functions](../../azure-functions/functions-overview.md).
* Sampling med fast hastighet är tillgängligt i de senaste versionerna av Application Insights SDK:er för ASP.NET, ASP.NET Core, Java (både agenten och SDK:n) och Python.
* Datainmatningssampling fungerar på Application Insights tjänstslutpunkt. Den gäller endast när ingen annan sampling används. Om SDK ger exempel på din telemetri inaktiveras inmatningssampling.
* Om du loggar anpassade händelser för webbprogram och behöver se till att en uppsättning händelser bevaras eller ignoreras tillsammans måste händelserna ha samma `OperationId` värde.
* Om du skriver Analytics-frågor bör du [ta hänsyn till sampling](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations). I synnerhet bör du använda i stället för att helt enkelt räkna `summarize sum(itemCount)` poster.
* Vissa telemetrityper, inklusive prestandamått och anpassade mått, behålls alltid oavsett om sampling är aktiverat eller inte.

I följande tabell sammanfattas de samplingstyper som är tillgängliga för varje SDK och typ av program:

| Application Insights SDK | Anpassningsbar sampling stöds | Sampling med fast frekvens stöds | Datainmatningssampling stöds |
|-|-|-|-|
| ASP.NET | [Ja (på som standard)](#configuring-adaptive-sampling-for-aspnet-applications) | [Ja](#configuring-fixed-rate-sampling-for-aspnet-applications) | Endast om ingen annan sampling används |
| ASP.NET Core | [Ja (på som standard)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Ja](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Endast om ingen annan sampling används |
| Azure Functions | [Ja (på som standard)](#configuring-adaptive-sampling-for-azure-functions) | No | Endast om ingen annan sampling används |
| Java | No | [Ja](#configuring-fixed-rate-sampling-for-java-applications) | Endast om ingen annan sampling används |
| Node.JS | No | [Ja](./nodejs.md#sampling) | Endast om ingen annan sampling används
| Python | No | [Ja](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Endast om ingen annan sampling används |
| Alla andra | Inga | Inga | [Ja](#ingestion-sampling) |

> [!NOTE]
> Informationen på de flesta av den här sidan gäller för de aktuella versionerna av Application Insights-SDK:er. Information om äldre versioner av DEDK:erna [finns i avsnittet nedan.](#older-sdk-versions)

## <a name="types-of-sampling"></a>Typer av sampling

Det finns tre olika samplingsmetoder:

* **Anpassningsbar sampling** justerar automatiskt mängden telemetri som skickas från SDK:n i ASP.NET/ASP.NET Core-appen och från Azure Functions. Det här är standardsampling när du använder ASP.NET eller ASP.NET Core SDK. Adaptiv sampling är för närvarande endast ASP.NET för telemetri på serversidan och för Azure Functions.

* Sampling med fast frekvens minskar mängden telemetri som skickas från både din ASP.NET eller ASP.NET Core- eller **Java-server** och från användarnas webbläsare. Du anger priset. Klienten och servern synkroniserar sin sampling så att du i Sök kan navigera mellan relaterade sidvisningar och förfrågningar.

* **Inmatningssampling** sker Application Insights tjänstslutpunkten. En del av telemetrin som tas emot från din app tas bort med en samplingsfrekvens som du anger. Det minskar inte telemetritrafiken som skickas från din app, men hjälper dig att hålla dig inom din månatliga kvot. Den största fördelen med inmatningssampling är att du kan ange samplingsfrekvensen utan att omdistribuera din app. Datainmatningssampling fungerar enhetligt för alla servrar och klienter, men den gäller inte när andra typer av sampling används.

> [!IMPORTANT]
> Om metoder för adaptiv eller fast frekvenssampling är aktiverade för en telemetrityp inaktiveras inmatningssampling för den telemetrin. Telemetrityper som undantas från sampling på SDK-nivå kommer dock fortfarande att omfattas av inmatningssampling med den hastighet som anges i portalen.

## <a name="adaptive-sampling"></a>Anpassningsbar sampling

Adaptiv sampling påverkar mängden telemetri som skickas från webbserverappen till den Application Insights tjänstslutpunkten.

> [!TIP]
> Anpassningsbar sampling är aktiverat som standard när du använder ASP.NET SDK eller ASP.NET Core SDK och är också aktiverat som standard för Azure Functions.

Volymen justeras automatiskt för att hålla inom en angiven högsta trafikhastighet och styrs via inställningen `MaxTelemetryItemsPerSecond` . Om programmet genererar en låg mängd telemetri, till exempel vid felsökning eller på grund av låg användning, kommer objekten inte att tas bort av samplingsprocessorn så länge volymen är lägre än `MaxTelemetryItemsPerSecond` . När mängden telemetri ökar justeras samplingsfrekvensen för att uppnå målvolymen. Justeringen beräknas om med jämna mellanrum och baseras på ett glidande medelvärde för den utgående överföringshastigheten.

För att uppnå målvolymen ignoreras en del av den genererade telemetrin. Men precis som andra typer av sampling behåller algoritmen relaterade telemetriobjekt. När du till exempel inspekterar telemetrin i Sök kan du hitta begäran som rör ett visst undantag.

Måttantal som begärandefrekvens och undantagsfrekvens justeras för att kompensera för samplingsfrekvensen, så att de visar ungefär rätt värden i Metric Explorer.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Konfigurera anpassningsbar sampling för ASP.NET program

> [!NOTE]
> Det här avsnittet gäller ASP.NET program, inte för ASP.NET Core-program. [Lär dig mer om att konfigurera anpassningsbar sampling ASP.NET Core-program senare i det här dokumentet.](#configuring-adaptive-sampling-for-aspnet-core-applications)

I [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) kan du justera flera parametrar i `AdaptiveSamplingTelemetryProcessor` noden. De siffror som visas är standardvärdena:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Målfrekvensen för logiska [åtgärder som den anpassningsbara](./correlation.md#data-model-for-telemetry-correlation) algoritmen syftar till att samla **in på varje servervärd.** Om webbappen körs på många värdar kan du minska det här värdet så att du håller dig inom målhastigheten för trafik på Application Insights portalen.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Intervallet då den aktuella telemetrihastigheten omvärderas. Utvärderingen utförs som ett glidande medelvärde. Du kanske vill förkorta det här intervallet om telemetrin kan leda till plötsliga plötsliga fel.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    När samplingsprocentvärdet ändras, hur snart efter får vi sänka samplingsprocenten igen för att samla in mindre data?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    När samplingsprocentvärdet ändras, hur snart efter kan vi öka samplingsprocenten igen för att samla in mer data?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Eftersom samplingsprocenten varierar, vad är det minsta värde som vi får ange?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Eftersom samplingsprocenten varierar, vilket är det högsta värde som vi får ange?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    I beräkningen av glidande medelvärde anger detta den vikt som ska tilldelas till det senaste värdet. Använd ett värde som är lika med eller mindre än 1. Mindre värden gör att algoritmen blir mindre reaktiv vid plötsliga ändringar.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Mängden telemetri som ska samplas när appen precis har startat. Minska inte det här värdet när du felsöker.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    En semikolonavgränsad lista över typer som du inte vill ska vara föremål för sampling. Identifierade typer är: `Dependency` , , , , , `Event` `Exception` `PageView` `Request` `Trace` . All telemetri för de angivna typerna överförs. de typer som inte anges samplas.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    En semikolonavgränsad lista över typer som du vill använda för sampling. Identifierade typer är: `Dependency` , , , , , `Event` `Exception` `PageView` `Request` `Trace` . De angivna typerna samplas. all telemetri för de andra typerna överförs alltid.

**Om du vill inaktivera** adaptiv sampling tar `AdaptiveSamplingTelemetryProcessor` du bort nodarna från `ApplicationInsights.config` .

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativ: Konfigurera adaptiv sampling i kod

I stället för att ange samplingsparametern `.config` i filen kan du programmatiskt ange dessa värden.

1. Ta bort `AdaptiveSamplingTelemetryProcessor` alla noder från `.config` filen.
2. Använd följande kodfragment för att konfigurera adaptiv sampling:

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Läs mer om telemetriprocessorer](./api-filtering-sampling.md#filtering).)

Du kan också justera samplingsfrekvensen för varje telemetrityp individuellt eller till och med utesluta vissa typer från att samplas alls:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Konfigurera anpassningsbar sampling för ASP.NET Core-program

Det finns inget `ApplicationInsights.config` för ASP.NET Core-program, så all konfiguration görs via kod.
Anpassningsbar sampling är aktiverat som standard för alla ASP.NET Core-program. Du kan inaktivera eller anpassa samplingsbeteendet.

#### <a name="turning-off-adaptive-sampling"></a>Stänga av adaptiv sampling

Standardsamplingsfunktionen kan inaktiveras när du Application Insights en tjänst i metoden `ConfigureServices` , med hjälp av i filen `ApplicationInsightsServiceOptions` `Startup.cs` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

Koden ovan inaktiverar adaptiv sampling. Följ stegen nedan för att lägga till sampling med fler anpassningsalternativ.

#### <a name="configure-sampling-settings"></a>Konfigurera samplingsinställningar

Använd tilläggsmetoder för `TelemetryProcessorChainBuilder` enligt nedan för att anpassa samplingsbeteendet.

> [!IMPORTANT]
> Om du använder den här metoden för att konfigurera sampling måste du ange egenskapen `aiOptions.EnableAdaptiveSampling` till när `false` du anropar `AddApplicationInsightsTelemetry()` . När du har gjort den här ändringen måste du  följa anvisningarna i kodblocket nedan exakt för att återaktivera adaptiv sampling med dina anpassningar på plats. Om du inte gör det kan det leda till överflödig datainmatning. Testa alltid efter att du ändrat samplingsinställningarna och ange ett lämpligt [dagligt datatak](pricing.md#set-the-daily-cap) för att kontrollera dina kostnader.

```csharp
using Microsoft.ApplicationInsights.Extensibility

public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Konfigurera anpassningsbar sampling för Azure Functions

Följ anvisningarna på [den här sidan](../../azure-functions/configure-monitoring.md#configure-sampling) för att konfigurera anpassningsbar sampling för appar som körs i Azure Functions.

## <a name="fixed-rate-sampling"></a>Sampling med fast hastighet

Sampling med fast hastighet minskar den trafik som skickas från webbservern och webbläsaren. Till skillnad från adaptiv sampling minskar telemetrin med en fast hastighet som du väljer. Sampling med fast hastighet är tillgängligt för ASP.NET, ASP.NET Core, Java och Python.

Precis som med andra samplingstekniker behålls även relaterade objekt. Den synkroniserar också klient- och serversampling så att relaterade objekt bevaras– när du till exempel tittar på en sidvy i Sök kan du hitta dess relaterade serverbegäranden. 

I Metrics Explorer multipliceras frekvenser som antal förfrågningar och undantag med en faktor för att kompensera för samplingsfrekvensen, så att de är ungefär korrekta.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Konfigurera fast samplingsfrekvens för ASP.NET program

1. **Inaktivera adaptiv sampling:** Ta [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) bort eller kommentera ut noden i `AdaptiveSamplingTelemetryProcessor` .

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Aktivera samplingsmodulen med fast hastighet.** Lägg till det här kodfragmentet i [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) :
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      I stället för att ange samplingsparametern `ApplicationInsights.config` i filen kan du programmässigt ange följande värden:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Läs mer om telemetriprocessorer](./api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Konfigurera fast samplingsfrekvens för ASP.NET Core-program

1. **Inaktivera adaptiv sampling:** Ändringar kan göras i `ConfigureServices` metoden med hjälp av `ApplicationInsightsServiceOptions` :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **Aktivera samplingsmodulen med fast hastighet.** Ändringar kan göras i metoden `Configure` enligt följande kodfragment:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = configuration.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Konfigurera fast samplingsfrekvens för Java-program

Som standard är ingen sampling aktiverad i Java-agenten och SDK: n. För närvarande stöder den endast fast sampling. Adaptiv sampling stöds inte i Java.

#### <a name="configuring-java-agent"></a>Konfigurera Java-agent

1. Ladda [ned applicationinsights-agent-3.0.0-PREVIEW.5.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar)

1. Om du vill aktivera sampling lägger du till följande i `applicationinsights.json` filen:

```json
{
  "sampling": {
    "percentage": 10 //this is just an example that shows you how to enable only 10% of transaction 
  }
}
```

#### <a name="configuring-java-sdk"></a>Konfigurera Java SDK

1. Ladda ned och konfigurera webbappen med den senaste [versionen Application Insights Java SDK.](./java-get-started.md)

2. **Aktivera samplingsmodulen med fast hastighet genom** att lägga till följande kodfragment i `ApplicationInsights.xml` filen:

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. Du kan inkludera eller undanta specifika typer av telemetri från sampling med hjälp av följande taggar i `Processor` taggens `FixedRateSamplingTelemetryProcessor` :
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

De telemetrityper som kan inkluderas eller undantas från sampling är: `Dependency` , , , , och `Event` `Exception` `PageView` `Request` `Trace` .

> [!NOTE]
> För samplingsprocenten väljer du en procentandel som är nära 100/N där N är ett heltal.  För närvarande har sampling inte stöd för andra värden.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Konfigurera sampling med fast hastighet för OpenCensus Python-program

Instrumentera ditt program med den senaste [OpenCensus-Azure Monitor med](./opencensus-python.md).

> [!NOTE]
> Sampling med fast frekvens är inte tillgängligt för måttexporterare. Det innebär att anpassade mått är de enda typerna av telemetri där sampling INTE kan konfigureras. Måttexportören skickar all telemetri som den spårar.

#### <a name="fixed-rate-sampling-for-tracing"></a>Sampling med fast frekvens för spårning ####
Du kan ange en `sampler` som en del av din `Tracer`-konfiguration. Om ingen explicit sampler anges `ProbabilitySampler` används som standard. `ProbabilitySampler`använder en frekvens på 1/10 000 som standard, vilket innebär att en av 10 000 begäranden skickas till Application Insights. Se nedan om du vill ange en samplingsfrekvens.

Om du vill ange samplingsfrekvensen kontrollerar du att anger en sampler med en `Tracer` samplingsfrekvens mellan 0,0 och 1,0. En samplingsfrekvens på 1,0 representerar 100 %, vilket innebär att alla dina begäranden skickas som telemetri till Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Sampling med fast frekvens för loggar ####
Du kan konfigurera sampling med fast hastighet för `AzureLogHandler` genom att ändra det `logging_sampling_rate` valfria argumentet. Om inget argument anges används samplingsfrekvensen 1,0. En samplingsfrekvens på 1,0 representerar 100 %, vilket innebär att alla dina begäranden skickas som telemetri till Application Insights.

```python
handler = AzureLogHandler(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Konfigurera sampling med fast hastighet för webbsidor med JavaScript

JavaScript-baserade webbsidor kan konfigureras för att använda Application Insights. Telemetri skickas från klientprogrammet som körs i användarens webbläsare och sidorna kan finnas på valfri server.

När du [konfigurerar dina JavaScript-baserade webbsidor för Application Insights](javascript.md)ändrar du det JavaScript-kodfragment som du får från Application Insights portalen.

> [!TIP]
> I ASP.NET-appar där JavaScript ingår hamnar kodfragmentet vanligtvis i `_Layout.cshtml` .

Infoga en rad som `samplingPercentage: 10,` före instrumenteringsnyckeln:

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

För samplingsprocenten väljer du en procentandel som är nära 100/N där N är ett heltal. För närvarande har sampling inte stöd för andra värden.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Samordna sampling på serversidan och på klientsidan

JavaScript SDK på klientsidan deltar i fast sampling tillsammans med SDK för serversidan. Instrumenterade sidor skickar endast telemetri på klientsidan från samma användare som SDK för serversidan tog beslutet att ta med i samplingen. Den här logiken är utformad för att upprätthålla integriteten för användarsessioner i program på klient- och serversidan. Därför kan du från ett visst telemetriobjekt i Application Insights hitta alla andra telemetriobjekt för den här användaren eller sessionen och i Sök, navigera mellan relaterade sidvisningar och begäranden.

Om telemetrin på klient- och serversidan inte visar koordinerade exempel:

* Kontrollera att du har aktiverat sampling både på servern och klienten.
* Kontrollera att du anger samma samplingsprocent i både klienten och servern.
* Kontrollera att SDK-versionen är 2.0 eller senare.

## <a name="ingestion-sampling"></a>Inmatningssampling

Inmatningssampling fungerar vid den punkt där telemetrin från webbservern, webbläsare och enheter når Application Insights tjänstslutpunkten. Även om det inte minskar telemetritrafiken som skickas från din app, minskar mängden bearbetade och behåller (och debiteras) av Application Insights.

Använd den här typen av sampling om din app ofta går över sin månadskvot och du inte har möjlighet att använda någon av de SDK-baserade typerna av sampling. 

Ange samplingsfrekvensen på sidan Användning och uppskattade kostnader:

![På programmets översiktsblad klickar du på Inställningar, Kvot, Exempel, väljer en samplingsfrekvens och klickar på Uppdatera.](./media/sampling/data-sampling.png)

Precis som andra typer av sampling behåller algoritmen relaterade telemetriobjekt. När du till exempel inspekterar telemetrin i Sök kan du hitta begäran som rör ett visst undantag. Måttantal som begärandefrekvens och undantagsfrekvens bevaras korrekt.

Datapunkter som tas bort med sampling är inte tillgängliga i någon funktion Application Insights till exempel [löpande export](./export-telemetry.md).

Inmatningssampling fungerar inte när adaptiv eller fast sampling används. Anpassningsbar sampling är aktiverat som standard när ASP.NET SDK eller ASP.NET Core SDK används, eller när Application Insights har aktiverats i [Azure App Service ](azure-web-apps.md) eller med hjälp av Statusövervakare. När telemetri tas emot av Application Insights-tjänstslutpunkten undersöker den telemetrin och om samplingsfrekvensen rapporteras vara mindre än 100 % (vilket indikerar att telemetri samplas) ignoreras den samplingsfrekvens för datainmatning som du anger.

> [!WARNING]
> Värdet som visas på portalpanelen anger det värde som du anger för inmatningssampling. Den representerar inte den faktiska samplingsfrekvensen om någon typ av SDK-sampling (adaptiv eller fast sampling) används.

## <a name="when-to-use-sampling&quot;></a>När du ska använda sampling

I allmänhet behöver du inte sampling för de flesta små och medelstora program. Den mest användbara diagnostikinformationen och den mest exakta statistiken hämtas genom att samla in data om alla dina användaraktiviteter. 

De främsta fördelarna med sampling är:

* Application Insights tjänsten släpper datapunkter (&quot;begränsar") när din app skickar mycket hög telemetrifrekvens under ett kort tidsintervall. Sampling minskar sannolikheten för att programmet kommer att se en begränsning.
* För att hålla [dig inom](pricing.md) kvoten för datapunkter för din prisnivå. 
* För att minska nätverkstrafiken från insamlingen av telemetri. 

### <a name="which-type-of-sampling-should-i-use"></a>Vilken typ av sampling ska jag använda?

**Använd inmatningssampling om:**

* Du använder ofta din månatliga kvot för telemetri.
* Du får för mycket telemetri från användarnas webbläsare.
* Du använder en version av SDK som inte stöder sampling, till exempel en ASP.NET tidigare versioner än 2.

**Använd fast sampling om:**

* Du vill synkroniserad sampling mellan klient och server så [](./diagnostic-search.md)att du kan navigera mellan relaterade händelser på klienten och servern när du undersöker händelser i Sök, till exempel sidvisningar och HTTP-begäranden.
* Du är säker på lämplig samplingsprocent för din app. Den bör vara tillräckligt hög för att få korrekta mått, men under den hastighet som överskrider din priskvot och begränsningsgränserna.

**Använd adaptiv sampling:**

Om villkoren för att använda de andra formerna av sampling inte gäller rekommenderar vi adaptiv sampling. Den här inställningen är aktiverad som standard i ASP.NET/ASP.NET Core SDK. Det minskar inte trafiken förrän en viss minimihastighet uppnås, och därför samplas förmodligen inte webbplatser med låg användning alls.

## <a name="knowing-whether-sampling-is-in-operation"></a>Kontrollera om sampling är aktiverat

Om du vill identifiera den faktiska samplingsfrekvensen oavsett var den har tillämpats använder du en [Analytics-fråga](../logs/log-query-overview.md) som den här:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Om du ser `RetainedPercentage` att för någon typ är mindre än 100 samplas den typen av telemetri.

> [!IMPORTANT]
> Application Insights exempelsession, mått (inklusive anpassade mått) eller telemetrityper för prestandaräknare i någon av samplingsteknikerna. Dessa typer undantas alltid från sampling eftersom en minskning av precisionen kan vara mycket oönskade för dessa telemetrityper.

## <a name="how-sampling-works"></a>Så här fungerar sampling

Samplingsalgoritmen avgör vilka telemetriobjekt som ska tas bort och vilka som ska behållas. Detta gäller oavsett om sampling görs av SDK eller i Application Insights tjänsten. Samplingsbeslutet baseras på flera regler som syftar till att bevara alla relaterade datapunkter intakta, vilket upprätthåller en diagnostisk upplevelse i Application Insights som är användbar och tillförlitlig även med en minskad datauppsättning. Om din app till exempel har en misslyckad begäran i ett exempel bevaras de ytterligare telemetriobjekten (till exempel undantag och spårningar som loggas för den här begäran). Samplingen håller eller släpper alla tillsammans. När du tittar på begärandeinformationen i Application Insights kan du därför alltid se begäran tillsammans med dess associerade telemetriobjekt.

Samplingsbeslutet baseras på begärans åtgärds-ID, vilket innebär att alla telemetriobjekt som tillhör en viss åtgärd bevaras eller tas bort. För telemetriobjekt som inte har en åtgärds-ID-uppsättning (till exempel telemetriobjekt som rapporteras från asynkrona trådar utan HTTP-kontext) samlar sampling bara in en procentandel telemetriobjekt av varje typ.

När du presenterar telemetri igen justerar Application Insights-tjänsten måtten med samma samplingsprocent som användes vid insamlingen för att kompensera för saknade datapunkter. När man tittar på telemetrin i Application Insights ser användarna statistiskt korrekta approximeringar som ligger mycket nära de verkliga talen.

Noggrannheten för uppskattningen beror till stor del på den konfigurerade samplingsprocenten. Dessutom ökar noggrannheten för program som hanterar en stor mängd vanligtvis liknande begäranden från många användare. Å andra sidan behövs inte sampling för program som inte fungerar med en betydande belastning, eftersom dessa program vanligtvis kan skicka all telemetri samtidigt som de håller sig inom kvoten, utan att orsaka dataförlust från begränsning. 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

*Vad är standardbeteendet för sampling i ASP.NET och ASP.NET Core-SDK:er?*

* Om du använder någon av de senaste versionerna av ovanstående SDK är adaptiv sampling aktiverat som standard med fem telemetriobjekt per sekund.
  Två noder läggs `AdaptiveSamplingTelemetryProcessor` till som standard och en inkluderar typen i `Event` sampling, medan den andra utesluter `Event` typen från sampling. Den här konfigurationen innebär att SDK försöker begränsa telemetriobjekt till fem typer av telemetriobjekt och fem telemetriobjekt av alla andra typer tillsammans, vilket säkerställer att samplas separat från andra `Event` `Events` telemetrityper. Händelser används vanligtvis för affärstelemetri och bör troligen inte påverkas av diagnostiktelemetrivolymer.
  
  Nedan visas den genererade `ApplicationInsights.config` standardfilen. I ASP.NET Core är samma standardbeteende aktiverat i koden. Använd [exemplen i det tidigare avsnittet på den här sidan för att](#configuring-adaptive-sampling-for-aspnet-core-applications) ändra det här standardbeteendet.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*Kan telemetri samplas mer än en gång?*

* Nej. SamplingTelemetryProcessors ignorerar objekt från samplingsöverväganden om objektet redan samplas. Samma sak gäller även för inmatningssampling, som inte tillämpar sampling på de objekt som redan samplats i själva SDK:n.

*Varför är inte sampling en enkel "samla in X procent av varje telemetrityp"?*

* Den här samplingsmetoden skulle ge en hög precision i måtttimeringar, men den skulle bryta möjligheten att korrelera diagnostikdata per användare, session och begäran, vilket är kritiskt för diagnostik. Därför fungerar sampling bättre med principer som "samla in alla telemetriobjekt för X procent av appanvändarna" eller "samla in all telemetri för X procent av appbegäranden". För telemetriobjekt som inte är associerade med begäranden (till exempel asynkron bearbetning i bakgrunden) är återställningen att "samla in X procent av alla objekt för varje telemetrityp". 

*Kan samplingsprocenten ändras över tid?*

* Ja, adaptiv sampling ändrar gradvis samplingsprocenten, baserat på den aktuella volymen av telemetrin.

*Hur vet jag vilken samplingsprocent som fungerar bäst för min app om jag använder sampling med fast frekvens?*

* Ett sätt är att börja med adaptiv sampling, ta reda på vilken frekvens den regleras på (se ovanstående fråga) och sedan växla till fast sampling med den frekvensen. 
  
    Annars måste du gissa dig fram. Analysera din aktuella telemetrianvändning i Application Insights, observera eventuella begränsningar som inträffar och beräkna volymen för den insamlade telemetrin. Dessa tre indata tillsammans med din valda prisnivå föreslår hur mycket du kanske vill minska volymen för den insamlade telemetrin. En ökning av antalet användare eller någon annan förändring i mängden telemetri kan dock göra uppskattningen ogiltig.

*Vad händer om jag konfigurerar samplingsprocenten så att den är för låg?*

* För låga samplingsprocent orsakar över aggressiv sampling och minskar noggrannheten för approximeringarna när Application Insights försöker kompensera för datavisualiseringen för datavolymminskningen. Även din diagnostiska upplevelse kan påverkas negativt, eftersom vissa av de misslyckade eller långsamma begärandena sällan samplas ut.

*Vad händer om jag konfigurerar samplingsprocenten så att den är för hög?*

* Om du konfigurerar för hög samplingsprocent (inte tillräckligt aggressivt) minskar inte volymen för den insamlade telemetrin. Du kan fortfarande uppleva förlust av telemetridata på grund av begränsning och kostnaden för att använda Application Insights kan vara högre än vad du planerat på grund av överkostnader.

*På vilka plattformar kan jag använda sampling?*

* Inmatningssampling kan ske automatiskt för alla telemetri över en viss volym, om SDK:n inte utför sampling. Den här konfigurationen skulle till exempel fungera om du använder en äldre version av ASP.NET SDK eller Java SDK.
* Om du använder den aktuella ASP.NET- eller ASP.NET Core-SDK:er (som finns i Azure eller på din egen server) får du adaptiv sampling som standard, men du kan växla till fast hastighet enligt beskrivningen ovan. Med fast sampling synkroniserar webbläsar-SDK automatiskt till exempelrelaterade händelser. 
* Om du använder den aktuella Java-agenten kan du konfigurera (för Java SDK, konfigurera ) för `applicationinsights.json` `ApplicationInsights.xml` att aktivera fast sampling. Sampling är inaktiverat som standard. Med fast sampling synkroniserar webbläsar-SDK och servern automatiskt till exempelrelaterade händelser.

*Det finns vissa sällsynta händelser som jag alltid vill se. Hur kan jag få dem att komma förbi samplingsmodulen?*

* Det bästa sättet att uppnå detta är att skriva en anpassad [TelemetryInitializer](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer), som anger till 100 för det telemetriobjekt som du vill `SamplingPercentage` behålla, enligt nedan. Eftersom initierare garanterat körs före telemetriprocessorer (inklusive sampling), säkerställer detta att alla samplingstekniker ignorerar det här objektet från eventuella samplingsöverväganden. Anpassade telemetriinitierare är tillgängliga i ASP.NET SDK, ASP.NET Core SDK, JavaScript SDK och Java SDK. Du kan till exempel konfigurera en telemetriinitiering med hjälp av ASP.NET SDK:

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>Äldre SDK-versioner

Adaptiv sampling är tillgänglig för Application Insights SDK för ASP.NET v2.0.0-beta3 och senare, Microsoft.ApplicationInsights.AspNetCore SDK v2.2.0-beta1 och senare, och är aktiverat som standard.

Sampling med fast hastighet är en funktion i SDK:n i ASP.NET versionerna 2.0.0 och Java SDK version 2.0.1 och senare.

Före v2.5.0-beta2 av ASP.NET SDK och v2.2.0-beta3 av ASP.NET Core SDK baserades samplingsbeslutet på hashen för användar-ID:t för program som definierar "användare" (det vill säga de vanligaste webbprogrammen). För de typer av program som inte har definierat användare (till exempel webbtjänster) baserades samplingsbeslutet på åtgärds-ID:t för begäran. De senaste versionerna ASP.NET och ASP.NET Core-SDK:er använder åtgärds-ID:t för samplingsbeslutet.

## <a name="next-steps"></a>Nästa steg

* [Filtrering](./api-filtering-sampling.md) kan ge mer strikt kontroll över vad sdk:n skickar.
* Läs artikeln Om utvecklarnätverk [optimera telemetri med Application Insights](/archive/msdn-magazine/2017/may/devops-optimize-telemetry-with-application-insights).

