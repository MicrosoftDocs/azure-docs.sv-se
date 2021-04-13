---
title: Azure Cosmos DB Apache Spark 3 OLTP Connector för SQL API (förhandsversion) – information och resurser
description: Lär dig mer om Azure Cosmos DB Apache Spark 3 OLTP Connector för SQL API (förhandsversion), inklusive lanseringsdatum, tillbakagångar och ändringar som görs mellan varje version av Azure Cosmos DB SQL Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: e7d206b63d6e1bf741a5dc298dd5bbc2d48ab11b
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368703"
---
# <a name="azure-cosmos-db-apache-spark-3-oltp-connector-for-core-sql-api-preview-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark 3 OLTP Connector for Core (SQL) API (förhandsversion): Viktig information och resurser
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

**Azure Cosmos DB Spark 3 OLTP-anslutningsprogram (förhandsversion)** ger Apache Spark v3-stöd för Azure Cosmos DB sql-API:et.
[Azure Cosmos DB](introduction.md) är en globalt distribuerad databastjänst som utvecklare kan använda för att arbeta med data med en mängd olika standard-API:er, till exempel SQL, MongoDB, Cassandra, Graph och Table.

> [!Note]
> Den här versionen Azure Cosmos DB Spark 3 OLTP-anslutning är en förhandsversion.
> Den här versionen har inte belastnings- eller prestandatestas.
> Den här versionen rekommenderas inte för användning i produktionsscenarier.
>

## <a name="documentation"></a>Dokumentation

- [Komma igång](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/quick-start.md)
- [Katalog-API](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md)
- [Referens för konfigurationsparameter](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md)


## <a name="version-compatibility"></a>Versionskompatibilitet

| Anslutning     | Spark         | Lägsta Java-version | Scala-versioner som stöds |
| ------------- | ------------- | -------------------- | -----------------------  |
| 4.0.0-beta.1  | 3.1.1         |        8             | 2.12                     |

## <a name="download"></a>Ladda ned

Du kan använda Maven-koordinaten för jar för att automatiskt installera Spark Connector till din Databricks Runtime 8 från Maven: `com.azure.cosmos.spark:azure-cosmos-spark_3-1_2-12:4.0.0-beta.1`

Du kan också integrera mot Cosmos DB Spark Connector i ditt SBT-projekt:
```scala
libraryDependencies += "com.azure.cosmos.spark" % "azure-cosmos-spark_3-1_2-12" % "4.0.0-beta.1"
```

Cosmos DB Spark-anslutningsappen finns på [Maven Central Repo](https://search.maven.org/artifact/com.azure.cosmos.spark/azure-cosmos-spark_3-1_2-12/4.0.0-beta.1/jar).

### <a name="general"></a>Allmänt

Om du stöter på en bugg kan du skicka in ett problem [här](https://github.com/Azure/azure-sdk-for-java/issues/new).

Om du vill föreslå en ny funktion eller ändringar som kan göras kan du öppna ett ärende på samma sätt som du skulle göra för en bugg.

[!INCLUDE[Changelog](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos-spark_3-1_2-12/CHANGELOG.md)]

## <a name="next-steps"></a>Nästa steg

Läs vår [snabbstartsguide för att arbeta med Azure Cosmos DB Oltp-anslutningsapp för Spark 3.](create-sql-api-spark.md)
