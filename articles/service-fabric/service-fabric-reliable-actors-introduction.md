---
title: Översikt över Service Fabric Reliable Actors
description: Introduktion till Service Fabric Reliable Actors programmerings modellen baserat på mönstret för virtuell aktör.
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 1a8a7003a69deaf6b74d6fbb8a3cf84b0a78eecf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576391"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Introduktion till Service Fabric Reliable Actors
Reliable Actors är ett Service Fabric program ramverk baserat på mönstret för [virtuell aktör](https://research.microsoft.com/en-us/projects/orleans/) . Reliable Actors-API: et tillhandahåller en enda trådad programmerings modell som bygger på de skalbarhets-och Tillförlitlighets garantier som tillhandahålls av Service Fabric.

## <a name="what-are-actors"></a>Vad är aktörer?
En aktör är en isolerad, oberoende enhet av beräkning och tillstånd med en enkel tråd körning. [Aktörs mönstret](https://en.wikipedia.org/wiki/Actor_model) är en beräknings modell för samtidiga eller distribuerade system där ett stort antal av dessa aktörer kan köras samtidigt och oberoende av varandra. Aktörer kan kommunicera med varandra och de kan skapa fler aktörer.

### <a name="when-to-use-reliable-actors"></a>När du ska använda Reliable Actors
Service Fabric Reliable Actors är en implementering av aktörens design mönster. Precis som med ett mönster för program varu design görs beslutet om att använda ett speciellt mönster baserat på om ett program design problem passar mönstret eller inte.

Även om form givnings mönstret för aktören kan vara en bra anpassning till ett antal distribuerade system problem och scenarier, bör du tänka över begränsningarna i mönstret och ramverket som implementerar det. Som allmän vägledning bör du ta hänsyn till aktörens mönster för att modellera ditt problem eller scenariot om:

* Ditt problem utrymme innebär ett stort antal (tusentals eller mer) av små, oberoende och isolerade enheter av tillstånd och logik.
* Du vill arbeta med entrådade objekt som inte kräver betydande interaktion från externa komponenter, inklusive fråga om tillstånd i en uppsättning aktörer.
* Dina aktörs instanser blockerar inte anropare med oförutsägbara fördröjningar genom att skicka I/O-åtgärder.

## <a name="actors-in-service-fabric"></a>Aktörer i Service Fabric
I Service Fabric implementeras aktörer i Reliable Actors Framework: ett mönsterbaserade program ramverk som bygger på [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md). Varje tillförlitlig aktörs tjänst du skriver är i själva verket en partitionerad, tillstånds känslig tillförlitlig tjänst.

Varje aktör definieras som en instans av en aktörs typ, som är identisk med hur ett .NET-objekt är en instans av en .NET-typ. Det kan till exempel finnas en aktörs typ som implementerar funktionerna i en kalkylator och det kan finnas många aktörer av den typen som distribueras på olika noder i ett kluster. Varje sådan aktör identifieras unikt av ett skådespelare-ID.

## <a name="actor-lifetime"></a>Aktörs livs längd
Service Fabric aktörer är virtuella, vilket innebär att deras livs längd inte är kopplad till sin minnes intern representation. Därför behöver de inte skapas eller förstöras explicit. Reliable Actors runtime aktiverar automatiskt en aktör första gången den får en begäran om detta skådespelare-ID. Om en aktör inte används under en viss tids period samlas det in i minnet av Reliable Actors runtime-skräpe. Den kommer också att ha kunskap om aktörens existens om den måste återaktiveras senare. Mer information finns i [livs cykel för aktör och skräp insamling](service-fabric-reliable-actors-lifecycle.md).

Den här virtuella aktörens livs längds abstraktion medför vissa varningar till följd av den virtuella aktörs modellen, och i själva verket är den Reliable Actors implementeringen avvikande vid tillfällen från den här modellen.

* En aktör aktive ras automatiskt (vilket gör att ett aktörs objekt kan konstrueras) första gången ett meddelande skickas till dess skådespelare-ID. Efter en viss tids period har aktörens objekt skräp insamlat. I framtiden, med hjälp av skådespelare-ID igen, gör ett nytt aktörs objekt konstruerat. En aktörs tillstånd utlevererar objektets livs längd när den lagras i tillstånds hanteraren.
* Att anropa en aktörs metod för ett skådespelare-ID aktiverar aktören. Av den anledningen har aktörs typen konstruktorn som anropas implicit av körnings miljön. Därför kan klient koden inte skicka parametrar till aktörs typens konstruktor, men parametrar kan skickas till aktörens konstruktor av själva tjänsten. Resultatet är att aktörerna kan konstrueras i ett delvis initierat tillstånd med tiden som andra metoder anropas, om aktören kräver initierings parametrar från klienten. Det finns ingen enkel start punkt för aktivering av en aktör från klienten.
* Även om Reliable Actors implicit skapa aktörs objekt; Du kan uttryckligen ta bort en aktör och dess tillstånd.

## <a name="distribution-and-failover"></a>Distribution och redundans
För att tillhandahålla skalbarhet och tillförlitlighet distribuerar Service Fabric aktörerna i klustret och migrerar automatiskt dem från felaktiga noder till felfria som nödvändiga. Detta är en abstraktion över en [partitionerad, tillstånds känslig tillförlitlig tjänst](service-fabric-concepts-partitioning.md). Distribution, skalbarhet, tillförlitlighet och automatisk redundans tillhandahålls på grund av det faktum att aktörerna körs i en tillstånds känslig tillförlitlig tjänst som kallas *aktörs tjänst*.

Aktörerna distribueras över-partitionerna i aktörs tjänsten och dessa partitioner distribueras mellan noderna i ett Service Fabric kluster. Varje tjänstmall innehåller en uppsättning aktörer. Service Fabric hanterar distribution och redundans för-tjänstepartitioner.

Till exempel skulle en aktörs tjänst med nio partitioner som distribueras till tre noder som använder standardplaceringen av aktörs partition distribueras på rätt sätt:

![Reliable Actors distribution][2]

Aktörs ramverket hanterar partition schema-och nyckel intervalls inställningar åt dig. Detta gör det enklare att välja några alternativ, men det innebär också en del överväganden:

* Med Reliable Services kan du välja ett partitionerings schema, nyckel intervall (när du använder ett intervall partitionerings schema) och antalet partitioner. Reliable Actors är begränsat till intervall partitionerings schemat (det enhetliga Int64-schemat) och kräver att du använder det fullständiga Int64-nyckel intervallet.
* Som standard placeras aktörer slumpmässigt i partitioner som resulterar i enhetlig distribution.
* Eftersom aktörerna placeras slumpmässigt, bör det förväntas att aktörs åtgärder alltid ska kräva nätverkskommunikation, inklusive serialisering och deserialisering av metod anrops data, vilket ger svars tid och kostnader.
* I avancerade scenarier är det möjligt att kontrol lera placeringen av aktörens partition genom att använda Int64 skådespelare-ID: n som mappar till vissa partitioner. Detta kan dock leda till en obalanserad distribution av aktörer mellan partitioner.

Mer information om hur aktörs tjänster partitioneras finns i [partitionerings koncept för aktörer](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

## <a name="actor-communication"></a>Aktörs kommunikation
Aktörs interaktioner definieras i ett gränssnitt som delas av aktören som implementerar gränssnittet och klienten som hämtar en proxy till en aktör via samma gränssnitt. Eftersom det här gränssnittet används för att anropa aktörs metoder asynkront måste varje metod i gränssnittet vara uppgifts RETUR.

Metod anrop och deras svar resulterar i slut på nätverks begär anden i klustret, så argumenten och resultat typerna för de uppgifter som de returnerar måste serialiseras av plattformen. De måste särskilt vara serialiserbara med [data kontrakt](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

### <a name="the-actor-proxy"></a>Aktörens proxy
Reliable Actors klient-API: et tillhandahåller kommunikation mellan en aktörs instans och en aktörs klient. För att kommunicera med en aktör skapar en klient ett aktörs objekt som implementerar aktörs gränssnittet. Klienten interagerar med aktören genom att anropa metoder på objektet proxy. Aktörs-proxyn kan användas för kommunikation mellan klient och skådespelare och aktör.

```csharp
// Create a randomly distributed actor ID
ActorId actorId = ActorId.CreateRandom();

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
IMyActor myActor = ActorProxy.Create<IMyActor>(actorId, new Uri("fabric:/MyApp/MyActorService"));

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
await myActor.DoWorkAsync();
```

```java
// Create actor ID with some name
ActorId actorId = new ActorId("Actor1");

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
MyActor myActor = ActorProxyBase.create(actorId, new URI("fabric:/MyApp/MyActorService"), MyActor.class);

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
myActor.DoWorkAsync().get();
```


Observera att de två informations delarna som används för att skapa aktörs-proxyobjekt är skådespelare-ID och program namn. Aktörs-ID: t identifierar aktören unikt, medan program namnet identifierar det [Service Fabric program](service-fabric-reliable-actors-platform.md#application-model) där aktören distribueras.

`ActorProxy`(C#)/ `ActorProxyBase` (Java)-klassen på klient sidan utför den nödvändiga lösningen för att hitta aktören efter ID och öppna en kommunikations kanal med den. Det försöker också att hitta aktören i händelse av kommunikations fel och redundans. Det innebär att meddelande leveransen har följande egenskaper:

* Leverans av meddelanden är bästa möjliga.
* Aktörer kan få dubbla meddelanden från samma klient.

## <a name="concurrency"></a>Samtidighet
Reliable Actors runtime ger en enkel, aktiv åtkomst modell för åtkomst till aktörs metoder. Det innebär att det inte går att aktivera mer än en tråd i en aktörs objekt kod när som helst. Med den här snabb åtkomsten fören Klars samtidiga system eftersom det inte behövs några synkroniseringsprogram för data åtkomst. Det innebär också att systemen måste utformas med särskilda överväganden för den enda trådbaserade åtkomst typen för varje aktörs instans.

* En enskild aktörs instans kan inte bearbeta mer än en begäran åt gången. En aktörs instans kan orsaka en Flask hals i data flödet om den förväntas hantera samtidiga begär Anden.
* Aktörer kan död lägen på varandra om det finns en cirkulär begäran mellan två aktörer när en extern begäran görs till en av aktörerna samtidigt. Aktörens körnings tid upphör automatiskt att svara på aktör samtal och genererar ett undantag till anroparen för att avbryta eventuella död situationer.

![Reliable Actors kommunikation][3]

### <a name="turn-based-access"></a>Turn-baserad åtkomst
En tur består av den fullständiga körningen av en aktörs metod som svar på en begäran från andra aktörer eller klienter, eller en fullständig körning av en [timer/påminnelse](service-fabric-reliable-actors-timers-reminders.md) motringning. Även om dessa metoder och återanrop är asynkrona, översätts inte aktörernas körningar. En tur måste slutföras helt innan en ny aktivering tillåts. Med andra ord måste en aktörs metod eller återanrop för timer/påminnelse som körs helt slutföras innan ett nytt anrop till en metod eller ett motanrop tillåts. En metod eller ett motanrop anses ha slutförts om körningen har returnerats från metoden eller återanropet och uppgiften som returnerades av metoden eller återanropet har slutförts. Det är värt att framhäva att den vridna samtidigheten respekteras på olika sätt, timers och återanrop.

Aktörs körningen tvingar fram en fast samtidighet genom att förvärva ett lås per aktör i början av en turn och släppa låset i slutet av tur och släpp. Det innebär att den tillvändiga samtidigheten tillämpas på per aktör och inte mellan aktörer. Aktörs metoder och återanrop till timer/påminnelser kan köras samtidigt för olika aktörer.

I följande exempel visas begreppen ovan. Överväg en aktörs typ som implementerar två asynkrona metoder (t. ex. *Method1* och *Method2*), en timer och en påminnelse. Diagrammet nedan visar ett exempel på en tids linje för körning av dessa metoder och återanrop åt två aktörer (*ActorId1* och *ActorId2*) som tillhör den här aktörs typen.

![Reliable Actors runtime-baserad samtidighet och åtkomst][1]

Det här diagrammet följer dessa konventioner:

* Varje lodrät linje visar det logiska flödet för körning av en metod eller ett återanrop för en viss aktörs räkning.
* De händelser som marker ATS på varje lodrät linje sker i kronologisk ordning, med nyare händelser som inträffar under äldre.
* Olika färger används för tids linjer som motsvarar olika aktörer.
* Markering används för att ange varaktigheten för när låset per aktör hålls för en metod eller ett motanrop.

Några viktiga saker att tänka på:

* Även om *Method1* körs på uppdrag av *ActorId2* som svar på klient begär ande *xyz789*, kommer en annan klientbegäran (*vi abc123*) att kräva att *Method1* körs av *ActorId2*. Den andra körningen av *Method1* startar dock inte förrän den tidigare körningen har slutförts. På samma sätt utlöses en påminnelse som registrerats av *ActorId2* medan *Method1* körs som svar på klient begär ande *xyz789*. Återanropet av påminnelsen utförs först när båda körningarna av *Method1* har slutförts. Allt detta beror på att den tidsbaserade samtidiga samtidigheten för *ActorId2* är aktive rad.
* På samma sätt tillämpas den ombaserade samtidigheten också för *ActorId1*, som demonstreras av körningen av *Method1*, *Method2* och timer-återanropet för *ActorId1* händer i serie.
* Körningen av *Method1* på uppdrag av *ActorId1* överlappar körningen på uppdrag av *ActorId2*. Detta beror på att den omvända samtidigheten endast tillämpas inom en aktör och inte mellan aktörer.
* I vissa av metod-/återanrops körningarna `Task` (C#)/ `CompletableFuture` (Java) som returnerades av metoden/motringningen slutförs efter att metoden returnerades. I vissa andra har den asynkrona åtgärden redan slutförts när metoden/motringningen returnerar. I båda fallen frigörs låset per aktör bara när både metoden/motringningen returnerar och den asynkrona åtgärden har slutförts.

### <a name="reentrancy"></a>Återinträde
I aktörernas körnings miljö tillåts återinträde som standard. Det innebär att om aktörs metoden *aktör a* anropar en metod på *aktör B*, som i sin tur anropar en annan metod på *aktör a*, tillåts den metoden att köras. Detta beror på att det är en del av samma logiska anrop i kontexten. Alla timer-och påminnelse anrop börjar med det nya logiska anrops sammanhanget. Se [Reliable Actors-återinträde](service-fabric-reliable-actors-reentrancy.md) för mer information.

### <a name="scope-of-concurrency-guarantees"></a>Omfattning av samtidighets garantier
Aktörernas körningar ger dessa samtidighets garantier i situationer där det styr anropet av dessa metoder. Den ger till exempel dessa garantier för de metod anrop som utförs som svar på en klientbegäran, samt för återanrop till timer och påminnelser. Men om aktörs koden direkt anropar dessa metoder utanför de mekanismer som tillhandahålls av aktörs körningen, kan körnings miljön inte tillhandahålla några samtidighet-garantier. Om metoden till exempel anropas i kontexten för en uppgift som inte är associerad med den uppgift som returneras av aktörs metoderna, kan körnings miljön inte tillhandahålla samtidighets garantier. Om metoden anropas från en tråd som aktören skapar på egen hand kan inte körningen heller tillhandahålla samtidighets garantier. För att du ska kunna utföra bakgrunds åtgärder bör därför aktörerna använda [aktörens timers och aktörs påminnelser](service-fabric-reliable-actors-timers-reminders.md) som respekterar en turn samtidighet.

## <a name="next-steps"></a>Nästa steg
Kom igång genom att skapa din första Reliable Actors-tjänst:
   * [Komma igång med Reliable Actors på .NET](service-fabric-reliable-actors-get-started.md)
   * [Komma igång med Reliable Actors i Java](./service-fabric-create-your-first-linux-application-with-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
