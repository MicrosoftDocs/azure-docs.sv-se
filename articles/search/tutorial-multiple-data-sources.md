---
title: C#-självstudie som indexerar flera Azure-datakällor
titleSuffix: Azure Cognitive Search
description: Lär dig hur du importerar data från flera data källor till ett enda Azure Kognitiv sökning-index med hjälp av indexerare. Den här självstudien och exempel koden finns i C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: b8711fa468cf361a42ed996c5b5dc50372d6cf90
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746160"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>Självstudie: index från flera data källor med hjälp av .NET SDK

Azure Kognitiv sökning kan importera, analysera och indexera data från flera data källor till ett enda konsoliderat Sök index. 

I den här självstudien används C# och klient biblioteket för [Azure.Search.Documents](/dotnet/api/overview/azure/search) i Azure SDK för .net för att indexera exempel på hotell data från en Azure Cosmos DB och slå samman med information om hotell rum som har tagits från Azure Blob Storage-dokument. Resultatet blir ett kombinerat hotell Sök index som innehåller hotell dokument, med rum som komplexa data typer.

I den här självstudien utför du följande uppgifter:

> [!div class="checklist"]
> * Ladda upp exempel data och skapa data källor
> * Identifiera dokument nyckeln
> * Definiera och skapa indexet
> * Indexera hotell data från Azure Cosmos DB
> * Slå samman hotell rums data från Blob Storage

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="overview"></a>Översikt

