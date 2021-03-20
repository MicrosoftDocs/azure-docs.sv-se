---
title: Skapa ett exempel på en slutpunkt-till-slutpunkt Azure Cosmos DB Java SDK v4-program med hjälp av ändra feed
description: Den här guiden vägleder dig genom ett enkelt Java SQL API-program som infogar dokument i en Azure Cosmos DB-behållare, samtidigt som en materialiserad vy av behållaren används med hjälp av Change feed.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 06/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 765fd3afc7fe688d3e6b0e3394e7dc8c39af69b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93096860"
---
# <a name="how-to-create-a-java-application-that-uses-azure-cosmos-db-sql-api-and-change-feed-processor"></a>Så här skapar du ett Java-program som använder Azure Cosmos DB SQL API och ändra flödes processor
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här instruktions guiden vägleder dig genom ett enkelt Java-program som använder Azure Cosmos DB SQL API för att infoga dokument i en Azure Cosmos DB-behållare, samtidigt som en materialiserad vy av behållaren används med hjälp av ändrings flöde och byte av flödes processor. Java-programmet kommunicerar med Azure Cosmos DB SQL API med hjälp av Azure Cosmos DB Java SDK v4.

> [!IMPORTANT]  
> Den här självstudien gäller endast Azure Cosmos DB Java SDK v4. Se Azure Cosmos DB Java SDK v4- [viktig](sql-api-sdk-java-v4.md)information, [maven-lagringsplatsen](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB Java SDK v4- [prestanda tips](performance-tips-java-sdk-v4-sql.md)och Azure Cosmos DB [fel söknings guide](troubleshoot-java-sdk-v4-sql.md) för Java SDK v4 för mer information. Om du använder en äldre version än v4 kan du läsa om hur du uppgraderar till v4 i [Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) -guide.
>

## <a name="prerequisites"></a>Förutsättningar

* URI och nyckel för ditt Azure Cosmos DB konto

* Maven

* Java 8

## <a name="background"></a>Bakgrund

Azure Cosmos DB ändra feed innehåller ett händelse drivna gränssnitt för att utlösa åtgärder som svar på dokument infogning. Detta har många användnings områden. Till exempel i program som både är både Läs och skrivbara är en Chief-användning av ändrings flöden att skapa en real tids **materialiserad vy** över en behållare när den matar in dokument. Behållaren för materialiserad vy innehåller samma data, men partitioneras för effektiva läsningar, vilket gör programmet både Läs-och skriv effektivitet.

Arbetet med att hantera ändringar av flödes händelser tas i stor utsträckning av det process bibliotek för ändrings flöden som är inbyggt i SDK: n. Det här biblioteket är tillräckligt kraftfullt för att distribuera ändringar av flödes händelser mellan flera arbetare, om det behövs. Allt du behöver göra är att ange ett återanrops bibliotek för ändrings flöden.

I det här enkla exemplet visas ett bibliotek för ändrings flödes processor med en enda arbets tagare som skapar och tar bort dokument från en materialiserad vy.

## <a name="setup"></a>Konfiguration

Om du inte redan har gjort det kan du klona appens exempel lagrings platsen:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

Öppna en Terminal i lagrings platsen-katalogen. Bygg appen genom att köra

```bash
mvn clean package
```

## <a name="walkthrough"></a>Genomgång

1. Som första kontroll bör du ha ett Azure Cosmos DB-konto. Öppna **Azure Portal** i webbläsaren, gå till ditt Azure Cosmos DB-konto och navigera till **datautforskaren** i det vänstra fönstret.

   :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_empty.JPG" alt-text="Azure Cosmos DB-konto":::

1. Kör appen i terminalen med följande kommando:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Tryck på RETUR när du ser

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    Gå sedan tillbaka till Azure Portal Datautforskaren i webbläsaren. Du ser att en databas- **GroceryStoreDatabase** har lagts till med tre tomma behållare: 

    * **InventoryContainer** – inventerings posten för vårt exempel på livsmedels lager, partitionerad på objekt ```id``` som är ett uuid.
    * **InventoryContainer – pktype** – en materialiserad vy av inventerings posten, optimerad för frågor över objekt ```type```
    * **InventoryContainer – leasing** avtal – en container container krävs alltid för ändrings flödet. lån spårar appens förlopp vid läsning av ändrings flödet.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG" alt-text="Tomma behållare":::

1. I terminalen bör du nu se en prompt

    ```bash
    Press enter to start creating the materialized view...
    ```

    Tryck på RETUR. Nu kommer följande kodblock att köra och initiera processorn för ändrings flöden i en annan tråd: 

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (maven com. Azure:: Azure-Cosmos) asynkront API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=InitializeCFP)]

    ```"SampleHost_1"``` är namnet på den ändrade flödes processorns arbetare. ```changeFeedProcessorInstance.start()``` är vad som faktiskt startar bearbetningen av Change feeds.

    Gå tillbaka till Azure Portal Datautforskaren i webbläsaren. Under behållaren **InventoryContainer-Leases** , klickar du på **objekt** för att se dess innehåll. Du ser att Change feed-processorn har fyllt i leasing containern, dvs. processorn har tilldelat ```SampleHost_1``` arbetaren ett lån på vissa partitioner i **InventoryContainer**.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_leases.JPG" alt-text="Lån":::

