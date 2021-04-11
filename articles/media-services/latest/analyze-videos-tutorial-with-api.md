---
title: Analysera videor med Media Services v3
description: Lär dig hur du analyserar videor med Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: f9c52544160e52c29b2d0e53aa957b40017dc8df
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870491"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>Självstudie: analysera videor med Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här självstudien visar hur du analyserar videor med Azure Media Services. Det finns många scenarier där du kanske vill veta mer om inspelat video- eller ljudinnehåll. Organisationer kan till exempel för att uppnå högre kundnöjdhet köra tal-till-text-bearbetning och konvertera inspelningar från kundtjänst till en sökbar katalog med index och instrumentpaneler.

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Ladda ned exempel appen som beskrivs i avsnittet.
> * Granska koden som analyserar den angivna videon.
> * Kör appen.
> * Granska resultatet.
> * Rensa resurser.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Efterlevnad, sekretess och säkerhet
 
Som en viktig påminnelse måste du följa alla tillämpliga lagar i din användning av Video Indexer. Du får inte använda Video Indexer eller någon annan Azure-tjänst på ett sätt som strider mot andras rättigheter. Innan du laddar upp videor, inklusive bio metriska data, till den Video Indexer tjänsten för bearbetning och lagring måste du ha alla lämpliga rättigheter, inklusive alla lämpliga medgivanden, från personerna i videon. För att lära dig om efterlevnad, sekretess och säkerhet i Video Indexer, [Cognitive Services villkoren](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)i Azure. För Microsofts sekretess avtal och hantering av dina data kan du läsa Microsofts [sekretess policy](https://privacy.microsoft.com/PrivacyStatement), [Online Services-villkoren](https://www.microsoft.com/licensing/product-licensing/products) (ost) och [data bearbetnings tillägget](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Mer sekretess information, inklusive för datakvarhållning, borttagning/förstörelse, finns i OST-filen och [här](../video-indexer/faq.md). Genom att använda Video Indexer samtycker du till att vara kopplad till Cognitive Services villkor, OST, DPA och sekretess policyn.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har Visual Studio installerat kan du hämta [Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Skapa ett Media Services-konto](./create-account-howto.md).<br/>Se till att komma ihåg de värden som du använde för resursgruppens namn och namnet på Media Services-kontot.
- Följ stegen i [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Få åtkomst till Azure Media Services-API med Azure CLI) och spara autentiseringsuppgifterna. Du måste använda dem för att få åtkomst till API: et.

## <a name="download-and-configure-the-sample"></a>Ladda ned och konfigurera exemplet

Klona en GitHub-lagringsplats som innehåller .NET-exemplet till din dator med följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Du hittar exemplet i mappen [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos).

Öppna [appsettings.jspå](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) i det nedladdade projektet. Ersätt värdena med de autentiseringsuppgifter som du har fått från [att komma åt API: er](./access-api-howto.md).

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>Granska koden som analyserar den angivna videon

Det här avsnittet går igenom funktionerna som definierades i filen [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) för projektet *AnalyzeVideos*.

Exemplet utför följande åtgärder:

1. Skapa en **transformering** och ett **jobb** som analyserar dina videor.
2. Skapar en inkommande **tillgång** och överför videon till den. TIllgången används som jobbets indata.
3. Skapar en utgående tillgång som lagrar utdata för jobbet.
4. Skickar jobbet.
5. Kontrollerar jobbets status.
6. Laddar ned filer som härrör från körningen av jobbet.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Börja använda API:er för Media Services med .NET SDK

Om du vill börja använda API:er för Media Services med .NET, måste du skapa ett **AzureMediaServicesClient**-objekt. När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I den kod som du har klonat i början av artikeln skapar funktionen **GetCredentialsAsync** objektet ServiceClientCredentials baserat på de autentiseringsuppgifter som anges i den lokala konfigurationsfilen. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Skapa en indatatillgång och ladda upp en lokal fil till den 

Funktionen **CreateInputAsset** skapar en ny indata [till gång](/rest/api/media/assets) och laddar upp den angivna lokala video filen i den. Tillgången används som indata för kodningsjobbet. I Media Services v3 kan indata till ett jobb antingen vara en tillgång eller innehåll som du gör tillgängligt för Media Services-kontot via HTTPS-webbadresser. Information om hur du kodar från en HTTPS-URL finns i [den här](job-input-from-http-how-to.md) artikeln.  

I Media Services v3 använder du Azure Storage-API:er till att ladda upp filer. I följande .NET-kodfragment visas hur du gör detta.

Följande funktion utför följande åtgärder:

* Skapar en till gång.
* Hämtar en skrivbar [SAS-URL](../../storage/common/storage-sas-overview.md) till till gångens [behållare i lagringen](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container).

    Om du använder funktionen [ListContainerSas](/rest/api/media/assets/listcontainersas) för att hämta SAS-URL: er, Observera att funktionen returnerar flera SAS-URL: er eftersom det finns två lagrings konto nycklar för varje lagrings konto. Ett lagrings konto har två nycklar eftersom det tillåter sömlös rotation av lagrings konto nycklar (till exempel att ändra ett när du använder den andra, och sedan börja använda den nya nyckeln och rotera den andra nyckeln). Den första SAS-URL: en representerar lagrings KEY1 och andra lagrings key2.
* Överför filen till behållaren i lagrings utrymmet med SAS-URL: en.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Skapa en utdatatillgång där resultatet av ett jobb lagras

[Utdatatillgången](/rest/api/media/assets) lagrar resultatet av ditt jobb. Projektet definierar funktionen **DownloadResults** som laddar ner resultaten från den här utdatatillgången till mappen ”utdata”, så att du kan se vad du har fått.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Skapa en transformering och ett jobb som analyserar videor

När du kodar eller bearbetar innehåll i Media Services, är det ett vanligt mönster för att ställa in kodnings inställningar som ett recept. Du skickar sedan ett **Jobb** som tillämpar receptet på en video. Genom att skicka nya jobb för varje ny video använder du det receptet på alla videor i biblioteket. Ett recept i Media Services kallas för en **transformering**. Mer information finns i [transformeringar och jobb](./transforms-jobs-concept.md). Det exempel som beskrivs i självstudien definierar ett recept som analyserar den angivna videon.

#### <a name="transform"></a>Transformering

När du skapar en ny instans för en [Transformering](/rest/api/media/transforms), måste du ange vilken utdata du vill att den ska skapa. **TransformOutput**  är en obligatorisk parameter. Varje **TransformOutput** innehåller en **Förinställning**. I **Förinställning** finns stegvisa anvisningar för den video- och/eller ljudbearbetning som ska användas för att generera önskad **TransformOutput**. I det här exemplet används **VideoAnalyzerPreset** för för inställning och språket ("en-US") skickas till dess konstruktor ( `new VideoAnalyzerPreset("en-US")` ). Med denna förinställning kan du extrahera flera ljud- och videoinsikter från en video. Du kan använda förinställningen **AudioAnalyzerPreset** om du vill extrahera flera ljudinsikter från en video.

När du skapar en **transformering** kontrollerar du först om det redan finns en som redan finns med **Get** -metoden, som du ser i koden som följer. I Media Services v3 returnerar **Get**-metoderna i entiteter **null** om entiteten inte finns (skiftlägesokänslig kontroll av namnet).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Jobb

Som nämns ovan är objektet [Transformering](/rest/api/media/transforms) receptet och ett [Jobb](/rest/api/media/jobs) är det faktiska begärandet till Media Services om att tillämpa **transformeringen** på en indatavideo eller ett ljudinnehåll. **Jobbet** anger information som platsen för indata-videon och platsen för utdata. Du kan ange platsen för din video med: HTTPS-webbadresser, SAS-URL:er eller tillgångar som finns på ditt Media Services-konto.

I det här exemplet är jobbets indata en lokal video.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Vänta tills jobbet är klart

Det tar lite tid att slutföra jobbet. När du gör det, vill du bli meddelad. Det finns olika alternativ för att få meddelanden om när [jobben](/rest/api/media/jobs) är klara. Det enklaste alternativet (som visas här) är att använda avsökning.

Avsökningen är inte en rekommenderad metod för produktion av appar på grund av potentiell latens. Avsökningen kan begränsas om den överanvänds på ett konto. Utvecklare bör i stället använda Event Grid.

Event Grid är utformat för hög tillgänglighet, konsekvent prestanda och dynamisk skalning. Med Event Grid kan dina appar lyssna efter och reagera på händelser från i princip alla Azure-tjänster, samt även från anpassade källor. Med enkel och HTTP-baserad reaktiv händelsehantering blir det lättare att skapa effektiva lösningar med hjälp av intelligent filtrering och dirigering av händelser. Mer information finns i [dirigera händelser till en anpassad webb slut punkt](monitoring/job-state-events-cli-how-to.md).

**Jobb** har vanligtvis följande tillstånd: **Schemalagd**, **I kö**, **Bearbetas**, **Slutförd** (slutlig status). Om jobbet har kommit över ett fel visas **fel** tillstånd. Om jobbet håller på att avbrytas **avbryts du och** **avbryts** när det är färdigt.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Jobbfelkoder

Se [Felkoder](/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>Ladda ned jobbets resultat

Följande funktion hämtar resultatet från utmatnings [till gången](/rest/api/media/assets) till mappen "utdata", så att du kan granska resultatet av jobbet.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Rensa resurser på ditt Media Services-konto

[!INCLUDE [clean-up-warning](includes/clean-up-warning.md)]

I allmänhet bör du rensa allt utom objekt som du planerar att återanvända (normalt återanvänder du transformeringar och persist StreamingLocators). Om du vill att ditt konto ska rensas efter experimentering tar du bort de resurser som du inte planerar att återanvända. Följande kod tar till exempel bort jobb-och utmatnings till gången:

### <a name="delete-resources-with-code"></a>Ta bort resurser med kod

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

Du kan också använda CLI.

[!INCLUDE [clean-up-resources-cli](includes/clean-up-resources-cli.md)]

## <a name="run-the-sample-app"></a>Kör exempelappen

Tryck på Ctrl + F5 för att köra *AnalyzeVideos* -appen.

När vi kör programmet skapar jobbet miniatyrer för varje ansikte som hittas i videon. Det skapar också filen insights.json.

## <a name="examine-the-output"></a>Granska utdatan

Utdatafilen för videoanalysen kallas insights.json. Den här filen innehåller insikter om videon. Beskrivning av elementen i json-filen finns i artikeln [Medieintelligens](./analyzing-video-audio-files-concept.md).

## <a name="multithreading"></a>Flertrådsteknik

> [!WARNING]
> Azure Media Services v3 SDK: erna är inte tråd säkra. När du arbetar med en multi-trådad app bör du generera ett nytt AzureMediaServicesClient-objekt per tråd.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: ladda upp, koda och strömma filer](stream-files-tutorial-with-api.md)
