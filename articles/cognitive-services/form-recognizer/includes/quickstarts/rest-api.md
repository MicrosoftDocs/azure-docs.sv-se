---
title: 'Snabb start: formulär igenkännings REST API'
description: Använd formulär igenkännings REST API för att skapa en app för formulär bearbetning som extraherar nyckel/värde-par och tabell data från dina anpassade dokument.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: d93567620dab17d82878548286ddb43413162814
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104761175"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!NOTE]
> Den här guiden använder sväng för att köra REST API-anrop. Det finns även [exempel kod på GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/FormRecognizer/rest) som illustrerar hur du ANROPAr REST-API: er med python.

## <a name="prerequisites"></a>Förutsättningar

* [spiralen](https://curl.haxx.se/windows/) är installerad.
* [PowerShell version 6.0 +](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows)eller liknande kommando rads program.
* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* En Azure Storage-blob som innehåller en uppsättning tränings data. Se [skapa en tränings data uppsättning för en anpassad modell](../../build-training-data-set.md) för tips och alternativ för att sätta samman din tränings data uppsättning. I den här snabb starten kan du använda filerna under mappen **träna** i [exempel data uppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451) (Hämta och extrahera *sample_data.zip*).
* När du har en Azure-prenumeration kan du <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" skapa en formulär igenkännings resurs "  target="_blank"> skapa en formulär igenkännings resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
  * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Forms igenkännings-API: et. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
  * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* En URL för en avbildning av ett kvitto. Du kan använda en [exempel bild](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) för den här snabb starten.
* En URL för en bild av ett visitkort. Du kan använda en [exempel bild](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg) för den här snabb starten.
* En URL för en bild av en faktura. Du kan använda ett [exempel dokument](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) för den här snabb starten.

## <a name="analyze-layout"></a>Analysera layout

Du kan använda formulär igenkänning för att analysera och extrahera tabeller, markerings märken, text och struktur i dokument, utan att behöva träna en modell. Mer information om extrahering av layout finns i [rikt linjer för layout](../../concept-layout.md). Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slut punkt som du fick med din igenkännings prenumeration för formulär.
1. Ersätt `{subscription key}` med den prenumerations nyckel som du kopierade från föregående steg.
1. Ersätt `\"{your-document-url}` med en av exempel-URL: erna.

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

---

Du får ett `202 (Success)` svar som innehåller rubriken för **åtgärden location** . Värdet för den här rubriken innehåller ett åtgärds-ID som du kan använda för att fråga efter statusen för den asynkrona åtgärden och hämta resultatet. I följande exempel är strängen efter `analyzeResults/` Åtgärds-ID: t.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Hämta resultat från layout

När du har anropat API för **[analys av layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)** anropar du API: et för att **[analysera utdata](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult)** för att hämta status för åtgärden och de extraherade data. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slut punkt som du fick med din igenkännings prenumeration för formulär.
1. Ersätt `{subscription key}` med den prenumerations nyckel som du kopierade från föregående steg.
1. Ersätt `{resultId}` med åtgärds-ID: t från föregående steg.
<!-- markdownlint-disable MD024 -->

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-results"></a>Granska resultaten

Du får ett `200 (success)` svar med JSON-innehåll.

Se följande faktura bild och dess motsvarande JSON-utdata.
* `"readResults"`Noden innehåller alla text rader med dess respektive placering på sidan. 
* `"selectionMarks"`Noden (i för hands versionen av v 2.1) visar varje markerings märke (kryss markering) och om dess status är "markerad" eller "omarkerad". 
* I `"pageResults"` avsnittet visas de tabeller som har extraherats. För varje tabell extraheras text-, rad-och kolumn index, rad-och kolumn omfång, avgränsnings ruta och fler.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Contoso Project Statement-dokument med en tabell.":::

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

Den här utmatningen har kortas ned för enkelhetens skull. Se den [fullständiga exempel utmatningen på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

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

Den här utmatningen har kortas ned för enkelhetens skull. Se den [fullständiga exempel utmatningen på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

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

## <a name="analyze-invoices"></a>Analysera fakturor

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

Om du vill börja analysera en faktura använder du kommandot vänd nedan. Mer information om faktura analys finns i [vägledningen för faktura](../../concept-invoices.md). Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slut punkt som du fick med din igenkännings prenumeration för formulär.
1. Ersätt `{your invoice URL}` med URL-adressen för ett faktura dokument.
1. Ersätt `{subscription key}` med din prenumerationsnyckel.

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

Du får ett `202 (Success)` svar som innehåller rubriken för **åtgärden location** . Värdet för den här rubriken innehåller ett åtgärds-ID som du kan använda för att fråga efter statusen för den asynkrona åtgärden och hämta resultatet.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Hämta faktura resultat

När du har anropat API för **[analys av fakturor](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)** anropar du API: et för att **[analysera resultatet](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9acb78c40a2533aee83)** och hämta status för åtgärden och de extraherade data. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slut punkt som du fick med ditt formulärs igenkännings prenumerations nyckel. Du hittar det på fliken **Översikt** i formulärets tolknings resurs.
1. Ersätt `{resultId}` med åtgärds-ID: t från föregående steg.
1. Ersätt `{subscription key}` med din prenumerationsnyckel.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Granska svaret

Du får ett `200 (Success)` svar med JSON-utdata. 
* `"readResults"`Fältet innehåller alla rader med text som har extraherats från fakturan.
* `"pageResults"`Inkluderar de tabeller och markerings märken som extraheras från fakturan.
* `"documentResults"`Fältet innehåller nyckel/värde-information för de mest relevanta delarna av fakturan.

Se följande faktura dokument och dess motsvarande JSON-utdata. 

* [Exempel faktura](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

Det här JSON-innehållet har kortas ned för läsbarhet. Se den [fullständiga exempel utmatningen på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json).

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

## <a name="train-a-custom-model"></a>Träna en anpassad modell

För att träna en anpassad modell behöver du en uppsättning tränings data i en Azure Storage-blob. Du behöver minst fem ifyllda formulär (PDF-dokument och/eller bilder) av samma typ/struktur. Se [skapa en tränings data uppsättning för en anpassad modell](../../build-training-data-set.md) för tips och alternativ för att sätta samman dina tränings data.

Träning utan märkta data är standard åtgärden och är enklare. Alternativt kan du manuellt etikettera en del eller alla dina utbildnings data i förväg. Detta är en mer komplex process men resulterar i en bättre tränad modell.

> [!NOTE]
> Du kan också träna modeller med ett grafiskt användar gränssnitt, t. ex. [formulär tolkens exempel etikett verktyg](../../quickstarts/label-tool.md).


### <a name="train-a-model-without-labels"></a>Träna en modell utan etiketter

Om du vill träna en formulär igenkännings modell med dokumenten i din Azure Blob-behållare anropar du det **[anpassade modell](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync)** -API: et för träna genom att köra följande spiral-kommando. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slut punkt som du fick med din igenkännings prenumeration för formulär.
1. Ersätt `{subscription key}` med den prenumerations nyckel som du kopierade från föregående steg.
1. Ersätt `{SAS URL}` med Azure Blob Storage-behållarens URL för signatur för delad åtkomst (SAS). [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Hämtning av SAS-URL":::

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

---

Du får ett `201 (Success)` svar med ett **plats** huvud. Värdet för den här rubriken är ID: t för den nya modell som tränas.

### <a name="train-a-model-with-labels"></a>Träna en modell med etiketter

För att träna med etiketter måste du ha särskilda etikett informations filer ( `\<filename\>.pdf.labels.json` ) i din Blob Storage-behållare tillsammans med utbildnings dokumenten. [Formulär tolkens exempel etikett verktyg](../../quickstarts/label-tool.md) innehåller ett användar gränssnitt som hjälper dig att skapa etikettfiler. När du har gjort det kan du anropa det **[anpassade modell](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync)** -API: t med- `"useLabelFile"` parametern som anges `true` i JSON-texten.

Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slut punkt som du fick med din igenkännings prenumeration för formulär.
1. Ersätt `{subscription key}` med den prenumerations nyckel som du kopierade från föregående steg.
1. Ersätt `{SAS URL}` med Azure Blob Storage-behållarens URL för signatur för delad åtkomst (SAS). [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Hämtning av SAS-URL":::

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true }"
```

---

Du får ett `201 (Success)` svar med ett **plats** huvud. Värdet för den här rubriken är ID: t för den nya modell som tränas.

### <a name="get-training-results"></a>Hämta utbildnings resultat

När du har startat träna-åtgärden använder du en ny åtgärd, **[hämtar anpassad modell](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModel)** för att kontrol lera kursens status. Skicka modell-ID: t till det här API-anropet för att kontrol lera utbildningens status:

1. Ersätt `{Endpoint}` med den slut punkt som du fick med ditt formulärs igenkännings prenumerations nyckel.
1. Ersätt `{subscription key}` med din prenumerations nyckel
1. Ersätt `{model ID}` med modell-ID: t som du fick i föregående steg

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
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

## <a name="analyze-forms-with-a-custom-model"></a>Analysera formulär med en anpassad modell

Sedan använder du din nya tränade modell för att analysera ett dokument och extrahera nyckel/värde-par och tabeller från det. Anropa **[analys formulärets](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)** API genom att köra följande spiral-kommando. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slut punkt som du fick från ditt formulärs igenkännings prenumerations nyckel. Du hittar det på fliken **Översikt** i formulärets tolknings resurs.
1. Ersätt `{model ID}` med det modell-ID som du fick i föregående avsnitt.
1. Ersätt `{SAS URL}` med en SAS-URL till din fil i Azure Storage. Följ stegen i avsnittet utbildning, men i stället för att få en SAS-URL för hela BLOB-behållaren, hämta en för den aktuella filen som du vill analysera.
1. Ersätt `{subscription key}` med din prenumerationsnyckel.

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

---

Du får ett `202 (Success)` svar med en **Åtgärds plats** rubrik. Värdet för den här rubriken innehåller ett resultat-ID som du använder för att spåra resultatet av analys åtgärden. Spara detta resultat-ID för nästa steg.

### <a name="get-the-analyze-results"></a>Hämta analys resultatet

Anropa anrops-API: et för att **[analysera](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult)** resultatet för att fråga resultatet av analys åtgärden.

1. Ersätt `{Endpoint}` med den slut punkt som du fick från ditt formulärs igenkännings prenumerations nyckel. Du hittar det på fliken **Översikt** i formulärets tolknings resurs.
1. Ersätt `{result ID}` med det ID som du fick i föregående avsnitt.
1. Ersätt `{subscription key}` med din prenumerationsnyckel.

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Du får ett `200 (Success)` svar med en JSON-text i följande format. Utdatan har kort ATS för enkelhetens skull. Lägg märke till `"status"` fältet längst ned. Detta kommer att ha värdet `"succeeded"` när analys åtgärden har slutförts. Om analys åtgärden inte har slutförts måste du fråga tjänsten igen genom att köra kommandot på nytt. Vi rekommenderar ett intervall på en sekund eller flera anrop mellan anrop.

I anpassade modeller som har tränats utan etiketter finns nyckel/värde-paret associationer och tabeller i `"pageResults"` noden i JSON-utdata. I anpassade modeller som har tränats med etiketter finns nyckel-och värdepars kopplingarna i `"documentResults"` noden. Om du även har angett oformaterad text extrahering genom *includeTextDetails* URL-parameter, `"readResults"` visar noden innehållet och positionerna för all text i dokumentet.

Det här exemplet på JSON-utdata har kortas ned för enkelhetens skull. Se den [fullständiga exempel utmatningen på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json).

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

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

## <a name="analyze-receipts"></a>Analysera kvitton

Det här avsnittet visar hur du analyserar och extraherar vanliga fält från amerikanska kvitton med hjälp av en förtränad kvitto modell. Mer information om inleverans analys finns i [Guide för inleveranser konceptuell](../../concept-receipts.md). Du börjar analysera ett kvitto genom att anropa API för att **[analysera kvitto](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync)** med hjälp av kommandot vänd nedan. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slut punkt som du fick med din igenkännings prenumeration för formulär.
1. Ersätt `{your receipt URL}` med URL-adressen för en kvitto avbildning.
1. Ersätt `{subscription key>` med den prenumerations nyckel som du kopierade från föregående steg.

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

---

Du får ett `202 (Success)` svar som innehåller rubriken för **åtgärden location** . Värdet för den här rubriken innehåller ett åtgärds-ID som du kan använda för att fråga efter statusen för den asynkrona åtgärden och hämta resultatet. I följande exempel är strängen efter `operations/` Åtgärds-ID: t.

```console
https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>Hämta kvitto resultat

När du har anropat API för att **analysera kvitto** anropar du API: et för att **[analysera kvitto resultat](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeReceiptResult)** för att hämta status för åtgärden och de extraherade data. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slut punkt som du fick med ditt formulärs igenkännings prenumerations nyckel. Du hittar det på fliken **Översikt** i formulärets tolknings resurs.
1. Ersätt `{operationId}` med åtgärds-ID: t från föregående steg.
1. Ersätt `{subscription key}` med din prenumerationsnyckel.

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-response"></a>Granska svaret

Du får ett `200 (Success)` svar med JSON-utdata. Det första fältet, `"status"` anger status för åtgärden. Om åtgärden inte är slutförd kommer värdet för `"status"` att vara `"running"` eller `"notStarted"` , och du bör anropa API: et igen, antingen manuellt eller genom ett skript. Vi rekommenderar ett intervall på en sekund eller flera anrop mellan anrop.

`"readResults"`Noden innehåller all den identifierade texten (om du anger den valfria *includeTextDetails* -parametern till `true` ). Texten sorteras efter sida, sedan efter rad, sedan efter enskilda ord. `"documentResults"`Noden innehåller de indatavärden som modellen identifierade. Här hittar du användbara nyckel/värde-par som skatt, totalt, handels adress och så vidare.

Se följande kvitto avbildning och dess motsvarande JSON-utdata.

![Ett kvitto från contoso Store](../../media/contoso-allinone.jpg)

Den här utmatningen har trunkerats för läsbarhet. Se den [fullständiga exempel utmatningen på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json).

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

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)  

Det här avsnittet visar hur du analyserar och extraherar vanliga fält från engelska visitkort med en förtränad modell. Mer information om företags korts analys finns i [konceptuell guide för visitkort](../../concept-business-cards.md). Om du vill börja analysera ett visitkort anropar du **[visitkorts](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync)** -API: et med kommandot vändning nedan. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slut punkt som du fick med din igenkännings prenumeration för formulär.
1. Ersätt `{your business card URL}` med URL-adressen för en kvitto avbildning.
1. Ersätt `{subscription key}` med den prenumerations nyckel som du kopierade från föregående steg.

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

Du får ett `202 (Success)` svar som innehåller rubriken för **åtgärden location** . Värdet för den här rubriken innehåller ett åtgärds-ID som du kan använda för att fråga efter statusen för den asynkrona åtgärden och hämta resultatet.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>Hämta resultat från företags kort

När du har anropat API för **analys av visitkort** anropar du API: et för att **[analysera affärskorts resultat](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeBusinessCardResult)** för att hämta status för åtgärden och de extraherade data. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `{Endpoint}` med den slut punkt som du fick med ditt formulärs igenkännings prenumerations nyckel. Du hittar det på fliken **Översikt** i formulärets tolknings resurs.
1. Ersätt `{resultId}` med åtgärds-ID: t från föregående steg.
1. Ersätt `{subscription key}` med din prenumerationsnyckel.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Granska svaret

Du får ett `200 (Success)` svar med JSON-utdata. 

`"readResults"`Noden innehåller all den identifierade texten. Texten sorteras efter sida, sedan efter rad, sedan efter enskilda ord. `"documentResults"`Noden innehåller de företagsspecifika värden som modellen identifierade. Här hittar du användbar kontakt information, till exempel företagets namn, förnamn, efter namn, telefonnummer och så vidare.

![Ett visitkort från contoso Company](../../media/business-card-english.jpg)

Det här exemplet på JSON-utdata har kort ATS för läsbarhet. Se den [fullständiga exempel utmatningen på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json).

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

Skriptet kommer att skriva ut svar till konsolen tills åtgärden **analysera visitkort** slutförs.

### <a name="v20"></a>[v2.0](#tab/v2-0)  

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

---

## <a name="manage-custom-models"></a>Hantera anpassade modeller

### <a name="get-a-list-of-custom-models"></a>Hämta en lista över anpassade modeller

Använd **[listan med anpassade modell](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModels)** -API: er i följande kommando för att returnera en lista över alla anpassade modeller som hör till din prenumeration.

1. Ersätt `{Endpoint}` med den slut punkt som du fick med din igenkännings prenumeration för formulär.
1. Ersätt `{subscription key}` med den prenumerations nyckel som du kopierade från föregående steg.

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

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

Du får ett `200` lyckat svar med JSON-data som följande. `"modelList"`Elementet innehåller alla dina skapade modeller och deras information.

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

### <a name="get-a-specific-model"></a>Hämta en speciell modell

Om du vill hämta detaljerad information om en speciell anpassad modell använder du API: et för **[anpassad modell](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModel)** i följande kommando.

1. Ersätt `{Endpoint}` med den slut punkt som du fick med din igenkännings prenumeration för formulär.
1. Ersätt `{subscription key}` med den prenumerations nyckel som du kopierade från föregående steg.
1. Ersätt `{modelId}` med ID: t för den anpassade modell som du vill söka efter.

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

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

### <a name="delete-a-model-from-the-resource-account"></a>Ta bort en modell från resurs kontot

Du kan också ta bort en modell från ditt konto genom att referera till dess ID. Det här kommandot anropar den **[anpassade modell](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/DeleteCustomModel)** -API: et för att ta bort modellen som används i föregående avsnitt.

1. Ersätt `{Endpoint}` med den slut punkt som du fick med din igenkännings prenumeration för formulär.
1. Ersätt `{subscription key}` med den prenumerations nyckel som du kopierade från föregående steg.
1. Ersätt `{modelId}` med ID: t för den anpassade modell som du vill söka efter.

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Du får ett `204` lyckat svar som anger att din modell har marker ATS för borttagning. Modell artefakter tas bort inom 48 timmar.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du formulär tolken REST API för att träna modeller och analysera formulär på olika sätt. Sedan läser du referens dokumentationen för att utforska formulärets tolknings-API i större djup.

> [!div class="nextstepaction"]
> [REST API referens dokumentation](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)

* [Vad är formigenkänning?](../../overview.md)
