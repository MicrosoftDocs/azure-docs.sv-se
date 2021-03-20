---
title: Övervaka ändringar av virtuella datorer – Azure Event Grid & Logic Apps
description: Sök efter ändringar på virtuella datorer med hjälp av Azure Event Grid och Logic Apps
services: logic-apps, event-grid
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: estfan, LADocs
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 1a5d8c36382433024efd1f1cc6ba9fd878d28ddc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92329533"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Självstudier: Övervaka ändringar av virtuella maskiner med Azure Event Grid och Logic Apps

Om du vill övervaka och svara på vissa händelser som inträffar i Azure-resurser eller resurser från tredje part, kan du automatisera och köra uppgifter som ett arbets flöde genom att skapa en [Logic-app](../logic-apps/logic-apps-overview.md) som använder minimal kod. Dessa resurser kan publicera händelser till ett [Azure Event-rutnät](../event-grid/overview.md). Händelserutnätet skickar i sin tur händelserna vidare till prenumeranter som har köer, webhooks eller [händelsehubbar](../event-hubs/event-hubs-about.md) som slutpunkter. Som prenumerant kan din Logi Kap par vänta på dessa händelser från händelse rutnätet innan automatiserade arbets flöden körs för att utföra åtgärder.

Här är exempel på händelser som utgivare kan skicka till prenumeranter via tjänsten Azure Event Grid:

* Händelser som skapar, läser, uppdaterar eller tar bort en resurs. Du kan till exempel övervaka ändringar som kan medföra avgifter i din Azure-prenumeration och som påverkar din faktura.

* Händelser som lägger till eller tar bort en person i en Azure-prenumeration.

* Händelser som är kopplade till en viss åtgärd som utförs av din app.

* Händelser som visar ett nytt meddelande i en kö.

Den här självstudien skapar en Logic-app som övervakar ändringar på en virtuell dator och skickar e-postmeddelanden om dessa ändringar. När du skapar en logikapp med en händelseprenumeration för en Azure-resurs flödar händelser från den resursen via ett rutnät till logikappen. Självstudiekursen beskriver steg för steg hur du skapar den här logikappen:

