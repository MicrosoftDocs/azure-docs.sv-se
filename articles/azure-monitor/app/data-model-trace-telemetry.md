---
title: Azure Application Insights data modell – trace telemetri
description: Application Insights data modell för trace-telemetri
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 8fea4bbf590816b2ef168a2ed16f197389ee282e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87320552"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Trace telemetri: Application Insights data modell

Trace telemetri (i [Application Insights](./app-insights-overview.md)) representerar `printf` format spårnings satser som är text-genomsökta. `Log4Net`, `NLog` och andra textbaserade logg fils poster översätts till instanser av den här typen. Spårningen har inte några mått som utöknings barhet.

## <a name="message"></a>Meddelande

Spårnings meddelande.

Maxlängd: 32768 tecken

## <a name="severity-level"></a>Allvarlighetsgrad

Allvarlighets grad för spårning. Värdet kan vara `Verbose` ,,, `Information` `Warning` `Error` , `Critical` .

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Nästa steg

- [Utforska .net-spårnings loggar i Application Insights](./asp-net-trace-logs.md).
- [Utforska Java trace-loggar i Application Insights](./java-trace-logs.md).
- Se [data modell](data-model.md) för Application Insights typer och data modell.
- [Skriv anpassad trace-telemetri](./api-custom-events-metrics.md#tracktrace)
- Kolla ut [plattformar](./platforms.md) som stöds av Application Insights.

