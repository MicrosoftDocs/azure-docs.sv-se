---
title: Hantera Service Bus händelser via Event Grid med Azure Functions
description: Den här artikeln innehåller steg för att hantera Service Bus händelser via Event Grid att använda Azure Functions.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: afc0a5bf9b83363d1f4baab955b55148fe3a8498
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95819429"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions"></a>Självstudie: svara på Azure Service Bus händelser som tas emot via Azure Event Grid med Azure Functions
I den här självstudien får du lära dig hur du svarar på Azure Service Bus händelser som tas emot via Azure Event Grid med Azure Functions och Azure Logic Apps. 

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa ett namnområde för Service Bus
> * Förbereda ett exempel program för att skicka meddelanden
> * Skicka meddelanden till Service Bus ämnet
> * Ta emot meddelanden med Logic Apps
> * Konfigurera en test funktion på Azure
> * Anslut funktionen och namnområdet via Event Grid
> * Ta emot meddelanden med Azure Functions

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="additional-prerequisites"></a>Ytterligare krav
Installera [Visual Studio 2019](https://www.visualstudio.com/vs) och ta med arbets belastningen **Azure Development** . Den här arbets belastningen innehåller **Azure Function-verktyg** som du behöver för att skapa, bygga och Distribuera Azure Functions projekt i Visual Studio. 

## <a name="deploy-the-function-app"></a>Distribuera funktionsappen 

>[!NOTE]
> Mer information om hur du skapar och distribuerar en Azure Functions-app finns i [utveckla Azure Functions med Visual Studio](../azure-functions/functions-develop-vs.md)

1. Öppna filen **ReceiveMessagesOnEvent. cs** från **FunctionApp1** -projektet för lösningen **SBEventGridIntegration. SLN** . 
1. Ersätt `<SERCICE BUS NAMESPACE - CONNECTION STRING>` med anslutnings strängen till Service Bus namn området. Den bör vara samma som den som du använde i filen **program. cs** i **MessageSender** -projektet i samma lösning. 
1. Högerklicka på **FunctionApp1** och välj **publicera**. 
1. På sidan **publicera** väljer du **Start**. De här stegen kan skilja sig från vad du ser, men publicerings processen bör vara liknande. 
1. I guiden **publicera** på sidan **mål** väljer du **Azure** for **target**. 
1. På sidan **specifika mål** väljer du **Azure Funktionsapp (Windows)**. 
1. På sidan **Functions instance** väljer du **skapa en ny Azure-funktion**. 
1. Följ de här stegen på sidan **Funktionsapp (Windows)** :
    1. Ange ett **namn** för Function-appen.
    1. Välj en Azure- **prenumeration**.
    1. Välj en befintlig **resurs grupp** eller skapa en ny resurs grupp. I den här självstudien väljer du den resurs grupp som har Service Bus namn rymden. 
    1. Välj en **plan typ** för App Service.
    1. Välj en **plats**. Välj samma plats som Service Bus namn rymden. 
    1. Välj en befintlig **Azure Storage** eller Välj **nytt** för att skapa ett nytt lagrings konto som ska användas av Functions-appen. 
    1. Välj **skapa** för att skapa Functions-appen. 
1. Tillbaka på sidan **funktions instans** i guiden **publicera** väljer du **Slutför**. 
1. På sidan **publicera** i Visual Studio väljer du **publicera** för att publicera appen Functions till Azure. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/publish-function-app.png" alt-text="Publicera Functions-appen":::    
1. I fönstret **utdata** ser du meddelandena från build och publish och bekräftar att de har slutförts. 
1. På sidan **publicera** väljer du sedan **Hantera i Azure Portal**. 
1. I Azure Portal på sidan **Funktionsapp** väljer du **funktioner** på den vänstra menyn och bekräftar att du ser två funktioner: 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/functions.png" alt-text="Två funktioner till hanterare Event Grid utlösare och HTTP-utlösare":::

    > [!NOTE]
    > `EventGridTriggerFunction`Använder en [Event Grid-utlösare](../azure-functions/functions-bindings-event-grid-trigger.md) och `HTTPTriggerFunction` använder en [http-utlösare](../azure-functions/functions-bindings-http-webhook-trigger.md).
1. Välj **EventGridTriggerFunction** i listan. Vi rekommenderar att du använder Event Grid utlösare med Azure Functions eftersom det har några fördelar med att använda HTTP-utlösaren. Mer information finns i [Azure Function som händelse hanterare för Event Grid händelser](../event-grid/handler-functions.md).
1. På **funktions** sidan för **EventGridTriggerFunction** väljer du **övervaka** på den vänstra menyn. 
1. Välj **Konfigurera** för att konfigurera Application Insights att registrera anrops loggen. Du kan använda den här sidan för att övervaka funktions körningar när du tar emot Service Bus händelser från Event Grid. 
1. På sidan **Application Insights** anger du ett namn för resursen, väljer en **plats** för resursen och väljer sedan **OK**. 
1. Välj funktionen **EventGridTriggerFunction** överst (dynamiska menyn) för att gå tillbaka till **funktions** sidan. 
1. Bekräfta att du på sidan **övervaka** . 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/monitor-before.png" alt-text="Övervaknings sidan för funktionen innan funktions anrop":::
    
    Behåll sidan öppen i en flik i webbläsaren. Du kommer att uppdatera den här sidan för att se anrop för den här funktionen senare.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Anslut funktionen och namnområdet via Event Grid
I det här avsnittet binder du samman funktionen och Service Bus namn området med hjälp av Azure Portal. 

Följ dessa steg om du vill skapa en Azure Event Grid-prenumeration:

1. Gå till ditt namn område i Azure Portal och välj sedan **händelser** i det vänstra fönstret. Ditt namnområdesfönster öppnas med två Event Grid-prenumerationer i fönstret till höger. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png" alt-text="Sidan Service Bus-händelser":::
2. Välj **+ händelse prenumeration** i verktygsfältet. 
3. Utför följande steg på sidan **Skapa händelse prenumeration** :
    1. Ange ett **namn** för prenumerationen. 
    2. Ange ett **namn** för **system-ämnet**. System ämnen är avsnitt som skapats för Azure-resurser, till exempel Azure Storage konto och Azure Service Bus. Mer information om system ämnen finns i [Översikt över system avsnitt](../event-grid/system-topics.md).
    2. Välj **Azure Function** för **slut punkts typ** och klicka på **Välj en slut punkt**. 

        ![Service Bus-Event Grid-prenumeration](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. På sidan **Välj Azure-funktion** väljer du prenumerationen, resurs gruppen, funktions programmet, platsen och funktionen och väljer sedan **Bekräfta markering**. 

        ![Funktion-välj slut punkten](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. På sidan **Skapa händelse prenumeration** växlar du till fliken **filter** och utför följande aktiviteter:
        1. Välj **Aktivera ämnes filtrering**
        2. Ange namnet på prenumerationen på Service Bus avsnittet (**S1**) som du skapade tidigare.
        3. Välj knappen **Skapa**. 

            ![Filter för händelse prenumeration](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Växla till fliken **händelse prenumerationer** på sidan **händelser** och bekräfta att du ser händelse prenumerationen i listan.

    ![Händelse prenumeration i listan](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="monitor-the-functions-app"></a>Övervaka Functions-appen
De meddelanden som du skickade till Service Bus avsnittet tidigare vidarebefordras till prenumerationen (S1). Event Grid vidarebefordrar meddelandena i prenumerationen till Azure-funktionen. I det här steget i självstudien bekräftar du att funktionen anropades och visar de loggade informations meddelandena. 

1. På sidan för din Azure Function-app växlar du till fliken **övervakare** om den inte redan är aktiv. Du bör se en post för varje meddelande som publiceras i avsnittet Service Bus. Om du inte ser dem uppdaterar du sidan efter att ha väntat några minuter. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-monitor.png" alt-text="Sidan övervaka för funktionen efter anrop":::
2. Välj anropet i listan för att se information. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/invocation-details.png" alt-text="Information om funktions anrop" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::

    Du kan också använda fliken **loggar** på **Monitor** -sidan för att se loggnings informationen när meddelandena skickas. Det kan ta en stund att se om det finns några minuter att se de loggade meddelandena. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-logs.png" alt-text="Funktionsloggar" lightbox="./media/service-bus-to-event-grid-integration-example/function-logs.png":::

## <a name="troubleshoot"></a>Felsöka
Om du inte ser några funktions anrop efter att ha väntat och uppdaterat en stund följer du dessa steg: 

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