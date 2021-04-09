---
title: Partitionering och horisontell skalning i Azure Cosmos DB
description: Lär dig mer om partitionering, logiska, fysiska partitioner i Azure Cosmos DB, metod tips när du väljer en partitionsnyckel och hur du hanterar logiska partitioner
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2021
ms.openlocfilehash: ab1b7028ce5f1afef861e696c98f25b56e78ef36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772475"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partitionering och horisontell skalning i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB använder partitionering för att skala enskilda behållare i en databas för att uppfylla ditt programs prestanda behov. I partitionering är objekten i en behållare indelade i distinkta del mängder som kallas *logiska partitioner*. Logiska partitioner skapas baserat på värdet för en *partitionsnyckel* som är associerad med varje objekt i en behållare. Alla objekt i en logisk partition har samma partitionerings nyckel värde.

Till exempel innehåller en behållare objekt. Varje objekt har ett unikt värde för `UserID` egenskapen. Om `UserID` fungerar som partitionsnyckel för objekten i behållaren och det finns 1 000 unika `UserID` värden, skapas 1 000 logiska partitioner för behållaren.

Förutom en partitionsnyckel som avgör objektets logiska partition, har varje objekt i en behållare ett *objekt-ID* (unikt inom en logisk partition). Genom att kombinera partitionsnyckel och *objekt-ID* skapas objektets *index*, vilket unikt identifierar objektet. [Att välja en partitionsnyckel](#choose-partitionkey) är ett viktigt beslut som påverkar programmets prestanda.

Den här artikeln förklarar förhållandet mellan logiska och fysiska partitioner. Den innehåller också metod tips för partitionering och ger en djupgående vy med hur horisontell skalning fungerar i Azure Cosmos DB. Du behöver inte känna till dessa interna uppgifter för att välja partitionsnyckel, men vi har täckt dem så att du har klarhet om hur Azure Cosmos DB fungerar.

## <a name="logical-partitions"></a>Logiska partitioner

En logisk partition består av en uppsättning objekt som har samma partitionsnyckel. I en behållare som innehåller data om livsmedels foder innehåller alla objekt till exempel en `foodGroup` egenskap. Du kan använda `foodGroup` som partitionsnyckel för behållaren. Grupper av objekt som har specifika värden för `foodGroup` , till exempel `Beef Products` ,, `Baked Products` och som `Sausages and Luncheon Meats` utgör distinkta logiska partitioner. Du behöver inte oroa dig för att ta bort en logisk partition när underliggande data tas bort.

En logisk partition definierar även omfattningen av databas transaktioner. Du kan uppdatera objekt i en logisk partition med hjälp av en [transaktion med ögonblicks bild isolering](database-transactions-optimistic-concurrency.md). När nya objekt läggs till i en behållare, skapas nya logiska partitioner transparent av systemet.

Det finns ingen gräns för antalet logiska partitioner i din behållare. Varje logisk partition kan lagra upp till 20 GB data. Lämpliga alternativ för partitionsalternativ har ett brett utbud av möjliga värden. I en behållare där alla objekt innehåller en `foodGroup` egenskap kan till exempel data i den `Beef Products` logiska partitionen växa upp till 20 GB. [Genom att välja en partitionsnyckel](#choose-partitionkey) med en mängd olika möjliga värden ser du till att behållaren kan skalas.

## <a name="physical-partitions"></a>Fysiska partitioner

En behållare skalas genom att distribuera data och data flöde över fysiska partitioner. Internt mappas en eller flera logiska partitioner till en enda fysisk partition. Normalt har mindre behållare flera logiska partitioner, men de behöver bara en enda fysisk partition. Till skillnad från logiska partitioner är fysiska partitioner en intern implementering av systemet och de hanteras helt av Azure Cosmos DB.

Antalet fysiska partitioner i din behållare är beroende av följande:

* Antalet allokerade data flöde (varje enskild fysisk partition kan tillhandahålla ett data flöde på upp till 10 000 enheter för programbegäran per sekund).
* Den totala data lagringen (varje enskild fysisk partition kan lagra upp till 50 GB data).

> [!NOTE]
> Fysiska partitioner är en intern implementering av systemet och de hanteras helt av Azure Cosmos DB. När du utvecklar dina lösningar bör du inte fokusera på fysiska partitioner eftersom du inte kan styra dem. Fokusera i stället på dina partitionsnyckel. Om du väljer en partitionsnyckel som jämnt distribuerar data flödes förbrukningen mellan logiska partitioner, ser du till att data flödes förbrukningen över fysiska partitioner är balanserade.

Det finns ingen gräns för det totala antalet fysiska partitioner i din behållare. När det etablerade data flödet eller data storleken ökar, skapar Azure Cosmos DB automatiskt nya fysiska partitioner genom att dela befintliga. Delning av fysiska partitioner påverkar inte programmets tillgänglighet. Efter delningen av den fysiska partitionen kommer alla data i en enda logisk partition fortfarande att lagras på samma fysiska partition. En fysisk partitions delning skapar helt enkelt en ny mappning av logiska partitioner till fysiska partitioner.

Det data flöde som har allokerats för en behållare är jämnt fördelat mellan fysiska partitioner. En partitionsnyckel som inte distribuerar förfrågningar jämnt kan resultera i att för många begär Anden dirigeras till en liten delmängd partitioner som blir "varma". Varma partitioner leder till ineffektiv användning av etablerade data flöden, vilket kan leda till avgiftsbelagda och högre kostnader.

Du kan se behållarens fysiska partitioner i **lagrings** avsnittet på **bladet mått** i Azure Portal:

:::image type="content" source="./media/partitioning-overview/view-partitions-zoomed-out.png" alt-text="Visa antal fysiska partitioner" lightbox="./media/partitioning-overview/view-partitions-zoomed-in.png" ::: 

I skärm bilden ovan har en behållare `/foodGroup` samma nyckel som partitionsnyckel. Var och en av de tre staplarna i diagrammet representerar en fysisk partition. I avbildningen är **partitionerings nyckel intervallet** detsamma som en fysisk partition. Den valda fysiska partitionen innehåller de tre viktigaste logiska partitionerna: `Beef Products` , `Vegetable and Vegetable Products` och `Soups, Sauces, and Gravies` .

Om du etablerar ett data flöde med 18 000-enheter för programbegäran per sekund (RU/s) kan var och en av de tre fysiska partitionerna använda 1/3 av det totala etablerade data flödet. I den valda fysiska partitionen, kan de logiska partitionernas nycklar `Beef Products` , `Vegetable and Vegetable Products` och `Soups, Sauces, and Gravies` kan gemensamt, använda den fysiska partitionens 6 000 etablerade ru/s. Eftersom det etablerade data flödet är jämnt delat över din behållares fysiska partitioner, är det viktigt att välja en partitionsnyckel som jämnt distribuerar data flödes förbrukningen genom [att välja rätt logisk partitionsnyckel](#choose-partitionkey). 

## <a name="managing-logical-partitions"></a>Hantera logiska partitioner

Azure Cosmos DB transparent och automatiskt hanterar placeringen av logiska partitioner på fysiska partitioner för att effektivt uppfylla behållarens skalbarhet och prestanda krav. När data flödes-och lagrings kraven för en applikation ökar, Azure Cosmos DB flyttar logiska partitioner för att automatiskt sprida belastningen över ett större antal fysiska partitioner. Du kan läsa mer om [fysiska partitioner](partitioning-overview.md#physical-partitions).

Azure Cosmos DB använder hash-baserad partitionering för att sprida logiska partitioner över fysiska partitioner. Azure Cosmos DB hash-värden för ett objekts partitionsnyckel. Det hashade resultatet avgör den fysiska partitionen. Sedan allokerar Azure Cosmos DB nyckel utrymmet för partitionens nyckel-hashar jämnt över de fysiska partitionerna.

Transaktioner (i lagrade procedurer eller utlösare) tillåts bara för objekt i en enda logisk partition.

## <a name="replica-sets"></a>Replikuppsättningar

Varje fysisk partition består av en uppsättning repliker, även kallade en [*replik uppsättning*](global-dist-under-the-hood.md). Varje replik uppsättning är värd för en instans av databas motorn. En replik uppsättning gör data lagrade i den fysiska partitionen tåligt, hög tillgängliga och konsekvent. Varje replik som utgör den fysiska partitionen ärver partitionens lagrings kvot. Alla repliker av en fysisk partition har gemensamt stöd för det data flöde som har allokerats till den fysiska partitionen. Azure Cosmos DB hanterar automatiskt replik uppsättningar.

Vanligt vis kräver mindre behållare bara en enda fysisk partition, men de kommer fortfarande att ha minst 4 repliker.

Följande bild visar hur logiska partitioner mappas till fysiska partitioner som distribueras globalt:

:::image type="content" source="./media/partitioning-overview/logical-partitions.png" alt-text="En bild som visar Azure Cosmos DB partitionering" border="false":::

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Välja en partitionsnyckel

En partitionsnyckel har två komponenter: **sökväg till partitionsnyckel** och nyckel **värde för partition**. Anta till exempel att du väljer ett objekt {"userId": "Anders", "worksFor": "Microsoft"} om du väljer "userId" som partitionsnyckel är följande de två partitionens nyckel komponenter:

* Sökvägen till partitionsnyckel (till exempel: "/userId"). Sökvägen till partitionsnyckel accepterar alfanumeriska tecken och under streck (_). Du kan också använda kapslade objekt med standard Sök vägs notation (/).

* Värdet för partitionsnyckel (till exempel: "Anders"). Värdet för partitionsnyckel kan vara sträng eller numeriska typer.

Mer information om gränserna för data flöde, lagring och längden på partitionsnyckel finns i artikeln [Azure Cosmos DB tjänst kvoter](concepts-limits.md) .

Att välja partitionsnyckel är ett enkelt men viktigt design val i Azure Cosmos DB. När du har valt partitionsnyckel är det inte möjligt att ändra den på plats. Om du behöver ändra din partitionsnyckel bör du flytta dina data till en ny behållare med din nya önskade partitionsnyckel.

För **alla** behållare bör din partitionsnyckel:

* Vara en egenskap som har ett värde som inte ändras. Om en egenskap är partitionsnyckel kan du inte uppdatera egenskapens värde.

* Ha en hög kardinalitet. Med andra ord bör egenskapen ha ett brett utbud av möjliga värden.

* Förbruknings enhet för begäran (RU) och data lagring jämnt över alla logiska partitioner. Detta säkerställer även RU-förbrukning och lagrings distribution över dina fysiska partitioner.

Om du behöver [transaktioner med flera objekt](database-transactions-optimistic-concurrency.md#multi-item-transactions) i Azure Cosmos DB måste du använda [lagrade procedurer eller utlösare](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures). Alla JavaScript-baserade lagrade procedurer och utlösare är begränsade till en enda logisk partition.

## <a name="partition-keys-for-read-heavy-containers"></a>Partitionsnyckel för Read-tung containrar

För de flesta behållare är ovanstående kriterier allt du behöver tänka på när du väljer en partitionsnyckel. För stora, omfattande behållare kanske du vill välja en partitionsnyckel som visas ofta som ett filter i dina frågor. Frågor kan [effektivt dirigeras till relevanta fysiska partitioner](how-to-query-container.md#in-partition-query) genom att inkludera partitionsnyckel i filtrets predikat.

Om de flesta av dina arbets belastnings begär Anden är frågor och de flesta av dina frågor har ett likhets filter för samma egenskap, kan den här egenskapen vara ett bra alternativ för partitionsnyckel. Om du till exempel ofta kör en fråga som filtrerar på `UserID` `UserID` så minskar antalet [frågor om flera partitioner](how-to-query-container.md#avoiding-cross-partition-queries)genom att välja som partitionsnyckel.

Men om din behållare är liten har du förmodligen inte tillräckligt med fysiska partitioner för att behöva oroa dig över prestanda påverkan för frågor över olika partitioner. De flesta små behållare i Azure Cosmos DB behöver bara en eller två fysiska partitioner.

Om din behållare kan växa till fler än ett fåtal fysiska partitioner, bör du se till att du väljer en partitionsnyckel som minimerar frågor om flera partitioner. Din behållare kräver mer än några få fysiska partitioner när något av följande stämmer:

* Din behållare kommer att ha över 30 000 RU-etableringen

* Din behållare kommer att lagra över 100 GB data

## <a name="using-item-id-as-the-partition-key"></a>Använda objekt-ID som partitionsnyckel

Om din behållare har en egenskap som har ett brett utbud av möjliga värden, är det troligt vis ett utmärkt partitionerings nyckel val. Ett möjligt exempel på en sådan egenskap är *objekt-ID*. För små Read-tung containrar eller Write-tungt behållare i valfri storlek är *objekt-ID: t* naturligt ett bra val för partitionsnyckel.

Systemets egenskaps *objekt-ID* finns i varje objekt i din behållare. Du kan ha andra egenskaper som representerar ett logiskt ID för ditt objekt. I många fall är dessa också fantastiska partitionsalternativ av olika anledningar som *objekt-ID*.

*Objekt-ID* är ett bra partitionerings val av följande orsaker:

* Det finns ett brett utbud av möjliga värden (ett unikt *objekt-ID* per objekt).
* Eftersom det finns ett unikt *objekt-ID* per objekt, gör *objekt-ID: t* ett bra jobb till jämn balansering av ru-förbrukning och data lagring.
* Du kan enkelt göra effektiva Poäng läsningar eftersom du alltid känner till ett objekts partitionsnyckel om du känner till dess *objekt-ID*.

Några saker att tänka på när du väljer *objekt-ID: t* som partitionsnyckel är:

* Om *objekt-ID: t* är partitionsnyckel, blir det en unik identifierare i hela behållaren. Du kan inte ha objekt som har en dubblett av *objekt-ID*.
* Om du har en Läs intensiv behållare som har många [fysiska partitioner](partitioning-overview.md#physical-partitions), blir frågorna mer effektiva om de har ett likhets filter med *objekt-ID*.
* Du kan inte köra lagrade procedurer eller utlösare över flera logiska partitioner.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [etablerade data flöden i Azure Cosmos DB](request-units.md).
* Lär dig mer om [global distribution i Azure Cosmos DB](distribute-data-globally.md).
* Lär dig hur du [etablerar data flöde i en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablerar data flöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).