![Skärm bild av Logic Apps designer, som visar arbets flöde för att övervaka virtuell dator med Event Grid.](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en logikapp som övervakar händelser från ett händelserutnät.
> * Lägga till ett villkor som specifikt söker efter ändringar på en virtuell dator.
> * Skicka e-post när den virtuella datorn ändras.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett e-postkonto från en e-postleverantör som stöds av Logic Apps för att skicka meddelanden, till exempel Office 365 Outlook, Outlook.com eller Gmail. För andra providrar [läser du listan med anslutningsappar här](/connectors/).

  I den här självstudien används ett Office 365 Outlook-konto. Om du använder ett annat e-postkonto är stegen desamma, men användargränssnittet kan vara lite annorlunda.

  > [!IMPORTANT]
  > Om du vill använda Gmail Connector kan endast företags konton i G-Suite använda den här anslutningen utan begränsning i Logic Apps. Om du har ett Gmail-konto kan du använda den här anslutningen med endast vissa Google-godkända tjänster, eller så kan du [skapa en Google-klient som används för autentisering med din Gmail-anslutning](/connectors/gmail/#authentication-and-bring-your-own-application). Mer information finns i [principer för data säkerhet och sekretess för Google Connectors i Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* En [virtuell dator](https://azure.microsoft.com/services/virtual-machines) som är fristående i sin egen Azure-resurs grupp. Om du inte redan har gjort det skapar du en virtuell dator via [själv studie kursen skapa en virtuell dator](../virtual-machines/windows/quick-create-portal.md). [Du behöver inte göra något mer](../event-grid/overview.md) för att den virtuella datorn ska publicera händelser.

## <a name="create-blank-logic-app"></a>Skapa en tom logikapp

1. Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto.

1. Från huvud menyn i Azure väljer du **skapa en app för resurs**  >  **integrerings**  >  **logik**.

   ![Skärm bild av Azure Portal som visar knappen för att skapa en Logic app-resurs.](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. Ange information om din Logic app-resurs under **Logic app**. När du är färdig väljer du **Skapa**.

   ![Skärm bild av menyn för att skapa Logic Apps, som visar information som namn, prenumeration, resurs grupp och plats.](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Namn** | Ja | <*Logic – App-Name*> | Ange ett unikt namn för din Logic app. |
   | **Prenumeration** | Ja | <*Azure-prenumeration-namn*> | Välj samma Azure-prenumeration för alla tjänster i den här självstudien. |
   | **Resursgrupp** | Ja | <*Azure-resurs-grupp*> | Namnet på Azure-resurs gruppen för din Logic app, som du kan välja för alla tjänster i den här självstudien. |
   | **Plats** | Ja | <*Azure-region*> | Välj samma region för alla tjänster i den här självstudiekursen. |
   |||

1. När Azure har distribuerat din Logic app visas en sida med en introduktions video och ofta använda utlösare i Logic Apps designer. Rulla förbi videon och utlösarna.

1. Under **Mallar** väljer du **Tom logikapp**.

   ![Skärm bild av Logic Apps mallar, som visar markering för att skapa en tom Logic-app.](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Logic Apps Designer visar nu de [*utlösare*](../logic-apps/logic-apps-overview.md#logic-app-concepts) som du kan använda för att starta din Logic app. Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när ett särskilt villkor uppfylls. Varje gång utlösaren utlöses skapar Azure Logic Apps en arbets flödes instans som kör din Logic app.

## <a name="add-an-event-grid-trigger"></a>Lägg till en Event Grid-utlösare

Lägg nu till den Event Grid utlösare som du använder för att övervaka resurs gruppen för den virtuella datorn.

1. Ange som filter i rutan Sök i designern `event grid` . I listan utlösare väljer du **när en resurs händelse inträffar** som utlösare.

   ![Skärm bild av Logic Apps designer, som visar val av Event Grid utlösare för en resurs händelse.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. När du uppmanas till det loggar du in på Azure Event Grid med dina autentiseringsuppgifter för Azure-kontot. I listan **klient organisation** , som visar Azure Active Directory klient som är associerad med din Azure-prenumeration, kontrollerar du att rätt klient visas, till exempel:

   ![Skärm bild av Logic Apps designer, som visar Azures inloggnings tolk för att ansluta till Event Grid.](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Om du är inloggad med ett personligt Microsoft-konto som @outlook.com eller @hotmail.com kanske Event Grid-utlösaren inte visas korrekt. Som en lösning väljer du [Anslut med tjänstens huvud namn](../active-directory/develop/howto-create-service-principal-portal.md)eller autentisera som medlem i den Azure Active Directory som är associerad med din Azure-prenumeration, till exempel *användar namn* @emailoutlook.onmicrosoft.com .

1. Prenumerera nu på din Logic app till händelser från utgivaren. Ange information om händelse prenumerationen enligt beskrivningen i följande tabell, till exempel:

   ![Skärm bild av Logic Apps designer, som visar informations redigeraren för Utlös ande av när en resurs händelse inträffar.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   | -------- | -------- | ----- | ----------- |
   | **Prenumeration** | Ja | <*händelse-utgivare-Azure-Subscription-namn*> | Välj namnet på den Azure-prenumeration som är associerad med *händelse utgivaren*. I den här självstudien väljer du namnet på Azure-prenumerationen för den virtuella datorn. |
   | **Resurstyp** | Ja | <*händelse – utgivare – Azure-resurs-typ*> | Välj resurs typen Azure för händelse utgivaren. Mer information om resurs typer i Azure finns i [Azure Resource providers och-typer](../azure-resource-manager/management/resource-providers-and-types.md). I den här självstudien väljer du `Microsoft.Resources.ResourceGroups` värdet för att övervaka Azures resurs grupper. |
   | **Resursnamn** |  Ja | <*händelse – utgivare – Azure-resurs-namn*> | Välj Azure-resursens namn för händelse utgivaren. Den här listan varierar beroende på vilken resurs typ du har valt. I den här självstudien väljer du namnet på den Azure-resurs grupp som innehåller den virtuella datorn. |
   | **Objekt i händelse typ** |  Inga | <*händelse typer*> | Välj en eller flera speciella händelse typer som ska filtreras och skickas till Event Grid. Du kan till exempel lägga till dessa händelse typer för att upptäcka när resurser ändras eller tas bort: <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>Mer information finns i de här ämnena: <p><p>- [Azure Event Grid händelse schema för resurs grupper](../event-grid/event-schema-resource-groups.md) <br>- [Förstå händelse filtrering](../event-grid/event-filtering.md) <br>- [Filtrera händelser för Event Grid](../event-grid/how-to-filter-events.md) |
   | Om du vill lägga till valfria egenskaper väljer du **Lägg till ny parameter** och väljer sedan de egenskaper som du vill använda. | Inga | {se beskrivningar} | * **Prefix filter**: i den här självstudien lämnar du den här egenskapen tom. Standardbeteendet matchar alla värden. Du kan dock ange en prefixsträng som ett filter, till exempel en sökväg och en parameter för en specifik resurs. <p>* **Suffix filter**: i den här självstudien lämnar du den här egenskapen tom. Standardbeteendet matchar alla värden. Du kan dock ange en suffixsträng som ett filter, till exempel ett filnamnstillägg, om du bara vill använda specifika filtyper. <p>* **Prenumerations namn**: i den här självstudien kan du ange ett unikt namn för din händelse prenumeration. |
   |||

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**. Om du vill komprimera och dölja en åtgärds information i din Logic app väljer du åtgärdens namn List.

   ![Skärm bild av Logic Apps designer, som visar knappen Spara, för att spara arbets flödes redigeringar.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   När du sparar logikappen med en utlösare för händelserutnät skapar Azure automatiskt en händelseprenumeration för logikappen för den resurs du valt. När resursen sedan publicerar en händelse i händelserutnätet, skickar händelserutnätet automatiskt händelsen till logikappen. Den här händelsen utlöser logikappen och skapar och kör sedan en instans av arbetsflödet som du definierar i följande steg.

Nu är logikappen aktiv och lyssnar på händelser från händelserutnätet, men den gör inget förrän du lägger till åtgärder i arbetsflödet.

## <a name="add-a-condition"></a>Lägga till ett villkor

Om du vill att din Logi Kap par endast ska köras när en händelse eller åtgärd sker, lägger du till ett villkor som kontrollerar `Microsoft.Compute/virtualMachines/write` åtgärden. När det här villkoret är sant skickar logikappen ett e-postmeddelande till dig med information om den uppdaterade virtuella datorn.

1. I Logic App Designer går du till händelse rutnäts utlösaren och väljer **nytt steg**.

   ![Skärm bild av Logic Apps designer, som visar knappen för att lägga till ett nytt steg i arbets flödet.](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. Under **Välj en åtgärd** går du till rutan Sök och anger `condition` som ditt filter. I listan åtgärder väljer du **villkors** åtgärden.

   ![Skärm bild av Logic Apps designer, som visar knappen för att lägga till en villkors åtgärd.](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Logic Apps-designern lägger till ett tomt villkor till arbetsflödet, inklusive åtgärdssökvägar som ska följas beroende på om villkoret är sant eller falskt.

   ![Skärm bild av Logic Apps designer, som visar ett tomt villkor som lagts till i arbets flödet.](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Byt namn på villkors rubriken till `If a virtual machine in your resource group has changed` . I villkorets namn List väljer du knappen med punkter (**...**) och sedan **Byt namn**.

   ![Skärm bild av Logic Apps designer, som visar villkors redigerarens snabb meny med alternativet Byt namn valt.](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Skapa ett villkor som kontrollerar händelsen `body` för ett `data` objekt där `operationName` egenskapen är lika med `Microsoft.Compute/virtualMachines/write` åtgärden. Lär dig mer om [händelsescheman i Event Grid](../event-grid/event-schema.md).

   1. Klicka i den vänstra rutan på den första raden under **Och**. I listan med dynamiskt innehåll som visas väljer du **uttryck**.

      ![Skärm bild av Logic Apps designer, som visar villkoret med uttrycks redigeraren markerat.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. I uttrycks redigeraren anger du det här uttrycket, som returnerar åtgärds namnet från utlösaren och väljer **OK**:

      `triggerBody()?['data']['operationName']`

      Exempel:

      ![Skärm bild av Logic Apps designer, som visar villkors redigeraren med uttryck för att extrahera åtgärds namnet.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. I den mellersta rutan behåller du operatorn **is equal to** (är lika med).

   1. I den högra rutan anger du det här värdet, vilket är den åtgärd som du vill övervaka:

      `Microsoft.Compute/virtualMachines/write`

   Ditt slutförda villkor ser nu ut som i det här exemplet:

   ![Skärm bild av Logic Apps designer, som visar ett villkor som jämför åtgärden.](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Om du växlar från designvyn till kodvyn och tillbaka till designvyn, matchas det uttryck som du angav i villkoret med **data. operationName** -token:

   ![Skärm bild av Logic Apps designer, som visar ett villkor med matchade tokens.](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Spara logikappen.

## <a name="send-email-notifications"></a>Skicka e-postmeddelanden

Lägg nu till en [*åtgärd*](../logic-apps/logic-apps-overview.md#logic-app-concepts) så att du kan få ett e-postmeddelande när det angivna villkoret är sant.

1. I villkorets ruta **Om sant** väljer du **Lägg till en åtgärd**.

   ![Skärm bild av villkors redigeraren för Logic Apps designer som visar knappen för att lägga till en åtgärd när villkoret är sant.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. Under **Välj en åtgärd** går du till rutan Sök och anger `send an email` som ditt filter. Baserat på din e-leverantör söker du och väljer matchande anslutningsapp. Välj sedan åtgärden "send email" (skicka e-post) för anslutningsappen. Exempel:

   * För ett arbets- eller skolkonto i Azure väljer du anslutningsappen Office 365 Outlook.

   * För personliga Microsoft-konton väljer du anslutningsappen Outlook.com.

   * För Gmail-konton väljer du Gmail-anslutningsappen.

   Den här självstudien fortsätter med Office 365 Outlook Connector. Om du använder en annan provider förblir stegen desamma, men användar gränssnittet kan verka något annorlunda.

   ![Skärm bild av Logic Apps designer, som visar sökning efter skicka en e-poståtgärd i Office 365 Outlook Connector.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Om du inte redan har en anslutning för e-postleverantören loggar du in på ditt e-postkonto när du tillfrågas om autentisering.

1. Byt namn på åtgärden skicka e-post till den här rubriken: `Send email when virtual machine updated`

1. Ange information om e-postmeddelandet som anges i följande tabell:

   ![Skärm bild av Logic Apps designer, som visar att det dynamiska innehållet är addded till e-postämnesraden för ett sant villkor.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Om du vill välja utdata från föregående steg i arbets flödet klickar du i en redigerings ruta så att listan med dynamiskt innehåll visas eller väljer **Lägg till dynamiskt innehåll**. För fler resultat väljer du **Mer information** för varje avsnitt i listan. Stäng listan med dynamiskt innehåll genom att välja **Lägg till dynamiskt innehåll** igen.

   | Egenskap | Krävs | Värde | Beskrivning |
   | -------- | -------- | ----- | ----------- |
   | **Till** | Ja | <*mottagar \@ domän*> | Ange mottagarens e-postadress. I testsyfte kan du använda din egen e-postadress. |
   | **Ämne** | Ja | `Resource updated:`**Ämne** | Ange innehållet för e-postmeddelandets ämne. I den här självstudien anger du den angivna texten och väljer händelsens **ämnes** fält. Här innehåller e-postmeddelandets ämne namnet på den uppdaterade resursen (virtuell dator). |
   | **Brödtext** | Ja | `Resource:` **Avsnitt** <p>`Event type:` **Händelsetyp**<p>`Event ID:` **ID**<p>`Time:`**Tid för händelsen** | Ange innehållet för e-postmeddelandets ämne. I den här självstudien anger du den angivna texten och väljer händelsens **ämne**, **händelse typ**, **ID** och **tids** fält för händelsen så att din e-post innehåller den resurs som utlöste händelsen, händelse typen, händelsens tidstämpel och händelse-ID för uppdateringen. I den här självstudien är resursen den Azure-resurs grupp som valts i utlösaren. <p>Tryck på Skift+Retur om du vill lägga till tomma rader i innehållet. |
   ||||

   > [!NOTE]
   > Om du väljer ett fält som representerar en matris lägger designverktyget automatiskt till en **For each**-loop runt åtgärden som refererar till matrisen. På så sätt kan din logikappsåtgärd utförs på varje element i matrisen.

   Nu bör din e-poståtgärd se ut ungefär som i det här exemplet:

   ![Skärm bild av Logic Apps designer, som visar valda utdata som ska skickas via e-post när den virtuella datorn uppdateras.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Och den färdiga logikappen bör se ut ungefär som i det här exemplet:

   ![Skärm bild av Logic Apps designer, som visar skapade Logic-appen med information om utlösare och åtgärder.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Spara logikappen. Om du vill dölja och dölja varje åtgärds information i din Logic app väljer du åtgärdens namn List.

   Nu är logikappen aktiv, men den väntar på att den virtuella datorn ska ändras innan den gör något. Om du vill testa din logikapp nu kan du fortsätta till nästa avsnitt.

## <a name="test-your-logic-app-workflow"></a>Testa logikappens arbetsflöde

1. Kontrollera att logikappen tar emot de angivna händelserna genom att uppdatera den virtuella datorn.

   Du kan till exempel ändra storlek på den virtuella datorn på Azure Portal eller [ändra storlek på den virtuella datorn med Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Du bör få ett e-postmeddelande efter en liten stund. Exempel:

   ![Skärm bild av exempel på Outlook-e-post, med information om uppdatering av virtuella datorer.](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Om du vill granska körnings-och utlösnings historiken för din Logic app väljer du **Översikt** på din Logic app-meny. Om du vill visa mer information om en körning väljer du raden för den körningen.

   ![Skärm bild av sidan Översikt över Logi Kap par som visar att en lyckad körning har valts.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. Expandera det steg som du vill granska för att visa indata och utdata för varje steg. Den här informationen kan hjälpa dig att diagnostisera och felsöka problem i din logikapp.

   ![Skärm bild av Logic appss körnings historik, med information om varje körning.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Grattis, du har skapat och kört en logikapp som övervakar resurshändelser via ett händelserutnät och som skickar e-postmeddelanden när dessa händelser inträffar. Du har också lärt dig hur du enkelt kan skapa arbetsflöden som automatiserar processer och integrerar system och molntjänster.

Du kan övervaka andra konfigurationsändringar med händelserutnät och logikappar, till exempel:

* En virtuell dator får behörighet som Azure-rollbaserad åtkomst kontroll (Azure RBAC).
* Ändringar görs i en nätverkssäkerhetsgrupp (NSG) för ett nätverkskort (NIC).
* Diskar för en virtuell dator läggs till eller tas bort.
* Nätverkskortet för en virtuell dator tilldelas en offentlig IP-adress.

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudien använder resurser och utför åtgärder som medför avgifter i din Azure-prenumeration. När du är klar med självstudiekursen och testningen bör du därför inaktivera eller ta bort resurser som du inte vill betala för.

* Om du vill sluta köra din logikapp utan att ta bort ditt arbete inaktiverar du appen. På din Logic app-meny väljer du **Översikt**. Välj **inaktivera** i verktygsfältet.

  ![Skärm bild av Logic Apps-översikt, visar knappen Inaktivera som är markerad för att inaktivera Logic app.](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Om du inte ser logikappmenyn kan du försöka återgå till Azure-instrumentpanelen och öppna logikappen på nytt.

* Om du vill ta bort din Logic app permanent går du till menyn Logic app och väljer **Översikt**. Välj **ta bort** i verktygsfältet. Bekräfta att du vill ta bort din Logic app och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

* [Skapa och dirigera anpassade händelser med Event Grid](../event-grid/custom-event-quickstart.md)
