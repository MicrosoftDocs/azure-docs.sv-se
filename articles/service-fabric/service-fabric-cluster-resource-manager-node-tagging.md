---
title: Azure Service Fabric dynamiska nodtaggar
description: Med Azure Service Fabric du dynamiskt lägga till och ta bort nodtaggar.
author: yu-supersonic
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: branim
ms.custom: devx-track-csharp
ms.openlocfilehash: 712e6422060619e5567a74d6335320eff9ed8e66
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741930"
---
# <a name="introduction-to-dynamic-node-tags"></a>Introduktion till dynamiska nodtaggar
Med nodtaggar kan du dynamiskt lägga till och ta bort taggar från noder för att påverka placeringen av tjänster. Nodtaggning är mycket flexibelt och tillåter ändringar av tjänstens placering utan program- eller klusteruppgraderingar. Taggar kan läggas till eller tas bort från noder när som helst och tjänster kan ange krav för vissa taggar när de skapas. En tjänst kan också ha sina taggkrav uppdaterade dynamiskt medan den körs.

Nodtaggning liknar [placeringsbegränsningar](service-fabric-cluster-resource-manager-configure-services.md) och används vanligtvis för att styra vilka noder en tjänst körs på. Varje Service Fabric tjänst kan konfigureras för att kräva att taggen placeras eller att den fortsätter att köras.

Nodtaggning stöds för alla Service Fabric värdtjänster (Reliable Services, körbara gästtyper och containrar). Om du vill använda nodtaggning måste du köra version 8.0 eller senare Service Fabric körningen.

Resten av den här artikeln beskriver hur du aktiverar eller inaktiverar nodtaggning och ger exempel på hur du använder den här funktionen.


## <a name="describing-dynamic-node-tags"></a>Beskriva dynamiska nodtaggar
Tjänster kan ange de taggar som de behöver. Det finns två typer av taggar:
* **Taggar som krävs för** placering beskriver en uppsättning taggar, som endast krävs för tjänstplacering. När repliken har placerats kan dessa taggar tas bort utan att tjänsten avbryts. Om någon av dessa taggar tas bort från noden fortsätter tjänstrepliken att fungera och Service Fabric tar inte bort tjänsten

* **Taggar som krävs för** att köra beskriver en uppsättning taggar som krävs för både placering och körning av tjänsten. Om någon av de nödvändiga taggarna som körs tas bort Service Fabric tjänsten till en annan nod som har de angivna taggarna.

Exempel: Krävs för placeringstaggar kan användas när du använder någon form av containeraktivatortjänst, och du behöver tjänsten för att din container ska placeras och så fort containern aktiveras behöver du inte aktivera eller längre och du kan ta bort taggen som är kopplad till den, men containern bör fortsätta köras.
Krävs för att köra taggar kan användas när du har en faktureringstjänst, vilket är användbart för att vara samplacerad med användarriktad tjänst. När faktureringstjänsten misslyckas på noden tar du bort taggen som är kopplad till den och den användarriktade tjänsten flyttas till en annan nod som har en faktureringstjänst och dess tagg.

En tagg eller uppsättning taggar kan läggas till, uppdateras eller tas bort från en enskild nod med hjälp av Service Fabric-gränssnittsmekanismer som C#-API:er, REST API:er eller PowerShell-kommandon.

> [!NOTE]
> Service Fabric underhåller inte UD/FD-distributioner när du använder nodtaggar. Hantera nodtaggar på rätt sätt, så att du inte får FD/UD-överträdelser på grund av felaktig distribution av taggar mellan domäner.

## <a name="enabling-dynamic-node-tags"></a>Aktivera dynamiska nodtaggar
För att den här funktionen ska fungera måste du aktivera NodeTaggingEnabled-konfiguration i avsnittet PlacementAndLoadBalancing i klustermanifestet med hjälp av XML eller JSON:

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="NodeTaggingEnabled" Value="true" />
</Section>
```

via ClusterConfig.jspå för fristående distributioner eller Template.jspå för Azure-värdindelade kluster:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": " NodeTaggingEnabled ",
          "value": "true"
      }
    ]
  }
]
```

## <a name="setting-dynamic-node-tags"></a>Ange dynamiska nodtaggar

### <a name="using-powershell"></a>Använda PowerShell

Lägga till nodtaggar i noden:

```posh
Add-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
Det här kommandot lägger till taggarna "SampleTag1" och "SampleTag2" på noden DB.1.

Ta bort nodtaggar från noden:

```posh
Remove-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
Det här kommandot tar bort taggarna "SampleTag1" och "SampleTag2" på noden DB.1.

### <a name="using-c-apis"></a>Använda C#-API:er

Lägga till nodtaggar i noden:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.AddNodeTagsAsync("DB.1", nodeTagsList);
```

Ta bort nodtaggar från noden:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.RemoveNodeTagsAsync("DB.1", nodeTagsList);
```

## <a name="setting-required-tags-for-services"></a>Ange nödvändiga taggar för tjänster

### <a name="using-powershell"></a>Använda PowerShell

Skapa en ny tjänst:

```posh
New-ServiceFabricService -ApplicationName fabric:/HelloWorld -ServiceName fabric:/HelloWorld/svc1 -ServiceTypeName HelloWorldStateful -Stateful -PartitionSchemeSingleton -TargetReplicaSetSize 5 -MinReplicaSetSize 3 -TagsRequiredToRun @("SampleTag1") - TagsRequiredToPlace @("SampleTag2")
```
Det här kommandot skapar en tjänst som kräver att "SampleTag2" finns på en nod för att tjänsten ska placeras där och "SampleTag1" ska finnas för att tjänsten ska kunna fortsätta köras på noden.

Uppdatera befintlig tjänst:

```posh
Update-ServiceFabricService -Stateful -ServiceName fabric:/myapp/test -TagsRequiredToRun @("SampleTag1") -TagsRequiredToPlace @("SampleTag2")
```
Det här kommandot uppdaterar en tjänst, som kräver att "SampleTag2" finns på en nod för att tjänsten ska placeras där, och "SampleTag1" ska finnas för att tjänsten ska kunna fortsätta köras på noden.

### <a name="using-c-apis"></a>Använda C#-API:er

Skapa en ny tjänst:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceDescription.ServiceTags = serviceTags;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Uppdatera befintlig tjänst:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceUpdate.ServiceTags = serviceTags;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```

Alla dessa kommandon gäller för tillståndslösa tjänster.

## <a name="next-steps"></a>Nästa steg
Läs mer om [placeringsbegränsningar](service-fabric-cluster-resource-manager-configure-services.md)
