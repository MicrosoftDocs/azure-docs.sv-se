---
title: Använd Azure Log Analytics för att undersöka Application Gateway brand Väggs loggar för webb program
description: Den här artikeln visar hur du kan använda Azure Log Analytics för att undersöka Application Gateway brand Väggs loggar för webb program
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/06/2019
ms.author: victorh
ms.openlocfilehash: c928d4234e82e7c43c2365e20d57d0b97d4dbc1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588999"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-waf-logs"></a>Använd Log Analytics för att undersöka Application Gateway Länkar för brand vägg för webbaserade program (WAF)

När din Application Gateway WAF fungerar kan du aktivera loggar för att kontrol lera vad som händer med varje begäran. Brand Väggs loggar ger insikt om vad WAF utvärderar, matchar och blockerar. Med Azure Monitor Log Analytics kan du undersöka data i brand Väggs loggarna för att ge ännu fler insikter. Mer information om hur du skapar en arbets yta för Log Analytics finns [i skapa en Log Analytics arbets yta i Azure Portal](../../azure-monitor/logs/quick-create-workspace.md). Mer information om logg frågor finns i [Översikt över logg frågor i Azure Monitor](../../azure-monitor/logs/log-query-overview.md).

## <a name="import-waf-logs"></a>Importera WAF-loggar

Information om hur du importerar brand Väggs loggar till Log Analytics finns i [backend-hälsa, resurs loggar och mått för Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging). När du har brand Väggs loggar i Log Analytics arbets ytan kan du visa data, skriva frågor, Skapa visualiseringar och lägga till dem i portalens instrument panel.

## <a name="explore-data-with-examples"></a>Utforska data med exempel

Om du vill visa rå data i brand Väggs loggen kan du köra följande fråga:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Detta ser ut ungefär som i följande fråga:

![Log Analytics fråga](../media/log-analytics/log-query.png)

Du kan öka detalj nivån i data och rita diagram eller skapa visualiseringar härifrån. Se följande frågor som utgångs punkt:

### <a name="matchedblocked-requests-by-ip"></a>Matchade/blockerade begär Anden efter IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Matchade/blockerade förfrågningar per URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Främsta matchade regler

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>De fem främsta matchade regel grupperna

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Lägg till på din instrument panel

När du har skapat en fråga kan du lägga till den på din instrument panel.  Välj **Fäst vid instrument panelen** längst upp till höger i Log Analytics-arbetsytan. Med föregående fyra frågor fästa på en exempel instrument panel, är det här data som du kan se snabbt:

![Skärm bild som visar en Azure-instrumentpanel där du kan lägga till din fråga.](../media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Nästa steg

[Server dels hälsa, resurs loggar och mått för Application Gateway](../../application-gateway/application-gateway-diagnostics.md)