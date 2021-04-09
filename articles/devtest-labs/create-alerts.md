---
title: Skapa aktivitets loggs aviseringar för labb i Azure DevTest Labs
description: Den här artikeln innehåller steg för att skapa aktivitets logg aviseringar för labb i Azure DevTest Labs.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: f774e3291961c58f55a9ed24026535e076235b98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588769"
---
# <a name="create-activity-log-alerts-for-labs-in-azure-devtest-labs"></a>Skapa aktivitets loggs aviseringar för labb i Azure DevTest Labs
Den här artikeln beskriver hur du skapar aktivitets logg aviseringar för labb i Azure DevTest Labs (till exempel: när en virtuell dator skapas eller när en virtuell dator tas bort).

## <a name="create-alerts"></a>Skapa aviseringar
I det här exemplet skapar du en avisering för alla administrativa åtgärder i ett labb med en åtgärd som skickar ett e-postmeddelande till Prenumerationens ägare. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I Sök fältet i Azure Portal skriver du **Monitor** och väljer sedan **övervaka** i resultat listan. 

    :::image type="content" source="./media/activity-logs/search-monitor.png" alt-text="Sök efter övervakare":::        
1. Välj **aviseringar** på den vänstra menyn och välj sedan **ny varnings regel** i verktygsfältet. 

    :::image type="content" source="./media/activity-logs/alerts-page.png" alt-text="Sidan aviseringar":::    
1. Klicka på **Välj resurs** på sidan **skapa aviserings regel** . 

    :::image type="content" source="./media/activity-logs/select-resource-link.png" alt-text="Välj resurs för aviseringen":::        
1. Välj **DevTest Labs** för **Filtrera efter resurs typ**, Välj ditt labb i listan och välj sedan **färdig**.

    :::image type="content" source="./media/activity-logs/select-lab-resource.png" alt-text="Välj labbet som resurs":::
1. Tillbaka på sidan **skapa aviserings regel** klickar du på **Välj villkor**. 

    :::image type="content" source="./media/activity-logs/select-condition-link.png" alt-text="Välj villkors länk":::    
1. På sidan **Konfigurera signal logik** väljer du en signal som stöds av DevTest Labs. 

    :::image type="content" source="./media/activity-logs/select-signal.png" alt-text="Välj signal":::
1. Filtrera efter **händelse nivå** (utförlig, information, varning, fel, kritiskt, alla), **status** (misslyckad, startad, lyckad) och **vem som initierade** händelsen. 
1. Slutför konfigurationen av villkoret genom att välja **klart** . 

    :::image type="content" source="./media/activity-logs/configure-signal-logic-done.png" alt-text="Konfigurera signal logik – färdig":::
1. Du har angett för omfattningen (labb) och villkoret för aviseringen. Nu måste du ange en åtgärds grupp med åtgärder som ska köras när villkoret är uppfyllt. Gå tillbaka till sidan **skapa aviserings regel** och välj **Välj åtgärds grupp**. 

    :::image type="content" source="./media/activity-logs/select-action-group-link.png" alt-text="Välj åtgärds grupp länk":::
1. Välj länken **skapa åtgärds grupp** i verktygsfältet. 

    :::image type="content" source="./media/activity-logs/create-action-group-link.png" alt-text="Skapa åtgärds grupp länk":::
1. Följ dessa steg på sidan **Lägg till åtgärds grupp** :
    1. Ange ett **namn** på åtgärds gruppen.
    1. Ange ett **kort namn** för åtgärds gruppen. 
    1. Välj den **resurs grupp** där du vill att aviseringen ska skapas. 
    1. Ange ett **namn för åtgärden**. 
    1. Välj **Åtgärds typ** (i det här exemplet **e-Azure Resource Manager roll för e-post**). 

        :::image type="content" source="./media/activity-logs/add-action-group.png" alt-text="Sidan Lägg till åtgärds grupp":::
    1. På sidan **e-Azure Resource Manager roll** väljer du rollen. I det här exemplet är det **ägare**. Välj sedan **OK**. 

        :::image type="content" source="./media/activity-logs/select-role.png" alt-text="Välj roll":::            
    1. Välj **OK** på sidan **Lägg till åtgärds grupp** . 
1. På sidan **skapa aviserings regel** anger du ett namn för varnings regeln och väljer sedan **OK**. 

    :::image type="content" source="./media/activity-logs/create-alert-rule-done.png" alt-text="Skapa aviserings regel – färdig":::

## <a name="view-alerts"></a>Visa aviseringar 
1. **Aviseringar visas för alla** administrativa åtgärder (i det här exemplet). Det kan ta en stund innan aviseringarna visas. 

    :::image type="content" source="./media/activity-logs/alerts.png" alt-text="Skärm dum par visar aviseringar på instrument panelen.":::
1. Om du väljer tal i en kolumn (till exempel: **Totalt antal aviseringar**) visas de aviseringar som har Aktiver ATS. 

    :::image type="content" source="./media/activity-logs/all-alerts.png" alt-text="Alla aviseringar":::
1. Om du väljer en avisering visas information om den. 

    :::image type="content" source="./media/activity-logs/alert-details.png" alt-text="Aviserings information":::
1. I det här exemplet får du också ett e-postmeddelande med innehåll som visas i följande exempel: 

    :::image type="content" source="./media/activity-logs/alert-email.png" alt-text="Aviseringsmeddelande":::

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du skapar åtgärds grupper med olika åtgärds typer finns i [skapa och hantera åtgärds grupper i Azure Portal](../azure-monitor/alerts/action-groups.md).
- Mer information om aktivitets loggar finns i  [Azure aktivitets logg](../azure-monitor/essentials/activity-log.md).
- Information om hur du anger aviseringar för aktivitets loggar finns i avsnittet [om aviseringar i aktivitets loggen](../azure-monitor/alerts/activity-log-alerts.md).

