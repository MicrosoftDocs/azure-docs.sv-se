---
title: Metod tips för Azure Service Fabric övervakning
description: Bästa praxis och design överväganden för övervakning av kluster och program med hjälp av Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 0eb9ce24f9ead44b7ba5a4d28d24177e62cb7757
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950526"
---
# <a name="monitoring-and-diagnostic-best-practices-for-azure-service-fabric"></a>Metod tips för övervakning och diagnostik för Azure Service Fabric

[Övervakning och diagnostik](./service-fabric-diagnostics-overview.md) är avgörande för att utveckla, testa och distribuera arbets belastningar i valfri moln miljö. Du kan till exempel spåra hur dina program används, de åtgärder som vidtas av Service Fabric plattform, din resursutnyttjande med prestanda räknare och det övergripande hälso tillståndet för klustret. Du kan använda den här informationen för att diagnostisera och åtgärda problem och förhindra att de inträffar i framtiden.

## <a name="application-monitoring"></a>Programövervakning

Program övervakningen spårar hur funktioner och komponenter i ditt program används. Övervaka dina program för att säkerställa att problem som påverkar dina användare fångas. Program övervakning är ansvaret för de som utvecklar programmet och dess tjänster, eftersom det är unikt för programmets affärs logik. Vi rekommenderar att du konfigurerar program övervakning med [Application Insights](./service-fabric-tutorial-monitoring-aspnet.md), Azures verktyg för program övervakning.

## <a name="cluster-monitoring"></a>Klusterövervakning

Ett av Service Fabricens mål är att göra program som är elastiska för maskin varu problem. Det här målet uppnås genom plattformens system tjänsters möjlighet att identifiera infrastruktur problem och snabbt redundansväxla arbets belastningar till andra noder i klustret. Men vad händer om system tjänsterna själva har problem? Eller om det vid ett försök att distribuera eller flytta en arbets belastning kränks regler för placering av tjänster? Service Fabric ger diagnostik för dessa, och andra problem, för att se till att du får information om hur Service Fabric plattform samverkar med dina program, tjänster, behållare och noder.

För Windows-kluster rekommenderar vi att du konfigurerar kluster övervakning med [Diagnostics agent](./service-fabric-diagnostics-event-aggregation-wad.md) och [Azure Monitor loggar](./service-fabric-diagnostics-oms-setup.md).

För Linux-kluster är Azure Monitor loggar även det rekommenderade verktyget för övervakning av Azure-plattform och infrastruktur. Linux Platform Diagnostics kräver en annan konfiguration som anges i [Service Fabric Linux-kluster händelser i syslog](./service-fabric-diagnostics-oms-syslog.md).

## <a name="infrastructure-monitoring"></a>Infrastrukturövervakning

[Azure Monitor loggar](./service-fabric-diagnostics-oms-agent.md) rekommenderas för att övervaka kluster nivå händelser. När du har konfigurerat Log Analytics-agenten med din arbets yta enligt beskrivningen i föregående länk, kommer du att kunna samla in prestanda mått som processor användning, .NET-prestanda räknare, till exempel processor användning på processnivå, Service Fabric prestanda räknare som # av undantag från en tillförlitlig tjänst och container mått som processor belastning.  Du måste skriva behållar loggar till stdout eller stderr så att de är tillgängliga i Azure Monitor loggar.

## <a name="watchdogs"></a>Övervaknings enhet

I allmänhet är en övervaknings enhet en separat tjänst som övervakar hälsa och belastning mellan tjänster, pingar slut punkter och rapporterar oväntade hälso händelser i klustret. Detta kan hjälpa till att förhindra fel som kanske inte identifieras baserat på prestanda för en enskild tjänst. Övervaknings enheter är också en bra plats för att vara värd för kod som utför åtgärder som inte kräver användar åtgärder, till exempel att rensa loggfiler i lagring vid vissa tidsintervall. Om du vill ha en fullständigt implementerad server med öppen källkod, som innehåller en lättanvänd modell med enkel användnings barhet och som körs i både Windows-och Linux-kluster, kan du läsa [FabricObserver](https://aka.ms/sf/FabricObserver) -projektet. FabricObserver är en produktions klar program vara. Vi rekommenderar att du distribuerar FabricObserver till dina test-och produktions kluster och utökar IT-behoven så att de passar dina behov genom sin plugin-modell eller genom att förgrena in den och skriva egna inbyggda observatörer. De tidigare (plugin-programmen) är den rekommenderade metoden.

## <a name="next-steps"></a>Nästa steg

* Kom igång med att instrumentera dina program: [händelse-och logg generering på program nivå](service-fabric-diagnostics-event-generation-app.md).
* Gå igenom stegen för att konfigurera Application Insights för ditt program med [övervaka och diagnostisera ett ASP.net Core program på Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Lär dig mer om att övervaka plattformen och de händelser Service Fabric tillhandahåller för dig: [händelse-och logg generering på plattforms nivå](service-fabric-diagnostics-event-generation-infra.md).
* Konfigurera Azure Monitor loggar integration med Service Fabric: [konfigurera Azure Monitor loggar för ett kluster](service-fabric-diagnostics-oms-setup.md)
* Lär dig hur du konfigurerar Azure Monitor loggar för övervaknings behållare: [övervakning och diagnostik för Windows-behållare i Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Se exempel på diagnostiska problem och lösningar med Service Fabric: [diagnosticera vanliga scenarier](service-fabric-diagnostics-common-scenarios.md)
* Lär dig mer om allmänna övervaknings rekommendationer för Azure-resurser: [bästa praxis – övervakning och diagnostik](/azure/architecture/best-practices/monitoring).
