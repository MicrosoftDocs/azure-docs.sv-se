---
title: Hämta avbildningar från webb API för bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Använd API för bildsökning i Bing för att söka efter och hämta relevanta avbildningar från webben.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 988a1332d03bf2c9563ab0576f7a20ee6b0615aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96342064"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Hämta bilder från webben med API för bildsökning i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

När du använder Bildsökning i Bing REST API kan du hämta avbildningar från webben som är relaterade till Sök termen genom att skicka följande GET-begäran:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Använd fråge parametern [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) för URL-kodad sökterm. Om du till exempel anger *seglings dinghies* anger `q` du till `sailing+dinghies` eller `sailing%20dinghies` .

> [!IMPORTANT]
> * Alla begär Anden måste göras från en server och inte från en klient.
> * Om det är första gången du anropar någon av Bing Search-API: erna ska du inte ta med klient-ID-huvudet. Ta bara med klient-ID om du tidigare har anropat ett Bing-API som returnerade ett klient-ID för användar-och enhets kombinationen.

## <a name="get-images-from-a-specific-web-domain"></a>Hämta avbildningar från en speciell webb domän

Om du vill hämta bilder från en specifik domän använder du frågeoperatorn [site:](/previous-versions/bing/search/ff795613(v=msdn.10)).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Svar på frågor som använder- `site:` operatorn kan innehålla innehåll som är vuxna oavsett [safeSearch](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) -inställningen. Använd endast `site:` om du är medveten om innehållet i domänen.

## <a name="filter-images"></a>Filtrera bilder

 Som standard returnerar Bildsökning-API alla avbildningar som är relevanta för frågan. Om du vill filtrera bilderna som Bing returnerar (till exempel om du bara vill returnera bilder med en genomskinlig bakgrund eller en angiven storlek) använder du följande frågeparametrar:

* [aspekt](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect)– filtrera bilder efter proportioner (till exempel standard-eller wide screen-bilder).
* [färg](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color)– filtrera bilder efter dominerande färger eller svart och vitt.
* [aktualitet](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness)– filtrera avbildningar efter ålder (till exempel bilder som upptäckts av Bing den senaste veckan).
* [höjd](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height), [Bredd](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width)– filtrera bilder efter bredd och höjd.
* [imageContent](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent)– filtrera bilder efter innehåll (till exempel bilder som bara visar en persons ansikte).
* [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)– filtrera bilder efter typ (till exempel ClipArt, animerade GIF-filer eller genomskinliga bakgrunder).
* [licens](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license)– filtrera avbildningar efter den typ av licens som är kopplad till platsen.
* [storlek](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size)– filtrera bilder efter storlek, till exempel små bilder upp till 200x200 bild punkter.

Om du vill hämta bilder från en specifik domän använder du frågeoperatorn [site:](/previous-versions/bing/search/ff795613(v=msdn.10)).

I följande exempel visas hur du får små bilder från ContosoSailing.com som Bing identifierade under den senaste veckan.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Bildsökning i Bing svars format

Svarsmeddelandet från Bing innehåller ett [bild](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) svar som innehåller en lista med bilder som Cognitive Services visat sig vara relevanta för frågan. Varje [avbildnings](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) objekt i listan innehåller följande information om avbildningen: URL, storlek, dess dimensioner, dess kodnings format, en URL till en miniatyr bild av bilden och miniatyrens dimensioner.

> [!NOTE]
> * Bilderna måste visas i den ordning som anges i svaret.
> * Eftersom URL-format och parametrar kan ändras utan föregående meddelande, använder du alla URL: er som de är. Du bör inte ta beroenden i URL-formatet eller parametrar förutom där det anges.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

När du anropar API för bildsökning i Bing returnerar Bing en lista med resultat. Listan är en delmängd av det totala antalet resultat som är relevanta för frågan. Svarets fält `totalEstimatedMatches` innehåller en uppskattning av det antal bilder som är tillgängliga för visning. Mer information om hur du bläddrar igenom resten av bilderna finns i [sid växlings bilder](../../bing-web-search/paging-search-results.md).

## <a name="next-steps"></a>Nästa steg

Om du inte har provat API för bildsökning i Bing tidigare försöker du med en [snabb start](../quickstarts/csharp.md). Om du vill ha något mer komplext kan du testa självstudien för att skapa en webbapp med en [enda sida](../tutorial-bing-image-search-single-page-app.md).