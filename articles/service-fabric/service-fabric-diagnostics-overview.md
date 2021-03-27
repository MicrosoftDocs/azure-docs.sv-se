---
title: Översikt över Azure Service Fabric-övervakning och-diagnostik
description: Lär dig mer om övervakning och diagnostik för Azure Service Fabric kluster, program och tjänster.
ms.topic: conceptual
ms.date: 1/17/2019
ms.openlocfilehash: 71ec86f26de1e94b4e17e0990d2eafd1fff954e2
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627752"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Övervakning och diagnostik för Azure Service Fabric

Den här artikeln innehåller en översikt över övervakning och diagnostik för Azure Service Fabric. Övervakning och diagnostik är avgörande för att utveckla, testa och distribuera arbets belastningar i valfri moln miljö. Du kan till exempel spåra hur dina program används, de åtgärder som vidtas av Service Fabric plattform, din resursutnyttjande med prestanda räknare och det övergripande hälso tillståndet för klustret. Du kan använda den här informationen för att diagnostisera och åtgärda problem och förhindra att de inträffar i framtiden. I de följande avsnitten beskrivs en kort beskrivning av de olika områdena i Service Fabric övervakning för att ta hänsyn till produktions arbets belastningar. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Programövervakning
Program övervakningen spårar hur funktioner och komponenter i ditt program används. Du vill övervaka dina program för att säkerställa att problem som påverkar användare fångas. Ansvaret för program övervakning finns på de användare som utvecklar ett program och dess tjänster eftersom det är unikt för programmets affärs logik. Övervakning av program kan vara användbart i följande scenarier:
* Hur mycket trafik är mitt program? – Behöver du skala dina tjänster så att de uppfyller användarnas krav eller adressera en potentiell Flask hals i ditt program?
* Lyckades service to service-samtal och spåras?
* Vilka åtgärder vidtas av användarna av mitt program? – Insamling av telemetri kan leda till framtida funktions utveckling och bättre diagnostik för program fel
* Kan mitt program hantera ohanterade undantag? 
* Vad händer i de tjänster som körs i mina behållare?

Den fantastiska program övervakningen är att utvecklare kan använda de verktyg och ramverk som de vill eftersom de är i ett programs kontext! Du kan lära dig mer om Azure-lösningen för program övervakning med Azure Monitor-Application Insights i [händelse analys med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).
Vi har också en själv studie kurs om hur du [konfigurerar detta för .NET-program](service-fabric-tutorial-monitoring-aspnet.md). Den här självstudien går igenom hur du installerar rätt verktyg, ett exempel på att skriva anpassad telemetri i ditt program och Visa Programdiagnostik och telemetri i Azure Portal. 


## <a name="platform-cluster-monitoring"></a>Plattforms övervakning (kluster)
En användare styr över vilken telemetri som kommer från deras program eftersom en användare skriver själva koden, men vad händer om diagnostiken från Service Fabrics plattformen? Ett av Service Fabricens mål är att hålla program som är elastiska för maskin varu problem. Det här målet uppnås genom plattformens system tjänsters möjlighet att identifiera infrastruktur problem och snabbt redundansväxla arbets belastningar till andra noder i klustret. Men i det här fallet, vad händer om system tjänsterna själva har problem? Eller om det vid ett försök att distribuera eller flytta en arbets belastning kränks regler för placering av tjänster? Service Fabric innehåller diagnostik för dessa och mer för att se till att du är informerad om aktivitet som sker i klustret. Några exempel scenarier för kluster övervakning är:

Service Fabric tillhandahåller en omfattande uppsättning händelser från rutan. Dessa [Service Fabric händelser](service-fabric-diagnostics-events.md) kan nås via EventStore eller drift kanalen (händelse kanalen som exponeras av plattformen). 

* Service Fabric Event Channels – i Windows är Service Fabric-händelser tillgängliga från en enda ETW-Provider med en uppsättning relevant som `logLevelKeywordFilters` används för att välja mellan drift-och Data & meddelande kanaler – det här är det sätt på vilket vi avgränsar utgående Service Fabric händelser som ska filtreras efter behov. I Linux går Service Fabric-händelser genom LTTng och placeras i en lagrings tabell där de kan filtreras efter behov. Dessa kanaler innehåller granskade, strukturerade händelser som kan användas för att bättre förstå klustrets tillstånd. Diagnostik aktive ras som standard när klustret skapas, vilket skapar en Azure Storage tabell där händelserna från dessa kanaler skickas så att du kan fråga i framtiden. 

