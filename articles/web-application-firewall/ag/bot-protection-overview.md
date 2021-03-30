---
title: Översikt över WAF för Azure Application Gateway robot
titleSuffix: Azure Web Application Firewall
description: Den här artikeln innehåller en översikt över brand vägg för webbaserade program (WAF) på Application Gateway bot-skydd
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 05/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e01f9ac8966223e11ad218af7bf6fbb2462f28f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "83714907"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Översikt över Azure Web Application-brandvägg på Azure Application Gateway-skydd

Ungefär 20% av all Internet trafik kommer från felaktiga robotar. De gör sådant som att kassera, skanna och söka efter sårbarheter i ditt webb program. När dessa robotar stoppas i brand väggen för webbaserade program (WAF) kan de inte angripa dig. De kan inte heller använda sig av dina resurser och tjänster, t. ex. dina Server delar och annan underliggande infrastruktur.

Du kan aktivera en hanterad skyddsregeluppsättning för din chattrobot så att brandväggen för webbaserade program blockerar eller loggar förfrågningar från kända skadliga IP-adresser. IP-adresserna hämtas från Microsoft Threat Intelligence-flödet. Microsoft Threat Intelligence bygger på Intelligent Security Graph och används av flera tjänster som Azure Security Center.

> [!IMPORTANT]
> Regel uppsättningen för bot-skydd är för närvarande en offentlig för hands version och tillhandahålls med ett service nivå avtal för för hands versionen. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Se [kompletterande användnings villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna   av mer information.

## <a name="use-with-owasp-rulesets"></a>Använd med OWASP rulesets

Du kan använda skyddsregeluppsättningen tillsammans med valfri OWASP-regeluppsättning (2.2.9, 3.0 och 3.1). Du kan bara använda en OWASP-regeluppsättning åt gången. Skyddsregeluppsättningen innehåller en ytterligare regel som visas i den egna regeluppsättningen. Den heter **Microsoft_BotManagerRuleSet_0** 1 och du kan aktivera eller inaktivera den som de andra OWASP-reglerna.

![Bot ruleset](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Ruleset-uppdatering

Listan med kända, skadliga IP-adresser i skyddsregeluppsättningen uppdateras flera gånger per dag via Microsoft Threat Intelligence-flödet så att chattrobotarna hålls synkroniserade. Dina webbappar skyddas kontinuerligt även när det uppstår nya attackvektorer.

## <a name="log-example"></a>Logg exempel

Här är ett exempel på en loggpost för bot-skydd:

```
{
        "timeStamp": "0000-00-00T00:00:00+00:00",
            "resourceId": "appgw",
            "operationName": "ApplicationGatewayFirewall",
            "category": "ApplicationGatewayFirewallLog",
            "properties": {
            "instanceId": "vm1",
                "clientIp": "1.2.3.4",
                "requestUri": "/hello.php?arg1=aaaaaaabccc",
                "ruleSetType": "MicrosoftBotProtection",
                "message": "IPReputationTriggered",
                "action": "Blocked",
                "hostname": "example.com",
                "transactionId": "abc",
                "policyId": "waf policy 1",
                "policyScope": "Global",
                "policyScopeName": "Default Policy",
                "engine": "Azwaf"
        }
    }
```

## <a name="next-steps"></a>Nästa steg

- [Konfigurera skydd för chattrobotar med brandväggen för webbaserade program i Azure Application Gateway (förhandsversion)](bot-protection.md)
