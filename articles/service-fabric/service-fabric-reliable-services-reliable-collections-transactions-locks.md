---
title: Transaktioner och lås lägen i Reliable Collections
description: Azure Service Fabric Reliable State Manager och Reliable Collections-transaktioner och-låsning.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: 5d2cbb517ea5ca45697cd9124b82e9ef13dd32db
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784350"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transaktioner och lås lägen i Azure Service Fabric Reliable Collections

## <a name="transaction"></a>Transaktion

En transaktion är en sekvens med åtgärder som utförs som en enda logisk arbets enhet. Den visar gemensamma egenskaper för [syra](https://en.wikipedia.org/wiki/ACID) (*Atomicitet*, *konsekvens*, *isolering*, *hållbarhet*) för databas transaktioner:

* **Atomicer**: en transaktion måste vara en atomisk arbets enhet. Med andra ord utförs antingen alla data ändringar eller ingen av dem.
* **Konsekvens**: när det är slutfört måste en transaktion lämna alla data i ett konsekvent tillstånd. Alla interna data strukturer måste vara korrekta i slutet av transaktionen.
* **Isolering**: ändringar gjorda av samtidiga transaktioner måste isoleras från ändringar som gjorts av andra samtidiga transaktioner. Isolerings nivån som används för en åtgärd i en [ITransaction](/dotnet/api/microsoft.servicefabric.data.itransaction) bestäms av [IReliableState](/dotnet/api/microsoft.servicefabric.data.ireliablestate) som utför åtgärden.
* **Hållbarhet**: när en transaktion har slutförts, är dess effekter permanent på plats i systemet. Ändringarna är kvar även om ett systemfel inträffar.

### <a name="isolation-levels"></a>Isoleringsnivåer

Isolerings nivån definierar i vilken grad transaktionen måste isoleras från ändringar som gjorts av andra transaktioner.
Det finns två isolerings nivåer som stöds i pålitliga samlingar:

* **Repeterbar läsning**: anger att instruktioner inte kan läsa data som har ändrats men ännu inte har allokerats av andra transaktioner och att inga andra transaktioner kan ändra data som har lästs av den aktuella transaktionen tills den aktuella transaktionen har slutförts.
* **Ögonblicks bild**: anger att data som lästs av valfri instruktion i en transaktion är den transaktions konsekventa versionen av de data som fanns i början av transaktionen.
  Transaktionen kan bara identifiera data ändringar som har genomförts innan transaktionen startades.
  Data ändringar som gjorts av andra transaktioner efter det att den aktuella transaktionen har startats är inte synliga för instruktioner som körs i den aktuella transaktionen.
  Detta är som om uttrycken i en transaktion får en ögonblicks bild av de allokerade data som fanns i början av transaktionen.
  Ögonblicks bilder är konsekventa i pålitliga samlingar.

Reliable Collections väljer automatiskt den isolerings nivå som ska användas för en specifik Läs åtgärd beroende på åtgärden och replik rollen vid tidpunkten för skapandet av transaktionen.
Följande är den tabell som visar isolerings nivå standarder för tillförlitliga ord listor och Queue-åtgärder.

| Åtgärd \ roll | Primär | Sekundär |
| --- |:--- |:--- |
| En entitet har lästs |Upprepnings bar läsning |Ögonblicksbild |
| Uppräkning, antal |Ögonblicksbild |Ögonblicksbild |

> [!NOTE]
> Vanliga exempel för åtgärder med enskild entitet är `IReliableDictionary.TryGetValueAsync` , `IReliableQueue.TryPeekAsync` .
> 

Både den pålitliga ord listan och den pålitliga kön har stöd för att *läsa dina skrivningar*.
Alla Skriv åtgärder i en transaktion är med andra ord synliga för en följande läsning som tillhör samma transaktion.

## <a name="locks"></a>Lås

I Reliable Collections implementerar alla transaktioner rigoröst två fas låsningar: en transaktion släpper inte de lås som har förvärv ATS förrän transaktionen avslutas med antingen ett avbrott eller en incheckning.

En tillförlitlig ord lista använder låsning på radnivå för alla åtgärder på samma entitet.
Reliable Queue-handelsmässiga från samtidighet för en strikt transaktionell FIFO-egenskap.
En tillförlitlig kö använder lås på åtgärds nivå som tillåter en transaktion med `TryPeekAsync` och/eller `TryDequeueAsync` en transaktion med `EnqueueAsync` i taget.
Observera att för att bevara FIFO, om en `TryPeekAsync` eller en `TryDequeueAsync` gång ser att den Reliable kön är tom, kommer de också att låsas `EnqueueAsync` .

Skriv åtgärder tar alltid exklusiva lås.
Vid Läs åtgärder är låsningen beroende av några faktorer:

- Alla Läs åtgärder som utfördes med ögonblicks bild isolering är låsnings fria.
- Alla upprepnings bara Läs åtgärder tar som standard delade lås.
- För alla Läs åtgärder som stöder repeterbar läsning kan användaren dock be om ett uppdaterings lås i stället för det delade låset.
Ett uppdaterings lås är ett asymmetriskt lås som används för att förhindra en vanlig form av död läge som inträffar när flera transaktioner låser resurser för potentiella uppdateringar vid ett senare tillfälle.

Matrisen för att låsa kompatibilitet finns i följande tabell:

| Begäran har beviljats | Inget | Delad | Uppdatera | Öppna |
| --- |:--- |:--- |:--- |:--- |
| Delad |Ingen konflikt |Ingen konflikt |Konflikt |Konflikt |
| Uppdatera |Ingen konflikt |Ingen konflikt |Konflikt |Konflikt |
| Öppna |Ingen konflikt |Konflikt |Konflikt |Konflikt |

Timeout-argumentet i Reliable Collections-API: er används för identifiering av död läge.
Två transaktioner (T1 och T2) försöker till exempel läsa och uppdatera K1.
Det är möjligt för dem att deadlock, eftersom de båda har ett delat lås.
I det här fallet har en eller båda åtgärderna nått sin tids gräns. I det här scenariot kan ett uppdaterings lås förhindra ett sådant död läge.

## <a name="next-steps"></a>Nästa steg

* [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Reliable Services meddelanden](service-fabric-reliable-services-notifications.md)
* [Reliable Services säkerhets kopiering och återställning (haveri beredskap)](service-fabric-reliable-services-backup-restore.md)
* [Tillförlitlig tillstånds hanterarens konfiguration](service-fabric-reliable-services-configuration.md)
* [Referens för utvecklare för tillförlitliga samlingar](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
