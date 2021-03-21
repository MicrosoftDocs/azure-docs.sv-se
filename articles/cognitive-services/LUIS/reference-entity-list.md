---
title: Lista enhets typ – LUIS
description: Lista entiteter representerar en fast, avslutad uppsättning relaterade ord tillsammans med deras synonymer. LUIS identifierar inte ytterligare värden för List-entiteter. Använd rekommendations funktionen om du vill se förslag på nya ord baserade på den aktuella listan.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 410b33b5c6078d096fa4b2acaa7b49bc14c95e31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97608280"
---
# <a name="list-entity"></a>Lista entitet

Lista entiteter representerar en fast, avslutad uppsättning relaterade ord tillsammans med deras synonymer. LUIS identifierar inte ytterligare värden för List-entiteter. Använd **rekommendations** funktionen om du vill se förslag på nya ord baserade på den aktuella listan. Om det finns mer än en List-entitet med samma värde returneras varje entitet i slut punkts frågan.

En lista entitet har inte registrerats på datorn. Det är en exakt text matchning. LUIS markerar valfri matchning till ett objekt i en lista som en entitet i svaret.

**Entiteten passar bra när text data:**

* Är en känd uppsättning.
* Ändras inte ofta. Om du behöver ändra listan ofta eller vill att listan ska expanderas själv, är en enkel entitet som ökar med en fras lista ett bättre alternativ.
* Uppsättningen inte överskrider de högsta [gränserna](luis-limits.md) för LUIS för den här entitetstypen.
* Texten i uttryck är inte Skift läges känslig med en synonym eller ett kanoniskt namn. LUIS använder inte listan bortom matchningen. Fuzzy Matching, igenkänning, plural och andra variationer löses inte med en List-entitet. Om du vill hantera variationer bör du överväga att använda ett [mönster](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) med valfri textsyntax.

![lista entitet](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Exempel. JSON att importera till List-entitet

  Du kan importera värden till en befintlig List-entitet med hjälp av följande. JSON-format:

  ```JSON
  [
      {
          "canonicalForm": "Blue",
          "list": [
              "navy",
              "royal",
              "baby"
          ]
      },
      {
          "canonicalForm": "Green",
          "list": [
              "kelly",
              "forest",
              "avacado"
          ]
      }
  ]
  ```

## <a name="example-json-response"></a>Exempel på JSON-svar

Anta att appen har en lista med namnet `Cities` , som tillåter variationer av Orts namn, till exempel stads-TAC, flyg plats kod (Sea), post nummer (98101) och telefon rikt nummer (206).

|List objekt|Objekt synonymer|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

I föregående uttryck `paris` mappas ordet till Paris-objektet som en del av `Cities` entiteten lista. List entiteten matchar både objektets normaliserade namn och objektets synonymer.

#### <a name="v2-prediction-endpoint-response"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)

Detta är JSON om anges `verbose=false` i frågesträngen:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Detta är JSON om anges `verbose=true` i frågesträngen:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
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
|Lista entitet|`Cities`|`paris`|

## <a name="next-steps"></a>Nästa steg

Lär dig mer om entiteter:

* [Begrepp](luis-concept-entity-types.md)
* [Så här skapar du](luis-how-to-add-entities.md)
