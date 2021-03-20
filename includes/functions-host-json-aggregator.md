---
title: ta med fil
description: ta med fil
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c63cee1c451918569e9b7aa2e76209e8069d86ac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92167668"
---
Anger hur många funktions anrop som ska aggregeras när du [beräknar mått för Application Insights](../articles/azure-functions/configure-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Egenskap |Standardvärde  | Beskrivning |
|---------|---------|---------| 
|batchSize|1000|Maximalt antal begär Anden som ska aggregeras.| 
|flushTimeout|00:00:30|Maximal tids period som ska aggregeras.| 

Funktions anrop sammanställs när den första av de två gränserna nås.
