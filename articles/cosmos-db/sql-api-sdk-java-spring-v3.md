---
title: Versionsanteckningar och resurser för Spring Data Azure Cosmos DB v3 för SQL API
description: Lär dig mer om Spring Data Azure Cosmos DB v3 för SQL API, inklusive lanseringsdatum, förfallodatum och ändringar som görs mellan varje version av Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 594d38425be0304a9f7737bdfba60b29187a2e2d
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363520"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Spring Data Azure Cosmos DB v3 for Core (SQL) API: Viktig information och resurser
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

Med Spring Data Azure Cosmos DB version 3 för Core (SQL) kan utvecklare använda Azure Cosmos DB Spring-program. Spring Data Azure Cosmos DB exponerar Spring Data-gränssnittet för att manipulera databaser och samlingar, arbeta med dokument och utfärda frågor. Både SYNC- och Async(Reactive) API:er stöds i samma Maven-artefakt. 

> [!IMPORTANT]
> Spring Data Azure Cosmos DB är beroende av Spring Data-ramverket.
> 
> azure-spring-data-cosmos-versioner från 3.0.0 till 3.4.0 stöder Spring Data version 2.2 och 2.3.
> 
> azure-spring-data-cosmos version 3.5.0 och senare stöder Spring Data-versionerna 2.4.3 och senare.
>

[Spring Framework är](https://spring.io/projects/spring-framework) en programmerings- och konfigurationsmodell som effektiviserar Java-programutveckling. Spring effektiviserar programmens "steg för steg" med hjälp av beroendeinjektion. Många utvecklare gillar Spring eftersom det gör det enklare att skapa och testa program. [Spring Boot](https://spring.io/projects/spring-boot) den här hanteringen av rör sig med ett öga mot webbprogram- och mikrotjänstutveckling. [Spring Data](https://spring.io/projects/spring-data) är en programmeringsmodell och ett ramverk för åtkomst till datalager som Azure Cosmos DB från kontexten för ett Spring- eller Spring Boot program. 

Du kan använda Spring Data-Azure Cosmos DB i [dina Azure Spring Cloud program.](https://azure.microsoft.com/services/spring-cloud/)

> [!IMPORTANT]  
> Dessa versionsanteckningar gäller för version 3 av Spring Data Azure Cosmos DB. Du hittar [versionsanteckningar för version 2 här.](sql-api-sdk-java-spring-v2.md) 
>
> Spring Data Azure Cosmos DB stöder endast SQL-API:et.
>
> I de här artiklarna finns information om Spring Data på andra Azure Cosmos DB API:er:
> * [Spring Data för Apache Cassandra med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="get-started-fast"></a>Kom igång snabbt

  Kom igång med Spring Data Azure Cosmos DB följa vår [Spring Boot Starter- guide.](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) Metoden Spring Boot Starter är det rekommenderade sättet att komma igång med Spring Data Azure Cosmos DB anslutningsappen.

  Du kan också lägga till Spring Data-Azure Cosmos DB beroendet till filen `pom.xml` enligt nedan:

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest-version</version>
  </dependency>
  ```

## <a name="helpful-content"></a>Användbart innehåll

| Innehåll | Länk |
|---|---|
|**SDK-nedladdning**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-cosmos) |
|**API-dokumentation** | [Java API-referensdokumentation](/java/api/com.azure.spring.data.cosmos) |
|**Bidra till SDK** | [Azure SDK för Java Central Repo på GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**Komma igång** | [Snabbstart: Skapa en Spring Data Azure Cosmos DB app för att hantera Azure Cosmos DB SQL API-data](./create-sql-api-spring-data.md) <br> [GitHub-lagringsplatsen med snabbstartskod](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**Grundläggande kodexempel** | [Azure Cosmos DB: Spring Data Azure Cosmos DB exempel för SQL API](sql-api-spring-data-sdk-samples.md) <br> [GitHub-lagringsplatsen med exempelkod](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **Prestandatips**| [Prestandatips för Java SDK v4 (gäller för Spring Data)](performance-tips-java-sdk-v4-sql.md)| 
| **Felsökning** | [Felsöka Java SDK v4 (gäller för Spring Data)](troubleshoot-java-sdk-v4-sql.md) | 
| **Azure Cosmos DB workshoppar och labb** |[Cosmos DB för studiecirklar](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>Ytterligare information

* Spring Data Azure Cosmos DB Java JDK 8 och Java JDK 11.
* Spring Data 2.3 stöds för närvarande, Spring Data 2.4 stöds inte för närvarande.

## <a name="faq"></a>Vanliga frågor

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Läs mer om [Spring Framework.](https://spring.io/projects/spring-framework)

Läs mer om [Spring Boot](https://spring.io/projects/spring-boot).

Läs mer om [Spring Data](https://spring.io/projects/spring-data).