---
title: 'Snabb start: skicka en Sök förfrågan till REST API med Node.js-Entitetssökning i Bing'
titleSuffix: Azure Cognitive Services
description: Använd den här snabb starten för att skicka en begäran till Entitetssökning i Bing REST API med Node.js och ta emot ett JSON-svar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: b9311329ea4115d49f36dd7d39782bbd748a356b
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106112"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Snabb start: skicka en Sök förfrågan till Entitetssökning i Bing REST API med Node.js

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Använd den här snabbstarten för att göra ditt första anrop till API för entitetssökning i Bing och visa JSON-svaret. Det här enkla JavaScript-programmet skickar en nyhetssökfråga till API:et och visar svaret. Käll koden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js).

Även om det här programmet är skrivet i Java Script är API: et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

## <a name="prerequisites"></a>Förutsättningar

* Den senaste versionen av [Node.js](https://nodejs.org/en/download/).

* [Java Script Request-biblioteket](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny JavaScript-fil i din favorit-IDE eller-redigerare och ange sträng-och HTTPS-krav.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Skapa variabler för API-slutpunkten, prenumerationsnyckeln och sökfrågan. Du kan använda den globala slut punkten i följande kod eller använda den [anpassade slut domänen](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Lägg till parametrarna för marknad och fråga till en sträng som heter `query`. Se till att URL-koda frågan med `encodeURI()`.
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>Hantera och parsa svaret

1. Definiera en funktion med namnet `response_handler()` som tar ett HTTP-anrop, `response`, som parameter. 

2. I den här funktionen definierar du en variabel som innehåller bröd texten i JSON-svaret.  
    ```javascript
    let response_handler = function (response) {
        let body = '';
    };
    ```

3. Lagra bröd texten i svaret när `data` flaggan anropas.
    ```javascript
    response.on('data', function (d) {
        body += d;
    });
    ```

4. När en `end` flagga signalerar, tolkar du JSON och skriver ut den.

    ```javascript
    response.on ('end', function () {
    let json = JSON.stringify(JSON.parse(body), null, '  ');
    console.log (json);
    });
    ```

## <a name="send-a-request"></a>Skicka en förfrågan

1. Skapa en funktion som heter `Search()` för att skicka en sökbegäran. I den utför du följande steg:

2. I den här funktionen skapar du ett JSON-objekt som innehåller parametrarna för begäran. Använd `Get` för metoden och Lägg till din värd-och Sök vägs information. Lägg till din prenumerationsnyckel i `Ocp-Apim-Subscription-Key`-huvudet. 

3. Använd `https.request()` för att skicka begäran med den svars hanterare som skapats tidigare och dina Sök parametrar.
    
   ```javascript
   let Search = function () {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + query,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    
    let req = https.request (request_params, response_handler);
    req.end ();
   }
      ```

2. Anropa funktionen `Search()`.

## <a name="example-json-response"></a>Exempel på JSON-svar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../tutorial-bing-entities-search-single-page-app.md)

* [Vad är API för entitetsökning i Bing?](../overview.md )
* [API för entitetsökning i Bing referens](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
