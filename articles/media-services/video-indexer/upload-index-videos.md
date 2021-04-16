---
title: Ladda upp och indexera videor med Video Indexer
titleSuffix: Azure Media Services
description: Det här avsnittet visar hur du använder API:er för att ladda upp och indexera videor med Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/04/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 90fca4342b1fe04adef97a1a4c1c2166ca7ec51e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532491"
---
# <a name="upload-and-index-your-videos"></a>Ladda upp och indexera dina videor  

När videon har laddats upp Video Indexer (valfritt) kodar videon (beskrivs i artikeln). När du skapar ett Video Indexer-konto kan du välja ett kostnadsfritt utvärderingskonto (där du får ett visst antal kostnadsfria indexeringsminuter) eller ett betalalternativ (där du inte begränsas av kvoten). Med den kostnadsfria utvärderingen ger Video Indexer upp till 600 minuter kostnadsfri indexering för webbplatsanvändare och upp till 2 400 minuter kostnadsfri indexering för API-användare. Med betalalternativet skapar du ett Video Indexer-konto som är [anslutet till din Azure-prenumeration och ett Azure Media Services-konto](connect-to-azure.md). Du betalar för minuter som indexeras. Mer information finns i [Media Services prissättning](https://azure.microsoft.com/pricing/details/media-services/).

När du laddar upp videor med Video Indexer-API:et har du följande uppladdningsalternativ: 

* ladda upp videon från en URL (rekommenderas),
* skicka videofilen som en bytematris i begärandetexten,
* använda en befintlig Azure Media Services-resurs genom att tillhandahålla [tillgångs-id:t](../latest/assets-concept.md) (stöds endast för betalkonton).

Artikeln visar hur du laddar upp och indexerar dina videor med följande alternativ:

* [Video Indexer-webbplatsen](#upload-and-index-a-video-using-the-video-indexer-website) 
* [API:er för Video Indexer](#upload-and-index-with-api)

## <a name="supported-file-formats-for-video-indexer"></a>Filformat som stöds för Video Indexer

I artikeln [indatacontainer/filformat](../latest/encode-media-encoder-standard-formats-reference.md) finns en lista över filformat som du kan använda med Video Indexer.

## <a name="video-files-storage"></a>Lagring av videofiler

- Med ett Video Indexer konto skapar du ett Video Indexer-konto som är anslutet till din Azure-prenumeration och ett Azure Media Services konto. Mer information finns i Skapa [ett Video Indexer-konto som är anslutet till Azure.](connect-to-azure.md)
- Videofiler lagras i Azure Storage av Azure Media Services. Det finns ingen tidsbegränsning.
- Du kan alltid ta bort video- och ljudfiler samt eventuella metadata och insikter som extraherats från dem genom att Video Indexer. När du tar bort en fil Video Indexer tas filen och dess metadata och insikter bort permanent från Video Indexer. Men om du har implementerat en egen säkerhetskopieringslösning i Azure Storage finns filen kvar i Azure Storage.
- Persistensen för en video är identisk, oavsett om uppladdningen är klar från Video Indexer webbplats eller med hjälp av API:et Upload.
   
## <a name="upload-and-index-a-video-using-the-video-indexer-website"></a>Ladda upp och indexera en video med Video Indexer webbplats

> [!NOTE]
> Namnet på videon får inte vara längre än 80 tecken.

1. Logga in på [Video Indexer](https://www.videoindexer.ai/)-webbplatsen.
1. Ladda upp en video genom att trycka på knappen eller länken **Ladda upp**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Överför":::
1. När videon har laddats upp påbörjar Video Indexer indexering och analys av videon.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Uppladdningens förlopp":::
1. När Video Indexer är klar med analysen får du ett e-postmeddelande med en länk till videon och en kort beskrivning av vad som hittades i videon. Till exempel: personer, ämnen och OCR.

## <a name="upload-and-index-with-api"></a>Ladda upp och indexera med API

Använd [API:et Upload video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) (Ladda upp video) för att ladda upp och indexera dina videor baserat på en URL. Det kodexempel som följer innehåller den kommenterade ut koden som visar hur du laddar upp bytematrisen. 

### <a name="configurations-and-params"></a>Konfigurationer och parametrar

I det här avsnittet beskrivs några av de valfria parametrarna och när du kan ange dem. Den mest aktuella parameterinformationen finns i Ladda upp [video-API.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)

#### <a name="externalid"></a>externalID 

Med den här parametern kan du ange ett ID som ska associeras med videon. ID:t kan tillämpas på extern VCM-systemintegrering (Video Content Management). Det går att söka efter de videor som finns på Video Indexer-portalen med det angivna externa ID:t.

#### <a name="callbackurl"></a>callbackUrl

[!INCLUDE [callback url](./includes/callback-url.md)]

##### <a name="other-considerations"></a>Ytterligare överväganden

- Video Indexer returnerar alla befintliga parametrar som anges i den ursprungliga webbadressen.
- Den tillhandahållna webbadressen måste vara kodad.

#### <a name="indexingpreset"></a>indexingPreset

Använd den här parametern för att definiera det AI-paket som du vill tillämpa på ljud- eller videofilen . Den här parametern används för att konfigurera indexeringsprocessen. Du kan ange följande värden:

- `AudioOnly` – Indexera och extrahera insikter med endast ljud (ignorera video).
- `VideoOnly` – Indexera och extrahera insikter med endast video (ignorera ljud).
- `Default` – Indexera och extrahera insikter med både ljud och video.
- `DefaultWithNoiseReduction` – Indexera och extrahera insikter från både ljud och video, samtidigt som algoritmer för brusreducering tillämpas på ljudströmmen.

    Värdet `DefaultWithNoiseReduction` mappas nu till standardförinställningen (inaktuell).
- `BasicAudio` – Indexera och extrahera insikter med endast ljud (ignorera video), inklusive endast grundläggande ljudfunktioner (transkription, översättning, formatera undertexter för utdata).
 - `AdvancedAudio` – Indexera och extrahera insikter med endast ljud (ignorera video), inklusive avancerade ljudfunktioner (identifiering av ljudhändelse) utöver standardljudanalysen.

> [!NOTE]
> Video Indexer omfattar upp till två ljudspår. Om det finns fler ljudspår i filen behandlas de som ett spår.<br/>
Om du vill indexera spåren separat måste du extrahera relevant ljudfil och indexera den som `AudioOnly` .

Priset beror på det valda indexeringsalternativet. Mer information finns i [Media Services priser.](https://azure.microsoft.com/pricing/details/media-services/)

#### <a name="priority"></a>prioritet

Videor indexeras av Video Indexer enligt deras prioritet. Använd parametern **prioritet** för att ange prioritet för indexet. Följande värden är giltiga: **Låg**, **Normal** (standard), och **Hög**.

**Prioritet**-parametern stöds endast för betalda konton.

#### <a name="streamingpreset"></a>streamingPreset

När videon har laddats upp kan Video Indexer koda videon. Sedan fortsätter den med indexering och analys av videon. När Video Indexer är klar med analysen får du ett meddelande med video-ID:t.  

När du använder API:t [Ladda upp video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) eller [Indexera om video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) är en av de valfria parametrarna `streamingPreset`. Om du ställer in `streamingPreset` på `Default`, `SingleBitrate` eller `AdaptiveBitrate` utlöses kodningsprocessen. När indexerings- och kodningsjobben är klara publiceras videon så att du även kan strömma videon. Slutpunkten för direktuppspelning som du vill strömma videon från måste ha tillståndet **Körs**.

För SingleBitrate tillämpas standardkodarkostnaden per utdata. Om videohöjden är större än eller lika med 720 Video Indexer den som 1280 x 720. Annars som 640 x 468.
Standardinställningen är [innehållsmedveten kodning](../latest/encode-content-aware-concept.md).

För att kunna köra indexerings- och kodningsjobben kräver [Azure Media Services-kontot som är anslutet till ditt Video Indexer-konto](connect-to-azure.md) reserverade enheter. Mer information finns i [Skala mediebearbetning](../previous/media-services-scale-media-processing-overview.md). Eftersom det är beräkningsintensiva jobb rekommenderas enhetstypen S3 starkt. Antalet RU:er definierar det maximala antalet jobb som kan köras parallellt. Baslinjerekommendationen är 10 S3-RU:er. 

Om du bara vill indexera videon men inte koda den ställer du in `streamingPreset` på `NoStreaming`.

#### <a name="videourl"></a>videoUrl

En URL till video-/ljudfilen som ska indexeras. URL:en måste peka på en mediefil (HTML-sidor stöds inte). Filen kan skyddas av en åtkomsttoken som tillhandahålls som en del av URI:n och slutpunkten som hanterar filen måste skyddas med TLS 1.2 eller senare. URL:en måste vara kodad. 

Om `videoUrl` inte anges förväntar sig Video Indexer att du skickar filen som multipart/form-brödtextinnehåll.

### <a name="code-sample"></a>Kodexempel

Följande C#-kodavsnitt visar hur du använder alla Video Indexer-API:er tillsammans.

**Anvisningar för att köra följande kodexempel**

När du har kopierat den här koden till utvecklingsplattformen måste du ange två parametrar: API Management autentiseringsnyckel och video-URL.

* API-nyckel – API-nyckeln är din personliga prenumerationsnyckel för API Management, som gör att du kan hämta en åtkomsttoken för att utföra åtgärder på ditt Video Indexer konto. 

    Hämta API-nyckeln genom att gå igenom det här flödet:

    * Navigera till https://api-portal.videoindexer.ai/
    * Inloggning
    * Gå till prenumerationen **för**  ->  **auktorisering**  ->  **av produkter**
    * Kopiera **primärnyckeln**
* Video-URL – En URL till video-/ljudfilen som ska indexeras. URL:en måste peka på en mediefil (HTML-sidor stöds inte). Filen kan skyddas av en åtkomsttoken som tillhandahålls som en del av URI:n och slutpunkten som hanterar filen måste skyddas med TLS 1.2 eller senare. URL:en måste vara kodad.

Resultatet av att köra kodexe exemplet inkluderar en URL för insiktswidgeten och en url för spelarwidget som gör att du kan undersöka de insikter och videor som laddats upp. 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```

### <a name="common-errors"></a>Vanliga fel

De statuskoder som visas i följande tabell kan returneras av uppladdingsåtgärden.

|Statuskod|ErrorType (i svarstexten)|Description|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|Samma video håller redan på att bearbetas i det angivna kontot.|
|400|VIDEO_ALREADY_FAILED|Samma video misslyckades med att bearbetas i det angivna kontot för mindre än 2 timmar sedan. API-klienter ska vänta minst 2 timmar innan en video laddas upp på nytt.|
|429||Utvärderingskonton tillåts 5 uppladdningar per minut. Betalda konton tillåts 50 uppladdningar per minut.|

## <a name="uploading-considerations-and-limitations"></a>Överväganden och begränsningar vid uppladdning
 
- Namnet på videon får inte vara längre än 80 tecken.
- När du laddar upp videon baserat på en webbadress (rekommenderas) måste slutpunkten skyddas med TLS 1.2 (eller senare).
- Uppladdningsstorleken med webbadressalternativet är begränsad till 30 GB.
- Längden på webbadressen i förfrågan är begränsad till 6 144 tecken där frågesträngsadressen är begränsad till 4 096 tecken.
- Uppladdningsstorleken med bytearrayalternativet är begränsad till 2 GB.
- Det finns en tidsgräns för bytearrayalternativet på 30 min.
- URL:en som `videoURL` anges i parametern måste kodas.
- Indexering av Media Services-resurser har samma begränsning som vid indexering från webbadresser.
- Video Indexer har en maximal tidsgräns på 4 timmar för en enskild fil.
- Webbadressen måste kunna nås (till exempel vara en offentlig webbadress). 

    Om det är en privat webbadress måste åtkomsttoken anges i förfrågan.
- URL:en måste peka på en giltig mediefil och inte till en webbsida, till exempel en länk till `www.youtube.com` sidan.
- Med ett betalkonto kan du överföra upp till 50 filmer per minut och med ett utvärderingskonto upp till 5 filmer per minut.

> [!Tip]
> Det rekommenderas att du använder .NET Framework version 4.6.2 eller senare eftersom äldre .NET Framework-versioner inte använder TLS 1.2 som standard.
>
> Om du måste använda äldre .NET Framework lägger du till en rad i koden innan du gör REST API-anropet:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="next-steps"></a>Nästa steg

[Granska Azure-Video Indexer som produceras av API](video-indexer-output-json-v2.md)
