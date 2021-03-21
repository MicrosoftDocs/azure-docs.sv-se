---
title: Azure Application insikter för konsol program | Microsoft Docs
description: Övervaka webb program för tillgänglighet, prestanda och användning.
ms.topic: conceptual
ms.date: 05/21/2020
ms.custom: devx-track-csharp
ms.reviewer: lmolkova
ms.openlocfilehash: aa39a1eca04621fc4db75f755402d3679403e814
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96920599"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights för .NET-konsol program

Med [Application Insights](./app-insights-overview.md) kan du övervaka webb programmet för tillgänglighet, prestanda och användning.

Du behöver en prenumeration med [Microsoft Azure](https://azure.com). Logga in med ett Microsoft-konto som du kan ha för Windows, Xbox Live eller andra Microsoft-molntjänster. Ditt team kan ha en organisations prenumeration på Azure: Be ägaren att lägga till dig med hjälp av din Microsoft-konto.

> [!NOTE]
> Vi *rekommenderar starkt* att du använder paketet [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) och tillhör ande [instruktioner för alla](./worker-service.md) konsol program. Det här paketet [`NetStandard2.0`](/dotnet/standard/net-standard) är mål och kan därför användas i .net Core 2,1 eller högre, och .NET Framework 4.7.2 eller högre.

## <a name="getting-started"></a>Komma igång

> [!IMPORTANT]
> Nya Azure-regioner **kräver** att anslutnings strängar används i stället för instrument knappar. [Anslutnings strängen](./sdk-connection-string.md?tabs=net) identifierar den resurs som du vill associera dina telemetridata med. Du kan också ändra de slut punkter som resursen kommer att använda som mål för din telemetri. Du måste kopiera anslutnings strängen och lägga till den i programmets kod eller till en miljö variabel.

* [Skapa en Application Insights-resurs](./create-new-resource.md) på [Azure Portal](https://portal.azure.com). För program typ väljer du **Allmänt**.
* Kopiera instrumenteringsnyckeln. Leta upp nyckeln i den **grundläggande** List rutan för den nya resurs som du skapade.
* Installera det senaste [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) -paketet.
* Ange Instrumentation-nyckeln i koden innan du spårar eventuell telemetri (eller anger APPINSIGHTS_INSTRUMENTATIONKEY miljövariabel). Därefter bör du kunna spåra telemetri manuellt och se det på Azure Portal

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> Telemetri skickas inte direkt. Telemetri-objekt grupperas och skickas av ApplicationInsights SDK. I-konsol program, som avslutas direkt efter anrops `Track()` metoder, kan telemetri inte skickas, såvida inte `Flush()` och `Sleep` / `Delay` utförs innan appen avslutas som i det [fullständiga exemplet](#full-example) senare i den här artikeln. `Sleep` krävs inte om du använder `InMemoryChannel` . Det finns ett aktivt problem som rör behovet av `Sleep` som spåras här: [ApplicationInsights-dotNet/Issues/407](https://github.com/microsoft/ApplicationInsights-dotnet/issues/407)


* Installera den senaste versionen av [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) -paketet – det spårar automatiskt http, SQL eller andra externa beroende anrop.

Du kan initiera och konfigurera Application Insights från koden eller med hjälp av `ApplicationInsights.config` filen. Kontrol lera att initieringen sker så tidigt som möjligt. 

> [!NOTE]
> Instruktioner som hänvisar till **ApplicationInsights.config** gäller endast för appar som är riktade till .NET Framework och som inte gäller för .net Core-program.

### <a name="using-config-file"></a>Använda konfigurations filen

Som standard letar Application Insights SDK efter `ApplicationInsights.config` fil i arbets katalogen när `TelemetryConfiguration` den skapas

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Du kan också ange sökväg till konfigurations filen.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Mer information finns i [konfigurations fil referens](configuration-with-applicationinsights-config.md).

Du kan få ett fullständigt exempel på konfigurations filen genom att installera den senaste versionen av [Microsoft. ApplicationInsights. Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) -paketet. Här är den **minimala** konfigurationen för beroende samling som motsvarar kod exemplet.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Konfigurerar telemetri-samling från kod
> [!NOTE]
> Det finns inte stöd för att läsa konfigurations filen på .NET Core. Du kan överväga att använda [Application Insights SDK för ASP.net Core](./asp-net-core.md)

* När programmet startas skapar du och konfigurerar `DependencyTrackingTelemetryModule` instans – det måste vara singleton och måste bevaras för programmets livs längd.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Lägg till vanliga telemetri-initierare

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Om du har skapat en konfiguration med en enkel `TelemetryConfiguration()` konstruktor måste du även aktivera korrelations stöd. **Det behövs inte** om du läser konfigurationen från filen, används `TelemetryConfiguration.CreateDefault()` eller `TelemetryConfiguration.Active` .

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Du kanske också vill installera och initiera modulen för insamlare av prestanda räknare enligt beskrivningen [här](https://apmtips.com/posts/2017-02-13-enable-application-insights-live-metrics-from-code/)


#### <a name="full-example"></a>Fullständigt exempel

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking when not using InMemoryChannel so wait a bit. There is an active issue regarding the need for `Sleep`/`Delay`
            // which is tracked here: https://github.com/microsoft/ApplicationInsights-dotnet/issues/407
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Nästa steg
* [Övervaka beroenden](./asp-net-dependencies.md) för att se om rest, SQL eller andra externa resurser saktar ned dig.
* [Använd API: et](./api-custom-events-metrics.md) för att skicka egna händelser och mått för en mer detaljerad vy av appens prestanda och användning.

