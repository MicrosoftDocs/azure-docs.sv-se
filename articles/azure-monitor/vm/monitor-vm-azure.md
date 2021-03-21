---
title: Övervaka virtuella Azure-datorer med Azure Monitor
description: Beskriver hur du samlar in och analyserar övervaknings data från virtuella datorer i Azure med hjälp av Azure Monitor.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: 2c93471436030f9260f4fa0d95d656c27d382346
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047051"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Övervaka virtuella Azure-datorer med Azure Monitor
Den här artikeln beskriver hur du använder Azure Monitor för att samla in och analysera övervaknings data från virtuella Azure-datorer för att upprätthålla deras hälsa. Virtuella datorer kan övervakas för tillgänglighet och prestanda med Azure Monitor som [andra Azure](../essentials/monitor-azure-resource.md)-resurser, men de är unika för andra resurser eftersom du också behöver övervaka gäst operativ systemet och de arbets belastningar som körs i den. 

> [!NOTE]
> Den här artikeln innehåller en fullständig översikt över begreppen och alternativen för övervakning av virtuella datorer i Azure Monitor. Om du vill börja övervaka dina virtuella datorer snabbt utan att behöva fokusera på underliggande koncept, se [snabb start: övervaka en virtuell Azure-dator med Azure Monitor](./quick-monitor-azure-vm.md).


## <a name="differences-from-other-azure-resources"></a>Skillnader jämfört med andra Azure-resurser
[Övervakning av Azure-resurser med Azure Monitor](../essentials/monitor-azure-resource.md) beskriver övervaknings data som genereras av Azure-resurser och hur du kan använda funktionerna i Azure Monitor för att analysera och varna för dessa data. Du kan samla in och agera på samma övervaknings data från virtuella Azure-datorer med följande skillnader:

-  [Plattforms mått](../essentials/data-platform-metrics.md) samlas in automatiskt för virtuella datorer men bara för den [virtuella värden](#monitoring-data). Du behöver en agent för att samla in prestanda data från gäst operativ systemet. 
- Virtuella datorer genererar inte [resurs loggar](../essentials/platform-logs-overview.md) som ger inblick i åtgärder som utförts i en Azure-resurs. Du kan använda en agent för att samla in loggdata från gäst operativ systemet.
- Du kan skapa [diagnostikinställningar](../essentials/diagnostic-settings.md) för en virtuell dator för att skicka plattforms mått till andra mål, till exempel lagrings-och händelse nav, men du kan inte konfigurera diagnostikinställningar i Azure Portal. 

## <a name="monitoring-data"></a>Övervaka data
Virtuella datorer i Azure genererar [loggar](../logs/data-platform-logs.md) och [mått](../essentials/data-platform-metrics.md) som visas i följande diagram.

![Översikt](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Virtuell dator värd
Virtuella datorer i Azure genererar följande data för den virtuella dator värden på samma sätt som andra Azure-resurser enligt beskrivningen i [övervaknings data](../essentials/monitor-azure-resource.md#monitoring-data).

- [Plattforms mått](../essentials/data-platform-metrics.md) – numeriska värden som samlas in automatiskt med jämna mellanrum och som beskriver viss aspekt av en resurs vid en viss tidpunkt. Plattforms mått samlas in för den virtuella värddatorn, men du måste använda det diagnostiska tillägget för att samla in mått för gäst operativ systemet.
- [Aktivitets logg](../essentials/platform-logs-overview.md) – ger inblick i åtgärderna på varje Azure-resurs i prenumerationen från utsidan (hanterings planet). För en virtuell dator inkluderar detta information som när den startades och eventuella konfigurations ändringar.


### <a name="guest-operating-system"></a>Gästoperativsystem
Om du vill samla in data från gäst operativ systemet på en virtuell dator behöver du en agent, som körs lokalt på varje virtuell dator och skickar data till Azure Monitor. Flera agenter är tillgängliga för Azure Monitor med varje insamling av olika data och skrivning av data till olika platser. Få en detaljerad jämförelse av de olika agenterna i [Översikt över Azure Monitors agenter](../agents/agents-overview.md). 

- [Log Analytics agent](../agents/agents-overview.md#log-analytics-agent) – tillgängligt för virtuella datorer i Azure, andra moln miljöer och lokalt. Samlar in data till Azure Monitor loggar. Stöder lösningar för VM-insikter och övervakning. Det här är samma agent som används för System Center Operations Manager.
- [Beroende agent](../agents/agents-overview.md#dependency-agent) – samlar in data om de processer som körs på den virtuella datorn och deras beroenden. Förlitar sig på Log Analytics agent för att överföra data till Azure och har stöd för VM Insights, Tjänstkarta och Wire Data 2.0 lösningar.
- [Azure Diagnostic-tillägg](../agents/agents-overview.md#azure-diagnostics-extension) – endast tillgängligt för Azure Monitor virtuella datorer. Kan samla in data till flera platser, men som främst används för att samla in prestanda data i Azure Monitor mått för virtuella Windows-datorer.
- [Teleympkvistar-agent](../essentials/collect-custom-metrics-linux-telegraf.md) – samla in prestanda data från virtuella Linux-datorer i Azure Monitor Mät värden.


## <a name="configuration-requirements"></a>Konfigurationskrav
Om du vill aktivera alla funktioner i Azure Monitor för övervakning av en virtuell dator måste du samla in övervaknings data från den virtuella värddatorn och gäst operativ systemet till både [Azure Monitor mått](../logs/data-platform-logs.md) och [Azure Monitor loggar](../logs/data-platform-logs.md). I följande tabell visas den konfiguration som måste utföras för att aktivera den här samlingen. Du kan välja att inte utföra alla dessa steg beroende på dina specifika krav.

| Konfigurations steg | Slutförda åtgärder | Aktiverade funktioner |
|:---|:---|:---|
| Ingen konfiguration | -Värd plattforms mått som samlas in på mått.<br>– Aktivitets logg insamlad. | – Metrics Explorer för värden.<br>– Mått aviseringar för värden.<br>– Aktivitets logg aviseringar. |
| [Aktivera VM Insights](#enable-vm-insights) | -Log Analytics-agenten installerad.<br>-Beroende agent installerad.<br>– Gäst prestanda data som samlas in i loggar.<br>– Bearbeta och beroende information som samlas in i loggar. | – Prestanda diagram och arbets böcker för gäst prestanda data.<br>-Logga frågor om prestanda data för gäst.<br>– Logg aviseringar för prestanda data för gäst.<br>-Beroende karta. |
| [Installera Diagnostics-tillägget och teleympkvistar-agenten](#enable-diagnostics-extension-and-telegraf-agent) | – Gäst prestanda data som samlas in på mått. | – Metrics Explorer för gäst.<br>– Mått aviseringar för gäst.  |
| [Konfigurera Log Analytics-arbetsyta](#configure-log-analytics-workspace) | -Händelser som samlas in från gäst. | – Logga frågor om gäst händelser.<br>– Logga aviseringar för gäst händelser. |
| [Skapa diagnostisk inställning för virtuell dator](#collect-platform-metrics-and-activity-log) | -Plattforms mått som samlas in i loggar.<br>– Aktivitets logg som samlas in i loggar. | -Logga frågor för värd mått.<br>– Logg aviseringar för värd mått.<br>-Logga frågor för aktivitets loggen.

Vart och ett av dessa konfigurations steg beskrivs i följande avsnitt.

### <a name="enable-vm-insights"></a>Aktivera VM Insights
[VM Insights](../vm/vminsights-overview.md) är en [inblick](../monitor-reference.md) i Azure Monitor som är det viktigaste verktyget för att övervaka virtuella datorer i Azure Monitor. Det ger följande ytterligare värde över standard Azure Monitor funktionerna.

- Förenklad onboarding av Log Analytics agent och beroende agent för att aktivera övervakning av en virtuell dators gäst operativ system och arbets belastningar. 
- Fördefinierade prestanda diagram och arbets böcker som du kan använda för att analysera kärn prestanda mått från den virtuella datorns gäst operativ system.
- Beroende karta som visar processer som körs på varje virtuell dator och de sammankopplade komponenterna med andra datorer och externa källor.

![Prestanda vy för VM Insights](media/monitor-vm-azure/vminsights-01.png)

![Vy över VM Insights-kartor](media/monitor-vm-azure/vminsights-02.png)


Aktivera VM Insights från alternativet **insikter** på menyn för den virtuella datorn i Azure Portal. Se [Aktivera översikt över VM-insikter](vminsights-enable-overview.md) för information och andra konfigurations metoder.

![Aktivera VM Insights](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Konfigurera Log Analytics-arbetsyta
Log Analytics agenten som används av VM Insights skickar data till en [Log Analytics arbets yta](../logs/data-platform-logs.md). Du kan aktivera insamling av ytterligare prestanda data, händelser och andra övervaknings data från agenten genom att konfigurera Log Analytics arbets ytan. Den behöver bara konfigureras en gång, eftersom en agent som ansluter till arbets ytan automatiskt kommer att hämta konfigurationen och börja samla in de data som definierats omedelbart. 

Du kan komma åt konfigurationen för arbets ytan direkt från VM-insikter genom att välja **arbets ytans konfiguration** från **Kom igång**. Klicka på arbets ytans namn för att öppna dess meny.

![Konfiguration av arbetsyta](media/monitor-vm-azure/workspace-configuration.png)

Välj **Avancerade inställningar** på menyn arbets yta och sedan **data** för att konfigurera data källor. För Windows-agenter väljer du **händelse loggar för Windows** och lägger till vanliga händelse loggar som *system* och *program*. För Linux-agenter väljer du **syslog** och lägger till vanliga funktioner som *kerning* och *daemon*. Se [agent data källor i Azure Monitor](../agents/agent-data-sources.md) om du vill ha en lista över tillgängliga data källor och information om hur du konfigurerar dem. 

![Konfigurera händelser](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> Du kan konfigurera prestanda räknare för att samlas in från arbets ytans konfiguration, men det kan vara överflödigt med prestanda räknarna som samlas in av VM Insights. VM Insights samlar in den vanligaste uppsättningen räknare med en frekvens på en gång per minut. Konfigurera bara prestanda räknare som ska samlas in av arbets ytan om du vill samla in räknare som inte redan har samlats in av VM Insights eller om du har befintliga frågor med prestanda data.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Aktivera Diagnostics-tillägg och teleympkvistar-agenten
VM-insikter baseras på Log Analytics-agenten som skickar data till en Log Analytics-arbetsyta. Detta stöder flera funktioner i Azure Monitor som [logg frågor](../logs/log-query-overview.md), [logg aviseringar](../alerts/alerts-log.md)och [arbets böcker](../visualize/workbooks-overview.md). [Diagnostics-tillägget](../agents/diagnostics-extension-overview.md) samlar in prestanda data från gäst operativ systemet på virtuella Windows-datorer för att Azure Storage och kan skicka prestanda data till [Azure Monitor mått](../essentials/data-platform-metrics.md). För virtuella Linux-datorer krävs det att [teleympkvistar-agenten](../essentials/collect-custom-metrics-linux-telegraf.md) skickar data till Azure-mått.  På så sätt kan andra funktioner i Azure Monitor, till exempel [mått Utforskaren](../essentials/metrics-getting-started.md) och [mått varningar](../alerts/alerts-metric.md). Du kan också konfigurera diagnostik-tillägget för att skicka händelser och prestanda data utanför Azure Monitor med Azure Event Hubs.

Installera diagnostik-tillägget för en virtuell Windows-dator i Azure Portal från alternativet **diagnostikinställningar** på menyn VM. Välj alternativet för att aktivera **Azure Monitor** på fliken **mottagare** . Information om hur du aktiverar tillägget från en mall eller en kommando rad för flera virtuella datorer finns i [installation och konfiguration](../agents/diagnostics-extension-overview.md#installation-and-configuration). Till skillnad från Log Analytics-agenten definieras de data som ska samlas in i konfigurationen för tillägget på varje virtuell dator.

![Diagnostisk inställning](media/monitor-vm-azure/diagnostic-setting.png)

Se [Installera och konfigurera teleympkvistar](../essentials/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) för information om hur du konfigurerar teleympkvistar-agenterna på virtuella Linux-datorer. Meny alternativet **diagnostiska inställningar** är tillgängligt för Linux, men du kan bara skicka data till Azure Storage.

### <a name="collect-platform-metrics-and-activity-log"></a>Samla in plattforms mått och aktivitets logg
Du kan visa de plattforms mått och aktivitets logg som samlats in för varje virtuell dator värd i Azure Portal. Samla in dessa data till samma Log Analytics arbets yta som VM Insights för att analysera den med andra övervaknings data som samlas in för den virtuella datorn. Den här samlingen har kon figurer ATS med en [diagnostisk inställning](../essentials/diagnostic-settings.md). Samla in aktivitets loggen med en [diagnostisk inställning för prenumerationen](../essentials/diagnostic-settings.md#create-in-azure-portal).

Samla in plattforms mått med en diagnostisk inställning för den virtuella datorn. Till skillnad från andra Azure-resurser kan du inte skapa en diagnostisk inställning för en virtuell dator i Azure Portal men måste använda [en annan metod](../essentials/diagnostic-settings.md#create-using-powershell). I följande exempel visas hur du samlar in mått för en virtuell dator med hjälp av både PowerShell och CLI.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```azurecli
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace
```

## <a name="monitoring-in-the-azure-portal"></a>Övervakning i Azure Portal 
När du har konfigurerat insamling av övervaknings data för en virtuell dator har du flera alternativ för att komma åt den i Azure Portal:

- Använd **Azure Monitor** -menyn för att få åtkomst till data från alla övervakade resurser. 
- Använd VM Insights för övervaknings uppsättningar av virtuella datorer i stor skala.
- Analysera data för en enskild virtuell dator från menyn i Azure Portal. I tabellen nedan visas olika alternativ för att övervaka menyn för virtuella datorer.

![Övervakning i Azure Portal](media/monitor-vm-azure/monitor-menu.png)

| Meny alternativ | Beskrivning |
|:---|:---|
| Översikt | Visar [plattforms mått](../essentials/data-platform-metrics.md) för den virtuella dator värden. Klicka på en graf för att arbeta med dessa data i [mått Utforskaren](../essentials/metrics-getting-started.md). |
| Aktivitetslogg | [Aktivitets logg](../essentials/activity-log.md#view-the-activity-log) poster som filtrerats för den aktuella virtuella datorn. |
| Insikter | Öppnar [VM Insights](../vm/vminsights-overview.md) med kartan för den aktuella virtuella datorn vald. |
| Aviseringar | Visar [aviseringar](../alerts/alerts-overview.md) för den aktuella virtuella datorn.  |
| Mått | Öppna [Metrics Explorer](../essentials/metrics-getting-started.md) med scopet inställt på den aktuella virtuella datorn. |
| Diagnostikinställningar | Aktivera och konfigurera [diagnostik-tillägg](../agents/diagnostics-extension-overview.md) för den aktuella virtuella datorn. |
| Advisor-rekommendationer | Rekommendationer för den aktuella virtuella datorn från [Azure Advisor](../../advisor/index.yml). |
| Loggar | Öppna [Log Analytics](../logs/log-analytics-overview.md) med [scopet](../logs/scope.md) inställt på den aktuella virtuella datorn. |
| Anslutnings övervakare | Öppna [Network Watcher anslutnings övervakare](../../network-watcher/connection-monitor-overview.md) för att övervaka anslutningar mellan den aktuella virtuella datorn och andra virtuella datorer. |


## <a name="analyzing-metric-data"></a>Analysera mått data
Du kan analysera mått för virtuella datorer med hjälp av Metric Explorer genom att öppna **mått** från den virtuella datorns meny. Mer information om hur du använder det här verktyget finns i [komma igång med Azure Metrics Explorer](../essentials/metrics-getting-started.md) . 

Det finns tre namn områden som används av virtuella datorer för mått:

| Namnområde | Beskrivning | Krav |
|:---|:---|:---|
| Virtuell värddator | Värd mått samlas in automatiskt för alla virtuella Azure-datorer. Detaljerad lista över mått på [Microsoft. Compute/virtualMachines](../essentials/metrics-supported.md#microsoftcomputevirtualmachines). | Samlas in automatiskt utan konfiguration krävs. |
| Gäst (klassisk) | Begränsad uppsättning prestanda data för gäst operativ system och program. Tillgängligt i Metrics Explorer men inte andra Azure Monitor funktioner som mått varningar.  | [Diagnostiskt tillägg](../agents/diagnostics-extension-overview.md) installerat. Data läses från Azure Storage.  |
| Gäst för virtuell dator | Prestanda data för gäst operativ system och program är tillgängliga för alla Azure Monitor funktioner med hjälp av mått. | För Windows är [diagnostiskt tillägg installerat](../agents/diagnostics-extension-overview.md) med Azure Monitor mottagare aktiverat. För Linux har du [installerat en teleympkvistar-agent](../essentials/collect-custom-metrics-linux-telegraf.md). |

![Mått Utforskaren i Azure Portal](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Analysera loggdata
Azure virtuella datorer samlar in följande data för att Azure Monitor loggar. 

Med VM Insights kan du samla in en fördefinierad uppsättning prestanda räknare som skrivs till tabellen *InsightsMetrics* . Detta är samma tabell som används av [behållar insikter](../containers/container-insights-overview.md). 

| Datakälla | Krav | Tables |
|:---|:---|:---|
| VM-insikter | Aktivera på varje virtuell dator. | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>Mer information finns i [så här frågar du efter loggar från VM-insikter](../vm/vminsights-log-search.md) . |
| Aktivitetslogg | Diagnostisk inställning för prenumerationen. | AzureActivity |
| Värd mått | Diagnostisk inställning för den virtuella datorn. | AzureMetrics |
| Data källor från gäst operativ systemet | Aktivera Log Analytics agent och konfigurera data källor. | Se dokumentationen för varje data källa. |


> [!NOTE]
> Prestanda data som samlas in av Log Analytics agent skriver till *perf* -tabellen medan VM Insights samlar in den till *InsightsMetrics* -tabellen. Detta är samma data, men tabellerna har en annan struktur. Om du har befintliga frågor baserade på *perf* måste du skrivas om för att använda *InsightsMetrics*.


## <a name="alerts"></a>Aviseringar
[Aviseringar](../alerts/alerts-overview.md) i Azure Monitor proaktivt meddela dig när viktiga villkor finns i dina övervaknings data och eventuellt starta en åtgärd som att starta en Logic app eller anropa en webhook. Varnings regler definierar den logik som används för att fastställa när en avisering ska skapas. Azure Monitor samlar in data som används av varnings regler, men du måste skapa regler för att definiera aviserings villkor i din Azure-prenumeration.

I följande avsnitt beskrivs de olika typerna av varnings regler och rekommendationer när du ska använda dem. Den här rekommendationen baseras på funktioner och kostnader för typen av varnings regel. Information om priser för aviseringar finns [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).


### <a name="activity-log-alert-rules"></a>Aviseringsregler för aktivitetsloggar
[Aktivitets logg aviserings regler aktive](../alerts/alerts-activity-log.md) ras när en post som matchar specifika kriterier skapas i aktivitets loggen. De har inga kostnader så de bör vara ditt första val om den logik du behöver finns i aktivitets loggen. 

Mål resursen för aktivitets logg aviseringar kan vara en speciell virtuell dator, alla virtuella datorer i en resurs grupp eller alla virtuella datorer i en prenumeration.

Du kan till exempel skapa en avisering om en kritisk virtuell dator stoppas genom att välja den *virtuella datorns ström* för signal namnet.

![Aktivitets logg avisering](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Måttaviseringsregler
[Mät regler för mått](../alerts/alerts-metric.md) som utlöses när ett mått värde överskrider ett tröskelvärde. Du kan definiera ett angivet tröskelvärde eller tillåta att Azure Monitor dynamiskt fastställer ett tröskelvärde baserat på historiska data.  Använd mått aviseringar när det är möjligt med metriska data eftersom de kostar mindre och svarar snabbare än logg aviserings regler. De är också tillstånds känsliga, vilket innebär att de kommer att lösa sig själva när måttet sjunker under tröskelvärdet.

Mål resursen för aktivitets logg aviseringar kan vara en speciell virtuell dator eller alla virtuella datorer i en resurs grupp.

Om du till exempel vill skapa en avisering när en virtuell dators processor överskrider ett visst värde, skapar du en regel för varning med *procent CPU* som signal typ. Ange antingen ett angivet tröskelvärde eller Tillåt Azure Monitor att ange ett dynamiskt tröskelvärde. 

![Mått-varning](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Loggaviseringar
[Logga varnings regler](../alerts/alerts-log.md) som utlöses när resultatet av en schemalagd logg fråga matchar vissa villkor. Logga aviserings aviseringar är de mest dyraste och minst uppfyllda aviserings reglerna, men de har till gång till de mest skiftande data och kan utföra komplex logik som inte kan utföras av de andra varnings reglerna. 

Mål resursen för en logg fråga är en Log Analytics-arbetsyta. Filtrera efter vissa datorer i frågan.

Om du till exempel vill skapa en avisering som kontrollerar om några virtuella datorer i en viss resurs grupp är offline, använder du följande fråga som returnerar en post för varje dator som missade ett pulsslag under de senaste tio minuterna. Använd ett tröskelvärde på 1 som utlöses om minst en dator har ett missat pulsslag.

```kusto
Heartbeat
| where TimeGenerated > ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Logg avisering för missat pulsslag](media/monitor-vm-azure/log-alert-01.png)

Om du vill skapa en avisering om ett alltför stort antal misslyckade inloggningar har inträffat på alla virtuella Windows-datorer i prenumerationen använder du följande fråga som returnerar en post för varje misslyckad inloggnings händelse under den senaste timmen. Använd en tröskel som angetts för antalet misslyckade inloggningar som du tillåter. 

```kusto
Event
| where TimeGenerated > ago(1hr)
| where EventID == 4625
```

![Logg avisering för misslyckade inloggningar](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager tillhandahåller detaljerad övervakning av arbets belastningar på virtuella datorer. I [övervaknings guiden för molnet](/azure/cloud-adoption-framework/manage/monitor/) finns en jämförelse mellan övervaknings plattformarna och olika strategier för implementering.

Om du har en befintlig Operations Managers miljö som du vill fortsätta att använda kan du integrera den med Azure Monitor för att tillhandahålla ytterligare funktioner. Log Analytics-agenten som används av Azure Monitor är samma som används för Operations Manager så att du har övervakade virtuella datorer skicka data till båda. Du måste fortfarande lägga till agenten i VM Insights och konfigurera arbets ytan för att samla in ytterligare data som anges ovan, men de virtuella datorerna kan fortsätta att köra sina befintliga hanterings paket i en Operations Manager-miljö utan modifiering.

Funktioner i Azure Monitor som förstärker en befintlig Operations Manager funktioner är följande:

- Använd Log Analytics för att interaktivt analysera logg-och prestanda data.
- Använd logg aviseringar för att definiera aviserings villkor över flera virtuella datorer och med långsiktiga trender som inte är möjliga med aviseringar i Operations Manager.   

Information om hur du ansluter din befintliga Operations Manager hanterings grupp till din Log Analytics arbets yta finns i [anslut Operations Manager till Azure Monitor](../agents/om-agents.md) .


## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du analyserar data i Azure Monitor loggar med hjälp av logg frågor.](../logs/get-started-queries.md)
* [Lär dig mer om aviseringar med hjälp av mått och loggar i Azure Monitor.](../alerts/alerts-overview.md)