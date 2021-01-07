---
title: Databas transaktioner och optimistisk concurrency-kontroll i Azure Cosmos DB
description: I den här artikeln beskrivs databas transaktioner och optimistisk concurrency-kontroll i Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 96652b2a1eb35668bd8a810b309ab31cec5afdb7
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97967267"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Kontroll över transaktioner och optimistisk samtidighet
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Databas transaktioner tillhandahåller en säker och förutsägbar programmerings modell för att hantera samtidiga ändringar av data. Traditionella Relations databaser, som SQL Server, låter dig skriva affärs logik med lagrade procedurer och/eller utlösare, skicka den till servern för körning direkt i databas motorn. Med traditionella Relations databaser måste du hantera två olika programmeringsspråk (icke-transaktionell) programprogrammeringsspråk, till exempel Java Script, python, C#, Java osv. och det transaktions programmerings språk (t. ex. T-SQL) som körs internt av databasen.

Databas motorn i Azure Cosmos DB har stöd för fullständiga syror (atomiska, konsekvens, isolering, hållbarhet)-kompatibla transaktioner med ögonblicks bild isolering. Alla databas åtgärder inom omfånget för en behållares [logiska partition](partitioning-overview.md) är transaktionella exekverade i databas motorn som är värd för partitionens replik. Dessa åtgärder omfattar både skrivning (uppdatering av ett eller flera objekt i den logiska partitionen) och Läs åtgärder. I följande tabell visas olika åtgärder och transaktions typer:

| **Åtgärd**  | **Åtgärds typ** | **Transaktion för enkel eller flera objekt** |
|---------|---------|---------|
| Infoga (utan en utlösare före/efter) | Skriva | Transaktion för enskilt objekt |
| Infoga (med en utlösare före/efter) | Skriv och Läs | Transaktion med flera objekt |
| Ersätt (utan en utlösare före/efter) | Skriva | Transaktion för enskilt objekt |
| Ersätt (med en utlösare före/efter) | Skriv och Läs | Transaktion med flera objekt |
| Upsert (utan en utlösare före/efter) | Skriva | Transaktion för enskilt objekt |
| Upsert (med en utlösare före/efter) | Skriv och Läs | Transaktion med flera objekt |
| Ta bort (utan en utlösare före/efter) | Skriva | Transaktion för enskilt objekt |
| Ta bort (med en utlösare före/efter) | Skriv och Läs | Transaktion med flera objekt |
| Kör lagrad procedur | Skriv och Läs | Transaktion med flera objekt |
| Systemet initierade körningen av en sammanfognings procedur | Skriva | Transaktion med flera objekt |
| Systemet har initierat körningen av borttagning av objekt baserat på ett objekts förfallo datum (TTL) | Skriva | Transaktion med flera objekt |
| Läsa | Läsa | Transaktion med enskilt objekt |
| Ändringsflöde | Läs | Transaktion med flera objekt |
| Sid läsning | Läs | Transaktion med flera objekt |
| Sid brytnings fråga | Läs | Transaktion med flera objekt |
| Kör UDF som en del av den sid brytnings fråga | Läs | Transaktion med flera objekt |

## <a name="multi-item-transactions"></a>Transaktioner med flera objekt

Med Azure Cosmos DB kan du skriva [lagrade procedurer, pre/post-utlösare, användardefinierade funktioner (UDF: er)](stored-procedures-triggers-udfs.md) och sammanfognings procedurer i Java Script. Azure Cosmos DB inbyggt stöder JavaScript-körning i sin databas motor. Du kan registrera lagrade procedurer, för-/post-utlösare, användardefinierade funktioner (UDF: er) och sammanfognings procedurer på en behållare och senare köra dem med transaktion i Azure Cosmos Database Engine. Genom att skriva program logik i Java Script kan du använda naturliga uttryck för kontroll flöde, variabel omfattning, tilldelning och integrering av undantags hanterings primitiver i databas transaktionerna direkt i JavaScript-språket.

JavaScript-baserade lagrade procedurer, utlösare, UDF: er och sammanfognings procedurer omsluts till en post i en omgivande syra med ögonblicks bild isolering för alla objekt i den logiska partitionen. Om JavaScript-programmet genererar ett undantag under körningen avbryts hela transaktionen och återställs. Den resulterande programmerings modellen är enkel men kraftfull. JavaScript-utvecklare får en hållbar programmerings modell och använder fortfarande välbekanta språk konstruktioner och biblioteks primitiver.

Möjligheten att köra Java Script direkt i databas motorn ger prestanda och transaktionell körning av databas åtgärder mot objekt i en behållare. Eftersom Azure Cosmos Database Engine har inbyggt stöd för JSON och Java Script, finns det dessutom inget hinder mellan typ systemen i ett program och databasen.

