---
title: Övervakning av Azure Service Fabric-plattforms nivå
description: Lär dig mer om händelser och loggar på plattforms nivå som används för att övervaka och diagnostisera Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: f9db0df9370197190c11b740f1fd1af3fb69f19c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581412"
---
# <a name="monitoring-the-cluster"></a>Övervaka klustret

Det är viktigt att övervaka på kluster nivå för att avgöra om maskin varan och klustret fungerar som förväntat. Även om Service Fabric kan hålla program som körs under ett maskin varu fel, men du måste fortfarande diagnostisera om ett fel inträffar i ett program eller i den underliggande infrastrukturen. Du bör också övervaka dina kluster för att bättre planera för kapacitet och hjälpa till med beslut om att lägga till eller ta bort maskin vara.

Service Fabric visar flera strukturerade plattforms händelser, som [Service Fabric händelser](service-fabric-diagnostics-events.md), via EventStore och olika logg kanaler direkt. 

I Windows är Service Fabric-händelser tillgängliga från en enda ETW-Provider med en uppsättning relevant som `logLevelKeywordFilters` används för att välja mellan drift-och Data & meddelande kanaler – det här är hur vi avgränsar utgående Service Fabric händelser som ska filtreras efter behov.

* **Drift** Hög nivå åtgärder som utförs av Service Fabric och klustret, inklusive händelser för en nod som kommer, ett nytt program som distribueras eller en uppgraderings återställning osv. Se den fullständiga listan med händelser [här](service-fabric-diagnostics-event-generation-operational.md).  

* **Drift – detaljerad**  
Hälso rapporter och beslut om belastnings utjämning.

Åtgärds kanalen kan nås via en mängd olika sätt, t. ex. ETW/Windows-händelseloggar, [EventStore](service-fabric-diagnostics-eventstore.md) (tillgängligt i Windows i version 6,2 och senare för Windows-kluster). EventStore ger dig åtkomst till klustrets händelser per entitet (entiteter, till exempel kluster, noder, program, tjänster, partitioner, repliker och behållare) och exponerar dem via REST-API: er och Service Fabric klient bibliotek. Använd EventStore för att övervaka dina dev/test-kluster och för att få en tidpunkts förståelse av statusen för dina produktions kluster.

* **Data & meddelanden**  
Kritiska loggar och händelser som genererats i meddelande tjänsten (för närvarande endast ReverseProxy) och data Sök väg (Reliable Services-modeller).

* **Data & meddelanden – detaljerat**  
Utförlig kanal som innehåller alla icke-kritiska loggar från data och meddelanden i klustret (denna kanal har en mycket hög mängd händelser).

Förutom dessa finns det två strukturerade EventSource-kanaler, samt loggar som vi samlar in för support syfte.

* [Reliable Services-händelser](service-fabric-reliable-services-diagnostics.md)  
Programmerings modell för vissa händelser.

* [Reliable Actors-händelser](service-fabric-reliable-actors-diagnostics.md)  
Programmerings modell för vissa händelser och prestanda räknare.

* Support loggar  
System loggar som genereras av Service Fabric bara användas av oss när de tillhandahåller support.

Dessa olika kanaler avser de flesta av plattforms nivå loggning som rekommenderas. För att förbättra plattforms nivå loggningen bör du överväga att investera i bättre förståelse för hälso modellen och lägga till anpassade hälso rapporter och lägga till anpassade **prestanda räknare** för att skapa en real tids förståelse av konsekvenserna av dina tjänster och program i klustret.

