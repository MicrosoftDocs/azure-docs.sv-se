---
title: Exchange-meddelanden med Azure Service Bus
description: Skapa automatiserade uppgifter och arbets flöden som skickar och tar emot meddelanden med hjälp av Azure Service Bus i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm, azla
ms.topic: conceptual
ms.date: 02/10/2021
tags: connectors
ms.openlocfilehash: 98d2ee8a85d25065c0021841a9b99a6d616a35d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367426"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Exchange-meddelanden i molnet med hjälp av Azure Logic Apps och Azure Service Bus

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) Connector kan du skapa automatiserade uppgifter och arbets flöden som överför data, till exempel försäljnings-och inköps order, journaler och inventerings förflyttningar mellan program för din organisation. Anslutningen övervakar, skickar och hanterar inte bara meddelanden, utan utför även åtgärder med köer, sessioner, ämnen, prenumerationer och så vidare, till exempel:

* Övervaka när meddelanden tas emot (kompletteras automatiskt) eller tas emot (Peek-lock) i köer, ämnen och ämnes prenumerationer.
* Skicka meddelanden.
* Skapa och ta bort ämnes prenumerationer.
* Hantera meddelanden i köer och ämnes prenumerationer, till exempel hämta, Hämta, slutföra, skjuta upp, överge och obeställbara meddelanden.
* Förnya lås på meddelanden och sessioner i köer och ämnes prenumerationer.
* Stäng sessioner i köer och ämnen.

