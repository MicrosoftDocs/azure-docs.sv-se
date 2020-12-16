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
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 98b8cca95b8815b32fb5de6567adcf4f150551eb
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561978"
---
# <a name="whats-new-in-computer-vision"></a>Vad är nytt i Visuellt innehåll

Läs om vad som är nytt i tjänsten. Dessa objekt kan vara viktig information, videor, blogg inlägg och andra typer av information. Skapa ett bok märke för den här sidan för att hålla dig uppdaterad med tjänsten.

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

Följ en [snabb start för att extrahera text](./quickstarts/csharp-hand-text.md?tabs=version-3) för att komma igång med 3,0-API: et.

## <a name="cognitive-service-updates"></a>Uppdatering av kognitiva tjänster

[Azure Update-meddelanden för Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)