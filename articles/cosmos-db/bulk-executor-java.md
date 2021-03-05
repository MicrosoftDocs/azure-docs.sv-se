---
title: Använd bulk utförar Java-bibliotek i Azure Cosmos DB för att utföra Mass import och uppdaterings åtgärder
description: Mass import och uppdatering Azure Cosmos DB dokument med bulk utförar Java Library
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 08/26/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: d0ccaf15df62b566b5a52ee7a4031ba7e270a4ae
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218136"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Använda massexecutor-biblioteket i Java för att utföra massåtgärder på Azure Cosmos DB-data
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här självstudien innehåller instruktioner om hur du använder Azure Cosmos DBs utförar Java-bibliotek för att importera och uppdatera Azure Cosmos DB dokument. Om du vill lära dig mer om bulk utförar Library och hur det hjälper dig att använda massivt data flöde och lagring, se [översikts](bulk-executor-overview.md) artikel för utförar-bibliotek. I den här självstudien skapar du ett Java-program som genererar slumpmässiga dokument och de har Mass importer ATS till en Azure Cosmos-behållare. När du har importerat kan du uppdatera vissa egenskaper för ett dokument. 

För närvarande stöds inte bulk utförar-biblioteket av Azure Cosmos DB SQL API-och Gremlin API-konton. Den här artikeln beskriver hur du använder bulk utförar Java-bibliotek med SQL API-konton. Mer information om hur du använder bulk utförar .NET-bibliotek med Gremlin API finns i [utföra Mass åtgärder i Azure Cosmos DB GREMLIN API](bulk-executor-graph-dotnet.md). Det samlings utförar-bibliotek som beskrivs är tillgängligt endast för [Azure Cosmos DB Java-synkronisering SDK v2](sql-api-sdk-java.md) och det är den aktuella rekommenderade lösningen för Java Mass stöd. Den är för närvarande inte tillgänglig för de 3. x, 4. x eller andra högre SDK-versionerna.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.  

