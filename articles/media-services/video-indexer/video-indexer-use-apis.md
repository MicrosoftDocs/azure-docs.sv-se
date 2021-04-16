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
ms.openlocfilehash: a445e9869b0cd9928d95364f39e60fc892214b9a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532463"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Självstudie: Använda Video Indexer-API:t

Video Indexer konsoliderar olika AI-tekniker (artificiell intelligens) för ljud och video som erbjuds av Microsoft till en integrerad tjänst, vilket gör utvecklingen enklare. API:erna är utformade för att utvecklare ska kunna fokusera på att använda AI-medieteknik utan att behöva oroa sig för skalning, global räckvidd, tillgänglighet och tillförlitlighet för molnplattformar. Du kan använda API:et för att ladda upp filer, få detaljerade videoinsikter, få URL:er för inbäddade insikter och spelarwidgetar med mera.

När du skapar ett Video Indexer-konto kan du välja ett kostnadsfritt utvärderingskonto (där du får ett visst antal kostnadsfria indexeringsminuter) eller ett betalalternativ (där du inte begränsas av kvoten). Med den kostnadsfria utvärderingen ger Video Indexer upp till 600 minuter kostnadsfri indexering för webbplatsanvändare och upp till 2 400 minuter kostnadsfri indexering för API-användare. Med ett betalalternativ skapar du ett Video Indexer som är anslutet till [din Azure-prenumeration och ett Azure Media Services konto](connect-to-azure.md). Du betalar för minuter som indexeras. Mer information finns [i Media Services prissättning](https://azure.microsoft.com/pricing/details/media-services/).

Den här artikeln visar hur utvecklarna kan dra nytta av [Video Indexer-API:t](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Prenumerera på API:t

1. Logga in på [Video Indexer-utvecklarportalen](https://api-portal.videoindexer.ai/).

    Läs en viktig anteckning om [inloggningsinformation](release-notes.md#october-2020).
    
     ![Logga in på Video Indexer Developer Portal](./media/video-indexer-use-apis/sign-in.png)

   > [!Important]
   > * Du måste använda samma provider som du använde när du registrerade dig för Video Indexer.
   > * Personliga Google- och Microsoft-konton (Outlook/Live) kan bara användas för utvärderingskonton. Konton som är anslutna till Azure kräver Azure AD.
   > * Det kan bara finnas ett aktivt konto per e-post. Om en användare försöker logga in med för LinkedIn och senare med för Google visar den senare en felsida som säger user@gmail.com user@gmail.com att användaren redan finns.
2. Prenumerera.

    Välj [fliken](https://api-portal.videoindexer.ai/products) Produkter. Välj sedan Auktorisering och prenumerera.
    
    ![Fliken Produkter i Video Indexer Developer Portal](./media/video-indexer-use-apis/authorization.png)

    > [!NOTE]
    > Nya användare prenumererar automatiskt på Auktorisering.
    
    När du har prenumererat kan du hitta din prenumeration under **Products Authorization (Produktauktorisering).**  ->   På prenumerationssidan hittar du de primära och sekundära nycklarna. Nycklarna ska vara skyddade. Nycklarna ska bara användas av din serverkod. De bör inte vara tillgängliga på klientsidan (.js, .html och så vidare).

    ![Prenumeration och nycklar i Video Indexer Developer Portal](./media/video-indexer-use-apis/subscriptions.png)

> [!TIP]
> Video Indexer-användare kan använda en enstaka prenumerationsnyckel för att ansluta till flera Video Indexer-konton. Sedan kan du länka dessa Video Indexer-konton till olika Media Services-konton.

## <a name="obtain-access-token-using-the-authorization-api"></a>Hämta åtkomsttoken med hjälp av auktoriserings-API:t

När du prenumererar på auktoriserings-API:et kan du hämta åtkomsttoken. Dessa åtkomsttoken används för att autentisera mot åtgärds-API:t.

Varje anrop till åtgärds-API:t ska associeras med en åtkomsttoken, som matchar auktoriseringsområdet för anropet.

- Användarnivå: Med åtkomsttoken på användarnivå kan du utföra åtgärder på **användarnivå.** Till exempel hämta associerade konton.
- Kontonivå: Med åtkomsttoken på kontonivå kan du utföra åtgärder **på kontonivå** eller **videonivå.** Du kan till exempel ladda upp video, lista alla videor, få videoinsikter och så vidare.
- Videonivå: Med åtkomsttoken på videonivå kan du utföra åtgärder på en specifik **video.** Du kan till exempel få videoinsikter, ladda ned undertexter, hämta widgetar och så vidare.

Du kan styra om dessa token är skrivskyddade eller om de tillåter redigering genom att ange **allowEdit=true/false**.

I de flesta server-till-server-scenarier använder  du förmodligen samma kontotoken eftersom den omfattar både **kontoåtgärder** och **videoåtgärder.** Men om du planerar att göra anrop på klientsidan till Video Indexer (till exempel från  JavaScript) vill du använda en videoåtkomsttoken för att förhindra att klienter får åtkomst till hela kontot. Det är också anledningen till att du måste ange en videoåtkomsttoken när du bäddar in Video Indexer klientkod  i din klient (till exempel med hjälp av **Hämta** insiktswidget eller Hämta **spelarwidget).**

Du kan göra det lättare genom att använda **Auktoriserings**-API:t > **GetAccounts** för att hämta dina konton utan att erhålla en användartoken först. Du kan också begära att få kontona med giltiga token, så att du kan hoppa över ett ytterligare anrop för att få en kontotoken.

Åtkomsttoken upphör att gälla efter 1 timme. Kontrollera att din åtkomsttoken är giltig innan du använder åtgärds-API:t. Om den upphör att gälla anropar du auktoriserings-API:et igen för att hämta en ny åtkomsttoken.

Du är redo att börja integrera med API:et. Se [den detaljerade beskrivningen av varje Video Indexer-REST API](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Konto-ID

Parametern Konto-ID krävs i alla åtgärds-API-anrop. Konto-ID är ett GUID som kan erhållas på något av följande sätt:

* Använd **Video Indexer-webbplatsen** och hämta konto-ID:t:

    1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
    2. Gå till sidan **Inställningar**.
    3. Kopiera konto-ID:t.

        ![Video Indexer och konto-ID](./media/video-indexer-use-apis/account-id.png)

* Använd **Video Indexer-utvecklarportalen** och hämta konto-ID:t programmatiskt.

    Använd [API:et Hämta](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account) konto.

    > [!TIP]
    > Du kan generera åtkomsttoken för kontona genom att definiera `generateAccessTokens=true`.

* Hämta konto-ID:t från URL:en till en spelarsida på ditt konto.

    När du tittar på en video visas ID:t efter avsnittet `accounts` och före avsnittet `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Rekommendationer

Det här avsnittet innehåller några rekommendationer när du använder Video Indexer-API:t.

- Om du planerar att ladda upp en video rekommenderar vi att du placerar filen på en offentlig nätverksplats (till exempel ett Azure Blob Storage konto). Hämta länken till videon och ange URL:en som parameter för uppladdningsfil.

    URL:en som skickas till Video Indexer måste peka på en mediefil (ljud eller video). En enkel verifiering för URL:en (eller SAS-URL: en) är att klistra in den i en webbläsare. Om filen börjar spelas upp/ned är det förmodligen en bra URL. Om webbläsaren återger en del visualisering är det förmodligen inte en länk till en fil utan till en HTML-sida.

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

När du är klar med den här självstudien kan du ta bort resurser som du inte planerar att använda.

## <a name="see-also"></a>Se även

- [Översikt över Video Indexer](video-indexer-overview.md)
- [Regioner](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Nästa steg

- [Granska information om JSON-utdata](video-indexer-output-json-v2.md)
- Kolla in [exempelkoden som](https://github.com/Azure-Samples/media-services-video-indexer) visar en viktig aspekt av att ladda upp och indexera en video. Genom att följa koden får du en bra uppfattning om hur du använder vårt API för grundläggande funktioner. Läs infogade kommentarer och lägg märke till våra metodtips.