* EventStore – EventStore är en funktion som erbjuds av plattformen som tillhandahåller Service Fabric plattforms händelser som är tillgängliga i Service Fabric Explorer och via REST API. Du kan se en Snapshot-vy över vad som händer i ditt kluster för varje entitet, t. ex. Node, service, program och fråga baserat på händelsens tidpunkt. Du kan också läsa mer om EventStore i EventStore- [översikten](service-fabric-diagnostics-eventstore.md).    

![Skärm bild som visar fliken händelser i fönstret noder flera händelser, inklusive en NodeDown-händelse.](media/service-fabric-diagnostics-overview/eventstore.png)

Den angivna diagnostiken är i form av en omfattande uppsättning händelser i rutan. Dessa [Service Fabric händelser](service-fabric-diagnostics-events.md) illustrerar åtgärder som utförs av plattformen på olika entiteter, till exempel noder, program, tjänster, partitioner osv. I det sista scenariot ovan skulle plattformen generera en händelse, om en nod skulle gå ned, `NodeDown` och du kan få ett meddelande direkt från det övervaknings verktyg du väljer. Andra vanliga exempel är `ApplicationUpgradeRollbackStarted` eller `PartitionReconfigured` under en redundansväxling. **Samma händelser är tillgängliga i både Windows-och Linux-kluster.**

Händelserna skickas via standard kanaler i både Windows och Linux och kan läsas av alla övervaknings verktyg som stöder dessa. Azure Monitor lösning är Azure Monitor loggar. Läs mer om vår [Azure Monitor loggar integrering](service-fabric-diagnostics-event-analysis-oms.md) som innehåller en anpassad drift instrument panel för klustret och några exempel frågor som du kan använda för att skapa aviseringar. Fler kluster övervaknings koncept är tillgängliga på [plattforms nivå händelse och logg generering](service-fabric-diagnostics-event-generation-infra.md).

### <a name="health-monitoring"></a>Hälsoövervakning
Service Fabric Platform innehåller en hälso modell som tillhandahåller utöknings bar hälso rapportering för status för entiteter i ett kluster. Varje nod, program, tjänst, partition, replik eller instans har en ständigt uppdaterbar hälso status. Hälso tillståndet kan antingen vara "OK", "varning" eller "fel". Tänk på Service Fabric händelser som verb som sköts av klustret till olika entiteter och hälsa som ett adjektiv för varje entitet. Varje gång hälsan för en viss enhet övergår kommer en händelse också att genereras. På så sätt kan du ställa in frågor och aviseringar för hälso tillstånds händelser i det övervaknings verktyg som du väljer, precis som andra händelser. 

Dessutom tillåter vi även att användare åsidosätter hälsan för entiteter. Om ditt program går igenom en uppgradering och du har testat verifierings test, kan du skriva till Service Fabric hälsa med hjälp av hälso-API: et för att indikera att programmet inte längre är felfritt och att Service Fabric återställer uppgraderingen automatiskt! Mer information om hälso modellen finns i [Introduktion till Service Fabric Health Monitoring](service-fabric-health-introduction.md)

