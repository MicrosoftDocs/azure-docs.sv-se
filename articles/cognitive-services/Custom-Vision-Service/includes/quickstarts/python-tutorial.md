---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: dde50986601a9e6412b81fe0c4faa9775784c6c8
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97582759"
---
Kom igång med Custom Vision klient biblioteket för python. Följ de här stegen för att installera paketet och prova exempel koden för att skapa en bild klassificerings modell. Du skapar ett projekt, lägger till taggar, tränar projektet och använder projektets förutsäga slut punkts-URL för att program mässigt testa det. Använd det här exemplet som mall för att skapa en egen bild igenkännings app.

> [!NOTE]
> Om du vill skapa och träna en klassificerings modell _utan att_ skriva kod, se den [webbläsarbaserade vägledningen](../../getting-started-build-a-classifier.md) i stället.

Använd Custom Vision klient bibliotek för python för att:

* Skapa ett nytt Custom Vision-projekt
* Lägg till taggar i projektet
* Ladda upp och tagga bilder
* Träna projektet
* Publicera den aktuella iterationen
* Testa förutsägelse slut punkten

[Referens dokumentation](/python/api/overview/azure/cognitiveservices/customvision?view=azure-python)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision)  |  [Paket (pypi)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/)  |  [Exempel](/samples/browse/?languages=python&products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* När du har en Azure-prenumeration skapar du <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" en Custom vision resurs "  target="_blank"> skapa en Custom vision resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att skapa en utbildnings-och förutsägelse resurs och hämta nycklar och slut punkt. Vänta tills den har distribuerats och klicka på knappen **gå till resurs** .
    * Du behöver nyckeln och slut punkten från de resurser som du skapar för att ansluta ditt program till Custom Vision. Du ska klistra in dina nycklar och din slut punkt i koden nedan senare i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="install-the-client-library"></a>Installera klient biblioteket

Om du vill skriva en app Analysis-app med Custom Vision för python behöver du Custom Vision klient biblioteket. När du har installerat python kör du följande kommando i PowerShell eller ett konsol fönster:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>Skapa ett nytt python-program

Skapa en ny python-fil och importera följande bibliotek.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py), som innehåller kod exemplen i den här snabb starten.

Skapa variabler för resursens Azure-slut punkts-och prenumerations nycklar.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_creds)]

> [!IMPORTANT]
> Gå till Azure-portalen. Om Custom Vision resurserna som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar nycklar och slut punkt i resursens nyckel- **och slut punkts** sidor under **resurs hantering**. Du måste få både dina utbildnings-och förutsägelse nycklar, tillsammans med utbildnings resursernas slut punkt.
>
> Du hittar värdet för förutsägelse resurs-ID på resursens flik **Översikt** , listad som **prenumerations-ID**.
>
> Kom ihåg att ta bort nycklarna från koden när du är klar och publicera dem aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Mer information finns i [säkerhets](../../../cognitive-services-security.md) artikeln Cognitive Services.

## <a name="object-model"></a>Objekt modell

|Name|Beskrivning|
|---|---|
|[CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-python) | Den här klassen hanterar skapandet, utbildningen och publiceringen av dina modeller. |
|[CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-python)| Den här klassen hanterar frågekörning för modeller för bild klassificerings förutsägelser.|
|[ImagePrediction](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction?view=azure-python)| Den här klassen definierar en enda objekt förutsägelse på en enda avbildning. Den innehåller egenskaper för objekt-ID och namn, objektets plats för objektets placering och en säkerhets poäng.|

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med Custom Vision klient biblioteket för python:

* [Autentisera klienten](#authenticate-the-client)
* [Skapa ett nytt Custom Vision-projekt](#create-a-new-custom-vision-project)
* [Lägg till taggar i projektet](#add-tags-to-the-project)
* [Ladda upp och tagga bilder](#upload-and-tag-images)
* [Träna projektet](#train-the-project)
* [Publicera den aktuella iterationen](#publish-the-current-iteration)
* [Testa förutsägelse slut punkten](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera en Training-och förutsägelse klient med din slut punkt och nycklar. Skapa **ApiKeyServiceClientCredentials** -objekt med dina nycklar och Använd dem med slut punkten för att skapa ett [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-python) -och [CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-python) -objekt.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Skapa ett nytt Custom Vision-projekt

Lägg till följande kod i skriptet för att skapa ett nytt Custom Vision Service-projekt. 

Se [create_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-) -metoden för att ange andra alternativ när du skapar ditt projekt (förklaras i guiden [skapa en klassificerings](../../getting-started-build-a-classifier.md) webb Portal).  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>Lägg till taggar i projektet

Lägg till klassificerings Taggar i projektet genom att lägga till följande kod:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

Hämta först exempel bilderna för projektet. Spara innehållet i [mappen exempel bilder](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) på din lokala enhet.

Infoga följande kod efter att taggen har skapats för att lägga till exempelbilder i projektet. Den här koden laddar upp varje bild med dess motsvarande tagg. Du kan ladda upp upp till 64 avbildningar i en enda batch.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_upload)]

> [!NOTE]
> Du måste ändra sökvägen till avbildningarna baserat på var du laddade ned Cognitive Services python SDK-exempel lagrings platsen.

## <a name="train-the-project"></a>Träna projektet

Den här koden skapar den första iterationen av förutsägelse modellen. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> Träna med valda Taggar
>
> Du kan välja att bara träna på en delmängd av dina använda taggar. Du kanske vill göra detta om du inte har använt tillräckligt med vissa Taggar ännu, men du har tillräckligt med andra. I **[train_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** -anropet ställer du in den valfria parametern *selected_tags* till en lista över ID-strängarna för de taggar som du vill använda. Modellen kommer att träna att bara identifiera taggarna i listan.

## <a name="publish-the-current-iteration"></a>Publicera den aktuella iterationen

En iteration är inte tillgänglig i förutsägelse slut punkten förrän den har publicerats. Följande kod gör den aktuella iterationen av modellen tillgänglig för frågor. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testa förutsägelse slut punkten

Om du vill skicka en bild till slutpunkten för förutsägelse och hämta förutsägelsen lägger du till följande kod i slutet av filen:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_test)]

## <a name="run-the-application"></a>Köra appen

Kör *CustomVisionQuickstart.py*.

```powershell
python CustomVisionQuickstart.py
```

Programmets utdata bör ser ut ungefär som nedanstående text:

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Sedan kan du kontrol lera att test avbildningen (som finns i **<base_image_location>/images/test/**) är korrekt formaterad. Du kan också gå tillbaka till [Custom Vision-webbplatsen](https://customvision.ai) och se det aktuella tillståndet för det nyskapade projektet.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur varje steg i bild klassificerings processen kan göras i kod. Det här exemplet kör en enda inlärnings upprepning, men ofta behöver du träna och testa din modell flera gånger för att göra den mer exakt.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](../../test-your-model.md)

* [Vad är Custom Vision?](../../overview.md)
* Du hittar käll koden för det här exemplet på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py)
* [Referensdokumentation för SDK](/python/api/overview/azure/cognitiveservices/customvision?view=azure-python)
