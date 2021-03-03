---
title: Nyheter i Azure Monitor-dokumentationen
description: Viktiga uppdateringar av Azure Monitor-dokumentationen uppdateras varje månad.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/10/2021
ms.openlocfilehash: b21e5ea8e25844e3e025915b0b9f15162c642f25
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713447"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Vad är nytt i Azure Monitor-dokumentationen?

Den här artikeln innehåller listor Azure Monitor artiklar som antingen är nya eller som har uppdaterats markant. Den kommer att uppdateras den första veckan i varje månad för att inkludera artikel uppdateringar från föregående månad.

## <a name="january-2021"></a>Januari 2021 

### <a name="general"></a>Allmänt 
- [Azure Monitor FAQ](faq.md) – tillagd post på enhets information för Application Insights.
### <a name="agents"></a>Agenter  
- [Samla in ETW (Event tracing for Windows)-händelser (ETW) för analys Azure Monitor loggar](./agents/data-sources-event-tracing-windows.md) – ny artikel.
- [Data insamlings regler i Azure Monitor (för hands version)](./agents/data-collection-rule-overview.md) – har lagt till länkar till PowerShell-och CLI-exempel.

### <a name="alerts"></a>Aviseringar  
- [Konfigurera Azure för att ansluta ITSM-verktyg med säker export](./alerts/itsm-connector-secure-webhook-connections-azure-configuration.md) – ny artikel.
- [Anslutnings status fel i ITSMC-instrumentpanelen](./alerts/itsmc-dashboard-errors.md) – ny artikel.
- [Undersök fel med hjälp av ITSMC-instrumentpanelen](./alerts/itsmc-dashboard.md) – ny artikel.
- [Felsöka Azure Metric Alerts](./alerts/alerts-troubleshoot-metric.md) – tillagda avsnitt för dynamiska tröskelvärden.
- [Felsök problem i anslutningsprogram för hantering av IT-tjänster (ITSM)](./alerts/itsmc-troubleshoot-overview.md) -ny artikel.

### <a name="application-insights"></a>Application Insights
- [Azure Application Insights telemetri korrelation](app/correlation.md) – tillagd spårnings korrelation när en modul anropar en annan i openräkningar python.
- [Application Insights för webb sidor](app/javascript.md) – ny artikel.
- [Klicka på analys-plugin-programmet för automatisk insamling för Application Insights JavaScript SDK](app/javascript-click-analytics-plugin.md) – ny artikel.
- [Övervaka dina appar utan kod ändringar – Auto-Instrumentation för Azure Monitor Application Insights](app/codeless-overview.md) -tillagd python-kolumn.
- [Reagera på plugin-programmet för Application Insights JavaScript SDK](app/javascript-react-plugin.md) – ny artikel.
- [Telemetri-processorer (för hands version) – Azure Monitor Application Insights för Java](app/java-standalone-telemetry-processors.md) -omskrivning.
- [Användnings analys med Azure Application Insights](app/usage-overview.md) – ny artikel.
- [Använda program ändrings analys i Azure Monitor för att hitta problem med webb-app](app/change-analysis.md) – tillagt fel messges.


### <a name="insights"></a>Insikter    
- [Azure Monitor för Azure datautforskaren (för hands version)](insights/data-explorer.md) – ny artikel.

### <a name="logs"></a>Loggar    
- [Azure Monitor kundhanterad nyckel](./logs/customer-managed-keys.md) – introducera användare som tilldelats hanterad identitet.
- [Azure Monitor loggar dedicerade kluster](./logs/logs-dedicated-clusters.md) -uppdaterad svars kod.
- [Fråga om Cross service – Azure Monitor och Azure datautforskaren (för hands version)](/azure/azure-monitor/platform/azure-data-explorer-monitor-cross-service-query) – ny artikel.

### <a name="metrics"></a>Mått
- [Azure Monitor Mät värden för Mät värden och Visa förklaring](./essentials/metrics-aggregation-explained.md) – ny artikel.

### <a name="platform-logs"></a>Plattforms loggar
- [Azure Monitor resurs loggar som stöds tjänster och kategorier](./essentials/resource-logs-categories.md) – ny artikel.

### <a name="visualizations"></a>Visualiseringar
- [Azure Monitor arbets böcker data källor](./visualize/workbooks-data-sources.md) – sammanfognings-och ändrings analys har lagts till.


## <a name="december-2020"></a>December 2020

### <a name="general"></a>Allmänt
- [Azure Monitor Kundhanterade nyckel](logs/customer-managed-keys.md) -tillagda fel meddelanden.
- [Partner som integreras med avsnittet Azure Monitor](partners.md) -tillägg i Event Hub-integrering.

### <a name="agents"></a>Agenter
- [Fråga Azure datautforskaren över hela resursen genom att använda Azure Monitor](logs/azure-monitor-data-explorer-proxy.md) nya artikeln.
- [Översikt över Azure Monitoring agents](agents/agents-overview.md) – stöd för Oracle 8 har lagts till.

### <a name="alerts"></a>Aviseringar
- [Felsöka Azure Metric Alerts](alerts/alerts-troubleshoot-metric.md) – fel sökning har lagts till för dynamiska tröskelvärden.
- [Anslutningsprogram för hantering av IT-tjänster (ITSM) i Log Analytics](alerts/itsmc-definition.md) – ny artikel.
- [Anslutningsprogram för hantering av IT-tjänster (ITSM) Översikt](alerts/itsmc-overview.md) – omstrukturerad felsöknings information.
- [Anslut Cherwell med anslutningsprogram för hantering av IT-tjänster (ITSM)](alerts/itsmc-connections-cherwell.md) ny artikel.
- [Anslut förhandlingen med anslutningsprogram för hantering av IT-tjänster (ITSM)](alerts/itsmc-connections-provance.md) ny artikel.
- [Anslut SCSM med anslutningsprogram för hantering av IT-tjänster (ITSM)](alerts/itsmc-connections-scsm.md) ny artikel.
- [Anslut ServiceNow med anslutningsprogram för hantering av IT-tjänster (ITSM)](alerts/itsmc-connections-servicenow.md) ny artikel.
- [Så här åtgärdar du ServiceNow Sync-problem](alerts/itsmc-resync-servicenow.md) – omstrukturerad felsöknings information.




### <a name="application-insights"></a>Application Insights
- [Azure Application insikter om JavaScript-webbappar](app/javascript.md) – installation av anslutnings sträng har lagts till.
- [Azure Application Insights standard mått](app/standard-metrics.md) – ny artikel.
- [Azure Monitor Application Insights Java](app/java-in-process-agent.md) – ytterligare information om hur du skickar anpassad telemetri från ditt program.
- [Kontinuerlig export av telemetri från Application Insights](app/export-telemetry.md) -tillagda diagnostikinställningar baserade på export.
- [Aktivera Snapshot debugger för .net-och .net Core-appar i Azure Functions](app/snapshot-debugger-function-app.md) nya artikeln.
- [IP-adresser som används av Application Insights och Log Analytics](app/ip-addresses.md) -TILLAGDa IP-adresser för Azure Government.
- [Felsök problem med Azure Application Insights](app/profiler-troubleshooting.md) -profilering – information om status sidan för diagnostiska tjänsternas webbplats tillägg har lagts till.
- [Felsök Azure Application Insights-tillgänglighets test](app/troubleshoot-availability.md) – uppdateringar av fel sökning för ping-test.
- [Fel sökning Azure Monitor Application Insights för Java](app/java-standalone-troubleshoot.md) -ny artikel.

### <a name="containers"></a>Containers
- [Rapporter i behållar insikter](insights/container-insights-reports.md) – ny artikel.

### <a name="logs"></a>Loggar
- [Azure Monitor loggar dedicerade kluster](logs/logs-dedicated-clusters.md) – nya automatiserade kommandon, metoder för att ta bort länkar och ta bort och felsöka.
- [Kors tjänst fråga mellan Azure Monitor och Azure datautforskaren (för hands version)](logs/azure-data-explorer-monitor-cross-service-query.md) – ny artikel.
- [Log Analytics arbets ytans data export i Azure Monitor (för hands version)](logs/logs-data-export.md) – tillagda arm-mallar.

### <a name="metrics"></a>Mått
- [Avancerade funktioner i Azure Metrics Explorer](essentials/metrics-charts.md) -tillagd information om resurs omfattnings väljaren.
- [Visa flera resurser i Metrics Explorer](essentials/metrics-dynamic-scope.md) – ny artikel.

### <a name="networks"></a>Nätverk
- [Azure Networking Analytics-lösning i Azure Monitor](insights/azure-networking-analytics.md) -tillagd information på nätverks insikts arbets bok.

### <a name="virtual-machines"></a>Virtual Machines
- [Aktivera Azure Monitor för en hybrid miljö](vm/vminsights-enable-hybrid.md) – ny version av beroende agent.


### <a name="visualizations"></a>Visualiseringar
- [Azure Monitor arbets bok kart visualiseringar](visualize/workbooks-map-visualizations.md) – ny artikel.
- [Azure Monitor arbets böcker ta med din egen lagrings](visualize/workbooks-bring-your-own-storage.md) nya artikel.


## <a name="november-2020"></a>November 2020

### <a name="general"></a>Allmänt
- [Azure Monitor tjänst gränser](service-limits.md) – uppdaterat för stöd för Azure Arc.

