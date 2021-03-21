---
title: Vad övervakas av Azure Monitor
description: Referens för alla tjänster och andra resurser som övervakas av Azure Monitor.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 08/15/2020
ms.openlocfilehash: 4bf792dd02e7cddcc40ef868e4a602fdb03ab3c6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052287"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Vad övervakas av Azure Monitor?
I den här artikeln beskrivs de olika program och tjänster som övervakas av Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Insikter och huvud lösningar
Grundläggande insikter och lösningar betraktas som en del av Azure Monitor och följer support-och service nivå avtalen för Azure. De stöds i alla Azure-regioner där Azure Monitor är tillgängligt.

### <a name="insights"></a>Insikter

Insikter ger en anpassad övervaknings miljö för specifika program och tjänster. De samlar in och analyserar både loggar och mått.

| Insight | Beskrivning |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Utöknings bar APM-tjänst (Application Performance Management) för att övervaka ditt Live-webbprogram på valfri plattform. |
| [Container Insights](containers/container-insights-overview.md) | Övervakar prestanda för behållar arbets belastningar som distribueras till antingen Azure Container Instances eller hanterade Kubernetes-kluster som finns i Azure Kubernetes service (AKS). |
| [Azure Monitor för Cosmos DB](insights/cosmosdb-insights-overview.md) | Innehåller en översikt över övergripande prestanda, fel, kapacitet och drifts hälsa för alla dina Azure Cosmos DB resurser i en enhetlig interaktiv upplevelse. |
| [Azure Monitor för nätverk (för hands version)](insights/network-insights-overview.md) | Innehåller en omfattande vy över hälso tillstånd och mått för alla nätverks resurser. Med den avancerade Sök funktionen kan du identifiera resurs beroenden, aktivera scenarier som att identifiera resurser som är värdar för din webbplats genom att helt enkelt söka efter namnet på din webbplats. |
[Azure Monitor för resurs grupper (förhands granskning)](insights/resource-group-insights.md) |  Prioritering och diagnostisera eventuella problem som kan uppstå för enskilda resurser, samtidigt som den erbjuder kontexten och prestandan för resurs gruppen som helhet. |
| [Azure Monitor för lagring](insights/storage-insights-overview.md) | Tillhandahåller omfattande övervakning av dina Azure Storage konton genom att leverera en enhetlig vy över dina Azure Storages prestanda, kapacitet och tillgänglighet. |
| [VM-insikter](vm/vminsights-overview.md) | Övervakar dina virtuella Azure-datorer (VM) och skalnings uppsättningar för virtuella datorer i stor skala. Den analyserar prestanda och hälsotillstånd för dina virtuella datorer med Windows och Linux, och övervakar deras processer och beroenden på andra resurser och i externa processer. |
| [Azure Monitor för Key Vault (förhands granskning)](./insights/key-vault-insights-overview.md) | Tillhandahåller omfattande övervakning av dina nyckel valv genom att leverera en enhetlig vy över dina Key Vault begär Anden, prestanda, haverier och svars tider. |
| [Azure Monitor för Azure cache för Redis (för hands version)](insights/redis-cache-insights-overview.md) |  Ger en enhetlig, interaktiv vy över övergripande prestanda, fel, kapacitet och drifts hälsa. |


### <a name="core-solutions"></a>Kärn lösningar

Lösningar baseras på logg frågor och vyer som är anpassade för ett visst program eller en viss tjänst. De samlar in och analyserar bara loggar och är föråldrade med tiden för insikter.

| Lösning | Beskrivning |
|:---|:---|
| [Agent hälsa](insights/solution-agenthealth.md) | Analysera hälsan och konfigurationen av Log Analyticss agenter. |
| [Varningshantering](insights/alert-management-solution.md) | Analysera aviseringar som samlats in från System Center Operations Manager, nagios eller zabbix. |
| [Tjänstkarta](vm/service-map.md) | Identifierar automatiskt program komponenter i Windows-och Linux-system och mappar kommunikationen mellan tjänsterna. |



