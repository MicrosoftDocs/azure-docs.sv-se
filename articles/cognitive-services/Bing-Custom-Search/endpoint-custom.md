---
title: Slutpunkt för Anpassad sökning i Bing
titleSuffix: Azure Cognitive Services
description: Skapa skräddarsydda Sök upplevelser för ämnen som du bryr dig om. Användarna ser Sök resultat som är anpassade till det innehåll de bryr sig om.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 35dd147574eb7820debba55b105deed001be80fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96338494"
---
# <a name="custom-search"></a>Anpassad sökning

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
Med anpassad sökning i Bing kan du skapa skräddarsydda sökningar om ämnen som intresserar dig. Användarna ser sökresultat som skräddarsytts efter det innehåll som intresserar dem, i stället för att behöva bläddra bland irrelevanta sökresultat.

## <a name="custom-search-endpoint"></a>Anpassad Sök slut punkt
Skicka en `GET` begäran till följande slut punkt för att få resultat med hjälp av API för anpassad Bing-sökning. Använd sidhuvudena och URL-parametrarna för att definiera ytterligare specifikationer.

Slut punkt: returnerar Sök förslag som JSON-resultat som är relevanta för användarens indata som definieras av `?q=""` .
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Exempel som beskriver hur du konfigurerar anpassade Sök källor finns i [självstudien](./tutorials/custom-search-web-page.md). Mer information om sidhuvuden, parametrar, marknads koder, svars objekt, fel osv. finns i referens för [API för anpassad Bing-sökning v7](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference) .

## <a name="custom-search-response-json"></a>JSON för anpassat söksvar
En anpassad Sök förfrågan returnerar resultat som JSON-objekt, se [Response Objects](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Anpassad autoföreslå
Med det anpassade API: et för automatiska förslag kan du skicka en ofullständig Sök-frågeterm till Bing och få tillbaka en lista över föreslagna frågor som du kan konfigurera. Med anpassade automatiska förslag kan du lägga till förslag som returneras av API: et och eventuellt ange om du vill ta med förslag som skapats av Bing.

## <a name="custom-autosuggest-endpoint"></a>Anpassad autoföreslå slut punkt
Om du vill begära anpassade fråge förslag skickar du en GET-begäran till:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Information om hur du definierar anpassade förslag finns i [definiera anpassade Sök förslag](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Anpassad Bildsökning
Med det anpassade Bildsökning-API: et kan du skicka en Sök fråga till Bing och hämta en lista över relevanta bilder från din anpassade Sök instans.

## <a name="custom-image-search-endpoint"></a>Anpassad Bildsökning-slutpunkt
Om du vill begära bilder från din anpassade Sök instans skickar du en GET-begäran till följande URL:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Information om hur du konfigurerar en anpassad Sök instans finns i [Konfigurera din anpassade Sök upplevelse](./define-your-custom-view.md).

## <a name="next-steps"></a>Nästa steg
API: erna för **Bing** stöder Sök åtgärder som returnerar resultat enligt deras typ. Alla Sök slut punkter returnerar resultat som JSON-svars objekt.  Alla slut punkter har stöd för frågor som returnerar ett särskilt språk och/eller plats efter longitud, latitud och Sök-radie.

Fullständig information om de parametrar som stöds av varje slut punkt finns i referens sidorna för varje typ.
Exempel på grundläggande förfrågningar som använder API för anpassad sökning finns i [snabb start för anpassad sökning](/azure/cognitive-services/bing-custom-search/)