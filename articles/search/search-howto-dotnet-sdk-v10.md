---
title: Använd Microsoft. Azure. search (v10) i .NET
titleSuffix: Azure Cognitive Search
description: Lär dig hur du skapar och hanterar Sök objekt i ett .NET-program med C# och Microsoft. Azure. search (version 10) av .NET SDK. Kodfragment visar hur du ansluter till tjänsten, skapar index och frågor.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 11102d95bc2aba65e6bc3cba71805a67f195947b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92681719"
---
# <a name="how-to-use-microsoftazuresearch-in-a-c-net-application"></a>Använda Microsoft. Azure. search i ett C# .NET-program

Den här artikeln beskriver hur du skapar och hanterar Sök objekt med C# och det äldre klient biblioteket, [**Microsoft. Azure. search**](/dotnet/api/overview/azure/search/client10) (version 10) i Azure SDK för .net. 

Version 10 är den senaste versionen av paketet Microsoft. Azure. search. Om du flyttar framåt kommer nya funktioner att distribueras i [**Azure.Search.Documents**](/dotnet/api/overview/azure/search.documents-readme) från Azure SDK-teamet.

> [!NOTE]
> Om du har befintliga eller synlighetssekvensnummer utvecklings projekt kan du fortsätta att använda version 10. För nya projekt, eller för att använda nya funktioner, ska du gå över till det [nya biblioteket](/dotnet/api/overview/azure/search.documents-readme).

## <a name="about-version-10"></a>Om version 10

SDK består av ett fåtal klient bibliotek som gör att du kan hantera dina index, data källor, indexerare och synonyma kartor, samt överföra och hantera dokument och köra frågor, allt utan att behöva hantera informationen om HTTP och JSON. Dessa klient bibliotek är alla distribuerade som NuGet-paket.

Det huvudsakliga NuGet-paketet är `Microsoft.Azure.Search` , som är ett meta-paket som innehåller alla andra paket som beroenden. Använd det här paketet om du precis har kommit igång eller om du vet att programmet behöver alla funktioner i Azure Kognitiv sökning.

De andra NuGet-paketen i SDK: n är:
 
  - `Microsoft.Azure.Search.Data`: Använd det här paketet om du utvecklar ett .NET-program med Azure Kognitiv sökning och du bara behöver fråga eller uppdatera dokument i dina index. Om du också behöver skapa eller uppdatera index, synonym mappningar eller andra resurser på tjänst nivå använder du `Microsoft.Azure.Search` paketet i stället.
  - `Microsoft.Azure.Search.Service`: Använd det här paketet om du utvecklar automatisering i .NET för att hantera Azure Kognitiv sökning-index, synonym kartor, indexerare, data källor eller andra resurser på tjänst nivå. Om du bara behöver fråga eller uppdatera dokument i dina index använder du `Microsoft.Azure.Search.Data` paketet i stället. Om du behöver alla funktioner i Azure Kognitiv sökning använder du `Microsoft.Azure.Search` paketet i stället.
  - `Microsoft.Azure.Search.Common`: Vanliga typer som krävs av Azure Kognitiv sökning .NET-biblioteken. Du behöver inte använda det här paketet direkt i ditt program. Den är endast avsedd att användas som ett beroende.

De olika klient biblioteken definierar klasser som `Index` , `Field` och `Document` , samt åtgärder som `Indexes.Create` och `Documents.Search` i `SearchServiceClient` `SearchIndexClient` klasserna och. Dessa klasser är indelade i följande namn rymder:

* [Microsoft. Azure. search](/dotnet/api/microsoft.azure.search)
* [Microsoft. Azure. search. Models](/dotnet/api/microsoft.azure.search.models)

