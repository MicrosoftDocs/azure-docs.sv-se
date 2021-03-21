---
title: Räkna upp aktörer på Azure Service Fabric
description: Lär dig mer om uppräkning av Reliable Actors och deras metadata i ett Azure-Service Fabric program med hjälp av exempel.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 11742d1a1c0837588e7122ee615f3f7929aae363
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96571359"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Räkna upp Service Fabric Reliable Actors
Med tjänsten Reliable Actors kan en klient räkna upp metadata om de aktörer som tjänsten är värd för. Eftersom aktörs tjänsten är en partitionerad tillstånds känslig tjänst utförs uppräkningen per partition. Eftersom varje partition kan innehålla många aktörer returneras uppräkningen som en uppsättning växlade resultat. Sidorna upprepas tills alla sidor har lästs. I följande exempel visas hur du skapar en lista över alla aktiva aktörer i en partition av en aktörs tjänst:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```



## <a name="next-steps"></a>Nästa steg
* [Hantering av aktörs tillstånd](service-fabric-reliable-actors-state-management.md)
* [Aktörs livs cykel och skräp insamling](service-fabric-reliable-actors-lifecycle.md)
* [Dokumentation om aktörers API-referens](/previous-versions/azure/dn971626(v=azure.100))
* [.NET-exempel kod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-exempel kod](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
