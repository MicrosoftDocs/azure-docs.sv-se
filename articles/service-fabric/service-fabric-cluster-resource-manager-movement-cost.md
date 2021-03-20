---
title: 'Service Fabric Cluster Resource Manager: rörelse kostnad'
description: Läs om förflyttnings kostnaden för Service Fabric tjänster och hur den kan anges för att passa eventuella arkitektur behov, inklusive dynamisk konfiguration.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 0fdcfb02851d56ed996ae4bf32671ab545782733
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89005351"
---
# <a name="service-movement-cost"></a>Kostnad för tjänste rörelse
En faktor som Service Fabric Cluster Resource Manager tar hänsyn till när du försöker bestämma vilka ändringar som ska göras i ett kluster, kostnaden för dessa ändringar. Begreppet "kostnad" sker i handeln mot hur mycket klustret kan förbättras. Kostnaden är faktor i när du flyttar tjänster för balansering, defragmentering och andra krav. Målet är att uppfylla kraven på minst störande eller kostsamma sätt.

Flytt av tjänster kostar minst CPU-tid och nätverks bandbredd. För tillstånds känsliga tjänster kräver det att du kopierar status för dessa tjänster, vilket förbrukar ytterligare minne och disk. Att minimera kostnaden för lösningar som Azure Service Fabric Cluster Resource Manager kommer att ha, hjälper till att säkerställa att klustrets resurser inte förbrukas i onödan. Men du vill inte heller ignorera lösningar som avsevärt förbättrar tilldelningen av resurser i klustret.

Kluster resurs hanteraren har två sätt att beräkna kostnader och begränsa dem när det försöker hantera klustret. Den första mekanismen räknar bara varje flyttning som det skulle göra. Om två lösningar genereras med ungefär samma saldo (Poäng), föredrar kluster resurs hanteraren den som har den lägsta kostnaden (totalt antal flyttningar).

Den här strategin fungerar bra. Men precis som med standard eller statisk belastning är det osannolikt i komplexa system att alla flyttningar är lika. Vissa är förmodligen mycket dyrare.

## <a name="setting-move-costs"></a>Ange flytt kostnader 
Du kan ange standard flytt kostnad för en tjänst när den skapas:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C# 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Du kan också ange eller uppdatera MoveCost dynamiskt för en tjänst när tjänsten har skapats: 

PowerShell: 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Dynamiskt ange flytt kostnad per replik

Föregående kodfragment är alla för att ange MoveCost för en hel tjänst på samma gång utanför själva tjänsten. Däremot är flytt kostnaden mest användbar när flytt kostnaden för ett särskilt tjänst objekt ändras över dess livs längd. Eftersom tjänsterna i själva verket förmodligen har den bästa uppfattningen om hur dyra de ska flytta en viss tid, finns det ett API för tjänster för att rapportera sina egna enskilda flytt kostnader under körningen. 

C#

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Påverkan av flytt kostnad
MoveCost har fem nivåer: noll, låg, medel, hög och VeryHigh. Följande regler gäller:

* MoveCosts är i förhållande till varandra, förutom noll och VeryHigh. 
* Noll flytt kostnad innebär att rörelsen är kostnads fri och inte bör räknas mot poängen i lösningen.
* Att ange flytt kostnad till hög eller VeryHigh ger *ingen* garanti för att repliken *aldrig* kommer att flyttas.
* Repliker med VeryHigh flytt kostnad flyttas bara om det finns en begränsnings överträdelse i klustret som inte kan åtgärdas på något annat sätt (även om det krävs flera andra repliker för att åtgärda överträdelsen)



<center>

![Flytta kostnad som en faktor i välja repliker för förflyttning][Image1]
</center>

MoveCost hjälper dig att hitta de lösningar som orsakar minsta möjliga avbrott och som är lätta att uppnå samtidigt som du får ett motsvarande saldo. En tjänsts begreppet kostnad kan vara relativt många saker. De vanligaste faktorerna när du beräknar din flytt kostnad är:

- Den mängd tillstånd eller data som tjänsten måste flytta.
- Kostnaden för från koppling av klienter. Att flytta en primär replik är vanligt vis dyrare än kostnaden för att flytta en sekundär replik.
- Kostnaden för att avbryta en flyg åtgärd. Vissa åtgärder på data lager nivån eller åtgärder som utförs som svar på ett klient anrop är dyra. Efter en viss punkt vill du inte stoppa dem om du inte behöver det. När åtgärden pågår ökar du flytt kostnaden för detta tjänst objekt för att minska sannolikheten för att det flyttas. När åtgärden är färdig ställer du tillbaka kostnaden på normal.

> [!IMPORTANT]
> Att använda flytt kostnaden för VeryHigh bör övervägas noga eftersom det begränsar möjligheten för kluster resurs hanteraren att hitta en globalt optimal placerings lösning i klustret. Repliker med VeryHigh flytt kostnad flyttas bara om det finns en begränsnings överträdelse i klustret som inte kan åtgärdas på något annat sätt (även om det krävs flera andra repliker för att åtgärda överträdelsen)

## <a name="enabling-move-cost-in-your-cluster"></a>Aktivera flytt kostnader i klustret
För att kunna ta hänsyn till mer detaljerade MoveCosts måste MoveCost vara aktiverat i klustret. Utan den här inställningen används standard läget för att räkna antalet flyttningar för att beräkna MoveCost, och MoveCost-rapporter ignoreras.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

via ClusterConfig.jspå för fristående distributioner eller Template.jspå för Azure-värdbaserade kluster:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Nästa steg
- Service Fabric Cluster Resource Manager använder mått för att hantera förbrukning och kapacitet i klustret. Om du vill veta mer om mått och hur du konfigurerar dem kan du läsa [Hantera resursförbrukning och belastning i Service Fabric med mått](service-fabric-cluster-resource-manager-metrics.md).
- Mer information om hur kluster resurs hanteraren hanterar och balanserar belastningen i klustret finns i [balansera Service Fabric klustret](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
