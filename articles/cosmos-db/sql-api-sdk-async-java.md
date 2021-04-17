---
title: 'Azure Cosmos DB: SQL Async Java API, SDK & resurser'
description: Lär dig allt om SQL Async Java API och SDK, inklusive lanseringsdatum, tillbakagångar och ändringar som görs mellan varje version av Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: a63194ae3ca9e5661b82113c450ebb929cc67e76
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364540"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async Java SDK för SQL API: Information och resurser
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
> * [REST](/rest/api
> * [REST-resursprovider](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-api-query-reference.md)
> * [Masskörningsbibliotek – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Masskörningsbibliotek – Java](sql-api-sdk-bulk-executor-java.md)

SQL API Async Java SDK skiljer sig från SQL API Java SDK genom att tillhandahålla asynkrona åtgärder med stöd för [Netty-biblioteket](https://netty.io/). Den befintliga [SQL API Java SDK:n](sql-api-sdk-java.md) stöder inte asynkrona åtgärder. 

> [!IMPORTANT]  
> Det här *är inte* den senaste Java SDK:n för Azure Cosmos DB! Överväg att [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) för projektet. Om du vill uppgradera följer du anvisningarna i [guiden Migrera till Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) och guiden Reactor vs [RxJava.](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) 
>

| | Länkar |
|---|---|
| **SDK-nedladdning** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API-dokumentation** |[Java API-referensdokumentation](/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient) | 
|**Bidra till SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Komma igång** | [Kom igång med Async Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Kodexempel** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Prestandatips**| [Readme-information för GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Minsta körningstid som stöds**|[JDK 8](/java/azure/jdk/) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se även
Mer information om Cosmos DB finns på sidan om tjänsten [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).