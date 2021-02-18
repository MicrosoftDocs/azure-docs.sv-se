---
title: Övervaka status, Visa historik och konfigurera aviseringar
description: Felsök Logi Kap par genom att kontrol lera körnings status, granska utlösarens historik och aktivera aviseringar i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 3c3d1930234c178a56227830ef0702450ddf4a8c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580662"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Övervaka körningsstatus, granska utlösarhistorik och konfigurera aviseringar för Azure Logic Apps

När du har [skapat och kört en Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md)kan du kontrol lera att appens körnings status, kör [Historik](#review-runs-history), [utlösnings historik](#review-trigger-history)och prestanda. Konfigurera [aviseringar](#add-azure-alerts)om du vill få meddelanden om fel eller andra möjliga problem. Du kan till exempel skapa en avisering som identifierar "när fler än fem körningar går sönder om en timme."

För händelse övervakning i real tid och bättre fel sökning, ställer du in diagnostikloggning för din Logic app genom att använda [Azure Monitor loggar](../azure-monitor/overview.md). Med den här Azure-tjänsten kan du övervaka molnet och lokala miljöer så att du enklare kan underhålla deras tillgänglighet och prestanda. Du kan sedan söka efter och Visa händelser, t. ex. utlösa händelser, köra händelser och åtgärds händelser. Genom att lagra informationen i [Azure Monitor loggar](../azure-monitor/logs/data-platform-logs.md)kan du skapa [logg frågor](../azure-monitor/logs/log-query-overview.md) som hjälper dig att hitta och analysera den här informationen. Du kan också använda dessa diagnostikdata med andra Azure-tjänster, till exempel Azure Storage och Azure Event Hubs. Mer information finns i [övervaka Logic Apps med hjälp av Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md).

> [!NOTE]
> Om dina Logi Kap par körs i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) som har skapats för att använda en [intern åtkomst slut punkt](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access), kan du Visa och komma åt indata och utdata från Logic Apps körnings historik endast inifrån *det virtuella nätverket*. Kontrol lera att du har nätverks anslutning mellan de privata slut punkterna och den dator där du vill komma åt körnings historiken. Klient datorn kan till exempel finnas i ISE: s virtuella nätverk eller i ett virtuellt nätverk som är anslutet till ISE: s virtuella nätverk, till exempel via peering eller ett virtuellt privat nätverk. Mer information finns i [åtkomst till ISE-slutpunkt](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). 

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>Granska körningshistorik

Varje gång utlösaren utlöses för ett objekt eller en händelse skapas och körs en separat arbets flödes instans för varje objekt eller händelse i Logic Appss motorn. Som standard körs varje arbets flödes instans parallellt så att inget arbets flöde måste vänta innan en körning påbörjas. Du kan granska vad som hände under körningen, inklusive status för varje steg i arbets flödet plus indata och utdata för varje steg.

