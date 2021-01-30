---
title: C#-självstudie optimera indexering med push-API
titleSuffix: Azure Cognitive Search
description: Lär dig hur du effektivt indexerar data med Azure Kognitiv söknings push-API. Den här självstudien och exempel koden finns i C#.
manager: luisca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 1/29/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: f97a99bf2d055805ee665ab51aff8cff12dc5a69
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094200"
---
# <a name="tutorial-optimize-indexing-with-the-push-api"></a>Självstudie: optimera indexering med push-API

Azure Kognitiv sökning stöder [två grundläggande metoder](search-what-is-data-import.md) för att importera data till ett sökindex *: att* skicka data till indexet program mässigt eller att peka en [Azure kognitiv sökning-indexerare](search-indexer-overview.md) på en data källa som stöds för att *Hämta* data.

I den här självstudien beskrivs hur du effektivt indexerar data med [push-modellen](search-what-is-data-import.md#pushing-data-to-an-index) genom batching-begäranden och en exponentiell backoff-strategi för återförsök. Du kan [Hämta och köra exempel programmet](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing). I den här artikeln beskrivs viktiga aspekter av programmet och faktorer som du bör tänka på när du indexerar data.

I den här självstudien används C# och [.NET SDK](/dotnet/api/overview/azure/search) för att utföra följande uppgifter:

> [!div class="checklist"]
> * Skapa ett index
> * Testa olika batch-storlekar för att fastställa den mest effektiva storleken
> * Index-batchar asynkront
> * Använd flera trådar för att öka indexerings hastigheten
> * Använd en exponentiell backoff-strategi för återförsök för att försöka använda misslyckade dokument

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Följande tjänster och verktyg krävs för den här självstudien.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), vilken utgåva som helst. Exempel kod och instruktioner har testats i den kostnads fria community-versionen.

