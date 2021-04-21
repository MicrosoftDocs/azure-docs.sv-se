---
title: Innehållstaggar – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som rör taggningsfunktionen bilder i Visuellt innehåll API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3ef2d07b736d391fc3509266dea77fdc1b325ff2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778723"
---
# <a name="applying-content-tags-to-images"></a>Applicera innehållstaggar på bilder

Visuellt innehåll taggar baserat på tusentals identifierbara objekt, levande varelser, landskap och åtgärder. I de fall där taggarna är tvetydiga eller dess betydelse inte är allmän kännedom ger API-svaren ”ledtrådar” för att tydliggöra taggarnas betydelse i en viss kontext. Taggar är inte ordnade som en taxonomi och inga arvshierarkier finns. En samling innehållstaggar utgör grunden för en ”bildbeskrivning” som visas som språk som kan läsas av människor som är formaterade i fullständiga meningar. Observera att engelska för tillfället är det enda språket som stöds för bildbeskrivning.

När du laddar upp en bild eller anger en bild-URL, matar algoritmer för Visuellt innehåll ut taggar baserade på objekt, levande varelser och åtgärder som identifierats i bilden. Taggar är inte begränsade till huvudföremålet på bilden, som till exempel en person i förgrunden, utan finns även för saker som bakgrund (inomhus eller utomhus), möbler, verktyg, växter, djur, accessoarer, saker och så vidare.

## <a name="image-tagging-example"></a>Exempel på bildtaggning

Följande JSON-svar illustrerar vad Visuellt innehåll returnerar vid taggning av visuella funktioner som identifierats i exempelbilden.

![Ett blått hus och fronten](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Använda API:et

Taggningsfunktionen är en del av [Analysera bild](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. Du kan anropa det här API:et via en intern SDK eller via REST-anrop. Inkludera `Tags` i **frågeparametern visualFeatures.** När du sedan får det fullständiga JSON-svaret parsar du helt enkelt strängen för innehållet i `"tags"` avsnittet.

* [Snabbstart: Visuellt innehåll REST API eller klientbibliotek](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om relaterade begrepp [för att kategorisera bilder](concept-categorizing-images.md) och beskriva [bilder](concept-describing-images.md).