Du kan använda utlösare som får svar från Service Bus och göra utdata tillgängliga för andra åtgärder i dina Logic Apps. Du kan också använda andra åtgärder för att använda utdata från Service Bus åtgärder. Om du är nybörjare på Service Bus och Logic Apps kan du läsa mer i [Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) och [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md)?

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En Service Bus namnrymd och meddelande enhet, till exempel en kö. Dessa objekt och din Logic app måste använda samma Azure-prenumeration. Om du inte har dessa objekt kan du läsa om hur du [skapar Service Bus namnrymd och en kö](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic-app där du använder Service Bus namnrymd och meddelande enhet. Din Logic app och Service Bus måste använda samma Azure-prenumeration. [Skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md)för att starta arbets flödet med en Service Bus utlösare. Om du vill använda en Service Bus-åtgärd i arbets flödet startar du din Logic-app med en annan utlösare, till exempel [utlösaren upprepning](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Kontrol lera behörigheter

Bekräfta att din Logic app har behörighet att komma åt din Service Bus-namnrymd.

1. Logga in med ditt Azure-konto i [Azure Portal](https://portal.azure.com).

1. Gå till din Service Bus *namnrum*. På sidan namn område under **Inställningar** väljer du **principer för delad åtkomst**. Under **anspråk** kontrollerar du att du har behörighet att **Hantera** för det namn området.

   ![Hantera behörigheter för Service Bus namnrymd](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Hämta anslutnings strängen för Service Bus namn området. Du behöver den här strängen när du anger anslutnings informationen i din Logic app.

   1. I fönstret **principer för delad åtkomst** väljer du **RootManageSharedAccessKey**.

   1. Välj knappen Kopiera bredvid din primära anslutnings sträng. Spara anslutnings strängen för senare användning.

      ![Kopiera Service Bus namn områdets anslutnings sträng](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Om du vill kontrol lera att anslutnings strängen är kopplad till din Service Bus-namnrymd eller en meddelande enhet, till exempel en kö, söker du efter `EntityPath` parameterns anslutnings sträng. Om du hittar den här parametern är anslutnings strängen för en speciell entitet och är inte rätt sträng som ska användas med din Logic app.

## <a name="add-service-bus-trigger"></a>Lägg till Service Bus-utlösare

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din tomma Logic-app i Logic App Designer.

1. I rutan Portal sökning anger du `azure service bus` . Välj den utlösare som du vill använda från listan utlösare som visas.

   Om du till exempel vill utlösa din Logi Kap par när ett nytt objekt skickas till en Service Bus-kö, markerar du utlösaren **när ett meddelande tas emot i en kö (automatisk komplettering)** .

   ![Välj Service Bus utlösare](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Här följer några saker att tänka på när du använder en Service Bus-utlösare:

   * Alla Service Bus-utlösare är *tids avsöknings* utlösare. Den här beskrivningen innebär att utlösaren bearbetar alla meddelanden när utlösaren utlöses och väntar sedan 30 sekunder på att fler meddelanden ska visas i kön eller ämnes prenumerationen. Om inga meddelanden visas på 30 sekunder hoppas utlösarens körning över. Annars fortsätter utlösaren att läsa meddelanden tills kön eller ämnes prenumerationen är tom. Nästa Utlös ande avsökning baseras på upprepnings intervallet som anges i utlösarens egenskaper.

   * Vissa utlösare, till exempel **när ett eller flera meddelanden tas emot i en kö (automatisk komplettering)** , kan returnera ett eller flera meddelanden. När utlösarna utlöses, returnerar de mellan ett och antalet meddelanden som anges av utlösarens egenskap för **maximalt antal meddelanden** .

     > [!NOTE]
     > Utlösaren för automatisk komplettering Slutför automatiskt ett meddelande, men slut för ande sker bara vid nästa anrop till Service Bus. Det här beteendet kan påverka din Logic Apps design. Undvik till exempel att ändra samtidigheten i utlösaren för automatisk komplettering eftersom den här ändringen kan resultera i dubbla meddelanden om din Logic-app går in i ett begränsat tillstånd. Om du ändrar samtidighets kontrollen skapas dessa villkor: begränsade utlösare hoppas över med `WorkflowRunInProgress` koden, slut för ande åtgärden inträffar inte och nästa Utlös ande körning sker efter avsöknings intervallet. Du måste ange varaktigheten för Service Bus-låset till ett värde som är längre än avsöknings intervallet. Men trots den här inställningen kanske inte meddelandet kan slutföras om din Logic-app är i ett begränsat tillstånd vid nästa avsöknings intervall.

   * Om du [aktiverar samtidighets inställningen](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) för en Service Bus utlösare är standardvärdet för `maximumWaitingRuns` egenskapen 10. Baserat på den Service Bus enhetens inställning för lås varaktighet och körnings tid för din Logi Kap par instansen kan standardvärdet vara för stort och kan orsaka ett "lock förlorat" undantag. Om du vill hitta det optimala värdet för ditt scenario börjar du testa med ett värde på 1 eller 2 för `maximumWaitingRuns` egenskapen. Om du vill ändra värdet för högsta antal väntande körningar kan du se [gränsen för ändring av väntande körning](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs)

1. Om utlösaren ansluter till Service Bus namn området för första gången följer du de här stegen när du uppmanas att ange anslutnings information i Logic Apps designer.

   1. Ange ett namn för anslutningen och välj Service Bus namn område.

      ![Skärm bild som visar att ange anslutnings namn och välja Service Bus namnrymd](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Om du vill ange anslutnings strängen manuellt i stället väljer du **Ange anslutnings information manuellt**. Om du inte har anslutnings strängen kan du läsa om [hur du hittar anslutnings strängen](#permissions-connection-string).

   1. Välj din Service Bus-princip och välj **skapa**.

      ![Skärm bild som visar hur du väljer Service Bus princip](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Välj den meddelande enhet som du vill använda, till exempel en kö eller ett ämne. I det här exemplet väljer du din Service Bus-kö.
   
      ![Skärm bild som visar val av Service Bus kö](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Ange den information som krävs för den valda utlösaren. Om du vill lägga till andra tillgängliga egenskaper till åtgärden öppnar du listan **Lägg till ny parameter** och väljer de egenskaper som du vill använda.

   I det här exemplets utlösare väljer du avsöknings intervall och frekvens för att kontrol lera kön.

   ![Skärm bild som visar avsöknings intervall i Service Bus utlösaren](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Mer information om tillgängliga utlösare och egenskaper finns på kopplingens [referens sida](/connectors/servicebus/).

1. Fortsätt att skapa din Logic app genom att lägga till de åtgärder som du vill ha.

   Du kan till exempel lägga till en åtgärd som skickar e-post när ett nytt meddelande tas emot. När utlösaren kontrollerar kön och hittar ett nytt meddelande, kör din Logic-app dina valda åtgärder för det hittade meddelandet.

## <a name="add-service-bus-action"></a>Lägg till Service Bus åtgärd

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

1. Under steget där du vill lägga till en åtgärd väljer du **nytt steg**.

   Eller om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj **Lägg till en åtgärd**.

1. Under **Välj en åtgärd** i rutan Sök anger du `azure service bus` . I listan åtgärder som visas väljer du den åtgärd som du vill använda. 

   I det här exemplet väljer du åtgärden **Skicka meddelande** .

   ![Skärm bild som visar hur du väljer åtgärden Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Om åtgärden ansluter till Service Bus namn området för första gången följer du de här stegen när du uppmanas att ange anslutnings information i Logic Apps designer.

   1. Ange ett namn för anslutningen och välj Service Bus namn område.

      ![Skärm bild som visar ett anslutnings namn och väljer ett Service Bus namn område](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Om du vill ange anslutnings strängen manuellt i stället väljer du **Ange anslutnings information manuellt**. Om du inte har anslutnings strängen kan du läsa om [hur du hittar anslutnings strängen](#permissions-connection-string).

   1. Välj din Service Bus-princip och välj **skapa**.

      ![Skärm bild som visar hur du väljer en Service Bus-princip och hur du väljer knappen Skapa](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Välj den meddelande enhet som du vill använda, till exempel en kö eller ett ämne. I det här exemplet väljer du din Service Bus-kö.

      ![Skärm bild som visar val av en Service Bus kö](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Ange nödvändig information för den valda åtgärden. Om du vill lägga till andra tillgängliga egenskaper till åtgärden öppnar du listan **Lägg till ny parameter** och väljer de egenskaper som du vill använda.

   Välj till exempel egenskaperna **innehåll** och **innehålls typ** så att du lägger till dem i åtgärden. Ange sedan innehållet för det meddelande som du vill skicka.

   ![Skärm bild som visar meddelandets innehålls typ och information](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Mer information om tillgängliga åtgärder och deras egenskaper finns på kopplingens [referens sida](/connectors/servicebus/).

1. Fortsätt att skapa din Logic app genom att lägga till andra åtgärder som du vill ha.

   Du kan till exempel lägga till en åtgärd som skickar e-post för att bekräfta att meddelandet har skickats.

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

<a name="sequential-convoy"></a>

## <a name="send-correlated-messages-in-order"></a>Skicka korrelerade meddelanden i ordning

När du behöver skicka relaterade meddelanden i en viss ordning kan du använda det [ *sekventiella konvojmönster* -mönstret](/azure/architecture/patterns/sequential-convoy) med hjälp av [Azure Service Bus Connector](../connectors/connectors-create-api-servicebus.md). Korrelerade meddelanden har en egenskap som definierar relationen mellan dessa meddelanden, till exempel ID för [sessionen](../service-bus-messaging/message-sessions.md) i Service Bus.

När du skapar en Logic app kan du välja den **korrelerade leveransen med hjälp av Service Bus-sessioner** , som implementerar det sekventiella konvojmönster-mönstret. Mer information finns i [skicka relaterade meddelanden i ordning](../logic-apps/send-related-messages-sequential-convoy.md).

## <a name="delays-in-updates-to-your-logic-app-taking-effect"></a>Fördröjningar i uppdateringar av din Logic app börjar fungera

Om avsöknings intervallet för en Service Bus-utlösare är litet, till exempel 10 sekunder, kan det hända att uppdateringar av din Logi Kap par inte börjar gälla i upp till 10 minuter. För att undvika det här problemet kan du inaktivera Logic-appen, göra ändringarna och sedan aktivera den igen.

<a name="connector-reference"></a>

## <a name="connector-reference"></a>Referens för anslutningsapp

Från en Service Bus kan Service Bus Connector Spara upp till 1 500 unika sessioner i taget till anslutnings-cachen, per [Service Bus meddelande enhet, till exempel en prenumeration eller ett ämne](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Om antalet sessioner överskrider den här gränsen tas gamla sessioner bort från cachen. Mer information finns i [Message sessions](../service-bus-messaging/message-sessions.md).

För annan teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens Swagger beskrivning, granskar du [kopplings referens sidan](/connectors/servicebus/). Mer information om Azure Service Bus meddelanden finns i [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)?

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
