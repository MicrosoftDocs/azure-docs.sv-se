---
title: Vad är Custom Vision?
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder Azure Custom Vision-tjänsten för att bygga anpassade klassificerare för AI-avbildningar och objekt identifieringar i Azure-molnet.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: bild igenkänning, bild identifierare, app för bild igenkänning, anpassad vision
ms.openlocfilehash: 12877f2d43f9b8f864871e5a5ab050aa0eeb61e2
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814609"
---
# <a name="what-is-custom-vision"></a>Vad är Custom Vision?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Custom Vision är en avbildnings igenkännings tjänst som gör att du kan bygga, distribuera och förbättra dina egna avbildnings identifierare. Ett bild-ID använder etiketter (som representerar klasser eller objekt) för bilder, enligt deras visuella egenskaper. Till skillnad från [visuellt innehåll](../computer-vision/overview.md) tjänsten kan du Custom vision ange etiketter och träna anpassade modeller för att identifiera dem.

## <a name="what-it-does"></a>Vad läget gör

Den Custom Vision tjänsten använder en Machine Learning-algoritm för att analysera avbildningar. Du, utvecklaren, skicka grupper med avbildningar som har funktioner och saknar de egenskaper som är aktuella. Du märker själv bilderna när de skickas in. Sedan kan algoritmen anpassas till dessa data och beräknar sin egen precision genom att testa dem på samma avbildningar. När algoritmen har tränats kan du testa, omträna och slutligen använda den i din app för bild igenkänning för att klassificera nya avbildningar. Du kan också exportera själva modellen för användning offline.

### <a name="classification-and-object-detection"></a>Klassificering och objektidentifiering

Anpassade funktioner för Custom Vision kan delas in i två funktioner. **Bildklassificering** applicerar en eller flera etiketter på en bild. **Objektidentifiering** är liknande, men returnerar även de koordinater i bilden där de tillämpade etiketterna kan hittas.

### <a name="optimization"></a>Optimering

Custom Visions tjänsten är optimerad för att snabbt identifiera större skillnader mellan bilder, så att du kan börja skapa prototyper för din modell med en liten mängd data. 50 bilder per etikett är vanligtvis en bra utgångspunkt. Tjänsten är dock inte optimal för att identifiera diskreta skillnader i bilder (till exempel att identifiera mindre sprickor eller indrag i kvalitets säkrings scenarier).

Dessutom kan du välja bland flera olika typer av Custom Vision-algoritmen som är optimerade för bilder med vissa motiv&mdash; exempelvis landmärken eller detaljhandelsobjekt. Mer information finns i avsnittet [bygga en klassificerare](getting-started-build-a-classifier.md) eller [bygga ett objekt detektor](get-started-build-detector.md) guider.

## <a name="what-it-includes"></a>Vad verktyget innehåller

Custom Vision Service är tillgänglig som en uppsättning inbyggda SDK: er samt via ett webbaserat gränssnitt på [Custom vision webbplats](https://customvision.ai/). Du kan skapa, testa och träna en modell via endera gränssnittet eller använda båda tillsammans.

![Custom Vision webbplats i ett Chrome-webbläsarfönster](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Som med alla Cognitive Services bör utvecklare som använder Custom Vision-tjänsten känna till Microsofts policyer gällande kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Följ guiden [skapa en klassificerings](getting-started-build-a-classifier.md) guide för att komma igång med Custom vision på webb portalen eller Slutför en [snabb start](quickstarts/image-classification.md) för att implementera de grundläggande scenarierna i kod.