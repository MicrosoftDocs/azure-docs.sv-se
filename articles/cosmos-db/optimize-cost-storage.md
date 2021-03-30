---
title: Optimera kostnader för lagring i Azure Cosmos DB
description: Den här artikeln förklarar hur du hanterar lagrings kostnader för data som lagras i Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 92bd3ff925080def4b2f074d07e662dfdbdbee01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93080858"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Optimera kostnader för lagring i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB erbjuder obegränsad lagring och data flöde. Till skillnad från genom strömning, som du måste etablera/konfigurera på dina Azure Cosmos-behållare eller databaser, faktureras lagrings utrymmet utifrån en konsumtions bas. Du faktureras bara för den logiska lagring som du använder och du behöver inte reservera någon lagring i förväg. Lagring skalas automatiskt upp och ned baserat på de data som du lägger till eller tar bort i en Azure Cosmos-behållare.

## <a name="storage-cost"></a>Lagringskostnad

Lagringen debiteras med enheten för GB. Lokalt SSD-baserat lagrings utrymme används av dina data och indexering. Det totala lagrings utrymmet som används motsvarar det lagrings utrymme som krävs av data och index som används i alla regioner där du använder Azure Cosmos DB. Om du globalt replikerar ett Azure Cosmos-konto över tre regioner betalar du för den totala lagrings kostnaden för var och en av de tre regionerna. Information om hur du beräknar lagrings kraven finns i verktyget [Capacity Planner](https://www.documentdb.com/capacityplanner) . Kostnaden för lagring i Azure Cosmos DB är $0,25 GB/månad, se [prissättnings sida](https://azure.microsoft.com/pricing/details/cosmos-db/) för de senaste uppdateringarna. Du kan ställa in aviseringar för att fastställa vilka lagrings enheter som används av din Azure Cosmos-behållare för att övervaka lagringen, se [övervaka Azure Cosmos DB](./monitor-cosmos-db.md)) artikeln.

## <a name="optimize-cost-with-item-size"></a>Optimera kostnaden med objekt storlek

Azure Cosmos DB förväntar sig att objekt storleken ska vara 2 MB eller mindre för optimala prestanda och kostnads besparingar. Om du behöver ett objekt för att lagra större än 2 MB data bör du överväga att utforma om objekt schema. I den sällsynta händelse att du inte kan skapa en ny design för schemat kan du dela upp objektet i under objekt och länka dem logiskt med en gemensam identifierare (ID). Alla Azure Cosmos DB funktionerna fungerar konsekvent genom att fästa mot den logiska identifieraren.

## <a name="optimize-cost-with-indexing"></a>Optimera kostnader med indexering

Som standard indexeras data automatiskt, vilket kan öka den totala förbrukade lagringen. Du kan dock använda anpassade index principer för att minska den här omkostnaderna. Automatisk indexering som inte har justerats genom principen är cirka 10-20% av objektets storlek. Genom att ta bort eller anpassa index principer betalar du inte extra kostnader för skrivningar och behöver inte ytterligare data flödes kapacitet. Konfigurera anpassade indexerings principer genom att se [indexering i Azure Cosmos DB](index-policy.md) . Om du har arbetat med relations databaser tidigare kan du tänka på att "indexera allt" innebär dubblerad lagring eller högre. I Azure Cosmos DB i median fallet är det dock mycket lägre. I Azure Cosmos DB är lagrings belastningen för indexet normalt låg (10-20%) även med automatisk indexering, eftersom det är utformat för att få en låg lagrings plats. Genom att hantera indexerings principen kan du kontrol lera kompromissen med index och fråga prestanda på ett mer kornigt sätt.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Optimera kostnaderna med tiden till Live och ändra feed

När du inte längre behöver data kan du ta bort den från ditt Azure Cosmos-konto genom att använda [Time to Live](time-to-live.md), [ändra feed](change-feed.md) eller migrera gamla data till ett annat data lager, till exempel Azure Blob Storage eller Azure Data Warehouse. Med Time to Live eller TTL ger Azure Cosmos DB möjlighet att ta bort objekt automatiskt från en behållare efter en viss tids period. Som standard kan du ange Time to Live på behållar nivån och åsidosätta värdet per objekt-basis. När du har ställt in TTL på en behållare eller på en objekt nivå, tar Azure Cosmos DB automatiskt bort objekten efter tids perioden sedan den senaste ändringen. Genom att använda ändra feed kan du migrera data till antingen en annan behållare i Azure Cosmos DB eller till ett externt data lager. Migreringen tar ingen tid och när du är färdig med migreringen kan du antingen ta bort eller konfigurera tiden för att ta bort källan för Azure Cosmos-behållaren.

## <a name="optimize-cost-with-rich-media-data-types"></a>Optimera kostnader med omfattande medie data typer 

Om du vill lagra omfattande medie typer, till exempel videor, bilder osv., har du ett antal alternativ i Azure Cosmos DB. Ett alternativ är att lagra dessa omfattande medie typer som Azure Cosmos-objekt. Det finns en gräns på 2 MB per objekt, och du kan undvika den här gränsen genom att länka data objektet till flera under objekt. Eller så kan du lagra dem i Azure Blob Storage och använda metadata för att referera till dem från dina Azure Cosmos-objekt. Det finns ett antal tekniker och nack delar med den här metoden. Med den första metoden får du bästa möjliga prestanda vad gäller svars tid, data flödes service avtal plus nyckel färdiga globala distributions möjligheter för medie data typerna förutom dina vanliga Azure Cosmos-objekt. Supporten är dock tillgänglig till ett högre pris. Genom att lagra media i Azure Blob Storage kan du sänka dina totala kostnader. Om svars tiden är kritisk kan du använda Premium Storage för Rich Media-filer som refereras från Azure Cosmos-objekt. Detta integrerar internt med CDN för att betjäna avbildningar från Edge-servern till lägre kostnad för att kringgå geo-restriktioner. Den här situationen är att du måste hantera två tjänster – Azure Cosmos DB och Azure Blob Storage, vilket kan öka drifts kostnaderna. 

## <a name="check-storage-consumed"></a>Kontrol lera förbrukad lagring

Om du vill kontrol lera lagrings användningen för en Azure Cosmos-behållare kan du köra en HEAD-eller GET-begäran på behållaren och kontrol lera `x-ms-request-quota` `x-ms-request-usage` rubrikerna och. När du arbetar med .NET SDK kan du också använda egenskaperna [DocumentSizeQuota](/previous-versions/azure/dn850325(v%3Dazure.100))och [DocumentSizeUsage](/previous-versions/azure/dn850324(v=azure.100)) för att hämta förbrukad lagring.

## <a name="using-sdk"></a>Använda SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Nästa steg

Härnäst kan du fortsätta med att lära dig mer om kostnads optimering i Azure Cosmos DB med följande artiklar:

* Läs mer om [optimering för utveckling och testning](optimize-dev-test.md)
* Lär dig mer om [att förstå din Azure Cosmos DB faktura](understand-your-bill.md)
* Läs mer om hur du [optimerar data flödes kostnaden](optimize-cost-throughput.md)
* Läs mer om hur [du optimerar kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Lär dig mer om hur [du optimerar kostnaden för frågor](./optimize-cost-reads-writes.md)
* Läs mer om hur [du optimerar kostnaden för Azure Cosmos-konton med flera regioner](optimize-cost-regions.md)
