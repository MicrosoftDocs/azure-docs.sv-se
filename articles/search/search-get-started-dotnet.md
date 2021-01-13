---
title: 'Snabb start: skapa ett Sök index i .NET'
titleSuffix: Azure Cognitive Search
description: I den här snabb starten för C#, lär du dig hur du skapar ett index, läser in data och kör frågor med hjälp av klient biblioteket för Azure.Search.Documents.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f0d912d5b14932c43d109f8f955d5f16381cf773
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180106"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Snabb start: skapa ett sökindex med hjälp av klient biblioteket för Azure.Search.Documents

Använd det nya [ klient biblioteket förAzure.Search.Documents (version 11)](/dotnet/api/overview/azure/search.documents-readme) för att skapa ett .net Core-konsolprogram i C# som skapar, läser in och skickar frågor till ett sökindex.

Du kan [Ladda ned käll koden](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) för att börja med ett färdigt projekt eller följa stegen i den här artikeln för att skapa en egen.

> [!NOTE]
> Letar du efter en tidigare version? Se [skapa ett sökindex med hjälp av Microsoft. Azure. search v10](search-get-started-dotnet-v10.md) i stället.

## <a name="prerequisites"></a>Krav

Innan du börjar har du följande verktyg och tjänster:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).

+ En Azure Kognitiv sökning-tjänst. [Skapa en tjänst](search-create-service-portal.md) eller [Sök efter en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Du kan använda en kostnads fri tjänst för den här snabb starten. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), vilken utgåva som helst. Exempel koden har testats i den kostnads fria community-versionen av Visual Studio 2019.

