---
title: 'Snabb start: Extrahera text-och layoutinformation med hjälp av python-formulär igenkänning'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten ska du använda formulär tolkens layout REST API med python för att läsa text-och tabell data från formulären.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 1777d4d09dfb12f490245f726715aed4eefd6227
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88517805"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>Snabb start: Extrahera text-och layoutinformation med hjälp av formulär tolken REST API med python

I den här snabb starten använder du Azures formulär tolken REST API med python för att extrahera layoutinformation och tabell data från formulär dokument.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten måste du ha:
- [Python](https://www.python.org/downloads/) installerat (om du vill köra exemplet lokalt).
- Ett formulär dokument. Du kan ladda ned en bild från [exempel data uppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451) för den här snabb starten.

> [!NOTE]
> I den här snabb starten används ett lokalt lagrat dokument. Information om hur du använder fjärrfiler som används av URL: er finns i [referens dokumentationen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync).


## <a name="create-a-form-recognizer-resource"></a>Skapa en formulär igenkännings resurs

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>Analysera formulärlayouten

Du börjar analysera layouten genom att anropa API: et för **[analys av layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** med python-skriptet nedan. Innan du kör skriptet gör du följande ändringar:

1. Ersätt `<Endpoint>` med den slut punkt som du fick med din igenkännings prenumeration för formulär.
1. Ersätt `<path to your form>` med sökvägen till ditt lokala formulär dokument.
1. Ersätt `<subscription key>` med den prenumerations nyckel som du kopierade från föregående steg.

    ```python
    ########### Python Form Recognizer Async Layout #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Spara koden i en fil med fil namns tillägget. py. Till exempel *form-Recognizer-layout.py*.
1. Öppna ett kommandotolksfönster.
1. I kommandotolken kör du exemplet med kommandot `python`. Till exempel `python form-recognizer-layout.py`.

Du får ett `202 (Success)` svar som innehåller ett **Åtgärds plats** huvud som skriptet skriver ut till-konsolen. Den här rubriken innehåller ett åtgärds-ID som du kan använda för att fråga efter statusen för den asynkrona åtgärden och hämta resultatet. I följande exempel värde är strängen efter `operations/` Åtgärds-ID: t.

```console
https://cognitiveservice/formrecognizer/v2.0/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-layout-results"></a>Hämta layout resultatet

När du har anropat API för **analys av layout** anropar du API: et för att **[analysera utdata](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** för att hämta status för åtgärden och de extraherade data. Lägg till följande kod längst ned i python-skriptet. I den här koden används åtgärds-ID-värdet i ett nytt API-anrop. Det här skriptet anropar API: n med jämna mellanrum tills resultaten är tillgängliga. Vi rekommenderar ett intervall på en sekund.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Layout results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Layout Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Layout Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Spara skriptet.
1. Använd kommandot igen `python` för att köra exemplet. Till exempel `python form-recognizer-layout.py`.

### <a name="examine-the-response"></a>Granska svaret

Skriptet skriver ut svar till konsolen tills åtgärden **analysera layout** slutförs. Sedan skrivs extraherade data i JSON-format. `"readResults"`Noden innehåller alla text rader med dess respektive placering på sidan. I `"pageResults"` fältet visas alla text delar i tabeller, var och en med dess rad-kolumn-koordinat.

Se följande faktura bild och dess motsvarande JSON-utdata. Utdatan har kort ATS för enkelhetens skull.

> [!div class="mx-imgBorder"]
> ![Contoso-faktura dokument med en tabell](../media/contoso-invoice.png)

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-11-12T19:55:36Z",
  "lastUpdatedDateTime":"2019-11-12T19:55:43Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "language":"en",
        "angle":0,
        "width":8.5,
        "height":11,
        "unit":"inch",
        "lines":[ 
          { 
            "language":"en",
            "boundingBox":[ 
              0.5384,
              1.1583,
              1.4466,
              1.1583,
              1.4466,
              1.3534,
              0.5384,
              1.3534
            ],
            "text":"Contoso",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.5384,
                  1.1583,
                  1.4466,
                  1.1583,
                  1.4466,
                  1.3534,
                  0.5384,
                  1.3534
                ],
                "text":"Contoso",
                "confidence":1
              }
            ]
          },
          { 
            "language":"en",
            "boundingBox":[ 
              0.7994,
              1.5143,
              1.3836,
              1.5143,
              1.3836,
              1.6154,
              0.7994,
              1.6154
            ],
            "text":"Address:",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.7994,
                  1.5143,
                  1.3836,
                  1.5143,
                  1.3836,
                  1.6154,
                  0.7994,
                  1.6154
                ],
                "text":"Address:",
                "confidence":1
              }
            ]
          },
          ...
          { 
            "language":"en",
            "boundingBox":[ 
              6.2285,
              3.4114,
              6.3919,
              3.4114,
              6.3919,
              3.5119,
              6.2285,
              3.5119
            ],
            "text":"PT",
            "words":[ 
              { 
                "boundingBox":[ 
                  6.2285,
                  3.4114,
                  6.3919,
                  3.4114,
                  6.3919,
                  3.5119,
                  6.2285,
                  3.5119
                ],
                "text":"PT",
                "confidence":1
              }
            ]
          }
        ]
      }
    ],
    "pageResults":[ 
      { 
        "page":1,
        "tables":[ 
          { 
            "rows":2,
            "columns":6,
            "cells":[ 
              { 
                "rowIndex":0,
                "columnIndex":0,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5075,
                  2.8088,
                  1.9061,
                  2.8088,
                  1.9061,
                  3.3219,
                  0.5075,
                  3.3219
                ],
                "elements":[ 
                  "#/readResults/0/lines/8/words/0",
                  "#/readResults/0/lines/8/words/1"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du formulär tolken REST API med python för att extrahera textens layout för en faktura. Sedan läser du referens dokumentationen för att utforska formulärets tolknings-API i större djup.

> [!div class="nextstepaction"]
> [REST API referens dokumentation](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)
