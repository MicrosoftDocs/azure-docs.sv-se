---
title: Tjänster och behållare för automatisk skalning i Azure Service Fabric
description: Med Azure Service Fabric kan du ange principer för automatisk skalning för tjänster och behållare.
author: radicmilos
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: miradic
ms.custom: devx-track-csharp
ms.openlocfilehash: 060bb9dcdd504846c76ab4c782b2857fdddfa394
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91354810"
---
# <a name="introduction-to-auto-scaling"></a>Introduktion till automatisk skalning
Automatisk skalning är en ytterligare funktion i Service Fabric för att dynamiskt skala dina tjänster baserat på den belastning som tjänsterna rapporterar, eller baserat på deras användning av resurser. Automatisk skalning ger bra elastiskhet och möjliggör etablering av ytterligare instanser eller partitioner för din tjänst på begäran. Hela processen för automatisk skalning automatiseras och är transparent, och när du har konfigurerat dina principer på en tjänst behöver du inte heller manuella skalnings åtgärder på service nivå. Automatisk skalning kan aktive ras antingen när tjänsten skapas, eller när som helst genom att uppdatera tjänsten.

Ett vanligt scenario där automatisk skalning är användbart är när belastningen på en viss tjänst varierar över tid. Till exempel kan en tjänst, till exempel en gateway, skala utifrån den mängd resurser som krävs för att hantera inkommande begär Anden. Låt oss ta en titt på ett exempel på hur dessa skalnings regler kan se ut:
* Om alla instanser av min Gateway använder fler än två kärnor i genomsnitt skalar du Gateway-tjänsten genom att lägga till en instans. Gör detta varje timme, men har aldrig fler än sju instanser totalt.
* Om alla instanser av min Gateway använder mindre än 0,5 kärnor i genomsnitt, skala sedan tjänsten i genom att ta bort en instans. Gör detta varje timme, men har aldrig färre än tre instanser.

Automatisk skalning stöds för både behållare och vanliga Service Fabric tjänster. För att kunna använda automatisk skalning måste du köra på version 6,2 eller senare av Service Fabric Runtime. 

Resten av den här artikeln beskriver skalnings principerna, sätt att aktivera eller inaktivera automatisk skalning och ger exempel på hur du använder den här funktionen.

## <a name="describing-auto-scaling"></a>Beskriva automatisk skalning
Principer för automatisk skalning kan definieras för varje tjänst i ett Service Fabric kluster. Varje skalnings princip består av två delar:
* **Skalnings utlösare** beskriver när skalning av tjänsten ska utföras. Villkor som definieras i utlösaren kontrol leras regelbundet för att avgöra om en tjänst ska skalas eller inte.

* **Skalnings mekanismen** beskriver hur skalningen ska utföras när den utlöses. Mekanismen tillämpas endast när villkoren från utlösaren är uppfyllda.

Alla utlösare som stöds för närvarande fungerar antingen med [logiska inläsnings mått](service-fabric-cluster-resource-manager-metrics.md)eller med fysiska mått som processor-eller minnes användning. Oavsett hur du använder Service Fabric övervakar den rapporterade belastningen för måttet och utvärderar utlösaren regelbundet för att avgöra om skalning krävs.

Det finns två mekanismer som för närvarande stöds för automatisk skalning. Den första är avsedd för tillstånds lösa tjänster eller för behållare där automatisk skalning utförs genom att lägga till eller ta bort [instanser](service-fabric-concepts-replica-lifecycle.md). För både tillstånds känsliga och tillstånds lösa tjänster kan automatisk skalning också utföras genom att lägga till eller ta bort namngivna [partitioner](service-fabric-concepts-partitioning.md) i tjänsten.

> [!NOTE]
> För närvarande finns det bara stöd för en skalnings princip per tjänst och endast en skalnings utlösare per skalnings princip.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Genomsnittlig utlösare för partitions beläggning med instans baserad skalning
Den första typen av utlösare baseras på instansernas inläsning i en tillstånds lös tjänstmall. Mått inläsningar är först jämna för att hämta belastningen för varje instans av en partition, och de här värdena är i genomsnitt alla instanser av partitionen. Det finns tre faktorer som avgör när tjänsten ska skalas:

* _Lägre tröskelvärde för inläsning_ är ett värde som avgör när tjänsten ska **skalas i**. Om den genomsnittliga belastningen för alla instanser av partitionerna är lägre än det här värdet kommer tjänsten att skalas i.
* Det _övre tröskelvärdet för inläsning_ är ett värde som anger när tjänsten ska **skalas ut**. Om den genomsnittliga belastningen för alla instanser av partitionen är högre än det här värdet kommer tjänsten att skalas ut.
* _Skalnings intervallet_ avgör hur ofta utlösaren ska kontrol leras. När utlösaren är markerad kommer mekanismen att tillämpas om skalning krävs. Om skalning inte behövs vidtas ingen åtgärd. I båda fallen kontrol leras inte utlösaren igen innan skalnings intervallet går ut igen.

