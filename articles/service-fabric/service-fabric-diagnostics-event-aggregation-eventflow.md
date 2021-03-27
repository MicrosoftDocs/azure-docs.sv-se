---
title: Azure Service Fabric Event agg regering med EventFlow
description: Lär dig mer om agg regering och insamling av händelser med EventFlow för övervakning och diagnostik av Azure Service Fabric-kluster.
ms.topic: conceptual
ms.date: 2/25/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 5a502fe05a862893d2c4879a6976d8d8d1044cbf
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627769"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Händelse agg regering och insamling med EventFlow

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) kan dirigera händelser från en nod till ett eller flera övervaknings mål. Eftersom det ingår som ett NuGet-paket i ditt tjänst projekt, EventFlow-kod och konfigurations resor med tjänsten, eliminerar konfigurations problemet per nod som nämnts tidigare om Azure-diagnostik. EventFlow körs i tjänst processen och ansluter direkt till de konfigurerade utmatningarna. På grund av den direkta anslutningen fungerar EventFlow för Azure, container och lokala tjänst distributioner. Var försiktig om du kör EventFlow i scenarier med hög densitet, t. ex. i en behållare, eftersom varje EventFlow-pipeline gör en extern anslutning. Så om du är värd för flera processer får du flera utgående anslutningar! Detta är inte lika mycket som är viktigt för Service Fabric program, eftersom alla repliker av en `ServiceType` körs i samma process och detta begränsar antalet utgående anslutningar. EventFlow erbjuder också händelse filtrering så att endast de händelser som matchar det angivna filtret skickas.

## <a name="set-up-eventflow"></a>Konfigurera EventFlow

EventFlow-binärfiler är tillgängliga som en uppsättning NuGet-paket. Om du vill lägga till EventFlow i ett Service Fabric-tjänst projekt högerklickar du på projektet i Solution Explorer och väljer Hantera NuGet-paket. Växla till fliken "Bläddra" och Sök efter " `Diagnostics.EventFlow` ":

![EventFlow NuGet-paket i Visual Studio NuGet Package Manager UI](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Du ser en lista över olika paket som visas, med etiketten "Inputs" och "outputs". EventFlow stöder olika typer av loggnings leverantörer och analys verktyg. Tjänstens värdbaserade EventFlow bör innehålla lämpliga paket beroende på källa och mål för program loggarna. Förutom kärnan ServiceFabric-paketet behöver du också minst en indata-och utdata-konfiguration. Du kan till exempel lägga till följande paket för att skicka EventSource-händelser till Application Insights:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` för att samla in data från tjänstens EventSource-klass och från standard EventSources som *Microsoft-ServiceFabric-Services* och *Microsoft-ServiceFabric-aktörer*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (vi ska skicka loggarna till en Azure Application Insights-resurs)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(aktiverar initiering av EventFlow-pipeline från Service Fabric tjänst konfiguration och rapporterar eventuella problem med att skicka diagnostikdata som Service Fabric hälso rapporter)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` -paketet kräver att tjänst projektet är målet .NET Framework 4,6 eller senare. Se till att ange rätt mål ramverk i projekt egenskaper innan du installerar det här paketet.

När alla paket har installerats är nästa steg att konfigurera och aktivera EventFlow i tjänsten.

## <a name="configure-and-enable-log-collection"></a>Konfigurera och aktivera logg insamling
EventFlow-pipeline som ansvarar för att skicka loggarna skapas från en specifikation som lagras i en konfigurations fil. `Microsoft.Diagnostics.EventFlow.ServiceFabric`Paketet installerar en start konfigurations fil för EventFlow under `PackageRoot\Config` mappen Solution, som heter `eventFlowConfig.json` . Den här konfigurations filen måste ändras för att avbilda data från standard tjänst `EventSource` klassen och andra indata som du vill konfigurera och skicka data till rätt plats.

>[!NOTE]
>Om din projekt fil har VisualStudio 2017-format `eventFlowConfig.json` kommer filen inte att läggas till automatiskt. Om du vill åtgärda detta skapar du filen i `Config` mappen och ställer in åtgärden skapa `Copy if newer` . 

Här är ett exempel *påeventFlowConfig.js* baserat på de NuGet-paket som nämns ovan:
```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

Namnet på tjänstens ServiceEventSource är värdet för egenskapen Name för den som `EventSourceAttribute` används för ServiceEventSource-klassen. Allt anges i `ServiceEventSource.cs` filen, som är en del av tjänst koden. I följande kodfragment är namnet på ServiceEventSource till exempel *företags-application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Observera att `eventFlowConfig.json` filen är en del av tjänst konfigurations paketet. Ändringar i den här filen kan inkluderas i uppgraderingar av fullständig eller endast konfiguration av tjänsten, beroende på Service Fabric uppgradering av hälso kontroller och automatisk återställning om det inte finns något uppgraderings fel. Mer information finns i [Service Fabric program uppgradering](service-fabric-application-upgrade.md).

I avsnittet *filter* i config kan du ytterligare anpassa den information som ska gå genom EventFlow-pipeline till utdata, så att du kan släppa eller ta med viss information eller ändra strukturen för händelse data. Mer information om filtrering finns i [EventFlow filters](https://github.com/Azure/diagnostics-eventflow#filters).

Det sista steget är att instansiera EventFlow-pipeline i din tjänsts start kod, som finns i `Program.cs` filen:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

Namnet som angavs som parameter för `CreatePipeline` metoden i `ServiceFabricDiagnosticsPipelineFactory` är namnet på den *hälsoentitet* som representerar pipeline för EventFlow logg insamling. Det här namnet används om EventFlow påträffar och fel och rapporterar det via under systemet för Service Fabric hälsa.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Använd Service Fabric inställningar och program parametrar i eventFlowConfig

EventFlow stöder användning av Service Fabric inställningar och program parametrar för att konfigurera EventFlow-inställningar. Du kan referera till Service Fabric inställnings parametrar med hjälp av den här särskilda syntaxen för värden:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` är namnet på avsnittet Service Fabric konfiguration och `<setting-name>` är konfigurations inställningen som tillhandahåller värdet som ska användas för att konfigurera en EventFlow-inställning. Om du vill läsa mer om hur du gör detta går du till [stöd för Service Fabric inställningar och program parametrar](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Verifiering

Starta tjänsten och titta på fönstret med fel söknings fönstret i Visual Studio. När tjänsten har startats bör du börja se bevis på att tjänsten skickar poster till de utdata som du har konfigurerat. Navigera till din händelse analys och visualiserings plattform och bekräfta att loggarna har börjat visas (det kan ta några minuter).

## <a name="next-steps"></a>Nästa steg

* [Händelse analys och visualisering med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Händelse analys och visualisering med Azure Monitor loggar](service-fabric-diagnostics-event-analysis-oms.md)
* [Dokumentation om EventFlow](https://github.com/Azure/diagnostics-eventflow)
