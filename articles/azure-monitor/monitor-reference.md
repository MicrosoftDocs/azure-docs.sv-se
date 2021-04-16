---
title: Vad övervakas av Azure Monitor
description: Referens för alla tjänster och andra resurser som övervakas av Azure Monitor.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 08/15/2020
ms.openlocfilehash: 513f262f5d09cf56c4506a4f20c9aa41507c2abd
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515285"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Vad övervakas av Azure Monitor?
I den här artikeln beskrivs de olika program och tjänster som övervakas av Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Insikter och kärnlösningar
Viktiga insikter och lösningar betraktas som en del Azure Monitor och följer support- och serviceavtalen för Azure. De stöds i alla Azure-regioner där Azure Monitor är tillgängliga.

### <a name="insights"></a>Insikter

Insikter ger en anpassad övervakningsupplevelse för specifika program och tjänster. De samlar in och analyserar både loggar och mått.

| Insight | Description |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Tjänsten Extensible Application Performance Management (APM) för att övervaka ditt live-webbprogram på valfri plattform. |
| [Containerinsikter](containers/container-insights-overview.md) | Övervakar prestanda för containerarbetsbelastningar som distribueras till antingen Azure Container Instances eller hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS). |
| [Azure Monitor för Cosmos DB](insights/cosmosdb-insights-overview.md) | Ger en vy över övergripande prestanda, fel, kapacitet och drifthälsa för alla dina Azure Cosmos DB i en enhetlig interaktiv upplevelse. |
| [Azure Monitor för nätverk (förhandsversion)](insights/network-insights-overview.md) | Ger en omfattande vy över hälsa och mått för alla dina nätverksresurser. Den avancerade sökfunktionen hjälper dig att identifiera resursberoenden, vilket möjliggör scenarier som att identifiera resurser som är värdar för din webbplats genom att helt enkelt söka efter namnet på din webbplats. |
[Azure Monitor för resursgrupper (förhandsversion)](insights/resource-group-insights.md) |  Att behandla och diagnostisera eventuella problem som dina enskilda resurser stöter på, samtidigt som det ger kontext vad gäller hälsotillstånd och prestanda för resursgruppen som helhet. |
| [Azure Monitor för Storage](insights/storage-insights-overview.md) | Ger omfattande övervakning av dina Azure Storage-konton genom att leverera en enhetlig vy över dina Azure Storage tjänsters prestanda, kapacitet och tillgänglighet. |
| [Insikter om virtuella datorer](vm/vminsights-overview.md) | Övervakar dina virtuella Azure-datorer (VM) och VM-skalningsuppsättningar i stor skala. Den analyserar prestanda och hälsotillstånd för dina virtuella datorer med Windows och Linux, och övervakar deras processer och beroenden på andra resurser och i externa processer. |
| [Azure Monitor för Key Vault (förhandsversion)](./insights/key-vault-insights-overview.md) | Ger omfattande övervakning av dina nyckelvalv genom att leverera en enhetlig vy över dina Key Vault, prestanda, fel och svarstider. |
| [Azure Monitor för Azure Cache for Redis (förhandsversion)](insights/redis-cache-insights-overview.md) |  Ger en enhetlig, interaktiv vy över övergripande prestanda, fel, kapacitet och drifthälsa. |


### <a name="core-solutions"></a>Kärnlösningar

Lösningarna baseras på loggfrågor och vyer som är anpassade för ett visst program eller en viss tjänst. De samlar bara in och analyserar loggar och blir inaktuella över tid till förmån för insikter.

| Lösning | Description |
|:---|:---|
| [Agenthälsa](insights/solution-agenthealth.md) | Analysera hälsotillståndet och konfigurationen för Log Analytics-agenter. |
| [Varningshantering](insights/alert-management-solution.md) | Analysera aviseringar som samlats in från System Center Operations Manager, Nagios eller Zabbix. |
| [Tjänstkarta](vm/service-map.md) | Identifierar automatiskt programkomponenter i Windows- och Linux-system och mappar kommunikationen mellan tjänster. |