Den här utlösaren kan endast användas med tillstånds lösa tjänster (antingen tillstånds lösa behållare eller Service Fabric tjänster). Om en tjänst har flera partitioner, utvärderas utlösaren för varje partition separat, och varje partition har den angivna mekanismen som tillämpas oberoende av varandra. I det här fallet är det möjligt att vissa av partitionerna för tjänsten kommer att skalas ut, vissa kommer att skalas i och vissa kommer inte att skalas samtidigt, baserat på belastningen.

Den enda mekanismen som kan användas med den här utlösaren är PartitionInstanceCountScaleMechanism. Det finns tre faktorer som avgör hur den här mekanismen tillämpas:
* _Skalnings ökning_ avgör hur många instanser som ska läggas till eller tas bort när mekanismen utlöses.
* _Maximalt antal instanser_ definierar den övre gränsen för skalning. Om antalet instanser av partitionen når den här gränsen kommer tjänsten inte att skalas ut, oavsett belastningen. Det går att utelämna denna gräns genom att ange värdet-1 och i så fall kommer tjänsten att skalas ut så mycket som möjligt (gränsen är antalet noder som är tillgängliga i klustret).
* _Lägsta instans antal_ definierar den nedre gränsen för skalning. Om antalet instanser av partitionen når den här gränsen kommer tjänsten inte att skalas i oberoende av belastningen.

## <a name="setting-auto-scaling-policy-for-instance-based-scaling"></a>Ange princip för automatisk skalning för instans baserad skalning

### <a name="using-application-manifest"></a>Använda applikations manifest
``` xml
<LoadMetrics>
<LoadMetric Name="MetricB" Weight="High"/>
</LoadMetrics>
<ServiceScalingPolicies>
<ScalingPolicy>
    <AveragePartitionLoadScalingTrigger MetricName="MetricB" LowerLoadThreshold="1" UpperLoadThreshold="2" ScaleIntervalInSeconds="100"/>
    <InstanceCountScalingMechanism MinInstanceCount="3" MaxInstanceCount="4" ScaleIncrement="1"/>
</ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Använda C# API: er
```csharp
FabricClient fabricClient = new FabricClient();
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//set up the rest of the ServiceDescription
AveragePartitionLoadScalingTrigger trigger = new AveragePartitionLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new PartitionInstanceCountScaleMechanism();
mechanism.MaxInstanceCount = 3;
mechanism.MinInstanceCount = 1;
mechanism.ScaleIncrement = 1;
trigger.MetricName = "servicefabric:/_CpuCores";
trigger.ScaleInterval = TimeSpan.FromMinutes(20);
trigger.LowerLoadThreshold = 1.0;
trigger.UpperLoadThreshold = 2.0;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceDescription.ScalingPolicies.Add(policy);
//as we are using scaling on a resource this must be exclusive service
//also resource monitor service needs to be enabled
serviceDescription.ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```
### <a name="using-powershell"></a>Använda PowerShell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.PartitionInstanceCountScaleMechanism
$mechanism.MinInstanceCount = 1
$mechanism.MaxInstanceCount = 6
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AveragePartitionLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_CpuCores"
$trigger.LowerLoadThreshold = 0.3
$trigger.UpperLoadThreshold = 0.8
$trigger.ScaleInterval = New-TimeSpan -Minutes 10
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
Update-ServiceFabricService -Stateless -ServiceName "fabric:/AppName/ServiceName" -ScalingPolicies $scalingpolicies
```

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Genomsnittlig tjänst inläsnings utlösare med partition baserad skalning
Den andra utlösaren baseras på belastningen på alla partitioner i en tjänst. Mått inläsningar är först jämna för att hämta belastningen för varje replik eller instans av en partition. För tillstånds känsliga tjänster anses belastningen på partitionen vara belastningen på den primära repliken, medan för tillstånds lösa tjänster är belastningen på partitionen den genomsnittliga belastningen för alla instanser av partitionen. Dessa värden är genomsnittliga för alla partitioner i tjänsten och det här värdet används för att utlösa den automatiska skalningen. Samma som i tidigare mekanism finns det tre faktorer som avgör när tjänsten ska skalas:

* _Lägre tröskelvärde för inläsning_ är ett värde som avgör när tjänsten ska **skalas i**. Om den genomsnittliga belastningen på alla partitioner för tjänsten är lägre än det här värdet kommer tjänsten att skalas i.
* Det _övre tröskelvärdet för inläsning_ är ett värde som anger när tjänsten ska **skalas ut**. Om den genomsnittliga belastningen för alla partitioner i tjänsten är högre än det här värdet kommer tjänsten att skalas ut.
* _Skalnings intervallet_ avgör hur ofta utlösaren ska kontrol leras. När utlösaren är markerad kommer mekanismen att tillämpas om skalning krävs. Om skalning inte behövs vidtas ingen åtgärd. I båda fallen kontrol leras inte utlösaren igen innan skalnings intervallet går ut igen.

