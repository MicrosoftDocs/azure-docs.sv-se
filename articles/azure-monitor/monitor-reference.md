---
title: Vad övervakas av Azure Monitor
description: Referens för alla tjänster och andra resurser som övervakas av Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 08/15/2020
ms.openlocfilehash: 153b153399bd430b4688d4790a3dd5785c545012
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579390"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Vad övervakas av Azure Monitor?
I den här artikeln beskrivs de olika program och tjänster som övervakas av Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Insikter och huvud lösningar
Grundläggande insikter och lösningar betraktas som en del av Azure Monitor och följer support-och service nivå avtalen för Azure. De stöds i alla Azure-regioner där Azure Monitor är tillgängligt.

### <a name="insights"></a>Insikter

Insikter ger en anpassad övervaknings miljö för specifika program och tjänster. De samlar in och analyserar både loggar och mått.

| Insight | Description |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Utöknings bar APM-tjänst (Application Performance Management) för att övervaka ditt Live-webbprogram på valfri plattform. |
| [Azure Monitor för behållare](containers/container-insights-overview.md) | Övervakar prestanda för behållar arbets belastningar som distribueras till antingen Azure Container Instances eller hanterade Kubernetes-kluster som finns i Azure Kubernetes service (AKS). |
| [Azure Monitor för Cosmos DB](insights/cosmosdb-insights-overview.md) | Innehåller en översikt över övergripande prestanda, fel, kapacitet och drifts hälsa för alla dina Azure Cosmos DB resurser i en enhetlig interaktiv upplevelse. |
| [Azure Monitor för nätverk (för hands version)](insights/network-insights-overview.md) | Innehåller en omfattande vy över hälso tillstånd och mått för alla nätverks resurser. Med den avancerade Sök funktionen kan du identifiera resurs beroenden, aktivera scenarier som att identifiera resurser som är värdar för din webbplats genom att helt enkelt söka efter namnet på din webbplats. |
[Azure Monitor för resurs grupper (förhands granskning)](insights/resource-group-insights.md) |  Prioritering och diagnostisera eventuella problem som kan uppstå för enskilda resurser, samtidigt som den erbjuder kontexten och prestandan för resurs gruppen som helhet. |
| [Azure Monitor för lagring](insights/storage-insights-overview.md) | Tillhandahåller omfattande övervakning av dina Azure Storage konton genom att leverera en enhetlig vy över dina Azure Storages prestanda, kapacitet och tillgänglighet. |
| [Azure Monitor för virtuella datorer](vm/vminsights-overview.md) | Övervakar dina virtuella Azure-datorer (VM) och skalnings uppsättningar för virtuella datorer i stor skala. Den analyserar prestanda och hälsotillstånd för dina virtuella datorer med Windows och Linux, och övervakar deras processer och beroenden på andra resurser och i externa processer. |
| [Azure Monitor för Key Vault (förhands granskning)](./insights/key-vault-insights-overview.md) | Tillhandahåller omfattande övervakning av dina nyckel valv genom att leverera en enhetlig vy över dina Key Vault begär Anden, prestanda, haverier och svars tider. |
| [Azure Monitor för Azure cache för Redis (för hands version)](insights/redis-cache-insights-overview.md) |  Ger en enhetlig, interaktiv vy över övergripande prestanda, fel, kapacitet och drifts hälsa. |


### <a name="core-solutions"></a>Kärn lösningar

Lösningar baseras på logg frågor och vyer som är anpassade för ett visst program eller en viss tjänst. De samlar in och analyserar bara loggar och är föråldrade med tiden för insikter.

