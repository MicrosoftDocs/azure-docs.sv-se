---
title: Använda Azure.Search.Documents (V11) i .NET
titleSuffix: Azure Cognitive Search
description: Lär dig hur du skapar och hanterar Sök objekt i ett .NET-program med C# och klient biblioteket för Azure.Search.Documents (V11). Kodfragment visar hur du ansluter till tjänsten, skapar index och frågor.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 15a878eb863b71a4519e75def2598f013152dfb7
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881641"
---
# <a name="how-to-use-azuresearchdocuments-in-a-c-net-application"></a>Använda Azure.Search.Documents i ett C# .NET-program

Den här artikeln beskriver hur du skapar och hanterar Sök objekt med C# och [**uments**](/dotnet/api/overview/azure/search) -klient biblioteket förAzure.Search.Doc.

## <a name="about-version-11"></a>Om version 11

Azure SDK för .NET lägger till ett nytt klient bibliotek för [**Azure.Search.Documents**](/dotnet/api/overview/azure/search) från Azure SDK-teamet som fungerar som likvärdigt med [Microsoft. Azure. Sök efter](/dotnet/api/overview/azure/search/client10) klient bibliotek, men använder gemensamma metoder och konventioner där det är tillämpligt. Några exempel är [`AzureKeyCredential`](/dotnet/api/azure.azurekeycredential) autentisering av nycklar och [System.Text.Jspå. Serialisering](/dotnet/api/system.text.json.serialization) för JSON-serialisering.

Precis som med tidigare versioner kan du använda det här biblioteket för att:

+ Skapa och hantera Sök index, data källor, indexerare, färdighetsuppsättningar och synonym kartor
+ Läs in och hantera Sök dokument i ett index
+ Köra frågor, allt utan att behöva hantera information om HTTP och JSON

Biblioteket distribueras som ett enda [Azure.Search.Documents NuGet-paket](https://www.nuget.org/packages/Azure.Search.Documents/), som innehåller alla API: er som används för program mässig åtkomst till en search-tjänst.

Klient biblioteket definierar klasser som `SearchIndex` , `SearchField` och, samt `SearchDocument` åtgärder som `SearchIndexClient.CreateIndex` och `SearchClient.Search` i `SearchIndexClient` `SearchClient` klasserna och. Dessa klasser är indelade i följande namn rymder:

+ [`Azure.Search.Documents`](/dotnet/api/azure.search.documents)
+ [`Azure.Search.Documents.Indexes`](/dotnet/api/azure.search.documents.indexes)
+ [`Azure.Search.Documents.Indexes.Models`](/dotnet/api/azure.search.documents.indexes.models)
+ [`Azure.Search.Documents.Models`](/dotnet/api/azure.search.documents.models)

Azure.Search.Documents (version 11) mål versionen [ `2020-06-30` av Azure Kognitiv sökning-REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/data-plane/Azure.Search/preview/2020-06-30). 

Klient biblioteket tillhandahåller inte [Service Management-åtgärder](/rest/api/searchmanagement/), till exempel att skapa och skala Sök tjänster och hantera API-nycklar. Om du behöver hantera dina Sök resurser från ett .NET-program använder du [Microsoft. Azure. Management. search](/dotnet/api/overview/azure/search/management) -biblioteket i Azure SDK för .net.

## <a name="upgrade-to-v11"></a>Uppgradera till v11

Om du har använt den tidigare versionen av .NET SDK och du vill uppgradera till den aktuella allmänt tillgängliga versionen, se [Uppgradera till Azure kognitiv sökning .NET SDK version 11](search-dotnet-sdk-migration-version-11.md)

## <a name="sdk-requirements"></a>Krav för SDK

+ Visual Studio 2019 eller senare.

+ Din egen Azure Kognitiv sökning-tjänst. För att du ska kunna använda SDK behöver du namnet på din tjänst och en eller flera API-nycklar. [Skapa en tjänst i portalen](search-create-service-portal.md) om du inte har någon.

+ Hämta [Azure.Search.Documents-paketet](https://www.nuget.org/packages/Azure.Search.Documents) med **verktyg**  >  **NuGet Package Manager**  >  **Hantera NuGet-paket för lösning** i Visual Studio. Sök efter paket namnet `Azure.Search.Documents` .

Azure SDK för .NET överensstämmer med [.net Standard 2,0](/dotnet/standard/net-standard#net-implementation-support), vilket innebär att .NET Framework 4.6.1 och .net Core 2,0 som minimi krav.

## <a name="example-application"></a>Exempelprogram

Den här artikeln "lär sig med exempel", som förlitar sig på [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) -kod exemplet på GitHub för att illustrera grundläggande begrepp i Azure kognitiv sökning – särskilt hur du skapar, läser in och söker efter ett sökindex.

I resten av den här artikeln förutsätter vi ett nytt index med namnet "Hotels", ifyllt med några dokument, med flera frågor som matchar i resultat.

Nedan visas huvud programmet som visar det övergripande flödet:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchIndexClient indexClient = CreateSearchIndexClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, indexClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, indexClient);

    SearchClient searchClient = indexClient.GetSearchClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(searchClient);

    SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

    Console.WriteLine("{0}", "Run queries...\n");
    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

Härnäst är en del skärm bild av utdata, förutsatt att du kör programmet med ett giltigt tjänst namn och API-nycklar:

:::image type="content" source="media/search-howto-dotnet-sdk/console-output.png" alt-text="Console. WriteLine-utdata från exempel programmet":::

### <a name="client-types"></a>Klient typer

Klient biblioteket använder tre klient typer för olika åtgärder: [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) för att skapa, uppdatera eller ta bort index, [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) läsa in eller fråga ett index och [`SearchIndexerClient`](/dotnet/api/azure.search.documents.indexes.searchindexerclient) arbeta med indexerare och färdighetsuppsättningar. Den här artikeln fokuserar på de två första. 

Alla klienter kräver minst tjänstens namn eller slut punkt och en API-nyckel. Det är vanligt att tillhandahålla den här informationen i en konfigurations fil, på samma sätt som du hittar i `appsettings.json` filen för [DotNetHowTo exempel program](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo). Om du vill läsa från konfigurations filen lägger `using Microsoft.Extensions.Configuration;` du till i programmet.

Följande instruktion skapar index klienten som används för att skapa, uppdatera eller ta bort index. Det tar en Sök slut punkt och en administratörs-API-nyckel.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceEndPoint), new AzureKeyCredential(adminApiKey));
    return indexClient;
}
```

Nästa instruktion skapar Sök klienten som används för att läsa in dokument eller köra frågor. `SearchClient` kräver ett index. Du behöver en administrations-API-nyckel för att läsa in dokument, men du kan använda en API-nyckel för frågor för att köra frågor. 

```csharp
string indexName = configuration["SearchIndexName"];

