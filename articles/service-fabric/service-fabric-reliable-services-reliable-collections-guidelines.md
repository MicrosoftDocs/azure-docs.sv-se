---
title: Rikt linjer för pålitliga samlingar
description: Rikt linjer och rekommendationer för att använda Service Fabric pålitliga samlingar i ett Azure Service Fabric-program.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: f12db76f324d07c178b49150d4e574476e7d9929
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784333"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Rikt linjer och rekommendationer för tillförlitliga samlingar i Azure Service Fabric
Det här avsnittet innehåller rikt linjer för att använda pålitliga tillstånds hanteraren och pålitliga samlingar. Målet är att hjälpa användarna att undvika vanliga fall GRO par.

Rikt linjerna är ordnade som enkla rekommendationer som *följer villkoren i* *åtanke*, *Undvik* och *inte.*

* Ändra inte ett objekt av en anpassad typ som returneras av Läs åtgärder (till exempel `TryPeekAsync` eller `TryGetValueAsync` ). Tillförlitliga samlingar, precis som samtidiga samlingar, returnerar en referens till objekten och inte en kopia.
* Skapa djup kopiera det returnerade objektet av en anpassad typ innan du ändrar det. Eftersom structs och inbyggda typer är direkt behöver du inte göra en djup kopia av dem om de inte innehåller fält eller egenskaper som du vill ändra.
* Använd inte `TimeSpan.MaxValue` för timeout. Tids gränser ska användas för att identifiera död lägen.
* Använd inte en transaktion när den har genomförts, avbrutits eller tagits bort.
* Använd inte en uppräkning utanför transaktions omfånget som den skapades i.
* Skapa inte en transaktion i en annan transaktions `using` instruktion eftersom den kan orsaka död lägen.
* Skapa inte ett tillförlitligt tillstånd med `IReliableStateManager.GetOrAddAsync` och Använd det pålitliga läget i samma transaktion. Detta resulterar i en InvalidOperationException.
* Kontrol lera att din `IComparable<TKey>` implementering är korrekt. Systemet använder beroende av `IComparable<TKey>` för att sammanfoga kontroll punkter och rader.
* Använd uppdaterings Lås när du läser ett objekt med avsikt att uppdatera det för att förhindra en viss klass av död lägen.
* Överväg att behålla antalet pålitliga samlingar per partition till mindre än 1000. Föredra pålitliga samlingar med fler objekt över mer pålitliga samlingar med färre objekt.
* Överväg att behålla dina objekt (till exempel TKey + TValue för tillförlitlig ord lista) under 80 KByte: mindre än bättre. Detta minskar mängden stora objekts heap-användning samt disk-och nätverks-i/o-krav. Det minskar ofta replikeringen av duplicerade data när bara en liten del av värdet uppdateras. Vanligt sätt att uppnå detta i en tillförlitlig ord lista, är att dela upp dina rader i till flera rader.
* Överväg att använda säkerhets kopierings-och återställnings funktioner för haveri beredskap.
* Undvik att blanda åtgärder för enskilda entiteter och flera entiteter (t. ex. `GetCountAsync` `CreateEnumerableAsync` ) i samma transaktion på grund av olika isolerings nivåer.
* Hantera InvalidOperationException. Användar transaktioner kan avbrytas av systemet av olika orsaker. Till exempel när den Reliable State Manager ändrar rollen som den är av primär eller när en tids krävande transaktion blockerar trunkering av transaktions loggen. I sådana fall kan användaren få InvalidOperationException som anger att transaktionen redan har avbrutits. Anta att transaktionen inte har begärts av användaren, det bästa sättet att hantera det här undantaget är att ta bort transaktionen, kontrol lera om token för annullering har signaler ATS (eller att replikens roll har ändrats), och om du inte vill skapa en ny transaktion och försöka igen.  

Här är några saker att tänka på:

* Standard tids gränsen är fyra sekunder för alla API: er för tillförlitliga samlingar. De flesta användare bör använda standard tids gränsen.
* Standard-token för annullering är `CancellationToken.None` i alla API: er för Reliable Collections.
* Nyckel typ parametern (*TKey*) för en tillförlitlig ord lista måste implementeras korrekt `GetHashCode()` och `Equals()` . Nycklar måste vara oföränderliga.
* För att uppnå hög tillgänglighet för de pålitliga samlingarna måste varje tjänst ha minst en mål storlek och minsta replik uppsättnings storlek på 3.
* Läs åtgärder på den sekundära versionen kan läsa versioner som inte är kvorum allokerade.
  Det innebär att en version av data som läses från en enda sekundär kan vara falskt.
  Läsningar från primär är alltid stabila: kan aldrig vara falskt.
* Säkerhet/sekretess för de data som behålls av ditt program i en tillförlitlig samling är ditt beslut och omfattas av de skydd som tillhandahålls av lagrings hanteringen. säga. Kryptering av operativ system diskar kan användas för att skydda dina data i vila.
* `ReliableDictionary` uppräkningen använder en sorterad data struktur sorterad efter nyckel. För att göra uppräkningen effektiv läggs incheckningarna till i en tillfällig hash och senare flyttas till den primära sorterade data struktur postens kontroll punkt. Tillägg/uppdateringar/borttagningar har bästa tänkbara fall för körning av O (1) och sämsta fall körning av O (log n) vid verifierings kontroller av förekomsten av nyckeln. Get kan vara O (1) eller O (log n) beroende på om du läser från en nyligen genomförd eller en äldre incheckning.

## <a name="volatile-reliable-collections"></a>Flyktigt pålitliga samlingar
När du bestämmer dig för att använda volatile Reliable Collections bör du tänka på följande:

* ```ReliableDictionary``` har flyktig support
* ```ReliableQueue``` har flyktig support
* ```ReliableConcurrentQueue``` saknar stöd för flyktighet
* Beständiga tjänster kan inte göras temporärt. Om du ändrar ```HasPersistedState``` flaggan till ```false``` måste du återskapa hela tjänsten från grunden
* Det går inte att göra temporära tjänster bestående. Om du ändrar ```HasPersistedState``` flaggan till ```true``` måste du återskapa hela tjänsten från grunden
* ```HasPersistedState``` är en konfiguration på service nivå. Det innebär att **alla** samlingar antingen är bestående eller temporära. Det går inte att blanda temporära och beständiga samlingar
* Kvorum förlust av en flyktig partition resulterar i fullständig data förlust
* Säkerhets kopiering och återställning är inte tillgängligt för temporära tjänster

## <a name="next-steps"></a>Nästa steg
* [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transaktioner och lås](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Hantera data
  * [Säkerhets kopiering och återställning](service-fabric-reliable-services-backup-restore.md)
  * [Aviseringar](service-fabric-reliable-services-notifications.md)
  * [Serialisering och uppgradering](service-fabric-application-upgrade-data-serialization.md)
  * [Tillförlitlig tillstånds hanterarens konfiguration](service-fabric-reliable-services-configuration.md)
* Andra
  * [Reliable Services snabb start](service-fabric-reliable-services-quick-start.md)
  * [Referens för utvecklare för tillförlitliga samlingar](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
