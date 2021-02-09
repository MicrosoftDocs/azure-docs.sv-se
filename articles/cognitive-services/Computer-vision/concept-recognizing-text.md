---
title: Optisk tecken läsning (OCR) – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp som rör optisk tecken läsning (OCR) av bilder och dokument med utskriven text och handskriven text med hjälp av API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 1d633b1a9f5fee0a5cceb48f2b37aaec2092069f
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979543"
---
# <a name="optical-character-recognition-ocr"></a>Optisk teckenläsning (OCR)

Azures API för visuellt innehåll innehåller OCR-funktioner (optisk tecken läsning) som extraherar tryckt eller handskriven text från bilder. Du kan extrahera text från bilder, till exempel foton av licens plattor eller behållare med serie nummer, samt från dokument fakturor, fakturor, ekonomiska rapporter, artiklar med mera.

## <a name="read-api"></a>Läs-API 

Visuellt innehåll [Read API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) är Azures senaste OCR-teknik ([Läs om vad som är nytt](./whats-new.md)) som extraherar utskriven text (på flera språk), handskriven text (endast engelska), siffror och valuta symboler från bilder och PDF-dokument med flera sidor. Det är optimerat för att extrahera text från text – tunga bilder och PDF-dokument med flera sidor med blandade språk. Det stöder identifiering av både skriven och handskriven text i samma bild eller dokument.

