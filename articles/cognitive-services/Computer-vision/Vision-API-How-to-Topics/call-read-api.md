---
title: Så här anropar du Read API
titleSuffix: Azure Cognitive Services
description: Lär dig att anropa Read API och konfigurera dess beteende i detalj.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: pafarley
ms.openlocfilehash: c1f610a5fb3db41091ddc4d0d921bf1a0cd2cf34
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012236"
---
# <a name="call-the-read-api"></a>Anropa Read API

I den här guiden får du lära dig hur du anropar Read API för att extrahera text från bilder. Du får lära dig de olika sätt som du kan konfigurera beteendet för det här API: et så att det passar dina behov.

Den här guiden förutsätter att du redan har <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" skapat en visuellt innehåll resurs "  target="_blank"> skapa en visuellt innehåll resurs </a> och erhållit en prenumerations nyckel och en slut punkts-URL. Om du inte har gjort det följer du en [snabb start](../quickstarts-sdk/client-library.md) för att komma igång.

## <a name="submit-data-to-the-service"></a>Skicka data till tjänsten

Läsnings-API: s [Läs anrop](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) tar en bild eller ett PDF-dokument som indata och extraherar text asynkront.

`https://{endpoint}/vision/v3.2-preview.3/read/analyze[?language][&pages][&readingOrder]`

Anropet returnerar med ett svars huvud fält som kallas `Operation-Location` . `Operation-Location`Värdet är en URL som innehåller det åtgärds-ID som ska användas i nästa steg.

|Svars huvud| Exempelvärde |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> Sidan [visuellt innehåll prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) innehåller pris nivån för läsning. Varje analyserad bild eller sida är en transaktion. Om du anropar åtgärden med ett PDF-eller TIFF-dokument som innehåller 100 sidor, räknas den som 100 transaktioner och du faktureras för 100 transaktioner. Om du gjorde 50 anrop till åtgärden och varje anrop skickade ett dokument med 100 sidor debiteras du för 50 X 100 = 5000 transaktioner.

## <a name="determine-how-to-process-the-data"></a>Bestämma hur data ska bearbetas

### <a name="language-specification"></a>Språk specifikation

[Read](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) -anropet har en valfri begär ande parameter för språk. Läs stöder automatisk språk identifiering och flerspråkiga dokument, så du behöver bara ange en språkkod om du vill tvinga dokumentet att bearbetas som det specifika språket.

### <a name="natural-reading-order-output-latin-languages-only"></a>Naturlig Läs ordning för utdata (endast latinska språk)
Med [läsa 3,2 för hands versions-API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)kan du ange i vilken ordning text raderna ska matas med `readingOrder` Frågeparametern. Använd `natural` för att se en mer användarvänlig läsnings ordning enligt följande exempel. Den här funktionen stöds bara för latinska språk.

:::image border type="content" source="../Images/ocr-reading-order-example.png" alt-text="Exempel på OCR-Läs ordning":::



### <a name="select-pages-or-page-ranges-for-text-extraction"></a>Välj sidor eller sid intervall för text extrahering
Med [läsa 3,2 för hands versions-API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005), för stora flersidiga dokument, använder du `pages` Frågeparametern för att ange sid nummer eller sid intervall för att extrahera text från de sidorna. I följande exempel visas ett dokument med 10 sidor, med text som extraherats för båda fallen – alla sidor (1-10) och valda sidor (3-6).

:::image border type="content" source="../Images/ocr-select-pages.png" alt-text="Valda sidor-utdata":::

## <a name="get-results-from-the-service"></a>Hämta resultat från tjänsten

Det andra steget är att anropa åtgärden [Hämta Läs resultat](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) . Den här åtgärden utförs som indatamängden för det åtgärds-ID som skapades av Läs åtgärden. 

`https://{endpoint}/vision/v3.2-preview.3/read/analyzeResults/{operationId}`

Den returnerar ett JSON-svar som innehåller ett **status** fält med följande möjliga värden. 

|Värde | Innebörd |
|:-----|:----|
| `notStarted`| Åtgärden har inte startats. |
| `running`| Åtgärden bearbetas. |
| `failed`| Åtgärden misslyckades. |
| `succeeded`| Åtgärden har slutförts. |

Du anropar den här åtgärden iterativt tills den returnerar värdet **lyckades** . Använd ett intervall på 1 till 2 sekunder för att undvika att överskrida antalet begär Anden per sekund (RPS).

> [!NOTE]
> Den kostnads fria nivån begränsar begär ande frekvensen till 20 anrop per minut. På den betalda nivån tillåts 10 begär Anden per sekund (RPS) som kan ökas på begäran. Använd support kanalen för Azure eller ditt konto team för att begära en högre RPS-taxa (Request/Second).

När fältet **status** har `succeeded` värdet, innehåller JSON-svaret det extraherade text innehållet från din avbildning eller ditt dokument. JSON-svaret underhåller de ursprungliga rad grupperna av identifierade ord. Den innehåller de extraherade text raderna och deras avgränsnings Rute koordinater. Varje textrad innehåller alla extraherade ord med deras koordinater och förtroende poäng.

> [!NOTE]
> De data som skickas till `Read` åtgärden krypteras tillfälligt och lagras i vila under en kort tid och tas sedan bort. På så sätt kan dina program hämta den extraherade texten som en del av tjänst svaret.

### <a name="sample-json-output"></a>Exempel på JSON-utdata

Se följande exempel på ett lyckat JSON-svar:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```

### <a name="handwritten-classification-for-text-lines-latin-languages-only"></a>Handskriven klassificering för text rader (endast latinska språk)
I API-svaret för [Read 3,2-förhands granskning](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) ingår att klassificera om varje textrad är av hand SKRIFTS format eller inte, tillsammans med en förtroende poäng. Den här funktionen stöds bara för latinska språk. I följande exempel visas den handskrivna klassificeringen för texten i bilden.

:::image border type="content" source="../Images/ocr-handwriting-classification.png" alt-text="Exempel på klassificering av OCR-handstil":::

## <a name="next-steps"></a>Nästa steg

Om du vill testa REST API går du till [Read API-referensen](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005).