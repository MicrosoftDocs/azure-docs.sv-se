---
title: Konfigurera bot-skydd för brand vägg för webbaserade program med Azures front dörr (för hands version)
description: Lär dig hur du konfigurerar skydds regeln för bot i Azure Web Application Firewall (WAF) för front dörren med hjälp av Azure Portal.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 2357c51f47bcb9bd8bbc6c408cb6d8edbab4d10e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91267014"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Konfigurera bot-skydd för brand vägg för webbaserade program (för hands version)
Den här artikeln visar hur du konfigurerar skydds regeln för bot i Azure Web Application Firewall (WAF) för front dörren med hjälp av Azure Portal. Skydds regeln för bot kan också konfigureras med CLI, Azure PowerShell eller Azure Resource Manager mall.

> [!IMPORTANT]
> Regel uppsättningen för bot-skydd är för närvarande en offentlig för hands version och tillhandahålls med ett service nivå avtal för för hands versionen. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet.  Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Skapa en grundläggande WAF-princip för front dörren genom att följa anvisningarna i [skapa en WAF-princip för Azure-frontend med hjälp av Azure Portal](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Aktivera regel uppsättning för bot-skydd

På sidan **hanterade regler** när du skapar en brand Väggs princip för webb program, markerar du kryss rutan **framför regeln** **Microsoft_BotManager_1 DPM\DPM\ProtectionAgents\RA\3.0.** på den nedrullningsbara menyn och väljer sedan **Granska + skapa**.

   ![Skydds regel för bot](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [övervakar WAF](waf-front-door-monitor.md).