Om du vill ge feedback om en kommande uppdatering av SDK: n, se vår [feedback-sida](https://feedback.azure.com/forums/263029-azure-search/) eller skapa ett problem på [GitHub](https://github.com/azure/azure-sdk-for-net/issues) och nämna "Azure kognitiv sökning" i ärende titeln.

.NET SDK mål versionen [ `2019-05-06` av Azure kognitiv sökning REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/data-plane/Microsoft.Azure.Search.Data/stable/2019-05-06). Den här versionen innehåller stöd för [komplexa typer](search-howto-complex-data-types.md), [AI-anrikning](cognitive-search-concept-intro.md), [komplettering](/rest/api/searchservice/autocomplete)och [JsonLines tolknings läge](search-howto-index-json-blobs.md) vid indexering av Azure-blobar. 

Detta SDK stöder inte [hanterings åtgärder](/rest/api/searchmanagement/) som att skapa och skala Sök tjänster och hantera API-nycklar. Om du behöver hantera dina Sök resurser från ett .NET-program kan du använda Azure- [kognitiv sökning .net Management SDK](/dotnet/api/overview/azure/search/management).

## <a name="upgrade-to-v10"></a>Uppgradera till v10
Om du redan använder en äldre version av Azure Kognitiv sökning .NET SDK och du vill uppgradera till den senaste allmänt tillgängliga versionen, förklarar [den här artikeln](search-dotnet-sdk-migration-version-9.md) hur.

## <a name="sdk-requirements"></a>Krav för SDK
1. Visual Studio 2017 eller senare.
2. Din egen Azure Kognitiv sökning-tjänst. För att du ska kunna använda SDK behöver du namnet på din tjänst och en eller flera API-nycklar. [Skapa en tjänst i portalen för](search-create-service-portal.md) att hjälpa dig med de här stegen.
3. Hämta Azure Kognitiv sökning .NET SDK [NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Search) med hjälp av "hantera NuGet-paket" i Visual Studio. Sök bara efter paket namnet `Microsoft.Azure.Search` på NuGet.org (eller något av de andra paket namnen ovan om du bara behöver en delmängd av funktionerna).

Azure Kognitiv sökning .NET SDK stöder program som riktar sig mot .NET Framework 4.5.2 och högre, samt .NET Core 2,0 och högre.

## <a name="core-scenarios"></a>Kärn scenarier
Det finns flera saker du behöver göra i sökprogrammet. I den här självstudien tar vi upp följande viktiga scenarier:

* Skapa ett index
* Fylla i indexet med dokument
* Söka efter dokument med full texts ökning och filter

Följande exempel kod visar var och en av dessa scenarier. Använd kodfragmenten i ditt eget program.

### <a name="overview"></a>Översikt
Exempel programmet som vi kommer att utforska skapar ett nytt index med namnet "Hotels", fyller det med några dokument och kör sedan vissa Sök frågor. Här är huvud programmet som visar det övergripande flödet:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> Du hittar hela källkoden för exempelprogrammet i den här genomgången på [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).
> 
>

Vi går igenom det här steget genom att gå igenom det här steget. Först måste vi skapa en ny `SearchServiceClient` . Med det här objektet kan du hantera index. För att kunna skapa en måste du ange namnet på din Azure Kognitiv sökning-tjänst och en administratörs-API-nyckel. Du kan ange den här informationen i `appsettings.json` filen i [exempel programmet](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> Om du anger en felaktig nyckel (till exempel en frågegrupp där en administratörs nyckel krävdes) kommer att orsaka `SearchServiceClient` `CloudException` fel meddelandet "förbjuden" första gången du anropar en åtgärds metod, till exempel `Indexes.Create` . Om det händer kan du kontrol lera vår API-nyckel.
> 
> 

Efterföljande rader anropar metoder för att skapa ett index med namnet "Hotels", ta bort det först om det redan finns. Vi går igenom dessa metoder lite senare.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Sedan måste indexet fyllas i. För att kunna fylla i indexet behöver vi en `SearchIndexClient` . Det finns två sätt att hämta ett: genom att konstruera det eller genom att anropa `Indexes.GetClient` `SearchServiceClient` . Vi använder det senare för enkelhetens skull.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> I ett typiskt sökprogram kan index hantering och ifyllning hanteras av en separat komponent från Sök frågor. `Indexes.GetClient` är praktiskt för att fylla ett index eftersom det sparar problem med att tillhandahålla ytterligare `SearchCredentials` . Den gör det genom att skicka administratörsnyckeln som du använde för att skapa `SearchServiceClient` till den nya `SearchIndexClient`. Men i den del av ditt program som kör frågor, är det bättre att skapa `SearchIndexClient` direkt så att du kan skicka in en frågegrupp, som bara tillåter att du läser data, i stället för en administratörs nyckel. Den här riktlinjen följer principen om lägsta behörighet och hjälper till att göra programmet säkrare. Du kan läsa mer om administratörs nycklar och frågeinställningar [här](/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Nu när vi har en kan `SearchIndexClient` vi fylla i indexet. Index ifyllning görs med en annan metod som vi kommer att gå igenom senare.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Slutligen kör vi några Sök frågor och visar resultatet. Den här gången använder vi en annan `SearchIndexClient` :

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Vi tar en närmare titt på `RunQueries` metoden senare. Här är koden för att skapa den nya `SearchIndexClient` :

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Den här gången använder vi en sessionsnyckel eftersom vi inte behöver skriv åtkomst till indexet. Du kan ange den här informationen i `appsettings.json` filen i [exempel programmet](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Om du kör det här programmet med ett giltigt tjänst namn och API-nycklar bör utdata se ut som i följande exempel: (vissa konsolers utdata har ersatts med "..." för illustrations syfte.)

```output

Deleting index...

Creating index...

Uploading documents...

Waiting for documents to be indexed...

Search the entire index for the term 'motel' and return only the HotelName field:

Name: Secret Point Motel

Name: Twin Dome Motel


Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

HotelId: 1
Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

HotelId: 2
Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Triple Landscape Hotel
Last renovated on: 9/20/2015 12:00:00 AM +00:00

Name: Twin Dome Motel
Last renovated on: 2/18/1979 12:00:00 AM +00:00


Search the hotel names for the term 'hotel':

HotelId: 3
Name: Triple Landscape Hotel
...

Complete.  Press any key to end application... 
```

Den fullständiga käll koden för programmet finns i slutet av den här artikeln.

Därefter tar vi en närmare titt på var och en av de metoder som anropas av `Main` .

### <a name="creating-an-index"></a>Skapa ett index
När `SearchServiceClient` du har skapat en `Main` tar bort indexet "Hotels" om det redan finns. Borttagningen görs på följande sätt:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

I den här metoden används det `SearchServiceClient` för att kontrol lera om indexet finns och ta bort det.

> [!NOTE]
> I exempel koden i den här artikeln används de synkrona metoderna i Azure Kognitiv sökning .NET SDK för enkelhetens skull. Vi rekommenderar att du använder asynkrona metoder i dina egna program så att de blir skalbara och responsiva. I metoden ovan kan du till exempel använda och i `ExistsAsync` `DeleteAsync` stället för `Exists` och `Delete` .
> 
> 

Skapar sedan `Main` ett nytt "Hotels"-index genom att anropa den här metoden:

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

Den här metoden skapar ett nytt `Index` objekt med en lista med `Field` objekt som definierar schemat för det nya indexet. Varje fält har ett namn, en datatyp och flera attribut som definierar Sök beteendet. `FieldBuilder`Klassen använder reflektion för att skapa en lista med `Field` objekt för indexet genom att undersöka de offentliga egenskaperna och attributen för den aktuella `Hotel` modell klassen. Vi tar en närmare titt på `Hotel` klassen senare.

> [!NOTE]
> Du kan alltid skapa en lista med `Field` objekt direkt i stället för att använda `FieldBuilder` vid behov. Till exempel kanske du inte vill använda en modell klass eller så kan du behöva använda en befintlig modell klass som du inte vill ändra genom att lägga till attribut.
>
> 

Förutom fält kan du också lägga till bedömnings profiler, förslags alternativ eller CORS-alternativ till indexet (dessa parametrar utelämnas från exemplet för det kortfattat). Du hittar mer information om index-objektet och dess komponent delar i SDK- [referensen](/dotnet/api/microsoft.azure.search.models.index), samt i [REST API referens för Azure kognitiv sökning](/rest/api/searchservice/).

### <a name="populating-the-index"></a>Fyller indexet
Nästa steg i `Main` fyller på det nyligen skapade indexet. Den här index populationen görs i följande metod: (en kod ersätts med "..." i illustrations syfte.  Se den fullständiga exempel lösningen för fullständig data populations kod.)

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
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
        },
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
        },
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

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

Den här metoden har fyra delar. Den första skapar en matris med 3 `Hotel` objekt var och en med 3 `Room` objekt som fungerar som vår indata för överföring till indexet. Dessa data är hårdkodade för enkelhetens skull. I ditt eget program kommer dina data troligen att komma från en extern data källa, till exempel en SQL-databas.

Den andra delen skapar en `IndexBatch` som innehåller dokumenten. Du anger den åtgärd som du vill använda för batchen vid den tidpunkt då du skapar den, i det här fallet genom att anropa `IndexBatch.Upload` . Gruppen överförs sedan till Azure Kognitiv sökning-indexet med `Documents.Index` metoden.

> [!NOTE]
> I det här exemplet laddar vi bara upp dokument. Om du vill sammanfoga ändringar i befintliga dokument eller ta bort dokument kan du skapa batchar genom att anropa `IndexBatch.Merge` , `IndexBatch.MergeOrUpload` eller `IndexBatch.Delete` i stället. Du kan också blanda olika åtgärder i en enda batch genom att anropa `IndexBatch.New` , som tar en samling med `IndexAction` objekt, där vart och ett meddelar Azure kognitiv sökning att utföra en viss åtgärd på ett dokument. Du kan skapa var `IndexAction` och en med en egen åtgärd genom att anropa motsvarande metod som `IndexAction.Merge` , `IndexAction.Upload` och så vidare.
> 
> 

Den tredje delen av den här metoden är ett catch-block som hanterar ett viktigt fel fall för indexering. Om din Azure Kognitiv sökning-tjänst inte kan indexera vissa av dokumenten i batchen, `IndexBatchException` genereras en av `Documents.Index` . Detta undantag kan inträffa om du indexerar dokument medan tjänsten är hårt belastad. **Vi rekommenderar starkt att du uttryckligen hanterar den här situationen i din kod.** Du kan fördröja och sedan försöka indexera dokumentet som misslyckades igen eller så kan du logga och fortsätta som i exemplet, eller göra något annat beroende på programmets krav på datakonsekvens.

> [!NOTE]
> Du kan använda [`FindFailedActionsToRetry`](/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) metoden för att skapa en ny batch som bara innehåller de åtgärder som misslyckats i ett tidigare anrop till `Index` . Det finns en diskussion om hur du använder den korrekt [på StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Slutligen `UploadDocuments` fördröjs metoden i två sekunder. Indexering sker asynkront i Azure Kognitiv sökning-tjänsten, så exempel programmet måste vänta en kort stund för att se till att dokumenten är tillgängliga för sökning. Fördröjningar som den här är normalt endast nödvändiga i demonstrationer, tester och exempelprogram.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Hur .NET SDK hanterar dokument
Du kanske undrar hur Azure Kognitiv sökning .NET SDK kan ladda upp instanser av en användardefinierad klass som `Hotel` index. För att hjälpa dig att besvara frågan ska vi titta på `Hotel` klassen:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsSearchable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.EnLucene)]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("Description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? Rating { get; set; }

    public Address Address { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    public Room[] Rooms { get; set; }
}
```

Det första du ska märka är att namnet på varje offentlig egenskap i `Hotel` klassen ska mappas till ett fält med samma namn i index definitionen. Om du vill att varje fält ska börja med en gemen bokstav ("kamel notation Case"), kan du se till att SDK: n mappar egenskaps namnen till kamel notation automatiskt med `[SerializePropertyNamesAsCamelCase]` attributet i klassen. Det här scenariot är vanligt i .NET-program som utför data bindning där mål schemat är utanför kontroll av programutvecklaren utan att behöva bryta mot namngivnings rikt linjerna för "Pascal Case" i .NET.

> [!NOTE]
> Azure Kognitiv sökning .NET SDK använder [NewtonSoft JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm) -biblioteket för att serialisera och deserialisera dina anpassade modell objekt till och från JSON. Du kan anpassa den här serialiseringen om det behövs. Mer information finns i [anpassad serialisering med JSON.net](#JsonDotNet).
> 
> 

Det andra är att märka att varje egenskap är dekorerad med attribut som `IsFilterable` , `IsSearchable` , `Key` och `Analyzer` . Dessa attribut mappar direkt till [motsvarande fältattribut i ett Azure kognitiv sökning-index](/rest/api/searchservice/create-index). `FieldBuilder`Klassen använder dessa egenskaper för att skapa fält definitioner för indexet.

Den tredje viktiga `Hotel` informationen om klassen är data typerna för de offentliga egenskaperna. .NET-typerna för dessa egenskaper mappar till deras motsvarande fälttyper i indexdefinitionen. Exempelvis mappar `Category`-strängegenskapen till `category`-fältet, som är av typen `Edm.String`. Det finns liknande typ mappningar mellan `bool?` , `Edm.Boolean` , `DateTimeOffset?` och `Edm.DateTimeOffset` så vidare. De speciella reglerna för typ mappningen finns dokumenterade med- `Documents.Get` metoden i [Azure KOGNITIV sökning .NET SDK-referensen](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). `FieldBuilder`Klassen tar hand om den här mappningen åt dig, men det kan fortfarande vara bra att förstå om du behöver felsöka eventuella problem med serialisering.

Har du meddelat `SmokingAllowed` egenskapen?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

`JsonIgnore`Attributet i den här egenskapen instruerar `FieldBuilder` att inte serialisera det till indexet som ett fält.  Detta är ett bra sätt att skapa beräknade egenskaper på klient sidan som du kan använda som hjälp program i ditt program.  I det här fallet `SmokingAllowed` Visar egenskapen om någon `Room` i `Rooms` samlingen tillåter rökning.  Om alla är falskt anger det att hela hotellet inte tillåter rökning.

Vissa egenskaper som `Address` och `Rooms` är instanser av .NET-klasser.  Dessa egenskaper representerar mer komplexa data strukturer och kräver därför fält med en [komplex datatyp](./search-howto-complex-data-types.md) i indexet.

`Address`Egenskapen representerar en uppsättning med flera värden i `Address` klassen, som definieras nedan:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Address
    {
        [IsSearchable]
        public string StreetAddress { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string City { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string StateProvince { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string PostalCode { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Country { get; set; }
    }
}
```

Den här klassen innehåller de standardvärden som används för att beskriva adresser i USA eller Kanada. Du kan använda typer som detta för att gruppera logiska fält tillsammans i indexet.

`Rooms`Egenskapen representerar en matris med `Room` objekt:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]
        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

Din data modell i .NET och dess motsvarande index schema bör utformas för att stödja den Sök upplevelse som du skulle vilja ge till din slutanvändare. Varje objekt på den översta nivån i .NET, IE-dokument i indexet motsvarar ett Sök resultat som du skulle presentera i användar gränssnittet. I ett hotell söknings program kan slutanvändarna exempelvis vilja söka efter hotell namn, funktioner på hotellet eller egenskaperna för ett visst rum. Vi kommer att få några exempel på frågor lite senare.

Den här möjligheten att använda egna klasser för att interagera med dokument i indexet fungerar i båda riktningarna. Du kan också hämta Sök Resultat och låta SDK: n deserialisera dem automatiskt till en typ som du väljer. vi kommer att se i nästa avsnitt.

> [!NOTE]
> Azure Kognitiv sökning .NET SDK stöder också dynamiskt skrivna dokument med hjälp av `Document` klassen, som är en nyckel/värde-mappning av fält namn till fält värden. Detta är användbart i scenarier då du inte känner till indexeringsschemat redan i designfasen, eller då det skulle vara opraktiskt att binda till specifika modellklasser. Alla metoder i SDK som hanterar dokument har överlagringar som fungerar med klassen `Document`, samt starkt typifierade överlagringar som använder en parameter av generisk typ. Endast de senare används i exempel koden i den här självstudien. [ `Document` Klassen](/dotnet/api/microsoft.azure.search.models.document) ärver från `Dictionary<string, object>` .
> 
>

**Varför du bör använda datatyper som kan ha värdet null**

När du skapar egna modell klasser som ska mappas till ett Azure Kognitiv sökning-index rekommenderar vi att du deklarerar egenskaper för värde typer som `bool` och kan `int` ha värdet null (t. ex. `bool?` i stället för `bool` ). Om du använder en icke-nullbar egenskap måste du **se till** att inga dokument i indexet innehåller ett null-värde för motsvarande fält. Varken SDK eller Azure Kognitiv sökning service hjälper dig att genomdriva detta.

Detta är inte bara ett hypotetiskt problem. Tänk dig ett scenario där du lägger till ett nytt fält till ett befintligt index som är av typen `Edm.Int32`. När index definitionen har uppdaterats har alla dokument ett null-värde för det nya fältet (eftersom alla typer kan ha värdet null i Azure Kognitiv sökning). Om du sedan använder en modellklass med en icke-nullbar `int`-egenskap för det fältet returneras ett `JsonSerializationException` som detta när du försöker hämta dokument:

```output
Error converting value {null} to type 'System.Int32'. Path 'IntValue'.
```

Av den anledningen rekommenderar vi att du använder nullbara typer i dina modellklasser som bästa praxis.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Anpassad serialisering med JSON.NET
SDK använder JSON.NET för serialisering och avserialisering av dokument. Du kan anpassa serialisering och deserialisering om det behövs genom att definiera egna `JsonConverter` eller `IContractResolver` . Mer information finns i JSON.NET- [dokumentationen](https://www.newtonsoft.com/json/help/html/Introduction.htm). Detta kan vara användbart när du vill anpassa en befintlig modell klass från programmet för användning med Azure Kognitiv sökning och andra fler avancerade scenarier. Med anpassad serialisering kan du till exempel:

* Ta med eller utelämna vissa egenskaper för din modell klass från att lagras som dokument fält.
* Mappa mellan egenskaps namn i kod-och fält namnen i ditt index.
* Skapa anpassade attribut som kan användas för att mappa egenskaper till dokument fält.

Du kan se exempel på implementering av anpassad serialisering i enhets testerna för Azure Kognitiv sökning .NET SDK på GitHub. En lämplig start punkt är [den här mappen](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models). Den innehåller klasser som används av anpassade serialiserings test.

### <a name="searching-for-documents-in-the-index"></a>Söker efter dokument i indexet
Det sista steget i exempel programmet är att söka efter vissa dokument i indexet:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

Varje gången den kör en fråga skapar den här metoden först ett nytt `SearchParameters` objekt. Det här objektet används för att ange ytterligare alternativ för frågan, till exempel sortering, filtrering, växling och fasettering. I den här metoden ställer vi in `Filter` egenskapen, `Select` , `OrderBy` , och `Top` för olika frågor. Alla `SearchParameters` egenskaper beskrivs [här](/dotnet/api/microsoft.azure.search.models.searchparameters).

Nästa steg är att köra Sök frågan. Att köra sökningen görs med hjälp av `Documents.Search` metoden. För varje fråga skickar vi Sök texten som ska användas som en sträng (eller `"*"` om det inte finns någon söktext) plus de Sök parametrar som skapades tidigare. Vi anger också `Hotel` som typ parameter för `Documents.Search` , vilket instruerar SDK att deserialisera dokument i Sök resultaten till objekt av typen `Hotel` .

> [!NOTE]
> Du hittar mer information om syntaxen för Sökfrågans uttryck [här](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Slutligen itererar den här metoden igenom alla matchningar i Sök resultaten och skriver ut varje dokument till-konsolen:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Låt oss ta en närmare titt på var och en av frågorna i sin tur. Här är koden för att köra den första frågan:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

I det här fallet söker vi igenom hela indexet efter ordet "Motel" i valfritt sökbart fält och vi vill bara hämta hotell namnen som anges i `Select` parametern. Här är resultaten:

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

Nästa fråga är lite mer intressant.  Vi vill hitta alla hotell som har ett rum med en natt taxa på mindre än $100 och bara returnera hotell-ID: t och en beskrivning:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Frågan använder ett OData `$filter` -uttryck `Rooms/any(r: r/BaseRate lt 100)` för att filtrera dokumenten i indexet. Detta använder [valfri operator](./search-query-odata-collection-operators.md) för att tillämpa "BaseRate lt 100" för alla objekt i samlingen rummen. Du kan läsa mer om OData-syntaxen som Azure Kognitiv sökning stöder [här](./query-odata-filter-orderby-syntax.md).

Här är resultatet av frågan:

```output
HotelId: 1
Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

HotelId: 2
Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...
```

Nu vill vi hitta de två första hotellen som har varit de senaste renovated och Visa hotell namnet och senaste renoverings datum. Här är koden: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

I det här fallet använder vi OData-syntaxen för att ange `OrderBy` parametern som `lastRenovationDate desc` . Vi har också angett `Top` till 2 för att se till att vi bara får de översta två dokumenten. Som tidigare angav vi `Select` för att ange vilka fält som ska returneras.

Här är resultaten:

```output
Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00
```

Slutligen vill vi hitta alla hotell namn som matchar ordet "hotell":

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

Här följer resultatet, som inkluderar alla fält eftersom vi inte angav `Select` egenskapen:

```output
    HotelId: 3
    Name: Triple Landscape Hotel
    ...
```

I det här steget Slutför du självstudierna, men stoppa inte här. * * Nästa steg innehåller ytterligare resurser för att lära dig mer om Azure Kognitiv sökning.

## <a name="next-steps"></a>Nästa steg
* Bläddra i referensinformationen till [.NET SDK](/dotnet/api/microsoft.azure.search) och [REST API](/rest/api/searchservice/).
* Granska [namn konventioner](/rest/api/searchservice/Naming-rules) för att lära dig reglerna för att namnge olika objekt.
* Granska [data typer som stöds](/rest/api/searchservice/Supported-data-types) i Azure kognitiv sökning.