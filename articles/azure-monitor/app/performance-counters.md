---
title: Prestanda räknare i Application Insights | Microsoft Docs
description: Övervaka system-och anpassade .NET-prestanda räknare i Application Insights.
ms.topic: conceptual
ms.date: 12/13/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: f772b129e20fbaaade7305d84064a713dd734199
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937384"
---
# <a name="system-performance-counters-in-application-insights"></a>System prestanda räknare i Application Insights

Windows har en mängd olika [prestandaräknare](/windows/desktop/perfctrs/about-performance-counters) för exempelvis användning av processorer, minne, diskar och nätverk. Du kan också definiera dina egna prestanda räknare. Insamling av prestanda räknare stöds så länge programmet körs under IIS på en lokal värd eller virtuell dator som du har administrativ åtkomst till. Även om program som körs som Azure Web Apps inte har direkt åtkomst till prestanda räknare samlas en del av de tillgängliga räknarna upp av Application Insights.

## <a name="view-counters"></a>Visa räknare

Fönstret mått visar standard uppsättningen med prestanda räknare.

![Prestanda räknare som rapporter ATS i Application Insights](./media/performance-counters/performance-counters.png)

De aktuella standard räknare som har kon figurer ATS för att samlas in för ASP.NET/ASP.NET Core-webbprogram är:
- Processor tid i procent för processor \\
- % Process \\ processor tid normaliserad
- \\Tillgängliga byte för minne
- ASP.NET-begäranden per sekund
- Utlösta .NET CLR-undantag/SEK
- Körnings tid för ASP.NET-ApplicationsRequest
- \\Privata byte för process
- Bearbeta \\ IO-databyte/s
- ASP.NET program \\ begär anden i program kön
- Processor (_Total) \\ % processor tid

## <a name="add-counters"></a>Lägg till räknare

Om den prestanda räknare som du vill använda inte finns med i listan över mått kan du lägga till den.

1. Ta reda på vilka räknare som är tillgängliga på servern med hjälp av PowerShell-kommandot på den lokala servern:

    `Get-Counter -ListSet *`

    (Se [`Get-Counter`](/powershell/module/microsoft.powershell.diagnostics/get-counter) .)
2. Öppna ApplicationInsights.config.

   * Om du har lagt till Application Insights i din app under utvecklingen redigerar du ApplicationInsights.config i projektet och distribuerar det sedan igen till dina servrar.
3. Redigera prestanda insamlings direktivet:

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> ASP.NET Core program har inte `ApplicationInsights.config` , och därför är metoden ovan inte giltig för ASP.net Core program.

Du kan avbilda både standard räknare och de som du har implementerat själv. `\Objects\Processes` är ett exempel på en standard räknare som är tillgänglig på alla Windows-system. `\Sales(photo)\# Items Sold` är ett exempel på en anpassad räknare som kan implementeras i en webb tjänst.

Formatet är `\Category(instance)\Counter"` , eller för kategorier som inte har instanser, bara `\Category\Counter` .

`ReportAs` krävs för räknar namn som inte matchar `[a-zA-Z()/-_ \.]+` – det vill säga de innehåller tecken som inte finns i följande uppsättningar: bokstäver, parenteser, snedstreck, bindestreck, under streck, blank steg, punkt.

Om du anger en instans kommer den att samlas in som en dimension "CounterInstanceName" av det rapporterade måttet.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Samla in prestanda räknare i kod för ASP.NET-webbprogram eller .NET/.NET Core-konsolprogram
Om du vill samla in system prestanda räknare och skicka dem till Application Insights kan du anpassa kodfragmentet nedan:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Eller så kan du göra samma sak med anpassade mått som du har skapat:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Samla in prestanda räknare i kod för ASP.NET Core webb program

Ändra `ConfigureServices` metod i `Startup.cs` klassen enligt nedan.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Prestanda räknare i Analytics
Du kan söka efter och Visa prestanda räknar rapporter i [Analytics](../log-query/log-query-overview.md).

**PerformanceCounters** -schemat visar `category` , `counter` namn och `instance` namn för varje prestanda räknare.  I telemetri för varje program visas endast räknare för programmet. Till exempel för att se vilka räknare som är tillgängliga: 

![Prestanda räknare i Application Insights Analytics](./media/performance-counters/analytics-performance-counters.png)

("Instans" här refererar till prestanda räknar instansen, inte rollen eller server maskin instansen. Prestanda räknarens instans namn brukar vara segment räknare som processor tid med namnet på processen eller programmet.)

Så här hämtar du ett diagram över tillgängligt minne under den senaste perioden: 

![Minnes timechart i Application Insights Analytics](./media/performance-counters/analytics-available-memory.png)

Precis som andra telemetri innehåller **performanceCounters** också en kolumn `cloud_RoleInstance` som anger identiteten för den värd Server instans där appen körs. Om du till exempel vill jämföra appens prestanda på olika datorer: 

![Prestanda segmenterad av roll instans i Application Insights Analytics](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET och Application Insights antal

*Vad är skillnaden mellan undantags frekvensen och undantags måtten?*

* *Undantags frekvensen* är en system prestanda räknare. CLR räknar alla hanterade och ohanterade undantag som genereras, och dividerar summan i ett samplings intervall med längden på intervallet. Application Insights SDK samlar in det här resultatet och skickar det till portalen.

* *Undantag* är antalet TrackException-rapporter som tagits emot av portalen i diagrammets samplings intervall. Den innehåller bara de hanterade undantagen där du har skrivit TrackException-anrop i koden, och innehåller inte alla [ohanterade undantag](./asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Prestanda räknare för program som körs i Azure Web Apps

Både ASP.NET-och ASP.NET Core-program som distribueras till Azure Web Apps köras i en särskild sandbox-miljö. Den här miljön tillåter inte direkt åtkomst till system prestanda räknare. En begränsad del av räknare visas dock som miljövariabler enligt beskrivningen [här](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). Application Insights SDK för ASP.NET och ASP.NET Core samlar in prestanda räknare från Azure Web Apps från dessa särskilda miljövariabler. Endast en del av räknarna är tillgängliga i den här miljön och den fullständiga listan finns [här.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Prestanda räknare i ASP.NET Core program

Stöd för prestanda räknare i ASP.NET Core är begränsat:

* [SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) -versioner 2.4.1 och senare samlar in prestanda räknare om programmet körs i Azure Web Apps (Windows).
* SDK-versioner 2.7.1 och senare samlar in prestanda räknare om programmet körs i Windows och mål `NETSTANDARD2.0` eller senare.
* För program som är riktade till .NET Framework har alla versioner av SDK stöd för prestanda räknare.
* SDK-versioner 2.8.0 och senare stöder CPU/minnes räknare i Linux. Ingen annan räknare stöds i Linux. Det rekommenderade sättet att hämta system räknare i Linux (och andra miljöer som inte kommer från Windows) är att använda [EventCounters](eventcounters.md)

## <a name="alerts"></a>Aviseringar
Precis som med andra mått kan du [Ange en avisering](../platform/alerts-log.md) som varnar dig om en prestanda räknare går utanför en gräns som du anger. Öppna fönstret aviseringar och klicka på Lägg till avisering.

## <a name="next-steps"></a><a name="next"></a>Nästa steg

* [Beroende spårning](./asp-net-dependencies.md)
* [Undantags spårning](./asp-net-exceptions.md)

