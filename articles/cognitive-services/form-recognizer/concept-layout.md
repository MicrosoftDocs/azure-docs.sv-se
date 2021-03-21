---
title: Formulär igenkänning för layouter
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som rör layout analyser med formatet tolknings-API-användning och begränsningar.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 73bef21f430bde1c6c2c95d7c3f685cccbbd9179
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467025"
---
# <a name="form-recognizer-layout-service"></a>Formulär tolkens layout-tjänst

Azure formulär tolken kan extrahera text, tabeller, markerings märken och struktur information från dokument med hjälp av dess layout-tjänst. Layout-API: et gör det möjligt för kunderna att ta dokument i en rad olika format och returnera strukturerade data representationer av dokumenten. Den kombinerar vår kraftfulla [OCR-kapacitet (optisk tecken läsning)](../computer-vision/concept-recognizing-text.md) med djup inlärnings modeller för att extrahera text, tabeller, markerings märken och dokument strukturer. 

## <a name="what-does-the-layout-service-do"></a>Vad gör layout tjänsten?

Layout-API: et extraherar text, tabeller, markerings märken och struktur information från dokument med enastående precision och returnerar ett organiserat, strukturerat, JSON-svar. Dokument kan vara olika format och kvalitet, inklusive hämtade bilder, skannade dokument och digitala PDF-filer. Layout-API: et extraherar korrekt strukturerad utdata från alla dessa dokument.

