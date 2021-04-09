---
title: Begär enhets avgifter för Azure Cosmos DB som nyckel värdes lager
description: Läs om avgifterna för förfrågans enhet för Azure Cosmos DB för enkla Skriv-och Läs åtgärder när de används som nyckel/värde-lager.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 9354ae0a22ef2e8ab4ee6a57563d3f3c4c8e4547
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339316"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB som ett nyckel värdes lager – kostnads översikt
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB är en globalt distribuerad databas tjänst med flera modeller för att skapa hög tillgängliga, storskaliga program i stor skala. Som standard indexeras Azure Cosmos DB automatiskt och effektivt indexera alla data som matas in. Detta möjliggör snabba och konsekventa [SQL](./sql-query-getting-started.md) -frågor (och [Java Script](stored-procedures-triggers-udfs.md)) för data. 

I den här artikeln beskrivs kostnaden för Azure Cosmos DB för enkla Skriv-och Läs åtgärder när den används som nyckel/värde-lagring. Skriv åtgärder inkluderar infogningar, ersätter, tar bort och upsertar av data objekt. Förutom att garantera ett service avtal på 99,999% tillgänglighet för alla konton med flera regioner, Azure Cosmos DB ger garanterad <10 MS-svars tid för läsningar och för (indexerade) skrivningar, vid 99 percentil. 

## <a name="why-we-use-request-units-rus"></a>Varför vi använder Request Units (ru: er)

Azure Cosmos DB prestanda baseras på mängden allokerat data flöde som uttrycks i [enheter för programbegäran](request-units.md) (ru/s). Etableringen är i en andra kornig het och köps in i RU/s (ska[inte förväxlas med fakturerings tiden per timme](https://azure.microsoft.com/pricing/details/cosmos-db/)). Ru: er bör betraktas som en logisk abstraktion (en valuta) som fören klar etableringen av nödvändigt data flöde för programmet. Användarna behöver inte tänka på att skilja mellan Läs-och Skriv data flöden. Den enkla valuta modellen i ru: er skapar effektiviteten för att dela den etablerade kapaciteten mellan läsningar och skrivningar. Den här etablerade kapacitets modellen gör det möjligt för tjänsten att tillhandahålla ett **förutsägbart och konsekvent data flöde, garanterad låg latens och hög tillgänglighet**. Slutligen, medan RU-modellen används för att illustrera data flödet, har varje etablerad RU också en definierad mängd resurser (t. ex. minne, kärnor/processor och IOPS).

Som ett globalt distribuerat databas system är Cosmos DB den enda Azure-tjänst som tillhandahåller en omfattande service avtal som täcker svars tid, data flöde, konsekvens och hög tillgänglighet. Det data flöde som du etablerar tillämpas på var och en av de regioner som är kopplade till ditt Cosmos-konto. För läsningar erbjuder Cosmos DB flera, väldefinierade [konsekvens nivåer](consistency-levels.md) som du kan välja bland. 

I följande tabell visas antalet ru: er som krävs för att utföra Läs-och skriv åtgärder baserat på ett data objekt av storlek 1 KB och 100 kB med automatisk indexering inaktive rad. 

|Objekt storlek|1 läsning|1 skrivning|
|-------------|------|-------|
|1 kB|1 RU|5 ru: er|
|100 kB|10 RU:er|50 ru: er|

## <a name="cost-of-reads-and-writes"></a>Kostnader för läsningar och skrivningar

Om du etablerar 1 000 RU/s uppgår detta belopp till 3 600 000 RU/timme och kommer att kosta $0,08 för timmen (i USA och Europa). För ett data objekt på 1 KB innebär det att du kan använda 3 600 000 läspaket eller 720 000 skrivningar (3 600 000 RU/5) med hjälp av det etablerade data flödet. Normaliserat till miljon läsningar och skrivningar skulle kostnaden bli $0,022/miljon av läsningar ($0,08/3,6) och $0.111/miljoner skrivningar ($0,08/0,72). Kostnaden per miljon blir minimal som visas i tabellen nedan.

|Objekt storlek|Kostnad för 1 000 000 läsningar|Kostnad på 1 000 000 skrivningar|
|-------------|-------|--------|
|1 kB|$0,022|$0,111|
|100 kB|$0,222|$1,111|


De flesta av de grundläggande BLOB-objekten eller objekt lagrings tjänsterna debiterar $0,40 per miljon Läs transaktion och $5 per miljon Skriv transaktion. Om det används optimalt kan Cosmos DB vara upp till 98% billigare än dessa andra lösningar (för 1 KB-transaktioner).

## <a name="next-steps"></a>Nästa steg

* Använd [ru-kalkylatorn](https://cosmos.azure.com/capacitycalculator/) för att beräkna data flödet för dina arbets belastningar.