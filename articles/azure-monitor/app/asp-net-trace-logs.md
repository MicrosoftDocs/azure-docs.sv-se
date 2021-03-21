---
title: Utforska .NET-spårnings loggar i Application Insights
description: Sök efter loggar som genereras av trace, NLog eller Log4Net.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/08/2019
ms.openlocfilehash: 083ddbd06561550f89e414d6c679cdc6433fa338
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98937555"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Utforska loggarna .NET/.NET Core och python i Application Insights

Skicka diagnostiska spårnings loggar för ditt ASP.NET/ASP.NET Core-program från ILogger, NLog, log4Net eller system. Diagnostics. trace till [Azure Application insikter][start]. För python-program skickar du diagnostiska spårnings loggar med AzureLogHandler i openräkningar python för Azure Monitor. Du kan sedan utforska och söka i dem. Dessa loggar slås samman med de andra loggfilerna från ditt program, så att du kan identifiera spårningar som är associerade med varje Användarbegäran och korrelera dem med andra händelser och undantags rapporter.

> [!NOTE]
> Behöver du modulen för logg avbildning? Det är ett användbart kort för loggar från tredje part. Men om du inte redan använder NLog, log4Net eller system. Diagnostics. trace bör du överväga att bara anropa [**Application Insights TrackTrace ()**](./api-custom-events-metrics.md#tracktrace) direkt.
>
>
## <a name="install-logging-on-your-app"></a>Installera loggning i din app
Installera det valda loggnings ramverket i projektet, vilket bör resultera i en post i app.config eller web.config.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Konfigurera Application Insights för att samla in loggar
[Lägg till Application Insights i projektet](./asp-net.md) om du inte har gjort det än. Du ser ett alternativ för att inkludera logg insamlaren.

Eller högerklicka på ditt projekt i Solution Explorer för att **konfigurera Application Insights**. Välj alternativet **Konfigurera spårnings samling** .

> [!NOTE]
> Ingen Application Insights meny eller logg insamlings alternativ? Försök [Felsöka](#troubleshooting).

## <a name="manual-installation"></a>Manuell installation
Använd den här metoden om din projekt typ inte stöds av installations programmet för Application Insights (till exempel ett Windows Desktop-projekt).

1. Om du planerar att använda log4Net eller NLog kan du installera det i projektet.
2. I Solution Explorer högerklickar du på projektet och väljer **Hantera NuGet-paket**.
3. Sök efter "Application Insights".
4. Välj något av följande paket:

   - För ILogger: [Microsoft. Extensions. logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/) 
 [ ![ NuGet ILogger banderoll](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - För NLog: [Microsoft. ApplicationInsights. NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/) 
 [ ![ NuGet NLog banderoll](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - För Log4Net: [Microsoft. ApplicationInsights. Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/) 
 [ ![ NuGet Log4Net banderoll](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - För system. Diagnostics: [Microsoft. ApplicationInsights. TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) 
 [ ![ NuGet system. Diagnostics banderoll](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft. ApplicationInsights. DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) 
 [ ![ Banderoll för NuGet-diagnostikens lyssnar källa](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft. ApplicationInsights. EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) 
 [ ![ NuGet ETW-insamlarens banderoll](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft. ApplicationInsights. EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) 
 [ ![ Informations källa för NuGet händelse källa](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

NuGet-paketet installerar nödvändiga sammansättningar och ändrar web.config eller app.config om detta är tillämpligt.

## <a name="ilogger"></a>ILogger

Exempel på hur du använder Application Insights ILogger-implementering med konsol program och ASP.NET Core finns i [ApplicationInsightsLoggerProvider för .net Core ILogger-loggar](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Infoga diagnostiska logg anrop
Om du använder system. Diagnostics. trace är ett typiskt anrop att:

```csharp
System.Diagnostics.Trace.TraceWarning("Slow response - database01");
```

Om du föredrar log4net eller NLog använder du:

```csharp
    logger.Warn("Slow response - database01");
```

## <a name="use-eventsource-events"></a>Använda EventSource-händelser
Du kan konfigurera [system. Diagnostics. tracing. EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) -händelser som ska skickas till Application Insights som spår. Installera först NuGet- `Microsoft.ApplicationInsights.EventSourceListener` paketet. Redigera sedan `TelemetryModules` avsnittet i [ApplicationInsights.configs ](./configuration-with-applicationinsights-config.md) filen.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

För varje källa kan du ange följande parametrar:
 * **Namn** anger namnet på den EventSource som ska samlas in.
 * **Nivå** anger den loggnings nivå som ska samlas in: *kritisk*, *fel*, *information*, *LogAlways*, *utförlig* eller *Varning*.
 * **Nyckelord** (valfritt) Ange heltal svärdet för nyckelords kombinationer som ska användas.

## <a name="use-diagnosticsource-events"></a>Använda DiagnosticSource-händelser
Du kan konfigurera [system. Diagnostics. DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) -händelser som ska skickas till Application Insights som spår. Installera först NuGet- [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) paketet. Redigera sedan avsnittet "TelemetryModules" i [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) -filen.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

För varje DiagnosticSource som du vill spåra lägger du till en post med attributet **Name** angivet till namnet på din DiagnosticSource.

## <a name="use-etw-events"></a>Använda ETW-händelser
Du kan konfigurera ETW (Event Tracing for Windows)-händelser (ETW) som ska skickas till Application Insights som spår. Installera först NuGet- `Microsoft.ApplicationInsights.EtwCollector` paketet. Redigera sedan avsnittet "TelemetryModules" i [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) -filen.

> [!NOTE] 
> ETW-händelser kan bara samlas in om processen som är värd för SDK: n körs under en identitet som är medlem i prestanda loggar användare eller administratörer.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

För varje källa kan du ange följande parametrar:
 * **ProviderName** är namnet på ETW-providern som ska samlas in.
 * **ProviderGuid** anger GUID för ETW-providern som ska samlas in. Den kan användas i stället för `ProviderName` .
 * **Nivå** anger den loggnings nivå som ska samlas in. Det kan vara *kritiskt*, *fel*, *information*, *LogAlways*, *utförlig* eller *Varning*.
 * **Nyckelord** (valfritt) Ange heltal svärdet för nyckelords kombinationer som ska användas.

## <a name="use-the-trace-api-directly"></a>Använda trace API direkt
Du kan anropa API: et för Application Insights trace direkt. Loggnings korten använder detta API.

Exempel:

```csharp
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
var telemetryClient = new TelemetryClient(configuration);
telemetry.TrackTrace("Slow response - database01");
```

En fördel med TrackTrace är att du kan ställa in relativt långa data i meddelandet. Du kan till exempel koda POST-data där.

Du kan också lägga till en allvarlighets grad i meddelandet. Liksom annan telemetri kan du lägga till egenskaps värden för att filtrera eller söka efter olika uppsättningar med spår. Exempel:

  ```csharp
  TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
  var telemetryClient = new TelemetryClient(configuration);
  telemetryClient.TrackTrace("Slow database response",
                              SeverityLevel.Warning,
                              new Dictionary<string, string> { { "database", "db.ID" } });
  ```

På så sätt kan du enkelt filtrera ut i [söka][diagnostic] alla meddelanden med en viss allvarlighets grad som relaterar till en viss databas.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler för openräkningar python
Med Azure Monitor logg hanteraren kan du exportera python-loggar till Azure Monitor.

Instrumentera ditt program med [python-SDK: n för openräkning](./opencensus-python.md) för Azure Monitor.

I det här exemplet visas hur du skickar en varnings nivå logg till Azure Monitor.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Utforska dina loggar
Kör appen i fel söknings läge eller distribuera den Live.

I appens översikts fönster i [Application Insights-portalen][portal]väljer du [Sök][diagnostic].

Du kan till exempel:

* Filtrera efter logg spår eller objekt med vissa egenskaper.
* Granska ett särskilt objekt i detalj.
* Hitta andra system logg data som relaterar till samma Användarbegäran (har samma OperationId).
* Spara konfigurationen av en sida som en favorit.

> [!NOTE]
>Om ditt program skickar mycket data och du använder Application Insights SDK för ASP.NET-version 2.0.0-beta3 eller senare, kan den *anpassningsbara samplings* funktionen hantera och bara skicka en del av din telemetri. [Läs mer om sampling.](./sampling.md)
>

## <a name="troubleshooting"></a>Felsökning
### <a name="how-do-i-do-this-for-java"></a>Hur gör jag för att gör du detta för Java?
I Java codeal Instrumentation (rekommenderas) loggar samlas in direkt i rutan, använder du [java 3,0-agenten](./java-in-process-agent.md).

Om du använder Java SDK använder du [Java log-nätverkskorten](./java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Det finns inget Application Insights alternativ på snabb menyn i Project
* Se till att Developer Analytics Tools är installerat på utvecklings datorn. I Visual Studio **Tools**  >  -**tillägg och uppdateringar** kan du leta efter **Developer Analytics-verktyg**. Om den inte finns på fliken **installerad** öppnar du fliken **online** och installerar den.
* Detta kan vara en projekt typ som inte stöds av Developer Analytics tools. Använd [manuell installation](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Det finns inget logg korts alternativ i konfigurations verktyget
* Installera loggnings ramverket först.
* Om du använder system. Diagnostics. trace kontrollerar du att du har [konfigurerat den i *web.config*](/dotnet/api/system.diagnostics.eventlogtracelistener).
* Kontrol lera att du har den senaste versionen av Application Insights. I Visual Studio går du till **verktyg**  >  **tillägg och uppdateringar** och öppnar fliken **uppdateringar** . Om **Developer Analytics tools** finns där väljer du det för att uppdatera det.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>Jag får fel meddelandet "Instrumentation-nyckeln kan inte vara tom"
Du har förmodligen installerat NuGet-paketet för loggnings kortet utan att installera Application Insights. I Solution Explorer högerklickar du på *ApplicationInsights.config* och väljer **Uppdatera Application Insights**. Du uppmanas att logga in på Azure och skapa en Application Insights resurs eller återanvända en befintlig. Det bör åtgärda problemet.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Jag kan se spårningar men inte andra händelser i diagnostisk sökning
Det kan ta en stund innan alla händelser och begär Anden kan gå igenom pipelinen.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Hur mycket data behålls?
Flera faktorer påverkar mängden data som behålls. Mer information finns i avsnittet om [begränsningar](./api-custom-events-metrics.md#limits) på sidan för kundens händelse mått.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Jag ser inte några logg poster som jag förväntade mig
Om ditt program skickar Voluminous data mängder och du använder Application Insights SDK för ASP.NET-version 2.0.0-beta3 eller senare, kan den anpassningsbara samplings funktionen hantera och bara skicka en del av din telemetri. [Läs mer om sampling.](./sampling.md)

## <a name="next-steps"></a><a name="add"></a>Nästa steg

* [Diagnostisera fel och undantag i ASP.NET][exceptions]
* [Läs mer om sökning][diagnostic]
* [Ställ in tillgänglighets-och svars tider][availability]
* [Felsökning][qna]

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[start]: ./app-insights-overview.md

