---
title: Händelse lista för Azure Service Fabric
description: En omfattande lista med händelser från Azure Service Fabric som hjälper dig att övervaka kluster.
author: srrengar
ms.topic: reference
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: e69b407bc7d58a83616daa44272ec008ccff9fad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85846646"
---
# <a name="list-of-service-fabric-events"></a>Lista över Service Fabric händelser 

Service Fabric visar en primär uppsättning kluster händelser som informerar dig om klustrets status som [Service Fabric händelser](service-fabric-diagnostics-events.md). De är baserade på åtgärder som utförs av Service Fabric på noderna och ditt kluster eller hanterings beslut från en kluster ägare/-operatör. Dessa händelser kan nås genom att konfigurera på ett antal sätt, t. ex. Konfigurera [Azure Monitor loggar med klustret](service-fabric-diagnostics-oms-setup.md)eller fråga [EventStore](service-fabric-diagnostics-eventstore.md). På Windows-datorer matas de här händelserna in i EventLog – så att du kan se Service Fabric händelser i Loggboken. 

Här följer några egenskaper för dessa händelser
* Varje händelse är kopplad till en specifik entitet i klustret, t. ex. program, tjänst, nod, replik.
* Varje händelse innehåller en uppsättning gemensamma fält: EventInstanceId, EventName och Category.
* Varje händelse innehåller fält som binder tillbaka händelsen till den entitet som den är kopplad till. Till exempel skulle ApplicationCreated-händelsen ha fält som identifierar namnet på programmet som skapats.
* Händelser är strukturerade på ett sådant sätt att de kan användas i en rad olika verktyg för ytterligare analys. Dessutom definieras relevant information för en händelse som separata egenskaper i stället för en lång sträng. 
* Händelser skrivs av olika del system i Service Fabric identifieras av källan (aktivitet) nedan. Mer information finns i dessa under system i [Service Fabric arkitektur](service-fabric-architecture.md) och [Service Fabric teknisk översikt](service-fabric-technical-overview.md).

Här är en lista över dessa Service Fabric händelser ordnade efter enhet.

## <a name="cluster-events"></a>Klusterhändelser

**Kluster uppgraderings händelser**

Mer information om kluster uppgraderingar hittar du [här](service-fabric-cluster-upgrade-windows-server.md).

| EventId | Name | Kategori | Beskrivning |Källa (aktivitet) | Nivå | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Uppgradera | En kluster uppgradering har startat | CM | Information |
| 29628 | ClusterUpgradeCompleted | Uppgradera | En kluster uppgradering har slutförts | CM | Information | 
| 29629 | ClusterUpgradeRollbackStarted | Uppgradera | En kluster uppgradering har börjat återställas  | CM | Varning | 
| 29630 | ClusterUpgradeRollbackCompleted | Uppgradera | En kluster uppgradering har återställts | CM | Varning | 
| 29631 | ClusterUpgradeDomainCompleted | Uppgradera | Uppgraderingen av en uppgraderings domän har avslut ATS under en kluster uppgradering | CM | Information | 

## <a name="node-events"></a>Node-händelser

**Händelse för nodens livs cykel** 

| EventId | Name | Kategori | Beskrivning |Källa (aktivitet) | Nivå |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | Malltypen statetransition | Inaktive ringen av en nod har slutförts | FM | Information | 
| 18603 | NodeUp | Malltypen statetransition | Klustret har upptäckt att en nod har startat | FM | Information | 
| 18604 | NodeDown | Malltypen statetransition | Klustret har upptäckt att en nod har avslut ATS. Under en omstart av en nod visas en NodeDown-händelse följt av en NodeUp-händelse |  FM | Fel | 
| 18605 | NodeAddedToCluster | Malltypen statetransition |  En ny nod har lagts till i klustret och Service Fabric kan distribuera program till den här noden | FM | Information | 
| 18606 | NodeRemovedFromCluster | Malltypen statetransition |  En nod har tagits bort från klustret. Service Fabric kommer inte längre att distribuera program till den här noden | FM | Information | 
| 18607 | NodeDeactivateStarted | Malltypen statetransition |  Inaktive ring av en nod har startat | FM | Information | 
| 25621 | NodeOpenSucceeded | Malltypen statetransition |  En nod har startats | FabricNode | Information | 
| 25622 | NodeOpenFailed | Malltypen statetransition |  En nod kunde inte starta och ansluta till ringen | FabricNode | Fel | 
| 25624 | NodeClosed | Malltypen statetransition |  En nod har stängts av | FabricNode | Information | 
| 25626 | NodeAborted | Malltypen statetransition |  En nod har avslut ATS på ett städat sätt | FabricNode | Fel | 

