---
title: Utveckla enhets test för tillstånds känsliga tjänster
description: Lär dig mer om enhets testning i Azure Service Fabric för tillstånds känsliga tjänster och särskilda överväganden som du bör tänka på under utvecklingen.
ms.topic: conceptual
ms.date: 09/04/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 287c0544daa3c44d91fd336b502c496b9b4bb266
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89011403"
---
# <a name="create-unit-tests-for-stateful-services"></a>Skapa enhets test för tillstånds känsliga tjänster
Enhets testning Service Fabric tillstånds känsliga tjänster avvisar vanliga misstag som inte nödvändigt vis fångas upp av konventionell program-eller domänbaserad enhets testning. När du utvecklar enhets test för tillstånds känsliga tjänster finns det några saker som bör tänkas vara i åtanke.

1. Varje replik kör program kod men under en annan kontext. Om tjänsten använder tre repliker körs tjänst koden på tre noder parallellt under olika kontext/roll.
2. Tillstånd som lagras i den tillstånds känsliga tjänsten bör vara konsekvent bland alla repliker. Tillstånds hanteraren och tillförlitliga samlingar kommer att tillhandahålla den här konsekvensen direkt. InMemory-tillstånd måste dock hanteras av program koden.
3. Varje replik kommer att ändra roller när som helst medan den körs i klustret. En sekundär replik blir primär, i händelse av att den nod som är värd för den primära blir otillgänglig eller överbelastad. Detta är ett naturligt beteende för Service Fabric därför att tjänsterna måste planeras för att slutligen köras under en annan roll.

Den här artikeln förutsätter att [enheten testar tillstånds känsliga tjänster i Service Fabric](service-fabric-concepts-unit-testing.md) har lästs.

