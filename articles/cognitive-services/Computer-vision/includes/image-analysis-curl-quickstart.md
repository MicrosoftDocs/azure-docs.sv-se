---
title: 'Snabbstart: Bildanalys REST API'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten kommer du igång med REST API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 1d20e484b46dedfc5ecae0d24b4b30205cbe32cd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799852"
---
Använd bildanalys-REST API för att:

* Analysera en bild för taggar, textbeskrivning, ansikten, vuxet innehåll med mera.
* Skapa en miniatyrbild med smart beskärning

> [!NOTE]
> Den här snabbstarten använder cURL-kommandon för att anropa REST API. Du kan också anropa REST API med ett programmeringsspråk. Se GitHub-exemplen för exempel [i C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST), [Java,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST) [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST)och [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration [– Skapa en kostnadsfritt](https://azure.microsoft.com/free/cognitive-services/) 
* När du har din Azure-prenumeration skapar du en Visuellt innehåll-resurs för att skapa Visuellt innehåll resurs i Azure Portal för att <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hämta din nyckel och "  target="_blank"> </a> slutpunkt. När den har distribuerats klickar du **på Gå till resurs**.
  * Du behöver nyckeln och slutpunkten från den resurs som du skapar för att ansluta ditt program till Visuellt innehåll tjänsten. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
  * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och senare uppgradera till en betald nivå för produktion.
* [cURL installerat](https://curl.haxx.se/)

## <a name="analyze-an-image"></a>Analysera en bild

Om du vill analysera en bild för en mängd olika visuella funktioner gör du följande:

1. Kopiera följande kommando till en textredigerare.
1. Gör följande ändringar i kommandot där det behövs:
    1. Ersätt värdet för `<subscriptionKey>` med din prenumerationsnyckel.
    1. Ersätt den första delen av url:en för begäran ( `westcentralus` ) med texten i din egen slutpunkts-URL.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Du kan också ändra bild-URL:en i begärandetexten (`http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\`) till URL:en för en annan bild som ska analyseras.
1. Öppna ett kommandotolksfönster.
1. Klistra in kommandot från textredigeraren i kommandotolkens fönster och kör sedan kommandot.

```bash
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/analyze?visualFeatures=Categories,Description&details=Landmarks" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

### <a name="examine-the-response"></a>Granska svaret

Ett svar som anger att åtgärden lyckades returneras i JSON. Exempelprogrammet parsar och visar ett lyckat svar i kommandotolkens fönster enligt följande exempel:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```


## <a name="generate-a-thumbnail"></a>Skapa en miniatyrbild

Du kan använda bildanalys för att generera en miniatyrbild med smart beskärning. Du anger önskad höjd och bredd, vilket kan skilja sig i proportion till den inmatade bilden. Bildanalys använder smart beskärning för att identifiera det intressanta området och generera koordinater för beskärning i den regionen.
 
Så här skapar du och kör exemplet:

1. Kopiera följande kommando till en textredigerare.
1. Gör följande ändringar i kommandot där det behövs:
    1. Ersätt värdet för `<subscriptionKey>` med din prenumerationsnyckel.
    1. Ersätt värdet för `<thumbnailFile>` med sökvägen och namnet på filen där du vill spara den returnerade miniatyrbilden.
    1. Ersätt den första delen av url:en för begäran ( `westcentralus` ) med texten i din egen slutpunkts-URL.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Du kan också ändra bild-URL i begärandetexten (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) till URL-adressen till en annan bild från vilken du kan skapa en miniatyrbild.
1. Öppna ett kommandotolksfönster.
1. Klistra in kommandot från textredigeraren i kommandotolkens fönster.
1. Tryck på Retur för att köra programmet.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.2/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

### <a name="examine-the-response"></a>Granska svaret

Ett lyckat svar skriver miniatyrbilden till filen som anges i `<thumbnailFile>`. Om begäran misslyckas innehåller svaret en felkod och ett meddelande som beskriver vad som gick fel. Om begäran verkar lyckas men den skapade miniatyrbilden inte är en giltig bildfil kan det vara så att din prenumerationsnyckel inte är giltig.


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du installerar grundläggande bildanalys-anrop med hjälp av REST API. Härnäst får du lära dig mer om funktionerna i Analysera API.

> [!div class="nextstepaction"]
>[Anropa API:et Analysera](../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Översikt över bildanalys](../overview-image-analysis.md)
