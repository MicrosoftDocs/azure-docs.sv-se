---
title: Skicka förfrågningar till API:et för stavningskontroll i Bing
titleSuffix: Azure Cognitive Services
description: Lär dig om lägen och inställningar för Stavningskontroll i Bing samt annan information som berör API:et.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 761cc3908b677b129e85be442b7a593a38a367f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96341571"
---
# <a name="sending-requests-to-the-bing-spell-check-api"></a>Skicka förfrågningar till API:et för stavningskontroll i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Om du vill kontrollera stavning och grammatikfel i en textsträng skickar du en GET-begäran till följande slutpunkt:  

```
https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
```  
  
Begäran måste använda HTTPS-protokollet.

Vi rekommenderar att alla förfrågningar kommer från en server. Om nyckeln distribueras som en del av ett klientprogram ökar risken för att en illvillig tredje part kan komma åt den. En server tillhandahåller dessutom en enskild uppgraderingspunkt för framtida versioner av API:et.

Begäran måste innehålla frågeparametern [text](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#text), som innehåller textsträngens som ska kontrolleras. Även om det är valfritt bör begäran även innehålla frågeparametern [mkt](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#mkt), som identifierar marknaden som du vill att resultatet ska komma från. En lista över valfria frågeparametrar som `mode` finns i [Frågeparametrar](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#query-parameters). Alla frågeparametervärden måste vara URL-kodade.  
  
Begäran måste innehålla huvudet [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#subscriptionkey). Även om det är valfritt rekommenderar vi att du även anger följande huvuden. Dessa huvuden hjälper API för stavningskontroll i Bing att returnera mer noggranna resultat:  
  
-   [User-Agent](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#location)  

En lista över alla sidhuvuden för begäranden och svar finns i [Sidhuvuden](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#headers).

Vid anrop till API för stavningskontroll i Bing via JavaScript kan det hända att webbläsarens inbyggda säkerhetsfunktioner förhindrar dig att komma åt värdena för de här rubrikerna.

Du kan lösa det här problemet genom att göra begäran för API för stavningskontroll i Bing via en CORS-proxy. Svaret från en sådan proxy har ett `Access-Control-Expose-Headers` huvud som filtrerar svarshuvuden och gör dem tillgängliga för Java Script.

Det är enkelt att installera en CORS-proxy för att tillåta att [självstudieappen](../tutorials/spellcheck.md) får åtkomst till valfria klientrubriker. [Installera Node.js](https://nodejs.org/en/download/) om du inte redan har det. Ange sedan följande kommando i en kommandotolk.

```console
npm install -g cors-proxy-server
```

Ändra sedan API för stavningskontroll i Bing-slutpunkten i HTML-filen till: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/`

Slutligen startar du CORS-proxyn med följande kommando:

```console
cors-proxy-server
```

Lämna kommandofönstret öppet medan du använder självstudieappen. Om du stänger fönstret stoppas proxyn. I det expanderbara avsnittet om HTTP-rubriker nedanför sökresultatet kan du nu se `X-MSEdge-ClientID`-rubriken (bland andra) och kontrollera att den är likadan för varje begäran.

## <a name="example-api-request"></a>Exempel på API-begäran

Nedan visas en begäran som innehåller alla föreslagna frågeparametrar och sidhuvuden. Om det är den första gången du anropar ett Bing-API inkluderar du inte klientens ID-huvud. Inkludera endast klient-ID om du har anropat ett Bing-API förut och om Bing returnerade ett klient-ID för användar- och enhetskombinationen. 
  
```http
GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

Nedan visas svaret på den tidigare begäran. Exemplet visar också Bing-specifika svarshuvuden.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  

## <a name="next-steps"></a>Nästa steg

- [Vad är API för stavningskontroll i Bing?](../overview.md)
- [API-referens för stavningskontroll i Bing v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)