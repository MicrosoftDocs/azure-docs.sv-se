---
title: Övervakning och loggning av brand vägg för Azure-webbprogram
description: Lär dig mer om brand vägg för webbaserade program (WAF) med ytterdörr-övervakning och loggning
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 596374d4f3f188e08a10bd25b36b178cc79a6e57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84808959"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Övervakning och loggning av brand vägg för Azure-webbprogram

Övervakning och loggning av Azure Web Application-brandväggen (WAF) tillhandahålls genom loggning och integrering med Azure Monitor-och Azure Monitor-loggar.

## <a name="azure-monitor"></a>Azure Monitor

WAF med ytterdörr-loggen är integrerad med [Azure Monitor](../../azure-monitor/overview.md). Med Azure Monitor kan du spåra diagnostikinformation, inklusive WAF-aviseringar och loggar. Du kan konfigurera WAF övervakning i den främre dörren i portalen under fliken **diagnostik** eller via tjänsten Azure Monitor direkt.

Från Azure Portal går du till resurs typen frontend-dörr. Från fliken **övervakning** / av **mått** till vänster kan du lägga till **WebApplicationFirewallRequestCount** för att spåra antalet förfrågningar som matchar WAF-regler. Anpassade filter kan skapas baserat på åtgärds typer och regel namn.

:::image type="content" source="../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png" alt-text="WAFMetrics ":::

## <a name="logs-and-diagnostics"></a>Loggar och diagnostik

WAF med front dörren ger detaljerad rapportering om varje hot som identifieras. Loggningen är integrerad med loggar och varningar för Azure Diagnostics och registreras i JSON-format. Dessa loggar kan integreras med [Azure Monitor loggar](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

[FrontdoorAccessLog](../../frontdoor/front-door-diagnostics.md) loggar alla begär Anden. FrontdoorWebApplicationFirewallLog loggar alla förfrågningar som matchar en WAF-regel med schemat nedan:

| Egenskap  | Beskrivning |
| ------------- | ------------- |
|Åtgärd|Åtgärd som vidtas på begäran|
| ClientIp | IP-adressen för klienten som gjorde begäran. Om det fanns ett X-vidarebefordrat – för huvud i begäran, plockas klientens IP-adress från fältet Rubrik. |
| ClientPort | IP-porten för den klient som gjorde begäran. |
| Information|Ytterligare information om den matchande begäran |
|| matchVariableName: http-parameter namn för begäran matchad, till exempel rubrik namn|
|| matchVariableValue: värden som utlöste matchningen|
| Värd | Värd rubriken för matchande begäran |
| Policy | Namnet på den WAF-princip som begäran matchade. |
| PolicyMode | Operations mode i WAF-principen. Möjliga värden är "förebyggande" och "identifiering" |
| RequestUri | Fullständig URI för matchad begäran. |
| RuleName | Namnet på WAF-regeln som begäran matchade. |
| SocketIp | Käll-IP-adressen som visas av WAF. Den här IP-adressen baseras på TCP-session, oberoende av eventuella begärandehuvuden.|
| TrackingReference | Den unika referens strängen som identifierar en begäran som betjänas av en front dörr, som också skickas som X-Azure-ref-huvud till klienten. Krävs för att söka efter information i åtkomst loggarna för en speciell begäran. |

Följande exempel på frågor returnerar WAF-loggar på blockerade begär Anden:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Här är ett exempel på en loggad begäran i WAF-loggen:

``` WAFlogQuerySample
{
    "time":  "2020-06-09T22:32:17.8376810Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoorWebApplicationFirewallLog/Write",
    "properties":
    {
        "clientIP":"xxx.xxx.xxx.xxx",
        "clientPort":"52097",
        "socketIP":"xxx.xxx.xxx.xxx",
        "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
        "ruleName":"Microsoft_DefaultRuleSet-1.1-SQLI-942100",
        "policy":"WafDemoCustomPolicy",
        "action":"Block",
        "host":"wafdemofrontdoorwebapp.azurefd.net",
        "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode":"prevention",
        "details":
            {
            "matches":
                [{
                "matchVariableName":"QueryParamValue:q",
                "matchVariableValue":"' or 1=1"
                }]
            }
     }
}
```

Följande exempel fråga returnerar AccessLogs-poster:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Här är ett exempel på en loggad begäran i Access logg:

``` AccessLogSample
{
"time": "2020-06-09T22:32:17.8383427Z",
"category": "FrontdoorAccessLog",
"operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
 "properties":
    {
    "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
    "httpMethod":"GET",
    "httpVersion":"2.0",
    "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
    "requestBytes":"715",
    "responseBytes":"380",
    "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4157.0 Safari/537.36 Edg/85.0.531.1",
    "clientIp":"xxx.xxx.xxx.xxx",
    "socketIp":"xxx.xxx.xxx.xxx",
    "clientPort":"52097",
    "timeTaken":"0.003",
    "securityProtocol":"TLS 1.2",
    "routingRuleName":"WAFdemoWebAppRouting",
    "rulesEngineMatchNames":[],
    "backendHostname":"wafdemowebappuscentral.azurewebsites.net:443",
    "sentToOriginShield":false,
    "httpStatusCode":"403",
    "httpStatusDetails":"403",
    "pop":"SJC",
    "cacheStatus":"CONFIG_NOCACHE"
    }
}

```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [front dörren](../../frontdoor/front-door-overview.md).
