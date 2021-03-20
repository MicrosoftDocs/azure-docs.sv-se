---
title: Pattern. all entitetstyp – LUIS
titleSuffix: Azure Cognitive Services
description: Mönster. det finns en plats hållare med variabel längd som bara används i ett mönsters mall uttryck för att markera var entiteten börjar och slutar.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.openlocfilehash: ec23be3709cebc534c059a21c52452abff683b18
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91542214"
---
# <a name="patternany-entity"></a>Entiteten Pattern.any

Mönster. det finns en plats hållare med variabel längd som bara används i ett mönsters mall uttryck för att markera var entiteten börjar och slutar.  

Mönster. alla entiteter måste markeras i exempel på [mönster](luis-how-to-model-intent-pattern.md) mal len, inte avsikten med användar exempel.

**Entiteten passar bra när:**

* Entitetens slut kan förväxlas med den återstående texten i uttryck.

## <a name="usage"></a>Användning

Ett klient program som söker efter böcker som baseras på rubrik, mönstret. alla extraherar den fullständiga titeln. En mall som uttryck använder mönster. alla för den här bok sökningen är `Was {BookTitle} written by an American this year[?]` .

I följande tabell har varje rad två versioner av uttryck. Det främsta uttryck är hur LUIS för första gången ser uttryck. Det är inte tydligt var bok rubriken börjar och slutar. Den nedre uttryck använder ett mönster. en entitet för att markera början och slutet av entiteten.

|Uttryck med entiteten i fetstil|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Var **det man som feltagit sin fru för en hatt och andra kliniska** visare som skrevs av ett amerikanskt år?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Var **hälften av ström spar läge i Frog Pajamas** Skrivet av ett amerikanskt år?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Var **den specifika ledsenhet av citron tårta: en nya** skriven av ett amerikanskt år?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Fanns **det en Wocket i min ficka!** Skrivet av ett amerikanskt år?|
||



## <a name="example-json"></a>Exempel-JSON

Överväg följande fråga:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

Med det inbäddade formulär namnet som ska extraheras som ett mönster. alla:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-response"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)

Detta är JSON om anges `verbose=false` i frågesträngen:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Detta är JSON om anges `verbose=true` i frågesträngen:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Nästa steg

I den här [självstudien](luis-tutorial-pattern.md)använder du **mönstret. en** entitet för att extrahera data från yttranden där yttranden är välformaterade och där data slutet kan lätt förväxlas med de återstående orden i uttryck.
