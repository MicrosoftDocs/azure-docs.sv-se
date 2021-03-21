---
title: Använd bulk utförar .NET Library i Azure Cosmos DB för Mass import och uppdaterings åtgärder
description: Mass import och uppdatering av de Azure Cosmos DB dokumenten med hjälp av utförar .NET-biblioteket.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 03/23/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 34aef5bd880e3ef080676fb9e90e62796d499e7b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102429824"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Använd Mass utförar .NET-bibliotek för att utföra Mass åtgärder i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!NOTE]
> Detta Mass utförar-bibliotek som beskrivs i den här artikeln finns kvar för program som använder .NET SDK 2. x-versionen. För nya program kan du använda **Mass stödet** som är direkt tillgängligt med [.NET SDK version 3. x](tutorial-sql-api-dotnet-bulk-import.md) och det krävs inga externa bibliotek. 

> Om du för närvarande använder bulk utförar-biblioteket och planerar att migrera till Mass support på den nyare SDK: n, använder du stegen i [migreringsguiden](how-to-migrate-from-bulk-executor-library.md) för att migrera ditt program.

Den här självstudien innehåller instruktioner om hur du använder .NET-biblioteket för massköraren för att importera och uppdatera dokument till en Azure Cosmos-container. Mer information om utförar-biblioteket och hur det hjälper dig att utnyttja massivt data flöde och lagrings utrymme finns i [översikts](bulk-executor-overview.md) artikeln för utförar-bibliotek. I den här självstudien får du se ett exempel på en .NET-app som Mass importer genererar slumpmässigt genererade dokument till en Azure Cosmos-behållare. När du har importerat visar det hur du kan massredigera importerade data genom att ange korrigeringar som åtgärder att utföra på vissa dokument fält.