## <a name="azure-services"></a>Azure-tjänster
I följande tabell visas Azure-tjänster och de data som samlas in i Azure Monitor. 

- Mått – tjänsten samlar automatiskt in mått i Azure Monitor Mät värden. 
- Loggar – tjänsten stöder diagnostikinställningar som kan samla in plattforms loggar och statistik för att Azure Monitor loggar.
- Insight – det finns en insikt tillgänglig för tjänsten som tillhandahåller en anpassad övervaknings upplevelse för tjänsten.

| Tjänst | Mått | Loggar | Insight | Kommentarer |
|:---|:---|:---|:---|:---|
|Active Directory | Nej | Ja | [Ja](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Inga | Inga | Inga |  |
|Active Directory Domain Services | Inga | Ja | Inga |  |
|Aktivitetslogg | Inga | Ja | Inga | |
|Advanced Threat Protection | Inga | Inga | Inga |  |
|Advisor | Inga | Inga | Inga |  |
|AI Builder | Inga | Inga | Inga |  |
|Analysis Services | Ja | Ja | Inga |  |
|API för FHIR | Inga | Inga | Inga |  |
|API Management | Ja | Ja | Inga |  |
|App Service | Ja | Ja | Inga |  |
|AppConfig | Inga | Inga | Inga |  |
|Application Gateway | Ja | Ja | Inga |  |
|Attesterings tjänst | Inga | Inga | Inga |  |
|Automation | Ja | Ja | Inga |  |
|Azure Service Manager (RDFE) | Inga | Inga | Inga |  |
|Backup | Inga | Ja | Inga |  |
|Bastion | Inga | Inga | Inga |  |
|Batch | Ja | Ja | Inga |  |
|Batch AI | Inga | Inga | Inga |  |
|Blockchain Service | Inga | Ja | Inga |  |
|Skisser | Inga | Inga | Inga |  |
|Bot Service | Inga | Inga | Inga |  |
|Cloud Services | Ja | Ja | Inga | Agent som krävs för att övervaka gäst operativ system och arbets flöden.  |
|Cloud Shell | Inga | Inga | Inga |  |
|Cognitive Services | Ja | Ja | Inga |  |
|Container Instances | Ja | Inga | Inga |  |
|Container Registry | Ja | Ja | Inga |  |
|Innehållsleverantörsnätverk (CDN) | Inga | Ja | Inga |  |
|Cosmos DB | Ja | Ja | [Ja](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | Inga | Inga | Inga |  |
|Data Box | Inga | Inga | Inga |  |
|Data Catalog Gen2 | Inga | Inga | Inga |  |
|Data Explorer | Ja | Ja | Inga |  |
|Data Factory | Ja | Ja | Inga |  |
|Data Factory v2 | Inga | Ja | Inga |  |
|Data Share | Inga | Inga | Inga |  |
|Database for MariaDB | Ja | Ja | Inga |  |
|Database for MySQL | Ja | Ja | Inga |  |
|Database for PostgreSQL | Ja | Ja | Inga |  |
|Database Migration Service | Inga | Inga | Inga |  |
|Databricks | Inga | Ja | Inga |  |
|DDoS Protection | Ja | Ja | Inga |  |
|DevOps | Inga | Inga | Inga |  |
|DNS | Ja | Inga | Inga |  |
|Domännamn | Inga | Inga | Inga |  |
|– | Inga | Inga | Inga |  |
|Dynamics 365 kund engagemang | Inga | Inga | Inga |  |
|Dynamics 365-ekonomi och-åtgärder | Inga | Inga | Inga |  |
|Event Grid | Ja | Inga | Inga |  |
|Event Hubs | Ja | Ja | Inga |  |
|ExpressRoute | Ja | Ja | Inga |  |
|Brandvägg | Ja | Ja | Inga |  |
|Front Door | Ja | Ja | Inga |  |
|Functions | Ja | Ja | Inga |  |
|HDInsight | Inga | Ja | Inga |  |
|HPC Cache | Inga | Inga | Inga |  |
|Information Protection | Inga | Ja | Inga |  |
|Intune | Inga | Ja | Inga |  |
|IoT Central | Inga | Inga | Inga |  |
|IoT Hub | Ja | Ja | Inga |  |
|Key Vault | Ja | Ja | [Ja](./insights/key-vault-insights-overview.md) |  |
|Kubernetes Service (AKS) | Inga | Inga | [Ja](containers/container-insights-overview.md)  |  |
|Load Balancer | Ja | Inga | Inga |  |
|Logic Apps | Ja | Ja | Inga |  |
|Machine Learning Service | Inga | Inga | Inga |  |
|Managed Applications  | Inga | Inga | Inga |  |
|Maps  | Inga | Inga | Inga |  |
|Media Services | Ja | Ja | Inga |  |
|Microsoft Hanterat skrivbord | Inga | Inga | Inga |  |
|Microsoft PowerApps | Inga | Inga | Inga |  |
|Microsoft Social Engagement | Inga | Inga | Inga |  |
|Microsoft Stream | Ja | Ja | Inga |  |
|Migrera | Inga | Inga | Inga |  |
|Multi-Factor Authentication | Inga | Ja | Inga |  |
|Network Watcher | Ja | Ja | Inga |  |
|Notification Hubs | Ja | Inga | Inga |  |
|Open Datasets | Inga | Inga | Inga |  |
|Policy | Inga | Inga | Inga |  |
|Power Automate | Inga | Inga | Nej |  |
|Power BI Embedded | Ja | Ja | Inga |  |
|Private Link | Inga | Inga | Inga |  |
|Kommunikations plattform för bufferthanterare | Inga | Inga | Inga |  |
|Red Hat OpenShift | Inga | Inga | Inga |  |
|Redis Cache | Ja | Ja | [Ja](insights/redis-cache-insights-overview.md) | |
|Resource Graph | Inga | Inga | Inga |  |
|Resource Manager | Inga | Inga | Inga |  |
|Butiks sökning – efter Bing | Inga | Inga | Inga |  |
|Sök | Ja | Ja | Inga |  |
|Service Bus | Ja | Ja | Inga |  |
|Service Fabric | Inga | Ja | Inga | Agent som krävs för att övervaka gäst operativ system och arbets flöden.  |
|Registrerings Portal | Inga | Inga | Inga |  |
|Site Recovery | Inga | Ja | Inga |  |
|Vår moln tjänst | Inga | Inga | Inga |  |
|Azure Synapse Analytics | Ja | Ja | Inga |  |
|SQL Database | Ja | Ja | Inga |  |
|SQL Server Stretch Database | Ja | Ja | Inga |  |
|Stack | Inga | Inga | Inga |  |
|Storage | Ja | Inga | [Ja](insights/storage-insights-overview.md) |  |
|Storage cache | Inga | Inga | Inga |  |
|Tjänster för synkronisering av lagring | Inga | Inga | Inga |  |
|Stream Analytics | Ja | Ja | Inga |  |
|Time Series Insights | Ja | Ja | Inga |  |
|TINA | Inga | Inga | Inga |  |
|Traffic Manager | Ja | Ja | Inga |  |
|Universell utskrift | Inga | Inga | Inga |  |
|Virtual Machine Scale Sets | Inga | Ja | [Ja](vm/vminsights-overview.md) | Agent som krävs för att övervaka gäst operativ system och arbets flöden. |
|Virtual Machines | Ja | Ja | [Ja](vm/vminsights-overview.md) | Agent som krävs för att övervaka gäst operativ system och arbets flöden. |
|Virtual Network | Ja | Ja | [Ja](insights/network-insights-overview.md) |  |
|Virtual Network NSG flödes loggar | Inga | Ja | Inga |  |
|VPN Gateway | Ja | Ja | Inga |  |
|Windows Virtual Desktop | Inga | Inga | Inga |  |

## <a name="virtual-machine-agents"></a>Agenter för virtuella datorer
I följande tabell visas de agenter som kan samla in data från gäst operativ systemet på virtuella datorer och skicka data som ska övervakas. Varje agent kan samla in olika data och skicka dem till antingen mått eller loggar i Azure Monitor. 

Se [Översikt över Azure Monitor agenter](agents/agents-overview.md) för information om de data som varje agent kan samla in.

| Agent |  Mått | Loggar |
|:---|:---|:---|:---|
| [Azure Monitor Agent (förhands granskning)](agents/azure-monitor-agent-overview.md) | Ja | Ja |
| [Log Analytics-agent](agents/log-analytics-agent.md) | Inga | Ja|
| [Diagnostiskt tillägg](agents/diagnostics-extension-overview.md) | Ja | Inga |
| [Teleympkvistar-agent](essentials/collect-custom-metrics-linux-telegraf.md) | Ja | Inga |
| [Beroendeagent](vm/vminsights-enable-overview.md) | Inga | Ja |


## <a name="product-integrations"></a>Produkt integreringar
Tjänsterna och lösningarna i följande tabell lagrar sina data på en Log Analytics arbets yta så att den kan analyseras med andra loggdata som samlas in av Azure Monitor.

| Produkt/tjänst | Beskrivning |
|:---|:---|
| [Azure Automation](../automation/index.yml) | Hantera uppdateringar av operativ system och spåra ändringar på Windows-och Linux-datorer. Se [ändringsspårning](../automation/change-tracking/overview.md) och [uppdateringshantering](../automation/update-management/overview.md). |
| [Azure Information Protection ](/azure/information-protection/) | Klassificera och eventuellt skydda dokument och e-postmeddelanden. Se [Central rapportering för Azure information Protection](/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Azure Security Center](../security-center/index.yml) | Samla in och analysera säkerhets händelser och utför hot analyser. Se [data insamling i Azure Security Center](../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../sentinel/index.yml) | Ansluter till olika källor, inklusive Office 365 och Amazon Web Services Cloud trailer. Se [Anslut data källor](../sentinel/connect-data-sources.md). |
| [Microsoft Intune](/intune/) | Skapa en diagnostisk inställning för att skicka loggar till Azure Monitor. Se [Skicka logg data till lagring, Event Hub eller Log Analytics i Intune (för hands version)](/intune/fundamentals/review-logs-using-azure-monitor).  |
| Nätverk  | [Övervakare av nätverksprestanda](insights/network-performance-monitor.md) -övervaka nätverks anslutningar och prestanda till tjänst-och program slut punkter.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics) – analysera loggar och mått från Azure Application Gateway.<br>[Trafikanalys](../network-watcher/traffic-analytics.md) -analyserar Network Watcher nätverks säkerhets grupps flödes loggar (NSG) för att ge insikter i trafikflödet i ditt Azure-moln. |
| [Office 365](insights/solution-office-365.md) | Övervaka din Office 365-miljö. Uppdaterad version med förbättrad onboarding tillgänglig via Azure Sentinel. |
| [SQL-analys](insights/azure-sql.md) | Övervaka prestanda för Azure SQL-databaser och SQL-hanterade instanser i stor skala och över flera prenumerationer. |
| [Surface Hub](insights/surface-hubs.md) | Spåra hälsan och användningen av Surface Hub enheter. |
| [System Center Operations Manager](/system-center/scom) | Samla in data från Operations Manager-agenter genom att ansluta hanterings gruppen till Azure Monitor. Se [ansluta Operations Manager till Azure Monitor](agents/om-agents.md)<br> Utvärdera riskerna och hälsan i din System Center Operations Manager hanterings grupp med [Operations Manager utvärderings](insights/scom-assessment.md) lösning. |
| [Microsoft Teams-rum](/microsoftteams/room-systems/azure-monitor-deploy) | Integrerad hantering av Microsoft Teams-enheter från slut punkt till slut punkt. |
| [Visual Studio App Center](/appcenter/) | Bygg, testa och distribuera program och övervaka sedan deras status och användning. Se [Starta analys av mobilappen med App Center och Application Insights](app/mobile-center-quickstart.md). |
| Windows | [Windows Update kompatibilitet](/windows/deployment/update/update-compliance-get-started) – utvärdera dina Windows Desktop-uppgraderingar.<br>[Skriv bords analys](/configmgr/desktop-analytics/overview) – integreras med Configuration Manager för att ge insikt och information för att fatta mer välgrundade beslut om uppdaterings beredskap för dina Windows-klienter. |



## <a name="other-solutions"></a>Andra lösningar
Andra lösningar är tillgängliga för övervakning av olika program och tjänster, men den aktiva utvecklingen har stoppats och är kanske inte tillgänglig i alla regioner. De omfattas av service avtalet för Azure Log Analytics data inmatnings tjänsten.

| Lösning | Beskrivning |
|:---|:---|
| [Active Directory hälso kontroll](insights/ad-assessment.md) | Utvärdera riskerna och hälso tillståndet i dina Active Directorys miljöer. |
| [Active Directory replikeringsstatus](insights/ad-replication-status.md) | Övervakar regelbundet din Active Directorys miljö för eventuella replikeringsfel. |
| [Aktivitets logg analys](essentials/activity-log.md#activity-log-analytics-monitoring-solution) | Visa aktivitets logg poster. |
| [DNS-analys (för hands version)](insights/dns-analytics.md) | Samlar in, analyserar och korrelerar Windows DNS analys-och gransknings loggar och andra relaterade data från dina DNS-servrar. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Samla in, Visa och analysera Cloud Foundry systemets hälso tillstånds-och prestanda mått i flera distributioner. |
| [Containrar](containers/containers.md) | Visa och hantera Docker-och Windows container-värdar. |
| [Bedömningar på begäran](/services-hub/health/getting_started_with_on_demand_assessments) | Utvärdera och optimera tillgänglighet, säkerhet och prestanda för dina lokala, hybrid-och molnbaserade Microsoft Technology-miljöer. |
| [SQL-hälsokontroll](insights/sql-assessment.md) | Utvärdera riskerna och hälso tillståndet i dina SQL Servers miljöer.  |
| [Wire Data](insights/wire-data.md) | Sammanställd nätverks-och prestanda data som samlas in från Windows-anslutna och Linux-anslutna datorer med Log Analytics-agenten. |

## <a name="third-party-integration"></a>Integration från tredje part

| Lösning | Beskrivning |
|:---|:---|
| [ITSM](alerts/itsmc-overview.md) | Med ITMS-anslutningsprogrammet (anslutningsprogram för hantering av IT-tjänster) kan du ansluta Azure och en ITSM-produkt/-tjänst (hantering av IT-tjänster (ITSM)) som stöds.  |


## <a name="resources-outside-of-azure"></a>Resurser utanför Azure
Azure Monitor kan samla in data från resurser utanför Azure med hjälp av metoderna som anges i följande tabell.

| Resurs | Metod |
|:---|:---|
| Program | Övervaka webb program utanför Azure med hjälp av Application Insights. Se [Vad är Application Insights?](./app/app-insights-overview.md). |
| Virtuella datorer | Använd agenter för att samla in data från gäst operativ systemet för virtuella datorer i andra moln miljöer eller lokalt. Se [Översikt över Azure Monitor agenter](agents/agents-overview.md). |
| REST API-klient | Separata API: er är tillgängliga för att skriva data till Azure Monitor loggar och mått från valfri REST API-klient. Se [Skicka loggdata till Azure monitor med API: t för HTTP-datainsamling](logs/data-collector-api.md) för loggar och [skicka anpassade mått för en Azure-resurs till Azure Monitor mått lagring med hjälp av en REST API](essentials/metrics-store-custom-rest-api.md) för mått. |



## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Monitor data plattform som lagrar loggar och mått som samlas in med insikter och lösningar](data-platform.md).
- Slutför en [själv studie kurs om att övervaka en Azure-resurs](essentials/tutorial-resource-logs.md).
- Slutför en [själv studie kurs om hur du skriver en logg fråga för att analysera data i Azure Monitor loggar](essentials/tutorial-resource-logs.md).
- Slutför en [själv studie kurs om hur du skapar ett mått diagram för att analysera data i Azure Monitor mått](essentials/tutorial-metrics-explorer.md).

