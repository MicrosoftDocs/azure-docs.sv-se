---
title: 'Snabb start: Visuellt innehåll klient bibliotek för .NET'
description: I den här snabb starten kommer du igång med Visuellt innehåll klient biblioteket för .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 645ae636adb1fcc3f3a79e6cbca618f4e341418f
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052788"
---
<a name="HOLTop"></a>

Använd Visuellt innehåll klient bibliotek för att:

* Analysera en bild för taggar, text beskrivning, ansikten, vuxen innehåll med mera.
* Skriv ut och handskriven text med Read API.

[Referens dokumentation](/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)  |  [Exempel](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) eller aktuell version av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" skapar du en visuellt innehåll resurs "  target="_blank"> skapa en visuellt innehåll resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Visuellt innehåll-tjänsten. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Skapa ett nytt .NET Core-program med Visual Studio. 

### <a name="install-the-client-library"></a>Installera klient biblioteket 

När du har skapat ett nytt projekt installerar du klient biblioteket genom att högerklicka på projekt lösningen i **Solution Explorer** och välja **Hantera NuGet-paket**. I paket hanteraren som öppnas väljer du **Bläddra**, markerar **ta med för hands version** och söker efter `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` . Välj version `6.0.0-preview.1` och **Installera** sedan. 

#### <a name="cli"></a>[CLI](#tab/cli)

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `computer-vision-quickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: *program.cs*.

```console
dotnet new console -n (product-name)-quickstart
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

### <a name="install-the-client-library"></a>Installera klient biblioteket

I program katalogen installerar du Visuellt innehåll klient biblioteket för .NET med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

---

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs), som innehåller kod exemplen i den här snabb starten.

Från projekt katalogen öppnar du *ComputerVisionQuickstart.cs* -filen i önskat redigerings program eller IDE. Lägg till följande `using` direktiv:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using)]

I programmets **program** klass skapar du variabler för resursens Azure-slutpunkt och nyckel.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_vars)]

> [!IMPORTANT]
> Gå till Azure-portalen. Om Visuellt innehåll resursen som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar din nyckel och slut punkt i resursens **nyckel och slut punkts** sida under **resurs hantering**. 
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Mer information finns i [säkerhets](../../../cognitive-services-security.md) artikeln Cognitive Services.

I programmets `Main` metod lägger du till anrop för de metoder som används i den här snabb starten. Du kommer att skapa dessa senare.


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyzeinmain)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

> [!div class="nextstepaction"]
> [Jag har konfigurerat klienten](?success=set-up-client#object-model) som [Jag stötte på ett problem](https://www.research.net/r/7QYZKHL?issue=set-up-client)

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Visuellt innehåll .NET SDK.

|Namn|Beskrivning|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | Den här klassen krävs för alla Visuellt innehåll-funktioner. Du instansierar det med din prenumerations information och använder den för att utföra de flesta avbildnings åtgärder.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| Den här klassen innehåller ytterligare metoder för **ComputerVisionClient**.|
|[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| Den här uppräkningen definierar de olika typerna av bild analys som kan göras i en standard analys åtgärd. Du anger en uppsättning VisualFeatureTypes-värden beroende på dina behov. |

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande uppgifter med Visuellt innehåll-klient biblioteket för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Analysera en bild](#analyze-an-image)
* [Skriv ut och handskriven text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Den här snabb starten förutsätter att du har [skapat miljövariabler](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) för din visuellt innehåll nyckel och slut punkt, med namnet respektive `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` .

I en ny metod instansierar du en klient med din slut punkt och nyckel. Skapa ett **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials?view=azure-dotnet)** -objekt med din nyckel och Använd den med slut punkten för att skapa ett **[ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet)** -objekt.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Jag autentiserade klienten att](?success=authenticate-client#analyze-an-image) [Jag stötte på ett problem](https://www.research.net/r/7QYZKHL?issue=authenticate-client)

## <a name="analyze-an-image"></a>Analysera en bild

Följande kod definierar en metod, `AnalyzeImageUrl` som använder klient objekt för att analysera en fjärravbildning och skriva ut resultaten. Metoden returnerar en text beskrivning, kategorisering, lista med taggar, identifierade ansikten, olämpliga innehålls flaggor, huvud färger och bildtyp.

> [!TIP]
> Du kan också analysera en lokal avbildning. Se [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) -metoderna, till exempel **AnalyzeImageInStreamAsync**. Eller, se exempel koden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) för scenarier som involverar lokala avbildningar.

### <a name="set-up-test-image"></a>Konfigurera test avbildning

I **program** -klassen sparar du en referens till URL: en för den avbildning som du vill analysera.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_url)]

### <a name="specify-visual-features"></a>Ange visuella funktioner

Definiera den nya metoden för bild analys. Lägg till koden nedan, som anger visuella funktioner som du vill extrahera i analysen. Se **[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)** -uppräkningen för en fullständig lista.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_visualfeatures)]

Infoga något av följande kod block i **AnalyzeImageUrl** -metoden för att implementera deras funktioner. Kom ihåg att lägga till en avslutande hak paren tes i slutet.

```csharp
}
```

### <a name="analyze"></a>Analysera

Metoden **AnalyzeImageAsync** returnerar ett **ImageAnalysis** -objekt som innehåller all extraherad information.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_call)]

