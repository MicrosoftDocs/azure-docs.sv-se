---
title: Azure Service Fabric händelse lager
description: Lär dig mer om Azure Service Fabrics EventStore, ett sätt att förstå och övervaka status för ett kluster eller arbets belastningar när som helst.
ms.topic: conceptual
ms.date: 6/6/2019
ms.openlocfilehash: ef5049fd934a29fa1d96514c334b13358e6600cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626562"
---
# <a name="eventstore-overview"></a>Översikt över EventStore

>[!NOTE]
>Från och med Service Fabric version 6,4. EventStore-API: er är bara tillgängliga för Windows-kluster som körs på Azure. Vi arbetar på att hamna på den här funktionaliteten både i Linux och i våra fristående kluster.

## <a name="overview"></a>Översikt

EventStore-tjänsten introducerades i version 6,2 och är ett övervaknings alternativ i Service Fabric. EventStore är ett sätt att förstå tillstånd för ditt kluster eller arbets belastningar vid en viss tidpunkt.
EventStore är en tillstånds känslig Service Fabric tjänst som upprätthåller händelser från klustret. Händelsen exponeras via Service Fabric Explorer, REST och API: er. EventStore frågar klustret direkt för att hämta diagnostikdata för alla entiteter i klustret och ska användas för att hjälpa:

* Diagnostisera problem i utveckling eller testning eller där du kan använda en övervaknings pipeline
* Bekräfta att hanterings åtgärder som du vidtar i klustret bearbetas korrekt
* Få en "ögonblicks bild" av hur Service Fabric interagerar med en viss entitet

![Skärm bild som visar fliken händelser i fönstret noder flera händelser, inklusive en NodeDown-händelse.](media/service-fabric-diagnostics-eventstore/eventstore.png)

Om du vill se en fullständig lista över händelser som är tillgängliga i EventStore, se [Service Fabric händelser](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>Från och med Service Fabric version 6,4. EventStore-API: er och UX är allmänt tillgängliga för Azure Windows-kluster. Vi arbetar på att hamna på den här funktionaliteten både i Linux och i våra fristående kluster.

EventStore-tjänsten kan frågas efter händelser som är tillgängliga för varje entitet och entitetstyp i klustret. Det innebär att du kan fråga efter händelser på följande nivåer:
* Kluster: händelser som är speciella för själva klustret (t. ex. kluster uppgradering)
* Noder: alla händelser på radnivå
* Node: händelser som är speciella för en nod, identifieras av `nodeName`
* Program: alla händelser på program nivå
* Program: händelser som är specifika för ett program som identifieras av `applicationId`
* Tjänster: händelser från alla tjänster i klustren
* Tjänst: händelser från en angiven tjänst som identifieras av `serviceId`
* Partitioner: händelser från alla partitioner
* Partition: händelser från en speciell partition som identifieras av `partitionId`
* Partitions repliker: händelser från alla repliker/instanser inom en speciell partition som identifieras av `partitionId`
* Partitions replik: händelser från en speciell replik/instans som identifieras av `replicaId` och `partitionId`

Läs mer om API-kontrollen i [EVENTSTORE API-referensen](/rest/api/servicefabric/sfclient-index-eventsstore).

EventStore-tjänsten har också möjlighet att korrelera händelser i klustret. Genom att titta på händelser som skrivits samtidigt från olika entiteter som kan ha påverkat varandra, kan EventStore-tjänsten länka dessa händelser till hjälp med att identifiera orsaker till aktiviteter i klustret. Om ett av dina program till exempel verkar vara ohälsosamt utan att några inducerade ändringar görs, kommer EventStore också att titta på andra händelser som exponeras av plattformen och kan korrelera detta med en `Error` eller- `Warning` händelse. Detta hjälper till med snabbare identifiering av fel och Rotors Aker analys.

## <a name="enable-eventstore-on-your-cluster"></a>Aktivera EventStore i klustret

### <a name="local-cluster"></a>Lokalt kluster

I [fabricSettings.jsi klustret lägger du](service-fabric-cluster-fabric-settings.md)till EventStoreService som en tilläggs funktion och utför en kluster uppgradering.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Azure-kluster version 6.5 +
Om ditt Azure-kluster uppgraderas till version 6,5 eller senare, aktive ras EventStore automatiskt i klustret. Om du vill avanmäla dig måste du uppdatera kluster mal len med följande:

* Använd en API-version av `2019-03-01` eller senare 
* Lägg till följande kod i avsnittet Egenskaper i klustret
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Azure-kluster version 6,4

Om du använder version 6,4 kan du redigera din Azure Resource Manager-mall för att aktivera EventStore-tjänsten. Detta görs genom att köra en [kluster konfigurations uppgradering](service-fabric-cluster-config-upgrade-azure.md) och lägga till följande kod. du kan använda PlacementConstraints för att lagra replikerna av EventStore-tjänsten på en särskild NodeType, t. ex. en NodeType som är dedikerad för system tjänsterna. I `upgradeDescription` avsnittet konfigureras konfigurations uppgraderingen för att utlösa en omstart på noderna. Du kan ta bort avsnittet i en annan uppdatering.

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              },
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>Nästa steg
* Kom igång med EventStore-API: et [med hjälp av EventStore-API: er i Azure Service Fabric-kluster](service-fabric-diagnostics-eventstore-query.md)
* Läs mer om listan över händelser som erbjuds av EventStore- [Service Fabric händelser](service-fabric-diagnostics-event-generation-operational.md)
* Översikt över övervakning och diagnostik i Service Fabric [övervakning och diagnostik för Service Fabric](service-fabric-diagnostics-overview.md)
* Visa en fullständig lista över API-anrop – [EventStore REST API referens](/rest/api/servicefabric/sfclient-index-eventsstore)
* Lär dig mer om att övervaka klustret [och plattformen för att övervaka](service-fabric-diagnostics-event-generation-infra.md)klustret.
