---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 12/09/2020
ms.topic: include
ms.openlocfilehash: cea3db080865727ab9b425f14a172041a54a5414
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948002"
---
Kom igång med Custom Vision REST API. Följ de här stegen för att anropa API: et och bygga en bild klassificerings modell. Du skapar ett projekt, lägger till taggar, tränar projektet och använder projektets förutsäga slut punkts-URL för att program mässigt testa det. Använd det här exemplet som mall för att skapa en egen bild igenkännings app.

> [!NOTE]
> Custom Vision används enkelt via ett klient biblioteks-SDK eller via den [webbläsarbaserade vägledningen](../../get-started-build-detector.md).

Använd Custom Vision klient bibliotek för .NET för att:

* Skapa ett nytt Custom Vision-projekt
* Lägg till taggar i projektet
* Ladda upp och tagga bilder
* Träna projektet
* Publicera den aktuella iterationen
* Testa förutsägelse slut punkten

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration skapar du <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" en Custom vision resurs "  target="_blank"> skapa en Custom vision resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att skapa en utbildnings-och förutsägelse resurs och hämta nycklar och slut punkt. Vänta tills den har distribuerats och klicka på knappen **gå till resurs** .
    * Du behöver nyckeln och slut punkten från de resurser som du skapar för att ansluta ditt program till Custom Vision. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.


## <a name="create-a-new-custom-vision-project"></a>Skapa ett nytt Custom Vision-projekt

Du använder ett kommando som följande för att skapa ett bild klassificerings projekt. Det skapade projektet visas på [Custom vision webbplats](https://customvision.ai/).


:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createproject":::

Kopiera kommandot till en text redigerare och gör följande ändringar:

* Ersätt `{subscription key}` med en giltig ansiktsprenumerationsnyckel.
* Ersätt `{endpoint}` med den slut punkt som motsvarar din prenumerations nyckel.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
* Ersätt `{name}` med namnet på ditt projekt.
* Du kan också ange andra URL-parametrar för att konfigurera vilken typ av modell ditt projekt ska använda. Se [CreatProject-API: et](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) för alternativ.

Du får ett JSON-svar som följande. Spara `"id"` värdet för projektet på en tillfällig plats.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="add-tags-to-the-project"></a>Lägg till taggar i projektet

Använd följande kommando för att definiera de taggar som du vill träna modellen på.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createtag":::

* Infoga sedan din egen nyckel och slut punkts-URL igen.
* Ersätt `{projectId}` med ditt eget projekt-ID.
* Ersätt `{name}` med namnet på den tagg som du vill använda.

Upprepa processen för alla Taggar som du vill använda i projektet. Om du använder de exempel bilder som anges lägger du till taggarna `"Hemlock"` och `"Japanese Cherry"` .

Du får ett JSON-svar som följande. Spara `"id"` värdet för varje tagg på en tillfällig plats.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "type": "Regular",
  "imageCount": 0
}
```

## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

Hämta sedan exempel bilderna för projektet. Spara innehållet i [mappen exempel bilder](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) på din lokala enhet.

> [!NOTE]
> Källa, ett Microsoft garage-projekt, gör att du kan samla in och köpa uppsättningar av avbildningar i utbildnings syfte. När du har samlat in dina avbildningar kan du hämta dem och sedan importera dem till ditt Custom Vision-projekt på vanligt sätt. Besök [sidan källa](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3) om du vill veta mer.

Använd följande kommando för att ladda upp avbildningar och använda taggar. en gång för "Hemlock"-avbildningar och separat för "japanska körsbär"-avbildningar. Mer alternativ finns i [skapa avbildningar från data](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb5) -API.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="uploadimages":::

* Infoga sedan din egen nyckel och slut punkts-URL igen.
* Ersätt `{projectId}` med ditt eget projekt-ID.
* Ersätt `{tagArray}` med ID för en tagg.
* Fyll sedan i bröd texten i begäran med de binära data för de avbildningar som du vill tagga.

## <a name="train-the-project"></a>Träna projektet

Den här metoden tränar modellen på de taggade bilder som du har laddat upp och returnerar ett ID för den aktuella projekt iterationen.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="trainproject":::

* Infoga sedan din egen nyckel och slut punkts-URL igen.
* Ersätt `{projectId}` med ditt eget projekt-ID.
* Ersätt `{tagArray}` med ID för en tagg.
* Fyll sedan i bröd texten i begäran med de binära data för de avbildningar som du vill tagga.
* Du kan också använda andra URL-parametrar. Se alternativ för [träna projekt](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) -API.

> [!TIP]
> Träna med valda Taggar
>
> Du kan välja att bara träna på en delmängd av dina använda taggar. Du kanske vill göra detta om du inte har använt tillräckligt med vissa Taggar ännu, men du har tillräckligt med andra. Lägg till valfritt JSON-innehåll i bröd texten i din begäran. Fyll i `"selectedTags"` matrisen med ID: n för de taggar som du vill använda.
> ```json
> {
>   "selectedTags": [
>     "00000000-0000-0000-0000-000000000000"
>   ]
> }
> ```

JSON-svaret innehåller information om det utbildade projektet, inklusive upprepnings-ID ( `"id"` ). Spara det här värdet för nästa steg.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "status": "string",
  "created": "string",
  "lastModified": "string",
  "trainedAt": "string",
  "projectId": "00000000-0000-0000-0000-000000000000",
  "exportable": true,
  "exportableTo": [
    "CoreML"
  ],
  "domainId": "00000000-0000-0000-0000-000000000000",
  "classificationType": "Multiclass",
  "trainingType": "Regular",
  "reservedBudgetInHours": 0,
  "trainingTimeInMinutes": 0,
  "publishName": "string",
  "originalPublishResourceId": "string"
}
```

