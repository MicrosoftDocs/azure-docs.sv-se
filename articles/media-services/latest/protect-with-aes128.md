---
title: kryptera video med AES-128: Azure Media Services Beskrivning: Lär dig hur du krypterar video med AES 128-bitars kryptering och hur du använder Key Delivery Service i Azure Media Services.
tjänster: Media-Services documentationcenter: ' ' author: IngridAtMicrosoft Manager: femila Editor: ' '

MS. service: Media-Services MS. arbets belastning: Media ms.tgt_pltfrm: na MS. devlang: na MS. topic: självstudie MS. Date: 08/31/2020 MS. author: inhenkel

---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Självstudie: kryptera video med AES-128 och Använd Key Delivery Service

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Även om självstudien använder [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) -exempel är de allmänna stegen desamma för [REST API](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event?view=azure-cli-latest)eller andra [SDK](media-services-apis-overview.md#sdks): er som stöds.

Du kan använda Media Services för att leverera HTTP Live Streaming (HLS), MPEG-streck och Smooth Streaming krypterat med AES genom att använda 128-bitars krypterings nycklar. Media Services tillhandahåller också den nyckel leverans tjänst som levererar krypterings nycklar till behöriga användare. Om du vill att Media Services dynamiskt ska kryptera din video, associerar du krypterings nyckeln med en strömmande lokaliserare och konfigurerar även innehålls nyckel principen. När en data ström begärs av en spelare, använder Media Services den angivna nyckeln för att dynamiskt Kryptera ditt innehåll med AES-128. Om spelaren vill dekryptera dataströmmen begär hon eller han nyckeln från nyckelleveranstjänsten. För att avgöra om användaren är behörig för nyckeln utvärderas den innehållsnyckelprincip som du angav för nyckeln.

Du kan kryptera varje tillgång med flera krypteringstyper (AES-128, PlayReady, Widevine, FairPlay). I dokumentationen om [direktuppspelningsprotokoll och krypteringstyper](content-protection-overview.md#streaming-protocols-and-encryption-types) ser du vad som är bra att kombinera. Se även [hur du skyddar med DRM](protect-with-drm.md).

Utdata från exemplet i den här artikeln innehåller en URL till Azure Media Player, manifest-URL och den AES-token som krävs för att spela upp innehållet. Exemplet anger förfallo datum för token för JSON Web Token (JWT) till 1 timme. Du kan öppna en webbläsare och klistra in den resulterande URL: en för att starta Azure Media Player demonstrations sidan med URL och token ifyllda för dig redan i följande format: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}``` .

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Hämta [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) -exemplet som beskrivs i artikeln.
> * Börja använda Media Services API: er med .NET SDK.
> * Skapa en utmatnings till gång.
> * Skapa en transformation av kodning.
> * Skicka ett jobb.
> * Vänta tills jobbet har slutförts.
> * Skapa en princip för innehålls nyckel
> * Konfigurera principen för att använda begränsning av JWT-token.
> * Skapa en strömmande positionerare.
> * Konfigurera den strömmande lokaliseraren för att kryptera videon med AES (ClearKey).
> * Hämta en testtoken.
> * Bygg en strömmande URL.
> * Rensa resurser.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att kunna genomföra självstudien.

* Gå igenom artikeln med [översikten om innehållsskydd](content-protection-overview.md).
* Installera Visual Studio Code eller Visual Studio.
* [Skapa ett Media Services-konto](./create-account-howto.md).
* Hämta autentiseringsuppgifter som krävs för att använda Media Services-API: er genom [att följa Access API: er](./access-api-howto.md)

## <a name="download-code"></a>Ladda ned kod

Klona en GitHub-lagringsplats som innehåller hela det .NET-exempel som diskuteras i den här artikeln till din dator med följande kommando:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Exemplet "Kryptera med AES-128" finns i mappen [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) .

> [!NOTE]
> Exemplet skapar unika resurser varje gång du kör appen. Normalt återanvänder du befintliga resurser som transformeringar och principer (om den befintliga resursen har obligatoriska konfigurationer).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Börja använda API:er för Media Services med .NET SDK

Du börjar använda Media Services API: er med .NET genom att skapa ett **AzureMediaServicesClient** -objekt. När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I den kod som du har klonat i början av artikeln skapade funktionen **GetCredentialsAsync** objektet ServiceClientCredentials baserat på de autentiseringsuppgifter som anges i den lokala konfigurationsfilen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Skapa en utdatatillgång  

Utmatnings [till gången](/rest/api/media/assets) lagrar resultatet av ditt kodnings jobb.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Hämta eller skapa en kodtransformering

När du skapar en ny instans för en [Transformering](/rest/api/media/transforms), måste du ange vilken utdata du vill att den ska skapa. Den obligatoriska parametern är objektet **TransformOutput** som visas i koden nedan. Varje **TransformOutput** innehåller en **Förinställning**. I **Förinställning** finns stegvisa anvisningar för den video- och/eller ljudbearbetning som ska användas för att generera önskad **TransformOutput**. Det exempel som beskrivs i artikeln använder en inbyggd förinställning som kallas **AdaptiveStreaming**. För inställningen kodar indata till en steg-för-bit-uppräkning (bit hastighets upplösnings par) baserat på indata för upplösning och bit hastighet och genererar sedan ISO MP4-filer med H. 264 video och AAC-ljud som motsvarar varje bit hastighets upplösnings par.

Innan du skapar en ny [transformering](/rest/api/media/transforms)måste du först kontrol lera om det redan finns en med **Get** -metoden, som du ser i koden som följer. I Media Services v3 returnerar **Get**-metoderna i entiteter **null** om entiteten inte finns (skiftlägesokänslig kontroll av namnet).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Skicka jobb

Som nämns ovan är objektet [Transformering](/rest/api/media/transforms) receptet och ett [Jobb](/rest/api/media/jobs) är det faktiska begärandet till Media Services om att tillämpa **transformeringen** på en indatavideo eller ett ljudinnehåll. **Jobbet** anger information som platsen för indata-videon och platsen för utdata.

I den här självstudien skapar vi jobbets indata baserat på en fil som matas in direkt från en [https-källans URL](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Vänta tills jobbet är klart

Det tar lite tid att slutföra jobbet. När du gör det, vill du bli meddelad. Kod exemplet nedan visar hur du avsöker tjänsten för [jobbets](/rest/api/media/jobs)status. Avsökningen är inte en rekommenderad metod för produktion av appar på grund av potentiell latens. Avsökningen kan begränsas om den överanvänds på ett konto. Utvecklare bör i stället använda Event Grid. Mer information finns i [dirigera händelser till en anpassad webb slut punkt](job-state-events-cli-how-to.md).

**Jobb** har vanligtvis följande tillstånd: **Schemalagd**, **I kö**, **Bearbetas**, **Slutförd** (slutlig status). Om jobbet har kommit över ett fel visas **fel** tillstånd. Om jobbet håller på att avbrytas **avbryts du och** **annulleras** när det är färdigt.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Skapa en princip för innehålls nyckel

En innehållsnyckel ger säker åtkomst till dina tillgångar. Du måste skapa en **princip för innehålls nycklar** som konfigurerar hur innehålls nyckeln levereras till slut klienter. Innehålls nyckeln är associerad med **streaming Locator**. Media Services tillhandahåller också den nyckel leverans tjänst som levererar krypterings nycklar till behöriga användare.

När en data ström begärs av en spelare, använder Media Services den angivna nyckeln för att dynamiskt Kryptera ditt innehåll (i det här fallet med AES-kryptering). För att dekryptera data strömmen begär spelaren nyckeln från Key Delivery Service. För att avgöra om användaren är behörig för nyckeln utvärderas den innehållsnyckelprincip som du angav för nyckeln.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Skapa en positionerare för direktuppspelning

När kodningen är klar och innehållsnyckelprincipen är inställd är nästa steg att göra videon i utdatatillgången tillgänglig för uppspelning av klienterna. Du gör videon tillgänglig i två steg:

1. Skapa en [strömmande positionerare](/rest/api/media/streaminglocators).
2. Skapa webbadresserna för direktuppspelning som klienter kan använda.

Processen för att skapa **streaming Locator** kallas publicering. Som standard är **streaming Locator** giltig omedelbart efter att du har gjort API-anropen. Det varar tills det tas bort, om du inte konfigurerar de valfria start-och slut tiderna.

När du skapar en [strömmande positionerare](/rest/api/media/streaminglocators)måste du ange önskad **StreamingPolicyName**. I den här självstudien använder vi en av PredefinedStreamingPolicies, som visar Azure Media Services hur du publicerar innehållet för strömning. I det här exemplet används AES-krypteringen (denna kryptering kallas även ClearKey-kryptering eftersom nyckeln levereras till uppspelnings klienten via HTTPS och inte en DRM-licens).

> [!IMPORTANT]
> När du använder en anpassad [StreamingPolicy](/rest/api/media/streamingpolicies)bör du utforma en begränsad uppsättning sådana principer för ditt Media Service-konto och sedan använda dem igen för dina strömmande positionerare när samma krypterings alternativ och protokoll behövs. Media Service-kontot har en kvot för antalet StreamingPolicy-poster. Du bör inte skapa en ny StreamingPolicy för varje strömmande positionerare.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Hämta en testtoken

I den här självstudien anger vi att innehållsnyckelprincipen ska ha en tokenbegränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder tokens i [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) -format och det är det vi konfigurerar i exemplet.

ContentKeyIdentifierClaim används i **innehålls nyckel principen**, vilket innebär att den token som presenteras för Key Delivery Service måste ha identifieraren för innehålls nyckeln. I exemplet har vi inte angett någon innehålls nyckel när du skapade den strömmande lokaliseraren. systemet skapade ett slumpmässigt en för oss. För att generera testtoken måste vi få ContentKeyId att placera i ContentKeyIdentifierClaim-anspråket.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Skapa en DASH-uppspelningsadress

Nu när du har skapat [streaming-adressen](/rest/api/media/streaminglocators) kan du hämta de strömmande URL: erna. Om du vill bygga en URL måste du sammanfoga [StreamingEndpoint](/rest/api/media/streamingendpoints) -värdnamnet och sökvägen för **strömmande lokalisering** . I det här exemplet används *standardvärdet* **Slutpunkt för direktuppspelning**. När du skapar ett Media Service-konto första gången kommer detta *standardvärde för* **StreamingEndpoint** vara i ett stoppat tillstånd, så du måste anropa **Starta**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Rensa resurser på ditt Media Services-konto

I allmänhet bör du rensa allt utom objekt som du planerar att återanvända (normalt återanvänder du transformeringar, omslags positioner för strömning och så vidare). Om du vill att ditt konto ska rensas efter experimentering tar du bort de resurser som du inte planerar att återanvända. Följande kod tar till exempel bort jobbet, skapade till gångar och innehålls nyckel princip:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive Media Services och de lagringskonton som du skapade för självstudien, tar du bort resursgruppen som du skapade tidigare.

Kör följande CLI-kommando:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skydda med DRM](protect-with-drm.md)
