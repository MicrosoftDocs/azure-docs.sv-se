---
title: Konfigurera säkerhets rubriker med regel uppsättningen för för hands versionen av Azure front dörr standard/Premium (för hands version)
description: Den här artikeln innehåller rikt linjer för hur du använder regel uppsättning för att konfigurera säkerhets rubriker.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c08ba57f43969bb2f0ee9c66b6cb4e92879ed258
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100460"
---
# <a name="configure-security-headers-with-azure-front-door-standardpremium-preview-rule-set"></a>Konfigurera säkerhets rubriker med regel uppsättningen för för hands versionen av Azure front dörr standard/Premium (för hands version)

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

Den här artikeln beskriver hur du implementerar säkerhets rubriker för att förhindra webbläsarbaserade sårbarheter som HTTP Strict-Transport-Security (HSTS), X-XSS-skydd, Content-Security-policy eller X-Frame-Options. Säkerhetsbaserade attribut kan också definieras med cookies.

I följande exempel visas hur du lägger till ett innehålls-säkerhets-princip-huvud till alla inkommande begär Anden som matchar sökvägen i vägen. Här kan vi bara tillåta att skript från vår betrodda plats **https://apiphany.portal.azure-api.net** körs i vårt program.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

* Innan du kan konfigurera Konfigurera säkerhets rubriker måste du först skapa en front dörr. Mer information finns i [Snabbstart: Skapa en Front Door](create-front-door-portal.md).
* Se hur du [konfigurerar en regel uppsättning](how-to-configure-rule-set.md) om du inte har använt funktionen för regel uppsättning tidigare.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Lägg till ett Content-Security-Policy-huvud i Azure Portal

1. Gå till Azures standard-/Premium-profil och välj **regel uppsättning** under **Inställningar.**

1. Välj **Lägg** till för att lägga till en ny regel uppsättning. Ange ett **namn** för regeln och ange sedan ett **namn** för regeln. Välj **Lägg till en åtgärd** och välj sedan **svars huvud**.

1. Ange operatorn Lägg **till för att** lägga till den här rubriken som ett svar på alla inkommande begär Anden för den här vägen.

1. Lägg till rubrik namnet: **Content-Security-Policy** och definiera de värden som rubriken ska acceptera. I det här scenariot väljer vi *"script-src" Self https://apiphany.portal.azure-api.net '*.

1. Glöm inte att associera regel uppsättningen med en väg när du har lagt till alla regler som du vill ha i konfigurationen. Det här steget *krävs* för att tillåta regel uppsättningen att vidta åtgärder. 

> [!NOTE]
> I det här scenariot har vi inte lagt till [matchnings villkor](concept-rule-set-match-conditions.md) för regeln. Den här regeln tillämpas på alla inkommande begär Anden som matchar den sökväg som definierats i den associerade vägen. Om du vill att det bara ska gälla för en delmängd av dessa begär Anden, måste du lägga till dina egna **matchnings villkor** för den här regeln.

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="deleting-a-rule"></a>Ta bort en regel

I föregående steg konfigurerade du innehållet – säkerhets princip huvud med regel uppsättning. Om du inte längre vill ha en regel kan du välja regel uppsättningens namn och välja ta bort regel. 

### <a name="deleting-a-rule-set"></a>Ta bort en regel uppsättning

Om du vill ta bort en regel uppsättning ser du till att du avassocierar den från alla vägar innan du tar bort den. Detaljerad vägledning om hur du tar bort en regel uppsättning finns i [Konfigurera regel uppsättningen](how-to-configure-rule-set.md).

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar en brand vägg för webbaserade program för din front dörr finns i [brand vägg för webb program och front dörr](../../web-application-firewall/afds/afds-overview.md).
