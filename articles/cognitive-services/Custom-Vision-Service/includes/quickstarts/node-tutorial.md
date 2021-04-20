---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: 617db5594af682bcdb67101e3317ec184e874d73
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725357"
---
Den här guiden innehåller instruktioner och exempelkod som hjälper dig att komma igång med Custom Vision-klientbiblioteket för Node.js att skapa en bildklassificeringsmodell. Du skapar ett projekt, lägger till taggar, tränar projektet och använder projektets slutpunkts-URL för förutsägelse för att testa det programmatiskt. Använd det här exemplet som en mall för att skapa en egen bildigenkänningsapp.

> [!NOTE]
> Om du vill skapa och träna en klassificeringsmodell _utan att_ skriva kod kan du gå till [webbläsarbaserad vägledning i](../../getting-started-build-a-classifier.md) stället.

Använd Custom Vision för .NET för att:

* Skapa ett nytt Custom Vision-projekt
* Lägg till taggar i projektet
* Ladda upp och tagga bilder
* Träna projektet
* Publicera den aktuella iterationen
* Testa förutsägelseslutpunkten

[Referensdokumentation (träning)](/javascript/api/@azure/cognitiveservices-customvision-training/) [(förutsägelse)](/javascript/api/@azure/cognitiveservices-customvision-prediction/) | [Bibliotekskällkod (träning)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [(förutsägelse)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction) | Paketexempel (npm) [(träning)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) [(förutsägelse)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction)  |  [](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– Skapa en utan kostnad](https://azure.microsoft.com/free/cognitive-services/)
* Den aktuella versionen av [Node.js](https://nodejs.org/)
* När du har din Azure-prenumeration skapar du en Custom Vision-resurs för att skapa en Custom Vision-resurs i Azure Portal för att skapa en utbildnings- och förutsägelseresurs och hämta dina nycklar <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> och </a> slutpunkter. Vänta tills den har distribuerats och klicka **på knappen Gå till** resurs.
    * Du behöver nyckeln och slutpunkten från de resurser som du skapar för att ansluta ditt program till Custom Vision. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och uppgradera senare till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsolfönster (till exempel cmd, PowerShell eller Bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör kommandot `npm init` för att skapa ett nodprogram med en `package.json` -fil. 

```console
npm init
```

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Om du vill skriva en bildanalysapp med Custom Vision för Node.js behöver du de Custom Vision NPM-paketen. Kör följande kommando i PowerShell för att installera dem:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

Appens `package.json` fil uppdateras med beroendena.

Skapa en fil med `index.js` namnet och importera följande bibliotek:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_imports)]


> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js), som innehåller kodexe exemplen i den här snabbstarten.

Skapa variabler för resursens Azure-slutpunkt och nycklar. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Gå till Azure-portalen. Om den Custom Vision Utbildningsresurs som du skapade i avsnittet Förutsättningar har **distribuerats** klickar du på knappen **Gå till** resurs under **Nästa steg.** Du hittar din nyckel och slutpunkt på resursens **nyckel- och** slutpunktssida. 
>
>Värdet för förutsägelseresurs-ID:t finns på fliken **Egenskaper för** resursen, som visas som **Prenumerations-ID.**
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och aldrig publicera den offentligt. Överväg att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter för produktion. Mer information finns i artikeln Cognitive Services [säkerhetsinformation.](../../../cognitive-services-security.md)

Lägg även till fält för projektnamnet och en timeout-parameter för asynkrona anrop.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_vars)]


## <a name="object-model"></a>Objektmodell

|Name|Beskrivning|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) | Den här klassen hanterar skapande, träning och publicering av dina modeller. |
|[PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)| Den här klassen hanterar frågor om bildklassificeringsförutsägelser i dina modeller.|
|[Prognos](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction)| Det här gränssnittet definierar en enskild förutsägelse på en enda bild. Den innehåller egenskaper för objekt-ID och namn samt en förtroendepoäng.|

## <a name="code-examples"></a>Kodexempel

