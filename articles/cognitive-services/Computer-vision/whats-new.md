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
ms.openlocfilehash: e42096fc32a504ae329d3b179004b6a123de4469
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365645"
---
# <a name="whats-new-in-computer-vision"></a>Vad är nytt i Visuellt innehåll

Lär dig vad som är nytt i tjänsten. Dessa objekt kan vara viktig information, videor, blogginlägg och andra typer av information. Bokmärk den här sidan för att hålla dig uppdaterad med tjänsten.

## <a name="april-2021"></a>April 2021

### <a name="computer-vision-v32-ga"></a>Visuellt innehåll v3.2 GA

Api Visuellt innehåll v3.2 är nu allmänt tillgängligt med följande uppdateringar:
* Förbättrad bildtaggningsmodell: analyserar visuellt innehåll och genererar relevanta taggar baserat på objekt, åtgärder och innehåll som visas i bilden. Detta är tillgängligt via [API:et taggavbildning.](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f200) Mer information finns i [i guiden och översikten](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi) [för](https://docs.microsoft.com/azure/cognitive-services/computer-vision/overview-image-analysis) bildanalys.
* Uppdaterad innehållsmodereringsmodell: identifierar förekomst av vuxet innehåll och tillhandahåller flaggor för att filtrera bilder som innehåller vuxet, racy och gory visuellt innehåll. Detta är tillgängligt via [API:et Analysera.](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) Mer information finns i [i guiden och översikten](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi) [för](https://docs.microsoft.com/azure/cognitive-services/computer-vision/overview-image-analysis) bildanalys.
* [OCR (Read) är tillgängligt för 73 språk,](./language-support.md#optical-character-recognition-ocr) inklusive förenklad och traditionell kinesiska, japanska, koreanska och latinska språk.
* [OCR (Read)](./overview-ocr.md) finns även som [en distrolös container](./computer-vision-how-to-install-containers.md?tabs=version-3-2) för lokal distribution.

> [!div class="nextstepaction"]
> [Se Visuellt innehåll v3.2 GA](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)

## <a name="march-2021"></a>Mars 2021

### <a name="computer-vision-32-public-preview-update"></a>Visuellt innehåll 3.2 – offentlig förhandsversion

Den offentliga Visuellt innehåll API v3.2 har uppdaterats. Förhandsversionen innehåller alla funktioner Visuellt innehåll tillsammans med uppdaterade API:er för att läsa och analysera.

> [!div class="nextstepaction"]
> [Se Visuellt innehåll v3.2 offentlig förhandsversion 3](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)

## <a name="february-2021"></a>Februari 2021

### <a name="read-api-v32-public-preview-with-ocr-support-for-73-languages"></a>Läs den offentliga förhandsversionen av API v3.2 med OCR-stöd för 73 språk
Visuellt innehåll offentliga förhandsversionen av Read API v3.2, som är tillgänglig som molntjänst och Docker-container, innehåller följande uppdateringar:
* [OCR för 73 språk,](./language-support.md#optical-character-recognition-ocr) inklusive förenklad och traditionell kinesiska, japanska, koreanska och latinska språk.
* Naturlig läsordning för textradsutdata (endast latinska språk)
* Klassificering av handskriftsstil för textrader tillsammans med en konfidenspoäng (endast latinska språk).
* Extrahera endast text för valda sidor för ett dokument med flera sidor.
* Tillgänglig som [en distrolös container](./computer-vision-how-to-install-containers.md?tabs=version-3-2) för lokal distribution.

Mer information [finns i läs-API-guiden.](Vision-API-How-to-Topics/call-read-api.md)

> [!div class="nextstepaction"]
> [Använda Read API v3.2 Public Preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)


## <a name="january-2021"></a>Januari 2021

### <a name="spatial-analysis-container-update"></a>Uppdatering av container för rumslig analys

En ny version av [containern spatial analys](spatial-analysis-container.md) har släppts med en ny funktionsuppsättning. Med den här Docker-containern kan du analysera strömmande video i realtid för att förstå rumsliga relationer mellan människor och deras förflyttning via fysiska miljöer. 

* [Åtgärder för rumslig](spatial-analysis-operations.md) analys kan nu konfigureras för att identifiera om en person bär ett skyddande ansikte, till exempel en mask. 
    * En maskerings klassificerare kan aktiveras för åtgärderna `personcount` , och genom att konfigurera `personcrossingline` `personcrossingpolygon` `ENABLE_FACE_MASK_CLASSIFIER` parametern .
    * Attributen `face_mask` och `face_noMask` returneras som metadata med förtroendepoäng för varje person som identifieras i videoströmmen
* Åtgärden *personcrossingpolygon* har utökats för att tillåta beräkning av den tid en person tillbringar i en zon. Du kan ange parametern i zonkonfigurationen för åtgärden till och en ny händelse av typen `type` `zonedwelltime` *personZoneDwellTimeEvent* innehåller fältet ifylld med det antal millisekunder som personen spenderade i `durationMs` zonen.
* **Icke-ändring:** *Händelsen personZoneEvent* har bytt namn till *personZoneEnterExitEvent.* Den här händelsen utlöses av *åtgärden personcrossingpolygon* när en person kommer in i eller lämnar zonen och ger riktningsinformation med den numrerade sidan av zonen som korsades.
* Video-URL kan anges som "Privat parameter/fördruppad" i alla åtgärder. Fördring är valfritt nu och fungerar bara om `KEY` `IV` och tillhandahålls som miljövariabler.
* Kalibrering är aktiverat som standard för alla åtgärder. Ange för `do_calibration: false` att inaktivera den.
* Stöd för automatisk omcalibrering (som standard inaktiverat) har lagts till via parametern . Mer information finns i `enable_recalibration` [Åtgärder för rumslig](./spatial-analysis-operations.md) analys
* Kameraparametrar för kalibrering till `DETECTOR_NODE_CONFIG` . Mer information [finns i Åtgärder för](./spatial-analysis-operations.md) rumslig analys.


## <a name="october-2020"></a>Oktober 2020

### <a name="computer-vision-api-v31-ga"></a>Visuellt innehåll API v3.1 GA

Api:Visuellt innehåll i allmän tillgänglighet har uppgraderats till v3.1.

## <a name="september-2020"></a>September 2020

### <a name="spatial-analysis-container-preview"></a>Förhandsversion av container för rumslig analys

Containern [för rumslig analys](spatial-analysis-container.md) är nu i förhandsversion. Med funktionen för rumslig analys Visuellt innehåll kan du analysera strömmande video i realtid för att förstå rumsliga relationer mellan människor och deras rörelser via fysiska miljöer. Rumslig analys är en Docker-container som du kan använda lokalt. 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Read API v3.1 Public Preview lägger till OCR för japanska
Visuellt innehåll den offentliga förhandsversionen av Read API v3.1 lägger till följande funktioner:
* OCR för japanska
* För varje textrad anger du om utseendet är Handwriting- eller Print-format, tillsammans med förtroendepoäng (endast latinska språk).
* För ett dokument med flera sidor extraherar du endast text för valda sidor eller sidintervall.

* Den här förhandsversionen av Api:et Read stöder engelska, nederländska, franska, tyska, italienska, japanska, portugisiska, förenklad kinesiska och spanska.

Mer information [finns i läs-API-guiden.](Vision-API-How-to-Topics/call-read-api.md)

> [!div class="nextstepaction"]
> [Läs mer om Read API v3.1 Public Preview 2](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>Juli 2020

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Läs den offentliga förhandsversionen av API v3.1 med OCR för förenklad kinesiska
Visuellt innehåll offentliga förhandsversionen av Read API v3.1 har stöd för förenklad kinesiska.

* Den här förhandsversionen av API:et Read stöder engelska, nederländska, franska, tyska, italienska, portugisiska, förenklad kinesiska och spanska.

Mer information [finns i läs-API-guiden.](Vision-API-How-to-Topics/call-read-api.md)

> [!div class="nextstepaction"]
> [Läs mer om Read API v3.1 Public Preview 1](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>Maj 2020
Visuellt innehåll API v3.0 har angetts för allmän tillgänglighet, med uppdateringar av Api för läsning:

* Stöd för engelska, nederländska, franska, tyska, italienska, portugisiska och spanska
* Förbättrad noggrannhet
* Förtroendepoäng för varje extraherat ord
* Nytt utdataformat

Mer information [finns i ÖVERSIKT ÖVER OCR.](overview-ocr.md)

## <a name="march-2020"></a>Mars 2020

* TLS 1.2 tillämpas nu för alla HTTP-begäranden till den här tjänsten. Mer information finns i [Azure Cognitive Services säkerhet](../cognitive-services-security.md).

## <a name="january-2020"></a>Januari 2020

### <a name="read-api-30-public-preview"></a>Read API 3.0 Public Preview

Nu har du möjlighet att använda version 3.0 av API:et Read för att extrahera tryckt eller handskriven text från bilder. Jämfört med tidigare versioner ger 3.0:
* Förbättrad noggrannhet
* Nytt utdataformat
* Förtroendepoäng för varje extraherat ord
* Stöd för både spanska och engelska språk med den ytterligare språkparametern

Följ [snabbstarten Extrahera text för](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-hand-text.md?tabs=version-3) att komma igång med 3.0-API:et.

## <a name="cognitive-service-updates"></a>Cognitive Service-uppdateringar

[Azure-uppdateringsmeddelanden för Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
