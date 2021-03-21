---
title: Så här filtrerar du Sök Resultat – API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: Du kan filtrera de typer av svar som Bing inkluderar i svaret (till exempel bilder, videor och nyheter) med hjälp av Frågeparametern ' responseFilter '.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: 571314009b6f58e5c2ab6aac02cfebc82c53f42f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96351869"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrera svaren som Sök svaret innehåller  

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

När du frågar på webben, returnerar Bing allt relevant innehåll som hittas för sökningen. Om Sök frågan till exempel är "segling + dinghies" kan svaret innehålla följande svar:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

## <a name="query-parameters"></a>Frågeparametrar

Om du vill filtrera svaren som returneras av Bing använder du nedanstående frågeparametrar när du anropar API: et.  

### <a name="responsefilter"></a>ResponseFilter

Du kan filtrera de typer av svar som Bing inkluderar i svaret (till exempel bilder, videor och nyheter) med hjälp av [responseFilter](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) -Frågeparametern, som är en kommaavgränsad lista med svar. Ett svar tas med i svaret om Bing hittar relevant innehåll för det. 

Om du vill utesluta vissa svar från svaret, till exempel bilder, lägga ett meddelande `-` till svars typen. Exempel:

```
&responseFilter=-images,-videos
```

Följande visar hur du kan använda `responseFilter` för att begära bilder, videor och nyheter från segling dinghies. När du kodar frågesträngen ändras kommatecken till% 2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Nedan visas svaret på den tidigare frågan. Eftersom Bing inte hittade relevanta video-och nyhets resultat, ingår inte svaret.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

Även om Bing inte returnerade video-och nyhets resultat i föregående svar, innebär det inte att video-och nyhets innehållet inte finns. Det innebär bara att sidan inte innehåller dem. Men [om du går](./paging-search-results.md) igenom fler resultat kommer de efterföljande sidorna förmodligen att innehålla dem. Om du anropar Videosökning- [API: et](../bing-video-search/overview.md) och nyhetssökning slut punkter för [API: er](../bing-news-search/search-the-web.md) direkt, skulle svaret förmodligen innehålla resultat.

Du rekommenderas inte `responseFilter` att använda för att få resultat från ett enda API. Om du vill ha innehåll från ett enda Bing API anropar du detta API direkt. Om du till exempel bara vill ta emot bilder skickar du en begäran till Bildsökning API-slutpunkten `https://api.cognitive.microsoft.com/bing/v7.0/images/search` eller någon av de andra slut punkterna för [avbildningar](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) . Anrop till det enskilda API: t är viktigt inte bara av prestanda skäl, men eftersom de innehållsbaserade API: erna erbjuder fler resultat. Du kan till exempel använda filter som inte är tillgängliga för Webbsökning API för att filtrera resultaten.  

### <a name="site"></a>Webbplats

Om du vill få Sök Resultat från en speciell domän inkluderar du `site:` frågeparametern i frågesträngen.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> Beroende på frågan, om du använder `site:` fråga-operatorn, finns det risk för att svaret kan innehålla innehåll som är olämpligt för barn oberoende av [safeSearch](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) -inställningen. Du bör endast använda `site:` om du är medveten om innehållet på webbplatsen och ditt scenario tillåter möjligheten att det förekommer innehåll som är olämpligt för barn.

### <a name="freshness"></a>Aktualitet

Om du vill begränsa webb svars resultatet till webb sidor som Bing identifierade under en viss period, ställer du in Frågeparametern för [uppdatering](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) till något av följande Skift läges okänsliga värden:

* `Day` – Returnera webb sidor som Bing har identifierat under de senaste 24 timmarna
* `Week` – Returnera webb sidor som Bing har identifierat under de senaste 7 dagarna
* `Month` – Returnera webb sidor som har identifierats under de senaste 30 dagarna

Du kan också ange den här parametern till ett anpassat datum intervall i formuläret `YYYY-MM-DD..YYYY-MM-DD` . 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Om du vill begränsa resultatet till ett enda datum anger du uppdaterings parametern till ett visst datum:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Resultaten kan vara webb sidor som ligger utanför den angivna perioden om antalet webb sidor som Bing matchar dina filter villkor är mindre än antalet webb sidor som du har begärt (eller det standard nummer som Bing returnerar).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Begränsa antalet svar i svaret

Bing kan returnera flera svars typer i JSON-svaret. Om du till exempel frågar *uppsegling + dinghies* kan Bing returnera `webpages` , `images` , `videos` , och `relatedSearches` .

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Om du vill begränsa antalet svar som Bing returnerar till de två översta svaren (webb sidor och bilder) anger du [answerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) till 2.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Svaret innehåller bara `webPages` och `images` .

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

Om du lägger till `responseFilter` frågeparametern i den föregående frågan och anger den till webb sidor och nyheter, innehåller svaret endast webb sidor eftersom nyheterna inte rangordnas.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>Befordra svar som inte rangordnas

Om de mest rankade svaren som Bing returnerar för en fråga är webb sidor, bilder, videor och relatedSearches, innehåller svaret dessa svar. Om du ställer in [answerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) på två (2) returnerar Bing de två främsta rankade svaren: webb sidor och bilder. Om du vill att Bing ska inkludera bilder och videor i svaret anger du parametern [befordra](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) fråga och anger den till bilder och videor.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Följande är svaret på ovanstående begäran. Bing returnerar de två vanligaste svaren, webb sidorna och bilderna och höjer videor till svaret.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Om du anger `promote` nyhets brev innehåller svaret inte nyhets svaret eftersom det inte är ett Rankat svar &mdash; . du kan bara befordra de rankade svaren.

Svaren som du vill befordra räknas inte mot `answerCount` gränsen. Om till exempel de rankade svaren är nyheter, bilder och videor och du anger `answerCount` till 1 och `promote` till nyheter, innehåller svaret nyheter och bilder. Eller, om de rankade svaren är videor, bilder och nyheter, innehåller svaret videor och nyheter.

Du får `promote` bara använda om du anger `answerCount` Frågeparametern.