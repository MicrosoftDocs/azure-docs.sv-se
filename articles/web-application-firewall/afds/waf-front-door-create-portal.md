---
title: 'Självstudie: skapa WAF-princip för Azures frontend-Azure Portal'
description: I den här självstudien får du lära dig hur du skapar en WAF-princip (Web Application Firewall) med hjälp av Azure Portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: victorh
ms.openlocfilehash: e7b4544530dc9c0c894ae7a0f2b1d2830f895928
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122264"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Självstudie: skapa en brand Väggs princip för webb program på Azure-frontend med hjälp av Azure Portal

I den här självstudien får du lära dig hur du skapar en grundläggande WAF-princip (Azure Web Application Firewall) och använder den på en klient dels värd i Azures front dörr.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en WAF-princip
> * Koppla den till en klient dels värd
> * Konfigurera WAF-regler

## <a name="prerequisites"></a>Förutsättningar

Skapa en [frontend-dörr](../../frontdoor/quickstart-create-front-door.md) eller en [Standard-/Premium-profil för front dörren](../../frontdoor/standard-premium/create-front-door-portal.md) . 

## <a name="create-a-web-application-firewall-policy"></a>Skapa en brand Väggs princip för webb program

Börja med att skapa en grundläggande WAF-princip med hanterad standard regel uppsättning (DRS) med hjälp av portalen. 

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs** > söker efter **WAF** > Välj **brand vägg för webbaserade program (WAF)** > Välj **skapa**.

1. På fliken **grundläggande** på sidan **skapa en WAF-princip** anger eller väljer du följande information, accepterar standardinställningarna för återstående inställningar och väljer sedan **Granska + skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Princip för              | Välj **Global WAF (Front dörr)**. |
    | SKU för front dörren          | Välj mellan SKU: n Basic, standard och Premium. |
    | Prenumeration            | Välj prenumerations namnet på din front dörr.|
    | Resursgrupp          | Välj resurs grupps namnet för front dörren.|
    | Principnamn             | Ange ett unikt namn för din WAF-princip.|
    | Princip tillstånd            | Ange som **aktive rad**. | 

   :::image type="content" source="../media/waf-front-door-create-portal/basic.png" alt-text="Skärm bild av sidan Skapa en o-F-princip med en granska + skapa-knapp och list rutor för prenumerationen, resurs gruppen och princip namnet.":::

1. På fliken **Association** på sidan **skapa en WAF-princip** väljer du **+ associera en profil för front dörr**, anger följande inställningar och väljer sedan **Lägg till**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Profil för front dörr              | Välj profil namnet för din klient del. |
    | Domains          | Välj de domäner som du vill associera WAF-principen till och välj sedan **Lägg till**. |

    :::image type="content" source="../media/waf-front-door-create-portal/associate-profile.png" alt-text="Skärm bild av sidan koppla en profil för en front dörr.":::
    
    > [!NOTE]
    > Om domänen är associerad med en WAF-princip visas den som nedtonad. Du måste först ta bort domänen från den associerade principen och sedan associera domänen till en ny WAF-princip.

1. Välj **Granska + skapa** och välj sedan **Skapa**.

## <a name="configure-web-application-firewall-rules-optional"></a>Konfigurera brand Väggs regler för webb program (valfritt)

### <a name="change-mode"></a>Ändra läge

När du skapar en WAF-princip är standard principen för WAF i **identifierings** läge. I **identifierings** läge blockerar WAF inte några begär Anden, i stället loggas begär Anden som matchar WAF-reglerna på WAF-loggar.
Om du vill se WAF i praktiken kan du ändra läges inställningarna från **identifiering** till **förebyggande**. I **skydds** läge blockeras och loggas begär Anden som matchar regler som definieras i standard regel UPPSÄTTNINGEN (DRS) och loggas på WAF-loggar.

 :::image type="content" source="../media/waf-front-door-create-portal/policy.png" alt-text="Skärm bild av avsnittet princip inställningar. Växla läge är inställt på förebyggande.":::

### <a name="custom-rules"></a>Anpassade regler

Du kan skapa en anpassad regel genom att välja **Lägg till anpassad regel** under avsnittet **anpassade regler** . Då startas sidan anpassad regel konfiguration. 

:::image type="content" source="../media/waf-front-door-create-portal/custom-rules.png" alt-text="Skärm bild av sidan anpassade regler.":::

Nedan visas ett exempel på hur du konfigurerar en anpassad regel för att blockera en begäran om frågesträngen innehåller **blockme**.

:::image type="content" source="../media/waf-front-door-create-portal/customquerystring2.png" alt-text="Skärm bild av sidan anpassad regel konfiguration som visar inställningarna för en regel som kontrollerar om variabeln QueryString innehåller värdet blockme.":::

### <a name="default-rule-set-drs"></a>Standard regel uppsättning (DRS)

Azure-hanterad standard regel uppsättning är aktiverat som standard. Den aktuella standard versionen är DefaultRuleSet_1.0. Från WAF **Managed Rules**, **Assign**, nyligen tillgängliga ruleset Microsoft_DefaultRuleSet_1 1 finns i list rutan.

Om du vill inaktivera en enskild regel markerar du **kryss rutan** framför regel numret och väljer **inaktivera** överst på sidan. Om du vill ändra åtgärds typer för enskilda regler i regel uppsättningen markerar du kryss rutan framför regel numret och väljer sedan **åtgärden ändra** överst på sidan.

:::image type="content" source="../media/waf-front-door-create-portal/managed-rules.png" alt-text="Skärm bild av sidan hanterade regler som visar en regel uppsättning, regel grupper, regler och knapparna Aktivera, inaktivera och ändra åtgärd." lightbox="../media/waf-front-door-create-portal/managed-rules-expanded.png":::

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen och alla relaterade resurser när de inte längre behövs.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azures front dörr](../../frontdoor/front-door-overview.md) 
>  [Läs mer om Azures främre dörr standard/Premium](../../frontdoor/standard-premium/overview.md)
