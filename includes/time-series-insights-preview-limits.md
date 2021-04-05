---
title: ta med fil
description: ta med fil
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 016ad0e11f3378dba887e0a235f235fa91e3aa03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98109434"
---
### <a name="property-limits"></a>Egenskaps gränser

Azure Time Series Insights egenskaps gränser har ökat till 1 000 för varmt lagring och ingen egenskaps gräns för kall lagring. Angivna händelse egenskaper har motsvarande JSON-, CSV-och diagram kolumner som du kan visa i [Azure Time Series Insights Gen2 Explorer](../articles/time-series-insights/quickstart-explore-tsi.md).

| SKU | Maximalt antal egenskaper |
| --- | --- |
| Gen2 (L1) | 1 000 egenskaper (kolumner) för varm lagring och obegränsad till kall lagring|
| Gen1 (S1) | 600 egenskaper (kolumner) |
| Gen1 (S2) | 800 egenskaper (kolumner) |

### <a name="streaming-ingestion"></a>Strömningsinmatning

* Det finns högst två [händelse källor](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) per miljö.

* De bästa metoderna och allmänna rikt linjer för händelse källor hittar du [här](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)

* Som standard kan Azure Time Series Insights Gen2 mata in inkommande data med en hastighet på **upp till 1 megabyte per sekund (Mbit/s) per Azure Time Series Insights Gen2-miljö**. Det finns ytterligare begränsningar [per nav-partition](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits). Du kan tillhandahålla hastigheter på upp till 2 Mbit/s genom att skicka ett support ärende via Azure Portal. Läs mer i [data flödes gränser för strömning](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md).

### <a name="api-limits"></a>API-gränser

REST API gränser för Azure Time Series Insights Gen2 anges i [REST API referens dokumentation](/rest/api/time-series-insights/preview#limits-1).