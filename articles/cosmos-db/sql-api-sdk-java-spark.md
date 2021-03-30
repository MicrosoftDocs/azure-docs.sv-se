---
title: Viktig information och resurser för Cosmos DB Apache Spark-anslutning för SQL API
description: Lär dig mer om Azure Cosmos DB Apache Spark Connector för SQL API, inklusive versions datum, datum för indrag och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 7ff75bf9d45803dd96927bcf7c70e7c7912db979
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097166"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark Connector for Core (SQL) API: viktig information och resurser
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Asynkron Java-SDK v2](sql-api-sdk-async-java.md)
> * [Synkron Java-SDK v2](sql-api-sdk-java.md)
> * [Spring-data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring-data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-anslutningsprogram](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST Resource Provider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Masskörningsbibliotek – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Masskörningsbibliotek – Java](sql-api-sdk-bulk-executor-java.md)

Du kan påskynda stor data analys med hjälp av Azure Cosmos DB Apache Spark Connector for Core (SQL). Med Spark-anslutningen kan du köra [Spark](https://spark.apache.org/) -jobb på data som lagras i Azure Cosmos dB. Batch-och Stream-bearbetning stöds.

Du kan använda anslutningen med [Azure Databricks](https://azure.microsoft.com/services/databricks) eller [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), som tillhandahåller hanterade Spark-kluster i Azure. I följande tabell visas versioner som stöds:

| Komponent | Version |
|---------|-------|
| Apache Spark | 2,4.*x*, 2,3. *x*, 2,2. *x* och 2,1. *x* |
| Scala | 2,11 |
| Azure Databricks (körnings version) | Senare än 3,4 |

> [!WARNING]
> Den här anslutningen stöder kärnan (SQL) API för Azure Cosmos DB.
> För Cosmos DB-API för MongoDB använder du [MongoDB-anslutaren för Spark](https://docs.mongodb.com/spark-connector/master/).
> För Cosmos DB API för Cassandra använder du [Cassandra Spark-anslutaren](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="resources"></a>Resurser

| Resurs | Länk |
|---|---|
| **SDK-nedladdning** | [Hämta senaste. jar](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG), [maven](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) |
|**API-dokumentation** | [Spark Connector-referens]() |
|**Bidra till SDK: n** | [Azure Cosmos DB Connector för Apache Spark på GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Komma igång** | [Påskynda stor data analys genom att använda Apache Spark för att Azure Cosmos DB koppling](./spark-connector.md#bk_working_with_connector) <br> [Använd Apache Spark strukturerad strömning med Apache Kafka och Azure Cosmos DB](../hdinsight/apache-kafka-spark-structured-streaming-cosmosdb.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Versions historik

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>Nya funktioner
- Lägger till ett nytt konfigurations alternativ, `changefeedstartfromdatetime` som kan användas för att ange start tiden för när changefeed ska bearbetas. Mer information finns i [konfigurations alternativ](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references).

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
- Åtgärdar en regression som orsakade överdriven minnes förbrukning på körningarna för stora resultat uppsättningar (till exempel med miljon tals rader), vilket resulterade i felet `java.lang.OutOfMemoryError: GC overhead limit exceeded` .

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Åtgärdar ett Edge-fall för strömnings kontroll punkt där det `ID` innehåller pipe-tecknet (|) med `ChangeFeedMaxPagesPerBatch` konfigurationen tillämpad.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Nya funktioner
* Lägger till stöd för Mass uppdateringar när kapslade partitionsuppsättningar används.
* Lägger till stöd för decimal-och flytt ALS data typer vid skrivningar till Azure Cosmos DB.
* Lägger till stöd för timestamp-typer när de använder långa (UNIX-epok) som ett värde.

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Korrigerar typecast-undantag som inträffar när `WriteThroughputBudget` config används.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Nya funktioner
* Lägger till fel information för Mass fel i undantag och loggen.

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Åtgärdar problem med direkt uppspelnings kontroll.

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* För att minska bruset, korrigerar logg nivån för ett meddelande av misstag med nivå fel.

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Åtgärdar en bugg i strukturerad strömning vid delning av partitioner. Felet kan leda till att det saknas ändringar i flödes poster eller null-undantag för kontroll punkts skrivningar.

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Åtgärdar ett fel som gör att ett anpassat schema som angetts för readStream ignoreras.

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Korrigerar en regression (unshader JAR inkluderar alla skuggade beroenden) som ökar Bygg tiden med 50 procent.

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Åtgärdar ett beroende problem som gör att det inte går att skicka direkt transporter över TCP till RequestTimeoutException.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Nya funktioner
* Förbättrar anslutnings hanteringen och anslutningspoolen för att minska antalet metadata-anrop.

## <a name="faq"></a>Vanliga frågor
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Läs mer om [Apache Spark](https://spark.apache.org/).
