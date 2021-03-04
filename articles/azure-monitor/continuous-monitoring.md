---
title: Kontinuerlig övervakning med Azure Monitor | Microsoft Docs
description: Beskriver de olika stegen för att använda Azure Monitor för att aktivera kontinuerlig övervakning i dina arbets flöden.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: 2fe9d7e4da8950cee2dcb2fbcfb26355ace7285d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044025"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Kontinuerlig övervakning med Azure Monitor

Kontinuerlig övervakning avser den process och teknik som krävs för att införliva övervakningen i varje fas av dina DevOps och IT-livscykler. Det hjälper till att kontinuerligt säkerställa hälsa, prestanda och tillförlitlighet för ditt program och din infrastruktur när det flyttas från utveckling till produktion. Kontinuerlig övervakning bygger på begreppen kontinuerlig integrering och kontinuerlig distribution (CI/CD) som hjälper dig att utveckla och leverera program vara snabbare och mer tillförlitligt för att ge användarna kontinuerliga värden.

[Azure Monitor](overview.md) är en enhetlig övervaknings lösning i Azure som ger fullständig till gång mellan program och infrastruktur i molnet och lokalt. Det fungerar sömlöst med [Visual Studio och Visual Studio Code](https://visualstudio.microsoft.com/) under utveckling och testning och integreras med [Azure DevOps](/azure/devops/user-guide/index) för versions hantering och hantering av arbets objekt under distribution och drift. Den integrerar även över de ITSM-och SIEM-verktyg som du väljer för att hjälpa dig att spåra problem och incidenter i dina befintliga IT-processer.

I den här artikeln beskrivs de olika stegen för att använda Azure Monitor för att aktivera kontinuerlig övervakning i dina arbets flöden. Den innehåller länkar till annan dokumentation som innehåller information om hur du implementerar olika funktioner.


## <a name="enable-monitoring-for-all-your-applications"></a>Aktivera övervakning för alla dina program
För att få till gång till hela din miljö måste du aktivera övervakning av alla dina webb program och-tjänster. På så sätt kan du enkelt visualisera slut punkt till slut punkts transaktioner och anslutningar i alla komponenter.

- [Azure DevOps Projects](../devops-project/overview.md) ge dig en förenklad upplevelse med din befintliga kod och git-lagringsplats, eller Välj från ett av exempel programmen för att skapa en pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) till Azure.
- [Med kontinuerlig övervakning i din DevOps release-pipeline](./app/continuous-monitoring.md) kan du Gate eller återställa distributionen baserat på övervaknings data.
- Med [statusövervakare](./app/monitor-performance-live-website-now.md) kan du instrumentera en Live .net-app i Windows med Azure Application Insights utan att behöva ändra eller distribuera om koden.
- Om du har åtkomst till koden för ditt program kan du Aktivera fullständig övervakning med [Application Insights](./app/app-insights-overview.md) genom att installera Azure Monitor Application Insights SDK för [.net](./app/asp-net.md), [.net Core](./app/asp-net-core.md), [Java](./app/java-get-started.md), [Node.js](./app/nodejs-quick-start.md)eller [något annat programmeringsspråk](./app/platforms.md). På så sätt kan du ange anpassade händelser, mått eller sid visningar som är relevanta för ditt program och ditt företag.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Aktivera övervakning för hela infrastrukturen
Program är bara tillförlitliga som den underliggande infrastrukturen. Om du har aktiverat övervakning i hela infrastrukturen kan du få fullständig information och göra det enklare att identifiera en potentiell rotorsak när något Miss lyckas. Azure Monitor hjälper dig att spåra hälso tillståndet och prestandan hos hela hybrid infrastrukturen, inklusive resurser som virtuella datorer, behållare, lagring och nätverk.

- Du får automatiskt [plattforms statistik, aktivitets loggar och](agents/data-sources.md) diagnostikloggar från de flesta Azure-resurser utan konfiguration.
- Aktivera djupare övervakning för virtuella datorer med [VM-insikter](vm/vminsights-overview.md).
-  Aktivera djupare övervakning av AKS-kluster med [Azure Monitor för behållare](containers/container-insights-overview.md).
- Lägg till [övervaknings lösningar](./monitor-reference.md) för olika program och tjänster i din miljö.


[Infrastruktur som kod](/azure/devops/learn/what-is-infrastructure-as-code) är hanteringen av infrastrukturen i en beskrivande modell med samma versions hantering som DevOps-team används för käll koden. Det ger bättre tillförlitlighet och skalbarhet för din miljö och gör att du kan utnyttja liknande processer som används för att hantera dina program.

-  Använd [Resource Manager-mallar](./logs/resource-manager-workspace.md) för att aktivera övervakning och konfiguration av aviseringar över en stor uppsättning resurser.
- Använd [Azure policy](../governance/policy/overview.md) för att genomdriva olika regler för dina resurser. Detta säkerställer att resurserna är kompatibla med företagets standarder och service nivå avtal. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Kombinera resurser i Azure-resurs grupper
Ett typiskt program på Azure innehåller i dag flera resurser, till exempel virtuella datorer och App Services eller mikrotjänster som finns på Cloud Services, AKS-kluster eller Service Fabric. De här programmen använder ofta beroenden som Event Hubs, Storage, SQL och Service Bus.

