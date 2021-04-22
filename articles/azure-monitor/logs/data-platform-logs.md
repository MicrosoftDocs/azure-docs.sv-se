---
title: Azure Monitor-loggar
description: Beskriver Azure Monitor loggar som används för avancerad analys av övervakningsdata.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 9794c5f048b8795652e4b31e0134b36a77715abe
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873387"
---
# <a name="azure-monitor-logs-overview"></a>Översikt över Azure Monitor-loggar
Azure Monitor Logs är en funktion i Azure Monitor som samlar in och organiserar logg- och prestandadata från [övervakade resurser](../monitor-reference.md). Data från olika [](../essentials/platform-logs-overview.md) källor, till exempel plattformsloggar från Azure-tjänster, logg- [](../app/app-insights-overview.md) och prestandadata från agenter för virtuella [datorer,](../agents/agents-overview.md)samt användnings- och prestandadata från program kan konsolideras till en enda arbetsyta så att de kan analyseras tillsammans med ett avancerat frågespråk som snabbt kan analysera miljontals poster. Du kan utföra en enkel fråga som bara hämtar en specifik uppsättning poster eller utför avancerad dataanalys för att identifiera kritiska mönster i dina övervakningsdata. Arbeta med loggfrågor och deras resultat interaktivt med Hjälp av Log Analytics, använd dem i en aviseringsregler för att proaktivt meddelas om problem eller visualisera resultaten i en arbetsbok eller instrumentpanel.

> [!NOTE]
> Azure Monitor Logs är hälften av dataplattformen som stöder Azure Monitor. Den andra [är Azure Monitor Metrics som](../essentials/data-platform-metrics.md) lagrar numeriska data i en tidsseriedatabas. Detta gör dessa data mer lätta än data i Azure Monitor Logs och kan stödja scenarier i nära realtid, vilket gör dem särskilt användbara för avisering och snabb identifiering av problem. Mått kan dock bara lagra numeriska data i en viss struktur, medan loggar kan lagra en mängd olika datatyper var och en med sin egen struktur. Du kan också utföra komplexa analyser på loggdata med hjälp av loggfrågor som inte kan användas för analys av måttdata.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Vad kan du göra med Azure Monitor loggar?
I följande tabell beskrivs några av de olika sätt som du kan använda loggar i Azure Monitor:

|  | Description |
|:---|:---|
| **Analysera** | Använd [Log Analytics](./log-analytics-tutorial.md) i Azure Portal för att skriva [loggfrågor](./log-query-overview.md) och interaktivt analysera loggdata med hjälp av en kraftfull analysmotor |
| **Varning** | Konfigurera en [loggaviseringsregel](../alerts/alerts-log.md) som skickar ett meddelande eller [vidtar automatiserade](../alerts/action-groups.md) åtgärder när resultatet av frågan matchar ett visst resultat. |
| **Visualisera** | Fäst frågeresultat som återges som tabeller eller diagram på en [Azure-instrumentpanel.](../../azure-portal/azure-portal-dashboards.md)<br>Skapa en [arbetsbok](../visualize/workbooks-overview.md) för att kombinera med flera datauppsättningar i en interaktiv rapport. <br>Exportera resultatet av en fråga till [Power BI](../visualize/powerbi.md) att använda olika visualiseringar och dela med användare utanför Azure.<br>Exportera resultatet av en fråga till [Grafana för att](../visualize/grafana-plugin.md) utnyttja dess instrumentpaneler och kombinera dem med andra datakällor.|
| **Insikter** | Stöd [för](../monitor-reference.md#insights-and-core-solutions) insikter som ger en anpassad övervakningsupplevelse för specifika program och tjänster.  |
| **Hämta** | Åtkomst till loggfrågans resultat från en kommandorad med [hjälp av Azure CLI](/cli/azure/monitor/log-analytics).<br>Åtkomst till loggfrågans resultat från en kommandorad med Hjälp av [PowerShell-cmdlets](/powershell/module/az.operationalinsights).<br>Åtkomst till loggfrågor från ett anpassat program med [hjälp av REST API](https://dev.loganalytics.io/). |
| **Export** | Konfigurera [automatisk export av loggdata till](./logs-data-export.md) Azure Storage-konto eller Azure Event Hubs.<br>Skapa ett arbetsflöde för att hämta loggdata och kopiera dem till en extern plats med [hjälp av Logic Apps](./logicapp-flow-connector.md). |

![Översikt över loggar](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>Datainsamling
När du har skapat en Log Analytics-arbetsyta måste du konfigurera olika källor för att skicka deras data. Inga data samlas in automatiskt. Den här konfigurationen varierar beroende på datakällan. Du kan till [exempel skapa diagnostikinställningar](../essentials/diagnostic-settings.md) för att skicka resursloggar från Azure-resurser till arbetsytan. [Aktivera VM-insikter](../vm/vminsights-enable-overview.md) för att samla in data från virtuella datorer. Konfigurera [datakällor på arbetsytan för att](../agents/data-sources.md) samla in ytterligare händelser och prestandadata.

- Se [Vad övervakas av Azure Monitor?](../monitor-reference.md) för en fullständig lista över datakällor som du kan konfigurera för att skicka data till Azure Monitor Logs.


## <a name="log-analytics-workspaces"></a>Log Analytics-arbetsytor
Data som samlas in Azure Monitor loggar lagras på en eller flera [Log Analytics-arbetsytor.](./design-logs-deployment.md) Arbetsytan definierar den geografiska platsen för data, åtkomstbehörigheter som definierar vilka användare som kan komma åt data och konfigurationsinställningar som prisnivå och datalagring.  

Du måste skapa minst en arbetsyta för att använda Azure Monitor Loggar. En enskild arbetsyta kan vara tillräcklig för alla dina övervakningsdata, eller så kan du välja att skapa flera arbetsytor beroende på dina behov. Du kan till exempel ha en arbetsyta för dina produktionsdata och en annan för testning. 

- Se [Skapa en Log Analytics-arbetsyta i Azure Portal](./quick-create-workspace.md) för att skapa en ny arbetsyta.
- Se [Designing your Azure Monitor Logs deployment on](design-logs-deployment.md) considerations for creating multiple workspaces (Utforma distribution av Azure Monitor loggar om överväganden för att skapa flera arbetsytor.

## <a name="data-structure"></a>Datastruktur
Loggfrågor hämtar sina data från en Log Analytics-arbetsyta. Varje arbetsyta innehåller flera tabeller som är ordnade i separata kolumner med flera rader med data. Varje tabell definieras av en unik uppsättning kolumner som delas av raderna med data som tillhandahålls av datakällan. 

[![Azure Monitor för loggar](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Loggdata från Application Insights lagras också i Azure Monitor loggar, men de lagras olika beroende på hur ditt program har konfigurerats. För ett arbetsytebaserat program lagras data i en Log Analytics-arbetsyta i en standarduppsättning med tabeller för att lagra data, till exempel programbegäranden, undantag och sidvisningar. Flera program kan använda samma arbetsyta. För ett klassiskt program lagras inte data på en Log Analytics-arbetsyta. Den använder samma frågespråk och du skapar och kör frågor med samma Log Analytics-verktyg i Azure Portal. Data för klassiska program lagras dock separat från varandra. Den allmänna strukturen är samma som arbetsytebaserade program, men tabell- och kolumnnamnen skiljer sig åt. Se [Ändringar av arbetsytebaserade](../app/apm-tables.md) resurser för en detaljerad jämförelse av schemat för arbetsytebaserade och klassiska program.


> [!NOTE]
> Vi ger fortfarande fullständig bakåtkompatibilitet för dina Application Insights klassiska resursfrågor, arbetsböcker och logga-baserade aviseringar i den Application Insights upplevelsen. Om du vill fråga/visa [mot den nya arbetsytebaserade tabellstrukturen/schemat](../app/apm-tables.md) måste du först navigera till Log Analytics-arbetsytan. Under förhandsversionen får du **åtkomst till** den Application Insights frågeupplevelsen om du väljer Loggar Application Insights fönstret. Mer [information finns](./scope.md) i Frågeomfång.


[![Azure Monitor för Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>Loggfrågor
Data hämtas från en Log Analytics-arbetsyta med hjälp av en loggfråga som är en skrivskyddad begäran om att bearbeta data och returnera resultat. Loggfrågor skrivs i [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/), som är samma frågespråk som används av Azure Data Explorer. Du kan skriva loggfrågor i Log Analytics för att interaktivt analysera resultaten, använda dem i aviseringsregler för att proaktivt meddelas om problem eller inkludera deras resultat i arbetsböcker eller instrumentpaneler. Insikter omfattar fördefinierade frågor som stöder deras vyer och arbetsböcker.

- Se [Loggfrågor i Azure Monitor](./log-query-overview.md) en lista över var loggfrågor används och referenser till självstudier och annan dokumentation för att komma igång.

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
Använd Log Analytics, som är ett verktyg i Azure Portal, för att redigera och köra loggfrågor och interaktivt analysera deras resultat. Du kan sedan använda de frågor som du skapar för att stödja andra funktioner i Azure Monitor till exempel logga frågeaviseringar och arbetsböcker. Öppna Log Analytics från **alternativet Loggar** i menyn Azure Monitor eller från de flesta andra tjänster i Azure Portal.

- Se [Översikt över Log Analytics i Azure Monitor](./log-analytics-overview.md) för en beskrivning av Log Analytics. 
- Se [Log Analytics-självstudie](./log-analytics-tutorial.md) för att gå igenom hur du använder Log Analytics-funktioner för att skapa en enkel loggfråga och analysera dess resultat.



## <a name="relationship-to-azure-data-explorer"></a>Relation till Azure Data Explorer
Azure Monitor baseras på Azure Data Explorer. En Log Analytics-arbetsyta är ungefär samma som en databas i Azure Data Explorer, tabeller är strukturerade på samma sätt och båda använder samma Kusto Query Language (KQL). Användningen av Log Analytics för att arbeta med Azure Monitor frågor i Azure Portal liknar upplevelsen med hjälp av Azure Data Explorer webbgränssnittet. Du kan även [inkludera data från en Log Analytics-arbetsyta i en Azure Data Explorer fråga](/azure/data-explorer/query-monitor-data). 


## <a name="next-steps"></a>Nästa steg

- Lär dig [mer om loggfrågor](./log-query-overview.md) för att hämta och analysera data från en Log Analytics-arbetsyta.
- Lär dig [mer om mått i Azure Monitor](../essentials/data-platform-metrics.md).
- Lär dig mer [om övervakningsdata som är](../agents/data-sources.md) tillgängliga för olika resurser i Azure.