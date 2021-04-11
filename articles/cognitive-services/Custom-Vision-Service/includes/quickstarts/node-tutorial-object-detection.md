---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: b78f74ec190f4b871e127fbe4e6b7a0f10d058c1
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113405"
---
Den här guiden innehåller instruktioner och exempel kod som hjälper dig att komma igång med Custom Vision klient biblioteket för Node.js för att skapa en modell för objekt identifiering. Du skapar ett projekt, lägger till taggar, tränar projektet och använder projektets förutsäga slut punkts-URL för att program mässigt testa det. Använd det här exemplet som mall för att skapa en egen bild igenkännings app.

> [!NOTE]
> Om du vill skapa och träna en objekt identifierings modell _utan att_ skriva kod, se den [webbläsarbaserade vägledningen](../../get-started-build-detector.md) i stället.

Använd Custom Vision klient bibliotek för .NET för att:

* Skapa ett nytt Custom Vision-projekt
* Lägg till taggar i projektet
* Ladda upp och tagga bilder
* Träna projektet
* Publicera den aktuella iterationen
* Testa förutsägelse slut punkten

Referens dokumentation [(utbildning)](/javascript/api/@azure/cognitiveservices-customvision-training/) [(förutsägelse)](/javascript/api/@azure/cognitiveservices-customvision-prediction/) | Biblioteks käll kod [(utbildning)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [(förutsägelse)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction) | Paket (NPM) [(utbildning)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) [(förutsägelse)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction)  |  [exempel](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)


## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* Den aktuella versionen av [Node.js](https://nodejs.org/)
* När du har en Azure-prenumeration skapar du <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title=" en Custom vision resurs "  target="_blank"> skapa en Custom vision resurs </a> i Azure Portal för att skapa en utbildnings-och förutsägelse resurs och hämta nycklar och slut punkt. Vänta tills den har distribuerats och klicka på knappen **gå till resurs** .
    * Du behöver nyckeln och slut punkten från de resurser som du skapar för att ansluta ditt program till Custom Vision. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör `npm init` kommandot för att skapa ett Node-program med en `package.json` fil. 

```console
npm init
```

### <a name="install-the-client-library"></a>Installera klient biblioteket

Om du vill skriva en app för bildanalys med Custom Vision för Node.js behöver du Custom Vision NPM-paket. Installera dem genom att köra följande kommando i PowerShell:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

Appens `package.json` fil kommer att uppdateras med beroenden.

Skapa en fil med namnet `index.js` och importera följande bibliotek:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js), som innehåller kod exemplen i den här snabb starten.

Skapa variabler för resursens Azure-slutpunkt och nycklar. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Gå till Azure-portalen. Om Custom Vision resurserna som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar nycklar och slut punkt i resursens nyckel- **och slut punkts** sidor under **resurs hantering**. Du måste hämta nycklarna för både utbildnings-och förutsägelse resurserna, tillsammans med API-slutpunkten för utbildnings resursen.
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Mer information finns i [säkerhets](../../../../cognitive-services/cognitive-services-security.md) artikeln Cognitive Services.

Lägg också till fält för ditt projekt namn och en timeout-parameter för asynkrona anrop.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_vars)]

## <a name="object-model"></a>Objekt modell

|Name|Beskrivning|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) | Den här klassen hanterar skapandet, utbildningen och publiceringen av dina modeller. |
|[PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)| Den här klassen hanterar frågekörning för modeller för objekt identifierings förutsägelser.|
|[Förutsägelse](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction)| Det här gränssnittet definierar en enstaka förutsägelse på en enda avbildning. Den innehåller egenskaper för objekt-ID och namn och en förtroende poäng.|

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande uppgifter med Custom Vision klient bibliotek för Java Script:

* [Autentisera klienten](#authenticate-the-client)
* [Skapa ett nytt Custom Vision-projekt](#create-a-new-custom-vision-project)
* [Lägg till taggar i projektet](#add-tags-to-the-project)
* [Ladda upp och tagga bilder](#upload-and-tag-images)
* [Träna projektet](#train-the-project)
* [Publicera den aktuella iterationen](#publish-the-current-iteration)
* [Testa förutsägelse slut punkten](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera klient objekt med din slut punkt och nyckel. Skapa ett **ApiKeyCredentials** -objekt med din nyckel och Använd den med slut punkten för att skapa ett [TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) -och [PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient) -objekt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="add-helper-function"></a>Lägg till hjälp funktion

Lägg till följande funktion för att hjälpa till att göra flera asynkrona anrop. Du kommer att använda detta senare.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Skapa ett nytt Custom Vision-projekt

Starta en ny funktion som innehåller alla dina Custom Vision funktions anrop. Lägg till följande kod för att skapa ett nytt Custom Vision-tjänst projekt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_create)]

## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

Hämta först exempel bilderna för projektet. Spara innehållet i [mappen exempel bilder](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) på din lokala enhet.

> [!NOTE]
> Behöver du en bredare uppsättning avbildningar för att slutföra utbildningen? Källa, ett Microsoft garage-projekt, gör att du kan samla in och köpa uppsättningar av avbildningar i utbildnings syfte. När du har samlat in dina avbildningar kan du hämta dem och sedan importera dem till ditt Custom Vision-projekt på vanligt sätt. Besök [sidan källa](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) om du vill veta mer.

Infoga följande kod efter att taggen har skapats för att lägga till exempelbilder i projektet. Den här koden laddar upp varje bild med dess motsvarande tagg. När du taggar bilder i objekt identifierings projekt måste du ange regionen för varje taggat objekt med normaliserade koordinater. I den här självstudien är regionerna hårdkodad infogade i koden. Regionerna specificerar avgränsningsfältet i normaliserade koordinater, och koordinaterna anges i följande ordning: vänster, överst, bredd, höjd. Du kan ladda upp upp till 64 avbildningar i en enda batch.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_upload)]


> [!IMPORTANT]
> Du måste ändra sökvägen till avbildningarna ( `sampleDataRoot` ) baserat på var du laddade ned Cognitive Services python SDK-exempel lagrings platsen.

> [!NOTE]
> Om du inte har ett verktyg för att klicka och dra för att markera koordinaterna för regionerna kan du använda webb gränssnittet på [Customvision.AI](https://www.customvision.ai/). I det här exemplet har koordinaterna redan angetts.


## <a name="train-the-project"></a>Träna projektet

Den här koden skapar den första iterationen av förutsägelse modellen. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>Publicera den aktuella iterationen

Den här koden publicerar den utbildade iterationen till förutsägelse slut punkten. Det namn som ges till den publicerade iterationen kan användas för att skicka förutsägelse begär Anden. En iteration är inte tillgänglig i förutsägelse slut punkten förrän den har publicerats.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testa förutsägelse slut punkten

Om du vill skicka en avbildning till förutsägelse slut punkten och hämta förutsägelsen lägger du till följande kod i din funktion. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_test)]

Stäng sedan Custom Vision-funktionen och anropa den.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_function_close)]

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `node` kommandot på snabb starts filen.

```shell
node index.js
```

Programmets utdata bör visas i konsolen. Sedan kan du kontrol lera att test avbildningen (hittas i **<sampleDataRoot> /test/**) är korrekt formaterad och att identifierings området är korrekt. Du kan också gå tillbaka till [Custom Vision-webbplatsen](https://customvision.ai) och se det aktuella tillståndet för det nyskapade projektet.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du gjort varje steg i processen för objekt identifiering i kod. Det här exemplet kör en enda inlärnings upprepning, men ofta behöver du träna och testa din modell flera gånger för att göra den mer exakt. Följande guide behandlar bildklassificering, men principerna liknar dem som gäller för objektidentifiering.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](../../test-your-model.md)

* [Vad är Custom Vision?](../../overview.md)
* Du hittar käll koden för det här exemplet på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js)
* [SDK Reference Documentation (utbildning)](/javascript/api/@azure/cognitiveservices-customvision-training/)
* [SDK Reference Documentation (förutsägelse)](/javascript/api/@azure/cognitiveservices-customvision-prediction/)
