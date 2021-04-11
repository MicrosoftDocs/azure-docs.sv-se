---
title: Köra frågor mot containrar i Azure Cosmos DB
description: Lär dig hur du söker efter behållare i Azure Cosmos DB att använda frågor i partitionen och över partitioner
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 5cd90e994e620960e0d974ef7609a67f8a5eb58b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448567"
---
# <a name="query-an-azure-cosmos-container"></a>Köra frågor mot en Azure Cosmos-container
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln beskriver hur du kör frågor mot en container (samling, graf eller tabell) i Azure Cosmos DB. I synnerhet täcker det hur frågor på partition och kors partition fungerar i Azure Cosmos DB.

## <a name="in-partition-query"></a>Frågekörning inom en partition

När du frågar efter data från behållare, om frågan har ett filter för partitionsnyckel, optimerar Azure Cosmos DB automatiskt frågan. Den dirigerar frågan till de [fysiska partitionerna](partitioning-overview.md#physical-partitions) som motsvarar de partitionsnyckel som anges i filtret.

Överväg till exempel frågan nedan med ett likhets filter på `DeviceId` . Om vi kör den här frågan på en behållare som partitionerats på `DeviceId` , kommer den här frågan att filtrera till en enda fysisk partition.

```sql
SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

Som i det tidigare exemplet kommer den här frågan också att filtrera till en enda partition. Att lägga till ytterligare filter i `Location` ändrar inte detta:

```sql
SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Här är en fråga som har ett intervall filter i partitionsnyckel och som inte kommer att begränsas till en enda fysisk partition. För att kunna vara en fråga i partitionen måste frågan ha ett likhets filter som innehåller partitionsnyckel:

```sql
SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Frågekörning mellan partitioner

Följande fråga saknar filter för partitionsnyckel ( `DeviceId` ). Därför måste den fläkta ut till alla fysiska partitioner där den körs mot varje partitions index:

```sql
SELECT * FROM c WHERE c.Location = 'Seattle`
```

Varje fysisk partition har sitt eget index. När du kör en fråga för flera partitioner på en behållare kör du därför en fråga *per* fysisk partition på ett effektivt sätt. Azure Cosmos DB sammanställer automatiskt resultaten mellan olika fysiska partitioner.

Indexen i olika fysiska partitioner är oberoende av varandra. Det finns inget globalt index i Azure Cosmos DB.

## <a name="parallel-cross-partition-query"></a>Parallell frågekörning mellan partitioner

Azure Cosmos DB SDK:erna 1.9.0 och senare stöder alternativ för parallell frågekörning. Parallell frågekörning mellan partitioner gör att du kan genomföra frågor med låg latens mellan partitioner.

Du kan hantera parallell frågekörning genom att justera följande parametrar:

- **MaxConcurrency**: anger det högsta antalet samtidiga nätverks anslutningar till behållarens partitioner. Om du ställer in den här egenskapen på `-1` , hanterar SDK graden av parallellitet. Om det  `MaxConcurrency` är inställt på `0` , finns det en enda nätverks anslutning till behållarens partitioner.

- **MaxBufferedItemCount**: Avväger frågesvarstid kontra minnesanvändning på klientsidan. Om det här alternativet utelämnas eller anges till -1 hanterar SDK:n antalet objekt som buffras under en parallell frågekörning.

På grund av Azure Cosmos DBs möjlighet att parallellisera frågor över olika partitioner, kommer fråge svars tiden ofta att skalas och systemet lägger till [fysiska partitioner](partitioning-overview.md#physical-partitions). Avgifterna för RU kommer dock att öka markant eftersom det totala antalet fysiska partitioner ökar.

När du kör en fråga för flera partitioner utförs en separat fråga per enskild fysisk partition. Även om frågor med frågor om kors partition kommer att använda index, om det är tillgängligt, är de fortfarande inte nästan lika effektiva som frågor i partitionen.

## <a name="useful-example"></a>Användbart exempel

Här är en analog för att bättre förstå frågor över partitioner:

Anta att du är en leverans driv rutin som måste leverera paket till olika lägenhets komplex. Varje Apartment-komplex har en lista på de anläggningar som innehåller alla inhemska enhets nummer. Vi kan jämföra varje lägenhet som är komplex för en fysisk partition och varje lista till den fysiska partitionens index.

Vi kan jämföra frågor på partition och kors partition med hjälp av det här exemplet:

### <a name="in-partition-query"></a>Frågekörning inom en partition

Om leverans driv rutinen känner till rätt Apartment-komplex (fysisk partition) kan de omedelbart driva till rätt byggnad. Driv rutinen kan kontrol lera den Apartment-komplexa listan över de inhemska enhets numren (indexet) och snabbt leverera lämpliga paket. I det här fallet avvisar driv rutinen ingen tid eller ansträngnings drivande till en lägenhet komplex för att kontrol lera och se om några paket mottagare bor där.

### <a name="cross-partition-query-fan-out"></a>Fråga över partitioner (fläkt ut)

Om leverans driv rutinen inte känner till rätt lägenhets komplexa (fysiska partition) måste de drivas till varje enskild lägenhets byggnad och kontrol lera listan med alla inhemska enhets nummer (index). När de anländer till varje lägenhet är det fortfarande möjligt att använda listan med adresserna för varje Resident. De måste dock kontrol lera varje Apartment komplex lista, oavsett om paket mottagarna bor där eller inte. Så här fungerar frågor mellan partitioner. Även om de kan använda indexet (behöver inte blockeras på varje enskild dörr) måste de separat kontrol lera indexet för varje fysisk partition.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Fråga över partitioner (begränsat till bara några fysiska partitioner)

Om leverans driv rutinen är medveten om att alla paket mottagare bor inom en viss Apartment-komplex, behöver de inte göra något till varje enskilt. Även om det krävs mer arbete för att kunna köra ett fåtal Apartment-objekt än att bara besöka en enda byggnad, sparar leverans driv rutinen fortfarande betydande tid och ansträngning. Om en fråga har partitionsnyckel i filtret med `IN` nyckelordet kontrollerar den bara den relevanta fysiska partitionens index för data.

## <a name="avoiding-cross-partition-queries"></a>Undvika frågor över partitioner

För de flesta behållare är det ofrånkomlig att du kommer att ha vissa frågor över flera partitioner. Vissa frågor över flera partitioner är OK! Nästan alla fråge åtgärder stöds i flera partitioner (både logiska partitionsnyckel och fysiska partitioner). Azure Cosmos DB också många optimeringar i parallellisera och klient-SDK: er för att köra frågekörning över fysiska partitioner.

För de flesta Läs-tung-scenarier rekommenderar vi att du bara väljer den vanligaste egenskapen i dina Frågefilter. Du bör också se till att din partitionsnyckel följer de [bästa metoderna för val av partitionsnyckel](partitioning-overview.md#choose-partitionkey).

Att undvika frågor över flera partitioner är vanligt vis bara frågor med stora behållare. Du debiteras minst om 2,5 RU-värden varje gången du kontrollerar en fysisk partitions index för resultat, även om inga objekt i den fysiska partitionen matchar frågans filter. Om du bara har ett (eller bara några) fysiska partitioner kommer frågor över flera partitioner inte att förbruka betydligt fler RU-frågor än i partitionen.

Antalet fysiska partitioner är kopplat till den allokerade RU: s. Varje fysisk partition tillåter upp till 10 000 etablerade RUs och kan lagra upp till 50 GB data. Azure Cosmos DB hanterar automatiskt fysiska partitioner åt dig. Antalet fysiska partitioner i din behållare är beroende av ditt etablerade data flöde och förbrukade lagrings utrymme.

Du bör försöka undvika frågor över partitioner om din arbets belastning uppfyller följande kriterier:
- Du planerar att ha över 30 000 RU-etableringen
- Du planerar att lagra över 100 GB data

## <a name="next-steps"></a>Nästa steg

I följande artiklar lär du dig mer om partitionering i Azure Cosmos DB:

- [Partitionering i Azure Cosmos DB](partitioning-overview.md)
- [Syntetiska partitionsnycklar i Azure Cosmos DB](synthetic-partition-keys.md)
