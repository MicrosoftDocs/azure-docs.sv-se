---
title: Word-justering – översättare
titleSuffix: Azure Cognitive Services
description: Om du vill ta emot information om anpassning använder du metoden Översätt och inkluderar den valfria includeAlignment-parametern.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 8368ca0ca4c3f2f0ab3cb88a5d54295d71451636
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98898027"
---
# <a name="how-to-receive-word-alignment-information"></a>Så här tar du emot information om Word-anpassning

## <a name="receiving-word-alignment-information"></a>Ta emot Word-justerings information
Om du vill ta emot information om anpassning använder du metoden Översätt och inkluderar den valfria includeAlignment-parametern.

## <a name="alignment-information-format"></a>Format för justerings information
Justeringen returneras som ett sträng värde av följande format för varje ord i källan. Informationen för varje ord avgränsas med ett blank steg, inklusive för icke-separerade språk (skript) som kinesiska:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Exempel på justerings sträng: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Kolon separerar exempelvis start-och slut index, strecket separerar språken och avståndet separerar orden. Ett ord kan justeras med noll, ett eller flera ord på det andra språket och de justerade orden kan vara icke-sammanhängande. Om ingen justerings information är tillgänglig är justerings elementet tomt. Metoden returnerar inga fel i detta fall.

## <a name="restrictions"></a>Begränsningar
Justering returneras bara för en delmängd av språk paren i detta läge:
* från engelska till ett annat språk.
* från andra språk till engelska utom förenklad kinesiska, traditionell kinesiska och lettiska till engelska
* från japanska till koreanska eller från koreanska till japanska får du ingen justerings information om meningen är en konserverad översättning. Exempel på en konserverad översättning är "det här är ett test", "Jag älskar dig" och andra meningar med hög frekvens.

## <a name="example"></a>Exempel

Exempel-JSON

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj&quot;: &quot;0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
