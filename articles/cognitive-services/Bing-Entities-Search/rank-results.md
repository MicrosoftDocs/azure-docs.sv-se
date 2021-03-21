---
title: Använda rangordning för att Visa svar – Entitetssökning i Bing
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder rangordning för att visa de svar som API för entitetsökning i Bing returnerar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: b63c2e53f34ac86f8ddf5ad300c2465ee2d9f032
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94365643"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Använda rangordning för att Visa Sök Resultat för enheter  

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Varje enhets Sök svar innehåller ett [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) -svar som anger hur du måste visa Sök resultat som returneras av API för entitetsökning i Bing. Rangordnings svar grupper resulterar i innehållet i Polen, Mainline och sid panelen. Polen-resultatet är det viktigaste eller framträdande resultatet och bör visas först. Om du inte visar återstående resultat i ett traditionellt Mainline-och sid List format måste du ange Mainline-innehållet som är större än innehållet i innehålls sidan. 
  
I varje grupp identifierar [objekt](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) mat ris ordningen som innehållet måste visas i. Varje objekt har två sätt att identifiera resultatet inom ett svar.  
 

|Fält | Beskrivning  |
|---------|---------|
|`answerType` och `resultIndex` | `answerType` identifierar svaret (antingen enhet eller plats) och `resultIndex` identifierar ett resultat inom det svaret (till exempel en entitet). Indexet börjar vid 0.|
|`value`    | `value` Innehåller ett ID som matchar ID: t för antingen ett svar eller ett resultat inom svaret. Antingen svaret eller resultatet innehåller ID: t, men inte båda. |
  
Med `answerType` och `resultIndex` är en två stegs process. Börja med `answerType` att identifiera det svar som innehåller de resultat som ska visas. Använd sedan `resultIndex` för att indexera i svarets resultat för att få resultatet att visas. ( `answerType` Värdet är namnet på fältet i [SearchResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) -objektet.) Om du vill visa alla svars resultat tillsammans innehåller inte rangordnings svars posten `resultIndex` fältet.

Med ID: t måste du matcha rangordnings-ID: t med ID: t för ett svar eller ett av dess resultat. Om ett svars objekt innehåller ett `id` fält visas alla svars resultat tillsammans. Om objektet till exempel `Entities` innehåller `id` fältet visas alla entiteter-artiklar tillsammans. Om `Entities` objektet inte innehåller `id` fältet, innehåller varje entitet ett `id` fält och rangordnings svaret blandar entiteterna med plats resultatet.  
  
## <a name="ranking-response-example"></a>Exempel på ranknings svar

Nedan visas ett exempel på en [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

Baserat på detta ranknings svar visar sid panelen de två enhets resultaten som är relaterade till Jimi Hendrix.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](tutorial-bing-entities-search-single-page-app.md)