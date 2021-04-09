---
title: ta med fil
description: ta med fil
services: application-insights
author: lgayhardt
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: lagayhar
ms.custom: include file
ms.openlocfilehash: 1c4f6b876a4aa80c7e51f2bb3ca88234203d0daa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726191"
---
Det finns vissa begränsningar för antalet mått och händelser per program, det vill säga per Instrumentation-nyckel. Gränserna beror på vilken [prisplan](https://azure.microsoft.com/pricing/details/application-insights/) du väljer.

| Resurs | Standardgräns | Anteckning
| --- | --- | --- |
| Totala data per dag | 100 GB | Du kan minska datamängden genom att ange ett tak. Om du behöver mer data kan du öka gränsen i portalen, upp till 1 000 GB. Skicka e-post till för kapaciteter som är större än 1 000 GB AIDataCap@microsoft.com .
| Begränsning | 32 000 händelser/sekund | Gränser är mätt under en minut.
| Data lagrings loggar | [30-730 dagar](../articles/azure-monitor/app/pricing.md#change-the-data-retention-period)  | Den här resursen är för [loggar](../articles/azure-monitor/logs/log-query-overview.md).
| Mått för datakvarhållning | 90 dagar| Den här resursen är för [Metrics Explorer](../articles/azure-monitor/essentials/metrics-charts.md).
| [Flerstegstest för tillgänglighet](../articles/azure-monitor/app/availability-multistep.md) (kvarhållning av detaljerade resultat) | 90 dagar | Den här resursen innehåller detaljerade resultat för varje steg.
| Maximal storlek för telemetri | 64 kB |
| Maximalt antal telemetri objekt per batch | 64 KB |
| Namnlängd för egenskaper och mätvärden | 150 | Se [typ scheman](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Stränglängd för egenskapsvärde | 8 192  | Se [typ scheman](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Längd för spårnings- och undantagsmeddelande | 32 768  | Se [typ scheman](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| [Tillgänglighetstester](../articles/azure-monitor/app/monitor-web-app-availability.md) (antal per app) | 100 |
| [Data](../articles/azure-monitor/app/profiler.md) kvarhållning för profilering | 5 dagar |
| [Profilerade](../articles/azure-monitor/app/profiler.md) data har skickats per dag | 10 GB |

Mer information finns i [Om priser och kvoter i Application Insights](../articles/azure-monitor/app/pricing.md).