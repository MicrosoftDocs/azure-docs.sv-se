---
title: Azure Service Fabric-händelser
description: Lär dig mer om Service Fabric-händelser som visas i rutan för att hjälpa dig att övervaka ditt Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 638b650e485ad3e83bd6021639a7e55b540d9cdc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75451732"
---
# <a name="service-fabric-events"></a>Service Fabric-händelser 

Service Fabric Platform skriver flera strukturerade händelser för viktiga operativa aktiviteter i klustret. Dessa intervall från kluster uppgraderingar till beslut om replik placering. Varje händelse som Service Fabric exponerar mappar till någon av följande entiteter i klustret:
* Kluster
* Program
* Tjänst
* Partition
* Replik 
* Container

Om du vill se en fullständig lista över händelser som exponeras av plattforms [listan över Service Fabric händelser](service-fabric-diagnostics-event-generation-operational.md).

Här följer några exempel på scenarier som du bör se händelser för i klustret. 
* Händelse för nodens livs cykel: när noderna kommer upp, går du nedåt, skalar in/ut, startar om och aktive ras/inaktive ras, visas dessa händelser och du får hjälp att identifiera om det är något fel med själva datorn eller om det har uppstått ett API som anropades via SF för att ändra status för en nod.
* Kluster uppgradering: När klustret uppgraderas (SF-version eller konfigurations ändring) ser du att uppgraderingen initieras, går igenom varje uppgraderings domän och slutför (eller återställnings). 
* Program uppgraderingar: precis som kluster uppgraderingar finns det en omfattande uppsättning händelser när uppgraderingen rullar igenom. Dessa händelser kan vara användbara för att förstå när en uppgradering har schemalagts, det aktuella läget för en uppgradering och den övergripande sekvensen av händelser. Detta är användbart för att se vilka uppgraderingar som har distribuerats eller om en återställning utlöstes.
* Distribution/borttagning av program/tjänst: det finns händelser för varje program, tjänst och behållare som skapas eller tas bort och är användbara när du skalar in eller ut, t. ex. ökar antalet repliker
* Partition flyttar (omkonfiguration): när en tillstånds känslig partition går genom en omkonfiguration (en ändring i replik uppsättningen) loggas en händelse. Detta är användbart om du vill förstå hur ofta din uppsättning av diskpartitioner ändras eller om du växlar över, eller spåra vilken nod som körde den primära repliken vid någon tidpunkt.
* Kaos-händelser: när du använder Service Fabric [kaos](service-fabric-controlled-chaos.md) -tjänsten visas händelser varje gång tjänsten startas eller stoppas, eller när ett fel matas in i systemet.
* Hälso händelser: Service Fabric exponerar hälso händelser varje gång en varning eller en fel hälso rapport skapas, eller om en enhet går tillbaka till ett hälso tillstånd som är OK eller om en hälso rapport upphör att gälla. Dessa händelser är mycket användbara för att spåra historisk hälso statistik för en entitet. 

## <a name="how-to-access-events"></a>Så här får du åtkomst till händelser

Det finns några olika sätt som Service Fabric händelser kan nås på:
* Händelserna loggas via standard kanaler som ETW/Windows-händelseloggar och kan visualiseras av alla övervaknings verktyg som har stöd för sådana som Azure Monitor loggar. Som standard har kluster som skapats i portalen diagnostik aktiverat och Windows Azure Diagnostics-agenten skickar händelserna till Azure Table Storage, men du måste fortfarande integrera detta med Log Analytics-resursen. Läs mer om hur du konfigurerar [Azure-diagnostik agent](service-fabric-diagnostics-event-aggregation-wad.md) för att ändra diagnostik-konfigurationen för klustret för att hämta fler loggar eller prestanda räknare och [integreringen med Azure Monitor loggar](service-fabric-diagnostics-event-analysis-oms.md)
* EventStore-tjänstens REST-API: er som gör att du kan köra frågor mot klustret direkt eller via Service Fabric klient bibliotek. Se [fråga EventStore-API: er för kluster händelser](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Nästa steg
* Mer information om övervakning av kluster – [övervakning av kluster och plattform](service-fabric-diagnostics-event-generation-infra.md).
* Läs mer om EventStore service- [EventStore service Overview](service-fabric-diagnostics-eventstore.md)