private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

> [!NOTE]
> Om du anger en ogiltig nyckel för import åtgärden (t. ex. en frågegrupp där en administratörs nyckel krävdes), kommer att orsaka `SearchClient` `CloudException` fel meddelandet "förbjuden" första gången du anropar en åtgärds metod på den. Om det händer kan du kontrol lera API-nyckeln.
>

### <a name="deleting-the-index"></a>Tar bort indexet

I det tidiga utvecklings skedet kanske du vill inkludera en [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) instruktion för att ta bort ett pågående index så att du kan återskapa det med en uppdaterad definition. Exempel kod för Azure Kognitiv sökning innehåller ofta ett borttagnings steg så att du kan köra exemplet igen.

Följande rader anropas `DeleteIndexIfExists` :

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, indexClient);
```

Med den här metoden kan du `SearchIndexClient` kontrol lera om indexet finns och ta bort det i så fall:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchIndexClient indexClient)
{
    try
    {
        if (indexClient.GetIndex(indexName) != null)
        {
            indexClient.DeleteIndex(indexName);
        }
    }
    catch (RequestFailedException e) when (e.Status == 404)
    {
        // Throw an exception if the index name isn't found
        Console.WriteLine("The index doesn't exist. No deletion occurred.");
```

> [!NOTE]
> I exempel koden i den här artikeln används synkrona metoder för enkelhet, men du bör använda de asynkrona metoderna i dina egna program för att hålla dem skalbara och tillgängliga. I metoden ovan kan du till exempel använda [`DeleteIndexAsync`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindexasync) i stället för [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) .
>

## <a name="create-an-index"></a>Skapa ett index

Du kan använda [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) för att skapa ett index. 

Metoden nedan skapar ett nytt [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) objekt med en lista med [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) objekt som definierar schemat för det nya indexet. Varje fält har ett namn, en datatyp och flera attribut som definierar Sök beteendet. 

