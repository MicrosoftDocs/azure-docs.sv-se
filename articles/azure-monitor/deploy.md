---
title: Distribuera Azure Monitor
description: Beskriver de olika stegen som krävs för en fullständig implementering av Azure Monitor för att övervaka alla resurser i din Azure-prenumeration.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: c37693bc6c9ce1cc5fed6c06ecb7fe628c315176
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573582"
---
# <a name="deploy-azure-monitor"></a>Distribuera Azure Monitor
Att aktivera Azure Monitor att övervaka alla dina Azure-resurser är en kombination av att konfigurera Azure Monitor komponenter och konfigurera Azure-resurser för att generera övervaknings data för Azure Monitor att samla in. I den här artikeln beskrivs de olika stegen som krävs för en fullständig implementering av Azure Monitor att använda en gemensam konfiguration för att övervaka alla resurser i din Azure-prenumeration. Grundläggande beskrivningar för varje steg finns med länkar till annan dokumentation för detaljerade konfigurations krav.

> [!IMPORTANT]
> Funktionerna i Azure Monitor och deras konfiguration varierar beroende på vilka verksamhets krav som har kostnaden för de aktiverade funktionerna. Varje steg nedan identifierar om det finns potentiell kostnad och du bör utvärdera kostnaderna innan du fortsätter med det steget. Se [Azure Monitor priser](https://azure.microsoft.com/pricing/details/monitor/) för fullständig pris information.

## <a name="configuration-goals"></a>Konfigurations mål
Målet med en fullständig implementering av Azure Monitor är att samla in alla tillgängliga data från alla dina moln resurser och program och att aktivera så många funktioner som möjligt i Azure Monitor som möjligt baserat på dessa data.

Data som samlas in av Azure Monitor skickas till antingen [Azure Monitor mått](essentials/data-platform-metrics.md) eller [Azure Monitor loggar](logs/data-platform-logs.md). Varje lagrar olika typer av data och möjliggör olika typer av analyser och aviseringar. Se [jämför Azure Monitor statistik och loggar](/data-platform.md) för en jämförelse av de två och [översikten över aviseringar i Microsoft Azure](alerts/alerts-overview.md) för en beskrivning av olika aviserings typer. 

Vissa data kan skickas till både mått och loggar för att kunna utnyttja dem med hjälp av olika funktioner. I dessa fall kan du behöva konfigurera varje separat. Till exempel skickas Metric-data automatiskt av Azure-resurser till mått, som stöder mått Utforskaren och mått varningar. Du måste skapa en diagnostisk inställning för varje resurs för att skicka samma mått data till loggar, vilket gör att du kan analysera prestanda trender med andra loggdata med hjälp av Log Analytics. I avsnitten nedan kan du se var data skickas och innehåller alla steg som krävs för att skicka data till alla möjliga platser.

Du kan ha ytterligare krav, till exempel övervakning av resurser utanför Azure och sändning av data utanför Azure Monitor. Krav som dessa kan uppnås med ytterligare konfiguration av de funktioner som beskrivs i den här artikeln. Följ länkarna till dokumentationen i varje steg för ytterligare konfigurations alternativ.

## <a name="collect-data-from-azure-resources"></a>Samla in data från Azure-resurser

> [!NOTE]
> Se [övervakning av Azure-resurser med Azure Monitor](essentials/monitor-azure-resource.md) för att få en fullständig guide för övervakning av virtuella datorer med Azure Monitor.

Viss övervakning av Azure-resurser är tillgänglig automatiskt utan konfiguration krävs, medan du måste utföra konfigurations stegen för att samla in ytterligare övervaknings data. I följande tabell visas de konfigurations steg som krävs för att samla in alla tillgängliga data från dina Azure-resurser, inklusive vilka steg data som skickas till Azure Monitor mått och Azure Monitor loggar. I avsnitten nedan beskrivs varje steg i detalj.

[![Distribuera Azure Resource Monitoring ](media/deploy/deploy-azure-resources.png)](media/deploy/deploy-azure-resources.png#lightbox)

### <a name="no-configuration"></a>Ingen konfiguration
Följande funktioner i Azure Monitor aktive ras utan konfiguration som krävs när du skapar en Azure-prenumeration. Det finns ingen kostnad för den här övervakningen.

[Azure Active Directory loggar](../active-directory/reports-monitoring/overview-reports.md) – ger inloggnings aktivitet på klient nivå och gransknings historiken för ändringar som gjorts i Azure Active Directory. Se [gransknings aktivitets rapporter i Azure Active Directory Portal](../active-directory/reports-monitoring/concept-audit-logs.md) och [rapporter om inloggnings aktiviteter i Azure Active Directory portalen](../active-directory/reports-monitoring/concept-sign-ins.md) för mer information om Azure Active Directory loggar och hur du visar dem i Azure Portal.

[Aktivitets logg](essentials/platform-logs-overview.md) – ger inblick i hanterings grupp och händelser på prenumerations nivå som har inträffat i Azure. Händelser skrivs automatiskt till aktivitets loggen när du skapar en ny Azure-resurs, ändrar en resurs eller utför en betydande aktivitet. Du kan visa händelser i Azure Portal och skapa aktivitets logg aviseringar när särskilda händelser skapas. Se [Azure aktivitets logg](essentials/activity-log.md) för information om aktivitets loggen och hur du visar den i Azure Portal.

[Plattforms mått](essentials/metrics-supported.md) – samlas in automatiskt från Azure-tjänster till [Azure Monitor mått](essentials/data-platform-metrics.md). Dessa data visas ofta på sidan **Översikt** i Azure Portal för olika tjänster. Mer information om hur du analyserar plattforms mått i Azure Portal finns i [komma igång med Azure Metrics Explorer](essentials/metrics-getting-started.md) . 


### <a name="create-log-analytics-workspace"></a>Skapa Log Analytics-arbetsyta
Du behöver minst en Log Analytics arbets yta för att aktivera [Azure Monitor loggar](logs/data-platform-logs.md), vilket krävs för att samla in sådana data som loggar från Azure-resurser, samla in data från gäst operativ systemet på virtuella Azure-datorer och för de flesta Azure Monitor insikter. Andra tjänster som Azure Sentinel och Azure Security Center också använda en Log Analytics arbets yta och kan dela samma som du använder för Azure Monitor. Du kan börja med en enda arbets yta för att stödja den här övervakningen, men se  [utforma Azure Monitor loggar distribution](logs/design-logs-deployment.md) för att få vägledning om när du ska använda flera arbets ytor.

Det kostar inget att skapa en Log Analytics arbets yta, men det finns en potentiell avgift när du har konfigurerat data som ska samlas in i den. Mer information finns i [Hantera användning och kostnader med Azure Monitor loggar](logs/manage-cost-storage.md) .  

Skapa en första Log Analytics arbets yta genom att se [skapa en Log Analytics arbets yta i Azure Portal](logs/quick-create-workspace.md) . Se [Hantera åtkomst till logg data och arbets ytor i Azure Monitor](logs/manage-access.md) för att konfigurera åtkomst. 

### <a name="create-diagnostic-setting-to-collect-tenant-and-subscription-logs"></a>Skapa en diagnostisk inställning för att samla in klient-och prenumerations loggar
Medan [Azure Active Directory loggar](../active-directory/reports-monitoring/overview-reports.md) för din klient och [aktivitets loggen](essentials/platform-logs-overview.md) för din prenumeration samlas in automatiskt, kan du analysera dessa händelser med andra logg data med hjälp av logg frågor i Log Analytics för att skicka dem till en Log Analytics arbets yta. Detta gör det också möjligt att skapa logg frågas aviseringar som är det enda sättet att varna för Azure Active Directory loggar och ger mer komplex logik än aktivitets logg aviseringar.

Det kostar inget att skicka aktivitets loggen till en arbets yta, men det finns en data inmatning och en lagrings avgift för Azure Active Directory loggar. 

Se [integrera Azure AD-loggar med Azure Monitor loggar](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) och [skapa diagnostikinställningar för att skicka plattforms loggar och mått till olika destinationer](essentials/diagnostic-settings.md) för att skapa en diagnostisk inställning för klienten och prenumerationen för att skicka logg poster till Log Analytics-arbetsytan. 

### <a name="create-diagnostic-setting-to-collect-resource-logs-and-platform-metrics"></a>Skapa en diagnostisk inställning för att samla in resurs loggar och plattforms mått
Resurser i Azure genererar automatiskt [resurs loggar](essentials/platform-logs-overview.md) som innehåller information om åtgärder som utförs i resursen. Till skillnad från plattforms mått måste du konfigurera resurs loggar som ska samlas in. Skapa en diagnostisk inställning för att skicka dem till en Log Analytics arbets yta för att kombinera dem med andra data som används med Azure Monitor loggar. Samma diagnostiska inställning kan användas för att också skicka plattforms mått för de flesta resurser till samma arbets yta, vilket gör att du kan analysera mått data med hjälp av logg frågor med andra insamlade data.

Det kostar en kostnad för den här samlingen, så du kan se [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/) innan du implementerar över ett stort antal resurser. Se även [Hantera användning och kostnader med Azure Monitor loggar](logs/manage-cost-storage.md) för information om hur du optimerar kostnaden för logg insamling.

Se [skapa diagnostisk inställning för att samla in resurs loggar och mått i Azure](essentials/diagnostic-settings.md#create-in-azure-portal) för att skapa en diagnostisk inställning för en Azure-resurs. Eftersom en diagnostisk inställning måste skapas för varje Azure-resurs, se [distribuera Azure Monitor i skala](deploy-scale.md) för information om hur du använder [Azure policy](../governance/policy/overview.md) för att skapa inställningar automatiskt varje gången en Azure-resurs skapas.

### <a name="enable-insights-and-solutions"></a>Aktivera insikter och lösningar
Insikter och lösningar tillhandahåller specialiserad övervakning för en viss tjänst eller lösning. Insikter använder fler nya funktioner i Azure Monitor, till exempel arbets böcker, så du bör använda en insikt om det är tillgängligt för din tjänst. De är automatiskt tillgängliga i varje Azure-prenumeration, men kan kräva viss konfiguration för fullständig funktionalitet. De använder vanligt vis plattforms mått och resurs loggar som du tidigare har konfigurerat och kan samla in ytterligare data.

Lösningar måste läggas till i varje prenumeration och fungerar enbart med data från Azure Monitor loggar och kan samla in ytterligare loggdata.

Det kostar inget för lösningar eller insikter, men du kan debiteras för alla data som samlas in.

Se [vad som övervakas av Azure Monitor?](monitor-reference.md) för en lista över tillgängliga insikter och lösningar i Azure Monitor. Se dokumentationen för var och en för unik konfiguration eller pris information. 

## <a name="collect-data-from-virtual-machines"></a>Samla in data från virtuella datorer

> [!NOTE]
> Se [övervaka virtuella Azure-datorer med Azure Monitor](vm/monitor-vm-azure.md) för att få en fullständig guide om hur du övervakar virtuella datorer med Azure Monitor. 

Virtuella datorer genererar liknande data som andra Azure-resurser, men du behöver en agent för att samla in data från gäst operativ systemet. Se [Översikt över Azure Monitor agenter](agents/agents-overview.md) för en jämförelse av de agenter som används av Azure Monitor. 

[Azure Monitor for VMS](vm/vminsights-overview.md) använder Log Analytics agent och beroende agent för att samla in data från gäst operativ systemet för virtuella datorer, så att du kan distribuera dessa agenter som en del av implementeringen av den här insikten. Detta gör det möjligt för Log Analytics-agenten för andra tjänster som använder den, till exempel Azure Security Center.


[![Distribuera virtuell Azure-dator ](media/deploy/deploy-azure-vm.png)](media/deploy/deploy-azure-vm.png#lightbox)


### <a name="configure-workspace-for-azure-monitor-for-vms"></a>Konfigurera arbets yta för Azure Monitor for VMs
Azure Monitor for VMs kräver en Log Analytics arbets yta som vanligt vis är samma som den som skapades för att samla in data från andra Azure-resurser. Innan du aktiverar några virtuella datorer måste du lägga till den lösning som krävs för att Azure Monitor for VMs till arbets ytan.

Mer information om hur du konfigurerar din Log Analytics arbets yta för Azure Monitor for VMs finns i [konfigurera Log Analytics arbets yta för Azure Monitor for VMS](vm/vminsights-configure-workspace.md) .

### <a name="enable-azure-monitor-for-vms-on-each-virtual-machine"></a>Aktivera Azure Monitor for VMs på varje virtuell dator
När en arbets yta har kon figurer ATS kan du aktivera varje virtuell dator genom att installera den Log Analytics agenten och beroende agenten. Det finns flera metoder för att installera dessa agenter, inklusive Azure Policy som gör att du automatiskt kan konfigurera varje virtuell dator när den skapas. Prestanda data och process information som samlas in av Azure Monitor for VMs lagras i Azure Monitor loggar.

Se [aktivera Azure Monitor for VMS översikt](vm/vminsights-enable-overview.md) för alternativ för att distribuera agenter till dina virtuella datorer och aktivera dem för övervakning.

### <a name="configure-workspace-to-collect-events"></a>Konfigurera arbets ytan för att samla in händelser
Azure Monitor for VMs samlar in prestanda data och information och beroenden för processer från gäst operativ systemet på varje virtuell dator. Log Analytics agenten kan också samla in loggar från gästen, inklusive händelse loggen från Windows och syslog från Linux. Den hämtar konfigurationen för dessa loggar från Log Analytics arbets ytan som den är ansluten till. Du behöver bara konfigurera arbets ytan en gång, och varje gång en agent ansluter, hämtas eventuella konfigurations ändringar. 

Mer information om hur du konfigurerar Log Analytics-arbetsytan finns [i agent data källor i Azure Monitor](agents/agent-data-sources.md) för att samla in ytterligare data från agentens virtuella datorer.

> [!NOTE]
> Du kan också konfigurera arbets ytan så att den samlar in prestanda räknare, men detta är troligen överflödigt med prestanda data som samlas in av Azure Monitor for VMs. Prestanda data som samlas in av arbets ytan kommer att lagras i tabellen *prestanda* , medan prestanda data som samlas in av Azure Monitor for VMS lagras i tabellen *InsightsMetrics* . Konfigurera prestanda insamling i arbets ytan endast om du behöver räknare som inte redan har samlats in av Azure Monitor for VMs.

### <a name="diagnostic-extension-and-telegraf-agent"></a>Diagnostiskt tillägg och teleympkvistar-agent
Azure Monitor for VMs använder Log Analytics-agenten som skickar prestanda data till en Log Analytics arbets yta, men inte Azure Monitor mått. Genom att skicka data till mått kan den analyseras med Metrics Explorer och användas med mått varningar. Detta kräver Diagnostic-tillägget på Windows och gruppen för teleympkvistar på Linux.

Mer information om hur du installerar och konfigurerar agenter finns i [Installera och konfigurera Windows Azure Diagnostics Extension (wad)](agents/diagnostics-extension-windows-install.md) och [samla in anpassade mått för en virtuell Linux-dator med InfluxData](essentials/collect-custom-metrics-linux-telegraf.md) .


## <a name="monitor-applications"></a>Övervakning av program
Azure Monitor övervakar dina anpassade program med [Application Insights](app/app-insights-overview.md)som du måste konfigurera för varje program som du vill övervaka. Konfigurations processen varierar beroende på vilken typ av program som övervakas och vilken typ av övervakning som du vill utföra. Data som samlas in av Application Insights lagras i Azure Monitor Mät värden, Azure Monitor loggar och Azure Blob Storage, beroende på funktion. Prestanda data lagras i både Azure Monitor mått och Azure Monitor loggar utan ytterligare konfiguration krävs.

### <a name="create-an-application-resource"></a>Skapa en program resurs
Du måste skapa en resurs i Application Insights för varje program som du ska övervaka. Loggdata som samlas in av Application Insights lagras i Azure Monitor loggar för ett arbets program som baseras på arbets ytan. Loggdata för klassiska program lagras separat från din Log Analytics arbets yta enligt beskrivningen i [data strukturen](logs/data-platform-logs.md#data-structure).

 När du skapar programmet måste du välja om du vill använda klassisk eller arbets yta baserad. Skapa ett klassiskt program genom att se [skapa en Application Insights resurs](app/create-new-resource.md) . Se [arbets ytans baserade Application Insights resurser (för hands version)](app/create-workspace-resource.md) för att skapa ett arbets program som baseras på arbets ytan.

### <a name="configure-codeless-or-code-based-monitoring"></a>Konfigurera kod baserad eller kodbaserade övervakning
Om du vill aktivera övervakning av ett program måste du bestämma om du ska använda kod lös eller kod baserad övervakning. Konfigurations processen varierar beroende på det här beslutet och vilken typ av program som du ska övervaka.

**Kod lös övervakning** är enklast att implementera och kan konfigureras efter din kod utveckling. Det krävs inga uppdateringar av koden. Se följande resurser för mer information om hur du aktiverar övervakning beroende på ditt program.

- [Program som finns på Azure Web Apps](app/azure-web-apps.md)
- [Java-program](app/java-in-process-agent.md)
- [ASP.NET program som finns i IIS på en virtuell Azure-dator eller skalnings uppsättning för virtuella Azure-datorer](app/azure-vm-vmss-apps.md)
- [ASP.NET program som finns i den lokala IIS-datorn](app/monitor-performance-live-website-now.md)


**Kod baserad övervakning** är mer anpassningsbar och samlar in ytterligare telemetri, men kräver att du lägger till ett beroende till koden i Application Insights SDK NuGet-paket. Se följande resurser för mer information om hur du aktiverar övervakning beroende på ditt program.

- [ASP.NET-program](app/asp-net.md)
- [ASP.NET Core program](app/asp-net-core.md)
- [.NET-konsol program](app/console.md)
- [Java](app/java-get-started.md)
- [Node.js](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [Andra plattformar](app/platforms.md)

### <a name="configure-availability-testing"></a>Konfigurera tillgänglighets testning
Tillgänglighets test i Application Insights återkommande tester som övervakar programmets tillgänglighet och svars tider med jämna mellanrum från platser runtom i världen. Du kan skapa ett enkelt ping-test kostnads fritt eller skapa en sekvens av webb förfrågningar för att simulera användar transaktioner som har tillhör ande kostnad. 

Se [övervaka tillgängligheten för en webbplats](app/monitor-web-app-availability.md) för sammanfattning av de olika typerna av test och information om hur du skapar dem.

### <a name="configure-profiler"></a>Konfigurera profiler
Profiler i Application Insights ger prestanda spårning för .NET-program. Det hjälper dig att identifiera den "frekvent" kod Sök väg som tar den längsta tid när den hanterar en viss webbegäran. Processen för att konfigurera profiler varierar beroende på typ av program. 

Se [profil produktions program i Azure med Application Insights](app/profiler-overview.md) för information om hur du konfigurerar profiler.

### <a name="configure-snapshot-debugger"></a>Konfigurera Snapshot Debugger
Snapshot Debugger i Application Insights övervakar undantags telemetri från ditt .NET-program och samlar in ögonblicks bilder i de övergivna undantagen så att du har den information du behöver för att diagnostisera problem i produktionen. Processen för att konfigurera Snapshot Debugger varierar beroende på typ av program. 

Se [fel sökning av ögonblicks bilder av undantag i .net-appar](app/snapshot-debugger.md) för information om hur du konfigurerar Snapshot debugger.


## <a name="visualize-data"></a>Visualisera data
Insikter och lösningar innehåller egna arbets böcker och vyer för att analysera data. Förutom dessa kan du skapa egna [visualiseringar](visualizations.md) inklusive arbets böcker för Azure Monitor data och instrument paneler för att kombinera Azure Monitor data med data från andra tjänster i Azure.


### <a name="create-workbooks"></a>Skapa arbets böcker
Med [arbets böcker](visualize/workbooks-overview.md) i Azure Monitor kan du skapa omfattande visuella rapporter i Azure Portal. Du kan kombinera olika uppsättningar av data från Azure Monitor mått och Azure Monitor loggar för att skapa enhetliga interaktiva upplevelser. Du kan komma åt ett galleri med arbets böcker på fliken **arbets böcker** på Azure Monitor-menyn. 

Mer information om hur du skapar anpassade arbets böcker finns i [Azure Monitor arbets böcker](visualize/workbooks-overview.md) .

### <a name="create-dashboards"></a>Skapa instrumentpaneler
[Azure-instrumentpaneler](../azure-portal/azure-portal-dashboards.md) är den primära instrument panels tekniken för Azure och gör att du kan kombinera Azure Monitor data med data från andra tjänster för att tillhandahålla ett enda fönster i en Azure-infrastruktur. Mer information om hur du skapar en instrument panel som innehåller data från Azure Monitor loggar finns i [skapa och dela instrument paneler för Log Analytics data](visualize/tutorial-logs-dashboards.md) . 

Se [skapa anpassade KPI-instrumentpaneler med hjälp av Azure Application Insights](app/tutorial-app-dashboards.md) för information om hur du skapar en instrument panel som innehåller data från Application Insights. 

## <a name="alerts"></a>Aviseringar
Aviseringar i Azure Monitor proaktivt meddela dig om viktiga data eller mönster som identifieras i dina övervaknings data. Vissa insikter genererar aviseringar utan konfiguration. För andra scenarier måste du skapa [aviserings regler](alerts/alerts-overview.md) som innehåller de data som ska analyseras och villkoren för när en avisering ska genereras och vilka åtgärds grupper som definierar den åtgärd som ska vidtas när en avisering genereras. 


### <a name="create-action-groups"></a>Skapa åtgärdsgrupper
[Åtgärds grupper](alerts/action-groups.md) är en samling aviserings inställningar som används av varnings regler för att fastställa vilken åtgärd som ska utföras när en avisering utlöses. Exempel på åtgärder är att skicka ett e-postmeddelande eller text, anropa en webhook eller skicka data till ett ITSM-verktyg. Varje varnings regel kräver minst en åtgärds grupp, och en enda åtgärds grupp kan användas av flera varnings regler.

Se [skapa och hantera åtgärds grupper i Azure Portal](alerts/action-groups.md) för information om hur du skapar en åtgärds grupp och en beskrivning av de olika åtgärder som den kan innehålla.


### <a name="create-alert-rules"></a>Skapa aviseringsregler
Det finns flera typer av varnings regler definierade av den typ av data som de använder. Varje har olika funktioner och en annan kostnad. Den grundläggande strategin du bör följa är att använda varnings regel typen med den lägsta kostnad som innehåller den logik som du behöver.

- [Aktivitets logg regler](alerts/activity-log-alerts.md). Skapar en avisering som svar på en ny aktivitets logg händelse som matchar de angivna villkoren. Det kostar inget innan du väljer dem. Se [skapa, Visa och hantera aktivitets logg aviseringar med hjälp av Azure Monitor](alerts/alerts-activity-log.md) för information om hur du skapar en aktivitets logg avisering.
- [Mått på varnings regler](alerts/alerts-metric-overview.md). Skapar en avisering som svar på ett eller flera mått värden som överskrider ett tröskelvärde. Mått varningar är tillstånds känsliga, vilket innebär att aviseringen stängs automatiskt när värdet sjunker under tröskelvärdet, och det skickar bara meddelanden när tillstånd ändras. Det finns en kostnad för mått varningar, men detta är betydligt mindre än logg aviseringar. Se [skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](alerts/alerts-metric.md) för information om hur du skapar en mått varning.
- [Logga varnings regler](alerts/alerts-unified-log.md). Skapar en avisering när resultatet av en schema fråga matchar de angivna kriterierna. De är de dyraste av aviserings reglerna, men de tillåter de mest komplexa villkoren. Se [skapa, Visa och hantera logg aviseringar med hjälp av Azure Monitor](alerts/alerts-log.md) för information om hur du skapar en logg frågas avisering.
- Med [program aviseringar](app/monitor-web-app-availability.md) kan du utföra proaktiv testning av prestanda och tillgänglighet för ditt webb program. Du kan utföra ett enkelt ping-test utan kostnad, men det kostar mer komplexa tester. Se [övervaka tillgängligheten för en webbplats](app/monitor-web-app-availability.md) för en beskrivning av de olika testerna och information om hur du skapar dem.


## <a name="next-steps"></a>Nästa steg

- Se [distribuera Azure Monitor i skala med Azure policy](deploy-scale.md).