För närvarande stöds bulk utförar-biblioteket endast av Azure Cosmos DB SQL API-och Gremlin API-konton. Den här artikeln beskriver hur du använder bulk utförar .NET-biblioteket med SQL API-konton. Mer information om hur du använder bulk utförar .NET-biblioteket med Gremlin API-konton finns i [utföra Mass åtgärder i Azure Cosmos DB GREMLIN API](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Förutsättningar

* Om du inte redan har Visual Studio 2019 installerat kan du ladda ned och använda [Visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Se till att aktivera "Azure-utveckling" under installationen av Visual Studio.

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

* Du kan [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden. Du kan också använda [Azure Cosmos DB emulatorn](./local-emulator.md) med `https://localhost:8081` slut punkten. Primärnyckeln finns i [Autentisera begäranden](local-emulator.md#authenticate-requests).

* Skapa ett Azure Cosmos DB SQL API-konto med hjälp av stegen som beskrivs i avsnittet [skapa databas konto](create-sql-api-dotnet.md#create-account) i artikeln om .net-snabb start.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi växla till att arbeta med kod genom att hämta ett exempel på ett .NET-program från GitHub. Det här programmet utför Mass åtgärder på de data som lagras i ditt Azure Cosmos-konto. Du kan klona programmet genom att öppna en kommando tolk och navigera till den katalog där du vill kopiera den och köra följande kommando:

```bash
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Den klonade lagrings platsen innehåller två exempel: "BulkImportSample" och "BulkUpdateSample". Du kan öppna något av exempel programmen, uppdatera anslutnings strängarna i App.config-filen med Azure Cosmos DB kontots anslutnings strängar, skapa lösningen och köra den.

Programmet "BulkImportSample" genererar slumpmässiga dokument och Mass importer dem till ditt Azure Cosmos-konto. Program Mass uppdateringen "BulkUpdateSample" uppdaterar de importerade dokumenten genom att ange korrigeringar som åtgärder att utföra på specifika dokument fält. I nästa avsnitt kommer du att granska koden i vart och ett av dessa exempel appar.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Mass import av data till ett Azure Cosmos-konto

1. Gå till mappen "BulkImportSample" och öppna filen "BulkImportSample. SLN".  

2. Azure Cosmos DBens anslutnings strängar hämtas från App.config-filen, vilket visas i följande kod:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Mass import verktyget skapar en ny databas och en behållare med databas namnet, behållar namnet och de data flödes värden som anges i App.config-filen.

3. Nästa DocumentClient-objekt initieras med direkt TCP-anslutnings läge:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. BulkExecutor-objektet initieras med ett högt återförsöks värde för vänte tid och begränsade begär Anden. Och de ställs in på 0 för att skicka överbelastnings kontroll till BulkExecutor för dess livs längd.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. Programmet anropar BulkImportAsync-API: et. .NET-biblioteket innehåller två överlagringar av Mass import-API: en som accepterar en lista med serialiserade JSON-dokument och den andra som accepterar en lista med deserialiserade POCO-dokument. Mer information om definitionerna för var och en av dessa överlagrade metoder finns i [API-dokumentationen](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **BulkImportAsync-metoden accepterar följande parametrar:**
   
   |**Parameter**  |**Beskrivning** |
   |---------|---------|
   |enableUpsert    |   En flagga som aktiverar upsert-åtgärder på dokumenten. Om det redan finns ett dokument med angivet ID uppdateras det. Som standard är den inställd på falskt.      |
   |disableAutomaticIdGeneration    |    En flagga som inaktiverar automatisk generering av ID. Som standard är den inställd på sant.     |
   |maxConcurrencyPerPartitionKeyRange    | Den högsta graden av samtidighet per partitionsnyckel, med värdet null, gör att bibliotek använder ett standardvärde på 20. |
   |maxInMemorySortingBatchSize     |  Det maximala antalet dokument som hämtas från uppräkna ren dokument, vilken skickas till API-anropet i varje steg. Om du anger den här parametern till null i minnes sorterings fasen som inträffar innan Mass importeringen, gör det möjligt för bibliotek att använda standardvärdet minimum (Documents. Count, 1000000).       |
   |cancellationToken    |    Avbrotts-token för att avsluta Mass import åtgärden på ett smidigt sätt.     |

   **Objekt definition för Mass import av svar** Resultatet av API-anropet för Mass import innehåller följande attribut:

   |**Parameter**  |**Beskrivning**  |
   |---------|---------|
   |NumberOfDocumentsImported (lång)   |  Det totala antalet dokument som har importer ATS från det totala antalet dokument som angavs för Mass import-API-anrop.       |
   |TotalRequestUnitsConsumed (dubbel)   |   Totalt antal begär ande enheter (RU) som förbrukas av API-anropet för Mass import.      |
   |TotalTimeTaken (TimeSpan)    |   Den totala tid det tar för Mass import-API-anrop att slutföra körningen.      |
   |BadInputDocuments (lista \<object> )   |     Listan med fel format dokument som inte har importer ATS i Mass import-API-anropet. Åtgärda de returnerade dokumenten och försök importera igen. Felaktigt formaterade dokument inkluderar dokument vars ID-värde inte är en sträng (null eller någon annan datatyp betraktas som ogiltig).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Mass uppdatering av data i ditt Azure Cosmos-konto

Du kan uppdatera befintliga dokument med BulkUpdateAsync-API: et. I det här exemplet ska du ange `Name` ett nytt värde för fältet och ta bort `Description` fältet från de befintliga dokumenten. En fullständig uppsättning uppdaterings åtgärder som stöds finns i API- [dokumentationen](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate).

1. Gå till mappen "BulkUpdateSample" och öppna filen "BulkUpdateSample. SLN".  

2. Definiera uppdaterings objekt tillsammans med motsvarande fält uppdaterings åtgärder. I det här exemplet ska du använda `SetUpdateOperation` för att uppdatera `Name` fältet och `UnsetUpdateOperation` ta bort `Description` fältet från alla dokument. Du kan också utföra andra åtgärder, t. ex. öka ett dokument fält med ett angivet värde, push-specificera värden till ett mat ris fält eller ta bort ett enskilt värde från ett mat ris fält. Mer information om olika metoder som tillhandahålls av Mass uppdaterings-API: et finns i [API-dokumentationen](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate).

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. Programmet anropar BulkUpdateAsync-API: et. Mer information om definitionen av BulkUpdateAsync-metoden finns i [API-dokumentationen](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **BulkUpdateAsync-metoden accepterar följande parametrar:**

   |**Parameter**  |**Beskrivning** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Den högsta graden av samtidighet per partitionsnyckel. om du anger den här parametern till null blir biblioteket att använder standardvärdet (20).   |
   |maxInMemorySortingBatchSize    |    Det maximala antalet uppdaterings objekt som hämtats från uppräkna ren uppdaterings objekt som har skickats till API-anropet i varje steg. När du anger den här parametern till null för sorterings fasen i minnet som inträffar före Mass uppdatering, kommer biblioteket att använda det minimala standardvärdet (updateItems. Count, 1000000).     |
   | cancellationToken|Avbrotts-token för att avsluta Mass uppdaterings åtgärden på ett smidigt sätt. |

   **Definition av svars objekt för Mass uppdatering** Resultatet av API-anropet för Mass uppdatering innehåller följande attribut:

   |**Parameter**  |**Beskrivning** |
   |---------|---------|
   |NumberOfDocumentsUpdated (lång)    |   Antalet dokument som har uppdaterats från det totala antalet dokument som angavs för Mass uppdaterings-API-anrop.      |
   |TotalRequestUnitsConsumed (dubbel)   |    Totalt antal begär ande enheter (ru: er) som används av API-anropet för Mass uppdatering.    |
   |TotalTimeTaken (TimeSpan)   | Den totala tid det tar för Mass uppdatering av API-anrop för att slutföra körningen. |
    
## <a name="performance-tips"></a>Prestandatips 

Tänk på följande när du får bättre prestanda när du använder bulk utförar-biblioteket:

* För bästa prestanda bör du köra ditt program från en virtuell Azure-dator som finns i samma region som ditt Azure Cosmos-kontos Skriv region.  

* Vi rekommenderar att du instansierar ett enskilt `BulkExecutor` objekt för hela programmet inom en enskild virtuell dator som motsvarar en viss Azure Cosmos-behållare.  

* Eftersom en enda API-körning för Mass åtgärder förbrukar en stor del av klient datorns processor och nätverkets IO (detta sker genom att flera aktiviteter skapas internt). Undvik att skapa flera samtidiga aktiviteter i program processen som kör API-anrop för Mass åtgärder. Om ett API-anrop med en enda Mass åtgärd som körs på en enskild virtuell dator inte kan använda hela behållarens data flöde (om behållarens data flöde > 1 000 000 RU/s), är det bäst att skapa separata virtuella datorer för att samtidigt köra API-anrop för Mass åtgärder.  

* Se till att `InitializeAsync()` metoden anropas efter att ha instansierat ett BulkExecutor-objekt för att hämta partitions mappningen för mål Cosmos-behållaren.  

* Kontrol lera att **gcServer** har Aktiver ATS för bättre prestanda i programmets App.Config
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Biblioteket genererar spår som kan samlas in antingen i en loggfil eller i-konsolen. Om du vill aktivera båda lägger du till följande kod i programmets App.Config-fil.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>Nästa steg

* Information om NuGet-paket och viktig information finns i [UTFÖRAR SDK-information](sql-api-sdk-bulk-executor-dot-net.md).
