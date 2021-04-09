---
title: Azure Service Fabric Event Analysis med Application Insights
description: Lär dig mer om visualisering och analys av händelser med hjälp av Application Insights för övervakning och diagnostik av Azure Service Fabric-kluster.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 0e84c3c1ab542dc4541ebdcfbc49f45e02458d9c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626222"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Händelse analys och visualisering med Application Insights

En del av Azure Monitor är Application Insights en utöknings bar plattform för program övervakning och diagnostik. Den innehåller ett kraftfullt verktyg för analys och frågor, anpassningsbar instrument panel och visualiseringar samt ytterligare alternativ, inklusive automatiserad avisering. Application Insights integreras med Service Fabric innehåller verktygs upplevelser för Visual Studio och Azure Portal, samt Service Fabric vissa mått, vilket ger en omfattande loggnings upplevelse direkt. Även om många loggar automatiskt skapas och samlas in åt dig med Application Insights, rekommenderar vi att du lägger till ytterligare anpassad loggning i dina program för att skapa en bättre diagnostik-upplevelse.

Den här artikeln hjälper dig att lösa följande vanliga frågor:

* Hur gör jag för att vet du vad som händer i mitt program och tjänster och samlar in telemetri?
* Hur gör jag för att felsöka mitt program, särskilt tjänster som kommunicerar med varandra?
* Hur gör jag för att få mått på hur mina tjänster presterar, till exempel sid inläsnings tid, HTTP-begäranden?

Syftet med den här artikeln är att visa hur du får insikter och felsöker inifrån Application Insights. Om du vill lära dig hur du konfigurerar och konfigurerar Application Insights med Service Fabric kan du läsa den här [självstudien](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Övervakning i Application Insights

Application Insights har en komplett Box-upplevelse när du använder Service Fabric. På sidan Översikt visar Application Insights viktig information om tjänsten, till exempel svars tiden och antalet bearbetade begär Anden. Genom att klicka på knappen Sök högst upp kan du se en lista över de senaste förfrågningarna i ditt program. Dessutom kan du se misslyckade begär Anden här och diagnostisera vilka fel som kan ha inträffat.

![Översikt över Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

På den högra panelen i föregående bild finns det två huvud typer av poster i listan: begär Anden och händelser. Begär Anden görs anrop till appens API via HTTP-begäranden i det här fallet och händelser är anpassade händelser som fungerar som telemetri som du kan lägga till var som helst i din kod. Du kan ytterligare utforska instrumentering av dina program i [Application Insights API för anpassade händelser och mått](../azure-monitor/app/api-custom-events-metrics.md). Om du klickar på en begäran visas ytterligare information som visas i följande bild, inklusive data som är särskilt för Service Fabric, som samlas in i Application Insights Service Fabric NuGet-paketet. Den här informationen är användbar för fel sökning och kunskap om vad appens tillstånd är, och all den här informationen är sökbar i Application Insights

![Skärm bild som visar mer information, inklusive data som är särskilt för Service Fabric, som samlas in i Application Insights Service Fabric NuGet-paketet.](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights har en angiven vy för frågor mot alla data som ingår i. Välj "Metrics Explorer" överst på översikts sidan för att navigera till Application Insights Portal. Här kan du köra frågor mot anpassade händelser som anges före, begär Anden, undantag, prestanda räknare och andra mått med hjälp av Kusto-frågespråket. I följande exempel visas alla begär Anden under den senaste timmen.

![Information om Application Insights begäran](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

För att ytterligare utforska funktionerna i Application Insights-portalen går du till [Application Insights Portal-dokumentationen](../azure-monitor/app/overview-dashboard.md).

### <a name="configuring-application-insights-with-eventflow"></a>Konfigurera Application Insights med EventFlow

Om du använder EventFlow för att aggregera händelser måste du först importera NuGet- `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` paketet. Följande kod krävs i avsnittet *utdata* i *eventFlowConfig.jspå*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Se till att göra nödvändiga ändringar i dina filter, samt inkludera andra indata (tillsammans med deras respektive NuGet-paket).

## <a name="application-insights-sdk"></a>Application Insights SDK

Vi rekommenderar att du använder EventFlow och WAD som samlings lösningar, eftersom de tillåter en mer modulär metod för diagnostik och övervakning, det vill säga om du vill ändra dina utdata från EventFlow, behöver den inte ändra din faktiska Instrumentation, bara en enkel ändring av konfigurations filen. Om du bestämmer dig för att investera i med Application Insights och inte troligt vis kommer att byta till en annan plattform bör du titta i Application Insights "New SDK för att aggregera händelser och skicka dem till Application Insights. Det innebär att du inte längre behöver konfigurera EventFlow för att skicka data till Application Insights, utan i stället installerar ApplicationInsight Service Fabric NuGet-paketet. Information om paketet hittar du [här](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Application Insights stöd för mikrotjänster och behållare](https://azure.microsoft.com/blog/app-insights-microservices/) visar några av de nya funktionerna som används (för närvarande fortfarande i Beta versionen), vilket gör att du kan få bättre övervaknings alternativ med Application Insights. Dessa omfattar beroende spårning (används för att skapa en AppMap av alla dina tjänster och program i ett kluster och kommunikationen mellan dem) och bättre korrelation av spår som kommer från dina tjänster (hjälper till att bättre hitta ett problem i arbets flödet för ett program eller en tjänst).

Om du utvecklar i .NET och kommer att använda några av Service Fabrics programmerings modeller, och är villiga att använda Application Insights som plattform för visualisering och analys av händelse-och loggdata, rekommenderar vi att du går via Application Insights SDK-vägen som arbets flöde för övervakning och diagnostik. Läs dokumentationen om [Application Insights dokumentation](../azure-monitor/azure-monitor-app-hub.yml) och [spårnings loggar](../azure-monitor/app/asp-net-trace-logs.md) för att komma igång med att använda Application Insights för att samla in och Visa dina loggar.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Navigera Application Insights resursen i Azure Portal

När du har konfigurerat Application Insights som utdata för dina händelser och loggar, ska information börja visas i din Application Insights-resurs om några minuter. Navigera till Application Insights resurs, som tar dig till Application Insights resurs instrument panel. Välj **Sök** i Application Insights aktivitets fältet för att se de senaste spårningarna som har tagits emot och för att kunna filtrera genom dem.

*Metrics Explorer* är ett användbart verktyg för att skapa anpassade instrument paneler baserat på mått som dina program, tjänster och kluster kan rapportera. Se [utforska mått i Application Insights](../azure-monitor/essentials/metrics-charts.md) för att skapa några diagram för dig själv baserat på de data som du samlar in.

Genom att klicka på **analys** går du till Application Insights Analytics-portalen där du kan fråga händelser och spår med bättre omfattning och frivillighet. Läs mer om det här i [analys i Application Insights](../azure-monitor/logs/log-query-overview.md).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera aviseringar i AI](../azure-monitor/alerts/alerts-log.md) för att få meddelanden om förändringar i prestanda eller användning
* [Smart identifiering i Application Insights](../azure-monitor/app/proactive-diagnostics.md) utför en proaktiv analys av telemetri som skickas till Application Insights för att varna dig om potentiella prestanda problem
