---
author: areddish
ms.custom: devx-track-java
ms.author: areddish
ms.service: cognitive-services
ms.date: 08/17/2020
ms.openlocfilehash: cd6388e6c6313ba84978d43d388855b114a4875d
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508549"
---
Den här artikeln visar hur du kommer igång med Custom Vision Java-klient biblioteket för att skapa en modell för bild klassificering. När den har skapats kan du lägga till taggar, ladda upp bilder, träna projektet, hämta slutpunkts-URL:en för projektets standardförutsägelse och använda slutpunkten för att testa en avbildning programmatiskt. Använd det här exemplet som en mall för att skapa ditt eget Java-program. Om du vill gå igenom processen med att skapa och använda en bildklassificeringsmodell _utan_ kod kan du istället läsa den [webbläsarbaserade vägledningen](../../getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Krav

- En valfri Java IDE
- [JDK 7 eller 8](https://aka.ms/azure-jdks) installerat.
- [Maven](https://maven.apache.org/) installerat
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library-and-sample-code"></a>Hämta Custom Vision klient bibliotek och exempel kod

Om du vill skriva ett Java-program som använder Custom Vision behöver du Custom Vision maven-paketen. De här paketen ingår i det exempel projekt som du hämtar, men du kan komma åt dem separat här.

Du hittar Custom Vision klient biblioteket i maven Central-lagringsplatsen:

- [Training SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Prediction SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Klona eller ladda ned [Cognitive Services Java SDK-exempelprojektet](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Navigera till mappen **Vision/CustomVision/**.

Det här projektet i Java skapar ett nytt Custom Vision-projekt för bildklassificering som kallas för __exempelprojektet för Java__, som kan nås via [Custom Vision-webbplatsen](https://customvision.ai/). Det laddar sedan upp bilder för att träna och testa en klassificerare. I det här projektet är klassificeraren avsedd att avgöra huruvida ett träd är en __hemlockgran__ eller ett __japanskt körsbärsträd__.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

Programmet är konfigurerat för att referera till dina nyckel data som miljövariabler. Navigera till mappen **vision/CustomVision** och ange följande PowerShell-kommandon för att ställa in miljövariablerna. 

> [!NOTE]
> Om du använder ett icke-Windows-operativsystem går du till [Konfigurera miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) för instruktioner.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Förstå koden

Läs in `Vision/CustomVision`-projektet i din Java IDE och öppna filen _CustomVisionSamples.java_. Hitta **runSample** -metoden och kommentera ut **ObjectDetection_Sample** metod anropet &mdash; den här metoden kör scenariot för objekt identifiering, som inte beskrivs i den här hand boken. Metoden **ImageClassification_Sample** implementerar de primära funktionerna i det här exemplet. Gå till dess definition och granska koden.

### <a name="create-a-custom-vision-service-project"></a>Skapa ett Custom Vision Service-projekt

Den här första delen med kod skapar ett projekt för klassificering av bilder. Det skapade projektet visas på den [Custom Vision-webbplats](https://customvision.ai/) som du besökte tidigare. Se [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) -metoden för att ange andra alternativ när du skapar ditt projekt (förklaras i guiden [skapa en klassificerings](../../getting-started-build-a-classifier.md) webb Portal).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Skapa taggar i projektet

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

Exempelbilderna finns i mappen **src/main/resources** i projektet. De är lästa därifrån och har överförts till tjänsten med lämpliga taggar.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

Föregående kodfragment använder två hjälp funktioner som hämtar avbildningarna som resurs strömmar och laddar upp dem till tjänsten (du kan ladda upp till 64 avbildningar i en enda batch).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-classifier-and-publish"></a>Träna klassificeraren och publicera

Den här koden skapar den första iterationen av förutsägelse modellen och publicerar sedan en upprepning till förutsägelse slut punkten. Det namn som ges till den publicerade iterationen kan användas för att skicka förutsägelse begär Anden. En iteration är inte tillgänglig i förutsägelse slut punkten förrän den har publicerats.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]

### <a name="use-the-prediction-endpoint"></a>Använd förutsägelse slut punkten

Förutsägelseslutpunkten, som representeras av objektet `predictor` här, är den referens som du kan använda för att skicka en bild till den aktuella modellen och få en klassificeringsförutsägelse. I det här exemplet har `predictor` definierats någon annanstans med hjälp av miljövariabeln för förutsägelsenyckeln.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>Kör programmet

Om du vill kompilera och köra lösningen med hjälp av maven går du till projekt katalogen (**vision/CustomVision**) i en kommando tolk och kör kommandot kör:

```bash
mvn compile exec:java
```

Programmets konsolutdata bör ser ut ungefär som nedanstående text:

```console
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Du kan sedan kontrollera att testbildens förutsägelse (de sista raderna i utdata) är korrekt.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur varje steg i objekt identifierings processen kan göras i kod. Det här exemplet kör en enda inlärnings upprepning, men ofta behöver du träna och testa din modell flera gånger för att göra den mer exakt.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](../../test-your-model.md)
