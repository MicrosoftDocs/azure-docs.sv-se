---
title: 'Snabb start – skicka en fråga till API: et i python-Bing lokal företags sökning'
titleSuffix: Azure Cognitive Services
description: Använd den här snabb starten för att börja använda Bing-API för lokal sökning i python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: ff06d29c613b626c48c347628992576fc29b3a89
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430147"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Snabb start: skicka en fråga till API: et för lokal sökning i Bing i python

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Använd den här snabb starten för att lära dig hur du skickar begär anden till API: et för lokal sökning i Bing, som är en Azure-tjänst för inlärning. Även om det här enkla programmet skrivs i python är API: et en RESTful-webbtjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON.

Det här exempel programmet hämtar lokala svars data från API: et för en Sök fråga.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/) 2. x eller 3. x.
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" skapar du en Bing-sökning resurs "  target="_blank"> skapa en Bing-sökning resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.

## <a name="run-the-complete-application"></a>Kör hela programmet

Följande exempel hämtar lokaliserade resultat, som implementeras i följande steg:
1. Deklarera variabler för att specificera slutpunkten med hjälp av värd och sökväg.
2. Ange Frågeparametern. 
3. Definiera Sök funktionen som skapar begäran och lägger till `Ocp-Apim-Subscription-Key` rubriken.
4. Ange `Ocp-Apim-Subscription-Key` sidhuvudet. 
5. Upprätta anslutningen och skicka begäran.
6. Skriv ut JSON-resultaten.

Den fullständiga koden för den här demon är följande:

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>Nästa steg
- [Lokal affärs sökning Java snabb start](local-search-java-quickstart.md)
- [Snabb start för lokal affärs sökning i C#](local-quickstart.md)
- [Snabb start för lokal företags sökning Node.js](local-search-node-quickstart.md)