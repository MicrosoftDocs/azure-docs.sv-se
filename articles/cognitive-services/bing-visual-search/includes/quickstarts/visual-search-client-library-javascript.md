---
title: Snabb start för Visuell sökning i Bing Java Script Client library
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: bd4f806013d92dee98bc16c67739748823804bbb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948175"
---
Använd den här snabb starten för att börja hämta bild insikter från den Visuell sökning i Bing tjänsten med hjälp av JavaScript-klientcertifikatet. Även om Visuell sökning i Bing har en REST API som är kompatibel med de flesta programmeringsspråk, är klient biblioteket ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js). 

[Referens dokumentation](/javascript/api/@azure/cognitiveservices-visualsearch/)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-visualsearch)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-visualsearch)  |  [Exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>Förutsättningar

* Den senaste versionen av [Node.js](https://nodejs.org/en/download/).
* [Visuell sökning I Bing SDK för Java Script](https://www.npmjs.com/package/@azure/cognitiveservices-visualsearch)
     *  Installera genom att köra `npm install @azure/cognitiveservices-visualsearch`
* `CognitiveServicesCredentials`Klassen från `@azure/ms-rest-azure-js` paketet för att autentisera klienten.
     * Installera genom att köra `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny JavaScript-fil i valfri IDE eller redigeringsprogram och lägg till följande krav. Skapa sedan variabler för din prenumerationsnyckel, ID för anpassad konfiguration och filsökväg för den bild du vill ladda upp. 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('@azure/cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. Instantiera klienten.

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>Sök efter bilder

1. Använd `fs.createReadStream()` för att läsa in bildfilen och skapa variabler för sökbegäran och resultat. Använd sedan klienten till att söka efter bilder.

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchClient.images.visualSearch({
            image: fileStream,
            knowledgeRequest: visualSearchRequest
        });
        console.log("Search visual search request with binary of image");
    } catch (err) {
        console.log("Encountered exception. " + err.message);
    }
    ```

2. Parsa resultaten från den föregående frågan:

    ```javascript
    // Visual Search results
    if (visualSearchResults.image.imageInsightsToken) {
        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
    }
    else {
        console.log("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.tags.length > 0) {
        let firstTagResult = visualSearchResults.tags[0];
        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);
    
        // List of actions in first tag
        if (firstTagResult.actions.length > 0) {
            let firstActionResult = firstTagResult.actions[0];
            console.log(`First tag action count: ${firstTagResult.actions.length}`);
            console.log(`First tag action type: ${firstActionResult.actionType}`);
        }
        else {
            console.log("Couldn't find tag actions!");
        }
    
    }
    else {
        console.log("Couldn't find image tags!");
    }
    
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../../tutorial-bing-visual-search-single-page-app.md)