![Layout-exempel](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Prova

Om du vill testa layouten för formulär tolken går du till verktyget online-exempel UI:

> [!div class="nextstepaction"]
> [Formulär för OCR-testverktyg (FOTT)](https://fott-preview.azurewebsites.net)

Du behöver en Azure-prenumeration ([skapa en kostnads fri](https://azure.microsoft.com/free/cognitive-services)) och en resurs slut punkt för [formulär igenkänning](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) och en nyckel för att testa formulär tolkens layout-API. 

![Skärm bild av exempel gränssnitt; text, tabeller och markerings markeringar i ett dokument analyseras](./media/analyze-layout.png)

### <a name="input-requirements"></a>Krav för indatamängd 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>Åtgärden analysera layout

Anropa först åtgärden [analysera layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync) . Analysera layout tar ett dokument (bild-, TIFF-eller PDF-fil) som indata och extraherar texten, tabellerna, markerings märkena och strukturen i dokumentet. Anropet returnerar ett svars huvud fält som kallas `Operation-Location` . `Operation-Location`Värdet är en URL som innehåller det resultat-ID som ska användas i nästa steg.

|Svars huvud| Resultat-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

### <a name="natural-reading-order-output-latin-only"></a>Naturlig Läs ordning för utdata (endast latinsk)

Du kan ange i vilken ordning text raderna ska matas med `readingOrder` Frågeparametern. Använd `natural` för att se en mer användarvänlig läsnings ordning enligt följande exempel. Den här funktionen stöds bara för latinska språk.

:::image type="content" source="../Computer-vision/Images/ocr-reading-order-example.png" alt-text="Exempel på layout för Läs ordning" lightbox="../Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>Välj sid nummer eller intervall för text extrahering

För stora dokument med flera sidor använder du `pages` Frågeparametern för att ange vissa sid nummer eller sid intervall för text extrahering. I följande exempel visas ett dokument med 10 sidor, med text som extraherats för båda fallen – alla sidor (1-10) och valda sidor (3-6).

:::image type="content" source="../Computer-vision/Images/ocr-select-pages.png" alt-text="Layout valda sidor utdata":::

## <a name="the-get-analyze-layout-result-operation"></a>Resultat åtgärden Hämta analys av layout

Det andra steget är att anropa [resultat åtgärden Hämta analys av layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult) . Den här åtgärden tar inmatat till det resultat-ID som skapades av åtgärden analysera layout. Den returnerar ett JSON-svar som innehåller ett **status** fält med följande möjliga värden. 

|Fält| Typ | Möjliga värden |
|:-----|:----:|:----|
|status | sträng | `notStarted`: Analys åtgärden har inte startats.<br /><br />`running`: Analys åtgärden pågår.<br /><br />`failed`: Det gick inte att utföra analysen.<br /><br />`succeeded`: Analys åtgärden har slutförts.|

Anropa den här åtgärden iterativt tills den returnerar `succeeded` värdet. Använd ett intervall på 3 till 5 sekunder för att undvika att överskrida antalet begär Anden per sekund (RPS).

När fältet **status** har värdet är `succeeded` JSON-svaret det extraherade layout-, text-, tabell-och markerings märkena. De extraherade data innehåller extraherade text rader och ord, avgränsnings rutor, text utseende med handskriven text, tabeller och markerings märken med markerat/omarkerat angivet. 

### <a name="handwritten-classification-for-text-lines-latin-only"></a>Handskriven klassificering för text rader (endast latinsk)

Svaret innefattar att klassificera om varje textrad är av hand SKRIFTS format eller inte, tillsammans med en förtroende poäng. Den här funktionen stöds bara för latinska språk. I följande exempel visas den handskrivna klassificeringen för texten i bilden.

:::image type="content" source="../Computer-vision/Images/ocr-handwriting-classification.png" alt-text="exempel på hand SKRIFTS klassificering":::

### <a name="sample-json-output"></a>Exempel på JSON-utdata

Svaret på resultat åtgärden *Hämta analys av layout* är en strukturerad representation av dokumentet med all information som extraheras. Här visas ett exempel på en [dokument fil](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) och dess [utdata från layouten](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)för strukturerad utdata.

JSON-utdata består av två delar:

* `readResults` noden innehåller alla tolkade text-och markerings märken. Texten sorteras efter sida, sedan efter rad, sedan efter enskilda ord. 
* `pageResults` Node innehåller tabeller och celler som extraheras med deras avgränsnings rutor, relevans och en referens till rader och ord i "readResults".

## <a name="example-output"></a>Exempel på utdata

### <a name="text"></a>Text

Layout-API extraherar text från dokument (PDF, TIFF) och bilder (JPG, PNG, BMP) med flera text vinklar och färger. Den accepterar foton av dokument, Fax, skrivna och/eller handskrivna (endast engelska) text och blandade lägen. Texten extraheras med information som finns på rader, ord, avgränsnings rutor, konfidens resultat och stil (handskriven eller annat). All text information ingår i `readResults` avsnittet i JSON-utdata. 

### <a name="tables"></a>Tables

Layout-API extraherar tabeller från dokument (PDF, TIFF) och bilder (JPG, PNG, BMP). Dokument kan skannas, fotograferas eller digitaliseras. Tabeller kan vara komplexa med sammanfogade celler eller kolumner, med eller utan kant linjer och med udda vinklar. Extraherad tabell information omfattar antalet kolumner och rader, rad omfång och kolumn intervall. Varje cell extraheras med dess avgränsnings ruta och referens till den text som extraheras i `readResults` avsnittet. Tabell information finns i `pageResults` avsnittet i JSON-utdata. 

![Tabell exempel](./media/tables-example.jpg)

### <a name="selection-marks"></a>Markerings märken

Layout-API: et extraherar även markerings märken från dokument. Extraherade markerings märken innehåller markerings rutan, förtroendet och läget (markerat/omarkerat). Information om markerings märken extraheras i `readResults` avsnittet i JSON-utdata. 

## <a name="next-steps"></a>Nästa steg

* Prova din egen extrahering av layout med [formulär tolkens exempel på användar gränssnitts verktyg](https://fott-preview.azurewebsites.net/)
* Slutför en [snabb start för formulär](quickstarts/client-library.md) för att komma igång med att extrahera layouter på valfritt programmeringsspråk.

## <a name="see-also"></a>Se även

* [Vad är formigenkänning?](./overview.md)
* [REST API referens dokument](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)
