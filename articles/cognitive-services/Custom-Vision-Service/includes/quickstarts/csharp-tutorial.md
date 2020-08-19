---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 08/17/2020
ms.openlocfilehash: 758d288480c8d6a7ef5185cff33a857633c5e95b
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508565"
---
Den här artikeln innehåller information och exempel kod som hjälper dig att komma igång med Custom Vision klient biblioteket med C# för att skapa en bild klassificerings modell. När den har skapats kan du lägga till taggar, ladda upp bilder, träna projektet, hämta slutpunkts-URL:en för projektets standardförutsägelse och använda slutpunkten för att testa en avbildning programmatiskt. Använd det här exemplet som mall för att skapa ditt eget .NET-program. Om du vill gå igenom processen för att skapa och använda en klassificerings modell _utan_ kod, se den [webbläsarbaserade vägledningen](../../getting-started-build-a-classifier.md) i stället.

## <a name="prerequisites"></a>Krav

- En version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library-and-sample-code"></a>Hämta Custom Vision klient bibliotek och exempel kod

Om du vill skriva en .NET-app som använder Custom Vision behöver du Custom Vision NuGet-paketen. De här paketen ingår i det exempel projekt som du hämtar, men du kan komma åt dem separat här.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Klona eller ladda ned [Cognitive Services .NET-exempelprojektet](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Navigera till mappen **CustomVision/ImageClassification** och öppna _ImageClassification.csproj_ i Visual Studio.

Det här projektet i Visual Studio skapar ett nytt Custom Vision-projekt med namnet __Mitt nya projekt__, som kan nås via [Custom Vision-webbplatsen](https://customvision.ai/). Det laddar sedan upp bilder för att träna och testa en klassificerare. I det här projektet är klassificeraren avsedd att avgöra huruvida ett träd är en __hemlockgran__ eller ett __japanskt körsbärsträd__.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

## <a name="understand-the-code"></a>Förstå koden

Öppna filen _Program.cs_ och granska koden. [Skapa miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för dina utbildnings-och förutsägelse nycklar med namnet `CUSTOM_VISION_TRAINING_KEY` respektive `CUSTOM_VISION_PREDICTION_KEY` . Skriptet kommer att söka efter dessa variabler.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

Hämta även slut punkts-URL: en från sidan Inställningar på webbplatsen för Custom Vision. Spara den till en miljö variabel som kallas `CUSTOM_VISION_ENDPOINT`. Skriptet sparar en referens till den i roten av klassen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

Följande rader i koden kör de primära funktionerna i projektet.

### <a name="create-a-new-custom-vision-service-project"></a>Skapa ett nytt projekt för Custom Vision Service

Det skapade projektet visas på den [Custom Vision-webbplats](https://customvision.ai/) som du besökte tidigare. Se [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) -metoden för att ange andra alternativ när du skapar ditt projekt (förklaras i guiden [skapa en klassificerings](../../getting-started-build-a-classifier.md) webb Portal).   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Skapa taggar i projektet

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

Avbildningarna för det här projektet ingår. De refereras till i metoden **LoadImagesFromDisk** i _Program.cs_. Du kan ladda upp upp till 64 avbildningar i en enda batch.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

### <a name="train-the-classifier-and-publish"></a>Träna klassificeraren och publicera

Den här koden skapar den första iterationen av förutsägelse modellen och publicerar sedan en upprepning till förutsägelse slut punkten. Du kan använda namnet på iterationen för att skicka förutsägelse begär Anden. En iteration är inte tillgänglig i förutsägelse slut punkten förrän den har publicerats.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

### <a name="set-the-prediction-endpoint"></a>Ange förutsägelseslutpunkten

Förutsägelseslutpunkten är den referens som du kan använda för att skicka en avbildning till den aktuella modellen och få en klassificeringsförutsägelse.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Skicka en avbildning till standardslutpunkten för förutsägelse

I det här skriptet läses testavbildningen in i metoden **LoadImagesFromDisk**, och modellens förutsägelseutdata ska visas i konsolen. Värdet för `publishedModelName` variabeln ska motsvara värdet "publicerat as" på Custom vision portalens **prestanda** -flik. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Köra appen

När programmet körs ska det öppna ett konsol fönster och skriva följande utdata:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Du kan sedan kontrollera att testbilden (som finns i **Images/Test/**) har taggats på rätt sätt. Avsluta programmet genom att trycka på valfri tangent. Du kan också gå tillbaka till [Custom Vision-webbplatsen](https://customvision.ai) och se det aktuella tillståndet för det nyskapade projektet.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur du gör varje steg i processen för bild klassificering i kod. Det här exemplet kör en enstaka träningsiteration, men ofta måste du träna och testa modellen flera gånger för att kunna göra den mer exakt.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](../../test-your-model.md)