I den här självstudien används det nya klient biblioteket [Azure.Search.Documents](/dotnet/api/overview/azure/search), version 11. x, för att skapa och köra flera indexerare. I den här självstudien får du konfigurera två Azure-datakällor så att du kan konfigurera en indexerare som hämtar från båda för att fylla i ett enda sökindex. De två data uppsättningarna måste ha ett gemensamt värde som stöder sammanfogningen. I det här exemplet är det fältet ett ID. Så länge det finns ett fält som är gemensamt för att stödja mappningen, kan en indexerare sammanfoga data från olika resurser: strukturerade data från Azure SQL, ostrukturerade data från Blob Storage eller någon kombination av [data källor som stöds](search-indexer-overview.md#supported-data-sources) i Azure.

En färdig version av koden i den här självstudien finns i följande projekt:

* [Multiple data-sources/V11 (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v11)

Den här självstudien har uppdaterats med Azure.Search.Documents-paketet (version 11). En tidigare version av .NET SDK finns i [kod exemplet Microsoft. Azure. search (version 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v10) i GitHub.

## <a name="prerequisites"></a>Krav

+ [Azure Cosmos DB](../cosmos-db/create-cosmosdb-resources-portal.md)
+ [Azure Storage](../storage/common/storage-account-create.md)
+ [Visual Studio](https://visualstudio.microsoft.com/)
+ [Azure Kognitiv sökning (version 11. x) NuGet-paket](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Skapa](search-create-service-portal.md) eller [hitta en befintlig Sök tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Du kan använda den kostnads fria tjänsten för den här självstudien. En kostnads fri Sök tjänst begränsar dig till tre index, tre indexerare och tre data källor. I den här kursen skapar du en av varje. Innan du börjar bör du kontrol lera att du har utrymme på tjänsten för att godkänna de nya resurserna.

## <a name="1---create-services"></a>1 – skapa tjänster

I den här självstudien används Azure Kognitiv sökning för indexering och frågor, Azure Cosmos DB för en data uppsättning och Azure Blob Storage för den andra data uppsättningen. 

Skapa om möjligt alla tjänster i samma region och resurs grupp för närhet och hanterbarhet. I praktiken kan dina tjänster finnas i vilken region som helst.

I det här exemplet används två små uppsättningar med data som beskriver sju fiktiva hotell. En uppsättning beskriver själva hotellen och kommer att läsas in i en Azure Cosmos DB databas. Den andra uppsättningen innehåller information om hotell rummet och tillhandahålls som sju separata JSON-filer som ska överföras till Azure Blob Storage.

### <a name="start-with-cosmos-db"></a>Börja med Cosmos DB

1. Logga in på [Azure Portal](https://portal.azure.com)och navigera sedan till översikts sidan för Azure Cosmos DB konto.

1. Välj **datautforskaren** och välj sedan **ny databas**.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-newdb.png" alt-text="Skapa en ny databas" border="false":::

1. Ange namnet **hotell-rum-DB**. Acceptera standardvärden för återstående inställningar.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-dbname.png" alt-text="Konfigurera databas" border="false":::

1. Skapa en ny behållare. Använd den befintliga databasen som du nyss skapade. Ange **hotell** för behållar namnet och Använd **/HotelId** för partitionsnyckel.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-add-container.png" alt-text="Lägga till containern" border="false":::

1. Välj **objekt** under **hotell** och klicka sedan på **överför objekt** i kommando fältet. Gå till och välj filen **cosmosdb/HotelsDataSubset_CosmosDb.jspå** i projektmappen.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-upload.png" alt-text="Överför till Azure Cosmos DB samling" border="false":::

1. Använd knappen Uppdatera för att uppdatera vyn av objekten i Hotels-samlingen. Sju nya databas dokument visas.

1. Kopiera en anslutnings sträng från sidan **nycklar** till anteckningar. Du behöver detta för att **appsettings.js** i ett senare steg. Om du inte använde det föreslagna databas namnet "hotell rum-dB" kopierar du även databas namnet.

### <a name="azure-blob-storage"></a>Azure Blob Storage

1. Logga in på [Azure Portal](https://portal.azure.com), navigera till ditt Azure Storage-konto, klicka på **blobbar** och klicka sedan på **+ container**.

1. [Skapa en BLOB-behållare](../storage/blobs/storage-quickstart-blobs-portal.md) med namnet **hotell-rum** där du kan lagra JSON-filer för hotell rummet. Du kan ställa in den offentliga åtkomst nivån på alla giltiga värden.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-add-container.png" alt-text="Skapa en blobcontainer" border="false":::

1. När behållaren har skapats öppnar du den och väljer **Ladda upp** i kommando fältet. Navigera till mappen som innehåller exempelfilerna. Markera alla och klicka sedan på **överför**.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-upload.png" alt-text="Ladda upp filer" border="false":::

1. Kopiera lagrings kontots namn och en anslutnings sträng från sidan **åtkomst nycklar** till anteckningar. Du behöver båda värdena för att **appsettings.js** i i ett senare steg.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Den tredje komponenten är Azure-Kognitiv sökning, som du kan [skapa i portalen](search-create-service-portal.md). 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Kopiera en Admin API-nyckel och URL för Azure Kognitiv sökning

Du behöver tjänst-URL och en åtkomst nyckel för att kunna autentisera till din Sök tjänst.

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningar**  >  **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

   :::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="Hämta tjänstens namn och administratör och fråge nycklar" border="false":::

En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="2---set-up-your-environment"></a>2 – Konfigurera din miljö

1. Starta Visual Studio och välj **NuGet Package Manager** i **verktyg** -menyn och **Hantera NuGet-paket för lösning...**. 

1. På fliken **Bläddra** letar du upp och installerar **Azure.Search.Documents** (version 11,0 eller senare). Du måste klicka på ytterligare dialog rutor för att slutföra installationen.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Använda NuGet för att lägga till Azure-bibliotek" border="false":::

1. Sök efter **Microsoft.Extensions.Configuration** och **Microsoft.Extensions.Configuration.Jspå** NuGet-paket och installera dem också.

1. Öppna lösnings filen **/V11/AzureSearchMultipleDataSources.SLN**.

1. I Solution Explorer redigerar du **appsettings.jspå** filen för att lägga till anslutnings information.  

    ```json
    {
      "SearchServiceUri": "<YourSearchServiceURL>",
      "SearchServiceAdminApiKey": "<YourSearchServiceAdminApiKey>",
      "BlobStorageAccountName": "<YourBlobStorageAccountName>",
      "BlobStorageConnectionString": "<YourBlobStorageConnectionString>",
      "CosmosDBConnectionString": "<YourCosmosDBConnectionString>",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

De första två posterna är URL-adress och administratörs nycklar för en Sök tjänst. Använd den fullständiga slut punkten, till exempel: `https://mydemo.search.windows.net` .

I nästa poster anges konto namn och information om anslutnings strängen för Azure-Blob Storage och Azure Cosmos DB data källor.

## <a name="3---map-key-fields"></a>3 – mappa nyckel fält

Sammanfogning av innehåll kräver att båda data strömmarna är riktade mot samma dokument i Sök indexet. 

I Azure Kognitiv sökning kan nyckel fältet unikt identifiera varje dokument. Varje Sök index måste ha exakt ett nyckel fält av typen `Edm.String` . Nyckel fältet måste finnas för varje dokument i en data källa som läggs till i indexet. (I själva verket är det det enda obligatoriska fältet.)

När du indexerar data från flera data källor ser du till att varje inkommande rad eller ett dokument innehåller en gemensam dokument nyckel för att koppla data från två fysiskt distinkta käll dokument till ett nytt sökdokument i det kombinerade indexet. 

Det krävs ofta viss planering för att identifiera en meningsfull dokument nyckel för ditt index och se till att den finns i båda data källorna. I den här demon finns `HotelId` nyckeln för varje hotell i Cosmos DB också i rummen JSON-blobbar i Blob Storage.

Azure Kognitiv sökning-indexerare kan använda fält mappningar för att byta namn på och till och med omformatera data fält under indexerings processen, så att källdata kan dirigeras till rätt index fält. I Cosmos DB anropas exempelvis hotell-ID: t **`HotelId`** . Men i JSON-BLOB-filerna för hotell rummen heter hotell-ID: t **`Id`** . Programmet hanterar detta genom att mappa **`Id`** fältet från blobarna till **`HotelId`** nyckel fältet i indexeraren.

> [!NOTE]
> I de flesta fall kan automatiskt genererade dokument nycklar, till exempel de som skapats som standard av vissa indexerare, inte göra dokument nycklar för kombinerade index. I allmänhet ska du använda ett meningsfullt, unikt nyckel värde som redan finns i eller som enkelt kan läggas till i data källorna.

## <a name="4---explore-the-code"></a>4 – utforska koden

När data-och konfigurations inställningarna är på plats bör exempel programmet i **/V11/AzureSearchMultipleDataSources.SLN** vara redo att bygga och köra.

Den här enkla/.NET-distribution.-konsolen i C# utför följande uppgifter:

* Skapar ett nytt index baserat på data strukturen i C#-klassen (som även hänvisar till adress-och rums klasserna).
* Skapar en ny data källa och en indexerare som mappar Azure Cosmos DB data till index fält. Detta är båda objekten i Azure Kognitiv sökning.
* Kör indexeraren för att läsa in hotell data från Cosmos DB.
* Skapar en andra data källa och en indexerare som mappar JSON-BLOB-data till index fält.
* Kör den andra indexeraren för att läsa in data i rum från Blob Storage.

 Innan du kör programmet ska du ta en minut för att undersöka koden och definitionerna index och Indexer för det här exemplet. Den relevanta koden finns i två filer:

  + **Hotel.cs** innehåller det schema som definierar indexet
  + **Program.cs** innehåller funktioner som skapar Azure kognitiv sökning-index, data källor och indexerare och läser in de kombinerade resultaten i indexet.

### <a name="create-an-index"></a>Skapa ett index

Det här exempel programmet använder [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) för att definiera och skapa ett Azure kognitiv sökning-index. Det drar nytta av klassen [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) för att generera en index struktur från en C#-data modell klass.

Data modellen definieras av hotell klassen, som också innehåller referenser till adress-och rums klasserna. FieldBuilder går igenom flera klass definitioner för att generera en komplex data struktur för indexet. Metadata-Taggar används för att definiera attributen för varje fält, till exempel om det är sökbart eller sorterbart.

Ett befintligt index av samma namn tas bort innan det nya skapas, om du vill köra det här exemplet mer än en gång.

Följande kodfragment från **Hotel.cs** -filen visar enskilda fält, följt av en referens till en annan data modell klass, room [], som i sin tur har definierats i **Room.cs** -filen (visas inte).

```csharp
. . .
[SimpleField(IsFilterable = true, IsKey = true)]
public string HotelId { get; set; }

[SearchableField(IsFilterable = true, IsSortable = true)]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

I **program.cs** -filen definieras en [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) med ett namn och en fält samling som genereras av `FieldBuilder.Build` metoden, och sedan skapas följande:

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Skapa Azure Cosmos DB data källa och indexerare

I huvud programmet ingår logik för att skapa Azure Cosmos DB data källa för hotell data.

Först sammanfogas Azure Cosmos DB databas namnet till anslutnings strängen. Sedan definierar den ett [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) -objekt.

```csharp
private static async Task CreateAndRunCosmosDbIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    // Append the database name to the connection string
    string cosmosConnectString =
        configuration["CosmosDBConnectionString"]
        + ";Database="
        + configuration["CosmosDBDatabaseName"];

    SearchIndexerDataSourceConnection cosmosDbDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["CosmosDBDatabaseName"],
        type: SearchIndexerDataSourceType.CosmosDb,
        connectionString: cosmosConnectString,
        container: new SearchIndexerDataContainer("hotels"));

    // The Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(cosmosDbDataSource);
```

När data källan har skapats konfigurerar programmet en Azure Cosmos DB indexerare med namnet **hotell-rum-Cosmos-Indexer**.

Programmet kommer att uppdatera befintliga indexerare med samma namn och skriva över den befintliga indexeraren med innehållet i ovanstående kod. Den innehåller också återställnings-och körnings åtgärder, om du vill köra det här exemplet mer än en gång.

I följande exempel definieras ett schema för indexeraren, så att det körs en gång per dag. Du kan ta bort egenskapen schema från det här anropet om du inte vill att indexeraren automatiskt ska köras igen i framtiden.

```csharp
SearchIndexer cosmosDbIndexer = new SearchIndexer(
    name: "hotel-rooms-cosmos-indexer",
    dataSourceName: cosmosDbDataSource.Name,
    targetIndexName: indexName)
{
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

// Indexers keep metadata about how much they have already indexed.
// If we already ran the indexer, it "remembers" and does not run again.
// To avoid this, reset the indexer if it exists.
try
{
    await indexerClient.GetIndexerAsync(cosmosDbIndexer.Name);
    // Reset the indexer if it exists.
    await indexerClient.ResetIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404)
{
    // If the indexer does not exist, 404 will be thrown.
}

await indexerClient.CreateOrUpdateIndexerAsync(cosmosDbIndexer);

Console.WriteLine("Running Cosmos DB indexer...\n");

try
{
    // Run the indexer.
    await indexerClient.RunIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 429)
{
    Console.WriteLine("Failed to run indexer: {0}", ex.Message);
}
```

Det här exemplet innehåller ett enkelt try-catch-block för att rapportera eventuella fel som kan uppstå under körningen.

När Azure Cosmos DB indexeraren har körts innehåller Sök indexet en fullständig uppsättning exempel på hotell-dokument. Fältet rum för varje hotell är dock en tom matris, eftersom Azure Cosmos DB data källan utelämnar rums information. Därefter tar programmet emot från Blob Storage för att läsa in och slå samman rums data.

### <a name="create-blob-storage-data-source-and-indexer"></a>Skapa data källa och indexerare för Blob Storage

För att få information om rummet ställer programmet först in en Blob Storage-datakälla för att referera till en uppsättning enskilda JSON-BLOB-filer.

```csharp
private static async Task CreateAndRunBlobIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    SearchIndexerDataSourceConnection blobDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["BlobStorageAccountName"],
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["BlobStorageConnectionString"],
        container: new SearchIndexerDataContainer("hotel-rooms"));

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(blobDataSource);
```

När data källan har skapats konfigurerar programmet en BLOB-indexerare med namnet **hotell-rum-BLOB-Indexer**, som du ser nedan.

JSON-blobbar innehåller ett nyckel fält med namnet **`Id`** i stället för **`HotelId`** . Koden använder `FieldMapping` klassen för att instruera indexeraren att dirigera **`Id`** fältvärdet till **`HotelId`** dokument nyckeln i indexet.

Blob Storage-indexerare kan använda [IndexingParameters](/dotnet/api/azure.search.documents.indexes.models.indexingparameters) för att ange ett tolknings läge. Du bör ange olika tolknings lägen beroende på om blobbar representerar ett enskilt dokument eller flera dokument inom samma blob. I det här exemplet representerar varje BLOB ett enda JSON-dokument, så koden använder `json` tolknings läget. Mer information om indexerare som analyserar parametrar för JSON-blobar finns i [index JSON-blobbar](search-howto-index-json-blobs.md).

Det här exemplet definierar ett schema för indexeraren så att det körs en gång per dag. Du kan ta bort egenskapen schema från det här anropet om du inte vill att indexeraren automatiskt ska köras igen i framtiden.

```csharp
// Map the Id field in the Room documents to the HotelId key field in the index
List<FieldMapping> map = new List<FieldMapping> {
    new FieldMapping("Id")
    {
        TargetFieldName =  "HotelId"
    }
};

IndexingParameters parameters = new IndexingParameters();
parameters.Configuration.Add("parsingMode", "json");

SearchIndexer blobIndexer = new SearchIndexer(
    name: "hotel-rooms-blob-indexer",
    dataSourceName: blobDataSource.Name,
    targetIndexName: indexName)
{
    Parameters = parameters,
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

blobIndexer.FieldMappings.Add(new FieldMapping("Id") { TargetFieldName = "HotelId" });

// Reset the indexer if it already exists
try
{
    await indexerClient.GetIndexerAsync(blobIndexer.Name);
    await indexerClient.ResetIndexerAsync(blobIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404) { }

await indexerClient.CreateOrUpdateIndexerAsync(blobIndexer);

try
{
    // Run the indexer.
    await searchService.Indexers.RunAsync(blobIndexer.Name);
}
catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
{
    Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
}
```

Eftersom indexet redan har fyllts med hotell data från Azure Cosmos DB databasen, uppdaterar BLOB-indexeraren befintliga dokument i indexet och lägger till rums information.

> [!NOTE]
> Om du har samma icke-nyckel fält i båda data källorna och data i dessa fält inte matchar, kommer indexet att innehålla värdena från den som indexeraren kördes senast. I vårt exempel innehåller båda data källorna ett **HotelName** -fält. Om data i det här fältet skiljer sig åt för dokument med samma nyckel värde, kommer **HotelName** -data från data källan som indexerats senast att vara det värde som lagras i indexet.

## <a name="5---search"></a>5-Sök

Du kan utforska det ifyllda Sök indexet när programmet har körts, med hjälp av [**Sök Utforskaren**](search-explorer.md) i portalen.

I Azure Portal öppnar du **översikts** sidan Sök tjänst och letar upp **hotell-rum-exempel** index i listan **index** .

  :::image type="content" source="media/tutorial-multiple-data-sources/index-list.png" alt-text="Lista över Azure Kognitiv sökning-index" border="false":::

Klicka på hotell-rum-exempel index i listan. Ett Sök Utforskaren-gränssnitt visas för indexet. Ange en fråga för en term som "lyxen". Du bör se minst ett dokument i resultatet och det här dokumentet ska innehålla en lista över rums objekt i matrisen för rummen.

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experiment stegen i utvecklingen är den mest praktiska metoden för design upprepning att ta bort objekten från Azure Kognitiv sökning och tillåta att koden återskapas. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Exempel koden söker efter befintliga objekt och tar bort eller uppdaterar dem så att du kan köra programmet igen.

Du kan också använda portalen för att ta bort index, indexerare och data källor.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration är det en bra idé att ta bort de resurser som du inte längre behöver i slutet av projektet. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken alla resurser eller resurs grupper i det vänstra navigerings fönstret.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med konceptet för att mata in data från flera källor tar vi en närmare titt på indexerings konfigurationen, från och med Cosmos DB.

> [!div class="nextstepaction"]
> [Konfigurera en Azure Cosmos DB indexerare](search-howto-index-cosmosdb.md)
