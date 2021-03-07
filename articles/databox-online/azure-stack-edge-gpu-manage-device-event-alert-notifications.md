---
title: Hantera aviseringar om enhets händelser för dina Azure Stack Edge Pro-resurser | Microsoft Docs
description: Beskriver hur du använder Azure Portal för att hantera aviseringar för enhets händelser på dina Azure Stack Edge Pro-resurser.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 585343137a4a8fd8a1fb591c640e1183d71c0fd3
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443105"
---
# <a name="manage-device-event-alert-notifications-on-azure-stack-edge-pro-resources"></a>Hantera aviseringar om enhets händelser på Azure Stack Edge Pro-resurser

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln beskriver hur du skapar åtgärds regler i Azure Portal för att utlösa eller ignorera aviseringar om enhets händelser som inträffar i en resurs grupp, en Azure-prenumeration eller en enskild Azure Stack Edge-resurs. Den här artikeln gäller för alla modeller av Azure Stack Edge.  

## <a name="about-action-rules"></a>Om åtgärds regler

En åtgärds regel kan utlösa eller ignorera aviserings meddelanden. Åtgärds regeln läggs till i en *Åtgärds grupp* – en uppsättning aviserings inställningar som används för att meddela användare som behöver agera på aviseringar som har utlösts i olika kontexter för en resurs eller uppsättning resurser.

Mer information om åtgärds regler finns i [Konfigurera en åtgärds regel](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule). Mer information om åtgärds grupper finns [i skapa och hantera åtgärds grupper i Azure Portal](../azure-monitor/alerts/action-groups.md).

> [!NOTE]
> Funktionen åtgärds regler är i för hands version. Vissa skärmar och steg kan ändras när processen är raffinerad.


## <a name="create-an-action-rule"></a>Skapa en åtgärds regel

Utför följande steg i Azure Portal för att skapa en åtgärds regel för din Azure Stack Edge-enhet.

> [!NOTE]
> De här stegen skapar en åtgärds regel som skickar meddelanden till en åtgärds grupp. Mer information om hur du skapar en åtgärds regel för att utelämna meddelanden finns i [Konfigurera en åtgärds regel](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule).

1. Gå till Azure Stack Edge-enheten i Azure Portal och gå sedan till **övervakning > aviseringar**. Välj **Hantera åtgärder**.

   ![Övervaknings aviseringar, hantera åtgärder-vy](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-01.png)

2. Välj **Åtgärds regler (förhands granskning)** och välj sedan **+ ny åtgärds regel**.

   ![Hantera åtgärder, alternativet åtgärds regler](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-02.png)

3. På skärmen **skapa åtgärds regel** använder du **omfång** för att välja en Azure-prenumeration, en resurs grupp eller en mål resurs. Åtgärds regeln fungerar på alla aviseringar som genereras inom den omfattningen.

   1. Välj **Välj** efter **omfång**.

      ![Välj ett omfång för en ny åtgärds regel](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-01.png)

   2. Välj **prenumerationen** för åtgärds regeln, filtrera efter en **resurs** typ om du vill. Om du vill filtrera Azure Stack kant resurser väljer du **data Box Edge enheter (dataBoxEdge)**.

      I **resurs** arean visas de tillgängliga resurserna utifrån dina val.
  
      ![Tillgängliga resurser på skärmen Välj omfång](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-02.png)

   3. Markera kryss rutan för varje resurs som du vill tillämpa regeln på. Du kan välja prenumeration, resurs grupper eller enskilda resurser. Välj sedan **Done** (Klar).

      ![Exempel inställningar för en åtgärds regel omfattning](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-03.png)

      Skärmen **skapa åtgärds regel** visar det valda omfånget.

      ![Slutfört omfång för en åtgärds regel för Azure Stack Edge](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-04.png)