Dessa kodfragment visar hur du utför följande uppgifter med Custom Vision klientbibliotek för JavaScript:

* [Autentisera klienten](#authenticate-the-client)
* [Skapa ett nytt Custom Vision-projekt](#create-a-new-custom-vision-project)
* [Lägg till taggar i projektet](#add-tags-to-the-project)
* [Ladda upp och tagga bilder](#upload-and-tag-images)
* [Träna projektet](#train-the-project)
* [Publicera den aktuella iterationen](#publish-the-current-iteration)
* [Testa förutsägelseslutpunkten](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera klientobjekt med din slutpunkt och nyckel. Skapa ett **ApiKeyCredentials-objekt** med din nyckel och använd det med slutpunkten för att skapa ett [TrainingAPIClient-](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) och [PredictionAPIClient-objekt.](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_auth)]


## <a name="create-a-new-custom-vision-project"></a>Skapa ett nytt Custom Vision-projekt

Starta en ny funktion som innehåller alla dina Custom Vision funktionsanrop. Lägg till följande kod i för att skapa ett nytt Custom Vision-tjänstprojekt.


[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Lägg till taggar i projektet

Om du vill skapa klassificeringstaggar i projektet lägger du till följande kod i funktionen:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

Ladda först ned exempelbilderna för det här projektet. Spara innehållet i [exempelmappen Bilder](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) på din lokala enhet.

> [!NOTE]
> Behöver du en bredare uppsättning bilder för att slutföra träningen? Med Trove, Microsoft Garage projekt, kan du samla in och köpa uppsättningar av bilder i utbildningssyfte. När du har samlat in dina bilder kan du ladda ned dem och sedan importera dem till ditt Custom Vision-projekt som vanligt. Besök [Trove-sidan om](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) du vill veta mer.

Infoga följande kod efter att taggen har skapats för att lägga till exempelbilder i projektet. Den här koden laddar upp varje bild med dess motsvarande tagg.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_upload)]

> [!IMPORTANT]
> Du måste ändra sökvägen till bilderna ( ) baserat på var du laddade ned `sampleDataRoot` lagringsplatsen Cognitive Services Python SDK Samples.

## <a name="train-the-project"></a>Träna projektet

Den här koden skapar den första iterationen av förutsägelsemodellen. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_train)]

## <a name="publish-the-current-iteration"></a>Publicera den aktuella iterationen

Den här koden publicerar den tränade iterationen till förutsägelseslutpunkten. Det namn som ges till den publicerade iterationen kan användas för att skicka förutsägelse begär Anden. En iteration är inte tillgänglig i förutsägelseslutpunkten förrän den har publicerats.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testa förutsägelseslutpunkten

Om du vill skicka en bild till förutsägelseslutpunkten och hämta förutsägelsen lägger du till följande kod i funktionen. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_test)]

Stäng sedan din Custom Vision och anropa den.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_function_close)]


## <a name="run-the-application"></a>Kör programmet

Kör programmet med kommandot `node` i snabbstartsfilen.

```console
node index.js
```

Programmets utdata bör ser ut ungefär som nedanstående text:

```console
Creating project...
Adding images...
Training...
Training started...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Results:
         Hemlock: 94.97%
         Japanese Cherry: 0.01%
```

Du kan sedan kontrollera att testbilden (finns i **<sampleDataRoot> /Test/**) har taggats på rätt sätt. Du kan också gå tillbaka till [Custom Vision-webbplatsen](https://customvision.ai) och se det aktuella tillståndet för det nyskapade projektet.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur varje steg i objektidentifieringsprocessen kan utföras i kod. Det här exemplet kör en enda tränings iteration, men ofta behöver du träna och testa din modell flera gånger för att göra den mer exakt.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](../../test-your-model.md)

* [Vad är Custom Vision?](../../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js)
* [SDK-referensdokumentation (utbildning)](/javascript/api/@azure/cognitiveservices-customvision-training/)
* [SDK-referensdokumentation (förutsägelse)](/javascript/api/@azure/cognitiveservices-customvision-prediction/)