### <a name="agents"></a>Agenter
- [Översikt över Azure Monitoring agents](agents/agents-overview.md) – uppdaterat för stöd för Azure Arc.
- [Installera Azure Monitor Agent](agents/azure-monitor-agent-install.md) – ny artikel.
- [Översikt över Azure Monitor Agent](agents/azure-monitor-agent-overview.md) – uppdaterad för stöd för Azure Arc.
- [Exempel på Resource Manager-mallar för agenter](agents/resource-manager-agent.md) – uppdaterat för stöd för Azure Arc.

### <a name="alerts"></a>Aviseringar
- [Skapa och hantera åtgärds grupper i de Azure Portal](alerts/action-groups.md) -IP-adresser som har lagts till för Webhooks.

### <a name="application-insights"></a>Application Insights
- [Java-kodad program övervakning Azure Monitor Application Insights](app/java-in-process-agent.md) -tillagt konfigurations exempel.
- [Reagera plugin-program för Application Insights JavaScript SDK](app/javascript-react-plugin.md) – tillagt avsnittet om att använda reakta hookar.
- [Uppgradera från Application Insights Java 2. x SDK](app/java-standalone-upgrade-from-2x.md) – ny artikel.
- [Viktig information för Microsoft. ApplicationInsights. SnapshotCollector](app/snapshot-collector-release-notes.md) – ny artikel.

### <a name="autoscale"></a>Automatisk skalning
- [Kom igång med autoskalning i Azure](autoscale/autoscale-get-started.md) -tillagt avsnitt om att flytta autoskalning till en annan region.

### <a name="data-collection"></a>Datainsamling
- [Konfigurera data insamling för Azure Monitor Agent (för hands version)](agents/data-collection-rule-azure-monitor-agent.md) – uppdaterad för stöd för Azure Arc.
- [Data insamlings regler i Azure Monitor (för hands version)](agents/data-collection-rule-overview.md) – uppdaterade för stöd för Azure Arc.
- [Exempel på Resource Manager-mallar för data insamlings regler](agents/resource-manager-data-collection-rules.md) – ny artikel.

### <a name="insights-and-solutions"></a>Insikter och lösningar
- [Anslut Azure till ITSM-verktyg med hjälp av Secure export](alerts/it-service-management-connector-secure-webhook-connections.md) -Added-avsnittet om hur du ansluter till ServiceNow.

### <a name="logs"></a>Loggar
- [Integrera Log Analytics och Excel](logs/log-excel.md) – ny artikel.
- Avsnittet [Log Analytics data säkerhet](logs/data-security.md) – tillagt på ytterligare säkerhetsfunktioner.
- [Log Analytics integration med Power BI](logs/log-powerbi.md) nya artikeln.
- [Standard kolumner i Azure Monitor logg poster](logs/log-standard-columns.md) – tillagd _SubscriptionId kolumn.

Nya och uppdaterade artiklar från struktur om logg frågans innehåll.

- [Log Analytics-självstudie](logs/log-analytics-tutorial.md)
- [Logg frågor i Azure Monitor](logs/log-query-overview.md)
- [Översikt över Log Analytics i Azure Monitor](logs/log-analytics-overview.md)
- [Exempel på frågor för Azure Datautforskaren och Azure Monitor](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor)
- [Självstudie: använda Kusto-frågor i Azure Datautforskaren och Azure Monitor](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)



### <a name="virtual-machines"></a>Virtuella datorer

- [Aktivera översikt över VM-insikter](vm/vminsights-enable-overview.md) – tillagda regioner som stöds.

Nya artiklar om gäst hälsa för VM Insights (för hands version)

- [Gäst hälsa för VM Insights (förhands granskning)](vm/vminsights-health-overview.md)
- [Aviseringar om gäst hälsa för VM Insights (för hands version)](vm/vminsights-health-alerts.md)
- [Konfigurera övervakning av gäst hälsa för VM Insights (för hands version)](vm/vminsights-health-configure.md)
- [Konfigurera övervakning av gäst hälsa för VM Insights med hjälp av data insamlings regler (för hands version)](vm/vminsights-health-configure-dcr.md)
- [Aktivera gäst hälsa för VM Insights (förhands granskning)](vm/vminsights-health-enable.md)
- [Felsöka VM Insights-gäst hälsa (för hands version)](vm/vminsights-health-troubleshoot.md)





## <a name="october-2020"></a>Oktober 2020

### <a name="general"></a>Allmänt
- [Azure Monitor API-Pension](logs/operationalinsights-api-retirement.md) – ny artikel.

### <a name="agents"></a>Agenter
- [Vad övervakas av Azure Monitor](monitor-reference.md) -lagt till-avsnittet på agenter.

### <a name="alerts"></a>Aviseringar
- [Skapa och hantera åtgärds grupper i avsnittet Azure Portal](alerts/action-groups.md) -Added på service tag.
- [Exempel på Resource Manager-mall för mått varningar](alerts/resource-manager-alerts-metric.md) – innehålls matchnings parameter och test platser har lagts till.
- [Felsöka Azure Metric Alerts](alerts/alerts-troubleshoot-metric.md) – bästa praxis för regel konfiguration har lagts till.

### <a name="application-insights"></a>Application Insights
- [Vinkel-plugin-programmet för Application Insights JavaScript SDK](app/javascript-angular-plugin.md) – ny artikel.
- [Azure Application insikter om ASP.net Core program](app/asp-net-core.md) – ytterligare frågor och svar om ILogger-loggar.
- [Konfigurera övervakning av ASP.net med Azure Application insikter](app/asp-net.md) – artikel har skrivits om.
- [Loggbaserade och föraggregerade mått i Azure Application insikter](app/pre-aggregated-metrics-log-metrics.md) – tillagda tabeller med föraggregerade mått.
- [Övervaka tillgänglighet och svars tider för alla webbplatser som](app/monitor-web-app-availability.md) lagts till på platsens populations taggar.
- [Övervaka Java-program överallt-Azure Monitor Application Insights](app/java-standalone-config.md) -tillagt konfigurations exempel.
- [Övervaka Java-program var som helst Azure Monitor Application Insights](app/java-standalone-telemetry-processors.md) – ny artikel.
- [Använd program ändrings analys i Azure Monitor för att hitta webb program problem](app/change-analysis.md) – tillagda avsnitt på virtuella datorer och aktivitets logg.
  
### <a name="autoscale"></a>Automatisk skalning
- [Kom igång med autoskalning i Azure](autoscale/autoscale-get-started.md) -tillagt avsnitt om att flytta autoskalning till en annan region.

### <a name="containers"></a>Containers
- [Konfigurera PV-övervakning med container Insights](containers/container-insights-persistent-volumes.md) – ny artikel.
- [Hantera behållar insikter-agenten](containers/container-insights-manage-agent.md) – stöd för Azure Arc-aktiverade Kubernetes-kluster har lagts till.
- [Mått varningar från behållar insikter](containers/container-insights-metric-alerts.md) – stöd har lagts till för Azure Arc-aktiverade Kubernetes-kluster.

### <a name="insights-and-solutions"></a>Insikter och lösningar
- [Anslutningsprogram för hantering av IT-tjänster (ITSM) säker export i avsnittet Azure Monitor](alerts/it-service-management-connector-secure-webhook-connections.md) -tillägg på ServiceNow.

### <a name="logs"></a>Loggar
- [Arkivera data från Log Analytics arbets yta till Azure Storage med hjälp av Logic app](logs/logs-export-logic-app.md) – ny artikel.
- [Log Analytics arbets ytans data export i Azure Monitor (för hands version)](logs/logs-data-export.md) – tillagd exempel text för rest-begäran för Event Hub.
- [Hantera användning och kostnader för Azure Monitor loggar](logs/manage-cost-storage.md) – har lagt till information om relationen mellan Azure Monitor loggar och Azure Security Center fakturering. Frågan har lagts till för Node-antal om du använder pris nivån per nod. 
- [Övervaka hälsan för Log Analytics arbets ytan i Azure Monitor](logs/monitor-workspace.md) nya artikeln.
- [Fråga efter data i Azure monitor att använda Azure datautforskaren (för hands version)](logs/azure-data-explorer-monitor-proxy.md) – ny artikel.
- [Fråga exporterade data från Azure monitor med Azure datautforskaren (för hands version)](logs/azure-data-explorer-query-storage.md) – ny artikel.

### <a name="networks"></a>Nätverk
- [Azure Monitor för förhands granskning av nätverk](insights/network-insights-overview.md) – tillägg av fel sökning. Avsnittet har lagts till för anslutning.

### <a name="platform-logs"></a>Plattformsloggar
- [Händelse schema för Azure aktivitets logg](essentials/activity-log-schema.md) – en beskrivning av allvarlighets nivåer har lagts till.

### <a name="virtual-machines"></a>Virtuella datorer
- [Ändra analys i Azure Monitor for VMS](vm/vminsights-change-analysis.md) – ny artikel.
- [Aktivera Azure Monitor for VMS översikt](vm/vminsights-enable-overview.md) – tillagda regioner som stöds.
- [Så här uppdaterar du behållar insikter för mått](containers/container-insights-update-metrics.md) – stöd har lagts till för Azure Arc-aktiverade Kubernetes-kluster.



## <a name="september-2020"></a>September 2020

