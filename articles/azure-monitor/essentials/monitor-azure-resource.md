---
title: Övervaka Azure-resurser med Azure Monitor | Microsoft Docs
description: Beskriver hur du samlar in och analyserar övervaknings data från resurser i Azure med hjälp av Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: af2803a945821a23ff59f196967d792a6593009f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033392"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Övervaka Azure-resurser med Azure Monitor
När du har viktiga program och affärs processer som förlitar sig på Azure-resurser, vill du övervaka resurserna för deras tillgänglighet, prestanda och drift. I den här artikeln beskrivs övervaknings data som genereras av Azure-resurser och hur du kan använda funktionerna i Azure Monitor för att analysera och varna för dessa data.

> [!IMPORTANT]
> Den här artikeln gäller för alla tjänster i Azure som använder Azure Monitor. Beräknings resurser, inklusive virtuella datorer och App Service, genererar samma övervaknings data som beskrivs här, men har även ett gäst operativ system som också kan generera loggar och mått. I övervaknings dokumentationen för dessa tjänster finns mer information om hur du samlar in och analyserar dessa data.

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?
Azure Monitor är en fullständig stack övervaknings tjänst i Azure som innehåller en fullständig uppsättning funktioner för att övervaka dina Azure-resurser, förutom resurser i andra moln och lokalt. [Azure Monitor data plattform](../data-platform.md) samlar in data i [loggar](../logs/data-platform-logs.md) och [Mät värden](../essentials/data-platform-metrics.md) där de kan analyseras med en fullständig uppsättning övervaknings verktyg. Se den fullständiga listan över program och tjänster som kan övervakas av Azure Monitor i [vad övervakas av Azure Monitor?](../monitor-reference.md).

