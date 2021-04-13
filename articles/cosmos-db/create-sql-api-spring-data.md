---
title: Snabbstart – Använda Spring Data Azure Cosmos DB v3 för att skapa en dokumentdatabas med Azure Cosmos DB
description: Den här snabbstarten presenterar ett Spring Data Azure Cosmos DB v3-kodexempel som du kan använda för att ansluta till och fråga Azure Cosmos DB SQL API
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 03/07/2021
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: a094ebb6b218027c195985312b023ccb8838f703
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365694"
---
# <a name="quickstart-build-a-spring-data-azure-cosmos-db-v3-app-to-manage-azure-cosmos-db-sql-api-data"></a>Snabbstart: Skapa en Spring Data Azure Cosmos DB v3-app för att hantera Azure Cosmos DB SQL API-data
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring-data v3](create-sql-api-spring-data.md)
> * [Spark v3-anslutningsapp](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

I den här snabbstarten skapar och hanterar du ett Azure Cosmos DB SQL API-konto från Azure Portal och med hjälp av en Spring Data Azure Cosmos DB v3-app som klonas från GitHub. Först skapar du ett Azure Cosmos DB SQL API-konto med hjälp av Azure Portal, skapar sedan en Spring Boot-app med Spring Data Azure Cosmos DB v3-anslutningsappen och lägger sedan till resurser till ditt Cosmos DB-konto med hjälp av Spring Boot-programmet. Azure Cosmos DB är en databastjänst med flera modeller som gör att du snabbt kan skapa och fråga databaser för dokument, tabeller, nyckelvärden och grafer med global distribution och horisontell skalning.

> [!IMPORTANT]  
> Den här versionsinformationen gäller för version 3 av Spring Data Azure Cosmos DB. Du hittar [versionsanteckningar för version 2 här.](sql-api-sdk-java-spring-v2.md) 
>
> Spring Data Azure Cosmos DB stöder endast SQL-API:et.
>
> I de här artiklarna finns information om Spring Data på Azure Cosmos DB API:er:
> * [Spring Data för Apache Cassandra med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Eller [prova Azure Cosmos DB utan en](https://azure.microsoft.com/try/cosmosdb/) Azure-prenumeration. Du kan också använda [Azure Cosmos DB emulatorn](https://aka.ms/cosmosdb-emulator) med en URI `https://localhost:8081` för och nyckeln `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Peka `JAVA_HOME` miljövariabeln till den mapp där JDK är installerad.
- Ett [Maven-binärt arkiv](https://maven.apache.org/download.cgi). I Ubuntu kör du för `apt-get install maven` att installera Maven.
- [Git](https://www.git-scm.com/downloads). I Ubuntu kör du för `sudo apt-get install git` att installera Git.

## <a name="introductory-notes"></a>Introduktionsanteckningar

*Strukturen för ett Cosmos DB konto.* Oavsett API eller programmeringsspråk innehåller  ett Cosmos DB-konto noll eller flera databaser *,* en databas *(DB)* innehåller noll eller flera containrar *och* en *container* innehåller noll eller flera objekt, enligt diagrammet nedan:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos-kontoentiteter" border="false":::

Du kan läsa mer om databaser, containrar och objekt [här.](account-databases-containers-items.md) Några viktiga egenskaper definieras på containernivå, bland annat etablerat *dataflöde och* *partitionsnyckel*. 

Det etablerade dataflödet mäts i enheter för begäran *(RU:er)* som har ett penningpris och som är en viktig faktor för kontots driftskostnader. Etablerat dataflöde kan väljas med kornighet per container eller kornighet per databas, men specifikationen för dataflöde på containernivå är normalt att föredra. Du kan läsa mer om dataflödesetablering [här.](set-throughput.md)

När objekt infogas i en Cosmos DB databas växer databasen vågrätt genom att lägga till mer lagring och beräkning för att hantera begäranden. Lagrings- och beräkningskapaciteten läggs till i diskreta enheter som kallas partitioner och du måste välja ett fält i dokumenten som *partitionsnyckel* som mappar varje dokument till en partition. Sättet som partitioner hanteras på är att varje partition tilldelas en ungefär lika stor sektor utanför intervallet med partitionsnyckelvärden. Därför rekommenderar vi att du väljer en partitionsnyckel som är relativt slumpmässig eller jämnt distribuerad. I annat fall ser vissa partitioner betydligt fler begäranden *(heta partitioner)* medan andra partitioner ser betydligt färre begäranden ( kall *partition*), och detta ska undvikas. Du kan lära dig mer om partitionering [här.](partitioning-overview.md)

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Innan du kan börja skapa en dokumentdatabas måste du skapa ett SQL-API-konto med Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Lägga till en container

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Lägga till exempeldata

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi övergå till att arbeta med kod. Vi ska klona en SQL API-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

```bash
git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started.git
```

## <a name="review-the-code"></a>Granska koden

Det här är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Köra appen](#run-the-app). 

### <a name="application-configuration-file"></a>Programkonfigurationsfil

Här visar vi hur Spring Boot och Spring Data förbättrar användarupplevelsen – processen för att upprätta en Cosmos-klient och ansluta till Cosmos-resurser är nu konfiguration snarare än kod. Vid programstarten Spring Boot alla dessa exempel med hjälp av inställningarna i **application.properties**:

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

När du har skapat ett Azure Cosmos DB-konto, en databas och en container fyller du bara i tomma filer i konfigurationsfilen så gör Spring Boot/Spring Data automatiskt följande: (1) skapa en underliggande Java SDK-instans med URI:n och nyckeln och (2) ansluta till databasen och `CosmosClient` containern. Allt är klart – ingen **mer resurshanteringskod!**

### <a name="java-source"></a>Java-källa

Värde add-tillägget för Spring Data kommer också från dess enkla, rena, standardiserade och plattformsoberoende gränssnitt för drift i datalager. Baserat på Det Spring Data GitHub-exempel som är länkat ovan finns CRUD och frågeexempel för att manipulera Azure Cosmos DB dokument med Spring Data Azure Cosmos DB.

* Objektskapande och uppdateringar med hjälp av `save` metoden .

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]
   
* Punktläsningar med den härledda frågemetoden som definierats i lagringsplatsen. `findByIdAndLastName`utför punktläsningar för `UserRepository` . Fälten som anges i metodnamnet gör att Spring Data kör en punktläsning som definieras av `id` fälten `lastName` och :

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Read)]

* Objektet tas bort med hjälp av `deleteAll` :

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

* Härledd fråga baserat på lagringsplatsens metodnamn. Spring Data implementerar metoden `UserRepository` `findByFirstName` som en Java SDK SQL-fråga i fältet (den här frågan kunde inte `firstName` implementeras som en punktläsning):

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

## <a name="run-the-app"></a>Kör appen

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och starta appen med din slutpunktsinformation. På så vis kan appen kommunicera med den värdbaserade databasen.

1. I git-terminalfönstret `cd` till exempelkodsmappen.

    ```bash
    cd azure-spring-data-cosmos-java-sql-api-getting-started/azure-spring-data-cosmos-java-getting-started/
    ```

2. I git-terminalfönstret använder du följande kommando för att installera de Spring Data-Azure Cosmos DB paket.

    ```bash
    mvn clean package
    ```

3. I git-terminalfönstret använder du följande kommando för att starta Spring Data Azure Cosmos DB-programmet:

    ```bash
    mvn spring-boot:run
    ```
    
4. Appen läser in **application.properties** och ansluter resurserna i ditt Azure Cosmos DB konto.
5. Appen utför CRUD-åtgärder för punkt som beskrivs ovan.
6. Appen utför en härledd fråga.
7. Appen tar inte bort dina resurser. Växla tillbaka till portalen för [att rensa resurserna från](#clean-up-resources) ditt konto om du vill undvika kostnader.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos DB SQL API-konto, skapar en dokumentdatabas och container med hjälp av Datautforskaren och kör en Spring Data-app för att göra samma sak programmässigt. Du kan nu importera ytterligare data till ditt Azure Cosmos DB konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)