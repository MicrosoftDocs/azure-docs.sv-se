---
title: Snabb start för Content Moderator .NET-klient bibliotek
titleSuffix: Azure Cognitive Services
description: Kom igång med Content Moderator klient biblioteket för .NET med den här snabb starten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: b064f6515c8ff4b3be4219cc6516ef25dabf0679
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510134"
---
Kom igång med Content Moderator klient biblioteket för .NET. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Content Moderator är en kognitiv tjänst som kontrollerar text-, bild-och video innehåll för material som kan vara stötande, riskfyllda eller på annat sätt olämpligt. När sådant material hittas tillämpar tjänsten lämplig etiketter (flaggor) på innehållet. Din app kan sedan hantera flaggat innehåll för att följa regler eller underhålla avsedd miljö för användare.

Använd Content Moderator klient bibliotek för .NET för att:

* [Måttlig text](#moderate-text)
* [Måttliga bilder](#moderate-images)
* [Skapa en granskning](#create-a-review)

[Referens dokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)  |  [Exempel](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* Den aktuella versionen av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-content-moderator-azure-resource"></a>Skapa en Content Moderator Azure-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Content Moderator med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. Du kan även:

* Visa din resurs på [Azure Portal](https://portal.azure.com/)

När du har fått en nyckel från resursen [skapar du miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel-och slut punkts-URL: en, med namnet respektive `CONTENT_MODERATOR_SUBSCRIPTION_KEY` `CONTENT_MODERATOR_ENDPOINT` .

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

Skapa ett nytt .NET Core-program i din önskade text redigerare eller IDE. 

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `content-moderator-quickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: *program.cs*.

```console
dotnet new console -n content-moderator-quickstart
```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

```console
dotnet build
```

Build-utdata får inte innehålla varningar eller fel. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Från projekt katalogen öppnar du *program.cs* -filen i önskat redigerings program eller IDE. Lägg till följande `using`-uttryck:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

I **program** -klassen skapar du variabler för resursens slut punkts plats och nyckel som miljövariabler.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> Om du har skapat miljövariablerna efter att du har startat programmet måste du stänga och öppna redigerings programmet, IDE eller gränssnittet som kör det för att få åtkomst till variablerna.

### <a name="install-the-client-library"></a>Installera klient biblioteket

I program katalogen installerar du Content Moderator klient biblioteket för .NET med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Om du använder Visual Studio IDE är klient biblioteket tillgängligt som ett nedladdnings Bart NuGet-paket.

## <a name="object-model"></a>Objekt modell

Följande klasser hanterar några av de viktigaste funktionerna i Content Moderator .NET-klient biblioteket.

|Namn|Beskrivning|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Den här klassen krävs för alla Content Moderator-funktioner. Du instansierar det med din prenumerations information och använder den för att skapa instanser av andra klasser.|
|[ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Den här klassen innehåller funktioner för att analysera bilder för innehåll som är olämpligt för barn, personlig information eller mänskliga ansikten.|
|[TextModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Den här klassen innehåller funktioner för att analysera text för språk, svordomar, fel och personlig information.|
|[Omdömen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Den här klassen innehåller funktionerna i gransknings-API: erna, inklusive metoder för att skapa jobb, anpassade arbets flöden och mänsklig granskning.|

## <a name="code-examples"></a>Kodexempel


De här kodfragmenten visar hur du gör följande uppgifter med Content Moderator-klient biblioteket för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Måttlig text](#moderate-text)
* [Måttliga bilder](#moderate-images)
* [Skapa en granskning](#create-a-review)

## <a name="authenticate-the-client"></a>Autentisera klienten

I en ny metod instansierar du klient objekt med din slut punkt och nyckel. Du behöver inte en annan klient för varje scenario, men den kan hjälpa dig att hålla koden organiserad.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>Måttlig text

I följande kod används en Content Moderator-klient för att analysera text texten och skriva ut resultatet till-konsolen. I roten för din **program** klass definierar du in-och utdatafiler:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

I roten för ditt projekt och Lägg till en *TextFile.txt* -fil. Lägg till din egen text i filen eller Använd följande exempel text:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Lägg till följande metod anrop till din `Main` metod:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

Definiera sedan text redigerings metoden någonstans i **program** klassen:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Måttliga bilder

I följande kod används en Content Moderator-klient, tillsammans med ett [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) -objekt, för att analysera fjärravbildningar för vuxen och vågat innehåll.

> [!NOTE]
> Du kan också analysera innehållet i en lokal avbildning. Se [referens dokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) för metoder och åtgärder som fungerar med lokala avbildningar.

### <a name="get-sample-images"></a>Hämta exempel bilder

Definiera indata-och utdatafiler:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Skapa sedan indatafilen *ImageFiles.txt*i roten för projektet. I den här filen lägger du till URL: er för avbildningar för att analysera &mdash; en URL på varje rad. Du kan använda följande exempel bilder:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

Skicka in dina indata-och utdatafiler till följande metod anrop i- `Main` metoden. Du definierar den här metoden i ett senare steg.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_call)]

### <a name="define-helper-class"></a>Definiera hjälp klass

Lägg till följande klass definition i **program** klassen. Den här inre klassen hanterar bild moderator resultat.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Definiera avbildnings moderator metoden

Följande metod itererar igenom bild-URL: erna i en textfil, skapar en **EvaluationData** -instans och analyserar bilden för innehåll, text och mänskliga ansikten som är vuxna/vågat. Sedan läggs den slutliga **EvaluationData** -instansen till i en lista och skriver den fullständiga listan med returnerade data till-konsolen.

#### <a name="iterate-through-image-urls"></a>Iterera genom bild-URL: er

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Analysera innehåll

Mer information om bildattributen som Content Moderator skärmar för finns i hand boken för [bild moderator](../../image-moderation-api.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Skriv redigerings resultat till fil

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Skapa en granskning

Du kan använda Content Moderator .NET-klient biblioteket för att mata in innehåll i [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com) så att de mänskliga moderatorerna kan granska det. Mer information om gransknings verktyget finns i [konceptuell guide för gransknings verktyget](../../review-tool-user-guide/human-in-the-loop.md).

Metoden i det här avsnittet använder klassen [granskningar](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) för att skapa en granskning, hämta dess ID och kontrol lera informationen efter att ha tagit emot mänsklig indata via gransknings verktygets webb Portal. Den loggar all den här informationen i en textfil med utdata. Anropa metoden från din `Main` metod:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>Hämta exempel bilder

Deklarera följande matris i roten i **program** klassen. Den här variabeln refererar till en exempel bild som ska användas för att skapa granskningen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Hämta granska autentiseringsuppgifter

Logga in på [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com) och hämta ditt grupp namn. Tilldela den sedan den aktuella variabeln i **program** klassen. Alternativt kan du skapa en slut punkt för återanrop för att ta emot uppdateringar om granskningens aktivitet.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Definiera hjälp klass

Lägg till följande klass definition i **program** klassen. Den här klassen kommer att användas för att representera en enda gransknings instans som skickas till gransknings verktyget.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Definiera hjälp metod

Lägg till följande metod i klassen **Program**. Med den här metoden skrivs resultaten från gransknings frågor till den utgående text filen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Definiera metoden för att skapa granskning

Nu är du redo att definiera metoden som hanterar gransknings skapandet och-frågor. Lägg till en ny metod, **CreateReviews**och definiera följande lokala variabler.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Publicera granskningar i gransknings verktyget

Lägg sedan till följande kod för att iterera genom de här exempel bilderna, Lägg till metadata och skicka dem till gransknings verktyget i en enda batch. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

Objektet som returneras från API-anropet innehåller unika ID-värden för varje avbildning som laddats upp. Följande kod tolkar dessa ID: n och använder dem sedan för att fråga Content Moderator efter status för varje avbildning i batchen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Visa gransknings information

Följande kod gör att programmet väntar på användarindata. När du kommer till det här steget vid körning kan du gå till [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com) själv, kontrol lera att exempel bilden har laddats upp och interagera med den. Information om hur du interagerar med en granskning finns i [instruktionen gransknings anvisningar](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). När du är klar kan du trycka på valfri tangent för att fortsätta programmet och hämta resultatet från gransknings processen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

Om du har använt en callback-slutpunkt i det här scenariot, ska den ta emot en händelse i följande format:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Kör programmet

Kör programmet från program katalogen med `dotnet run` kommandot.

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder Content Moderator .NET-biblioteket för att utföra redigerings uppgifter. Läs sedan mer om hur du använder avbildningen eller andra media genom att läsa en konceptuell guide.

> [!div class="nextstepaction"]
> [Bild moderator koncept](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Vad är Azure Content Moderator?](../../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs).