När du skapar en Azure-resurs är Azure Monitor aktive rad och börjar samla in mått och aktivitets loggar som du kan [Visa och analysera i Azure Portal](#monitoring-in-the-azure-portal). Med viss konfiguration kan du samla in ytterligare övervaknings data och aktivera ytterligare funktioner. Mer information om konfigurations krav finns i [övervaknings data](#monitoring-data) nedan.


## <a name="costs-associated-with-monitoring"></a>Kostnader för övervakning
Det kostar inget att analysera övervaknings data som samlas in som standard. Det här gäller bland annat följande:

- Samla in plattforms mått och analysera dem med Metrics Explorer.
- Samla in aktivitets logg och analysera den i Azure Portal.
- Skapar en varnings regel för aktivitets loggen.

Det finns inga Azure Monitor kostnader för insamling och export av loggar och mått, men det kan finnas relaterade kostnader som är kopplade till målet:

- Kostnader som är kopplade till data inmatning och kvarhållning vid insamling av loggar och mått i Log Analytics-arbetsyta. Se [Azure Monitor prissättning för Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).
- Kostnader som är kopplade till data lagring vid insamling av loggar och mått till ett Azure Storage-konto. Se [Azure Storage priser för Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Kostnader som är kopplade till strömmande av Event Hub när du vidarebefordrar loggar och mått till Azure Event Hubs. Se [priser för Azure Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

Det kan finnas Azure Monitor kostnader som är kopplade till följande. Se [Azure Monitor priser](https://azure.microsoft.com/pricing/details/monitor/):

- Kör en logg fråga.
- Skapa en varnings regel för mått eller logg fråga.
- Skickar ett meddelande från en varnings regel.
- Åtkomst till mått via API.

## <a name="monitoring-data"></a>Övervaka data
Resurser i Azure genererar [loggar](../logs/data-platform-logs.md) och [mått](../essentials/data-platform-metrics.md) som visas i följande diagram. Se dokumentationen för varje Azure-tjänst för de data som de genererar och eventuella ytterligare lösningar eller insikter som de tillhandahåller.

![Översikt](media/monitor-azure-resource/logs-metrics.png)



- [Plattforms mått](../essentials/data-platform-metrics.md) – numeriska värden som samlas in automatiskt med jämna mellanrum och som beskriver viss aspekt av en resurs vid en viss tidpunkt. 
- [Resurs loggar](./platform-logs-overview.md) – ger insikter om åtgärder som utförts i en Azure-resurs (data planet), till exempel för att få en hemlighet från en Key Vault eller en begäran till en databas. Innehållet och strukturen i resurs loggar varierar beroende på Azure-tjänsten och resurs typen.
- [Aktivitets logg](./platform-logs-overview.md) – ger inblick i åtgärderna på varje Azure-resurs i prenumerationen från utsidan (hanterings planet), till exempel när du skapar en ny resurs eller startar en virtuell dator. Det här är information om vad, vem och när för Skriv åtgärder (skicka, skicka och ta bort) som har tagits till resurserna i din prenumeration.


## <a name="configuration-requirements"></a>Konfigurationskrav

### <a name="configure-monitoring"></a>Konfigurera övervakning
Vissa övervaknings data samlas in automatiskt, men du kan behöva utföra vissa konfigurationer beroende på dina behov. Se informationen nedan om du vill ha detaljerad information för varje typ av övervaknings data.

- [Plattforms mått](../essentials/data-platform-metrics.md) – plattforms mått samlas in automatiskt i [Azure Monitor Mät värden](../essentials/data-platform-metrics.md) utan konfiguration krävs. Skapa en diagnostisk inställning för att skicka poster till Azure Monitor loggar eller vidarebefordra dem utanför Azure.
- [Resurs loggar](./platform-logs-overview.md) – resurs loggar genereras automatiskt av Azure-resurser, men samlas inte in utan en diagnostisk inställning.  Skapa en diagnostisk inställning för att skicka poster till Azure Monitor loggar eller vidarebefordra dem utanför Azure.
- [Aktivitets logg](./platform-logs-overview.md) – aktivitets loggen samlas in automatiskt utan konfiguration som krävs och kan visas i Azure Portal. Skapa en diagnostisk inställning för att kopiera dem till Azure Monitor loggar eller vidarebefordra dem utanför Azure.

### <a name="log-analytics-workspace"></a>Log Analytics-arbetsyta
Att samla in data i Azure Monitor loggar kräver en Log Analytics arbets yta. Du kan börja övervaka din tjänst snabbt genom att skapa en ny arbets yta, men det kan finnas ett värde i använda en arbets yta som samlar in data från andra tjänster. Mer information om hur du skapar en arbets yta och hur du skapar [distributioner för Azure Monitor loggar](../logs/design-logs-deployment.md) finns i [skapa en Log Analytics arbets yta i Azure Portal](../logs/quick-create-workspace.md) . Om du använder en befintlig arbets yta i din organisation behöver du nödvändiga behörigheter enligt beskrivningen i [Hantera åtkomst till loggdata och arbets ytor i Azure Monitor](../logs/manage-access.md). 





## <a name="diagnostic-settings"></a>Diagnostikinställningar
Diagnostiska inställningar definierar var resurs loggar och-mått för en viss resurs ska skickas. Möjliga destinationer är:

- [Log Analytics arbets yta](./resource-logs.md#send-to-log-analytics-workspace) som gör det möjligt att analysera data med andra övervaknings data som samlas in av Azure monitor med hjälp av kraftfulla logg frågor och också för att utnyttja andra Azure Monitor funktioner som logg aviseringar och visualiseringar. 
- [Event Hub](./resource-logs.md#send-to-azure-event-hubs) för att strömma data till externa system, till exempel Siem från tredje part och andra Log Analytics-lösningar. 
- [Azure Storage-konto](./resource-logs.md#send-to-azure-storage) som är användbart för granskning, statisk analys eller säkerhets kopiering.

Följ proceduren i [skapa diagnostisk inställning för att samla in plattforms loggar och mått i Azure](../essentials/diagnostic-settings.md) för att skapa och hantera diagnostikinställningar via Azure Portal. Se [skapa diagnostisk inställning i Azure med hjälp av en Resource Manager-mall](./resource-manager-diagnostic-settings.md) för att definiera dem i en mall och aktivera fullständig övervakning för en resurs när den skapas.


## <a name="monitoring-in-the-azure-portal"></a>Övervakning i Azure Portal
 Du kan komma åt övervaknings data för de flesta Azure-resurser från resurs menyn i Azure Portal. Detta ger dig åtkomst till en enskild resurs data med hjälp av standard Azure Monitor verktyg. Vissa Azure-tjänster tillhandahåller olika alternativ, så du bör hänvisa till dokumentationen för tjänsten för ytterligare information. Använd **Azure Monitor** -menyn om du vill analysera data från alla övervakade resurser. 

### <a name="overview"></a>Översikt
Många tjänster omfattar övervaknings data på sina **översikts** sidor så att du snabbt kan se hur de fungerar. Detta är vanligt vis baserat på en delmängd av plattforms måtten som lagras i Azure Monitor Mät värden. Andra övervaknings alternativ är vanligt vis tillgängliga i ett **övervaknings** avsnitt på tjänstens meny.

![Översiktssidan](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Insikter och lösningar 
Vissa tjänster tillhandahåller verktyg utöver standard funktionerna i Azure Monitor. [Insikter](../monitor-reference.md) ger en anpassad övervaknings upplevelse som bygger på Azure Monitor data plattform och standard funktioner. [Lösningar](../insights/solutions.md) tillhandahåller fördefinierad övervaknings logik som bygger på Azure Monitor loggar. 

Om en tjänst har Azure Monitor insikter kan du komma åt den från **övervakning** i varje resurs meny. Få till gång till alla insikter och lösningar från **Azure Monitor** -menyn.

![Insikter i Azure Portal](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Mått
Analysera mått i Azure Portal med hjälp av [Metrics Explorer](./metrics-getting-started.md) som är tillgängligt på meny alternativet **mått** för de flesta tjänsterna. Med det här verktyget kan du arbeta med enskilda mått eller kombinera flera för att identifiera korrelationer och trender. 

- Mer information om hur du använder Metrics Explorer finns i [komma igång med Azure Metrics Explorer](./metrics-getting-started.md) .
- Se [avancerade funktioner i Azure Metrics Explorer](../essentials/metrics-charts.md) för avancerade funktioner i mått Utforskaren, till exempel att använda flera mått och använda filter och delning.

![Mått Utforskaren i Azure Portal](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Aktivitetslogg 
Visa poster i aktivitets loggen i Azure Portal med det ursprungliga filtret inställt på den aktuella resursen. Kopiera aktivitets loggen till en Log Analytics-arbetsyta för att komma åt den för att använda den i logg frågor och arbets böcker. 

- Se [Visa och hämta händelser i Azure aktivitets loggen](../essentials/activity-log.md#view-the-activity-log) om du vill ha mer information om hur du visar aktivitets loggen och hämtar poster med hjälp av olika metoder.
- I dokumentationen för Azure-tjänsten finns information om vilka händelser som loggas.

![Aktivitetslogg](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar
Azure Monitor loggar konsoliderar loggar och mått från flera tjänster och andra data källor för analys med ett kraftfullt verktyg för frågor. Som beskrivs ovan skapar du en diagnostisk inställning för att samla in plattforms mått, aktivitets logg och resurs loggar i en Log Analytics arbets yta i Azure Monitor.

Med [Log Analytics](../logs/log-analytics-tutorial.md) kan du arbeta med [logg frågor](../logs/log-query-overview.md), vilket är en kraftfull funktion i Azure Monitor som gör att du kan utföra avancerad analys av loggdata med ett fullständigt aktuellt frågespråk. Öppna Log Analytics från **loggar** på menyn **övervakning** för en Azure-resurs för att arbeta med logg frågor med hjälp av resursen som [fråge omfånget](../logs/scope.md#query-scope). På så sätt kan du analysera data i flera tabeller för just den resursen. Använd **loggar** från Azure Monitor-menyn för att få åtkomst till loggar för alla resurser. 

- I [Kom igång med logg frågor i Azure Monitor](../logs/get-started-queries.md) finns en själv studie kurs om hur du använder frågespråket som används för att skriva logg frågor.
- Mer information om hur resurs loggar samlas in i Azure Monitor loggar och information om hur du kommer åt dem i en fråga finns i [samla in Azure-resursposter i Log Analytics arbets ytan i Azure Monitor](./resource-logs.md#send-to-log-analytics-workspace) .
- Se [insamlings läge](./resource-logs.md#send-to-log-analytics-workspace) för en förklaring av hur resurs logg data är strukturerade i Azure Monitor loggar.
- I dokumentationen för varje Azure-tjänst finns mer information om tabellen i Azure Monitor loggar.

![Log Analytics i Azure Portal](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Övervakning från kommando raden
Du kan komma åt övervaknings data som samlats in från din resurs från en kommando rad eller inkludera i ett skript med hjälp av [Azure PowerShell](/powershell/azure/) eller [Azure kommando rads gränssnitt](/cli/azure/). 

- Se [CLI Metrics-referens](/cli/azure/monitor/metrics) för åtkomst till mått data från cli.
- Se [CLI Log Analytics referens](/cli/azure/ext/log-analytics/monitor/log-analytics) för åtkomst till Azure Monitor loggar data med hjälp av en logg fråga från cli.
- Se [Azure PowerShell mått referens](/powershell/module/azurerm.insights/get-azurermmetric) för åtkomst till mått data från Azure PowerShell.
- Se [Azure PowerShell logg fråga referens](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) för åtkomst till Azure Monitor loggar data med hjälp av en logg fråga från Azure PowerShell.

## <a name="monitoring-from-rest-api"></a>Övervakning från REST API
Inkludera övervaknings data som samlats in från din resurs i ett anpassat program med hjälp av en REST API.

- Mer information om hur du får åtkomst till mått från Azure Monitor REST API finns i [genom gång av Azure monitoring REST API](./rest-api-walkthrough.md) .
- Information om hur du kommer åt Azure Monitor loggar data med hjälp av en logg fråga från Azure PowerShell finns i [Azure Log Analytics REST API](https://dev.loganalytics.io/) .

## <a name="alerts"></a>Aviseringar
[Aviseringar](../alerts/alerts-overview.md) proaktivt meddelar dig och kan vidta åtgärder när viktiga villkor finns i dina övervaknings data. Du skapar en aviserings regel som definierar ett mål för aviseringen, villkoren för om du vill skapa en avisering och vilka åtgärder som ska vidtas som svar.

Olika typer av övervaknings data används för olika typer av varnings regler.

- [Aktivitets logg avisering](../alerts/alerts-activity-log.md) – skapa en avisering när en post skapas i aktivitets loggen som matchar vissa villkor. På så sätt kan du bli meddelad till exempel när en viss typ av resurs skapas eller om en konfigurations ändring Miss lyckas.
- [Mått varning](../alerts/alerts-metric.md) – skapa en avisering när ett Metric-värde överskrider ett visst tröskelvärde. Mått varningar är mer svars bara än andra aviseringar och kan lösas automatiskt när problemet har åtgärd ATS.
- [Varning om logg fråga](../alerts/alerts-log.md) – kör en logg fråga med jämna mellanrum och skapa en avisering om ett visst villkor hittas. På så sätt kan du utföra komplexa analyser i flera data uppsättningar och.

Använd **aviseringar** från en resurs meny för att visa aviseringar och hantera aviserings regler för resursen. Endast aktivitets logg aviseringar och mått varningar använder enskilda Azure-resurser som mål. Varnings aviseringar för logg frågor använder en Log Analytics-arbetsyta som mål och baseras på en fråga som har åtkomst till alla loggar som lagras på arbets ytan. Använd Azure Monitor-menyn om du vill visa och hantera aviseringar för alla resurser och aviserings reglerna för att hantera logg frågor.

- Se artiklarna för de olika typerna av aviseringar ovan för information om hur du skapar aviserings regler.
- Se [skapa och hantera åtgärds grupper i Azure Portal](../alerts/action-groups.md) för information om hur du skapar en åtgärds grupp som gör att du kan hantera svar på aviseringar.



## <a name="next-steps"></a>Nästa steg

* Mer information om resurs loggar för olika Azure-tjänster finns i [tjänster, scheman och kategorier som stöds för Azures resurs loggar](./resource-logs-schema.md) .