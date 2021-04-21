---
title: Identifiering av avbildningstyp – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp som rör identifieringsfunktionen för avbildningstyp i Visuellt innehåll API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: dc24788ddd21ca2b7df1f9f92238c776dee33016
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778885"
---
# <a name="detecting-image-types-with-computer-vision"></a>Identifiera bildtyper med Visuellt innehåll

Med [Analysera bild](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API kan Visuellt innehåll analysera innehållstypen för bilder, vilket anger om en bild är ClipArt eller en linjeritning.

## <a name="detecting-clip-art"></a>Identifiera ClipArt

Visuellt innehåll analyserar en bild och beter sannolikheten för att bilden är ClipArt på en skala från 0 till 3, enligt beskrivningen i följande tabell.

| Värde | Innebörd |
|-------|---------|
| 0 | Non-clip-art |
| 1 | Tvetydig |
| 2 | Normal ClipArt |
| 3 | Good-Clip-Art |

### <a name="clip-art-detection-examples"></a>Exempel på identifiering av ClipArt

Följande JSON-svar illustrerar vad Visuellt innehåll returnerar när du bedömer sannolikheten för att exempelbilderna är ClipArt.

![En ClipArt-bild av en ostsegment](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Ett blått hus och fronten](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Identifiera linjeritningar

Visuellt innehåll analyserar en bild och returnerar ett booleskt värde som anger om bilden är en linjeteckning.

### <a name="line-drawing-detection-examples"></a>Exempel på linjeritning

Följande JSON-svar illustrerar vad Visuellt innehåll returnerar när du anger om exempelbilderna är linjeritningar.

![En linjeritning av ett drak](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![En vit blomma med en grön bakgrund](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Använda API:et

Bildtypsidentifieringsfunktionen är en del [Analysera bild](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API:et. Du kan anropa det här API:et via ett inbyggt SDK eller via REST-anrop. Inkludera `ImageType` i **frågeparametern visualFeatures.** När du sedan får det fullständiga JSON-svaret parsar du helt enkelt strängen för innehållet i `"imageType"` avsnittet.

* [Snabbstart: Visuellt innehåll REST API eller klientbibliotek](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
