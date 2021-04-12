---
title: Händelser i skådespelare-baserade Azure Service Fabric-aktörer
description: Lär dig mer om händelser för Service Fabric Reliable Actors, ett effektivt sätt att kommunicera mellan skådespelare och klient.
ms.topic: conceptual
ms.date: 10/06/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 591e3539715ba0873e44f56a93d05df6552bb1ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96571376"
---
# <a name="actor-events"></a>Aktörs händelser
Aktörs händelser ger ett sätt att skicka aviseringar med bästa prestanda från aktören till klienterna. Aktörs händelser är utformade för kommunikation mellan skådespelare och klient och bör inte användas för kommunikation mellan skådespelare och aktör.

Följande kodfragment visar hur du använder aktör händelser i ditt program.

Definiera ett gränssnitt som beskriver de händelser som publiceras av aktören. Det här gränssnittet måste härledas från `IActorEvents` gränssnittet. Argumenten för metoderna måste vara [serialiserbara med data kontrakt](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Metoderna måste returnera void, eftersom händelse meddelanden är ett sätt och bästa möjliga ansträngning.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```
Deklarera de händelser som publiceras av aktören i aktörs gränssnittet.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```
Implementera händelse hanteraren på klient sidan.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

På klienten skapar du en proxy till den aktör som publicerar händelsen och prenumererar på dess händelser.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

I händelse av redundans kan aktören redundansväxla till en annan process eller nod. Aktörens proxy hanterar de aktiva prenumerationerna och prenumererar automatiskt på nytt. Du kan styra omprenumerations intervallet via API: `ActorProxyEventExtensions.SubscribeAsync<TEvent>` et. Om du vill avbryta prenumerationen använder du `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API: et.

Publicera händelserna när de sker på aktören. Om det finns prenumeranter på händelsen skickas meddelandet via körnings miljön.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Nästa steg
* [Aktör återinträde](service-fabric-reliable-actors-reentrancy.md)
* [Aktörens diagnostik och prestanda övervakning](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentation om aktörs-API-referens](/previous-versions/azure/dn971626(v=azure.100))
* [Exempel kod för C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Core-exempel kod](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java-exempel kod](https://github.com/Azure-Samples/service-fabric-java-getting-started)
