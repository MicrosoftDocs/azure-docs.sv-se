---
title: Azure Cosmos DB Apache Spark 2 OLTP Connector for SQL API release notes and resources (Information och resurser om OLTP-anslutningsprogram för SQL API)
description: Lär dig mer om Azure Cosmos DB Apache Spark 2 OLTP Connector för SQL API, inklusive lanseringsdatum, tillbakakopplingsdatum och ändringar som görs mellan varje version av Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: bd948814b4b647bcc3fbfe58b090b1e794504232
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363639"
---
# <a name="azure-cosmos-db-apache-spark-2-oltp-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark 2 OLTP Connector for Core (SQL) API: Viktig information och resurser
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
> * [Spark 3 OLTP Connector](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resursprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Masskörningsbibliotek – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Masskörningsbibliotek – Java](sql-api-sdk-bulk-executor-java.md)

Du kan påskynda analys av stordata med hjälp Azure Cosmos DB Apache Spark 2 OLTP Connector for Core (SQL). Med Spark-anslutningsappen kan du köra [Spark-jobb](https://spark.apache.org/) på data som lagras Azure Cosmos DB. Batch- och strömbearbetning stöds.

Du kan använda anslutningsappen [med Azure Databricks](https://azure.microsoft.com/services/databricks) eller [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), som tillhandahåller hanterade Spark-kluster i Azure. Följande tabell visar versioner som stöds:

| Komponent | Version |
|---------|-------|
| Apache Spark | 2.4.*x*, 2.3. *x*, 2.2. *x* och 2.1. *x* |
| Scala | 2,11 |
| Azure Databricks (körningsversion) | Senare än 3.4 |

> [!WARNING]
> Den här anslutningsappen stöder kärn-API:et (SQL) för Azure Cosmos DB.
> För Cosmos DB API för MongoDB använder du [MongoDB-anslutningsappen för Spark.](https://docs.mongodb.com/spark-connector/master/)
> För Cosmos DB API för Cassandra du [Cassandra Spark-anslutningsappen](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="resources"></a>Resurser

| Resurs | Länk |
|---|---|
| **SDK-nedladdning** | [Ladda ned senaste .jar,](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) [Maven](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) |
|**API-dokumentation** | [Referens för Spark-anslutningsapp]() |
|**Bidra till SDK** | [Azure Cosmos DB Connector för Apache Spark på GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Komma igång** | [Påskynda analys av stordata med hjälp av Apache Spark för Azure Cosmos DB anslutningsappen](./spark-connector.md#bk_working_with_connector) <br> [Använda Apache Spark Structured Streaming med Apache Kafka och Azure Cosmos DB](../hdinsight/apache-kafka-spark-structured-streaming-cosmosdb.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Lanseringshistorik

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>Nya funktioner
- Lägger till ett nytt konfigurationsalternativ, , som kan användas för att ange `changefeedstartfromdatetime` starttiden för när ändringsfeed ska bearbetas. Mer information finns i [Konfigurationsalternativ.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
- Åtgärdar en regression som orsakade överdriven minnesförbrukning på utförarna för stora resultatuppsättningar (till exempel med miljontals rader), vilket resulterar i felet `java.lang.OutOfMemoryError: GC overhead limit exceeded` .

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* Åtgärdar ett gränsfall för strömningskontrollpunkter `ID` där innehåller pipe-tecknet (|) med `ChangeFeedMaxPagesPerBatch` -konfigurationstecknet tillämpat.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Nya funktioner
* Lägger till stöd för massuppdateringar när kapslade partitionsnycklar används.
* Lägger till stöd för decimal- och flyttalsdatatyper under skrivningar till Azure Cosmos DB.
* Lägger till stöd för tidsstämpeltyper när de använder Long (Unix-epok) som ett värde.

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* Åtgärdar typecast-undantag som inträffar `WriteThroughputBudget` när konfigurationsfiler används.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Nya funktioner
* Lägger till felinformation för massfel i undantag och logg.

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* Åtgärdar problem med kontrollpunkter för direktuppspelning.

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* För att minska bruset korrigerar loggnivån för ett meddelande oavsiktligt med nivån ERROR.

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* Åtgärdar en bugg i strukturerad strömning under partitionsdelningar. Buggen kan resultera i vissa saknade poster för ändringsflöde eller Null-undantag för skrivningar till kontrollpunkter.

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* Åtgärdar en bugg som gör att ett anpassat schema som anges för readStream ignoreras.

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* Åtgärdar en regression (oskadd JAR innehåller alla skuggade beroenden) som ökar byggtiden med 50 procent.

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* Åtgärdar ett beroendeproblem som gör att Direct Transport över TCP misslyckas med RequestTimeoutException.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Nya funktioner
* Förbättrar anslutningshantering och anslutningspooler för att minska antalet metadata-anrop.

## <a name="faq"></a>Vanliga frågor
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Läs mer om [Apache Spark](https://spark.apache.org/).
