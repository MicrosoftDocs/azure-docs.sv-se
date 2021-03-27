---
title: Azure Service Fabric Event Analysis med Azure Monitor loggar
description: Lär dig mer om visualisering och analys av händelser med hjälp av Azure Monitor loggar för övervakning och diagnostik av Azure Service Fabric-kluster.
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: f1e22213c857b400cc36c51cefb90e2379352893
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628976"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Händelse analys och visualisering med Azure Monitor loggar
 Azure Monitor loggar samlar in och analyserar telemetri från program och tjänster som finns i molnet och tillhandahåller analys verktyg som hjälper dig att maximera deras tillgänglighet och prestanda. Den här artikeln beskriver hur du kör frågor i Azure Monitor loggar för att få insikter och felsöka vad som händer i klustret. Följande vanliga frågor behandlas:

* Hur gör jag för att felsöka hälso händelser?
* Hur gör jag för att reda på när en nod kraschar?
* Hur gör jag för att vet du om programmets tjänster har startats eller stoppats?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Översikt över arbets ytan Log Analytics

>[!NOTE] 
>Även om Diagnostic Storage är aktiverat som standard när klustret skapas, måste du ändå konfigurera Log Analytics arbets ytan för att läsa från det diagnostiska lagrings utrymmet.

Azure Monitor loggar samlar in data från hanterade resurser, inklusive en Azure Storage-tabell eller en agent, och behåller den på en central lagrings plats. Data kan sedan användas för analys, aviseringar och visualisering, eller ytterligare export. Azure Monitor loggar har stöd för händelser, prestanda data eller andra anpassade data. Ta en titt på stegen om du vill [Konfigurera diagnostikprogrammet för att samla in händelser](service-fabric-diagnostics-event-aggregation-wad.md) och [steg för att skapa en Log Analytics arbets yta för att läsa från händelserna i lagringen](service-fabric-diagnostics-oms-setup.md) för att se till att data flödar till Azure Monitor-loggar.

När data har tagits emot av Azure Monitor loggar har Azure flera *övervaknings lösningar* som är förpaketerade lösningar eller drifts instrument paneler för att övervaka inkommande data, anpassade till flera olika scenarier. Detta inkluderar en *Service Fabric-analys* lösning och en *container* lösning som är de två mest relevanta för diagnostik och övervakning när du använder Service Fabric kluster. Den här artikeln beskriver hur du använder Service Fabric-analys-lösningen som skapas med arbets ytan.

## <a name="access-the-service-fabric-analytics-solution"></a>Komma åt Service Fabric-analys-lösningen

I [Azure Portal](https://portal.azure.com)går du till den resurs grupp där du skapade Service Fabric-analys-lösningen.

Välj resursen **ServiceFabric \<nameOfOMSWorkspace\>**.

I visas `Summary` paneler i form av en graf för var och en av de lösningar som är aktiverade, inklusive en för Service Fabric. Fortsätt till Service Fabric-analys-lösningen genom att klicka på **Service Fabric** graf.

![Service Fabric lösning](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

Följande bild visar Service Fabric-analys lösningens start sida. Den här start sidan innehåller en Snapshot-vy över vad som händer i klustret.

![Skärm bild som visar start sidan för den Service Fabric-analys lösningen.](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Om du har aktiverat diagnostik när klustret har skapats kan du se händelser för 

* [Service Fabric kluster händelser](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors programmerings modell händelser](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services programmerings modell händelser](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Utöver Service Fabric-händelser i rutan kan mer detaljerade system händelser samlas in genom att [du uppdaterar konfigurationen av tillägget för diagnostik](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Visa Service Fabric händelser, inklusive åtgärder på noder

På sidan Service Fabric-analys klickar du på grafen för att **Service Fabric händelser**.

![Service Fabric lösningens operativa kanal](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Klicka på **lista** om du vill visa händelserna i en lista. En gång till visas alla system händelser som har samlats in. För referens är dessa från **WADServiceFabricSystemEventsTable** i Azure Storage-kontot, och på samma sätt som de Reliable Services-och skådespelare-händelser som du ser härnäst kommer du från respektive tabell.
    
![Frågans operativa kanal](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Alternativt kan du klicka på förstorings glaset till vänster och använda Kusto för att hitta det du söker. Om du till exempel vill hitta alla åtgärder som vidtas på noder i klustret kan du använda följande fråga. De händelse-ID: n som används nedan finns i [referens för drift kanal händelser](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Du kan fråga efter många fler fält, till exempel de speciella noderna (dator) som system tjänsten (aktivitets namn).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Visa Service Fabric Reliable service-och aktörs händelser

På sidan Service Fabric-analys klickar du på grafen för **Reliable Services**.

![Service Fabric lösning Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Klicka på **lista** om du vill visa händelserna i en lista. Här kan du se händelser från de pålitliga tjänsterna. Du kan se olika händelser för när tjänsten runasync har startats och slutförts, vilket vanligt vis sker vid distributioner och uppgraderingar. 

![Fråga Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Pålitliga aktörs händelser kan visas på liknande sätt. Om du vill konfigurera mer detaljerade händelser för tillförlitliga aktörer måste du ändra `scheduledTransferKeywordFilter` i konfigurationen för diagnostiskt tillägg (visas nedan). Information om värdena för dessa finns i [händelse referens för pålitliga aktörer](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

Frågespråket för Kusto är kraftfullt. En annan värdefull fråga som du kan köra är att ta reda på vilka noder som genererar de flesta händelser. Frågan i skärm bilden nedan visar Service Fabric operativa händelser som sammanställs med den aktuella tjänsten och noden.

![Fråga efter händelser per nod](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Nästa steg

* Om du vill aktivera övervakning av infrastrukturen, t. ex. prestanda räknare, överhuvud till att [lägga till Log Analytics agenten](service-fabric-diagnostics-oms-agent.md). Agenten samlar in prestanda räknare och lägger till dem i din befintliga arbets yta.
* För lokala kluster erbjuder Azure Monitor-loggar en gateway (HTTP Forward proxy) som kan användas för att skicka data till Azure Monitor loggar. Läs mer om det i [ansluta datorer utan Internet åtkomst till Azure Monitor loggar med hjälp av Log Analytics Gateway](../azure-monitor/agents/gateway.md).
* Konfigurera  [automatiserad avisering](../azure-monitor/alerts/alerts-overview.md) till stöd för identifiering och diagnostik.
* Bekanta dig med funktionerna för [loggs ökning och frågor](../azure-monitor/logs/log-query-overview.md) som erbjuds som en del av Azure Monitor loggar.
* Få en mer detaljerad översikt över Azure Monitor loggar och vad den erbjuder, Läs [Vad är Azure Monitor loggar?](../azure-monitor/overview.md).
