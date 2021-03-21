---
title: Översikt över Azure Monitor | Microsoft Docs
description: Översikt över Microsoft-tjänster och funktioner som bidrar till en fullständig övervakningsstrategi för dina Azure-tjänster och program.
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 11/17/2019
ms.openlocfilehash: 544d6937e412e3e1cfc2cf4e520c02f3f804fc8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047170"
---
# <a name="azure-monitor-overview"></a>Översikt över Azure Monitor

Azure Monitor hjälper dig att maximera tillgängligheten och prestanda för dina program och tjänster. Den innehåller en omfattande lösning för att samla in, analysera och agera på telemetri från molnet och lokala miljöer. Den här informationen hjälper dig att förstå hur dina program presterar och proaktivt identifiera problem som påverkar dem och de resurser som de är beroende av.

Några exempel på vad du kan göra med Azure Monitor är:

- Identifiera och diagnostisera problem mellan program och beroenden med [Application Insights](app/app-insights-overview.md).
- Korrelera infrastruktur problem med [VM Insights](vm/vminsights-overview.md) och [behållar insikter](containers/container-insights-overview.md).
- Öka detalj nivån i dina övervaknings data med [Log Analytics](logs/log-query-overview.md) för fel sökning och djup diagnostik.
- Stöd åtgärder i stor skala med [smarta aviseringar](alerts/alerts-smartgroups-overview.md) och [automatiserade åtgärder](alerts/alerts-action-rules.md).
- Skapa visualiseringar med Azure- [instrumentpaneler](visualize/tutorial-logs-dashboards.md) och [arbets böcker](visualize/workbooks-overview.md).
- Samla in data från [övervakade resurser](./monitor-reference.md) med hjälp av [Azure Monitor mått](./essentials/data-platform-metrics.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Översikt

I följande diagram ges en översikt över Azure Monitor på hög nivå. I diagrammets centrum finns data lager för mått och loggar, som är de två grundläggande typer av data som används av Azure Monitor. Till vänster visas [källorna till de övervaknings data](agents/data-sources.md) som finns i dessa [data lager](data-platform.md). Till höger finns olika funktioner som Azure Monitor utför med dessa insamlade data. Detta inkluderar sådana åtgärder som analys, aviseringar och strömning till externa system.

![Översikt över Azure Monitor](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Övervaknings data plattform

Alla data som samlas in av Azure Monitor passar in i en av två grundläggande typer, [mått och loggar](data-platform.md). [Mått](essentials/data-platform-metrics.md) är numeriska värden som beskriver någon aspekt av ett system vid en viss tidpunkt. De är lätta och stöder nästan real tids scenarier. [Loggar](logs/data-platform-logs.md) innehåller olika typer av data som är ordnade i poster med olika uppsättningar egenskaper för varje typ. Telemetri som händelser och spår lagras som loggar förutom prestanda data så att alla kan kombineras för analys.

För många Azure-resurser kommer du att se data som samlas in av Azure Monitor direkt på sidan Översikt i Azure Portal. Ta en titt på vilken virtuell dator som helst, så ser du till exempel flera diagram som visar prestanda mått. Klicka på något av diagrammen för att öppna data i [mått Utforskaren](essentials/metrics-charts.md) i Azure Portal, vilket gör att du kan skapa diagram över värdena för flera mått över tid.  Du kan visa diagrammen interaktivt eller fästa dem på en instrument panel för att visa dem med andra visualiseringar.

![Diagrammet visar Mät data som flödar till Metrics Explorer som ska användas i visualiseringar.](media/overview/metrics.png)

Loggdata som samlas in av Azure Monitor kan analyseras med [frågor](logs/log-query-overview.md) för att snabbt hämta, konsolidera och analysera insamlade data.  Du kan skapa och testa frågor med hjälp av [Log Analytics](./logs/log-query-overview.md) i Azure Portal. Du kan sedan antingen analysera data direkt med olika verktyg eller spara frågor för användning med [visualiseringar](visualizations.md) eller [varnings regler](alerts/alerts-overview.md).

Azure Monitor använder en version av [Kusto-frågespråket](/azure/kusto/query/) som passar för enkla logg frågor, men även avancerade funktioner som agg regeringar, kopplingar och smart analys. Du kan snabbt lära dig frågespråket med [flera lektioner](logs/get-started-queries.md).  Viss vägledning erbjuds användare som redan är bekanta med [SQL](/azure/data-explorer/kusto/query/sqlcheatsheet) och [Splunk](/azure/data-explorer/kusto/query/splunk-cheat-sheet).

![Diagrammet visar loggar data som flödar till Log Analytics för analys.](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Vilka data samlar Azure Monitor in?

Azure Monitor kan samla in data från [olika källor](monitor-reference.md). Detta är ett intervall från ditt program, alla operativ system och tjänster som det förlitar sig på, ned till själva plattformen. Azure Monitor samlar in data från följande nivåer:

- **Program övervaknings data**: data om prestanda och funktioner i den kod som du har skrivit, oavsett plattform.
- **Övervaknings data för gäst operativ** system: data om det operativ system som programmet körs på. Det kan köras i Azure, i ett annat moln eller lokalt. 
- **Övervakningsdata för Azure-resurser**: Data om hur en Azure-resurs fungerar.
- **Övervaknings data för Azure-prenumeration**: data om drift och hantering av en Azure-prenumeration, samt data om hälso tillståndet och driften av Azure. 
- **Azure-klient övervaknings data**: data om driften av Azure-tjänster på klient nivå, till exempel Azure Active Directory.

När du skapar en Azure-prenumeration och börjar lägga till resurser, till exempel virtuella datorer och webbappar, börjar Azure Monitor samla in data.  [Aktivitets loggar](essentials/platform-logs-overview.md) poster när resurser skapas eller ändras. [Mått](data-platform.md) visar hur resursen presterar och vilka resurser den använder. 

[Aktivera diagnostik](essentials/platform-logs-overview.md) för att utöka de data som du samlar in i den interna åtgärden för resurserna.  [Lägg till en agent](agents/agents-overview.md) för att beräkna resurser för att samla in telemetri från sina gäst operativ system. 

Aktivera övervakning för ditt program med [Application Insights](app/app-insights-overview.md) för att samla in detaljerad information, till exempel sidvyer, program begär Anden och undantag. Kontrol lera att programmet är tillgängligt genom att konfigurera ett [tillgänglighets test](app/monitor-web-app-availability.md) för att simulera användar trafik.

### <a name="custom-sources"></a>Anpassade källor

Azure Monitor kan samla in loggdata från alla REST-klienter med hjälp av [API: et för data insamling](logs/data-collector-api.md). På så sätt kan du skapa anpassade övervaknings scenarier och utöka övervakningen till resurser som inte visar telemetri via andra källor.

## <a name="insights"></a>Insikter
Övervaknings data är bara användbara om det kan öka din insyn i driften av din dator miljö. [Insikter](monitor-reference.md#insights-and-core-solutions) ger en anpassad övervaknings upplevelse för specifika Azure-tjänster. De kräver minimal konfiguration och ökar din insyn i driften av kritiska resurser.

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) övervakar tillgänglighet, prestanda och användning av dina webb program oavsett om de finns i molnet eller lokalt. Det utnyttjar den kraftfulla data analys plattformen i Azure Monitor för att ge dig djupgående insikter om programmets åtgärder. Du kan diagnostisera fel utan att vänta på att användaren ska rapportera dem. Application Insights innehåller anslutnings punkter till olika utvecklingsverktyg och integreras med Visual Studio för att stödja dina DevOps-processer.

![App Insights](media/overview/app-insights.png)

### <a name="container-insights"></a>Container Insights
[Behållar insikter](containers/container-insights-overview.md) övervakar prestanda för behållar arbets belastningar som distribueras till hanterade Kubernetes-kluster som finns i Azure Kubernetes service (AKS). Det ger dig prestanda synlighet genom att samla in mått från styrenheter, noder och behållare som är tillgängliga i Kubernetes via Metrics-API: et. Containerloggar samlas också in.  När du har aktiverat övervakning från Kubernetes-kluster samlas dessa mått och loggar in automatiskt åt dig via en behållar version av Log Analytics agent för Linux.

![Hälso tillstånd för behållare](media/overview/container-insights.png)

### <a name="vm-insights"></a>VM-insikter
[VM Insights](vm/vminsights-overview.md) övervakar dina virtuella Azure-datorer (VM) i stor skala. Den analyserar prestanda och hälsa för dina virtuella Windows-och Linux-datorer och identifierar olika processer och sammankopplade beroenden för externa processer. Lösningen innehåller stöd för övervakning av prestanda-och program beroenden för virtuella datorer som finns lokalt eller i en annan moln leverantör.  


![VM-insikter](media/overview/vm-insights.png)


## <a name="responding-to-critical-situations"></a>Svara på kritiska situationer
Förutom att göra det möjligt att interaktivt analysera övervaknings data, måste en effektiv övervaknings lösning kunna reagera proaktivt på kritiska villkor som identifieras i de data som samlas in. Detta kan skicka en text eller e-post till en administratör som ansvarar för att undersöka ett problem. Eller så kan du starta en automatiserad process som försöker åtgärda ett fel tillstånd.


### <a name="alerts"></a>Aviseringar
[Aviseringar i Azure Monitor](alerts/alerts-overview.md) proaktivt meddela dig om kritiska villkor och eventuellt försök att vidta lämpliga åtgärder. Varnings regler baserade på mått ger nära real tids aviseringar baserat på numeriska värden. Regler som baseras på loggar möjliggör komplex logik över data från flera källor.

Varnings regler i Azure Monitor använda [Åtgärds grupper](alerts/action-groups.md)som innehåller unika uppsättningar av mottagare och åtgärder som kan delas över flera regler. Utifrån dina krav kan åtgärds grupper utföra sådana åtgärder som att använda Webhooks för att få aviseringar att starta externa åtgärder eller integrera med dina ITSM-verktyg.

![Skärm bild som visar aviseringar i Azure Monitor med allvarlighets grad, totala aviseringar och annan information.](media/overview/alerts.png)

### <a name="autoscale"></a>Automatisk skalning
Med autoskalning kan du använda rätt mängd resurser för att hantera belastningen på ditt program. Skapa regler som använder mått som samlas in av Azure Monitor för att avgöra när du ska lägga till resurser automatiskt när belastningen ökar. Spara pengar genom att ta bort resurser som är inaktiva. Du anger ett minsta och högsta antal instanser och logiken för när du vill öka eller minska resurserna.

![Diagrammet visar autoskalning, med flera servrar på en rad med etiketten processor tid > 80% och två servrar som har marker ATS som minst tre servrar som aktuell kapacitet och fem som Max.](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Visualiserar övervaknings data
[Visualiseringar](visualizations.md) som diagram och tabeller är effektiva verktyg för att sammanfatta övervaknings data och presentera dem för olika mål grupper. Azure Monitor har egna funktioner för visualisering av övervaknings data och utnyttjar andra Azure-tjänster för att publicera den till olika mål grupper.

### <a name="dashboards"></a>Instrumentpaneler
Med [Azure-instrumentpaneler](../azure-portal/azure-portal-dashboards.md) kan du kombinera olika typer av data i ett enda fönster i [Azure Portal](https://portal.azure.com). Du kan välja att dela instrument panelen med andra Azure-användare. Lägg till utdata från alla logg frågor eller mått diagram på en Azure-instrumentpanel. Du kan till exempel skapa en instrument panel som kombinerar paneler som visar ett diagram över mått, en tabell med aktivitets loggar, ett användnings diagram från Application Insights och utdata från en logg fråga.

![Skärm bild som visar en Azure-instrumentpanel, som innehåller program-och säkerhets paneler, tillsammans med annan anpassningsbar information.](media/overview/dashboard.png)

### <a name="workbooks"></a>Arbetsböcker
[Arbets böcker](visualize/workbooks-overview.md) ger en flexibel arbets yta för data analys och skapandet av rika visuella rapporter i Azure Portal. De gör att du kan trycka på flera data källor i Azure och kombinera dem till enhetliga interaktiva upplevelser. Använd arbets böcker som tillhandahålls med insikter eller skapa egna från fördefinierade mallar.


![Exempel på arbets böcker](media/overview/workbooks.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) är en Business Analytics-tjänst som tillhandahåller interaktiva visualiseringar i flera olika data källor. Det är ett effektivt sätt att göra data tillgängliga för andra inom och utanför organisationen. Du kan konfigurera Power BI att [automatiskt importera logg data från Azure Monitor](./visualize/powerbi.md) för att dra nytta av dessa ytterligare visualiseringar.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integrera och exportera data
Du har ofta behov av att integrera Azure Monitor med andra system och att bygga anpassade lösningar som använder dina övervaknings data. Andra Azure-tjänster fungerar med Azure Monitor för att tillhandahålla denna integrering.

### <a name="event-hub"></a>Händelsehubb
[Azure Event Hubs](../event-hubs/index.yml) är en strömmande plattform och händelse inmatnings tjänst. Den kan transformera och lagra data med hjälp av en analys av real tids analys eller batch-/lagrings kort. Använd Event Hubs för att [strömma Azure Monitor data](essentials/stream-monitoring-data-event-hubs.md) till partner Siem och övervaknings verktyg.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) är en tjänst som gör att du kan automatisera uppgifter och affärs processer med hjälp av arbets flöden som integreras med olika system och tjänster. Det finns aktiviteter för att läsa och skriva mått och loggar i Azure Monitor. På så sätt kan du bygga arbets flöden som integreras med olika system.


### <a name="api"></a>API
Flera API: er är tillgängliga för att läsa och skriva mått och loggar till och från Azure Monitor utöver åtkomst till genererade aviseringar. Du kan också konfigurera och hämta aviseringar. Det ger dig i stort sett obegränsade möjligheter att bygga anpassade lösningar som integreras med Azure Monitor.

## <a name="next-steps"></a>Nästa steg
Läs mer om:

* [Mått och loggar](https://docs.microsoft.com/azure/azure-monitor/data-platform#metrics) för data som samlas in av Azure Monitor.
* [Data källor](agents/data-sources.md) för hur de olika komponenterna i ditt program skickar telemetri.
* [Logg frågor](logs/log-query-overview.md) för att analysera insamlade data.
* [Metod tips](/azure/architecture/best-practices/monitoring) för övervakning av moln program och-tjänster.
