---
title: Data modell för Metric-telemetri – Azure Application insikter
description: Application Insights data modell för telemetri av mått
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: f8e787b8a94987e7d68e12a0e4e69f4c63e21e19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87320586"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Mått telemetri: Application Insights data modell

Det finns två typer av mått telemetri som stöds av [Application Insights](./app-insights-overview.md): enstaka mått och föraggregerade mått. Enstaka mått är bara ett namn och ett värde. Föraggregerade mått anger det lägsta och högsta värdet för måttet i agg regerings intervallet och standard avvikelsen för det.

Telemetri för föraggregerade mått förutsätter att agg regerings perioden är en minut.

Det finns flera välkända mått namn som stöds av Application Insights. Dessa mått placeras i performanceCounters-tabellen.

Mått som representerar system-och process räknare:

| **.NET-namn**             | **Plattformens oberoende namn** | **REST API namn** | **Beskrivning**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Pågående arbete... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | Total maskin-CPU
| `\Memory\Available Bytes`                 | Pågående arbete... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Visar mängden fysiskt minne, i byte, som är tillgängligt för processer som körs på datorn. Det beräknas genom att summan av utrymmet på minnes listorna nollställs, fri och standby används. Ledigt minne är klart att använda. nollställt minne består av sidor med minne som är fyllda med nollor för att förhindra senare processer från att se data som används i en tidigare process. standby-minne är minne som har tagits bort från en processs aktiva sidmängd (dess fysiska minne) en väg till disk, men är fortfarande tillgängligt för att återkallas. Se [minnes objekt](/previous-versions/ms804008(v=msdn.10))
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Pågående arbete... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | CPU för processen som är värd för programmet
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Pågående arbete... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | minne som används av processen som är värd för programmet
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Pågående arbete... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | antalet I/O-åtgärder som körs genom processen som är värd för programmet
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Pågående arbete... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | antal begär Anden som bearbetas av programmet 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Pågående arbete... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | frekvens av undantag som har utlösts av programmet
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Pågående arbete... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | genomsnittlig körnings tid för begär Anden
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Pågående arbete... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | antal begär Anden som väntar på bearbetning i en kö

## <a name="name"></a>Name

Namnet på det mått som du vill se i Application Insights Portal och användar gränssnitt. 

## <a name="value"></a>Värde

Enskilt värde för mått. Summan av enskilda mått för aggregation.

## <a name="count"></a>Antal

Mått vikt för det aggregerade måttet. Ska inte anges för ett mått.

## <a name="min"></a>Min

Minsta värde för det aggregerade måttet. Ska inte anges för ett mått.

## <a name="max"></a>Max

Högsta värde för det aggregerade måttet. Ska inte anges för ett mått.

## <a name="standard-deviation"></a>Standardavvikelse

Standard avvikelsen för det aggregerade måttet. Ska inte anges för ett mått.

## <a name="custom-properties"></a>Anpassade egenskaper

Mått med den anpassade egenskaps `CustomPerfCounter` uppsättningen som `true` anger att måttet representerar Windows prestanda räknare. Dessa mått placeras i performanceCounters-tabellen. Inte i customMetrics. Dessutom parsas namnet på det här måttet för att extrahera kategori, räknare och instans namn.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder [Application Insights API för anpassade händelser och mått](./api-custom-events-metrics.md#trackmetric).
- Se [data modell](data-model.md) för Application Insights typer och data modell.
- Kolla ut [plattformar](./platforms.md) som stöds av Application Insights.

