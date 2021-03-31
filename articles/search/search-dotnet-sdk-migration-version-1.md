---
title: Uppgradera till Azure Search .NET SDK version 1,1
titleSuffix: Azure Cognitive Search
description: Migrera kod till Azure Search .NET SDK version 1,1 från äldre API-versioner. Lär dig vad som är nytt och vilka kod ändringar som krävs.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 4860918fc4f995ad267fc35b142d3fcb460ce177
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89002835"
---
# <a name="upgrade-to-azure-search-net-sdk-version-11"></a>Uppgradera till Azure Search .NET SDK version 1,1

Om du använder version 1.0.2 eller äldre av [Azure Search .NET SDK](/dotnet/api/overview/azure/search)hjälper den här artikeln dig att uppgradera ditt program till att använda version 1,1.

En mer allmän genom gång av SDK inklusive exempel finns i [så här använder du Azure Search från ett .NET-program](search-howto-dotnet-sdk.md).

> [!NOTE]
> När du har uppgraderat till version 1,1, eller om du redan använder en version mellan 1,1 och 2,0-för hands version, bör du uppgradera till version 3. Instruktioner finns i [Uppgradera till Azure Search .NET SDK version 3](search-dotnet-sdk-migration.md) .
>

Först uppdaterar du din NuGet-referens för `Microsoft.Azure.Search` med hjälp av NuGet Package Manager-konsolen eller genom att högerklicka på dina projekt referenser och välja "hantera NuGet-paket..." i Visual Studio.

När NuGet har laddat ned de nya paketen och deras beroenden kan du återskapa projektet.

Om du tidigare använde version 1.0.0 – för hands version, 1.0.1-Preview eller 1.0.2-Preview bör versionen lyckas och du är redo att sätta igång!

Om du tidigare använde version 0.13.0 – Preview eller äldre bör du se build-fel som följande:

```output
Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)
```

