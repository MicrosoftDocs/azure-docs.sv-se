---
title: Sentiment-analys – LUIS
titleSuffix: Azure Cognitive Services
description: Om sentiment-analysen har kon figurer ATS innehåller LUIS JSON-svaret sentiment analys.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: 2e8e5a127741625fde7910aaabd421836148fc35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018675"
---
# <a name="sentiment-analysis"></a>Attitydanalys
Om sentiment-analysen har kon figurer ATS innehåller LUIS JSON-svaret sentiment analys. Läs mer om sentiment-analys i [textanalys](../text-analytics/index.yml) -dokumentationen.

LUIS använder Textanalys v2. 

## <a name="resolution-for-sentiment"></a>Lösning för sentiment

Sentiment-data är ett resultat mellan 1 och 0 som anger positiv (närmare 1) eller negativ (närmare 0) sentiment av data.

#### <a name="english-language"></a>[Engelskt språk](#tab/english)

När kulturen är är `en-us` svaret:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Andra språk](#tab/other-languages)

För alla andra kulturer är svaret:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).