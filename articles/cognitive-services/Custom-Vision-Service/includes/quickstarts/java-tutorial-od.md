---
author: PatrickFarley
ms.custom: devx-track-java
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/13/2020
ms.openlocfilehash: 16112ffe7ba5fbc23335f9b60cdcbc045ea7cd2b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725177"
---
Kom igång med att Custom Vision klientbiblioteket för Java för att skapa en objektidentifieringsmodell. Följ de här stegen för att installera paketet och prova exempelkoden för grundläggande uppgifter. Använd det här exemplet som en mall för att skapa en egen bildigenkänningsapp.

> [!NOTE]
> Om du vill skapa och träna en objektidentifieringsmodell _utan att_ skriva kod kan du gå till [webbläsarbaserad vägledning i](../../get-started-build-detector.md) stället.

Använd Custom Vision för Java för att:

* Skapa ett nytt Custom Vision-projekt
* Lägg till taggar i projektet
* Ladda upp och tagga bilder
* Träna projektet
* Publicera den aktuella iterationen
* Testa förutsägelseslutpunkten

[Referensdokumentation](/java/api/overview/azure/cognitiveservices/client/customvision) | [Bibliotekskällkod (träning)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-training) [(förutsägelse)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-prediction)| Artefaktexempel (Maven) [(träning)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-training/1.1.0-preview.2/jar) [(förutsägelse)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-prediction/1.1.0-preview.2/jar)  | 
 [](/samples/browse/?products=azure&terms=custom%20vision)


## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration [– Skapa en utan kostnad](https://azure.microsoft.com/free/cognitive-services/)
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle-byggverktyget](https://gradle.org/install/), eller någon annan beroendehanterare.
* När du har din Azure-prenumeration skapar du en Custom Vision-resurs för att skapa en Custom Vision-resurs i Azure Portal för att skapa en utbildnings- och förutsägelseresurs och hämta dina nycklar <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> och </a> slutpunkter. Vänta tills den har distribuerats och klicka **på knappen Gå till** resurs.
    * Du behöver nyckeln och slutpunkten från de resurser som du skapar för att ansluta ditt program till Custom Vision. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och uppgradera senare till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-gradle-project"></a>Skapa ett nytt Gradle-projekt

I ett konsolfönster (till exempel cmd, PowerShell eller Bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör `gradle init` kommandot från arbetskatalogen. Det här kommandot skapar viktiga byggfiler för Gradle, inklusive *build.gradle.kts*, som används vid körning för att skapa och konfigurera ditt program.

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Leta *upp build.gradle.kts* och öppna det med önskad IDE eller textredigerare. Kopiera sedan följande byggkonfiguration. Den här konfigurationen definierar projektet som ett Java-program vars startpunkt är klassen **CustomVisionQuickstart**. Den importerar de Custom Vision biblioteken.

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


Kör följande kommando från arbetskatalogen för att skapa en projektkällmapp:

```console
mkdir -p src/main/java
```

Navigera till den nya mappen och skapa en fil med namnet *CustomVisionQuickstart.java.* Öppna den i önskad redigerare eller IDE och lägg till följande `import` -instruktioner:

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_imports)]

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java), som innehåller kodexe exemplen i den här snabbstarten.


I programmets **CustomVisionQuickstart-klass** skapar du variabler för resursens nycklar och slutpunkt.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_creds)]


> [!IMPORTANT]
> Gå till Azure-portalen. Om Custom Vision resurser som du skapade  i avsnittet Förutsättningar har distribuerats klickar du på knappen **Gå till** resurs under **Nästa steg.** Du hittar dina nycklar och slutpunkter på resursernas **nyckel- och slutpunktssidor** under **resurshantering.** Du måste hämta nycklarna för både dina tränings- och förutsägelseresurser, tillsammans med API-slutpunkten för din träningsresurs.
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och aldrig publicera den offentligt. Överväg att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter för produktion. Mer information finns i artikeln Cognitive Services [säkerhetsinformation.](../../../cognitive-services-security.md)

I programmets huvudmetod **lägger du** till anrop för de metoder som används i den här snabbstarten. Du definierar dessa senare.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_maincalls_od)]

## <a name="object-model"></a>Objektmodell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Custom Vision Java-klientbiblioteket.