Fält kan definieras från en modell klass med hjälp av [`FieldBuilder`](/dotnet/api/azure.search.documents.indexes.fieldbuilder) . `FieldBuilder`Klassen använder reflektion för att skapa en lista med `SearchField` objekt för indexet genom att undersöka de offentliga egenskaperna och attributen för den aktuella `Hotel` modell klassen. Vi tar en närmare titt på `Hotel` klassen senare.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    indexClient.CreateOrUpdateIndex(definition);
}
```

Förutom fält kan du också lägga till bedömnings profiler, förslags alternativ eller CORS-alternativ till indexet (dessa parametrar utelämnas från exemplet för det kortfattat). Du hittar mer information om SearchIndex-objektet och dess komponent delar i [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) listan egenskaper, samt i [referensen REST API](/rest/api/searchservice/).

> [!NOTE]
> Du kan alltid skapa en lista med `Field` objekt direkt i stället för att använda `FieldBuilder` vid behov. Till exempel kanske du inte vill använda en modell klass eller så kan du behöva använda en befintlig modell klass som du inte vill ändra genom att lägga till attribut.
>

### <a name="call-createindex-in-main"></a>Anropa CreateIndex i Main ()

`Main` skapar ett nytt "Hotels"-index genom att anropa metoden ovan:

```csharp
Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, indexClient);
```

## <a name="use-a-model-class-for-data-representation"></a>Använd en modell klass för data representation

I DotNetHowTo-exemplet används modell klasser för data strukturer för [hotell](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Hotel.cs), [adress](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Address.cs)och [rum](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Room.cs) . `Hotel` referenser `Address` , en komplex typ av enskild nivå (ett fält med flera delar) och `Room` (en samling med fält med flera delar).

Du kan använda dessa typer för att skapa och läsa in indexet och för att strukturera svaret från en fråga:

```csharp
// Use-case: <Hotel> in a field definition
FieldBuilder fieldBuilder = new FieldBuilder();
var searchFields = fieldBuilder.Build(typeof(Hotel));

// Use-case: <Hotel> in a response
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

En annan metod är att lägga till fält i ett index direkt. I följande exempel visas bara några fält.

   ```csharp
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };
   ```

### <a name="field-definitions"></a>Fält definitioner

Din data modell i .NET och dess motsvarande index schema bör ha stöd för den Sök upplevelse som du vill ge till slutanvändaren. Varje objekt på den översta nivån i .NET, till exempel ett sökdokument i ett sökindex, motsvarar ett Sök resultat som du skulle presentera i användar gränssnittet. I ett hotell söknings program kan slutanvändarna exempelvis vilja söka efter hotell namn, funktioner på hotellet eller egenskaperna för ett visst rum. 

I varje klass definieras ett fält med en datatyp och attribut som avgör hur det används. Namnet på varje offentlig egenskap i varje klass mappar till ett fält med samma namn i index definitionen. 

Ta en titt på följande kodfragment som hämtar flera fält definitioner från hotell-klassen. Observera att adresserna och rummen är C#-typer med egna klass definitioner (se exempel koden om du vill visa dem). Båda är komplexa typer. Mer information finns i [så här modellerar du komplexa typer](search-howto-complex-data-types.md).

```csharp
public partial class Hotel
{
    [SimpleField(IsKey = true, IsFilterable = true)]
    public string HotelId { get; set; }

    [SearchableField(IsSortable = true)]
    public string HotelName { get; set; }

    [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
    public string Description { get; set; }

    [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
    public string Category { get; set; }

    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [SearchableField]
    public Address Address { get; set; }

    public Room[] Rooms { get; set; }
```

#### <a name="choosing-a-field-class"></a>Välja en fält klass

När du definierar fält kan du använda Bask [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) Lassen, eller så kan du använda derivat hjälp modeller som fungerar som "mallar" med förkonfigurerade egenskaper.

Exakt ett fält i indexet måste fungera som dokument nyckel ( `IsKey = true` ). Det måste vara en sträng och måste identifiera varje dokument unikt. Det måste också finnas `IsHidden = true` , vilket innebär att det inte kan visas i Sök resultaten.

