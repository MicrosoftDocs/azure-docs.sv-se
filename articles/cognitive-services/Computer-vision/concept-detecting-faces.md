---
title: Ansikts identifiering – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som rör funktionen för ansikts igenkänning i API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3aa8f9057c3c9e3a141824a9d23e9be8d0bbe4b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96532643"
---
# <a name="face-detection-with-computer-vision"></a>Ansikts igenkänning med Visuellt innehåll

Visuellt innehåll kan identifiera mänskliga ansikten i en bild och generera ålder, kön och rektangeln för varje identifierad ansikte. 

> [!NOTE]
> Den här funktionen erbjuds även av Azure [Face](../face/index.yml) service. Se det här alternativet för mer detaljerad ansikts analys, inklusive identifiering av ansikte och identifiering. 

## <a name="face-detection-examples"></a>Exempel på ansikts igenkänning

I följande exempel demonstreras det JSON-svar som returneras av Visuellt innehåll för en bild som innehåller ett enda människo ansikte.

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

Nästa exempel visar det JSON-svar som returneras för en bild som innehåller flera mänskliga ansikten.

![Syn analys av familjens foto ansikte](./Images/family_photo_face.png)

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

Funktionen för ansikts igenkänning är en del av API: et [analys av avbildning](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Du kan anropa API: et via en inbyggd SDK eller via REST-anrop. Inkludera `Faces` i **visualFeatures** -Frågeparametern. När du sedan får det fullständiga JSON-svaret ska du bara parsa strängen för innehållet i `"faces"` avsnittet.

* [Snabb start: Visuellt innehåll REST API-eller klient bibliotek](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
