---
title: Check lista för Azure Service Fabric Production readiness
description: Förbered din Service Fabric program-och kluster produktion genom att följa bästa praxis.
ms.topic: conceptual
ms.date: 6/05/2019
ms.openlocfilehash: a03df40a8ce213c5de9ed7017d47713c4de3449d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87835875"
---
# <a name="production-readiness-checklist"></a>Checklista för produktionsberedskap

Är ditt program och kluster redo att ta produktions trafik? Att köra och testa ditt program och ditt kluster innebär inte nödvändigt vis att det är klart att sätta igång i produktionen. Se till att ditt program och kluster körs smidigt genom att gå igenom följande check lista. Vi rekommenderar starkt att alla dessa objekt checkas ut. Självklart kan du välja att använda alternativa lösningar för ett visst rad objekt (till exempel egna diagnostik-ramverk).


## <a name="prerequisites-for-production"></a>Krav för produktion
1. Metod tips för Azure Service Fabric: [program design](./service-fabric-best-practices-applications.md), [säkerhet](./service-fabric-best-practices-security.md), [nätverk](./service-fabric-best-practices-networking.md), [kapacitets planering och skalning](./service-fabric-best-practices-capacity-scaling.md), [infrastruktur som kod](./service-fabric-best-practices-infrastructure-as-code.md)och [övervakning och diagnostik](./service-fabric-best-practices-monitoring.md). 
1. [Konfigurera FabricTransport-inställningar](./service-fabric-reliable-actors-fabrictransportsettings.md) om du använder programmerings modellen för Reliable Actors och kräver säker kommunikation mellan tjänster.
1. För kluster med fler än 20 kärnor eller 10 noder skapar du en dedikerad typ av primär nod för system tjänster. Lägg till [placerings begränsningar](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) för att reservera den primära nodtypen för system tjänster.
1. Använd en virtuella d2v2 eller en högre SKU för den primära nodtypen. Vi rekommenderar att du väljer en SKU med minst 50 GB hård disk kapacitet.
1. Produktions kluster måste vara [skyddade](service-fabric-cluster-security.md). Ett exempel på hur du konfigurerar ett säkert kluster finns i den här [kluster mal len](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Använd egna namn för certifikat och Undvik att använda självsignerade certifikat.
1. Lägg till [resurs begränsningar på behållare och tjänster](service-fabric-resource-governance.md), så att de inte förbrukar mer än 75% av Node-resurserna. 
1. Förstå och ange [hållbarhets nivån](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster). Den silver eller högre hållbarhets nivån rekommenderas för nodtyper som kör tillstånds känsliga arbets belastningar.
1. Förstå och välj [Tillförlitlighets nivån](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) för nodtypen. Silver eller högre tillförlitlighet rekommenderas.
1. Testa belastningen och skala dina arbets belastningar för att identifiera [kapacitets kraven](service-fabric-cluster-capacity.md) för klustret. 
1. Dina tjänster och program övervakas och program loggar skapas och lagras, med aviseringar. Se [till exempel Lägg till loggning i Service Fabric programmet](service-fabric-how-to-diagnostics-log.md) och [övervaka behållare med Azure Monitor loggar](service-fabric-diagnostics-oms-containers.md).
1. Klustret övervakas med avisering (till exempel med [Azure Monitor loggar](service-fabric-diagnostics-event-analysis-oms.md)). 
1. Den underliggande infrastrukturen för skalnings uppsättningar för virtuella datorer övervakas med aviseringar (till exempel med [Azure Monitor loggar](service-fabric-diagnostics-oms-agent.md).
1. Klustret har alltid [primära och sekundära certifikat](service-fabric-cluster-security-update-certs-azure.md) (så att du inte blir utelåst).
1. Underhålla separata kluster för utveckling, mellanlagring och produktion. 
1. [Program uppgraderingar](service-fabric-application-upgrade.md) och [kluster uppgraderingar](service-fabric-tutorial-upgrade-cluster.md) testas i utvecklings-och mellanlagrings kluster först. 
1. Inaktivera automatiska uppgraderingar i produktions kluster och aktivera dem för utveckling och mellanlagrings kluster (återställning efter behov). 
1. Upprätta en återställnings punkt mål (återställnings punkt) för din tjänst och konfigurera en [katastrof återställnings process](service-fabric-disaster-recovery.md) och testa den.
1. Planera för [skalning](service-fabric-cluster-scaling.md) av klustret manuellt eller program mässigt.
1. Planera för att [korrigera](service-fabric-patch-orchestration-application.md) klusternoder. 
1. Upprätta en CI/CD-pipeline så att dina senaste ändringar testas kontinuerligt. Till exempel med hjälp av [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) eller [Jenkins](/azure/developer/jenkins/deploy-to-service-fabric-cluster)
1. Testa din utvecklings & mellanlagrings kluster under belastning med [fel analys tjänsten](service-fabric-testability-overview.md) och inducera kontrollerade [kaos](service-fabric-controlled-chaos.md). 
1. Planera för [skalning](service-fabric-concepts-scalability.md) av dina program. 


Om du använder Service Fabric Reliable Services-eller Reliable Actors programmerings modellen måste följande objekt vara markerade:
1. Uppgradera program under lokal utveckling för att kontrol lera att din tjänst kod följer den token för uppsägning i- `RunAsync` metoden och stänger anpassade kommunikations lyssnare.
1. Undvik [vanliga fall GRO par](service-fabric-work-with-reliable-collections.md) när du använder pålitliga samlingar.
1. Övervaka prestanda räknarna för .NET CLR-minne vid körning av belastnings test och kontrol lera om det finns höga hastigheter för skräp insamlingen eller utökning av heap.
1. Underhåll offline-säkerhetskopiering av [Reliable Services och Reliable Actors](service-fabric-reliable-services-backup-restore.md) och testa återställnings processen.
1. Antalet tillåtna instanser av virtuella NodeType-datorer bör helst motsvara det lägsta antalet för klustrets Tillförlitlighets nivå. villkor när det är lämpligt att överskrida nivån minimum omfattar: tillfälligt när du skalar den primära NodeTypes för skalnings uppsättningen för virtuella datorer vertikalt.

## <a name="optional-best-practices"></a>Valfria metod tips

Även om listorna ovan är för hands krav för att gå in i produktionen, bör följande saker övervägas:
1. Sätt i [Service Fabric Health-modellen](service-fabric-health-introduction.md) för att utöka den inbyggda hälso utvärderingen och rapporteringen.
1. Distribuera en anpassad övervaknings enhet som övervakar program-och rapport [belastningen](service-fabric-cluster-resource-manager-metrics.md) för [resurs balansering](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Nästa steg
* [Distribuera ett Service Fabric Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Distribuera ett Service Fabric Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Läs om Service Fabric-[applivscykeln](service-fabric-application-lifecycle.md).