När du konfigurerar ditt projekt hämtar du [Azure.Search.Documents NuGet-paketet](https://www.nuget.org/packages/Azure.Search.Documents/).

Azure SDK för .NET överensstämmer med [.net Standard 2,0](/dotnet/standard/net-standard#net-implementation-support), vilket innebär att .NET Framework 4.6.1 och .net Core 2,0 som minimi krav.

## <a name="set-up-your-project"></a>Konfigurera projektet

Sätt samman anslutnings information för tjänsten och starta sedan Visual Studio för att skapa ett nytt konsol program som kan köras på .NET Core.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Kopiera en nyckel och slut punkt

Anrop till tjänsten kräver en URL-slutpunkt och en åtkomst nyckel på varje begäran. Som ett första steg hittar du API-nyckeln och URL: en som ska läggas till i projektet. Du kommer att ange båda värdena när du skapar klienten i ett senare steg.

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

2. I **Inställningar**  >  **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten, som krävs om du skapar eller tar bort objekt. Det finns två utbytbara primär-och sekundär nycklar. Du kan använda något av alternativen.

   ![Hämta en HTTP-slutpunkt och åtkomst nyckel](media/search-get-started-rest/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomst nyckel")

Alla begär Anden kräver en API-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

### <a name="install-the-nuget-package"></a>Installera NuGet-paketet

När projektet har skapats lägger du till klient biblioteket. [Azure.Search.Documents-paketet](https://www.nuget.org/packages/Azure.Search.Documents/) består av ett klient bibliotek som innehåller alla API: er som används för att arbeta med en Sök tjänst i .net.

1. Starta Visual Studio och skapa ett .NET Core-konsolprogram.

1. I **verktyg**  >  **NuGet Package Manager** väljer du **Hantera NuGet-paket för lösning.**... 

1. Klicka på **Browse** (Bläddra).

1. Sök efter `Azure.Search.Documents` och välj version 11,0 eller senare.

1. Klicka på **Installera** till höger för att lägga till sammansättningen i projektet och lösningen.

### <a name="create-a-search-client"></a>Skapa en sökklient

1. I **program.cs** ändrar du namn området till `AzureSearch.SDK.Quickstart.v11` och lägger sedan till följande `using` direktiv.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Skapa två klienter: [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) skapar indexet och [SearchClient](/dotnet/api/azure.search.documents.searchclient) läser in och skickar frågor till ett befintligt index. Båda behöver tjänstens slut punkt och en administrations-API-nyckel för autentisering med behörigheterna Skapa/ta bort.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

        // Create a SearchIndexClient to send create/delete index commands
        Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
        AzureKeyCredential credential = new AzureKeyCredential(apiKey);
        SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);

        // Create a SearchClient to load and query documents
        SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 – Skapa ett index

Den här snabb starten skapar ett hotell index som du läser in med hotell data och kör frågor mot. I det här steget definierar du fälten i indexet. Varje fält definition innehåller ett namn, data typ och attribut som avgör hur fältet används.

I det här exemplet används synkrona metoder för Azure.Search.Documents-biblioteket för enkelhet och läsbarhet. I produktions scenarier bör du dock använda asynkrona metoder för att hålla din app skalbar och svarar. Du kan till exempel använda [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) i stället för [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex).

1. Lägg till en tom klass definition i projektet: **Hotel.cs**

1. Kopiera följande kod till **Hotel.cs** för att definiera strukturen för ett hotell dokument. Attributen i fältet avgör hur det används i ett program. Till exempel `IsFilterable` måste attributet tilldelas till alla fält som har stöd för ett filter uttryck.

    ```csharp
    using System;
    using System.Text.Json.Serialization;
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;

    namespace AzureSearch.Quickstart
    {
        public partial class Hotel
        {
            [SimpleField(IsKey = true, IsFilterable = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            [SearchableField]
            public Address Address { get; set; }
        }
    }
    ```

   I klient biblioteket för Azure.Search.Documents kan du använda [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) och [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) för att förenkla fält definitionerna. Båda är derivat av en [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) och kan eventuellt förenkla koden:

   + `SimpleField` kan vara vilken datatyp som helst, är alltid icke-sökbar (ignoreras för fullständiga texts öknings frågor) och kan hämtas (den är inte dold). Andra attribut är inaktiverade som standard, men kan aktive ras. Du kan använda en `SimpleField` för dokument-ID: n eller fält som endast används i filter, ansikts eller bedömnings profiler. I så fall, se till att använda alla attribut som krävs för scenariot, till exempel `IsKey = true` för ett dokument-ID. Mer information finns i [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) i käll koden.

   + `SearchableField` måste vara en sträng och är alltid sökbar och hämtnings bar. Andra attribut är inaktiverade som standard, men kan aktive ras. Eftersom den här fält typen är sökbar, stöder den synonymer och hela komplementet till analys egenskaperna. Mer information finns i [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) i käll koden.

   Oavsett om du använder Basic `SearchField` -API: et eller någon av hjälp modeller måste du uttryckligen aktivera attributen filter, aspekt och sortering. Till exempel måste [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)och [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) uttryckligen vara attribut som visas i exemplet ovan. 

1. Lägg till en andra tom klass definition i projektet: **address.cs**.  Kopiera följande kod till-klassen.

   ```csharp
   using Azure.Search.Documents.Indexes;

    namespace AzureSearch.Quickstart
    {
        public partial class Address
        {
            [SearchableField(IsFilterable = true)]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
   ```

1. Skapa två fler klasser: **Hotel.Methods.cs** och **address.Methods.cs** för toString () åsidosättningar. Dessa klasser används för att återge Sök resultat i konsolens utdata.  Innehållet i dessa klasser anges inte i den här artikeln, men du kan kopiera koden från [filer i GitHub](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11/AzureSearchQuickstart-v11).

1. I **program.cs** skapar du ett [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) -objekt och anropar sedan [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) -metoden för att uttrycka indexet i din Sök tjänst. Indexet innehåller också en [SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester) för att aktivera Autoavsluta för de angivna fälten.

   ```csharp
    // Create hotels-quickstart index
    private static void CreateIndex(string indexName, SearchIndexClient adminClient)
    {
        FieldBuilder fieldBuilder = new FieldBuilder();
        var searchFields = fieldBuilder.Build(typeof(Hotel));

        var definition = new SearchIndex(indexName, searchFields);

        var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
        definition.Suggesters.Add(suggester);

        adminClient.CreateOrUpdateIndex(definition);
    }
   ```

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 Läs in dokument

Azure Kognitiv sökning söker efter innehåll som lagras i tjänsten. I det här steget ska du läsa in JSON-dokument som stämmer överens med det hotell index som du nyss skapade.

I Azure Kognitiv sökning är Sök dokument data strukturer som båda är indata för indexering och utdata från frågor. Som hämtas från en extern data källa kan dokument indata vara rader i en databas, blobbar i blob-lagring eller JSON-dokument på disk. I det här exemplet ska vi ta en genväg och bädda in JSON-dokument för fyra hotell i själva koden. 

När du överför dokument måste du använda ett [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) -objekt. Ett `IndexDocumentsBatch` -objekt innehåller en samling [åtgärder](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions)som innehåller ett dokument och en egenskap som talar om för Azure kognitiv sökning vilken åtgärd som ska utföras ([Ladda upp, sammanfoga, ta bort och mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. I **program.cs** skapar du en matris med dokument-och index åtgärder och skickar sedan matrisen till `IndexDocumentsBatch` . Dokumenten nedan följer hotell-snabb starts indexet, som definieras av hotellet-klassen.

    ```csharp
    // Upload documents in a single Upload request.
    private static void UploadDocuments(SearchClient searchClient)
    {
        IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.60,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                })
            );

        try
        {
            IndexDocumentsResult result = searchClient.IndexDocuments(batch);
        }
        catch (Exception)
        {
            // If for some reason any documents are dropped during indexing, you can compensate by delaying and
            // retrying. This simple demo just logs the failed document keys and continues.
            Console.WriteLine("Failed to index some of the documents: {0}");
        }
    }
    ```

    När du har initierat [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) -objektet kan du skicka det till indexet genom att anropa [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) på [SearchClient](/dotnet/api/azure.search.documents.searchclient) -objektet.

1. Lägg till följande rader i Main (). Inläsning av dokument görs med SearchClient, men åtgärden kräver även administratörs behörighet för tjänsten, som vanligt vis är kopplad till SearchIndexClient. Ett sätt att konfigurera den här åtgärden är att hämta SearchClient via SearchIndexClient (adminClient i det här exemplet).

   ```csharp
    SearchClient ingesterClient = adminClient.GetSearchClient(indexName);

    // Load documents
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(ingesterClient);
   ```

1. Eftersom det här är en konsol app som kör alla kommandon i tur och ordning, lägger du till en vänte tid på 2 sekunder mellan indexering och frågor.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    Den 2 sekunderade fördröjningen kompenserar för indexering, som är asynkron, så att alla dokument kan indexeras innan frågorna körs. Att koda i en fördröjning är vanligt vis bara nödvändigt i demonstrationer, tester och exempel program.

## <a name="3---search-an-index"></a>3 – Söka i ett index

Du kan få frågeresultat så snart det första dokumentet har indexerats, men den faktiska testningen av indexet ska vänta tills alla dokument har indexerats.

I det här avsnittet läggs två delar av funktionalitet: fråga efter logik och resultat. Använd [Sök](/dotnet/api/azure.search.documents.searchclient.search) metoden för frågor. Den här metoden tar Sök text (frågesträngen) och andra [alternativ](/dotnet/api/azure.search.documents.searchoptions).

[SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) -klassen representerar resultatet.

1. I **program.cs** skapar du en **WriteDocuments** -metod som skriver ut Sök resultat till-konsolen.

    ```csharp
    // Write search results to console
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.GetResults())
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Skapa en **RunQueries** -Metod för att köra frågor och returnera resultat. Resultaten är hotell objekt. Det här exemplet visar Metodsignaturen och den första frågan. Den här frågan visar den valda parametern som gör att du kan skapa resultatet med hjälp av markerade fält i dokumentet.

    ```csharp
    // Run queries, use WriteDocuments to print output
    private static void RunQueries(SearchClient srchclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on empty term '*' to return all documents, showing a subset of fields...\n");

        options = new SearchOptions()
        {
            IncludeTotalCount = true,
            Filter = "",
            OrderBy = { "" }
        };

        options.Select.Add("HotelId");
        options.Select.Add("HotelName");
        options.Select.Add("Address/City");

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    ```

1. I den andra frågan söker du efter en term, lägger till ett filter som väljer dokument där betyget är större än 4 och sorterar efter klassificering i fallande ordning. Filter är ett booleskt uttryck som utvärderas över [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) fält i ett index. Filtrera frågor, antingen inkludera eller exkludera värden. Därför finns det inga relevans-Poäng för en filter fråga. 

    ```csharp
    Console.WriteLine("Query #2: Search on 'hotels', filter on 'Rating gt 4', sort by Rating in descending order...\n");

    options = new SearchOptions()
    {
        Filter = "Rating gt 4",
        OrderBy = { "Rating desc" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Rating");

    response = srchclient.Search<Hotel>("hotels", options);
    WriteDocuments(response);
    ```

1. Den tredje frågan visar searchFields, som används för att begränsa en fullständig texts ökning till vissa fält.

    ```csharp
    Console.WriteLine("Query #3: Limit search to specific fields (pool in Tags field)...\n");

    options = new SearchOptions()
    {
        SearchFields = { "Tags" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Tags");

    response = srchclient.Search<Hotel>("pool", options);
    WriteDocuments(response);
    ```

1. Den fjärde frågan visar Faces, som kan användas för att strukturera en aspektad navigerings struktur. 

   ```csharp
    Console.WriteLine("Query #4: Facet on 'Category'...\n");

    options = new SearchOptions()
    {
        Filter = ""
    };

    options.Facets.Add("Category");

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Category");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
   ```

1. Returnera ett särskilt dokument i den femte frågan. En dokuments ökning är ett typiskt svar på händelsen OnClick i en resultat uppsättning.

   ```csharp
    Console.WriteLine("Query #5: Look up a specific document...\n");

    Response<Hotel> lookupResponse;
    lookupResponse = srchclient.GetDocument<Hotel>("3");

    Console.WriteLine(lookupResponse.Value.HotelId);
   ```

1. Den sista frågan visar syntaxen för Autoavsluta, som simulerar en partiell användar indata från "sa" som matchar två möjliga matchningar i sourceFields som är associerade med den förslags ställare som du definierade i indexet.

   ```csharp
    Console.WriteLine("Query #6: Call Autocomplete on HotelName that starts with 'sa'...\n");

    var autoresponse = srchclient.Autocomplete("sa", "sg");
    WriteDocuments(autoresponse);
   ```

1. Lägg till **RunQueries** i Main ().

    ```csharp
    // Call the RunQueries method to invoke a series of queries
    Console.WriteLine("Starting queries...\n");
    RunQueries(srchclient);

    // End the program
    Console.WriteLine("{0}", "Complete. Press any key to end this program...\n");
    Console.ReadKey();
    ```

Föregående frågor visar flera [olika sätt att matcha termer i en fråga](search-query-overview.md#types-of-queries): full texts ökning, filter och Autoavsluta.

Full texts ökning och filter utförs med hjälp av metoden [SearchClient. search](/dotnet/api/azure.search.documents.searchclient.search) . En Sök fråga kan skickas i `searchText` strängen, medan ett filter uttryck kan skickas i egenskapen [filter](/dotnet/api/azure.search.documents.searchoptions.filter) i klassen [SearchOptions](/dotnet/api/azure.search.documents.searchoptions) . Om du vill filtrera utan att söka behöver du bara skicka `"*"` för `searchText` parametern för [Sök](/dotnet/api/azure.search.documents.searchclient.search) metoden. Om du vill söka utan filtrering lämnar du `Filter` egenskapen unset eller skickar den inte i en `SearchOptions` instans alls.

## <a name="run-the-program"></a>Köra programmet

Tryck på F5 för att återskapa appen och köra programmet i sin helhet. 

Utdata innehåller meddelanden från [Console. WriteLine](/dotnet/api/system.console.writeline), med tillägget av frågans information och resultat.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

I den här snabb starten av C# arbetade du med en uppsättning aktiviteter för att skapa ett index, läsa in det med dokument och köra frågor. I olika steg tog vi genvägar för att förenkla koden för läsbarhet och förståelse. Om du är van vid de grundläggande begreppen rekommenderar vi nästa artikel för en utforskning av alternativa metoder och koncept som kommer att fördjupa dina kunskaper. 

> [!div class="nextstepaction"]
> [Utveckla i .NET](search-howto-dotnet-sdk.md)

Vill du optimera och Spara på dina moln utgifter?

> [!div class="nextstepaction"]
> [Börja analysera kostnaderna med Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
