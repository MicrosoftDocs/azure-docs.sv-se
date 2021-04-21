---
title: Bildkategorisering – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som rör bildkategoriseringsfunktionen i Visuellt innehåll API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 97a6a08730ae0c87df3b65185d48297e2338e69b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768115"
---
# <a name="categorize-images-by-subject-matter"></a>Kategorisera bilder efter ämne

Förutom taggar och en beskrivning returnerar Visuellt innehåll de taxonomibaserade kategorier som identifierats i en bild. Till skillnad från taggar organiseras kategorier i en överordnad/underordnad hierarki och det finns färre av dem (86, till skillnad från tusentals taggar). Alla kategorinamn är på engelska. Kategorisering kan göras på egen hand eller tillsammans med den nyare taggmodellen.

## <a name="the-86-category-concept"></a>Konceptet 86-kategori

Datorseende kan kategorisera en bild brett eller specifikt med hjälp av listan med 86 kategorier i följande diagram. Läs den fullständiga taxonomin i textformat i [Kategoritaxonomi](category-taxonomy.md).

![Grupperade listor över alla kategorier i kategori taxonomi](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Exempel på bildkategorisering

Följande JSON-svar illustrerar vad Visuellt innehåll returnerar när du kategoriserar exempelbilden baserat på dess visuella funktioner.

![En kvinna på taket på en byggnad](./Images/woman_roof.png)

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

I följande tabell visas en typisk bilduppsättning och den kategori som returneras av Visuellt innehåll för varje bild.

| Bild | Kategori |
|-------|----------|
| ![Fyra personer samlas som en familj](./Images/family_photo.png) | people_group |
| ![En hund som sitter i ett snigt fält](./Images/cute_dog.png) | animal_dog |
| ![En person som står på en sten på ett berg vid en solnedgång](./Images/mountain_vista.png) | outdoor_mountain |
| ![En hög med brödroller på en tabell](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>Använda API:et

Kategoriseringsfunktionen är en del [](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) av Analysera bild API.et. Du kan anropa det här API:et via en intern SDK eller via REST-anrop. Inkludera `Categories` i **frågeparametern visualFeatures.** När du sedan får det fullständiga JSON-svaret parsar du helt enkelt strängen för innehållet i `"categories"` avsnittet.

* [Snabbstart: Visuellt innehåll REST API eller klientbibliotek](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om relaterade begrepp [för att tagga](concept-tagging-images.md) bilder [och beskriva bilder.](concept-describing-images.md)
