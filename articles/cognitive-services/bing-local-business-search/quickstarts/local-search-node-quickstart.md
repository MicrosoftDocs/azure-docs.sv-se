---
title: 'Snabb start – skicka en fråga till API: et med hjälp av Node.js-Bing lokal företags sökning'
titleSuffix: Azure Cognitive Services
description: 'Använd den här snabb starten för att börja skicka begär anden till API: et för lokal sökning i Bing, som är en Azure-tjänst för inlärning.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 35d6aa7d606248796b3405d8fe1dabd64e0996d5
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430113"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Snabb start: skicka en fråga till API: et för lokal sökning i Bing med Node.js

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Använd den här snabb starten för att lära dig hur du skickar begär anden till API: et för lokal sökning i Bing, som är en Azure-tjänst för inlärning. Även om det här enkla programmet är skrivet i Node.js, är API: et en RESTful-webbtjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON.

Det här exempel programmet hämtar lokala svars data från API: et för en Sök fråga.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* Den senaste versionen av [Node.js](https://nodejs.org/en/download/).
* [Java Script Request-biblioteket](https://github.com/request/request).
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" skapar du en Bing-sökning resurs "  target="_blank"> skapa en Bing-sökning resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.


## <a name="code-scenario"></a>Kodscenario

Följande kod definierar och skickar begäran, som implementeras i följande steg:

1. Deklarera variabler för att specificera slutpunkten med hjälp av värd och sökväg.
2. Ange frågan och Lägg till Frågeparametern.
3. Skapa en hanterarfunktion för svaret.
4. Definiera Sök funktionen som skapar begäran och lägger till `Ocp-Apim-Subscription-Key` rubriken.
5. Kör Sök funktionen.


```javascript
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>Nästa steg

* [Snabb start för lokal affärs sökning i C#](local-quickstart.md)
* [Lokal affärs sökning Java snabb start](local-search-java-quickstart.md)
* [Snabb start för att söka i lokalt företag](local-search-python-quickstart.md)