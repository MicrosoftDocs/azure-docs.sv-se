---
title: Anpassa regler med hjälp av Portal – Azure Web Application-brandvägg
description: Den här artikeln innehåller information om hur du anpassar brand Väggs regler för webb program i Application Gateway med Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "74048367"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Anpassa brand Väggs regler för webb program med hjälp av Azure Portal

Brand väggen för webbaserade program (WAF) i Azure Application Gateway tillhandahåller skydd för webb program. Dessa skydd tillhandahålls av OWASP (Open Web Application Security Project) Core regel set (DATORISERAt). Vissa regler kan orsaka falska positiva identifieringar och blockera verklig trafik. Därför ger Application Gateway möjlighet att anpassa regel grupper och regler. Mer information om de specifika regel grupperna och reglerna finns i [lista över regel grupper och regler för webb programs brand vägg](application-gateway-crs-rulegroups-rules.md)

>[!NOTE]
> Om din Application Gateway inte använder WAF-nivån visas alternativet för att uppgradera programgatewayen till WAF-nivån i den högra rutan. 

![Aktivera WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Visa regel grupper och regler

**Visa regel grupper och regler**
1. Bläddra till programgatewayen och välj sedan **brand vägg för webbaserade program**.  
2. Välj din **WAF-princip**.
2. Välj **hanterade regler**.

   I den här vyn visas en tabell på sidan för alla regel grupper som ingår i den valda regel uppsättningen. Alla kryss rutor för regeln är markerade.

## <a name="disable-rule-groups-and-rules"></a>Inaktivera regel grupper och regler

> [!IMPORTANT]
> Var försiktig när du inaktiverar regel grupper eller regler. Detta kan innebära ökad säkerhets risk.

När du inaktiverar regler kan du inaktivera en hel regel grupp eller vissa regler under en eller flera regel grupper. 

**Så här inaktiverar du regel grupper eller vissa regler**

   1. Sök efter de regler eller regel grupper som du vill inaktivera.
   2. Markera kryss rutorna för de regler som du vill inaktivera. 
   3. Välj åtgärden överst på sidan (aktivera/inaktivera) för de valda reglerna.
   2. Välj **Spara**. 

![Spara ändringar][3]

## <a name="mandatory-rules"></a>Obligatoriska regler

Följande lista innehåller villkor som gör att WAF blockerar begäran i förebyggande läge. I identifierings läge är de loggade som undantag.

De kan inte konfigureras eller inaktive ras:

* Det gick inte att parsa begär ande bröd texten i begäran som blockeras, om inte kontroll av brödtext är inaktive rad (XML, JSON, formulär data)
* Begär ande text (utan filer) data längden är större än den konfigurerade gränsen
* Begär ande texten (inklusive filer) är större än gränsen
* Ett internt fel uppstod i WAF-motorn

Datoriserat boknings system 3. x:

* Inkommande avvikelse Poäng överskred tröskelvärdet

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat dina inaktiverade regler kan du lära dig hur du visar dina WAF-loggar. Mer information finns i [Application Gateway Diagnostics](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
