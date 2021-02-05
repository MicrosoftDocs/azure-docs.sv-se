---
title: Data partitionering i Azure Cosmos DB Gremlin-API
description: Lär dig hur du kan använda en partitionerad graf i Azure Cosmos DB. Den här artikeln beskriver också krav och bästa praxis för ett partitionerat diagram.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 91516c9815cfd71ffb59c399ea6580c6e28d8fce
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576472"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Använda en partitionerad graf i Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

En av de viktigaste funktionerna i Gremlin-API: et i Azure Cosmos DB är möjligheten att hantera storskaliga grafer genom vågrät skalning. Behållare kan skalas oberoende av lagring och data flöde. Du kan skapa behållare i Azure Cosmos DB som automatiskt kan skalas för att lagra diagram data. Data bal anse ras automatiskt baserat på den angivna **partitionsnyckel**.

Partitionering görs internt om behållaren förväntas lagra mer än 20 GB i storlek eller om du vill allokera mer än 10 000 begär ande enheter per sekund (ru: er). Data partitioneras automatiskt utifrån den partitionsnyckel som du anger. Partitionsnyckel krävs om du skapar graf-behållare från Azure Portal eller 3. x eller senare versioner av Gremlin-drivrutiner. Partitionsnyckel krävs inte om du använder 2. x eller lägre versioner av Gremlin-drivrutiner. 

Samma allmänna principer från mekanismen för [Azure Cosmos DB partitionering](partitioning-overview.md) gäller med några diagram olika optimeringar som beskrivs nedan.

:::image type="content" source="./media/graph-partitioning/graph-partitioning.png" alt-text="Diagram partitionering." border="false":::

## <a name="graph-partitioning-mechanism"></a>Diagram partitionerings funktion

Följande rikt linjer beskriver hur partitionerings strategin i Azure Cosmos DB fungerar:

- **Både hörn och kanter lagras som JSON-dokument**.

- **Hörn kräver en partitionsnyckel**. Den här nyckeln avgör i vilken partition som hörnen ska lagras via en hash-algoritm. Egenskaps namnet för partitionsnyckel definieras när du skapar en ny behållare och har formatet: `/partitioning-key-name` .

- **Kanterna kommer att lagras med deras käll-hörn**. Med andra ord definierar dess partitionsnyckel var de lagras tillsammans med dess utgående kanter. Den här optimeringen görs för att undvika frågor över partitioner när du använder `out()` kardinalitet i graf-frågor.

- **Kanterna innehåller referenser till de hörn som de pekar på**. Alla kanter lagras med partitionsalternativ och ID: n för de hörn som de pekar på. Den här beräkningen gör `out()` att alla riktnings frågor alltid är en omfångs partition med partitionerad fråga och inte en fråga om en hemlig partition.

- **Diagram frågor måste ange en partitionsnyckel**. Om du vill dra full nytta av den vågräta partitionering i Azure Cosmos DB bör du ange partitionsnyckel när ett enda hörn är valt, när det är möjligt. Följande är frågor för att markera ett eller flera hörn i ett partitionerat diagram:

    - `/id` och `/label` stöds inte som partitionsnyckel för en behållare i Gremlin-API: et.


    - Välj ett formhörn efter ID och **Använd sedan `.has()` steget för att ange egenskapen partitionsnyckel**:

        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```

    - Välja ett formhörn genom att **Ange en tupel, inklusive partitionsnyckel och ID**:

        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```

    - Ange en **matris med tupler av värden för partitionsnyckel och ID**:

        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```

    - Välja en uppsättning formhörn med sina ID: n och **Ange en lista med värden för partitionsnyckel**:

        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Använda en **partitions strategi** i början av en fråga och ange en partition för omfånget för resten av Gremlin-frågan:

        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Metod tips när du använder en partitionerad Graf

Använd följande rikt linjer för att säkerställa prestanda och skalbarhet när du använder partitionerade diagram med obegränsade behållare:

- **Ange alltid värdet för partitionsnyckel vid frågor mot ett hörn**. Att hämta vertex från en känd partition är ett sätt att uppnå prestanda. Alla efterföljande angränsande åtgärder kommer alltid att begränsas till en partition eftersom kanterna innehåller referens-ID och partitionsnyckel till sina mål formhörn.

- **Använd den utgående riktningen när du frågar efter kanter när det är möjligt**. Som nämnts ovan lagras kanter med deras käll hörn i den utgående riktningen. Det innebär att det är möjligt att att använda frågor över olika partitioner minimeras när data och frågor är utformade med det här mönstret i åtanke. I motsatsen är `in()` frågan alltid en dyr fläkt fråga.

- **Välj en partitionsnyckel som jämnt distribuerar data mellan partitioner**. Det här beslutet beror kraftigt på data modellen för lösningen. Läs mer om hur du skapar en lämplig partitionsnyckel i [partitionering och skalning i Azure Cosmos DB](partitioning-overview.md).

- **Optimera frågor för att hämta data inom gränserna för en partition**. En optimal partitionerings strategi skulle justeras mot fråge mönstren. Frågor som hämtar data från en enda partition ger bästa möjliga prestanda.

## <a name="next-steps"></a>Nästa steg

Härnäst kan du fortsätta att läsa följande artiklar:

* Lär dig mer om [partition och skalning i Azure Cosmos DB](partitioning-overview.md).
* Läs mer om [Gremlin-stöd i Gremlin-API](gremlin-support.md).
* Lär dig mer om [Introduktion till Gremlin-API](graph-introduction.md).
