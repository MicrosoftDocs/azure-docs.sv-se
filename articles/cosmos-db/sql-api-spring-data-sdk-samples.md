---
title: 'Azure Cosmos DB SQL API: våren data v3-exempel'
description: Hitta våren data v3-exempel på GitHub för vanliga uppgifter som använder Azure Cosmos DB SQL API, inklusive CRUD-åtgärder.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: 378bb891c8539a6cf3d61f6511a0f58377d2bfd2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93091148"
---
# <a name="azure-cosmos-db-sql-api-spring-data-azure-cosmos-db-v3-examples"></a>Azure Cosmos DB SQL API: våren data Azure Cosmos DB v3-exempel
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET v2 SDK-exempel](sql-api-dotnet-samples.md)
> * [.NET v3 SDK-exempel](sql-api-dotnet-v3sdk-samples.md)
> * [Exempel för Java v4 SDK](sql-api-java-sdk-samples.md)
> * [Exempel på våren data v3 SDK](sql-api-spring-data-sdk-samples.md)
> * [Node.js-exempel](sql-api-nodejs-samples.md)
> * [Python-exempel](sql-api-python-samples.md)
> * [Azure-kodexempelgalleri](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

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

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- Du kan [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): din Visual Studio-prenumeration ger dig krediter varje månad som kan användas för Azure-betaltjänster.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

De senaste exempel programmen som utför CRUD-åtgärder och andra vanliga åtgärder på Azure Cosmos DB-resurser finns i [Azure-våren-data-Cosmos-Java-SQL-API-samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples) GitHub-lagringsplatsen. Den här artikeln innehåller:

* Länkar till aktiviteterna i vart och ett av exempel på våren-data Azure Cosmos DB-projektfiler. 
* Länkar till det relaterade API-referensinnehållet.

**Förutsättningar**

Du behöver följande för att köra det här exempelprogrammet:

* Java Development Kit 8
* Våren data Azure Cosmos DB v3

Du kan också använda Maven för att hämta de senaste våren-data Azure Cosmos DB v3-binärfilerna för användning i projektet. Maven lägger automatiskt till alla nödvändiga beroenden. Annars kan du hämta de beroenden som anges i **pom.xml** -filen direkt och lägga till dem i bygg sökvägen.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Köra exempelprogrammen**

Klona lagringsplatsexemplet:
```bash
$ git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples

$ cd azure-spring-data-cosmos-java-sql-api-samples
```

Du kan köra exemplen med antingen en IDE (Sol förmörkelse, IntelliJ eller VSCODE) eller från kommando raden med hjälp av Maven.

I **programmet. Properties** måste de här miljövariablerna anges

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

för att ge exempel Läs-och skriv åtkomst till ditt konto, databaser och behållare.

Din IDE kan ge möjlighet att köra exempel koden fjäder data. Annars kan du använda följande Terminal-kommando för att köra exemplet:

```bash
mvn spring-boot:run
```

## <a name="document-crud-examples"></a>Dokumentera CRUD-exempel
[Exempel](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) filen visar hur du utför följande uppgifter. Information om Azure Cosmos-dokument innan du kör följande exempel finns i avsnittet [arbeta med databaser, behållare och objekt](account-databases-containers-items.md) konceptuell artikel.

| Uppgift | API-referens |
| --- | --- |
| [Skapa ett dokument](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L46-L47) | CosmosRepository. Save |
| [Läsa ett dokument efter ID](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L56-L58) | CosmosRepository.derivedQueryMethod |
| [Ta bort alla dokument](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L39-L41) | CosmosRepository.deleteAll |

## <a name="derived-query-method-examples"></a>Exempel på härledda fråge metoder
[Exempel](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) filen visar hur du utför följande uppgifter. Om du vill veta mer om Azure Cosmos DB frågor innan du kör följande exempel kan det vara bra att läsa [Baeldung: s härledda fråge metoder i vår](https://www.baeldung.com/spring-data-derived-queries) artikel.

| [Fråga efter dokument](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L73-L77) | CosmosRepository. derivedQueryMethod |

## <a name="custom-query-examples"></a>Exempel på anpassade frågor
[Exempel](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) filen visar hur du utför följande uppgifter med hjälp av SQL Query-grammatik. Om du vill veta mer om SQL-frågan i Azure Cosmos DB innan du kör följande exempel kan du läsa [mer i SQL-fr åga for Azure Cosmos DB](./sql-query-getting-started.md). 


| Uppgift | API-referens |
| --- | --- |
| [Fråga för alla dokument](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L20-L22) | @Query antecknings |
| [Fråga för likhet med ==](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L24-L26) | @Query antecknings |
| [Fråga för olikhet med != och NOT](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L28-L38) | @Query antecknings |
| [Fråga med intervalloperatorer som >, <, >=, <=](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L40-L42) | @Query antecknings |
| [Fråga med intervalloperatorer mot strängar](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L44-L46) | @Query antecknings |
| [Fråga med ORDER BY](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L48-L50) | @Query antecknings |
| [Fråga med distinkt](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L52-L54) | @Query antecknings |
| [Fråga med mängd funktioner](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L56-L62) | @Query antecknings |
| [Arbeta med underdokument](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L64-L66) | @Query antecknings |
| [Fråga med intradokumentkopplingar](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L68-L85) | @Query antecknings |
| [Fråga med operatorer av sträng, matematik och matris](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L87-L97) | @Query antecknings |