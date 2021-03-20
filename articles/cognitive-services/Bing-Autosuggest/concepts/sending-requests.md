---
title: Skickar begär anden till API för automatiska förslag i Bing
titleSuffix: Azure Cognitive Services
description: API för automatiska förslag för Bing returnerar en lista med föreslagna frågor som baseras på den partiella frågesträngen i sökrutan. Läs mer om att skicka begär Anden.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: dd845c0fb877afa76b84eb5c2d86392f763eccf7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353399"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Skickar begär anden till API för automatiska förslag i Bing.

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Om programmet skickar frågor till något av API:erna för Bing-sökning, kan du använda API för automatiska förslag för Bing till att förbättra användarnas sökupplevelse. API för automatiska förslag för Bing returnerar en lista med föreslagna frågor som baseras på den partiella frågesträngen i sökrutan. När du har angett tecken i en sökruta i programmet kan du Visa förslag i en nedrullningsbar listruta. Använd den här artikeln om du vill veta mer om att skicka begär anden till detta API. 

## <a name="bing-autosuggest-api-endpoint"></a>API för automatiska förslag i Bing slut punkt

**API för automatiska förslag i Bing** innehåller en slut punkt som returnerar en lista över föreslagna frågor från en delvis sökterm.

Skicka en `GET` begäran till följande slut punkt för att få förslag på frågor med Bing-API: et. Använd sidhuvudena och URL-parametrarna för att definiera ytterligare specifikationer.

**Slut punkt:** Returnerar Sök förslag som JSON-resultat som är relevanta för användarens indata som definieras av `?q=""` .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Mer information om sidhuvuden, parametrar, marknads koder, svars objekt, fel osv. finns i referens för [API för automatiska förslag i Bing v7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) .

API: erna för **Bing** stöder Sök åtgärder som returnerar resultat enligt deras typ. Alla Sök slut punkter returnerar resultat som JSON-svars objekt.
Alla slut punkter har stöd för frågor som returnerar ett särskilt språk och/eller plats efter longitud, latitud och Sök-radie.

Fullständig information om de parametrar som stöds av varje slut punkt finns i referens sidorna för varje typ.
Exempel på grundläggande begär Anden som använder automatiska förslag finns i [snabb starter för automatiska](/azure/cognitive-services/Bing-Autosuggest)förslag.

## <a name="bing-autosuggest-api-requests"></a>API för automatiska förslag i Bing begär Anden

> [!NOTE]
> * Begär anden till API för automatiska förslag i Bing måste använda HTTPS-protokollet.

Vi rekommenderar att alla förfrågningar kommer från en server. Om du distribuerar nyckeln som en del av ett klient program får du fler möjligheter till skadlig åtkomst från tredje part. Dessutom ger anrop från en server en enda uppgraderings punkt för framtida uppdateringar.

Begäran måste ange frågeparametern [q](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query), som innehåller användarens partiella sökterm. Även om det är valfritt bör begäran även innehålla frågeparametern [mkt](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt), som identifierar marknaden som du vill att resultatet ska komma från. En lista över valfria frågeparametrar finns i [Frågeparametrar](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Alla frågeparametervärden måste vara URL-kodade.

Begäran måste innehålla huvudet [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey). Även om det är valfritt rekommenderar vi även att följande huvuden finns med:

- [User-Agent](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Sidhuvuden för klientens IP-adress och platsen är viktiga för att returnera platsmedvetet innehåll.

En lista över alla sidhuvuden för begäranden och svar finns i [Sidhuvuden](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

> [!NOTE]
> När du anropar API för automatiska förslag i Bing från java script kan webbläsarens inbyggda säkerhetsfunktioner förhindra åtkomst till värdena i dessa huvuden.

Du kan lösa detta genom att göra API för automatiska förslag i Bing begäran via en CORS-proxy. Svaret från en sådan proxy har ett `Access-Control-Expose-Headers` huvud som filtrerar svarshuvuden och gör dem tillgängliga för Java Script.

Det är enkelt att installera en CORS-proxy så att vår [självstudie](../tutorials/autosuggest.md) kan komma åt de valfria klient rubrikerna. [Installera Node.js](https://nodejs.org/en/download/) om du inte redan har det. Ange sedan följande kommando i en kommandotolk.

```console
npm install -g cors-proxy-server
```

Ändra sedan API för automatiska förslag i Bing slut punkten i HTML-filen till:

```http
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
```

Slutligen startar du CORS-proxyn med följande kommando:

```console
cors-proxy-server
```

Lämna kommandofönstret öppet medan du använder självstudieappen. Om du stänger fönstret stoppas proxyn. I det expanderbara avsnittet om HTTP-huvuden nedan kan du nu se `X-MSEdge-ClientID`-huvudet (bland annat) under sökresultatet och du kan kontrollera att det är samma för varje begäran.

Förfrågningar ska innehålla alla föreslagna frågeparametrar och sidhuvuden. 

I följande exempel visas en begäran som returnerar de föreslagna frågesträngarna för *sail* (segla).

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Om det är den första gången du anropar ett Bing-API inkluderar du inte klientens ID-huvud. Inkludera endast klientens ID-huvud om du har anropat ett Bing-API förut och om Bing returnerade ett klient-ID för användar- och enhetskombinationen.

Följande webb förslags grupp är ett svar på begäran ovan. Gruppen innehåller en lista med förslag för Sök frågor, med varje förslag, inklusive `displayText` `query` fälten, och `url` .

Fältet `displayText` innehåller den föreslagna frågan som du använder för att fylla i sökrutans listruta. Du måste visa alla förslag som svaret innehåller, och i den angivna ordningen.  

Om användaren väljer en fråga i list rutan kan du använda den för att anropa en av [API:er för Bing-sökresultat](../../bing-web-search/bing-api-comparison.md?bc=%2fen-us%2fazure%2fbread%2ftoc.json&toc=%2fen-us%2fazure%2fcognitive-services%2fbing-autosuggest%2ftoc.json) och visa resultatet själv eller skicka användaren till Bing-resultat sidan med det fält som returneras `url` .

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>Nästa steg

- [Vad är Automatiska förslag i Bing?](../get-suggested-search-terms.md)
- [Referens för API v7 för automatiska förslag i Bing](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [Hämta föreslagna Sök villkor från API för automatiska förslag i Bing](get-suggestions.md)