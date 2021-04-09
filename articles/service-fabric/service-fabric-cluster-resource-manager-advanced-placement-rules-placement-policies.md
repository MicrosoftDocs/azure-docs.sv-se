---
title: Service Fabric Cluster Resource Manager-placerings principer
description: Översikt över ytterligare placerings principer och regler för Service Fabric tjänster
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 263e45928642aa74d682fc490e424a24deeb8076
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790689"
---
# <a name="placement-policies-for-service-fabric-services"></a>Placerings principer för Service Fabric-tjänster
Placerings principer är ytterligare regler som kan användas för att styra tjänst placeringen i vissa olika, mindre vanliga scenarier. Några exempel på scenarier är:

- Ditt Service Fabric-kluster omfattar geografiska avstånd, t. ex. flera lokala data Center eller mellan Azure-regioner
- Din miljö omfattar flera områden av politisk eller juridisk kontroll, eller något annat fall där du har princip gränser som du måste tillämpa
- Det finns information om kommunikations prestanda eller svars tider på grund av stora avstånd eller användning av långsammare eller mindre pålitliga nätverks länkar
- Du måste ha vissa arbets belastningar samordnad som bästa ansträngning, antingen med andra arbets belastningar eller i närhet till kunder
- Du behöver flera tillstånds lösa instanser av en partition på en enskild nod

De flesta av dessa krav överensstämmer med klustrets fysiska layout, som visas som fel domäner i klustret. 

De avancerade placerings principerna som hjälper dig att lösa de här scenarierna är:

1. Ogiltiga domäner
2. Obligatoriska domäner
3. Önskade domäner
4. Otillåten replik packning
5. Tillåt flera tillstånds lösa instanser på noden

De flesta av följande kontroller kan konfigureras via nodens egenskaper och placerings begränsningar, men några är mer komplicerade. För att göra det enklare är Service Fabric Cluster Resource Manager dessa ytterligare placerings principer. Placerings principer konfigureras på en per namngiven tjänst instans. De kan också uppdateras dynamiskt.

## <a name="specifying-invalid-domains"></a>Ange ogiltiga domäner
Med placerings principen **InvalidDomain** kan du ange att en viss fel domän är ogiltig för en viss tjänst. Den här principen säkerställer att en viss tjänst aldrig körs i ett visst utrymme, till exempel för både politisk-eller företags princip skäl. Flera ogiltiga domäner kan anges via separata principer.

<center>

![Ogiltigt domän exempel][Image1]
</center>

Kod:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Ange nödvändiga domäner
Den obligatoriska principen för domän placering kräver att tjänsten endast finns i den angivna domänen. Flera obligatoriska domäner kan anges via separata principer.

<center>

![Nödvändigt domän exempel][Image2]
</center>

Kod:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Ange en önskad domän för de primära replikerna av en tillstånds känslig tjänst
Den primära domän som föredras anger den feldomän där den primära platsen ska placeras. Den primära delen upphör i den här domänen när allt är felfritt. Om domänen eller den primära repliken Miss lyckas eller stängs av flyttas den primära platsen till en annan plats, helst i samma domän. Om den nya platsen inte finns i den önskade domänen, flyttas kluster resurs hanteraren tillbaka till den önskade domänen så snart som möjligt. Naturlig den här inställningen passar bara för tillstånds känsliga tjänster. Den här principen är mest användbar i kluster som sträcker sig över Azure-regioner eller flera data Center, men har tjänster som föredrar placering på en viss plats. Att hålla presidentval nära sina användare eller andra tjänster bidrar till att ge kortare svars tider, särskilt för läsningar som hanteras av presidentval som standard.

<center>

