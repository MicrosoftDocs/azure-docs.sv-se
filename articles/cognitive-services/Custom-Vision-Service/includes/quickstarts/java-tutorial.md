---
author: PatrickFarley
ms.custom: devx-track-java
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/13/2020
ms.openlocfilehash: 6c5c0a7b1c2b94ba637f0204d603f15d1214607d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947725"
---
Kom igång med Custom Vision klient bibliotek för Java för att skapa en bild klassificerings modell. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Använd det här exemplet som mall för att skapa en egen bild igenkännings app.

> [!NOTE]
> Om du vill skapa och träna en klassificerings modell _utan att_ skriva kod, se den [webbläsarbaserade vägledningen](../../getting-started-build-a-classifier.md) i stället.

Använd Custom Vision klient bibliotek för Java för att:

* Skapa ett nytt Custom Vision-projekt
* Lägg till taggar i projektet
* Ladda upp och tagga bilder
* Träna projektet
* Publicera den aktuella iterationen
* Testa förutsägelse slut punkten

[Referens dokumentation](/java/api/overview/azure/cognitiveservices/client/customvision) | Biblioteks käll kod [(utbildning)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-training) [(förutsägelse)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-prediction)| Artefakt (maven) [(utbildning)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-training/1.1.0-preview.2/jar) [(förutsägelse)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-prediction/1.1.0-preview.2/jar)  | 
 [exempel](/samples/browse/?products=azure&terms=custom%20vision)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle build-verktyget](https://gradle.org/install/)eller någon annan beroende hanterare.
* När du har en Azure-prenumeration skapar du <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title=" en Custom vision resurs "  target="_blank"> skapa en Custom vision resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att skapa en utbildnings-och förutsägelse resurs och hämta nycklar och slut punkt. Vänta tills den har distribuerats och klicka på knappen **gå till resurs** .
    * Du behöver nyckeln och slut punkten från de resurser som du skapar för att ansluta ditt program till Custom Vision. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
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

Leta upp *build. gradle. KTS* och öppna den med önskad IDE-eller text redigerare. Kopiera sedan i följande build-konfiguration. Den här konfigurationen definierar projektet som ett Java-program vars start punkt är klassen **CustomVisionQuickstart**. Custom Vision bibliotek importeras.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "CustomVisionQuickstart"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-training", version = "1.1.0-preview.2")
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-prediction", version = "1.1.0-preview.2")
}
```

### <a name="create-a-java-file"></a>Skapa en Java-fil


Kör följande kommando från din arbets katalog för att skapa en mapp för projekt Källa:

```console
mkdir -p src/main/java
```

Navigera till den nya mappen och skapa en fil med namnet *CustomVisionQuickstart. java*. Öppna det i önskat redigerings program eller IDE och Lägg till följande- `import` uttryck:

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_imports)]

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java), som innehåller kod exemplen i den här snabb starten.


I programmets **CustomVisionQuickstart** -klass skapar du variabler för resursens nycklar och slut punkt.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_creds)]


> [!IMPORTANT]
> Gå till Azure-portalen. Om Custom Vision resurserna som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar nycklar och slut punkt i resursens nyckel- **och slut punkts** sidor under **resurs hantering**. Du måste få både dina utbildnings-och förutsägelse nycklar, tillsammans med utbildnings resursernas slut punkt.
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Mer information finns i [säkerhets](../../../cognitive-services-security.md) artikeln Cognitive Services.

I programmets **main** -metod lägger du till anrop för de metoder som används i den här snabb starten. Du definierar dessa senare.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_maincalls)]

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Custom Vision Java-klient biblioteket.

|Name|Beskrivning|
|---|---|
|[CustomVisionTrainingClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Den här klassen hanterar skapandet, utbildningen och publiceringen av dina modeller. |
|[CustomVisionPredictionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Den här klassen hanterar frågekörning för modeller för bild klassificerings förutsägelser.|
|[ImagePrediction](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Den här klassen definierar en enstaka förutsägelse för en enda avbildning. Den innehåller egenskaper för objekt-ID och namn och en förtroende poäng.|

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande uppgifter med Custom Vision klient bibliotek för java:

* [Autentisera klienten](#authenticate-the-client)
* [Skapa ett nytt Custom Vision-projekt](#create-a-new-custom-vision-project)
* [Lägg till taggar i projektet](#add-tags-to-the-project)
* [Ladda upp och tagga bilder](#upload-and-tag-images)
* [Träna projektet](#train-the-project)
* [Publicera den aktuella iterationen](#publish-the-current-iteration)
* [Testa förutsägelse slut punkten](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autentisera klienten

I **huvud** metoden instansierar du inlärnings-och förutsägelse klienter med hjälp av din slut punkt och nycklar.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_auth)]


## <a name="create-a-custom-vision-project"></a>Skapa ett Custom Vision-projekt

T # # skapa ett nytt Custom Vision-projekt

Nästa metod skapar ett bild klassificerings projekt. Det skapade projektet visas på den [Custom Vision-webbplats](https://customvision.ai/) som du besökte tidigare. Se [CreateProject](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_&preserve-view=true) -metoden för att ange andra alternativ när du skapar ditt projekt (förklaras i guiden [skapa en identifierings](../../get-started-build-detector.md) webb Portal).

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

## <a name="add-tags-to-your-project"></a>Lägga till taggar till projektet

Den här metoden definierar de taggar som du kommer att träna modellen på.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

Hämta först exempel bilderna för projektet. Spara innehållet i [mappen exempel bilder](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) på din lokala enhet.

> [!NOTE]
> Källa, ett Microsoft garage-projekt, gör att du kan samla in och köpa uppsättningar av avbildningar i utbildnings syfte. När du har samlat in dina avbildningar kan du hämta dem och sedan importera dem till ditt Custom Vision-projekt på vanligt sätt. Besök [sidan källa](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3) om du vill veta mer.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

Föregående kodfragment använder två hjälp funktioner som hämtar avbildningarna som resurs strömmar och laddar upp dem till tjänsten (du kan ladda upp till 64 avbildningar i en enda batch).

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-the-project"></a>Träna projektet

Den här metoden skapar den första inlärningen iteration i projektet. Tjänsten frågar tjänsten tills utbildningen är slutförd.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>Publicera den aktuella iterationen

Den här metoden gör den aktuella iterationen av modellen tillgänglig för frågor. Du kan använda modell namnet som referens för att skicka förutsägelse begär Anden. Du måste ange ett eget värde för `predictionResourceId` . Du hittar resurs-ID för förutsägelse på fliken **Översikt** i Azure Portal, som **prenumerations-ID**.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_publish)]

## <a name="test-the-prediction-endpoint"></a>Testa förutsägelse slut punkten

Den här metoden läser in test avbildningen, frågar modell slut punkten och matar ut förutsägelse data till-konsolen.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>Kör programmet

Du kan bygga appen med:

```console
gradle build
```

Kör programmet med `gradle run` kommandot:

```console
gradle run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur varje steg i bild klassificerings processen kan göras i kod. Det här exemplet kör en enda inlärnings upprepning, men ofta behöver du träna och testa din modell flera gånger för att göra den mer exakt.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](../../test-your-model.md)

* [Vad är Custom Vision?](../../overview.md)
* Du hittar käll koden för det här exemplet på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java)
