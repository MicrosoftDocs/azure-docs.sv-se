---
title: Viktig information och resurser för Azure Cosmos DB Apache Spark-anslutning för SQL API
description: Lär dig allt om Azure Cosmos DB Apache Spark-anslutningen för SQL API, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 46ddbd18051ffa44232468704ce189d4171b50e7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590017"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark Connector for Core (SQL) API: viktig information och resurser
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Asynkron Java-SDK v2](sql-api-sdk-async-java.md)
> * [Synkron Java-SDK v2](sql-api-sdk-java.md)
> * [Våren data v2](sql-api-sdk-java-spring-v2.md)
> * [Våren data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-anslutning](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resursprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Mass utförar – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Mass utförar – Java](sql-api-sdk-bulk-executor-java.md)

Påskynda stor data analys med hjälp av Azure Cosmos DB Apache Spark Connector for Core (SQL). Med Spark-anslutningen kan du köra [Spark ](https://spark.apache.org/) -jobb på data som lagras i Azure Cosmos dB. Batch-och Stream-bearbetning stöds.

Du kan använda anslutningen med [Azure Databricks](https://azure.microsoft.com/services/databricks) eller [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), som tillhandahåller hanterade Spark-kluster i Azure. I följande tabell visas de Spark-versioner som stöds.

| Komponent | Version |
|---------|-------|
| Apache Spark | 2.4. x, 2.3. x, 2,2. x och 2.1. x |
| Scala | 2,11 |
| Azure Databricks körnings version | > 3,4 |

> [!WARNING]
> Den här anslutningen stöder kärnan (SQL) API för Azure Cosmos DB.
> För Cosmos DB för MongoDB-API använder du [MongoDB Spark-anslutningsprogrammet](https://docs.mongodb.com/spark-connector/master/).
> För Cosmos DB API för Cassandra använder du [Cassandra Spark-anslutaren](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="helpful-content"></a>Användbart innehåll

| Innehåll | Länk |
|---|---|
| **SDK-hämtning** | [Ladda ned från Apache Spark](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) |
|**API-dokumentation** | [Spark Connector-referens]() |
|**Bidra till SDK** | [Azure Cosmos DB Connector för Apache Spark på GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Komma igång** | [Påskynda stor data analys genom att använda Apache Spark för att Azure Cosmos DB koppling](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [Använd Apache Spark strukturerad strömning med Apache Kafka och Azure Cosmos DB](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Versions historik

### <a name="311"></a>3.1.1
#### <a name="new-features"></a>Nya funktioner
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Åtgärdar ett Edge-fall för strömnings kontroll punkt där i "ID" innehåller "|"-tecknen med "ChangeFeedMaxPagesPerBatch"-konfigurationen tillämpad

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Nya funktioner
* Lägger till stöd för Mass uppdateringar när du använder kapslade partitionsnyckel
* Lägger till stöd för decimal-och flytt ALS data typer vid skrivningar till Cosmos DB.
* Lägger till stöd för timestamp-typer när de använder långa (UNIX-epok) som ett värde
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar

### <a name="308"></a>3.0.8
#### <a name="new-features"></a>Nya funktioner
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Korrigerar typ konverterings undantag vid användning av "WriteThroughputBudget" config.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Nya funktioner
* Lägger till fel information för Mass fel i undantag och loggen.
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar

### <a name="306"></a>3.0.6
#### <a name="new-features"></a>Nya funktioner
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Åtgärdar problem med direkt uppspelnings kontroll.

### <a name="305"></a>3.0.5
#### <a name="new-features"></a>Nya funktioner
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Korrigerar logg nivån för ett meddelande som har lämnats oavsiktligt med nivå fel för att minska bruset

### <a name="304"></a>3.0.4
#### <a name="new-features"></a>Nya funktioner
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Korrigerar ett fel i strukturerad strömning under partitions delningar – eventuellt resulterade i att vissa ändringar i flödes poster saknas eller att inga undantag visas för kontroll punkts skrivningar

### <a name="303"></a>3.0.3
#### <a name="new-features"></a>Nya funktioner
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Åtgärdar ett fel där ett anpassat schema som angetts för readStream ignoreras

### <a name="302"></a>3.0.2
#### <a name="new-features"></a>Nya funktioner
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Rättningar regression (unshader JAR inkluderar alla skuggade beroenden) som ökade Bygg tiden med 50%

### <a name="301"></a>3.0.1
#### <a name="new-features"></a>Nya funktioner
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Åtgärdar ett beroende problem som orsakar direkt transport över TCP till att fungera med RequestTimeoutException

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Nya funktioner
* Förbättrar anslutnings hanteringen och anslutningspoolen för att minska antalet metadata-anrop
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om Cosmos DB finns i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan.

Mer information om Apache Spark finns [på Start sidan](https://spark.apache.org/).