---
title: Vad är nytt i Visuellt innehåll?
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller nyheter om Visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/13/2021
ms.author: pafarley
ms.openlocfilehash: 78746e7623f58af5ae9df829b48245295dc39f01
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487108"
---
# <a name="whats-new-in-computer-vision"></a>Vad är nytt i Visuellt innehåll

Läs om vad som är nytt i tjänsten. Dessa objekt kan vara viktig information, videor, blogg inlägg och andra typer av information. Skapa ett bok märke för den här sidan för att hålla dig uppdaterad med tjänsten.

## <a name="march-2021"></a>Mars 2021

### <a name="computer-vision-32-public-preview-update"></a>Visuellt innehåll 3,2 offentlig förhands gransknings uppdatering

Den offentliga för hands versionen av API för visuellt innehåll v 3.2 har uppdaterats. För hands versionen har alla Visuellt innehåll funktioner tillsammans med uppdaterade API: er för läsning och analys.

> [!div class="nextstepaction"]
> [Se Visuellt innehåll v 3.2 Public Preview 3](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)

## <a name="february-2021"></a>Februari 2021

### <a name="read-api-v32-public-preview-with-ocr-support-for-73-languages"></a>Läs API v 3.2 offentlig för hands version med OCR-stöd för 73 språk
Visuellt innehåll s Read API v 3.2 offentlig för hands version, som är tillgänglig som moln tjänst och Docker-behållare, innehåller följande uppdateringar:
* [OCR för 73-språk](./language-support.md#optical-character-recognition-ocr) , inklusive förenklade och traditionella kinesiska, japanska, koreanska och latinska språk.
* Naturlig Läs ordning för text radens utdata (endast latinska språk)
* Stil klassificering för hand skrift för text rader tillsammans med en förtroende poäng (endast latinska språk).
* Extrahera endast text för markerade sidor för ett flersidigt dokument.
* Tillgängligt som en [Distroless-behållare](./computer-vision-how-to-install-containers.md?tabs=version-3-2) för lokal distribution.

[Läs mer](concept-recognizing-text.md) om Read API.

> [!div class="nextstepaction"]
> [Använd Read API v 3.2 offentlig för hands version](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)


## <a name="january-2021"></a>Januari 2021

### <a name="spatial-analysis-container-update"></a>Uppdatering av rums analys behållare

En ny version av [behållaren för rums analys](spatial-analysis-container.md) har släppts med en ny funktions uppsättning. Med den här Docker-behållaren kan du analysera real tids strömnings video för att förstå spatiala relationer mellan människor och deras rörelse genom fysiska miljöer. 

* [Åtgärder för spatial analys](spatial-analysis-operations.md) kan nu konfigureras för att identifiera om en person får en skyddande ansikte som en mask. 
    * Du kan aktivera en mask-klassificerare `personcount` för `personcrossingline` `personcrossingpolygon` åtgärderna och genom att konfigurera `ENABLE_FACE_MASK_CLASSIFIER` parametern.
    * Attributen `face_mask` och `face_noMask` kommer att returneras som metadata med förtroende poäng för varje person som identifieras i video strömmen
* *Personcrossingpolygon* -åtgärden har utökats för att tillåta beräkningen av bostads tiden som en person tillbringar i en zon. Du kan ställa in `type` parametern i zon konfigurationen för åtgärden till `zonedwelltime` och en ny händelse av typen *personZoneDwellTimeEvent* innehåller `durationMs` fältet ifyllt med antalet millisekunder som den person som har använt zonen.
* **Ändrad ändring**: *personZoneEvent* -händelsen har bytt namn till *personZoneEnterExitEvent*. Den här händelsen utlöses av *personcrossingpolygon* -åtgärden när en person går in eller avslutar zonen och ger riktad information med den numrerade sidan i den zon som har korsats.
* Video-URL kan anges som "privat parameter/fördunklade" i alla åtgärder. Döljande är valfritt och fungerar bara om `KEY` och `IV` tillhandahålls som miljövariabler.
* Kalibrering är aktiverat som standard för alla åtgärder. Ange `do_calibration: false` för att inaktivera den.
* Stöd har lagts till för automatisk omkalibrering (som standard inaktive rad) via `enable_recalibration` parametern. mer information finns i avsnittet om [spatial analys](./spatial-analysis-operations.md) .
* Kamera kalibrerings parametrar till `DETECTOR_NODE_CONFIG` . Mer information finns i [åtgärder för spatial analys](./spatial-analysis-operations.md) .


## <a name="october-2020"></a>Oktober 2020

### <a name="computer-vision-api-v31-ga"></a>API för visuellt innehåll v 3.1 GA

Den API för visuellt innehåll som är allmänt tillgänglig har uppgraderats till v 3.1.

## <a name="september-2020"></a>September 2020

### <a name="spatial-analysis-container-preview"></a>Förhands granskning av rums analys behållare

[Behållaren för rums analys](spatial-analysis-container.md) är nu i för hands version. Med funktionen för spatial analys i Visuellt innehåll kan du analysera real tids strömnings video för att förstå spatiala relationer mellan människor och deras rörelse genom fysiska miljöer. Rums analys är en Docker-behållare som du kan använda lokalt. 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Read API v 3.1 offentlig för hands version lägger till OCR för japanska
I Visuellt innehåll Läs-API v 3.1, som är allmänt tillgänglig, läggs dessa funktioner till:
* OCR för japanskt språk
* För varje textrad anger du om utseendet är hand skrift eller utskrifts format, tillsammans med en förtroende poäng (endast latinska språk).
* För ett flersidigt dokument extrahera text enbart för markerade sidor eller sid intervall.

* Den här för hands versionen av Read API stöder engelska, nederländska, franska, tyska, italienska, japanska, portugisiska, kinesiska (förenklad) och spanska språk.

Mer information finns i [Översikt över Read API](concept-recognizing-text.md) .

> [!div class="nextstepaction"]
> [Läs mer om Read API v 3.1 Public Preview 2](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>Juli 2020

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Read API v 3.1 offentlig för hands version med OCR för förenklad kinesiska
Visuellt innehåll s Read API v 3.1 allmän för hands version lägger till stöd för förenklad kinesiska.

* Den här för hands versionen av Read API stöder engelska, nederländska, franska, tyska, italienska, portugisiska, kinesiska (förenklad) och spanska språk.

Mer information finns i [Översikt över Read API](concept-recognizing-text.md) .

> [!div class="nextstepaction"]
> [Läs mer om Read API v 3.1 offentlig för hands version 1](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>Maj 2020
API för visuellt innehåll v 3.0 angav allmän tillgänglighet, med uppdateringar för [Read API](concept-recognizing-text.md):

* Stöd för engelska, nederländska, franska, tyska, italienska, portugisiska och spanska
* Förbättrad precision
* Säkerhets Poäng för varje extraherat ord
* Nytt format för utdata

## <a name="march-2020"></a>Mars 2020

* TLS 1,2 upprätthålls nu för alla HTTP-begäranden till den här tjänsten. Mer information finns i [Azure Cognitive Services Security](../cognitive-services-security.md).

## <a name="january-2020"></a>Januari 2020

### <a name="read-api-30-public-preview"></a>Läs API 3,0 offentlig för hands version

Nu har du möjlighet att använda version 3,0 av Read API för att extrahera utskrift eller handskriven text från bilder. Jämfört med tidigare versioner tillhandahåller 3,0:
* Förbättrad precision
* Nytt format för utdata
* Säkerhets Poäng för varje extraherat ord
* Stöd för både spanska och engelska språk med ytterligare språk parameter

Följ en [snabb start för att extrahera text](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-hand-text.md?tabs=version-3) för att komma igång med 3,0-API: et.

## <a name="cognitive-service-updates"></a>Uppdatering av kognitiva tjänster

[Azure Update-meddelanden för Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)