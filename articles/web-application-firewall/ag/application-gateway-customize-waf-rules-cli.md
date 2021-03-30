---
title: Anpassa regler med hjälp av CLI-Azure Web Application-brandvägg
description: Den här artikeln innehåller information om hur du anpassar brand Väggs regler för webb program i Application Gateway med Azure CLI.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 8e8aaa9458619bc937c5bb11c450f3197b92f451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "74048520"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>Anpassa brand Väggs regler för webb program med hjälp av Azure CLI

Brand väggen för webbaserade program (WAF) i Azure Application Gateway tillhandahåller skydd för webb program. Dessa skydd tillhandahålls av OWASP (Open Web Application Security Project) Core regel set (DATORISERAt). Vissa regler kan orsaka falska positiva identifieringar och blockera verklig trafik. Därför ger Application Gateway möjlighet att anpassa regel grupper och regler. Mer information om de specifika regel grupperna och reglerna finns i [lista över regel grupper och regler för webb programs brand vägg](application-gateway-crs-rulegroups-rules.md)

## <a name="view-rule-groups-and-rules"></a>Visa regel grupper och regler

Följande kod exempel visar hur du visar regler och regel grupper som kan konfigureras.

### <a name="view-rule-groups"></a>Visa regel grupper

I följande exempel visas hur du visar regel grupperna:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Följande utdata är ett trunkerat svar från föregående exempel:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Visa regler i en regel grupp

I följande exempel visas hur du visar regler i en angiven regel grupp:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

Följande utdata är ett trunkerat svar från föregående exempel:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Inaktivera regler

I följande exempel inaktive ras regler `910018` och `910017` en Application Gateway:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
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
