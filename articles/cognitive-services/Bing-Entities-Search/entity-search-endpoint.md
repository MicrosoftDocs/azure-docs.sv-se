---
title: API för entitetsökning i Bing slut punkten
titleSuffix: Azure Cognitive Services
description: API för entitetsökning i Bing har en slut punkt som returnerar entiteter från webben baserat på en fråga. Dessa Sök Resultat returneras i JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 7fc65b27c3c02d6102210ae277690795d763d91a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96338265"
---
# <a name="bing-entity-search-api-endpoint"></a>API för entitetsökning i Bing slut punkt

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).


API för entitetsökning i Bing har en slut punkt som returnerar entiteter från webben baserat på en fråga. Dessa Sök Resultat returneras i JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Hämta enhets resultat från slut punkten

Skicka en begäran till följande slut punkt för att hämta enhets resultat med **Bing-API: et** `GET` . Använd [huvuden](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) och [frågeparametrar](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) för att anpassa din Sök förfrågan. Sök begär Anden kan skickas med hjälp av `?q=` parametern.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Vad är API för entitetsökning i Bing?](overview.md)

## <a name="see-also"></a>Se även 

Mer information om huvuden, parametrar, marknads koder, svars objekt, fel och mer finns i referens artikeln om [API för entitetsökning i Bing v7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) .