### <a name="general"></a>Allmänt
- [Azure Monitor FAQ](faq.md) – tillagt avsnitt om opentelemetri.

### <a name="agents"></a>Agenter
- [Översikt över Azure Monitor Agent](agents/azure-monitor-agent-overview.md) – besluts faktorer har lagts till för att växla till den nya agenten.
- [Översikt över Azure Monitoring agents](agents/agents-overview.md) – stöd för Windows 10 har lagts till.

### <a name="alerts"></a>Aviseringar
- [Skapa en logg avisering med Azure Resource Manager mall](alerts/alerts-log-create-templates.md) – ny artikel.
- [Felsöka Azure Metric Alerts](alerts/alerts-troubleshoot-metric.md) – avsnittet om att exportera arm-mall för en mått varnings regel har lagts till.

### <a name="application-insights"></a>Application Insights
- [Skapa en ny Azure Monitor Application Insights arbets ytans baserade resurs](app/create-workspace-resource.md) -borttagen förhands gransknings beteckning.
- [Datakvarhållning och lagring i Azure Application insikter](app/data-retention-privacy.md) – ytterligare information för nytt stöd för Mac-och Linux-dataförlustskydd.
- [Händelse räknare i Application Insights](app/eventcounters.md) -tillagda anteckningar om räknare som samlas in som standard.
- [Loggbaserade och föraggregerade mått i Azure Application Insights](app/pre-aggregated-metrics-log-metrics.md) -borttagen för hands version.
- [Migrera en Azure Monitor Application Insights klassisk resurs till en arbets yta-baserad resurs](app/convert-classic-resource.md) -ny artikel.
- [Övervaka Java-program i alla miljö-Azure Monitor Application Insights](app/java-in-process-agent.md) – uppdaterade för den nya för hands versionen av agenten.
- [Konfigurera Web App Analytics för ASP.net med Azure Application insikter](app/asp-net.md) – artikel har skrivits om.
- [Telemetri kanaler i Azure Application insikter](app/telemetry-channels.md) – ytterligare information om nytt stöd för Mac-och Linux-dataskydd.
- [Felsök Azure Application insights Snapshot debugger](app/snapshot-debugger-troubleshoot.md) – SSL-avsnittet har lagts till för att Snapshot debugger fel sökning.
- [Använd program ändrings analys i Azure Monitor för att hitta problem med webb-app](app/change-analysis.md) -tillagd virtuell dator och aktivitets logg.


### <a name="containers"></a>Containers
- [Konfigurera Azure Arc-aktiverade Kubernetes-kluster med behållar insikter](containers/container-insights-enable-arc-enabled-clusters.md) – rikt linjer för att aktivera övervakning med tjänstens huvud namn.
- [Distributions & hPa mått med container Insights](containers/container-insights-deployment-hpa-metrics.md) – ny artikel.

### <a name="insights-and-solutions"></a>Insikter och lösningar
- [Azure Monitor för Azure cache för Redis](insights/redis-cache-insights-overview.md) -borttagen för hands version.
- [Azure Monitor for Networks (för hands version)](insights/network-insights-overview.md) – tillagda anslutnings-och trafik avsnitt.
- [Anslutningsprogram för hantering av IT-tjänster (ITSM) säker export i Azure Monitor](alerts/it-service-management-connector-secure-webhook-connections.md) – ny artikel.
- [Anslutningsprogram för hantering av IT-tjänster (ITSM) i Azure Monitor](alerts/itsmc-connections.md) – Observera Cherwell-och ITSM-integration.
- [Övervaka Key Vault med Azure Monitor för Key Vault](insights/key-vault-insights-overview.md) -borttagen förhands granskning.

### <a name="logs"></a>Loggar
- [Gransknings frågor i Azure Monitor logg frågor](logs/query-audit.md) – ny artikel.
- [Azure Monitor kundhanterad nyckel](logs/customer-managed-keys.md) som har lagts till av kund.
- [Azure Monitor loggar dedikerade kluster](logs/logs-dedicated-clusters.md) – ny artikel.
- [Utforma Azure Monitor loggar distribution](logs/design-logs-deployment.md) – avsnittet skalning och inläsnings volym hastighet.
- [Logg fråge omfånget i Azure Monitor Log Analytics](logs/scope.md) – uppdateringar för att inkludera arbets ytans baserade program.
- [Loggar in Azure Monitor](logs/data-platform-logs.md) – uppdateringar för att inkludera arbets ytor baserade program.
- [Standard kolumner i Azure Monitor logg poster](logs/log-standard-columns.md) – uppdateringar som innehåller arbets ytans baserade program.
- [Azure Monitor tjänst gränser](service-limits.md) – uppdaterade gränser för begränsning av användar frågor.
- [Använda Kundhanterade lagrings konton i Azure Monitor Log Analytics](logs/private-storage.md) artikel.
- [Visa och analysera data i Azure Log Analytics](./logs/data-platform-logs.md) – uppdateringar för att inkludera arbets ytans baserade program.


### <a name="platform-logs"></a>Plattformsloggar
- [Händelse schema för Azure aktivitets logg – Azure Monitor](essentials/activity-log-schema.md) -tillagda allvarlighets nivåer.
- [Resource Manager-mall exempel för diagnostikinställningar](essentials/resource-manager-diagnostic-settings.md) – tillagt exempel för Azure Storage-konto.

### <a name="visualizations"></a>Visualiseringar
- [Azure Monitor visualiseringar i arbets boks diagram](visualize/workbooks-chart-visualizations.md) – ny artikel.
- [Azure Monitor arbets bok sammansatt stapel åter givning](visualize/workbooks-composite-bar.md) – ny artikel.
- [Diagram visualiseringar för Azure Monitor arbets böcker](visualize/workbooks-graph-visualizations.md) – ny artikel.
- [Azure Monitor arbets bok rutnät visualiseringar](visualize/workbooks-grid-visualizations.md) – ny artikel.
- [Azure Monitor arbets bok honung kamma-visualiseringar](visualize/workbooks-honey-comb.md) – ny artikel.
- [Azure Monitor visualiseringar av arbets boken](visualize/workbooks-text-visualizations.md) – ny artikel.
- [Azure Monitor arbets boks panels visualiseringar](visualize/workbooks-tile-visualizations.md) – ny artikel.
- [Azure Monitor arbets bok träds visualiseringar](visualize/workbooks-tree-visualizations.md) – ny artikel.




## <a name="august-2020"></a>Augusti 2020

### <a name="general"></a>Allmänt

- [Vad övervakas av Azure Monitor](monitor-reference.md) -uppdaterat för att inkludera Azure Monitor-agenten.


### <a name="agents"></a>Agenter
- [Översikt över Azure Monitor Agent](agents/azure-monitor-agent-overview.md) – ny artikel.
- [Aktivera Azure Monitor för en hybrid miljö](vm/vminsights-enable-hybrid.md) – uppdaterad beroende agent version.
- [Översikt över Azure Monitoring agents](agents/agents-overview.md) – lade till Azure Monitor Agent och stöd tabell för konsoliderade operativ system.


#### <a name="new-and-updated-articles-from-restructure-of-agent-content"></a>Nya och uppdaterade artiklar från omstrukturering av agent innehåll
- [Aktivera översikt över VM Insights](vm/vminsights-enable-overview.md)
- [Installera Log Analytics-agenten på Linux-datorer](agents/agent-linux.md)
- [Installera Log Analytics-agenten på Windows-datorer](agents/agent-windows.md)
- [Översikt över Log Analytics agent](agents/log-analytics-agent.md)

### <a name="application-insights"></a>Application Insights
- [Azure Application Insights för JavaScript-webbappar](app/javascript.md) – tillagt avsnitt för att klargöra klient serverns korrelation och konfiguration för CORS-korrelation.
- [Skapa en ny Azure Monitor Application Insights arbets ytans baserade resurser](app/create-workspace-resource.md) som har lagts till av arbets ytans baserade program.
- [IP-adresser som används av Application Insights och Log Analytics](app/ip-addresses.md) uppdaterade IP-adresser för Live Metrics Stream.
- [Övervaka Java-program i valfri miljö – Azure Monitor Application Insights](app/java-in-process-agent.md) -tillagd tabell för anpassad telemetri som stöds.
- [Native reakta plugin-program för Application Insights JavaScript SDK](app/javascript-react-native-plugin.md) – ny artikel.
- [Reagera på plugin-programmet för Application Insights JavaScript SDK](app/javascript-react-plugin.md) – ny artikel.
- [Exempel på Resource Manager-mall för att skapa Azure Function-appar med Application Insights övervakning](app/resource-manager-function-app.md) – ny artikel.
- [Exempel på Resource Manager-mallar för att skapa Azure App Services-webbappar med Application Insights övervakning](app/resource-manager-web-app.md) – ny artikel.
- [Användnings analys med Azure Application Insights](app/usage-overview.md) -tillagd video.

### <a name="autoscale"></a>Automatisk skalning
- [Kom igång med autoskalning i Azure](autoscale/autoscale-get-started.md) -tillägg i routning till felfria instanser för App Service.

### <a name="data-collection"></a>Datainsamling
- [Konfigurera data insamling för Azure Monitor Agent (för hands version)](agents/data-collection-rule-azure-monitor-agent.md) – ny artikel.
- [Data insamlings regler i Azure Monitor (för hands version)](agents/data-collection-rule-overview.md) – ny artikel.


