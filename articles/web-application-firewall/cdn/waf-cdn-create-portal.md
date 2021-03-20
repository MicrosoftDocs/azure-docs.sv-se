---
title: 'Självstudie: skapa WAF-princip för Azure CDN-Azure Portal'
description: I den här självstudien får du lära dig hur du skapar en WAF-princip (Web Application Firewall) på Azure CDN med hjälp av Azure Portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/16/2020
ms.author: victorh
ms.openlocfilehash: 9579d0da3347bdd4ecc627662cee42f909cbfaf7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92132779"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>Självstudie: skapa en WAF-princip på Azure CDN med hjälp av Azure Portal

I den här självstudien får du lära dig hur du skapar en grundläggande WAF-princip (Azure Web Application Firewall) och tillämpar den på en slut punkt i Azure Content Delivery Network (CDN).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en WAF-princip
> * Koppla den till en CDN-slutpunkt. Du kan bara koppla en WAF-princip till de slut punkter som finns på **Azure CDN Standard från Microsoft** SKU.
> * Konfigurera WAF-regler

## <a name="prerequisites"></a>Förutsättningar

Skapa en Azure CDN profil och slut punkt genom att följa anvisningarna i [snabb start: skapa en Azure CDN profil och slut punkt](../../cdn/cdn-create-new-endpoint.md). 

## <a name="create-a-web-application-firewall-policy"></a>Skapa en brand Väggs princip för webb program

Börja med att skapa en grundläggande WAF-princip med en hanterad standard regel uppsättning (DRS) med hjälp av portalen.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**>söker efter **WAF**>väljer **brand vägg för webbaserade program** > väljer **skapa**.
2. På fliken **grundläggande** på sidan **skapa en WAF-princip** anger eller väljer du följande information, accepterar standardinställningarna för återstående inställningar och väljer sedan **Granska + skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Princip för            |Välj Azure CDN (för hands version).|
    | Prenumeration            |Välj prenumerations namnet på din front dörr.|
    | Resursgrupp          |Välj resurs grupps namnet för front dörren.|
    | Principnamn             |Ange ett unikt namn för din WAF-princip.|

   :::image type="content" source="../media/waf-cdn-create-portal/basic.png" alt-text="Skärm bild av sidan Skapa en o-F-princip med en granska + skapa-knapp och värden som har angetts för olika inställningar." border="false":::

3. På fliken **Association** på sidan **skapa en WAF-princip** väljer du **Lägg till CDN-slutpunkt**, anger följande inställningar och väljer sedan **Lägg till**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | CDN-profil              | Välj ditt CDN-profil namn.|
    | Slutpunkt           | Välj namnet på slut punkten och välj sedan **Lägg till**.|
    
    > [!NOTE]
    > Om slut punkten är associerad med en WAF-princip visas den nedtonad. Du måste först ta bort slut punkten från den associerade principen och sedan associera slut punkten till en ny WAF-princip.
1. Välj **Granska + skapa** och välj sedan **Skapa**.

## <a name="configure-web-application-firewall-policy-optional"></a>Konfigurera brand Väggs princip för webb program (valfritt)

### <a name="change-mode"></a>Ändra läge

Som standard är WAF-principen i *identifierings* läge när du skapar en WAF-princip. I *identifierings* läge blockerar WAF inte några förfrågningar. I stället loggas begär Anden som matchar WAF-reglerna på WAF-loggar.

Om du vill se WAF i praktiken kan du ändra läges inställningarna från *identifiering* till *förebyggande*. I *skydds* läge blockeras och loggas begär Anden som matchar regler som definieras i standard regel UPPSÄTTNINGEN (DRS) och loggas på WAF-loggar.

 :::image type="content" source="../media/waf-cdn-create-portal/policy.png" alt-text="Skärm bild av avsnittet princip inställningar. Växla läge är inställt på förebyggande." border="false":::

### <a name="custom-rules"></a>Anpassade regler

Om du vill skapa en anpassad regel väljer du **Lägg till anpassad regel** under avsnittet **anpassade regler** . Då öppnas sidan anpassad regel konfiguration. Det finns två typer av anpassade regler: **matcha regeln** och **hastighets begränsnings** regeln.

Följande skärm bild visar en anpassad matchnings regel för att blockera en begäran om frågesträngen innehåller värdet **blockme**.

:::image type="content" source="../media/waf-cdn-create-portal/custommatch.png" alt-text="Skärm bild av sidan anpassad regel konfiguration som visar inställningarna för en regel som kontrollerar om variabeln QueryString innehåller värdet blockme." border="false":::

Hastighets begränsnings regler kräver två ytterligare fält: **hastighets begränsningens varaktighet** och **hastighets begränsnings tröskeln (begär Anden)** som visas i följande exempel:

:::image type="content" source="../media/waf-cdn-create-portal/customrate.png" alt-text="Skärm bild av sidan för konfiguration av hastighets begränsnings regler. En List ruta för varaktighets gräns och frekvens gräns (begär Anden) visas." border="false":::

### <a name="default-rule-set-drs"></a>Standard regel uppsättning (DRS)

Standard regel uppsättningen för Azure Managed är aktive rad som standard. Om du vill inaktivera en enskild regel i en regel grupp expanderar du reglerna inom den regel gruppen, markerar kryss rutan framför regel numret och väljer **inaktivera** på fliken ovan. Om du vill ändra åtgärds typer för enskilda regler i regel uppsättningen markerar du kryss rutan framför regel numret och väljer sedan fliken **ändra åtgärd** ovan.

 :::image type="content" source="../media/waf-cdn-create-portal/managed2.png" alt-text="Skärm bild av sidan hanterade regler som visar en regel uppsättning, regel grupper, regler och knapparna Aktivera, inaktivera och ändra åtgärd. En regel har marker ATS." border="false":::

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen och alla relaterade resurser när de inte längre behövs.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure WebApplication-brandvägg](../overview.md)
