---
title: Viktig information och resurser för vår data Azure Cosmos DB v2 för SQL API
description: Lär dig allt om våren data Azure Cosmos DB v2 för SQL API, inklusive versions datum, datum för indata och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 47d9a8ff884e29dc5692c97d5e7867a856d01063
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590744"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Våren data Azure Cosmos DB v2 för Core (SQL) API: viktig information och resurser
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

Med vår data Azure Cosmos DB v2 för Core (SQL) kan utvecklare använda Azure Cosmos DB i våren-program. Våren data Azure Cosmos DB visar data gränssnittet våren för att manipulera databaser och samlingar, arbeta med dokument och skicka frågor. Både synkronisering och asynkron (reaktiv) API: er stöds i samma maven-artefakt. 

[Våren-ramverket](https://spring.io/projects/spring-framework) är en programmerings-och konfigurations modell som effektiviserar utveckling av Java-program. För att citera organisationens webbplats effektiviserar våren "rör koppling" av program med hjälp av beroende inmatning. Många utvecklare gillar att skapa och testa program blir enklare. [Våren Boot](https://spring.io/projects/spring-boot) utökar den här idén med att hantera rör med ett öga mot webb program och utveckling av mikrotjänster. [Våren data](https://spring.io/projects/spring-data) är en programmerings modell för att få åtkomst till data lager som Azure Cosmos dB från ett våren-eller våren Boot-program. 

Du kan använda vår data Azure Cosmos DB i dina [Azure våren Cloud](https://azure.microsoft.com/services/spring-cloud/) -program.

> [!IMPORTANT]  
> De här viktiga anteckningarna är till för v2 av vår data Azure Cosmos DB. Du hittar v3-viktig information [här](sql-api-sdk-java-spring-v3.md). 
>
> Våren data Azure Cosmos DB endast stöder SQL API.
>
> Följande guider stöder våren-data på andra Azure Cosmos DB API: er:
> * [Våren-data för Apache Cassandra med Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Fjädra data MongoDB med Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Fjädra data Gremlin med Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Vill du komma igång snabbt?
> 1. Installera den [lägsta Java-körningen som stöds, JDK 8,](/java/azure/jdk/?view=azure-java-stable) så att du kan använda SDK: n.
> 2. Skapa en våren data Azure Cosmos DB-app med Start programmet – [det är enkelt](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)!
> 3. Arbeta med hjälp av [vår informations Azure Cosmos DB utvecklare](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) som går igenom grundläggande Azure Cosmos DB förfrågningar.
>
> Du kan sätta upp våren Boot starter-appar snabbt med [våren Initializr](https://start.spring.io/)!
>

## <a name="helpful-content"></a>Användbart innehåll

| Innehåll | Länk |
|---|---|
| **SDK-hämtning** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API-dokumentation** | [Dokumentation om vår data Azure Cosmos DB referens]() |
|**Bidra till SDK** | [Våren data Azure Cosmos DB lagrings platsen på GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Start starter för våren**| [Azure Cosmos DB fjäder start starter klient bibliotek för Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Exempel på våren att göra-appen med Azure Cosmos DB**| [Java-upplevelse från slut punkt till slut punkt i App Service Linux (del 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Guide för utvecklare** | [Hand bok för våren data Azure Cosmos DB-utvecklare](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Guide för att använda Start programmet** | [Så här använder du Spring Boot Starter med Azure Cosmos DB SQL API](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [GitHub-lagrings platsen för Azure våren Boot starter Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Exempel med App Services** | [Så här använder du Spring och Cosmos DB med App Service på Linux](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Exempel på att göra-appen](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Versions historik

### <a name="230-2020-05-21"></a>2.3.0 (2020-05-21)
#### <a name="new-features"></a>Nya funktioner
* Uppdatera vår start version till 2.3.0 
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar

### <a name="225-2020-05-19"></a>2.2.5 (2020-05-19)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterad Azure Cosmos DB-version till v-3.7.3
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Innehåller korrigeringar av minnes läckor och nett versions uppgraderingar från Cosmos SDK v 3.7.3 

### <a name="224-2020-04-06"></a>2.2.4 (2020-04-06)
#### <a name="new-features"></a>Nya funktioner
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Flagga för fast allowTelemetry som tar hänsyn till från CosmosDbConfig
* Fast TTL-egenskap för behållare

### <a name="223-2020-02-25"></a>2.2.3 (2020-02-25)
#### <a name="new-features"></a>Nya funktioner
* Nya findAll har lagts till efter partitionsnyckel-API
* Uppdaterad Azure-Cosmos-version till 3.7.0
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Fast samlings-> containerName
* Fast entityClass & domainClass-> domainType
* Fast "returnerad enhets samling som sparats av lagrings platsen i stället för indataenheten"

### <a name="2110-2020-02-25"></a>2.1.10 (2020-02-25)
#### <a name="new-features"></a>Nya funktioner
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Korrigerad korrigering för "returnera entitets samling Sparad av lagrings plats i stället för entiteten entitet"

### <a name="222-2020-01-15"></a>2.2.2 (2020-01-15)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterad Azure-Cosmos-version till v 3.6.0
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar

### <a name="221-2019-12-31"></a>2.2.1 (2019-12-31)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterad Cosmos DB SDK-version till 3.5.0
* Antecknings fältet har lagts till för att aktivera/inaktivera automatisk skapande av samling
* Bättre undantags hantering, exponerade CosmosClientException via CosmosDBAccessException
* Exponerade requestCharge och activityId via ResponseDiagnostics
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* SDK-3.5.0 uppdaterings korrigeringar "undantag vid Cosmos DB HTTP-svarshuvuden är större än 8192 byte", "ConsistencyPolicy. defaultConsistencyLevel () Miss lyckas med det begränsade inaktuellheten och konsekvent prefix"
* Fast findById API-beteende, retur tom påträffas inte, i stället för undantag
* Ett fel har åtgärd ATS där sorteringen inte tillämpades på nästa sida när CosmosPageRequest användes

### <a name="219-2019-12-26"></a>2.1.9 (2019-12-26)
#### <a name="new-features"></a>Nya funktioner
* Antecknings fältet har lagts till för att aktivera/inaktivera automatisk skapande av samling
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Fast findById API-beteende, retur tom påträffas inte, i stället för undantag

### <a name="220-2019-10-21"></a>2.2.0 (2019-10-21)
#### <a name="new-features"></a>Nya funktioner
* Fullständigt stöd för reactive Cosmos-lagringsplats
* Stöd för Cosmos DB begär diagnostik
* Cosmos DB SDK-version uppdatering till 3.3.1
* Uppgradering av våren Framework-version till 5.2.0. RELEASE
* Versions uppgradering av våren data till 2.2.0. RELEASE
* FindByIdAndPartitionKey, deleteByIdAndPartitionKey-API: er har lagts till
* Beroende av Azure-doumentdb har tagits bort
* Ommärkesering av DocumentDb till Cosmos
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Fast "sortering Throwing-undantag när pageSize är mindre än det totala antalet objekt i databasen"

### <a name="218-2019-10-18"></a>2.1.8 (2019-10-18)
#### <a name="new-features"></a>Nya funktioner
* Föråldrade API: er för dokument databaser
* FindByIdAndPartitionKey, deleteByIdAndPartitionKey-API: er har lagts till.
* Optimistisk låsning har lagts till baserat på _etag
* Aktiverat SPeL-uttryck för dokument samlings namn
* ObjectMapper förbättringar.
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar

### <a name="217-2019-10-18"></a>2.1.7 (2019-10-18)
#### <a name="new-features"></a>Nya funktioner
* Cosmos SDK v3-beroende har lagts till
* En reaktiv Cosmos-lagringsplats har lagts till
* Uppdaterad implementering av DocumentDbTemplate för att använda Cosmos SDK v3.
* Andra konfigurations ändringar för reactive Cosmos-lagringsplatsens stöd.
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar

### <a name="212-2019-03-19"></a>2.1.2 (2019-03-19)
#### <a name="new-features"></a>Nya funktioner
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Ta bort applicationInsights-beroende för
    * Potentiell risk för föroreningar som är beroende av beroenden
    * Inkompatibilitet med Java 11
    * Undvika potentiell prestanda påverkan på CPU och/eller minne.

### <a name="207-2019-03-20"></a>2.0.7 (2019-03-20)
#### <a name="new-features"></a>Nya funktioner
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Backport tar bort applicationInsights beroendet för
    * Potentiell risk för föroreningar som är beroende av beroenden
    * Inkompatibilitet med Java 11
    * Undvika potentiell prestanda påverkan på CPU och/eller minne.

### <a name="211-2019-03-07"></a>2.1.1 (2019-03-07)
#### <a name="new-features"></a>Nya funktioner
* Uppdatera huvud version till 2.1.1
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar

### <a name="206-2019-03-07"></a>2.0.6 (2019-03-07)
#### <a name="new-features"></a>Nya funktioner
* Ignorera alla undantag från telemetri
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar

### <a name="210-2018-12-17"></a>2.1.0 (2018-12-17)
#### <a name="new-features"></a>Nya funktioner
* Uppdatera version till 2.1.0 för att åtgärda problem
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar

### <a name="205-2018-09-13"></a>2.0.5 (2018-09-13)
#### <a name="new-features"></a>Nya funktioner
* Lägg till nyckelord finns, startsWith
* Uppdatera viktigt
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Åtgärda "Fjärranropet till själv href" för entiteten "
* Åtgärda "findAll Miss fungerar om samlingen inte skapas"

### <a name="204-pre-release-2018-08-23"></a>2.0.4 (för hands version) (2018-08-23)
#### <a name="new-features"></a>Nya funktioner
* Byter namn på paketet från DocumentDB till cosmosdb,
* Lägg till ny funktion i nyckelordet frågeterm, 16 nyckelord från SQL-API som stöds.
* Lägg till en ny funktion i fråga med sid indelning och sortering.
* Förenkla konfigurationen av våren-data-cosmosdb.
* Lägg till API för deleteCollection och deleteAll.

#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Fel korrigering och fel förbättring.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nästa steg
Mer information om Cosmos DB finns i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan.

Om du vill veta mer om våren-ramverket går du till [projektets start sida](https://spring.io/projects/spring-framework).

Mer information om våren boot finns på [projektets start sida](https://spring.io/projects/spring-boot).

Mer information om våren-data finns på [Start sidan för projektet](https://spring.io/projects/spring-data).