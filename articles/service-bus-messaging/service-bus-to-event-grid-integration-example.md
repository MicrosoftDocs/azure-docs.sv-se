---
title: Hantera Service Bus händelser via Event Grid med Azure Logic Apps
description: Den här artikeln innehåller steg för att hantera Service Bus händelser via Event Grid att använda Azure Logic Apps.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 10/16/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 93375f6047fbe4eda2132e024dab0e067e83ccf1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999067"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-logic-apps"></a>Självstudie: svara på Azure Service Bus händelser som tas emot via Azure Event Grid med Azure Logic Apps
I den här självstudien får du lära dig hur du svarar på Azure Service Bus händelser som tas emot via Azure Event Grid med Azure Logic Apps. 

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="receive-messages-by-using-logic-apps"></a>Ta emot meddelanden med Logic Apps
I det här steget skapar du en Azure Logic-app som tar emot Service Bus händelser via Azure Event Grid. 

1. Skapa en Logic-app i Azure Portal.
    1. Välj **+ skapa en resurs**, Välj **integration** och välj sedan **Logic app**. 
    2. På sidan **Logic app – skapa** anger du ett **namn** för Logic app.
    3. Välj din Azure- **prenumeration**. 
    4. Välj **Använd befintlig** för **resurs gruppen** och välj den resurs grupp som du använde för andra resurser (t. ex. Azure Function, Service Bus namnrymd) som du skapade tidigare. 
    5. Välj **plats** för Logic-appen. 
    6. Välj **Granska + skapa**. 
    1. På sidan **Granska + skapa** väljer du **skapa** för att skapa Logic-appen. 
1. På sidan **Logic Apps designer** väljer du **Tom Logic app** under **mallar**. 
1. I designern utför du följande steg:
    1. Sök efter **Event Grid**. 
    2. Välj **när en resurs händelse inträffar – Azure Event Grid**. 

        ![Logic Apps designer – Välj Event Grid utlösare](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Välj **Logga** in, ange dina autentiseringsuppgifter för Azure och välj **Tillåt åtkomst**. 
5. Utför följande steg på sidan **när en resurs händelse inträffar** :
    1. Välj din Azure-prenumeration. 
    2. För **resurs typ** väljer du **Microsoft. Service Bus. Namespaces**. 
    3. För **resurs namn** väljer du namn området för Service Bus. 
    4. Välj **Lägg till ny parameter** och välj **suffix filter**. 
    5. Vid **filter för suffix** anger du namnet på din andra Service Bus ämnes prenumeration. 
        ![Logic Apps designer – konfigurera händelse](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Välj **+ nytt steg** i designern och utför följande steg:
    1. Sök efter **Service Bus**.
    2. Välj **Service Bus** i listan. 
    3. Välj för **Hämta meddelanden** i listan **åtgärder** . 
    4. Välj **Hämta meddelanden från en ämnes prenumeration (Peek-lock)**. 

        ![Logic Apps designer – åtgärd för att hämta meddelanden](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Ange ett **namn för anslutningen**. Exempel: **Hämta meddelanden från ämnes prenumerationen** och välj Service Bus namn området. 

        ![Logic Apps designer – Välj namn området Service Bus](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Välj **RootManageSharedAccessKey** och välj sedan **skapa**.

        ![Logic Apps designer – Välj den delade åtkomst nyckeln](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. Välj ditt **ämne** och din **prenumeration**. 
    
        ![Skärm bild som visar var du väljer ämnet och din prenumeration.](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Välj **+ nytt steg** och utför följande steg: 
    1. Välj **Service Bus**.
    2. Välj **Slutför meddelandet i en ämnes prenumeration** i listan med åtgärder. 
    3. Välj ditt Service Bus **ämne**.
    4. Välj den andra **prenumerationen** för ämnet.
    5. För **meddelandets lås-token** väljer du **Lås token** från det **dynamiska innehållet**. 

        ![Logic Apps designer – Slutför meddelandet](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Spara Logic-appen genom att välja **Spara** i verktygsfältet på Logic Apps designer. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/save-logic-app.png" alt-text="Spara Logic app":::
1. Om du inte redan har skickat test meddelanden till avsnittet följer du anvisningarna i avsnittet [skicka meddelanden till Service Bus ämne](#send-messages-to-the-service-bus-topic) för att skicka meddelanden till ämnet. 
1. Växla till **översikts** sidan för din Logic app. Du ser att Logic App körs i **körnings historiken** för de meddelanden som skickas. Det kan ta några minuter innan du ser att Logic App körs. Uppdatera sidan genom att välja **Uppdatera** i verktygsfältet. 

    ![Logic Apps designer – Logic App körs](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)
1. Välj en Logic app-körning för att se information. Observera att den bearbetade 5 meddelanden i for-slingan. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/logic-app-run-details.png" alt-text="Körnings information för Logic app":::    

## <a name="troubleshoot"></a>Felsöka
Om du inte ser några anrop efter att ha väntat och uppdaterat för någon gång, följer du dessa steg: 

1. Bekräfta att meddelandena har nått avsnittet Service Bus. Se räknaren **inkommande meddelanden** på sidan **Service Bus ämne** . I det här fallet körde jag **MessageSender** -programmet två gånger, så att jag ser 10 meddelanden (5 meddelanden för varje körning).

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="Sidan Service Bus ämne – inkommande meddelanden":::    
1. Bekräfta att det inte finns **några aktiva meddelanden** på Service Bus prenumerationen. 
    Om du inte ser några händelser på den här sidan kontrollerar du att sidan **Service Bus prenumeration** inte visar några **aktiva meddelande antal**. Om antalet för den här räknaren är större än noll, vidarebefordras inte meddelandena i prenumerationen till hanterings funktionen (händelse prenumerations hanterare) av någon anledning. Kontrol lera att du har konfigurerat händelse prenumerationen på rätt sätt. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="Antal aktiva meddelanden vid Service Bus prenumeration":::    
1. Du ser också **levererade händelser** på sidan **händelser** i namn området Service Bus. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="Sidan händelser-levererade händelser" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. Du kan också se att händelserna levereras på sidan **händelse prenumeration** . Du kan komma åt den här sidan genom att välja händelse prenumerationen på sidan **händelser** . 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="Sida för händelse prenumeration – levererade händelser":::
## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Event Grid](../event-grid/index.yml).
* Läs mer om [Azure Functions](../azure-functions/index.yml).
* Läs mer om [Logic Apps-funktionen i Azure App Service](../logic-apps/index.yml).
* Läs mer om [Azure Service Bus](/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png