Nästa steg är att åtgärda build-felen en i taget. De flesta kräver att vissa klass-och metod namn ändras som har bytt namn i SDK. [Lista över ändringar i version 1,1](#ListOfChangesV1) innehåller en lista över dessa namn ändringar.

Om du använder anpassade klasser för att modellera dina dokument och dessa klasser har egenskaper för primitiva typer som inte är null-typer (t. ex. `int` eller `bool` i C#), finns en fel korrigering i 1,1-versionen av SDK: n som du bör vara medveten om. Mer information finns i [fel korrigeringar i version 1,1](#BugFixesV1) .

Slutligen, när du har åtgärdat eventuella build-fel, kan du göra ändringar i programmet för att dra nytta av nya funktioner om du vill.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Lista över överbrytande ändringar i version 1,1
I följande lista ordnas sannolikheten för att ändringen ska påverka program koden.

### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch-och IndexAction-ändringar
`IndexBatch.Create` har bytt namn till `IndexBatch.New` och har inte längre något `params` argument. Du kan använda `IndexBatch.New` för batchar som blandar olika typer av åtgärder (sammanslagningar, borttagningar osv.). Dessutom finns det nya statiska metoder för att skapa batchar där alla åtgärder är desamma: `Delete` , `Merge` , `MergeOrUpload` och `Upload` .

`IndexAction` har inte längre några offentliga konstruktorer och dess egenskaper är nu oföränderliga. Du bör använda de nya statiska metoderna för att skapa åtgärder för olika syfte: `Delete` ,, `Merge` `MergeOrUpload` och `Upload` . `IndexAction.Create` har tagits bort. Om du har använt överlagringen som bara tar ett dokument ska du se till att använda `Upload` i stället.

#### <a name="example"></a>Exempel
Om din kod ser ut så här:

```csharp
var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
indexClient.Documents.Index(batch);
```

Du kan ändra den till detta för att åtgärda eventuella build-fel:

```csharp
var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
indexClient.Documents.Index(batch);
```

Om du vill kan du ytterligare förenkla det med:

```csharp
var batch = IndexBatch.Upload(documents);
indexClient.Documents.Index(batch);
```

### <a name="indexbatchexception-changes"></a>IndexBatchException ändringar
`IndexBatchException.IndexResponse`Egenskapen har bytt namn till `IndexingResults` och dess typ är nu `IList<IndexingResult>` .

#### <a name="example"></a>Exempel
Om din kod ser ut så här:

```csharp
catch (IndexBatchException e)
{
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
}
```

Du kan ändra den till detta för att åtgärda eventuella build-fel:

```csharp
catch (IndexBatchException e)
{
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}
```

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Ändringar i åtgärds metod
Varje åtgärd i Azure Search .NET SDK visas som en uppsättning metod överlagringar för synkrona och asynkrona anropare. Signaturerna och factoring för dessa metod överföringar har ändrats i version 1,1.

Till exempel exponeras åtgärden "Hämta index statistik" i äldre versioner av SDK för följande signaturer:

Följande gäller i `IIndexOperations`:

```csharp
// Asynchronous operation with all parameters
Task<IndexGetStatisticsResponse> GetStatisticsAsync(
    string indexName,
    CancellationToken cancellationToken);
```

Följande gäller i `IndexOperationsExtensions`:

```csharp
// Asynchronous operation with only required parameters
public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
    this IIndexOperations operations,
    string indexName);

// Synchronous operation with only required parameters
public static IndexGetStatisticsResponse GetStatistics(
    this IIndexOperations operations,
    string indexName);
```

Metoden signaturer för samma åtgärd i version 1,1 ser ut så här:

Följande gäller i `IIndexesOperations`:

```csharp
// Asynchronous operation with lower-level HTTP features exposed
Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
    string indexName,
    SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
    Dictionary<string, List<string>> customHeaders = null,
    CancellationToken cancellationToken = default(CancellationToken));
```

Följande gäller i `IndexesOperationsExtensions`:

```csharp
// Simplified asynchronous operation
public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
    this IIndexesOperations operations,
    string indexName,
    SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
    CancellationToken cancellationToken = default(CancellationToken));

// Simplified synchronous operation
public static IndexGetStatisticsResult GetStatistics(
    this IIndexesOperations operations,
    string indexName,
    SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));
```

Från och med version 1,1 organiserar Azure Search .NET SDK olika åtgärds metoder på olika sätt:

* Valfria parametrar modelleras nu som standard parametrar snarare än ytterligare metod överbelastningar. Detta minskar antalet metod överföringar, ibland dramatiskt.
* Tilläggs metoderna döljer nu en stor del av den extra informationen om HTTP från anroparen. Till exempel returnerade äldre versioner av SDK ett Response-objekt med en HTTP-statuskod som du ofta inte behövde kontrol lera eftersom åtgärds metoder returnerar `CloudException` status kod som anger ett fel. De nya tilläggs metoderna returnerar bara modell objekt, och du slipper att packa upp dem i din kod.
* Huvud gränssnitten visar nu metoder som ger dig mer kontroll på HTTP-nivå om du behöver det. Nu kan du skicka in anpassade HTTP-huvuden som ska ingå i begär Anden och den nya `AzureOperationResponse<T>` RETUR typen ger direkt åtkomst till `HttpRequestMessage` och `HttpResponseMessage` för åtgärden. `AzureOperationResponse` definieras i `Microsoft.Rest.Azure` namn området och ersätts `Hyak.Common.OperationResponse` .

### <a name="scoringparameters-changes"></a>ScoringParameters ändringar
En ny klass med namnet `ScoringParameter` har lagts till i den senaste SDK: n för att göra det enklare att tillhandahålla parametrar för att lägga till profiler i en Sök fråga. Tidigare har `ScoringProfiles` egenskapen för `SearchParameters` klassen angetts som `IList<string>` ; Nu har den angetts som `IList<ScoringParameter>` .

#### <a name="example"></a>Exempel
Om din kod ser ut så här:

```csharp
var sp = new SearchParameters();
sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };
```

Du kan ändra den till detta för att åtgärda eventuella build-fel: 

```csharp
var sp = new SearchParameters();
sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
sp.ScoringParameters =
    new[]
    {
        new ScoringParameter("featuredParam", new[] { "featured" }),
        new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
    };
```

### <a name="model-class-changes"></a>Ändringar i modell klass
På grund av ändringar i signaturen som beskrivs i [Åtgärds metod ändringar](#OperationMethodChanges), har många klasser i `Microsoft.Azure.Search.Models` namn området bytt namn eller tagits bort. Exempel:

* `IndexDefinitionResponse` har ersatts av `AzureOperationResponse<Index>`
* `DocumentSearchResponse` har bytt namn till `DocumentSearchResult`
* `IndexResult` har bytt namn till `IndexingResult`
* `Documents.Count()` returnerar nu en `long` med antalet dokument i stället för en `DocumentCountResponse`
* `IndexGetStatisticsResponse` har bytt namn till `IndexGetStatisticsResult`
* `IndexListResponse` har bytt namn till `IndexListResult`

För att summera, `OperationResponse` Härledda klasser som existerade bara för att omsluta ett modell objekt har tagits bort. Deras suffix har ändrats från till för de återstående klasserna `Response` `Result` .

#### <a name="example"></a>Exempel
Om din kod ser ut så här:

```csharp
IndexerGetStatusResponse statusResponse = null;

try
{
    statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
}
catch (Exception ex)
{
    Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
    return;
}

IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;
```

Du kan ändra den till detta för att åtgärda eventuella build-fel:

```csharp
IndexerExecutionInfo status = null;

try
{
    status = _searchClient.Indexers.GetStatus(indexer.Name);
}
catch (Exception ex)
{
    Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
    return;
}

IndexerExecutionResult lastResult = status.LastResult;
```

#### <a name="response-classes-and-ienumerable"></a>Svars klasser och IEnumerable
En ytterligare ändring som kan påverka din kod är att svars klasser som innehåller samlingar inte längre implementeras `IEnumerable<T>` . I stället kan du komma åt egenskapen Collection direkt. Om koden till exempel ser ut så här:

```csharp
DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
foreach (SearchResult<Hotel> result in response)
{
    Console.WriteLine(result.Document);
}
```

Du kan ändra den till detta för att åtgärda eventuella build-fel:

```csharp
DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
foreach (SearchResult<Hotel> result in response.Results)
{
    Console.WriteLine(result.Document);
}
```

#### <a name="special-case-for-web-applications"></a>Specialfall för webb program
Om du har ett webb program som serialiserar `DocumentSearchResponse` direkt för att skicka Sök resultat till webbläsaren, måste du ändra koden, annars kommer resultatet inte att serialiseras korrekt. Om koden till exempel ser ut så här:

```csharp
public ActionResult Search(string q = "")
{
    // If blank search, assume they want to search everything
    if (string.IsNullOrWhiteSpace(q))
        q = "*";

    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = _featuresSearch.Search(q)
    };
}
```

Du kan ändra det genom att hämta `.Results` egenskapen för Sök svaret för att korrigera Sök Resultat:

```csharp
public ActionResult Search(string q = "")
{
    // If blank search, assume they want to search everything
    if (string.IsNullOrWhiteSpace(q))
        q = "*";

    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = _featuresSearch.Search(q).Results
    };
}
```

Du kommer att behöva leta efter sådana fall i din kod själv. **Kompilatorn kommer inte att varna dig** eftersom `JsonResult.Data` är av typen `object` .

### <a name="cloudexception-changes"></a>CloudException ändringar
`CloudException`Klassen har flyttats från `Hyak.Common` namn området till `Microsoft.Rest.Azure` namn området. `Error`Egenskapen har också bytt namn till `Body` .

### <a name="searchserviceclient-and-searchindexclient-changes"></a>SearchServiceClient-och SearchIndexClient-ändringar
`Credentials`Egenskaps typen har ändrats från `SearchCredentials` till dess basklass, `ServiceClientCredentials` . `SearchCredentials` `SearchIndexClient` `SearchServiceClient` Använd den nya egenskapen om du behöver åtkomst till en eller `SearchCredentials` .

I äldre versioner av SDK `SearchServiceClient` och `SearchIndexClient` hade konstruktorer som använde en `HttpClient` parameter. Dessa har ersatts av konstruktorer som tar en `HttpClientHandler` och en matris med `DelegatingHandler` objekt. Detta gör det enklare att installera anpassade hanterare för att i förväg bearbeta HTTP-begäranden vid behov.

Slutligen har konstruktörerna som tog en `Uri` och `SearchCredentials` ändrats. Om du till exempel har kod som ser ut så här:

```csharp
var client =
    new SearchServiceClient(
        new SearchCredentials("abc123"),
        new Uri("http://myservice.search.windows.net"));
```

Du kan ändra den till detta för att åtgärda eventuella build-fel:

```csharp
var client =
    new SearchServiceClient(
        new Uri("http://myservice.search.windows.net"),
        new SearchCredentials("abc123"));
```

Observera också att typen för parametern credentials har ändrats till `ServiceClientCredentials` . Detta är osannolikt att påverka koden eftersom den `SearchCredentials` härleds från `ServiceClientCredentials` .

### <a name="passing-a-request-id"></a>Skickar ett fråge-ID
I äldre versioner av SDK kan du ange ett ID för begäran på `SearchServiceClient` eller `SearchIndexClient` och det ska inkluderas i varje begäran till REST API. Detta är användbart för fel sökning av problem med din Sök tjänst om du behöver kontakta supporten. Det är dock mer användbart att ange ett unikt ID för begäran för varje åtgärd i stället för att använda samma ID för alla åtgärder. Av den anledningen är `SetClientRequestId` metoderna för `SearchServiceClient` och `SearchIndexClient` har tagits bort. I stället kan du skicka ett fråge-ID till varje åtgärds metod via den valfria `SearchRequestOptions` parametern.

> [!NOTE]
> I en framtida version av SDK lägger vi till en ny mekanism för att ställa in ett fråge-ID globalt på klient objekt som är konsekvent med den metod som används av andra Azure-SDK: er.
> 
> 

### <a name="example"></a>Exempel
Om du har kod som ser ut så här:

```csharp
client.SetClientRequestId(Guid.NewGuid());
...
long count = client.Documents.Count();
```

Du kan ändra den till detta för att åtgärda eventuella build-fel:

```csharp
long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));
```

### <a name="interface-name-changes"></a>Ändringar av gränssnitts namn
Namn på åtgärds grupp gränssnitt har alla ändrats så att de överensstämmer med deras motsvarande egenskaps namn:

* Typen för `ISearchServiceClient.Indexes` har fått ett nytt namn från `IIndexOperations` till `IIndexesOperations` .
* Typen för `ISearchServiceClient.Indexers` har fått ett nytt namn från `IIndexerOperations` till `IIndexersOperations` .
* Typen för `ISearchServiceClient.DataSources` har fått ett nytt namn från `IDataSourceOperations` till `IDataSourcesOperations` .
* Typen för `ISearchIndexClient.Documents` har fått ett nytt namn från `IDocumentOperations` till `IDocumentsOperations` .

Den här ändringen påverkar inte din kod om du inte har skapat några modeller av dessa gränssnitt i test syfte.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Fel korrigeringar i version 1,1
Det uppstod ett fel i äldre versioner av Azure Search .NET SDK som rör serialisering av anpassade modell klasser. Felet kan uppstå om du har skapat en anpassad modell klass med en egenskap av en värdetyp som inte kan ha värdet null.

### <a name="steps-to-reproduce"></a>Steg för att återskapa
Skapa en anpassad modell klass med en egenskap av Värdetyp som inte kan ha värdet null. Lägg till exempel till en offentlig `UnitCount` egenskap av typen `int` i stället för `int?` .

Om du indexerar ett dokument med standardvärdet av den typen (till exempel 0 för `int` ) kommer fältet att vara null i Azure Search. Om du senare söker efter dokumentet `Search` kommer anropet att utlösa `JsonSerializationException` klagande att det inte kan konverteras `null` till `int` .

Filter kan inte heller fungera som förväntat eftersom null skrevs till indexet i stället för det avsedda värdet.

### <a name="fix-details"></a>Åtgärda information
Vi har åtgärdat det här problemet i version 1,1 av SDK. Om du nu har en modell klass så här:

```csharp
public class Model
{
    public string Key { get; set; }

    public int IntValue { get; set; }
}
```

och om du anger `IntValue` till 0 serialiseras det värdet nu korrekt som 0 i kabeln och lagras som 0 i indexet. Round Tripping fungerar också som förväntat.

Det finns ett möjligt problem att känna till med den här metoden: om du använder en modell typ med en egenskap som inte kan ha värdet null måste du **garantera** att inga dokument i indexet innehåller ett null-värde för motsvarande fält. Varken SDK eller Azure Search REST API hjälper dig att genomdriva detta.

Detta är inte bara ett hypotetiskt problem. Tänk dig ett scenario där du lägger till ett nytt fält till ett befintligt index som är av typen `Edm.Int32`. När du har uppdaterat indexdefinitionen har alla dokument ett null-värde för det nya fältet (eftersom alla typer kan vara null i Azure Search). Om du sedan använder en modellklass med en icke-nullbar `int`-egenskap för det fältet returneras ett `JsonSerializationException` som detta när du försöker hämta dokument:

```output
Error converting value {null} to type 'System.Int32'. Path 'IntValue'.
```

Därför rekommenderar vi fortfarande att du använder Nullable-typer i dina modell klasser som bästa praxis.

Mer information om det här felet och korrigeringen finns i [det här problemet på GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).