---
title: 'Kognitiva service SKU: er och priser'
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 8cc4bc6907f83ce062fed82dde17815fc4debd67
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719691"
---
Se listan över SKU: er och pris information nedan. 

#### <a name="multi-service"></a>Multi-service

| Tjänst                    | Variant                      |
|----------------------------|---------------------------|
| Flera tjänster. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/cognitive-services/) .            | `CognitiveServices`     |


#### <a name="vision"></a>Visuellt innehåll

| Tjänst                    | Variant                      |
|----------------------------|---------------------------|
| Visuellt innehåll            | `ComputerVision`          |
| Custom Vision förutsägelse | `CustomVision.Prediction` |
| Custom Vision-utbildning   | `CustomVision.Training`   |
| Ansikte                       | `Face`                    |
| Formigenkänning            | `FormRecognizer`          |

#### <a name="search"></a>Sök

| Tjänst            | Variant                  |
|--------------------|-----------------------|
| Automatiska förslag i Bing   | `Bing.Autosuggest.v7` |
| Anpassad sökning i Bing | `Bing.CustomSearch`   |
| Entitetssökning i Bing | `Bing.EntitySearch`   |
| Bing-sökning        | `Bing.Search.v7`      |
| Stavningskontroll i Bing   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Speech

| Tjänst            | Variant                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| Taligenkänning | `SpeakerRecognition` |

#### <a name="language"></a>Språk

| Tjänst            | Variant                |
|--------------------|---------------------|
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Textanalys     | `TextAnalytics`     |
| Textöversättning   | `TextTranslation`   |

#### <a name="decision"></a>Beslut

| Tjänst           | Variant               |
|-------------------|--------------------|
| Avvikelseidentifiering  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personanpassning      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Pris nivåer och fakturering

Pris nivåerna (och den mängd du debiteras) baseras på antalet transaktioner som du skickar med hjälp av autentiseringsinformationen. Varje pris nivå anger:
* maximalt antal tillåtna transaktioner per sekund (TPS).
* tjänst funktioner som Aktiver ATS inom pris nivån.
* kostnad för ett fördefinierat antal transaktioner. Om du fortsätter över det här numret kommer det att leda till en extra avgift som anges i [pris informationen](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) för din tjänst.

> [!NOTE]
> Många av de Cognitive Services har en kostnads fri nivå som du kan använda för att testa tjänsten. Använd den kostnads fria nivån `F0` som SKU för din resurs.