|Name|Beskrivning|
|---|---|
|[CustomVisionTrainingClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Den här klassen hanterar skapandet, träningen och publiceringen av dina modeller. |
|[CustomVisionPredictionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Den här klassen hanterar frågor från dina modeller för förutsägelser om objektidentifiering.|
|[ImagePrediction](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Den här klassen definierar en enskild objektförutsägelse på en enskild bild. Den innehåller egenskaper för objekt-ID och namn, objektets begränsningsruta och förtroendepoäng.|

## <a name="code-examples"></a>Kodexempel

Dessa kodfragment visar hur du utför följande uppgifter med Custom Vision klientbibliotek för Java:

* [Autentisera klienten](#authenticate-the-client)
* [Skapa ett nytt Custom Vision-projekt](#create-a-new-custom-vision-project)
* [Lägg till taggar i projektet](#add-tags-to-the-project)
* [Ladda upp och tagga bilder](#upload-and-tag-images)
* [Träna projektet](#train-the-project)
* [Publicera den aktuella iterationen](#publish-the-current-iteration)
* [Testa förutsägelseslutpunkten](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autentisera klienten

I din **huvudmetod** instansierar du tränings- och prediktionsklienter med hjälp av din slutpunkt och dina nycklar.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Skapa ett nytt Custom Vision-projekt

Nästa metod skapar ett projekt för objektidentifiering. Det skapade projektet visas på den [Custom Vision-webbplats](https://customvision.ai/) som du besökte tidigare. Se [Överlagringar av CreateProject-metoder](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_&preserve-view=true) för att ange andra alternativ när du skapar projektet (förklaras i guiden [Skapa en webbportal för](../../get-started-build-detector.md) detektorer).

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

## <a name="add-tags-to-your-project"></a>Lägga till taggar till projektet

Den här metoden definierar de taggar som du ska träna modellen på.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

Ladda först ned exempelbilderna för det här projektet. Spara innehållet i [exempelmappen Bilder](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) på din lokala enhet.

> [!NOTE]
> Behöver du en bredare uppsättning bilder för att slutföra träningen? Med Trove, Microsoft Garage projekt, kan du samla in och köpa uppsättningar av bilder i utbildningssyfte. När du har samlat in dina bilder kan du ladda ned dem och sedan importera dem till ditt Custom Vision-projekt som vanligt. Besök [Trove-sidan om](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) du vill veta mer.

När du taggar bilder i objekt identifierings projekt måste du ange regionen för varje taggat objekt med normaliserade koordinater. Följande kod associerar var och en av exempel bilderna med dess taggade region.

> [!NOTE]
> Om du inte har ett klick- och dra-verktyg för att markera koordinaterna för regioner kan du använda webbgränssnittet på [Customvision.ai](https://www.customvision.ai/). I det här exemplet har koordinaterna redan angetts.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Nästa kodblock lägger till bilderna i projektet. Du måste ändra argumenten för anropen så att de pekar på platserna för för- och mapparna som `GetImage` du laddade  ned. 

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

Föregående kodfragment använder två hjälpfunktioner som hämtar bilderna som resursströmmar och laddar upp dem till tjänsten (du kan ladda upp upp till 64 bilder i en enda batch). Definiera dessa metoder. 

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-the-project"></a>Träna projektet

Den här metoden skapar den första tränings iterationen i projektet. Den frågar tjänsten tills träningen har slutförts.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]


## <a name="publish-the-current-iteration"></a>Publicera den aktuella iterationen

Den här metoden gör den aktuella iterationen av modellen tillgänglig för frågor. Du kan använda modellnamnet som referens för att skicka förutsägelsebegäranden. Du måste ange ett eget värde för `predictionResourceId` . Du hittar resurs-ID:t för förutsägelsen på fliken **Översikt** för resursen Azure Portal **prenumerations-ID.**

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_publishOD)]

## <a name="test-the-prediction-endpoint"></a>Testa förutsägelseslutpunkten

Den här metoden läser in testbilden, frågar modellslutpunkten och matar ut förutsägelsedata till konsolen.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict_od)]

## <a name="run-the-application"></a>Kör programmet

Du kan skapa appen med:

```console
gradle build
```

Kör programmet med `gradle run` kommandot :

```console
gradle run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resursgruppen. När du tar bort resursgruppen tas även alla andra resurser som är associerade med den bort.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du gjort alla steg i objektidentifieringsprocessen i koden. Det här exemplet kör en enda tränings-iteration, men ofta behöver du träna och testa din modell flera gånger för att göra den mer exakt. Följande guide behandlar bildklassificering, men principerna liknar dem som gäller för objektidentifiering.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](../../test-your-model.md)

* [Vad är Custom Vision?](../../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java)