- Kombinera resurser i Azure-resurs grupper för att få fullständig insyn i alla resurser som utgör dina olika program. [Azure Monitor för resurs grupper](./insights/resource-group-insights.md) är ett enkelt sätt att hålla koll på hela programmets prestanda och prestanda och göra det möjligt att gå djupare till respektive komponent för eventuella undersökningar eller fel sökning.

## <a name="ensure-quality-through-continuous-deployment"></a>Säkerställ kvalitet genom kontinuerlig distribution
Kontinuerlig integrering/kontinuerlig distribution gör att du automatiskt kan integrera och distribuera kod ändringar i programmet baserat på resultaten av automatiserad testning. Det effektiviserar distributions processen och säkerställer kvaliteten på eventuella ändringar innan de flyttas till produktion.


- Använd [Azure-pipelines](/azure/devops/pipelines) för att implementera kontinuerlig distribution och automatisera hela processen från kod genomförande till produktion utifrån dina CI/CD-tester.
- Använd [kvalitets grindar](/azure/devops/pipelines/release/approvals/gates) för att integrera övervakning i för distribution eller efter distribution. Detta säkerställer att du uppfyller KPI: erna (Key Health/prestanda mått) när dina program flyttas från dev till produktion och eventuella skillnader i infrastruktur miljön eller skalan inte påverkar dina KPI: er negativt.
- [Upprätthålla separata övervaknings instanser](./app/separate-resources.md) mellan dina olika distributions miljöer, till exempel dev, test, Kanarie och Prod. Detta säkerställer att insamlade data är relevanta för alla associerade program och infrastrukturer. Om du behöver korrelera data i olika miljöer kan du använda diagram med [flera resurser i Metrics Explorer](./essentials/metrics-charts.md) eller skapa [kors resurs frågor i Azure Monitor](logs/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Skapa åtgärds bara aviseringar med åtgärder
En viktig aspekt av övervakningen meddelar proaktivt administratörer om eventuella aktuella och förutsedda problem. 

- Skapa [aviseringar i Azure Monitor](./alerts/alerts-overview.md) baserat på loggar och mått för att identifiera tillstånd för förutsägbara haverier. Du bör ha målet att göra alla aviseringar åtgärds bara, vilket innebär att de representerar faktiska kritiska villkor och söker för att minska antalet falska positiva identifieringar. Använd [dynamiska tröskelvärden](alerts/alerts-dynamic-thresholds.md) för att automatiskt beräkna bas linjer för mått data i stället för att definiera egna statiska tröskelvärden. 
- Definiera åtgärder för aviseringar för att använda det mest effektiva sättet att meddela dina administratörer. Tillgängliga [åtgärder för avisering](alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal) är SMS, e-post, push-meddelanden eller röst samtal.
- Använd mer avancerade åtgärder för att [ansluta till ditt ITSM-verktyg](alerts/itsmc-overview.md) eller andra aviserings hanterings system via [Webhooks](alerts/activity-log-alerts-webhook.md).
- Åtgärda situationer som identifieras i aviseringar och [Azure Automation runbooks](../automation/automation-webhooks.md) eller [Logic Apps](/connectors/custom-connectors/create-webhook-trigger) som kan startas från en avisering med hjälp av Webhooks. 
- Använd automatisk [skalning](./autoscale/tutorial-autoscale-performance-schedule.md) för att dynamiskt öka och minska dina beräknings resurser baserat på insamlade mått.

## <a name="prepare-dashboards-and-workbooks"></a>Förbereda instrument paneler och arbets böcker
Att se till att din utveckling och verksamhet har åtkomst till samma telemetri och verktyg, så att de kan visa mönster i hela din miljö och minimera genomsnitts tiden för att identifiera (MTTD) och genomsnittlig tid för att återställa (MTTR).

- Förbered [anpassade instrument paneler](./app/tutorial-app-dashboards.md) baserat på vanliga mått och loggar för de olika rollerna i din organisation. Instrument paneler kan kombinera data från alla Azure-resurser.
- Förbered [arbets böcker](./visualize/workbooks-overview.md) för att säkerställa kunskaps delning mellan utveckling och åtgärder. Dessa kan förberedas som dynamiska rapporter med mått diagram och logg frågor, eller till och med fel söknings guider som förbereds av utvecklare som hjälper kund support eller åtgärder för att hantera grundläggande problem.

## <a name="continuously-optimize"></a>Optimera kontinuerligt
 Övervakning är en av de grundläggande aspekterna av populära bygge-mått – lär dig filosofi, som regelbundet rekommenderar att du kontinuerligt spårar dina KPI: er och användar beteende mått och sedan striving för att optimera dem genom att planera iterationer. Azure Monitor hjälper dig att samla in mått och loggar som är relevanta för din verksamhet och för att lägga till nya data punkter i nästa distribution vid behov.

- Använd verktyg i Application Insights för att [spåra beteende och engagemang](./app/tutorial-users.md)för slutanvändare.
- Använd [effekten analys](./app/usage-impact.md) för att hjälpa dig att prioritera vilka områden som ska koncentreras till viktiga KPI: er.


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om skillnads komponenterna i [Azure Monitor](overview.md).
- [Lägg till kontinuerlig övervakning](./app/continuous-monitoring.md) i din versions pipeline.