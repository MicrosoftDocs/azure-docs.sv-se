---
title: Övervaka Azure-resurser med Azure Monitor | Microsoft Docs
description: Beskriver hur du samlar in och analyserar övervakningsdata från resurser i Azure med hjälp av Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: cb778d826ef094d71fd27f3c10bc1f2c292baa47
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862407"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Övervaka Azure-resurser med Azure Monitor
När du har kritiska program och affärsprocesser som förlitar sig på Azure-resurser vill du övervaka resursernas tillgänglighet, prestanda och drift. I den här artikeln beskrivs övervakningsdata som genereras av Azure-resurser och hur du kan använda funktionerna i Azure Monitor för att analysera och varna om dessa data.

> [!IMPORTANT]
> Den här artikeln gäller för alla tjänster i Azure som använder Azure Monitor. Beräkningsresurser, inklusive virtuella datorer och App Service, genererar samma övervakningsdata som beskrivs här, men har även ett gästoperativsystemet som också kan generera loggar och mått. Mer information om hur du samlar in och analyserar dessa data finns i övervakningsdokumentationen för dessa tjänster.

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?
Azure Monitor är en fullständig stackövervakningstjänst i Azure som tillhandahåller en komplett uppsättning funktioner för att övervaka dina Azure-resurser utöver resurser i andra moln och lokalt. [Dataplattformen Azure Monitor samlar](../data-platform.md) in data [](../logs/data-platform-logs.md) i loggar och mått där de kan [analyseras](../essentials/data-platform-metrics.md) tillsammans med en fullständig uppsättning övervakningsverktyg. Se den fullständiga listan över program och tjänster som kan övervakas av Azure Monitor i [Vad övervakas av Azure Monitor?](../monitor-reference.md).

