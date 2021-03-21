---
title: Azure Monitor vanliga frågor och svar | Microsoft Docs
description: Svar på vanliga frågor om Azure Monitor.
services: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2020
ms.openlocfilehash: 29cc0a3201b7c4ce1c685029de2a40f115b23e82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606964"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Vanliga frågor och svar om Azure Monitor

Microsoft FAQ (vanliga frågor och svar) är en lista över vanliga frågor om Azure Monitor. Om du har ytterligare frågor kan du gå till [diskussions forumet](/answers/questions/topics/single/24223.html) och publicera dina frågor. När en fråga ofta är tillfrågad, lägger vi till den i den här artikeln så att den snabbt och enkelt kan hittas.


## <a name="general"></a>Allmänt

### <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?
[Azure Monitor](overview.md) är en tjänst i Azure som tillhandahåller prestanda-och tillgänglighets övervakning för program och tjänster i Azure, andra moln miljöer eller lokalt. Azure Monitor samlar in data från flera källor i en gemensam data plattform där de kan analyseras för trender och avvikelser. Omfattande funktioner i Azure Monitor hjälper dig att snabbt identifiera och reagera på kritiska situationer som kan påverka ditt program.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Vad är skillnaden mellan Azure Monitor, Log Analytics och Application Insights?
I september 2018 har Microsoft kombinerat Azure Monitor, Log Analytics och Application Insights till en enda tjänst för att tillhandahålla kraftfull övervakning av programmen och de komponenter de förlitar sig på. Funktioner i Log Analytics och Application Insights har inte ändrats, men vissa funktioner har ändrats till Azure Monitor för att bättre återspegla sitt nya omfång. Logg data motorn och frågespråket för Log Analytics kallas nu Azure Monitor loggar. Se [Azure Monitor terminologi-uppdateringar](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Vad kostar Azure Monitor?
Funktioner i Azure Monitor som aktive ras automatiskt, till exempel insamling av mått och aktivitets loggar får utan kostnad. Det finns en kostnad som är kopplad till andra funktioner som logg frågor och aviseringar. Se [sidan Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/) för detaljerad pris information.

### <a name="how-do-i-enable-azure-monitor"></a>Hur gör jag för att aktivera Azure Monitor?
Azure Monitor aktive ras så snart du skapar en ny Azure-prenumeration, och [aktivitets logg](./essentials/platform-logs-overview.md) -och plattforms [mått](essentials/data-platform-metrics.md) samlas in automatiskt. Skapa [diagnostikinställningar](essentials/diagnostic-settings.md) för att samla in mer detaljerad information om driften av dina Azure-resurser och lägga till [övervaknings lösningar](insights/solutions.md) och [insikter](./monitor-reference.md) för att tillhandahålla ytterligare analys av insamlade data för specifika tjänster. 

### <a name="how-do-i-access-azure-monitor"></a>Hur gör jag för att åtkomst Azure Monitor?
Få åtkomst till alla Azure Monitor funktioner och data från **Monitor** -menyn i Azure Portal. I avsnittet **övervakning** på menyn för olika Azure-tjänster får du till gång till samma verktyg med data som har filtrerats till en viss resurs. Azure Monitor data är också tillgängliga för en mängd olika scenarier med CLI, PowerShell och en REST API.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Finns det en lokal version av Azure Monitor?
Nej. Azure Monitor är en skalbar moln tjänst som bearbetar och lagrar stora mängder data, även om Azure Monitor kan övervaka resurser som är lokala och i andra moln.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Kan Azure Monitor övervaka lokala resurser?
Ja, förutom att samla in övervaknings data från Azure-resurser kan Azure Monitor samla in data från virtuella datorer och program i andra moln och lokalt. Se [källor för övervaknings data för Azure Monitor](agents/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Är Azure Monitor integrera med System Center Operations Manager?
Du kan ansluta din befintliga System Center Operations Manager hanterings grupp till Azure Monitor för att samla in data från agenter till Azure Monitor loggar. På så sätt kan du använda logg frågor och lösning för att analysera data som samlas in från agenter. Du kan också konfigurera befintliga System Center Operations Managers agenter att skicka data direkt till Azure Monitor. Se [ansluta Operations Manager till Azure Monitor](agents/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Vilka IP-adresser använder Azure Monitor?
Se [IP-adresser som används av Application Insights och Log Analytics](app/ip-addresses.md) för en lista över IP-adresser och portar som krävs för agenter och andra externa resurser för att få åtkomst till Azure Monitor. 

## <a name="monitoring-data"></a>Övervaka data

### <a name="where-does-azure-monitor-get-its-data"></a>Var kan Azure Monitor hämta sina data?
Azure Monitor samlar in data från en mängd olika källor, inklusive loggar och mått från Azure-plattform och resurser, anpassade program och agenter som körs på virtuella datorer. Andra tjänster som Azure Security Center och Network Watcher samla in data i en Log Analytics arbets yta så att den kan analyseras med Azure Monitor data. Du kan också skicka anpassade data till Azure Monitor med hjälp av REST API för loggar eller mått. Se [källor för övervaknings data för Azure Monitor](agents/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Vilka data samlas in av Azure Monitor? 
Azure Monitor samlar in data från en mängd olika källor i [loggar](logs/data-platform-logs.md) eller [mått](essentials/data-platform-metrics.md). Varje typ av data har sina egna relativa fördelar och var och en har stöd för en viss uppsättning funktioner i Azure Monitor. Det finns en enda mått databas för varje Azure-prenumeration, medan du kan skapa flera Log Analytics arbets ytor för att samla in loggar beroende på dina behov. Se [Azure Monitor data plattform](data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Finns det en maximal mängd data som jag kan samla in i Azure Monitor?
Det finns ingen gräns för mängden Mät data som du kan samla in, men dessa data lagras i högst 93 dagar. Se [kvarhållning av mått](essentials/data-platform-metrics.md#retention-of-metrics). Det finns ingen gräns för mängden logg data som du kan samla in, men det kan påverkas av den pris nivå som du väljer för arbets ytan Log Analytics. Se [pris information](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Hur gör jag för att åtkomst till data som samlats in av Azure Monitor?
Insikter och lösningar ger en anpassad upplevelse för att arbeta med data som lagras i Azure Monitor. Du kan arbeta direkt med loggdata med hjälp av en logg fråga som skrivits i KQL (Kusto Query Language). I Azure Portal kan du skriva och köra frågor och analysera data interaktivt med hjälp av Log Analytics. Analysera mått i Azure Portal med Metrics Explorer. Se [analysera loggdata i Azure Monitor](logs/log-query-overview.md) och [komma igång med Azure Metrics Explorer](essentials/metrics-getting-started.md).

## <a name="solutions-and-insights"></a>Lösningar och insikter

### <a name="what-is-an-insight-in-azure-monitor"></a>Vad är insikter i Azure Monitor?
Insikter ger en anpassad övervaknings upplevelse för specifika Azure-tjänster. De använder samma mått och loggar som andra funktioner i Azure Monitor men kan samla in ytterligare data och ge en unik upplevelse i Azure Portal. Se [insikter i Azure Monitor](./monitor-reference.md).

Information om hur du visar insikter i Azure Portal finns i avsnittet om **insikter** på **Monitor** -menyn eller i avsnittet **övervakning** på tjänstens meny.

### <a name="what-is-a-solution-in-azure-monitor"></a>Vad är en lösning i Azure Monitor?
Övervaknings lösningar är paketerade uppsättningar av logik för att övervaka ett visst program eller en viss tjänst baserat på Azure Monitor funktioner. De samlar in loggdata i Azure Monitor och tillhandahåller logg frågor och vyer för analys av dem med hjälp av en vanlig upplevelse i Azure Portal. Se [övervaknings lösningar i Azure Monitor](insights/solutions.md).

Om du vill visa lösningar i Azure Portal klickar du på **mer** i avsnittet om **insikter** på **Monitor** -menyn. Klicka på **Lägg** till för att lägga till ytterligare lösningar på arbets ytan.

## <a name="logs"></a>Loggar

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Vad är skillnaden mellan Azure Monitor loggar och Azure Datautforskaren?
Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Monitor-loggar bygger på Azure-Datautforskaren och använder samma KQL (Kusto Query Language) med vissa mindre skillnader. Se [skillnader i Azure Monitor loggs frågespråk](/azure/data-explorer/kusto/query/).

### <a name="how-do-i-retrieve-log-data"></a>Hur gör jag för att hämta logg data?
Alla data hämtas från en Log Analytics-arbetsyta med hjälp av en logg fråga som skrivits med hjälp av KQL (Kusto Query Language). Du kan skriva egna frågor eller använda lösningar och insikter som innehåller logg frågor för ett visst program eller en viss tjänst. Se [Översikt över logg frågor i Azure Monitor](logs/log-query-overview.md).
P
### <a name="can-i-delete-data-from-a-log-analytics-workspace"></a>Kan jag ta bort data från en Log Analytics arbets yta?
Data tas bort från en arbets yta enligt kvarhållningsperioden [.](logs/manage-cost-storage.md#change-the-data-retention-period) Du kan ta bort vissa data för sekretess eller efterlevnad. Mer information finns i [Exportera och ta bort privata data](logs/personal-data-mgmt.md#how-to-export-and-delete-private-data) .

### <a name="is-log-analytics-storage-immutable"></a>Är Log Analytics lagring oföränderligt?
Data i databas lagringen kan inte ändras när de har matats in men kan tas bort via [ *rensnings* -API-sökvägen för borttagning av privata data](./logs/personal-data-mgmt.md#delete). Även om det inte går att ändra data, kräver vissa certifieringar att data förblir oföränderliga och inte kan ändras eller tas bort i lagrings utrymmet. Data oföränderlighets kan uppnås med hjälp av [data export](./logs/logs-data-export.md) till ett lagrings konto som har kon figurer ATS som [oföränderligt lagrings utrymme](../storage/blobs/storage-blob-immutability-policies-manage.md).

### <a name="what-is-a-log-analytics-workspace"></a>Vad är en Log Analytics-arbetsyta?
Alla loggdata som samlas in av Azure Monitor lagras i en Log Analytics arbets yta. En arbets yta är i grunden en behållare där loggdata samlas in från olika källor. Du kan ha en enda Log Analytics arbets yta för alla dina övervaknings data eller så kan det finnas krav för flera arbets ytor. Se [utforma distributioner av Azure Monitor loggar](logs/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Kan du flytta en befintlig Log Analytics arbets yta till en annan Azure-prenumeration?
Du kan flytta en arbets yta mellan resurs grupper eller prenumerationer, men inte till en annan region. Se [flytta en Log Analytics arbets yta till en annan prenumeration eller resurs grupp](logs/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Varför kan jag inte se Query Explorer och spara knappar i Log Analytics?

Knapparna i **query Explorer**, **Spara** och **ny varnings regel** är inte tillgängliga när [fråge omfånget](logs/scope.md) är inställt på en enskild resurs. Om du vill skapa aviseringar, Spara eller läsa in en fråga måste Log Analytics begränsas till en arbets yta. Öppna Log Analytics i kontexten för arbets ytan genom att välja **loggar** på **Azure Monitor** menyn. Den senast använda arbets ytan har valts, men du kan välja en annan arbets yta. Se [omfång och tidsintervall för logg frågor i Azure Monitor Log Analytics](logs/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Varför visas följande fel meddelande: "Registrera Resource providern Microsoft. Insights för den här prenumerationen om du vill aktivera den här frågan" när du öppnar Log Analytics från en virtuell dator? 
Många resurs leverantörer registreras automatiskt, men du kan behöva registrera vissa resurs leverantörer manuellt. Omfånget för registreringen är alltid prenumerationen. Mer information finns i [Resursproviders och resurstyper](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="why-am-i-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Varför får jag inget åtkomst fel meddelande när jag öppnar Log Analytics från en virtuell dator? 
Om du vill visa VM-loggar måste du beviljas med Läs behörighet till de arbets ytor som lagrar de virtuella dator loggarna. I dessa fall måste administratören bevilja dig behörigheter i Azure.

## <a name="metrics"></a>Mått

### <a name="why-are-metrics-from-the-guest-os-of-my-azure-virtual-machine-not-showing-up-in-metrics-explorer"></a>Varför visas inte mått från gäst operativ systemet för min virtuella Azure-dator i Metrics Explorer?
[Plattforms mått](essentials/monitor-azure-resource.md#monitoring-data) samlas in automatiskt för Azure-resurser. Du måste utföra vissa konfigurationer om du vill samla in mått från gäst operativ systemet på en virtuell dator. För en virtuell Windows-dator installerar du diagnostiskt tillägg och konfigurerar Azure Monitor Sink enligt beskrivningen i [Installera och konfigurera Windows Azure Diagnostics Extension (wad)](agents/diagnostics-extension-windows-install.md). För Linux installerar du teleympkvistar-agenten enligt beskrivningen i [samla in anpassade mått för en virtuell Linux-dator med InfluxData-agenten för teleympkvistar](essentials/collect-custom-metrics-linux-telegraf.md).

## <a name="alerts"></a>Aviseringar

### <a name="what-is-an-alert-in-azure-monitor"></a>Vad är en avisering i Azure Monitor?
Aviseringar proaktivt meddela dig när viktiga villkor finns i dina övervaknings data. De gör att du kan identifiera och åtgärda problem innan användarna av systemet ser dem. Det finns flera typer av aviseringar:

- Metric-Metric-värdet överskrider ett tröskelvärde.
- Logg fråga – resultat av en logg fråga matchar definierade villkor.
- Aktivitets logg-aktivitets logg händelse matchar definierade villkor.
- Webb test – resultat av tillgänglighets test matchar definierade kriterier.


Se [Översikt över aviseringar i Microsoft Azure](alerts/alerts-overview.md).


### <a name="what-is-an-action-group"></a>Vad är en åtgärds grupp?
En åtgärds grupp är en samling meddelanden och åtgärder som kan utlösas av en avisering. Flera aviseringar kan använda en enda åtgärds grupp så att du kan använda vanliga uppsättningar meddelanden och åtgärder. Se [skapa och hantera åtgärds grupper i Azure Portal](alerts/action-groups.md).


### <a name="what-is-an-action-rule"></a>Vad är en åtgärds regel?
Med en åtgärds regel kan du ändra beteendet för en uppsättning aviseringar som matchar ett visst villkor. På så sätt kan du utföra sådana krav som att inaktivera aviserings åtgärder under en underhålls period. Du kan också använda en åtgärds grupp till en uppsättning aviseringar i stället för att använda dem direkt i varnings reglerna. Se [Åtgärds regler](alerts/alerts-action-rules.md).

## <a name="agents"></a>Agenter

### <a name="does-azure-monitor-require-an-agent"></a>Kräver Azure Monitor en agent?
En agent krävs bara för att samla in data från operativ systemet och arbets belastningarna på virtuella datorer. De virtuella datorerna kan finnas i Azure, en annan moln miljö eller lokalt. Se [Översikt över Azure Monitors agenter](agents/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Vad är skillnaden mellan Azure Monitors agenter?
Azure Diagnostic Extension är för Azure Virtual Machines och samlar in data för att Azure Monitor Mät värden, Azure Storage och Azure Event Hubs. Log Analytics agenten är för virtuella datorer i Azure, en annan moln miljö eller lokalt och samlar in data till Azure Monitor loggar. Beroende agenten kräver Log Analytics agent och insamlad process information och beroenden. Se [Översikt över Azure Monitors agenter](agents/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Använder min agent trafik min ExpressRoute-anslutning?
Trafik till Azure Monitor använder Microsoft peering ExpressRoute-kretsen. Se [ExpressRoute-dokumentationen](../expressroute/expressroute-faqs.md#supported-services) för en beskrivning av de olika typerna av ExpressRoute-trafik. 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Hur kan jag bekräfta att Log Analytics-agenten kan kommunicera med Azure Monitor?
Från kontroll panelen på agent datorn väljer du **säkerhets & inställningar**, * * Microsoft Monitoring Agent. På fliken **Azure Log Analytics (OMS)** bekräftar en grön kryss markerings ikon att agenten kan kommunicera med Azure Monitor. En gul varnings ikon innebär att agenten har problem. En vanlig orsak är att **Microsoft Monitoring Agent** -tjänsten har stoppats. Använd Service Control Manager för att starta om tjänsten.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Hur gör jag för att stoppar du Log Analyticss agenten från att kommunicera med Azure Monitor?
För agenter som är anslutna till Log Analytics direkt öppnar du kontroll panelen och väljer **säkerhets & inställningar**, **Microsoft Monitoring Agent**. Ta bort alla arbets ytor som visas under fliken **Azure Log Analytics (OMS)** . I System Center Operations Manager tar du bort datorn från listan Log Analytics hanterade datorer. Operations Manager uppdaterar agentens konfiguration så att den inte längre rapporterar till Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Hur mycket data skickas per agent?
Mängden data som skickas per agent beror på:

* De lösningar du har aktiverat
* Antalet loggar och prestanda räknare som samlas in
* Data mängden i loggarna

Mer information finns i [Hantera användning och kostnader med Azure Monitor loggar](logs/manage-cost-storage.md) .

För datorer som kan köra WireData-agenten använder du följande fråga för att se hur mycket data som skickas:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Hur mycket nätverks bandbredd används av Microsofts hanterings agent (MMA) när du skickar data till Azure Monitor?
Bandbredd är en funktion för mängden data som skickas. Data komprimeras när de skickas över nätverket.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Hur kan jag få ett meddelande när data insamlingen från den Log Analytics agenten stoppas?

Använd stegen som beskrivs i [skapa en ny logg avisering](alerts/alerts-metric.md) som ska meddelas när data insamlingen stoppas. Använd följande inställningar för aviserings regeln:

- **Definiera aviserings villkor**: Ange Log Analytics arbets ytan som resurs mål.
- **Aviserings villkor** 
   - **Signal namn**: *anpassad loggs ökning*
   - **Sök fråga**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Aviserings logik**: **baserat på** *antalet resultat*, **villkoret** är *större än*, **tröskelvärdet** *0*
   - **Utvärderad baserat på**: **period (i minuter)** *30*, **frekvens (i minuter)** *10*
- **Definiera aviseringsinformationen** 
   - **Namn**: *data insamlingen stoppades*
   - **Allvarlighets grad**: *Varning*

Ange en befintlig eller ny [Åtgärds grupp](alerts/action-groups.md) så att du får ett meddelande om att ett pulsslag saknas under 15 minuter när logg aviseringen matchar kriterierna.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Vilka är brand Väggs kraven för Azure Monitors agenter?
Se [krav för nätverks brand väggen](agents/log-analytics-agent.md#network-requirements)för information om brand Väggs krav.


## <a name="visualizations"></a>Visualiseringar

### <a name="why-cant-i-see-view-designer"></a>Varför kan jag inte se Visa designer?

View Designer är bara tillgängligt för användare som har tilldelats deltagar behörigheter eller högre i Log Analytics-arbetsytan.

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Konfigurations problem
*Jag har problem med att konfigurera mitt:*

* [.NET-app](app/asp-net-troubleshoot-no-data.md)
* [Övervaka en app som redan körs](app/monitor-performance-live-website-now.md#troubleshoot)
* [Azure Diagnostics](agents/diagnostics-extension-to-application-insights.md)
* [Java-webbapp](app/java-troubleshoot.md)

*Jag får inga data från min server:*

* [Ange brand Väggs undantag](app/ip-addresses.md)
* [Konfigurera en ASP.NET-Server](app/monitor-performance-live-website-now.md)
* [Konfigurera en Java-Server](app/java-agent.md)

*Hur många Application Insights-resurser ska jag distribuera:*

* [Hur du utformar din Application Insights-distribution: en till många Application Insights resurser?](app/separate-resources.md)

### <a name="can-i-use-application-insights-with-"></a>Kan jag använda Application Insights med...?

* [Webbappar på en IIS-server på en virtuell Azure-dator eller skalnings uppsättning för virtuella Azure-datorer](app/azure-vm-vmss-apps.md)
* [Web Apps på en IIS-server – lokalt eller i en virtuell dator](app/asp-net.md)
* [Java-webbappar](app/java-get-started.md)
* [Node.js-appar](app/nodejs.md)
* [Web Apps på Azure](app/azure-web-apps.md)
* [Cloud Services på Azure](app/cloudservices.md)
* [App-servrar som körs i Docker](./azure-monitor-app-hub.yml)
* [Webb program med en enda sida](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Windows Desktop-app](app/windows-desktop.md)
* [Andra plattformar](app/platforms.md)

### <a name="is-it-free"></a>Är det kostnads fritt?

Ja, för experimentell användning. I den grundläggande pris Planen kan ditt program skicka en viss justering av data varje månad utan kostnad. Den kostnads fria tilldelningen är tillräckligt stor för att kunna utveckla och publicera en app för ett litet antal användare. Du kan ange en Cap för att förhindra att fler än en angiven mängd data bearbetas.

Större telemetri debiteras av GB. Vi ger några tips om hur du [begränsar dina avgifter](app/pricing.md).

Företags planen debiteras en avgift för varje dag som varje nod i webb servern skickar telemetri. Det är lämpligt om du vill använda kontinuerlig export i stor skala.

[Läs pris Planen](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Vad kostar det?

* Öppna **sidan användning och uppskattade kostnader** i en Application Insights resurs. Det finns ett diagram över senaste användningen. Du kan ange en data volyms gräns om du vill.
* Öppna [Azures fakturerings blad](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) för att se dina fakturor för alla resurser.

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Vad kan Application Insights ändra i mitt projekt?
Informationen beror på projekt typen. För ett webb program:

* Lägger till de här filerna i projektet:
  * ApplicationInsights.config
  * ai.js
* Installerar dessa NuGet-paket:
  * *Application Insights API* – Core API
  * *Application Insights-API för webb program* – används för att skicka telemetri från servern
  * *Application Insights API för JavaScript-program* – används för att skicka telemetri från klienten
* Paketen innehåller följande sammansättningar:
  * Microsoft. ApplicationInsights
  * Microsoft. ApplicationInsights. Platform
* Infogar objekt i:
  * Web.config
  * packages.config
* (Endast nya projekt – om du [lägger till Application Insights i ett befintligt projekt][start]måste du göra det manuellt.) Infogar kodfragment i klient-och Server koden för att initiera dem med Application Insights resurs-ID. I en MVC-app infogas till exempel kod i vyerna för huvud sidan/Shared/ \_ layout. cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Hur gör jag för att uppgradering från äldre SDK-versioner?
Se [viktig information](app/release-notes.md) om SDK: n som passar din typ av program.

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Hur kan jag ändra vilken Azure-resurs mitt projekt skickar data till?
I Solution Explorer högerklickar du på `ApplicationInsights.config` och väljer **Uppdatera Application Insights**. Du kan skicka data till en befintlig eller ny resurs i Azure. Uppdaterings guiden ändrar Instrumentation-nyckeln i ApplicationInsights.config, som avgör var serverns SDK skickar dina data. Om du inte avmarkerar kryss rutan uppdatera alla, ändras även nyckeln där den visas på dina webb sidor.

### <a name="do-new-azure-regions-require-the-use-of-connection-strings"></a>Kräver nya Azure-regioner användningen av anslutnings strängar?

Nya Azure-regioner **kräver** att anslutnings strängar används i stället för instrument knappar. [Anslutnings strängen](./app/sdk-connection-string.md) identifierar den resurs som du vill associera dina telemetridata med. Du kan också ändra de slut punkter som resursen kommer att använda som mål för din telemetri. Du måste kopiera anslutnings strängen och lägga till den i programmets kod eller till en miljö variabel.

### <a name="can-i-use-providersmicrosoftinsights-componentsapiversions0-in-my-azure-resource-manager-deployments"></a>Kan jag använda `providers('Microsoft.Insights', 'components').apiVersions[0]` i mina Azure Resource Manager-distributioner?

Vi rekommenderar inte att du använder den här metoden för att fylla i API-versionen. Den senaste versionen kan representera för hands versioner som kan innehålla viktiga ändringar. Även om det finns nyare versioner som inte är för hands versioner är API-versionerna inte alltid bakåtkompatibla med befintliga mallar, eller i vissa fall kan API-versionen inte vara tillgänglig för alla prenumerationer.

### <a name="what-is-status-monitor"></a>Vad är Statusövervakaren?

En Skriv bords app som du kan använda i IIS-webbservern för att konfigurera Application Insights i Web Apps. Den samlar inte in telemetri: du kan stoppa det när du inte konfigurerar en app. 

[Läs mer](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Vilken telemetri samlas in av Application Insights?

Från Server Web Apps:

* HTTP-begäranden
* [Beroenden](app/asp-net-dependencies.md). Anrop till: SQL-databaser; HTTP-anrop till externa tjänster; Azure Cosmos DB, Table, Blob Storage och Queue. 
* [Undantag](app/asp-net-exceptions.md) och stack spår.
* [Prestanda räknare](app/performance-counters.md) – om du använder [statusövervakare](app/monitor-performance-live-website-now.md), [Azure-övervakning för app Services](app/azure-web-apps.md), [Azure-övervakning för virtuell dator eller skalnings uppsättning för virtuella datorer](app/azure-vm-vmss-apps.md), eller [Application Insights insamlad skrivare](app/java-collectd.md).
* [Anpassade händelser och mått](app/api-custom-events-metrics.md) som du kodar.
* [Spårnings loggar](app/asp-net-trace-logs.md) om du konfigurerar lämplig insamlare.

Från [klient webb sidor](app/javascript.md):

* [Antal sidvisningar](app/usage-overview.md)
* [AJAX-anrop](app/asp-net-dependencies.md) Begär Anden som görs från ett skript som körs.
* Läsa in data för sid visning
* Antal användare och sessioner
* [Autentiserade användar-ID: n](app/api-custom-events-metrics.md#authenticated-users)

Från andra källor, om du konfigurerar dem:

* [Azure Diagnostics](agents/diagnostics-extension-to-application-insights.md)
* [Importera till analys](logs/data-collector-api.md)
* [Log Analytics](logs/data-collector-api.md)
* [Logstash](logs/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Kan jag filtrera ut eller ändra vissa telemetri?

Ja, på den server som du kan skriva:

* Telemetri-processor för att filtrera eller lägga till egenskaper för valda telemetridata innan de skickas från din app.
* Telemetri-initierare för att lägga till egenskaper till alla objekt i telemetri.

Läs mer om [ASP.net](app/api-filtering-sampling.md) eller [Java](app/java-filter-telemetry.md).

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Hur beräknas stad, land/region och annan Geo-plats data?

Vi letar upp IP-adressen (IPv4 eller IPv6) för webb klienten med hjälp av [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Webb läsar telemetri: vi samlar in avsändarens IP-adress.
* Server telemetri: Application Insights-modulen samlar in klientens IP-adress. Den samlas inte in om `X-Forwarded-For` har angetts.
* Mer information om hur IP-adress och data för geolokalisering samlas in i Application Insights finns i den här [artikeln](./app/ip-collection.md).

Du kan konfigurera `ClientIpHeaderTelemetryInitializer` att ta med IP-adressen från en annan rubrik. I vissa system flyttas den till exempel av en proxy, belastningsutjämnare eller CDN till `X-Originating-IP` . [Läs mer](https://apmtips.com/posts/2016-07-05-client-ip-address/).

Du kan [använda Power BI](app/export-power-bi.md ) för att visa din begär ande telemetri på en karta.


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Hur länge sparas data i portalen? Är det säkert?
Ta en titt på [data kvarhållning och sekretess][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Vad händer med att program insikter telemetri när en server eller enhet förlorar anslutning till Azure?

Alla våra SDK: er, inklusive Web SDK, innehåller "Reliable transport" eller "robust transport". När servern eller enheten förlorar anslutning till Azure [lagras telemetri lokalt i fil systemet](./app/data-retention-privacy.md#does-the-sdk-create-temporary-local-storage) (Server-SDK: er) eller i HTML5-session lagring (Web SDK). SDK gör ett nytt försök att skicka Telemetrin igen tills vår inmatnings tjänst tar hänsyn till "föråldrad" (48 timmar för loggar, 30 minuter för mått). Inaktuell telemetri kommer att tas bort. I vissa fall, t. ex. när den lokala lagringen är full, görs inget nytt försök.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>Kan personliga data skickas i telemetri?

Detta är möjligt om din kod skickar sådana data. Det kan också hända om variabler i stack spår innehåller person uppgifter. Utvecklings gruppen bör genomföra riskbedömningar för att säkerställa att person uppgifter hanteras korrekt. [Läs mer om data kvarhållning och sekretess](app/data-retention-privacy.md).

**Alla** oktetter i klient webb adressen är alltid inställda på 0 när attributen för geo-platsen har sökts upp.

[Application Insights Java Script SDK](app/javascript.md) inkluderar inte några personliga data i ifyllning som standard. Vissa personliga data som används i ditt program kan dock hämtas av SDK (till exempel fullständiga namn i `window.title` eller konto-ID i XHR URL-frågeparametrar). För anpassad personlig data maskning lägger du till en [telemetri-initierare](app/api-filtering-sampling.md#javascript-web-applications).

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Min Instrumentation-nyckel är synlig i min webb Side källa.

* Det här är en vanlig metod för övervakning av lösningar.
* Det kan inte användas för att stjäla dina data.
* Det kan användas för att skeva data eller utlösa aviseringar.
* Vi har inte hört att någon kund har haft sådana problem.

Du kan:

* Använd två separata instrument knappar (separata Application Insights-resurser) för klient-och Server data. Eller
* Skriv en proxy som körs på servern och låt webb klienten skicka data via den proxyservern.

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Hur gör jag för att se skicka data i diagnostisk sökning?
Vi loggar inte in POST-data automatiskt, men du kan använda ett TrackTrace-anrop: Lägg till data i parametern Message. Det finns en längre storleks gräns än gränserna för sträng egenskaper, men det går inte att filtrera på den.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Bör jag använda en eller flera Application Insights resurser?

Använd en enda resurs för alla komponenter eller roller i ett enda företags system. Använd separata resurser för utveckling, test och versions versioner och för oberoende program.

* [Se diskussionen här](app/separate-resources.md)
* [Exempel – moln tjänst med Worker-och Web-roller](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Hur gör jag för att du ändra Instrumentation-nyckeln dynamiskt?

* [Diskussion här](app/separate-resources.md)
* [Exempel – moln tjänst med Worker-och Web-roller](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Vad är antalet användare och sessioner?

* Java Script SDK anger en användar-cookie på webb klienten, för att identifiera användare och en sessions-cookie för att gruppera aktiviteter.
* Om det inte finns något skript på klient sidan kan du [Ange cookies på servern](https://apmtips.com/posts/2016-07-09-tracking-users-in-api-apps/).
* Om en riktig användare använder din webbplats i olika webbläsare, eller använder privat/Incognito-surfning eller olika datorer, kommer de att räknas mer än en gång.
* Om du vill identifiera en inloggad användare på datorer och webbläsare lägger du till ett anrop till [setAuthenticatedUserContext ()](app/api-custom-events-metrics.md#authenticated-users).

### <a name="how-does-application-insights-generate-device-information-browser-os-language-model"></a>Hur skapar Application Insights enhets information (webbläsare, operativ system, språk, modell)?

Webbläsaren skickar användar agent strängen i HTTP-huvudet för begäran och Application Insights-inmatnings tjänsten använder [UA-parser](https://github.com/ua-parser/uap-core) för att generera fält som visas i data tabeller och upplevelser. Det innebär att Application Insights användare inte kan ändra dessa fält.

Ibland kan dessa data saknas eller vara felaktiga om användaren eller företaget inaktiverar sändning av användar agent i webb läsar inställningar. Dessutom kan [UA parser-regexna](https://github.com/ua-parser/uap-core/blob/master/regexes.yaml) inte innehålla all enhets information eller Application Insights kanske inte har antagit de senaste uppdateringarna.

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> Har jag aktiverat allt i Application Insights?
| Vad du bör se | Så här hämtar du det | Varför du vill ha den |
| --- | --- | --- |
| Tillgänglighets diagram |[Webbtester](app/monitor-web-app-availability.md) |Vet att din webbapp är igång |
| Server App-prestanda: svars tider,... |[Lägg till Application Insights i projektet](app/asp-net.md) eller [installera AI-statusövervakare på servern](app/monitor-performance-live-website-now.md) (eller Skriv din egen kod för att [spåra beroenden](app/api-custom-events-metrics.md#trackdependency)) |Identifiera prestanda problem |
| Beroende telemetri |[Installera AI-Statusövervakare på servern](app/monitor-performance-live-website-now.md) |Diagnostisera problem med databaser eller andra externa komponenter |
| Hämta stack spår från undantag |[Infoga TrackException-anrop i koden](app/asp-net-exceptions.md) (men vissa rapporteras automatiskt) |Identifiera och diagnostisera undantag |
| Sök logg spårningar |[Lägg till ett loggnings kort](app/asp-net-trace-logs.md) |Diagnostisera undantag, prestanda problem |
| Grundläggande om klient användning: sidvyer, sessioner,... |[JavaScript-initierare på webb sidor](app/javascript.md) |Användningsanalys |
| Anpassade mått för klienter |[Spåra samtal på webb sidor](app/api-custom-events-metrics.md) |Förbättra användar upplevelsen |
| Anpassade mått för Server |[Spåra anrop i Server](app/api-custom-events-metrics.md) |Business Intelligence |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Varför är antalet i Sök-och mått diagram desamma?

[Sampling](app/sampling.md) minskar antalet telemetridata (begär Anden, anpassade händelser osv.) som faktiskt skickas från din app till portalen. I sökningen visas antalet objekt som faktiskt har tagits emot. I mått diagram som visar antalet händelser visas antalet ursprungliga händelser som har inträffat. 

Varje objekt som överförs har en `itemCount` egenskap som visar hur många ursprungliga händelser som objektet representerar. Du kan köra den här frågan i Analytics för att se hur prover fungerar:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```

### <a name="how-do-i-move-an-application-insights-resource-to-a-new-region"></a>Hur gör jag för att flyttar du en Application Insights resurs till en ny region?

Det finns **för närvarande inte stöd för** att flytta befintliga Application Insights-resurser från en region till en annan. Historiska data som du har samlat in **kan inte migreras** till en ny region. Den enda delvis lösningen är att:

1. Skapa en helt ny Application Insights resurs ([klassisk](app/create-new-resource.md) eller [arbets yta baserad](./app/create-workspace-resource.md)) i den nya regionen.
2. Återskapa alla unika anpassningar som är specifika för den ursprungliga resursen i den nya resursen.
3. Ändra ditt program till att använda den nya region resursens [instrument nyckel](app/create-new-resource.md#copy-the-instrumentation-key) eller [anslutnings sträng](app/sdk-connection-string.md).  
4. Test för att bekräfta att allt fortsätter att fungera som förväntat med din nya Application Insights-resurs. 
5. Nu kan du antingen ta bort den ursprungliga resursen som leder till att **alla historiska data förloras**. Eller Behåll den ursprungliga resursen för historiska rapporterings syften under varaktigheten för dess inställningar för datakvarhållning.

Unika anpassningar som ofta behöver återskapas manuellt eller uppdateras för resursen i den nya regionen är, men är inte begränsade till:

- Återskapa anpassade instrument paneler och arbets böcker. 
- Återskapa eller uppdatera omfånget för eventuella anpassade logg-/mått varningar. 
- Återskapa tillgänglighets aviseringar.
- Återskapa eventuella anpassade inställningar för Azure-rollbaserad åtkomst kontroll (Azure RBAC) som krävs för att användarna ska kunna komma åt den nya resursen. 
- Replikera inställningar som omfattar provtagning, data kvarhållning, dagligt tak och anpassad mått aktivering. De här inställningarna styrs via fönstret **användning och uppskattade kostnader** .
- All integrering som förlitar sig på API-nycklar som [versions anteckningar](./app/annotations.md), [real tids mått skydda kontroll kanal](app/live-stream.md#secure-the-control-channel) osv. Du måste generera nya API-nycklar och uppdatera associerad integrering. 
- Kontinuerlig export i klassiska resurser måste konfigureras igen.
- Diagnostikinställningar i arbets ytans baserade resurser måste konfigureras igen.

> [!NOTE]
> Om den resurs som du skapar i en ny region ersätter en klassisk resurs rekommenderar vi att du utforskar fördelarna med att [skapa en ny arbets yta baserad resurs](app/create-workspace-resource.md) eller [att migrera din befintliga resurs till arbets ytans baserade](app/convert-classic-resource.md). 

### <a name="automation"></a>Automation

#### <a name="configuring-application-insights"></a>Konfigurera Application Insights

Du kan [skriva PowerShell-skript](app/powershell.md) med hjälp av Azure resursövervakare för att:

* Skapa och uppdatera Application Insights-resurser.
* Ange pris planen.
* Hämta Instrumentation-nyckeln.
* Lägg till en måtta varning.
* Lägg till ett tillgänglighets test.

Du kan inte konfigurera en Metric Explorer-rapport eller konfigurera löpande export.

#### <a name="querying-the-telemetry"></a>Fråga telemetri

Använd [REST API](https://dev.applicationinsights.io/) för att köra [analys](./logs/log-query-overview.md) frågor.

### <a name="how-can-i-set-an-alert-on-an-event"></a>Hur kan jag ange en avisering för en händelse?

Azure-aviseringar är bara på mått. Skapa ett anpassat mått som korsar ett värde tröskelvärde när händelsen inträffar. Ange sedan en avisering för måttet. Du får ett meddelande när måttet korsar tröskelvärdet i båda riktningarna. du får inget meddelande förrän den första korsningen, oavsett om det ursprungliga värdet är högt eller lågt. Det finns alltid en fördröjning på några minuter.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Kostar data överföring mellan en Azure-webbapp och Application Insights?

* Om din Azure-webbapp finns i ett Data Center där det finns en Application Insights samlings slut punkt finns det ingen kostnad. 
* Om det inte finns någon samlings slut punkt i värd data centret kommer din apps telemetri att debiteras [utgående Azure-avgifter](https://azure.microsoft.com/pricing/details/bandwidth/).

Detta är inte beroende av var Application Insights resursen finns. Det beror bara på distributionen av våra slut punkter.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Kan jag skicka telemetri till Application Insights-portalen?

Vi rekommenderar att du använder våra SDK [: er](app/api-custom-events-metrics.md)och använder SDK-API: et. Det finns varianter av SDK för olika [plattformar](app/platforms.md). Dessa SDK: er hanterar buffring, komprimering, begränsning, nya försök och så vidare. Men inmatnings [schema](https://github.com/microsoft/ApplicationInsights-dotnet/tree/master/BASE/Schema/PublicSchema) och [slut punkts protokoll](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) är offentliga.

### <a name="can-i-monitor-an-intranet-web-server"></a>Kan jag övervaka en intranät webb server?

Ja, men du måste tillåta trafik till våra tjänster genom brand Väggs undantag eller omdirigering av proxy.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Läs vår fullständiga lista över tjänster och IP-adresser [här](app/ip-addresses.md).

#### <a name="firewall-exception"></a>Brand Väggs undantag

Tillåt att din webb server skickar telemetri till våra slut punkter. 

#### <a name="gateway-redirect"></a>Omdirigera Gateway

Dirigera trafik från servern till en gateway på intranätet genom att skriva över slut punkter i konfigurationen. Om dessa "slut punkts egenskaper" inte finns i konfigurationen, använder dessa klasser standardvärdena som visas nedan i exemplet ApplicationInsights.config. 

Din gateway ska dirigera trafik till vår slut punkts bas adress. Ersätt standardvärdena i konfigurationen med `http://<your.gateway.address>/<relative path>` .


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Exempel ApplicationInsights.config med standard slut punkter:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> ApplicationIdProvider är tillgängligt från och med v 2.6.0.



#### <a name="proxy-passthrough"></a>Proxy-genomströmning

Du kan åstadkomma proxy genom att konfigurera antingen en proxy på dator nivå eller på program nivå.
Mer information finns i dotNet-artikeln på [DefaultProxy](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Exempel Web.config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Kan jag köra tillgänglighets webb test på en intranät Server?

Våra [webbtester](app/monitor-web-app-availability.md) körs på närvaro punkter som distribueras världen över. Det finns två lösningar:

* Brand Väggs dörr – Tillåt förfrågningar till servern från [den långa och den ändrings bara listan med webb test agenter](app/ip-addresses.md).
* Skriv din egen kod för att skicka periodiska förfrågningar till servern inifrån intranätet. Du kan köra Visual Studio-webbtester för detta ändamål. Testaren kan skicka resultatet till Application Insights med hjälp av TrackAvailability ()-API: et.

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Hur lång tid tar det för telemetri att samlas in?

De flesta Application Insights data har en fördröjning på under 5 minuter. Vissa data kan ta längre tid. oftast större loggfiler. Mer information finns i [service avtalet för Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md

### <a name="http-502-and-503-responses-are-not-always-captured-by-application-insights"></a>HTTP 502-och 503-svar fångas inte alltid av Application Insights

felen "502 Felaktig gateway" och "503-tjänst ej tillgänglig" är inte alltid hämtade av Application Insights. Om endast Java Script på klient sidan används för övervakning förväntas detta, eftersom fel svaret returneras före sidan som innehåller HTML-huvudet och det övervaknings-JavaScript-kodfragment som återges. 

Om 502-eller 503-svaret skickades från en server med övervakning på Server sidan, skulle felen samlas in av Application Insights SDK. 

Det finns dock fortfarande fall där övervakning på Server sidan har Aktiver ATS på ett programs webb server som ett 502-eller 503-fel inte kommer att fångas av Application Insights. Många moderna webb servrar tillåter inte att en klient kommunicerar direkt, utan använder i stället lösningar som omvända proxyservrar för att överföra information fram och tillbaka mellan klienten och frontend-webbservrar. 

I det här scenariot kan ett 502-eller 503-svar returneras till en klient på grund av ett problem med det omvända proxy-lagret och det skulle inte samlas in direkt av Application Insights. För att hjälpa att identifiera problem i det här lagret kan du behöva vidarebefordra loggar från den omvända proxyn till Log Analytics och skapa en anpassad regel för att söka efter 502/503 svar. Mer information om vanliga orsaker till 502 och 503-fel finns i [artikeln Azure App Service fel sökning för "502 Felaktig gateway" och "503 service är inte tillgänglig"](../app-service/troubleshoot-http-502-http-503.md).     


## <a name="opentelemetry"></a>OpenTelemetry

### <a name="what-is-opentelemetry"></a>Vad är opentelemetri

En ny standard för öppen källkod för att kunna observera. Läs mer på [https://opentelemetry.io/](https://opentelemetry.io/) .

### <a name="why-is-microsoft--azure-monitor-investing-in-opentelemetry"></a>Varför är Microsoft/Azure Monitor-investeringar i opentelemetri?

Vi tror att det bättre tjänar våra kunder av tre anledningar:
   1. Aktivera stöd för fler kund scenarier.
   2. Instrument utan att det är någon som är försäkrad från leverantören.
   3. Öka kundernas insyn och engagemang.

Den anpassas också med Microsofts strategi för att omfatta [öppen källkod](https://opensource.microsoft.com/).

### <a name="what-additional-value-does-opentelemetry-give-me"></a>Vilket extra värde ger opentelemetri mig?

Förutom orsakerna ovan är opentelemetri mer effektivt i stor skala och ger konsekvent design/konfigurationer på olika språk.

### <a name="how-can-i-test-out-opentelemetry"></a>Hur kan jag testa opentelemetri?

Registrera dig för att gå med i vår Azure Monitor Application Insights hon community för tidiga antaganden [https://aka.ms/AzMonOtel](https://aka.ms/AzMonOtel) .

### <a name="what-does-ga-mean-in-the-context-of-opentelemetry"></a>Vad betyder GA i samband med opentelemetri?

Gruppen opentelemetri definierar allmänt tillgänglig (GA) [här](https://medium.com/opentelemetry/ga-planning-f0f6d7b5302). Opentelemetri "GA" betyder dock inte funktions paritet med befintliga Application Insights SDK: er. Azure Monitor fortsätter att rekommendera våra aktuella Application Insights SDK: er för kunder som kräver funktioner som [föraggregerade mått](app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics), Live- [mått](app/live-stream.md), [anpassningsbara samplingar](app/sampling.md#adaptive-sampling), [profiler](app/profiler-overview.md)och [ögonblicks bilds fel sökning](app/snapshot-debugger.md) tills SDK: erna för opentelemetri når funktions förfallo tid.

### <a name="can-i-use-preview-builds-in-production-environments"></a>Kan jag använda för hands versioner i produktions miljöer?

Det rekommenderas inte. Mer information finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versionerna.

### <a name="whats-the-difference-between-opentelemetry-sdk-and-auto-instrumentation"></a>Vad är skillnaden mellan opentelemetri SDK och automatiska Instrumentation?

I opentelemetri-specifikationen definieras [SDK](https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/glossary.md#telemetry-sdk). I korthet är "SDK" ett språkspecifikt paket som samlar in telemetridata i de olika komponenterna i programmet och skickar data till Azure Monitor via en exportör.

Begreppet Auto-Instrumentation (kallas ibland för bytekod-insprutning, kod lös eller agent-baserad) syftar på möjligheten att instrumentera ditt program utan att ändra din kod. Titta till exempel på sidan [Opentelemetri Java Auto-Instrumentation viktigt](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/master/README.md) för mer information.

### <a name="whats-the-opentelemetry-collector"></a>Vad är opentelemetri Collector?

Insamlaren opentelemetri beskrivs i [Readme-filen för GitHub](https://github.com/open-telemetry/opentelemetry-collector#opentelemetry-collector). För närvarande använder Microsoft inte insamlaren för opentelemetri och är beroende av direkta exportörer som skickar till Azure Monitor Application Insights.

### <a name="whats-the-difference-between-opencensus-and-opentelemetry"></a>Vad är skillnaden mellan openräkning och opentelemetri?

[Openräkning](https://opencensus.io/) är markören till [opentelemetri](https://opentelemetry.io/). Microsoft hjälpte till att samla in [Opentracing](https://opentracing.io/) och openräkning för att skapa opentelemetri, en enda observerbar standard för världen. Azure Monitor s nuvarande [produktion – Rekommenderad python SDK](app/opencensus-python.md) baseras på openräkning, men till och med kommer alla Azure Monitor SDK: er att baseras på opentelemetri.


## <a name="container-insights"></a>Container Insights

### <a name="what-does-other-processes-represent-under-the-node-view"></a>Vad representerar *andra processer* under vyn Node?

**Andra processer** är avsedda att hjälpa dig att tydligt förstå rotor saken till hög resursanvändning på din nod. På så sätt kan du skilja användningen mellan processer i behållare jämfört med processer som inte bearbetas.

Vilka är de här **processerna**? 

Dessa processer är icke-containern som körs på noden.  

Hur beräknar vi detta?

**Andra processer**  =  *Total användning från CAdvisor*  -  *Användning från container process*

De **andra processerna** innehåller:

- Självhanterade eller hanterade Kubernetes processer som inte bearbetas 

- Kör tids processer för behållare  

- Kubelet  

- System processer som körs på noden 

- Andra icke-Kubernetes arbets belastningar som körs på nod-maskinvara eller VM 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Jag ser inte bild-och namn egenskaps värden ifyllda när jag frågar ContainerLog-tabellen.

För agent version ciprod12042019 och senare fylls dessa två egenskaper i som standard för varje loggnings rad för att minimera kostnaderna för loggdata som samlas in. Det finns två alternativ för att fråga tabellen som innehåller dessa egenskaper med sina värden:

#### <a name="option-1"></a>Alternativ 1 

Gå med i andra tabeller för att inkludera dessa egenskaps värden i resultaten.

Ändra dina frågor så att de innehåller bild-och ImageTag egenskaper från ```ContainerInventory``` tabellen genom att ansluta till egenskapen ContainerID. Du kan inkludera egenskapen namn (som tidigare fanns i ```ContainerLog``` tabellen) från KubepodInventory-tabellens ContaineName-fält genom att koppla till egenskapen ContainerID. Detta är det rekommenderade alternativet.

Följande exempel är ett exempel på en detaljerad fråga som förklarar hur du hämtar dessa fält värden med kopplingar.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>Alternativ 2

Återaktivera insamling för de här egenskaperna för varje behållar logg rad.

Om det första alternativet inte är bekvämt på grund av ändringar i frågan, kan du återaktivera insamling av de här fälten genom att aktivera inställningen ```log_collection_settings.enrich_container_logs``` i agentens konfigurations mappning enligt beskrivningen i [konfigurations inställningarna för data insamling](containers/container-insights-agent-config.md).

> [!NOTE]
> Det andra alternativet rekommenderas inte med stora kluster som har fler än 50 noder eftersom det genererar API-Server anrop från varje nod i klustret för att utföra denna anrikning. Det här alternativet ökar också data storleken för varje logg rad som samlas in.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Kan jag visa mått som samlats in i Grafana?

Behållar insikter stöder visning av mått som lagras i Log Analytics-arbetsytan på Grafana-instrumentpaneler. Vi har angett en mall som du kan ladda ned från Grafana för [instrument panelen](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) för att komma igång och referera till att hjälpa dig att fråga efter ytterligare data från de övervakade klustren för att visualisera anpassade Grafana-instrumentpaneler. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-container-insights"></a>Kan jag övervaka mitt AKS-kluster med behållar insikter?

Behållar insikter stöder övervakning av arbets belastningar för arbets flöden som distribueras till AKS-motorn (tidigare ACS-Engine) som finns på Azure. Mer information och en översikt över de steg som krävs för att aktivera övervakning för det här scenariot finns i [använda behållar insikter för AKS-Engine](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Varför visas inte data i min Log Analytics-arbetsyta?

Om du inte kan se några data i Log Analytics-arbetsytan vid en viss tidpunkt varje dag kan du ha nått upp till standardgränsen på 500 MB, eller den dagliga begränsningen för att reglera mängden data som samlas in dagligen. När gränsen för dagen är uppnådd stoppas datainsamlingen för att återupptas först nästa dag. Information om hur du granskar din data användning och uppdaterar till en annan pris nivå baserat på dina förväntade användnings mönster finns i [logga data användning och kostnad](logs/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Vilka är behållar tillstånden angivna i ContainerInventory-tabellen?

Tabellen ContainerInventory innehåller information om både stoppade och pågående behållare. Tabellen fylls i av ett arbets flöde i agenten som frågar Docker efter alla behållare (körs och stoppas) och vidarebefordrar dessa data till Log Analytics arbets ytan.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Hur gör jag för att lösa *saknade prenumerations registrerings* fel?

Om du får ett fel meddelande om att **prenumerations registrering saknas för Microsoft. OperationsManagement** kan du lösa det genom att registrera resurs leverantören **Microsoft. OperationsManagement** i prenumerationen där arbets ytan definieras. Dokumentationen för hur du kan göra detta finns [här](../azure-resource-manager/templates/error-register-resource-provider.md).

### <a name="is-there-support-for-kubernetes-rbac-enabled-aks-clusters"></a>Finns det stöd för Kubernetes RBAC-aktiverade AKS-kluster?

Lösningen för övervakning av behållare stöder inte Kubernetes RBAC, men stöds av container Insights. Sidan lösnings information visar kanske inte rätt information i bladet som visar data för de här klustren.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Hur gör jag för att aktivera logg insamling för behållare i namn området Kube-system via Helm?

Logg samlingen från behållare i Kube-systemets namnrymd är inaktive rad som standard. Logg insamling kan aktive ras genom att ställa in en miljö variabel på omsagent. Mer information finns på sidan [container Insights](https://aka.ms/azuremonitor-containers-helm-chart) GitHub. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Hur gör jag för att du uppdatera omsagent till den senaste versionen?

Information om hur du uppgraderar agenten finns i [agent hantering](containers/container-insights-manage-agent.md).

### <a name="why-are-log-lines-larger-than-16kb-split-into-multiple-records-in-log-analytics"></a>Varför är logg rader större än 16 KB delade i flera poster i Log Analytics?

Agenten använder [Docker JSON-filloggnings driv rutinen](https://docs.docker.com/config/containers/logging/json-file/) för att avbilda STDOUT och stderr av behållare. Denna loggnings driv rutin delar upp logg rader som är [större än 16 KB](https://github.com/moby/moby/pull/22982) till flera rader när de kopieras från STDOUT eller stderr till en fil.

### <a name="how-do-i-enable-multi-line-logging"></a>Hur gör jag för att aktivera loggning på flera rader?

För närvarande har behållar insikter inte stöd för loggning på flera rader, men det finns tillgängliga lösningar. Du kan konfigurera alla tjänster som ska skrivas i JSON-format och sedan Docker/Moby att skriva dem som en enda rad.

Du kan till exempel figursätta loggen som ett JSON-objekt, som du ser i exemplet nedan för ett exempel node.js program:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Dessa data kommer att se ut som i följande exempel i Azure Monitor för loggar när du frågar efter den:

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

En detaljerad översikt över problemet finns i följande [GitHub-länk](https://github.com/moby/moby/issues/22920).

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Hur gör jag för att lösa Azure AD-fel när jag aktiverar Live-loggar? 

Följande fel kan visas: svars-URL: en som **anges i begäran matchar inte de svars-URL: er som har kon figurer ATS \> för programmet: <program-ID**. Lösningen för att lösa problemet finns i artikeln [så här visar du behållar data i real tid med container Insights](containers/container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Varför kan jag inte uppgradera klustret efter onboarding?

Om du har aktiverat behållar insikter för ett AKS-kluster tar du bort arbets ytan Log Analytics som klustret skickade data till, vid försök att uppgradera klustret. För att undvika detta måste du inaktivera övervakning och sedan återaktivera den till en annan giltig arbets yta i din prenumeration. När du försöker utföra kluster uppgraderingen igen bör den bearbetas och slutföras.  

### <a name="which-ports-and-domains-do-i-need-to-openallow-for-the-agent"></a>Vilka portar och domäner behöver jag öppna/tillåta för agenten?

Se [nätverks brand Väggs kraven](containers/container-insights-onboard.md#network-firewall-requirements) för proxy-och brand Väggs konfigurations information som krävs för behållarens agent med Azure, Azure amerikanska myndigheter och Azure Kina 21Vianet-moln.


## <a name="vm-insights"></a>VM-insikter

### <a name="can-i-onboard-to-an-existing-workspace"></a>Kan jag publicera till en befintlig arbets yta?
Om dina virtuella datorer redan är anslutna till en Log Analytics arbets yta kan du fortsätta att använda arbets ytan vid registrering till VM-insikter, förutsatt att den finns i någon av de [regioner som stöds](vm/vminsights-configure-workspace.md#supported-regions).


### <a name="can-i-onboard-to-a-new-workspace"></a>Kan jag publicera till en ny arbets yta? 
Om dina virtuella datorer inte är anslutna till en befintlig Log Analytics arbets yta måste du skapa en ny arbets yta för att lagra dina data. Att skapa en ny standard arbets yta görs automatiskt om du konfigurerar en enskild virtuell Azure-dator för VM-insikter via Azure Portal.

Om du väljer att använda den skriptbaserade metoden beskrivs de här stegen i artikeln [Aktivera VM Insights med hjälp av Azure PowerShell-eller Resource Manager-mall](./vm/vminsights-enable-powershell.md) . 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Vad gör jag om min virtuella dator redan rapporterar till en befintlig arbets yta?
Om du redan samlar in data från dina virtuella datorer kanske du redan har konfigurerat den för att rapportera data till en befintlig Log Analytics-arbetsyta.  Så länge arbets ytan finns i en av våra regioner som stöds, kan du aktivera VM Insights till den befintliga arbets ytan.  Om den arbets yta som du redan använder inte finns i någon av våra regioner som stöds, kommer du inte att kunna publicera till VM-insikter just nu.  Vi arbetar aktivt med att stödja ytterligare regioner.


### <a name="why-did-my-vm-fail-to-onboard"></a>Varför gick det inte att publicera den virtuella datorn?
När du registrerar en virtuell Azure-dator från Azure Portal inträffar följande steg:

* En standard arbets yta Log Analytics skapas, om alternativet har valts.
* Log Analytics Agent installeras på virtuella Azure-datorer med ett VM-tillägg, om det är klart krävs det.  
* Agenten för den virtuella datorns Insights-mappning är installerad på virtuella Azure-datorer med ett tillägg, om det är klart krävs. 

Under onboard-processen kontrollerar vi status för var och en av ovanstående för att returnera en meddelande status till dig i portalen. Konfigurationen av arbets ytan och Agent installationen tar vanligt vis 5 till 10 minuter. Det tar ytterligare 5 till 10 minuter att Visa övervaknings data i portalen.  

Om du har initierat onboarding och ser meddelanden som anger att den virtuella datorn måste registreras, kan du göra upp till 30 minuter för att den virtuella datorn ska kunna slutföra processen. 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Jag ser inte några eller några data i prestanda diagrammen för min virtuella dator
Våra prestanda diagram har uppdaterats för att använda data som lagras i *InsightsMetrics* -tabellen.  Om du vill se data i dessa diagram måste du uppgradera för att använda den nya lösningen för VM-insikter.  Mer information finns i [vanliga frågor och svar om ga](vm/vminsights-ga-release-faq.md) .

Om du inte ser prestanda data i disk tabellen eller i vissa prestanda diagram, kanske prestanda räknarna inte konfigureras på arbets ytan. Kör följande [PowerShell-skript](./vm/vminsights-enable-powershell.md)för att lösa problemet.


### <a name="how-is-vm-insights-map-feature-different-from-service-map"></a>Hur skiljer sig funktionerna för VM Insights-mappar från Tjänstkarta?
Kart funktionen för VM Insights baseras på Tjänstkarta, men har följande skillnader:

* Du kan komma åt kart visningen från bladet VM och från VM-insikter under Azure Monitor.
* Anslutningarna på kartan är nu klickbara och visar en vy över anslutnings måttets data på sido panelen för den valda anslutningen.
* Det finns ett nytt API som används för att skapa Maps för att bättre stödja mer komplexa kartor.
* Övervakade virtuella datorer ingår nu i noden klient grupp och ring diagrammet visar förhållandet mellan övervakade och oövervakade virtuella datorer i gruppen.  Den kan också användas för att filtrera listan över datorer när gruppen expanderas.
* Övervakade virtuella datorer ingår nu i noderna i server port gruppen och ring diagrammet visar förhållandet mellan övervakade och oövervakade datorer i gruppen.  Den kan också användas för att filtrera listan över datorer när gruppen expanderas.
* Kart formatet har uppdaterats så att det blir mer konsekvent med app Map från Application Insights.
* Sido panelerna har uppdaterats och har inte den fullständiga uppsättningen integreringar som stöds i Tjänstkarta-Uppdateringshantering, Ändringsspårning, säkerhet och Service Desk. 
* Alternativet för att välja grupper och datorer som ska mappas har uppdaterats och stöder nu prenumerationer, resurs grupper, skalnings uppsättningar för virtuella Azure-datorer och moln tjänster.
* Du kan inte skapa nya Tjänstkarta dator grupper i funktionen för mappning av virtuell dator.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Varför visar mina prestanda diagram prickade linjer?
Detta kan inträffa av några skäl.  I de fall där det finns en lucka i data insamlingen visas linjerna som prickade.  Om du har ändrat data samplings frekvensen för aktiverade prestanda räknare (Standardinställningen är att samla in data var 60 sekund). du kan se prickade linjer i diagrammet om du väljer ett smalt tidsintervall för diagrammet och samplings frekvensen är mindre än den Bucket-storlek som används i diagrammet (till exempel är samplings frekvensen var 10: e minut och varje Bucket i diagrammet är 5 minuter).  Om du väljer ett bredare tidsintervall som ska visas bör det leda till att diagram linjerna visas som heldragna linjer i stället för punkter i det här fallet.

### <a name="are-groups-supported-with-vm-insights"></a>Stöds grupper med VM-insikter?
Ja, när du har installerat beroende agenten samlar vi in information från de virtuella datorerna för att Visa grupper baserat på prenumeration, resurs grupp, skalnings uppsättningar för virtuella datorer och moln tjänster.  Om du har använt Tjänstkarta och skapat dator grupper visas dessa även.  Dator grupper visas också i filtret grupper om du har skapat dem för den arbets yta som du visar. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Hur gör jag för att se information om vad som driver den 95 percentils linjen i de sammanställda prestanda diagrammen?
Som standard sorteras listan för att visa de virtuella datorer som har det högsta värdet för 95-percentilen för det valda måttet, förutom det tillgängliga minnes diagrammet, som visar datorerna med det lägsta värdet för den femte percentilen.  Om du klickar på diagrammet öppnas den **översta N-listvyn**  med lämpligt mått valt.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Hur hanterar kart funktionen dubbla IP-adresser över olika virtuella nätverk och undernät?
Om du duplicerar IP-intervall med virtuella datorer eller skalnings uppsättningar för virtuella Azure-datorer över undernät och virtuella nätverk, kan det leda till att den virtuella datorns Insights-karta visar felaktig information. Detta är ett känt problem och vi undersöker alternativ för att förbättra den här upplevelsen.

### <a name="does-map-feature-support-ipv6"></a>Stöder kart funktionen IPv6?
Kart funktionen stöder för närvarande endast IPv4 och vi undersöker stödet för IPv6. Vi stöder också IPv4 som är tunnlad i IPv6.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>När jag läser in en karta för en resurs grupp eller en annan stor grupp är det svårt att visa kartan
Vi har gjort förbättringar i kartan för att hantera stora och komplexa konfigurationer, men vi inser att en karta kan ha många noder, anslutningar och noder som fungerar som ett kluster.  Vi strävar efter att fortsätta att förbättra supporten för att öka skalbarheten.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Varför ser nätverks diagrammet på fliken prestanda annorlunda ut än nätverks diagrammet på översikts sidan för Azure VM?

Översikts sidan för en virtuell Azure-dator visar diagram baserat på värdens mätning av aktivitet på den virtuella gäst datorn.  För nätverks diagrammet på Azure VM-översikten visar det bara nätverks trafik som kommer att faktureras.  Detta omfattar inte trafik mellan virtuella nätverk.  Data och diagram som visas för VM-insikter baseras på data från den virtuella gäst datorn och nätverks diagrammet visar all TCP/IP-trafik som är inkommande och utgående till den virtuella datorn, inklusive mellan virtuella nätverk.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Hur mäts svars tiden för data som lagras i VMConnection och visas i anslutnings panelen och arbets böcker?

Svars tiden är en uppskattning. Eftersom vi inte instrumenterar koden för programmet, vet vi egentligen inte när en begäran börjar och när svaret kommer. I stället noterar vi data som skickas på en anslutning och sedan data som kommer tillbaka på den anslutningen. Vår agent håller koll på dessa skickar och tar emot och försöker para ihop dem: en sekvens med sändningar, följt av en sekvens med mottagna, tolkas som ett begär ande/svar-par. Tiden mellan dessa åtgärder är svars tiden. Den omfattar nätverks fördröjningen och Server bearbetnings tiden.

Den här uppskattningen fungerar bra för protokoll som är Request/Response-baserade: en enskild begäran skickas över anslutningen och ett enda svar tas emot. Detta är fallet för HTTP (S) (utan pipelinering), men inte uppfyllt för andra protokoll.

### <a name="are-there-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Finns det några begränsningar om jag är på Log Analytics kostnads fri pris sättnings plan?
Om du har konfigurerat Azure Monitor med en Log Analytics arbets yta som använder den *kostnads fria* pris nivån, kommer funktionen för VM Insights-mappning bara att ha stöd för fem anslutna datorer som är anslutna till arbets ytan. Om du har fem virtuella datorer som är anslutna till en kostnads fri arbets yta, kopplar du från en av de virtuella datorerna och senare ansluter en ny virtuell dator. den nya virtuella datorn övervakas inte och visas inte på kart sidan.  

Under det här tillståndet uppmanas du att välja alternativet **prova nu** när du öppnar den virtuella datorn och väljer **insikter** i den vänstra rutan, även efter att den redan har installerats på den virtuella datorn.  Du tillfrågas dock inte om alternativ som normalt skulle inträffa om den virtuella datorn inte har publicerats i VM Insights. 

## <a name="sql-insights-preview"></a>SQL Insights (förhands granskning)

### <a name="what-versions-of-sql-server-are-supported"></a>Vilka versioner av SQL Server stöds?
Se [versioner](insights/sql-insights-overview.md#supported-versions) som stöds för SQL-versioner som stöds.

### <a name="what-sql-resource-types-are-supported"></a>Vilka SQL-resurs typer stöds?

- Azure SQL Database. Endast enkel databas, inte databaser i en Elastisk pool.
- Azure SQL Managed Instance 
- Virtuella Azure SQL-datorer ([Windows](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms), [Linux](../azure-sql/virtual-machines/linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create)) och virtuella azure-datorer som SQL Server är installerade på.

### <a name="what-operating-systems-for-the-machine-running-sql-server-are-supported"></a>Vilka operativ system för datorn som kör SQL Server stöds?
Alla operativ system som stöder körning av SQL-versioner som stöds.

### <a name="what-operating-system-for-the-remote-monitoring-server-are-supported"></a>Vilket operativ system har stöd för fjärr styrnings servern?

Ubuntu 18,04 är för närvarande det enda operativ system som stöds.

### <a name="where-will-the-monitoring-data-be-stored-in-log-analytics"></a>Var kommer övervaknings data att lagras i Log Analytics 
Alla övervaknings data lagras i tabellen **InsightsMetrics** . Kolumnen **Origin** har värdet *Solutions.AZM.MS/telegraf/SqlInsights*. **Namn områdes** kolumnen har värden som börjar med *sqlserver_*.

### <a name="how-often-is-data-collected"></a>Hur ofta samlas data in? 
Se [data som samlas in av SQL Insights](../insights/../azure-monitor/insights/sql-insights-overview.md#data-collected-by-sql-insights) för information om hur ofta olika data samlas in.

## <a name="next-steps"></a>Nästa steg
Om din fråga inte besvaras här kan du referera till följande forum för ytterligare frågor och svar.

- [Log Analytics](/answers/topics/azure-monitor.html)
- [Application Insights](/answers/topics/azure-monitor.html)

Om du vill ha allmän feedback på Azure Monitor besöker du [feedback-forumet](https://feedback.azure.com/forums/34192--general-feedback).