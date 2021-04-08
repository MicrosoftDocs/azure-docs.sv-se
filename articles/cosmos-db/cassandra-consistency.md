---
title: Apache Cassandra och Azure Cosmos DB konsekvens nivåer
description: Apache Cassandra och Azure Cosmos DB konsekvens nivåer.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 3107610215d5b37c43124ce4129b2eb5437e3b62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516829"
---
# <a name="apache-cassandra-and-azure-cosmos-db-cassandra-api-consistency-levels"></a>Apache Cassandra och Azure Cosmos DB API för Cassandra konsekvens nivåer
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Till skillnad från Azure Cosmos DB ger Apache Cassandra inte inbyggt exakt definierade konsekvens garantier. I stället ger Apache Cassandra en Skriv konsekvens nivå och en Läs konsekvens nivå för att möjliggöra hög tillgänglighet, konsekvens och latens fördröjningar. När du använder Azure Cosmos DB API för Cassandra:

* Den Skriv konsekvens nivån för Apache Cassandra mappas till standard konsekvens nivån som kon figurer ATS på ditt Azure Cosmos-konto. Konsekvens för en Skriv åtgärd (CL) kan inte ändras per begäran.

* Azure Cosmos DB mappar dynamiskt den Läs konsekvens nivå som anges av Cassandra-klient driv rutinen till en av Azure Cosmos DB konsekvens nivåer som kon figurer ATS dynamiskt på en läsbegäran.

## <a name="multi-region-writes-vs-single-region-writes"></a>Skrivningar i flera regioner jämfört med en region

Apache Cassandra Database är ett system med flera huvud servrar som standard, och innehåller inget alternativ för en enda regions skrivning med replikering med flera regioner för läsningar. Azure Cosmos DB ger dock nyckel färdiga möjligheter att antingen ha en enda region eller skriva konfigurationer i [flera regioner](how-to-multi-master.md) . En av fördelarna med att kunna välja en enda regions skrivnings konfiguration i flera regioner är att undvika konflikter mellan olika regioner och möjligheten att upprätthålla stark konsekvens i flera regioner. 

Med en enda regions skrivning kan du upprätthålla stark konsekvens och samtidigt upprätthålla en hög tillgänglighet i flera regioner med [Automatisk redundans](high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage). I den här konfigurationen kan du fortfarande utnyttja data för att minska Läs fördröjningen genom att nedgradera till eventuell konsekvens på per begäran. Förutom dessa funktioner ger Azure Cosmos DB-plattformen även möjligheten att aktivera [zon redundans](high-availability.md#availability-zone-support) när du väljer en region. Till skillnad från inbyggda Apache-Cassandra gör Azure Cosmos DB att du kan navigera i CAP satsen [handels spektrum](consistency-levels.md#rto) med mer detaljerad information.

## <a name="mapping-consistency-levels"></a>Mappning av konsekvensnivåer

Azure Cosmos DBs plattformen tillhandahåller en uppsättning med fem väldefinierade konsekvens inställningar för affärsanvändning med avseende på replikering och de kompromisser som definieras av Cap- [satsen](https://en.wikipedia.org/wiki/CAP_theorem) och [PACLC satsen](https://en.wikipedia.org/wiki/PACELC_theorem). Eftersom den här metoden skiljer sig avsevärt från Apache Cassandra rekommenderar vi att du tar tid att granska och förstå Azure Cosmos DB konsekvens inställningar i vår [dokumentation](consistency-levels.md)eller titta på den här korta [video](https://www.youtube.com/watch?v=t1--kZjrG-o) guiden för att förstå konsekvens inställningarna i Azure Cosmos DBS plattformen.

I följande tabell visas möjliga mappningar mellan Apache Cassandra och Azure Cosmos DB konsekvens nivåer när du använder API för Cassandra. Detta visar konfigurationer för en enda region, läsningar i flera regioner med skrivningar i en region och flera regioner.

> [!NOTE]
> Dessa är inte exakta mappningar. Vi har i stället tillhandahållit de närmaste jämförelserna till Apache Cassandra och disambiguated eventuella kvalitativa skillnader i kolumnen längst till höger. Som vi nämnt ovan rekommenderar vi att du visar Azure Cosmos DB [konsekvens inställningar](consistency-levels.md). 

:::image type="content" source="./media/cassandra-consistency/account.png" alt-text="Mappning av Cassandra-konsekvens på konto nivå" lightbox="./media/cassandra-consistency/account.png" :::

:::image type="content" source="./media/cassandra-consistency/dynamic.png" alt-text="Dynamisk mappning av Cassandra-konsekvens" lightbox="./media/cassandra-consistency/dynamic.png" :::

Om ditt Azure Cosmos-konto har kon figurer ATS med en annan konsekvens nivå än den starka konsekvensen kan du ta reda på sannolikheten att dina klienter kan få starka och konsekventa läsningar för dina arbets belastningar genom att titta på Probabilistically-måttet ( *Bound* ). Det här måttet visas i Azure Portal. mer information finns i [övervaka Probabilistically-gränser för Inaktuellitet (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistic-begränsad föråldrad visar hur den slutliga konsekvensen är. Det här måttet ger en inblick i hur ofta du kan få en bättre konsekvens än den konsekvens nivå som du för närvarande har konfigurerat på ditt Azure Cosmos-konto. Med andra ord kan du se sannolikheten (mätt i millisekunder) för att få starkt konsekventa läsningar för en kombination av Skriv-och Läs regioner.

## <a name="next-steps"></a>Nästa steg

Läs mer om global distribution och konsekvens nivåer för Azure Cosmos DB:

* [Översikt av global distribution](distribute-data-globally.md)
* [Översikt över konsekvens nivå](consistency-levels.md)
* [Hög tillgänglighet](high-availability.md)
