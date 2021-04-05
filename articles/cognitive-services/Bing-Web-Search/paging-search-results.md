---
title: Så här bläddrar du igenom Sök Resultat – API:er för Bing-sökresultat
titleSuffix: Azure Cognitive Services
description: Lär dig hur du bläddrar genom Sök Resultat från API:er för Bing-sökresultat.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: 670460759a9495de735da35ae9f3d8388e59e0e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96350627"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Så här bläddrar du genom resultat från API:er för Bing-sökresultat

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

När du skickar ett anrop till webb-, anpassade-, bild-, diskussions-eller Videosökning-API: er för Bing returnerar Bing en delmängd av det totala antalet resultat som kan vara relevanta för frågan. Om du vill få det uppskattade totala antalet tillgängliga resultat kan du komma åt svars objektets `totalEstimatedMatches` fält. 

Exempel: 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>Växling genom Sök Resultat

Om du vill bläddra igenom de tillgängliga resultaten använder du `count` parametrarna och för att `offset` skicka din begäran.  

> [!NOTE]
>
> * Sid indelning med Bing Video-, bild-och diskussions grupps-API: erna gäller bara för allmänna video ( `/video/search` ), Nyheter ( `/news/search` ) och bild ( `/image/search` )-sökningar. Det går inte att växla mellan olika trender och kategorier.  
> * `TotalEstimatedMatches`Fältet är en uppskattning av det totala antalet Sök Resultat för den aktuella frågan. När du ställer in `count` `offset` parametrarna och kan den här uppskattningen ändras.

| Parameter | Beskrivning                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Anger antalet resultat som ska returneras i svaret. Observera att standardvärdet för `count` och det maximala antalet resultat som kan begäras varierar beroende på API. Du hittar dessa värden i referens dokumentationen under [Nästa steg](#next-steps). |
| `offset`  | Anger antalet resultat som ska hoppas över. `offset`Är noll-baserat och måste vara mindre än ( `totalEstimatedMatches`  -  `count` ).                                           |

Om du till exempel vill visa 15 resultat per sida, anger du `count` till 15 och `offset` 0 för att få den första resultat sidan. För varje efterföljande API-anrop skulle du öka `offset` med 15. I följande exempel begärs 15 webb sidor som börjar vid förskjutningen 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Om du använder `count` standardvärdet behöver du bara ange `offset` frågeparametern i dina API-anrop.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

När du använder Bing-avbildningen och video-API: erna kan du använda `nextOffset` värdet för att undvika dubbla Sök resultat. Hämta värdet från `Images` `Videos` -eller-svar-objekten och Använd det i dina begär Anden med `offset` parametern.  

> [!NOTE]
> API för webbsökning i Bing returnerar Sök resultat som kan innehålla webb sidor, bilder, videor och nyheter. När du bläddrar genom Sök resultatet från API för webbsökning i Bing, är du bara att växla [webb sidor](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)och inte andra svars typer, till exempel bilder eller nyheter. Sök resultat i `WebPage` objekt kan innehålla resultat som även visas i andra svars typer.
>
> Om du använder `responseFilter` Frågeparametern utan att ange några filter värden ska du inte använda `count` parametrarna och `offset` . 

## <a name="next-steps"></a>Nästa steg

* [Vad är Webbsökning i Bing API: er?](bing-api-comparison.md)
* [Referens för API för webbsökning i Bing v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [API för anpassad Bing-sökning v7-referens](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [API för nyhetssökning i Bing v7-referens](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [API för videosökning i Bing v7-referens](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [API för bildsökning i Bing v7-referens](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)