## <a name="publish-the-current-iteration"></a>Publicera den aktuella iterationen

Den här metoden gör den aktuella iterationen av modellen tillgänglig för frågor. Du använder det returnerade modell namnet som en referens för att skicka förutsägelse begär Anden. 

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Infoga sedan din egen nyckel och slut punkts-URL igen.
* Ersätt `{projectId}` med ditt eget projekt-ID.
* Ersätt `{iterationId}` med det ID som returnerades i föregående steg.
* Ersätt `{publishedName}` med det namn som du vill tilldela din förutsägelse modell.
* Ersätt `{predictionId}` med ditt eget förutsägelse resurs-ID. Du hittar den på fliken **Översikt** för förutsägelse resursen i Azure Portal, listad som **prenumerations-ID**.
* Du kan också använda andra URL-parametrar. Se API för [publicerings upprepning](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fdded5) .

## <a name="test-the-prediction-endpoint"></a>Testa förutsägelse slut punkten

Använd slutligen det här kommandot för att testa din tränade modell genom att ladda upp en ny bild som kan klassificeras med taggar. Du kan använda avbildningen i "test"-mappen i exempelfilerna som du laddade ned tidigare.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Infoga sedan din egen nyckel och slut punkts-URL igen.
* Ersätt `{projectId}` med ditt eget projekt-ID.
* Ersätt `{publishedName}` med det namn som du använde i föregående steg.
* Lägg till binära data från din lokala avbildning i begär ande texten.
* Du kan också använda andra URL-parametrar. Se avsnittet [klassificera bild](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3) -API.

Det returnerade JSON-svaret kommer att vara minst var och en av de taggar som modellen tillämpade på din avbildning, tillsammans med sannolikhets Poäng för varje tagg. 

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "project": "00000000-0000-0000-0000-000000000000",
  "iteration": "00000000-0000-0000-0000-000000000000",
  "created": "string",
  "predictions": [
    {
      "probability": 0.0,
      "tagId": "00000000-0000-0000-0000-000000000000",
      "tagName": "string",
      "boundingBox": {
        "left": 0.0,
        "top": 0.0,
        "width": 0.0,
        "height": 0.0
      },
      "tagType": "Regular"
    }
  ]
}
```

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du gjort varje steg i bild klassificerings processen med hjälp av REST API. Det här exemplet kör en enda inlärnings upprepning, men ofta behöver du träna och testa din modell flera gånger för att göra den mer exakt.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](../../test-your-model.md)

* [Vad är Custom Vision?](../../overview.md)
* [API-referens dokumentation (utbildning)](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
* [API-referens dokumentation (förutsägelse)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)