![Hur OCR konverterar bilder och dokument till strukturerade utdata med extraherad text](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Krav för indatamängd
**Läs** anropet tar bilder och dokument som inaktuella. De har följande krav:

* Fil format som stöds: JPEG, PNG, BMP, PDF och TIFF
* För PDF-och TIFF-filer, bearbetas upp till 2000 sidor (endast de två första sidorna för den kostnads fria nivån).
* Fil storleken måste vara mindre än 50 MB (4 MB för den kostnads fria nivån) och dimensioner minst 50 x 50 bild punkter och högst 10000 x 10000 bild punkter. 
* PDF-dimensionerna måste bestå av högst 17 × 17 tum, som motsvarar legal eller a3 pappers storlekar och mindre.

> [!NOTE]
> **Språk information** 
>
> [Read-anropet](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) har en valfri begär ande parameter för språk. Läs stöder automatisk språk identifiering och flerspråkiga dokument, så du behöver bara ange en språkkod om du vill tvinga dokumentet att bearbetas som det specifika språket.

## <a name="ocr-demo-examples"></a>OCR-demo (exempel)

![OCR-demonstrationer](./Images/ocr-demo.gif)

## <a name="step-1-the-read-operation"></a>Steg 1: Läs åtgärden

Läsnings-API: s [Läs anrop](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) tar en bild eller ett PDF-dokument som indata och extraherar text asynkront. Anropet returnerar med ett svars huvud fält som kallas `Operation-Location` . `Operation-Location`Värdet är en URL som innehåller det åtgärds-ID som ska användas i nästa steg.

|Svars huvud| Resultat-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> Sidan [visuellt innehåll prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) innehåller pris nivån för läsning. Varje analyserad bild eller sida är en transaktion. Om du anropar åtgärden med ett PDF-eller TIFF-dokument som innehåller 100 sidor, räknas den som 100 transaktioner och du faktureras för 100 transaktioner. Om du gjorde 50 anrop till åtgärden och varje anrop skickade ett dokument med 100 sidor debiteras du för 50 X 100 = 5000 transaktioner.

## <a name="step-2-the-get-read-results-operation"></a>Steg 2: Åtgärden hämta Läs resultat

Det andra steget är att anropa åtgärden [Hämta Läs resultat](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) . Den här åtgärden utförs som indatamängden för det åtgärds-ID som skapades av Läs åtgärden. Den returnerar ett JSON-svar som innehåller ett **status** fält med följande möjliga värden. Du anropar den här åtgärden iterativt tills den returnerar värdet **lyckades** . Använd ett intervall på 1 till 2 sekunder för att undvika att överskrida antalet begär Anden per sekund (RPS).

|Fält| Typ | Möjliga värden |
|:-----|:----:|:----|
|status | sträng | notStarted: åtgärden har inte startats. |
| |  | körs: åtgärden bearbetas. |
| |  | misslyckades: åtgärden misslyckades. |
| |  | lyckades: åtgärden har slutförts. |

> [!NOTE]
> Den kostnads fria nivån begränsar begär ande frekvensen till 20 anrop per minut. På den betalda nivån tillåts 10 begär Anden per sekund (RPS) som kan ökas på begäran. Använd support kanalen för Azure eller ditt konto team för att begära en högre RPS-taxa (Request/Second).

När fältet **status** har värdet **lyckades** innehåller JSON-svaret det extraherade text innehållet från din avbildning eller ditt dokument. JSON-svaret underhåller de ursprungliga rad grupperna av identifierade ord. Den innehåller de extraherade text raderna och deras avgränsnings Rute koordinater. Varje textrad innehåller alla extraherade ord med deras koordinater och förtroende poäng.

> [!NOTE]
> De data som skickas till `Read` åtgärden krypteras tillfälligt och lagras i vila under en kort tid och tas sedan bort. På så sätt kan dina program hämta den extraherade texten som en del av tjänst svaret.

## <a name="sample-json-output"></a>Exempel på JSON-utdata

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

## <a name="natural-reading-order-output"></a>Naturlig Läs ordning för utdata
Med [läsa 3,2 för hands versions-API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005)anger du i vilken ordning text raderna ska matas med `readingOrder` Frågeparametern. Använd `natural` för att se en mer användarvänlig läsnings ordning enligt följande exempel.

:::image border type="content" source="./Images/ocr-reading-order-example.png" alt-text="Exempel på OCR-Läs ordning":::

## <a name="handwritten-classification-for-text-lines-latin-only"></a>Handskriven klassificering för text rader (endast latinsk)
I API-svaret för [Read 3,2-förhands granskning](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005) ingår att klassificera om varje textrad är av hand SKRIFTS format eller inte, tillsammans med en förtroende poäng. Den här funktionen stöds bara för latinska språk. I följande exempel visas den handskrivna klassificeringen för texten i bilden.

:::image border type="content" source="./Images/ocr-handwriting-classification.png" alt-text="Exempel på klassificering av OCR-handstil":::

## <a name="select-pages-or-page-ranges-for-text-extraction"></a>Välj sidor eller sid intervall för text extrahering
Med [läsa 3,2 för hands versions-API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005), för stora flersidiga dokument, använder du `pages` Frågeparametern för att ange sid nummer eller sid intervall för att extrahera text från de sidorna. I följande exempel visas ett dokument med 10 sidor, med text som extraherats för båda fallen – alla sidor (1-10) och valda sidor (3-6).

:::image border type="content" source="./Images/ocr-select-pages.png" alt-text="Valda sidor-utdata":::

## <a name="supported-languages"></a>Språk som stöds
Läsnings-API: erna stöder totalt 73 språk för utskrifts format text. Se den fullständiga listan över [språk som stöds av OCR](./language-support.md#optical-character-recognition-ocr). OCR av handskriven stil stöds enbart för engelska.

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Använd moln-API eller distribuera lokalt
Moln-API: erna för att läsa 3. x är det bästa alternativet för de flesta kunder på grund av enkel integrering och snabb produktivitet. Azure och Visuellt innehåll service hanterar skalning, prestanda, data säkerhet och efterlevnad för att möta kundernas behov.

För lokal distribution kan du med hjälp av den [skrivskyddade Docker-behållaren (för hands version)](./computer-vision-how-to-install-containers.md) distribuera de nya OCR-funktionerna i din egen lokala miljö. Containrar är bra för specifika säkerhets- och datastyrningskrav.

## <a name="ocr-api"></a>OCR-API

[OCR-API: t](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) använder en äldre igenkännings modell, stöder bara bilder och körs synkront, och returneras omedelbart med den identifierade texten. Se de [språk som stöds för OCR](./language-support.md#optical-character-recognition-ocr) och sedan Read API.

> [!NOTE]
> Datorn Vison 2,0 RecognizeText-åtgärder håller på att bli föråldrad med den nya Read-API: n som beskrivs i den här artikeln. Befintliga kunder ska [övergå till att använda Läs åtgärder](upgrade-api-versions.md).

## <a name="next-steps"></a>Nästa steg

- Kom igång med [snabb starterna för Visuellt innehåll REST API eller klient biblioteket](./quickstarts-sdk/client-library.md).
- Lär dig mer om att [läsa 3,1 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Läs mer om den [offentliga för hands versionen av läs 3,2 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005) med stöd för totalt 73-språk.
