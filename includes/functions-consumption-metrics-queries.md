---
ms.openlocfilehash: 0bae8946b7d81fbf45698cefe7c8b00bbc94d00c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92168126"
---
#### <a name="determine-memory-usage"></a>Fastställa minnes användning 

Under **övervakning** väljer du **loggar (analys)** och kopierar sedan följande telemetri-fråga och klistrar in den i frågefönstret och väljer **Kör**. Den här frågan returnerar den totala minnes användningen vid varje prov tid.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

Resultatet ser ut som i följande exempel:

| tidsstämpel \[ UTC\]          | name          | värde       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14 \. 947 am | Privata byte | 209 932 288 |
| 9/12/2019, 1:06:14 \. 994 am | Privata byte | 212 189 184 |
| 9/12/2019 1:06:30 \. 010 am | Privata byte | 231 714 816 |
| 9/12/2019, 1:07:15 \. 040 am | Privata byte | 210 591 744 |
| 9/12/2019, 1:12:16 \. 285 am | Privata byte | 216 285 184 |
| 9/12/2019, 1:12:31 \. 376 am | Privata byte | 235 806 720 |

#### <a name="determine-duration"></a>Bestäm varaktighet 

Azure Monitor spårar mått på resurs nivå, som för functions är Function-appen. Application Insights integration genererar mått per funktion. Här är ett exempel på en analys fråga som hämtar den genomsnittliga varaktigheten för en funktion:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16 \. 087                     |
| QueueTrigger MaxDurationMs | 90 \. 249                     |
| QueueTrigger MinDurationMs | 8 \. 522                      |
