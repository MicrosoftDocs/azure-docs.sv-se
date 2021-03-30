---
title: Tillbakadragna Azure Cosmos DB prestanda nivåer
description: Lär dig mer om prestanda nivåerna S1, S2 och S3 som tidigare var tillgängliga i Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 4cb07d9d19d85cd8dff9a52eeeb7e173b60f4d6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93080778"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Dra tillbaka prestandanivåerna S1, S2 och S3
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!IMPORTANT] 
> Prestanda nivåerna S1, S2 och S3 som diskuteras i den här artikeln dras tillbaka och är inte längre tillgängliga för nya Azure Cosmos DB-konton.

Den här artikeln innehåller en översikt över prestanda nivåerna S1, S2 och S3, och beskriver hur samlingarna som använder dessa prestanda nivåer kan migreras till en enda partitionerad samling. När du har läst den här artikeln kan du svara på följande frågor:

- [Varför dras prestanda nivåerna S1, S2 och S3 bort?](#why-retired)
- [Hur jämförs samlingar med enskilda partitioner och partitionerade samlingar med prestanda nivåerna S1, S2, S3?](#compare)
- [Vad behöver jag för att säkerställa oavbruten åtkomst till mina data?](#uninterrupted-access)
- [Hur ändras min samling efter migreringen?](#collection-change)
- [Hur förändras faktureringen efter att jag har migrerat till en enda partitions samlingar?](#billing-change)
- [Vad händer om jag behöver mer än 20 GB lagrings utrymme?](#more-storage-needed)
- [Kan jag ändra mellan prestanda nivåerna S1, S2 och S3 före den planerade migreringen?](#change-before)
- [Hur gör jag för att migrera från S1-, S2-och S3-prestanda nivåerna till en enda partition på egen hand?](#migrate-diy)
- [Hur påverkas det om jag är en EA-kund?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Varför dras prestanda nivåerna S1, S2 och S3 bort?

Prestanda nivåerna S1, S2 och S3 erbjuder inte den flexibilitet som finns i standard Azure Cosmos DB erbjudandet. Med prestanda nivåerna S1, S2, S3 är både data flödet och lagrings kapaciteten fördefinierade och erbjöds inte elastiskt. Azure Cosmos DB har nu möjlighet att anpassa ditt data flöde och din lagring, vilket ger dig större flexibilitet i din möjlighet att skala efter behov.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Hur jämförs samlingar med enskilda partitioner och partitionerade samlingar med prestanda nivåerna S1, S2, S3?

I följande tabell jämförs de data flödes-och lagrings alternativ som finns tillgängliga i samlingar med en enda partition, partitionerade samlingar och S1, S2, S3-prestanda nivåer. Här är ett exempel på regionen USA, östra 2:

| Kvot namn  |Partitionerad samling|Samling med en partition|S1|S2|S3|
|---|---|---|---|---|---|
|Maximalt dataflöde|Obegränsat|10 000 RU/s|250 RU/s|1 K RU/s|2,5 n RU/s|
|Minsta data flöde|2,5 n RU/s|400 RU/s|250 RU/s|1 K RU/s|2,5 n RU/s|
|Maximalt lagrings utrymme|Obegränsat|20 GB|20 GB|20 GB|20 GB|
|Pris (månatligt)|Data flöde: $6/100 RU/s<br><br>Lagring: $0,25/GB|Data flöde: $6/100 RU/s<br><br>Lagring: $0,25/GB|$25 USD|$50 USD|$100 USD|

Är du EA-kund? I så fall, se [Hur påverkas jag av en EA-kund?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Vad behöver jag för att säkerställa oavbruten åtkomst till mina data?

Om du har en S1-, S2-eller S3-samling bör du migrera samlingen till en enda partitionsuppsättning program mässigt [med hjälp av .NET SDK](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Hur ändras min samling efter migreringen?

Om du har en S1-samling kan du migrera dem till en enda partitions samling med 400 RU/s-dataflöde. 400 RU/s är det lägsta data flödet som är tillgängligt med en samling med enskilda partitioner. Kostnaden för 400 RU/s i en enda partitions samling är dock ungefär samma som du betalade med din S1-samling och 250 RU/s – så att du inte betalar för de extra 150 RU/s som är tillgängliga för dig.

Om du har en S2-samling kan du migrera dem till en enda partitions samling med 1 000 RU/s. Du får ingen ändring av data flödes nivån.

Om du har en S3-samling kan du migrera dem till en enda partitions samling med 2,5 000 RU/s. Du får ingen ändring av data flödes nivån.

I vart och ett av dessa fall kommer du att kunna anpassa din data flödes nivå efter att du har migrerat samlingen, eller skala upp och ned efter behov för att ge låg latens åtkomst till dina användare. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Hur förändras faktureringen efter att jag har migrerat till samlingarna med en enda partition?

Förutsatt att du har 10 S1-samlingar, 1 GB lagrings utrymme för var och en, i regionen USA, östra, och du migrerar de här 10 S1-samlingarna till 10 enskilda partitionsuppsättningar på 400 RU/SEK (minimi nivån). Din faktura kommer att se ut så här om du behåller de 10 enskilda partitionsuppsättningar i en fullständig månad:

:::image type="content" source="./media/performance-levels/s1-vs-standard-pricing.png" alt-text="Hur S1-priser för 10 samlingar jämförs med 10 samlingar med priser för en enda partition samling" border="false":::

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-20-gb-of-storage"></a>Vad händer om jag behöver mer än 20 GB lagrings utrymme?

Oavsett om du har en samling med prestanda nivån S1, S2 eller S3, eller om du har en enda partitionsuppsättning, som har 20 GB lagrings utrymme kan du använda verktyget för datamigrering för Azure Cosmos DB för att migrera dina data till en partitionerad samling med praktiskt taget obegränsad lagring. Information om fördelarna med en partitionerad samling finns [i partitionering och skalning i Azure Cosmos DB](partitioning-overview.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Kan jag ändra mellan prestanda nivåerna S1, S2 och S3 före den planerade migreringen?

Endast befintliga konton med prestanda för S1, S2 och S3 kan ändras och ändra prestanda nivå nivå program mässigt [med hjälp av .NET SDK](#migrate-diy). Om du ändrar från S1, S3 eller S3 till en enda partitions samling kan du inte återgå till prestanda nivåerna S1, S2 eller S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Hur gör jag för att migrera från S1-, S2-och S3-prestanda nivåerna till en enda partition på egen hand?

Du kan migrera från prestanda nivåerna S1, S2 och S3 till en enda partition [genom programmering med hjälp av .NET SDK](#migrate-diy). Du kan göra detta på egen hand före den planerade migreringen för att dra nytta av de flexibla data flödes alternativ som är tillgängliga med en enda partition samling.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migrera till samlingar med en enda partition med hjälp av .NET SDK

Det här avsnittet handlar bara om att ändra en samlings prestanda nivå med [SQL .NET API](sql-api-sdk-dotnet.md), men processen liknar våra övriga SDK: er.

Här är ett kodfragment för att ändra samlings data flödet till 5 000 enheter för programbegäran per sekund:
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Besök [MSDN](/dotnet/api/microsoft.azure.documents.client.documentclient) och Visa fler exempel och lär dig mer om våra erbjudande metoder:

* [**ReadOfferAsync**](/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync)
* [**ReadOffersFeedAsync**](/dotnet/api/microsoft.azure.documents.client.documentclient.readoffersfeedasync)
* [**ReplaceOfferAsync**](/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync)
* [**CreateOfferQuery**](/previous-versions/azure/dn975114(v=azure.100))

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Hur påverkas det om jag är en EA-kund?

EA-kunder blir ett pris som skyddas tills det aktuella kontraktet upphör att gälla.

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om prissättning och hantering av data med Azure Cosmos DB kan du utforska följande resurser:

1.  [Partitionerar data i Cosmos DB](partitioning-overview.md). Förstå skillnaden mellan behållare med en enda partition och partitionerade behållare, samt tips om hur du implementerar en partitionerings strategi för skalning sömlöst.
2.  [Cosmos DB prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/). Lär dig mer om kostnaden för etablering av data flöde och förbrukning av lagrings utrymme.
3.  [Enheter för programbegäran](request-units.md). Förstå användningen av data flödet för olika åtgärds typer, till exempel läsa, skriva och fråga.