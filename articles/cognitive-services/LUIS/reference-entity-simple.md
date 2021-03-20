---
title: Enkel entitetstyp – LUIS
titleSuffix: Azure Cognitive Services
description: En enkel entitet beskriver ett enda begrepp från Machine-Learning-kontexten. Lägg till en fras lista när du använder en enkel entitet för att förbättra resultaten.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.openlocfilehash: 384d3df2de551e7c79f13a0fe47ffb26c7825f1b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91539294"
---
# <a name="simple-entity"></a>Enkel entitet

En enkel entitet är en generisk entitet som beskriver ett enda koncept och som har lärts från kontexten för maskin inlärning. Eftersom enkla entiteter vanligt vis är namn, till exempel företags namn, produkt namn eller andra kategorier av namn, lägger du till en [fras lista](luis-concept-feature.md) när du använder en enkel entitet för att öka signalen av de namn som används.

**Entiteten passar bra när:**

* Data formateras inte konsekvent, utan anger samma sak.

![enkel entitet](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Exempel-JSON

`Bob Jones wants 3 meatball pho`

I föregående uttryck `Bob Jones` är märkt som en enkel `Customer` entitet.

Data som returneras från slut punkten innehåller enhets namnet, den identifierade texten från uttryck, platsen för den identifierade texten och poängen:

#### <a name="v2-prediction-endpoint-response"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)

Detta är JSON om anges `verbose=false` i frågesträngen:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|Data objekt|Entitetsnamn|Värde|
|--|--|--|
|Enkel entitet|`Customer`|`bob jones`|

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mönstermatchningssyntax](reference-pattern-syntax.md)