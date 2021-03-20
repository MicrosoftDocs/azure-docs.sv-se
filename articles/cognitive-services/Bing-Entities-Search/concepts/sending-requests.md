---
title: Skicka sökbegäranden till API:et för entitetssökning i Bing
titleSuffix: Azure cognitive Services
description: API:et för entitetsökning i Bing skickar en sökfråga till Bing och hämtar resultat som innehåller entiteter och platser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: ad6d89fec9f2c94129e19c09ee3e1e76d5bb6e44
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353280"
---
# <a name="sending-search-requests-to-the-bing-entity-search-api"></a>Skicka sökbegäranden till API:et för entitetssökning i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

API:et för entitetsökning i Bing skickar en sökfråga till Bing och hämtar resultat som innehåller entiteter och platser. Platsresultat omfattar restauranger, hotell eller andra lokala företag. För platser kan frågan specificera namnet på det lokala företaget eller fråga efter en lista (exempelvis restauranger i närheten). Entitetsresultat omfattar personer, platser eller saker. Plats i det här sammanhanget är turist Attractions, stater, länder/regioner osv.

## <a name="the-endpoint"></a>Slutpunkten

För att få sökresultat för entitet och plats skickar du en GET-begäran till följande slutpunkt:  

```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Begäranden måste använda HTTPS-protokollet.

Vi rekommenderar att alla förfrågningar kommer från en server. Om nyckeln distribueras som en del av ett klientprogram ökar risken för att en illvillig tredje part kan komma åt den. Anrop från en server innebär dessutom att det bara finns en enda uppgraderingspunkt för framtida versioner av API:et.

## <a name="specifying-query-parameters-and-headers"></a>Specificera frågeparametrar och huvuden

Begäran måste innehålla frågeparametern [q](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query), som innehåller användarens sökterm. Begäran måste även specificera frågeparametern [mkt](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mkt), som identifierar den marknad som du vill att resultatet ska komma från. En lista över valfria frågeparametrar finns i [Frågeparametrar](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters). URL-koda alla frågeparametrar.  
  
Begäran måste innehålla huvudet [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#subscriptionkey). Även om det är valfritt rekommenderar vi även att följande huvuden finns med:  
  
-   [User-Agent](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#location)  

Sidhuvuden för klientens IP-adress och platsen är viktiga för att returnera platsmedvetet innehåll.  

En lista över alla sidhuvuden för begäranden och svar finns i [Sidhuvuden](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers).

## <a name="the-request"></a>Begäran

Nedan visas en entitetsbegäran som innehåller alla föreslagna frågeparametrar och huvuden. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Om det är den första gången du anropar ett Bing-API inkluderar du inte klientens ID-huvud. Inkludera endast klient-ID om du har anropat ett Bing-API förut och om Bing returnerade ett klient-ID för användar- och enhetskombinationen.

## <a name="the-response"></a>Svaret

Nedan visas svaret på den tidigare begäran. Exemplet visar också Bing-specifika svarshuvuden. Mer information om svarsobjektet finns i [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse).

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```


## <a name="next-steps"></a>Nästa steg

* [Söka efter entiteter med entitets-API:t i Bing](search-for-entities.md)
* [Användnings- och visningskrav för Bing-API](../../bing-web-search/use-display-requirements.md)