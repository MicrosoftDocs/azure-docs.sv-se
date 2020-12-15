---
title: 'Snabb start: Extrahera text-och layoutinformation med hjälp av python-formulär igenkänning'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten ska du använda formulär tolkens layout REST API med python för att läsa text-och tabell data från formulären.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: e9368b5c6c90fe7ce65fc066e0b19523e07ece51
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504346"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>Snabb start: Extrahera text-och layoutinformation med hjälp av formulär tolken REST API med python

I den här snabb starten använder du Azures formulär tolken REST API med python för att extrahera layoutinformation och tabell data från formulär dokument.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten måste du ha:
- [Python](https://www.python.org/downloads/) installerat (om du vill köra exemplet lokalt).
- Ett formulär dokument. Du kan ladda ned en bild från [exempel data uppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451) (Hämta och extrahera *sample_data.zip*) för den här snabb starten.

> [!NOTE]
> I den här snabb starten används ett lokalt lagrat dokument. Information om hur du använder fjärrfiler som används av URL: er finns i [referens dokumentationen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync).


## <a name="create-a-form-recognizer-resource"></a>Skapa en formulär igenkännings resurs

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>Analysera formulärlayouten

Du börjar analysera layouten genom att anropa API: et för **[analys av layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** med python-skriptet nedan. Innan du kör skriptet gör du följande ändringar:

1. Ersätt `<Endpoint>` med den slut punkt som du fick med din igenkännings prenumeration för formulär.
1. Ersätt `<path to your form>` med sökvägen till ditt lokala formulär dokument.
1. Ersätt `<subscription key>` med den prenumerations nyckel som du kopierade från föregående steg.

    # <a name="v20"></a>[v2.0](#tab/v2-0) 
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
        # Change Content-Type as appropriate
        'Content-Type': 'application/pdf',
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
    # <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)  
    ```python
    ########### Python Form Recognizer Async Layout #############
    
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        # Change Content-Type as appropriate
        'Content-Type': 'application/pdf',
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
    
    
      ---


1. Spara koden i en fil med fil namns tillägget. py. Till exempel *form-Recognizer-layout.py*.
1. Öppna ett kommandotolksfönster.
1. I kommandotolken kör du exemplet med kommandot `python`. Ett exempel är `python form-recognizer-layout.py`.

Du får ett `202 (Success)` svar som innehåller ett **Åtgärds plats** huvud som skriptet skriver ut till-konsolen. Den här rubriken innehåller ett åtgärds-ID som du kan använda för att fråga efter statusen för den asynkrona åtgärden och hämta resultatet. I följande exempel värde är strängen efter `operations/` Åtgärds-ID: t.

# <a name="v20"></a>[v2.0](#tab/v2-0)   
```console
https://cognitiveservice/formrecognizer/v2.0/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
``` 
# <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)  
```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```  

---
    



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
1. Använd kommandot igen `python` för att köra exemplet. Ett exempel är `python form-recognizer-layout.py`.

### <a name="examine-the-response"></a>Granska svaret

Skriptet skriver ut svar till konsolen tills åtgärden **analysera layout** slutförs. Sedan skrivs extraherade data i JSON-format. `"readResults"`Noden innehåller alla text rader med dess respektive placering på sidan. `"selectionMarks"`Noden (i för hands versionen av v 2.1) visar varje markerings märke (kryss markering) och om dess status är "markerad" eller "omarkerad". I `"pageResults"` fältet visas alla text delar i tabeller, var och en med dess rad-kolumn-koordinat.

Se följande faktura bild och dess motsvarande JSON-utdata. Utdatan har kort ATS för enkelhetens skull.

:::image type="content" source="../media/contoso-invoice.png" alt-text="Contoso Project Statement-dokument med en tabell.":::

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:36:52Z",
    "lastUpdatedDateTime": "2020-08-20T20:36:58Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/14/words/0",
                                    "#/readResults/0/lines/14/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

# <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)   
```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:40:50Z",
    "lastUpdatedDateTime": "2020-08-20T20:40:55Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                        ]
                    }
                ],
                "selectionMarks": [
                    {
                        "boundingBox": [
                            3.9737,
                            3.7475,
                            4.1693,
                            3.7475,
                            4.1693,
                            3.9428,
                            3.9737,
                            3.9428
                        ],
                        "confidence": 0.989,
                        "state": "selected"
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/12/words/0",
                                    "#/readResults/0/lines/12/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
``` 

---



## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du formulär tolken REST API med python för att extrahera textens layout för en faktura. Sedan läser du referens dokumentationen för att utforska formulärets tolknings-API i större djup.

> [!div class="nextstepaction"]
> [REST API referens dokumentation](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)