Den här utlösaren kan användas både med tillstånds känsliga och tillstånds lösa tjänster. Den enda mekanismen som kan användas med den här utlösaren är AddRemoveIncrementalNamedPartitionScalingMechanism. När tjänsten skalas ut läggs en ny partition till och när tjänsten skalas i en befintlig partition tas den bort. Det finns begränsningar som kommer att kontrol leras när tjänsten skapas eller uppdateras och tjänsten håller på att skapas eller uppdateras, om dessa villkor inte är uppfyllda:
* Named partition Scheme måste användas för tjänsten.
* Partitionsnamn måste vara i följd heltal, t. ex. "0", "1"...
* Det första partitionsnamnet måste vara "0".

Till exempel, om en tjänst ursprungligen skapas med tre partitioner, är den enda giltiga möjligheten för partitionsnamn "0", "1" och "2".

Den faktiska automatiska skalnings åtgärden som utförs påverkar även detta namngivnings schema:
* Om aktuella partitioner för tjänsten heter "0", "1" och "2", får partitionen som ska läggas till för utskalning till "3".
* Om aktuella partitioner för tjänsten heter "0", "1" och "2", kommer partitionen som ska tas bort för skalning i partitionen med namnet "2".

Samma som med en mekanism som använder skalning genom att lägga till eller ta bort instanser finns det tre parametrar som avgör hur mekanismen tillämpas:
* _Skalnings ökning_ avgör hur många partitioner som ska läggas till eller tas bort när mekanismen utlöses.
* _Högsta antal partitioner_ definierar den övre gränsen för skalning. Om antalet partitioner för tjänsten når den här gränsen kommer tjänsten inte att skalas ut, oavsett belastningen. Det går att utelämna denna gräns genom att ange värdet-1 och i så fall kommer tjänsten att skalas ut så mycket som möjligt (gränsen är den faktiska kapaciteten för klustret).
* _Lägsta instans antal_ definierar den nedre gränsen för skalning. Om antalet partitioner för tjänsten når den här gränsen kommer tjänsten inte att skalas i oberoende av belastningen.

> [!WARNING] 
> När AddRemoveIncrementalNamedPartitionScalingMechanism används med tillstånds känsliga tjänster, kommer Service Fabric att lägga till eller ta bort partitioner **utan meddelande eller varning**. Ompartitionering av data utförs inte när skalnings metoden utlöses. I händelse av en skalbar åtgärd är nya partitioner tomma och i händelse av skalning i drift, tas **partitionen bort tillsammans med alla data som den innehåller**.

## <a name="setting-auto-scaling-policy-for-partition-based-scaling"></a>Ange princip för automatisk skalning för partition baserad skalning

### <a name="using-application-manifest"></a>Använda applikations manifest
``` xml
<NamedPartition>
    <Partition Name="0" />
</NamedPartition>
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Använda C# API: er
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedPartitionScalingMechanism();
mechanism.MaxPartitionCount = 4;
mechanism.MinPartitionCount = 1;
mechanism.ScaleIncrement = 1;
//expecting that the service already has metric NumberOfConnections
trigger.MetricName = "NumberOfConnections";
trigger.ScaleInterval = TimeSpan.FromMinutes(15);
trigger.LowerLoadThreshold = 10000;
trigger.UpperLoadThreshold = 20000;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceUpdate.ScalingPolicies = new List<ScalingPolicyDescription>;
serviceUpdate.ScalingPolicies.Add(policy);
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```
### <a name="using-powershell"></a>Använda PowerShell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedPartitionScalingMechanism
$mechanism.MinPartitionCount = 1
$mechanism.MaxPartitionCount = 3
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AverageServiceLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_MemoryInMB"
$trigger.LowerLoadThreshold = 5000
$trigger.UpperLoadThreshold = 10000
$trigger.ScaleInterval = New-TimeSpan -Minutes 25
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -TargetReplicaSetSize 3 -MinReplicaSetSize 2 -HasPersistedState true -PartitionNames @("0","1") -ServicePackageActivationMode ExclusiveProcess -ScalingPolicies $scalingpolicies
```

## <a name="auto-scaling-based-on-resources"></a>Automatisk skalning baserat på resurser

För att aktivera resurs övervaknings tjänsten för skalning baserat på faktiska resurser

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Det finns två mått som representerar faktiska fysiska resurser. En av dem är servicefabric:/_CpuCores som representerar den faktiska processor användningen (så 0,5 representerar hälften en kärna) och den andra som servicefabric:/_MemoryInMB som representerar minnes användningen i MB.
ResourceMonitorService ansvarar för att spåra processor-och minnes användning för användar tjänster. Den här tjänsten kommer att tillämpa viktat glidande medelvärde för att kunna ta hänsyn till potentiella längd toppar. Resurs övervakning stöds för både behållare och appar som inte har containers i Windows och för behållare på Linux. Automatisk skalning på resurser är bara aktiverat för tjänster som Aktiver ATS i en [exklusiv process modell](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>Nästa steg
Läs mer om [skalbarhet för program](service-fabric-concepts-scalability.md).
