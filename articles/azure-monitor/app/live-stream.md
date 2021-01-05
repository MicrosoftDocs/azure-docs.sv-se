---
title: Diagnostisera med Live Metrics Stream-Azure Application insikter
description: Övervaka din webbapp i real tid med anpassade mått och diagnostisera problem med en direkt flöde av fel, spår och händelser.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: c50f7e57c520321d752d6d46e36c45c615b5e785
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803586"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: övervaka & diagnostisera med en andra svars tid

Övervaka din Live-, produktions webb tillämpning genom att använda Live Metrics Stream (kallas även QuickPulse) från [Application Insights](./app-insights-overview.md). Välj och filtrera mått och prestanda räknare för att se Real tid utan störningar på tjänsten. Undersök stack spårningar från exempel misslyckade förfrågningar och undantag. Tillsammans med [profiler](./profiler.md) och [fel sökning av ögonblicks bilder](./snapshot-debugger.md)tillhandahåller Live Metrics Stream ett kraftfullt och icke-invasivt diagnos verktyg för din Live-webbplats.

Med Live Metrics Stream kan du:

* Verifiera en åtgärd när den släpps genom att titta på prestanda-och antal haverier.
* Se resultatet av test belastningar och diagnostisera problem Live.
* Fokusera på specifika testsessioner eller filtrera bort kända problem genom att välja och filtrera de mått som du vill titta på.
* Hämta undantags spårningar när de sker.
* Experimentera med filter för att hitta de mest relevanta KPI: erna.
* Övervaka alla Windows prestanda räknare Live.
* Identifiera enkelt en server som har problem och filtrera alla KPI/Live-flöden på bara den servern.

![Fliken Live Metrics](./media/live-stream/live-metric.png)

Live-mått stöds för närvarande för ASP.NET-, ASP.NET Core-, Azure Functions-, Java-och Node.js-appar.

## <a name="get-started"></a>Kom igång

