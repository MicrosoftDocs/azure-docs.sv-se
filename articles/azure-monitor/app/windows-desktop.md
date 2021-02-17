---
title: Övervaka användning och prestanda för Windows-appar
description: Analysera användning och prestanda för Windows-program med Application Insights.
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 15331494b40021f10c162fba75abf6fe88d2d419
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583326"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Övervaka användning och prestanda för klassiska Windows-appar

Program som finns lokalt, i Azure och i andra moln kan dra nytta av Application Insights. Den enda begränsningen är behovet av att [tillåta kommunikation](./ip-addresses.md) till Application Insights-tjänsten. För övervakning av UWP-program (Universell Windows-plattform) rekommenderar vi [Visual Studio App Center](../app/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Så här skickar du telemetri till Application Insights från ett klassiskt Windows-program
1. [Skapa en Application Insights-resurs](./create-new-resource.md) på [Azure Portal](https://portal.azure.com). 
2. Kopiera instrumenteringsnyckeln.
3. Redigera NuGet-paketet av för ett programprojekt och lägg till Microsoft.ApplicationInsights.WindowsServer i Visual Studio. (Eller välj Microsoft. ApplicationInsights om du bara vill ha bas-API: et, utan standardmodulerna för telemetri för telemetri.)
4. Ange antingen instrumentationsnyckeln i koden:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*din nyckel*`";`
   
    eller i ApplicationInsights.config (om du har installerat ett standard-telemetripaket):
   
    `<InstrumentationKey>`*din nyckel*`</InstrumentationKey>` 
   
    Om du använder ApplicationInsights.config, kontrollera att egenskaperna i Solution Explorer är inställda på **Byggåtgärd = Innehåll, Kopiera till utdatakatalog = Kopiera**.
5. [Använda API](./api-custom-events-metrics.md) att skicka telemetri.
6. Kör din app och se Telemetrin i resursen som du skapade i Azure Portal.

## <a name="example-code"></a><a name="telemetry"></a>Exempelkod

```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnFormClosing(System.Windows.Forms.FormClosingEventArgs e)
        {
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnFormClosing(e);
        }
        
        ...
```

## <a name="override-storage-of-computer-name"></a>Åsidosätt lagring av dator namn

Som standard samlar denna SDK upp och lagrar dator namnet för telemetri för system som du avger.

Dator namnet används av Application Insights [äldre Enterprise (per nod) pris nivå](./pricing.md#legacy-enterprise-per-node-pricing-tier) för interna fakturerings behov. Som standard om du använder en telemetri-initierare för att åsidosätta `telemetry.Context.Cloud.RoleInstance` skickas en separat egenskap `ai.internal.nodeName` som fortfarande kommer att innehålla värdet dator namn. Det här värdet kommer inte att lagras med din Application Insights telemetri, men används internt vid inmatning för att möjliggöra bakåtkompatibilitet med den äldre nodens fakturerings modell.

Om du befinner dig på den [äldre pris nivån för Enterprise (per nod)](./pricing.md#legacy-enterprise-per-node-pricing-tier) och bara behöver åsidosätta lagringen av dator namnet använder du en telemetri-initierare:

**Skriv anpassade TelemetryInitializer enligt nedan.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // Set custom role name here. Providing an empty string will result
                // in the computer name still be sent via this property.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

Instansiera initieraren i `Program.cs` `Main()` metoden nedan ange Instrumentation-nyckeln:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
            //...
        }
```

## <a name="override-transmission-of-computer-name"></a>Åsidosätt överföring av dator namn

Om du inte är på den [äldre pris nivån för Enterprise (per nod)](./pricing.md#legacy-enterprise-per-node-pricing-tier) och vill förhindra att all telemetri som innehåller dator namnet skickas, måste du använda en telemetri-processor.

### <a name="telemetry-processor"></a>Telemetri-processor

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;


namespace WindowsFormsApp2
{
    public class CustomTelemetryProcessor : ITelemetryProcessor
    {
        private readonly ITelemetryProcessor _next;

        public CustomTelemetryProcessor(ITelemetryProcessor next)
        {
            _next = next;
        }

        public void Process(ITelemetry item)
        {
            if (item != null)
            {
                item.Context.Cloud.RoleInstance = string.Empty;
            }

            _next.Process(item);
        }
    }
}
```

Instansiera telemetri-processorn i `Program.cs` `Main()` metoden nedan och ange Instrumentation-nyckeln:

```csharp
using Microsoft.ApplicationInsights.Extensibility;

namespace WindowsFormsApp2
{
    static class Program
    {
        static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.Use((next) => new CustomTelemetryProcessor(next));
            builder.Build();
            //...
        }
    }
}

```

> [!NOTE]
> Även om du kan använda en telemetri-processor på ett tekniskt sätt enligt beskrivningen ovan även om du befinner dig på den [äldre pris nivån för Enterprise (per nod)](./pricing.md#legacy-enterprise-per-node-pricing-tier), kommer detta att resultera i att det går att betala för flera noder.

## <a name="next-steps"></a>Nästa steg
* [Skapa en instrumentpanel](./overview-dashboard.md)
* [Diagnostisk sökning](./diagnostic-search.md)
* [Utforska mått](../essentials/metrics-charts.md)
* [Skriv analysfrågor](../logs/log-query-overview.md)

