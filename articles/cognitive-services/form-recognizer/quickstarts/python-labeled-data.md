---
title: 'Snabb start: träna med etiketter med hjälp av REST API och python-formulär tolken'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder funktionen för formulär igenkänning med etiketten data med REST API och python för att träna en anpassad modell.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 653b3dedcf969c611afa4c81cc5268c43020a7e7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88517786"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>Träna en formulär igenkännings modell med etiketter med hjälp av REST API och python

I den här snabb starten använder du formulär tolken REST API med python för att träna en anpassad modell med manuellt märkta data. Mer information om den här funktionen finns i avsnittet [träna med etiketter](../overview.md#train-with-labels) i översikten.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten måste du ha:
- [Python](https://www.python.org/downloads/) installerat (om du vill köra exemplet lokalt).
- En uppsättning av minst sex formulär av samma typ. Du använder dessa data för att träna modellen och testa ett formulär. Du kan använda en [exempel data uppsättning](https://go.microsoft.com/fwlink/?linkid=2090451) för den här snabb starten. Ladda upp utbildnings-filerna till roten för en Blob Storage-behållare i ett Azure Storage konto med standard prestanda nivå.

> [!NOTE]
> I den här snabb starten används fjärrdokument som används av URL: en. Om du vill använda lokala filer i stället, se [referens dokumentationen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync).

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulär igenkännings resurs

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>Konfigurera tränings data

Härnäst måste du konfigurera nödvändiga indata. Funktionen märkta data har särskilda inmatnings krav utöver vad som behövs för att träna en anpassad modell utan etiketter.

Kontrol lera att alla utbildnings dokument har samma format. Om du har formulär i flera format, sorterar du dem i undermappar baserat på vanligt format. När du tränar måste du dirigera API: et till en undermapp.

För att kunna träna en modell med märkta data behöver du följande filer som indata i undermappen. Du får lära dig hur du skapar dessa filer nedan.

* **Käll formulär** – formulär att extrahera data från. De typer som stöds är JPEG, PNG, PDF eller TIFF.
* **OCR** -presentationsfiler – dessa är JSON-filer som beskriver storlekarna och positionerna för all läsbar text i varje käll formulär. Du använder formatet för formulär tolkens layout för att skapa dessa data. 
* **Etikettfiler** – dessa är JSON-filer som beskriver de data etiketter som en användare har angett manuellt.

Alla dessa filer bör ha samma undermapp och ha följande format:

* input_file1.pdf 
* input_file1.pdf.ocr.jspå
* input_file1.pdf.labels.jspå 
* input_file2.pdf 
* input_file2.pdf.ocr.jspå
* input_file2.pdf.labels.jspå
* ...

> [!TIP]
> När du etiketterar formulär med hjälp av formulär tolkens [exempel etikett verktyg](./label-tool.md)skapar verktyget dessa etiketter och OCR-filer automatiskt.

### <a name="create-the-ocr-output-files"></a>Skapa filer för OCR-utdata

Du behöver filer med OCR-resultat för att tjänsten ska kunna beakta motsvarande indatafiler för etiketterad utbildning. Följ stegen nedan om du vill hämta OCR-resultat för ett angivet käll formulär:

1. Anropa API för **[analys av layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** i Read layout-behållaren med indatafilen som en del av begär ande texten. Spara ID: t som finns i svarets **Åtgärds plats** rubrik.
1. Anropa API: t för att **[analysera layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** med ÅTGÄRDS-ID från föregående steg.
1. Hämta svaret och skriv innehållet till en fil. För varje käll formulär ska motsvarande OCR-fil ha det ursprungliga fil namnet bifogad med `.ocr.json` . OCR-JSON-utdata ska ha följande format. I [exempel-OCR-filen](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json) finns ett fullständigt exempel. 

    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
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
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```

### <a name="create-the-label-files"></a>Skapa etikettfiler

Etikettfiler innehåller nyckel värdes kopplingar som en användare har angett manuellt. De behövs för etiketterad data träning, men alla källfiler måste ha motsvarande etikett fil. Källfiler utan etiketter kommer att behandlas som vanliga utbildnings dokument. Vi rekommenderar fem eller fler märkta filer för tillförlitlig utbildning. Du kan använda ett GRÄNSSNITTs verktyg som [exempel verktyget](./label-tool.md) för att generera dessa filer.

När du skapar en etikett fil kan du välja att ange exakta regioner &mdash; för värdena i dokumentet. Detta ger inlärningen ännu högre noggrannhet. Regionerna är formaterade som en uppsättning av åtta värden som motsvarar fyra X, Y-koordinater: övre vänstra, övre högra, nedre högra och nedre vänstra. Koordinaternas värden är mellan noll och ett, skalas till sidans dimensioner.

För varje käll formulär bör motsvarande etikett fil ha det ursprungliga fil namnet bifogad med `.labels.json` . Etikett filen ska ha följande format. Se [exempel etikett filen](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json) för ett fullständigt exempel.

```json
{
    "document": "Invoice_1.pdf",
    "labels": [
        {
            "label": "Provider",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Contoso",
                    "boundingBoxes": [
                        [
                            0.06334117647058823,
                            0.1053,
                            0.17018823529411767,
                            0.1053,
                            0.17018823529411767,
                            0.12303636363636362,
                            0.06334117647058823,
                            0.12303636363636362
                        ]
                    ]
                }
            ]
        },
        {
            "label": "For",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Microsoft",
                    "boundingBoxes": [
                        [
                            0.6122941176470589,
                            0.1374,
                            0.6841764705882353,
                            0.1374,
                            0.6841764705882353,
                            0.14682727272727272,
                            0.6122941176470589,
                            0.14682727272727272
                        ]
                    ]
                },
                {
                    "page": 1,
                    "text": "1020",
                    "boundingBoxes": [
                        [
                            0.6121882352941176,
                            0.156,
                            0.6462941176470588,
                            0.156,
                            0.6462941176470588,
                            0.1653181818181818,
                            0.6121882352941176,
                            0.1653181818181818
                        ]
                    ]
                },
                ...
```

> [!IMPORTANT]
> Du kan bara använda en etikett för varje text element och varje etikett kan bara tillämpas en gång per sida. Du kan inte använda en etikett på flera sidor.


## <a name="train-a-model-using-labeled-data"></a>Träna en modell med hjälp av märkta data

Om du vill träna en modell med märkta data anropar du det **[anpassade modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** -API: et för träna genom att köra följande python-kod. Innan du kör koden gör du följande ändringar:

1. Ersätt `<Endpoint>` med slut punkts-URL: en för formulär igenkännings resursen.
1. Ersätt `<SAS URL>` med Azure Blob Storage-behållarens URL för signatur för delad åtkomst (SAS). Hämta SAS-URL: en genom att öppna Microsoft Azure Storage Explorer, högerklicka på behållaren och välja **Hämta signatur för delad åtkomst**. Kontrol lera att **Läs** -och **list** behörigheterna är markerade och klicka på **skapa**. Kopiera sedan värdet i **URL** -avsnittet. Den bör ha formatet: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
1. Ersätt `<Blob folder name>` med mappnamnet i BLOB-behållaren där indata finns. Om dina data finns i roten lämnar du detta tomt och tar bort `"prefix"` fältet från bröd texten i HTTP-begäran.

```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```

## <a name="get-training-results"></a>Hämta utbildnings resultat

När du har startat träna-åtgärden använder du det returnerade ID: t för att hämta status för åtgärden. Lägg till följande kod längst ned i python-skriptet. Detta använder ID-värdet från inlärnings anropet i ett nytt API-anrop. Inlärnings åtgärden är asynkron, så det här skriptet anropar API: n med jämna mellanrum tills inlärnings statusen har slutförts. Vi rekommenderar ett intervall på en sekund.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = headers)
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
            quit()
        model_status = resp_json["modelInfo"]["status"]
        if model_status == "ready":
            print("Training succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if model_status == "invalid":
            print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
            quit()
        # Training still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET model failed:\n%s" % str(e)
        print(msg)
        quit()
print("Train operation did not complete within the allocated time.")
```

När övnings processen har slutförts får du ett `201 (Success)` svar med JSON-innehåll som följande. Svaret har förkort ATS för enkelhetens skull.

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

Kopiera `"modelId"` värdet för användning i följande steg.

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

När processen har slutförts får du ett `202 (Success)` svar med JSON-innehåll i följande format. Svaret har förkort ATS för enkelhetens skull. Huvud nyckel/värde-associationerna finns i `"documentResults"` noden. Layout-API-resultatet (innehållet och positionerna för all text i dokumentet) finns i `"readResults"` noden.

```json
{ 
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
                0.5375,
                1.1349,
                2.6064,
                1.1349,
                2.6064,
                1.354,
                0.5375,
                1.354
              ],
              "text":"Contoso Suites",
              "words":[ 
                { 
                  "boundingBox":[ 
                    0.5375,
                    1.1402,
                    1.6595,
                    1.1402,
                    1.6595,
                    1.354,
                    0.5375,
                    1.354
                  ],
                  "text":"Contoso",
                  "confidence":1
                },
                { 
                  "boundingBox":[ 
                    1.758,
                    1.1349,
                    2.6064,
                    1.1349,
                    2.6064,
                    1.3534,
                    1.758,
                    1.3534
                  ],
                  "text":"Suites",
                  "confidence":1
                }
              ]
            },
            ...
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
                    "#/readResults/0/lines/7/words/0",
                    "#/readResults/0/lines/7/words/1"
                  ]
                },
                { 
                  "rowIndex":0,
                  "columnIndex":1,
                  "text":"Invoice Date",
                  "boundingBox":[ 
                    1.9061,
                    2.8088,
                    3.3074,
                    2.8088,
                    3.3074,
                    3.3219,
                    1.9061,
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
      ],
      "documentResults":[ 
        { 
          "docType":"Analyze",
          "pageRange":[ 
            1,
            1
          ],
          "fields":{ 
            "total":{ 
              "type":"string",
              "valueString":"$22,123.24",
              "text":"$22,123.24",
              "boundingBox":[ 
                5.29,
                3.41,
                5.9750000000000009,
                3.41,
                5.9750000000000009,
                3.54,
                5.29,
                3.54
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                "#/analyzeResult/readResults/0/lines/15/words/0"
              ]
            },
            "invoice #":{ 
              "type":"string",
              "valueString":"7689302",
              "text":"7689302",
              "boundingBox":[ 
                0.54,
                3.41,
                1.065,
                3.41,
                1.065,
                3.515,
                0.54,
                3.515
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                "#/analyzeResult/readResults/0/lines/12/words/0"
              ]
            },
            "vat":{ 
              "type":"string",
              "valueString":"QR",
              "text":"QR",
              "boundingBox":[ 
                6.2250000000000009,
                3.41,
                6.425,
                3.41,
                6.425,
                3.52,
                6.2250000000000009,
                3.52
              ],
              "page":1,
              "confidence":0.9839357733726502,
              "elements":[ 
                "#/analyzeResult/readResults/0/lines/16/words/0"
              ]
            },
            ...
          }
        }
      ]
    },
    "status":"succeeded",
    "createdDateTime":"2019-11-12T21:26:19+00:00",
    "lastUpdatedDateTime":"2019-11-12T21:27:27.0488571+00:00"
}
```

## <a name="improve-results"></a>Förbättra resultaten

Undersök `"confidence"` värdena för varje nyckel/värde-resultat under `"documentResults"` noden. Du bör också titta på förtroende poängen i `"readResults"` noden, som motsvarar layout åtgärden. Resultatet av layouten påverkar inte säkerheten för extrahering av nyckel/värde, så du bör kontrol lera båda.
* Om förtroende poängen för layouten är låg, försöker du förbättra kvaliteten på dina inaktuella dokument (se [krav](../overview.md#input-requirements)för inläsning).
* Om förtroende poängen för extraherings åtgärden för nyckel/värde är låg, se till att dokumenten som analyseras är av samma typ som dokumenten som används i inlärnings uppsättningen. Om dokumenten i inlärnings uppsättningen har variationer i utseendet kan du överväga att dela upp dem i olika mappar och träna en modell för varje variation.

### <a name="avoid-cluttered-labels"></a>Undvik röriga etiketter

Ibland när du använder olika etiketter inom samma textrad, kan tjänsten sammanfoga dessa etiketter till ett fält. I en adress kan du t. ex. namnge ort, delstat och post nummer som olika fält, men under förutsägelse de fälten identifieras inte separat.

Vi förstår att det här scenariot är viktigt för våra kunder och vi arbetar på att förbättra det i framtiden. För närvarande rekommenderar vi våra användare att märka flera färdiga fält som ett fält och sedan separera villkoren i en efter bearbetning av extraherings resultatet.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder formulär tolken REST API med python för att träna en modell med manuellt märkta data. Sedan läser du API-referensens dokumentation för att utforska formulärets tolknings-API i större djup.

> [!div class="nextstepaction"]
> [REST API referens dokumentation](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
