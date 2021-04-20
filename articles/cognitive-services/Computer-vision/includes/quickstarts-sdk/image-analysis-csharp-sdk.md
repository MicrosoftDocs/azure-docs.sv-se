---
title: 'Snabbstart: Klientbibliotek för bildanalys för .NET'
description: I den här snabbstarten kommer du igång med klientbiblioteket för bildanalys för .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 03/29/2021
ms.author: pafarley
ms.openlocfilehash: 0af6c97d6179a645b078f2335ff38f48890c42a3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728202"
---
<a name="HOLTop"></a>

Använd klientbiblioteket för bildanalys för att analysera en bild efter taggar, textbeskrivning, ansikten, vuxet innehåll med mera.

[Referensdokumentation](/dotnet/api/overview/azure/cognitiveservices/client/computervision)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)  |  [Exempel](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration [– Skapa en kostnadsfritt](https://azure.microsoft.com/free/cognitive-services/)
* Den [Visual Studio IDE eller](https://visualstudio.microsoft.com/vs/) den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* När du har din Azure-prenumeration skapar du en Visuellt innehåll-resurs för att skapa Visuellt innehåll resurs i Azure Portal för att <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hämta din nyckel och "  target="_blank"> </a> slutpunkt. När den har distribuerats klickar du **på Gå till resurs**.
    * Du behöver nyckeln och slutpunkten från den resurs som du skapar för att ansluta ditt program till Visuellt innehåll tjänsten. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Använd Visual Studio skapa ett nytt .NET Core-program. 

### <a name="install-the-client-library"></a>Installera klientbiblioteket 

När du har skapat ett nytt projekt installerar du klientbiblioteket genom att högerklicka på projektlösningen i **Solution Explorer** och välja **Hantera NuGet-paket.** I pakethanteraren som öppnas väljer du **Bläddra,** **markerar Inkludera förhandsversion** och söker efter `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` . Välj version `7.0.0` och sedan **Installera**. 

#### <a name="cli"></a>[CLI](#tab/cli)

I ett konsolfönster (till exempel cmd, PowerShell eller Bash) använder du kommandot för att skapa en `dotnet new` ny konsolapp med namnet `computer-vision-quickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
```

Ändra din katalog till den nyligen skapade appmappen. Du kan skapa programmet med:

```console
dotnet build
```

Byggutdata får inte innehålla några varningar eller fel. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installera klientbiblioteket

I programkatalogen installerar du Visuellt innehåll för .NET med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 7.0.0
```

---

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs), som innehåller kodexe exemplen i den här snabbstarten.

Från projektkatalogen öppnar du filen *Program.cs* i önskad redigerare eller IDE. Lägg till följande `using` -direktiv:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using)]

I programmets **Program-klass** skapar du variabler för resursens Azure-slutpunkt och nyckel.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_vars)]

> [!IMPORTANT]
> Gå till Azure-portalen. Om den Visuellt innehåll som du skapade  i avsnittet Förutsättningar har distribuerats klickar du på **knappen Gå till** resurs under **Nästa steg.** Du hittar din nyckel och slutpunkt på resursens nyckel- **och slutpunktssida** under **resurshantering .** 
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och aldrig publicera den offentligt. Överväg att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter för produktion. Mer information Cognitive Services artikeln om Cognitive Services [säkerhet.](../../../cognitive-services-security.md)

I programmets -metod `Main` lägger du till anrop för de metoder som används i den här snabbstarten. Du kommer att skapa dessa senare.


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyzeinmain)]

> [!div class="nextstepaction"]
> [Jag konfigurerade klienten som](?success=set-up-client#object-model) [jag stötte på ett problem med](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>Objektmodell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i .NET SDK för bildanalys.

|Name|Beskrivning|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Den här klassen behövs för alla Visuellt innehåll funktioner. Du instansierar den med din prenumerationsinformation och använder den för att göra de flesta avbildningsåtgärder.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| Den här klassen innehåller ytterligare metoder för **ComputerVisionClient**.|
|[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Den här uppräkning definierar de olika typerna av bildanalys som kan göras i en standardåtgärd för Analys. Du anger en uppsättning VisualFeatureTypes-värden beroende på dina behov. |

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du utför följande uppgifter med klientbiblioteket för bildanalys för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Analysera en bild](#analyze-an-image)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Den här snabbstarten förutsätter att [du har](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) skapat miljövariabler för din Visuellt innehåll nyckel och slutpunkt, med `COMPUTER_VISION_SUBSCRIPTION_KEY` namnet `COMPUTER_VISION_ENDPOINT` respektive.

I en ny metod i **klassen Program** instansierar du en klient med din slutpunkt och nyckel. Skapa ett **[ApiKeyServiceClientCredentials-objekt](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** med din nyckel och använd det med slutpunkten för att skapa ett **[ComputerVisionClient-objekt.](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Jag autentiserade klienten jag](?success=authenticate-client#analyze-an-image) [stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analysera en bild

Följande kod definierar en -metod, `AnalyzeImageUrl` , som använder klientobjektet för att analysera en fjärrbild och skriva ut resultatet. Metoden returnerar en textbeskrivning, kategorisering, lista över taggar, identifierade ansikten, flaggor för vuxet innehåll, huvudfärger och bildtyp.

> [!TIP]
> Du kan också analysera en lokal bild. Se [ComputerVisionClient-metoderna,](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) till exempel **AnalyzeImageInStreamAsync.** Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) scenarier som rör lokala avbildningar.

### <a name="set-up-test-image"></a>Konfigurera testbild

Spara en **referens** till URL:en för den bild som du vill analysera i klassen Program.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_url)]

### <a name="specify-visual-features"></a>Ange visuella funktioner

Definiera den nya metoden för bildanalys. Lägg till koden nedan, som anger visuella funktioner som du vill extrahera i analysen. En fullständig lista finns i uppräkning **[av VisualFeatureTypes.](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_visualfeatures)]

### <a name="call-the-analyze-api"></a>Anropa API:et Analysera

Metoden **AnalyzeImageAsync** returnerar ett **ImageAnalysis-objekt** som innehåller all extraherad information.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_call)]

Följande avsnitt visar hur du parsar den här informationen i detalj.

Infoga något av följande kodblock i metoden **AnalyzeImageUrl** för att parsa data från de visuella funktioner som du begärde ovan. Kom ihåg att lägga till en avslutande hakparentes i slutet.

```csharp
}
```

### <a name="get-image-description"></a>Hämta bildbeskrivning

Följande kod hämtar listan över genererade bildtexter för avbildningen. Mer [information finns i](../../concept-describing-images.md) Beskriv bilder.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Hämta bildkategori

Följande kod hämtar den identifierade kategorin för bilden. Mer [information finns i](../../concept-categorizing-images.md) Kategorisera bilder.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Hämta bildtaggar

Följande kod hämtar uppsättningen identifierade taggar i bilden. Mer [information finns i](../../concept-tagging-images.md) Innehållstaggar.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Upptäcka objekt

Följande kod identifierar vanliga objekt i bilden och skriver ut dem till konsolen. Mer [information finns](../../concept-object-detection.md) i Objektidentifiering.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Identifiera varumärken

Följande kod identifierar företagets varumärken och logotyper i bilden och skriver ut dem till konsolen. Mer [information finns i](../../concept-brand-detection.md) Varumärkesidentifiering.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Identifiera ansikten

Följande kod returnerar de identifierade ansiktena i bilden med deras rektangelkoordinater och väljer ansiktsattribut. Mer [information finns i](../../concept-detecting-faces.md) Ansiktsigenkänning.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Identifiera vuxet, ojämnt eller gory-innehåll

Följande kod skriver ut den identifierade förekomsten av vuxet innehåll i bilden. Mer [information finns i Vuxet, racy, gory-innehåll.](../../concept-detecting-adult-content.md)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Hämta bildfärgschema

Följande kod skriver ut de identifierade färgattributen i bilden, till exempel de dominerande färgerna och accentfärgen. Mer [information finns i](../../concept-detecting-color-schemes.md) Färgscheman.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Hämta domänspecifikt innehåll

Bildanalys kan använda specialiserade modeller för ytterligare analys av bilder. Mer [information finns i Domänspecifikt](../../concept-detecting-domain-content.md) innehåll. 

Följande kod parsar data om identifierade kändisar i bilden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_celebs)]

Följande kod parsar data om identifierade landmärken i bilden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Hämta avbildningstypen

Följande kod skriver ut information om typen av bild oavsett &mdash; om det är ClipArt eller en linjeritning.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_type)]

> [!div class="nextstepaction"]
> [Jag analyserade en bild som](?success=analyze-image#run-the-application) [jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=analyze-image)



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

I den här snabbstarten har du lärt dig hur du installerar klientbiblioteket för bildanalys och gör grundläggande bildanalys-anrop. Härnäst får du lära dig mer om funktionerna i Analysera API.


> [!div class="nextstepaction"]
>[Anropa API:et Analysera](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Översikt över bildanalys](../../overview-image-analysis.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).