## <a name="application-events"></a>Program händelser

**Livs cykel händelser för program**

| EventId | Name | Kategori | Beskrivning |Källa (aktivitet) | Nivå | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | – | Ett nytt program har skapats | CM | Information | 
| 29625 | ApplicationDeleted | – | Ett befintligt program har tagits bort | CM | Information | 
| 23083 | ApplicationProcessExited | – | En process i ett program har avslut ATS | Värd | Information | 

**Program uppgraderings händelser**

Mer information om program uppgraderingar hittar du [här](service-fabric-application-upgrade.md).

| EventId | Name | Kategori | Beskrivning |Källa (aktivitet) | Nivå | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Uppgradera | En program uppgradering har startat | CM | Information | 
| 29622 | ApplicationUpgradeCompleted | Uppgradera | En program uppgradering har slutförts | CM | Information | 
| 29623 | ApplicationUpgradeRollbackStarted | Uppgradera | En program uppgradering har börjat återställas |CM | Varning | 
| 29624 | ApplicationUpgradeRollbackCompleted | Uppgradera | En program uppgradering har slutfört återställning | CM | Varning | 
| 29626 | ApplicationUpgradeDomainCompleted | Uppgradera | En uppgraderings domän har avslutat uppgraderingen under en program uppgradering | CM | Information | 

## <a name="service-events"></a>Tjänst händelser

**Tjänste livs cykel händelser**

| EventId | Name | Kategori | Beskrivning |Källa (aktivitet) | Nivå | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | – | En ny tjänst har skapats | FM | Information | 
| 18658 | ServiceDeleted | – | En befintlig tjänst har tagits bort | FM | Information | 

## <a name="partition-events"></a>Partitionera händelser

**Flytta händelser för partition**

| EventId | Name | Kategori | Beskrivning |Källa (aktivitet) | Nivå | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | – | En omkonfiguration av partitionen har slutförts | RA | Information | 

## <a name="replica-events"></a>Replik händelser

**Händelser för replik livs cykel**

| EventId | Name | Kategori | Beskrivning |Källa (aktivitet) | Nivå |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | – | En tillförlitlig ord lista har öppnats | DistributedDictionary | Information |
| 61702 | ReliableDictionaryClosed | – | Tillförlitlig ord lista har stängts | DistributedDictionary | Information |
| 61703 | ReliableDictionaryCheckpointRecovered | – | En tillförlitlig ord lista har återställt kontroll punkten | DistributedDictionary | Information |
| 61704 | ReliableDictionaryCheckpointFilesSent | – | Repliken har skickat en tillförlitlig ord lista med Checkpoint-filer | DistributedDictionary | Information |
| 61705 | ReliableDictionaryCheckpointFilesReceived | – | Repliken har tagit emot en tillförlitlig ord lista med Checkpoint-filer | DistributedDictionary | Information |
| 61963 | ReliableQueueOpened | – | Reliable Queue har öppnats | DistributedQueue | Information |
| 61964 | ReliableQueueClosed | – | Den Reliable kön har stängts | DistributedQueue | Information |
| 61965 | ReliableQueueCheckpointRecovered | – | Den tillförlitliga kön har återställt kontroll punkten | DistributedQueue | Information |
| 61966 | ReliableQueueCheckpointFilesSent | – | Repliken har skickat en tillförlitlig kö med kontroll punkts filer | DistributedQueue | Information |
| 63647 | ReliableQueueCheckpointFilesReceived | – | Repliken har tagit emot en tillförlitlig kö med Checkpoint-filer | DistributedQueue | Information |
| 63648 | ReliableConcurrentQueueOpened | – | Den tillförlitliga samtidiga kön har öppnats | ReliableConcurrentQueue | Information |
| 63649 | ReliableConcurrentQueueClosed | – | Den tillförlitliga samtidiga kön har stängts | ReliableConcurrentQueue | Information |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | – | Den tillförlitliga samtidiga kön har återställt kontroll punkten | ReliableConcurrentQueue | Information |
| 61687 | TStoreError | Fel | En tillförlitlig samling fick ett oväntat fel | TStore | Fel |
| 63831 | PrimaryFullCopyInitiated | – | Den primära repliken har initierat en fullständig kopia | TReplicator | Information |
| 63832 | PrimaryPartialCopyInitiated | – | Den primära repliken har initierat en partiell kopia | TReplicator | Information |
| 16831 | BuildIdleReplicaStarted | – | Den primära repliken har börjat skapa en inaktiv replik | Replikering | Information |
| 16832 | BuildIdleReplicaCompleted | – | Den primära repliken har slutfört skapandet av inaktiv replik | Replikering | Information |
| 16833 | BuildIdleReplicaFailed | – | Den primära repliken har inte kunnat skapa en inaktiv replik | Replikering | Varning |
| 16834 | PrimaryReplicationQueueFull | Hälsa | Den primära replikens transaktionskö är full | Replikering | Varning |
| 16835 | PrimaryReplicationQueueWarning | Hälsa | Den primära replikens transaktionskö är nästan full | Replikering | Varning |
| 16836 | PrimaryReplicationQueueWarningMitigated | Hälsa | Den primära replikens transaktionskö är OK | Replikering | Information |
| 16837 | SecondaryReplicationQueueFull | Hälsa | Den sekundära replikens transaktionskö är full | Replikering | Varning |
| 16838 | SecondaryReplicationQueueWarning | Hälsa | Den sekundära replikens transaktionskö är nästan full | Replikering | Varning |
| 16839 | SecondaryReplicationQueueWarningMitigated | Hälsa | Den sekundära replikens replikeringsfil är OK | Replikering | Information |
| 16840 | PrimaryFaultedSlowSecondary | Hälsa | Den primära repliken har trasigt en långsam sekundär replik | Replikering | Varning |
| 16841 | ReplicatorFaulted | Hälsa | Repliken har fel | Replikering | Varning |

