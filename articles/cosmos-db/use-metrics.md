---
title: Övervaka och felsöka med mått i Azure Cosmos DB
description: Använd mått i Azure Cosmos DB för att felsöka vanliga problem och övervaka databasen.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/09/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: cf92d9e1a1f92c2dc3294b71e3e620166fd90680
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818714"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Övervaka och felsöka med mått i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB tillhandahåller mått för dataflöde, lagring, konsekvens, tillgänglighet och svarstid. Azure-portalen innehåller en sammanställd vy över dessa mått. Du kan också Visa Azure Cosmos DB-mått från Azure Monitor-API:et. Dimensionsvärdena för måtten, till exempel containernamn, är icke-känsliga. Därför måste du använda icke-fallkänslig jämförelse när du gör strängjämförelser på dessa dimensionsvärden. Mer information om hur du visar mått från Azure Monitor finns i [artikeln Hämta mått Azure Monitor](./monitor-cosmos-db.md) mått.

Den här artikeln vägleder dig genom vanliga användningsfall och beskriver hur Azure Cosmos DB-mått kan användas för att analysera och felsöka dessa problem. Mått samlas in var femte minut och sparas i sju dagar.

## <a name="view-metrics-from-azure-portal"></a>Visa mått från Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com/)

1. Öppna **fönstret** Mått. Som standard visar måttfönstret mått för lagring, index och enheter för programbegäran för alla databaser i ditt Azure Cosmos-konto. Du kan filtrera dessa mått per databas, container eller region. Du kan också filtrera måtten vid en viss tidskornighet. Mer information om måtten dataflöde, lagring, tillgänglighet, svarstid och konsekvens finns på separata flikar. 

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Cosmos DB prestandamått i Azure Portal":::

Följande mått är tillgängliga i **fönstret** Mått:

* **Dataflödesmått** – Det här måttet visar antalet förbrukade eller misslyckade begäranden (429 svarskod) eftersom dataflödet eller lagringskapaciteten som etablerats för containern har överskridits.

* **Lagringsmått** – Det här måttet visar storleken på data- och indexanvändningen.

* **Tillgänglighetsmått** – Det här måttet visar procentandelen lyckade begäranden över det totala antalet begäranden per timme. Lyckade resultat definieras av de Azure Cosmos DB serviceavtalen.

* **Svarstidsmått –** Det här måttet visar den läs- och skrivfördröjning som observerats av Azure Cosmos DB i den region där ditt konto används. Du kan visualisera svarstiden mellan regioner för ett geo-replikerat konto. Det här måttet representerar inte svarstiden för förfrågningar från slutet till slut.

* **Konsekvensmått –** Det här måttet visar hur slutlig är konsekvensen för den konsekvensmodell du väljer. För konton i flera regioner visar det här måttet även replikeringsfördröjningen mellan de regioner som du har valt.

* **Systemmått –** Det här måttet visar hur många metadatabegäranden som betjänas av den primära partitionen. Det hjälper också till att identifiera begränsade begäranden.

I följande avsnitt beskrivs vanliga scenarier där du kan använda Azure Cosmos DB mått. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Förstå hur många begäranden som lyckas eller orsakar fel

Kom igång genom att gå till [Azure Portal](https://portal.azure.com) och gå till **bladet Mått.** På bladet hittar du diagrammet **Antal begäranden överskred kapaciteten per 1 minut. Det här diagrammet visar en minut per minut för totalt antal begäranden segmenterade efter statuskod. Mer information om HTTP-statuskoder finns i [HTTP-statuskoder för Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Den vanligaste felstatuskoden är 429 (hastighetsbegränsning/begränsning). Det här felet innebär att begäranden Azure Cosmos DB är mer än det etablerade dataflödet. Den vanligaste lösningen på det här problemet är att [skala upp RU:erna](./set-throughput.md) för den angivna samlingen.

:::image type="content" source="media/use-metrics/metrics-12.png" alt-text="Antal begäranden per minut":::

## <a name="determine-the-throughput-distribution-across-partitions"></a>Fastställa dataflödesfördelningen mellan partitioner

Att ha en bra kardinalitet för dina partitionsnycklar är viktigt för alla skalbara program. Om du vill fastställa **dataflödesdistributionen** för partitionerade containrar uppdelade efter partitioner går du till bladet [Mått i Azure Portal](https://portal.azure.com). På fliken **Dataflöde** visas lagringsuppdelningen i diagrammet **Maximal förbrukad RU/sekund av varje fysisk partition.** Följande bild visar ett exempel på en dålig fördelning av data som visas av den skeva partitionen längst till vänster.

:::image type="content" source="media/use-metrics/metrics-17.png" alt-text="En enskild partition med hög användning":::

En ojämn dataflödesdistribution kan *orsaka heta* partitioner, vilket kan resultera i begränsade begäranden och kräva ompartitionering. Mer information om partitionering i Azure Cosmos DB finns i [Partition and scale in Azure Cosmos DB](./partitioning-overview.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Fastställa lagringsdistributionen mellan partitioner

Att ha en bra kardinalitet för partitionen är viktigt för alla skalbara program. Om du vill fastställa lagringsdistributionen för partitionerade containrar uppdelade efter partitioner går du till bladet Mått i [Azure Portal](https://portal.azure.com). På fliken Lagring visas lagringsuppdelningen i diagrammet Data + Indexlagring som används av de översta partitionsnycklarna. Följande bild visar en dålig fördelning av datalagring som visas av den skeva partitionen längst till vänster.

:::image type="content" source="media/use-metrics/metrics-07.png" alt-text="Exempel på dålig datadistribution":::

Du kan rotorsaken till vilken partitionsnyckel som förvränger fördelningen genom att klicka på partitionen i diagrammet.

:::image type="content" source="media/use-metrics/metrics-05.png" alt-text="Partitionsnyckeln förvränger distributionen":::

När du har identifierat vilken partitionsnyckel som orsakar snedfördelningen kan du behöva partitionera om containern med en mer distribuerad partitionsnyckel. Mer information om partitionering i Azure Cosmos DB finns i [Partition and scale in Azure Cosmos DB](./partitioning-overview.md).

## <a name="compare-data-size-against-index-size"></a>Jämföra datastorlek med indexstorlek

I Azure Cosmos DB är den totala förbrukade lagringen kombinationen av både Datastorlek och Indexstorlek. Vanligtvis är indexstorleken en bråkdel av datastorleken. Mer information finns i artikeln [Indexstorlek.](index-policy.md#index-size) På bladet Mått i [Azure Portal](https://portal.azure.com)visar fliken Lagring en analys av lagringsförbrukningen baserat på data och index.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Om du vill spara indexutrymme kan du justera [indexeringsprincipen](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>Felsöka varför frågor körs långsamt

I SQL API-SDK:erna Azure Cosmos DB frågekörningsstatistik.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* innehåller information om hur lång tid varje komponent i frågan tog att köra. Den vanligaste rotorsaken till långvariga frågor är genomsökningar, vilket innebär att frågan inte kunde utnyttja indexen. Det här problemet kan lösas med ett bättre filtervillkor.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du övervakar och felsöker problem med hjälp av måtten i Azure Portal. Du kanske vill veta mer om att förbättra databasprestanda genom att läsa följande artiklar:

* Mer information om hur du visar mått från Azure Monitor finns i artikeln [Hämta mått Azure Monitor](./monitor-cosmos-db.md) mått. 
* [Prestanda- och skalningstestning med Azure Cosmos DB](performance-testing.md)
* [Prestandatips för Azure Cosmos DB](performance-tips.md)