---
title: Bild kategorisering – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som rör funktionen för kategorisering av bilder i API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9721ffa807c9adbeb50839113bc64fd23d8eb13f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96533731"
---
# <a name="categorize-images-by-subject-matter"></a>Kategorisera bilder efter ämne

Förutom Taggar och en beskrivning returnerar Visuellt innehåll de taxonomibaserade kategorierna som identifierats i en bild. Till skillnad från Taggar ordnas kategorier i en överordnad/underordnad hereditary-hierarki och det finns färre av dem (86, i stället för tusentals taggar). Alla kategori namn är på engelska. Kategorisering kan göras fristående eller tillsammans med den nyare taggars modellen.

## <a name="the-86-category-concept"></a>Konceptet 86-kategori

Dator vision kan kategorisera en bild brett eller specifikt med hjälp av listan över 86 kategorier i följande diagram. Läs den fullständiga taxonomin i textformat i [Kategoritaxonomi](category-taxonomy.md).

![Grupperade listor över alla kategorier i kategorin taxonomi](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Exempel på avbildnings kategorisering

Följande JSON-svar illustrerar vad Visuellt innehåll returnerar när du kategoriserar exempel bilden baserat på dess visuella funktioner.

![En kvinna på taket för en lägenhets byggnad](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

I följande tabell visas en typisk avbildnings uppsättning och kategorin som returneras av Visuellt innehåll för varje bild.

| Bild | Kategori |
|-------|----------|
| ![Fyra personer träffas tillsammans som en familj](./Images/family_photo.png) | people_group |
| ![En Puppy som sitter i ett gräs fält](./Images/cute_dog.png) | animal_dog |
| ![En person som står på en Mountain rock vid solnedgång](./Images/mountain_vista.png) | outdoor_mountain |
| ![En stapel med bröd roller i en tabell](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>Använda API:et

Kategoriserings funktionen är en del av API: et för [analys av avbildning](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Du kan anropa API: et via en inbyggd SDK eller via REST-anrop. Inkludera `Categories` i **visualFeatures** -Frågeparametern. När du sedan får det fullständiga JSON-svaret ska du bara parsa strängen för innehållet i `"categories"` avsnittet.

* [Snabb start: Visuellt innehåll REST API-eller klient bibliotek](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Nästa steg

Lär dig relaterade begrepp för att [Tagga bilder](concept-tagging-images.md) och att [beskriva bilder](concept-describing-images.md).
