---
title: Hantera Azure Service Fabric-tillstånd
description: Lär dig mer om att komma åt, Spara och ta bort tillstånd för Azure Service Fabric Reliable skådespelare och överväganden när du skapar ett program.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 1e796232719342883efc2aa5dd377d586f3039ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96571325"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Åtkomst, Spara och ta bort Reliable Actors tillstånd
[Reliable Actors](service-fabric-reliable-actors-introduction.md) är entrådade objekt som kan kapsla in både logik och tillstånd och bevara tillstånd på ett tillförlitligt sätt. Varje aktörs instans har sin egen [tillstånds ansvarig](service-fabric-reliable-actors-state-management.md): en ord lista som är en data struktur som på ett tillförlitligt sätt lagrar nyckel/värde-par. Tillstånds hanteraren är en omslutning runt en tillstånds leverantör. Du kan använda den för att lagra data oavsett vilken [beständighets inställning](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) som används.

Tillstånds hanterarens nycklar måste vara strängar. Värdena är generiska och kan vara vilken typ som helst, inklusive anpassade typer. Värden som lagras i tillstånds hanteraren måste vara serialiserbar för data kontrakt eftersom de kan överföras via nätverket till andra noder under replikeringen och kan skrivas till disk, beroende på en aktörs inställning för tillstånds persistence.

Tillstånds hanteraren visar gemensamma ordboks metoder för att hantera tillstånd, liknande de som finns i en tillförlitlig ord lista.

Mer information finns [i metod tips i hantera aktörs tillstånd](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Åtkomst tillstånd
Tillstånd nås via tillstånds hanteraren efter nyckel. Tillstånds hanterarens metoder är alla asynkrona eftersom de kan kräva disk-I/O när aktörer har sparat tillstånd. Vid första åtkomst cachelagras tillstånds objekt i minnet. Upprepa åtkomst åtgärder åtkomst objekt direkt från minnet och returnera dem synkront utan att behöva dra på disk-I/O eller asynkron kontext växling. Ett tillstånds objekt tas bort från cachen i följande fall:

* En aktörs metod genererar ett ohanterat undantag efter att det har hämtat ett objekt från tillstånds hanteraren.
* En aktör återaktiveras, antingen efter att ha inaktiverats eller efter ett haveri.
* Statusen för tillstånds leverantörens sidor till disk. Detta beteende beror på implementeringen av tillstånds leverantören. Standardprovidern för `Persisted` inställningen har det här beteendet.

Du kan hämta tillstånd genom att använda en standard åtgärd för *hämtning* som returnerar `KeyNotFoundException` (C#) eller `NoSuchElementException` (Java) om det inte finns någon post för nyckeln:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

Du kan också hämta tillstånd genom att använda en *TryGet* -metod som inte utlöses om det inte finns någon post för en nyckel:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>Spara tillstånd
Hämtnings metoder för tillstånds hanteraren returnerar en referens till ett objekt i lokalt minne. Att bara ändra det här objektet i lokalt minne gör att det inte kan sparas varaktigt. När ett objekt hämtas från tillstånds hanteraren och ändras, måste det infogas i tillstånds hanteraren för att kunna sparas varaktigt.

Du kan infoga tillstånd genom att använda en ovillkorlig *uppsättning*, vilket är motsvarigheten till `dictionary["key"] = value` syntaxen:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

Du kan lägga till tillstånd med hjälp av en *Add* -metod. Den här metoden genererar `InvalidOperationException` (C#) eller `IllegalStateException` (Java) när den försöker lägga till en nyckel som redan finns.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

Du kan också lägga till tillstånd med hjälp av en *TryAdd* -metod. Den här metoden utlöses inte när den försöker lägga till en nyckel som redan finns.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

I slutet av en aktörs metod sparar tillstånds hanteraren automatiskt alla värden som har lagts till eller ändrats av en infognings-eller uppdaterings åtgärd. Ett "Spara" kan omfatta beständighet till disk och replikering, beroende på vilka inställningar som används. Värden som inte har ändrats sparas inte eller replikeras inte. Om inga värden har ändrats gör Spara-åtgärden ingenting. Om det inte går att spara ignoreras statusen ändrad och det ursprungliga läget läses in igen.

Du kan också spara tillstånd manuellt genom att anropa `SaveStateAsync` metoden i aktörs basen:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>Ta bort tillstånd
Du kan ta bort statusen permanent från en aktörs tillstånds ansvarig genom att anropa metoden *Remove* . Den här metoden genererar `KeyNotFoundException` (C#) eller `NoSuchElementException` (Java) när den försöker ta bort en nyckel som inte finns.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

Du kan också ta bort statusen permanent genom att använda metoden *TryRemove* . Den här metoden utlöses inte när den försöker ta bort en nyckel som inte finns.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>Nästa steg

Tillstånd som lagras i Reliable Actors måste serialiseras innan den skrivs till disk och replikeras för hög tillgänglighet. Läs mer om [typ serialisering för aktör](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Läs sedan mer om [aktörs diagnostik och prestanda övervakning](service-fabric-reliable-actors-diagnostics.md).
