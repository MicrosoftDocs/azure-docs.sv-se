---
title: Sammansatt entitetstyp – LUIS
titleSuffix: Azure Cognitive Services
description: En sammansatt entitet består av andra entiteter, till exempel färdiga entiteter, enkla, reguljära uttryck och list enheter. Separata entiteter utgör en hel entitet.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 3d936555a4d9a9c80718a24ba892c762f5a8354a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "97608400"
---
# <a name="composite-entity"></a>Sammansatt entitet

En sammansatt entitet består av andra entiteter, till exempel färdiga entiteter, enkla, reguljära uttryck och list enheter. Separata entiteter utgör en hel entitet.

> [!CAUTION]
> Den här entiteten är **inaktuell**. Migrera till [entiteten för maskin inlärning](reference-entity-machine-learned-entity.md).

**Den här entiteten passar bra när data:**

* Är relaterade till varandra.
* De är relaterade till varandra i yttrandet.
* Använd olika typer av enheter.
* Måste grupperas och bearbetas av klient programmet som en informations enhet.
* Ha en rad olika användar yttranden som kräver maskin inlärning.

![sammansatt entitet](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Exempel-JSON

Betrakta en sammansatt entitet av fördefinierad `number` och `Location::ToLocation` med följande uttryck:

`book 2 tickets to cairo`

Observera att `2` antalet och `cairo` ToLocation innehåller ord mellan dem som inte ingår i någon av entiteterna. Den gröna understrykningen, som används i en etikettad uttryck på [Luis](luis-reference-regions.md) -webbplatsen, anger en sammansatt entitet.

![Sammansatt entitet](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

Sammansatta entiteter returneras i en `compositeEntities` matris och alla entiteter i den sammansatta returneras också i `entities` matrisen:

```JSON
  "entities": [
    {
      "entity": "2 tickets to cairo",
      "type": "ticketinfo",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.9214487
    },
    {
      "entity": "cairo",
      "type": "builtin.geographyV2.city",
      "startIndex": 18,
      "endIndex": 22
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "subtype": "integer",
        "value": "2"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "ticketinfo",
      "value": "2 tickets to cairo",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "builtin.geographyV2.city",
          "value": "cairo"
        }
      ]
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)

Detta är JSON om anges `verbose=false` i frågesträngen:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ]
        }
    ]
}
```

Detta är JSON om anges `verbose=true` i frågesträngen:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 5,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2.city",
                        "text": "cairo",
                        "startIndex": 18,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "$instance": {
        "ticketinfo": [
            {
                "type": "ticketinfo",
                "text": "2 tickets to cairo",
                "startIndex": 5,
                "length": 18,
                "score": 0.9214487,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
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
|Fördefinierad entitet-nummer|"Builtin. Number"|11.2|
|Fördefinierad entitet – GeographyV2|"Plats:: ToLocation"|Cairo|

## <a name="next-steps"></a>Nästa steg

Lär dig mer om entiteter:

* [Begrepp](luis-concept-entity-types.md)
* [Så här skapar du](luis-how-to-add-entities.md)
