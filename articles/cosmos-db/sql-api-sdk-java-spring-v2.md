---
title: Versionsanteckningar och resurser för Spring Data Azure Cosmos DB v2 för SQL API
description: Lär dig mer om Spring Data Azure Cosmos DB v2 för SQL API, inklusive lanseringsdatum, tillbakagångar och ändringar som görs mellan varje version av Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 4cd92efb7b6596288e4b6ef8e81f82f775a24e01
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363452"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Spring Data Azure Cosmos DB v2 for Core (SQL) API: Viktig information och resurser
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

 Med Spring Data Azure Cosmos DB version 2 för Core (SQL) kan utvecklare använda Azure Cosmos DB i Spring-program. Spring Data Azure Cosmos DB exponerar Spring Data-gränssnittet för att manipulera databaser och samlingar, arbeta med dokument och utfärda frågor. Både SYNC- och Async(Reactive) API:er stöds i samma Maven-artefakt. 

[Spring Framework är](https://spring.io/projects/spring-framework) en programmerings- och konfigurationsmodell som effektiviserar Java-programutveckling. Spring effektiviserar programmens "steg för steg" med hjälp av beroendeinjektion. Många utvecklare gillar Spring eftersom det gör det enklare att skapa och testa program. [Spring Boot](https://spring.io/projects/spring-boot) den här hanteringen av rör sig med ett öga mot webbprogram- och mikrotjänstutveckling. [Spring Data](https://spring.io/projects/spring-data) är en programmeringsmodell för åtkomst till datalager som Azure Cosmos DB från kontexten för ett Spring- eller Spring Boot-program. 

Du kan använda Spring Data-Azure Cosmos DB i [dina Azure Spring Cloud program.](https://azure.microsoft.com/services/spring-cloud/)

> [!IMPORTANT]  
> Dessa versionsanteckningar gäller för version 2 av Spring Data Azure Cosmos DB. Du hittar [versionsanteckningar för version 3 här.](sql-api-sdk-java-spring-v3.md) 
>
> Spring Data Azure Cosmos DB stöder endast SQL-API:et.
>
> I följande artiklar finns information om Spring Data på andra Azure Cosmos DB API:er:
> * [Spring Data för Apache Cassandra med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Vill du komma igång snabbt?
> 1. Installera den [minsta Java-körningstid som stöds, JDK 8,](/java/azure/jdk/)så att du kan använda SDK:n.
> 2. Skapa en Spring Data Azure Cosmos DB app med hjälp av [startprogrammet](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db). Det är enkelt!
> 3. Gå igenom [Spring Data Azure Cosmos DB utvecklarhandbok, som](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb)går igenom grundläggande Azure Cosmos DB begäranden.
>
> Du kan snabbt skapa Spring Boot Starter-appar med hjälp av [Spring Initializr!](https://start.spring.io/)
>

## <a name="resources"></a>Resurser

| Resurs | Länk |
|---|---|
| **SDK-nedladdning** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API-dokumentation** | [Referensdokumentation Azure Cosmos DB Spring Data]() |
|**Bidra till SDK** | [Spring Data Azure Cosmos DB-lagringsplatsen på GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Spring Boot Starter**| [Azure Cosmos DB Spring Boot Starter-klientbibliotek för Java](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Spring TODO-appexempel med Azure Cosmos DB**| [Java-upplevelse från App Service (del 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Utvecklarguide** | [Utvecklarguide för Spring Data Azure Cosmos DB](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Använda Starter** | [Så här använder du Spring Boot Starter med sql Azure Cosmos DB-API:et](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [GitHub-lagringsplatsen för Azure Cosmos DB Spring Boot Starter](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Exempel med Azure App Service** | [Så här använder du Spring och Cosmos DB med App Service på Linux](/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [TODO-appexempel](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Lanseringshistorik

### <a name="230-may-21-2020"></a>2.3.0 (21 maj 2020)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterar Spring Boot version till 2.3.0.


### <a name="225-may-19-2020"></a>2.2.5 (19 maj 2020)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterar Azure Cosmos DB version till 3.7.3.
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* Innehåller korrigeringar av minnesläckor och Netty-versionsuppgraderingar från Azure Cosmos DB SDK 3.7.3.

### <a name="224-april-6-2020"></a>2.2.4 (6 april 2020)
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* Åtgärdar `allowTelemetry` flaggan för att ta hänsyn till från `CosmosDbConfig` .
* Åtgärdar `TTL` egenskapen för containern.

### <a name="223-february-25-2020"></a>2.2.3 (25 februari 2020)
#### <a name="new-features"></a>Nya funktioner
* Lägger till ny `findAll` av API för partitionsnyckel.
* Uppdaterar Azure Cosmos DB version till 3.7.0.
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* Åtgärdar `collectionName`  ->  `containerName` .
* Korrigeringar `entityClass` och `domainClass`  ->  `domainType` .
* Åtgärdar "Return entity collection saved by repository instead of input entity" (Returnera entitetssamling som sparats av lagringsplatsen i stället för en indataentitet).

### <a name="2110-february-25-2020"></a>2.1.10 (25 februari 2020)
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* Korrigering av bakåtportar för "Return entity collection saved by repository instead of input entity" (Returnera entitetssamling som sparats av lagringsplatsen i stället för entitet för indata).

### <a name="222-january-15-2020"></a>2.2.2 (15 januari 2020)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterar Azure Cosmos DB version till 3.6.0.
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar

### <a name="221-december-31-2019"></a>2.2.1 (31 december 2019)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterar Azure Cosmos DB SDK-version till 3.5.0.
* Lägger till anteckningsfält för att aktivera eller inaktivera automatisk generering av samlingar.
* Förbättrar undantagshanteringen. Exponerar `CosmosClientException` via `CosmosDBAccessException` .
* Exponerar `requestCharge` och `activityId` via `ResponseDiagnostics` .
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* SDK 3.5.0 uppdaterar korrigeringar "Undantag när Cosmos DB HTTP-svarshuvudet är större än 8 192 byte", "ConsistencyPolicy.defaultConsistencyLevel() misslyckas vid gränsad föråldelse och konsekvent prefix."
* Åtgärdar `findById` metodens beteende. Tidigare returnerade den här metoden tom om entiteten inte hittades i stället för att utlösa ett undantag.
* Åtgärdar ett fel där sortering inte tillämpades på nästa sida när `CosmosPageRequest` användes.

### <a name="219-december-26-2019"></a>2.1.9 (26 december 2019)
#### <a name="new-features"></a>Nya funktioner
* Lägger till anteckningsfält för att aktivera eller inaktivera automatiskt skapande av samlingar.
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
*  Åtgärdar `findById` metodens beteende. Tidigare returnerade den här metoden tom om entiteten inte hittades i stället för att utlösa ett undantag.

### <a name="220-october-21-2019"></a>2.2.0 (21 oktober 2019)
#### <a name="new-features"></a>Nya funktioner
* Slutför stödet för Reactive Cosmos-lagringsplatsen.
* Azure Cosmos DB stöd för diagnostiksträngar och frågemått.
* Azure Cosmos DB SDK-version till 3.3.1.
* Uppgradering av Spring Framework-version till 5.2.0.RELEASE.
* Spring Data Commons-versionen uppgraderas till 2.2.0.RELEASE.
* Lägger `findByIdAndPartitionKey` till och `deleteByIdAndPartitionKey` API:er.
* Tar bort beroendet från azure-documentdb.
* Gör om DocumentDB till Azure Cosmos DB.
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* Åtgärdar "Sortering kastar undantag när pageSize är mindre än det totala antalet objekt på lagringsplatsen".

### <a name="218-october-18-2019"></a>2.1.8 (18 oktober 2019)
#### <a name="new-features"></a>Nya funktioner
* Tar bort DocumentDB-API:er.
* Lägger `findByIdAndPartitionKey` till och `deleteByIdAndPartitionKey` API:er.
* Lägger till optimistisk låsning baserat på `_etag` .
* Aktiverar SpEL-uttryck för dokumentsamlingens namn.
* Lägger `ObjectMapper` till förbättringar.

### <a name="217-october-18-2019"></a>2.1.7 (18 oktober 2019)
#### <a name="new-features"></a>Nya funktioner
* Lägger Azure Cosmos DB SDK version 3 beroende.
* Lägger till reaktiv Cosmos-lagringsplats.
* Uppdaterar implementeringen `DocumentDbTemplate` av för att Azure Cosmos DB SDK version 3.
* Lägger till andra konfigurationsändringar för stöd för Reactive Cosmos Repository.

### <a name="212-march-19-2019"></a>2.1.2 (19 mars 2019)
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* Tar bort `applicationInsights` beroendet för:
    * Potentiell risk för beroenden som beror på detta.
    * Java 11-inkompatibilitet.
    * Undvik potentiell prestandapåverkan på CPU och/eller minne.

### <a name="207-march-20-2019"></a>2.0.7 (20 mars 2019)
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* Bakåtporten tar bort `applicationInsights` beroendet för:
    * Potentiell risk för beroenden som beror på detta.
    * Java 11-inkompatibilitet.
    * Undvik potentiell prestandapåverkan på CPU och/eller minne.

### <a name="211-march-7-2019"></a>2.1.1 (7 mars 2019)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterar huvudversionen till 2.1.1.

### <a name="206-march-7-2019"></a>2.0.6 (7 mars 2019)
#### <a name="new-features"></a>Nya funktioner
* Ignorera alla undantag från telemetri.

### <a name="210-december-17-2018"></a>2.1.0 (17 december 2018)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterar version till 2.1.0 för att åtgärda problemet.

### <a name="205-september-13-2018"></a>2.0.5 (13 september 2018)
#### <a name="new-features"></a>Nya funktioner
* Lägger till nyckelord `exists` och `startsWith` .
* Uppdaterar Viktigt.
#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* Åtgärdar "Det går inte att anropa self href direkt för Entity".
* Åtgärdar "findAll misslyckas om ingen samling skapas".

### <a name="204-prerelease-august-23-2018"></a>2.0.4 (förhandsversion) (23 augusti 2018)
#### <a name="new-features"></a>Nya funktioner
* Byter namn på paketet från documentdb till cosmosdb.
* Lägger till ny funktion i nyckelordet för frågemetod. 16 nyckelord från SQL API stöds nu.
* Lägger till en ny funktion i frågan med växling och sortering.
* Förenklar konfigurationen av spring-data-cosmosdb.
* Lägger `deleteCollection` till och `deleteAll` API:er.

#### <a name="key-bug-fixes"></a>Viktiga felkorrigeringar
* Felkorrigering och minskning av defekter.

## <a name="faq"></a>Vanliga frågor
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nästa steg
Läs mer om [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Läs mer om [Spring Framework.](https://spring.io/projects/spring-framework)

Läs mer om [Spring Boot](https://spring.io/projects/spring-boot).

Läs mer om [Spring Data](https://spring.io/projects/spring-data).