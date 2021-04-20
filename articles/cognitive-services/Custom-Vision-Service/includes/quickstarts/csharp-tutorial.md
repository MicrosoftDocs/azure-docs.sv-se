---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 3fcc3598348dcfd3e0d0b81bded40161743126d3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725339"
---
Kom igång med Custom Vision klientbibliotek för .NET. Följ de här stegen för att installera paketet och prova exempelkoden för att skapa en bildklassificeringsmodell. Du skapar ett projekt, lägger till taggar, tränar projektet och använder projektets slutpunkts-URL för förutsägelse för att testa det programmatiskt. Använd det här exemplet som en mall för att skapa en egen bildigenkänningsapp.

> [!NOTE]
> Om du vill skapa och träna en klassificeringsmodell _utan att_ skriva kod kan du gå till [webbläsarbaserad vägledning i](../../getting-started-build-a-classifier.md) stället.

Använd Custom Vision för .NET för att:

* Skapa ett nytt Custom Vision-projekt
* Lägg till taggar i projektet
* Ladda upp och tagga bilder
* Träna projektet
* Publicera den aktuella iterationen
* Testa förutsägelseslutpunkten

[Referensdokumentation](/dotnet/api/overview/azure/cognitiveservices/client/customvision) | [Bibliotekskällkod (träning)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Training) [(förutsägelse)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Prediction) | Paketexempel (NuGet) [(träning)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/) [(förutsägelse)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)  |  [](/samples/browse/?products=azure&term=vision&terms=vision)


## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– Skapa en utan kostnad](https://azure.microsoft.com/free/cognitive-services/)
* Den [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) eller den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* När du har din Azure-prenumeration skapar du en Custom Vision-resurs för att skapa en Custom Vision-resurs i Azure Portal för att skapa en utbildnings- och förutsägelseresurs och hämta dina nycklar <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> och </a> slutpunkter. Vänta tills den har distribuerats och klicka **på knappen Gå till** resurs.
    * Du behöver nyckeln och slutpunkten från de resurser som du skapar för att ansluta ditt program till Custom Vision. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och uppgradera senare till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Med Visual Studio skapar du ett nytt .NET Core-program. 

### <a name="install-the-client-library"></a>Installera klientbiblioteket 

När du har skapat ett nytt projekt installerar du klientbiblioteket genom att högerklicka på projektlösningen i **Solution Explorer** och välja **Hantera NuGet-paket.** I pakethanteraren som öppnas väljer du **Bläddra,** **markerar Inkludera förhandsversion** och söker efter och `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training` `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction` . Välj den senaste versionen och sedan **Installera**. 

#### <a name="cli"></a>[CLI](#tab/cli)

I ett konsolfönster (till exempel cmd, PowerShell eller Bash) använder du kommandot för att skapa en `dotnet new` ny konsolapp med namnet `custom-vision-quickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *program.cs*. 

```console
dotnet new console -n custom-vision-quickstart
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

I programkatalogen installerar du Custom Vision för .NET med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

---

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs), som innehåller kodexe exemplen i den här snabbstarten.

Öppna filen *program.cs från projektkatalogen* och lägg till följande `using` -direktiv:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_imports)]


I programmets **Main-metod** skapar du variabler för resursens nyckel och slutpunkt. Du deklarerar även vissa grundläggande objekt som ska användas senare.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_creds)]

> [!IMPORTANT]
> Gå till Azure-portalen. Om Custom Vision resurser som du skapade  i avsnittet Förutsättningar har distribuerats klickar du på knappen **Gå till** resurs under **Nästa steg.** Du hittar dina nycklar och slutpunkter på resursernas **nyckel- och slutpunktssidor.** Du måste hämta nycklarna för både dina tränings- och förutsägelseresurser, tillsammans med API-slutpunkten för din träningsresurs.
>
> Du hittar värdet för förutsägelseresurs-ID:t på fliken **Egenskaper för** resursen, som visas som **Prenumerations-ID.**
> 
> Kom ihåg att ta bort nycklarna från koden när du är klar och aldrig publicera dem offentligt. Överväg att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter för produktion. Mer information finns i artikeln Cognitive Services [säkerhet.](../../../cognitive-services-security.md)

I programmets Main-metod **lägger** du till anrop för de metoder som används i den här snabbstarten. Du implementerar dessa senare.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Objektmodell