| Fälttyp | Beskrivning och användning |
|------------|-----------------------|
| [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) | Basklass, med de flesta egenskaper inställt på null, förutom `Name` vad som krävs, och `AnalyzerName` som är standardvärdet för standard Lucene. |
| [`SimpleField`](/dotnet/api/azure.search.documents.indexes.models.simplefield) | Hjälp modell. Kan vara vilken datatyp som helst, är alltid icke-sökbar (ignoreras för fullständiga texts öknings frågor) och kan hämtas (den är inte dold). Andra attribut är inaktiverade som standard, men kan aktive ras. Du kan använda en `SimpleField` för dokument-ID: n eller fält som endast används i filter, ansikts eller bedömnings profiler. I så fall, se till att använda alla attribut som krävs för scenariot, till exempel `IsKey = true` för ett dokument-ID. Mer information finns i [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) i käll koden. |
| [`SearchableField`](/dotnet/api/azure.search.documents.indexes.models.searchablefield) | Hjälp modell. Måste vara en sträng och är alltid sökbar och hämtnings bar. Andra attribut är inaktiverade som standard, men kan aktive ras. Eftersom den här fält typen är sökbar, stöder den synonymer och hela komplementet till analys egenskaperna. Mer information finns i [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) i käll koden. |

Oavsett om du använder Basic `SearchField` -API: et eller någon av hjälp modeller måste du uttryckligen aktivera attributen filter, aspekt och sortering. Till exempel måste [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)och [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) uttryckligen vara attribut som visas i exemplet ovan.

#### <a name="adding-field-attributes"></a>Lägger till fältattribut

Observera hur varje fält dekoreras med attribut som `IsFilterable` ,, `IsSortable` `IsKey` och `AnalyzerName` . Dessa attribut mappar direkt till [motsvarande fältattribut i ett Azure kognitiv sökning-index](/rest/api/searchservice/create-index). `FieldBuilder`Klassen använder dessa egenskaper för att skapa fält definitioner för indexet.

#### <a name="field-type-mapping"></a>Fält typs mappning

.NET-typerna för egenskaper mappar till motsvarande fält typer i index definitionen. Exempelvis mappar `Category`-strängegenskapen till `category`-fältet, som är av typen `Edm.String`. Det finns liknande typ mappningar mellan `bool?` , `Edm.Boolean` , `DateTimeOffset?` och `Edm.DateTimeOffset` så vidare. 

Har du meddelat `SmokingAllowed` egenskapen?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

`JsonIgnore`Attributet i den här egenskapen instruerar `FieldBuilder` att inte serialisera det till indexet som ett fält.  Detta är ett bra sätt att skapa beräknade egenskaper på klient sidan som du kan använda som hjälp program i ditt program.  I det här fallet `SmokingAllowed` Visar egenskapen om någon `Room` i `Rooms` samlingen tillåter rökning. Om alla är falskt anger det att hela hotellet inte tillåter rökning.

## <a name="load-an-index"></a>Läs in ett index

Nästa steg i `Main` fyller på det nyligen skapade "Hotels"-indexet. Den här index populationen görs i följande metod: (en kod ersätts med "..." i illustrations syfte. Se den fullständiga exempel lösningen för fullständig data populations kod.)

```csharp
private static void UploadDocuments(SearchClient searchClient)
{
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                HotelName = "Secret Point Motel",
                ...
                Address = new Address()
                {
                    StreetAddress = "677 5th Ave",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Budget Room, 1 Queen Bed (Cityside)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (City View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                HotelName = "Twin Dome Motel",
                ...
                {
                    StreetAddress = "140 University Town Center Dr",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Suite, 2 Double Beds (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Standard Room, 1 Queen Bed (City View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Waterfront View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "3",
                HotelName = "Triple Landscape Hotel",
                ...
                Address = new Address()
                {
                    StreetAddress = "3393 Peachtree Rd",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Standard Room, 2 Queen Beds (Amenities)",
                        ...
                    },
                    new Room ()
                    {
                        Description = "Standard Room, 2 Double Beds (Waterfront View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (Cityside)",
                        ...
                    }
                }
            }
        };

    try
    {
        IndexDocumentsResult result = searchClient.IndexDocuments(batch);
    }
    catch (Exception)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine("Failed to index some of the documents: {0}");
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
```

Den här metoden har fyra delar. Den första skapar en matris med 3 `Hotel` objekt var och en med 3 `Room` objekt som fungerar som vår indata för överföring till indexet. Dessa data är hårdkodade för enkelhetens skull. I ett faktiskt program kommer data troligen från en extern data källa, till exempel en SQL-databas.

