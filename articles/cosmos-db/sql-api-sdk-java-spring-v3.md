---
title: Våren data Azure Cosmos DB v3 för SQL API-viktig information och resurser
description: Lär dig allt om våren-data Azure Cosmos DB v3 för SQL API, inklusive versions datum, datum för indata och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 04a50d37606754ff4540d1056e378d46388e2592
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590725"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Våren data Azure Cosmos DB v3 for Core (SQL) API: viktig information och resurser
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

Med våren data Azure Cosmos DB v3 for Core (SQL) kan utvecklare använda Azure Cosmos DB i våren-program. Våren data Azure Cosmos DB visar data gränssnittet våren för att manipulera databaser och samlingar, arbeta med dokument och skicka frågor. Både synkronisering och asynkron (reaktiv) API: er stöds i samma maven-artefakt. 

Våren data Azure Cosmos DB tar ett beroende på våren data Framework. Azure Cosmos DB SDK-team släpper maven-artefakter för våren data v 2.2 och v 2.3.

[Våren-ramverket](https://spring.io/projects/spring-framework) är en programmerings-och konfigurations modell som effektiviserar utveckling av Java-program. För att citera organisationens webbplats effektiviserar våren "rör koppling" av program med hjälp av beroende inmatning. Många utvecklare gillar att skapa och testa program blir enklare. [Våren Boot](https://spring.io/projects/spring-boot) utökar den här idén med att hantera rör med ett öga mot webb program och utveckling av mikrotjänster. [Våren data](https://spring.io/projects/spring-data) är en programmerings modell och ett ramverk för åtkomst till data lager, till exempel Azure Cosmos dB från ett våren-eller våren Boot-program. 

Du kan använda vår data Azure Cosmos DB i dina [Azure våren Cloud](https://azure.microsoft.com/services/spring-cloud/) -program.

> [!IMPORTANT]  
> Den här viktiga informationen gäller för v3 av vår data Azure Cosmos DB. Du hittar versions anmärkningar för v2 [här](sql-api-sdk-java-spring-v2.md). 
>
> Våren data Azure Cosmos DB endast stöder SQL API.
>
> Följande guider stöder våren-data på andra Azure Cosmos DB API: er:
> * [Våren-data för Apache Cassandra med Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Fjädra data MongoDB med Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Fjädra data Gremlin med Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Börja här

# <a name="explore"></a>[Utforska](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

### <a name="navigate-the-tabs-above-for-basic-spring-data-azure-cosmos-db-samples"></a>Navigera till flikarna ovan för grundläggande våren-data Azure Cosmos DB exempel.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Konfigurera beroenden

Två våren data Azure Cosmos DB v3 maven-artefakter är tillgängliga.

Artefakt som är beroende av våren data Framework v 2.2:
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-2-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

Artefakt som är beroende av våren data Framework v 2.3:
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-3-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

# <a name="connect"></a>[Anslut](#tab/connect)

### <a name="connect"></a>Anslut

Ange Azure Cosmos DB konto-och behållar information. Våren data Azure Cosmos DB skapar automatiskt klienten och ansluter till behållaren.

[Application. Properties](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-2-cosmos-java-getting-started/src/main/resources/application.properties):
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[Dok OPS](#tab/docs)

### <a name="document-operations"></a>Dokument åtgärder

[Skapa](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]

[Ta bort](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

# <a name="query"></a>[Query](#tab/queries)

### <a name="query"></a>Söka i data

[Fråga](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

---

## <a name="helpful-content"></a>Användbart innehåll

| Innehåll | Våren data Framework v 2.2 | Våren data Framework v 2.3 |
|---|---|
| **SDK-hämtning** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-2-cosmos) | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-3-cosmos) |
|**Bidra till SDK** | [Våren data Azure Cosmos DB lagrings platsen på GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-2-cosmos) | [Våren data Azure Cosmos DB lagrings platsen på GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-3-cosmos) | 
|**Självstudier**| [Själv studie kurs om vår data Azure Cosmos DB GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-2-cosmos-java-getting-started) | [Själv studie kurs om vår data Azure Cosmos DB GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-3-cosmos-java-getting-started) |

## <a name="release-history"></a>Versions historik

### <a name="300-beta1-2020-08-17"></a>3.0.0 – beta. 1 (2020-08-17)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterat grupp-ID till `com.azure` .
* Uppdaterade artefakt-ID till `azure-spring-data-2-3-cosmos` .
* Uppdaterat Azure-Cosmos SDK-beroendet till `4.3.2-beta.2` .
* Stöd för granskning av entiteter – automatisk hantering av createdBy-, createdDate-, lastModifiedBy-och lastModifiedDate-kommenterade fält.
* `@GeneratedValue` antecknings stöd för automatisk ID-generering för ID-fält av `String` typen.
* Stöd för konfiguration av flera databaser för ett enskilt Cosmos-konto med flera databaser och flera Cosmos-konton med flera databaser.
* Stöd för `@Version` anteckning för alla sträng fält.
* Uppdaterade synkroniserings-API: er returnerade typer till `Iterable` typer i stället för `List` .
* Exponeras `CosmosClientBuilder` från Cosmos SDK som fjädrande bönor till `@Configuration` klass.
* Uppdaterat `CosmosConfig` med att innehålla frågesträngar och implementering av Response Diagnostics-processor.
* Stöd för att returnera `Optional` data typen för enskilda resultat frågor.
#### <a name="renames"></a>Byter namn på
* `CosmosDbFactory` till `CosmosFactory` .
* `CosmosDBConfig` till `CosmosConfig` .
* `CosmosDBAccessException` till `CosmosAccessException` .
* `Document` anteckning till `Container` anteckning.
* `DocumentIndexingPolicy` anteckning till `CosmosIndexingPolicy` anteckning.
* `DocumentQuery` till `CosmosQuery` .
* flaggan Application. Properties `populateQueryMetrics` till `queryMetricsEnabled` .
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Schemalägger loggning av diagnostikdata till `Parallel` trådar för att undvika att blockera I/O-trådar i Netnettning.
* Fast optimistisk låsning vid borttagnings åtgärd.
* Åtgärdat problem med undantags frågor för `IN` sats.
* Fast problem genom att tillåta `long` data typen för `@Id` .
* Fast problem genom att tillåta `boolean` , `long` , `int` , `double` som data typer för `@PartitionKey` anteckning.
* Fasta `IgnoreCase`  &  `AllIgnoreCase` nyckelord för ignorera Case-frågor.
* Tog bort standardvärdet för enhets enhet 4000 när behållare skapas automatiskt.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nästa steg
Mer information om Cosmos DB finns i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan.

Om du vill veta mer om våren-ramverket går du till [projektets start sida](https://spring.io/projects/spring-framework).

Mer information om våren boot finns på [projektets start sida](https://spring.io/projects/spring-boot).

Mer information om våren-data finns på [Start sidan för projektet](https://spring.io/projects/spring-data).