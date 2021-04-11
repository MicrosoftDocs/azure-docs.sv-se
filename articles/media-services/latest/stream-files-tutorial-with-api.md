---
title: Ladda upp, koda och strömma med Media Services v3: Azure Media Services Beskrivning: självstudie som visar hur du laddar upp en fil, koda video och strömma innehåll med Azure Media Services v3.
tjänster: Media-Services documentationcenter: ' ' author: IngridAtMicrosoft Manager: femila Editor: ' '

MS. service: Media-Services MS. arbets belastning: MS. topic: självstudie MS. Custom: MVC MS. Date: 03/17/2021 MS. author: inhenkel
---

# <a name="tutorial-upload-encode-and-stream-videos-with-media-services-v3"></a>Självstudie: Ladda upp, koda och strömma videor med Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Även om den här självstudien använder [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent) -exempel är de allmänna stegen desamma för [REST API](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event)eller andra [SDK](media-services-apis-overview.md#sdks): er som stöds.

Med Azure Media Services kan du koda dina mediefiler till format som spelas upp på en rad olika webbläsare och enheter. Du kanske vill strömma ditt innehåll i Apples HLS- eller MPEG DASH-formaten. Innan du strömmar, bör du koda dina högkvalitativa digitala mediafiler. För hjälp med kodning, se [encoding-begrepp](encode-concept.md). Den här självstudiekursen laddar upp en lokal videofil och kodar den överförda filen. Du kan också koda innehåll som du gör tillgängligt via en HTTPS-URL. Mer information finns i [Skapa jobbindata från en HTTP(s)-URL](job-input-from-http-how-to.md).

![Spela upp en video med Azure Media Player](./media/stream-files-tutorial-with-api/final-video.png)

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Ladda ned exempel appen som beskrivs i avsnittet.
> * Granska koden som laddar upp, kodar och strömmar.
> * Kör appen.
> * Testa strömnings-URL: en.
> * Rensa resurser.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har Visual Studio installerat kan du hämta [Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Skapa ett Media Services-konto](./account-create-how-to.md).<br/>Se till att komma ihåg de värden som du använde för resursgruppens namn och namnet på Media Services-kontot.
- Följ stegen i [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Få åtkomst till Azure Media Services-API med Azure CLI) och spara autentiseringsuppgifterna. Du måste använda dem för att få åtkomst till API: et.

## <a name="download-and-set-up-the-sample"></a>Hämta och konfigurera exemplet