1. I [Azure Portal](https://portal.azure.com)kan du söka efter och öppna din Logic app i Logic App Designer.

   Om du vill hitta din Logic app går du till rutan för Azure Search, anger `logic apps` och väljer **Logic Apps**.

   ![Sök efter och välj tjänsten Logic Apps](./media/monitor-logic-apps/find-your-logic-app.png)

   I Azure Portal visas alla Logic Apps som är associerade med dina Azure-prenumerationer. Du kan filtrera listan baserat på namn, prenumeration, resurs grupp, plats och så vidare.

   ![Visa Logic Apps som är associerade med prenumerationer](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Välj din Logic app och välj sedan **Översikt**.

   I översikts fönstret, under **körnings historik**, alla tidigare, aktuella och eventuella väntande körningar för din Logic-app visas. Om listan visar många körningar och du inte hittar den post som du vill använda kan du prova att filtrera listan.

   > [!TIP]
   > Om körnings statusen inte visas kan du försöka att uppdatera översikts sidan genom att välja **Uppdatera**. Ingen körning sker för en utlösare som hoppas över på grund av ouppfyllda-villkor eller inga data hittas.

   ![Översikt, körnings historik och annan information om Logic Apps](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   Här är möjliga körnings status:

   | Körnings status | Description |
   |------------|-------------|
   | **Avbruten** | Körningen stoppades eller slutfördes inte på grund av externa problem, till exempel ett system avbrott eller en upphörde Azure-prenumeration. |
   | **Avbröts** | Körningen utlöstes och startades men tog emot en begäran om annullering. |
   | **Misslyckades** | Minst en åtgärd i körningen misslyckades. Inga efterföljande åtgärder i arbets flödet har ställts in för att hantera det här problemet. |
   | **Körs** | Körningen utlöstes och pågår, men den här statusen kan också visas för en körning som är begränsad på grund av [Åtgärds gränser](logic-apps-limits-and-config.md) eller den [aktuella pris Planen](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Tips**: om du konfigurerar [diagnostikloggning](monitor-logic-apps-log-analytics.md)kan du få information om eventuella begränsnings händelser som inträffar. |
   | **Brutit** | Körningen lyckades. Om en åtgärd Miss lyckas, hanterar en efterföljande åtgärd i arbets flödet detta fel. |
   | **Tids gränsen uppnåddes** | Tids gränsen för körningen uppnåddes eftersom den aktuella varaktigheten överskred tids gränsen för körning, vilket styrs av inställningen för [ **kvarhållning av körnings historik i dagar**](logic-apps-limits-and-config.md#run-duration-retention-limits). Körningens varaktighet beräknas med hjälp av körningens start tid och tids gräns för körning vid den Start tiden. <p><p>**Obs!** om Körningens varaktighet också överskrider den aktuella *gränsen för körnings historik*, som också styrs av inställningen för [ **kvarhållning av körnings historik i dagar**](logic-apps-limits-and-config.md#run-duration-retention-limits), rensas körningen från körnings historiken med ett dagligt rensnings jobb. Oavsett om tids gränsen för körningen är slut eller slutförd beräknas alltid kvarhållningsperioden med hjälp av start tiden och den *aktuella* kvarhållningsperioden. Så om du minskar tids gränsen för en pågående körnings tid för en flygning. Körningen är dock antingen kvar eller så tas den bort från körnings historiken, baserat på om Körningens varaktighet överskred gränsen för kvarhållning. |
   | **Väntar** | Körningen har inte startat eller pausats, till exempel på grund av en tidigare arbets flödes instans som fortfarande körs. |
   |||

1. Om du vill granska stegen och annan information för en speciell körning väljer du den i kör **Historik**.

   ![Välj en enskild körning som ska granskas](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   **Körnings fönstret för Logic app** visar varje steg i den valda körningen, varje stegs körnings status och den tid det tar för varje steg att köra, till exempel:

   ![Varje åtgärd i den angivna körningen](./media/monitor-logic-apps/logic-app-run-pane.png)

   Om du vill visa den här informationen i list formuläret går du till fliken **Logic app Run** och väljer **Kör information**.

   ![I verktygsfältet väljer du kör information](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   Vyn körnings information visar varje steg, deras status och annan information.

   ![Granska informationen om varje steg i körningen](./media/monitor-logic-apps/review-logic-app-run-details.png)

   Du kan till exempel hämta egenskapen **korrelations-ID** för körning, som du kan behöva när du använder [REST API för Logic Apps](/rest/api/logic).

1. Om du vill ha mer information om ett speciellt steg väljer du något av alternativen:

   * I fönstret **körning av Logic app** väljer du steget så att formen expanderas. Nu kan du Visa information, till exempel indata, utdata och eventuella fel som har inträffat i det steget, till exempel:

     ![I körnings fönstret för Logic app, Visa misslyckades steg](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * Välj det steg som du vill använda i **informations fönstret kör information för Logic app** .

     ![I fönstret kör information, Visa misslyckat steg](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     Nu kan du Visa information om t. ex. indata och utdata för det steget, till exempel:

   > [!NOTE]
   > All körnings information och händelser krypteras i Logic Appss tjänsten. De dekrypteras bara när en användare begär att visa dessa data. Du kan [dölja indata och utdata i körnings historiken](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) eller kontrol lera användarnas åtkomst till den här informationen med hjälp av [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md).

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Granska utlösarhistorik

Varje Logic app-körning börjar med en utlösare. I utlösarens historik visas alla utlösare försök att din Logi Kap par och information om indata och utdata för varje Utlös ande försök.

1. I [Azure Portal](https://portal.azure.com)kan du söka efter och öppna din Logic app i Logic App Designer.

   Om du vill hitta din Logic app går du till rutan för Azure Search, anger `logic apps` och väljer **Logic Apps**.

   ![Sök efter och välj tjänsten Logic Apps](./media/monitor-logic-apps/find-your-logic-app.png)

   I Azure Portal visas alla Logic Apps som är associerade med dina Azure-prenumerationer. Du kan filtrera listan baserat på namn, prenumeration, resurs grupp, plats och så vidare.

   ![Visa Logic Apps som är associerade med prenumerationer](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Välj din Logic app och välj sedan **Översikt**.

1. På din Logic Apps-meny väljer du **Översikt**. I avsnittet **Sammanfattning** under **utvärdering** väljer du **Se utlösarens historik**.

   ![Visa utlösarens historik för din Logic app](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   I fönstret utlösnings historik visas alla utlösare försök som din Logic app har gjort. Varje gång utlösaren utlöses för ett objekt eller en händelse skapar Logic Apps-motorn en separat Logic App-instans som kör arbets flödet. Som standard körs varje instans parallellt så att inget arbets flöde måste vänta innan en körning påbörjas. Så om din Logic app utlöser flera objekt samtidigt visas en utlösnings post med samma datum och tid för varje objekt.

   ![Flera utlösare försöker för olika objekt](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Här följer de möjliga status för utlösarens försök:

   | Utlösarstatus | Description |
   |----------------|-------------|
   | **Misslyckades** | Ett fel inträffade. Om du vill granska eventuella genererade fel meddelanden för en misslyckad utlösare väljer du det Utlös ande försöket och väljer **utdata**. Du kan till exempel hitta indata som inte är giltiga. |
   | **Överhoppad** | Utlösaren kontrollerade slut punkten men hittade inga data som uppfyller de angivna kriterierna. |
   | **Brutit** | Utlösaren kontrollerade slut punkten och hittade tillgängliga data. Normalt visas en **utlöst** status tillsammans med denna status. Om inte, kan utlösarens definition ha ett villkor eller `SplitOn` kommando som inte uppfylldes. <p><p>Den här statusen kan gälla för en manuell utlösare, upprepnings utlösare eller avsöknings utlösare. En utlösare kan köras utan problem, men själva körningen kan fortfarande Miss lyckas när åtgärderna genererar ohanterade fel. |
   |||

   > [!TIP]
   > Du kan kontrol lera utlösaren igen utan att vänta på nästa upprepning. I verktygsfältet översikt väljer du **Kör utlösare** och väljer utlösaren, vilket tvingar en kontroll. Eller Välj **Kör** i Logic Apps designer-verktygsfältet.

1. Om du vill visa information om ett angivet Utlösar-försök väljer du den Utlös ande händelsen i utlösnings fönstret. Om listan visar många Utlös ande försök och du inte hittar den post som du vill använda kan du prova att filtrera listan. Om du inte hittar de data du förväntar dig kan du prova att välja **Uppdatera** i verktygsfältet.

   ![Visa ett enskilt utlösarnamn](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   Nu kan du granska information om den valda utlösnings händelsen, till exempel:

   ![Visa information om en speciell utlösare](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>Konfigurera övervakningsaviseringar

Om du vill få aviseringar baserat på vissa mått eller överskridna tröskelvärden för din Logic app, ställer du in [aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-overview.md). Lär dig mer om [mått i Azure](../azure-monitor/data-platform.md). Följ dessa steg om du vill konfigurera aviseringar utan att använda [Azure Monitor](../azure-monitor/logs/log-query-overview.md).

1. På din Logic app-meny, under **övervakning**, väljer du **aviseringar**  >  **ny aviserings regel**.

   ![Lägg till en avisering för din Logic app](./media/monitor-logic-apps/add-new-alert-rule.png)

1. I fönstret **Skapa regel** under **resurs** väljer du din Logi Kap par, om du inte redan har gjort det. Under **villkor** väljer du **Lägg till** så att du kan definiera det villkor som utlöser aviseringen.

   ![Lägg till ett villkor för regeln](./media/monitor-logic-apps/add-condition-for-rule.png)

1. I fönstret **Konfigurera signal logik** kan du söka efter och välja den signal som du vill få en avisering för. Du kan använda sökrutan eller sortera signalerna alfabetiskt, Markera kolumn rubriken **signal namn** .

   Om du till exempel vill skicka en avisering när en utlösare Miss lyckas, följer du dessa steg:

   1. I kolumnen **signal namn** letar du reda på och väljer signalen **misslyckad utlösare** .

      ![Välj signal för att skapa avisering](./media/monitor-logic-apps/find-and-select-signal.png)

   1. I informations fönstret som öppnas för den valda signalen, under **aviserings logik**, ställer du in ditt villkor, till exempel:

   1. För **operatorn** väljer du **större än eller lika** med.

   1. För **sammansättnings typ** väljer du **Count**.

   1. Ange för **tröskel värde** `1` .

   1. Under **villkors förhands granskning** bekräftar du att villkoret verkar vara korrekt.

   1. Under **utvärdera baserat på** ställer du in intervallet och frekvensen för körning av varnings regeln. För **agg regerings kornig het (period)** väljer du period för gruppering av data. För **utvärderings frekvens** väljer du hur ofta du vill kontrol lera villkoret.

   1. När du är klar väljer du **klar**.

   Här är det färdiga villkoret:

   ![Konfigurera villkor för avisering](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   Sidan **Skapa regel** visar nu det villkor som du har skapat och kostnaden för att köra den aviseringen.

   ![Ny avisering på sidan "Skapa regel"](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Ange ett namn, en valfri beskrivning och allvarlighets grad för aviseringen. Lämna antingen inställningen **Aktivera regel när en skapande** är aktive rad eller inaktivera tills du är redo att aktivera regeln.

1. När du är klar väljer du **skapa aviserings regel**.

> [!TIP]
> Om du vill köra en Logi Kap par från en avisering kan du ta med [utlösaren för begäran](../connectors/connectors-native-reqres.md) i ditt arbets flöde, där du kan utföra uppgifter som exempel:
> 
> * [Publicera till slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Skicka ett sms](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Lägga till ett meddelande i en kö](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>Nästa steg

* [Övervaka Logic Apps med hjälp av Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md)
