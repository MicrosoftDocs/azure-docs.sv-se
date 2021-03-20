---
title: Slut punkter för API för bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: 'Bildsökning-API: et innehåller tre slut punkter. Slut punkt 1 returnerar bilder från webben. Slut punkt 2 returnerar ImageInsights. Slut punkt 3 returnerar trend bilder.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 67fec77136f5d593279be2846e63c51b60e16bb4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94593526"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Slut punkter för API för bildsökning i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

**Bildsökning-API: et** innehåller tre slut punkter.  Slut punkt 1 returnerar bilder från webben baserat på en fråga. Slut punkt 2 returnerar [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  Slut punkt 3 returnerar trend bilder.

## <a name="endpoints"></a>Slutpunkter

Skicka en begäran till någon av följande slut punkter för att hämta avbildnings resultat med Bing-API: et. Använd sidhuvudena och URL-parametrarna för att definiera ytterligare specifikationer.

**Slut punkt 1:** Returnerar bilder som är relevanta för användarens Sök fråga som definieras av `?q=""` .
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Slut punkt 2:** Returnerar insikter om en bild med antingen `GET` eller `POST` .
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
En GET-begäran returnerar insikter om en bild, till exempel webb sidor som innehåller bilden. Inkludera parametern [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) med en `GET` begäran.

Du kan också inkludera en binär bild i bröd texten i en `POST` begäran och ange parametern [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) till `RecognizedEntities` . Detta returnerar en [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) som kan användas som en parameter i en efterföljande `GET` begäran, som returnerar information om personer i avbildningen.  Ställ in `modules` på `All` för att hämta alla insikter, förutom `RecognizedEntities` i resultaten av `POST` utan något annat anrop med hjälp av `insightsToken` .


**Slut punkt 3:** Returnerar bilder som är trender baserat på Sök begär Anden som andra har gjort. Bilderna är indelade i olika kategorier, till exempel baserat på intressanta personer eller evenemang.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

En lista över marknader som stöder Trends images finns i [Trends bilder](./trending-images.md).

Mer information om sidhuvuden, parametrar, marknads koder, svars objekt, fel osv. finns i referens för [API för bildsökning i Bing v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) .
## <a name="response-json"></a>Svars-JSON
Svaret på en avbildnings Sök förfrågan innehåller resultat som JSON-objekt. Exempel på hur du tolkar resultaten finns i [självstudien](./tutorial-bing-image-search-single-page-app.md) och [käll koden](./tutorial-bing-image-search-single-page-app.md).

## <a name="next-steps"></a>Nästa steg
API: erna för **Bing** stöder Sök åtgärder som returnerar resultat enligt deras typ. Alla Sök slut punkter returnerar resultat som JSON-svars objekt.  Alla slut punkter har stöd för frågor som returnerar ett särskilt språk och/eller plats efter longitud, latitud och Sök-radie.

Fullständig information om de parametrar som stöds av varje slut punkt finns i referens sidorna för varje typ.
Exempel på grundläggande förfrågningar som använder API: et för bilds ökning finns [bildsökning snabb starter](./overview.md).