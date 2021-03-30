---
title: Begränsning i Service Fabric Cluster Resource Manager
description: Lär dig hur du konfigurerar de begränsningar som tillhandahålls av Service Fabric Cluster Resource Manager.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b4d78b339bab02b5c44a31939e0da769dc21c3ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75452163"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Begränsa Service Fabric Cluster Resource Manager
Även om du har konfigurerat kluster resurs hanteraren på rätt sätt kan klustret brytas. Det kan till exempel finnas samtidiga nod-och fel domän fel – vad skulle hända om det inträffade under en uppgradering? Kluster resurs hanteraren försöker alltid åtgärda allt, vilket förbrukar kluster resurser som försöker omorganisera och åtgärda klustret. Begränsningar bidrar till att ge ett avbrott så att klustret kan använda resurser för att stabilisera-noderna kommer tillbaka, vilka nätverks partitioner som korrigeras, korrigerade BITS-distribution.

För att hjälpa till med dessa typer av situationer innehåller Service Fabric Cluster Resource Manager flera begränsningar. Dessa begränsningar är alla ganska stora hammare. De bör vanligt vis inte ändras utan noggrann planering och testning.

Om du ändrar kluster resurs hanterarens begränsningar bör du justera dem till den förväntade faktiska belastningen. Du kan bestämma att du behöver ha vissa begränsningar på plats, även om det innebär att klustret tar längre tid att stabilisera i vissa situationer. Testning krävs för att fastställa korrekta värden för begränsningar. Begränsningarna måste vara tillräckligt höga för att tillåta att klustret svarar på förändringar under en rimlig tid och är tillräckligt lågt för att förhindra för mycket resursförbrukning. 

I de flesta fall har vi sett kundernas användnings begränsningar eftersom de redan finns i en resurs begränsad miljö. Vissa exempel skulle vara begränsade nätverks bandbredd för enskilda noder eller diskar som inte kan bygga flera tillstånds känsliga repliker parallellt på grund av data flödes begränsningar. Utan begränsningar kan åtgärder överbelasta dessa resurser, vilket leder till att åtgärder inte fungerar eller är långsamma. I dessa fall har kunderna använt sig av begränsningar och visste att de skulle ta klustret att uppnå ett stabilt tillstånd. Kunderna förstår också att de kan få en lägre övergripande tillförlitlighet medan de var begränsade.


## <a name="configuring-the-throttles"></a>Konfigurera begränsningar

Service Fabric har två mekanismer för att begränsa antalet replik förflyttningar. Standard mekanismen som fanns före Service Fabric 5,7 representerar begränsning som ett absolut antal tillåtna flyttningar. Detta fungerar inte för kluster av alla storlekar. För stora kluster kan standardvärdet vara för litet, vilket avsevärt saktar ned balanseringen även när det är nödvändigt, men inte har någon påverkan på mindre kluster. Den här tidigare mekanismen har ersatts av procent-baserad begränsning, som skalar bättre med dynamiska kluster där antalet tjänster och noder ändras regelbundet.

Begränsningarna baseras på en procent andel av antalet repliker i klustren. Procent baserade begränsningar aktivera uttrycker regeln: "flytta inte fler än 10% av replikerna inom ett 10-minuters intervall", till exempel.

Konfigurations inställningarna för procent baserad begränsning är:

  - GlobalMovementThrottleThresholdPercentage – maximalt antal förflyttningar som tillåts i kluster, uttryckt i procent av det totala antalet repliker i klustret. 0 anger ingen gräns. Standardvärdet är 0. Om både den här inställningen och GlobalMovementThrottleThreshold anges, används den mer restriktiva gränsen.
  - GlobalMovementThrottleThresholdPercentageForPlacement – maximalt antal förflyttningar som tillåts under placerings fasen, uttryckt i procent av det totala antalet repliker i klustret. 0 anger ingen gräns. Standardvärdet är 0. Om både den här inställningen och GlobalMovementThrottleThresholdForPlacement anges, används den mer restriktiva gränsen.
  - GlobalMovementThrottleThresholdPercentageForBalancing – maximalt antal förflyttningar som tillåts under balans fasen, uttryckt i procent av det totala antalet repliker i klustret. 0 anger ingen gräns. Standardvärdet är 0. Om både den här inställningen och GlobalMovementThrottleThresholdForBalancing anges, används den mer restriktiva gränsen.

När du anger begränsnings procenten anger du 5% som 0,05. Intervallet som dessa begränsningar styrs av är GlobalMovementThrottleCountingInterval, som anges i sekunder.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

via ClusterConfig.jspå för fristående distributioner eller Template.jspå för Azure-värdbaserade kluster:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThresholdPercentage",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForPlacement",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForBalancing",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

### <a name="default-count-based-throttles"></a>Standardbaserat antal baserade begränsningar
Den här informationen anges om du har äldre kluster eller om du fortfarande behåller dessa konfigurationer i kluster som har uppgraderats. I allmänhet rekommenderar vi att de ersätts med de procentbaserade begränsningarna ovan. Eftersom procent baserat begränsning är inaktiverat som standard förblir de här begränsningarna standard begränsningar för ett kluster tills de har inaktiverats och ersatts med de procentbaserade begränsningarna. 

  - GlobalMovementThrottleThreshold – den här inställningen styr det totala antalet förflyttningar i klustret under en viss tid. Tids mängden anges i sekunder som GlobalMovementThrottleCountingInterval. Standardvärdet för GlobalMovementThrottleThreshold är 1000 och standardvärdet för GlobalMovementThrottleCountingInterval är 600.
  - MovementPerPartitionThrottleThreshold – den här inställningen styr det totala antalet förflyttningar för en tjänstmall under en viss tid. Tids mängden anges i sekunder som MovementPerPartitionThrottleCountingInterval. Standardvärdet för MovementPerPartitionThrottleThreshold är 50 och standardvärdet för MovementPerPartitionThrottleCountingInterval är 600.

Konfigurationen för dessa begränsningar följer samma mönster som den procentbaserade begränsningen.

## <a name="next-steps"></a>Nästa steg
- Om du vill veta mer om hur kluster resurs hanteraren hanterar och balanserar belastningen i klustret kan du läsa artikeln om [balansering av belastning](service-fabric-cluster-resource-manager-balancing.md)
- Kluster resurs hanteraren har många alternativ för att beskriva klustret. Läs mer om dem i den här artikeln om hur du [beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)
