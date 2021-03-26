---
title: Azure Monitor-loggar
description: Beskriver Azure Monitor loggar som används för avancerad analys av övervaknings data.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 6cb85bc1da7d98d3e2ad46229588a91d971ce014
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567298"
---
# <a name="azure-monitor-logs-overview"></a>Översikt över Azure Monitor-loggar
Azure Monitor loggar är en funktion i Azure Monitor som samlar in och ordnar logg-och prestanda data från [övervakade resurser](../monitor-reference.md). Data från olika källor, t. ex. [plattforms loggar](../essentials/platform-logs-overview.md) från Azure-tjänster, logg-och prestanda data från [virtuella datorer agenter](../agents/agents-overview.md)och användnings-och prestanda data från [program](../app/app-insights-overview.md) kan konsol IDE ras till en enda arbets yta så att de kan analyseras tillsammans med ett avancerat frågespråk som kan snabbt analysera miljon tals poster. Du kan utföra en enkel fråga som bara hämtar en bestämd uppsättning poster eller utföra avancerad data analys för att identifiera kritiska mönster i dina övervaknings data. Arbeta med logg frågor och deras resultat interaktivt med Log Analytics, Använd dem i en varnings regel för att proaktivt informera om problem eller visualisera resultaten i en arbets bok eller instrument panel.

> [!NOTE]
> Azure Monitor loggar är en hälft av data plattformen som stöder Azure Monitor. Den andra är [Azure Monitor Mät värden](../essentials/data-platform-metrics.md) som lagrar numeriska data i en tids serie databas. Detta gör dessa data mer lätta än data i Azure Monitor loggar och stöder nästan real tids scenarier som gör dem särskilt användbara för aviseringar och snabb identifiering av problem. Mått kan bara lagra numeriska data i en viss struktur, medan loggar kan lagra olika typer av data med sin egen struktur. Du kan också utföra komplex analys på loggar data med hjälp av logg frågor som inte kan användas för analys av mät data.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Vad kan du göra med Azure Monitor loggar?
I följande tabell beskrivs några av de olika sätt som du kan använda loggar i Azure Monitor:

