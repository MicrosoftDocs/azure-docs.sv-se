---
title: Anpassa och föreslå bild Sök frågor – API för bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Läs om hur du anpassar Sök frågorna som du skickar till API för bildsökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 2566b2cf950df915f8ea843c34ea1fb6f8e7ea21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96342013"
---
# <a name="customize-and-suggest-image-search-queries"></a>Anpassa och föreslå bilds öknings frågor

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Använd den här artikeln för att lära dig hur du anpassar frågor och föreslår Sök villkor som ska skickas till API för bildsökning i Bing.

## <a name="suggest-search-terms"></a>Föreslå Sök villkor

Om din app har en sökruta där Sök villkor har angetts kan du använda [API för automatiska förslag i Bing](../../bing-autosuggest/get-suggested-search-terms.md) för att förbättra upplevelsen. API: et kan Visa föreslagna Sök villkor i real tid. API: et returnerar föreslagna frågesträngar baserat på del Sök villkor och Cognitive Services.

## <a name="pivot-the-query"></a>Pivotera frågan

Om Bing kan segmentera den ursprungliga Sök frågan innehåller objektet returnerade [bilder](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) `pivotSuggestions` . Pivot-förslag kan visas som valfria Sök villkor för användaren. Om den ursprungliga frågan exempelvis var *Microsoft Surface*, kan Bing segmentera frågan till *Microsoft* och *ytan* och tillhandahålla föreslagna pivoteror för var och en. Dessa förslag kan visas som valfria sökord för användaren.

I följande exempel visas Pivot-förslag för *Microsoft-ytan*:  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

Fältet `pivotSuggestions` innehåller listan över segment (pivoter) som den ursprungliga frågan delades in i. För varje pivot innehåller svaret en lista över [Query](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj)-objekt som innehåller föreslagna frågor. `text`Fältet innehåller den föreslagna frågan. `displayText`Fältet innehåller den term som ersätter Pivot-funktionen i den ursprungliga frågan. Ett exempel är lanserings datum för ytan.

Om den pivoterande frågesträngen är vad användaren söker efter använder du `text` `thumbnail` fälten och för att visa de olika fråga-strängarna. Gör miniatyren och texten klicknings bara genom att använda `webSearchUrl` URL: en eller URL: en `searchLink` . Används `webSearchUrl` för att skicka användaren till Bing search-resultaten. Om du anger en egen resultat sida använder du `searchLink` .

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>Expandera frågan

Om Bing kan expandera frågan för att begränsa den ursprungliga sökningen innehåller objektet [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) (Bilder) fältet `queryExpansions`. Om frågan exempelvis var *Microsoft-yta* kan de utökade frågorna vara:
- Microsoft Surface **Pro 3**.
- Microsoft Surface **RT**.
- Microsoft Surface **Phone**.
- Microsoft Surface **Hub**.

Följande exempel visar de expanderade frågorna för *Microsoft Surface*.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

Fältet `queryExpansions` innehåller en lista över [Query](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj)-objekt. `text`Fältet innehåller den utökade frågan. `displayText`Fältet innehåller expansions perioden. Om den expanderade frågesträngen är vad användaren söker efter använder du `text` `thumbnail` fälten och för att visa de expanderade frågesträngarna. Gör miniatyren och texten klicknings bara genom att använda `webSearchUrl` URL: en eller URL: en `searchLink` . Används `webSearchUrl` för att skicka användaren till Bing search-resultaten. Om du anger en egen resultat sida använder du `searchLink` .

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nästa steg

Om du inte har provat API för bildsökning i Bing tidigare försöker du med en [snabb start](../quickstarts/csharp.md). Om du vill ha något mer komplext kan du testa självstudien för att skapa en webbapp med en [enda sida](../tutorial-bing-image-search-single-page-app.md).