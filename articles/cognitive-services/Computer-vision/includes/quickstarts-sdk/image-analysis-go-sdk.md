---
title: 'Snabb start: klient bibliotek för avbildnings analys för go'
titleSuffix: Azure Cognitive Services
description: Kom igång med klient biblioteket för avbildnings analys för att gå med i den här snabb starten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 6a0d13dbc0beb90e0e276c5f594ff09c721f06f8
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073494"
---
<a name="HOLTop"></a>

Använd klient biblioteket för avbildnings analys för att analysera en bild för taggar, text beskrivning, ansikten, vuxen innehåll med mera.

[Referens dokumentation](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision)  |  [Paket](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* Den senaste versionen av [Go](https://golang.org/dl/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" skapar du en visuellt innehåll resurs "  target="_blank"> skapa en visuellt innehåll resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Visuellt innehåll-tjänsten. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-go-project-directory"></a>Skapa en go-projektfil

I ett konsol fönster (cmd, PowerShell, Terminal, bash) skapar du en ny arbets yta för ditt go-projekt, med namnet `my-app` och navigerar till den.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Arbets ytan kommer att innehålla tre mappar:

* **src** – den här katalogen kommer att innehålla källkod och paket. Alla paket som installeras med `go get` kommandot hamnar i den här katalogen.
* **pkg** – den här katalogen kommer att innehålla de kompilerade go-paket-objekten. De här filerna har ett `.a` fil namns tillägg.
* **bin** – den här katalogen innehåller de binära körbara filer som skapas när du kör `go install` .

> [!TIP]
> Läs mer om strukturen för en Go-arbetsyta i [språk dokumentationen för go](https://golang.org/doc/code.html#Workspaces). Den här guiden innehåller information om hur du ställer in `$GOPATH` och `$GOROOT` .

### <a name="install-the-client-library-for-go"></a>Installera klient biblioteket för go

Installera sedan klient biblioteket för Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

eller i din lagringsplatskörning om du använder dep:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Skapa ett Go-program 

Skapa sedan en fil i **src** -katalogen med namnet `sample-app.go` :

```bash
cd src
touch sample-app.go
```

Öppna `sample-app.go` i önskad IDE-eller text redigerare. Lägg sedan till paket namnet och importera följande bibliotek:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Deklarera också en kontext i roten i skriptet. Du behöver det här objektet för att köra de flesta bild analys funktions anrop:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Härnäst ska du börja lägga till kod för att utföra olika Visuellt innehåll åtgärder.

> [!div class="nextstepaction"]
> [Jag har konfigurerat klienten](?success=set-up-client#object-model) som [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client)

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Image Analysis go SDK.

|Name|Beskrivning|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Den här klassen krävs för alla Visuellt innehåll funktioner, till exempel bild analys och text läsning. Du instansierar det med din prenumerations information och använder den för att utföra de flesta avbildnings åtgärder.|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Den här typen innehåller resultatet av ett **AnalyzeImage** -funktions anrop. Det finns liknande typer för var och en av de företagsspecifika funktionerna.|
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Den här typen definierar de olika typerna av bild analys som kan göras i en standard analys åtgärd. Du anger en uppsättning VisualFeatureTypes-värden beroende på dina behov. |

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du utför följande uppgifter med klient biblioteket för avbildnings analys för Go:

* [Autentisera klienten](#authenticate-the-client)
* [Analysera en bild](#analyze-an-image)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Det här steget förutsätter att du har [skapat miljövariabler](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) för din visuellt innehåll nyckel och slut punkt, med namnet respektive `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` .

Skapa en `main` funktion och Lägg till följande kod i den för att instansiera en klient med din slut punkt och nyckel.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Jag autentiserade klienten att](?success=authenticate-client#analyze-an-image) [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analysera en bild

I följande kod används klient objekt för att analysera en fjärravbildning och skriva ut resultatet till-konsolen. Du kan få en text beskrivning, kategorisering, lista med taggar, identifierade objekt, identifierade märken, identifierade ansikten, vuxen innehålls flaggor, huvud färger och bildtyp.

### <a name="set-up-test-image"></a>Konfigurera test avbildning

Spara först en referens till URL: en för den avbildning som du vill analysera. Lägg detta i din `main` funktion.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!TIP]
> Du kan också analysera en lokal avbildning. Se [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) -metoderna, till exempel **DescribeImageInStream**. Eller, se exempel koden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) för scenarier som involverar lokala avbildningar.

### <a name="specify-visual-features"></a>Ange visuella funktioner

Följande funktions anrop extraherar olika visuella funktioner från exempel bilden. Du definierar dessa funktioner i följande avsnitt.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Beskrivning av Hämta avbildning

Följande funktion hämtar listan över genererade under texter för avbildningen. Mer information om avbildnings beskrivning finns i [Beskriv avbildningar](../../concept-describing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Hämta bild kategori

Följande funktion hämtar den identifierade kategorin för avbildningen. Mer information finns i [kategorisera bilder](../../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Hämta bild etiketter

Följande funktion hämtar en uppsättning identifierade Taggar i bilden. Mer information finns i [innehålls etiketter](../../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Upptäcka objekt

Följande funktion identifierar vanliga objekt i avbildningen och skriver ut dem till-konsolen. Mer information finns i [objekt identifiering](../../concept-object-detection.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Identifiera varumärken

Följande kod identifierar företags märken och logo typer i bilden och skriver ut dem till-konsolen. För mer information, [varumärkes identifiering](../../concept-brand-detection.md).

Deklarera först en referens till en ny avbildning i din `main` funktion.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

Följande kod definierar varumärkes identifierings funktionen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Identifiera ansikten

Följande funktion returnerar identifierade ansikten i bilden med deras Rectangle-koordinater och vissa ansikts attribut. Mer information finns i [ansikts igenkänning](../../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Identifiera vuxna, vågat eller fullständig innehåll

Följande funktion skriver ut den identifierade förekomsten av olämpligt innehåll i bilden. Mer information finns i [vuxen, vågat, fullständig-innehåll](../../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Hämta färg schema för bild

Följande funktion skriver ut de identifierade färgattributen i bilden, till exempel dominerande färger och dekor färg. Mer information finns i [färg scheman](../../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Hämta domänbaserat innehåll

Bild analyser kan använda specialiserade modeller för att utföra ytterligare analyser av avbildningar. Mer information finns i [domänbaserat innehåll](../../concept-detecting-domain-content.md). 

Följande kod tolkar data om identifierade kändisar i avbildningen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

Följande kod tolkar data om identifierade landmärken i bilden.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Hämta avbildnings typen

Följande funktion skriver ut information om typen av bild &mdash; oavsett om den är ClipArt eller en linje ritning.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

> [!div class="nextstepaction"]
> [Jag analyserade en bild](?success=analyze-image#run-the-application) som [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=analyze-image)


## <a name="run-the-application"></a>Kör programmet

Kör programmet från program katalogen med `go run` kommandot.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [Jag körde det program](?success=run-the-application#clean-up-resources) [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application)

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Jag har rensat resurser](?success=clean-up-resources#next-steps) som [jag stött på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du installerar klient biblioteket för avbildnings analys och gör grundläggande bild analys anrop. Läs sedan mer om hur du analyserar API-funktionerna.

> [!div class="nextstepaction"]
>[Anropa API: et för analys](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Översikt över bild analys](../../overview-image-analysis.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).

