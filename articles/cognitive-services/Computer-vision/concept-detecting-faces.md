---
title: Ansiktsigenkänning – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som rör ansiktsavkänningsfunktionen i Visuellt innehåll-API:et.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: bfb352c68b910a114e13041da4e8e86529e52040
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778849"
---
# <a name="face-detection-with-computer-vision"></a>Ansiktsavkänning med Visuellt innehåll

Visuellt innehåll kan identifiera ansikten i en bild och generera ålder, kön och rektangel för varje identifierat ansikte. 

> [!NOTE]
> Den här funktionen erbjuds också av Ansiktstjänsten [i](../face/index.yml) Azure. Se det här alternativet för mer detaljerad ansiktsanalys, inklusive ansiktsidentifiering och attitydidentifiering. 

## <a name="face-detection-examples"></a>Exempel på ansiktsavkänning

I följande exempel visas JSON-svaret som returneras av Visuellt innehåll en bild som innehåller ett enda mänskligt ansikte.

![Visuellt innehåll analyserar kvinnoansikte på tak](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

I nästa exempel visas det JSON-svar som returneras för en bild som innehåller flera mänskliga ansikten.

![Bildanalys för familjefoto](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="use-the-api"></a>Använda API:et

Ansiktsavkänningsfunktionen är en del av [Analysera bild](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. Du kan anropa det här API:et via ett inbyggt SDK eller via REST-anrop. Inkludera `Faces` i **frågeparametern visualFeatures.** När du sedan får det fullständiga JSON-svaret parsar du helt enkelt strängen för innehållet i `"faces"` avsnittet.

* [Snabbstart: Visuellt innehåll REST API eller klientbibliotek](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