1. Tryck på RETUR igen i terminalen. Detta kommer att utlösa 10 dokument som ska infogas i **InventoryContainer**. Varje dokument infogning visas i ändra feed som JSON. följande callback-kod hanterar dessa händelser genom att spegla JSON-dokumenten till en materialiserad vy:

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (maven com. Azure:: Azure-Cosmos) asynkront API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=CFPCallback)]

1. Tillåt att koden kör 5-10sec. Gå sedan tillbaka till Azure Portal Datautforskaren och gå till **InventoryContainer > objekt**. Du bör se att objekten infogas i lager behållaren. Anteckna partitionsnyckel ( ```id``` ).

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_items.JPG" alt-text="Feed-behållare":::

1. Nu går du till Datautforskaren navigera till **InventoryContainer-pktype > objekt**. Detta är den materialiserade vyn – objekten i denna behållares spegel **InventoryContainer** eftersom de infogades program mässigt av ändrings flödet. Anteckna partitionsnyckel ( ```type``` ). Den här materialiserade vyn är optimerad för frågor som filtrerar över ```type``` , vilket skulle vara ineffektivt på **InventoryContainer** eftersom den är partitionerad ```id``` .

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG" alt-text="Skärm bild som visar Datautforskaren sidan för ett Azure Cosmos D B-konto med valda objekt.":::

1. Vi håller på att ta bort ett dokument från både **InventoryContainer** och **InventoryContainer-pktype** med bara ett enda ```upsertItem()``` anrop. Börja med att titta på Azure Portal Datautforskaren. Vi tar bort dokumentet som ```/type == "plums"``` är inringat i rött under

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG" alt-text="Skärm bild som visar Datautforskaren sidan för ett Azure Cosmos D B-konto med ett visst objekt I D valt.":::

    Tryck på RETUR igen för att anropa funktionen ```deleteDocument()``` i exempel koden. Den här funktionen, som visas nedan, upsertar en ny version av dokumentet med ```/ttl == 5``` , som anger TTL-värdet (Time-to-Live) till 5Sec. 
    
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (maven com. Azure:: Azure-Cosmos) asynkront API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=DeleteWithTTL)]

    Ändrings flödet ```feedPollDelay``` är inställt på 100 MS. därför svarar ändrings flödet på den här uppdateringen nästan omedelbart och samtal som ```updateInventoryTypeMaterializedView()``` visas ovan. Det senaste funktions anropet kommer att upsert det nya dokumentet med TTL för 5Sec till **InventoryContainer-pktype**.

    Detta innebär att dokumentet upphör att gälla efter 5 sekunder och tas bort från båda behållarna.

    Den här proceduren är nödvändig eftersom ändrings flödet bara utfärdar händelser vid artikel infogning eller uppdatering, inte för borttagning av objekt.

1. Tryck på RETUR en gång för att stänga programmet och rensa dess resurser.
