---
title: Ange aviseringar för Azure Event Grid mått och aktivitets logg åtgärder
description: Den här artikeln beskriver hur du skapar aviseringar för Azure Event Grid mått och aktivitets logg åtgärder.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 48cb402e31435cb3e9390e8aeb461fcc5f90702f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572013"
---
# <a name="set-alerts-on-azure-event-grid-metrics-and-activity-logs"></a>Ange aviseringar för Azure Event Grid mått och aktivitets loggar
Den här artikeln beskriver hur du skapar aviseringar för Azure Event Grid mått och aktivitets logg åtgärder. Du kan skapa aviseringar för både publicerings-och leverans mått för Azure Event Grid resurser (ämnen och domäner). För system ämnen [skapar du aviseringar med hjälp av sidan **mått**](#create-alerts-using-the-metrics-page).

## <a name="create-alerts-on-dead-lettered-events"></a>Skapa aviseringar för händelser med obeställbara meddelanden
Följande procedur visar hur du skapar en avisering om mått för **obeställbara händelser** för ett anpassat ämne. I det här exemplet skickas ett e-postmeddelande till Azures resurs grupps ägare när antalet förlorade händelser för ett ämne hamnar ovanför 10. 

1. På sidan **Event Grid ämne** för ditt ämne väljer du **aviseringar** på den vänstra menyn och väljer sedan **+ ny varnings regel**. 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="Sidan aviseringar – knappen Ny varnings regel":::
2. På sidan **skapa aviserings regel** kontrollerar du att ditt ämne är markerat för resursen. Klicka sedan på **Välj villkor**. 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="Sidan aviseringar – Välj villkor":::    
3. Följ dessa steg på sidan **Konfigurera signal logik** :
    1. Välj ett mått eller en aktivitets logg post. I det här exemplet är inloggade **brevade händelser** markerade. 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="Välj händelser för obeställbara meddelanden":::        
    2. Välj dimensioner (valfritt). 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="Konfigurera signallogiken":::        

        > [!NOTE]
        > Du kan välja **+** knapp för **EventSubscriptionName** för att ange ett namn på en händelse prenumeration för att filtrera händelser. 
    3. Rulla nedåt. I avsnittet **aviserings logik** väljer du en **operator**, **sammansättnings typ** och anger ett **tröskelvärde** och väljer **klar**. I det här exemplet utlöses en avisering när det totala antalet död antalet förlorade händelser är större än 10. 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="Aviserings logik":::                
4. Gå tillbaka till sidan **skapa aviserings regel** och klicka på **Välj åtgärds grupp**.

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="Knappen Välj åtgärds grupp":::
5. Välj **skapa åtgärds grupp** i verktygsfältet för att skapa en ny åtgärds grupp. Du kan också välja en befintlig åtgärds grupp.        
6. Följ dessa steg på sidan **Lägg till åtgärds grupp** :
    1. Ange ett **namn på åtgärds gruppen**.
    1. Ange ett **kort namn** för åtgärds gruppen.
    1. Välj en **Azure-prenumeration** där du vill skapa åtgärds gruppen.
    1. Välj den **Azure-resurs grupp** där du vill skapa åtgärds gruppen.
    1. Ange ett **namn för åtgärden**. 
    1. Välj **Åtgärds typ**. I det här exemplet är **e-postAzure Resource Manager rollen** markerad, särskilt rollen **ägare** . 
    1. Välj **OK** för att stänga sidan. 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="Sidan Lägg till åtgärds grupp":::                   
7. Gå tillbaka till sidan **skapa aviserings regel** , ange ett namn för aviserings regeln och välj sedan **skapa aviserings regel**.

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="Namn på aviseringsregel":::  
8. På sidan **aviseringar** i avsnittet ser du nu en länk för att hantera aviserings regler om det inte finns några aviseringar än. Om det finns aviseringar väljer du **chefs aviserings regler** i verktygsfältet.  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="Hantera aviseringar":::

## <a name="create-alerts-on-other-metrics-or-activity-log-operations"></a>Skapa aviseringar för andra mått eller aktivitets logg åtgärder
I föregående avsnitt visar vi hur du skapar aviseringar om händelser som har dött. Stegen för att skapa aviseringar om andra mått eller aktivitets logg åtgärder liknar varandra. 

Om du till exempel vill skapa en avisering för en leverans fel händelse väljer du **leverans misslyckade händelser** på sidan **Konfigurera signal logik** . 

:::image type="content" source="./media/set-alerts/delivery-failed-events.png" alt-text="Välj leverans misslyckade händelser":::


## <a name="create-alerts-using-the-metrics-page"></a>Skapa aviseringar med hjälp av sidan mått
Du kan också skapa aviseringar med hjälp av sidan **mått** . Stegen är liknande. I system avsnitt kan du bara använda sidan **mått** för att skapa aviseringar eftersom sidan **aviseringar** inte är tillgänglig. 

:::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="Sidan mått – knappen Skapa avisering":::   
    

> [!NOTE]
> Den här artikeln beskriver inte alla de olika stegen och kombinationerna som du kan använda för att skapa en avisering. En översikt över aviseringar finns i [Översikt över aviseringar](../azure-monitor/alerts/alerts-metric.md).

## <a name="next-steps"></a>Nästa steg

* Om du vill ha information om händelse leverans och försök kan du [Event Grid meddelande leverans och försöka igen](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