När du skapar en Azure-resurs aktiveras Azure Monitor och börjar samla in mått och aktivitetsloggar som du kan visa och analysera i [Azure Portal](#monitoring-in-the-azure-portal). Med viss konfiguration kan du samla in ytterligare övervakningsdata och aktivera ytterligare funktioner. Se [Övervakningsdata](#monitoring-data) nedan för information om eventuella konfigurationskrav.


## <a name="costs-associated-with-monitoring"></a>Kostnader som är kopplade till övervakning
Det kostar inget att analysera övervakningsdata som samlas in som standard. Det här gäller bland annat följande:

- Samla in plattformsmått och analysera dem med Metrics Explorer.
- Samla in aktivitetsloggen och analysera den i Azure Portal.
- Skapa en aviseringsregel för aktivitetslogg.

Det finns inga Azure Monitor kostnader för att samla in och exportera loggar och mått, men det kan finnas relaterade kostnader kopplade till målet:

- Kostnader som är kopplade till datainmatning och kvarhållning vid insamling av loggar och mått på Log Analytics-arbetsytan. Se [Azure Monitor priser för Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).
- Kostnader som är kopplade till datalagring vid insamling av loggar och mått till ett Azure-lagringskonto. Se [Azure Storage för Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Kostnader som är kopplade till händelsehubbströmning vid vidarebefordran av loggar och mått till Azure Event Hubs. Se [Azure Event Hubs prissättning.](https://azure.microsoft.com/pricing/details/event-hubs/)

Det kan finnas Azure Monitor kostnader som är kopplade till följande. Se [Azure Monitor priser:](https://azure.microsoft.com/pricing/details/monitor/)

- Köra en loggfråga.
- Skapa en aviseringsregel för mått eller loggfrågor.
- Skicka ett meddelande från en aviseringsregel.
- Åtkomst till mått via API.

## <a name="monitoring-data"></a>Övervaka data
Resurser i Azure [genererar](../logs/data-platform-logs.md) loggar [och mått](../essentials/data-platform-metrics.md) som visas i följande diagram. Se dokumentationen för varje Azure-tjänst för specifika data som de genererar och eventuella ytterligare lösningar eller insikter som de tillhandahåller.

![Översikt](media/monitor-azure-resource/logs-metrics.png)



- [Plattformsmått](../essentials/data-platform-metrics.md) – Numeriska värden som samlas in automatiskt med jämna mellanrum och beskriver någon aspekt av en resurs vid en viss tidpunkt. 
- [Resursloggar](./platform-logs-overview.md) – Ger inblick i åtgärder som utfördes inom en Azure-resurs (dataplanet), till exempel att hämta en hemlighet från en Key Vault eller göra en begäran till en databas. Innehållet och strukturen för resursloggar varierar beroende på Azure-tjänsten och resurstypen.
- [Aktivitetslogg](./platform-logs-overview.md) – Ger inblick i åtgärderna för varje Azure-resurs i prenumerationen utifrån (hanteringsplanet), till exempel att skapa en ny resurs eller starta en virtuell dator. Det här är information om vad, vem och när för eventuella skrivåtgärder (PUT, POST, DELETE) som vidtas för resurserna i din prenumeration.


## <a name="configuration-requirements"></a>Konfigurationskrav

### <a name="configure-monitoring"></a>Konfigurera övervakning
Vissa övervakningsdata samlas in automatiskt, men du kan behöva utföra viss konfiguration beroende på dina krav. Se informationen nedan för specifik information för varje typ av övervakningsdata.

- [Plattformsmått](../essentials/data-platform-metrics.md) – Plattformsmått samlas in automatiskt i [Azure Monitor mått utan](../essentials/data-platform-metrics.md) att någon konfiguration krävs. Skapa en diagnostikinställning för att skicka poster till Azure Monitor loggar eller vidarebefordra dem utanför Azure.
- [Resursloggar](./platform-logs-overview.md) – Resursloggar genereras automatiskt av Azure-resurser men samlas inte in utan en diagnostikinställning.  Skapa en diagnostikinställning för att skicka poster till Azure Monitor loggar eller vidarebefordra dem utanför Azure.
- [Aktivitetslogg](./platform-logs-overview.md) – Aktivitetsloggen samlas in automatiskt utan att någon konfiguration krävs och kan visas i Azure Portal. Skapa en diagnostikinställning för att kopiera dem till Azure Monitor loggar eller vidarebefordra dem utanför Azure.

### <a name="log-analytics-workspace"></a>Log Analytics-arbetsyta
Insamling av data i Azure Monitor Logs kräver en Log Analytics-arbetsyta. Du kan börja övervaka tjänsten snabbt genom att skapa en ny arbetsyta, men det kan finnas värde i att använda en arbetsyta som samlar in data från andra tjänster. Se [Skapa en Log Analytics-arbetsyta i Azure Portal](../logs/quick-create-workspace.md) för information om hur du skapar en arbetsyta och designar [distributionen av Azure Monitor-loggar](../logs/design-logs-deployment.md) för att avgöra den bästa arbetsytedesignen för dina behov. Om du använder en befintlig arbetsyta i din organisation behöver du lämpliga behörigheter enligt beskrivningen i Hantera åtkomst till loggdata och [arbetsytor i Azure Monitor](../logs/manage-access.md). 





## <a name="diagnostic-settings"></a>Diagnostikinställningar
Diagnostikinställningar definierar var resursloggar och mått för en viss resurs ska skickas. Möjliga mål är:

- [Log Analytics-arbetsytan](./resource-logs.md#send-to-log-analytics-workspace) där du kan analysera data med andra övervakningsdata som samlas in av Azure Monitor med hjälp av kraftfulla loggfrågor och även använda andra Azure Monitor-funktioner som loggaviseringar och visualiseringar. 
- [Händelsehubbb](./resource-logs.md#send-to-azure-event-hubs) för att strömma data till externa system, till exempel SIEM från tredje part och andra log analytics-lösningar. 
- [Azure Storage-konto](./resource-logs.md#send-to-azure-storage) som är användbart för granskning, statisk analys eller säkerhetskopiering.

Följ proceduren i Inställningen Skapa diagnostik för att samla [in plattformsloggar](../essentials/diagnostic-settings.md) och mått i Azure för att skapa och hantera diagnostikinställningar via Azure Portal. Se [Skapa diagnostikinställning i Azure med en Resource Manager för](./resource-manager-diagnostic-settings.md) att definiera dem i en mall och aktivera fullständig övervakning för en resurs när den skapas.


## <a name="monitoring-in-the-azure-portal"></a>Övervakning i Azure Portal
 Du kan komma åt övervakningsdata för de flesta Azure-resurser från resursens meny i Azure Portal. Detta ger dig åtkomst till en enskild resurss data med hjälp av standardverktyg Azure Monitor resurs. Vissa Azure-tjänster har olika alternativ, så du bör läsa mer i dokumentationen för den tjänsten. Använd menyn **Azure Monitor** för att analysera data från alla övervakade resurser. 

### <a name="overview"></a>Översikt
Många tjänster innehåller övervakningsdata på **översiktssidan** som en snabb överblick över hur de fungerar. Detta baseras vanligtvis på en delmängd av plattformsmått som lagras Azure Monitor mått. Andra övervakningsalternativ är vanligtvis tillgängliga i **avsnittet** Övervakning på tjänstens meny.

![Översiktssidan](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Insikter och lösningar 
Vissa tjänster tillhandahåller verktyg utöver standardfunktionerna i Azure Monitor. [Insikter](../monitor-reference.md) ger en anpassad övervakningsupplevelse som bygger på Azure Monitor dataplattform och standardfunktioner. [Lösningar](../insights/solutions.md) tillhandahåller fördefinierad övervakningslogik som bygger Azure Monitor loggar. 

Om en tjänst har Azure Monitor insikter kan du komma åt den **från Övervakning** på varje resurss meny. Få åtkomst till alla insikter och lösningar **Azure Monitor** menyn.

![Insikter i Azure Portal](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Mått
Analysera mått i den här Azure Portal [metrics explorer](./metrics-getting-started.md) som är tillgänglig från **menyalternativet Mått** för de flesta tjänster. Med det här verktyget kan du arbeta med enskilda mått eller kombinera flera för att identifiera korrelationer och trender. 

- I [Komma igång med Azure Metrics Explorer](./metrics-getting-started.md) du grunderna i att använda Metrics Explorer.
- Avancerade [funktioner i Azure Metrics Explorer](../essentials/metrics-charts.md) för avancerade funktioner i Metrics Explorer, till exempel att använda flera mått och tillämpa filter och dela upp.

![Metrics Explorer i Azure Portal](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Aktivitetslogg 
Visa poster i aktivitetsloggen i Azure Portal med det första filtret inställt på den aktuella resursen. Kopiera aktivitetsloggen till en Log Analytics-arbetsyta för att komma åt den och använda den i loggfrågor och arbetsböcker. 

- Mer [information om hur du visar aktivitetsloggen och](../essentials/activity-log.md#view-the-activity-log) hämtar poster med olika metoder finns i Visa och hämta Händelser i Azure-aktivitetsloggen.
- De specifika händelser som loggas finns i dokumentationen för din Azure-tjänst.

![Aktivitetslogg](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar
Azure Monitor Loggar konsoliderar loggar och mått från flera tjänster och andra datakällor för analys med ett kraftfullt frågeverktyg. Enligt beskrivningen ovan skapar du en diagnostikinställning för att samla in plattformsmått, aktivitetsloggar och resursloggar till en Log Analytics-arbetsyta i Azure Monitor.

[Med Log Analytics](../logs/log-analytics-tutorial.md) kan du arbeta med loggfrågor [,](../logs/log-query-overview.md)vilket är en kraftfull funktion i Azure Monitor som gör att du kan utföra avancerad analys av loggdata med hjälp av ett fullständigt frågespråk. Öppna Log Analytics från **Loggar** på menyn **Övervakning** för att en Azure-resurs ska fungera med loggfrågor med hjälp av resursen som [frågeomfång.](../logs/scope.md#query-scope) På så sätt kan du analysera data i flera tabeller för bara den resursen. Använd **Loggar** från Azure Monitor för att komma åt loggar för alla resurser. 

- Se [Kom igång med loggfrågor i Azure Monitor](../logs/get-started-queries.md) en självstudie om hur du använder frågespråket som används för att skriva loggfrågor.
- Se Samla in Azure-resursloggar på [Log Analytics-arbetsytan i Azure Monitor](./resource-logs.md#send-to-log-analytics-workspace) för information om hur resursloggar samlas in i Azure Monitor-loggar och information om hur du kommer åt dem i en fråga.
- Se [Samlingsläge](./resource-logs.md#send-to-log-analytics-workspace) för en förklaring av hur resursloggdata struktureras i Azure Monitor loggar.
- Se dokumentationen för varje Azure-tjänst för information om dess tabell i Azure Monitor Loggar.

![Log Analytics i Azure Portal](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Övervakning från kommandoraden
Du kan komma åt övervakningsdata som samlas in från din resurs från en kommandorad eller inkludera i ett [skript med hjälp Azure PowerShell](/powershell/azure/) eller [Azure-kommandoradsgränssnittet](/cli/azure/). 

- Se [REFERENS för CLI-mått](/cli/azure/monitor/metrics) för åtkomst till måttdata från CLI.
- Se [CLI Log Analytics-referens](/cli/azure/monitor/log-analytics) för åtkomst till Azure Monitor loggdata med hjälp av en loggfråga från CLI.
- Se [Azure PowerShell måttreferens för åtkomst](/powershell/module/azurerm.insights/get-azurermmetric) till måttdata från Azure PowerShell.
- Se [Azure PowerShell för att komma åt](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) data Azure Monitor loggar med hjälp av en loggfråga från Azure PowerShell.

## <a name="monitoring-from-rest-api"></a>Övervakning från REST API
Inkludera övervakningsdata som samlas in från din resurs i ett anpassat program med hjälp av REST API.

- Se [genomgången REST API Azure Monitoring Azure Monitor REST API.](./rest-api-walkthrough.md)
- Se [Azure Log Analytics REST API](https://dev.loganalytics.io/) information om hur du kommer Azure Monitor loggdata med hjälp av en loggfråga från Azure PowerShell.

## <a name="alerts"></a>Aviseringar
[Aviseringar](../alerts/alerts-overview.md) meddelar dig proaktivt och kan eventuellt vidta åtgärder när viktiga villkor finns i dina övervakningsdata. Du skapar en aviseringsregel som definierar ett mål för aviseringen, villkoren för att skapa en avisering och eventuella åtgärder som ska vidtas som svar.

Olika typer av övervakningsdata används för olika typer av aviseringsregler.

- [Aktivitetsloggavisering](../alerts/alerts-activity-log.md) – Skapa en avisering när en post skapas i aktivitetsloggen som matchar specifika villkor. På så sätt kan du meddelas, till exempel när en viss typ av resurs skapas eller om en konfigurationsändring misslyckas.
- [Måttavisering](../alerts/alerts-metric.md) – Skapa en avisering när ett måttvärde överskrider ett visst tröskelvärde. Måttaviseringar är mer responsiva än andra aviseringar och kan lösas automatiskt när problemet åtgärdas.
- [Logga frågeavisering](../alerts/alerts-log.md) – Kör en loggfråga med jämna mellanrum och skapa en avisering om ett visst villkor hittas. På så sätt kan du utföra komplexa analyser över flera datauppsättningar och .

Använd **Aviseringar** från en resurs-meny för att visa aviseringar och hantera aviseringsregler för den resursen. Endast aktivitetsloggaviseringar och måttaviseringar använder enskilda Azure-resurser som mål. Logga frågeaviseringar använder en Log Analytics-arbetsyta som mål och baseras på en fråga som kan komma åt alla loggar som lagras på den arbetsytan. Använd menyn Azure Monitor för att visa och hantera aviseringar för alla resurser och hantera aviseringsregler för loggfrågor.

- Mer information om hur du skapar aviseringsregler finns i artiklarna för de olika typerna av aviseringar ovan.
- Se [Skapa och hantera åtgärdsgrupper i Azure Portal](../alerts/action-groups.md) information om hur du skapar en åtgärdsgrupp som gör att du kan hantera svar på aviseringar.



## <a name="next-steps"></a>Nästa steg

* Se [Tjänster, scheman och kategorier som stöds för Azure-resursloggar](./resource-logs-schema.md) för information om resursloggar för olika Azure-tjänster.
