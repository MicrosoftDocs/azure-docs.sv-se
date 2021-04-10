---
title: 'Snabb start: klient bibliotek för avbildnings analys för Java'
description: I den här snabb starten ska du komma igång med klient biblioteket för avbildnings analys för Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 9349ac5c0b207d0dffb71295117f35849ab5caba
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073282"
---
<a name="HOLTop"></a>

Använd klient biblioteket för avbildnings analys för att analysera en bild för taggar, text beskrivning, ansikten, vuxen innehåll med mera.

[Referens dokumentation](/java/api/overview/azure/cognitiveservices/client/computervision)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-computervision)  | [Artefakt (maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision)  |  [Exempel](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle build-verktyget](https://gradle.org/install/)eller någon annan beroende hanterare.
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" skapar du en visuellt innehåll resurs "  target="_blank"> skapa en visuellt innehåll resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Visuellt innehåll-tjänsten. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-gradle-project"></a>Skapa ett nytt Gradle-projekt

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör `gradle init` kommandot från din arbets katalog. Med det här kommandot skapas viktiga build-filer för Gradle, inklusive *build. Gradle. KTS*, som används vid körning för att skapa och konfigurera ditt program.

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

### <a name="install-the-client-library"></a>Installera klient biblioteket

I den här snabb starten används Gradle-beroende hanteraren. Du hittar klient biblioteket och information för andra beroende hanterare på den [centrala maven-lagringsplatsen](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision).

Leta upp *build. gradle. KTS* och öppna den med önskad IDE-eller text redigerare. Kopiera sedan i följande build-konfiguration. Den här konfigurationen definierar projektet som ett Java-program vars start punkt är klassen **ComputerVisionQuickstarts**. Visuellt innehålls biblioteket importeras.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.4-beta")
}
```

### <a name="create-a-java-file"></a>Skapa en Java-fil

Kör följande kommando från din arbets katalog för att skapa en mapp för projekt Källa:

```console
mkdir -p src/main/java
```

Navigera till den nya mappen och skapa en fil med namnet *ComputerVisionQuickstarts. java*. Öppna det i önskat redigerings program eller IDE och Lägg till följande- `import` uttryck:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java), som innehåller kod exemplen i den här snabb starten.

Definiera klassen **ComputerVisionQuickstarts**.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_classdef_1)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_classdef_2)]

I klassen **ComputerVisionQuickstarts** skapar du variabler för resursens nyckel och slut punkt.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_creds)]


> [!IMPORTANT]
> Gå till Azure-portalen. Om Visuellt innehåll resursen som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar din nyckel och slut punkt i resursens **nyckel och slut punkts** sida under **resurs hantering**. 
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Mer information finns i [säkerhets](../../../cognitive-services-security.md) artikeln Cognitive Services.

I programmets **main** -metod lägger du till anrop för de metoder som används i den här snabb starten. Du definierar dessa senare.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_beginmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_authinmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzeinmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_endmain)]


> [!div class="nextstepaction"]
> [Jag har konfigurerat klienten](?success=set-up-client#object-model) som [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=set-up-client)

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Image Analysis Java SDK.

|Name|Beskrivning|
|---|---|
| [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Den här klassen krävs för alla Visuellt innehåll-funktioner. Du instansierar det med din prenumerations information och använder den för att skapa instanser av andra klasser.|
|[ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision)| Den här klassen kommer från klient objekt och hanterar direkt alla avbildnings åtgärder, till exempel bild analys, text identifiering och generering av miniatyrer.|
|[VisualFeatureTypes](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Den här uppräkningen definierar de olika typerna av bild analys som kan göras i en standard analys åtgärd. Du anger en uppsättning VisualFeatureTypes-värden beroende på dina behov. |

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande uppgifter med klient biblioteket för avbildnings analys för java:

* [Autentisera klienten](#authenticate-the-client)
* [Analysera en bild](#analyze-an-image)

## <a name="authenticate-the-client"></a>Autentisera klienten

I en ny metod instansierar du ett [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) -objekt med din slut punkt och nyckel.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Jag autentiserade klienten att](?success=authenticate-client#analyze-an-image) [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analysera en bild

Följande kod definierar en metod, `AnalyzeLocalImage` som använder klient objekt för att analysera en lokal avbildning och skriva ut resultaten. Metoden returnerar en text beskrivning, kategorisering, lista med taggar, identifierade ansikten, olämpliga innehålls flaggor, huvud färger och bildtyp.

> [!TIP]
> Du kan också analysera en fjärran sluten avbildning med hjälp av dess URL. Se [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision) -metoderna, till exempel **AnalyzeImage**. Eller, se exempel koden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) för scenarier som rör fjärranslutna avbildningar.

### <a name="set-up-test-image"></a>Konfigurera test avbildning

Börja med att skapa en **resurs/** mapp i ditt projekts **src/main/-** mapp och Lägg till en avbildning som du vill analysera. Lägg sedan till följande metod definition i **ComputerVisionQuickstarts** -klassen. Ändra värdet för `pathToLocalImage` att matcha bild filen. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

### <a name="specify-visual-features"></a>Ange visuella funktioner

Ange sedan vilka visuella funktioner du vill extrahera i analysen. Se [VisualFeatureTypes](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes) -uppräkningen för en fullständig lista.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analysera
Det här blocket skriver ut detaljerade resultat till-konsolen för varje omfång av bild analys. Metoden **analyzeImageInStream** returnerar ett **ImageAnalysis** -objekt som innehåller all extraherad information.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

I följande avsnitt visas hur du kan analysera den här informationen i detalj.

### <a name="get-image-description"></a>Beskrivning av Hämta avbildning

Följande kod hämtar listan över genererade under texter för avbildningen. Mer information finns i [Beskriv avbildningar](../../concept-describing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>Hämta bild kategori

Följande kod hämtar den identifierade kategorin för avbildningen. Mer information finns i [kategorisera bilder](../../concept-categorizing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>Hämta bild etiketter

Följande kod hämtar en uppsättning identifierade Taggar i avbildningen. Mer information finns i [innehålls etiketter](../../concept-tagging-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>Identifiera ansikten

Följande kod returnerar identifierade ansikten i bilden med deras Rectangle-koordinater och väljer ansikts attribut. Mer information finns i [ansikts igenkänning](../../concept-detecting-faces.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-objects"></a>Upptäcka objekt

Följande kod returnerar de identifierade objekten i avbildningen med deras koordinater. Mer information finns i [objekt identifiering](../../concept-object-detection.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_objects)]


### <a name="detect-brands"></a>Identifiera varumärken

Följande kod returnerar identifierade varumärkes logo typer i bilden med deras koordinater. Mer information finns i [varumärkes identifiering](../../concept-brand-detection.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_brands)]



### <a name="detect-adult-racy-or-gory-content"></a>Identifiera vuxna, vågat eller fullständig innehåll

Följande kod skriver ut den identifierade förekomsten av olämpligt innehåll i bilden. Mer information finns i [vuxen, vågat, fullständig-innehåll](../../concept-detecting-adult-content.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Hämta färg schema för bild

Följande kod skriver ut de identifierade färgattributen i bilden, till exempel dominerande färger och dekor färg. Mer information finns i [färg scheman](../../concept-detecting-color-schemes.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Hämta domänbaserat innehåll

Bild analys kan använda specialiserad modell för att utföra ytterligare analyser av avbildningar. Mer information finns i [domänbaserat innehåll](../../concept-detecting-domain-content.md). 

Följande kod tolkar data om identifierade kändisar i avbildningen.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

Följande kod tolkar data om identifierade landmärken i bilden.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>Hämta avbildnings typen

Följande kod skriver ut information om typen av bild &mdash; oavsett om den är en ClipArt-eller linje ritning.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

> [!div class="nextstepaction"]
> [Jag analyserade en bild](?success=analyze-image#run-the-application) som [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=analyze-image)

### <a name="close-out-the-method"></a>Stäng av metoden

Slutför try/catch-blocket och Stäng metoden.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyze_catch)]


## <a name="run-the-application"></a>Kör programmet

Du kan bygga appen med:

```console
gradle build
```

Kör programmet med `gradle run` kommandot:

```console
gradle run
```

> [!div class="nextstepaction"]
> [Jag körde det program](?success=run-the-application#clean-up-resources) [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=run-the-application)

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Jag har rensat resurser](?success=clean-up-resources#next-steps) som [jag stött på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du installerar klient biblioteket för avbildnings analys och gör grundläggande bild analys anrop. Läs sedan mer om hur du analyserar API-funktionerna.

> [!div class="nextstepaction"]
>[Anropa API: et för analys](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Översikt över bild analys](../../overview-image-analysis.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).
