---
title: 'Snabb start: träna en modell och extrahera formulär data med hjälp av typografiska formulär igenkänning'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten använder du formulär tolken REST API med sväng för att träna en modell och extrahera data från formulär.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: d0bf5da3633ce8f34e34cbdf0cee12a94c128d72
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88517973"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Snabb start: träna en formulär igenkännings modell och extrahera formulär data med hjälp av REST API med vändning

I den här snabb starten använder du Azures formulär tolken REST API med sväng för att träna och skapa Poäng för att extrahera nyckel/värde-par och tabeller.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten måste du ha:
- [spiralen](https://curl.haxx.se/windows/) är installerad.
- En uppsättning av minst sex formulär av samma typ. Du kommer att använda fem av dessa för att träna modellen och sedan testa den med sjätte form. Dina formulär kan vara av olika filtyper men måste vara av samma typ av dokument. Du kan använda en [exempel data uppsättning](https://go.microsoft.com/fwlink/?linkid=2090451) för den här snabb starten. Ladda upp utbildnings-filerna till roten för en Blob Storage-behållare i ett Azure Storage konto med standard prestanda nivå. Du kan ställa in testfilerna i en separat mapp.

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulär igenkännings resurs

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Träna en formulär igenkännings modell

Först behöver du en uppsättning tränings data i en Azure Storage-blob. Du bör ha minst fem ifyllda formulär (PDF-dokument och/eller bilder) av samma typ/struktur som dina viktigaste indata. Eller så kan du använda ett enda tomt formulär med två ifyllda formulär. Det tomma formulärets fil namn måste innehålla ordet "Empty". Se [skapa en tränings data uppsättning för en anpassad modell](../build-training-data-set.md) för tips och alternativ för att sätta samman dina tränings data.

> [!NOTE]
> Du kan använda funktionen märkta data för att manuellt märka vissa eller alla dina utbildnings data i förväg. Detta är en mer komplex process men resulterar i en bättre tränad modell. Mer information om den här funktionen finns i avsnittet [träna med etiketter](../overview.md#train-with-labels) i översikten.

Om du vill träna en formulär igenkännings modell med dokumenten i din Azure Blob-behållare anropar du det **[anpassade modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** -API: et för träna genom att köra följande spiral-kommando. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `<Endpoint>` med den slut punkt som du fick med din igenkännings prenumeration för formulär.
1. Ersätt `<subscription key>` med den prenumerations nyckel som du kopierade från föregående steg.
1. Ersätt `<SAS URL>` med Azure Blob Storage-behållarens URL för signatur för delad åtkomst (SAS). Hämta SAS-URL: en genom att öppna Microsoft Azure Storage Explorer, högerklicka på behållaren och välja **Hämta signatur för delad åtkomst**. Kontrol lera att **Läs** -och **list** behörigheterna är markerade och klicka på **skapa**. Kopiera sedan värdet i **URL** -avsnittet. Den bör ha formatet: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Du får ett `201 (Success)` svar med ett **plats** huvud. Värdet för den här rubriken är ID: t för den nya modell som tränas.

## <a name="get-training-results"></a>Hämta utbildnings resultat

När du har startat träna-åtgärden använder du en ny åtgärd, **[hämtar anpassad modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** för att kontrol lera kursens status. Skicka modell-ID: t till det här API-anropet för att kontrol lera utbildningens status:

1. Ersätt `<Endpoint>` med den slut punkt som du fick med ditt formulärs igenkännings prenumerations nyckel.
1. Ersätt `<subscription key>` med din prenumerations nyckel
1. Ersätt `<model ID>` med modell-ID: t som du fick i föregående steg

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

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

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

Du får ett `202 (Success)` svar med en **Åtgärds plats** rubrik. Värdet för den här rubriken innehåller ett resultat-ID som du använder för att spåra resultatet av analys åtgärden. Spara detta resultat-ID för nästa steg.

## <a name="get-the-analyze-results"></a>Hämta analys resultatet

Använd följande API för att fråga resultatet av analys åtgärden.

1. Ersätt `<Endpoint>` med den slut punkt som du fick från ditt formulärs igenkännings prenumerations nyckel. Du hittar det på fliken **Översikt** i formulärets tolknings resurs.
1. Ersätt `<result ID>` med det ID som du fick i föregående avsnitt.
1. Ersätt `<subscription key>` med din prenumerationsnyckel.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Du får ett `200 (Success)` svar med en JSON-text i följande format. Utdatan har kort ATS för enkelhetens skull. Lägg märke till `"status"` fältet längst ned. Detta kommer att ha värdet `"succeeded"` när analys åtgärden har slutförts. Om analys åtgärden inte har slutförts måste du fråga tjänsten igen genom att köra kommandot på nytt. Vi rekommenderar ett intervall på en sekund eller flera anrop mellan anrop.

Huvud nyckel/värde paret associationer och tabeller finns i `"pageResults"` noden. Om du även har angett oformaterad text extrahering genom *includeTextDetails* URL-parameter, `"readResults"` visar noden innehållet och positionerna för all text i dokumentet.

```json
{
  "analyzeResult":{
    "readResults":[
      {
        "page":1,
        "width":8.5,
        "height":11.0,
        "angle":0,
        "unit":"inch",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              0.5278,
              1.0597,
              1.4569,
              1.0597,
              1.4569,
              1.4347,
              0.5278,
              1.4347
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  0.5278,
                  1.0597,
                  1.4569,
                  1.0597,
                  1.4569,
                  1.4347,
                  0.5278,
                  1.4347
                ]
              }
            ]
          },
          ...
          {
            "text":"PT",
            "boundingBox":[
              6.2181,
              3.3528,
              6.3944,
              3.3528,
              6.3944,
              3.5417,
              6.2181,
              3.5417
            ],
            "words":[
              {
                "text":"PT",
                "boundingBox":[
                  6.2181,
                  3.3528,
                  6.3944,
                  3.3528,
                  6.3944,
                  3.5417,
                  6.2181,
                  3.5417
                ]
              }
            ]
          }
        ]
      }
    ],
    "version":"2.0.0",
    "errors":[

    ],
    "documentResults":[

    ],
    "pageResults":[
      {
        "page":1,
        "clusterId":1,
        "keyValuePairs":[
          {
            "key":{
              "text":"Address:",
              "boundingBox":[
                0.7972,
                1.5125,
                1.3958,
                1.5125,
                1.3958,
                1.6431,
                0.7972,
                1.6431
              ],
              "elements":[
                "#/readResults/0/lines/1/words/0"
              ]
            },
            "value":{
              "text":"1 Redmond way Suite 6000 Redmond, WA 99243",
              "boundingBox":[
                0.7972,
                1.6764,
                2.15,
                1.6764,
                2.15,
                2.2181,
                0.7972,
                2.2181
              ],
              "elements":[
                "#/readResults/0/lines/4/words/0",
                "#/readResults/0/lines/4/words/1",
                "#/readResults/0/lines/4/words/2",
                "#/readResults/0/lines/4/words/3",
                "#/readResults/0/lines/6/words/0",
                "#/readResults/0/lines/6/words/1",
                "#/readResults/0/lines/6/words/2",
                "#/readResults/0/lines/8/words/0"
              ]
            },
            "confidence":0.86
          },
          {
            "key":{
              "text":"Invoice For:",
              "boundingBox":[
                4.3903,
                1.5125,
                5.1139,
                1.5125,
                5.1139,
                1.6431,
                4.3903,
                1.6431
              ],
              "elements":[
                "#/readResults/0/lines/2/words/0",
                "#/readResults/0/lines/2/words/1"
              ]
            },
            "value":{
              "text":"Microsoft 1020 Enterprise Way Sunnayvale, CA 87659",
              "boundingBox":[
                5.1917,
                1.4458,
                6.6583,
                1.4458,
                6.6583,
                2.0347,
                5.1917,
                2.0347
              ],
              "elements":[
                "#/readResults/0/lines/3/words/0",
                "#/readResults/0/lines/5/words/0",
                "#/readResults/0/lines/5/words/1",
                "#/readResults/0/lines/5/words/2",
                "#/readResults/0/lines/7/words/0",
                "#/readResults/0/lines/7/words/1",
                "#/readResults/0/lines/7/words/2"
              ]
            },
            "confidence":0.86
          },
          ...
        ],
        "tables":[
          {
            "caption":null,
            "rows":2,
            "columns":5,
            "cells":[
              {
                "rowIndex":0,
                "colIndex":0,
                "header":true,
                "text":"Invoice Number",
                "boundingBox":[
                  0.5347,
                  2.8722,
                  1.575,
                  2.8722,
                  1.575,
                  3.0028,
                  0.5347,
                  3.0028
                ],
                "elements":[
                  "#/readResults/0/lines/9/words/0",
                  "#/readResults/0/lines/9/words/1"
                ]
              },
              {
                "rowIndex":0,
                "colIndex":1,
                "header":true,
                "text":"Invoice Date",
                "boundingBox":[
                  1.9403,
                  2.8722,
                  2.7569,
                  2.8722,
                  2.7569,
                  3.0028,
                  1.9403,
                  3.0028
                ],
                "elements":[
                  "#/readResults/0/lines/10/words/0",
                  "#/readResults/0/lines/10/words/1"
                ]
              },
              {
                "rowIndex":0,
                "colIndex":2,
                "header":true,
                "text":"Invoice Due Date",
                "boundingBox":[
                  3.3403,
                  2.8722,
                  4.4583,
                  2.8722,
                  4.4583,
                  3.0028,
                  3.3403,
                  3.0028
                ],
                "elements":[
                  "#/readResults/0/lines/11/words/0",
                  "#/readResults/0/lines/11/words/1",
                  "#/readResults/0/lines/11/words/2"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  },
  "lastUpdatedDateTime":"2019-10-07T19:32:18+00:00",
  "status":"succeeded",
  "createdDateTime":"2019-10-07T19:32:15+00:00"
}
```

## <a name="improve-results"></a>Förbättra resultaten

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du formulär tolken REST API med sväng för att träna en modell och köra den i ett exempel scenario. Sedan läser du referens dokumentationen för att utforska formulärets tolknings-API i större djup.

> [!div class="nextstepaction"]
> [REST API referens dokumentation](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