## <a name="optimistic-concurrency-control"></a>Optimistisk samtidighetskontroll

Med optimistisk concurrency-kontroll kan du förhindra förlorade uppdateringar och borttagningar. Samtidiga är att motstridiga åtgärder har den vanliga pessimistiska låsningen av databas motorn som är värd för den logiska partition som äger objektet. När två samtidiga åtgärder försöker uppdatera den senaste versionen av ett objekt i en logisk partition, kommer en av dem att vinna och det andra Miss lyckas. Men om en eller två åtgärder som försöker uppdatera samma objekt tidigare hade läst ett äldre värde av objektet, vet inte databasen om det tidigare läsning svärdet av antingen eller båda de motstridiga åtgärderna faktiskt var det senaste värdet för objektet. I tur och miljö kan den här situationen identifieras med **optimistisk Samtidighets kontroll (OCC)** innan de två åtgärderna anges i databas motorn. OCC skyddar dina data från att oavsiktligt skriva över ändringar som har gjorts av andra. Det förhindrar också andra från att av misstag skriva över dina egna ändringar.

Samtidiga uppdateringar av ett objekt underkastas OCC av protokoll skiktet för Azure Cosmos DB. För Azure Cosmos-konton som kon figurer ATS för **skrivningar i en region** säkerställer Azure Cosmos DB att versions versionen av det objekt som du uppdaterar (eller tar bort) är samma som för objektet i Azure Cosmos-behållaren. Detta säkerställer att dina skrivningar är skyddade från att skrivas över av misstag av andras skrivningar och tvärtom. I en miljö med flera användare skyddar den optimistisk samtidighets kontrollen dig från att oavsiktligt ta bort eller uppdatera fel version av ett objekt. Därför skyddas objekt mot Infamous "förlorad uppdatering" eller "förlorad borttagning".

I ett Azure Cosmos-konto som kon figurer ATS med **skrivningar i flera regioner** kan data genomföras oberoende i sekundära regioner om dess `_etag` matchar data i den lokala regionen. När nya data har allokerats lokalt i en sekundär region slås de samman i hubben eller den primära regionen. Om principen för konflikt lösning sammanfogar nya data till Hub-regionen replikeras dessa data globalt med den nya `_etag` . Om principen för konflikt lösning avvisar nya data, kommer den sekundära regionen att återställas till ursprungliga data och `_etag` .

Alla objekt som lagras i en Azure Cosmos-behållare har en systemdefinierad `_etag` egenskap. Värdet för `_etag` genereras automatiskt och uppdateras av servern varje gång objektet uppdateras. `_etag` kan användas med klientens `if-match` begär ande huvud för att tillåta att servern bestämmer om ett objekt kan uppdateras villkorligt. Värdet för `if-match` rubriken matchar värdet på `_etag` på servern, objektet uppdateras sedan. Om värdet för `if-match` begär ande rubriken inte längre är aktuellt, avvisar servern åtgärden med svarsmeddelandet "HTTP 412-förhands fel". Klienten kan sedan återskapa objektet för att hämta den aktuella versionen av objektet på servern eller åsidosätta versionen av objektet på servern med ett eget `_etag` värde för objektet. `_etag`Kan dessutom användas med `if-none-match` rubriken för att avgöra om en återhämtning av en resurs behövs.

Objektets `_etag` värde ändras varje gång objektet uppdateras. För att ersätta objekt åtgärder `if-match` måste uttryckligen uttryckas som en del av begär ande alternativen. Ett exempel finns i exempel koden i [GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L676-L772). `_etag` värden kontrol leras implicit för alla skrivna objekt som vidrörs av den lagrade proceduren. Om en konflikt upptäcks återställer den lagrade proceduren transaktionen och genererar ett undantag. Med den här metoden tillämpas alla eller inga skrivningar inom den lagrade proceduren på ett atomict sätt. Detta är en signal till programmet att tillämpa uppdateringar på nytt och försöka utföra den ursprungliga klientbegäran igen.

## <a name="next-steps"></a>Nästa steg

Läs mer om databas transaktioner och optimistisk concurrency-kontroll i följande artiklar:

- [Arbeta med Azure Cosmos-databaser, behållare och objekt](account-databases-containers-items.md)
- [Konsekvensnivåer](consistency-levels.md)
- [Konflikttyper och matchningsprinciper](conflict-resolution-policies.md)
- [Använda TransactionalBatch](transactional-batch.md)
- [Lagrade procedurer, utlösare och användardefinierade funktioner](stored-procedures-triggers-udfs.md)
