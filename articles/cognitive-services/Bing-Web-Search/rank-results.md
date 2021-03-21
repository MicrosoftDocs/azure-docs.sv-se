---
title: Så här använder du rangordningar för att Visa Sök Resultat – API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder rangordning för att Visa Sök Resultat från API för webbsökning i Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 20501d0993cc4566a79d6e916d801911606bea35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94380457"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Så här använder du rangordning för att Visa API för webbsökning i Bing resultat  

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Varje Sök svar innehåller ett [RankingResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse) -svar som anger hur du måste visa Sök resultaten. Ranknings svars grupper resulterar i Mainline innehåll och sid List innehåll för en traditionell Sök resultat sida. Om du inte visar resultatet i ett traditionellt Mainline-och sidofält-format måste du ange Mainline-innehållet som är större än innehållet i innehålls sidan.  

I varje grupp (Mainline eller sid panelen) identifierar [objekt](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items) mat ris ordningen som innehållet måste visas i. Varje objekt har följande två sätt att identifiera resultatet inom ett svar.  

-   `answerType` och `resultIndex` – `answerType` fältet identifierar svaret (till exempel webb sida eller nyheter) och `resultIndex` identifierar ett resultat inom svaret (till exempel en nyhets artikel). Indexet är noll baserat.  

-   `value` – `value` Fältet innehåller ett ID som matchar ID: t för antingen ett svar eller ett resultat inom svaret. Antingen svaret eller resultatet innehåller ID: t, men inte båda.  

Att använda ID är enklare att använda eftersom du bara behöver matcha rangordnings-ID: t med ID: t för ett svar eller ett av dess resultat. Om ett svars objekt innehåller ett `id` fält visas alla svars resultat tillsammans. Om objektet till exempel `News` innehåller `id` fältet, visar du alla nyhets artiklar. Om `News` objektet inte innehåller `id` fältet innehåller varje nyhets artikel ett `id` fält och ranknings svaret blandar nyhets artiklarna med resultaten från andra svar.  

Att använda `answerType` och `resultIndex` är lite mer komplicerat. Du använder `answerType` för att identifiera svaret som innehåller de resultat som ska visas. Sedan använder du `resultIndex` för att indexera genom svarets resultat för att få resultatet att visas. ( `answerType` Värdet är namnet på fältet i [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) -objektet.) Om du vill visa alla svars resultat tillsammans innehåller inte rangordnings svars posten `resultIndex` fältet.  

## <a name="ranking-response-example"></a>Exempel på ranknings svar

Nedan visas ett exempel på en [RankingResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse). Eftersom webbsvaret inte innehåller något `id` fält visar du alla webb sidor individuellt baserat på rangordningen (varje webb sida innehåller ett `id` fält). Eftersom bilder, videor och relaterade sökningar i sökningar inkluderar `id` fältet, visar du resultatet för var och en av dessa svar baserat på rangordningen.

```json
{  
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
        "totalEstimatedMatches" : 835000,
        "value" : [
            {
                "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
                "name" : "Motor Sports - Live at the race track ...",
                "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                "displayUrl" : "www.contoso.com\/usa\/eventsandracing\/motorsport",
                "snippet" : "Here you will find detailed information about racing...",
                "deepLinks" : [{
                    "name" : "Customer Racing",
                    "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                    "snippet" : "Customer racing news; General news..."
            },
            . . .  
        ]  
    }],  
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "2016 Supercar Wallpapers",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2017-03-25T11:14:00",
                "contentUrl" : "http:\/\/www.contoso.com\/wall...",
                "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "contentSize" : "373283 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "http:\/\/www.contoso.com\/lmp-...",
                "width" : 1920,
                "height" : 1080,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "insightsSourcesSummary" : {
                    "shoppingSourcesCount" : 0,
                    "recipeSourcesCount" : 0
                }
            },
            . . .  
        ]  
    },  
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [
            {
                "text" : "vintage racing teams",
                "displayText" : "vintage racing teams",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2..."
            },
            . . .  
        ]  
    },  
    "videos" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/videos...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "Why We Race",
                "description" : "A new era begins in motorsports this weekend...",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2...",
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.Vo1...",
                "datePublished" : "2014-01-25T16:31:48",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=oL...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "encodingFormat" : "mp4",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=oLAZgD...",
                "width" : 480,
                "height" : 360,
                "duration" : "PT2M42S",
                "motionThumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OM...",
                "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www.you...<\/iframe>",
                "allowHttpsEmbed" : true,
                "viewCount" : 47325,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "allowMobileEmbed" : true,
                "isSuperfresh" : false
            },
            . . .  
        ]  
    },  
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "Videos",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}  
```  

Baserat på detta ranknings svar skulle Mainline Visa följande Sök Resultat:  

-   Den första webb sidans resultat
-   Alla bilder  
-   Den andra och tredje webb sidans resultat  
-   Alla videor  
-   Resultat från den fjärde, femte och sjätte webb sidan  

Och sid panelen visar följande Sök Resultat:  

-   Alla relaterade sökningar  


## <a name="next-steps"></a>Nästa steg

Information om hur du befordrar resultat finns i [befordra svar som inte rangordnas](./filter-answers.md#promoting-answers-that-are-not-ranked).

Information om hur du begränsar antalet rankade svar i svaret finns i [begränsa antalet svar i svaret](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

För ett C#-exempel som använder rangordning för att visa resultat, se [vägledning för C#-rankning](./csharp-ranking-tutorial.md).