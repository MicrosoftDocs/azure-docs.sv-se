---
title: Innehålls etiketter – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som rör funktionen för att tagga bilder i API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a75e55cd51890173b6733ab5f8c0f1bcbfeef0f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96532983"
---
# <a name="applying-content-tags-to-images"></a>Applicera innehållstaggar på bilder

Visuellt innehåll returnerar Taggar baserat på tusentals identifierbara objekt, boende, landskap och åtgärder. I de fall där taggarna är tvetydiga eller dess betydelse inte är allmän kännedom ger API-svaren ”ledtrådar” för att tydliggöra taggarnas betydelse i en viss kontext. Taggar är inte ordnade som en taxonomi och inga arvshierarkier finns. En samling innehållstaggar utgör grunden för en ”bildbeskrivning” som visas som språk som kan läsas av människor som är formaterade i fullständiga meningar. Observera att engelska för tillfället är det enda språket som stöds för bildbeskrivning.

När du laddar upp en bild eller anger en bild-URL, matar algoritmer för Visuellt innehåll ut taggar baserade på objekt, levande varelser och åtgärder som identifierats i bilden. Taggar är inte begränsade till huvudföremålet på bilden, som till exempel en person i förgrunden, utan finns även för saker som bakgrund (inomhus eller utomhus), möbler, verktyg, växter, djur, accessoarer, saker och så vidare.

## <a name="image-tagging-example"></a>Exempel på bild taggning

Följande JSON-svar illustrerar vad Visuellt innehåll returnerar när du taggar visuella funktioner som identifieras i exempel bilden.

![Ett blått hus och en främre meter](./Images/house_yard.png).

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

Taggnings funktionen ingår i [analys avbildningens](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API. Du kan anropa API: et via en inbyggd SDK eller via REST-anrop. Inkludera `Tags` i **visualFeatures** -Frågeparametern. När du sedan får det fullständiga JSON-svaret ska du bara parsa strängen för innehållet i `"tags"` avsnittet.

* [Snabb start: Visuellt innehåll REST API-eller klient bibliotek](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Nästa steg

Lär dig om de relaterade begreppen för att [kategorisera bilder](concept-categorizing-images.md) och att [beskriva bilder](concept-describing-images.md).