### <a name="containers"></a>Containers
- [Distributions & hPa mått med container Insights](containers/container-insights-deployment-hpa-metrics.md) – ny artikel.

### <a name="insights"></a>Insikter
- [Övervaknings lösningar i Azure Monitor](insights/solutions.md) -uppdaterat för nytt användar gränssnitt.
- [Övervakare av nätverksprestanda lösning i Azure](insights/network-performance-monitor.md) – stödda arbetsyte regioner som stöds.


### <a name="logs"></a>Loggar
- [Azure Monitor FAQ](faq.md) – tillagd post för att ta bort data från en arbets yta. Lade till post på 502 och 503 svar.
  - [Utforma Azure Monitor loggar distribution](logs/design-logs-deployment.md) – uppdateringar av avsnittet volym hastighets begränsning.
- [Hantera användning och kostnader för Azure Monitor loggar](logs/manage-cost-storage.md) – uppdaterade användnings frågor för mer effektivt fråge format.
- [Optimera logg frågor i Azure Monitor](logs/query-optimization.md) – vissa värden har lagts till i prestanda indikatorer.
- [Resource Manager-mall exempel för diagnostiska inställningar](essentials/resource-manager-diagnostic-settings.md) – tillagt exempel för logg gransknings loggar.


### <a name="platform-logs"></a>Plattformsloggar
- [Skapa diagnostikinställningar för att skicka plattforms loggar och mått till olika destinationer](essentials/diagnostic-settings.md) – extra regionalt krav för diagnostikinställningar.

### <a name="visualizations"></a>Visualiseringar
- [Översikt över Azure Monitor arbets böcker](visualize/workbooks-overview.md) – lägga till video.
- [Flytta en mall för Azure-arbetsböcker till en annan region](visualize/workbook-templates-move-region.md) – ny artikel.
- [Flytta en Azure-arbetsbok till en annan region](visualize/workbooks-move-region.md) – ny artikel.



## <a name="july-2020"></a>Juli 2020

### <a name="general"></a>Allmänt
- [Distribuera Azure Monitor](deploy-scale.md) omstrukturering av innehåll för VM Insights-registrering.
- [Använd Azures privata länk för att på ett säkert sätt ansluta nätverk till Azure Monitor](logs/private-link-security.md) -tillagt avsnittet om gränser.

### <a name="alerts"></a>Aviseringar
- [Åtgärds regler för Azure Monitor aviseringar](alerts/alerts-action-rules.md) – tillagda CLI-processer.
- [Skapa och hantera åtgärds grupper i Azure Portal](alerts/action-groups.md) -uppdaterat för att avspegla ändringar i användar gränssnittet.
- [Exempel frågor i Azure Monitor Log Analytics](logs/example-queries.md) – ny artikel.
- [Felsök logg aviseringar i Azure Monitor](alerts/alerts-troubleshoot-log.md) -tillägg i varnings regelns kvot.
- [Felsöka Azure Metric Alerts](alerts/alerts-troubleshoot-metric.md) – avsnittet har lagts till i varnings regeln på ett anpassat mått som inte har genererats än.
- [Förstå hur mått varningar fungerar i Azure Monitor.](alerts/alerts-metric-overview.md) -Rekommendationen för att välja agg regerings kornig het har lagts till.

### <a name="application-insights"></a>Application Insights
- [Viktig information för Azure Web App Extension – Application Insights](app/web-app-extension-release-notes.md) -New-artikeln.
- [Exempel på Resource Manager-mallar för Application Insights resurser](app/resource-manager-app-resource.md) – ny artikel.
- [Felsök problem med Azure Application Insights profiler](app/profiler-troubleshooting.md) – en anteckning om fel som kör profiler för ASP.net Core appar på Azure App Service. 

### <a name="containers"></a>Containers
- [Logga aviseringar från container Insights](containers/container-insights-log-alerts.md) – ny artikel.
- [Mått aviseringar från container Insights](containers/container-insights-metric-alerts.md) – ny artikel.

### <a name="logs"></a>Loggar
- [Azure Monitor kundhanterad nyckel](logs/customer-managed-keys.md) som har lagts till för att lägga till fel meddelande och avsnitt CMK konfiguration för frågor.
- [Azure Monitor http-datacollector-API](logs/data-collector-api.md) – tillagt python 3-exempel.
- [Optimera logg frågor i Azure Monitor](logs/query-optimization.md) -tillägg-avsnittet om hur du undviker flera data genomsökningar när du använder under frågor.
- [Självstudie: kom igång med Log Analytics frågor](./logs/log-analytics-tutorial.md) – tillagd video.

### <a name="platform-logs"></a>Plattformsloggar
- [Skapa diagnostikinställningar för att skicka plattforms loggar och mått till olika destinationer](essentials/diagnostic-settings.md) – tillagd video.
- Exempel [på Resource Manager-mallar för Azure Monitor](/resource-manager-samples.md) -tillagt arm-exempel med loggar destinations typ. 

### <a name="solutions"></a>Lösningar
- [Övervaknings lösningar i Azure Monitor](insights/solutions.md) -tillagda CLI-processer.
- [Office 365-hanterings lösning i Azure](insights/solution-office-365.md) – ändrat indragnings datum.

### <a name="virtual-machines"></a>Virtuella datorer

Nya och uppdaterade artiklar från omstrukturering av innehåll för VM Insights

- [Vad är VM Insights?](vm/vminsights-overview.md)
- [Konfigurera Log Analytics arbets yta för VM-insikter](vm/vminsights-configure-workspace.md)
- [Anslut Linux-datorer till Azure Monitor](agents/agent-linux.md)
- [Aktivera Azure Monitor för en hybrid miljö](vm/vminsights-enable-hybrid.md)
- [Aktivera Azure Monitor för en virtuell dator eller skalnings uppsättning för virtuell dator i Azure Portal](vm/vminsights-enable-portal.md)
- [Aktivera VM-insikter med hjälp av Azure Policy](./vm/vminsights-enable-policy.md)
- [Aktivera översikt över VM Insights](vm/vminsights-enable-overview.md)
- [Aktivera VM Insights med PowerShell](vm/vminsights-enable-powershell.md)
- [Aktivera VM Insights med Resource Manager-mallar](vm/vminsights-enable-resource-manager.md)
- [Aktivera VM-insikter med PowerShell eller mallar](./vm/vminsights-enable-powershell.md)


### <a name="visualizations"></a>Visualiseringar
- [Uppgradera Log Analytics instrument panels visualiseringar](logs/dashboard-upgrade.md) – uppdaterat uppdaterings intervall.
- [Visualisera data från Azure Monitor](visualizations.md) -tillagd video.


## <a name="june-2020"></a>Juni 2020

### <a name="general"></a>Allmänt
- [Distribuera Azure Monitor](deploy-scale.md) – ny artikel.
- [Azure Monitor billingtype-egenskap med kundhanterad nyckel](logs/customer-managed-keys.md) uppdaterad. PowerShell-kommandon har lagts till.

### <a name="agents"></a>Agenter
- [Översikt över Log Analytics agent](agents/log-analytics-agent.md) – ytterligare python 2-krav har lagts till.

### <a name="alerts"></a>Aviseringar
- [Så här uppdaterar du aviserings regler eller åtgärds regler när deras mål resurs flyttas till en annan Azure-region](alerts/alerts-resource-move.md) – ny artikel.
- [Fel sökning av Azure Metric-aviseringar](alerts/alerts-troubleshoot-metric.md) – ny artikel.
- [Felsöka logg aviseringar i Azure Monitor](alerts/alerts-troubleshoot-metric.md) – ny artikel.
  
### <a name="application-insights"></a>Application Insights
- [Azure Application Insights för JavaScript-webbappar](app/javascript.md) – uppdatera till JavaScript SDK-avsnittet. Det uppdaterade kodfragmentet för att rapportera inläsnings problem.
- [Konfigurera BYOS (ta med din egen lagring) för profiler & Snapshot debugger](app/profiler-bring-your-own-storage.md) – ny artikel.
- [Spårning av inkommande begär anden i Azure Application insikter med Openräkningar python](app/opencensus-python-request.md) -uppdaterad loggning och konfiguration för openräkning.
- [Övervaka en live ASP.NET-webbapp med Azure Application Insights](app/monitor-performance-live-website-now.md) – uppdaterat utfasnings datum för statusövervakare v1.
- [Övervaka Node.js tjänster med Azure Application insikter](app/nodejs.md) – flera uppdateringar, inklusive migrering från tidigare-versioner och SDK-konfiguration
- [Övervaka python-program med Azure Monitor (för hands version)](app/opencensus-python.md) – tillagt avsnitt om konfiguration av Azure Monitor exportörer.
- [Övervaka dina appar utan kod ändringar – Auto-Instrumentation för Azure Monitor Application Insights](app/codeless-overview.md) nya artikeln.
- [FELSÖKA SDK-inläsnings fel för JavaScript-webbprogram](app/javascript-sdk-load-failure.md) – ny artikel.

