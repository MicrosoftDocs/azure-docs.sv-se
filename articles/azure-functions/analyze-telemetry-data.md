---
title: Analysera Azure Functions telemetri i Application Insights
description: Lär dig att visa och fråga efter Azure Functions telemetridata som samlas in av och lagras i Azure Application insikter.
ms.topic: how-to
ms.date: 10/14/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: d06fe64ddc0475b5ca7d9c16876c8dfc9acda544
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729376"
---
# <a name="analyze-azure-functions-telemetry-in-application-insights"></a>Analysera Azure Functions telemetri i Application Insights 

Azure Functions integreras med Application Insights för att bättre kunna övervaka dina funktions appar. Application Insights samlar in telemetri-data som genererats av din Function-app, inklusive information som appen skriver till loggar. Application Insights integration aktive ras vanligt vis när din Function-app skapas. Om din Function-app saknar Instrumentation-nyckeln, måste du först [aktivera Application Insights-integrering](configure-monitoring.md#enable-application-insights-integration). 

Som standard lagras data som samlas in från din Function-app i Application Insights. I [Azure Portal](https://portal.azure.com)innehåller Application Insights en omfattande uppsättning visualiseringar av dina telemetridata. Du kan öka detalj nivån för fel loggar och fråga händelser och mått. Den här artikeln innehåller grundläggande exempel på hur du kan visa och fråga dina insamlade data. Mer information om hur du utforskar dina funktions AppData i Application Insights finns [Application Insights?](../azure-monitor/app/app-insights-overview.md). 

Mer information om datakvarhållning och potentiella lagrings kostnader finns i [data insamling, kvarhållning och lagring i Application Insights](../azure-monitor/app/data-retention-privacy.md).   

## <a name="viewing-telemetry-in-monitor-tab"></a>Visa telemetri på fliken övervaka

Med [Application Insights integrering aktive rad](configure-monitoring.md#enable-application-insights-integration)kan du Visa telemetridata på fliken **övervaka** .

1. På sidan Function-app väljer du en funktion som har körts minst en gång efter Application Insights har kon figurer ATS. Välj sedan **övervaka** i det vänstra fönstret. Välj **Uppdatera** regelbundet, tills listan över funktions anrop visas.

   ![Lista med anrop](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > Det kan ta upp till fem minuter innan listan visas medan telemetri-klienten registrerar data för överföring till servern. Fördröjningen gäller inte för [Live Metrics Stream](../azure-monitor/app/live-stream.md). Den här tjänsten ansluter till Functions-värden när du läser in sidan, så loggarna strömmas direkt till sidan.

1. Om du vill se loggar för ett visst funktions anrop, väljer du kolumn länken **date (UTC)** för detta anrop. Loggnings resultatet för det anropet visas på en ny sida.

   ![Information om anrop](media/functions-monitoring/invocation-details-ai.png)

1. Välj **Kör i Application Insights** om du vill visa källan till frågan som hämtar Azure Monitor loggdata i Azure log. Om det här är första gången du använder Azure Log Analytics i din prenumeration, uppmanas du att aktivera det.

1. När du har aktiverat Log Analytics visas följande fråga. Du kan se att frågeresultaten är begränsade till de senaste 30 dagarna ( `where timestamp > ago(30d)` ) och resultatet visar högst 20 rader ( `take 20` ). Listan med information om anrop för din funktion är däremot under de senaste 30 dagarna utan begränsning.

   ![Lista med Application Insights analys anrop](media/functions-monitoring/ai-analytics-invocation-list.png)

Mer information finns i [query telemetri-data](#query-telemetry-data) senare i den här artikeln.

## <a name="view-telemetry-in-application-insights"></a>Visa telemetri i Application Insights

Öppna Application Insights från en Function-app i [Azure Portal](https://portal.azure.com):

1. Bläddra till din Function-app i portalen.

1. Välj **Application Insights** under **Inställningar** på den vänstra sidan. 

1. Om det här är första gången du använder Application Insights med din prenumeration uppmanas du att aktivera den. Om du vill göra det väljer du **aktivera Application Insights** och väljer sedan **Använd** på nästa sida.

![Öppna Application Insights från översikts sidan för Function-appen](media/functions-monitoring/ai-link.png)

Information om hur du använder Application Insights finns i Application Insights- [dokumentationen](/azure/application-insights/). I det här avsnittet visas några exempel på hur du kan visa data i Application Insights. Om du redan är bekant med Application Insights kan du gå direkt till [avsnitten om hur du konfigurerar och anpassar telemetridata](configure-monitoring.md#configure-log-levels).

![Fliken Application Insights översikt](media/functions-monitoring/metrics-explorer.png)

Följande områden i Application Insights kan vara användbara när du ska utvärdera beteende, prestanda och fel i dina funktioner:

| Undersök
 | Beskrivning |
| ---- | ----------- |
| **[Fel](../azure-monitor/app/asp-net-exceptions.md)** |  Skapa diagram och aviseringar baserat på funktions fel och Server undantag. **Åtgärds namnet** är funktions namnet. Felen i beroenden visas inte om du inte implementerar anpassad telemetri för beroenden. |
| **[Prestanda](../azure-monitor/app/performance-counters.md)** | Analysera prestanda problem genom att Visa resursutnyttjande och data flöde per **moln roll instanser**. Dessa prestanda data kan vara användbara för fel sökning av scenarier där funktioner bogging de underliggande resurserna. |
| **[Mått](../azure-monitor/essentials/metrics-charts.md)** | Skapa diagram och aviseringar som baseras på mått. Mått inkluderar antalet funktions anrop, körnings tid och lyckade kostnader. |
| **[Live-mått](../azure-monitor/app/live-stream.md)** | Visa mått data när de skapas i nära real tid. |

## <a name="query-telemetry-data"></a>Fråga telemetri-data

[Application Insights Analytics](../azure-monitor/logs/log-query-overview.md) ger dig åtkomst till alla telemetridata i form av tabeller i en databas. Analytics innehåller ett frågespråk för extrahering, manipulering och visualisering av data. 

Välj **loggar** för att utforska eller fråga efter loggade händelser.

![Analys exempel](media/functions-monitoring/analytics-traces.png)

Här är ett exempel på en fråga som visar distributionen av förfrågningar per arbetare under de senaste 30 minuterna.

```kusto
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

De tabeller som är tillgängliga visas på fliken **schema** till vänster. Du kan hitta data som genererats av funktions anrop i följande tabeller:

| Tabell | Beskrivning |
| ----- | ----------- |
| **Anden** | Loggar som skapats av körnings miljön, skalnings styrenheten och spårningen från funktions koden. |
| **begäran** | En begäran för varje funktions anrop. |
| **undantag** | Eventuella undantag som har utlösts av körningen. |
| **customMetrics** | Antalet lyckade och misslyckade anrop, lyckade kostnader och varaktighet. |
| **customEvents** | Händelser som spåras av körningen, till exempel: HTTP-begäranden som utlöser en funktion. |
| **performanceCounters** | Information om prestanda för de servrar som funktionerna körs på. |

De andra tabellerna är för tillgänglighets test och telemetri för klienter och webbläsare. Du kan implementera anpassad telemetri för att lägga till data i dem.

I varje tabell finns vissa av de funktioner som är aktuella i ett `customDimensions` fält.  Följande fråga hämtar till exempel alla spår som har loggnings nivå `Error` .

```kusto
traces 
| where customDimensions.LogLevel == "Error"
```

Körningen innehåller `customDimensions.LogLevel` fälten och `customDimensions.Category` . Du kan ange ytterligare fält i loggar som du skriver i funktions koden. Ett exempel i C# finns i [strukturerad loggning](functions-dotnet-class-library.md#structured-logging) i guiden för utvecklare av .NET-klass bibliotek.

## <a name="query-scale-controller-logs"></a>Loggar för skalnings styrenhet för frågor

_Den här funktionen är i förhandsversion._

När du har aktiverat loggning och [Application Insights-integrering](configure-monitoring.md#enable-application-insights-integration)i både [skalnings styrenheten](configure-monitoring.md#configure-scale-controller-logs) kan du använda loggs ökningen Application Insights för att fråga efter loggarna för den utgivna skalnings kontrollen Loggar för skalnings styrenhet sparas i `traces` samlingen under kategorin **ScaleControllerLogs** .

Följande fråga kan användas för att söka efter alla loggar för skalnings styrenhet för den aktuella Function-appen inom den angivna tids perioden:

```kusto
traces 
| extend CustomDimensions = todynamic(tostring(customDimensions))
| where CustomDimensions.Category == "ScaleControllerLogs"
```

Följande fråga expanderas i föregående fråga för att visa hur du bara hämtar loggar som indikerar en skalnings förändring:

```kusto
traces 
| extend CustomDimensions = todynamic(tostring(customDimensions))
| where CustomDimensions.Category == "ScaleControllerLogs"
| where message == "Instance count changed"
| extend Reason = CustomDimensions.Reason
| extend PreviousInstanceCount = CustomDimensions.PreviousInstanceCount
| extend NewInstanceCount = CustomDimensions.CurrentInstanceCount
```

## <a name="consumption-plan-specific-metrics"></a>Förbruknings plan – vissa mått

Vid körning i en [förbruknings plan](consumption-plan.md)mäts körnings *kostnaden* för en enskild funktions funktion i *GB-sekunder*. Körnings kostnaden beräknas genom att kombinera minnes användningen med dess körnings tid. Mer information finns i [uppskatta förbruknings plan kostnader](functions-consumption-costs.md).

Följande telemetridata är speciella för mått som påverkar kostnaden för att köra funktioner i förbruknings planen.

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="azure-monitor-metrics"></a>Azure Monitor mått

Förutom telemetri-data som samlas in av Application Insights kan du också hämta information om hur Function-appen körs från [Azure Monitor mått](../azure-monitor/essentials/data-platform-metrics.md). Tillsammans med de vanliga [mått som är tillgängliga för App Service appar](../app-service/web-sites-monitor.md#understand-metrics)finns det två mått som är specifika för funktioner som är av intresse:

| Metric | Beskrivning |
| ---- | ---- |
| **FunctionExecutionCount** | Antal funktions körningar anger hur många gånger din funktions app har körts. Detta motsvarar antalet gånger som en funktion körs i din app. Det här måttet stöds för närvarande inte för Premium-och dedikerade-planer (App Service) som körs på Linux. |
| **FunctionExecutionUnits** | Funktions körnings enheter är en kombination av körnings tid och minnes användning.  Minnes data är inte ett mått som för närvarande är tillgängligt via Azure Monitor. Men om du vill optimera minnes användningen för din app kan använda prestanda räknar data som samlas in av Application Insights. Det här måttet stöds för närvarande inte för Premium-och dedikerade-planer (App Service) som körs på Linux.|

Mer information om hur du beräknar kostnader för en förbruknings plan med Application Insights data finns i [uppskatta förbruknings plan kostnader](functions-consumption-costs.md). Mer information om hur du använder Monitor Explorer för att visa mått finns i [komma igång med Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md).


## <a name="next-steps"></a>Nästa steg

Läs mer om övervaknings Azure Functions:

+ [Övervaka Azure Functions](functions-monitoring.md)
+ [Så här konfigurerar du övervakning för Azure Functions](configure-monitoring.md)