## <a name="azure-services"></a>Azure-tjänster
I följande tabell visas Azure-tjänster och de data som de samlar in i Azure Monitor. 

- Mått – Tjänsten samlar automatiskt in mått Azure Monitor mått. 
- Loggar – Tjänsten stöder diagnostikinställningar som kan samla in plattformsloggar och mått för att Azure Monitor Loggar.
- Insikt – Det finns en insikt tillgänglig för tjänsten som ger en anpassad övervakningsupplevelse för tjänsten.

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
|Attestation Service | Inga | Inga | Inga |  |
|Automation | Ja | Ja | Inga |  |
|Azure Service Manager (RDFE) | Inga | Inga | Inga |  |
|Backup | Inga | Ja | Inga |  |
|Bastion | Inga | Inga | Inga |  |
|Batch | Ja | Ja | Inga |  |
|Batch AI | Inga | Inga | Inga |  |
|Blockchain Service | Inga | Ja | Inga |  |
|Skisser | Inga | Inga | Inga |  |
|Bot Service | Inga | Inga | Inga |  |
|Cloud Services | Ja | Ja | Inga | Agent som krävs för att övervaka gästoperativsystemet och arbetsflöden.  |
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
|Dps | Inga | Inga | Inga |  |
|Dynamics 365 Customer Engagement | Inga | Inga | Inga |  |
|Dynamics 365 Finance and Operations | Inga | Inga | Inga |  |
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
|Kommunikationsplattform för projektpool | Inga | Inga | Inga |  |
|Red Hat OpenShift | Inga | Inga | Inga |  |
|Redis Cache | Ja | Ja | [Ja](insights/redis-cache-insights-overview.md) | |
|Resource Graph | Inga | Inga | Inga |  |
|Resource Manager | Inga | Inga | Inga |  |
|Detaljhandelssökning – efter Bing | Inga | Inga | Inga |  |
|Sök | Ja | Ja | Inga |  |
|Service Bus | Ja | Ja | Inga |  |
|Service Fabric | Inga | Ja | Inga | Agent som krävs för att övervaka gästoperativsystemet och arbetsflöden.  |
|Registrering av portal | Inga | Inga | Inga |  |
|Site Recovery | Inga | Ja | Inga |  |
|Spring Cloud Service | Inga | Inga | Inga |  |
|Azure Synapse Analytics | Ja | Ja | Inga |  |
|SQL Database | Ja | Ja | Inga |  |
|SQL Server Stretch Database | Ja | Ja | Inga |  |
|Stack | Inga | Inga | Inga |  |
|Storage | Ja | Inga | [Ja](insights/storage-insights-overview.md) |  |
|Lagringscache | Inga | Inga | Inga |  |
|Tjänster för synkronisering av lagring | Inga | Inga | Inga |  |
|Stream Analytics | Ja | Ja | Inga |  |
|Time Series Insights | Ja | Ja | Inga |  |
|Tina | Inga | Inga | Inga |  |
|Traffic Manager | Ja | Ja | Inga |  |
|Universell utskrift | Inga | Inga | Inga |  |
|Virtual Machine Scale Sets | Inga | Ja | [Ja](vm/vminsights-overview.md) | Agent som krävs för att övervaka gästoperativsystemet och arbetsflöden. |
|Virtual Machines | Ja | Ja | [Ja](vm/vminsights-overview.md) | Agent som krävs för att övervaka gästoperativsystemet och arbetsflöden. |
|Virtual Network | Ja | Ja | [Ja](insights/network-insights-overview.md) |  |
|Virtual Network – NSG-flödesloggar | Inga | Ja | Inga |  |
|VPN Gateway | Ja | Ja | Inga |  |
|Windows Virtual Desktop | Inga | Ja | Inga |  |

## <a name="virtual-machine-agents"></a>Agenter för virtuella datorer
I följande tabell visas de agenter som kan samla in data från gästoperativsystemet på virtuella datorer och skicka data till Monitor. Varje agent kan samla in olika data och skicka dem till mått eller loggar i Azure Monitor. 