## <a name="container-events"></a>Container händelser

**Livs cykel händelser för behållare** 

| EventId | Name | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | En behållare har startat | Värd | Information | 1 |
| 23075 | ContainerDeactivated | En behållare har stoppats | Värd | Information | 1 |
| 23082 | ContainerExited | En behållare har avslut ATS-kontrol lera flaggan UnexpectedTermination | Värd | Information | 1 |

## <a name="health-reports"></a>Hälso rapporter

[Service Fabric hälso modellen](service-fabric-health-introduction.md) innehåller en omfattande, flexibel och utöknings bar hälso utvärdering och rapportering. Från Service Fabric version 6,2 är hälso data skrivna som plattforms händelser för att tillhandahålla historiska hälso tillstånd. För att hålla volymen av hälso tillstånds händelser låg skriver vi bara följande som Service Fabric händelser:

* Alla `Error` eller `Warning` hälso rapporter
* `Ok` hälso rapporter vid över gångar
* När en `Error` eller `Warning` hälso tillstånds händelse upphör att gälla. Detta kan användas för att avgöra hur länge en enhet var felfri

**Hälso rapport händelser i kluster**

| EventId | Name | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Det finns en ny kluster hälso rapport | IGNERINGSALGORITM | Information | 1 |
| 54437 | ClusterHealthReportExpired | En befintlig hälso rapport för klustret har upphört att gälla | IGNERINGSALGORITM | Information | 1 |

**Händelser för nodens hälso rapport**

| EventId | Name | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | En ny hälso rapport för noden är tillgänglig | IGNERINGSALGORITM | Information | 1 |
| 54432 | NodeHealthReportExpired | En befintlig hälso rapport för noden har upphört att gälla | IGNERINGSALGORITM | Information | 1 |

**Händelser för program hälso rapport**

| EventId | Name | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | En ny program hälso rapport har skapats. Detta är för program som inte har distribuerats. | IGNERINGSALGORITM | Information | 1 |
| 54426 | DeployedApplicationNewHealthReport | En ny distribuerad program hälso rapport skapades | IGNERINGSALGORITM | Information | 1 |
| 54427 | DeployedServicePackageNewHealthReport | En ny distribuerad tjänst hälso rapport skapades | IGNERINGSALGORITM | Information | 1 |
| 54434 | ApplicationHealthReportExpired | En befintlig program hälso rapport har upphört att gälla | IGNERINGSALGORITM | Information | 1 |
| 54435 | DeployedApplicationHealthReportExpired | En befintlig distribuerad program hälso rapport har upphört att gälla | IGNERINGSALGORITM | Information | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | En befintlig distribuerad tjänst hälso rapport har upphört att gälla | IGNERINGSALGORITM | Information | 1 |

**Händelser för service Health-rapport**

| EventId | Name | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | En ny tjänst hälso rapport skapades | IGNERINGSALGORITM | Information | 1 |
| 54433 | ServiceHealthReportExpired | En befintlig tjänst hälso rapport har upphört att gälla | IGNERINGSALGORITM | Information | 1 |

**Partitionera hälso rapport händelser**

| EventId | Name | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | En ny hälso rapport för partition skapades | IGNERINGSALGORITM | Information | 1 |
| 54431 | PartitionHealthReportExpired | En befintlig hälso rapport för partitionen har upphört att gälla | IGNERINGSALGORITM | Information | 1 |

