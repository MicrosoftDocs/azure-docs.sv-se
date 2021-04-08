---
title: 'Självstudie: Lägg till säkerhets rubriker med regel motor – Azure-front dörr'
description: I den här självstudien får du lära dig hur du konfigurerar ett säkerhets huvud via regel motorn på Azures front dörr
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 1727193faa028a1d681f2a74df950afeb9570ec9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91270057"
---
# <a name="tutorial-add-security-headers-with-rules-engine"></a>Självstudie: Lägg till säkerhets rubriker med regel motor

I den här självstudien visas hur du implementerar säkerhets rubriker för att förhindra webbläsarbaserade sårbarheter som HTTP Strict-Transport-Security (HSTS), X-XSS-skydd, Content-Security-policy eller X-Frame-Options. Säkerhetsbaserade attribut kan också definieras med cookies.

I följande exempel visas hur du lägger till ett innehålls-säkerhets-princip-huvud till alla inkommande förfrågningar som matchar den sökväg som definierats i den väg som regel motor konfigurationen är associerad med. Här kan vi bara tillåta att skript från vår betrodda plats **https://apiphany.portal.azure-api.net** körs i vårt program.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Konfigurera en innehålls-säkerhets princip i regel motorn.

## <a name="prerequisites"></a>Förutsättningar

* Innan du kan slutföra stegen i den här kursen behöver du skapa en Front Door. Mer information finns i [Snabbstart: Skapa en Front Door](quickstart-create-front-door.md).
* Om det här är första gången du använder funktionen för regel motor, se så här [konfigurerar du en regel motor](front-door-tutorial-rules-engine.md).

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Lägg till ett Content-Security-Policy-huvud i Azure Portal

1. Klicka på **Lägg till** för att lägga till en ny regel. Ange ett namn för regeln och klicka sedan på **Lägg till ett åtgärds**  >  **svars huvud**.

1. Ange att operatorn ska **läggas till för att** lägga till den här rubriken som ett svar på alla inkommande begär anden till den här vägen.

1. Lägg till rubrik namnet: **Content-Security-Policy** och definiera de värden som rubriken ska acceptera. I det här scenariot väljer vi *"script-src ' Self ' https://apiphany.portal.azure-api.net ."*

1. När du har lagt till alla regler som du vill ha i konfigurationen kan du inte glömma att gå till önskad väg och koppla din regel Motors konfiguration till din väg regel. Det här steget krävs för att aktivera regeln för att fungera. 

![Portal exempel](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> I det här scenariot har vi inte lagt till [matchnings villkor](front-door-rules-engine-match-conditions.md) för regeln. Alla inkommande begär Anden som matchar den sökväg som definierats i flödes regeln har den här regeln tillämpats. Om du vill att det bara ska gälla för en delmängd av dessa begär Anden, måste du lägga till dina egna **matchnings villkor** för den här regeln.

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg konfigurerade du säkerhets rubriker med regel motorn. Om du inte längre vill ha regeln kan du ta bort den genom att klicka på ta bort regel.

:::image type="content" source="./media/front-door-rules-engine/rules-engine-delete-rule.png" alt-text="Ta bort regel":::

## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa självstudie om du vill lära dig hur du konfigurerar en brand vägg för webbaserade program för din front dörr.

> [!div class="nextstepaction"]
> [Brandvägg för webbaserade program och Front Door](front-door-waf.md)
