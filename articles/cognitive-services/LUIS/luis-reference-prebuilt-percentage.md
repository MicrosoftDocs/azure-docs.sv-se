---
title: Fördefinierad enhet i procent – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller fördefinierad enhets information i procent i Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: bb64a32e2bdd3976fba3ce63433b13eb4891afc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541704"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Fördefinierad procent andel av en LUIS-app
Procent siffror kan visas som bråktal, `3 1/2` eller som procent, `2%` . Eftersom entiteten redan har tränats behöver du inte lägga till exempel yttranden som innehåller procent till program avsikterna. Enheten i procent stöds i [många kulturer](luis-reference-prebuilt-entities.md).

## <a name="types-of-percentage"></a>Procent typer
Procent andelen hanteras från GitHub-lagringsplatsen för [identifierare – text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114)

## <a name="resolution-for-prebuilt-percentage-entity"></a>Lösning för fördefinierad procent entitet

Följande enhets objekt returneras för frågan:

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON- `verbose` parameter har angetts till `false` :

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 utförlig Response](#tab/V3-verbose)
Följande JSON- `verbose` parameter har angetts till `true` :

```json
"entities": {
    "percentage": [
        2
    ],
    "$instance": {
        "percentage": [
            {
                "type": "builtin.percentage",
                "text": "2%",
                "startIndex": 36,
                "length": 2,
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

I följande exempel visas en lösning på enheten **Builtin. procent** .

```json
"entities": [
    {
        "entity": "2%",
        "type": "builtin.percentage",
        "startIndex": 36,
        "endIndex": 37,
        "resolution": {
        "value": "2%"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om entiteterna [ordning](luis-reference-prebuilt-ordinal.md), [antal](luis-reference-prebuilt-number.md)och [temperatur](luis-reference-prebuilt-temperature.md) .