4. Använd **filter** alternativ för att begränsa regelns tillämpning till delmängd av aviseringar inom det valda omfånget.

   1. Välj **Lägg till** för att öppna fönstret **Lägg till filter** .

      ![Alternativ för att lägga till filter i en åtgärds regel](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-01.png)

   2. Under **filter** lägger du till de filter som du vill använda. För varje filter väljer du filter typ, **operator** och **värde**.
   
      En lista över filter alternativ finns i [filter villkor](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#filter-criteria).

      Exempel filtren nedan gäller för alla aviseringar på allvarlighets grad 2, 3 och 4 som övervaknings tjänsten genererar för Azure Stack Edge-resurser.

      När du är klar med att lägga till filter väljer du **klar**.
   
      ![Exempel filter för en åtgärds regel](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-02.png)

5. På skärmen **skapa åtgärds regel** väljer du **Åtgärds grupp** för att skapa en regel som skickar meddelanden. Välj sedan **Välj** under **åtgärder**.

   ![Åtgärds grupp alternativ för att skapa en åtgärds regel som skickar meddelanden](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-01.png)

   > [!NOTE]
   > Om du vill skapa en regel som förhindrar meddelanden väljer du under **tryckning**. Mer information finns i [Konfigurera en åtgärds regel](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule).

6. Välj den åtgärds grupp som du vill använda med den här åtgärds regeln. Välj sedan **Välj**. Din nya åtgärds regel kommer att läggas till i aviserings inställningarna för den valda åtgärds gruppen.

   Om du behöver skapa en ny åtgärds grupp väljer du **+ skapa åtgärds grupp** och följer stegen i [skapa en åtgärds grupp med hjälp av Azure Portal](../azure-monitor/alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal).

   ![Välj en åtgärds grupp som ska användas med regeln och välj sedan Välj.](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-02.png)

7. Ge den nya åtgärds regeln ett **namn** och en **Beskrivning** och tilldela regeln till en resurs grupp.

9. Den nya regeln är aktive rad som standard. Om du inte vill börja använda regeln direkt väljer du **Nej** för **att aktivera skapande av regel uppdatering**.

10. När du är klar med inställningarna väljer du **skapa**.

    ![Slutförda inställningar för en åtgärds regel som skickar aviserings meddelanden](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-completed-settings.png)

    Skärmen **Åtgärds regler (förhands granskning)** öppnas, men du kanske inte kan se den nya åtgärds regeln omedelbart. Fokus är **alla** resurs grupper.

11. Välj resurs grupp för regeln om du vill se din nya åtgärds regel.

    ![Åtgärds regler skärmen med den nya regeln som visas](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-displayed.png)


## <a name="view-notifications"></a>Visa aviseringar

Meddelanden skickas när en ny händelse utlöser en avisering för en resurs som ligger inom omfånget för en åtgärds regel.

Åtgärds gruppen för en regel uppsättning som tar emot ett meddelande och typen av meddelande som skickas – e-post, ett meddelande om ett meddelande (SMS) eller både och.

Det kan ta några minuter att ta emot meddelanden när en avisering har utlösts.

E-postaviseringen kommer att se ut ungefär så här.

![Exempel på e-postavisering om en åtgärds regel](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/sample-action-rule-email-notification.png)


## <a name="next-steps"></a>Nästa steg

<!-- - See [Create and manage action groups in the Azure portal](../azure-monitor/alerts/action-groups.md) for guidance on creating a new action group.
- See [Configure an action rule](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule) for more info about creating action rules that send or suppress alert notifications. -2 bullets referenced above. Making room for local tasks in "Next Steps." --> 
- Se [övervaka Azure Stack Edge Pro](azure-stack-edge-monitor.md) för information om hur du kan granska enhets händelser, maskin varu status och mått diagram. 
- Se [använda Azure Monitor](azure-stack-edge-gpu-enable-azure-monitor.md) för information om att optimera Azure Monitor för Azure Stack Edge Pro GPU-enheter.
- Se [skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor länkmål](../azure-monitor/alerts/alerts-metric.md) för information om hur du hanterar enskilda aviseringar.