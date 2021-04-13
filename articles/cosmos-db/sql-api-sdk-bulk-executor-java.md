---
title: 'Azure Cosmos DB: Java API för massut executor, SDK & resurser'
description: Lär dig allt om Java API och SDK för mass utförare, inklusive lanseringsdatum, tillbakagångar och ändringar som gjorts mellan varje version av Azure Cosmos DB Java SDK för massutförande.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 2d3c7026fd221b1a17b8efe56b03b2a26358c7ab
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364436"
---
# <a name="java-bulk-executor-library-download-information"></a>Java-massuttagningsbibliotek: Ladda ned information
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
> * [Spark 3 OLTP-anslutning](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resursprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Masskörningsbibliotek – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Masskörningsbibliotek – Java](sql-api-sdk-bulk-executor-java.md)

| | Länk/anteckningar |
|---|---|
|**Beskrivning**|Massuttagningsbiblioteket gör att klientprogram kan utföra massåtgärder i Azure Cosmos DB konton. bulk executor-biblioteket innehåller Namnrymder för BulkImport och BulkUpdate. BulkImport-modulen kan massinläsa dokument på ett optimerat sätt så att dataflödet som etablerats för en samling förbrukas i maximal utsträckning. BulkUpdate-modulen kan massuppdatera befintliga data i Azure Cosmos-containrar som korrigeringar.|
|**SDK-nedladdning**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Massuttagningsbibliotek i GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API-dokumentation**| [Java API-referensdokumentation](/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Komma igång**|[Kom igång med Java SDK för massut executor-biblioteket](bulk-executor-java.md)|
|**Minsta körningstid som stöds**|[Java Development Kit (JDK) 7+](/java/azure/jdk/)|

## <a name="release-notes"></a>Viktig information

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* Korrigering för DocumentAnalyzer.java för korrekt extrahering av kapslade partitionsnyckelvärden från json.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Lägg till funktioner i BulkDelete-åtgärder för att försöka igen vid specifika fel och även returnera en lista över fel till användaren som kan försökas igen.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Uppdatering för Cosmos SDK version 2.4.7.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* Korrigering för att "mergeAll" ska fortsätta med id- och partitionsnyckelvärdet så att alla korrigerade dokumentegenskaper som placeras efter "id" och partitionsnyckelvärdet läggs till i den uppdaterade objektlistan.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Uppdatera startgraden för samtidighet till 1 och lägga till felsökningsloggar för minibatch.