---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d74279c9c4d5d88e5837046e9484f5af7aad1442
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001216"
---
Med den `ApplicationInsights` här inställningen kan du lägga till stöd för [Azure Application Insights](/azure/application-insights) -telemetri till din behållare. Application Insights ger detaljerad övervakning av din behållare. Du kan enkelt övervaka din behållare för tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i din behållare.

I följande tabell beskrivs de konfigurations inställningar som stöds i `ApplicationInsights` avsnittet.

|Obligatorisk| Name | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Nej| `InstrumentationKey` | Sträng | Instrumentation-nyckeln för Application Insights-instansen som telemetridata för behållaren skickas till. Mer information finns i [Application Insights för ASP.net Core](../articles/azure-monitor/app/asp-net-core.md). <br><br>Exempel:<br>`InstrumentationKey=123456789`|