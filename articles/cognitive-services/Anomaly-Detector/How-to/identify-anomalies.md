---
title: 'Använda API: t för avvikelse detektor i dina tids serie data'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du identifierar avvikelser i dina data antingen som en batch eller strömmande data.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: mbullwin
ms.openlocfilehash: 74f891ba7f5b400b5782565e670539167f4e2464
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97703440"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Gör så här: Använd API: t för avvikelse detektor i dina tids serie data  

[API: t för avvikelse detektor](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) ger två metoder för avvikelse identifiering. Du kan antingen identifiera avvikelser som en batch i hela tids serien eller när dina data genereras genom att identifiera avvikelse status för den senaste data punkten. Identifierings modellen returnerar avvikelse resultat tillsammans med varje data punkts förväntade värde och de övre och lägre avvikelse identifierings gränserna. Du kan använda dessa värden för att visualisera intervallet av normala värden och avvikelser i data.

## <a name="anomaly-detection-modes"></a>Avvikelse identifierings lägen 

API: t för avvikelse detektor tillhandahåller identifierings lägen: batch och strömning.

> [!NOTE]
> Följande URL-adresser för begäran måste kombineras med lämplig slut punkt för din prenumeration. Exempelvis: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Batch-identifiering

Om du vill identifiera avvikelser i en batch med data punkter under ett angivet tidsintervall använder du följande URI för begäran med dina tids serie data: 

`/timeseries/entire/detect`. 

Genom att skicka dina Time Series-data samtidigt genererar API: t en modell med hela serien och analyserar varje data punkt med den.  

### <a name="streaming-detection"></a>Strömmande identifiering

För att kontinuerligt identifiera avvikelser vid strömmande data använder du följande URI för begäran med din senaste data punkt: 

`/timeseries/last/detect'`. 

Genom att skicka nya data punkter när du genererar dem kan du övervaka dina data i real tid. En modell skapas med de data punkter som du skickar och API: et avgör om den senaste punkten i tids serien är en avvikelse.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Justera nedre och övre avvikelse identifierings gränser

Som standard beräknas de övre och nedre gränserna för avvikelse identifiering med `expectedValue` , `upperMargin` och `lowerMargin` . Om du behöver olika gränser rekommenderar vi att du använder en `marginScale` till `upperMargin` eller `lowerMargin` . Gränserna beräknas enligt följande:

|Gräns  |Beräkning  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

I följande exempel visas ett API-resultat för avvikelse detektor vid olika sensitivities.

### <a name="example-with-sensitivity-at-99"></a>Exempel med känslighet vid 99

![Standard känslighet](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Exempel med känslighet vid 95

![99-känslighet](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Exempel med känslighet vid 85

![85-känslighet](../media/sensitivity_85.png)

## <a name="next-steps"></a>Nästa steg

* [Vad är API:et för avvikelseidentifiering?](../overview.md)
* [Snabb start: identifiera avvikelser i dina tids serie data med hjälp av avvikelse detektorn](../quickstarts/client-libraries.md)
