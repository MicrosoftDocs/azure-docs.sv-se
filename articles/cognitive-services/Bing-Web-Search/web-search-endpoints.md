---
title: Slut punkt för Webbs ökning
titleSuffix: Azure Cognitive Services
description: Skicka en `GET` begäran till följande slut punkt för att få webb Sök resultat. Rubrik-och URL-parametrarna definierar ytterligare specifikationer.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: c882e3e4d0cd6ba594a700f4fd53c14103a8d1d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "94381260"
---
# <a name="web-search-endpoint"></a>Webbsökning slut punkt

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

**Webbsökning-API: et** returnerar webb sidor, nyheter, bilder, videor och [entiteter](../bing-entities-search/overview.md). Entiteter har sammanfattnings information om en person, plats eller ämne.

## <a name="endpoint"></a>Slutpunkt

Skicka en `GET` begäran till följande slut punkt om du vill få webb Sök resultat med Bing-API: et. Rubrik-och URL-parametrarna definierar ytterligare specifikationer.

**Slut punkt**: returnerar webb resultat som är relevanta för användarens Sök fråga som definieras av `?q=""` .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Slut punkt: Mer information om sidhuvuden, parametrar, marknads koder, svars objekt, fel och mer finns i [Bing Web API v7-](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) referensen.

## <a name="response-json"></a>Svars-JSON

Svaret på en Webbs öknings förfrågan innehåller alla resultat som JSON-objekt. Parsning av resultatet kräver procedurer som hanterar elementen i varje typ. Se [självstudien](./tutorial-bing-web-search-single-page-app.md) och [käll koden](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) för exempel.

## <a name="next-steps"></a>Nästa steg

API: erna för **Bing** stöder Sök åtgärder som returnerar resultat enligt deras typ. Alla Sök slut punkter returnerar resultat som JSON-svars objekt.  Alla slut punkter har stöd för frågor som returnerar ett särskilt språk och en specifik plats efter longitud, latitud och Sök-radie.

Fullständig information om de parametrar som stöds av varje slut punkt finns i referens sidorna för varje typ.
Exempel på grundläggande förfrågningar som använder API: et för Webbs ökning finns i [söka på webben snabb starter](./overview.md).