![Prioriterade primära domäner och redundans][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(primaryDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Kräva replik distribution och otillåten paketering
Repliker distribueras _normalt_ mellan fel-och uppgraderings domäner när klustret är felfritt. Det finns dock fall där fler än en replik för en viss partition kan komma att bli tillfälligt förpackad i en enda domän. Anta till exempel att klustret har nio noder i tre fel domäner, fd:/0, fd:/1 och fd:/2. Vi antar också att tjänsten har tre repliker. Anta att de noder som användes för dessa repliker i fd:/1 och fd:/2 gick ner. Normalt föredrar kluster resurs hanteraren andra noder i samma fel domäner. I det här fallet säger vi på grund av kapacitets problem ingen av de andra noderna i dessa domäner är giltiga. Om kluster resurs hanteraren skapar ersättningar för dessa repliker, måste du välja noder i fd:/0. Men om du gör _det_ skapas en situation där fel domän begränsningen är bruten. Packnings repliker ökar risken för att hela replik uppsättningen kan stängas av eller gå förlorade. 

> [!NOTE]
> Mer information om begränsningar och begränsnings prioriteter finns i [det här avsnittet](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Om du någonsin har sett ett hälso meddelande som " `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain` ", har du nått det här villkoret eller något liknande. Vanligt vis packas endast en eller två repliker tillsammans. Så länge det finns färre än ett kvorum med repliker i en specifik domän är du säker. Packning är sällsynt, men det kan hända, och vanligt vis är dessa situationer tillfälliga eftersom noderna kommer tillbaka. Om noderna stannar kvar och kluster resurs hanteraren behöver skapa ersättningar, finns det vanligt vis andra noder i de idealiska fel domänerna.

Vissa arbets belastningar föredrar alltid att ha mål antalet repliker, även om de är förpackade i färre domäner. Dessa arbets belastningar är slår vad mot totala samtidiga permanenta domän fel och kan vanligt vis återställa lokalt tillstånd. Andra arbets belastningar skulle i stället ta drift stoppet som är tidigare än risk för data förlust eller data förlust. De flesta produktions arbets belastningar körs med fler än tre repliker, fler än tre fel domäner och många giltiga noder per fel domän. Därför tillåter standard beteendet domän packning som standard. Standard beteendet tillåter normal balansering och redundans för att hantera dessa extrema fall, även om det innebär temporär domän packning.

Om du vill inaktivera sådan packning för en specifik arbets belastning kan du ange `RequireDomainDistribution` principen för tjänsten. När den här principen har angetts ser kluster resurs hanteraren till att inga två repliker från samma partition körs i samma fel-eller uppgraderings domän.

Kod:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Är det nu möjligt att använda de här konfigurationerna för tjänster i ett kluster som inte var geografiskt utsträckta? Du kan, men det är inte en bra anledning. De obligatoriska, ogiltiga och önskade domän konfigurationerna bör undvikas om inte scenarierna kräver det. Det är ingen mening att försöka tvinga en viss arbets belastning att köras i ett enda rack eller att föredra ett visst segment i ditt lokala kluster över ett annat. Olika maskinvarukonfigurationer bör spridas mellan fel domäner och hanteras via vanliga placerings begränsningar och Node-egenskaper.

## <a name="placement-of-multiple-stateless-instances-of-a-partition-on-single-node"></a>Placering av flera tillstånds lösa instanser av en partition på en enskild nod
**AllowMultipleStatelessInstancesOnNode** placerings princip tillåter placering av flera tillstånds lösa instanser av en partition på en enda nod. Som standard kan flera instanser av en enda partition inte placeras på en nod. Även med en-1-tjänst går det inte att skala antalet instanser utöver antalet noder i klustret för en angiven tjänst. Den här placerings principen tar bort den här begränsningen och tillåter att InstanceCount anges högre än antalet noder.

Om du någonsin har sett ett hälso meddelande som " `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: ReplicaExclusion` ", har du nått det här villkoret eller något liknande. 

Genom att ange `AllowMultipleStatelessInstancesOnNode` principen för tjänsten kan InstanceCount anges bortom antalet noder i klustret.

Kod:

```csharp
ServicePlacementAllowMultipleStatelessInstancesOnNodePolicyDescription allowMultipleInstances = new ServicePlacementAllowMultipleStatelessInstancesOnNodePolicyDescription();
serviceDescription.PlacementPolicies.Add(allowMultipleInstances);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless –PartitionSchemeSingleton –PlacementPolicy @(“AllowMultipleStatelessInstancesOnNode”) -InstanceCount 10 -ServicePackageActivationMode ExclusiveProcess 
```

> [!NOTE]
> Placerings principen är för närvarande en för hands version och ligger bakom `EnableUnsupportedPreviewFeatures` kluster inställningen. Eftersom det här är en förhands gransknings funktion för tillfället förhindrar att klustret uppgraderas till/från om du anger förhands gransknings konfigurationen. Med andra ord måste du skapa ett nytt kluster för att testa funktionen.
>

> [!NOTE]
> För närvarande stöds principen bara för tillstånds lösa tjänster med [aktiverings läget ExclusiveProcess Service Package](/dotnet/api/system.fabric.description.servicepackageactivationmode).
>

> [!WARNING]
> Principen stöds inte när den används med statiska port slut punkter. Användning av både tillsammans kan leda till ett kluster som inte är felfri eftersom flera instanser på samma nod försöker binda till samma port och kan inte komma upp. 
>

> [!NOTE]
> Att använda ett högt värde för [MinInstanceCount](/dotnet/api/system.fabric.description.statelessservicedescription.mininstancecount) med den här placerings principen kan leda till låsta program uppgraderingar. Om du till exempel har ett kluster med fem noder och anger InstanceCount = 10, kommer du att ha två instanser på varje nod. Om du anger MinInstanceCount = 9 kan ett försök att uppgradera appen bli fastnat. med MinInstanceCount = 8 kan detta undvikas.
>

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du konfigurerar tjänster finns i [så här konfigurerar du tjänster](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