Den andra delen skapar en [`IndexDocumentsBatch`](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) som innehåller dokumenten. Du anger den åtgärd som du vill använda för batchen vid den tidpunkt då du skapar den, i det här fallet genom att anropa [`IndexDocumentsAction.Upload`](/dotnet/api/azure.search.documents.models.indexdocumentsaction.upload) . Gruppen överförs sedan till Azure Kognitiv sökning-indexet med [`IndexDocuments`](/dotnet/api/azure.search.documents.searchclient.indexdocuments) metoden.

> [!NOTE]
> I det här exemplet laddar vi bara upp dokument. Om du vill sammanfoga ändringar i befintliga dokument eller ta bort dokument kan du skapa batchar genom att anropa `IndexDocumentsAction.Merge` , `IndexDocumentsAction.MergeOrUpload` eller `IndexDocumentsAction.Delete` i stället. Du kan också blanda olika åtgärder i en enda batch genom att anropa `IndexBatch.New` , som tar en samling med `IndexDocumentsAction` objekt, där vart och ett meddelar Azure kognitiv sökning att utföra en viss åtgärd på ett dokument. Du kan skapa var `IndexDocumentsAction` och en med en egen åtgärd genom att anropa motsvarande metod som `IndexDocumentsAction.Merge` , `IndexAction.Upload` och så vidare.
> 

Den tredje delen av den här metoden är ett catch-block som hanterar ett viktigt fel fall för indexering. Om Sök tjänsten inte kan indexera några av dokumenten i batchen, `IndexBatchException` utlöses en av `IndexDocuments` . Detta undantag kan inträffa om du indexerar dokument medan tjänsten är hårt belastad. **Vi rekommenderar starkt att du uttryckligen hanterar den här situationen i din kod.** Du kan fördröja och sedan försöka indexera dokumentet som misslyckades igen eller så kan du logga och fortsätta som i exemplet, eller göra något annat beroende på programmets krav på datakonsekvens.

Slutligen `UploadDocuments` fördröjs metoden i två sekunder. Indexering sker asynkront i din Sök tjänst, så exempel programmet måste vänta en kort stund för att se till att dokumenten är tillgängliga för sökning. Fördröjningar som den här är normalt endast nödvändiga i demonstrationer, tester och exempelprogram.

### <a name="call-uploaddocuments-in-main"></a>Anropa UploadDocuments i Main ()