Se [Översikt över Azure Monitor för](agents/agents-overview.md) information om de data som varje agent kan samla in.

| Agent |  Mått | Loggar |
|:---|:---|:---|:---|
| [Azure Monitor agent (förhandsversion)](agents/azure-monitor-agent-overview.md) | Ja | Ja |
| [Log Analytics-agent](agents/log-analytics-agent.md) | Inga | Ja|
| [Diagnostiktillägg](agents/diagnostics-extension-overview.md) | Ja | Inga |
| [Telegraf-agent](essentials/collect-custom-metrics-linux-telegraf.md) | Ja | Inga |
| [Beroendeagent](vm/vminsights-enable-overview.md) | Inga | Ja |


## <a name="product-integrations"></a>Produktintegreringar
Tjänsterna och lösningarna i följande tabell lagrar sina data på en Log Analytics-arbetsyta så att de kan analyseras med andra loggdata som samlas in av Azure Monitor.

| Produkt/tjänst | Description |
|:---|:---|
| [Azure Automation](../automation/index.yml) | Hantera uppdateringar av operativsystemet och spåra ändringar på Windows- och Linux-datorer. Se [Ändringsspårning](../automation/change-tracking/overview.md) och [Uppdateringshantering](../automation/update-management/overview.md). |
| [Azure Information Protection ](/azure/information-protection/) | Klassificera och skydda dokument och e-postmeddelanden om du vill. Se [Central rapportering för Azure Information Protection](/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Azure Security Center](../security-center/index.yml) | Samla in och analysera säkerhetshändelser och utför hotanalys. Se [Datainsamling i Azure Security Center](../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../sentinel/index.yml) | Ansluter till olika källor, inklusive Office 365 och Amazon Web Services Cloud Trail. Se [Ansluta datakällor.](../sentinel/connect-data-sources.md) |
| [Microsoft Intune](/intune/) | Skapa en diagnostikinställning för att skicka loggar till Azure Monitor. Se [Skicka loggdata till lagring, händelsehubbb eller logganalys i Intune (förhandsversion)](/intune/fundamentals/review-logs-using-azure-monitor).  |
| Nätverk  | [Övervakare av nätverksprestanda](insights/network-performance-monitor.md) – Övervaka nätverksanslutning och prestanda till tjänst- och programslutpunkter.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics) – Analysera loggar och mått från Azure Application Gateway.<br>[Trafikanalys](../network-watcher/traffic-analytics.md) – analyserar Network Watcher NSG-flödesloggar (Network Security Group) för att ge insikter om trafikflödet i ditt Azure-moln. |
| [Office 365](insights/solution-office-365.md) | Övervaka din Office 365-miljö. Uppdaterad version med förbättrad registrering tillgänglig via Azure Sentinel. |
| [SQL-analys](insights/azure-sql.md) | Övervaka prestanda för Azure SQL databaser och SQL Managed Instances i stor skala och över flera prenumerationer. |
| [Surface Hub](insights/surface-hubs.md) | Spåra hälsotillstånd och användning av Surface Hub enheter. |
| [System Center Operations Manager](/system-center/scom) | Samla in data Operations Manager agenterna genom att ansluta hanteringsgruppen till Azure Monitor. Se [Ansluta Operations Manager till Azure Monitor](agents/om-agents.md)<br> Utvärdera risken och hälsotillståndet för din System Center Operations Manager med hjälp av [Operations Manager Assessment.](insights/scom-assessment.md) |
| [Microsoft Teams-rum](/microsoftteams/room-systems/azure-monitor-deploy) | Integrerad hantering från hela microsoft Teams-rumsenheter. |
| [Visual Studio App Center](/appcenter/) | Skapa, testa och distribuera program och övervaka deras status och användning. Se [Börja analysera mobilappen med hjälp App Center och Application Insights](app/mobile-center-quickstart.md). |
| Windows | [Windows Update – Utvärdera](/windows/deployment/update/update-compliance-get-started) dina Windows-skrivbordsuppgraderingar.<br>[Desktop Analytics](/configmgr/desktop-analytics/overview) – integrerar med Konfigurationshanteraren för att ge insikter och information för att fatta mer välgrundade beslut om Windows-klienters uppdateringsberedskap. |



