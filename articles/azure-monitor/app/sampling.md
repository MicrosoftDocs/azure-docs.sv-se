---
title: Telemetri-sampling i Azure Application Insights | Microsoft Docs
description: Hur du behåller volymen av telemetri under kontroll.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: bb6793bc1e3d5bb55426c1f344520ae19a22a9f9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549573"
---
# <a name="sampling-in-application-insights"></a>Sampling i Application Insights

Sampling är en funktion i [Azure Application insikter](./app-insights-overview.md). Det är det rekommenderade sättet att minska telemetri-trafik, data kostnader och lagrings kostnader, samtidigt som en statistisk korrekt analys av program data bevaras. Genom att använda sampling kan du också undvika Application Insights begränsning av din telemetri. Samplings filtret väljer objekt som är relaterade, så att du kan navigera mellan objekt när du gör diagnostiska undersökningar.

När antalet mått visas i portalen, är de omnormaliserade för att ta hänsyn till konto insamlingen. Detta minimerar eventuell påverkan på statistiken.

## <a name="brief-summary"></a>Kort sammanfattning

* Det finns tre olika typer av sampling: anpassningsbar sampling, sampling med fast pris och insamling av inmatningar.
* Adaptiv sampling är aktiverat som standard i alla de senaste versionerna av Application Insights ASP.NET och ASP.NET Core Software Development Kits (SDK: er). Den används också av [Azure Functions](../../azure-functions/functions-overview.md).
* Fast pris sampling är tillgängligt i de senaste versionerna av Application Insights SDK: er för ASP.NET, ASP.NET Core, Java (både agenten och SDK) och python.
* Provtagnings samplingen fungerar på Application Insights tjänstens slut punkt. Den gäller endast när ingen annan sampling tillämpas. Om SDK-exemplen för telemetri inaktive ras inmatnings sampling.
* För webb program, om du loggar anpassade händelser och måste se till att en uppsättning händelser behålls eller ignoreras tillsammans, måste händelserna ha samma `OperationId` värde.
* Om du skriver analys frågor bör du [ta hänsyn till sampling](../log-query/aggregations.md). I stället för att helt enkelt räkna poster bör du använda `summarize sum(itemCount)` .
* Vissa typer av telemetri, inklusive prestanda mått och anpassade mått, hålls alltid oavsett om sampling är aktiverat eller inte.

I följande tabell sammanfattas de samplings typer som är tillgängliga för varje SDK och typ av program:

| Application Insights SDK | Anpassad sampling stöds | Stöd för fast pris insamling | Inmatnings sampling stöds |
|-|-|-|-|
| ASP.NET | [Ja (aktiverat som standard)](#configuring-adaptive-sampling-for-aspnet-applications) | [Ja](#configuring-fixed-rate-sampling-for-aspnet-applications) | Endast om ingen annan sampling gäller |
| ASP.NET Core | [Ja (aktiverat som standard)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Ja](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Endast om ingen annan sampling gäller |
| Azure Functions | [Ja (aktiverat som standard)](#configuring-adaptive-sampling-for-azure-functions) | No | Endast om ingen annan sampling gäller |
| Java | No | [Ja](#configuring-fixed-rate-sampling-for-java-applications) | Endast om ingen annan sampling gäller |
| Node.JS | No | [Ja](./nodejs.md#sampling) | Endast om ingen annan sampling gäller
| Python | No | [Ja](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Endast om ingen annan sampling gäller |
| Alla andra | Nej | Nej | [Ja](#ingestion-sampling) |

> [!NOTE]
> Informationen på de flesta av den här sidan gäller för de aktuella versionerna av Application Insights SDK: er. Mer information om äldre versioner av SDK: er [finns i avsnittet nedan](#older-sdk-versions).

## <a name="types-of-sampling"></a>Typer av sampling

Det finns tre olika samplings metoder:

* **Adaptiv sampling** justerar automatiskt hur många telemetri som skickas från SDK: n i ASP.net/ASP.net Core-appen och från Azure Functions. Det här är standard samplingen när du använder ASP.NET eller ASP.NET Core SDK. Adaptiv sampling är för närvarande endast tillgängligt för ASP.NET på Server sidan och för Azure Functions.

* Med **fast pris sampling** minskar mängden telemetri som skickas från både din ASP.NET eller ASP.net Core eller Java Server och från användarnas webbläsare. Du ställer in priset. Klienten och servern synkroniserar sin sampling så att du i sökningen kan navigera mellan relaterade sidvyer och begär Anden.

* **Provtagnings sampling** sker i Application Insights tjänstens slut punkt. Den tar bort vissa av Telemetrin som kommer från din app, enligt en samplings frekvens som du anger. Det minskar inte telemetri trafik som skickas från din app, men hjälper dig att hålla dig inom din månads kvot. Den största fördelen med inmatnings sampling är att du kan ange samplings frekvensen utan att distribuera om appen. Provtagnings samplingen fungerar enhetligt för alla servrar och klienter, men gäller inte när någon annan typ av sampling är i drift.

> [!IMPORTANT]
> Om anpassningsbara eller fasta samplings metoder är i drift inaktive ras inmatnings sampling.

## <a name="adaptive-sampling"></a>Anpassningsbar sampling

Adaptiv sampling påverkar hur många telemetri som skickas från din webbapp till Application Insights tjänstens slut punkt.

> [!TIP]
> Adaptiva samplingar är aktiverat som standard när du använder ASP.NET SDK eller ASP.NET Core SDK, och är också aktiverat som standard för Azure Functions.

Volymen justeras automatiskt för att hållas inom en angiven högsta trafik hastighet och styrs via inställningen `MaxTelemetryItemsPerSecond` . Om programmet genererar en liten mängd telemetri, till exempel vid fel sökning eller på grund av låg användning, kommer objekten inte att släppas av samplings processorn så länge volymen är lägre `MaxTelemetryItemsPerSecond` . När mängden telemetri ökar justeras samplings frekvensen så att mål volymen uppnås. Justeringen omberäknas med jämna mellanrum och baseras på ett glidande medelvärde för utgående överföringshastigheten.

För att uppnå mål volymen ignoreras en del av den genererade Telemetrin. Men som andra typer av sampling behåller algoritmen relaterade telemetri objekt. När du till exempel inspekterar Telemetrin i sökningen kommer du att kunna hitta begäran som är relaterad till ett visst undantag.

Mått, till exempel begär ande frekvens och undantags frekvens justeras för att kompensera för samplings frekvensen, så att de visar ungefär rätt värden i Metric Explorer.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Konfigurera adaptiv sampling för ASP.NET-program

> [!NOTE]
> Det här avsnittet gäller för ASP.NET-program, inte för att ASP.NET Core program. [Läs mer om hur du konfigurerar adaptiv sampling för ASP.NET Core program senare i det här dokumentet.](#configuring-adaptive-sampling-for-aspnet-core-applications)

I [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) kan du justera flera parametrar i `AdaptiveSamplingTelemetryProcessor` noden. De siffror som visas är standardvärden:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Mål frekvensen för [logiska åtgärder](./correlation.md#data-model-for-telemetry-correlation) som den anpassningsbara algoritmen kan samla in **på varje server värd**. Om din webbapp körs på många värdar kan du minska det här värdet så att det ligger kvar i trafik hastigheten på Application Insights portalen.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Intervallet då den aktuella takten för telemetri utvärderas igen. Utvärderingen utförs som ett glidande medelvärde. Du kanske vill förkorta det här intervallet om din telemetri är ansvarig för plötsliga burst-överföring.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    När samplings procentens värde ändras, hur lång tid det tar att minska samplings procenten igen för att samla in mindre data?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    När samplings procentens värde ändras, hur snart vi har tillåtit att öka samplings procenten igen för att samla in mer data?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Som samplings procent varierar, vilket är det minsta värde som vi har tillåtelse att ange?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Som samplings procent varierar, vilket är det högsta tillåtna värdet som vi kan ställa in?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Vid beräkningen av glidande medelvärde anger detta den vikt som ska tilldelas det senaste värdet. Använd ett värde som är lika med eller mindre än 1. Lägre värden gör algoritmen mindre aktiv till plötsliga ändringar.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Mängden telemetri som ska samplas när appen precis har startats. Minska inte det här värdet när du felsöker.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    En semikolonavgränsad lista med typer som du inte vill ska omfattas av sampling. Godkända typer är: `Dependency` , `Event` ,,, `Exception` `PageView` `Request` , `Trace` . All telemetri av de angivna typerna överförs. de typer som inte anges kommer att samplas.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    En semikolonavgränsad lista med typer som du vill ska omfattas av sampling. Godkända typer är: `Dependency` , `Event` ,,, `Exception` `PageView` `Request` , `Trace` . De angivna typerna kommer att samplas; all telemetri av andra typer skickas alltid.

**Om du vill stänga av** adaptiv sampling tar du bort `AdaptiveSamplingTelemetryProcessor` noden (-erna) från `ApplicationInsights.config` .

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativ: Konfigurera adaptiv sampling i kod

I stället för att ställa in parametern sampling i `.config` filen kan du konfigurera dessa värden program mässigt.

1. Ta bort alla `AdaptiveSamplingTelemetryProcessor` noder från `.config` filen.
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

    ([Läs mer om telemetri-processorer](./api-filtering-sampling.md#filtering).)

Du kan också justera samplings frekvensen för varje typ av telemetri individuellt, eller så kan du till och med undanta vissa typer från att samplas över huvud taget:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Konfigurera adaptiv sampling för ASP.NET Core program

Det finns inget `ApplicationInsights.config` för ASP.net Core program, så all konfiguration görs via kod.
Adaptiv sampling är aktiverat som standard för alla ASP.NET Core-program. Du kan inaktivera eller anpassa samplings beteendet.

#### <a name="turning-off-adaptive-sampling"></a>Stänga av adaptiv sampling

Standard samplings funktionen kan inaktive ras när du lägger till Application Insights tjänst i-metoden `ConfigureServices` med hjälp av `ApplicationInsightsServiceOptions` i `Startup.cs` filen:

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

Koden ovan kommer att inaktivera anpassad sampling. Följ stegen nedan för att lägga till sampling med fler anpassnings alternativ.

#### <a name="configure-sampling-settings"></a>Konfigurera inställningar för sampling

Använd tilläggs metoder `TelemetryProcessorChainBuilder` som visas nedan för att anpassa samplings beteendet.

> [!IMPORTANT]
> Om du använder den här metoden för att konfigurera sampling, se till att ange `aiOptions.EnableAdaptiveSampling` egenskapen till `false` vid anrop `AddApplicationInsightsTelemetry()` .

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

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Konfigurera adaptiv sampling för Azure Functions

Följ anvisningarna på [den här sidan](../../azure-functions/functions-monitoring.md#configure-sampling) om du vill konfigurera adaptiv sampling för appar som körs i Azure Functions.

## <a name="fixed-rate-sampling"></a>Sampling med fast pris

Med fast pris sampling minskar trafiken som skickas från webb servern och webbläsare. Till skillnad från adaptiv sampling minskar den Telemetrin till en fast pris kontroll. Fast pris sampling är tillgängligt för ASP.NET-, ASP.NET Core-, Java-och python-program.

Precis som andra samplings tekniker behåller detta även relaterade objekt. Den synkroniserar också klient-och Server samplingen så att relaterade objekt bevaras, till exempel när du tittar på en sidvy i sökningen kan du hitta dess relaterade server begär Anden. 

I Metrics Explorer multipliceras priser som begäran och undantags antal med en faktor för att kompensera för samplings frekvensen, så att de är ungefär korrekta.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Konfigurera fast priss insamling för ASP.NET-program

1. **Inaktivera adaptiv sampling**: i [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) , ta bort eller kommentera ut `AdaptiveSamplingTelemetryProcessor` noden.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Aktivera samplings-modulen för fast pris.** Lägg till det här kodfragmentet i [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) :
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      Alternativt kan du, i stället för att ange parametern för att `ApplicationInsights.config` Ange i filen, ange följande värden program mässigt:

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

    ([Läs mer om telemetri-processorer](./api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Konfigurera fast pris sampling för ASP.NET Core program

1. **Inaktivera adaptiv sampling**: ändringar kan göras i `ConfigureServices` -metoden med hjälp av `ApplicationInsightsServiceOptions` :

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

2. **Aktivera samplings-modulen för fast pris.** Ändringar kan göras i- `Configure` metoden som visas i följande kodfragment:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Konfigurera sampling med fast pris för Java-program

Som standard är ingen sampling aktive rad i Java-agenten och SDK. För närvarande stöder den bara fast pris sampling. Anpassad sampling stöds inte i Java.

#### <a name="configuring-java-agent"></a>Konfigurera Java-agent

1. Ladda ned [applicationinsights-agent-3.0.0-Preview. 5. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar)

1. Om du vill aktivera sampling lägger du till följande i `ApplicationInsights.json` filen:

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10 //this is just an example that shows you how to enable only only 10% of transaction 
        }
      }
    }
  }
}
```

#### <a name="configuring-java-sdk"></a>Konfigurera Java SDK

1. Hämta och konfigurera ditt webb program med de senaste [Application Insights Java SDK: n](./java-get-started.md).

2. **Aktivera samplings-modulen med fast pris** genom att lägga till följande kodfragment till `ApplicationInsights.xml` filen:

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

3. Du kan inkludera eller exkludera vissa typer av telemetri från sampling med hjälp av följande Taggar i `Processor` taggen `FixedRateSamplingTelemetryProcessor` :
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

De typer av telemetri som kan tas med eller undantas från sampling är: `Dependency` ,,,, `Event` `Exception` `PageView` `Request` och `Trace` .

> [!NOTE]
> För samplings procenten väljer du en procents ATS som ligger nära 100/N där N är ett heltal.  För närvarande stöder sampling inte andra värden.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Konfigurera fast pris sampling för python-program för openräkning

Instrumentera ditt program med de senaste [Azure Monitor exportörerna för openräkning](./opencensus-python.md).

> [!NOTE]
> Fast priss insamling är inte tillgängligt för export verktyget för mått. Det innebär att anpassade mått är de enda typerna av telemetri där sampling inte kan konfigureras. Mått export verktyget skickar all telemetri som den spårar.

#### <a name="fixed-rate-sampling-for-tracing"></a>Fast pris sampling för spårning ####
Du kan ange en `sampler` som en del av din `Tracer`-konfiguration. Om inget explicit sampler anges `ProbabilitySampler` används som standard. `ProbabilitySampler`Använder en frekvens på 1/10000 som standard, vilket innebär att en av varje 10000-begäranden skickas till Application Insights. Se nedan om du vill ange en samplingsfrekvens.

Om du vill ange samplings frekvensen måste du se till att `Tracer` Ange ett exempel med en samplings frekvens på mellan 0,0 och 1,0. En samplings frekvens på 1,0 representerar 100%, vilket innebär att alla dina begär Anden skickas som telemetri till Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Fast pris sampling för loggar ####
Du kan konfigurera sampling med fast pris `AzureLogHandler` genom att ändra det `logging_sampling_rate` valfria argumentet. Om inget argument anges används en samplings frekvens på 1,0. En samplings frekvens på 1,0 representerar 100%, vilket innebär att alla dina begär Anden skickas som telemetri till Application Insights.

```python
handler = AzureLogHandler(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Konfigurera fast priss insamling för webb sidor med Java Script

JavaScript-baserade webb sidor kan konfigureras att använda Application Insights. Telemetri skickas från klient programmet som körs i användarens webbläsare och sidorna kan hämtas från vilken server som helst.

När du [konfigurerar dina JavaScript-baserade webb sidor för Application Insights](javascript.md)ändrar du JavaScript-kodfragmentet som du får från Application Insights Portal.

> [!TIP]
> I ASP.NET-appar med Java Script ingår vanligt vis kod avsnittet `_Layout.cshtml` .

Infoga en rad som `samplingPercentage: 10,` före Instrumentation-tangenten:

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

För samplings procenten väljer du en procents ATS som ligger nära 100/N där N är ett heltal. För närvarande stöder sampling inte andra värden.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Koordinera insamling på Server sidan och på klient Sidan

JavaScript SDK för klient sidan ingår i fast priss insamling tillsammans med SDK för Server sidan. De instrumenterade sidorna skickar endast telemetri på klient sidan från samma användare som SDK för Server sidan gjorde sitt beslut att inkludera i samplingen. Den här logiken är utformad för att upprätthålla integriteten för användarsessioner över klient-och serverbaserade program. Som ett resultat av ett visst telemetri-objekt i Application Insights kan du hitta alla andra telemetridata för den här användaren eller sessionen och i Sök kan du navigera mellan relaterade sidvyer och begär Anden.

Om din klient och telemetri på Server sidan inte visar koordinerade exempel:

* Kontrol lera att du har aktiverat sampling både på servern och klienten.
* Kontrol lera att du anger samma samplings procent i både klienten och servern.
* Kontrol lera att SDK-versionen är 2,0 eller högre.

## <a name="ingestion-sampling"></a>Inmatnings sampling

Provtagnings samplingen fungerar vid den punkt där telemetri från din webb server, webbläsare och enheter når Application Insights tjänstens slut punkt. Även om den inte minskar telemetri trafiken som skickas från din app, minskar den mängden bearbetad och bevaras (och debiteras för) av Application Insights.

Använd den här typen av sampling om din app ofta går över sin månads kvot och du inte har möjlighet att använda någon av SDK-baserade typer av sampling. 

Ange samplings frekvensen på sidan användning och uppskattade kostnader:

![Från bladet översikt över program klickar du på Inställningar, kvot, exempel och väljer sedan en samplings frekvens och klickar på Uppdatera.](./media/sampling/data-sampling.png)

I likhet med andra typer av sampling behåller algoritmen relaterade telemetri objekt. När du till exempel inspekterar Telemetrin i sökningen kommer du att kunna hitta begäran som är relaterad till ett visst undantag. Mått, till exempel begär ande frekvens och undantags frekvens, bevaras korrekt.

Data punkter som tas bort från sampling är inte tillgängliga i någon Application Insights funktion som [kontinuerlig export](./export-telemetry.md).

Provtagnings samplingen fungerar inte medan adaptiv eller fast pris sampling är i drift. Adaptiv sampling är aktiverat som standard när ASP.NET SDK eller ASP.NET Core SDK används, eller när Application Insights har Aktiver ATS i [Azure App Service ](azure-web-apps.md) eller med hjälp av statusövervakare. När telemetri tas emot av Application Insights tjänstens slut punkt, undersöker den Telemetrin och om samplings frekvensen rapporteras vara mindre än 100% (vilket betyder att telemetri samplas), ignoreras den inmatnings samplings frekvens som du har angett.

> [!WARNING]
> Värdet som visas på Portal panelen visar det värde som du angav för inmatnings sampling. Den representerar inte den faktiska samplings frekvensen om någon form av SDK-sampling (anpassningsbar eller fast pris sampling) är i drift.

## <a name="when-to-use-sampling"></a>När du ska använda sampling

I allmänhet för de flesta små och medel stora program behöver du inte använda sampling. Den mest användbara diagnostikinformation och den mest exakta statistiken erhålls genom att samla in data för alla dina användar aktiviteter. 

De största fördelarna med sampling är:

* Application Insights tjänst släpper ("begränsning") data punkter när din app skickar en mycket hög telemetri i ett kort tidsintervall. Sampling minskar sannolikheten för att programmet ska se begränsningen.
* För att hålla dig inom [kvoten](pricing.md) för data punkter för din pris nivå. 
* För att minska nätverks trafiken från insamling av telemetri. 

### <a name="which-type-of-sampling-should-i-use"></a>Vilken typ av sampling ska jag använda?

**Använd inmatnings sampling om:**

* Du använder ofta din månads kvot för telemetri.
* Du får för mycket telemetri från användarnas webbläsare.
* Du använder en version av SDK som inte stöder sampling, t. ex. ASP.NET versioner som är äldre än 2.

**Använd sampling med fast pris om:**

* Du vill ha synkroniserad sampling mellan klienten och servern så att när du undersöker händelser i [sökningen](./diagnostic-search.md)kan du navigera mellan relaterade händelser på klienten och servern, till exempel sidvyer och HTTP-begäranden.
* Du är säker på rätt samplings procent för din app. Det bör vara tillräckligt högt för att få korrekta mått, men under den hastighet som överskrider din prissättnings kvot och begränsnings gränser.

**Använd anpassningsbar sampling:**

Om villkoren för att använda andra former av sampling inte gäller rekommenderar vi anpassad sampling. Den här inställningen är aktive rad som standard i ASP.NET/ASP.NET Core SDK. Det minskar inte trafiken förrän en viss minimi hastighet uppnås, därför kommer webbplatser med låg användning troligen inte att samplas över huvud taget.

## <a name="knowing-whether-sampling-is-in-operation"></a>Att veta om sampling är i drift

Om du vill identifiera den faktiska samplings frekvensen oavsett var den har använts använder du en [analys fråga](../log-query/log-query-overview.md) som detta:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Om du ser att `RetainedPercentage` för någon typ är mindre än 100 samplas den typen av telemetri.

> [!IMPORTANT]
> Application Insights inte samplar session, mått (inklusive anpassade mått) eller typer av telemetri för prestanda räknare i någon av samplings teknikerna. Dessa typer är alltid exkluderade från sampling eftersom en minskning av precisionen kan vara mycket olämplig för dessa typer av telemetri.

## <a name="how-sampling-works"></a>Så här fungerar sampling

Samplings algoritmen bestämmer vilka telemetri-objekt som ska släppas och vilka som ska behållas. Detta är sant om sampling görs av SDK eller i Application Insights tjänsten. Samplings beslutet baseras på flera regler som syftar till att bevara alla samrelaterade data punkter, vilket bibehåller en diagnostisk upplevelse i Application Insights som är åtgärds bara och tillförlitliga, även med en reducerad data uppsättning. Om din app till exempel har en misslyckad begäran som ingår i ett exempel, kommer ytterligare telemetri-objekt (till exempel undantag och spår som loggats för denna begäran) att bevaras. Provtagningen behåller eller släpper ihop dem tillsammans. När du tittar på förfrågnings informationen i Application Insights kan du alltid se begäran tillsammans med tillhör ande mallobjekt.

Samplings beslutet baseras på åtgärds-ID för begäran, vilket innebär att alla telemetridata som tillhör en viss åtgärd antingen bevaras eller tas bort. För telemetri-objekt som inte har något angivet åtgärds-ID (t. ex. telemetri-objekt som rapporter ATS från asynkrona trådar med ingen HTTP-kontext) fångar bara en procent andel telemetri objekt av varje typ.

När du presenterar telemetri tillbaka till dig justerar Application Insights-tjänsten måtten efter samma samplings procent som användes vid tidpunkten för samlingen, för att kompensera för de data punkter som saknas. När du tittar på Telemetrin i Application Insights, ser användarna då statistiskt korrekta uppskattningar som är mycket nära de reella talen.

Noggrannheten i uppskattningen beror i stor utsträckning på den konfigurerade samplings procenten. Dessutom ökar noggrannheten för program som hanterar en stor mängd av allmänt förekommande liknande förfrågningar från många användare. För program som inte fungerar med en betydande belastning behövs dock inte samplingen eftersom dessa program normalt kan skicka all telemetri samtidigt som de ligger inom kvoten, utan att orsaka data förlust från begränsning. 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

*Vad är standard samplings beteendet i ASP.NET och ASP.NET Core SDK: er?*

* Om du använder någon av de senaste versionerna av ovanstående SDK, aktive ras adaptiv sampling som standard med fem telemetri objekt per sekund.
  Det finns två `AdaptiveSamplingTelemetryProcessor` noder som läggs till som standard och en innehåller `Event` typen i sampling, medan den andra utesluter `Event` typen från sampling. Den här konfigurationen innebär att SDK försöker begränsa telemetri-objekt till fem typer av telemetri `Event` , och fem telemetridata av alla andra typer, vilket säkerställer att `Events` de samplas separat från andra typer av telemetri. Händelser används vanligt vis för affärstelemetri, och de mest sannolika bör inte påverkas av diagnostiska telemetri volymer.
  
  Följande visar standard filen som `ApplicationInsights.config` skapas. I ASP.NET Core är samma standard beteende aktive rad i kod. Använd [exemplen i det tidigare avsnittet av den här sidan](#configuring-adaptive-sampling-for-aspnet-core-applications) för att ändra det här standard beteendet.

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

* Nej. SamplingTelemetryProcessors ignorerar objekt från samplings överväganden om objektet redan har testats. Samma sak gäller för insamlings sampling, som inte använder sampling för de objekt som redan har tagits med i själva SDK: n.

*Varför är det inte att sampla en enkel "samla in X procent av varje typ av telemetri"?*

* Även om den här samplings metoden ger en hög precisions nivå i mått approximationer skulle det brytas möjligheten att korrelera diagnostikdata per användare, session och begäran, vilket är kritiskt för diagnostik. Därför fungerar samplingen bättre med principer som "samla in alla telemetridata för X procent av App-användare" eller "samla in all telemetri för X procent av app-begäranden". För telemetri-objekten som inte är associerade med begär Anden (till exempel bakgrunds asynkron bearbetning) är återställningen till "samla in X procent av alla objekt för varje typ av telemetri". 

*Kan samplings procent förändringen över tid?*

* Ja, anpassningsbar sampling ändrar gradvis samplings procent baserat på den aktuella observerade volymen för Telemetrin.

*Hur vet jag vilken samplings procent som fungerar bäst för min app om jag använder sampling med fast pris.*

* Ett sätt är att börja med anpassningsbara samplingar, ta reda på vilken taxa den har påbörjat (se ovanstående fråga) och växla sedan till fast pris sampling med denna hastighet. 
  
    Annars måste du gissa. Analysera den aktuella telemetri användningen i Application Insights, Observera eventuella begränsningar som inträffar och beräkna volymen för den insamlade telemetri. Dessa tre indata, tillsammans med den valda pris nivån, föreslår hur mycket du kanske vill minska mängden insamlad telemetri. En ökning av antalet användare eller någon annan Skift i volymen av telemetri kan dock ogiltig förklara beräkningen.

*Vad händer om jag konfigurerar samplings procenten så att den är för låg?*

* Alltför låg andel samplings procent ger över aggressiva samplingar och minskar noggrannheten i de ungefärliger som Application Insights försöker kompensera visualiseringen för data volymens minskning. Den diagnostiska upplevelsen kan också påverkas negativt, eftersom vissa av de misslyckade eller långsamma begär Anden kan samplas ut.

*Vad händer om jag konfigurerar samplings procenten så att den är för hög?*

* Om du konfigurerar för högt en samplings procent (inte tillräckligt aggressiv) resulterar det i otillräcklig minskning av den insamlade telemetri-volymen. Du kan fortfarande uppleva telemetri data förlust relaterat till begränsning, och kostnaden för att använda Application Insights kan vara högre än vad du har planerat på grund av överutnyttjade avgifter.

*På vilka plattformar kan jag använda sampling?*

* Provtagnings sampling kan ske automatiskt för en telemetri över en viss volym, om SDK: n inte utför sampling. Den här konfigurationen fungerar, till exempel om du använder en äldre version av ASP.NET SDK eller Java SDK.
* Om du använder den aktuella ASP.NET eller ASP.NET Core SDK: er (som finns i Azure eller på din egen server) får du anpassningsbara samplingar som standard, men du kan växla till fast pris enligt beskrivningen ovan. Med fast Rate-sampling synkroniserar webbläsarens SDK automatiskt till exempel relaterade händelser. 
* Om du använder den aktuella Java-agenten kan du konfigurera `ApplicationInsights.json` (för Java SDK, konfigurera `ApplicationInsights.xml` ) för att aktivera fast priss insamling. Sampling är inaktive rad som standard. Med fast Rate-sampling, synkroniserar webbläsarens SDK och servern automatiskt exempel på relaterade händelser.

*Det finns vissa sällsynta händelser som jag alltid vill se. Hur kan jag få dem förbi samplings modulen?*

* Det bästa sättet att åstadkomma detta är att skriva en anpassad [TelemetryInitializer](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer), som ställer in `SamplingPercentage` till 100 på det telemetridata som du vill ha kvar, enligt bilden nedan. Eftersom initierare är garanterat att köras före telemetri-processorer (inklusive sampling) ser detta till att alla samplings tekniker kommer att ignorera det här objektet från alla exempel på att tänka på. Anpassade telemetri-initierare är tillgängliga i ASP.NET SDK, ASP.NET Core SDK, Java Script SDK och Java SDK. Du kan till exempel konfigurera en telemetri-initierare med hjälp av ASP.NET SDK:

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

Adaptiv sampling är tillgängligt för Application Insights SDK för ASP.NET v 2.0.0-beta3 och senare, Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0-beta1 och senare, och är aktiverat som standard.

Sampling med fast pris är en funktion i SDK i ASP.NET-versioner från 2.0.0 och Java SDK version 2.0.1 och senare.

Före v-2.5.0-beta2 av ASP.NET SDK och v 2.2.0-beta3 för ASP.NET Core SDK baserades samplings beslutet på hashen för användar-ID: t för program som definierar "användare" (det vill säga de flesta vanliga webb program). För de typer av program som inte definierade användare (t. ex. webb tjänster) var samplings beslutet baserat på åtgärds-ID för begäran. De senaste versionerna av ASP.NET och ASP.NET Core SDK: er använder åtgärds-ID: t för samplings beslutet.

## <a name="next-steps"></a>Nästa steg

* [Filtrering](./api-filtering-sampling.md) kan ge mer strikt kontroll över vad din SDK skickar.
* Läs artikeln om Developer Network [optimera telemetri med Application Insights](/archive/msdn-magazine/2017/may/devops-optimize-telemetry-with-application-insights).

