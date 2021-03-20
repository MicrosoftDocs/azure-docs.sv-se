---
title: Mappa konsekvens nivåer för Azure Cosmos DB-API för MongoDB
description: Mappa konsekvens nivåer för Azure Cosmos DB-API för MongoDB.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: e8ac3e376c8d67e82def3a57910707c6b1433912
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93333162"
---
# <a name="consistency-levels-for-azure-cosmos-db-and-the-api-for-mongodb"></a>Konsekvens nivåer för Azure Cosmos DB och API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Till skillnad från Azure Cosmos DB ger det inbyggda MongoDB inte exakt definierade konsekvens garantier. I stället tillåter interna MongoDB att användare konfigurerar följande konsekvens garantier: en Skriv angelägenhet, en läsning och isMaster-direktivet – för att dirigera Läs åtgärder till antingen primära eller sekundära repliker för att uppnå önskad konsekvens nivå.

När du använder Azure Cosmos DBs API för MongoDB, behandlar MongoDB-drivrutinen din Skriv region som den primära repliken och alla andra regioner är läsa replik. Du kan välja vilken region som är kopplad till ditt Azure Cosmos-konto som en primär replik.

> [!NOTE]
> Standard konsekvens modellen för Azure Cosmos DB är session. Session är en klientbaserad konsekvens modell som inte stöds internt av antingen Cassandra eller MongoDB. Mer information om vilken konsekvens modell som ska väljas finns [i avsnittet om konsekvens nivåer i Azure Cosmos DB](consistency-levels.md)

När du använder Azure Cosmos DBs API för MongoDB:

* Skriv skyddet mappas till standard konsekvens nivån som kon figurer ATS på ditt Azure Cosmos-konto.

* Azure Cosmos DB mappar dynamiskt det läsnings problem som anges av MongoDB-klient driv rutinen till en av de Azure Cosmos DB konsekvens nivåer som har kon figurer ATS dynamiskt på en Read-begäran.  

* Du kan kommentera en speciell region som är kopplad till ditt Azure Cosmos-konto som "primär" genom att göra regionen till den första skrivbara regionen. 

## <a name="mapping-consistency-levels"></a>Mappning av konsekvensnivåer

I följande tabell illustreras hur de inbyggda Skriv-och Läs problemen i MongoDB mappas till konsekvens nivåerna i Azure Cosmos när du använder Azure Cosmos DB s API för MongoDB:

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="MongoDB konsekvens modell mappning" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

Om ditt Azure Cosmos-konto har kon figurer ATS med en annan konsekvens nivå än den starka konsekvensen kan du ta reda på sannolikheten att dina klienter kan få starka och konsekventa läsningar för dina arbets belastningar genom att titta på Probabilistically-måttet ( *Bound* ). Det här måttet visas i Azure Portal. mer information finns i [övervaka Probabilistically-gränser för Inaktuellitet (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistic-begränsad föråldrad visar hur den slutliga konsekvensen är. Det här måttet ger en inblick i hur ofta du kan få en bättre konsekvens än den konsekvens nivå som du för närvarande har konfigurerat på ditt Azure Cosmos-konto. Med andra ord kan du se sannolikheten (mätt i millisekunder) för att få starkt konsekventa läsningar för en kombination av Skriv-och Läs regioner.

## <a name="next-steps"></a>Nästa steg

Läs mer om global distribution och konsekvens nivåer för Azure Cosmos DB:

* [Översikt av global distribution](distribute-data-globally.md)
* [Översikt över konsekvens nivå](consistency-levels.md)
* [Hög tillgänglighet](high-availability.md)