Följande kodfragment ställer in en instans av [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) med hjälp av [`GetSearchClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient.getsearchclient) metoden indexClient. IndexClient använder en administrations-API-nyckel för dess förfrågningar, vilket krävs för att läsa in eller uppdatera dokument.

En annan metod är att anropa `SearchClient` direkt och skicka in en administrations-API-nyckel på `AzureKeyCredential` .

```csharp
SearchClient searchClient = indexClient.GetSearchClient(indexName);

Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(searchClient);
```

## <a name="run-queries"></a>Köra frågor

Konfigurera först en `SearchClient` som läser Sök slut punkten och frågans API-nyckel från **appsettings.jspå**:

```csharp
private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

Sedan definierar du en metod som skickar en förfrågan. 

Varje gången metoden kör en fråga skapas ett nytt [`SearchOptions`](/dotnet/api/azure.search.documents.searchoptions) objekt. Det här objektet används för att ange ytterligare alternativ för frågan, till exempel sortering, filtrering, växling och fasettering. I den här metoden ställer vi in `Filter` egenskapen, `Select` , och `OrderBy` för olika frågor. Mer information om syntaxen för Sökfrågans uttryck är [enkel](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)frågesyntax.

Nästa steg är att köra Sök frågan. Att köra sökningen görs med hjälp av `SearchClient.Search` metoden. För varje fråga ska du skicka Sök texten som ska användas som en sträng (eller `"*"` om det inte finns någon söktext) plus de sökalternativ som skapades tidigare. Vi anger också `Hotel` som typ parameter för `SearchClient.Search` , vilket instruerar SDK att deserialisera dokument i Sök resultaten till objekt av typen `Hotel` .

```csharp
private static void RunQueries(SearchClient searchClient)
{
    SearchOptions options;
    SearchResults<Hotel> results;

    Console.WriteLine("Query 1: Search for 'motel'. Return only the HotelName in results:\n");

    options = new SearchOptions();
    options.Select.Add("HotelName");

    results = searchClient.Search<Hotel>("motel", options);

    WriteDocuments(results);

    Console.Write("Query 2: Apply a filter to find hotels with rooms cheaper than $100 per night, ");
    Console.WriteLine("returning the HotelId and Description:\n");

    options = new SearchOptions()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)"
    };
    options.Select.Add("HotelId");
    options.Select.Add("Description");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.Write("Query 3: Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    options =
        new SearchOptions()
        {
            Size = 2
        };
    options.OrderBy.Add("LastRenovationDate desc");
    options.Select.Add("HotelName");
    options.Select.Add("LastRenovationDate");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.WriteLine("Query 4: Search the HotelName field for the term 'hotel':\n");

    options = new SearchOptions();
    options.SearchFields.Add("HotelName");

    //Adding details to select, because "Location" is not supported yet when deserialize search result to "Hotel"
    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Category");
    options.Select.Add("Tags");
    options.Select.Add("ParkingIncluded");
    options.Select.Add("LastRenovationDate");
    options.Select.Add("Rating");
    options.Select.Add("Address");
    options.Select.Add("Rooms");

    results = searchClient.Search<Hotel>("hotel", options);

    WriteDocuments(results);
}
```

För det tredje definierar du en metod som skriver svaret och skriver ut varje dokument till-konsolen:

```csharp
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

### <a name="call-runqueries-in-main"></a>Anropa RunQueries i Main ()

```csharp
SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

Console.WriteLine("{0}", "Running queries...\n");
RunQueries(indexClientForQueries);
```

### <a name="explore-query-constructs"></a>Utforska frågornas konstruktioner

Låt oss ta en närmare titt på var och en av frågorna i sin tur. Här är koden för att köra den första frågan:

```csharp
options = new SearchOptions();
options.Select.Add("HotelName");

results = searchClient.Search<Hotel>("motel", options);

WriteDocuments(results);
```

I det här fallet söker vi igenom hela indexet efter ordet "Motel" i valfritt sökbart fält och vi vill bara hämta hotell namnen som anges av `Select` alternativet. Här är resultaten:

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

I den andra frågan använder du ett filter för att välja rum med en natt takt på mindre än $100. Returnera bara hotell-ID: t och beskrivningen i resultaten:

```csharp
options = new SearchOptions()
{
    Filter = "Rooms/any(r: r/BaseRate lt 100)"
};
options.Select.Add("HotelId");
options.Select.Add("Description");

results = searchClient.Search<Hotel>("*", options);
```

Frågan ovan använder ett OData `$filter` -uttryck, `Rooms/any(r: r/BaseRate lt 100)` för att filtrera dokumenten i indexet. Detta använder [valfri operator](./search-query-odata-collection-operators.md) för att tillämpa "BaseRate lt 100" för alla objekt i samlingen rummen. Mer information finns i [syntax för OData-filter](./query-odata-filter-orderby-syntax.md).

I den tredje frågan hittar du de två viktigaste hotellen som har varit mest nyligen renovated och visar hotell namnet och senaste renoverings datumet. Här är koden: 

```csharp
options =
    new SearchOptions()
    {
        Size = 2
    };
options.OrderBy.Add("LastRenovationDate desc");
options.Select.Add("HotelName");
options.Select.Add("LastRenovationDate");

results = searchClient.Search<Hotel>("*", options);

WriteDocuments(results);
```

I den senaste frågan söker du efter alla hotell namn som matchar ordet "hotell":

```csharp
options.Select.Add("HotelId");
options.Select.Add("HotelName");
options.Select.Add("Description");
options.Select.Add("Category");
options.Select.Add("Tags");
options.Select.Add("ParkingIncluded");
options.Select.Add("LastRenovationDate");
options.Select.Add("Rating");
options.Select.Add("Address");
options.Select.Add("Rooms");

results = searchClient.Search<Hotel>("hotel", options);

WriteDocuments(results);
```

Det här avsnittet avslutar introduktionen till .NET SDK, men stoppa inte här. Nästa avsnitt innehåller ytterligare resurser för att lära dig mer om programmering med Azure Kognitiv sökning.

## <a name="next-steps"></a>Nästa steg

+ Sök i API Reference-dokumentationen för [Azure.Search.Documents](/dotnet/api/azure.search.documents) och [REST API](/rest/api/searchservice/)

+ Bläddra bland andra kod exempel baserat på Azure.Search.Documents i [Azure-Search-dotNet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) och [search-kom igång-dotNet](https://github.com/Azure-Samples/search-dotnet-getting-started)

+ Granska [namn konventioner](/rest/api/searchservice/Naming-rules) för att lära dig reglerna för att namnge olika objekt

+ Granska [data typer som stöds](/rest/api/searchservice/Supported-data-types)