1. Använd språkspecifika rikt linjer för att aktivera Live-mått.
   * [ASP.net](./asp-net.md) – Live mått är aktiverat som standard.
   * [ASP.net Core](./asp-net-core.md)-Live-mått är aktiverat som standard.
   * [.Net/.net Core-konsolen/Worker](./worker-service.md)-Live-mått är aktiverat som standard.
   * [.NET-program – aktivera med hjälp av kod](#enable-livemetrics-using-code-for-any-net-application).
    * [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) -Live-mått är aktiverat som standard.
   * [Node.js](./nodejs.md#live-metrics)

2. I [Azure Portal](https://portal.azure.com)öppnar du Application Insights resurs för din app och öppnar sedan Live Stream.

3. [Skydda kontroll kanalen](#secure-the-control-channel) om du kan använda känsliga data som kund namn i dina filter.

### <a name="enable-livemetrics-using-code-for-any-net-application"></a>Aktivera LiveMetrics med kod för alla .NET-program

Även om LiveMetrics är aktiverat som standard när du registrerar med rekommenderade instruktioner för .NET-program, visar följande hur du konfigurerar Live-mått manuellt.

1. Installera NuGet-paketet [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector)
2. Följande exempel på konsolens app-kod visar hur du konfigurerar Live-mått.

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using System;
using System.Threading.Tasks;

namespace LiveMetricsDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create a TelemetryConfiguration instance.
            TelemetryConfiguration config = TelemetryConfiguration.CreateDefault();
            config.InstrumentationKey = "INSTRUMENTATION-KEY-HERE";
            QuickPulseTelemetryProcessor quickPulseProcessor = null;
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    quickPulseProcessor = new QuickPulseTelemetryProcessor(next);
                    return quickPulseProcessor;
                })
                .Build();

            var quickPulseModule = new QuickPulseTelemetryModule();

            // Secure the control channel.
            // This is optional, but recommended.
            quickPulseModule.AuthenticationApiKey = "YOUR-API-KEY-HERE";
            quickPulseModule.Initialize(config);
            quickPulseModule.RegisterTelemetryProcessor(quickPulseProcessor);

            // Create a TelemetryClient instance. It is important
            // to use the same TelemetryConfiguration here as the one
            // used to setup Live Metrics.
            TelemetryClient client = new TelemetryClient(config);

            // This sample runs indefinitely. Replace with actual application logic.
            while (true)
            {
                // Send dependency and request telemetry.
                // These will be shown in Live Metrics stream.
                // CPU/Memory Performance counter is also shown
                // automatically without any additional steps.
                client.TrackDependency("My dependency", "target", "http://sample",
                    DateTimeOffset.Now, TimeSpan.FromMilliseconds(300), true);
                client.TrackRequest("My Request", DateTimeOffset.Now,
                    TimeSpan.FromMilliseconds(230), "200", true);
                Task.Delay(1000).Wait();
            }
        }
    }
}
```

Exemplet ovan är för en konsol app, men samma kod kan användas i alla .NET-program. Om någon annan TelemetryModules är aktive rad som automatiskt samlar in telemetri, är det viktigt att se till att samma konfiguration som används för att initiera dessa moduler används även för Live Metrics-modulen.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Hur skiljer sig Live Metrics Stream från Metrics Explorer och analyser?

| |Direktsänd ström | Metrics Explorer och analys |
|---|---|---|
|**Svarstid**|Data som visas inom en sekund|Sammanställt över minuter|
|**Ingen kvarhållning**|Data sparas medan de finns i diagrammet och ignoreras sedan|[Data kvarhållna i 90 dagar](./data-retention-privacy.md#how-long-is-the-data-kept)|
|**På begäran**|Data strömmas endast när Live Metrics-fönstret är öppet |Data skickas när SDK är installerat och aktiverat|
|**Kostnadsfri**|Det kostar inget att Live Stream data|Omfattas av [prissättning](./pricing.md)
|**Sampling**|Alla valda mått och räknare överförs. Felen och stack spåren samplas. |Händelser kan [samplas](./api-filtering-sampling.md)|
|**Kontroll kanal**|Filter kontroll signaler skickas till SDK: n. Vi rekommenderar att du skyddar den här kanalen.|Kommunikation är ett sätt, till portalen|

## <a name="select-and-filter-your-metrics"></a>Välj och filtrera dina mått

(Tillgänglig med ASP.NET, ASP.NET Core och Azure Functions (v2).)

Du kan övervaka anpassad KPI Live genom att använda godtyckliga filter på alla Application Insights telemetri från portalen. Klicka på den filter kontroll som visas när du pekar på ett diagram. Följande diagram ritar en anpassad KPI för antalet begär Anden med filter på URL-och duration-attribut. Verifiera dina filter med avsnittet för hands version av data ström som visar en live-feed med telemetri som matchar de kriterier som du har angett vid en viss tidpunkt.

![Filter begär ande frekvens](./media/live-stream/filter-request.png)

Du kan övervaka ett värde som skiljer sig från antalet. Vilka alternativ som är tillgängliga beror på vilken typ av data ström som kan vara en Application Insights telemetri: begär Anden, beroenden, spårningar, händelser eller mått. Det kan vara din egen [anpassade mått enhet](./api-custom-events-metrics.md#properties):

![Frågeverktyg för begär ande frekvens med anpassat mått](./media/live-stream/query-builder-request.png)

Förutom att Application Insights telemetri kan du också övervaka Windows prestanda räknare genom att välja det från data Ströms alternativen och ange namnet på prestanda räknaren.

Live-mått sammanställs på två punkter: lokalt på varje server och sedan på alla servrar. Du kan ändra standardvärdet genom att välja andra alternativ i respektive listruta.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Exempel på telemetri: anpassade Live Diagnostic-händelser
Som standard visar live-matningen av händelser exempel på misslyckade förfrågningar och beroende anrop, undantag, händelser och spår. Klicka på filter ikonen för att se de tillämpade kriterierna vid varje tidpunkt.

![Knappen Filtrera](./media/live-stream/filter.png)

Precis som med mått kan du ange valfria villkor till vilken typ av Application Insights telemetri som helst. I det här exemplet väljer vi vissa begär ande problem och händelser.

![Frågebyggaren](./media/live-stream/query-builder.png)

> [!NOTE]
> För närvarande kan du använda det yttersta undantags meddelandet för undantags meddelande villkor. I föregående exempel kan du filtrera bort det ofarliga undantaget med ett internt undantags meddelande (efter "<--"-avgränsare) "klienten kopplades från." Använd meddelandet not-contains "fel vid läsning av begär ande innehåll".

Se information om ett objekt i Live-flödet genom att klicka på det. Du kan pausa matningen antingen genom att klicka på **pausa** eller helt enkelt rulla nedåt eller klicka på ett objekt. Live-matningen återupptas när du har rullat överst eller genom att klicka på objekt räknaren som samlats in när den pausades.

![Skärm bild som visar fönstret exempel på telemetri med ett undantag valt och undantags informationen som visas längst ned i fönstret.](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>Filtrera efter Server instans

Om du vill övervaka en viss server roll instans kan du filtrera efter server. Filtrera genom att välja Server namnet under *servrar*.

![Exempel på aktiva startfel](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>Skydda kontroll kanalen

> [!NOTE]
> För närvarande kan du bara konfigurera en autentiserad kanal med hjälp av kod baserad övervakning och kan inte autentisera servrar med hjälp av kod koppling.

De anpassade filter kriterier som du anger i Live Metrics-portalen skickas tillbaka till Live Metrics-komponenten i Application Insights SDK. Filtren kan eventuellt innehålla känslig information, till exempel customerID. Du kan göra kanalen säker med en hemlig API-nyckel förutom Instrumentation-nyckeln.

### <a name="create-an-api-key"></a>Skapa en API-nyckel

![API-nyckel > skapa API-nyckel skapa API-nyckel ](./media/live-stream/api-key.png)
 ![ fliken. Välj "verifiera SDK-kontroll kanal" och sedan "generera nyckel"](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>Lägg till API-nyckel i konfigurationen

### <a name="aspnet"></a>ASP.NET

I applicationinsights.config-filen lägger du till AuthenticationApiKey i QuickPulseTelemetryModule:

```XML
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>
```

### <a name="aspnet-core"></a>ASP.NET Core

Följ instruktionerna nedan för [ASP.net Core](./asp-net-core.md) program.

Ändra `ConfigureServices` din startup.CS-fil på följande sätt:

Lägg till följande namnrymd.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Ändra sedan `ConfigureServices` metoden enligt nedan.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // existing code which include services.AddApplicationInsightsTelemetry() to enable Application Insights.
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
}
```

