---
title: Skicka korrelerade meddelanden i ordning med en sekventiell konvojmönster
description: Skicka relaterade meddelanden i ordning med hjälp av det sekventiella konvojmönster-mönstret i Azure Logic Apps med Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: apseth, divswa, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 8c00d2e4f622bcfad7b2468013336f0d936e318c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87048664"
---
# <a name="send-related-messages-in-order-by-using-a-sequential-convoy-in-azure-logic-apps-with-azure-service-bus"></a>Skicka relaterade meddelanden i ordning med en sekventiell konvojmönster i Azure Logic Apps med Azure Service Bus

När du behöver skicka korrelerade meddelanden i en viss ordning kan du följa det [ *sekventiella konvojmönster* -mönstret](/azure/architecture/patterns/sequential-convoy) när du använder [Azure Logic Apps](../logic-apps/logic-apps-overview.md) med hjälp av [Azure Service Bus-anslutningen](../connectors/connectors-create-api-servicebus.md). Korrelerade meddelanden har en egenskap som definierar relationen mellan dessa meddelanden, till exempel ID för [sessionen](../service-bus-messaging/message-sessions.md) i Service Bus.

Anta till exempel att du har 10 meddelanden för en session med namnet "session 1" och att du har 5 meddelanden för en session med namnet "session 2" som alla skickas till samma [Service Bus kö](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Du kan skapa en Logic app som bearbetar meddelanden från kön så att alla meddelanden från "session 1" hanteras av en enda Utlös ande körning och att alla meddelanden från "session 2" hanteras av nästa Utlös ande körning.

![Allmänt sekventiellt konvojmönster-mönster](./media/send-related-messages-sequential-convoy/sequential-convoy-pattern-general.png)

Den här artikeln visar hur du skapar en logisk app som implementerar det här mönstret genom att använda den **korrelerade leveransen med hjälp av Service Bus-sessioner** . Den här mallen definierar ett Logic app-arbetsflöde som börjar med Service Bus Connector **när ett meddelande tas emot i en kö (Peek-lock)** -utlösare, som tar emot meddelanden från en [Service Bus kö](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Här följer de övergripande steg som den här Logic-appen utför:

* Initiera en session baserat på ett meddelande om att utlösaren läser från Service Bus kön.

* Läs och bearbeta alla meddelanden från samma session i kön under den aktuella arbets flödes körningen.

Information om hur du granskar mallens JSON-fil finns i [GitHub: service-bus-sessions.jspå](https://github.com/Azure/logicapps/blob/master/templates/service-bus-sessions.json).

Mer information finns i [sekventiella konvojmönster-mönster – design mönster för Azure Architecture-molnet](/azure/architecture/patterns/sequential-convoy).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett Service Bus-namnområde och en [Service Bus kö](../service-bus-messaging/service-bus-queues-topics-subscriptions.md), som är en meddelande enhet som du kommer att använda i din Logic app. Dessa objekt och din Logic app måste använda samma Azure-prenumeration. Se till att du väljer **Aktivera sessioner** när du skapar din kö. Om du inte har dessa objekt kan du läsa om [hur du skapar Service Bus namnrymd och en kö](../service-bus-messaging/service-bus-create-namespace-portal.md).

  [!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

* Grundläggande information om hur du skapar Logic Apps. Om du inte har använt Azure Logic Apps kan du prova snabb starten och [skapa ditt första automatiserade arbets flöde](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-access-to-service-bus-namespace"></a>Kontrol lera åtkomst till Service Bus namnrymd

Om du är osäker på om din Logi Kap par har behörighet att komma åt din Service Bus-namnrymd bekräftar du dessa behörigheter.

1. Logga in på [Azure-portalen](https://portal.azure.com). Sök efter och välj ditt Service Bus *namn område*.

1. Välj **principer för delad åtkomst** på namn områdes menyn under **Inställningar**. Under **anspråk** kontrollerar du att du har behörighet att **Hantera** för det namn området.

   ![Hantera behörigheter för Service Bus namnrymd](./media/send-related-messages-sequential-convoy/check-service-bus-permissions.png)

1. Hämta nu anslutnings strängen för Service Bus namn området. Du kan använda den här strängen senare när du skapar en anslutning till namn området från din Logic app.

   1. I fönstret **principer för delad åtkomst** under **princip** väljer du **RootManageSharedAccessKey**.
   
   1. Välj knappen Kopiera bredvid din primära anslutnings sträng. Spara anslutnings strängen för senare användning.

      ![Kopiera Service Bus namn områdets anslutnings sträng](./media/send-related-messages-sequential-convoy/copy-service-bus-connection-string.png)

   > [!TIP]
   > Om du vill kontrol lera att anslutnings strängen är kopplad till din Service Bus-namnrymd eller en meddelande enhet, till exempel en kö, söker du efter `EntityPath` parameterns anslutnings sträng. Om du hittar den här parametern är anslutnings strängen för en speciell entitet och är inte rätt sträng som ska användas med din Logic app.

## <a name="create-logic-app"></a>Skapa en logikapp

I det här avsnittet skapar du en Logic-app med hjälp av den **korrelerade order leveransen med hjälp av Service Bus-sessioner** , som innehåller utlösare och åtgärder för att implementera det här arbets flödes mönstret. Du kan också skapa en anslutning till Service Bus namn området och ange namnet på den Service Bus kö som du vill använda.

1. Skapa en tom Logic-app i [Azure Portal](https://portal.azure.com). På Azures start sida väljer du **skapa en app för resurs**  >  **integrering**  >  **Logic**.

1. När mallgalleriet visas rullar du förbi avsnitten video och vanliga utlösare. Välj mallen i avsnittet **mallar** och **korrelerad leverans i ordning med Service Bus-sessioner**.

   ![Välj mallen "korrelerad leverans i ordning med Service Bus-sessioner"](./media/send-related-messages-sequential-convoy/select-correlated-in-order-delivery-template.png)

1. När rutan bekräftelse visas väljer du **Använd den här mallen**.

1. I Logic Apps designer väljer du **Fortsätt** i **Service Bus** form och väljer sedan plus tecknet ( **+** ) som visas i figuren.

   ![Välj "Fortsätt" för att ansluta till Azure Service Bus](./media/send-related-messages-sequential-convoy/connect-to-service-bus.png)

1. Skapa nu en Service Bus anslutning genom att välja något av alternativen:

   * Följ dessa steg om du vill använda anslutnings strängen som du kopierade tidigare från Service Bus namn området:

     1. Välj **Ange anslutnings information manuellt**.

     1. Ange ett namn för anslutningen för **anslutnings namn**. För **anslutnings sträng** klistrar du in anslutnings strängen för namn området och väljer **skapa**, till exempel:

        ![Ange anslutnings namn och Service Bus anslutnings sträng](./media/send-related-messages-sequential-convoy/provide-service-bus-connection-string.png)

        > [!TIP]
        > Om du inte har den här anslutnings strängen kan du läsa om hur du [hittar och kopierar den Service Bus namn områdets anslutnings sträng](#permissions-connection-string).

   * Följ dessa steg om du vill välja ett Service Bus namn område från din aktuella Azure-prenumeration:

     1. Ange ett namn för anslutningen för **anslutnings namn**. För **Service Bus namnrymd** väljer du Service Bus namn området, till exempel:

        ![Ange anslutnings namn och välj Service Bus namnrymd](./media/send-related-messages-sequential-convoy/create-service-bus-connection.png)

     1. När nästa ruta visas väljer du din Service Bus princip och väljer **skapa**.

        ![Välj Service Bus princip och sedan "skapa"](./media/send-related-messages-sequential-convoy/create-service-bus-connection-2.png)

1. När du är klar väljer du **Fortsätt**.

   Logic App Designer visar nu den **korrelerade order leveransen med hjälp av Service Bus-sessioner** , som innehåller ett förifyllt arbets flöde med en utlösare och åtgärder, inklusive två omfattningar som implementerar fel hantering som följer `Try-Catch` mönstret.

Nu kan du antingen lära dig mer om utlösare och åtgärder i mallen eller gå vidare och [Ange värden för Logic app-mallen](#complete-template).

<a name="template-summary"></a>

## <a name="template-summary"></a>Sammanfattning av mall

Här är arbets flödet på den översta nivån i den **korrelerade order leveransen med hjälp av Service Bus-sessioner** när informationen är komprimerad:

![Mallens arbets flöde på högsta nivån](./media/send-related-messages-sequential-convoy/template-top-level-flow.png)

| Name | Beskrivning |
|------|-------------|
| **`When a message is received in a queue (peek-lock)`** | Baserat på den angivna upprepningen kontrollerar detta Service Bus utlösaren den angivna Service Bus kön för alla meddelanden. Om det finns ett meddelande i kön utlöses utlösaren som skapar och kör en arbets flödes instans. <p><p>Termen *Peek-lock* innebär att utlösaren skickar en begäran om att hämta ett meddelande från kön. Om ett meddelande finns hämtar och låser utlösaren meddelandet så att ingen annan bearbetning sker i meddelandet förrän låsnings perioden går ut. Om du vill ha mer information [initierar du sessionen](#initialize-session). |
| **`Init isDone`** | Den här [ **initierande variabel** åtgärden](../logic-apps/logic-apps-create-variables-store-values.md#initialize-variable) skapar en boolesk variabel som är inställd på `false` och anger när följande villkor är uppfyllda: <p><p>-Det går inte att läsa fler meddelanden i sessionen. <br>– Låset för låset behöver inte längre förnyas så att den aktuella arbets flödes instansen kan slutföras. <p><p>Mer information finns i [initiera sessionen](#initialize-session). |
| **`Try`** | Den här [ **omfattnings** åtgärden](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) innehåller de åtgärder som körs för att bearbeta ett meddelande. Om ett problem inträffar i `Try` omfånget, hanterar den efterföljande `Catch` **omfattnings** åtgärden det problemet. Mer information finns i ["Try"-omfång](#try-scope). |
| **`Catch`**| Den här [ **omfattnings** åtgärden](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) innehåller de åtgärder som körs om ett problem inträffar i föregående `Try` omfång. Mer information finns i ["catch"-omfattning](#catch-scope). |
|||

<a name="try-scope"></a>

### <a name="try-scope"></a>"Try"-område

Här är flödet på den översta nivån i `Try` [omfattnings åtgärden](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) när informationen är komprimerad:

![Åtgärds arbets flöde för "prova"](./media/send-related-messages-sequential-convoy/try-scope-action.png)

| Name | Beskrivning |
|------|-------------|
| **`Send initial message to topic`** | Du kan ersätta den här åtgärden med vilken åtgärd du vill hantera det första meddelandet från sessionen i kön. Sessions-ID: t anger sessionen. <p><p>För den här mallen skickar en Service Bus-åtgärd det första meddelandet till ett Service Bus ämne. Mer information finns i [hantera det inledande meddelandet](#handle-initial-message). |
| (parallell gren) | Den här [parallella gren åtgärden](../logic-apps/logic-apps-control-flow-branches.md) skapar två sökvägar: <p><p>-Gren #1: Fortsätt att bearbeta meddelandet. Mer information finns i [gren #1: Slutför inledande meddelande i kö](#complete-initial-message). <p><p>-Gren #2: överge meddelandet om något går fel och släpp för hämtning av en annan utlöses körning. Mer information finns i [gren #2: överge inledande meddelande från kö](#abandon-initial-message). <p><p>Båda vägarna går längre till senare i **stängnings sessionen i en kö och åtgärden lyckades** , som beskrivs i nästa rad. |
| **`Close a session in a queue and succeed`** | Den här Service Bus åtgärden kopplar ihop de tidigare beskrivna grenarna och stänger sessionen i kön när någon av följande händelser inträffar: <p><p>-Arbets flödet har slutfört bearbetningen av tillgängliga meddelanden i kön. <br>-Arbets flödet överger det ursprungliga meddelandet eftersom något gick fel. <p><p>Mer information finns i [Stäng en session i en kö och lyckas](#close-session-succeed). |
|||

<a name="complete-initial-message"></a>

#### <a name="branch-1-complete-initial-message-in-queue"></a>Gren #1: Slutför inledande meddelande i kö

| Name | Beskrivning |
|------|-------------|
| `Complete initial message in queue` | Detta Service Bus åtgärd markerar ett meddelande som har hämtats som slutfört och tar bort meddelandet från kön för att förhindra ombearbetning. Mer information finns i [hantera det inledande meddelandet](#handle-initial-message). |
| `While there are more messages for the session in the queue` | Detta [ **tills** loopen](../logic-apps/logic-apps-control-flow-loops.md#until-loop) fortsätter att hämta meddelanden när det finns meddelanden eller tills en timme skickas. Mer information om åtgärderna i den här slingan finns [när det finns fler meddelanden för sessionen i kön](#while-more-messages-for-session). |
| **`Set isDone = true`** | När det inte finns några fler meddelanden anges den här [ **uppsättningen variabel** åtgärd](../logic-apps/logic-apps-create-variables-store-values.md#set-variable) `isDone` `true` . |
| **`Renew session lock until cancelled`** | Detta [ **tills** loopen](../logic-apps/logic-apps-control-flow-loops.md#until-loop) säkerställer att sessions låset hålls av den här Logic-appen när meddelanden finns eller tills en timme passerat. Mer information om åtgärderna i den här slingan finns i [förnya session lock tills avbryts](#renew-session-while-messages-exist). |
|||

<a name="abandon-initial-message"></a>

#### <a name="branch-2-abandon-initial-message-from-the-queue"></a>Gren #2: överge det inledande meddelandet från kön

Om åtgärden som hanterar det första meddelandet Miss lyckas, Service Bus åtgärden **överge inledande meddelande från kön**, släpper meddelandet för en annan arbets flödes instans körs för att hämta och bearbeta. Mer information finns i [hantera det inledande meddelandet](#handle-initial-message).

<a name="catch-scope"></a>

### <a name="catch-scope"></a>"Catch"-omfattning

Om åtgärder i `Try` omfånget inte fungerar, måste Logic app fortfarande stänga sessionen. `Catch` [Omfattnings åtgärden](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) körs när `Try` omfattnings åtgärden resulterar i status, `Failed` , `Skipped` eller `TimedOut` . Omfånget returnerar ett fel meddelande som innehåller det sessions-ID där problemet uppstod och avslutar Logic-appen.

Här är flödet på den översta nivån i `Catch` omfattnings åtgärden när informationen är komprimerad:

![Åtgärds arbets flöde för "catch"-omfattning](./media/send-related-messages-sequential-convoy/catch-scope-action.png)

| Name | Beskrivning |
|------|-------------|
| **`Close a session in a queue and fail`** | Den här Service Bus åtgärden stänger sessionen i kön så att det inte går att öppna det. Mer information finns i [Stäng en session i en kö och sluta fungera](#close-session-fail). |
| **`Find failure msg from 'Try' block`** | Den här [ **filter mat ris** åtgärden](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) skapar en matris från indata och utdata från alla åtgärder i `Try` omfånget baserat på de angivna kriterierna. I det här fallet returnerar den här åtgärden utdata från de åtgärder som resulterade i `Failed` status. Mer information finns i [hitta felmeddelande från try-block](#find-failure-message). |
| **`Select error details`** | Den här [ **Select** -åtgärden](../logic-apps/logic-apps-perform-data-operations.md#select-action) skapar en matris som innehåller JSON-objekt baserat på de angivna kriterierna. Dessa JSON-objekt är skapade från värdena i matrisen som skapats av föregående åtgärd `Find failure msg from 'Try' block` . I det här fallet returnerar den här åtgärden en matris som innehåller ett JSON-objekt som skapats från fel informationen som returnerades från föregående åtgärd. Mer information finns i [Välj fel information](#select-error-details). |
| **`Terminate`** | Den här [ **avbrotts** åtgärden](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) stoppar körningen för arbets flödet, avbryter alla åtgärder som pågår, hoppar över eventuella återstående åtgärder och returnerar angiven status, sessions-ID och fel resultat från `Select error details` åtgärden. Mer information finns i [Avsluta Logic app](#terminate-logic-app). |
|||

<a name="complete-template"></a>

## <a name="complete-the-template"></a>Slutför mallen

Följ dessa steg om du vill ange värden för utlösare och åtgärder i den **korrelerade leveransen med hjälp av Service Bus-sessioner** . Du måste ange alla obligatoriska värden som är markerade med en asterisk ( **\*** ), innan du kan spara din Logic app.

<a name="initialize-session"></a>

### <a name="initialize-the-session"></a>Initiera sessionen

* För **när ett meddelande tas emot i en kö (Peek-lock)** utlösare, ange den här informationen så att mallen kan initiera en session med hjälp av **Session ID-** egenskapen, till exempel:

  ![Service Bus utlösnings information för "när ett meddelande tas emot i en kö (Peek-lock)"](./media/send-related-messages-sequential-convoy/service-bus-check-message-peek-lock-trigger.png)

  > [!NOTE]
  > Inlednings vis är avsöknings intervallet inställt på tre minuter så att Logic app inte körs oftare än förväntat och resulterar i oväntade fakturerings avgifter. Vi rekommenderar att du ställer in intervall och frekvens på 30 sekunder så att Logic app utlöses omedelbart när ett meddelande anländer.

  | Egenskap | Krävs för det här scenariot | Värde | Beskrivning |
  |----------|----------------------------|-------|-------------|
  | **Könamn** | Ja | <*Queue-Name*> | Namnet på din tidigare skapade Service Bus-kö. I det här exemplet används "Fabrikam-Service-Bus-Queue". |
  | **Kötyp** | Ja | **Huvudtillg** | Din primära Service Bus-kö |
  | **Sessions-ID** | Ja | **Nästa tillgängliga** | Med det här alternativet får du en session för varje utlösare som körs baserat på sessions-ID: t från meddelandet i Service Bus kön. Sessionen är också låst så att ingen annan Logic-app eller annan klient kan bearbeta meddelanden som är relaterade till den här sessionen. Arbets flödets efterföljande åtgärder bearbetar alla meddelanden som är associerade med den sessionen, enligt beskrivningen längre fram i den här artikeln. <p><p>Här är mer information om de andra **sessions-ID-** alternativen: <p>- **Ingen**: standard alternativet, vilket leder till att inga sessioner används och inte kan användas för att implementera det sekventiella konvojmönster-mönstret. <p>- **Ange anpassat värde**: Använd det här alternativet när du känner till det sessions-ID som du vill använda och du alltid vill köra utlösaren för sessions-ID: t. <p>**Obs!** Service Bus-anslutningen kan spara ett begränsat antal unika sessioner i taget från Azure Service Bus till kopplingens cacheminne. Om antalet sessioner överskrider den här gränsen tas gamla sessioner bort från cachen. Mer information finns i [Exchange-meddelanden i molnet med Azure Logic Apps och Azure Service Bus](../connectors/connectors-create-api-servicebus.md#connector-reference). |
  | **Intervall** | Ja | <*antal intervall*> | Antalet tidsenheter mellan upprepningar innan ett meddelande kontrol leras. |
  | **Frekvens** | Ja | **Sekund**, **minut**, **timme**, **dag**, **vecka** eller **månad** | Tidsenheten för upprepningen som ska användas vid sökning efter ett meddelande. <p>**Tips**: om du vill lägga **till en tidszon** eller **Start tid** väljer du dessa egenskaper i listan **Lägg till ny parameter** . |
  |||||

  Mer information om utlösare finns i [Service Bus – när ett meddelande tas emot i en kö (Peek-lock)](/connectors/servicebus/#when-a-message-is-received-in-a-queue-(peek-lock)). Utlösaren matar ut en [ServiceBusMessage](/connectors/servicebus/#servicebusmessage).

När sessionen har initierats använder arbets flödet åtgärden **initiera variabel** för att skapa en boolesk variabel som ursprungligen ställs in på `false` och anger när följande villkor är uppfyllda: 

* Det går inte att läsa några fler meddelanden i sessionen.

* Det här låset behöver inte längre förnyas så att den aktuella arbets flödes instansen kan slutföras.

![Åtgärds information om att initiera variabeln för "init isDone"](./media/send-related-messages-sequential-convoy/init-is-done-variable.png)

I **try** -blocket utför arbets flödet sedan åtgärder för det första meddelandet som läses.

<a name="handle-initial-message"></a>

### <a name="handle-the-initial-message"></a>Hantera det första meddelandet

Den första åtgärden är en plats hållare Service Bus åtgärd, **Skicka inledande meddelande till ämne**, som du kan ersätta med andra åtgärder som du vill hantera det första meddelandet från sessionen i kön. Sessions-ID: t anger från vilken session meddelandet kommer.

Åtgärden plats hållare Service Bus skickar det första meddelandet till ett Service Bus-ämne som anges av egenskapen **sessions-ID** . På så sätt går alla meddelanden som är associerade med en speciell session till samma ämne. Alla egenskaper för **sessions-ID** för efterföljande åtgärder i den här mallen använder samma sessions-ID-värde.

![Service Bus åtgärds information för "skicka inledande meddelande till ämne"](./media/send-related-messages-sequential-convoy/send-initial-message-to-topic-action.png)

1. I rutan Service Bus åtgärd fyller du i **inledande meddelande i kö**, anger namnet på Service Bus kön och behåller alla andra standard egenskaps värden i åtgärden.

   ![Service Bus åtgärds information för "Slutför inledande meddelande i kö"](./media/send-related-messages-sequential-convoy/complete-initial-message-queue.png)

1. I den Service Bus åtgärden **överge inledande meddelande från kön**, ange namnet på Service Bus kön och Behåll alla andra standard egenskaps värden i åtgärden.

   ![Service Bus åtgärds information för "överge inledande meddelande från kön"](./media/send-related-messages-sequential-convoy/abandon-initial-message-from-queue.png)

Sedan anger du den information som krävs för de åtgärder som följer det **fullständiga inledande meddelandet i Queue** -åtgärden. Du börjar med åtgärderna i **när det finns fler meddelanden om sessionen i Queue** -loopen.

<a name="while-more-messages-for-session"></a>

### <a name="while-there-are-more-messages-for-the-session-in-the-queue"></a>Det finns fler meddelanden för sessionen i kön

Detta [ **tills** loopen](../logic-apps/logic-apps-control-flow-loops.md#until-loop) kör dessa åtgärder när det finns meddelanden i kön eller tills en timme skickas. Om du vill ändra loopens tids gräns redigerar du loopens **timeout** -värde.

* Hämta ytterligare meddelanden från kön medan meddelanden finns.

* Kontrol lera antalet återstående meddelanden. Fortsätt att bearbeta meddelanden om meddelanden fortfarande finns. Om det inte finns fler meddelanden anger arbets flödet `isDone` variabeln till `true` och avslutar slingan.

![Till loop-bearbeta meddelanden i kö](./media/send-related-messages-sequential-convoy/while-more-messages-for-session-in-queue.png)

1. I åtgärds Service Bus kan du **Hämta ytterligare meddelanden från sessionen**, ange namnet på Service Bus kön. Annars behåller du alla andra standard egenskaps värden i åtgärden.

   > [!NOTE]
   > Som standard är det maximala antalet meddelanden som är inställt på `175` , men den här gränsen påverkas av egenskapen meddelande storlek och maximal meddelande storlek i Service Bus. Mer information finns i [meddelande storleken för en kö](../service-bus-messaging/service-bus-quotas.md).

   ![Service Bus åtgärd – "Hämta ytterligare meddelanden från sessionen"](./media/send-related-messages-sequential-convoy/get-additional-messages-from-session.png)

   Därefter delas arbets flödet i dessa parallella grenar:

   * Om ett fel inträffar eller om ett fel inträffar när du söker efter ytterligare meddelanden ställer du in `isDone` variabeln på `true` .

   * **Process meddelanden om vi fick ett** villkor kontrollerar om antalet återstående meddelanden är noll. Om det finns falskt och fler meddelanden fortsätter bearbetningen. Om värdet är sant och inga fler meddelanden finns, anger arbets flödet `isDone` variabeln till `true` .

   ![Villkor – bearbeta meddelanden om några](./media/send-related-messages-sequential-convoy/process-messages-if-any.png)

   I avsnittet **om falskt** behandlar varje meddelande varje meddelande i första **och den första** (FIFO). I loopens **Inställningar** är inställningen för **samtidighets kontroll** inställd på `1` , så att endast ett enskilt meddelande behandlas i taget.

   !["För varje" loop – bearbeta varje meddelande en i taget](./media/send-related-messages-sequential-convoy/for-each-additional-message.png)

1. För Service Bus åtgärder **Slutför du meddelandet i en kö** och skickar **meddelandet i en kö**, och anger namnet på din Service Bus kö.

   ![Service Bus åtgärder – "Slutför meddelandet i en kö" och "överge meddelandet i en kö"](./media/send-related-messages-sequential-convoy/abandon-or-complete-message-in-queue.png)

   När det finns **fler meddelanden om att sessionen i kön** är avslutad, anger arbets flödet `isDone` variabeln till `true` .

Sedan anger du den information som krävs för åtgärderna i låset för att **låsa sessionen tills den avbrutits** .

<a name="renew-session-while-messages-exist"></a>

### <a name="renew-session-lock-until-cancelled"></a>Förnya session lås tills det avbröts

Detta [ **tills** loopen](../logic-apps/logic-apps-control-flow-loops.md#until-loop) säkerställer att sessions låset hålls av den här Logic-appen när det finns meddelanden i kön eller förrän en timme skickas genom att köra de här åtgärderna. Om du vill ändra loopens tids gräns redigerar du loopens **timeout** -värde.

* Fördröjning i 25 sekunder eller en tids period som är mindre än tids gränsen för låsning av kön som bearbetas. Den minsta lås varaktigheten är 30 sekunder, så standardvärdet är tillräckligt. Du kan dock optimera antalet gånger som loopen körs genom att justera på rätt sätt.

* Kontrol lera om `isDone` variabeln har angetts till `true` .

  * Om `isDone` inte är inställt på `true` , bearbetar arbets flödet fortfarande meddelanden, så arbets flödet förnyar låset på sessionen i kön och kontrollerar loop-villkoret igen.

    Du måste ange namnet på Service Bus kön i Service Bus åtgärd, [**förnya låset på sessionen i en kö**](#renew-lock-on-session).

  * Om `isDone` är inställt på `true` , förnyar arbets flödet inte låset på sessionen i kön och avslutar slingan.

  ![Tills loopen – "förnya session lås tills det har avbrutits"](./media/send-related-messages-sequential-convoy/renew-lock-until-session-cancelled.png)

<a name="renew-lock-on-session"></a>

#### <a name="renew-lock-on-the-session-in-a-queue"></a>Förnya låset för sessionen i en kö

Med den här Service Bus åtgärden förnyas låset på sessionen i kön medan arbets flödet fortfarande bearbetar meddelanden.

* I åtgärden Service Bus **förnyar du låset på sessionen i en kö**, anger namnet på din Service Bus kö.

  ![Service Bus åtgärd – "förnya lås på sessionen i kön"](./media/send-related-messages-sequential-convoy/renew-lock-on-session-in-queue.png)

Sedan anger du den information som krävs för åtgärden Service Bus, **stänger en session i en kö och lyckas**.

<a name="close-session-succeed"></a>

### <a name="close-a-session-in-a-queue-and-succeed"></a>Stäng en session i en kö och lyckas

Med den här Service Bus åtgärden stängs sessionen i kön när arbets flödet har slutfört bearbetningen av alla tillgängliga meddelanden i kön, eller så avbryts det första meddelandet i arbets flödet.

* I åtgärds Service Bus, **Stäng en session i en kö och slutförd**, ange namnet på din Service Bus kö.

  ![Service Bus åtgärd – "Stäng en session i en kö och lyckas"](./media/send-related-messages-sequential-convoy/close-session-in-queue-succeed.png)

I följande avsnitt beskrivs åtgärderna i `Catch` avsnittet som hanterar fel och undantag som inträffar i ditt arbets flöde.

<a name="close-session-fail"></a>

### <a name="close-a-session-in-a-queue-and-fail"></a>Stäng en session i en kö och rapportera inte

Den här Service Bus åtgärden körs alltid som den första åtgärden i `Catch` omfånget och stänger sessionen i kön.

* I åtgärds Service Bus, **Stäng en session i en kö och det går inte** att ange namnet på Service Bus kön.

  ![Service Bus åtgärd – "Stäng en session i en kö och rapportera inte"](./media/send-related-messages-sequential-convoy/close-session-in-queue-fail.png)

Därefter skapar arbets flödet en matris som har indata och utdata från alla åtgärder i `Try` omfånget så att Logic-appen kan komma åt information om felet eller felet som har inträffat.

<a name="find-failure-message"></a>

### <a name="find-failure-msg-from-try-block"></a>Hitta ett meddelande från try-block

Den här [ **filter mat ris** åtgärden](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) skapar en matris som har indata och utdata från alla åtgärder i `Try` omfånget baserat på de angivna villkoren med hjälp av [ `result()` funktionen](../logic-apps/workflow-definition-language-functions-reference.md#result). I det här fallet returnerar den här åtgärden utdata från de åtgärder som har `Failed` status med hjälp av [ `equals()` funktionen](../logic-apps/workflow-definition-language-functions-reference.md#equals) och [ `item()` funktionen](../logic-apps/workflow-definition-language-functions-reference.md#item).

![Filter mat ris åtgärd – "hitta ett meddelande från try-block"](./media/send-related-messages-sequential-convoy/find-failure-message.png)

Här är JSON-definitionen för den här åtgärden:

```json
"Find_failure_msg_from_'Try'_block": {
   "inputs": {
      "from": "@Result('Try')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "Close_the_session_in_the_queue_and_fail": [
         "Succeeded"
      ]
   },
   "type": "Query"
},
```

Därefter skapar arbets flödet en matris med ett JSON-objekt som innehåller fel informationen i matrisen som returnerades från `Find failure msg from 'Try' block` åtgärden.

<a name="select-error-details"></a>

### <a name="select-error-details"></a>Välj fel information

Den här [ **Select** -åtgärden](../logic-apps/logic-apps-perform-data-operations.md#select-action) skapar en matris som innehåller JSON-objekt baserat på den indataport som är utdata från föregående åtgärd `Find failure msg from 'Try' block` . Mer specifikt returnerar den här åtgärden en matris som bara har de angivna egenskaperna för varje objekt i matrisen. I det här fallet innehåller matrisen åtgärds namn och egenskaper för fel resultat.

![Välj åtgärd-"Välj fel information"](./media/send-related-messages-sequential-convoy/select-error-details.png)

Här är JSON-definitionen för den här åtgärden:

```json
"Select_error_details": {
   "inputs": {
      "from": "@body('Find_failure_msg_from_''Try''_block')[0]['outputs']",
      "select": {
         "action": "@item()['name']",
         "errorResult": "@item()"
      }
   },
   "runAfter": {
      "Find_failure_msg_from_'Try'_block": [
         "Succeeded"
      ]
   },
   "type": "Select"
},
```

Sedan stoppar arbets flödet Logic app-körningen och returnerar körnings statusen tillsammans med mer information om felet eller felet som har inträffat.

<a name="terminate-logic-app"></a>

### <a name="terminate-logic-app-run"></a>Avsluta Logic app-körning

Med [ **den här** åtgärden](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) avbryts körningen av Logic app och returneras `Failed` som status för Logic Apps-körningen tillsammans med sessions-ID: t och fel resultatet från `Select error details` åtgärden.

![Avsluta åtgärden för att stoppa körningen av Logic app](./media/send-related-messages-sequential-convoy/terminate-logic-app-run.png)

Här är JSON-definitionen för den här åtgärden:

```json
"Terminate": {
   "description": "This Failure Termination only runs if the Close Session upon Failure action runs - otherwise the LA will be terminated as Success",
   "inputs": {
      "runError": {
         "code": "",
         "message": "There was an error processing messages for Session ID @{triggerBody()?['SessionId']}. The following error(s) occurred: @{body('Select_error_details')['errorResult']}"
         },
         "runStatus": "Failed"
      },
      "runAfter": {
         "Select_error_details": [
            "Succeeded"
         ]
      },
      "type": "Terminate"
   }
},
```

## <a name="save-and-run-logic-app"></a>Spara och kör Logic app

När du har slutfört mallen kan du nu spara din Logic app. I verktygsfältet designer väljer du **Spara**.

Om du vill testa din Logic-App skickar du meddelanden till din Service Bus-kö. 

## <a name="next-steps"></a>Nästa steg

* Läs mer om [utlösare och åtgärder för Service Bus-anslutaren](/connectors/servicebus/)
