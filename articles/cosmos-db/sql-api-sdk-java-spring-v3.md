---
title: Våren data Azure Cosmos DB v3 för SQL API-viktig information och resurser
description: Lär dig mer om våren-data Azure Cosmos DB v3 för SQL API, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/15/2021
ms.author: kuthapar
ms.custom: devx-track-java
ms.openlocfilehash: 536c0ab78a4b7e08abb1532635aff722df9e15cd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563114"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Våren data Azure Cosmos DB v3 for Core (SQL) API: viktig information och resurser
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

Med vår data Azure Cosmos DB version 3 för Core (SQL) kan utvecklare använda Azure Cosmos DB i vår program. Våren data Azure Cosmos DB visar data gränssnittet våren för att manipulera databaser och samlingar, arbeta med dokument och skicka frågor. Både synkronisering och asynkron (reaktiv) API: er stöds i samma maven-artefakt. 

> [!IMPORTANT]
> Våren data Azure Cosmos DB är beroende av våren data Framework.
> 
> Azure-våren-data-Cosmos-versioner från 3.0.0 till 3.4.0 stöder våren data version 2,2 och 2,3.
> 
> Azure-våren-data-Cosmos-versioner 3.5.0 och senare stöder våren-data versioner 2.4.3 och senare.
>

[Våren-ramverket](https://spring.io/projects/spring-framework) är en programmerings-och konfigurations modell som fören klar utveckling av Java-program. Våren effektiviserar "rör koppling" av program genom att använda beroende inmatning. Många utvecklare liknar våren eftersom det gör det enklare att skapa och testa program. [Våren Boot](https://spring.io/projects/spring-boot) utökar denna hantering av rör med ett öga mot webb program och utveckling av mikrotjänster. [Våren data](https://spring.io/projects/spring-data) är en programmerings modell och ett ramverk för att komma åt data lager som Azure Cosmos dB från kontexten för ett våren-eller våren Boot-program. 

Du kan använda vår data Azure Cosmos DB i dina [Azure våren Cloud](https://azure.microsoft.com/services/spring-cloud/) -program.

> [!IMPORTANT]  
> Den här viktig information gäller för version 3 av vår data Azure Cosmos DB. [Viktig information om version 2 finns här](sql-api-sdk-java-spring-v2.md). 
>
> Våren data Azure Cosmos DB stöder bara SQL-API: et.
>
> I de här artiklarna finns information om våren-data på andra Azure Cosmos DB API: er:
> * [Våren-data för Apache Cassandra med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Fjädra data MongoDB med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Fjädra data Gremlin med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="get-started-fast"></a>Kom igång snabbt

  Kom igång med vår data Azure Cosmos DB genom att följa vår guide för att [Starta](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)starter. Metoden våren Boot Starter är det rekommenderade sättet att komma igång med hjälp av våren data Azure Cosmos DB Connector.

  Du kan också lägga till våren data Azure Cosmos DB beroende av `pom.xml` filen enligt nedan:

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
|**API-dokumentation** | [Referens dokumentation för Java API](/java/api/com.azure.spring.data.cosmos) |
|**Bidra till SDK** | [Azure SDK för Java Central lagrings platsen på GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**Komma igång** | [Snabb start: Bygg en våren data Azure Cosmos DB-app för att hantera Azure Cosmos DB SQL API-data](./create-sql-api-spring-data.md) <br> [GitHub lagrings platsen med snabb starts kod](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**Grundläggande kod exempel** | [Azure Cosmos DB: våren data Azure Cosmos DB exempel för SQL API](sql-api-spring-data-sdk-samples.md) <br> [GitHub lagrings platsen med exempel kod](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **Prestandatips**| [Prestanda tips för Java SDK v4 (gäller för våren-data)](performance-tips-java-sdk-v4-sql.md)| 
| **Felsökning** | [Felsöka Java SDK v4 (gäller för våren data)](troubleshoot-java-sdk-v4-sql.md) | 
| **Azure Cosmos DB workshops och labb** |[Start sida för Cosmos DB workshops](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>Ytterligare information

* Våren data Azure Cosmos DB stöder Java JDK 8 och Java JDK 11.
* Våren data 2,3 stöds för närvarande, våren data 2,4 stöds inte för närvarande.

## <a name="faq"></a>Vanliga frågor

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Lär dig mer om [våren-ramverket](https://spring.io/projects/spring-framework).

Läs mer om [våren Boot](https://spring.io/projects/spring-boot).

Lär dig mer om [fjädrande data](https://spring.io/projects/spring-data).