Mer information om hur du konfigurerar ASP.NET Core program finns i vår vägledning om hur du [konfigurerar telemetri-moduler i ASP.net Core](./asp-net-core.md#configuring-or-removing-default-telemetrymodules).

### <a name="workerservice"></a>WorkerService

Följ anvisningarna nedan för [WorkerService](./worker-service.md) -program.

Lägg till följande namnrymd.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Lägg sedan till följande rad före anropet `services.AddApplicationInsightsTelemetryWorkerService` .

```csharp
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Mer information om hur du konfigurerar WorkerService-program finns i vår vägledning om hur du [konfigurerar telemetri-moduler i WorkerServices](./worker-service.md#configuring-or-removing-default-telemetrymodules).

### <a name="azure-function-apps"></a>Azure-funktionsappar

För Azure Function Apps (v2) kan du skydda kanalen med en API-nyckel med en miljö variabel.

Skapa en API-nyckel inifrån din Application Insights resurs och gå till **inställningar > konfiguration** för din Funktionsapp. Välj **ny program inställning** och ange ett namn på `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` och ett värde som motsvarar din API-nyckel.

Men om du känner igen och litar på alla anslutna servrar kan du prova anpassade filter utan den autentiserade kanalen. Det här alternativet är tillgängligt i sex månader. Den här åsidosättningen krävs en gång i varje ny session, eller när en ny server är online.

![Alternativ för Live Metrics-autentisering](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Vi rekommenderar starkt att du konfigurerar den autentiserade kanalen innan du anger potentiellt känslig information som CustomerID i filter kriterierna.
>

## <a name="supported-features-table"></a>Tabell med funktioner som stöds

| Språk                         | Grundläggande mått       | Prestandamått | Anpassad filtrering    | Exempel på telemetri    | CPU-delning per process |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET Framework                   | Stöds (V-2.7.2 +) | Stöds (V-2.7.2 +) | Stöds (V-2.7.2 +) | Stöds (V-2.7.2 +) | Stöds (V-2.7.2 +)  |
| .NET Core (Target =. NET Framework)| Stöds (V 2.4.1 +) | Stöds (V 2.4.1 +) | Stöds (V 2.4.1 +) | Stöds (V 2.4.1 +) | Stöds (V 2.4.1 +)  |
| .NET Core (Target =. NET Core)     | Stöds (V 2.4.1 +) | Stöds*          | Stöds (V 2.4.1 +) | Stöds (V 2.4.1 +) | **Stöds inte**    |
| Azure Functions v2               | Stöds           | Stöds           | Stöds           | Stöds           | **Stöds inte**    |
| Java                             | Stöds (V-2.0.0 +) | Stöds (V-2.0.0 +) | **Stöds inte**   | **Stöds inte**   | **Stöds inte**    |
| Node.js                          | Stöds (V-1.3.0 +) | Stöds (V-1.3.0 +) | **Stöds inte**   | Stöds (V-1.3.0 +) | **Stöds inte**    |

Grundläggande mått inkluderar begäran, beroende och undantags frekvens. Prestanda mått (prestanda räknare) inkluderar minne och CPU. Exempel på telemetri visar en data ström med detaljerad information om misslyckade förfrågningar och beroenden, undantag, händelser och spår.

 \* PerfCounters-supporten varierar något mellan versioner av .NET Core som inte riktar sig mot .NET Framework:

- PerfCounters-mått stöds när de körs i Azure App Service för Windows. (AspNetCore SDK version 2.4.1 eller senare)
- PerfCounters stöds när appen körs i Windows-datorer (virtuell dator eller moln tjänst eller lokal osv.) (AspNetCore SDK version 2.7.1 eller senare), men för appar som riktar sig till .NET Core 2,0 eller högre.
- PerfCounters stöds när appen körs var som helst (Linux, Windows, App Service för Linux, behållare osv.) i de senaste versionerna (t. ex. AspNetCore SDK-version 2.8.0 eller senare), men endast för appar som är riktade till .NET Core 2,0 eller högre.

## <a name="troubleshooting"></a>Felsökning

Live Metrics Stream använder andra IP-adresser än andra Application Insights telemetri. Kontrol lera att [de här IP-adresserna](./ip-addresses.md) är öppna i brand väggen. Kontrol lera också att de [utgående portarna för Live Metrics Stream](./ip-addresses.md#outgoing-ports) är öppna i brand väggen för dina servrar.

## <a name="next-steps"></a>Nästa steg

* [Övervaka användning med Application Insights](./usage-overview.md)
* [Använda diagnostisk sökning](./diagnostic-search.md)
* [Profilerare](./profiler.md)
* [Fel sökning av ögonblicks bild](./snapshot-debugger.md)