### <a name="containers"></a>Containers
- [Så här slutar du övervaka ditt hybrid Kubernetes-kluster](containers/container-insights-optout-hybrid.md) för Arc-aktiverad Kubernetes.
- [Konfigurera Azure Arc-aktiverat Kubernetes-kluster med container Insights](containers/container-insights-enable-arc-enabled-clusters.md) – ny artikel.
- [Konfigurera Azure Red Hat OpenShift v4. x med container Insights](containers/container-insights-azure-redhat4-setup.md) – uppdaterade krav.
- [Konfigurera behållar insikter Live data (förhands granskning)](containers/container-insights-livedata-setup.md) – borttagen anmärkning om funktionen är inte tillgänglig i Azure amerikanska myndigheter.

### <a name="insights"></a>Insikter
- [Vanliga frågor och svar – övervakare av nätverksprestanda lösning i Azure](insights/network-performance-monitor-faq.md) – vanliga frågor och svar om ExpressRoute-övervakaren.

### <a name="logs"></a>Loggar
- [Ta bort och Återställ Azure-Log Analytics arbets ytan](logs/delete-workspace.md) – extra PowerShell-kommando. Uppdaterad fel sökning.
- [Hantera Log Analytics arbets ytor i Azure Monitor](logs/manage-access.md) -lagt till exempel för tabeller som inte tillåts i Azure RBAC-avsnittet.
- [Hantera användning och kostnader för Azure Monitor loggar](logs/manage-cost-storage.md) – ytterligare information om beräkning av data storlek. Uppdaterade konfiguration av data volym aviseringar. Information om säkerhets data som samlas in av Azure Sentinel. Klargörande om data kap.
- [Använd Azure Monitor loggar med Azure Logic Apps-och Energis par](logs/logicapp-flow-connector.md) -tillagda anslutnings gränser.

### <a name="metrics"></a>Mått
- [Azure Monitor mått som stöds av resurs typ](essentials/metrics-supported.md) – uppdaterade SQL Server mått.


### <a name="platform-logs"></a>Plattformsloggar

- [Exempel på Resource Manager-mallar för diagnostikinställningar](essentials/resource-manager-diagnostic-settings.md) – korrigering för diagnostisk inställning för aktivitets loggen.
- [Skicka Azure aktivitets logg till Log Analytics arbets ytan med Azure Portal](essentials/quick-collect-activity-log-portal.md) – ny artikel.
- [Skicka Azure aktivitets logg till Log Analytics arbets yta med Azure Resource Manager mall](essentials/quick-collect-activity-log-arm.md) – ny artikel.

Nya och uppdaterade artiklar från omstrukturera och konsolidering av plattforms logg innehåll

