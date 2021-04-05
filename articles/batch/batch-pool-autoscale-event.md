---
title: Autoskalning av Azure Batch pool
description: Referens för autoskalning av batch-pool, som genereras när en Pools automatiska skalning körs. Innehållet i loggen kommer att visa den automatiska skalnings formeln och utvärderings resultatet för poolen.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e548ed484e5f683a8f79434ce4095ac66900f01a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91852177"
---
# <a name="pool-autoscale-event"></a>Händelse för poolautoskalning

 Den här händelsen genereras när en automatisk skalning av pooler körs. Innehållet i loggen kommer att visa den automatiska skalnings formeln och utvärderings resultatet för poolen.

 I följande exempel visas innehållet i en pool AutoScale-händelse för automatisk skalning av pooler som misslyckades på grund av otillräckliga exempel data.

```
{
    "id": "myPool1",
    "timestamp": "2020-09-21T18:59:56.204Z",
    "formula": "...",
    "results": "...",
    "error": {
        "code": "InsufficientSampleData",
        "message": "Autoscale evaluation failed due to insufficient sample data",
        "values": [{
                "name": "Message",
                "value": "Line 15, Col 44: Insufficient data from data set: $RunningTasks wanted 70%, received 50%"
            }
        ]
    }
}
```

|Element|Typ|Kommentarer|
|-------------|----------|-----------|
|`id`|Sträng|Poolens ID.|
|`timestamp`|DateTime|Tidsstämpeln när automatisk skalning körs.|
|`formula`|Sträng|Formeln som definierats för automatisk skalning.|
|`results`|Sträng|Utvärderings resultat för alla variabler som används i formeln.|
|[`error`](#error)|Komplex typ|Det detaljerade felet för automatisk skalning.|

###  <a name="error"></a><a name="error"></a> Fels

|Elementnamn|Typ|Kommentarer|
|------------------|----------|-----------|
|`code`|Sträng|En identifierare för det automatiska skalnings felet. Koder är invarianter och är avsedda att konsumeras program mässigt.|
|`message`|Sträng|Ett meddelande som beskriver felet för automatisk skalning som är avsett att vara lämpligt för visning i ett användar gränssnitt.|
|`values`|Matris|Lista över namn/värde-par som beskriver mer information om det automatiska skalnings felet.|
