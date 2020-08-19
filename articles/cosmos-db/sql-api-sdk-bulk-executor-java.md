---
title: 'Azure Cosmos DB: bulk utförar Java API, SDK &-resurser'
description: Lär dig allt om bulk-utförar Java API och SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB bulk utförar Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 60c2b3e194fc7703ad1c3f1d3138f9a2c6301f33
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585057"
---
# <a name="java-bulk-executor-library-download-information"></a>Java-bibliotek för Mass utförar: Hämta information

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

| |  |
|---|---|
|**Beskrivning**|Med bulk utförar-biblioteket kan klient program utföra Mass åtgärder i Azure Cosmos DB konton. i bulk utförar-biblioteket finns BulkImport-och BulkUpdate-namnrymder. BulkImport-modulen kan samla in dokument på ett optimerat sätt, så att det data flöde som har allokerats för en samling förbrukas i maximal omfattning. BulkUpdate-modulen kan massredigera befintliga data i Azure Cosmos-behållare som korrigeringsfiler.|
|**SDK-hämtning**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Mass utförar-bibliotek i GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API-dokumentation**| [Referens dokumentation för Java API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Komma igång**|[Kom igång med bulk utförar Library Java SDK](bulk-executor-java.md)|
|**Lägsta körnings tid som stöds**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Viktig information

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* Korrigering för DocumentAnalyzer. Java för att korrekt extrahera kapslade nyckel värden från JSON.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Lägg till funktioner i BulkDelete-åtgärder för att försöka igen vid vissa fel och returnera även en lista över fel till användaren som kan provas igen.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Uppdatering för Cosmos SDK-version 2.4.7.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* Korrigera för "mergeAll" om du vill fortsätta med "ID" och partitionsnyckel så att alla korrigerade dokument egenskaper som placeras efter "ID" och partitionsnyckel läggs till i listan över uppdaterade objekt.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Uppdatera start grad för samtidighet till 1 och tillagda fel söknings loggar för minibatch.


