---
title: Defragmentering av mått i Azure Service Fabric
description: Lär dig mer om att använda defragmentering eller packning som en strategi för Mät värden i Service Fabric. Den här tekniken är användbar för mycket stora tjänster.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6dee9a845df305b06153d99c2bafe6d74524a139
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449211"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Defragmentering av mått och belastning i Service Fabric
Den Service Fabric kluster resurs hanterarens standard strategi för att hantera inläsnings mått i klustret är att distribuera belastningen. Att se till att noderna används jämnt och förhindrar frekventa och kalla fläckar som leder till både konkurrens och slöseri med resurser. Att distribuera arbets belastningar i klustret är också det säkraste när det gäller kvarvarande fel eftersom det garanterar att ett fel inte tar ut en stor del av en viss arbets belastning. 

Service Fabric Cluster Resource Manager har stöd för en annan strategi för att hantera belastning, vilket är defragmentering. Defragmentering innebär att det konsol IDE ras i stället för att försöka distribuera ett mått över hela klustret. Konsolidering är bara en inversion av strategin för standard balansering – i stället för att minimera den genomsnittliga standard avvikelsen för mått belastningen försöker kluster resurs hanteraren öka den.

## <a name="when-to-use-defragmentation"></a>När defragmentering ska användas
Att distribuera belastning i klustret förbrukar några av resurserna på varje nod. Vissa arbets belastningar skapar tjänster som är undantagna stora och använder många eller alla noder. I dessa fall är det möjligt att när det finns stora arbets belastningar som skapar att det inte finns tillräckligt med utrymme på noderna för att köra dem. Stora arbets belastningar är inte ett problem i Service Fabric. i dessa fall avgör kluster resurs hanteraren att den måste organisera om klustret för att göra plats för den här stora arbets belastningen. Men under tiden kan arbets belastningen Vänta på att schemaläggas i klustret.

Om det finns många tjänster och tillstånd att flytta runt, kan det ta lång tid för den stora arbets belastningen att placeras i klustret. Detta är mer sannolikt om andra arbets belastningar i klustret också är stora och det tar längre tid att organisera om dem. Service Fabrics gruppens uppmätta tider i simuleringar av det här scenariot. Vi hittade att det tog mycket längre tid att skapa stora tjänster så snart kluster användningen var över 30% och 50%. För att hantera det här scenariot introducerade vi defragmentering som en balans strategi. Vi har identifierat att för stora arbets belastningar, särskilt där skapandet av tiden var viktigt, och defragmentering hjälpte de nya arbets belastningarna att bli schemalagda i klustret.

Du kan konfigurera defragmentering-mått om du vill att kluster resurs hanteraren ska försöka att komprimera belastningen för tjänsterna till färre noder. Detta säkerställer att det nästan alltid finns utrymme för stora tjänster utan att organisera om klustret. Om du inte behöver organisera om klustret kan du snabbt skapa stora arbets belastningar.

De flesta personer behöver inte defragmentera. Tjänsterna är vanligt vis små, så det är inte svårt att hitta plats för dem i klustret. När omstrukturering är möjlig går det snabbt, eftersom de flesta tjänsterna är små och kan flyttas snabbt och parallellt. Men om du har stora tjänster och behöver dem skapade snabbt, är defragmentering-strategin för dig. Vi diskuterar kompromisserna med att använda defragmentering härnäst. 

## <a name="defragmentation-tradeoffs"></a>Defragmentering av kompromisser
Defragmentering kan öka impactfulness, eftersom fler tjänster körs på noder som Miss lyckas. Defragmentering kan också öka kostnaderna, eftersom resurser i klustret måste hållas i reserv, vilket väntar på att skapa stora arbets belastningar.

Följande diagram ger en visuell representation av två kluster, ett som defragmenterar och ett som inte är det. 

<center>

![Jämföra balanserade och defragmentna kluster][Image1]
</center>

I det balanserade fallet bör du fundera över antalet transporter som skulle vara nödvändiga för att placera ett av de största tjänst objekten. I det defragmenterade klustret kan stora arbets belastningar placeras på noderna fyra eller fem utan att behöva vänta på att andra tjänster flyttas.

## <a name="defragmentation-pros-and-cons"></a>Defragmentering av proffs och nack delar
Vad är de andra koncepten kompromisser? Här är en snabb tabell med saker att tänka på:

| Defragmentering-proffs | Defragmentering av nack delar |
| --- | --- |
| Tillåter snabbare skapande av stora tjänster |Koncentrerar belastningen till färre noder och ökar konkurrens |
| Aktiverar lägre data förflyttning under skapandet |Fel kan påverka fler tjänster och orsaka mer omsättning |
| Tillåter utförlig beskrivning av krav och regenerering av utrymme |Mer komplex konfiguration av resurs hantering |

Du kan blanda defragmentade och normala mått i samma kluster. Kluster resurs hanteraren försöker konsolidera defragmentning-måtten så mycket som möjligt vid spridning av de andra. Resultaten av att blanda defragmentering och balansera strategier beror på flera faktorer, inklusive:
  - antalet Utjämnings mått jämfört med antalet defragmentering-mått
  - Oavsett om tjänsten använder båda typerna av mått 
  - måttets vikt
  - aktuella mått inläsningar
  
Experimentering krävs för att fastställa den exakta konfiguration som krävs. Vi rekommenderar en noggrann mätning av dina arbets belastningar innan du aktiverar defragmentering av mått i produktionen. Detta gäller särskilt för att blanda defragmentering och balanserade mått i samma tjänst. 

## <a name="configuring-defragmentation-metrics"></a>Konfigurera defragmentering-mått
Konfigurering av defragmentering-mått är ett globalt beslut i klustret och enskilda mått kan väljas för defragmentering. Följande config-kodfragment visar hur du konfigurerar mått för defragmentering. I det här fallet är "Metric1" konfigurerat som ett defragmentering-mått, medan "Metric2" fortsätter att bal anse ras som vanligt. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

via ClusterConfig.jspå för fristående distributioner eller Template.jspå för Azure-värdbaserade kluster:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Metric1",
          "value": "true"
      },
      {
          "name": "Metric2",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Nästa steg
- Kluster resurs hanteraren har många alternativ för att beskriva klustret. Läs mer om dem i den här artikeln om hur du [beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)
- Mått är hur Service Fabric Cluster Resource Manager hanterar förbrukning och kapacitet i klustret. Mer information om mått och hur du konfigurerar dem finns i [den här artikeln](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
