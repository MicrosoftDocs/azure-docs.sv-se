---
title: Implementera funktioner i Azure Service Fabric-aktörer
description: Beskriver hur du skriver en egen aktörs tjänst som implementerar service nivå funktioner på samma sätt som när du ärver StatefulService.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: d39ec93e0ad03d6c860bae9d0790e860c95457a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575575"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Implementera service nivå funktioner i aktörs tjänsten

Som det beskrivs i [Service lager](service-fabric-reliable-actors-platform.md#service-layering)är aktörs tjänsten en tillförlitlig tjänst. Du kan skriva en egen tjänst som är härledd från `ActorService` . Du kan även implementera funktioner på service nivå på samma sätt som när du ärver en tillstånds känslig tjänst, till exempel:

- Säkerhets kopiering och återställning av tjänsten.
- Delade funktioner för alla aktörer, till exempel en krets brytare.
- Fjärran rop i själva aktörs tjänsten och på varje enskild aktör.

## <a name="use-the-actor-service"></a>Använda aktörs tjänsten

Aktörs instanser har åtkomst till aktörs tjänsten där de körs. Via aktörs tjänsten kan aktörs instanser Hämta tjänst kontexten program mässigt. Tjänst kontexten har partitions-ID, tjänst namn, program namn och annan Azure Service Fabric plattformsspecifik information.

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

Precis som alla Reliable Services måste aktörs tjänsten vara registrerad med en tjänst typ i Service Fabric Runtime. För att aktörs tjänsten ska kunna köra aktörs instanserna måste aktörs typen också registreras hos aktörs tjänsten. `ActorRuntime`-registreringsmetoden gör det här jobbet för aktörerna. I det enklaste fallet kan du registrera din aktörs typ och aktörs tjänsten använder standardinställningarna.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

Alternativt kan du använda ett lambda-villkor som tillhandahålls av registrerings metoden för att skapa aktörs tjänsten själv. Du kan sedan konfigurera aktörs tjänsten och konstruera dina aktörs instanser explicit. Du kan mata in beroenden till din aktör via dess konstruktor.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>Aktörs tjänst metoder

Aktörs tjänsten implementerar `IActorService` (c#) eller `ActorService` (Java), som i sin tur implementerar `IService` (c#) eller `Service` (Java). Det här gränssnittet används av Reliable Services fjärr kommunikation, som tillåter fjärran rop på tjänst metoder. Den innehåller metoder på service nivå som kan anropas via fjärr anslutning via tjänstens fjärr kommunikation. Du kan använda den för att [räkna upp](service-fabric-reliable-actors-enumerate.md) och [ta bort](service-fabric-reliable-actors-delete-actors.md) aktörer.


## <a name="custom-actor-service"></a>Anpassad aktörs tjänst

Med hjälp av lambda-registreringen kan du registrera en egen anpassad aktörs tjänst som är härledd från `ActorService` (C#) och `FabricActorService` (Java). Du kan sedan implementera egna funktioner på tjänst nivå genom att skriva en tjänst klass som ärver `ActorService` (C#) eller `FabricActorService` (Java). En anpassad aktörs tjänst ärver alla aktörs körnings funktioner från `ActorService` (C#) eller `FabricActorService` (Java). Den kan användas för att implementera egna tjänst metoder.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implement-actor-backup-and-restore"></a>Implementera säkerhets kopiering och återställning av aktör

En anpassad aktörs tjänst kan exponera en metod för att säkerhetskopiera aktörs data genom att dra nytta av fjärrlyssnaren för fjärr kommunikation som redan finns i `ActorService` . Ett exempel finns i [säkerhets kopierings-och återställnings aktörer](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Skådespelare som använder en stack för fjärran vändning v2 (gränssnitts kompatibel)

Fjärran vändning v2 (gränssnitts kompatibel, känd som V2_1) stack har alla funktioner i stacken v2 för fjärr kommunikation. Dess gränssnitt är kompatibelt med fjärr kommunikation v1-stacken, men är inte bakåtkompatibel med v2 och v1. Om du vill uppgradera från v1 till V2_1 utan effekter på tjänstens tillgänglighet följer du stegen i nästa avsnitt.

Följande ändringar krävs för att använda fjärr V2_1s stacken:

1. Lägg till följande Assembly-attribut på aktörens gränssnitt.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Skapa och uppgradera aktörs tjänst och aktörs klient projekt för att börja använda v2-stacken.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Aktörs tjänst uppgradering till fjärr kommunikation v2 (gränssnitts kompatibel) stack utan att påverka tjänstens tillgänglighet

Den här ändringen är en uppgradering i två steg. Följ stegen i den här sekvensen.

1. Lägg till följande Assembly-attribut på aktörens gränssnitt. Det här attributet startar två lyssnare för aktörs tjänsten, V1 (befintlig) och V2_1 lyssnaren. Uppgradera aktörs tjänsten med den här ändringen.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Uppgradera aktörs klienterna när du har slutfört den tidigare uppgraderingen.
   Det här steget ser till att aktörs-proxyn använder fjärr V2_1s stacken.

3. Det här är valfritt. Ändra föregående attribut för att ta bort v1-lyssnaren.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Skådespelare som använder Remoting v2-stacken

Med version 2,8 NuGet-paketet kan användarna nu använda Remoting v2-stacken, som fungerar bättre och innehåller funktioner som anpassad serialisering. Fjärr kommunikation v2 är inte bakåtkompatibel med den befintliga Remoting-stacken (nu kallat v1 Remoting-stacken).

Följande ändringar krävs för att använda fjärr kommunikation v2-stacken.

1. Lägg till följande Assembly-attribut på aktörens gränssnitt.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Skapa och uppgradera aktörs tjänsten och aktörens klient projekt för att börja använda v2-stacken.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Uppgradera skådespelare-tjänsten till fjärr kommunikation v2-stacken utan att påverka tjänstens tillgänglighet

Den här ändringen är en uppgradering i två steg. Följ stegen i den här sekvensen.

1. Lägg till följande Assembly-attribut på aktörens gränssnitt. Det här attributet startar två lyssnare för aktörs tjänsten, V1 (befintlig) och v2-lyssnaren. Uppgradera aktörs tjänsten med den här ändringen.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Uppgradera aktörs klienterna när du har slutfört den tidigare uppgraderingen.
   Det här steget ser till att aktörens proxy använder Remoting v2-stacken.

3. Det här är valfritt. Ändra föregående attribut för att ta bort v1-lyssnaren.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
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