Klona en GitHub-lagringsplats som har strömmande .NET-exempel till din dator med hjälp av följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Du hittar exemplet i mappen [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

Öppna [appsettings.jspå](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/appsettings.json) i det nedladdade projektet. Ersätt värdena med autentiseringsuppgifterna som du fick från avsnittet om [åtkomst till API:er](./access-api-howto.md).

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Granska koden som överför, kodar och strömmar

Det här avsnittet går igenom funktionerna som definierats i filen [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) för projektet *UploadEncodeAndStreamFiles*.

Exemplet utför följande åtgärder:

1. Skapar en ny **transformering** (först kontrolleras om den angivna transformeringen finns).
2. Skapar en utgående **till gång** som används som kodnings **jobbets** utdata.
3. Skapa en **indatatillgång** och ladda upp den angivna lokala videofilen till den. TIllgången används som jobbets indata.
4. Skickar kodningsjobbet med de indata och utdata som skapades.
5. Kontrollerar jobbets status.
6. Skapa en **positionerare för direktuppspelning**.
7. Skapar strömnings-URL:er.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Börja använda API:er för Media Services med .NET SDK

Om du vill börja använda API:er för Media Services med .NET, måste du skapa ett **AzureMediaServicesClient**-objekt. Om du vill skapa objektet måste du ange autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I den kod som du har klonat i början av artikeln skapar funktionen **GetCredentialsAsync** objektet ServiceClientCredentials baserat på de autentiseringsuppgifter som anges i den lokala konfigurationsfilen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Skapa en indatatillgång och ladda upp en lokal fil till den

Funktionen **CreateInputAsset** skapar en ny indata [till gång](/rest/api/media/assets) och laddar upp den angivna lokala video filen i den. Den här **till gången** används som inmatad till ditt kodnings jobb. I Media Services v3 kan inaktuella inloggade **jobb** antingen vara en **till gång** eller ett innehåll som du gör tillgängligt för ditt Media Services-konto via HTTPS-URL: er. Information om hur du kodar från en HTTPS-URL finns i [den här](job-input-from-http-how-to.md) artikeln.

I Media Services v3 använder du Azure Storage-API:er till att ladda upp filer. I följande .NET-kodfragment visas hur du gör detta.

Den här funktionen utför följande åtgärder:

* Skapar en **till gång**.
* Hämtar en skrivbar [SAS-URL](../../storage/common/storage-sas-overview.md) till till gångens [behållare i lagringen](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container).

    Om du använder funktionen [ListContainerSas](/rest/api/media/assets/listcontainersas) för att hämta SAS-URL: er, Observera att funktionen returnerar flera SAS-URL: er eftersom det finns två lagrings konto nycklar för varje lagrings konto. Ett lagrings konto har två nycklar eftersom det tillåter sömlös rotation av lagrings konto nycklar (till exempel att ändra ett när du använder den andra, och sedan börja använda den nya nyckeln och rotera den andra nyckeln). Den första SAS-URL: en representerar lagrings KEY1 och andra lagrings key2.
* Överför filen till behållaren i lagrings utrymmet med SAS-URL: en.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Skapa en utdatatillgång där resultatet av ett jobb lagras

Utmatnings [till gången](/rest/api/media/assets) lagrar resultatet av ditt kodnings jobb. Projektet definierar funktionen **DownloadResults** som laddar ner resultaten från den här utdatatillgången till mappen ”utdata”, så att du kan se vad du har fått.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Skapa en transformering och ett jobb som kodar den uppladdade filen

När du kodar eller bearbetar innehåll i Media Services, är det ett vanligt mönster för att ställa in kodnings inställningar som ett recept. Du skickar sedan ett **Jobb** som tillämpar receptet på en video. Genom att skicka nya jobb för varje ny video använder du det receptet på alla videor i biblioteket. Ett recept i Media Services kallas för en **transformering**. Mer information finns i [Transformeringar och jobb](./transform-jobs-concept.md). Det exempel som beskrivs i självstudien definierar ett recept som kodar videon för att strömma den till olika iOS- och Android-enheter.

#### <a name="transform"></a>Transformering

När du skapar en ny instans för en [Transformering](/rest/api/media/transforms), måste du ange vilken utdata du vill att den ska skapa. Den obligatoriska parametern är objektet **TransformOutput** som visas i koden nedan. Varje **TransformOutput** innehåller en **Förinställning**. I **Förinställning** finns stegvisa anvisningar för den video- och/eller ljudbearbetning som ska användas för att generera önskad **TransformOutput**. Det exempel som beskrivs i artikeln använder en inbyggd förinställning som kallas **AdaptiveStreaming**. Förinställningen kodar indatavideon i en automatiskt genererad bithastighetsstege (par för bithastighetsupplösning) som baseras på indatans upplösning och bithastighet, samt producerar ISO MP4-filer med H.264-video och AAC-ljud som motsvarar varje par för bithastighetsupplösningen. Information om denna förinställning finns i [Automatisk generering av bithastighetsstege](encode-autogen-bitrate-ladder.md).

Du kan använda en inbyggd EncoderNamedPreset eller anpassade förinställningar. Mer information finns i [Så här anpassar du förinställningar för kodning](encode-custom-presets-how-to.md).

När du skapar en [Transformering](/rest/api/media/transforms) bör du först kontrollera om det redan finns en. Det gör du med metoden **Get** som visas i koden nedan. I Media Services v3 returnerar **Get**-metoderna i entiteter **null** om entiteten inte finns (skiftlägesokänslig kontroll av namnet).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Jobb

Som nämns ovan är objektet [Transformering](/rest/api/media/transforms) receptet och ett [Jobb](/rest/api/media/jobs) är det faktiska begärandet till Media Services om att tillämpa **transformeringen** på en indatavideo eller ett ljudinnehåll. **Jobb** anger information som platsen för indatavideon och platsen för utdatan.

I det här exemplet har indatavideon laddats upp från din lokala dator. Om du vill lära dig hur du kodar från en HTTPS-URL, se [den här](job-input-from-http-how-to.md) artikeln.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Vänta tills jobbet är klart

Jobbet tar en stund att slutföra och du meddelas när detta sker. Kod exemplet nedan visar hur du avsöker tjänsten för [jobbets](/rest/api/media/jobs)status. Avsökningen är inte en rekommenderad metod för produktion av appar på grund av potentiell latens. Avsökningen kan begränsas om den överanvänds på ett konto. Utvecklare bör i stället använda Event Grid.

Event Grid är utformat för hög tillgänglighet, konsekvent prestanda och dynamisk skalning. Med Event Grid kan dina appar lyssna efter och reagera på händelser från i princip alla Azure-tjänster, samt även från anpassade källor. Med enkel och HTTP-baserad reaktiv händelsehantering blir det lättare att skapa effektiva lösningar med hjälp av intelligent filtrering och dirigering av händelser.  Se [Dirigera händelser till en anpassad webbslutpunkt](monitoring/job-state-events-cli-how-to.md).

**Jobb** har vanligtvis följande tillstånd: **Schemalagd**, **I kö**, **Bearbetas**, **Slutförd** (slutlig status). Om jobbet har påträffat ett fel visas tillståndet **Fel**. Om jobbet håller på att avbrytas **avbryts du och** **annulleras** när det är färdigt.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Jobbfelkoder

Se [Felkoder](/rest/api/media/jobs/get#joberrorcode).

### <a name="get-a-streaming-locator"></a>Hämta en positionerare för direktuppspelning

När kodningen är klar är nästa steg att göra videon i utdatatillgången tillgänglig för uppspelning av klienterna. Du kan göra det tillgängligt i två steg: börja med att skapa en [strömmande Locator](/rest/api/media/streaminglocators)och en andra, och skapa de strömmande URL: er som klienter kan använda.

Processen att skapa en **positionerare för direktuppspelning** kallas för publicering. Som standard är **streaming Locator** giltig omedelbart efter att du har gjort API-anrop och varar tills den tas bort, om du inte konfigurerar de valfria start-och slut tiderna.

När du skapar en [StreamingLocator](/rest/api/media/streaminglocators)måste du ange önskad **StreamingPolicyName**. I det här exemplet kommer du att strömma in-Clear (eller icke-krypterat innehåll) så att den fördefinierade rensnings principen för att strömma (**PredefinedStreamingPolicy. ClearStreamingOnly**) används.

> [!IMPORTANT]
> Om du använder en anpassad [strömningsprincip](/rest/api/media/streamingpolicies) bör du skapa en begränsad uppsättning av sådana principer för ditt Media Service-konto, och återanvända dem för dina StreamingLocators när samma krypterings- och protokollalternativ krävs. Media Service-kontot har en kvot för antalet strömningsprincipposter. Du bör inte skapa en ny strömmande princip för varje strömmande positionerare.

I följande kod förutsätter vi att du anropar funktionen med ett unikt locatorName.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Även om exemplet i det här avsnittet beskriver strömning, kan du använda samma anrop för att skapa en positionerare för direktuppspelning som levererar video via progressiv nedladdning.

### <a name="get-streaming-urls"></a>Hämta strömnings-URL:er

Nu när [positioneraren för direktuppspelning](/rest/api/media/streaminglocators) har skapats kan du hämta direktuppspelnings-URL:erna, som du ser i **GetStreamingURLs**. För att skapa en webbadress måste du sammanfoga [strömningsslutpunktens](/rest/api/media/streamingendpoints) värdnamn och sökvägen för **positioneraren för direktuppspelning**. I det här exemplet används *standardvärdet* **Slutpunkt för direktuppspelning**. När du skapar ett Media Service-konto första gången kommer detta *standardvärde för* **StreamingEndpoint** vara i ett stoppat tillstånd, så du måste anropa **Starta**.

> [!NOTE]
> I den här metoden behöver du det locatorName som användes när du skapade **positioneraren för direktuppspelning** för utdatatillgången.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Rensa resurser på ditt Media Services-konto

I allmänhet bör du rensa allt utom de objekt som du planerar att återanvända (normalt återanvänder du transformeringar och du behåller StreamingLocators osv.). Om du vill att ditt konto ska rensas efter experimentering tar du bort de resurser som du inte planerar att återanvända. Följande kod tar till exempel bort jobbet, skapade till gångar och innehålls nyckel princip:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Kör exempelappen

1. Tryck på Ctrl + F5 för att köra *EncodeAndStreamFiles* -appen.
2. Kopiera en av de strömmande URL:erna från konsolen.

I det här exemplet visas URL:er som kan användas för att spela upp videon med olika protokoll:

![Exempel på utdata som visar URL: er för Media Services strömmande video](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Testa strömnings-URL:en

I den här artikeln används Azure Media Player till att testa strömningen.

> [!NOTE]
> Om en spelare finns på en HTTPS-webbplats uppdaterar du URL:en till ”HTTPS”.

1. Öppna en webbläsare och gå till [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. I rutan **URL:** klistrar du in en av de strömmande URL-värdena som du fick när du körde appen.
3. Välj **uppdaterings spelaren**.

Azure Media Player kan användas för testning men bör inte användas i en produktions miljö.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive Media Services och de lagringskonton som du skapade för självstudien, tar du bort resursgruppen som du skapade tidigare.

Kör följande CLI-kommando:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Flertrådsteknik

Azure Media Services v3 SDK: erna är inte tråd säkra. När du utvecklar en app med flera trådar bör du skapa och använda ett nytt AzureMediaServicesClient-objekt per tråd.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du ska ladda upp, koda och strömma videon kan du läsa följande artikel: 

> [!div class="nextstepaction"]
> [Analysera videor](analyze-videos-tutorial.md)
