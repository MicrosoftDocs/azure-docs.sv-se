---
title: Snabbstart – Skicka en fråga till API:et i Python – Bing-företagssökning i närområde
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten för att börja använda Bing-företagssökning i närområde API i Python.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.date: 05/12/2020
ms.topic: quickstart
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: adbf3d9abddf01ba67046cfa433ffd46f713ff83
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536674"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Snabbstart: Skicka en fråga till BING-FÖRETAGSSÖKNING I NÄROMRÅDE-API:et i Python

> [!WARNING]
> Bing-sökning API:er flyttas från Cognitive Services till Bing-sökning Services. Från **och med 30 oktober 2020** måste alla nya instanser av Bing-sökning etableras enligt den process som beskrivs [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing-sökning API:er som etablerats med Cognitive Services kommer att stödjas under de kommande tre åren eller fram till slutet av ditt Enterprise-avtal, beroende på vilket som inträffar först.
> Migreringsanvisningar finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Använd den här snabbstarten om du vill lära dig hur du skickar begäranden Bing-företagssökning i närområde API: et, som är en Azure Cognitive Service. Även om det här enkla programmet är skrivet i Python är API:et en RESTful-webbtjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON.

Det här exempelprogrammet hämtar lokala svarsdata från API:et för en sökfråga.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration [– Skapa en kostnadsfritt](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/) 2.x eller 3.x.
* När du har din Azure-prenumeration skapar Bing-sökning resurs en Bing-sökning resurs i Azure Portal för <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" att hämta din nyckel och "  target="_blank"> </a> slutpunkt. När den har distribuerats klickar du **på Gå till resurs**.

## <a name="run-the-complete-application"></a>Kör det fullständiga programmet

Följande exempel hämtar lokaliserade resultat som implementeras i följande steg:
1. Deklarera variabler för att specificera slutpunkten med hjälp av värd och sökväg.
2. Ange frågeparametern. 
3. Definiera sökfunktionen som skapar begäran och lägger till `Ocp-Apim-Subscription-Key` -huvudet.
4. Ange `Ocp-Apim-Subscription-Key` rubriken. 
5. Upprätta anslutningen och skicka begäran.
6. Skriv ut JSON-resultaten.

Den fullständiga koden för den här demonstrationen är följande:

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
- [Snabbstart för sökning efter lokala företag i Java](local-search-java-quickstart.md)
- [C#-snabbstart för sökning efter lokala företag](local-quickstart.md)
- [Snabbstart för sökning efter Node.js lokala företag](local-search-node-quickstart.md)
