---
title: Sök efter videor med hjälp av API för videosökning i Bing
titleSuffix: Azure Cognitive Services
description: Videosökning i Bing APIfinds och returnerar relevanta videor från webben innehåller flera funktioner för intelligent och fokuserad video hämtning på webben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 10277efe1f06de3633b2d614e2ee5ec0cc351c76
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96351937"
---
# <a name="search-for-videos-with-the-bing-video-search-api"></a>Sök efter videor med API för videosökning i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Med API för videosökning i Bing är det enkelt att integrera Bings funktioner för kognitiv nyhetssökning i dina program. API:et hittar och returnerar i första hand relevanta videor på webben, men har även flera funktioner för intelligent och fokuserad videosökning på webben.

## <a name="getting-videos"></a>Hämta videor

För att hämta videor relaterade till användarens söktermer från webben skickar du följande GET-förfrågan:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Alla begäranden måste ske från en server.

Om det är den första gången du anropar ett Bing-API inkluderar du inte klientens ID-huvud. Inkludera endast klient-ID om du har anropat ett Bing-API förut och om Bing returnerade ett klient-ID för användar- och enhetskombinationen.

Om du vill hämta videor från en specifik domän använder du frågeoperatorn [site:](/previous-versions/bing/search/ff795613(v=msdn.10)).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Svaret innehåller ett [Videos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos)-svar som innehåller en lista med videor som Bing anser vara relevanta för frågan. Varje [Video](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video)-objekt i listan innehåller videons webbadress, längd, storlek, kodningsformat och andra attribut. Videoobjektet innehåller även webbadressen till en miniatyrbild av videon och miniatyrbildens mått.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

## <a name="video-thumbnails"></a>Videominiatyrer

Du kan visa alla eller en delmängd av videominiatyrerna som returneras av API för videosökning i Bing. Om du visar en delmängd bör du ge användaren möjlighet att visa återstående videor. som en del av [användnings- och visningskraven](../../bing-web-search/use-display-requirements.md) för Bing API måste du visa videorna i den ordning som anges i svaret. Information om att ändra storlek på miniatyrbilden finns i [Resizing and Cropping Thumbnails](../../bing-web-search/resize-and-crop-thumbnails.md) (Ändra storlek på och beskär miniatyrbilder). 

När användaren för muspekaren över miniatyrbilden kan du använda [motionThumbnailUrl](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-motionthumbnailurl) till att spela upp en miniatyrversion av videon. Se till att du tillskriver ursprunget när du visar en video.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

När du klickar på en miniatyrbild finns det tre alternativ för att visa videon:

- Använd [hostPageUrl](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-hostpageurl) till att visa videon på ursprungswebbplatsen (till exempel YouTube)
- Använd [webSearchUrl](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-websearchurl) till att visa videon i Bing-spelaren
- Använd [embdedHtml](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-embedhtml) till att bädda in videon i en egen upplevelse 

Glöm inte att ange videons utgivare och upphovsman när du spelar upp den.

Mer information om hur du använder [videoId](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-videoid) till att få insikter om videon finns i [Videoinsikter](../video-insights.md).

## <a name="filtering-videos"></a>Filtrera videor

Som standard returnerar API för videosökning i Bing alla videor som är relevanta för frågan. Om du bara vill visa kostnadsfria videor eller klipp som är kortare än fem minuter skulle du använda följande filterparametrar i frågan:

- [priser](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#pricing) &mdash; Filtrera videor efter prissättning (till exempel videor som är kostnads fria eller som du måste betala för)
- [lösning](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#resolution) &mdash; Filtrera videor efter upplösning (till exempel video med en 720p eller högre upplösning)
- [videoLength](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videolength) &mdash; Filtrera videor efter video längd (till exempel videor som är mindre än fem minuter långt)
- [aktualitet](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#freshness) &mdash; Filtrera videor efter ålder (till exempel videor som upptäckts av Bing den senaste veckan)

Om du vill hämta videor från en specifik domän tar du med frågeoperatorn [site:](/previous-versions/bing/search/ff795613(v=msdn.10)) i frågesträngen.

> [!NOTE]
> Beroende på frågan finns det en risk när du använder frågeoperatorn `site:` att svaret har innehåll som är olämpligt för barn, oberoende av inställningen för [safeSearch](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#safesearch). Du bör endast använda `site:` om du är medveten om innehållet på webbplatsen och ditt scenario tillåter möjligheten att det förekommer innehåll som är olämpligt för barn.

I följande exempel visas hur du hämtar kostnadsfria videor från ContosoSailing.com med upplösningen 720p eller högre och som Bing har identifierat den senaste månaden.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>Expandera frågan

Om Bing kan expandera frågan för att begränsa den ursprungliga sökningen innehåller objektet [Videos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) fältet `queryExpansions`. Om frågan till exempel var *Cleaning Gutters* (rensa stuprännor) kan de expanderade frågorna vara Cleaning Gutters **Tools** (verktyg), Cleaning Gutters **From the Ground** (från marken), Gutter Gleaning **Machine** (maskin) och **Easy** Gutter Cleaning.

Här ser du de expanderade frågorna för *Cleaning Gutters*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

Fältet `queryExpansions` innehåller en lista över [Query](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj)-objekt. Fältet `text` innehåller den expanderade frågan, och fältet `displayText` innehåller expansionstermen. Du kan använda text- och miniatyrfälten till att visa de expanderade frågesträngarna för användaren om en expanderad frågesträng är det användaren egentligen letar efter. Gör miniatyrbilden och texten klickbara genom att använda URL:en `webSearchUrl` eller URL:en `searchLink`. Använd `webSearchUrl` för att skicka användaren till Bing-sökresultat eller `searchLink` om du tillhandahåller din egen resultatsida.

## <a name="pivoting-the-query"></a>Pivotera frågan

Om Bing kan segmentera den ursprungliga sökfrågan innehåller objektet [Videos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) fältet `pivotSuggestions`. Om den ursprungliga frågan till exempel var *Cleaning Gutters* kan Bing segmentera frågan till *Cleaning* och *Gutters*.

Här är ett exempel på pivotförslagen för *Cleaning Gutters*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

För varje pivot innehåller svaret en lista över [Query](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj)-objekt som innehåller föreslagna frågor. Fältet `text` innehåller den föreslagna frågan, och fältet `displayText` innehåller den term som ersätter pivoten i den ursprungliga frågan. Till exempel Window Cleaning (fönsterputsning).

Du kan använda fälten `text` och `thumbnail` för att visa användaren de expanderade frågesträngarna om den expanderade frågesträngen är det användaren letar efter. Gör miniatyrbilden och texten klickbara genom att använda URL:en `webSearchUrl` eller URL:en `searchLink`. Använd `webSearchUrl` för att skicka användaren till Bing-sökresultat eller `searchLink` om du tillhandahåller din egen resultatsida.

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]