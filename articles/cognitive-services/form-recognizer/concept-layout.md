---
title: Formulär igenkänning för layouter
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som rör layout analyser med formatet tolknings-API-användning och begränsningar.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: a63f910b3a939e33b8c71d8f22d15f6d610a12cc
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845566"
---
# <a name="form-recognizer-layout-service"></a>Formulär tolkens layout-tjänst

Azure formulär tolken kan extrahera text, tabeller, markerings märken och struktur information från dokument med hjälp av dess layout-tjänst. Layout-API: et gör det möjligt för kunderna att ta dokument i en rad olika format och returnera strukturerade data och åter givning av dokumentet. Den kombinerar vår kraftfulla [OCR-kapacitet (optisk tecken läsning)](../computer-vision/concept-recognizing-text.md) med dokument som förstår djup inlärnings modeller för att extrahera text, tabeller, markerings märken och dokument strukturer. 

## <a name="what-does-the-layout-service-do"></a>Vad gör layout tjänsten?

Layout-API: et extraherar text, tabeller, markerings märken och struktur information från dokument med enastående precision och returnerar dem i ett organiserat strukturerat JSON-svar. Dokument kan vara från en rad olika format och kvalitet, inklusive hämtade bilder, skannade dokument och digitala PDF-filer. Layout-API: et extraherar det strukturerade resultatet från alla dessa dokument.

![Layout-exempel](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Prova

Om du vill testa layouten för formulär tolken går du till verktyget online-exempel UI:

> [!div class="nextstepaction"]
> [Exempel-UI](https://fott-preview.azurewebsites.net/)

Du behöver en Azure-prenumeration ([skapa en kostnads fri](https://azure.microsoft.com/free/cognitive-services)) och en resurs slut punkt för [formulär igenkänning](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) och en nyckel för att testa formulär tolkens layout-API. 

![Skärm bild av exempel gränssnitt; text, tabeller och markerings markeringar i ett dokument analyseras](./media/analyze-layout.png)

### <a name="input-requirements"></a>Krav för indatamängd 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>Åtgärden analysera layout

Åtgärden [analysera layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) tar ett dokument (bild-, TIFF-eller PDF-fil) som indata och extraherar texten, tabellerna, markerings märkena och strukturen i dokumentet. Anropet returnerar ett svars huvud fält som kallas `Operation-Location` . `Operation-Location`Värdet är en URL som innehåller det resultat-ID som ska användas i nästa steg.

|Svars huvud| Resultat-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-layout-result-operation"></a>Resultat åtgärden Hämta analys av layout

Det andra steget är att anropa [resultat åtgärden Hämta analys av layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult) . Den här åtgärden tar inmatat till det resultat-ID som skapades av åtgärden analysera layout. Den returnerar ett JSON-svar som innehåller ett **status** fält med följande möjliga värden. 

|Fält| Typ | Möjliga värden |
|:-----|:----:|:----|
|status | sträng | `notStarted`: Analys åtgärden har inte startats.<br /><br />`running`: Analys åtgärden pågår.<br /><br />`failed`: Det gick inte att utföra analysen.<br /><br />`succeeded`: Analys åtgärden har slutförts.|

Du anropar den här åtgärden iterativt tills den returnerar `succeeded` värdet. Använd ett intervall på 3 till 5 sekunder för att undvika att överskrida antalet begär Anden per sekund (RPS).

När fältet **status** har `succeeded` värdet, inkluderar JSON-svaret resultatet, text, tabeller och markerings markeringar som extraheras. De extraherade data innehåller extraherade text rader och ord, markerings ruta, text utseende, tabeller och markerings märken med en indikation på markerad/omarkerad. 

### <a name="sample-json-output"></a>Exempel på JSON-utdata

Svaret på resultat åtgärden Hämta analys av layout blir den strukturerade åter givningen av dokumentet med all information som extraheras. Här visas ett exempel på en [dokument fil](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) och dess [utdata från layouten](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)för strukturerad utdata.

JSON-utdata består av två delar: 
* `"readResults"` noden innehåller alla tolkade text-och markerings märken. Texten sorteras efter sida, sedan efter rad, sedan efter enskilda ord. 
* `"pageResults"` Node innehåller tabeller och celler som extraheras med deras avgränsnings rutor, relevans och en referens till rader och ord i "readResults".

## <a name="example-output"></a>Exempel på utdata

### <a name="text"></a>Text

Layout extraherar text från dokument (PDF, TIFF) och bilder (jpg, PNG, BMP) med olika text vinklar, färger, vinklar, foton av dokument, Fax, skrivna, handskrivna (endast engelska) och blandade lägen. Texten extraheras med information om rader, ord, avgränsnings rutor, konfidens resultat och stil (handskrivet eller annat). All text information ingår i `"readResults"` avsnittet i JSON-utdata. 

### <a name="tables"></a>Tabeller

Layout extraherar tabeller från dokument (PDF, TIFF) och bilder (jpg, PNG, BMP). Dokument kan skannas, fotograferas eller digitaliseras. Tabeller kan vara komplexa tabeller med sammanfogade celler eller kolumner, med eller utan kant linjer och med udda vinklar. Extraherade tabeller inkluderar antalet kolumner och rader, rad omfång och kolumn intervall. Varje cell extraheras med dess avgränsnings ruta och referens till den text som extraheras i `"readResults"` avsnittet. Tabell information finns i `"pageResults"` avsnittet i JSON-utdata. 

![Tabell exempel](./media/tables-example.jpg)

### <a name="selection-marks"></a>Markerings märken

Layout extraherar också markerings märken från dokument. Extraherade markerings markeringar inkluderar markerings ruta, förtroende och tillstånd (markerat/omarkerat). Information om markerings märken extraheras i `"readResults"` avsnittet i JSON-utdata. 

## <a name="next-steps"></a>Nästa steg

- Prova din egen extrahering av layouten med hjälp av [formulär tolkens exempel gränssnitt](https://fott-preview.azurewebsites.net/)
- Slutför en [snabb start för formulär](quickstarts/client-library.md) för att komma igång med att extrahera layouter på valfritt språk.

## <a name="see-also"></a>Se även

* [Vad är formigenkänning?](./overview.md)
* [REST API referens dokument](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)