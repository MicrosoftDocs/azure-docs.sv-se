---
title: Fördefinierad enhet för LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller fördefinierad enhets information för en fras i Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 02a48c8d106795b970d573bb71f52ba92e0a90ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91541993"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>fördefinierad enhets fras för en LUIS-app
Entiteten nyckel fras extraherar en rad olika nyckel fraser från en uttryck. Du behöver inte lägga till exempel yttranden som innehåller en-fras till programmet. Entiteten för en funktions fras stöds i [många kulturer](luis-language-support.md#languages-supported) som en del av funktionerna för [text analys](../text-analytics/overview.md) .

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Lösning för fördefinierad enhet för en enhets fras

Följande enhets objekt returneras för frågan:

`where is the educational requirements form for the development and engineering group`

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON- `verbose` parameter har angetts till `false` :

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 utförlig Response](#tab/V3-verbose)
Följande JSON- `verbose` parameter har angetts till `true` :

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "educational requirements",
                "startIndex": 13,
                "length": 24,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "development",
                "startIndex": 51,
                "length": 11,
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

I följande exempel visas upplösningen för entiteten **Builtin.**

```json
"entities": [
    {
        "entity": "development",
        "type": "builtin.keyPhrase",
        "startIndex": 51,
        "endIndex": 61
    },
    {
        "entity": "educational requirements",
        "type": "builtin.keyPhrase",
        "startIndex": 13,
        "endIndex": 36
    }
]
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om enheterna [procent](luis-reference-prebuilt-percentage.md), [antal](luis-reference-prebuilt-number.md)och [ålder](luis-reference-prebuilt-age.md) .