## <a name="other-solutions"></a>Andra lösningar
Andra lösningar är tillgängliga för övervakning av olika program och tjänster, men aktiv utveckling har stoppats och de kanske inte är tillgängliga i alla regioner. De omfattas av servicenivåavtalet för datainmatning i Azure Log Analytics.

| Lösning | Description |
|:---|:---|
| [Active Directory-hälsokontroll](insights/ad-assessment.md) | Utvärdera riskerna och hälsotillståndet för dina Active Directory-miljöer. |
| [Active Directory-replikeringsstatus](insights/ad-replication-status.md) | Övervakar regelbundet Active Directory-miljön för eventuella replikeringsfel. |
| [Aktivitetslogganalys](essentials/activity-log.md#activity-log-analytics-monitoring-solution) | Visa aktivitetsloggposter. |
| [DNS-analys (förhandsversion)](insights/dns-analytics.md) | Samlar in, analyserar och korrelerar Windows DNS-analys- och granskningsloggar och andra relaterade data från DNS-servrarna. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Samla in, visa och analysera Cloud Foundry för systemets hälsotillstånd och prestanda över flera distributioner. |
| [Containrar](containers/containers.md) | Visa och hantera Docker- och Windows-containervärdar. |
| [Utvärderingar på begäran](/services-hub/health/getting_started_with_on_demand_assessments) | Utvärdera och optimera tillgänglighet, säkerhet och prestanda för dina lokala, hybridbaserade och molnbaserade Microsoft-teknikmiljöer. |
| [SQL-hälsokontroll](insights/sql-assessment.md) | Utvärdera riskerna och hälsotillståndet för dina SQL Server miljöer.  |
| [Wire Data](insights/wire-data.md) | Konsoliderade nätverks- och prestandadata som samlats in från Windows-anslutna och Linux-anslutna datorer med Log Analytics-agenten. |

## <a name="third-party-integration"></a>Integrering från tredje part

| Lösning | Description |
|:---|:---|
| [ITSM](alerts/itsmc-overview.md) | Med ITMS-anslutningsprogrammet (anslutningsprogram för hantering av IT-tjänster) kan du ansluta Azure och en ITSM-produkt/-tjänst (hantering av IT-tjänster (ITSM)) som stöds.  |


## <a name="resources-outside-of-azure"></a>Resurser utanför Azure
Azure Monitor kan samla in data från resurser utanför Azure med hjälp av metoderna i följande tabell.

| Resurs | Metod |
|:---|:---|
| Program | Övervaka webbprogram utanför Azure med hjälp av Application Insights. Se [Vad är Application Insights?](./app/app-insights-overview.md). |
| Virtuella datorer | Använd agenter för att samla in data från gästoperativsystemet på virtuella datorer i andra molnmiljöer eller lokalt. Se [Översikt över Azure Monitor agenter.](agents/agents-overview.md) |
| REST API Client | Separata API:er är tillgängliga för att skriva data Azure Monitor loggar och mått från valfri REST API klient. Se [Skicka loggdata till Azure Monitor](logs/data-collector-api.md) med HTTP Data Collector API för loggar och Skicka anpassade mått för en Azure-resurs till [Azure Monitor-måttlagret](essentials/metrics-store-custom-rest-api.md) med hjälp av en REST API för mått. |



## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Monitor dataplattform som lagrar loggar och mått som samlas in av insikter och lösningar.](data-platform.md)
- Slutför en [självstudie om övervakning av en Azure-resurs](essentials/tutorial-resource-logs.md).
- Slutför en [självstudie om att skriva en loggfråga för att analysera data i Azure Monitor Logs](essentials/tutorial-resource-logs.md).
- Slutför en [självstudie om hur du skapar ett måttdiagram för att analysera data Azure Monitor mått](essentials/tutorial-metrics-explorer.md).

