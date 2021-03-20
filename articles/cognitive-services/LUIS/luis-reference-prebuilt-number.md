---
title: Fördefinierad entitet för nummer – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller information om fördefinierad entitetsinformation i Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 13594886b83d4474ee2531185db5868a5198ca64
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541974"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Numrerad fördefinierad entitet för en LUIS-app
Det finns många sätt på vilka numeriska värden används för att kvantifiera, uttrycka och beskriva delar av information. I den här artikeln beskrivs bara några av de möjliga exemplen. LUIS tolkar variationerna i User yttranden och returnerar konsekventa numeriska värden. Eftersom entiteten redan har tränats behöver du inte lägga till exempel yttranden som innehåller nummer till program avsikterna.

## <a name="types-of-number"></a>Typer av tal
Antalet hanteras från GitHub-lagringsplatsen för [identifierare – text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml)

## <a name="examples-of-number-resolution"></a>Exempel på nummer upplösning

| Yttrande        | Entitet   | Lösning |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      |
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LUIS innehåller det identifierade värdet för en **`builtin.number`** entitet i `resolution` fältet i JSON-svaret som returneras.

## <a name="resolution-for-prebuilt-number"></a>Lösning för fördefinierat nummer

Följande enhets objekt returneras för frågan:

`order two dozen eggs`

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON- `verbose` parameter har angetts till `false` :

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 utförlig Response](#tab/V3-verbose)

Följande JSON- `verbose` parameter har angetts till `true` :

```json
"entities": {
    "number": [
        24
    ],
    "$instance": {
        "number": [
            {
                "type": "builtin.number",
                "text": "two dozen",
                "startIndex": 6,
                "length": 9,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2-svar](#tab/V2)

I följande exempel visas ett JSON-svar från LUIS, som innehåller upplösningen för värdet 24, för uttryck "två dussin".

```json
"entities": [
  {
    "entity": "two dozen",
    "type": "builtin.number",
    "startIndex": 6,
    "endIndex": 14,
    "resolution": {
      "subtype": "integer",
      "value": "24"
    }
  }
]
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om [valutan](luis-reference-prebuilt-currency.md), [ordnings talet](luis-reference-prebuilt-ordinal.md)och [procent andelen](luis-reference-prebuilt-percentage.md).
