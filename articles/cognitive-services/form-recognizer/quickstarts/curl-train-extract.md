---
title: 'Snabb start: träna en modell och extrahera formulär data med hjälp av typografiska formulär igenkänning'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten använder du formulär tolken REST API med sväng för att träna en modell och extrahera data från formulär.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.openlocfilehash: 0738e06fbd26526ed78991d5db18e7f8c5c58ea7
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505349"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Snabb start: träna en formulär igenkännings modell och extrahera formulär data med hjälp av REST API med vändning

I den här snabb starten använder du Azures formulär tolken REST API med sväng för att träna och skapa Poäng för att extrahera nyckel/värde-par och tabeller.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten måste du ha:
- [spiralen](https://curl.haxx.se/windows/) är installerad.
- En uppsättning av minst sex formulär av samma typ. Du kommer att använda fem av dessa för att träna modellen och sedan testa den med sjätte form. Dina formulär kan vara av olika filtyper men måste vara av samma typ av dokument. Du kan använda en [exempel data uppsättning](https://go.microsoft.com/fwlink/?linkid=2090451) (Hämta och extrahera *sample_data.zip*) för den här snabb starten. Ladda upp utbildnings-filerna till roten för en Blob Storage-behållare i ett Azure Storage konto med standard prestanda nivå. Du kan ställa in testfilerna i en separat mapp.

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulär igenkännings resurs

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Träna en formulär igenkännings modell

Först behöver du en uppsättning tränings data i en Azure Storage-blob. Du bör ha minst fem ifyllda formulär (PDF-dokument och/eller bilder) av samma typ/struktur som dina viktigaste indata. Eller så kan du använda ett enda tomt formulär med två ifyllda formulär. Det tomma formulärets fil namn måste innehålla ordet "Empty". Se [skapa en tränings data uppsättning för en anpassad modell](../build-training-data-set.md) för tips och alternativ för att sätta samman dina tränings data.

> [!NOTE]
> Du kan använda funktionen märkta data för att manuellt märka vissa eller alla dina utbildnings data i förväg. Detta är en mer komplex process men resulterar i en bättre tränad modell. Mer information om den här funktionen finns i avsnittet [träna med etiketter](../overview.md#train-with-labels) i översikten.



Om du vill träna en formulär igenkännings modell med dokumenten i din Azure Blob-behållare anropar du det **[anpassade modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** -API: et för träna genom att köra följande spiral-kommando. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `<Endpoint>` med den slut punkt som du fick med din igenkännings prenumeration för formulär.
1. Ersätt `<subscription key>` med den prenumerations nyckel som du kopierade från föregående steg.
1. Ersätt `<SAS URL>` med Azure Blob Storage-behållarens URL för signatur för delad åtkomst (SAS). [!INCLUDE [get SAS URL](../includes/sas-instructions.md)]


    # <a name="v20"></a>[v2.0](#tab/v2-0)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    # <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.1-preview.2/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    
    ---



Du får ett `201 (Success)` svar med ett **plats** huvud. Värdet för den här rubriken är ID: t för den nya modell som tränas.

## <a name="get-training-results"></a>Hämta utbildnings resultat

När du har startat träna-åtgärden använder du en ny åtgärd, **[hämtar anpassad modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** för att kontrol lera kursens status. Skicka modell-ID: t till det här API-anropet för att kontrol lera utbildningens status:

1. Ersätt `<Endpoint>` med den slut punkt som du fick med ditt formulärs igenkännings prenumerations nyckel.
1. Ersätt `<subscription key>` med din prenumerations nyckel
1. Ersätt `<model ID>` med modell-ID: t som du fick i föregående steg



# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview.2/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```ce\": \""<SAS URL>"\"}"
```
    
---

Du får ett `200 (Success)` svar med en JSON-text i följande format. Lägg märke till `"status"` fältet. Detta kommer att ha värdet `"ready"` när inlärningen är klar. Om modellen inte är avslutad måste du fråga tjänsten igen genom att köra kommandot på nytt. Vi rekommenderar ett intervall på en sekund eller flera anrop mellan anrop.

`"modelId"`Fältet innehåller ID: t för den modell som du tränar. Du behöver detta för nästa steg.

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analysera formulär för nyckel/värde-par och tabeller

Sedan använder du din nya tränade modell för att analysera ett dokument och extrahera nyckel/värde-par och tabeller från det. Anropa **[analys formulärets](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** API genom att köra följande spiral-kommando. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `<Endpoint>` med den slut punkt som du fick från ditt formulärs igenkännings prenumerations nyckel. Du hittar det på fliken **Översikt** i formulärets tolknings resurs.
1. Ersätt `<model ID>` med det modell-ID som du fick i föregående avsnitt.
1. Ersätt `<SAS URL>` med en SAS-URL till din fil i Azure Storage. Följ stegen i avsnittet utbildning, men i stället för att få en SAS-URL för hela BLOB-behållaren, hämta en för den aktuella filen som du vill analysera.
1. Ersätt `<subscription key>` med din prenumerationsnyckel.

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
# <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)    
```bash
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.1-preview.2/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
    
---



Du får ett `202 (Success)` svar med en **Åtgärds plats** rubrik. Värdet för den här rubriken innehåller ett resultat-ID som du använder för att spåra resultatet av analys åtgärden. Spara detta resultat-ID för nästa steg.

## <a name="get-the-analyze-results"></a>Hämta analys resultatet

Använd följande API för att fråga resultatet av analys åtgärden.

1. Ersätt `<Endpoint>` med den slut punkt som du fick från ditt formulärs igenkännings prenumerations nyckel. Du hittar det på fliken **Översikt** i formulärets tolknings resurs.
1. Ersätt `<result ID>` med det ID som du fick i föregående avsnitt.
1. Ersätt `<subscription key>` med din prenumerationsnyckel.


# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
    
---

Du får ett `200 (Success)` svar med en JSON-text i följande format. Utdatan har kort ATS för enkelhetens skull. Lägg märke till `"status"` fältet längst ned. Detta kommer att ha värdet `"succeeded"` när analys åtgärden har slutförts. Om analys åtgärden inte har slutförts måste du fråga tjänsten igen genom att köra kommandot på nytt. Vi rekommenderar ett intervall på en sekund eller flera anrop mellan anrop.

Huvud nyckel/värde paret associationer och tabeller finns i `"pageResults"` noden. Om du även har angett oformaterad text extrahering genom *includeTextDetails* URL-parameter, `"readResults"` visar noden innehållet och positionerna för all text i dokumentet.

Det här exemplet på JSON-utdata har kortas ned för enkelhetens skull.

# <a name="v20"></a>[v2.0](#tab/v2-0)
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
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
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```    
# <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)    
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
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
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
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
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ], 
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ], 
    "documentResults": [],
    "errors": []
  }
}
``` 

---


## <a name="improve-results"></a>Förbättra resultaten

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du formulär tolken REST API med sväng för att träna en modell och köra den i ett exempel scenario. Sedan läser du referens dokumentationen för att utforska formulärets tolknings-API i större djup.

> [!div class="nextstepaction"]
> [REST API referens dokumentation](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
