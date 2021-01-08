---
title: Använda Video Indexer-API:et
titleSuffix: Azure Media Services
description: Den här artikeln beskriver hur du kommer igång med Azure Media Services Video Indexer API.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/07/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: fcd194e2503610db314f6a975a4afb1d27962f8c
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028216"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Självstudie: Använda Video Indexer-API:t

Video Indexer konsoliderar olika funktioner för ljud-och video artificiell intelligens (AI) som erbjuds av Microsoft till en integrerad tjänst, vilket gör det enklare att utveckla. API: erna är utformade så att utvecklare kan fokusera på att använda Media AI-tekniker utan att behöva oroa sig för skalning, global räckvidd, tillgänglighet och pålitlighet för moln plattformarna. Du kan använda API: t för att ladda upp filer, få detaljerad information om video insikter, Hämta URL: er för inbäddnings bara insikter och uppspelnings-widgetar med mera.

När du skapar ett Video Indexer-konto kan du välja ett kostnadsfritt utvärderingskonto (där du får ett visst antal kostnadsfria indexeringsminuter) eller ett betalalternativ (där du inte begränsas av kvoten). Med den kostnadsfria utvärderingen ger Video Indexer upp till 600 minuter kostnadsfri indexering för webbplatsanvändare och upp till 2 400 minuter kostnadsfri indexering för API-användare. Med ett betalt alternativ skapar du ett Video Indexer-konto som [är kopplat till din Azure-prenumeration och ett Azure Media Services-konto](connect-to-azure.md). Du betalar för minuter indexerat, mer information finns i [Media Services prissättning](https://azure.microsoft.com/pricing/details/media-services/).

Den här artikeln visar hur utvecklarna kan dra nytta av [Video Indexer-API:t](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Prenumerera på API:t

1. Logga in på [Video Indexer-utvecklarportalen](https://api-portal.videoindexer.ai/).

    Granska en versions kommentar om [inloggnings information](release-notes.md#october-2020).
    
     ![Logga in på Video Indexer Developer-portalen](./media/video-indexer-use-apis/sign-in.png)

   > [!Important]
   > * Du måste använda samma provider som du använde när du registrerade dig för Video Indexer.
   > * Personliga Google-och Microsoft-konton (Outlook/Live) kan bara användas för utvärderings konton. Konton som är anslutna till Azure kräver Azure AD.
   > * Det kan bara finnas ett aktivt konto per e-post. Om en användare försöker logga in med user@gmail.com för LinkedIn och senare med user@gmail.com för Google visas en felsida i den senare, vilket säger att användaren redan finns.
2. Prenumerera.

    Välj fliken [produkter](https://api-portal.videoindexer.ai/products) . Välj sedan auktorisering och prenumerera.
    
    ![Fliken produkter i Video Indexer Developer-portalen](./media/video-indexer-use-apis/authorization.png)

    > [!NOTE]
    > Nya användare prenumererar automatiskt på Auktorisering.
    
    När du prenumererar kan du hitta prenumerationen under **produkter** som du har  ->  **tillstånd** för. På sidan prenumeration hittar du de primära och sekundära nycklarna. Nycklarna ska vara skyddade. Nycklarna ska bara användas av din serverkod. De bör inte vara tillgängliga på klient sidan (. js,. html och så vidare).

    ![Prenumeration och nycklar i Video Indexer Developer-portalen](./media/video-indexer-use-apis/subscriptions.png)

> [!TIP]
> Video Indexer-användare kan använda en enstaka prenumerationsnyckel för att ansluta till flera Video Indexer-konton. Sedan kan du länka dessa Video Indexer-konton till olika Media Services-konton.

## <a name="obtain-access-token-using-the-authorization-api"></a>Hämta åtkomsttoken med hjälp av auktoriserings-API:t

När du prenumererar på API: et för auktorisering kan du hämta åtkomsttoken. Dessa åtkomsttoken används för att autentisera mot åtgärds-API:t.

Varje anrop till åtgärds-API:t ska associeras med en åtkomsttoken, som matchar auktoriseringsområdet för anropet.

- Användar nivå: åtkomsttoken på användar nivå gör att du kan utföra åtgärder på **användar** nivå. Till exempel hämta associerade konton.
- Konto nivå: med åtkomst-token på konto nivå kan du utföra åtgärder på **konto** nivå eller **video** nivå. Du kan till exempel Ladda upp video, Visa alla videor, få video insikter och så vidare.
- Video nivå: med åtkomst-token på video nivå kan du utföra åtgärder på en enskild **video**. Du kan till exempel få video insikter, Hämta under texter, Hämta widgetar och så vidare.

Du kan kontrol lera om dessa tokens är skrivskyddade eller om de tillåter redigering genom att ange **AllowEdit = TRUE/FALSE**.

För de flesta server-till-Server-scenarier använder du förmodligen samma **konto** -token eftersom det täcker både **konto** åtgärder och **video** åtgärder. Men om du planerar att göra klient sidans anrop till Video Indexer (till exempel från Java Script) vill du **använda en videoåtkomsttoken för** att förhindra att klienter får åtkomst till hela kontot. Det beror också på att när du bäddar in Video Indexer klient kod i klienten (till exempel med hjälp av **Hämta insikts-widget** eller **Hämta Player-widget**) måste du ange en **video** åtkomst-token.

Du kan göra det lättare genom att använda **Auktoriserings**-API:t > **GetAccounts** för att hämta dina konton utan att erhålla en användartoken först. Du kan också begära att få kontona med giltiga token, så att du kan hoppa över ett ytterligare anrop för att få en kontotoken.

Åtkomsttoken upphör att gälla efter 1 timme. Kontrollera att din åtkomsttoken är giltig innan du använder åtgärds-API:t. Om den går ut anropar du API: et för auktorisering igen för att få en ny åtkomsttoken.

Du är redo att börja integrera med API: et. Se [den detaljerade beskrivningen av varje Video Indexer-REST API](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Konto-ID

Parametern Konto-ID krävs i alla åtgärds-API-anrop. Konto-ID är ett GUID som kan erhållas på något av följande sätt:

* Använd **Video Indexer-webbplatsen** och hämta konto-ID:t:

    1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
    2. Gå till sidan **Inställningar**.
    3. Kopiera konto-ID:t.

        ![Video Indexer inställningar och konto-ID](./media/video-indexer-use-apis/account-id.png)

* Använd **Video Indexer-utvecklarportalen** och hämta konto-ID:t programmatiskt.

    Använd [Hämta konto](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account?) -API.

    > [!TIP]
    > Du kan generera åtkomsttoken för kontona genom att definiera `generateAccessTokens=true`.

* Hämta konto-ID:t från URL:en till en spelarsida på ditt konto.

    När du tittar på en video visas ID:t efter avsnittet `accounts` och före avsnittet `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Rekommendationer

Det här avsnittet innehåller några rekommendationer när du använder Video Indexer-API:t.

- Om du planerar att ladda upp en video rekommenderas att placera filen i en offentlig nätverks plats (till exempel ett Azure Blob Storage-konto). Hämta länken till videon och ange URL:en som parameter för uppladdningsfil.

    URL:en som skickas till Video Indexer måste peka på en mediefil (ljud eller video). En enkel verifiering för URL: en (eller SAS-URL) är att klistra in den i en webbläsare, om filen börjar spelas upp/hämtas är det förmodligen en bra URL. Om webbläsaren återger viss visualisering är det troligt vis inte en länk till en fil, men till en HTML-sida.

- När du anropar API:t som hämtar videoinsikter för den angivna videon får du detaljerade JSON-utdata som svarsinnehåll. [Information om returnerad JSON finns i det här avsnittet](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Kodexempel

Följande C#-kodavsnitt visar hur du använder alla Video Indexer-API:er tillsammans.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2"; // replace with the account's location, or with “trial” if this is a trial account
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer = new byte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(new ByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med den här självstudien tar du bort resurser som du inte planerar att använda.

## <a name="see-also"></a>Se även

- [Översikt över Video Indexer](video-indexer-overview.md)
- [Regioner](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Nästa steg

- [Granska information om JSON för utdata](video-indexer-output-json-v2.md)
- Kolla in [exempel koden](https://github.com/Azure-Samples/media-services-video-indexer) som visar viktig aspekt av att ladda upp och indexera en video. Efter koden får du en bra uppfattning om hur du använder vårt API för grundläggande funktioner. Se till att läsa de infogade kommentarerna och Lägg märke till våra bästa praxis råd.

