---
title: Content Moderator snabbstart för .NET-klientbibliotek
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten lär du dig hur du kommer igång med Azure Content Moderator klientbibliotek för .NET. Skapa programvara för innehållsfiltrering i din app för att följa regler eller underhålla den avsedda miljön för dina användare.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.author: pafarley
ms.custom: devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: 573778316802b6e445b4c9d78576a8813af514a6
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726146"
---
Kom igång med Azure Content Moderator klientbibliotek för .NET. Följ de här stegen för att installera NuGet-paketet och prova exempelkoden för grundläggande uppgifter. 

Content Moderator är en AI-tjänst som gör att du kan hantera innehåll som är potentiellt stötande, riskabelt eller på annat sätt oönskat. Använd den AI-baserade innehållsmodereringstjänsten för att söka igenom text, bilder och videor och tillämpa innehållsflaggor automatiskt. Integrera sedan din app med granskningsverktyget, en onlinemodermiljö för ett team med mänskliga granskare. Skapa programvara för innehållsfiltrering i din app för att följa regler eller underhålla den avsedda miljön för dina användare.

Använd Content Moderator för .NET för att:

* Moderera text
* Moderera bilder
* Skapa en granskning

[Referensdokumentation](/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)  |  [Exempel](../../samples-dotnet.md)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– Skapa en utan kostnad](https://azure.microsoft.com/free/cognitive-services/)
* Den [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) eller den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* När du har din Azure-prenumeration skapar du Content Moderator resurs för att skapa Content Moderator resurs i Azure Portal för att hämta <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" "  target="_blank"> din nyckel och </a> slutpunkt. Vänta tills den har distribuerats och klicka **på knappen Gå till** resurs.
    * Du behöver nyckeln och slutpunkten från den resurs som du skapar för att ansluta ditt program till Content Moderator. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Använd Visual Studio skapa ett nytt .NET Core-program. 

### <a name="install-the-client-library"></a>Installera klientbiblioteket 

När du har skapat ett nytt projekt installerar du klientbiblioteket genom att högerklicka på projektlösningen i **Solution Explorer** och välja **Hantera NuGet-paket.** I pakethanteraren som öppnas väljer du **Bläddra,** **markerar Inkludera förhandsversion** och söker efter `Microsoft.Azure.CognitiveServices.ContentModerator` . Välj version `2.0.0` och sedan **Installera**. 

#### <a name="cli"></a>[CLI](#tab/cli)

I ett konsolfönster (till exempel cmd, PowerShell eller Bash) använder du kommandot för att skapa en `dotnet new` ny konsolapp med namnet `content-moderator-quickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *Program.cs*.

```console
dotnet new console -n content-moderator-quickstart
```

Ändra katalogen till den nyligen skapade appmappen. Du kan skapa programmet med:

```console
dotnet build
```

Build-utdata får inte innehålla några varningar eller fel. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installera klientbiblioteket 

I programkatalogen installerar du Content Moderator för .NET med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

---

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs), som innehåller kodexe exemplen i den här snabbstarten.

Öppna filen *Program.cs* i önskad redigerare eller IDE från projektkatalogen. Lägg till följande `using`-uttryck:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

I **klassen Program** skapar du variabler för resursens nyckel och slutpunkt.

> [!IMPORTANT]
> Gå till Azure-portalen. Om den Content Moderator som du  skapade i avsnittet Förutsättningar har distribuerats klickar du på **knappen Gå till** resurs under **Nästa steg.** Du hittar din nyckel och slutpunkt på resursens nyckel- **och slutpunktssida** under **resurshantering .** 
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och aldrig publicera den offentligt. Överväg att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter för produktion. Mer information finns i artikeln Cognitive Services [säkerhet.](../../../cognitive-services-security.md)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]


I programmets -metod `main()` lägger du till anrop för de metoder som används i den här snabbstarten. Du kommer att skapa dessa senare.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]


## <a name="object-model"></a>Objektmodell

Följande klasser hanterar några av de viktigaste funktionerna i Content Moderator .NET-klientbiblioteket.

|Name|Beskrivning|
|---|---|
|[ContentModeratorClient](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient)|Den här klassen behövs för alla Content Moderator funktioner. Du instansierar den med din prenumerationsinformation och använder den för att skapa instanser av andra klasser.|
|[ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation)|Den här klassen innehåller funktioner för att analysera bilder för vuxet innehåll, personlig information eller mänskliga ansikten.|
|[TextModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation)|Den här klassen innehåller funktioner för att analysera text för språk, svordomar, fel och personlig information.|
|[Omdömen](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews)|Den här klassen innehåller funktionerna i gransknings-API:erna, inklusive metoder för att skapa jobb, anpassade arbetsflöden och mänskliga granskningar.|

## <a name="code-examples"></a>Kodexempel

Dessa kodfragment visar hur du utför följande uppgifter med Content Moderator klientbibliotek för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Moderera text](#moderate-text)
* [Moderera bilder](#moderate-images)
* [Skapa en granskning](#create-a-review)

## <a name="authenticate-the-client"></a>Autentisera klienten

I en ny metod instansierar du klientobjekt med din slutpunkt och nyckel.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_auth)]

## <a name="moderate-text"></a>Moderera text

Följande kod använder en Content Moderator-klient för att analysera en texttext och skriva ut resultatet till konsolen. Definiera indata- och **utdatafilerna** i roten för klassen Program:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

Lägg sedan till enTextFile.txt *projektroten.* Lägg till din egen text i den här filen eller använd följande exempeltext:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```


Definiera sedan textmodereringsmetoden någonstans i **klassen** Program:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Moderera bilder

I följande kod används en Content Moderator klient, tillsammans med ett [ImageModeration-objekt,](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation) för att analysera fjärrbilder efter vuxet och oanständigt innehåll.

> [!NOTE]
> Du kan också analysera innehållet i en lokal bild. Se [referensdokumentationen](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) för metoder och åtgärder som fungerar med lokala avbildningar.

### <a name="get-sample-images"></a>Hämta exempelbilder

Definiera dina indata- och utdatafiler i roten för **klassen Program:**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Skapa sedan indatafilen, *ImageFiles.txt*, i roten av projektet. I den här filen lägger du till url:erna för bilder för att &mdash; analysera en URL på varje rad. Du kan använda följande exempelbilder:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Definiera hjälpklass

Lägg till följande klassdefinition i **klassen** Program. Den här inre klassen hanterar bildmodereringsresultat.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Definiera bildmodereringsmetoden

Följande metod itererar genom bild-URL:erna i en textfil, skapar en **EvaluationData-instans** och analyserar bilden för vuxet/racy-innehåll, text och mänskliga ansikten. Sedan lägger den till den slutliga **EvaluationData-instansen** i en lista och skriver den fullständiga listan över returnerade data till konsolen.

#### <a name="iterate-through-images"></a>Iterera genom bilder

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Analysera innehåll

Mer information om de bildattribut som Content Moderator skärmar för finns i guiden [Begrepp för bildmoderering.](../../image-moderation-api.md)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Skriva modereringsresultat till fil

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Skapa en granskning

Du kan använda Content Moderator .NET-klientbiblioteket för att mata in innehåll i granskningsverktyget [så](https://contentmoderator.cognitive.microsoft.com) att mänskliga moderatorer kan granska det. Mer information om granskningsverktyget finns i [begreppsguiden för granskningsverktyget.](../../review-tool-user-guide/human-in-the-loop.md)

Metoden i det här avsnittet använder [klassen Reviews](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews) för att skapa en granskning, hämta dess ID och kontrollera dess information när den har fått mänsklig information via granskningsverktygets webbportal. Den loggar all den här informationen i en utdatatextfil. 

### <a name="get-sample-images"></a>Hämta exempelbilder

Deklarera följande matris i roten för klassen **Program.** Den här variabeln refererar till en exempelbild som ska användas för att skapa granskningen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Hämta autentiseringsuppgifter för granskning

Logga in på [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com) och hämta ditt teamnamn. Tilldela den sedan till lämplig variabel i **klassen** Program. Du kan också konfigurera en återanropsslutpunkt för att ta emot uppdateringar av granskningsaktiviteten.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Definiera hjälpklass

Lägg till följande klassdefinition i **klassen** Program. Den här klassen används för att representera en enda granskningsinstans som skickas till granskningsverktyget.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Definiera hjälpmetod

Lägg till följande metod i klassen **Program**. Den här metoden skriver resultatet av granskningsfrågor till utdatatextfilen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Definiera metoden för att skapa granskning

Nu är du redo att definiera den metod som ska hantera skapande och frågegranskning. Lägg till en ny **metod, CreateReviews,** och definiera följande lokala variabler.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Publicera granskningar i granskningsverktyget

Lägg sedan till följande kod för att iterera genom de angivna exempelbilderna, lägga till metadata och skicka dem till granskningsverktyget i en enda batch. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

Objektet som returneras från API-anropet innehåller unika ID-värden för varje bild som laddas upp. Följande kod parsar dessa ID:er och använder dem sedan för Content Moderator frågar efter status för varje avbildning i batchen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Hämta granskningsinformation

Följande kod gör att programmet väntar på användarindata. När du kommer till det här steget vid [](https://contentmoderator.cognitive.microsoft.com) körningen kan du gå till granskningsverktyget själv, kontrollera att exempelavbildningen har laddats upp och interagera med den. Information om hur du interagerar med en granskning finns i guiden [Granska](../../review-tool-user-guide/review-moderated-images.md). När du är klar kan du trycka på valfri tangent för att fortsätta programmet och hämta resultatet av granskningsprocessen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

Om du använde en återanropsslutpunkt i det här scenariot bör den ta emot en händelse i det här formatet:

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

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Kör programmet genom att klicka **på knappen** Felsök längst upp i IDE-fönstret.

#### <a name="cli"></a>[CLI](#tab/cli)

Kör programmet från programkatalogen med `dotnet run` kommandot .

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resursgruppen. När du tar bort resursgruppen tas även alla andra resurser som är associerade med den bort.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du använder Content Moderator .NET-biblioteket för att utföra modereringsuppgifter. Härnäst kan du läsa mer om moderering av bilder eller andra media genom att läsa en konceptuell guide.

> [!div class="nextstepaction"]
> [Begrepp inom bildmoderering](../../image-moderation-api.md)