## <a name="the-servicefabricmocks-library"></a>Biblioteket ServiceFabric. skisser
Från och med version 3.3.0 tillhandahåller [ServiceFabric.](https://www.nuget.org/packages/ServiceFabric.Mocks/) ett API för att modellera både dirigeringen av replikerna och tillstånds hanteringen. Detta kommer att användas i exemplen.

[NuGet](https://www.nuget.org/packages/ServiceFabric.Mocks/) 
 [GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric. skisser ägs inte av Microsoft. Detta är dock för närvarande Microsofts rekommenderade bibliotek för tillstånds känsliga tjänster för enhets testning.*

## <a name="set-up-the-mock-orchestration-and-state"></a>Konfigurera en modell för att dirigera och delstaten
Som en del av den ordnade delen av ett test skapas en modell med en blå replik uppsättning och en tillstånds hanterare. Replik uppsättningen äger sedan skapandet av en instans av den testade tjänsten för varje replik. Den kommer också att äga körning av livs cykel händelser som `OnChangeRole` och `RunAsync` . Den modellerade tillstånds hanteraren ser till att alla åtgärder som utförs mot tillstånds hanteraren körs och hålls som den faktiska tillstånds hanteraren.

1. Skapa ett tjänst fabriks ombud som kommer att instansiera den tjänst som testas. Detta bör vara liknande eller samma som återanropet av tjänst fabriken som vanligt vis finns i `Program.cs` för en Service Fabric tjänst eller aktör. Detta bör följa följande signatur:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Skapa en instans av `MockReliableStateManager` klassen. Detta kommer att modellera alla interaktioner med tillstånds hanteraren.
3. Skapa en instans av `MockStatefulServiceReplicaSet<TStatefulService>` där `TStatefulService` är typen av tjänst som testas. Detta kräver att ombudet som skapades i steg #1 och tillstånds hanteraren instansieras i #2
4. Lägg till repliker i replik uppsättningen. Ange rollen (till exempel primär, ActiveSecondary, IdleSecondary) och ID för repliken
   > Vänta på replik-ID! De kommer sannolikt att användas under Act-och assert-delar av ett enhets test.

```csharp
//service factory to instruct how to create the service instance
var serviceFactory = (StatefulServiceContext context, IReliableStateManagerReplica2 stateManager) => new MyStatefulService(context, stateManager);
//instantiate a new mock state manager
var stateManager = new MockReliableStateManager();
//instantiate a new replica set with the service factory and state manager
var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
//add a new Primary replica with id 1
await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
//add a new ActiveSecondary replica with id 2
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
//add a second ActiveSecondary replica with id 3
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);
```

## <a name="execute-service-requests"></a>Köra tjänst begär Anden
Tjänst begär Anden kan köras på en speciell replik med hjälp av bekvämlighets egenskaper och sökningar.
```csharp
const string stateName = "test";
var payload = new Payload(StatePayload);

//execute a request on the primary replica using
await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);

//execute a request against replica with id 2
await replicaSet[2].ServiceInstance.InsertAsync(stateName, payload);

//execute a request against one of the active secondary replicas
await replicaSet.FirstActiveSecondary.InsertAsync(stateName, payload);
```

## <a name="execute-a-service-move"></a>Köra en tjänst flyttning
Den blå replik uppsättningen visar flera praktiska metoder för att utlösa olika typer av tjänst flyttningar.
```csharp
//promote the first active secondary to primary
replicaSet.PromoteNewReplicaToPrimaryAsync();
//promote the secondary with replica id 4 to primary
replicaSet.PromoteNewReplicaToPrimaryAsync(4);

//promote the first idle secondary to an active secondary
PromoteIdleSecondaryToActiveSecondaryAsync();
//promote idle secondary with replica id 4 to active secondary
PromoteIdleSecondaryToActiveSecondaryAsync(4);

//add a new replica with randomly assigned replica id and promote it to primary
PromoteNewReplicaToPrimaryAsync()
//add a new replica with replica id 4 and promote it to primary
PromoteNewReplicaToPrimaryAsync(4)
```

## <a name="putting-it-all-together"></a>Färdigställa allt
Följande test visar hur du konfigurerar en replik uppsättning med tre noder och kontrollerar att data är tillgängliga från en sekundär efter en roll ändring. Ett vanligt problem som detta kan fånga är om data som lagts till under `InsertAsync` sparats antingen i minnet eller till en tillförlitlig samling utan att köra `CommitAsync` . I båda fallen är den sekundära synkroniseringen inte synkroniserad med den primära. Detta skulle leda till inkonsekventa svar när tjänsten har flyttats.

```csharp
[TestMethod]
public async Task TestServiceState_InMemoryState_PromoteActiveSecondary()
{
    var stateManager = new MockReliableStateManager();
    var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
    await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);

    const string stateName = "test";
    var payload = new Payload(StatePayload);

    //insert data
    await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);
    //promote one of the secondaries to primary
    await replicaSet.PromoteActiveSecondaryToPrimaryAsync(2);
    //get data
    var payloads = (await replicaSet.Primary.ServiceInstance.GetPayloadsAsync()).ToList();

    //data should match what was inserted against the primary
    Assert.IsTrue(payloads.Count == 1);
    Assert.IsTrue(payloads[0].Content == payload.Content);

    //verify the data was saved against the reliable dictionary
    var dictionary = await StateManager.GetOrAddAsync<IReliableDictionary<string, Payload>>(MyStatefulService.StateManagerDictionaryKey);
    using(var tx = StateManager.CreateTransaction())
    {
        var payload = await dictionary.TryGetValue(stateName);
        Assert.IsTrue(payload.HasValue);
        Assert.IsTrue(payload.Value.Content == payload.Content);
    }
}
```

## <a name="next-steps"></a>Nästa steg
Lär dig hur du testar [tjänst-till-tjänst-kommunikation](service-fabric-testability-scenarios-service-communication.md) och [simulerar fel med hjälp av kontrollerade kaos](service-fabric-controlled-chaos.md).
