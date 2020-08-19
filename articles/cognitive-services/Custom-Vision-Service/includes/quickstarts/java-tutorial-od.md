---
author: areddish
ms.custom: devx-track-java
ms.author: areddish
ms.service: cognitive-services
ms.date: 08/17/2020
ms.openlocfilehash: a822ab52024a801f4443a9dd864b4b96ec52d000
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511339"
---
Den här artikeln visar hur du kommer igång med Custom Vision klient bibliotek med Java för att skapa en modell för objekt identifiering. När den har skapats kan du lägga till taggade regioner, ladda upp bilder, träna projektet, Hämta projektets standard-URL för förutsägelse slut punkt och använda slut punkten för att testa en avbildning. Använd det här exemplet som en mall för att skapa ditt eget Java-program.

## <a name="prerequisites"></a>Krav

- En valfri Java IDE
- [JDK 7 eller 8](https://aka.ms/azure-jdks) installerat.
- [Maven](https://maven.apache.org/) installerat
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library-and-sample-code"></a>Hämta Custom Vision klient bibliotek och exempel kod

Om du vill skriva ett Java-program som använder Custom Vision behöver du Custom Vision maven-paketen. De här paketen ingår i det exempel projekt som du kommer att hämta, men du kan komma åt dem separat här.

Du hittar Custom Vision klient biblioteket i maven Central-lagringsplatsen:
- [Training SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Prediction SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Klona eller ladda ned [Cognitive Services Java SDK-exempelprojektet](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Navigera till mappen **Vision/CustomVision/**.

Det här Java-projektet skapar ett nytt Custom Vision-projekt för objektidentifiering som kallas för __Sample Java OD Project__, som kan nås via [Custom Vision-webbplatsen](https://customvision.ai/). Det laddar sedan upp bilder för att träna och testa en klassificerare. I det här projektet är klassificeraren avsedd att avgöra om ett objekt är en **förgrening** eller **sax**.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

Programmet är konfigurerat för att referera till dina nyckel data som miljövariabler. Navigera till mappen **vision/CustomVision** och ange följande PowerShell-kommandon för att ställa in miljövariablerna. 

> [!NOTE]
> Om du använder ett icke-Windows-operativsystem går du till [Konfigurera miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) för instruktioner.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Förstå koden

Läs in `Vision/CustomVision`-projektet i din Java IDE och öppna filen _CustomVisionSamples.java_. Hitta **runSample** -metoden och kommentera ut **ImageClassification_Sample** metod anropet &mdash; den här metoden kör bild klassificerings scenariot, som inte beskrivs i den här hand boken. Metoden **ObjectDetection_Sample** implementerar de primära funktionerna i den här snabbstarten. Gå till dess definition och granska koden. 

### <a name="create-a-new-custom-vision-service-project"></a>Skapa ett nytt Custom Vision Service-projekt

Gå till det kodblock som skapar en träningsklient och ett projekt för objektidentifiering. Det skapade projektet visas på den [Custom Vision-webbplats](https://customvision.ai/) som du besökte tidigare. Se [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) -metoden för att ange andra alternativ när du skapar ditt projekt (förklaras i guiden [skapa en identifierings](../../get-started-build-detector.md) webb Portal).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

### <a name="add-tags-to-your-project"></a>Lägga till taggar till projektet

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

### <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

När du taggar bilder i objekt identifierings projekt måste du ange regionen för varje taggat objekt med normaliserade koordinater. Gå till definitionen av `regionMap`-kartan. Den här koden associerar var och en av exempelbilderna med dess taggade region.

> [!NOTE]
> Om du inte har ett verktyg för att klicka och dra för att markera koordinaterna för regionerna kan du använda webb gränssnittet på [Customvision.AI](https://www.customvision.ai/). I det här exemplet har koordinaterna redan angetts.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Gå sedan till det kodblock som lägger till bilderna i projektet. Bilderna läses från mappen **src/main/resurser** i projektet och laddas upp till tjänsten med lämpliga taggar och regionskoordinater.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

Föregående kodfragment använder två hjälp funktioner som hämtar avbildningarna som resurs strömmar och laddar upp dem till tjänsten (du kan ladda upp till 64 avbildningar i en enda batch).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-project-and-publish"></a>Träna projektet och publicera

Den här koden skapar den första iterationen av förutsägelse modellen och publicerar sedan en upprepning till förutsägelse slut punkten. Det namn som ges till den publicerade iterationen kan användas för att skicka förutsägelse begär Anden. En iteration är inte tillgänglig i förutsägelse slut punkten förrän den har publicerats.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]

### <a name="use-the-prediction-endpoint"></a>Använd förutsägelse slut punkten

Förutsägelseslutpunkten, som representeras av objektet `predictor` här, är den referens som du kan använda för att skicka en bild till den aktuella modellen och få en klassificeringsförutsägelse. I det här exemplet har `predictor` definierats någon annanstans med hjälp av miljövariabeln för förutsägelsenyckeln.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_prediction_od)]

## <a name="run-the-application"></a>Kör programmet

Om du vill kompilera och köra lösningen med hjälp av maven går du till projekt katalogen (**vision/CustomVision**) i en kommando tolk och kör kommandot kör:

```bash
mvn compile exec:java
```

Visa konsolens utdata för loggnings- och förutsägelseresultat. Du kan sedan kontrollera att testbilden har taggats på rätt sätt och att regionidentifieringen är rätt.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur varje steg i objekt identifierings processen kan göras i kod. Det här exemplet kör en enda inlärnings upprepning, men ofta behöver du träna och testa din modell flera gånger för att göra den mer exakt. Följande tränings guide behandlar bild klassificering, men dess principer liknar objekt identifiering.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](../../test-your-model.md)
