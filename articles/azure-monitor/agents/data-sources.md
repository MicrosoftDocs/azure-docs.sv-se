---
title: Data källor i Azure Monitor | Microsoft Docs
description: Beskriver de data som är tillgängliga för övervakning av hälso tillstånd och prestanda för dina Azure-resurser och de program som körs på dem.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/06/2020
ms.openlocfilehash: 537163b4a78954443eeb7799dc624837ba03e2bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036630"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Källor för övervaknings data för Azure Monitor
Azure Monitor baseras på en [gemensam övervaknings data plattform](../data-platform.md) som innehåller [loggar](../logs/data-platform-logs.md) och [mått](../essentials/data-platform-metrics.md). Genom att samla in data till den här plattformen kan data från flera resurser analyseras tillsammans med en gemensam uppsättning verktyg i Azure Monitor. Övervaknings data kan också skickas till andra platser för att stödja vissa scenarier och vissa resurser kan skriva till andra platser innan de kan samlas in i loggar eller mått.

I den här artikeln beskrivs de olika källorna till övervaknings data som samlas in av Azure Monitor utöver de övervaknings data som skapats av Azure-resurser. Det finns länkar till detaljerad information om konfigurationen som krävs för att samla in dessa data till olika platser.

## <a name="application-tiers"></a>Programnivåer

Källor för övervaknings data från Azure-program kan ordnas i nivåer, de högsta nivåerna är själva programmet och de lägre nivåerna är komponenter i Azure-plattformen. Metoden för att komma åt data från varje nivå varierar. Program nivåerna sammanfattas i tabellen nedan och källorna till övervaknings data på varje nivå visas i följande avsnitt. Se [övervaka data platser i Azure](../monitor-reference.md) för en beskrivning av varje data plats och hur du kan komma åt data.


![Övervaknings nivåer](../media/overview/overview.png)


### <a name="azure"></a>Azure
I följande tabell beskrivs kortfattat de program nivåer som är specifika för Azure. Följ länken för mer information om var och en i avsnitten nedan.