|  | Description |
|:---|:---|
| **Analysera** | Använd [Log Analytics](./log-analytics-tutorial.md) i Azure Portal för att skriva [logg frågor](./log-query-overview.md) och analysera logg data interaktivt med en kraftfull analys motor |
| **Varning** | Konfigurera en [logg varnings regel](../alerts/alerts-log.md) som skickar ett meddelande eller [automatiserar en åtgärd](../alerts/action-groups.md) när resultatet av frågan matchar ett visst resultat. |
| **Visualisera** | Fäst frågeresultaten som återges som tabeller eller diagram på en [Azure-instrumentpanel](../../azure-portal/azure-portal-dashboards.md).<br>Skapa en [arbets bok](../visualize/workbooks-overview.md) som ska kombineras med flera data uppsättningar i en interaktiv rapport. <br>Exportera resultatet av en fråga för att [Power BI](../visualize/powerbi.md) att använda olika visualiseringar och dela med användare utanför Azure.<br>Exportera resultatet av en fråga till [Grafana](../visualize/grafana-plugin.md) för att dra nytta av dess instrument panel och kombinera med andra data källor.|
| **Insikter** | Stöd för [insikter](../monitor-reference.md#insights-and-core-solutions) som ger en anpassad övervaknings miljö för specifika program och tjänster.  |
| **Hämta** | Få åtkomst till logg frågeresultat från en kommando rad med hjälp av [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Kom åt logg frågeresultaten från en kommando rad med [PowerShell-cmdletar](/powershell/module/az.operationalinsights).<br>Få åtkomst till logg frågeresultaten från ett anpassat program med hjälp av [REST API](https://dev.loganalytics.io/). |
| **Export** | Konfigurera [automatisk export av loggdata](./logs-data-export.md) till Azure Storage-konto eller azure-Event Hubs.<br>Bygg ett arbets flöde för att hämta loggdata och kopiera det till en extern plats med hjälp av [Logic Apps](./logicapp-flow-connector.md). |

![Översikt över loggar](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>Datainsamling
När du skapar en arbets yta för Log Analytics måste du konfigurera olika källor för att skicka data. Inga data samlas in automatiskt. Den här konfigurationen är olika beroende på data källan. Du kan till exempel [skapa diagnostikinställningar](../essentials/diagnostic-settings.md) för att skicka resurs loggar från Azure-resurser till arbets ytan. [Aktivera VM-insikter](../vm/vminsights-enable-overview.md) för att samla in data från virtuella datorer. Konfigurera [data källor på arbets ytan](../agents/data-sources.md) för att samla in ytterligare händelser och prestanda data.

- Se [vad som övervakas av Azure Monitor?](../monitor-reference.md) för en fullständig lista över data källor som du kan konfigurera för att skicka data till Azure Monitor loggar.


## <a name="log-analytics-workspaces"></a>Log Analytics-arbetsytor
Data som samlas in av Azure Monitor loggar lagras i en eller flera [Log Analytics arbets ytor](./design-logs-deployment.md). Arbets ytan definierar den geografiska platsen för data, åtkomst behörighet som definierar vilka användare som har åtkomst till data och konfigurations inställningar som pris nivå och datakvarhållning.  

Du måste skapa minst en arbets yta för att kunna använda Azure Monitor loggar. En enskild arbets yta kan vara tillräcklig för alla dina övervaknings data, eller så kan du välja att skapa flera arbets ytor, beroende på dina behov. Du kan till exempel ha en arbets yta för dina produktions data och en annan för testning. 

- Skapa en ny arbets yta genom att se [skapa en Log Analytics arbets yta i Azure Portal](./quick-create-workspace.md) .
- Se [utforma dina Azure Monitor loggar distribution](design-logs-deployment.md) på överväganden för att skapa flera arbets ytor.

## <a name="data-structure"></a>Datastruktur
Logg frågor hämtar data från en Log Analytics-arbetsyta. Varje arbets yta innehåller flera tabeller som är ordnade i separata kolumner med flera rader med data. Varje tabell definieras av en unik uppsättning kolumner som delas av rader med data från data källan. 

[![Struktur för Azure Monitor loggar](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Loggdata från Application Insights lagras också i Azure Monitor loggar, men den lagras olika beroende på hur programmet har kon figurer ATS. För ett arbets grupps program lagras data i en Log Analytics arbets yta i en standard uppsättning tabeller för att lagra data, till exempel program begär Anden, undantag och sid visningar. Flera program kan använda samma arbets yta. För ett klassiskt program lagras inte data i en Log Analytics-arbetsyta. Den använder samma frågespråk och du skapar och kör frågor med hjälp av samma Log Analytics-verktyg i Azure Portal. Data för klassiska program lagras separat från varandra. Den allmänna strukturen är densamma som arbets ytans program, även om tabell-och kolumn namnen skiljer sig åt. Se [arbets ytans baserade resurs ändringar](../app/apm-tables.md) för en detaljerad jämförelse av schemat för arbets ytans baserade och klassiska program.


> [!NOTE]
> Vi ger fortfarande fullständig bakåtkompatibilitet för dina Application Insights klassiska resurs frågor, arbets böcker och loggbaserade aviseringar inom Application Insightss upplevelsen. Om du vill fråga/Visa mot den [nya arbetsytebaserade tabell strukturen/schemat](../app/apm-tables.md) måste du först gå till din Log Analytics-arbetsyta. Under förhands granskningen får du till gång till den klassiska Application Insights fråge upplevelsen genom att välja **loggar** från Application Insights fönstret. Mer information finns i [fråge omfånget](./scope.md) .


[![Azure Monitor loggar struktur för Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>Loggfrågor
Data hämtas från en Log Analytics arbets yta med en logg fråga som är en skrivskyddad begäran för att bearbeta data och returnera resultat. Logg frågor skrivs i [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/), som är samma frågespråk som används av Azure datautforskaren. Du kan skriva logg frågor i Log Analytics för att interaktivt analysera resultaten, använda dem i varnings regler för att proaktivt informera om problem eller ta med resultaten i arbets böcker eller instrument paneler. Insikter innehåller fördefinierade frågor som stöder sina vyer och arbets böcker.

- I [logg frågor i Azure Monitor](./log-query-overview.md) finns en lista över var logg frågor används och referenser till självstudier och annan dokumentation som hjälper dig att komma igång.

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
Använd Log Analytics, som är ett verktyg i Azure Portal, för att redigera och köra logg frågor och analysera resultaten interaktivt. Du kan sedan använda de frågor som du skapar för att stödja andra funktioner i Azure Monitor till exempel aviseringar och arbets böcker för logg frågor. Åtkomst Log Analytics från alternativet **loggar** på Azure Monitor-menyn eller från de flesta andra tjänster i Azure Portal.

- En beskrivning av Log Analytics finns [i Översikt över Log Analytics i Azure Monitor](./log-analytics-overview.md) . 
- Se [Log Analytics själv studie kursen](./log-analytics-tutorial.md) för att gå igenom hur du använder Log Analytics funktioner för att skapa en enkel logg fråga och analysera resultatet.



## <a name="relationship-to-azure-data-explorer"></a>Relation till Azure Datautforskaren
Azure Monitor loggar baseras på Azure Datautforskaren. En Log Analytics-arbetsyta är ungefär detsamma som en databas i Azure Datautforskaren, tabeller struktureras samma och båda använder samma KQL (Kusto Query Language). Erfarenheten av att använda Log Analytics att arbeta med Azure Monitor frågor i Azure Portal liknar upplevelsen med Azure Datautforskaren-webbgränssnittet. Du kan till och med [ta med data från en Log Analytics arbets yta i en Azure datautforskaren-fråga](/azure/data-explorer/query-monitor-data). 


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [logg frågor](./log-query-overview.md) för att hämta och analysera data från en Log Analytics-arbetsyta.
- Lär dig mer om [mått i Azure Monitor](../essentials/data-platform-metrics.md).
- Lär dig mer om [övervaknings data som är tillgängliga](../agents/data-sources.md) för olika resurser i Azure.