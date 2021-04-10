---
title: Översikt över Azure Service Fabric aktörens livs cykel
description: Förklarar Service Fabric pålitliga aktörs livs cykel, skräp insamling och manuellt ta bort aktörer och deras status
ms.topic: conceptual
ms.date: 10/06/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 88db4bb2376cbc418d6954e274a18a6c18a280d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576051"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Aktörs livs cykel, automatisk skräp insamling och manuell borttagning
En aktör aktive ras första gången ett anrop görs till någon av dess metoder. En aktör inaktive ras (skräp insamlad av aktörernas körningar) om den inte används under en konfigurerbar tids period. En aktör och dess tillstånd kan också tas bort manuellt när som helst.

## <a name="actor-activation"></a>Aktörs aktivering
När en aktör aktive ras inträffar följande:

* När ett samtal skickas till en aktör och det inte redan är aktivt skapas en ny aktör.
* Aktörens tillstånd läses in om det är i underhålls läge.
* `OnActivateAsync`(C#) eller `onActivateAsync` (Java) metoden (som kan åsidosättas i aktörs implementeringen) anropas.
* Aktören anses nu vara aktiv.

## <a name="actor-deactivation"></a>Aktörs inaktive ring
När en aktör är inaktive rad inträffar följande:

* När en aktör inte används under en viss tids period, tas den bort från tabellen med aktiva aktörer.
* `OnDeactivateAsync`(C#) eller `onDeactivateAsync` (Java) metoden (som kan åsidosättas i aktörs implementeringen) anropas. Detta rensar alla timers för aktören. Aktörs åtgärder som tillstånds ändringar ska inte anropas från den här metoden.

> [!TIP]
> Infrastrukturen för Fabric-aktörer genererar vissa [händelser som rör aktörs aktivering och inaktive](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters)ring. De är användbara i diagnostik-och prestanda övervakning.
>
>

### <a name="actor-garbage-collection"></a>Skräp insamling för aktör
När en aktör är inaktive rad släpps referenser till aktörs objekt och kan samlas in normalt av skräp insamlaren Common Language Runtime (CLR) eller Java Virtual Machine (JVM). Skräp insamling rensar bara aktörs objekt. Det tar **inte** bort tillstånd som lagras i aktörens tillstånds hanterare. Nästa gången aktören aktive ras skapas ett nytt aktörs objekt och dess tillstånd återställs.

Vad räknas som "används" för inaktive ring och skräp insamling?

* Tar emot ett samtal
* `IRemindable.ReceiveReminderAsync` anropad metod (gäller endast om aktören använder påminnelser)

> [!NOTE]
> om aktören använder timers och dess timer-återanrop anropas, räknas den **inte** som "används".
>
>

Innan vi går in på Detaljer om inaktive ring är det viktigt att definiera följande villkor:

* *Genomsöknings intervall*. Detta är det intervall med vilket aktörernas körning skannar sin aktiva skådespelare tabell för aktörer som kan inaktive ras och skräp insamlas. Standardvärdet för detta är 1 minut.
* *Tids gräns för inaktivitet*. Detta är den tid som en aktör måste fortsätta att vara oanvänd (inaktiv) innan den kan inaktive ras och skräp insamlas. Standardvärdet för detta är 60 minuter.

Normalt behöver du inte ändra dessa standardinställningar. Om det behövs kan du dock ändra dessa intervall `ActorServiceSettings` när du registrerar din [aktörs tjänst](service-fabric-reliable-actors-platform.md):

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
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
    }
}
```
För varje aktiv aktör håller aktörs körningen reda på hur lång tid det har varit inaktivt (dvs. inte används). Aktörens körning kontrollerar var och en av aktörerna var `ScanIntervalInSeconds` för att se om den kan vara skräp insamlad och markerar den om den har varit inaktiv `IdleTimeoutInSeconds` .

När en aktör används återställs tiden till 0. Efter detta kan aktören bara samlas in om den är inaktiv för `IdleTimeoutInSeconds` . Kom ihåg att en aktör anses ha använts om antingen en aktörs gränssnitts metod eller en påminnelse om åter kallelse av aktör körs. En aktör anses **inte** ha använts om dess timer-motanrop körs.

Följande diagram visar livs cykeln för en enskild aktör för att illustrera dessa begrepp.

![Exempel på inaktiv tid][1]

Exemplet visar effekten av aktörs metod anrop, påminnelser och timers för den här aktörens livs längd. Följande punkter om exemplet är värda att nämna:

* ScanInterval och IdleTimeout har angetts till 5 respektive 10. (Enheter spelar ingen roll här eftersom vårt syfte bara är att illustrera begreppet.)
* Genomsökningen av aktörer som ska vara skräp insamlad sker i T = 0, 5, 10, 15, 20, 25, som definieras av genomsöknings intervallet 5.
* En periodisk timer utlöses vid T = 4, 8, 12, 16, 20, 24 och återanropet körs. Den påverkar inte inaktiv tid för aktören.
* Ett aktörs metod anrop vid T = 7 återställer tiden för inaktivitet till 0 och skjuter upp skräp insamling i aktören.
* En påminnelse om aktör återanrop körs vid T = 14 och förskjuter din aktörs skräp insamling ytterligare.
* Under skräp insamlings skanningen vid T = 25, överskrider aktörens inaktivitet för inaktivitet på 10, och aktören har skräpinsamlats.

En aktör kommer aldrig att vara skräp insamlad när den kör någon av dess metoder, oavsett hur lång tid det tar att köra metoden. Som tidigare nämnts förhindrar körningen av aktörens gränssnitts metoder och återställnings-återanrop skräp insamling genom att återställa aktörens inaktivitet till 0. Körningen av timer-återanrop återställs inte inaktivitet till 0. Skräp insamlingen av aktören skjuts upp tills det timer-motanrop som slutförts har körts.

## <a name="manually-deleting-actors-and-their-state"></a>Ta bort aktörer och deras status manuellt
Skräp insamling av inaktiverade aktörer rensar bara aktörs objekt, men tar inte bort data som lagras i en aktörs tillstånds hanterare. När en aktör återaktiveras, blir dess data återigen tillgängliga för den via tillstånds hanteraren. I de fall där aktörer lagrar data i tillstånds hanteraren och inaktive ras men aldrig aktive ras på nytt, kan det vara nödvändigt att rensa data.  Exempel på hur du tar bort aktörer finns i [ta bort aktörer och deras tillstånd](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Nästa steg
* [Timers för aktör och påminnelser](service-fabric-reliable-actors-timers-reminders.md)
* [Aktörs händelser](service-fabric-reliable-actors-events.md)
* [Aktör återinträde](service-fabric-reliable-actors-reentrancy.md)
* [Aktörens diagnostik och prestanda övervakning](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentation om aktörs-API-referens](/previous-versions/azure/dn971626(v=azure.100))
* [Exempel kod för C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-exempel kod](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
