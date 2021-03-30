---
title: Anpassa regler med PowerShell
titleSuffix: Azure Web Application Firewall
description: Den här artikeln innehåller information om hur du anpassar brand Väggs regler för webb program i Application Gateway med PowerShell.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 55eea15da8c3a10b0421ff1576082d6b42fc7c56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "74048509"
---
# <a name="customize-web-application-firewall-rules-using-powershell"></a>Anpassa brand Väggs regler för webb program med PowerShell

Brand väggen för webbaserade program (WAF) i Azure Application Gateway tillhandahåller skydd för webb program. Dessa skydd tillhandahålls av OWASP (Open Web Application Security Project) Core regel set (DATORISERAt). Vissa regler kan orsaka falska positiva identifieringar och blockera verklig trafik. Därför ger Application Gateway möjlighet att anpassa regel grupper och regler. Mer information om de specifika regel grupperna och reglerna finns i [lista över regel grupper och regler för webb programs brand vägg](application-gateway-crs-rulegroups-rules.md)

## <a name="view-rule-groups-and-rules"></a>Visa regel grupper och regler

Följande kod exempel visar hur du visar regler och regel grupper som kan konfigureras på en WAF-aktiverad Application Gateway.

### <a name="view-rule-groups"></a>Visa regel grupper

I följande exempel visas hur du visar regel grupper:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

Följande utdata är ett trunkerat svar från föregående exempel:

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>Inaktivera regler

I följande exempel inaktive ras regler `911011` och `911012` en Application Gateway:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Obligatoriska regler

Följande lista innehåller villkor som gör att WAF blockerar begäran i förebyggande läge (i identifierings läge loggas de som undantag). De kan inte konfigureras eller inaktive ras:

* Det gick inte att parsa begär ande bröd texten i begäran som blockeras, om inte kontroll av brödtext är inaktive rad (XML, JSON, formulär data)
* Begär ande text (utan filer) data längden är större än den konfigurerade gränsen
* Begär ande texten (inklusive filer) är större än gränsen
* Ett internt fel uppstod i WAF-motorn

Datoriserat boknings system 3. x:

* Inkommande avvikelse Poäng överskred tröskelvärdet

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat dina inaktiverade regler kan du lära dig hur du visar dina WAF-loggar. Mer information finns i [Application Gateway Diagnostics](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
