---
title: Anpassa regler med hjälp av portalen – Azure Web Application Firewall
description: Den här artikeln innehåller information om hur du anpassar Web Application Firewall i Application Gateway med Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 04/21/2021
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 0ab122d178e5390a53e5a3a39f1b7763b298dc6d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878334"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Anpassa Web Application Firewall med hjälp av Azure Portal

Den Azure Application Gateway Web Application Firewall (WAF) ger skydd för webbprogram. Dessa skydd tillhandahålls av Open Web Application Security Project (OWASP) Core Rule Set (CRS). Vissa regler kan orsaka falska positiva resultat och blockera verklig trafik. Därför ger Application Gateway möjlighet att anpassa regelgrupper och regler. Mer information om specifika regelgrupper och regler finns i Lista över Web Application Firewall [CRS-regelgrupper och -regler.](application-gateway-crs-rulegroups-rules.md)

>[!NOTE]
> Om programgatewayen inte använder WAF-nivån visas alternativet för att uppgradera programgatewayen till WAF-nivån i den högra rutan. 

:::image type="content" source="../media/application-gateway-customize-waf-rules-portal/1.png" alt-text="Aktivera WAF"::: 

## <a name="view-rule-groups-and-rules"></a>Visa regelgrupper och regler

**Visa regelgrupper och regler**
1. Bläddra till programgatewayen och välj sedan **Brandvägg för webbaserade program.**  
2. Välj **WAF-princip.**
2. Välj **Hanterade regler.**

   Den här vyn visar en tabell på sidan för alla regelgrupper som medföljer den valda regeluppsättningen. Alla kryssrutor för regeln är markerade.

## <a name="disable-rule-groups-and-rules"></a>Inaktivera regelgrupper och regler

> [!IMPORTANT]
> Var försiktig när du inaktiverar regelgrupper eller regler. Detta kan medföra ökade säkerhetsrisker.

**Inaktivera regelgrupper eller specifika regler**

   1. Sök efter de regler eller regelgrupper som du vill inaktivera.
   2. Markera kryssrutorna för de regler som du vill inaktivera. 
   3. Välj åtgärden överst på sidan (aktivera/inaktivera) för de valda reglerna.
   2. Välj **Spara**.
    :::image type="content" source="../media/application-gateway-customize-waf-rules-portal/figure3.png" alt-text="Spara inaktiverade regler"::: 

## <a name="mandatory-rules"></a>Obligatoriska regler

Följande lista innehåller villkor som gör att WAF blockerar begäran i skyddsläge. I identifieringsläget loggas de som undantag.

Dessa kan inte konfigureras eller inaktiveras:

* Om begärandetexten inte parsas blockeras begäran, såvida inte brödtextgranskning är inaktiverad (XML, JSON, formulärdata)
* Datalängden för begärandetexten (utan filer) är större än den konfigurerade gränsen
* Begärandetexten (inklusive filer) är större än gränsen
* Ett internt fel inträffade i WAF-motorn

CRS 3.x-specifik:

* Inkommande avvikelsepoäng överskred tröskelvärdet

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat dina inaktiverade regler kan du lära dig hur du visar WAF-loggarna. Mer information finns i [Application Gateway diagnostik](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).