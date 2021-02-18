---
title: Automatisera Azure Application insikter-processer med Logic Apps
description: Lär dig hur du snabbt kan automatisera upprepade processer genom att lägga till Application Insights Connector i din Logic app.
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: d7ff75be3cb847235405a740df4a20803cdc87b3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589918"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatisera Application Insights processer med Logic Apps

Hittar du dig själv upprepade gånger med samma frågor på dina telemetridata för att kontrol lera om tjänsten fungerar korrekt? Vill du automatisera de här frågorna för att hitta trender och avvikelser och sedan bygga egna arbets flöden runt dem? Azure Application Insights-anslutaren för Logic Apps är rätt verktyg för det här ändamålet.

> [!NOTE]
> Azure Application Insights-anslutningen har ersatts med [Azure Monitor-anslutningen](../logs/logicapp-flow-connector.md) som är integrerad med Azure Active Directory i stället för att kräva en API-nyckel och du kan även hämta data från en Log Analytics arbets yta.

Med den här integreringen kan du automatisera flera processer utan att behöva skriva en enda rad kod. Du kan skapa en Logic-app med Application Insights-anslutningen för att snabbt automatisera eventuella Application Insights processer. 

Du kan också lägga till ytterligare åtgärder. Logic Apps funktionen i Azure App Service gör hundratals åtgärder tillgängliga. Genom att använda en Logic app kan du till exempel automatiskt skicka ett e-postmeddelande eller skapa en bugg i Azure-DevOps. Du kan också använda en av de många tillgängliga [mallarna](../../logic-apps/logic-apps-create-logic-apps-from-templates.md) för att påskynda processen med att skapa din Logic app. 

## <a name="create-a-logic-app-for-application-insights"></a>Skapa en Logic app för Application Insights

I den här självstudien får du lära dig hur du skapar en Logic-app som använder algoritmen för analys av utdata för att gruppera attribut i data för ett webb program. Flödet skickar automatiskt resultaten per e-post, bara ett exempel på hur du kan använda Application Insights Analytics och Logic Apps tillsammans. 

### <a name="step-1-create-a-logic-app"></a>Steg 1: skapa en Logic app
1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **skapa en resurs**, Välj **webb och mobilt** och välj sedan **Logic app**.

    ![Nytt Logic app-fönster](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Steg 2: skapa en utlösare för din Logic app
1. I fönstret **Logic App Designer** , under **börja med en gemensam utlösare**, väljer du **upprepning**.

    ![Fönstret Logic App Designer](./media/automate-with-logic-apps/2logicappdesigner.png)

1. I rutan  **intervall** skriver du **1** och sedan,**frekvens** Box, väljer du **dag**.

    ![Fönstret Logic App Designer "upprepning"](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>Steg 3: Lägg till en Application Insights-åtgärd
1. Klicka på **nytt steg**.

1. I sökrutan **Välj en åtgärd** skriver du **Azure Application Insights**.

1. Under **åtgärder** klickar du på **Azure Application Insights-visualisera analys fråga**.

    ![Logic App Designer "Välj en åtgärd"-fönstret](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Steg 4: ansluta till en Application Insights-resurs

För att slutföra det här steget måste du ha ett program-ID och en API-nyckel för din resurs. Du kan hämta dem från Azure Portal, som du ser i följande diagram:

![Skärm bild som visar sidan för API-åtkomst i Azure Portal med knappen Skapa API-nyckel vald.](./media/automate-with-logic-apps/5apiaccess.png)

![Program-ID i Azure Portal](./media/automate-with-logic-apps/6apikey.png)

Ange ett namn för anslutningen, program-ID och API-nyckeln.

![Flödes anslutnings fönster för Logic App Designer](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Steg 5: Ange analys frågan och diagram typen
I följande exempel väljer frågan de misslyckade förfrågningarna under den senaste dagen och korrelerar dem med undantag som inträffat som en del av åtgärden. Analytics korrelerar de misslyckade förfrågningarna baserat på operation_Id identifieraren. Frågan delar sedan resultatet med hjälp av algoritmen för autokluster. 

När du skapar egna frågor måste du kontrol lera att de fungerar korrekt i Analytics innan du lägger till dem i ditt flöde.

1. I rutan **fråga** lägger du till följande analys fråga:

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```

1. I rutan **diagram typ** väljer du **HTML-tabell**.

    ![Konfigurations fönster för Analytics-fråga](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Steg 6: Konfigurera Logic-appen för att skicka e-post

1. Klicka på **nytt steg**.

1. Skriv **Office 365 Outlook** i rutan Sök.

1. Klicka på **Office 365 Outlook – skicka ett e-postmeddelande**.

    ![Office 365 Outlook-val](./media/automate-with-logic-apps/9sendemail.png)

1. I fönstret **Skicka ett e-postmeddelande** gör du följande:

   a. Ange mottagarens e-postadress.

   b. Ange ett ämne för e-postmeddelandet.

   c. Klicka någonstans i **text** rutan och välj sedan **brödtext** på menyn för dynamiskt innehåll som öppnas till höger.
    
   d. Klicka på list rutan **Lägg till ny parameter** och välj bilagor och är HTML.

      ![Skärm bild som visar fönstret skicka ett e-postmeddelande med bröd text rutan och menyn med dynamiskt innehåll markerat med bröd texten på höger sida.](./media/automate-with-logic-apps/10emailbody.png)

      ![Office 365 Outlook-konfiguration](./media/automate-with-logic-apps/11emailparameter.png)

1. Gör följande på menyn för dynamiskt innehåll:

    a. Välj **namn på bifogad fil**.

    b. Välj **innehåll för bifogad fil**.
    
    c. I rutan **är HTML** väljer du **Ja**.

      ![Skärm bild för Office 365-e-postkonfiguration](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Steg 7: Spara och testa din Logic app
* Klicka på **Spara** för att spara dina ändringar.

Du kan vänta på att utlösaren ska köra Logic-appen, eller så kan du köra Logic-appen direkt genom att välja **Kör**.

![Skärmen för att skapa logiska appar](./media/automate-with-logic-apps/13save.png)

När din Logic App körs får mottagarna du angav i e-postlistan ett e-postmeddelande som ser ut ungefär så här:

![E-postmeddelande för Logic app](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att skapa [analys frågor](../logs/get-started-queries.md).
- Läs mer om [Logic Apps](../../logic-apps/logic-apps-overview.md).



<!--Link references-->