- [Arkivera Azures resurs loggar till lagrings kontot](./essentials/resource-logs.md#send-to-azure-storage)
- [Händelse schema för Azure aktivitets logg](essentials/activity-log-schema.md)
- [Azure aktivitets logg](essentials/activity-log.md)
- [Azure Monitor CLI-exempel](/cli-samples.md)
- [Azure Monitor PowerShell-exempel](/powershell-samples.md)
- [Genomgång av REST-API:et för Azure Monitoring](essentials/rest-api-walkthrough.md)
- [Tjänster och scheman som stöds av Azure-resurs loggar](./essentials/resource-logs-schema.md)
- [Azure-resursloggar](essentials/resource-logs.md)
- [Samla in och analysera Azure aktivitets logg i Azure Monitor](./essentials/activity-log.md)
- [Samla in Azures resurs loggar i Log Analytics arbets yta](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [Skapa diagnostikinställningar för att skicka plattformsloggar och mått till olika målplatser](essentials/diagnostic-settings.md)
- [Exportera Azure aktivitets loggen](./essentials/activity-log.md#legacy-collection-methods)
- [Översikt över Azure-plattformsloggar](essentials/platform-logs-overview.md)
- [Strömma Azure-plattformar loggar till en händelsehubben](./essentials/resource-logs.md#send-to-azure-event-hubs)
- [Visa Azure aktivitets logg händelser i Azure Monitor](./essentials/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>Virtuella datorer
- [Aktivera VM Insights i Azure Portal](./vm/vminsights-enable-portal.md) -uppdaterat för att inkludera Azure-bågen.
- [Aktivera översikt över VM-insikter](vm/vminsights-enable-overview.md) – uppdaterat med Azure Arc.
- [Vad är VM Insights?](vm/vminsights-overview.md) – Uppdaterad för att inkludera Azure-båge.


### <a name="visualizations"></a>Visualiseringar
- [Azure Monitor arbets böcker-data källor](visualize/workbooks-data-sources.md) – lägga till aviseringar och anpassade slut punkts avsnitt.
- [Felsöka Azure Monitor arbetsböcker-baserade insikter](insights/troubleshoot-workbooks.md) – ny artikel.
- [Uppgradera dina visualiseringar av Log Analytics-instrumentpanel](logs/dashboard-upgrade.md) – ny artikel.



## <a name="may-2020"></a>Maj 2020

### <a name="general"></a>Allmänt

- [Azure Monitor FAQ](faq.md) – tillagt avsnitt för mått.
- [Azure Monitor kundhanterad nyckel](logs/customer-managed-keys.md) – flera ändringar som är i förberedelse för allmän tillgänglighet.
- [Inbyggda princip definitioner för Azure Monitor](./policy-reference.md) nya artikeln.
- [Kundägda lagrings konton för logg](logs/private-storage.md) inmatning – ny artikel.
- [Hantera användning och kostnader för Azure Monitor loggar](logs/manage-cost-storage.md) – proportionell fakturering har lagts till i kluster.
- [Använd Azures privata länk för att på ett säkert sätt ansluta nätverk till Azure Monitor](logs/private-link-security.md) nya artikeln.


#### <a name="new-resource-manager-template-samples"></a>Nya mall exempel för Resource Manager 
- [Exempel på Resource Manager-mallar för Azure Monitor](/resource-manager-samples.md)
- [Exempel på Resource Manager-mallar för åtgärds grupper](alerts/resource-manager-action-groups.md)
- [Exempel på Resource Manager-mallar för agenter](agents/resource-manager-agent.md)
- [Exempel på Resource Manager-mallar för container Insights](containers/resource-manager-container-insights.md)
- [Exempel på Resource Manager-mallar för VM-insikter](vm/resource-manager-vminsights.md)
- [Exempel på Resource Manager-mallar för diagnostikinställningar](essentials/resource-manager-diagnostic-settings.md)
- [Exempel på Resource Manager-mallar för Log Analytics arbets ytor](logs/resource-manager-workspace.md)
- [Exempel på Resource Manager-mallar för logg frågor](logs/resource-manager-log-queries.md)
- [Exempel på Resource Manager-mallar för aviserings regler för logg frågor](alerts/resource-manager-alerts-log.md)
- [Exempel på Resource Manager-mallar för mått aviserings regler](alerts/resource-manager-alerts-metric.md)
- [Exempel på Resource Manager-mallar för arbets böcker](visualize/resource-manager-workbooks.md)

### <a name="agents"></a>Agenter
- [Installera och konfigurera Windows Azure Diagnostics Extension (wad)](agents/diagnostics-extension-windows-install.md) – ytterligare information om hur du konfigurerar diagnostik.
- [Översikt över Log Analytics agent](agents/log-analytics-agent.md) – stödda Linux-versioner som stöds.

### <a name="application-insights"></a>Application Insights

- [Övervaka program som körs på Azure Functions med Application Insights-Azure Monitor](app/monitor-functions.md) – ny artikel.
- [Övervaka Node.js tjänster med Azure Application Insights](app/nodejs.md) – allmänna uppdateringar inklusive nytt avsnitt om migrering från tidigare versioner.
- [IP-adresser som används av Application Insights och Log Analytics](app/ip-addresses.md) -TILLAGDa IP-adresser för Webhooks och amerikanska myndigheter.
- [Övervaka program på Azure Kubernetes service (AKS) med Application Insights-Azure Monitor](app/kubernetes-codeless.md) – ny artikel.
- [Fel sökning av inga data-Application Insights för .net](app/asp-net-troubleshoot-no-data.md) -tillagt avsnitt om insamling av loggar med dotNet-trace.
- [Använd program ändrings analys i Azure Monitor för att hitta problem med webb program](app/change-analysis.md) – flera uppdateringar i funktionen för ändrings analys.

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>Nya och uppdaterade artiklar för för hands versionen av arbets ytans baserade program
- [Azure Monitor Application Insights arbets yta-baserat resurs schema](app/apm-tables.md)
- [Skapa en ny Azure Monitor Application Insights arbets ytans baserade resurs](app/create-workspace-resource.md)
- [app ()-uttryck i Azure Monitor logg frågor](logs/app-expression.md)
- [Logg frågans omfång i Azure Monitor Log Analytics](logs/scope.md)
- [Fråga över resurser med Azure Monitor](logs/cross-workspace-query.md)
- [Standard egenskaper i Azure Monitor logg poster](./logs/log-standard-columns.md)
- [Struktur för Azure Monitor loggar](./logs/data-platform-logs.md)





### <a name="containers"></a>Containers
- [Så här aktiverar du container Insights](containers/container-insights-onboard.md) -uppdaterad brand Väggs konfiguration tabell.
- [Så här uppdaterar du behållar insikter för mått](containers/container-insights-update-metrics.md) – uppdatera för att använda hanterade identiteter för att samla in mått.
- [Övervaknings kostnad för container Insights](containers/container-insights-cost.md) – ny artikel.
- [Konfigurera behållar insikter Live-data (för hands version)](containers/container-insights-livedata-setup.md) – stöd för den nya kluster roll bindningen.

### <a name="insights"></a>Insikter
- [Azure Monitor för Azure cache för Redis (för hands version)](insights/redis-cache-insights-overview.md) – ny artikel.
- [Övervaka Key Vault med Azure Monitor för Key Vault (för hands version)](./insights/key-vault-insights-overview.md) – ny artikel.

### <a name="logs"></a>Loggar
- [Skapa & konfigurera Log Analytics med PowerShell](logs/powershell-workspace-configuration.md) – fel söknings avsnittet.
- [Skapa en Log Analytics arbets yta i](logs/quick-create-workspace.md) avsnittet fel sökning Azure Portal-tillägg.
- [Skapa en Log Analytics arbets yta med Azure CLI](logs/quick-create-workspace-cli.md) – fel söknings avsnittet.
- [Ta bort och Återställ Azure Log Analytics-arbetsytan](logs/delete-workspace.md) – uppdaterad information om hur du återställer en borttagen arbets yta.
- [Funktioner i Azure Monitor logg frågor](logs/functions.md) -borttagna kommentarer om funktioner som inte innehåller andra funktioner.
- [Struktur för Azure Monitor loggar](./logs/data-platform-logs.md) – för tydliga egenskaps beskrivningar för Application Insights tabell.
- [Använd Azure Monitor loggar med avsnittet begränsningar för Azure Logic Apps och Energis par som](logs/logicapp-flow-connector.md) har lagts till automatiskt.
- [Använd PowerShell för att skapa och konfigurera en Log Analytics arbets yta](logs/powershell-workspace-configuration.md) som har lagts till fel söknings avsnittet.


### <a name="metrics"></a>Mått
- [Azure Monitor mått som stöds av resurs typ](essentials/metrics-supported.md) -fördefinierade gäst mått och mått routning. 

### <a name="solutions"></a>Lösningar
- [Optimera din Active Directory-miljö med Azure Monitor](insights/ad-assessment.md) -lagt till versioner som stöds av Windows Server 2019.
- [Optimera din SQL Server-miljö med Azure Monitor](insights/sql-assessment.md) – till stödda versioner av SQL Server.


### <a name="virtual-machines"></a>Virtuella datorer
- [Aktivera översikt över VM-insikter](vm/vminsights-enable-overview.md) – har lagts till i versioner av Ubuntu Server som stöds. Regioner som stöds för Log Analytics-arbetsyta.
- [Så här diagramerar du prestanda med VM Insights](vm/vminsights-performance.md) -avsnittet begränsningar för otillgängliga mått.

### <a name="visualizations"></a>Visualiseringar
- [Azure Monitor arbets böcker och Azure Resource Manager mallar](visualize/workbooks-automate.md) – har lagt till Resource Manager-uppdatering för distribution av en mall för arbets böcker.
- [Azure Monitor arbets böcker grupper](./visualize/workbooks-groups.md) – ny artikel.
- [Azure Monitor arbets böcker – TRANSFORMERA JSON-data med JSONPath](visualize/workbooks-jsonpath.md) -ny artikel.


## <a name="april-2020"></a>April 2020

### <a name="general"></a>Allmänt

- [Azure Monitor kundhanterat nyckel](logs/customer-managed-keys.md) tillagt avsnitt om asynkrona åtgärder
- [Hantera Log Analytics arbets ytor i Azure Monitor](logs/manage-access.md) uppdaterade anpassade loggar.

### <a name="alerts"></a>Aviseringar

- [Åtgärds regler för Azure Monitor aviseringar](alerts/alerts-action-rules.md) – tillagd video.
- [Översikt över aviseringar och meddelande övervakning i Azure](alerts/alerts-overview.md) -tillagd video.

### <a name="application-insights"></a>Application Insights

- [Program karta i Azure Application insikter](app/app-map.md) – moln roll namn konfiguration för Java-agent har lagts till.
- [Referens för Azure Application Insights .net-Agent-API: t](app/status-monitor-v2-api-reference.md) .
- [IP-adresser som används av Application Insights och Log Analytics](app/ip-addresses.md) uppdaterade IP-adresser för App Insights-och Log Analytics-API: er, åtgärds grupp Webhooks, Azure amerikanska myndigheter.
- [Övervaka Java-program var som helst](app/java-standalone-config.md) – ny artikel.
- [Övervaka Java-program i valfri miljö](app/java-in-process-agent.md) – ny artikel.
- [Övervaka Java-program som körs i en miljö](app/java-standalone-arguments.md) – ny artikel.
- [Övervaka Java-program som körs lokalt](app/java-on-premises.md) – ny artikel.
- [Ta bort Application Insights i Visual Studio](app/remove-application-insights.md) – ny artikel.
- [Telemetri-sampling i Azure Application Insights](app/sampling.md) -Fix i fast priss exempel i python.

### <a name="containers"></a>Containers

- [Konfigurera Azure Red Hat OpenShift v4. x med container Insights](containers/container-insights-azure-redhat4-setup.md) – ny artikel.
- [Åtgärda ServiceNow-synkroniseringsproblem manuellt](alerts/itsmc-resync-servicenow.md) – ny artikel.
- [Så här stoppar du övervakning av v4-kluster för Azure och Red Hat OpenShift](containers/container-insights-optout-openshift-v4.md) – ny artikel.
- [Så här stoppar du övervakning av Azure Red Hat OpenShift v3-kluster](containers/container-insights-optout-openshift-v3.md) – ny artikel.
- [Så här stoppar du övervakning av ditt hybrid Kubernetes-kluster](containers/container-insights-optout-hybrid.md) – ny artikel.

### <a name="insights"></a>Insikter

- [Övervaka Azure Key Vaults med Azure Monitor för nyckel valv (för hands version)](insights/key-vault-insights-overview.md) – ny artikel.

### <a name="logs"></a>Loggar

- [Azure Monitor tjänst gränser](service-limits.md) – begränsning av användar frågor har lagts till.
- [Hantera användning och kostnader för Azure Monitor loggar](logs/manage-cost-storage.md) – tillagd fakturering för loggar kluster. Lade till Kusto-fråga för att möjliggöra för kunder med äldre pris nivå per nod att avgöra om de ska flyttas till en per GB-eller kapacitets reservations nivå.

### <a name="metrics"></a>Mått

- [Avancerade funktioner i Azure Metrics Explorer](essentials/metrics-charts.md) -tillägg av agg regerings avsnitt.

### <a name="workbooks"></a>Arbetsböcker

- [Azure Monitor arbets böcker och Azure Resource Manager mallar](visualize/workbooks-automate.md) – har lagt till Resource Manager-mallen för att distribuera en mall för arbets böcker.

## <a name="march-2020"></a>Mars 2020

### <a name="general"></a>Allmänt

- [Azure Monitor översikt](overview.md) – lägg till Azure Monitor översikts video.
- [Azure Monitor kundhanterad nyckel konfiguration](logs/customer-managed-keys.md) – allmänna uppdateringar.
- [Azure Monitor data referens](/azure/azure-monitor/reference/) – ny plats.

### <a name="alerts"></a>Aviseringar

- [Skapa, Visa och hantera aktivitets logg aviseringar i Azure Monitor](alerts/alerts-activity-log.md) – ytterligare förklaring till Resource Manager-mallen.
- [Förstå hur mått varningar fungerar i Azure Monitor.](alerts/alerts-metric-overview.md) – Uppdaterat för myndighets support.
- [Felsöka Azure Monitor aviseringar och meddelanden](alerts/alerts-troubleshoot.md) – ny artikel.

### <a name="application-insights"></a>Application Insights

- [Automatisera Azure Application insikter med PowerShell](app/powershell.md) -ARMClient exempel.
- [Kontinuerlig export av telemetri från Application Insights](app/export-telemetry.md) – Lägg till tabell med information om export struktur.
- [Aktivera Snapshot debugger för .net-appar i Azure App Service](app/snapshot-debugger-appservice.md) -tillagd Resource Manager-mall exempel.
- [Hantera användning och kostnader för Azure Application insikter](app/pricing.md) – information om data Cap-aviseringar har lagts till.
- [Övervaka python-program med Azure Monitor (för hands version)](app/opencensus-python.md) – tillagda standard mått.
- [Käll kart stöd för JavaScript-program – Azure Monitor Application Insights](app/source-map-support.md) – ny artikel.

### <a name="containers"></a>Containers

- [Azure Monitor FAQ](faq.md) – uppdatering för behållar insikter.
- [Konfigurera GPU-övervakning med container Insights](containers/container-insights-gpu-monitoring.md) – ny artikel.

### <a name="insights"></a>Insikter

- [Office 365-hanterings lösning i Azure](insights/solution-office-365.md) – uppdaterat utfasnings datum.

### <a name="logs"></a>Loggar

- [Optimera logg frågor i Azure Monitor](logs/query-optimization.md) -extra processor villkor för XML-och JSON-parsning.
- [Ta bort och återställa Azure Log Analytics-arbetsytan](logs/delete-workspace.md) – fel sökning har lagts till.
- [Använd Azure Monitor loggar med Azure Logic Apps och energi automatisering](logs/logicapp-flow-connector.md) – uppdaterat för nya Azure Monitor-anslutningar.

### <a name="metrics"></a>Mått

- [Föråldrade disk värden i den Azure Portal](essentials/portal-disk-metrics-deprecation.md) nya artikeln.
- [Självstudie – Skapa ett mått diagram i Azure Monitor](essentials/tutorial-metrics-explorer.md) -tillagd video.

### <a name="platform-logs"></a>Plattformsloggar

- [Samla in och analysera Azure aktivitets logg i Azure Monitor](./essentials/activity-log.md) – Skriv om för att få en bättre förklaring av aktivitets loggen med diagnostiska inställningar.

### <a name="virtual-machines"></a>Virtuella datorer

- [Övervaka virtuella Azure-datorer med Azure Monitor](vm/monitor-vm-azure.md) ny artikel.
- [Snabb start: övervaka virtuella Azure-datorer med Azure Monitor](vm/quick-monitor-azure-vm.md) -uppdaterat för att lägga till VM-insikter.
- [Aviseringar från VM Insights](vm/vminsights-alerts.md) – ny artikel.
- [Aktivera översikt över VM-insikter](vm/vminsights-enable-overview.md) -uppdaterade Länkar för agent hämtning.

Allmänna uppdateringar för allmän tillgänglighet för VM-insikter

- [Vad är VM Insights?](vm/vminsights-overview.md)
- [Vanliga frågor och svar om VM Insights (GA)](vm/vminsights-ga-release-faq.md) 
- [Aktivera VM-insikter med hjälp av Azure Policy](./vm/vminsights-enable-policy.md) 
- [Så här diagramerar du prestanda med VM Insights](vm/vminsights-performance.md)
- [Så här frågar du efter loggar från VM Insights](vm/vminsights-log-search.md)
- [Visa program beroenden med VM Insights](vm/vminsights-maps.md) 

### <a name="visualizations"></a>Visualiseringar

- [Visualiserar data från Azure Monitor](visualizations.md) -uppdaterat för att notera planerad utfasning av View Designer.

## <a name="february-2020"></a>Februari 2020

### <a name="agents"></a>Agenter

Flera uppdateringar som en del av omskrivning av innehåll för diagnostik-tillägg.

- [Översikt över Azure Monitoring agents](agents/agents-overview.md) – strukturerade tabeller för att bättre klargöra unika funktioner i varje agent.
- [Översikt över Azure-diagnostik-tillägg](agents/diagnostics-extension-overview.md) – fullständig omskrivning.
- [Använd Blob Storage för IIS och tabell lagring för händelser i Azure Monitor](essentials/diagnostics-extension-logs.md) – allmän omskrivning för uppdatering och tydlighet.
- [Installera och konfigurera Windows Azure Diagnostics Extension (wad)](agents/diagnostics-extension-windows-install.md) – ny artikel. 
- [Tilläggs schema för Windows-diagnostik](agents/diagnostics-extension-schema-windows.md) – omorganisera.
- [Skicka data från Windows Azure Diagnostics-tillägget till Azure Event Hubs](agents/diagnostics-extension-stream-event-hubs.md) -helt omskrivna och uppdaterade.
- [Lagra och Visa diagnostikdata i Azure Storage](../cloud-services/diagnostics-extension-to-storage.md) -helt omskrivna och uppdaterade.
- [Log Analytics tillägg för virtuell dator för Windows](../virtual-machines/extensions/oms-windows.md) – bättre klargör relationen med Log Analytics-agenten.
- [Azure Monitor tillägg för virtuell dator för Linux](../virtual-machines/extensions/oms-linux.md) – bättre klargör relationen med Log Analytics-agenten.

### <a name="application-insights"></a>Application Insights

- [Anslutnings strängar i Azure Application Insights](app/sdk-connection-string.md) – ny artikel.

### <a name="insights-and-solutions"></a>Insikter och lösningar

#### <a name="container-insights"></a>Container Insights

- [Integrera Azure Active Directory med Azure Kubernetes service](../aks/azure-ad-integration-cli.md) – en anteckning om att skapa ett klient program som stöder Kubernetes RBAC-aktiverat kluster för att stödja behållar insikter.

#### <a name="vm-insights"></a>VM-insikter

- [Vanliga frågor och svar om VM Insights (ga)](vm/vminsights-ga-release-faq.md) – ändring av hur prestanda data lagras.

#### <a name="office-365"></a>Office 365

- [Office 365-hanterings lösning i Azure](insights/solution-office-365.md) – uppdaterat utfasnings datum.


### <a name="logs"></a>Loggar

- [Optimera logg frågor i Azure Monitor](logs/query-optimization.md) – ny artikel.
- [Hantera användning och kostnader för Azure Monitor loggar](logs/manage-cost-storage.md) – förbättrade exempel frågor som hjälper dig att förstå din användning.

### <a name="metrics"></a>Mått

- [Azure Monitor plattforms mått som exporteras via diagnostikinställningar](essentials/metrics-supported-export-diagnostic-settings.md) – avsnittet har lagts till vid ändring av beteendet för null-värden och nollvärden.

### <a name="visualizations"></a>Visualiseringar

Flera nya artiklar för konverterings guiden för Visa designer till arbets böcker.

- [Azure Monitor Visa designer till arbets böcker över gångs guide](visualize/view-designer-conversion-overview.md) – ny artikel.
- [Azure Monitor Visa designer till arbets böcker konverterings alternativ](visualize/view-designer-conversion-options.md) -ny artikel.
- [Azure Monitor Visa designer till panel konverteringar](visualize/view-designer-conversion-tiles.md) – ny artikel.
- [Azure Monitor Visa designer till arbets böcker konverterings Sammanfattning och Access](visualize/view-designer-conversion-access.md) -ny artikel.
- [Azure Monitor Visa designer till arbets böcker konvertering av vanliga uppgifter](visualize/view-designer-conversion-tasks.md) – ny artikel.
- [Azure Monitor Visa designer till arbets böcker konverterings exempel](visualize/view-designer-conversion-examples.md) – ny artikel.

## <a name="january-2020"></a>Januari 2020

### <a name="general"></a>Allmänt

- [Vad övervakas av Azure Monitor?](monitor-reference.md) – Ny artikel.

### <a name="agents"></a>Agenter

- [Samla in loggdata med Azure Log Analytics agent](agents/log-analytics-agent.md) -uppdaterad tabell över nätverks brand Väggs krav.

### <a name="alerts"></a>Aviseringar

- [Skapa och hantera åtgärds grupper i Azure Portal](alerts/action-groups.md) -inställningen tas bort för v2-funktioner som inte längre krävs.
- [Skapa en mått varning med en Resource Manager-mall – ett](alerts/alerts-metric-create-templates.md) exempel har lagts till för parametern *ignoreDataBefore* .  Begränsningar för regler för flera villkor har lagts till.
- Att [använda Log Analytics varning REST API](alerts/api-alerts.md) – JSON-exempel korrigeras.

### <a name="application-insights"></a>Application Insights

- [IP-adresser som används av Application Insights och Log Analytics](app/ip-addresses.md) – uppdaterat avsnittet tillgänglighets test med anvisningar för hur du lägger till en regel för inkommande portar för att tillåta trafik med hjälp av Azure nätverks säkerhets grupper.
- [Felsök problem med Azure Application Insights profiler](app/profiler-troubleshooting.md) -uppdaterad allmän fel sökning.
- [Telemetri-sampling i Azure Application insikter](app/sampling.md) – uppdateras och struktureras om för att förbättra läsbarheten baserat på feedback från kunderna.

### <a name="data-security"></a>Datasäkerhet

- [Azure Monitor kundhanterad nyckel konfiguration](logs/customer-managed-keys.md) – ny artikel.

### <a name="insights-and-solutions"></a>Insikter och lösningar

#### <a name="container-insights"></a>Container Insights

- [Konfigurera data insamling för container Insights](containers/container-insights-agent-config.md) – ytterligare information för att uppgradera agenten finns i OpenShift i Azure Red Hat och ytterligare information har lagts till för att skilja på metoderna för att uppgradera agenten.
- [Skapa prestanda varningar för behållar insikter](./containers/container-insights-log-alerts.md) – ändrad information och uppdaterade steg för att skapa en avisering om prestanda data som lagras i arbets ytan med hjälp av arbets ytans kontext aviseringar.
- [Kubernetes övervakning med container Insights](containers/container-insights-analyze.md) – uppdaterad både översikts artikeln och analys artikeln om stöd för Windows Kubernetes-kluster.
- [Konfigurera Azure Red Hat OpenShift-kluster med behållar insikter](containers/container-insights-azure-redhat-setup.md) – ytterligare information för att uppgradera agenten i Azure Red Hat OpenShift och ytterligare information som särskiljer metoderna för att uppgradera agenten.
- [Konfigurera hybrid Kubernetes-kluster med behållar insikter](containers/container-insights-hybrid-setup.md) – uppdateras för att avspegla ytterligare stöd för säker port: 10250 med Kubelet cAdvisor.
- [Hantera behållar insikter-agenten](containers/container-insights-manage-agent.md) – uppdaterad information relaterad till beteende och konfiguration av mått kassation med Azure Red Hat OpenShift jämfört med andra typer av Kubernetes-kluster.
- [Konfigurera Prometheus-integration för container Insights](containers/container-insights-prometheus-integration.md) – uppdaterad information relaterad till beteende och konfiguration av mått kassation med Azure Red Hat OpenShift jämfört med andra typer av Kubernetes-kluster.
- [Uppdatera behållar insikter för mått](containers/container-insights-update-metrics.md) – uppdaterad information relaterad till beteende och konfiguration av mått kassation med Azure Red Hat OpenShift jämfört med andra typer av Kubernetes-kluster.

#### <a name="vm-insights"></a>VM-insikter

- [Vanliga frågor och svar om VM Insights (ga)](vm/vminsights-ga-release-faq.md) – ytterligare information om hur du uppgraderar arbets ytan och agenter till den nya versionen.

#### <a name="office-365"></a>Office 365

- [Office 365-hanterings lösning i Azure](insights/solution-office-365.md) – ytterligare information och vanliga frågor och svar om migrering till Office 365-lösning i Azure Sentinel. Avsnittet har tagits bort.

### <a name="logs"></a>Loggar

- [Hantera Log Analytics arbets ytor i Azure Monitor](logs/manage-access.md) – uppdateringar av inte åtgärder.
- [Hantera användning och kostnader för Azure Monitor loggar](logs/manage-cost-storage.md) – du har lagt till klargöranden vid beräkning av data volym i avsnittet prissättnings modell.
- [Använd Azure Resource Manager mallar för att skapa och konfigurera en mall för Log Analytics-arbetsytan](./logs/resource-manager-workspace.md) – uppdaterad med nya pris nivåer.

### <a name="platform-logs"></a>Plattformsloggar

- [Samla in Azure aktivitets logg med diagnostikinställningar – Azure Monitor](./essentials/activity-log.md) – ytterligare information om ändrade egenskaper.
- [Exportera Azure aktivitets loggen](./essentials/activity-log.md#legacy-collection-methods) – uppdaterad för ändringar i gränssnittet. 

## <a name="december-2019"></a>December 2019

### <a name="agents"></a>Agenter

- [Anslut Linux-datorer till Azure Monitor](agents/agent-linux.md) -ny artikel.

### <a name="alerts"></a>Aviseringar

- [Skapa en mått varning med en Resource Manager-mall](alerts/alerts-metric-create-templates.md) – exempel på anpassat mått har lagts till.
- [Skapa aviseringar med dynamiska tröskelvärden i avsnittet Azure Monitor](alerts/alerts-dynamic-thresholds.md) -Added för tolkning av dynamiska tröskel diagram.
- [Översikt över aviseringar och meddelande övervakning i Azure](alerts/alerts-overview.md) -uppdaterad resurs diagram fråga.
- [Resurser som stöds för mått varningar i Azure Monitor](alerts/alerts-metric-near-real-time.md) – uppdatering av mått och dimensioner som stöds.
- [Växla från äldre Log Analytics aviserings-API till nya](alerts/alerts-log-api-switch.md) aviseringar i Azure-aviseringar – Lägg till anteckning om ändrat aviserings namn.
- [Förstå hur mått varningar fungerar i Azure Monitor.](alerts/alerts-metric-overview.md) -Resurs typer som stöds för övervakning i skala har lagts till.

### <a name="application-insights"></a>Application Insights

- [Application Insights för Worker service-appar (icke-http-appar)](app/worker-service.md) – en standard loggnings nivå har lagts till i C#-koden. Uppdaterad paket referens version.
- [ApplicationInsights.config referens – Azure](app/configuration-with-applicationinsights-config.md) -uppdaterad exempel kod.
- [Automatisera Azure Application Insights med PowerShell](app/powershell.md) – uppdatera till Resource Manager-mall.
- [Skapa en ny Azure Application Insights-resurs](app/create-new-resource.md) -anteckning som har lagts till i globalt unikt namn.
- [Diagnostisera med Live Metrics Stream-Azure Application Insights](app/live-stream.md) – uppdaterat krav för ASP.net Core SDK-version.
- [Händelse räknare i Application Insights](app/eventcounters.md) -uppdaterad kategori och tabell till customMetrics.
- [Utforska Java trace-loggar i Azure Application Insights](app/java-trace-logs.md) -tillagd konfiguration för loggnings tröskeln för Java-agent.
- [IP-adresser som används av Application Insights och Log Analytics](app/ip-addresses.md) uppdaterade IP-adresser för Live Metrics Stream.
- [Övervaka Azure App Services-prestanda](app/azure-web-apps.md) – stöd har lagts till för ASP.net Core 3,0. 
- [Övervaka python-program med Azure Monitor (för hands version)](app/opencensus-python.md) – du har lagt till klargöranden för python-schema mappning i openpool till Azure. Övervaka schema
- [Viktig information för Azure Application insikter](app/release-notes.md) – tillagda anteckningar för äldre versioner.
- [Telemetri kanaler i Azure Application insikter](app/telemetry-channels.md) – uppdaterad varaktighet för förkastade data under den utökade tids perioden för förlorad anslutning.
- [Telemetri-sampling i Azure Application Insights](app/sampling.md) – korrigerat kodfragment för anpassade TelemetryInitializer.
- [Felsök Application Insights i en Java-webbprojekt](app/java-troubleshoot.md) -borttagen instruktion om att inte stödja beroende insamling i JDK 9.

### <a name="insights-and-solutions"></a>Insikter och lösningar

- [Behållar frågor för behållar](./faq.md) information – tillagd fråga i fälten bild och namn.
- [Azure SQL-analys lösning i Azure Monitor](insights/azure-sql.md) -uppdaterad databas väntar på stöd för hanterade instanser.
- [Konfigurera inställningen för container Insights-agentens data insamling](containers/container-insights-agent-config.md) – har lagts till för enrich_container_logs.
- [Konfigurera hybrid Kubernetes-kluster med behållar insikter](containers/container-insights-hybrid-setup.md) – fel söknings avsnittet har lagts till.
- [Övervaka Active Directory replikeringsstatus med Azure Monitor](insights/ad-replication-status.md) -.NET Framework nödvändiga uppdateringar.
- [Övervakare av nätverksprestanda lösning i Azure](insights/network-performance-monitor.md) – tillagda regioner som stöds.
- [Optimera din Active Directory miljö med Azure Monitor](insights/ad-assessment.md) -.NET Framework nödvändiga uppdateringar.
- [Optimera din SQL Server miljö med Azure Monitor](insights/sql-assessment.md) -.NET Framework nödvändiga uppdateringar.
- [Optimera din System Center Operations Manager-miljö med Azure Log Analytics](insights/scom-assessment.md) -.NET Framework nödvändiga uppdateringar.
- [Anslutningar som stöds med anslutningsprogram för hantering av IT-tjänster (ITSM) i Azure Log Analytics](alerts/itsmc-connections.md) – tillagt New York till det nödvändiga klient-ID: t och klient hemligheten.

### <a name="logs"></a>Loggar

- [Ta bort och Återställ Azure-Log Analytics arbets yta](logs/delete-workspace.md) som har lagts till med PowerShell-metoden.
- Genom [att utforma dina Azure Monitor loggar distribution](logs/design-logs-deployment.md) efter inmatnings takt för en arbets yta ökade.

### <a name="metrics"></a>Mått

- [Azure Monitor plattforms mått som exporteras via diagnostikinställningar](essentials/metrics-supported-export-diagnostic-settings.md) – ny artikel.

### <a name="platform-logs"></a>Plattformsloggar

Flera artiklar har uppdaterats som en del av omstrukturering av innehåll för plattforms loggar baserat på en ny funktion för att konfigurera aktivitets loggen med hjälp av diagnostikinställningar.

- [Arkivera Azures resurs loggar till lagrings kontot](./essentials/resource-logs.md#send-to-azure-storage)
- [Händelse schema för Azure aktivitets logg](essentials/activity-log-schema.md)
- [Azure Monitor tjänst gränser](service-limits.md)
- [Samla in och analysera Azure aktivitets loggar i Log Analytics arbets yta](./essentials/activity-log.md)
- [Samla in Azure aktivitets logg med diagnostikinställningar (för hands version) – Azure Monitor](./essentials/activity-log.md)
- [Samla in Azures aktivitets loggar i en Log Analytics arbets yta över Azure-klienter](./essentials/activity-log.md)
- [Samla in Azures resurs loggar i Log Analytics arbets yta](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [Skapa diagnostisk inställning i Azure med Resource Manager-mall](./essentials/resource-manager-diagnostic-settings.md)
- [Skapa en diagnostisk inställning för insamling av loggar och mått i Azure](essentials/diagnostic-settings.md)
- [Exportera Azure aktivitets loggen](./essentials/activity-log.md#legacy-collection-methods)
- [Översikt över Azure-plattformsloggar](essentials/platform-logs-overview.md)
- [Strömma Azure-övervaknings data till händelsehubben](essentials/stream-monitoring-data-event-hubs.md)
- [Strömma Azure-plattformar loggar till en händelsehubben](./essentials/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>Snabbstarter och självstudier

- [Skapa ett mått diagram i Azure Monitor](essentials/tutorial-metrics-explorer.md) nya artikeln.
- [Samla in resurs loggar från en Azure-resurs och analysera med Azure Monitor](essentials/tutorial-resource-logs.md) nya artikeln.
- [Övervaka en Azure-resurs med Azure Monitor](essentials/quick-monitor-azure-resource.md) ny artikel.
   
## <a name="next-steps"></a>Nästa steg

- Om du vill bidra till Azure Monitor-dokumentationen kan du läsa [guiden dokument bidrags givare](/contribute/).