| Nivå | Beskrivning | Samlings metod |
|:---|:---|:---|
| [Azure-klient](#azure-tenant) | Information om statusen för Azure-tjänster på klientorganisationsnivå, till exempel Azure Active Directory. | Visa AAD-data i portalen eller konfigurera samlingen för att Azure Monitor med hjälp av en klient diagnos inställning. |
| [Azure-prenumeration](#azure-subscription) | Data som rör hälsan och hantering av Cross-Resource-tjänster i din Azure-prenumeration, till exempel Resource Manager och Service Health. | Visa i portalen eller konfigurera samlingen för att Azure Monitor med hjälp av en logg profil. |
| [Azure-resurser](#azure-resources) |  Data om drift och prestanda för varje Azure-resurs. | Mått som samlas in automatiskt, visas i Metrics Explorer.<br>Konfigurera diagnostikinställningar för att samla in loggar i Azure Monitor.<br>Övervaknings lösningar och insikter tillgängliga för mer detaljerad övervakning av särskilda resurs typer. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, annat moln eller lokalt 
I följande tabell beskrivs kortfattat de program nivåer som kan finnas i Azure, ett annat moln eller lokalt. Följ länken för mer information om var och en i avsnitten nedan.

| Nivå | Beskrivning | Samlings metod |
|:---|:---|:---|
| [Operativ system (gäst)](#operating-system-guest) | Data om operativ systemet på beräknings resurser. | Installera Log Analytics agent för att samla in klient data källor i Azure Monitor och beroende agent för att samla in beroenden som stöder VM-insikter.<br>För virtuella Azure-datorer installerar du Azure Diagnostic-tillägget för att samla in loggar och statistik i Azure Monitor. |
| [Program kod](#application-code) | Data om prestanda och funktionalitet för det faktiska programmet och koden, inklusive prestanda spårning, program loggar och telemetri för användare. | Instrumentera din kod för att samla in data i Application Insights. |
| [Anpassade källor](#custom-sources) | Data från externa tjänster eller andra komponenter eller enheter. | Samla in logg-eller mått data i Azure Monitor från alla REST-klienter. |

## <a name="azure-tenant"></a>Azure-klientorganisation
Telemetri som är relaterade till din Azure-klient samlas in från klient-wide-tjänster som Azure Active Directory.

![Azure-klient samling](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory gransknings loggar
[Azure Active Directory rapportering](../../active-directory/reports-monitoring/overview-reports.md) innehåller historiken för inloggnings aktivitet och gransknings loggen för ändringar som gjorts inom en viss klient. 

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Konfigurera Azure AD-loggar som ska samlas in i Azure Monitor för att analysera dem med andra övervaknings data. | [Integrera Azure AD-loggar med Azure Monitor loggar (förhands granskning)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Exportera Azure AD-loggar till Azure Storage för arkivering. | [Självstudie: Arkivera Azure AD-loggar till ett Azure-lagringskonto (förhandsversion)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Händelsehubb | Strömma Azure AD-loggar till andra platser med hjälp av Event Hubs. | [Självstudie: strömma Azure Active Directory loggar till en Azure Event Hub (för hands version)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Azure-prenumeration
Telemetri som rör hälsan och driften av din Azure-prenumeration.

![Azure-prenumeration](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure-aktivitetslogg 
[Azure aktivitets loggen](../essentials/platform-logs-overview.md) innehåller tjänst hälso poster tillsammans med poster om eventuella konfigurations ändringar som gjorts i resurserna i din Azure-prenumeration. Aktivitets loggen är tillgänglig för alla Azure-resurser och representerar deras _externa_ vy.

| Mål | Beskrivning | Referens |
|:---|:---|
| Aktivitetslogg | Aktivitets loggen samlas in i ett eget data lager som du kan visa från Azure Monitor-menyn eller använda för att skapa aktivitets logg aviseringar. | [Fråga aktivitets loggen i Azure Portal](../essentials/activity-log.md#view-the-activity-log) |
| Azure Monitor-loggar | Konfigurera Azure Monitor loggar för att samla in aktivitets loggen för att analysera den med andra övervaknings data. | [Samla in och analysera Azure aktivitets loggar i Log Analytics arbets yta i Azure Monitor](../essentials/activity-log.md) |
| Azure Storage | Exportera aktivitets loggen till Azure Storage för arkivering. | [Arkivera aktivitets logg](../essentials/resource-logs.md#send-to-azure-storage)  |
| Event Hubs | Strömma aktivitets loggen till andra platser med hjälp av Event Hubs | [Strömma aktivitets logg till händelsehubben](../essentials/resource-logs.md#send-to-azure-event-hubs). |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md) ger information om hälso tillståndet för de Azure-tjänster i din prenumeration som ditt program och dina resurser förlitar sig på.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Aktivitetslogg<br>Azure Monitor-loggar | Service Health poster lagras i Azure aktivitets loggen så att du kan visa dem i Azure Portal eller utföra andra aktiviteter som du kan utföra med aktivitets loggen. | [Visa aviseringar om hälsotillståndet för tjänsten med hjälp av Azure-portalen](../../service-health/service-notifications.md) |


## <a name="azure-resources"></a>Azure-resurser
Mått och resurs loggar innehåller information om den _interna_ driften av Azure-resurser. De är tillgängliga för de flesta Azure-tjänster och övervaknings lösningar och insikter samlar in ytterligare data för specifika tjänster.

![Azure-resurs samling](media/data-sources/data-source-azure-resources.svg)


### <a name="platform-metrics"></a>Plattformsmått 
De flesta Azure-tjänster skickar [plattforms mått](../essentials/data-platform-metrics.md) som återspeglar deras prestanda och drift direkt till mått databasen. De angivna [måtten varierar för varje typ av resurs](../essentials/metrics-supported.md). 

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-statistik | Plattforms mått skrivs till Azure Monitor Metrics-databasen utan konfiguration. Få åtkomst till plattforms mått från Metrics Explorer.  | [Komma igång med Azure Metrics Explorer](../essentials/metrics-getting-started.md)<br>[Mått som stöds med Azure Monitor](../essentials/metrics-supported.md) |
| Azure Monitor-loggar | Kopiera plattforms mått till loggar för utveckling och annan analys med hjälp av Log Analytics. | [Azure Diagnostics Direct till Log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace) |
| Event Hubs | Strömma mått till andra platser med hjälp av Event Hubs. |[Strömma Azure övervaknings data till en Event Hub för användning av ett externt verktyg](../essentials/stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Resursloggar
[Resurs loggar](../essentials/platform-logs-overview.md) ger insikter om den _interna_ åtgärden för en Azure-resurs.  Resurs loggar skapas automatiskt, men du måste skapa en diagnostisk inställning för att ange ett mål för dem som ska samlas in för varje resurs.

Konfigurations kraven och innehållet i resurs loggar varierar beroende på resurs typ och inte alla tjänster. Se [tjänster, scheman och kategorier som stöds för Azures resurs loggar](../essentials/resource-logs-schema.md) för information om varje tjänst och länkar till detaljerade konfigurations procedurer. Om tjänsten inte finns med i listan i den här artikeln skapar tjänsten för närvarande inte resurs loggar.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Skicka resurs loggar till Azure Monitor loggar för analys med andra insamlade logg data. | [Samla in Azures resurs loggar i Log Analytics arbets yta i Azure Monitor](../essentials/resource-logs.md#send-to-azure-storage) |
| Storage | Skicka resurs loggar till Azure Storage för arkivering. | [Arkivera Azures resurs loggar](../essentials/resource-logs.md#send-to-log-analytics-workspace) |
| Event Hubs | Strömma resurs loggar till andra platser med hjälp av Event Hubs. |[Strömma Azure-resurs loggar till en Event Hub](../essentials/resource-logs.md#send-to-azure-event-hubs) |

## <a name="operating-system-guest"></a>Operativ system (gäst)
Beräknings resurser i Azure, i andra moln och lokalt har ett gäst operativ system att övervaka. Med installationen av en eller flera agenter kan du samla in telemetri från gästen i Azure Monitor för att analysera den med samma övervaknings verktyg som själva Azure-tjänsterna.

![Azure Compute Resource-samling](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure Diagnostic-tillägg
Genom att aktivera Azure-diagnostik-tillägget för virtuella Azure-datorer kan du samla in loggar och mått från gäst operativ systemet i Azure Compute-resurser, inklusive Azure Cloud Service (klassisk) webb-och arbets roller, Virtual Machines, skalnings uppsättningar för virtuella datorer och Service Fabric.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Storage | Azure Diagnostics-tillägget skriver alltid till ett Azure Storage-konto. | [Installera och konfigurera Windows Azure Diagnostics-tillägget (WAD)](./diagnostics-extension-windows-install.md)<br>[Använda Linux-diagnostiktillägget för att övervaka mått och loggar](../../virtual-machines/extensions/diagnostics-linux.md) |
| Azure Monitor-statistik | När du konfigurerar Diagnostics-tillägget för att samla in prestanda räknare skrivs de till Azure Monitor Metrics-databasen. | [Skicka gäst operativ systemets mått till Azure Monitor Metric Store med hjälp av en Resource Manager-mall för en virtuell Windows-dator](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) |
| Event Hubs | Konfigurera diagnostik-tillägget för att strömma data till andra platser med hjälp av Event Hubs.  | [Strömma Azure-diagnostik data med hjälp av Event Hubs](./diagnostics-extension-stream-event-hubs.md)<br>[Använda Linux-diagnostiktillägget för att övervaka mått och loggar](../../virtual-machines/extensions/diagnostics-linux.md) |
| Application Insights loggar | Samla in loggar och prestanda räknare från beräknings resursen som stöder ditt program så att det kan analyseras med andra program data. | [Skicka data från moln tjänst, virtuell dator eller Service Fabric diagnostikdata till Application Insights](./diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Log Analytics-agent 
Installera Log Analytics agent för omfattande övervakning och hantering av dina virtuella Windows-eller Linux-datorer. Den virtuella datorn kan köras i Azure, ett annat moln eller lokalt.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Log Analytics-agenten ansluter till Azure Monitor antingen direkt eller via System Center Operations Manager och gör att du kan samla in data från data källor som du konfigurerar eller från att övervaka lösningar som ger ytterligare insikter om program som körs på den virtuella datorn. | [Agent data källor i Azure Monitor](../agents/agent-data-sources.md)<br>[Anslut Operations Manager till Azure Monitor](./om-agents.md) |
| VM-lagring | VM Insights använder Log Analytics agent för att lagra information om hälso-tillstånd på en anpassad plats. Mer information finns i nästa avsnitt.  |


### <a name="vm-insights"></a>VM-insikter 
[VM Insights](../vm/vminsights-overview.md) är en anpassad övervaknings upplevelse för virtuella datorer som tillhandahåller funktioner utöver Core Azure Monitor-funktioner. Det krävs en Dependency Agent på virtuella Windows-och Linux-datorer som integreras med Log Analytics-agenten för att samla in identifierade data om processer som körs på den virtuella datorn och externa process beroenden.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Lagrar data om processer och beroenden på agenten. | [Använda kartan för VM Insights (för hands version) för att förstå program komponenter](../vm/vminsights-maps.md) |



## <a name="application-code"></a>Program kod
Detaljerad program övervakning i Azure Monitor görs med [Application Insights](/azure/application-insights/) som samlar in data från program som körs på en mängd olika plattformar. Programmet kan köras i Azure, ett annat moln eller lokalt.

![Insamling av program data](media/data-sources/applications.png)


### <a name="application-data"></a>Programdata
När du aktiverar Application Insights för ett program genom att installera ett Instrumentation-paket samlar det in statistik och loggar som rör programmets prestanda och funktion. Application Insights lagrar de data som samlas in i samma Azure Monitor data plattform som används av andra data källor. Den innehåller omfattande verktyg för analys av dessa data, men du kan också analysera dem med data från andra källor med hjälp av verktyg som Metrics Explorer och Log Analytics.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Användnings data om ditt program, till exempel sidvyer, program begär Anden, undantag och spår. | [Analysera loggdata i Azure Monitor](../logs/log-query-overview.md) |
|                    | Beroende information mellan program komponenter för att stödja program mappning och telemetri korrelation. | [Telemetri korrelation i Application Insights](../app/correlation.md) <br> [Programkarta](../app/app-map.md) |
|            | Resultat av tillgänglighets test som testar tillgänglighet och svars tider för ditt program från olika platser på det offentliga Internet. | [Övervaka tillgänglighet och svarstider på valfri webbplats](../app/monitor-web-app-availability.md) |
| Azure Monitor-statistik | Application Insights samlar in Mät värden som beskriver programmets prestanda och funktion förutom anpassade mått som du definierar i ditt program i Azure Monitor Metrics-databasen. | [Loggbaserade och föraggregerade mått i Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[API för Application Insights för anpassade händelser och mått](../app/api-custom-events-metrics.md) |
| Azure Storage | Skicka program data till Azure Storage för arkivering. | [Exportera telemetri från Application Insights](../app/export-telemetry.md) |
|            | Information om tillgänglighets test lagras i Azure Storage. Använd Application Insights i Azure Portal för att ladda ned för lokal analys. Resultaten av tillgänglighets test lagras i Azure Monitor loggar. | [Övervaka tillgänglighet och svarstider på valfri webbplats](../app/monitor-web-app-availability.md) |
|            | Spårnings data för profiler lagras i Azure Storage. Använd Application Insights i Azure Portal för att ladda ned för lokal analys.  | [Profilera produktions program i Azure med Application Insights](../app/profiler-overview.md) 
|            | Fel sökning av ögonblicks bild data som samlas in för en delmängd av undantag lagras i Azure Storage. Använd Application Insights i Azure Portal för att ladda ned för lokal analys.  | [Hur ögonblicks bilder fungerar](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Övervaknings lösningar och insikter
[Övervaknings lösningar](../insights/solutions.md) och [insikter](../monitor-reference.md) samlar in data för att ge ytterligare insikter om driften av en viss tjänst eller ett visst program. De kan rikta in sig på resurser på olika program nivåer och till och med flera nivåer.

### <a name="monitoring-solutions"></a>Övervakningslösningar

| Mål | Beskrivning | Referens
|:---|:---|:---|
| Azure Monitor-loggar | Övervaknings lösningar samlar in data i Azure Monitor loggar där de kan analyseras med hjälp av frågespråket eller [vyer](../visualize/view-designer.md) som vanligt vis ingår i lösningen. | [Information om data insamling för övervakning av lösningar i Azure](../monitor-reference.md) |


### <a name="container-insights"></a>Container Insights
[Behållar insikter](../containers/container-insights-overview.md) ger en anpassad övervaknings upplevelse för [Azure KUBERNETES service (AKS)](../../aks/index.yml). Den samlar in ytterligare data om de här resurserna som beskrivs i följande tabell.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Lagrar övervaknings data för AKS, inklusive inventering, loggar och händelser. Mått data lagras också i loggar för att utnyttja dess analys funktioner i portalen. | [Förstå AKS-kluster prestanda med behållar insikter](../containers/container-insights-analyze.md) |
| Azure Monitor-statistik | Mått data lagras i mått databasen för att driva visualisering och aviseringar. | [Visa behållar mått i Metrics Explorer](../containers/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Ger direkt åtkomst till dina AKS-Kubernetes (Azure Service), händelser och Pod mått i portalen. | [Så här visar du Kubernetes-loggar, händelser och Pod-mått i real tid ](../containers/container-insights-livedata-overview.md) |

### <a name="vm-insights"></a>VM-insikter
[VM Insights](../vm/vminsights-overview.md) är en anpassad upplevelse för övervakning av virtuella datorer. En beskrivning av de data som samlas in av VM Insights finns i avsnittet [operativ system (gäst)](#operating-system-guest) ovan.

## <a name="custom-sources"></a>Anpassade källor
Förutom standard nivåerna för ett program kan du behöva övervaka andra resurser som har telemetri som inte kan samlas in med andra data källor. För dessa resurser skriver du dessa data till antingen mått eller loggar med hjälp av en Azure Monitor-API.

![Anpassad samling](media/data-sources/custom.png)

| Mål | Metod | Beskrivning | Referens |
|:---|:---|:---|:---|
| Azure Monitor-loggar | API för datainsamling | Samla in loggdata från alla REST-klienter och lagra dem på Log Analytics arbets ytan. | [Skicka loggdata till Azure Monitor med API: t för HTTP-datainsamling (offentlig för hands version)](../logs/data-collector-api.md) |
| Azure Monitor-statistik | API för anpassade mått | Samla in mått data från alla REST-klienter och lagra i Azure Monitor Metrics-databasen. | [Skicka anpassade mått för en Azure-resurs till Azure Monitor mått lagret med hjälp av en REST API](../essentials/metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Övriga tjänster
Andra tjänster i Azure skriver data till Azure Monitor data plattform. På så sätt kan du analysera data som samlas in av dessa tjänster med data som samlas in av Azure Monitor och utnyttjar samma verktyg för analys och visualisering.

| Tjänst | Mål | Beskrivning | Referens |
|:---|:---|:---|:---|
| [Azure Security Center](../../security-center/index.yml) | Azure Monitor-loggar | Azure Security Center lagrar säkerhets data som samlas in på en Log Analytics arbets yta, vilket gör att den kan analyseras med andra loggdata som samlas in av Azure Monitor.  | [Datainsamling i Azure Security Center](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../../sentinel/index.yml) | Azure Monitor-loggar | Azure Sentinel lagrar data som samlas in från olika data källor på en Log Analytics arbets yta, vilket gör att den kan analyseras med andra loggdata som samlas in av Azure Monitor.  | [Ansluta till datakällor](../../sentinel/quickstart-onboard.md) |


## <a name="next-steps"></a>Nästa steg

- Läs mer om de [typer av övervaknings data som samlas in av Azure Monitor](../data-platform.md) och hur du visar och analyserar dessa data.
- Ange [olika platser där Azure-resurser lagrar data](../monitor-reference.md) och hur du kan komma åt dem.