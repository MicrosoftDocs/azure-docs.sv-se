---
title: Viktig information och resurser för vår data Azure Cosmos DB v2 för SQL API
description: Lär dig mer om våren data Azure Cosmos DB v2 för SQL API, inklusive versions datum, datum för indata och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 1f78d7185415b66445c78f3b4cdb54ed0d63cb0a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216623"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Våren data Azure Cosmos DB v2 för Core (SQL) API: viktig information och resurser
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

 Med vår data Azure Cosmos DB version 2 för Core (SQL) kan utvecklare använda Azure Cosmos DB i vår program. Våren data Azure Cosmos DB visar data gränssnittet våren för att manipulera databaser och samlingar, arbeta med dokument och skicka frågor. Både synkronisering och asynkron (reaktiv) API: er stöds i samma maven-artefakt. 

[Våren-ramverket](https://spring.io/projects/spring-framework) är en programmerings-och konfigurations modell som fören klar utveckling av Java-program. Våren effektiviserar "rör koppling" av program genom att använda beroende inmatning. Många utvecklare liknar våren eftersom det gör det enklare att skapa och testa program. [Våren Boot](https://spring.io/projects/spring-boot) utökar denna hantering av rör med ett öga mot webb program och utveckling av mikrotjänster. [Våren data](https://spring.io/projects/spring-data) är en programmerings modell för att få åtkomst till data lager som Azure Cosmos dB från kontexten för ett våren-eller våren Boot-program. 

Du kan använda vår data Azure Cosmos DB i dina [Azure våren Cloud](https://azure.microsoft.com/services/spring-cloud/) -program.

> [!IMPORTANT]  
> Den här viktig information gäller för version 2 av vår data Azure Cosmos DB. [Viktig information om version 3 finns här](sql-api-sdk-java-spring-v3.md). 
>
> Våren data Azure Cosmos DB stöder bara SQL-API: et.
>
> I följande artiklar finns information om våren-data på andra Azure Cosmos DB API: er:
> * [Våren-data för Apache Cassandra med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Fjädra data MongoDB med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Fjädra data Gremlin med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Vill du komma igång snabbt?
> 1. Installera den [lägsta stödda Java Runtime, JDK 8](/java/azure/jdk/), så att du kan använda SDK: n.
> 2. Skapa en våren data Azure Cosmos DB-appen genom att använda [Start](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)programmet. Det är enkelt!
> 3. Arbeta via [vår Data Azure Cosmos DB Developer ' s guide](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb), som guidar genom grundläggande Azure Cosmos DB förfrågningar.
>
> Du kan sätta upp våren Boot starter-appar snabbt genom att använda [våren Initializr](https://start.spring.io/)!
>

## <a name="resources"></a>Resurser

| Resurs | Länk |
|---|---|
| **SDK-nedladdning** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API-dokumentation** | [Dokumentation om vår data Azure Cosmos DB referens]() |
|**Bidra till SDK: n** | [Våren data Azure Cosmos DB lagrings platsen på GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Start starter för våren**| [Azure Cosmos DB fjäder start starter klient bibliotek för Java](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Exempel på våren att göra-appen med Azure Cosmos DB**| [Java-upplevelse från slut punkt till slut punkt i App Service Linux (del 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Guide för utvecklare** | [Utvecklarguide för Spring Data Azure Cosmos DB](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Använda Start programmet** | [Använda våren Boot starter med Azure Cosmos DB SQL API](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [GitHub lagrings platsen för Azure Cosmos DB fjäder start starter](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Exempel med Azure App Service** | [Så här använder du Spring och Cosmos DB med App Service på Linux](/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Exempel på att göra-appen](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Versions historik

### <a name="230-may-21-2020"></a>2.3.0 (21 maj 2020)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterar start versionen till 2.3.0.


### <a name="225-may-19-2020"></a>2.2.5 (19 maj 2020)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterar Azure Cosmos DB version till 3.7.3.
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Innehåller korrigeringar av minnes läckor och netversion-uppgraderingar från Azure Cosmos DB SDK-3.7.3.

### <a name="224-april-6-2020"></a>2.2.4 (6 april 2020)
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Korrigerar `allowTelemetry` flagga att ta med i konto från `CosmosDbConfig` .
* Korrigerar `TTL` egenskap i behållare.

### <a name="223-february-25-2020"></a>2.2.3 (25 februari 2020)
#### <a name="new-features"></a>Nya funktioner
* Lägger till ny `findAll` efter partitionsnyckel-API.
* Uppdaterar Azure Cosmos DB version till 3.7.0.
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Korrigeringar `collectionName`  ->  `containerName` .
* Korrigeringar `entityClass` och `domainClass`  ->  `domainType` .
* Korrigerar "returnera entitets samling som sparats av en lagrings plats i stället för indataenheten."

### <a name="2110-february-25-2020"></a>2.1.10 (25 februari 2020)
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Backports-korrigering för "returnera Entity-samling som sparats av lagrings platsen i stället för entiteten."

### <a name="222-january-15-2020"></a>2.2.2 (15 januari 2020)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterar Azure Cosmos DB version till 3.6.0.
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar

### <a name="221-december-31-2019"></a>2.2.1 (31 december 2019)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterar Azure Cosmos DB SDK-version till 3.5.0.
* Lägger till antecknings fält för att aktivera eller inaktivera automatisk samlings skapande.
* Förbättrar undantags hanteringen. Exponeras `CosmosClientException` `CosmosDBAccessException` .
* Exponeras `requestCharge` och `activityId` via `ResponseDiagnostics` .
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* SDK-3.5.0 uppdaterings korrigeringar "undantag när Cosmos DB HTTP-svarshuvuden är större än 8192 byte," "ConsistencyPolicy. defaultConsistencyLevel () Miss lyckas med det begränsade inaktuellt och konsekvent prefix."
* Korrigerar `findById` metodens beteende. Tidigare returnerades den här metoden tom om entiteten inte hittades i stället för ett undantag.
* Åtgärdar ett fel där sorteringen inte tillämpades på nästa sida när användes `CosmosPageRequest` .

### <a name="219-december-26-2019"></a>2.1.9 (26 december 2019)
#### <a name="new-features"></a>Nya funktioner
* Lägger till antecknings fält för att aktivera eller inaktivera automatisk samlings skapande.
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
*  Korrigerar `findById` metodens beteende. Tidigare returnerades den här metoden tom om entiteten inte hittades i stället för ett undantag.

### <a name="220-october-21-2019"></a>2.2.0 (21 oktober 2019)
#### <a name="new-features"></a>Nya funktioner
* Fullständigt stöd för reactive Cosmos-lagringsplats.
* Azure Cosmos DB förfrågnings diagnos sträng och stöd för fråge mått.
* Azure Cosmos DB SDK-version uppdatering till 3.3.1.
* Uppgradering av våren Framework-version till 5.2.0. RELEASE.
* Versions uppgradering av våren data till 2.2.0. RELEASE.
* Tillägg `findByIdAndPartitionKey` och `deleteByIdAndPartitionKey` API: er.
* Tar bort beroende från Azure-DocumentDB.
* Anpassar DocumentDB till Azure Cosmos DB.
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Åtgärds fel vid sortering av undantag när pageSize är mindre än det totala antalet objekt i databasen. "

### <a name="218-october-18-2019"></a>2.1.8 (18 oktober 2019)
#### <a name="new-features"></a>Nya funktioner
* Föråldrade DocumentDB-API: er.
* Tillägg `findByIdAndPartitionKey` och `deleteByIdAndPartitionKey` API: er.
* Lägger till optimistisk låsning baserat på `_etag` .
* Aktiverar SpEL-uttryck för dokument samlings namn.
* Lägger till `ObjectMapper` förbättringar.

### <a name="217-october-18-2019"></a>2.1.7 (18 oktober 2019)
#### <a name="new-features"></a>Nya funktioner
* Lägger till Azure Cosmos DB SDK version 3-beroende.
* Lägger till reaktiv Cosmos-lagringsplats.
* Uppdaterar implementeringen av `DocumentDbTemplate` för att använda Azure Cosmos DB SDK version 3.
* Lägger till andra konfigurations ändringar för reactive Cosmos-lagringsplatsens stöd.

### <a name="212-march-19-2019"></a>2.1.2 (19 mars 2019)
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Tar bort `applicationInsights` beroende för:
    * Potentiell risk för föroreningar som är beroende av beroenden.
    * Java 11 inkompatibilitet.
    * Undvika potentiell prestanda påverkan på CPU och/eller minne.

### <a name="207-march-20-2019"></a>2.0.7 (20 mars 2019)
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Backport tar bort `applicationInsights` beroende för:
    * Potentiell risk för föroreningar som är beroende av beroenden.
    * Java 11 inkompatibilitet.
    * Undvika potentiell prestanda påverkan på CPU och/eller minne.

### <a name="211-march-7-2019"></a>2.1.1 (7 mars 2019)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterar huvud versionen till 2.1.1.

### <a name="206-march-7-2019"></a>2.0.6 (7 mars 2019)
#### <a name="new-features"></a>Nya funktioner
* Ignorera alla undantag från telemetri.

### <a name="210-december-17-2018"></a>2.1.0 (17 december 2018)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterar versionen till 2.1.0 för att åtgärda problemet.

### <a name="205-september-13-2018"></a>2.0.5 (13 september 2018)
#### <a name="new-features"></a>Nya funktioner
* Lägger till nyckelord `exists` och `startsWith` .
* Uppdaterar viktigt.
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Korrigeringar "det går inte att anropa Self href direkt för entiteten."
* Rättningar "findAll kommer att Miss pare ras om samlingen inte skapas."

### <a name="204-prerelease-august-23-2018"></a>2.0.4 (för hands version) (23 augusti 2018)
#### <a name="new-features"></a>Nya funktioner
* Byter namn på paketet från DocumentDB till cosmosdb.
* Lägger till ny funktion i nyckelordet frågeterm. 16 nyckelord från SQL API stöds nu.
* Lägger till en ny funktion i frågan med sid indelning och sortering.
* Fören klar konfigurationen av våren-data-cosmosdb.
* Tillägg `deleteCollection` och `deleteAll` API: er.

#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Fel korrigering och defekt minskning.

## <a name="faq"></a>Vanliga frågor
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nästa steg
Läs mer om [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Lär dig mer om [våren-ramverket](https://spring.io/projects/spring-framework).

Läs mer om [våren Boot](https://spring.io/projects/spring-boot).

Lär dig mer om [fjädrande data](https://spring.io/projects/spring-data).