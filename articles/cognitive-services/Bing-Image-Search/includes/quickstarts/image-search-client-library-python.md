---
title: Snabb start för Bildsökning i Bing python-klient bibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: 338e4f0c1c47a7786e21ad98bb3e56a1505f9bb2
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104777"
---
Använd den här snabb starten för att göra din första avbildnings sökning med hjälp av Bildsökning i Bing klient biblioteket, som är en omslutning för API: et och innehåller samma funktioner. Den här enkla Python-appen skickar en bildsökningsfråga, parsar JSON-svaret och visar webbadressen till den första bild som returneras.

Käll koden för det här exemplet finns [på GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) med ytterligare fel hantering och anteckningar.

## <a name="prerequisites"></a>Förutsättningar

* [Python 2,7 eller 3.6 +](https://www.python.org/).

* [Klient biblioteket för Azure-bildsökning](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) för python
    * Installera med `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa ett nytt Python-skript i valfri IDE eller redigeringsprogram och importera följande:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Skapa variabler för din prenumerationsnyckel och sökvillkor.

    ```python
    subscription_key = "Enter your key here"
    subscription_endpoint = "Enter your endpoint here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Skapa bildsökningsklient

1. Skapa en instans av `CognitiveServicesCredentials` och skapa en instans av klienten:

    ```python
    client = ImageSearchClient(endpoint=subscription_endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```
1. Skicka en sökbegäran till API:et för bildsökning i Bing:
    ```python
    image_results = client.images.search(query=search_term)
    ```
   ## <a name="process-and-view-the-results"></a>Bearbeta och visa svaren

Parsa bild resultatet som returneras i svaret.


Om svaret innehåller sökresultat ska du lagra och skriva ut information om det första resultatet, som en webbadress till miniatyrbilden, en webbadress till originalet och det totala antalet returnerade bilder.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(
        first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie om enkel app för bildsökning i Bing](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Se även

* [Vad är bildsökning i Bing?](../../overview.md)  
* [Prova en interaktiv demo online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Python-exempel för Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Dokumentation om Azure Cognitive Services](../../../index.yml)
* [API-referens för bildsökning i Bing](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)