---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: 2ac00ddf419dc06ff2b330423cd4025499329b33
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725346"
---
Kom igång med Custom Vision klientbibliotek för Python. Följ de här stegen för att installera paketet och prova exempelkoden för att skapa en bildklassificeringsmodell. Du skapar ett projekt, lägger till taggar, tränar projektet och använder projektets slutpunkts-URL för förutsägelse för att testa det programmatiskt. Använd det här exemplet som en mall för att skapa en egen bildigenkänningsapp.

> [!NOTE]
> Om du vill skapa och träna en klassificeringsmodell _utan att_ skriva kod kan du gå till [webbläsarbaserad vägledning i](../../getting-started-build-a-classifier.md) stället.

Använd Custom Vision för Python för att:

* Skapa ett nytt Custom Vision-projekt
* Lägg till taggar i projektet
* Ladda upp och tagga bilder
* Träna projektet
* Publicera den aktuella iterationen
* Testa förutsägelseslutpunkten

[Referensdokumentation](/python/api/overview/azure/cognitiveservices/customvision)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision)  |  [Paket (PyPI)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/)  |  [Exempel](/samples/browse/?languages=python&products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– Skapa en utan kostnad](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * Python-installationen bör innehålla [pip](https://pip.pypa.io/en/stable/). Du kan kontrollera om du har installerat pip genom `pip --version` att köra på kommandoraden. Hämta pip genom att installera den senaste versionen av Python.
* När du har din Azure-prenumeration skapar du en Custom Vision-resurs för att skapa en Custom Vision-resurs i Azure Portal för att skapa en utbildnings- och förutsägelseresurs och hämta dina nycklar <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> och </a> slutpunkter. Vänta tills den har distribuerats och klicka **på knappen Gå till** resurs.
    * Du behöver nyckeln och slutpunkten från de resurser som du skapar för att ansluta ditt program till Custom Vision. Du klistrar in dina nycklar och slutpunkter i koden nedan senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och uppgradera senare till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Om du vill skriva en bildanalysapp Custom Vision för Python behöver du Custom Vision klientbiblioteket. När du har installerat Python kör du följande kommando i PowerShell eller ett konsolfönster:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Skapa en ny Python-fil och importera följande bibliotek.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py), som innehåller kodexe exemplen i den här snabbstarten.

Skapa variabler för resursens Azure-slutpunkt och prenumerationsnycklar.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_creds)]

> [!IMPORTANT]
> Gå till Azure-portalen. Om Custom Vision resurser som du skapade  i avsnittet Förutsättningar har distribuerats klickar du på knappen **Gå till** resurs under **Nästa steg.** Du hittar dina nycklar och slutpunkter på resursernas **nyckel- och slutpunktssidor.** Du måste hämta nycklarna för både dina tränings- och förutsägelseresurser, tillsammans med API-slutpunkten för din träningsresurs.
>
> Du hittar värdet för förutsägelseresurs-ID:t på fliken **Egenskaper för** resursen, som visas som **Prenumerations-ID.**
>
> Kom ihåg att ta bort nycklarna från koden när du är klar och aldrig publicera dem offentligt. Överväg att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter för produktion. Mer information finns i artikeln Cognitive Services [säkerhetsinformation.](../../../cognitive-services-security.md)

## <a name="object-model"></a>Objektmodell

|Name|Beskrivning|
|---|---|
|[CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Den här klassen hanterar skapande, träning och publicering av dina modeller. |
|[CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Den här klassen hanterar frågor om bildklassificeringsförutsägelser i dina modeller.|
|[ImagePrediction](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Den här klassen definierar en enskild objektförutsägelse på en enda bild. Den innehåller egenskaper för objekt-ID och namn, objektets begränsningsruta och förtroendepoäng.|

## <a name="code-examples"></a>Kodexempel

Dessa kodfragment visar hur du gör följande med Custom Vision klientbibliotek för Python:

* [Autentisera klienten](#authenticate-the-client)
* [Skapa ett nytt Custom Vision-projekt](#create-a-new-custom-vision-project)
* [Lägg till taggar i projektet](#add-tags-to-the-project)
* [Ladda upp och tagga bilder](#upload-and-tag-images)
* [Träna projektet](#train-the-project)
* [Publicera den aktuella iterationen](#publish-the-current-iteration)
* [Testa förutsägelseslutpunkten](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera en tränings- och förutsägelseklient med din slutpunkt och dina nycklar. Skapa **ApiKeyServiceClientCredentials-objekt** med dina nycklar och använd dem med slutpunkten för att skapa ett [CustomVisionTrainingClient-](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) och [CustomVisionPredictionClient-objekt.](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Skapa ett nytt Custom Vision-projekt

Lägg till följande kod i skriptet för att skapa ett nytt Custom Vision Service-projekt. 

Se metoden [create_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-) för att ange andra alternativ när du skapar projektet (förklaras i guiden [Skapa en klassificerare i](../../getting-started-build-a-classifier.md) webbportalen).  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>Lägg till taggar i projektet

Lägg till följande kod för att lägga till klassificeringstaggar i projektet:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

Ladda först ned exempelbilderna för det här projektet. Spara innehållet i [exempelmappen Bilder](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) på din lokala enhet.

> [!NOTE]
> Behöver du en bredare uppsättning bilder för att slutföra träningen? Med Trove, Microsoft Garage projekt, kan du samla in och köpa uppsättningar av bilder i utbildningssyfte. När du har samlat in dina bilder kan du ladda ned dem och sedan importera dem till ditt Custom Vision-projekt som vanligt. Besök [Trove-sidan om](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) du vill veta mer.

Infoga följande kod efter att taggen har skapats för att lägga till exempelbilder i projektet. Den här koden laddar upp varje bild med dess motsvarande tagg. Du kan ladda upp upp till 64 bilder i en enda batch.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_upload)]

> [!NOTE]
> Du måste ändra sökvägen till bilderna baserat på var du laddade ned lagringsplatsen Cognitive Services Python SDK Samples.

## <a name="train-the-project"></a>Träna projektet

Den här koden skapar den första iterationen av förutsägelsemodellen. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> Träna med valda taggar
>
> Om du vill kan du bara träna med en delmängd av dina tillämpade taggar. Du kanske vill göra detta om du inte har tillämpat tillräckligt med vissa taggar ännu, men du har tillräckligt med andra. I **[train_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** anropar du den valfria *parametern selected_tags* till en lista över ID-strängarna för de taggar som du vill använda. Modellen kommer att tränas att endast identifiera taggarna i listan.

## <a name="publish-the-current-iteration"></a>Publicera den aktuella iterationen

En iteration är inte tillgänglig i förutsägelseslutpunkten förrän den har publicerats. Följande kod gör den aktuella iterationen av modellen tillgänglig för frågor. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testa förutsägelseslutpunkten

Om du vill skicka en bild till slutpunkten för förutsägelse och hämta förutsägelsen lägger du till följande kod i slutet av filen:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_test)]

## <a name="run-the-application"></a>Kör programmet

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

Du kan sedan kontrollera att testbilden (finns i **<base_image_location>/images/Test/**) har taggats på rätt sätt. Du kan också gå tillbaka till [Custom Vision-webbplatsen](https://customvision.ai) och se det aktuella tillståndet för det nyskapade projektet.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur varje steg i bildklassificeringsprocessen kan utföras i kod. Det här exemplet kör en enda tränings iteration, men ofta behöver du träna och testa modellen flera gånger för att göra den mer exakt.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](../../test-your-model.md)

* [Vad är Custom Vision?](../../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py)
* [Referensdokumentation för SDK](/python/api/overview/azure/cognitiveservices/customvision)
