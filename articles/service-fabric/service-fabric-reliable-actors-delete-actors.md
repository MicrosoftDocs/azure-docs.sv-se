---
title: Ta bort Azure Service Fabric-aktörer
description: Lär dig hur du manuellt och helt tar bort Reliable Actors och deras tillstånd i ett Azure Service Fabric-program.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 16d4ab6a3c155f897cf9212fb1cd6c34d977b9ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96574028"
---
# <a name="delete-reliable-actors-and-their-state"></a>Ta bort Reliable Actors och deras tillstånd
Skräp insamling av inaktiverade aktörer rensar bara aktörs objekt, men tar inte bort data som lagras i en aktörs tillstånds hanterare. När en aktör återaktiveras, blir dess data återigen tillgängliga för den via tillstånds hanteraren. I de fall där aktörer lagrar data i tillstånds hanteraren och inaktive ras men aldrig återaktiveras, kan det vara nödvändigt att rensa data.

[Aktörs tjänsten](service-fabric-reliable-actors-platform.md) tillhandahåller en funktion för att ta bort aktörer från en fjärran sluten anropare:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Att ta bort en aktör har följande effekter beroende på om aktören för närvarande är aktiv eller inte:

* **Aktiv aktör**
  * Aktören tas bort från listan över aktiva aktörer och inaktive ras.
  * Statusen tas bort permanent.
* **Inaktiv aktör**
  * Statusen tas bort permanent.

En aktör kan inte anropa ta bort sig själv från någon av dess aktörs metoder eftersom aktören inte kan tas bort när den körs inom en aktörs anrops kontext, där körningen har fått ett lås runt aktörs anropet för att genomdriva åtkomst med enkel tråd.

Läs följande om du vill ha mer information om Reliable Actors:
* [Timers för aktör och påminnelser](service-fabric-reliable-actors-timers-reminders.md)
* [Aktörs händelser](service-fabric-reliable-actors-events.md)
* [Aktör återinträde](service-fabric-reliable-actors-reentrancy.md)
* [Aktörens diagnostik och prestanda övervakning](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentation om aktörs-API-referens](/previous-versions/azure/dn971626(v=azure.100))
* [Exempel kod för C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-exempel kod](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