* Du kan [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden. Du kan också använda [Azure Cosmos DB emulatorn](./local-emulator.md) med `https://localhost:8081` slut punkten. Primärnyckeln finns i [Autentisera begäranden](local-emulator.md#authenticate-requests).  

* [Java Development Kit (JDK) 1.7 +](/java/azure/jdk/)  
  - I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.  

  - Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.

* [Hämta](https://maven.apache.org/download.cgi) och [Installera](https://maven.apache.org/install.html) ett [maven](https://maven.apache.org/) -binärt Arkiv  
  
  - I Ubuntu kan du köra `apt-get install maven` för att installera Maven.

* Skapa ett Azure Cosmos DB SQL API-konto med hjälp av stegen som beskrivs i avsnittet [skapa databas konto](create-sql-api-java.md#create-a-database-account) i artikeln Java snabb start.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi växla till att arbeta med kod genom att hämta ett exempel-Java-program från GitHub. Det här programmet utför Mass åtgärder på Azure Cosmos DB data. Du kan klona programmet genom att öppna en kommando tolk och navigera till den katalog där du vill kopiera programmet och köra följande kommando:

```bash
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

Den klonade lagrings platsen innehåller två exempel: "bulkimport" och "bulkupdate" i relation till mappen "\azure-cosmosdb-bulkexecutor-Java-Getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor". Programmet "bulkimport" genererar slumpmässiga dokument och importerar dem till Azure Cosmos DB. Programmet "bulkupdate" uppdaterar vissa dokument i Azure Cosmos DB. I nästa avsnitt kommer vi att granska koden i vart och ett av dessa exempel appar. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Mass import av data till Azure Cosmos DB

1. Azure Cosmos DBens anslutnings strängar läses som argument och tilldelas variabler som definierats i CmdLineConfiguration. java-filen.  

2. Nästa DocumentClient-objekt initieras med hjälp av följande uttryck:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. DocumentBulkExecutor-objektet initieras med ett högt värde för återförsök för vänte tid och begränsade begär Anden. Och de ställs in på 0 för att skicka överbelastnings kontroll till DocumentBulkExecutor för dess livs längd.  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the container's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
   ```

4. Anropa det inportal-API som genererar slumpmässiga dokument för Mass import till en Azure Cosmos-behållare. Du kan konfigurera kommando rads konfigurationerna i CmdLineConfiguration. java-filen.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   API: et för Mass import accepterar en samling JSON-serialiserade dokument och har följande syntax, mer information finns i [API-dokumentationen](/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   Den inportal metoden accepterar följande parametrar:
 
   |**Parameter**  |**Beskrivning**  |
   |---------|---------|
   |isUpsert    |   En flagga som aktiverar upsert av dokumenten. Om ett dokument med angivet ID redan finns, uppdateras det.  |
   |disableAutomaticIdGeneration     |   En flagga som inaktiverar automatisk generering av ID. Som standard är den inställd på sant.   |
   |maxConcurrencyPerPartitionRange    |  Den högsta graden av samtidighet per nyckel intervall. Standardvärdet är 20.  |

   **Objekt definition för Mass import av svar** Resultatet av API-anropet för Mass import innehåller följande get-metoder:

   |**Parameter**  |**Beskrivning**  |
   |---------|---------|
   |int getNumberOfDocumentsImported ()  |   Det totala antalet dokument som har importer ATS från dokumenten som har skickats till API-anropet för Mass import.      |
   |dubbel getTotalRequestUnitsConsumed ()   |  Totalt antal begär ande enheter (RU) som förbrukas av API-anropet för Mass import.       |
   |Varaktighet getTotalTimeTaken ()   |    Den totala tid det tar för Mass importer att köra API-anrop för att slutföra körningen.     |
   |Visa lista \<Exception> getErrors () |  Hämtar listan över fel om några dokument från batchen som angavs för Mass import API-anropet inte kunde infogas.       |
   |Visa lista \<Object> getBadInputDocuments ()  |    Listan med fel format dokument som inte har importer ATS i Mass import-API-anropet. Användaren bör åtgärda de returnerade dokumenten och försöka importera igen. Felaktigt formaterade dokument inkluderar dokument vars ID-värde inte är en sträng (null eller någon annan datatyp betraktas som ogiltig).     |

5. När du har Mass import applikationen klar skapar du kommando rads verktyget från källan genom att använda kommandot "mvn Clean Package". Det här kommandot genererar en jar-fil i målmappen:  

   ```bash
   mvn clean package
   ```

6. När mål beroendena har genererats kan du anropa Mass import programmet med hjälp av följande kommando:  

   ```bash
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB's endpoint>*  -masterKey *<Fill in your Azure Cosmos DB's primary key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   Mass import verktyget skapar en ny databas och en samling med databas namnet, samlings namnet och data flödes värden som anges i App.config-filen. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Mass uppdaterings data i Azure Cosmos DB

Du kan uppdatera befintliga dokument med BulkUpdateAsync-API: et. I det här exemplet ska du ange fältet namn till ett nytt värde och ta bort fältet Beskrivning från de befintliga dokumenten. En fullständig uppsättning fält uppdaterings åtgärder som stöds finns i [API-dokumentationen](/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Definierar uppdaterings objekt tillsammans med motsvarande fält uppdaterings åtgärder. I det här exemplet ska du använda SetUpdateOperation för att uppdatera namn fältet och UnsetUpdateOperation för att ta bort fältet Beskrivning från alla dokument. Du kan också utföra andra åtgärder, t. ex. öka ett dokument fält med ett angivet värde, push-specificera värden till ett mat ris fält eller ta bort ett enskilt värde från ett mat ris fält. Mer information om olika metoder som tillhandahålls av Mass uppdaterings-API: [et](/java/api/com.microsoft.azure.documentdb.bulkexecutor)finns i API-dokumentationen.  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                        
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. Anropa updateAll-API: et som genererar slumpmässiga dokument som sedan Mass importer ATS till en Azure Cosmos-behållare. Du kan konfigurera kommando rads konfigurationerna så att de skickas i CmdLineConfiguration. java-filen.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   Mass uppdaterings-API: n accepterar en samling objekt som ska uppdateras. Varje uppdaterings objekt anger listan över fält uppdaterings åtgärder som ska utföras på ett dokument som identifieras av ett ID och ett nyckel värde för partitionen. Mer information finns i API- [dokumentationen](/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   Metoden updateAll accepterar följande parametrar:

   |**Parameter** |**Beskrivning** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  Den högsta graden av samtidighet per nyckel intervall. Standardvärdet är 20.  |
 
   **Objekt definition för Mass import av svar** Resultatet av API-anropet för Mass import innehåller följande get-metoder:

   |**Parameter** |**Beskrivning**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated ()  |   Det totala antalet dokument som har uppdaterats från de dokument som har skickats till API-anropet för Mass uppdatering.      |
   |dubbel getTotalRequestUnitsConsumed () |  Totalt antal begär ande enheter (RU) som förbrukas av API-anropet för Mass uppdatering.       |
   |Varaktighet getTotalTimeTaken ()  |   Den totala tid det tar för Mass uppdatering av API-anrop för att slutföra körningen.      |
   |Visa lista \<Exception> getErrors ()   |       Hämtar listan över drift-eller nätverks problem som rör uppdaterings åtgärden.      |
   |Visa lista \<BulkUpdateFailure> getFailedUpdates ()   |       Hämtar listan över uppdateringar som inte kunde slutföras tillsammans med de speciella undantagen som leder till fel.|

3. När du har Mass uppdaterings applikationen klar skapar du kommando rads verktyget från källan genom att använda kommandot "mvn Clean Package". Det här kommandot genererar en jar-fil i målmappen:  

   ```bash
   mvn clean package
   ```

4. När mål beroendena har genererats kan du anropa Mass uppdaterings programmet med hjälp av följande kommando:

   ```bash
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB's endpoint>* -masterKey **<Fill in your Azure Cosmos DB's primary key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Prestandatips 

Tänk på följande när du får bättre prestanda när du använder utförar-bibliotek:

* För bästa prestanda bör du köra ditt program från en virtuell Azure-dator i samma region som din Cosmos DB-kontots Skriv region.  
* För att uppnå högre data flöde:  

   * Ange JVM heap-storlek till ett tillräckligt stort antal för att undvika minnes problem vid hantering av ett stort antal dokument. Föreslagen Heap-storlek: Max (3 GB, 3 * sizeof (alla dokument som skickas till Mass imports-API i en batch)).  
   * Det finns en förbehandlings tid, på grund av vilken du får högre data flöde när du utför Mass åtgärder med ett stort antal dokument. Så om du vill importera 10 000 000-dokument, som kör Mass import 10 gånger på 10 Mass dokument var och en av storleken 1 000 000, är det bättre att använda Mass import 100 gånger på 100 av dokument var och en av storlekarna 100 000-dokument.  

* Vi rekommenderar att instansiera ett enskilt DocumentBulkExecutor-objekt för hela programmet inom en enskild virtuell dator som motsvarar en viss Azure Cosmos-behållare.  

* Eftersom en enda API-körning för Mass åtgärder förbrukar en stor del av klient datorns CPU och nätverkets IO. Detta sker genom att flera aktiviteter skapas internt, vilket innebär att du inte behöver skapa flera samtidiga aktiviteter i din program process varje körning av API-anrop för Mass åtgärder. Om ett API-anrop för en enda Mass åtgärd som körs på en enskild virtuell dator inte kan använda hela behållarens data flöde (om behållarens data flöde > 1 000 000 RU/s), är det bättre att skapa separata virtuella datorer för att samtidigt köra API-anrop för Mass åtgärder.

    
## <a name="next-steps"></a>Nästa steg
* Läs mer om maven-paket information och viktig information om bulk utförar Java Library i[UTFÖRAR SDK-information](sql-api-sdk-bulk-executor-java.md).