+ [Skapa en Azure kognitiv sökning-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration.

<a name="get-service-info"></a>

## <a name="download-files"></a>Ladda ned filer

Käll koden för den här självstudien finns i mappen [optimzize-data-Indexing/V11](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing/v11) i mappen [Azure-samples/Azure-Search-dotNet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) GitHub.

## <a name="key-considerations"></a>Viktiga överväganden

När du skickar data till ett index finns det flera viktiga överväganden som påverkar indexerings hastigheten. Du kan lära dig mer om dessa faktorer i [artikeln om stora data uppsättningar för index](search-howto-large-index.md).

Sex viktiga faktorer att överväga är:

+ **Tjänst nivån och antalet partitioner/repliker** – om du lägger till partitioner och ökar nivån ökar indexerings hastigheten.
+ **Index schema** – lägga till fält och lägga till ytterligare egenskaper till fält (till exempel sökbar, *aspektable* eller *filtrerings* *bara*) sänker både indexerings hastigheten.
+ **Batchstorlek** – den optimala batchstorleken varierar beroende på ditt index schema och data uppsättning.
+ **Antal trådar/arbetare** – en enda tråd drar inte full nytta av indexerings hastigheter
+ **Återförsöks strategi** – en exponentiell backoff-strategi för återförsök bör användas för att optimera indexeringen.
+ **Hastighet för nätverks data överföring** – data överförings hastigheter kan vara en begränsnings faktor. Indexera data från din Azure-miljö för att öka data överförings hastigheten.


## <a name="1---create-azure-cognitive-search-service"></a>1 – Skapa Azure Kognitiv sökning-tjänsten

För att slutföra den här självstudien behöver du en Azure Kognitiv sökning-tjänst som du kan [skapa i portalen](search-create-service-portal.md). Vi rekommenderar att du använder samma nivå som du planerar att använda i produktionen så att du kan testa och optimera indexerings hastigheterna på ett korrekt sätt.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Hämta en Admin API-nyckel och URL för Azure Kognitiv sökning

API-anrop kräver tjänst-URL och en åtkomst nyckel. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningar**  >  **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

   ![Hämta en HTTP-slutpunkt och åtkomst nyckel](media/search-get-started-rest/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomst nyckel")

## <a name="2---set-up-your-environment"></a>2 – Konfigurera din miljö

1. Starta Visual Studio och öppna **OptimizeDataIndexing. SLN**.
1. I Solution Explorer öppnar du **appsettings.jspå** för att ange anslutnings information.

```json
{
  "SearchServiceUri": "https://{service-name}.search.windows.net",
  "SearchServiceAdminApiKey": "",
  "SearchIndexName": "optimize-indexing"
}
```

## <a name="3---explore-the-code"></a>3 – utforska koden

När du har uppdaterat *appsettings.jspå*, bör exempel programmet i **OptimizeDataIndexing. SLN** vara redo att bygga och köra.

Den här koden är härledd från snabb starten av [C#](search-get-started-dotnet.md). Du hittar mer detaljerad information om grunderna i hur du arbetar med .NET SDK i artikeln.

Den här enkla/.NET-distribution.-konsolen i C# utför följande uppgifter:

+ Skapar ett nytt index baserat på data strukturen i C#-klassen (som även refererar till klassen Address).
+ Testar olika batch-storlekar för att fastställa den mest effektiva storleken
+ Indexerar data asynkront
    + Använda flera trådar för att öka indexerings hastigheten
    + Använda en exponentiell backoff-strategi för återförsök för att försöka utföra misslyckade objekt

 Innan du kör programmet ska du ta en minut för att undersöka koden och index definitionerna för det här exemplet. Relevant kod finns i flera filer:

  + **Hotel.cs** och **address.cs** innehåller det schema som definierar indexet
  + **DataGenerator.cs** innehåller en enkel klass som gör det enkelt att skapa stora mängder hotell data
  + **ExponentialBackoff.cs** innehåller kod för att optimera indexerings processen enligt beskrivningen nedan
  + **Program.cs** innehåller funktioner som skapar och tar bort Azure kognitiv sökning indexet, indexerar batchar med data och testar olika batch-storlekar

### <a name="creating-the-index"></a>Skapar indexet

Det här exempel programmet använder .NET SDK för att definiera och skapa ett Azure Kognitiv sökning-index. Det drar nytta av `FieldBuilder` klassen för att generera en index struktur från en C#-data modell klass.

Data modellen definieras av hotell klassen, som också innehåller referenser till klassen address. FieldBuilder går igenom flera klass definitioner för att generera en komplex data struktur för indexet. Metadata-Taggar används för att definiera attributen för varje fält, till exempel om det är sökbart eller sorterbart.

Följande kodfragment från **Hotel.cs** -filen visar hur ett enskilt fält och en referens till en annan data modell klass kan anges.

```csharp
. . .
[SearchableField(IsSortable = true)]
public string HotelName { get; set; }
. . .
public Address Address { get; set; }
. . .
```

I **program.cs** -filen definieras indexet med ett namn och en fält samling som genereras av `FieldBuilder.Build(typeof(Hotel))` metoden, och sedan skapas följande:

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address class is referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### <a name="generating-data"></a>Genererar data

En enkel klass implementeras i **DataGenerator.cs** -filen för att generera data för testning. Det enda syftet med den här klassen är att göra det enkelt att generera ett stort antal dokument med ett unikt ID för indexering.

Om du vill hämta en lista över 100 000 hotell med unika ID: n kör du följande kodrader:

```csharp
long numDocuments = 100000;
DataGenerator dg = new DataGenerator();
List<Hotel> hotels = dg.GetHotels(numDocuments, "large");
```

Det finns två storlekar på hotell som är tillgängliga för testning i det här exemplet: **liten** och  **stor**.

Schemat för ditt index kan ha en betydande inverkan på indexerings hastigheten. På grund av den här effekten är det klokt att konvertera den här klassen för att generera data som matchar det avsedda index schemat när du har kört den här självstudien.

## <a name="4---test-batch-sizes"></a>4-testa batchstorleken

Azure Kognitiv sökning stöder följande API: er för att läsa in enstaka eller flera dokument i ett index:

+ [Lägg till, uppdatera eller ta bort dokument (REST API)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [IndexDocumentsAction-klass](/dotnet/api/azure.search.documents.models.indexdocumentsaction) eller [IndexDocumentsBatch-klass](/dotnet/api/azure.search.documents.models.indexdocumentsbatch)

Indexering av dokument i batchar kan förbättra indexerings prestanda avsevärt. Dessa batchar kan vara upp till 1000 dokument eller upp till ungefär 16 MB per batch.

Att fastställa den optimala batchstorleken för dina data är en viktig komponent i optimering av indexerings hastigheter. De två primära faktorerna som påverkar den optimala batchstorleken är:

+ Schemat för ditt index
+ Storleken på dina data

Eftersom den optimala batchstorleken är beroende av ditt index och dina data, är det bästa sättet att testa olika batch-storlekar för att avgöra vilka resultat som finns i de snabbaste indexerings hastigheterna för ditt scenario.

Följande funktion visar en enkel metod för att testa batchstorleken.

```csharp
public static async Task TestBatchSizesAsync(SearchClient searchClient, int min = 100, int max = 1000, int step = 100, int numTries = 3)
{
    DataGenerator dg = new DataGenerator();

    Console.WriteLine("Batch Size \t Size in MB \t MB / Doc \t Time (ms) \t MB / Second");
    for (int numDocs = min; numDocs <= max; numDocs += step)
    {
        List<TimeSpan> durations = new List<TimeSpan>();
        double sizeInMb = 0.0;
        for (int x = 0; x < numTries; x++)
        {
            List<Hotel> hotels = dg.GetHotels(numDocs, "large");

            DateTime startTime = DateTime.Now;
            await UploadDocumentsAsync(searchClient, hotels).ConfigureAwait(false);
            DateTime endTime = DateTime.Now;
            durations.Add(endTime - startTime);

            sizeInMb = EstimateObjectSize(hotels);
        }

        var avgDuration = durations.Average(timeSpan => timeSpan.TotalMilliseconds);
        var avgDurationInSeconds = avgDuration / 1000;
        var mbPerSecond = sizeInMb / avgDurationInSeconds;

        Console.WriteLine("{0} \t\t {1} \t\t {2} \t\t {3} \t {4}", numDocs, Math.Round(sizeInMb, 3), Math.Round(sizeInMb / numDocs, 3), Math.Round(avgDuration, 3), Math.Round(mbPerSecond, 3));

        // Pausing 2 seconds to let the search service catch its breath
        Thread.Sleep(2000);
    }

    Console.WriteLine();
}
```

Eftersom inte alla dokument har samma storlek (även om de finns i det här exemplet) beräknar vi storleken på de data som vi skickar till Sök tjänsten. Vi gör detta med hjälp av funktionen nedan som först konverterar objektet till JSON och bestämmer dess storlek i byte. Med den här tekniken kan vi avgöra vilka batchstorleken som är mest effektiva i förhållande till MB/s indexerings hastigheter.

```csharp
// Returns size of object in MB
public static double EstimateObjectSize(object data)
{
    // converting object to byte[] to determine the size of the data
    BinaryFormatter bf = new BinaryFormatter();
    MemoryStream ms = new MemoryStream();
    byte[] Array;

    // converting data to json for more accurate sizing
    var json = JsonSerializer.Serialize(data);
    bf.Serialize(ms, json);
    Array = ms.ToArray();

    // converting from bytes to megabytes
    double sizeInMb = (double)Array.Length / 1000000;

    return sizeInMb;
}
```

Funktionen kräver `SearchClient` både och antalet försök som du vill testa för varje batchstorlek. Eftersom det kan finnas vissa variationer i indexerings tiderna för varje batch, försöker vi varje batch tre gånger som standard för att göra resultaten mer statistiskt signifikanta.

```csharp
await TestBatchSizesAsync(searchClient, numTries: 3);
```

När du kör funktionen bör du se utdata som nedan i-konsolen:

   ![Utdata från funktionen test batchstorlek](media/tutorial-optimize-data-indexing/test-batch-sizes.png "Utdata från funktionen test batchstorlek")

Identifiera vilken batchstorlek som är mest effektiv och Använd sedan den batch-storleken i nästa steg i självstudien. Du kan se en platå i MB/s över olika batch-storlekar.

## <a name="5---index-data"></a>5 – index data

Nu när vi har identifierat batchstorleken som vi tänker använda, är nästa steg att börja indexera data. För att indexera data effektivt, det här exemplet:

+ Använder flera trådar/arbetare.
+ Implementerar en exponentiell backoff-strategi för återförsök.

### <a name="use-multiple-threadsworkers"></a>Använd flera trådar/arbetare

För att dra full nytta av Azures Kognitiv söknings indexerings hastigheter måste du förmodligen använda flera trådar för att skicka begär Anden om batch-indexering samtidigt till tjänsten.  

Flera av de viktigaste aspekterna som nämns ovan påverkar det bästa antalet trådar. Du kan ändra det här exemplet och testa med olika antal trådar för att fastställa det optimala antalet trådar för ditt scenario. Men så länge du har flera trådar som körs samtidigt, bör du kunna utnyttja de flesta av effektivitets vinsterna.

När du ramperar de begär Anden som når Sök tjänsten kan du stöta på [HTTP-statuskod](/rest/api/searchservice/http-status-codes) som indikerar att begäran inte lyckades fullständigt. Under indexeringen är två vanliga HTTP-status koder:

+ **503 tjänsten är inte tillgänglig** – det här felet innebär att systemet är hårt belastat och att din begäran inte kan bearbetas just nu.
+ **207 multi-status** – det här felet innebär att vissa dokument lyckades, men att minst en misslyckades.

### <a name="implement-an-exponential-backoff-retry-strategy"></a>Implementera en strategi för exponentiell backoff-återförsök

Om ett fel inträffar ska förfrågningarna göras om med en [exponentiell backoff-strategi för återförsök](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

Azure Kognitiv söknings .NET SDK försöker automatiskt 503s och andra misslyckade förfrågningar, men du måste implementera din egen logik för att försöka igen 207s. Verktyg med öppen källkod, till exempel [Polly](https://github.com/App-vNext/Polly) , kan också användas för att implementera en strategi för återförsök.

I det här exemplet implementerar vi vår egen strategi för exponentiell backoff-återförsök. För att implementera den här strategin börjar vi med att definiera några variabler, inklusive `maxRetryAttempts` och initial `delay` för en misslyckad begäran:

```csharp
// Create batch of documents for indexing
var batch = IndexDocumentsBatch.Upload(hotels);

// Create an object to hold the result
IndexDocumentsResult result = null;

// Define parameters for exponential backoff
int attempts = 0;
TimeSpan delay = delay = TimeSpan.FromSeconds(2);
int maxRetryAttempts = 5;
```

Resultatet av indexerings åtgärden lagras i variabeln `IndexDocumentResult result` . Den här variabeln är viktig eftersom du kan kontrol lera om några dokument i batchen misslyckades på det sätt som visas nedan. Om det uppstår ett partiellt fel skapas en ny batch utifrån det misslyckade dokumentets ID.

`RequestFailedException` undantag bör också fångas eftersom de anger att begäran inte kunde utföras fullständigt och även bör göras om.

```csharp
// Implement exponential backoff
do
{
    try
    {
        attempts++;
        result = await searchClient.IndexDocumentsAsync(batch).ConfigureAwait(false);

        var failedDocuments = result.Results.Where(r => r.Succeeded != true).ToList();

        // handle partial failure
        if (failedDocuments.Count > 0)
        {
            if (attempts == maxRetryAttempts)
            {
                Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
                break;
            }
            else
            {
                Console.WriteLine("[Batch starting at doc {0} had partial failure]", id);
                Console.WriteLine("[Retrying {0} failed documents] \n", failedDocuments.Count);

                // creating a batch of failed documents to retry
                var failedDocumentKeys = failedDocuments.Select(doc => doc.Key).ToList();
                hotels = hotels.Where(h => failedDocumentKeys.Contains(h.HotelId)).ToList();
                batch = IndexDocumentsBatch.Upload(hotels);

                Task.Delay(delay).Wait();
                delay = delay * 2;
                continue;
            }
        }

        return result;
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("[Batch starting at doc {0} failed]", id);
        Console.WriteLine("[Retrying entire batch] \n");

        if (attempts == maxRetryAttempts)
        {
            Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
            break;
        }

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
} while (true);
```

Härifrån kommer vi att packa upp exponent backoff-koden i en funktion så att den enkelt kan anropas.

En annan funktion skapas sedan för att hantera aktiva trådar. För enkelhetens skull ingår inte den funktionen här, men den kan hittas i [ExponentialBackoff.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/optimize-data-indexing/v11/OptimizeDataIndexing/ExponentialBackoff.cs). Funktionen kan anropas med följande kommando där `hotels` är de data som vi vill överföra, `1000` är batchstorleken och `8` är antalet samtidiga trådar:

```csharp
await ExponentialBackoff.IndexData(indexClient, hotels, 1000, 8);
```

När du kör funktionen bör du se utdata som nedan:

![Utdata från funktionen index data](media/tutorial-optimize-data-indexing/index-data-start.png "Utdata från funktionen index data")

När en batch med dokument Miss lyckas skrivs ett fel ut som anger felet och att batchen provas igen:

```
[Batch starting at doc 6000 had partial failure]
[Retrying 560 failed documents]
```

När funktionen har körts klart kan du kontrol lera att alla dokument har lagts till i indexet.

## <a name="6---explore-index"></a>6 – utforska index

Du kan utforska det ifyllda Sök indexet när programmet har kört program mässigt eller med hjälp av [**Sök Utforskaren**](search-explorer.md) i portalen.

### <a name="programatically"></a>Program mässigt

Det finns två huvud alternativ för att kontrol lera antalet dokument i ett index: API för [antal dokument](/rest/api/searchservice/count-documents) och [Hämta index statistik](/rest/api/searchservice/get-index-statistics). Båda vägarna kan kräva ytterligare tid för att uppdateras, så du behöver inte göra något larm om antalet returnerade dokument är lägre än förväntat.

#### <a name="count-documents"></a>Räkna dokument

Med åtgärden räkna dokument hämtas antalet dokument i ett sökindex:

```csharp
long indexDocCount = await searchClient.GetDocumentCountAsync();
```

#### <a name="get-index-statistics"></a>Hämta index statistik

Åtgärden hämta index statistik returnerar ett dokument antal för det aktuella indexet samt lagrings användningen. Index statistik tar längre tid än antalet dokument att uppdatera.

```csharp
var indexStats = await indexClient.GetIndexStatisticsAsync(indexName);
```

### <a name="azure-portal"></a>Azure-portalen

I Azure Portal öppnar du sidan Sök tjänst **Översikt** och letar rätt på **optimerings** indexet i listan **index** .

  ![Lista över Azure Kognitiv sökning-index](media/tutorial-optimize-data-indexing/portal-output.png "Lista över Azure Kognitiv sökning-index")

*Antalet dokument* och *lagrings storleken* baseras på [Hämta index statistik-API](/rest/api/searchservice/get-index-statistics) och kan ta flera minuter att uppdatera.

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experiment stegen i utvecklingen är den mest praktiska metoden för design upprepning att ta bort objekten från Azure Kognitiv sökning och tillåta att koden återskapas. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Exempel koden för den här självstudien kontrollerar om det finns befintliga index och tar bort dem så att du kan köra koden igen.

Du kan också använda portalen för att ta bort index.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration är det en bra idé att ta bort de resurser som du inte längre behöver i slutet av projektet. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med konceptet att mata in data på ett effektivt sätt kan vi ta en närmare titt på Lucene-Query-arkitekturen och hur full texts ökning fungerar i Azure Kognitiv sökning.

> [!div class="nextstepaction"]
> [Så här fungerar fulltextsökning i Azure Cognitive Search](search-lucene-query-architecture.md)