---
title: Konfigurera bot-skydd för Azure Web Application-brandväggen (WAF)
description: Lär dig hur du konfigurerar bot-skydd för brand vägg för webbaserade program (WAF) på Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "73516869"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Konfigurera skydd för chattrobotar med brandväggen för webbaserade program i Azure Application Gateway (förhandsversion)

Den här artikeln visar hur du konfigurerar en skydds regel för bot i Azure brand vägg för webbaserade program (WAF) för Application Gateway med hjälp av Azure Portal. 

Du kan aktivera en hanterad skyddsregeluppsättning för din chattrobot så att brandväggen för webbaserade program blockerar eller loggar förfrågningar från kända skadliga IP-adresser. IP-adresserna hämtas från Microsoft Threat Intelligence-flödet. Microsoft Threat Intelligence bygger på Intelligent Security Graph och används av flera tjänster som Azure Security Center.

> [!NOTE]
> Regel uppsättningen för bot-skydd är för närvarande en offentlig för hands version och tillhandahålls med ett service nivå avtal för för hands versionen. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Se [kompletterande användnings villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna   av mer information.

## <a name="prerequisites"></a>Förutsättningar

Skapa en grundläggande WAF-princip för Application Gateway genom att följa instruktionerna som beskrivs i [skapa brand Väggs principer för webb program för Application Gateway](create-waf-policy-ag.md).

## <a name="enable-bot-protection-rule-set"></a>Aktivera regel uppsättning för bot-skydd

1. På sidan **grundläggande** princip som du skapade tidigare väljer du **regler** under **Inställningar**.  

2. På sidan information under avsnittet  **Hantera regler**   går du till den nedrullningsbara menyn och markerar kryss rutan för skydds regeln för bot. Välj sedan **Spara**.

> [!div class="mx-imgBorder"]
> ![Robotskydd](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Nästa steg

Mer information om anpassade regler finns i [anpassade regler för Web Application Firewall v2 på Azure Application Gateway](custom-waf-rules-overview.md).