![SFX hälso instrument panel](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Övervaknings enhet
I allmänhet är en övervaknings enhet en separat tjänst som kan se hälso tillstånd och belastning mellan tjänster, ping-slutpunkter och rapportera hälsa för allt i klustret. Detta kan hjälpa till att förhindra fel som inte kan identifieras baserat på visningen av en enskild tjänst. Övervaknings enheter är också en bra plats för att vara värd för kod som utför åtgärds åtgärder utan användar interaktion (till exempel att rensa loggfiler i lagring vid vissa tidsintervall). Du hittar en exempel på övervaknings tjänst implementering [här](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="infrastructure-performance-monitoring"></a>Övervakning av infrastruktur (prestanda)
Nu när vi har täckt diagnostiken i ditt program och plattformen, hur vet vi att maskin varan fungerar som förväntat? Övervakning av den underliggande infrastrukturen är en viktig del av att förstå klustrets tillstånd och resursutnyttjande. Att mäta systemets prestanda beror på många faktorer som kan vara beroende av dina arbets belastningar. Dessa faktorer mäts vanligt vis genom prestanda räknare. Dessa prestanda räknare kan komma från en rad olika källor, inklusive operativ systemet, .NET Framework eller själva Service Fabric själva plattformen. Vissa scenarier där de skulle vara användbara är

* Utnyttjar jag maskin varan effektivt? Vill du använda maskin varan med 90% CPU eller 10% CPU. Detta är praktiskt när du skalar klustret eller optimerar programmets processer.
* Kan jag förutse infrastruktur problem proaktivt? – många problem föregås av plötsliga ändringar (sjunker) i prestanda, så du kan använda prestanda räknare som nätverks-I/O och CPU-användning för att förutsäga och diagnostisera problemen proaktivt.

En lista över prestanda räknare som ska samlas in på infrastruktur nivån finns i [prestanda mått](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric tillhandahåller också en uppsättning prestanda räknare för programmerings modellerna Reliable Services och aktörer. Om du använder någon av dessa modeller, kan dessa prestanda räknare se till att dina aktörer snurrar upp och ned korrekt, eller att dina Reliable Services-begäranden hanteras tillräckligt snabbt. Mer information finns i [övervakning av Reliable service Remoting](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) och [prestanda övervakning för Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). 

Azure Monitor-lösningen för att samla in dessa är Azure Monitor loggar precis som övervakning på plattforms nivå. Du bör använda [Log Analytics agenten](service-fabric-diagnostics-oms-agent.md) för att samla in lämpliga prestanda räknare och visa dem i Azure Monitor loggar.

## <a name="recommended-setup"></a>Rekommenderad installation
Nu när vi har gått över varje område med övervaknings-och exempel scenarier, är här en sammanfattning av Azures övervaknings verktyg och konfigureras för att övervaka alla områden ovan. 

* Program övervakning med [Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* Kluster övervakning med [Diagnostics agent](service-fabric-diagnostics-event-aggregation-wad.md) och [Azure Monitor loggar](service-fabric-diagnostics-oms-setup.md)
* Infrastruktur övervakning med [Azure Monitor loggar](service-fabric-diagnostics-oms-agent.md)

Du kan också använda och ändra exempel ARM-mallen som finns [här](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) för att automatisera distributionen av alla nödvändiga resurser och agenter. 

## <a name="other-logging-solutions"></a>Andra loggnings lösningar

Även om de två lösningar som vi rekommenderar, [Azure Monitor loggar](service-fabric-diagnostics-event-analysis-oms.md) och [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) har byggts i integrering med Service Fabric, skrivs många händelser ut via ETW-leverantörer och är utöknings bara av andra loggnings lösningar. Du bör också titta på den [elastiska stacken](https://www.elastic.co/products) (särskilt om du överväger att köra ett kluster i en offline-miljö), [dynaTrace](https://www.dynatrace.com/)eller någon annan plattform som du föredrar. Vi har en lista över integrerade partners som är tillgängliga [här](service-fabric-diagnostics-partners.md).

De viktigaste punkterna för vilken plattform du väljer bör inkludera hur bekvämt du är med användar gränssnittet, vilka fråge funktioner, vilka anpassade visualiseringar och instrument paneler som är tillgängliga samt de ytterligare verktyg som de tillhandahåller för att förbättra din övervaknings upplevelse. 

## <a name="next-steps"></a>Nästa steg

* För att komma igång med att instrumentera dina program, se [händelse-och logg generering på program nivå](service-fabric-diagnostics-event-generation-app.md).
* Gå igenom stegen för att konfigurera Application Insights för ditt program med [övervaka och diagnostisera ett ASP.net Core program på Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Lär dig mer om att övervaka plattformen och händelserna Service Fabric ger dig på [plattforms nivå händelse och logg generering](service-fabric-diagnostics-event-generation-infra.md).
* Konfigurera Azure Monitor loggar integration med Service Fabric i [konfigurera Azure Monitor loggar för ett kluster](service-fabric-diagnostics-oms-setup.md)
* Lär dig hur du konfigurerar Azure Monitor loggar för övervakning av behållare – [övervakning och diagnostik för Windows-behållare i Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Se exempel på diagnostiska problem och lösningar med Service Fabric för att [diagnosticera vanliga scenarier](service-fabric-diagnostics-common-scenarios.md)
* Kolla in andra diagnostiska produkter som integreras med Service Fabric i [Service Fabric diagnostiska partner](service-fabric-diagnostics-partners.md)
* Lär dig mer om allmänna övervaknings rekommendationer för Azure-resurser – [bästa praxis – övervakning och diagnostik](/azure/architecture/best-practices/monitoring). 