I följande avsnitt visas hur du kan analysera den här informationen i detalj.

### <a name="get-image-description"></a>Beskrivning av Hämta avbildning

Följande kod hämtar listan över genererade under texter för avbildningen. Se [Beskriv avbildningar](../../concept-describing-images.md) för mer information.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Hämta bild kategori

Följande kod hämtar den identifierade kategorin för avbildningen. Se [kategorisera bilder](../../concept-categorizing-images.md) för mer information.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Hämta bild etiketter

Följande kod hämtar en uppsättning identifierade Taggar i avbildningen. Se [innehålls etiketter](../../concept-tagging-images.md) för mer information.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Upptäcka objekt

Följande kod identifierar vanliga objekt i avbildningen och skriver ut dem till-konsolen. Mer information finns i [objekt identifiering](../../concept-object-detection.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Identifiera varumärken

Följande kod identifierar företags märken och logo typer i bilden och skriver ut dem till-konsolen. Mer information finns i [varumärkes identifiering](../../concept-brand-detection.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Identifiera ansikten

Följande kod returnerar identifierade ansikten i bilden med deras Rectangle-koordinater och välj ansikts attribut. Mer information finns i [ansikts igenkänning](../../concept-detecting-faces.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Identifiera vuxna, vågat eller fullständig innehåll

Följande kod skriver ut den identifierade förekomsten av olämpligt innehåll i bilden. Mer information finns i [vuxen, vågat, fullständig-innehåll](../../concept-detecting-adult-content.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Hämta färg schema för bild

Följande kod skriver ut de identifierade färgattributen i bilden, till exempel dominerande färger och dekor färg. Se [färg scheman](../../concept-detecting-color-schemes.md) för mer information.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Hämta domänbaserat innehåll

Visuellt innehåll kan använda specialiserade modeller för att utföra ytterligare analyser av avbildningar. Se [domänbaserat innehåll](../../concept-detecting-domain-content.md) för mer information. 

Följande kod tolkar data om identifierade kändisar i avbildningen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_celebs)]

Följande kod tolkar data om identifierade landmärken i bilden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Hämta avbildnings typen

Följande kod skriver ut information om typen av bild &mdash; oavsett om den är ClipArt eller en linje ritning.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_type)]

> [!div class="nextstepaction"]
> [Jag analyserade en bild](?success=analyze-image#read-printed-and-handwritten-text) som [Jag stötte på ett problem](https://www.research.net/r/7QYZKHL?issue=analyze-image)

## <a name="read-printed-and-handwritten-text"></a>Skriv ut och handskriven text

Visuellt innehåll kan läsa synlig text i en bild och konvertera den till en tecken ström. Mer information om text igenkänning finns i konceptuellt dokument för [OCR (optisk tecken läsning)](../../concept-recognizing-text.md#read-api) . Koden i det här avsnittet använder den senaste [visuellt innehåll SDK-versionen för Read 3,0](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/6.0.0-preview.1) och definierar en metod, `BatchReadFileUrl` som använder klient objekt för att identifiera och extrahera text i avbildningen.

> [!TIP]
> Du kan också Extrahera text från en lokal avbildning. Se [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) -metoderna, till exempel **ReadInStreamAsync**. Eller, se exempel koden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) för scenarier som involverar lokala avbildningar.

### <a name="set-up-test-image"></a>Konfigurera test avbildning

Spara en referens till URL: en för den avbildning som du vill extrahera text från i **program** klassen. Det här kodfragmentet innehåller exempel bilder för både utskrift och handskriven text.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>Anropa Read API

Definiera den nya metoden för att läsa text. Lägg till koden nedan, som anropar **ReadAsync** -metoden för den aktuella avbildningen. Detta returnerar ett åtgärds-ID och startar en asynkron process för att läsa innehållet i avbildningen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_url)]

### <a name="get-read-results"></a>Hämta Läs resultat

Hämta sedan det åtgärds-ID som returnerades från **ReadAsync** -anropet och Använd det för att fråga tjänsten efter åtgärds resultat. Följande kod kontrollerar åtgärden tills resultatet returneras. Den skriver sedan ut de extraherade text data till-konsolen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_response)]

### <a name="display-read-results"></a>Visa Läs resultat

Lägg till följande kod för att parsa och Visa hämtade text data och slutför metod definitionen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [Jag](?success=read-printed-handwritten-text#run-the-application) [fick ett problem](https://www.research.net/r/7QYZKHL?issue=read-printed-handwritten-text) med att läsa texten

## <a name="run-the-application"></a>Kör programmet

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Kör programmet genom att klicka på knappen **Felsök** överst i IDE-fönstret.

#### <a name="cli"></a>[CLI](#tab/cli)

Kör programmet från program katalogen med `dotnet run` kommandot.

```dotnet
dotnet run
```

---
> [!div class="nextstepaction"]
> [Jag körde det program](?success=run-the-application#clean-up-resources) [Jag stötte på ett problem](https://www.research.net/r/7QYZKHL?issue=run-the-application)

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Jag har rensat resurser](?success=clean-up-resources#next-steps) som [jag stött på ett problem](https://www.research.net/r/7QYZKHL?issue=clean-up-resources) 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[API för visuellt innehåll referens (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)


* [Vad är visuellt innehåll?](../../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).