---
title: Skicka och använda API-frågor och svar – Bing lokal företags sökning
titleSuffix: Azure Cognitive Services
description: Använd den här artikeln för att lära dig hur du skickar och använder Sök frågor med API för lokal sökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rosh
ms.openlocfilehash: 70a33774ac82312660d887fb86f7e2a482c30a0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96487175"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Skicka och använda Bing API-frågor och svar för lokal sökning i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Du kan få lokala resultat från Bing-API: et för lokal sökning genom att skicka en Sök fråga till dess slut punkt och inkludera `Ocp-Apim-Subscription-Key` rubriken, vilket krävs. Förutom tillgängliga [rubriker](local-search-reference.md#headers) och [parametrar](local-search-reference.md#query-parameters)kan du anpassa sökningarna genom att ange [geografiska gränser](specify-geographic-search.md) för det område som ska genomsökas och vilka [Kategorier](local-search-query-response.md) av platser som returneras.

## <a name="creating-a-request"></a>Skapa en begäran

Om du vill skicka en begäran till Bing-API: et för lokal sökning lägger du till en sökterm i `q=` parametern innan du lägger till den i API-slutpunkten och inkluderar `Ocp-Apim-Subscription-Key` rubriken. Exempel:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

URL-syntaxen för fullständig begäran visas nedan. Se [snabb starter](quickstarts/local-quickstart.md)för den lokala Business Search-API: n och referens innehåll för [huvuden](local-search-reference.md#headers) och [parametrar](local-search-reference.md#query-parameters) för mer information om att skicka begär Anden. 

Information om lokala Sök kategorier finns i [Sök kategorier för API: et för lokal sökning i Bing](local-categories.md).

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Använda svar

JSON-svar från Bing Local Business Search-API: et innehåller ett- `SearchResponse` objekt. API: et kommer att returnera relevanta Sök resultat i `places` fältet. Om inga resultat hittas tas `places` fältet inte med i svaret.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>Sök Resultat-attribut

JSON-resultaten som returneras av API: et omfattar följande attribut:

* _type
* adress
* entityPresentationInfo
* geografiska
* id
* name
* routeablePoint
* telefon
* url

Allmän information om sidhuvuden, parametrar, marknads koder, svars objekt, fel osv. finns i [Bing-v7 för lokal sökning i API](local-search-reference.md) .

> [!NOTE]
> Du eller en tredje part å din vägnar får inte använda, behålla, lagra, cachelagra, dela eller distribuera data från den lokala Sök-API: n för att testa, utveckla, träna, distribuera eller göra tillgängliga tjänster eller funktioner som inte kommer från Microsoft. 


## <a name="example-json-response"></a>Exempel på JSON-svar

Följande JSON-svar innehåller Sök resultat som anges av frågan `?q=restaurant+in+Bellevue` .

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>Nästa steg
- [Snabb start för lokal affärs sökning](quickstarts/local-quickstart.md)
- [Lokal affärs sökning Java snabb start](quickstarts/local-search-java-quickstart.md)
- [Snabb start för lokal Business search-nod](quickstarts/local-search-node-quickstart.md)
- [Snabb start för att söka i lokalt företag](quickstarts/local-search-python-quickstart.md)