|Name|Beskrivning|
|---|---|
|[CustomVisionTrainingClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Den här klassen hanterar skapandet, träningen och publiceringen av dina modeller. |
|[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Den här klassen hanterar frågor från dina modeller för bildklassificeringsförutsägelser.|
|[PredictionModel](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel)| Den här klassen definierar en enskild förutsägelse för en enskild bild. Den innehåller egenskaper för objekt-ID och namn samt en förtroendepoäng.|

## <a name="code-examples"></a>Kodexempel

Dessa kodfragment visar hur du utför följande uppgifter med Custom Vision klientbibliotek för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Skapa ett nytt Custom Vision-projekt](#create-a-new-custom-vision-project)
* [Lägg till taggar i projektet](#add-tags-to-the-project)
* [Ladda upp och tagga bilder](#upload-and-tag-images)
* [Träna projektet](#train-the-project)
* [Publicera den aktuella iterationen](#publish-the-current-iteration)
* [Testa förutsägelseslutpunkten](#test-the-prediction-endpoint)


## <a name="authenticate-the-client"></a>Autentisera klienten

I en ny metod instansierar du tränings- och förutsägelseklienter med hjälp av din slutpunkt och dina nycklar.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Skapa ett nytt Custom Vision-projekt

Nästa kod skapar ett bildklassificeringsprojekt. Det skapade projektet visas på Custom Vision [webbplats](https://customvision.ai/). Se [createProject-metoden](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) för att ange andra alternativ när du skapar projektet (förklaras i guiden Skapa [en klassificerare i](../../getting-started-build-a-classifier.md) webbportalen).  

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Lägg till taggar i projektet

Den här metoden definierar de taggar som du ska träna modellen på.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_addtags)]

## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

Ladda först ned exempelbilderna för det här projektet. Spara innehållet i mappen [Avbildningsexempel](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) på din lokala enhet.

> [!NOTE]
> Behöver du en bredare uppsättning bilder för att slutföra träningen? Med Trove, Microsoft Garage-projekt, kan du samla in och köpa uppsättningar av bilder i utbildningssyfte. När du har samlat in dina bilder kan du ladda ned dem och sedan importera dem till ditt Custom Vision-projekt som vanligt. Besök [Trove-sidan om](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) du vill veta mer.

Definiera sedan en hjälpmetod för att ladda upp bilderna i den här katalogen. Du kan behöva redigera argumentet **GetFiles** så att det pekar på den plats där bilderna sparas.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_loadimages)]

Definiera sedan en metod för att ladda upp bilderna och tillämpa taggar enligt deras mappplats (bilderna är redan sorterade). Du kan ladda upp och tagga bilder iterativt eller i en batch (upp till 64 per batch). Det här kodfragmentet innehåller exempel på båda. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]


## <a name="train-the-project"></a>Träna projektet

Den här metoden skapar den första tränings iterationen i projektet. Den frågar tjänsten tills träningen har slutförts.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

> [!TIP]
> Träna med valda taggar
>
> Om du vill kan du bara träna på en delmängd av dina tillämpade taggar. Du kanske vill göra detta om du inte har tillämpat tillräckligt många taggar ännu, men du har tillräckligt med andra. I [anropet TrainProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) använder du *parametern trainingParameters.* Skapa en [TrainingParameters](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters) och ange dess **SelectedTags-egenskap** till en lista med ID:er för de taggar som du vill använda. Modellen kommer att tränas att endast identifiera taggarna i listan.

## <a name="publish-the-current-iteration"></a>Publicera den aktuella iterationen

Den här metoden gör den aktuella iterationen av modellen tillgänglig för frågor. Du kan använda modellnamnet som referens för att skicka förutsägelsebegäranden. Du måste ange ett eget värde för `predictionResourceId` . Du hittar resurs-ID:t för förutsägelsen på **fliken** Översikt för resursen Azure Portal **prenumerations-ID**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testa förutsägelseslutpunkten

Den här delen av skriptet läser in test avbildningen, frågar modell slut punkten och matar ut förutsägelse data till-konsolen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_test)]


## <a name="run-the-application"></a>Köra appen

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Kör programmet genom att klicka **på knappen** Felsök längst upp i IDE-fönstret.

#### <a name="cli"></a>[CLI](#tab/cli)

Kör programmet från programkatalogen med `dotnet run` kommandot .

```dotnet
dotnet run
```

---

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

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du gjort varje steg i bildklassificeringsprocessen i kod. Det här exemplet kör en enda tränings-iteration, men ofta behöver du träna och testa din modell flera gånger för att göra den mer exakt.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](../../test-your-model.md)

* [Vad är Custom Vision?](../../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs)
* [Referensdokumentation för SDK](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
