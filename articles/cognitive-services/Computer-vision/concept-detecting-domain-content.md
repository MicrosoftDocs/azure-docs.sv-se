---
title: Domänspecifikt innehåll – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig hur du anger en domän för bildkategorisering för att returnera mer detaljerad information om en bild.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 530ca81cedad06c949323889cc02d2a233dd0c02
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778831"
---
# <a name="detect-domain-specific-content"></a>Identifiera domänspecifikt innehåll

Förutom taggning och kategorisering på hög nivå stöder Visuellt innehåll ytterligare domänspecifik analys med hjälp av modeller som har tränats med specialiserade data.

Det finns två sätt att använda domänspecifika modeller: på egen hand (begränsad analys) eller som en förbättring av kategoriseringsfunktionen.

### <a name="scoped-analysis"></a>Omfångsanalys

Du kan analysera en bild med bara den valda domänspecifika modellen genom att anropa [API:et \<model\> Models//Analyze.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b)

Följande är ett JSON-exempelsvar som returneras av **modeller/kändisar/analysera** API för den angivna bilden:

![Satya Nadella, leende](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>Förbättrad kategoriseringsanalys

Du kan också använda domänspecifika modeller för att komplettera allmän bildanalys. Du gör detta som en del av en högnivåkategorisering  genom att ange domänspecifika modeller i informationsparametern för [API-anropet](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) Analysera. [](concept-categorizing-images.md)

I det här fallet anropas taxonomi-klassificeraren med 86 kategorier först. Om någon av de identifierade kategorierna har en matchande domänspecifik modell skickas även avbildningen genom den modellen och resultatet läggs till.

Följande JSON-svar visar hur domänspecifik analys kan inkluderas som `detail` nod i en bredare kategoriseringsanalys.

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>Lista de domänspecifika modellerna

För närvarande Visuellt innehåll stöd för följande domänspecifika modeller:

| Name | Beskrivning |
|------|-------------|
| Kändisar | Kändisigenkänning, stöds för bilder som klassificeras i `people_` kategorin |
| Landmärken | Landmärkesigenkänning, stöds för bilder klassificerade i `outdoor_` `building_` kategorierna eller |

Om du [anropar](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f20e) MODELL-API:et returneras den här informationen tillsammans med de kategorier som varje modell kan tillämpas på:

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [om kategorisering av bilder](concept-categorizing-images.md).