**Hälso rapport händelser för replik**

| EventId | Name | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | En hälso rapport för tillstånds känslig replik skapades | IGNERINGSALGORITM | Information | 1 |
| 54430 | StatelessInstanceNewHealthReport | En ny tillstånds lös instans hälso rapport skapades | IGNERINGSALGORITM | Information | 1 |
| 54438 | StatefulReplicaHealthReportExpired | En befintlig hälso rapport för tillstånds känslig replik har upphört att gälla | IGNERINGSALGORITM | Information | 1 |
| 54439 | StatelessInstanceHealthReportExpired | En befintlig tillstånds lös instans hälso rapport har upphört att gälla | IGNERINGSALGORITM | Information | 1 |

## <a name="chaos-testing-events"></a>Kaos test händelser 

**Kaos**

| EventId | Name | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | En kaos-testsession har startats | Testnings | Information | 1 |
| 50023 | ChaosStopped | En kaos-testsession har stoppats | Testnings | Information | 1 |

**Kaos Node-händelser**

| EventId | Name | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | En nod har schemalagts för omstart som en del av en kaos-testsession | Testnings | Information | 1 |
| 50087 | ChaosNodeRestartCompleted | En nod har avslutat omstarten som en del av en kaos-test-session | Testnings | Information | 1 |

**Kaos program händelser**

| EventId | Name | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | En omstart av kod paketet har schemalagts under en kaos-testsession | Testnings | Information | 1 |
| 50101 | ChaosCodePackageRestartCompleted | En omstart av kod paketet har slutförts under en kaos-testsession | Testnings | Information | 1 |

**Kaos-partitionerings händelser**

| EventId | Name | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | En primär partition har schemalagts för att flyttas som en del av en kaos-testsession | Testnings | Information | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | En sekundär partition har schemalagts för att flyttas som en del av en kaos-testsession | Testnings | Information | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Djupgående analysen av den primära partitionen flyttas är tillgänglig | Testnings | Information | 1 |

**Kaos replik händelser**

| EventId | Name | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | En replik omstart har schemalagts som en del av en kaos-testsession | Testnings | Information | 1 |
| 50051 | ChaosReplicaRemovalScheduled | En replik borttagning har schemalagts som en del av en kaos-test-session | Testnings | Information | 1 |
| 50093 | ChaosReplicaRemovalCompleted | En replik borttagning har slutförts som en del av en kaos-test-session | Testnings | Information | 1 |

## <a name="other-events"></a>Andra händelser

**Korrelations händelser**

| EventId | Name | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | En korrelation har identifierats | Testnings | Information | 1 |

## <a name="events-prior-to-version-62"></a>Händelser före version 6,2

Här är en omfattande lista över händelser som tillhandahålls av Service Fabric före version 6,2.

| EventId | Name | Källa (aktivitet) | Nivå |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Information |
| 25621 | NodeOpenedSuccess | FabricNode | Information |
| 25622 | NodeOpenedFailed | FabricNode | Information |
| 25623 | NodeClosing | FabricNode | Information |
| 25624 | NodeClosed | FabricNode | Information |
| 25625 | NodeAborting | FabricNode | Information |
| 25626 | NodeAborted | FabricNode | Information |
| 29627 | ClusterUpgradeStart | CM | Information |
| 29628 | ClusterUpgradeComplete | CM | Information |
| 29629 | ClusterUpgradeRollback | CM | Information |
| 29630 | ClusterUpgradeRollbackComplete | CM | Information |
| 29631 | ClusterUpgradeDomainComplete | CM | Information |
| 23074 | ContainerActivated | Värd | Information |
| 23075 | ContainerDeactivated | Värd | Information |
| 29620 | ApplicationCreated | CM | Information |
| 29621 | ApplicationUpgradeStart | CM | Information |
| 29622 | ApplicationUpgradeComplete | CM | Information |
| 29623 | ApplicationUpgradeRollback | CM | Information |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Information |
| 29625 | ApplicationDeleted | CM | Information |
| 29626 | ApplicationUpgradeDomainComplete | CM | Information |
| 18566 | ServiceCreated | FM | Information |
| 18567 | ServiceDeleted | FM | Information |

## <a name="next-steps"></a>Nästa steg

* Få en översikt över [diagnostik i Service Fabric](service-fabric-diagnostics-overview.md)
* Läs mer om EventStore i [Service Fabric EventStore-översikt](service-fabric-diagnostics-eventstore.md)
* Ändra [Azure-diagnostik](service-fabric-diagnostics-event-aggregation-wad.md) -konfigurationen för att samla in fler loggar
* Konfigurera [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) för att se dina drifts kanal loggar