| Lösning | Description |
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
|Active Directory B2C | Nej | Nej | Nej |  |
|Active Directory Domain Services | Nej | Ja | Nej |  |
|Aktivitetslogg | Nej | Ja | Nej | |
|Advanced Threat Protection | Nej | Nej | Nej |  |
|Advisor | Nej | Nej | Nej |  |
|AI Builder | Nej | Nej | Nej |  |
|Analysis Services | Ja | Ja | Nej |  |
|API för FHIR | Nej | Nej | Nej |  |
|API Management | Ja | Ja | Nej |  |
|App Service | Ja | Ja | Nej |  |
|AppConfig | Nej | Nej | Nej |  |
|Application Gateway | Ja | Ja | Nej |  |
|Attesterings tjänst | Nej | Nej | Nej |  |
|Automation | Ja | Ja | Nej |  |
|Azure Service Manager (RDFE) | Nej | Nej | Nej |  |
|Backup | Nej | Ja | Nej |  |
|Bastion | Nej | Nej | Nej |  |
|Batch | Ja | Ja | Nej |  |
|Batch AI | Nej | Nej | Nej |  |
|Blockchain Service | Nej | Ja | Nej |  |
|Skisser | Nej | Nej | Nej |  |
|Bot Service | Nej | Nej | Nej |  |
|Cloud Services | Ja | Ja | Nej | Agent som krävs för att övervaka gäst operativ system och arbets flöden.  |
|Cloud Shell | Nej | Nej | Nej |  |
|Cognitive Services | Ja | Ja | Nej |  |
|Container Instances | Ja | Nej | Nej |  |
|Container Registry | Ja | Ja | Nej |  |
|Innehållsleverantörsnätverk (CDN) | Nej | Ja | Nej |  |
|Cosmos DB | Ja | Ja | [Ja](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | Nej | Nej | Nej |  |
|Data Box | Nej | Nej | Nej |  |
|Data Catalog Gen2 | Nej | Nej | Nej |  |
|Data Explorer | Ja | Ja | Nej |  |
|Data Factory | Ja | Ja | Nej |  |
|Data Factory v2 | Nej | Ja | Nej |  |
|Data Share | Nej | Nej | Nej |  |
|Database for MariaDB | Ja | Ja | Nej |  |
|Database for MySQL | Ja | Ja | Nej |  |
|Database for PostgreSQL | Ja | Ja | Nej |  |
|Database Migration Service | Nej | Nej | Nej |  |
|Databricks | Nej | Ja | Nej |  |
|DDoS Protection | Ja | Ja | Nej |  |
|DevOps | Nej | Nej | Nej |  |
|DNS | Ja | Nej | Nej |  |
|Domännamn | Nej | Nej | Nej |  |
|– | Nej | Nej | Nej |  |
|Dynamics 365 kund engagemang | Nej | Nej | Nej |  |
|Dynamics 365-ekonomi och-åtgärder | Nej | Nej | Nej |  |
|Event Grid | Ja | Nej | Nej |  |
|Event Hubs | Ja | Ja | Nej |  |
|ExpressRoute | Ja | Ja | Nej |  |
|Brandvägg | Ja | Ja | Nej |  |
|Front Door | Ja | Ja | Nej |  |
|Functions | Ja | Ja | Nej |  |
|HDInsight | Nej | Ja | Nej |  |
|HPC Cache | Nej | Nej | Nej |  |
|Information Protection | Nej | Ja | Nej |  |
|Intune | Nej | Ja | Nej |  |
|IoT Central | Nej | Nej | Nej |  |
|IoT Hub | Ja | Ja | Nej |  |
|Key Vault | Ja | Ja | [Ja](./insights/key-vault-insights-overview.md) |  |
|Kubernetes Service (AKS) | Nej | Nej | [Ja](containers/container-insights-overview.md)  |  |
|Load Balancer | Ja | Nej | Nej |  |
|Logic Apps | Ja | Ja | Nej |  |
|Machine Learning Service | Nej | Nej | Nej |  |
|Managed Applications  | Nej | Nej | Nej |  |
|Maps  | Nej | Nej | Nej |  |
|Media Services | Ja | Ja | Nej |  |
|Microsoft Hanterat skrivbord | Nej | Nej | Nej |  |
|Microsoft PowerApps | Nej | Nej | Nej |  |
|Microsoft Social Engagement | Nej | Nej | Nej |  |
|Microsoft Stream | Ja | Ja | Nej |  |
|Migrera | Nej | Nej | Nej |  |
|Multi-Factor Authentication | Nej | Ja | Nej |  |
|Network Watcher | Ja | Ja | Nej |  |
|Notification Hubs | Ja | Nej | Nej |  |
|Open Datasets | Nej | Nej | Nej |  |
|Policy | Nej | Nej | Nej |  |
|Power Automate | Nej | Nej | Nej |  |
|Power BI Embedded | Ja | Ja | Nej |  |
|Private Link | Nej | Nej | Nej |  |
|Kommunikations plattform för bufferthanterare | Nej | Nej | Nej |  |
|Red Hat OpenShift | Nej | Nej | Nej |  |
|Redis Cache | Ja | Ja | [Ja](insights/redis-cache-insights-overview.md) | |
|Resource Graph | Nej | Nej | Nej |  |
|Resource Manager | Nej | Nej | Nej |  |
|Butiks sökning – efter Bing | Nej | Nej | Nej |  |
|Sök | Ja | Ja | Nej |  |
|Service Bus | Ja | Ja | Nej |  |
|Service Fabric | Nej | Ja | Nej | Agent som krävs för att övervaka gäst operativ system och arbets flöden.  |
|Registrerings Portal | Nej | Nej | Nej |  |
|Site Recovery | Nej | Ja | Nej |  |
|Vår moln tjänst | Nej | Nej | Nej |  |
|Azure Synapse Analytics | Ja | Ja | Nej |  |
|SQL Database | Ja | Ja | Nej |  |
|SQL Server Stretch Database | Ja | Ja | Nej |  |
|Stack | Nej | Nej | Nej |  |
|Storage | Ja | Nej | [Ja](insights/storage-insights-overview.md) |  |
|Storage cache | Nej | Nej | Nej |  |
|Tjänster för synkronisering av lagring | Nej | Nej | Nej |  |
|Stream Analytics | Ja | Ja | Nej |  |
|Time Series Insights | Ja | Ja | Nej |  |
|TINA | Nej | Nej | Nej |  |
|Traffic Manager | Ja | Ja | Nej |  |
|Universell utskrift | Nej | Nej | Nej |  |
|Virtual Machine Scale Sets | Nej | Ja | [Ja](vm/vminsights-overview.md) | Agent som krävs för att övervaka gäst operativ system och arbets flöden. |
|Virtual Machines | Ja | Ja | [Ja](vm/vminsights-overview.md) | Agent som krävs för att övervaka gäst operativ system och arbets flöden. |
|Virtual Network | Ja | Ja | [Ja](insights/network-insights-overview.md) |  |
|Virtual Network NSG flödes loggar | Nej | Ja | Nej |  |
|VPN Gateway | Ja | Ja | Nej |  |
|Windows Virtual Desktop | Nej | Nej | Nej |  |

## <a name="virtual-machine-agents"></a>Agenter för virtuella datorer
I följande tabell visas de agenter som kan samla in data från gäst operativ systemet på virtuella datorer och skicka data som ska övervakas. Varje agent kan samla in olika data och skicka dem till antingen mått eller loggar i Azure Monitor. 

Se [Översikt över Azure Monitor agenter](agents/agents-overview.md) för information om de data som varje agent kan samla in.

| Agent |  Mått | Loggar |
|:---|:---|:---|:---|
| [Azure Monitor Agent (förhands granskning)](agents/azure-monitor-agent-overview.md) | Ja | Ja |
| [Log Analytics-agent](agents/log-analytics-agent.md) | Nej | Ja|
| [Diagnostiskt tillägg](agents/diagnostics-extension-overview.md) | Ja | Nej |
| [Teleympkvistar-agent](essentials/collect-custom-metrics-linux-telegraf.md) | Ja | Nej |
| [Beroendeagent](vm/vminsights-enable-overview.md) | Nej | Ja |


## <a name="product-integrations"></a>Produkt integreringar
Tjänsterna och lösningarna i följande tabell lagrar sina data på en Log Analytics arbets yta så att den kan analyseras med andra loggdata som samlas in av Azure Monitor.

| Produkt/tjänst | Description |
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

| Lösning | Description |
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

| Lösning | Description |
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

- Läs mer om [Azure Monitor data plattform som lagrar loggar och mått som samlas in med insikter och lösningar](/data-platform.md).
- Slutför en [själv studie kurs om att övervaka en Azure-resurs](essentials/tutorial-resource-logs.md).
- Slutför en [själv studie kurs om hur du skriver en logg fråga för att analysera data i Azure Monitor loggar](essentials/tutorial-resource-logs.md).
- Slutför en [själv studie kurs om hur du skapar ett mått diagram för att analysera data i Azure Monitor mått](essentials/tutorial-metrics-explorer.md).