För att kunna använda dessa loggar, rekommenderar vi starkt att du lämnar "diagnostik" aktiverat när klustret skapas i Azure-portalen. Genom att aktivera diagnostik, när klustret har distribuerats, kan Windows Azure-diagnostik bekräfta de operativa, Reliable Services och pålitliga aktörernas kanaler, och lagra data som förklaras ytterligare i [mängd händelser med Azure-diagnostik](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric Health och inläsnings rapportering

Service Fabric har en egen hälso modell som beskrivs i detalj i de här artiklarna:

- [Introduktion till Service Fabric-hälsoövervakning](service-fabric-health-introduction.md)
- [Rapportera och kontrollera hälsan hos tjänster](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Lägg till anpassade Service Fabric hälso rapporter](service-fabric-report-health.md)
- [Visa Service Fabric hälso rapporter](service-fabric-view-entities-aggregated-health.md)

Hälso övervakning är avgörande för flera aspekter av driften av en tjänst, särskilt under en program uppgradering. När varje uppgraderings domän för tjänsten uppgraderas måste uppgraderings domänen klara hälso kontroller innan distributionen flyttas till nästa uppgraderings domän. Om OK-hälsostatus inte kan uppnås återställs distributionen, så att programmet förblir i ett känt OK-tillstånd. Även om vissa kunder kan påverkas innan tjänsterna återställs, kommer de flesta kunder inte att uppleva ett problem. Dessutom uppstår en lösning relativt snabbt utan att behöva vänta på åtgärd från en mänsklig operatör. De mer hälso kontroller som ingår i din kod, desto mer elastiska tjänsten är till distributions problem.

En annan aspekt av tjänste hälsan är rapporterings mått från tjänsten. Mått är viktiga i Service Fabric eftersom de används för att utjämna resursanvändningen. Mått kan också vara en indikator för system hälsan. Du kan till exempel ha ett program som har många tjänster och varje instans rapporterar ett RPS-mått (begär Anden per sekund). Om en tjänst använder fler resurser än en annan tjänst, Service Fabric flyttar tjänst instanser runt klustret, för att försöka upprätthålla även resursutnyttjande. En mer detaljerad förklaring av hur resursutnyttjande fungerar finns i [Hantera resursförbrukning och belastning i Service Fabric med mått](service-fabric-cluster-resource-manager-metrics.md).

Mått kan också hjälpa dig att ge dig insikter om hur tjänsten presterar. Med tiden kan du använda mått för att kontrol lera att tjänsten körs i förväntade parametrar. Om till exempel trender visar att kl. 9 AM på måndag morgon den genomsnittliga RPS är 1 000, kan du skapa en hälso rapport som varnar dig om RPS är lägre än 500 eller högre än 1 500. Allt kan vara helt fint, men det kan vara värt att se till att kunderna har en bra upplevelse. Tjänsten kan definiera en uppsättning mått som kan rapporteras för hälso kontroll, men som inte påverkar resurs utjämning av klustret. Det gör du genom att ställa in måttets vikt på noll. Vi rekommenderar att du startar alla mått med en vikt på noll och inte ökar vikten tills du är säker på att du förstår hur viktningen av måtten påverkar resurs utjämning för klustret.

> [!TIP]
> Använd inte för många viktade mått. Det kan vara svårt att förstå varför tjänst instanser flyttas runt för balansering. Några mått kan vara mycket bra!

All information som kan indikera hälso tillstånd och prestanda för ditt program är en kandidat för mått och hälso rapporter. **En processor prestanda räknare kan meddela dig hur noden används, men den meddelar inte om en viss tjänst är felfri, eftersom flera tjänster kan köras på en enda nod.** Men mått som RPS, objekt som bearbetas och svars tid alla kan ange hälso tillståndet för en speciell tjänst.

## <a name="service-fabric-support-logs"></a>Service Fabric support loggar

Om du behöver kontakta Microsoft Support för att få hjälp med ditt Azure Service Fabric-kluster är support loggar nästan alltid obligatoriska. Om klustret finns i Azure konfigureras support loggar automatiskt och samlas in som en del av att skapa ett kluster. Loggarna lagras i ett dedikerat lagrings konto i klustrets resurs grupp. Lagrings kontot har inget fast namn, men i kontot visas BLOB-behållare och tabeller med namn som börjar med *infrastruktur resurser*. Information om hur du konfigurerar logg samlingar för ett fristående kluster finns i [skapa och hantera ett fristående Azure Service Fabric-kluster](service-fabric-cluster-creation-for-windows-server.md) och [konfigurations inställningar för ett fristående Windows-kluster](service-fabric-cluster-manifest.md). För fristående Service Fabric-instanser ska loggarna skickas till en lokal fil resurs. Du **måste** ha dessa loggar för support, men de är inte avsedda att användas av någon utanför Microsofts kund support team.

## <a name="measuring-performance"></a>Mäta prestanda

Genom att mäta prestanda för klustret får du hjälp att förstå hur det kan hantera belastnings-och enhets beslut kring skalning av klustret (mer information om skalning av ett kluster [i Azure](service-fabric-cluster-scale-in-out.md)eller [lokalt](service-fabric-cluster-windows-server-add-remove-nodes.md)). Prestanda data är också användbara vid jämförelse med åtgärder som du eller dina program och tjänster kan ha vidtagit när du analyserar loggar i framtiden. 

En lista över prestanda räknare som ska samlas in när du använder Service Fabric finns i [prestanda räknare i Service Fabric](service-fabric-diagnostics-event-generation-perf.md)

Här följer två vanliga metoder som du kan använda för att samla in prestanda data för klustret:

* **Använda en agent**  
Detta är det bästa sättet att samla in prestanda från en dator eftersom agenter vanligt vis har en lista över möjliga prestanda mått som kan samlas in, och det är en relativt enkel process att välja de mått som du vill samla in eller ändra. Läs om Azure Monitor som erbjuder Azure Monitor loggar i Service Fabric s [Azure Monitor loggar integrering](service-fabric-diagnostics-event-analysis-oms.md) och [installation av Log Analytics agent](../azure-monitor/agents/agent-windows.md) för att lära dig mer om Log Analytics agent, vilket är en övervaknings agent som kan hämta prestanda data för virtuella kluster datorer och distribuerade behållare.

* **Prestanda räknare för Azure Table Storage**  
Du kan också skicka prestanda mått till samma tabell lagring som händelserna. Detta kräver att du ändrar Azure-diagnostik-konfigurationen för att hämta lämpliga prestanda räknare från de virtuella datorerna i klustret och att det ska kunna hämta Docker-statistik om du distribuerar alla behållare. Läs om hur du konfigurerar [prestanda räknare i wad](service-fabric-diagnostics-event-aggregation-wad.md) i Service Fabric att konfigurera prestanda räknar samlingen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om Service Fabric [Azure Monitor loggar integration](service-fabric-diagnostics-event-analysis-oms.md) för att samla in kluster diagnoser och skapa anpassade frågor och aviseringar
* Lär dig mer om Service Fabric i den integrerade diagnostiken, [EventStore](service-fabric-diagnostics-eventstore.md)
* Gå igenom några [vanliga diagnostiska scenarier](service-fabric-diagnostics-common-scenarios.md) i Service Fabric
