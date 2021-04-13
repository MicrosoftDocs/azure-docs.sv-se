---
title: Snabbstart – Använda Java för att skapa en dokumentdatabas med hjälp av Azure Cosmos DB
description: Den här snabbstarten presenterar ett Java-kodexempel som du kan använda för att ansluta till och fråga Azure Cosmos DB SQL API
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 03/07/2021
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: e1f81ddba717dc2a9df02fda82ef74b52da8fd82
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366053"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Snabbstart: Skapa en Java-app för att hantera Azure Cosmos DB SQL API-data
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

I den här snabbstarten skapar och hanterar du ett Azure Cosmos DB SQL API-konto från Azure Portal och med hjälp av en Java-app som klonas från GitHub. Först skapar du ett Azure Cosmos DB SQL API-konto med hjälp av Azure Portal, sedan skapar du en Java-app med HJÄLP av SQL Java SDK och lägger sedan till resurser till ditt Cosmos DB-konto med hjälp av Java-programmet. Azure Cosmos DB är en databastjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckelvärdes- och grafdatabaser med global distribution och horisontella skalningsfunktioner.

> [!IMPORTANT]  
> Den här snabbstarten är endast Azure Cosmos DB Java SDK v4. Mer information finns Azure Cosmos DB viktig information om Java SDK [v4,](sql-api-sdk-java-v4.md)Maven-lagringsplatsen, Azure Cosmos DB Java SDK v4-prestandatips och felsökningsguiden för Azure Cosmos DB Java SDK [v4.](troubleshoot-java-sdk-v4-sql.md) [](https://mvnrepository.com/artifact/com.azure/azure-cosmos) [](performance-tips-java-sdk-v4-sql.md) Om du använder en äldre version än v4 kan du gå till guiden Migrera till [Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) för hjälp med att uppgradera till v4.
>

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa en utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Eller [prova Azure Cosmos DB utan en](https://azure.microsoft.com/try/cosmosdb/) Azure-prenumeration. Du kan också använda [Azure Cosmos DB emulatorn](https://aka.ms/cosmosdb-emulator) med en URI `https://localhost:8081` för och nyckeln `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Peka `JAVA_HOME` miljövariabeln till mappen där JDK är installerad.
- Ett [binärt Maven-arkiv.](https://maven.apache.org/download.cgi) I Ubuntu kör du för `apt-get install maven` att installera Maven.
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
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>Granska koden

Det här är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Köra appen](#run-the-app). 


# <a name="sync-api"></a>[Synkroniserings-API](#tab/sync)

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Hantera databasresurser med hjälp av det synkrona API:et (synkronisering)

* `CosmosClient`-initiering. tillhandahåller `CosmosClient` logisk representation på klientsidan för Azure Cosmos-databastjänsten. Den här klienten används för att konfigurera och köra förfrågningar till tjänsten.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* `CosmosDatabase` Skapandet.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosContainer` Skapandet.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Skapa objekt med hjälp av `createItem` metoden .

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* Punktläsningar utförs med hjälp av `readItem` metoden .

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* SQL-frågor över JSON utförs med hjälp av `queryItems` metoden .

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

# <a name="async-api"></a>[Asynkront API](#tab/async)

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Hantera databasresurser med hjälp av det asynkrona (asynkrona) API:et

* Asynkrona API-anrop returneras omedelbart utan att vänta på svar från servern. Mot bakgrund av detta visar följande kodfragment rätt designmönster för att utföra alla föregående hanteringsuppgifter med hjälp av asynkront API.

* `CosmosAsyncClient`-initiering. tillhandahåller `CosmosAsyncClient` logisk representation på klientsidan för Azure Cosmos-databastjänsten. Den här klienten används för att konfigurera och köra asynkrona begäranden mot tjänsten.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* `CosmosAsyncDatabase` Skapandet.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosAsyncContainer` Skapandet.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Precis som med synkroniserings-API:et skapas objekt med hjälp av `createItem` metoden . Det här exemplet visar hur du effektivt utfärdar flera asynkrona begäranden genom att prenumerera på en `createItem` Reactive Stream som utfärdar begäranden och skriver ut meddelanden. Eftersom det här enkla exemplet slutförs och avslutas, används instanser för att säkerställa `CountDownLatch` att programmet inte avslutas när objektet skapas. **Rätt asynkron programmeringspraxis är att inte blockera på asynkrona anrop – i realistiska användningsfall genereras begäranden från en main()-loop som körs på obestämd tid, vilket eliminerar behovet av att låsa vid asynkrona anrop.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* Precis som med synkroniserings-API:et utförs punktläsningar med hjälp av `readItem` metoden .

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* Precis som med synkroniserings-API:et utförs SQL-frågor över JSON med hjälp av `queryItems` metoden .

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=QueryItems)]

---

## <a name="run-the-app"></a>Kör appen

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och starta appen med din slutpunktsinformation. På så vis kan appen kommunicera med den värdbaserade databasen.

1. I git-terminalfönstret `cd` till exempelkodsmappen.

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. I git-terminalfönstret använder du följande kommando för att installera de Java-paket som krävs.

    ```bash
    mvn package
    ```

3. I git-terminalfönstret använder du följande kommando för att starta Java-programmet (ersätt SYNCASYNCMODE med eller beroende på vilken exempelkod du vill köra, ersätt YOUR_COSMOS_DB_HOSTNAME med det angivna URI-värdet från portalen och ersätt YOUR_COSMOS_DB_MASTER_KEY med den angivna primärnyckeln från `sync` `async` portalen)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    Terminalfönstret visar ett meddelande om att FamilyDB-databasen har skapats. 
    
4. Appen skapar en databas med namnet `AzureSampleFamilyDB`
5. Appen skapar en container med namnet `FamilyContainer`
6. Appen utför punktläsningar med objekt-ID:n och partitionsnyckelvärdet (som är lastName i vårt exempel). 
7. Appen kommer att fråga efter objekt för att hämta alla familjer med efternamn i ('Andersen', 'Wakefield', 'Johnson')

7. Appen tar inte bort de skapade resurserna. Växla tillbaka till portalen för att [rensa resurserna](#clean-up-resources).  från ditt konto så att du inte debiteras.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos DB SQL API-konto, skapar en dokumentdatabas och container med hjälp av Datautforskaren och kör en Java-app för att göra samma sak programmässigt. Du kan nu importera ytterligare data till ditt Azure Cosmos DB konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)
