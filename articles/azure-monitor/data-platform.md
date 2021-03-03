---
title: Azure Monitor data plattform | Microsoft Docs
description: Övervaknings data som samlas in av Azure Monitor delas upp i mått som är lätta och stöder nästan real tids scenarier och loggar som används för avancerad analys.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 7356b9bb814f8bca5465fe74d48409b9dbca6d3b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731705"
---
# <a name="azure-monitor-data-platform"></a>Azure Monitor data plattform

Att ge dig möjlighet att vara medveten om dagens komplexa dator miljöer som kör distribuerade program som är beroende av både moln tjänster och lokala tjänster, kräver insamling av drift data från alla skikt och varje komponent i det distribuerade systemet. Du måste kunna utföra djupgående insikter om dessa data och konsolidera dem till ett enda fönster i glaset med olika perspektiv för att stödja antalet intressenter i din organisation.

[Azure Monitor](overview.md) samlar in och samlar in data från en mängd olika källor till en gemensam data plattform där det kan användas för analys, visualisering och aviseringar. Det ger en konsekvent upplevelse av data från flera källor, vilket ger dig djupgående insikter över alla övervakade resurser och till och med data från andra tjänster som lagrar data i Azure Monitor.


![Översikt över Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Information om att observera data i Azure Monitor
Mått, loggar och distribuerade spår kallas ofta tre pelare för att kunna observera. Det här är de olika typerna av data som ett övervaknings verktyg måste samla in och analysera för att ge en tillräckligt god lämplighet för ett övervakat system. Du kan se om du korrelerar data från flera pelare och aggregerar data över hela uppsättningen resurser som övervakas. Eftersom Azure Monitor lagrar data från flera källor tillsammans, kan data korreleras och analyseras med hjälp av en gemensam uppsättning verktyg. Den korrelerar också data över flera Azure-prenumerationer och-klienter, förutom att vara värd för data för andra tjänster.

Azure-resurser genererar en betydande mängd övervaknings data. Azure Monitor konsoliderar dessa data tillsammans med övervaknings data från andra källor till antingen en mått-eller loggar plattform. Var och en är optimerad för särskilda övervaknings scenarier och har stöd för olika funktioner i Azure Monitor. Funktioner som data analys, visualiseringar eller varningar kräver att du förstår skillnaderna så att du kan implementera ditt nödvändiga scenario på det mest effektiva och kostnads effektiva sättet. Insikter i Azure Monitor som [Application Insights](app/app-insights-overview.md) eller [VM Insights](vm/vminsights-overview.md) har analys verktyg som gör att du kan fokusera på det specifika övervaknings scenariot utan att behöva förstå skillnaderna mellan de två typerna av data. 


### <a name="metrics"></a>Mått
[Mått](essentials/data-platform-metrics.md) är numeriska värden som beskriver någon aspekt av ett system vid en viss tidpunkt. De samlas in med jämna mellanrum och identifieras med en tidsstämpel, ett namn, ett värde samt en eller flera definierande etiketter. Mått kan aggregeras via en mängd olika algoritmer, jämföras med andra mått och analyseras för trender över tid. 

Mått i Azure Monitor lagras i en databas för tids serier som är optimerad för att analysera tidsstämplade data. Detta gör mått som är särskilt lämpade för aviseringar och snabb identifiering av problem. De kan tala om för dig hur systemet presterar, men det måste vanligt vis kombineras med loggar för att identifiera rotor saken till problem.

Mått är tillgängliga för interaktiv analys i Azure Portal med [Azure Metrics Explorer](essentials/metrics-getting-started.md). De kan läggas till i en [Azure-instrumentpanel](app/tutorial-app-dashboards.md) för visualisering i kombination med andra data och används för [aviseringar](alerts/alerts-metric.md)i nära real tid.

Läs mer om Azure Monitor mått, inklusive deras data källor i [mått i Azure Monitor](essentials/data-platform-metrics.md).

### <a name="logs"></a>Loggar
[Loggar](logs/data-platform-logs.md) är händelser som inträffat i systemet. De kan innehålla olika typer av data och kan vara strukturerade eller kostnads fria formulär text med en tidsstämpel. De kan skapas sporadiskt när händelser i miljön genererar loggposter. System som är under hög belastning genererar vanligtvis större loggvolymer.

Loggar i Azure Monitor lagras i en Log Analytics arbets yta som baseras på [Azure datautforskaren](/azure/data-explorer/) , vilket ger en kraftfull analys motor och ett [Avancerat frågespråk](/azure/kusto/query/). Loggarna ger vanligt vis tillräckligt med information för att tillhandahålla en komplett kontext för det problem som identifieras och är värdefullt för att identifiera problem med rot fall.

> [!NOTE]
> Det är viktigt att skilja mellan Azure Monitor loggar och källor till loggdata i Azure. Händelser på prenumerations nivå i Azure skrivs till exempel till en [aktivitets logg](essentials/platform-logs-overview.md) som du kan visa från Azure Monitor-menyn. De flesta resurser kommer att skriva funktions information till en [resurs logg](essentials/platform-logs-overview.md) som du kan vidarebefordra till olika platser. Azure Monitor loggar är en logg data plattform som samlar in aktivitets loggar och resurs loggar tillsammans med andra övervaknings data för att ge en djup analys över hela uppsättningen med resurser.


 Du kan arbeta med [logg frågor](logs/log-query-overview.md) interaktivt med [Log Analytics](logs/log-query-overview.md) i Azure Portal eller lägga till resultaten till en [Azure-instrumentpanel](app/tutorial-app-dashboards.md) för visualisering i kombination med andra data. Du kan också skapa [logg aviseringar](alerts/alerts-log.md) som utlöser en avisering baserat på resultatet av en schema fråga.

Läs mer om Azure Monitor loggar, inklusive data källor i [loggar i Azure Monitor](logs/data-platform-logs.md).

### <a name="distributed-traces"></a>Distribuerade spår
Spårningar är serier med relaterade händelser som följer en användar förfrågan via ett distribuerat system. De kan användas för att fastställa beteendet för program kod och prestanda för olika transaktioner. Även om loggar ofta skapas av enskilda komponenter i ett distribuerat system, mäter en spårning åtgärd och prestanda för ditt program över hela uppsättningen komponenter.

Distribuerad spårning i Azure Monitor aktive ras med [Application Insights SDK](app/distributed-tracing.md)och spårnings data lagras med andra program logg data som samlas in av Application Insights. Detta gör det tillgängligt för samma analys verktyg som andra loggdata, inklusive logg frågor, instrument paneler och aviseringar.

Läs mer om distribuerad spårning i [Vad är distribuerad spårning?](app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Jämför Azure Monitor mått och loggar

I följande tabell jämförs mått och loggar i Azure Monitor.

| Attribut  | Mått | Loggar |
|:---|:---|:---|
| Fördelar | Lätta och kan användas nära real tids scenarier, till exempel aviseringar. Idealisk för snabb identifiering av problem. | Analyseras med RTF-frågespråket. Perfekt för djup analys och identifiering av rotor saken. |
| Data | Endast numeriska värden | Text eller numeriska data |
| Struktur | Standard uppsättning egenskaper, inklusive exempel tid, resurs som övervakas, ett numeriskt värde. Vissa mått innehåller flera dimensioner för ytterligare definition. | Unik uppsättning egenskaper beroende på logg typen. |
| Samling | Samlas in med jämna mellanrum. | Kan samlas in sporadiskt när händelser utlöser en post som ska skapas. |
| Visa i Azure-portalen | Metrics Explorer | Log Analytics |
| Data källor innehåller | Plattforms mått som samlas in från Azure-resurser.<br>Program som övervakas av Application Insights.<br>Anpassad definierad av program eller API. | Program-och resurs loggar.<br>Övervaknings lösningar.<br>Agenter och VM-tillägg.<br>Program begär Anden och undantag.<br>Azure Security Center.<br>API för data insamling. |

## <a name="collect-monitoring-data"></a>Samla in övervaknings data
Olika [data källor för Azure Monitor](agents/data-sources.md) skriver till antingen en Log Analytics arbets yta (loggar) eller Azure Monitor mått databas (mått) eller båda. Vissa källor kommer att skriva direkt till dessa data lager, medan andra kan skriva till en annan plats, till exempel Azure Storage och kräva viss konfiguration för att fylla i loggar eller mått. 

Se [mått i Azure Monitor](essentials/data-platform-metrics.md) och [loggar i Azure Monitor](logs/data-platform-logs.md) för en lista över olika data källor som fyller varje typ.


## <a name="stream-data-to-external-systems"></a>Strömma data till externa system
Förutom att använda verktygen i Azure för att analysera övervaknings data kan du ha ett krav för att vidarebefordra det till ett externt verktyg, till exempel en SIEM-produkt (Security information and Event Management). Den här vidarebefordran görs normalt direkt från övervakade resurser via [Azure Event Hubs](../event-hubs/index.yml). Vissa källor kan konfigureras för att skicka data direkt till en Event Hub medan du kan använda en annan process, till exempel en Logic app, för att hämta nödvändiga data. Mer information finns i [data Ströms övervaknings data i Azure till en händelsehubben för användning av ett externt verktyg](essentials/stream-monitoring-data-event-hubs.md) .



## <a name="next-steps"></a>Nästa steg

- Läs mer om [mått i Azure Monitor](essentials/data-platform-metrics.md).
- Läs mer om [loggar i Azure Monitor](logs/data-platform-logs.md).
- Lär dig mer om [övervaknings data som är tillgängliga](agents/data-sources.md) för olika resurser i Azure.

