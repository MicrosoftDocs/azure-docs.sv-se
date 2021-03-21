---
title: Snabb start – Använd Java för att skapa en dokument databas med hjälp av Azure Cosmos DB
description: I den här snabb starten presenteras ett Java-kod exempel som du kan använda för att ansluta till och fråga Azure Cosmos DB SQL API
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 4b62b591c408f663fd28d5077af924f785ee66c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93090417"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Snabb start: bygga en Java-app för att hantera Azure Cosmos DB SQL API-data
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring-data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

I den här snabb starten skapar och hanterar du ett Azure Cosmos DB SQL API-konto från Azure Portal och genom att använda en Java-app som klonas från GitHub. Först skapar du ett Azure Cosmos DB SQL API-konto med hjälp av Azure Portal och skapar sedan en Java-app med hjälp av SQL Java SDK och lägger sedan till resurser till ditt Cosmos DB-konto med hjälp av Java-programmet. Azure Cosmos DB är en databas tjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckel värdes-och Graf-databaser med globala funktioner för distribution och horisontell skalning.

> [!IMPORTANT]  
> Den här snabb starten är endast för Azure Cosmos DB Java SDK v4. Se Azure Cosmos DB Java SDK v4- [viktig](sql-api-sdk-java-v4.md)information, [maven-lagringsplatsen](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB Java SDK v4- [prestanda tips](performance-tips-java-sdk-v4-sql.md)och Azure Cosmos DB [fel söknings guide](troubleshoot-java-sdk-v4-sql.md) för Java SDK v4 för mer information. Om du använder en äldre version än v4 kan du läsa om hur du uppgraderar till v4 i [Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) -guide.
>

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Eller [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration. Du kan också använda [Azure Cosmos DB emulatorn](https://aka.ms/cosmosdb-emulator) med en URI `https://localhost:8081` och nyckeln `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Peka `JAVA_HOME` miljö variabeln till den mapp där JDK är installerad.
- Ett [binärt maven-Arkiv](https://maven.apache.org/download.cgi). Kör `apt-get install maven` för att installera maven på Ubuntu.
- [Git](https://www.git-scm.com/downloads). Kör `sudo apt-get install git` för att installera git på Ubuntu.

## <a name="introductory-notes"></a>Inledande anmärkningar

*Strukturen för ett Cosmos DB konto.* Oavsett API eller programmeringsspråk innehåller ett Cosmos DB- *konto* noll eller flera *databaser*, en *databas* (dB) innehåller noll eller flera *behållare*, och en *behållare* innehåller noll eller flera objekt, som visas i diagrammet nedan:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos Account-entiteter" border="false":::

Du kan läsa mer om databaser, behållare och objekt [här.](account-databases-containers-items.md) Några viktiga egenskaper definieras på nivån för behållaren, bland dem med det *etablerade data flödet* och *partitionsnyckel*. 

Det etablerade data flödet mäts i *ru: er*(Request units) som har ett penning pris och är en väsentlig avgörande faktor för kontots drift kostnad. Det etablerade data flödet kan väljas per behållar kornig het eller granularitet per databas, men data flödes specifikationen på behållare nivå är vanligt vis lämplig. Du kan läsa mer om data flödes etablering [här.](set-throughput.md)

När objekt infogas i en Cosmos DB-behållare blir databasen vågrätt genom att lägga till mer lagrings utrymme och data bearbetning för att hantera begär Anden. Lagrings-och beräknings kapaciteten läggs till i diskreta enheter som kallas *partitioner*, och du måste välja ett fält i dina dokument för att vara den partitionsnyckel som mappar varje dokument till en partition. Hur partitioner hanteras är att varje partition tilldelas en ungefär lika stor sektor från intervallet av nyckel värden. Därför bör du välja en partitionsnyckel som är relativt slumpmässig eller jämnt distribuerad. Annars kommer vissa partitioner att se betydligt fler begär Anden (*het partition*) medan andra partitioner ser betydligt färre begär Anden (*kall partition*) och detta bör undvikas. Du kan lära dig mer om partitionering [här](partitioning-overview.md).

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


# <a name="sync-api"></a>[Sync-API](#tab/sync)

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Hantera databas resurser med synkron (Sync) API

* `CosmosClient`-initiering. `CosmosClient`Tillhandahåller logisk representation på klient sidan för Azure Cosmos Database-tjänsten. Den här klienten används för att konfigurera och köra förfrågningar till tjänsten.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* `CosmosDatabase` flikar.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosContainer` flikar.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Skapa objekt med hjälp av `createItem` metoden.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* Punkt läsningar utförs med hjälp av `readItem` metoden.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* SQL-frågor över JSON utförs med hjälp av `queryItems` metoden.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

# <a name="async-api"></a>[Asynkront API](#tab/async)

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Hantera databas resurser med asynkron (asynkron) API

* Asynkrona API-anrop returneras omedelbart, utan att vänta på ett svar från servern. Till följd av detta visar följande kodfragment lämpliga design mönster för att utföra alla tidigare hanterings uppgifter med hjälp av asynkront API.

* `CosmosAsyncClient`-initiering. `CosmosAsyncClient`Tillhandahåller logisk representation på klient sidan för Azure Cosmos Database-tjänsten. Den här klienten används för att konfigurera och köra asynkrona begär Anden mot tjänsten.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* `CosmosAsyncDatabase` flikar.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosAsyncContainer` flikar.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Precis som med Sync-API: et utförs objektet med hjälp av- `createItem` metoden. Det här exemplet visar hur du effektivt kan utfärda flera asynkrona `createItem` begär Anden genom att prenumerera på en reaktiv ström som skickar begär Anden och skriver ut meddelanden. Eftersom det här enkla exemplet körs för att slutföras och avslutas, `CountDownLatch` används instanser för att se till att programmet inte avslutas när objektet skapas. **Den rätta metoden för asynkron programmering är inte att blockera vid asynkrona anrop – i realistiska frågor om användnings fall genereras från en Main ()-loop som körs oändligt, vilket eliminerar behovet av att låsa på asynkrona anrop.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* Precis som med Sync-API: et utförs punkt läsningar med hjälp av `readItem` metoden.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* Precis som med Sync-API: et utförs SQL-frågor över JSON med hjälp av `queryItems` metoden.

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

3. I git-terminalfönstret använder du följande kommando för att starta Java-programmet (Ersätt SYNCASYNCMODE med `sync` eller `async` beroende på vilken exempel kod du vill köra, ersätter YOUR_COSMOS_DB_HOSTNAME med det CITERAde URI-värdet från portalen och ersätter YOUR_COSMOS_DB_MASTER_KEY med den primära nyckeln från portalen)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    Terminalfönstret visar ett meddelande om att FamilyDB-databasen har skapats. 
    
4. Appen skapar en databas med namnet `AzureSampleFamilyDB`
5. Appen skapar behållaren med namnet `FamilyContainer`
6. Appen utför punkt läsningar med hjälp av objekt-ID: n och värdet för partitionsnyckel (som är lastName i vårt exempel). 
7. Appen frågar objekt om att hämta alla familjer med efter namn i (' Andersen ', ' Wakefield ', ' Johnson ')

7. Appen tar inte bort de skapade resurserna. Växla tillbaka till portalen för att [rensa resurserna](#clean-up-resources).  från ditt konto så att du inte debiteras.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB SQL API-konto, skapar en dokument databas och behållare med hjälp av Datautforskaren och kör en Java-app för att göra samma sak program mässigt. Nu kan du importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)
