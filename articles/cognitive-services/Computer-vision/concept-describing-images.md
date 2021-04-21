---
title: Bildbeskrivningar – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp som rör bildbeskrivningsfunktionen i api:et för Visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: c517fa98bfc17d4702a51d4990e860b2ed7aaefd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778939"
---
# <a name="describe-images-with-human-readable-language"></a>Beskriva bilder med läsbart språk

Visuellt innehåll kan analysera en bild och generera en läsbar mening som beskriver dess innehåll. Algoritmen returnerar faktiskt flera beskrivningar baserat på olika visuella funktioner, och varje beskrivning får en förtroendepoäng. De slutliga utdata är en lista med beskrivningar sorterade från högsta till lägsta konfidens.

## <a name="image-description-example"></a>Exempel på bildbeskrivning

Följande JSON-svar illustrerar vad Visuellt innehåll returnerar när du beskriver exempelbilden baserat på dess visuella funktioner.

![En svart och vit bild av byggnader i Gör](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Använda API:et

Bildbeskrivningsfunktionen är en del av [Analysera bild](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. Du kan anropa det här API:et via ett inbyggt SDK eller via REST-anrop. Inkludera `Description` i **frågeparametern visualFeatures.** När du sedan får det fullständiga JSON-svaret parsar du helt enkelt strängen för innehållet i `"description"` avsnittet.

* [Snabbstart: Visuellt innehåll REST API eller klientbibliotek](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om relaterade [begrepp för att tagga](concept-tagging-images.md) [bilder och kategorisera bilder.](concept-categorizing-images.md)
