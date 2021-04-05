---
title: Reliable Actors kommentarer om typ serialisering av aktör
description: Beskriver grundläggande krav för att definiera serialiserbara klasser som kan användas för att definiera Service Fabric Reliable Actors tillstånd och gränssnitt
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a1118e394046b217a288663659a2c910098e992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576017"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Information om Service Fabric Reliable Actors typ serialisering
Argumenten för alla metoder, resultat typer för de uppgifter som returneras av varje metod i ett aktörs gränssnitt och objekt som lagras i en aktörs tillstånds hanterare måste vara [serialiserbar för data kontrakt](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer). Detta gäller även argumenten för de metoder som definieras i [aktörens händelse gränssnitt](service-fabric-reliable-actors-events.md). (Aktörens händelse gränssnitts metoder returnerar alltid Void.)

## <a name="custom-data-types"></a>Anpassade data typer
I det här exemplet definierar följande aktörs gränssnitt en metod som returnerar en anpassad datatyp som heter `VoicemailBox` :

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

Gränssnittet implementeras av en aktör som använder tillstånds hanteraren för att lagra ett `VoicemailBox` objekt:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

I det här exemplet `VoicemailBox` serialiseras objektet när:

* Objektet överförs mellan en aktörs instans och en anropare.
* Objektet sparas i tillstånds hanteraren där det sparas i en disk och replikeras till andra noder.

Det pålitliga aktörs ramverket använder DataContract-serialisering. Det innebär att de anpassade data objekten och deras medlemmar måste vara kommenterade med attributen **DataContract** respektive **datamembers** .

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>Nästa steg
* [Aktörs livs cykel och skräp insamling](service-fabric-reliable-actors-lifecycle.md)
* [Timers för aktör och påminnelser](service-fabric-reliable-actors-timers-reminders.md)
* [Aktörs händelser](service-fabric-reliable-actors-events.md)
* [Aktör återinträde](service-fabric-reliable-actors-reentrancy.md)
* [Fler än skådespelare och objektorienterade design mönster](service-fabric-reliable-actors-polymorphism.md)
* [Aktörens diagnostik och prestanda övervakning](service-fabric-reliable-actors-diagnostics.md)
