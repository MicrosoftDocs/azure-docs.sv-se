---
title: 'Snabbstart: Formigenkänning REST API'
description: Använd Formigenkänning REST API för att skapa en formulärbearbetningsapp som extraherar nyckel/värde-par och tabelldata från dina anpassade dokument.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: cd9cf0e4a2abec1ad29d5b34f3d6f473ca5bf71d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564658"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!NOTE]
> Den här guiden använder cURL för att köra REST API-anrop.

|[Formigenkänning REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) | [Referens för Azure REST API](/rest/api/azure/)|

## <a name="prerequisites"></a>Förutsättningar

* [cURL](https://curl.haxx.se/windows/) installerat.
* [PowerShell version 6.0+](/powershell/scripting/install/installing-powershell-core-on-windows)eller ett liknande kommandoradsprogram.
* Azure-prenumeration [– Skapa en kostnadsfritt](https://azure.microsoft.com/free/cognitive-services/)
* En Azure Storage blob som innehåller en uppsättning träningsdata. Se [Skapa en träningsdatauppsättning för en anpassad modell för](../../build-training-data-set.md) tips och alternativ för att sätta ihop din träningsdatauppsättning. I den här snabbstarten kan du använda filerna under mappen **Train** (Träna) i [exempeldatauppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451) (ladda ned och extrahera *sample_data.zip*).
* När du har din Azure-prenumeration skapar Formigenkänning resurs en Formigenkänning resurs i Azure Portal för att <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" hämta din nyckel och "  target="_blank"> </a> slutpunkt. När den har distribuerats klickar du **på Gå till resurs**.
  * Du behöver nyckeln och slutpunkten från den resurs som du skapar för att ansluta ditt program till Formigenkänning-API:et. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
  * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och senare uppgradera till en betald nivå för produktion.
* En URL för en bild av ett kvitto. Du kan använda en [exempelavbildning för](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) den här snabbstarten.
* En URL för en bild av ett visitkort. Du kan använda en [exempelavbildning för](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg) den här snabbstarten.
* En URL för en bild av en faktura. Du kan använda ett [exempeldokument för](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) den här snabbstarten.

## <a name="analyze-layout"></a>Analysera layout

Du kan använda Formigenkänning för att analysera och extrahera tabeller, markeringsmarkeringar, text och struktur i dokument, utan att behöva träna en modell. Mer information om layout-extrahering finns i [layoutkonceptuell guide.](../../concept-layout.md) Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slutpunkt som du fick med din Formigenkänning prenumeration.
1. Ersätt `{subscription key}` med prenumerationsnyckeln som du kopierade från föregående steg.
1. Ersätt `\"{your-document-url}` med en av exempel-URL:erna.

### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/v2-1)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

---

Du får ett svar som `202 (Success)` innehåller am **Operation-Location-huvudet.** Värdet för det här huvudet innehåller ett åtgärds-ID som du kan använda för att fråga efter status för den asynkrona åtgärden och hämta resultatet. I följande exempel är strängen efter `analyzeResults/` åtgärds-ID:t.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Hämta layoutresultat

När du har anropat API:et **[Analysera](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)** layout anropar du API:et **[Get Analyze Layout Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult)** för att hämta status för åtgärden och extraherade data. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slutpunkt som du fick med din Formigenkänning prenumeration.
1. Ersätt `{subscription key}` med prenumerationsnyckeln som du kopierade från föregående steg.
1. Ersätt `{resultId}` med åtgärds-ID:t från föregående steg.
<!-- markdownlint-disable MD024 -->

### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-results"></a>Granska resultaten

Du får ett svar `200 (success)` med JSON-innehåll.

Se följande fakturabild och dess motsvarande JSON-utdata.
* Noden `"readResults"` innehåller varje textrad med respektive placering av en avgränsare på sidan.
* Noden `"selectionMarks"` (i v2.1 preview) visar varje markering (kryssruta, radiomarkering) och om dess status är "selected" eller "unselected".
* Avsnittet `"pageResults"` innehåller de tabeller som extraheras. För varje tabell extraheras text, rad- och kolumnindex, rad- och kolumnintervall, en begränsningsruta med mera.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Contosos projektutdragsdokument med en tabell.":::

### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/v2-1)

Dessa utdata har förkortats för enkelhetens skull. Se fullständiga [exempelutdata på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

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

### <a name="v20"></a>[v2.0](#tab/v2-0)

Dessa utdata har förkortats för enkelhetens skull. Se fullständiga [exempelutdata på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

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

---

## <a name="analyze-receipts"></a>Analysera kvitton

Det här avsnittet visar hur du analyserar och extraherar vanliga fält från amerikanska kvitton med hjälp av en förtränad kvittomodell. Mer information om kvittoanalys finns i [konceptuella kvittoguiden](../../concept-receipts.md). Om du vill börja analysera ett kvitto anropar du **[API:et Analyze Receipt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync)** med hjälp av cURL-kommandot nedan. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slutpunkt som du fick med din Formigenkänning prenumeration.
1. Ersätt `{your receipt URL}` med URL-adressen för en kvittobild.
1. Ersätt `{subscription key>` med prenumerationsnyckeln som du kopierade från föregående steg.

### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

---

Du får ett svar som `202 (Success)` innehåller am **Operation-Location-huvudet.** Värdet för det här huvudet innehåller ett åtgärds-ID som du kan använda för att fråga efter status för den asynkrona åtgärden och hämta resultatet. I följande exempel är strängen efter `operations/` åtgärds-ID:t.

```console
https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>Hämta kvittoresultatet

När du har anropat API:et **Analysera** kvitto anropar du API:et **[Get Analyze Receipt Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeReceiptResult)** för att hämta status för åtgärden och extraherade data. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slutpunkt som du fick med din Formigenkänning prenumerationsnyckel. Du hittar den på fliken Formigenkänning **resursöversikt.**
1. Ersätt `{operationId}` med åtgärds-ID:t från föregående steg.
1. Ersätt `{subscription key}` med din prenumerationsnyckel.

### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-response"></a>Granska svaret

Du får ett svar med `200 (Success)` JSON-utdata. Det första `"status"` fältet, , anger status för åtgärden. Om åtgärden inte slutförs blir värdet eller , och du bör anropa API:et `"status"` `"running"` `"notStarted"` igen, antingen manuellt eller via ett skript. Vi rekommenderar ett intervall på en sekund eller mer mellan anrop.

Noden `"readResults"` innehåller all tolkad text (om du anger den valfria *includeTextDetails-parametern* till `true` ). Text ordnas efter sida, sedan efter rad och sedan efter enskilda ord. Noden `"documentResults"` innehåller de kvittospecifika värden som modellen identifierade. Här hittar du användbara nyckel/värde-par som skatt, summa, säljadress och så vidare.

Se följande kvittobild och dess motsvarande JSON-utdata.

![Ett kvitto från Contoso-butiken](../../media/contoso-allinone.jpg)

Dessa utdata har förkortats för läsbarhet. Se fullständiga [exempelutdata på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json).

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.1.0",
    "readResults":[
      {
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[
      {
        "docType":"prebuilt:receipt",
        "pageRange":[
          1,
          1
        ],
        "fields":{
          "ReceiptType":{
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{
            "type":"array",
            "valueArray":[
              {
                "type":"object",
                "valueObject":{
                  "Quantity":{
                    "type":"number",
                    "text":"1",
                    "boundingBox":[
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="analyze-business-cards"></a>Analysera visitkort

### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/v2-1)

Det här avsnittet visar hur du analyserar och extraherar vanliga fält från engelska visitkort med hjälp av en förtränad modell. Mer information om visitkortsanalys finns i den [konceptuella handboken för visitkort.](../../concept-business-cards.md) Om du vill börja analysera ett visitkort anropar du API:et **[Analysera](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync)** visitkort med hjälp av cURL-kommandot nedan. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slutpunkt som du fick med din Formigenkänning prenumeration.
1. Ersätt `{your business card URL}` med URL-adressen för en kvittobild.
1. Ersätt `{subscription key}` med prenumerationsnyckeln som du kopierade från föregående steg.

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

Du får ett svar som `202 (Success)` innehåller am **Operation-Location-huvudet.** Värdet för den här rubriken innehåller ett åtgärds-ID som du kan använda för att fråga efter status för den asynkrona åtgärden och hämta resultatet.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>Hämta visitkortsresultat

När du har anropat API:et **Analysera** visitkort anropar du API:et **[Get Analyze Business Card Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeBusinessCardResult)** (Hämta analys av visitkortsresultat) för att hämta status för åtgärden och extraherade data. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slutpunkt som du fick med din Formigenkänning prenumerationsnyckel. Du hittar den på fliken Formigenkänning **resursöversikt.**
1. Ersätt `{resultId}` med åtgärds-ID:t från föregående steg.
1. Ersätt `{subscription key}` med din prenumerationsnyckel.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Granska svaret

Du får ett svar med `200 (Success)` JSON-utdata.

Noden `"readResults"` innehåller all tolkad text. Text ordnas efter sida, sedan efter rad och sedan efter enskilda ord. Noden `"documentResults"` innehåller de visitkortsspecifika värden som modellen identifierade. Här hittar du användbar kontaktinformation som företagsnamn, förnamn, efternamn, telefonnummer och så vidare.

![Ett visitkort från Contoso-företaget](../../media/business-card-english.jpg)

Det här JSON-exempelutdata har förkortats för läsbarhet. Se fullständiga [exempelutdata på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json).

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Skriptet skriver ut svar till konsolen tills **åtgärden Analysera visitkort** har slutförts.

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

---

## <a name="analyze-invoices"></a>Analysera fakturor

### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/v2-1)

Börja analysera en faktura med hjälp av cURL-kommandot nedan. Mer information om fakturaanalys finns i den [konceptuella guiden Faktura.](../../concept-invoices.md) Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slutpunkt som du fick med din Formigenkänning prenumeration.
1. Ersätt `{your invoice URL}` med URL-adressen för ett fakturadokument.
1. Ersätt `{subscription key}` med din prenumerationsnyckel.

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

Du får ett svar som `202 (Success)` innehåller am **Operation-Location-huvudet.** Värdet för det här huvudet innehåller ett åtgärds-ID som du kan använda för att fråga efter status för den asynkrona åtgärden och hämta resultatet.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Hämta fakturaresultat

När du har anropat API:et **[Analysera](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)** faktura anropar du API:et **[Get Analyze Invoice Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9acb78c40a2533aee83)** (Hämta analysera fakturaresultat) för att hämta status för åtgärden och extraherade data. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slutpunkt som du fick med din Formigenkänning prenumerationsnyckel. Du hittar den på fliken Formigenkänning **resursöversikt.**
1. Ersätt `{resultId}` med åtgärds-ID:t från föregående steg.
1. Ersätt `{subscription key}` med din prenumerationsnyckel.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Granska svaret

Du får ett svar med `200 (Success)` JSON-utdata.

* Fältet `"readResults"` innehåller alla textrader som extraherades från fakturan.
* `"pageResults"`innehåller de tabeller och markeringar som extraherats från fakturan.
* Fältet `"documentResults"` innehåller nyckel-/värdeinformation för de mest relevanta delarna av fakturan.

Se följande fakturadokument och dess motsvarande JSON-utdata.

* [Exempelfaktura](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

Det här JSON-innehållet har förkortats för läsbarhet. Se fullständiga [exempelutdata på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json).

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-11-06T23:32:11Z",
    "lastUpdatedDateTime": "2020-11-06T23:32:20Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [{
            "page": 1,
            "angle": 0,
            "width": 8.5,
            "height": 11,
            "unit": "inch"
        }],
        "pageResults": [{
            "page": 1,
            "tables": [{
                "rows": 3,
                "columns": 4,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "QUANTITY",
                    "boundingBox": [0.4953,
                    5.7306,
                    1.8097,
                    5.7306,
                    1.7942,
                    6.0122,
                    0.4953,
                    6.0122]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "DESCRIPTION",
                    "boundingBox": [1.8097,
                    5.7306,
                    5.7529,
                    5.7306,
                    5.7452,
                    6.0122,
                    1.7942,
                    6.0122]
                },
                ...
                ],
                "boundingBox": [0.4794,
                5.7132,
                8.0158,
                5.714,
                8.0118,
                6.5627,
                0.4757,
                6.5619]
            },
            {
                "rows": 2,
                "columns": 6,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "SALESPERSON",
                    "boundingBox": [0.4979,
                    4.963,
                    1.8051,
                    4.963,
                    1.7975,
                    5.2398,
                    0.5056,
                    5.2398]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "P.O. NUMBER",
                    "boundingBox": [1.8051,
                    4.963,
                    3.3047,
                    4.963,
                    3.3124,
                    5.2398,
                    1.7975,
                    5.2398]
                },
                ...
                ],
                "boundingBox": [0.4976,
                4.961,
                7.9959,
                4.9606,
                7.9959,
                5.5204,
                0.4972,
                5.5209]
            }]
        }],
        "documentResults": [{
            "docType": "prebuilt:invoice",
            "pageRange": [1,
            1],
            "fields": {
                "AmountDue": {
                    "type": "number",
                    "valueNumber": 610,
                    "text": "$610.00",
                    "boundingBox": [7.3809,
                    7.8153,
                    7.9167,
                    7.8153,
                    7.9167,
                    7.9591,
                    7.3809,
                    7.9591],
                    "page": 1,
                    "confidence": 0.875
                },
                "BillingAddress": {
                    "type": "string",
                    "valueString": "123 Bill St, Redmond WA, 98052",
                    "text": "123 Bill St, Redmond WA, 98052",
                    "boundingBox": [0.594,
                    4.3724,
                    2.0125,
                    4.3724,
                    2.0125,
                    4.7125,
                    0.594,
                    4.7125],
                    "page": 1,
                    "confidence": 0.997
                },
                "BillingAddressRecipient": {
                    "type": "string",
                    "valueString": "Microsoft Finance",
                    "text": "Microsoft Finance",
                    "boundingBox": [0.594,
                    4.1684,
                    1.7907,
                    4.1684,
                    1.7907,
                    4.2837,
                    0.594,
                    4.2837],
                    "page": 1,
                    "confidence": 0.998
                },
                ...
            }
        }]
    }
}
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

---

## <a name="analyze-id-document"></a>Analysera ID-dokument

### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/v2-1)

Börja analysera ett identifieringsdokument med hjälp av cURL-kommandot nedan. Mer information om identitetsdokumentanalys finns i [begreppsguide för identitetsdokument.](../../concept-identification-cards.md) Om du vill börja analysera ett identitetsdokument anropar du API:et **[Analysera ID-dokument] https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7738978e467c5fb8707)** med hjälp av cURL-kommandot nedan. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{endpoint}` med den slutpunkt som du fick med din Formigenkänning prenumeration.
1. Ersätt `{your ID document URL}` med URL-adressen för en kvittobild.
1. Ersätt `{subscription key}` med prenumerationsnyckeln som du kopierade från föregående steg.

### <a name="v21-previewtabv2-1"></a>[v2.1 förhandsversion] (#tab/v2-1

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your  ID document URL}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

---

Du får ett svar `202 (Success)` som innehåller rubriken **Operation-Location.** Värdet för den här rubriken innehåller ett resultat-ID som du kan använda för att fråga efter status för den asynkrona åtgärden och hämta resultatet. I följande exempel är strängen efter `analyzeResults/` resultat-ID:t.

```console
https://westus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/0c6cb19e-538f-4b8d-98b7-e105c9995ba6
```

### <a name="get-the-analyze-id-document-result"></a>Hämta resultatet analysera ID-dokument

När du har anropat API:et **Analysera ID-dokument** anropar du API:et Get **[Analyze Id Document Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822)** för att hämta status för åtgärden och extraherade data.  Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{endpoint}` med den slutpunkt som du fick med din Formigenkänning prenumerationsnyckel. Du hittar den på fliken Formigenkänning **resursöversikt.**
1. Ersätt `{resultId}` med åtgärds-ID:t från föregående steg.
1. Ersätt `{subscription key}` med din prenumerationsnyckel.

### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/v2-1)

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Granska svaret

Du får ett svar med `200 (Success)` JSON-utdata. Det första `"status"` fältet, , anger status för åtgärden. Om åtgärden inte slutförs är värdet eller , och du bör anropa API:et igen, antingen manuellt eller via ett skript tills `"status"` `"running"` du får `"notStarted"` `succeeded` värdet. Vi rekommenderar ett intervall på en sekund eller mer mellan anrop.

* Fältet `"readResults"` innehåller varje rad med text som har extraherats från identitetsdokumentet.
* Fältet `"documentResults"` innehåller en matris med objekt som var och en representerar ett ID-dokument som identifierats i indatadokumentet.

Nedan visas ett exempel på ett identitetsdokument och dess motsvarande JSON-utdata

* :::image type="content" source="https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/id-license.jpg" alt-text="exempel på drivrutinslicens":::

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-04-13T17:24:52Z",
    "lastUpdatedDateTime": "2021-04-13T17:24:55Z",
    "analyzeResult": {
      "version": "2.1.0",
      "readResults": [
        {
          "page": 1,
          "angle": -0.2823,
          "width": 450,
          "height": 294,
          "unit": "pixel"
        }
      ],
      "documentResults": [
        {
          "docType": "prebuilt:idDocument:driverLicense",
          "docTypeConfidence": 0.995,
          "pageRange": [
            1,
            1
          ],
          "fields": {
            "Address": {
              "type": "string",
              "valueString": "123 STREET ADDRESS YOUR CITY WA 99999-1234",
              "text": "123 STREET ADDRESS YOUR CITY WA 99999-1234",
              "boundingBox": [
                158,
                151,
                326,
                151,
                326,
                177,
                158,
                177
              ],
              "page": 1,
              "confidence": 0.965
            },
            "Country": {
              "type": "country",
              "valueCountry": "USA",
              "confidence": 0.99
            },
            "DateOfBirth": {
              "type": "date",
              "valueDate": "1958-01-06",
              "text": "01/06/1958",
              "boundingBox": [
                187,
                133,
                272,
                132,
                272,
                148,
                187,
                149
              ],
              "page": 1,
              "confidence": 0.99
            },
            "DateOfExpiration": {
              "type": "date",
              "valueDate": "2020-08-12",
              "text": "08/12/2020",
              "boundingBox": [
                332,
                230,
                414,
                228,
                414,
                244,
                332,
                245
              ],
              "page": 1,
              "confidence": 0.99
            },
            "DocumentNumber": {
              "type": "string",
              "valueString": "LICWDLACD5DG",
              "text": "LIC#WDLABCD456DG",
              "boundingBox": [
                162,
                70,
                307,
                68,
                307,
                84,
                163,
                85
              ],
              "page": 1,
              "confidence": 0.99
            },
            "FirstName": {
              "type": "string",
              "valueString": "LIAM R.",
              "text": "LIAM R.",
              "boundingBox": [
                158,
                102,
                216,
                102,
                216,
                116,
                158,
                116
              ],
              "page": 1,
              "confidence": 0.985
            },
            "LastName": {
              "type": "string",
              "valueString": "TALBOT",
              "text": "TALBOT",
              "boundingBox": [
                160,
                86,
                213,
                85,
                213,
                99,
                160,
                100
              ],
              "page": 1,
              "confidence": 0.987
            },
            "Region": {
              "type": "string",
              "valueString": "Washington",
              "confidence": 0.99
            },
            "Sex": {
              "type": "gender",
              "valueGender": "M",
              "text": "M",
              "boundingBox": [
                226,
                190,
                232,
                190,
                233,
                201,
                226,
                201
              ],
              "page": 1,
              "confidence": 0.99
            }
          }
        }
      ]
    }
  }
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

---

## <a name="train-a-custom-model"></a>Träna en anpassad modell

Om du vill träna en anpassad modell behöver du en uppsättning träningsdata i en Azure Storage blob. Du behöver minst fem ifyllda formulär (PDF-dokument och/eller bilder) av samma typ/struktur. Se [Skapa en träningsdatauppsättning för en anpassad modell för](../../build-training-data-set.md) tips och alternativ för att sätta ihop dina träningsdata.

Träning utan märkta data är standardåtgärden och är enklare. Du kan också manuellt märka vissa eller alla dina träningsdata i förväg. Det här är en mer komplex process men resulterar i en bättre tränad modell.

> [!NOTE]
> Du kan också träna modeller med ett grafiskt användargränssnitt, till exempel [Formigenkänning exempeletikettverktyget](../../quickstarts/label-tool.md).


### <a name="train-a-model-without-labels"></a>Träna en modell utan etiketter

Om du vill träna Formigenkänning modell med dokumenten i din Azure-blobcontainer anropar du API:et **[Train Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync)** genom att köra följande cURL-kommando. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slutpunkt som du fick med din Formigenkänning prenumeration.
1. Ersätt `{subscription key}` med prenumerationsnyckeln som du kopierade från föregående steg.
1. Ersätt `{SAS URL}` med AZURE Blob Storage-containerns URL för signatur för delad åtkomst (SAS). [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="HÄMTNING AV SAS-URL":::

### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

---

Du får ett svar `201 (Success)` med ett **Location-huvud.** Värdet för den här rubriken är ID:t för den nya modell som tränas.

### <a name="train-a-model-with-labels"></a>Träna en modell med etiketter

Om du vill träna med etiketter måste du ha särskilda etikettinformationsfiler ( `\<filename\>.pdf.labels.json` ) i bloblagringscontainern tillsammans med träningsdokumenten. [Exempeletikettverktyget Formigenkänning ett användargränssnitt](../../quickstarts/label-tool.md) som hjälper dig att skapa etikettfilerna. När du har dem kan du anropa API:et **[Train Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync)** (Träna anpassad modell) med `"useLabelFile"` parametern inställd på i `true` JSON-brödtexten.

Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slutpunkt som du fick med din Formigenkänning prenumeration.
1. Ersätt `{subscription key}` med prenumerationsnyckeln som du kopierade från föregående steg.
1. Ersätt `{SAS URL}` med URL:en för Azure Blob Storage-containerns signatur för delad åtkomst (SAS). [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="HÄMTNING AV SAS-URL":::

### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true }"
```

---

Du får ett svar `201 (Success)` med ett **Location-huvud.** Värdet för den här rubriken är ID:t för den nya modellen som tränas.

### <a name="get-training-results"></a>Hämta träningsresultat

När du har startat träningsåtgärden använder du en ny åtgärd, **[Hämta anpassad modell, för](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModel)** att kontrollera träningsstatusen. Skicka modell-ID:t till det här API-anropet för att kontrollera träningsstatusen:

1. Ersätt `{Endpoint}` med den slutpunkt som du fick med din Formigenkänning prenumerationsnyckel.
1. Ersätt `{subscription key}` med din prenumerationsnyckel
1. Ersätt `{model ID}` med modell-ID:t som du fick i föregående steg

### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Du får ett svar `200 (Success)` med en JSON-brödtext i följande format. Lägg märke till `"status"` fältet . Värdet visas när `"ready"` träningen är klar. Om modellen inte har slutfört träningen måste du fråga tjänsten igen genom att köra kommandot igen. Vi rekommenderar ett intervall på en sekund eller flera mellan anrop.

Fältet `"modelId"` innehåller ID:t för den modell som du tränar. Du behöver detta för nästa steg.

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

## <a name="analyze-forms-with-a-custom-model"></a>Analysera formulär med en anpassad modell

Nu ska du använda din nyligen tränade modell för att analysera ett dokument och extrahera nyckel/värde-par och tabeller från den. Anropa **[API:et Analysera](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)** formulär genom att köra följande cURL-kommando. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slutpunkt som du fick från din Formigenkänning prenumerationsnyckel. Du hittar den på fliken Formigenkänning **resursöversikt.**
1. Ersätt `{model ID}` med modell-ID:t som du fick i föregående avsnitt.
1. Ersätt `{SAS URL}` med en SAS-URL till din fil i Azure Storage. Följ stegen i avsnittet Träning, men i stället för att hämta en SAS-URL för hela blobcontainern hämtar du en för den specifika fil som du vill analysera.
1. Ersätt `{subscription key}` med din prenumerationsnyckel.

### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/v2-1)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

---

Du får ett svar `202 (Success)` med rubriken **Operation-Location.** Värdet för den här rubriken innehåller ett resultat-ID som du använder för att spåra resultatet av åtgärden Analysera. Spara det här resultat-ID:t för nästa steg.

### <a name="get-the-analyze-results"></a>Hämta analysresultaten

Anropa **[API:et Get Analyze Form Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult)** (Hämta analysformulärresultat) för att köra frågor mot resultatet av åtgärden Analysera.

1. Ersätt `{Endpoint}` med den slutpunkt som du fick från din Formigenkänning prenumerationsnyckel. Du hittar den på fliken Formigenkänning **resursöversikt.**
1. Ersätt `{result ID}` med det ID som du fick i föregående avsnitt.
1. Ersätt `{subscription key}` med din prenumerationsnyckel.

### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Du får ett svar `200 (Success)` med en JSON-brödtext i följande format. Utdata har förkortats för enkelhetens skull. Lägg märke `"status"` till fältet längst ned. Detta får värdet när `"succeeded"` åtgärden Analysera är klar. Om åtgärden Analysera inte har slutförts måste du fråga tjänsten igen genom att köra kommandot igen. Vi rekommenderar ett intervall på en sekund eller mer mellan anrop.

I anpassade modeller som tränats utan etiketter finns nyckel/värde-parkopplingar och tabeller i `"pageResults"` noden i JSON-utdata. I anpassade modeller som tränats med etiketter finns nyckel/värde-parkopplingarna i `"documentResults"` noden. Om du även har angett extrahering av oformaterad text via URL-parametern *includeTextDetails* visar noden innehållet och positionerna för `"readResults"` all text i dokumentet.

Det här JSON-exempelutdata har förkortats för enkelhetens skull. Se fullständiga [exempelutdata på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json).

### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/v2-1)

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

### <a name="v20"></a>[v2.0](#tab/v2-0)

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

---

### <a name="improve-results"></a>Förbättra resultaten

[!INCLUDE [improve results](../improve-results-unlabeled.md)]

## <a name="manage-custom-models"></a>Hantera anpassade modeller

### <a name="get-a-list-of-custom-models"></a>Hämta en lista över anpassade modeller

Använd **[API:et Lista](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModels)** anpassade modeller i följande kommando för att returnera en lista över alla anpassade modeller som tillhör din prenumeration.

1. Ersätt `{Endpoint}` med den slutpunkt som du fick med din Formigenkänning prenumeration.
1. Ersätt `{subscription key}` med prenumerationsnyckeln som du kopierade från föregående steg.

### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Du får ett `200` lyckat svar med JSON-data som följande. Elementet `"modelList"` innehåller alla dina skapade modeller och deras information.

```json
{
  "summary": {
    "count": 0,
    "limit": 0,
    "lastUpdatedDateTime": "string"
  },
  "modelList": [
    {
      "modelId": "string",
      "status": "creating",
      "createdDateTime": "string",
      "lastUpdatedDateTime": "string"
    }
  ],
  "nextLink": "string"
}
```

### <a name="get-a-specific-model"></a>Hämta en specifik modell

Om du vill hämta detaljerad information om en specifik anpassad modell använder du **[API:et Hämta anpassad](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModel)** modell i följande kommando.

1. Ersätt `{Endpoint}` med den slutpunkt som du fick med din Formigenkänning prenumeration.
1. Ersätt `{subscription key}` med prenumerationsnyckeln som du kopierade från föregående steg.
1. Ersätt `{modelId}` med ID:t för den anpassade modell som du vill söka efter.

### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Du får ett `200` lyckat svar med JSON-data som följande.

```json
{
  "modelInfo": {
    "modelId": "string",
    "status": "creating",
    "createdDateTime": "string",
    "lastUpdatedDateTime": "string"
  },
  "keys": {
    "clusters": {}
  },
  "trainResult": {
    "trainingDocuments": [
      {
        "documentName": "string",
        "pages": 0,
        "errors": [
          "string"
        ],
        "status": "succeeded"
      }
    ],
    "fields": [
      {
        "fieldName": "string",
        "accuracy": 0.0
      }
    ],
    "averageModelAccuracy": 0.0,
    "errors": [
      {
        "message": "string"
      }
    ]
  }
}
```

### <a name="delete-a-model-from-the-resource-account"></a>Ta bort en modell från resurskontot

Du kan också ta bort en modell från ditt konto genom att referera till dess ID. Det här kommandot anropar **[API:et Ta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/DeleteCustomModel)** bort anpassad modell för att ta bort modellen som användes i föregående avsnitt.
kod
1. Ersätt `{Endpoint}` med den slutpunkt som du fick med din Formigenkänning prenumeration.
1. Ersätt `{subscription key}` med prenumerationsnyckeln som du kopierade från föregående steg.
1. Ersätt `{modelId}` med ID:t för den anpassade modell som du vill söka efter.

### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/v2-1)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Du får ett svar `204` som anger att din modell har markerats för borttagning. Modellartefakter tas bort inom 48 timmar.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du Formigenkänning REST API att träna modeller och analysera formulär på olika sätt. Se sedan referensdokumentationen för att utforska Formigenkänning API på djupet.

> [!div class="nextstepaction"]
> [REST API referensdokumentation](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)

* [Vad är formigenkänning?](../../overview.md)
