---
title: Reliable Actors på Service Fabric
description: Beskriver hur Reliable Actors skiktas på Reliable Services och använder funktionerna i Service Fabric-plattformen.
ms.topic: conceptual
ms.date: 3/9/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: e204b47b7fa9f528341507c315c926159d524e13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789590"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Hur Reliable Actors använder Service Fabrics plattformen
I den här artikeln förklaras hur Reliable Actors fungerar på Azure Service Fabric-plattformen. Reliable Actors köras i ett ramverk som är värd för en implementering av en tillstånds känslig tillförlitlig tjänst som kallas *aktörs tjänst*. Aktörs tjänsten innehåller alla komponenter som krävs för att hantera livs cykeln och meddelande sändning för dina aktörer:

* Aktörens körning hanterar livs cykeln, skräp insamling och framtvingar åtkomst med enkel tråd.
* En tjänst för kommunikation mellan en aktörs tjänst godkänner fjärråtkomst-anrop till aktörer och skickar dem till en dispatcher för att vidarebefordra till en lämplig aktörs instans.
* Aktörs leverantörs leverantören omsluter tillstånds leverantörer (till exempel den tillförlitliga samlingens tillstånds leverantör) och tillhandahåller ett kort för hantering av aktörs tillstånd.

Dessa komponenter utgör tillsammans de pålitliga aktörs ramverket.

## <a name="service-layering"></a>Tjänst skiktning
Eftersom aktörs tjänsten själva är en tillförlitlig tjänst är alla [program modeller](service-fabric-application-model.md), livs cykel, [paketering](service-fabric-package-apps.md), [distribution](service-fabric-deploy-remove-applications.md), uppgradering och skalning av Reliable Services att använda samma sätt för aktörs tjänster.

![Tjänst skiktning för skådespelare][1]

Föregående diagram visar förhållandet mellan Service Fabric program ramverk och användar kod. Blå element representerar Reliable Services Application Framework, orange representerar det tillförlitliga aktörs ramverket och grönt representerar användar kod.

I Reliable Services ärver tjänsten `StatefulService` klassen. Den här klassen härleds i sig själv från `StatefulServiceBase` (eller `StatelessService` för tillstånds lösa tjänster). I Reliable Actors använder du aktörs tjänsten. Aktörs tjänsten är en annan implementering av `StatefulServiceBase` klassen som implementerar aktörens mönster där dina aktörer körs. Eftersom aktörs tjänsten bara är en implementering av `StatefulServiceBase` , kan du skriva en egen tjänst som är härledd från `ActorService` och implementera funktioner på service nivå på samma sätt som när du ärver `StatefulService` , till exempel:

* Säkerhets kopiering och återställning av tjänsten.
* Delade funktioner för alla aktörer, till exempel en krets brytare.
* Fjärran rop i själva aktörs tjänsten och på varje enskild aktör.

Mer information finns i [implementera service nivå funktioner i aktörs tjänsten](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Programmodell
Aktörs tjänster är Reliable Services, så program modellen är densamma. Men med build-verktygen för aktörs ramverket genereras några av program modell filerna åt dig.

### <a name="service-manifest"></a>Tjänst manifest
Verktyget för att skapa aktörs ramverk genererar automatiskt innehållet i din aktörs tjänsts ServiceManifest.xml-fil. Den här filen innehåller:

* Aktörs tjänst typ. Typ namnet genereras baserat på din aktörs projekt namn. Baserat på det persistence attributet på din aktör anges även flaggan HasPersistedState.
* Kod paket.
* Konfigurations paket.
* Resurser och slut punkter.

### <a name="application-manifest"></a>Programmanifest
Aktörs ramverkets build-verktyg skapar automatiskt en standard tjänst definition för din aktörs tjänst. Bygg verktygen fyller i standard tjänst egenskaperna:

* Antalet replik uppsättningar bestäms av det persistence attributet på din aktör. Varje gången det beständiga attributet på din aktör ändras återställs antalet replik uppsättningar i standard tjänst definitionen.
* Partitions schema och intervall anges till enhetligt Int64 med det fullständiga Int64-nyckelvärdet.

## <a name="service-fabric-partition-concepts-for-actors"></a>Metoder för Service Fabric partition för aktörer
Aktörs tjänster är partitionerade tillstånds känsliga tjänster. Varje partition i en aktörs tjänst innehåller en uppsättning aktörer. Tjänste partitioner distribueras automatiskt över flera noder i Service Fabric. Aktörs instanser distribueras som ett resultat.

![Skådespelare partitionering och distribution][5]

Reliable Services kan skapas med olika partitioner och partitionerings nyckel intervall. Aktörs tjänsten använder Int64 partitionerings schema med det fullständiga Int64-nyckel intervallet för att mappa aktörer till partitioner.

### <a name="actor-id"></a>Skådespelare-ID
Varje aktör som skapas i tjänsten har ett unikt ID som är kopplat till den, som representeras av `ActorId` klassen. `ActorId` är ett ogenomskinligt ID-värde som kan användas för enhetlig distribution av aktörer över diskpartitioner genom att generera slumpmässiga ID: n:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Varje `ActorId` hash-kodas till en Int64. Detta är anledningen till att aktörs tjänsten måste använda ett Int64-partitionerings schema med ett fullständigt Int64-nyckelpar. Anpassade ID-värden kan dock användas för en `ActorID` , inklusive GUID/UUID-värden, strängar och Int64s.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

När du använder GUID/UUID: er och strängar, hashas värdena till en Int64. Men när du explicit anger en Int64 till en `ActorId` , kommer Int64 att mappa direkt till en partition utan ytterligare hashing. Du kan använda den här metoden för att styra vilken partition som aktörerna placeras i.


## <a name="next-steps"></a>Nästa steg
* [Hantering av aktörs tillstånd](service-fabric-reliable-actors-state-management.md)
* [Aktörs livs cykel och skräp insamling](service-fabric-reliable-actors-lifecycle.md)
* [Dokumentation om aktörers API-referens](/dotnet/api/microsoft.servicefabric.actors)
* [.NET-exempel kod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-exempel kod](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
