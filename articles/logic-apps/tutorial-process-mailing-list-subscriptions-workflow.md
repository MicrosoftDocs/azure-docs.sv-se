---
title: Skapa godkännandebaserade automatiserade arbetsflöden
description: Självstudie – Skapa ett godkännandebaserat automatiserat arbetsflöde som bearbetar e-postlisteprenumerationer med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: c6186e6e9f60e852f77943834bcd1ae3d526491d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777283"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Självstudie: Skapa automatiserade godkännandebaserade arbetsflöden med hjälp av Azure Logic Apps

Den här självstudien visar hur du skapar [en exempellogikapp](../logic-apps/logic-apps-overview.md) som automatiserar ett godkännandebaserat arbetsflöde. Mer specifikt bearbetar den här logikappen prenumerationsbegäranden för en distributionslista som hanteras av [MailChimp-tjänsten.](https://mailchimp.com/) Den här logikappen innehåller olika steg, som börjar med att övervaka ett e-postkonto för begäranden, skickar dessa begäranden för godkännande, kontrollerar om begäran får godkännande eller inte, lägger till godkända medlemmar i distributionslistan och bekräftar om nya medlemmar läggs till i listan.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa en tom logikapp.
> * Lägg till en utlösare som övervakar och söker efter prenumerationsbegäranden.
> * Lägg till en åtgärd som skickar e-postmeddelanden för att godkänna och avvisa förfrågningar.
> * Lägg till ett villkor som kontrollerar godkännandesvaret.
> * Lägg till en åtgärd som lägger till godkända medlemmar på distributionslistan.
> * Lägg till ett villkor som kontrollerar om dessa medlemmar har lagts till på listan.
> * Lägg till en åtgärd som skickar e-post angående om dessa medlemmar har lagts till på listan.

När du är klar ser logikappen ut som det här arbetsflödet på en hög nivå:

![Översikt över slutförd logikapp på hög nivå](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ett MailChimp-konto där du tidigare skapade en lista med namnet "test-members-ML" där logikappen kan lägga till e-postadresser för godkända medlemmar. Om du inte har ett konto kan du registrera dig för ett kostnadsfritt konto [och](https://login.mailchimp.com/signup/)sedan lära dig hur du skapar [en MailChimp-lista.](https://us17.admin.mailchimp.com/lists/#)

* Ett e-postkonto från en e-postleverantör som stöds av Logic Apps, till exempel Office 365 Outlook, Outlook.com eller Gmail. För andra providrar [läser du listan med anslutningsappar här](/connectors/). Den här snabbstarten använder Office 365 Outlook med ett arbets- eller skolkonto. Om du använder ett annat e-postkonto förblir de allmänna stegen desamma, men användargränssnittet kan skilja sig något.

* Ett e-postkonto i Office 365 Outlook eller Outlook.com, som har stöd för godkännandearbetsflöden. Den här självstudien använder Office 365 Outlook. Om du använder ett annat e-postkonto är stegen desamma, men användargränssnittet kan vara lite annorlunda.

* Om logikappen behöver kommunicera via en brandvägg som begränsar trafiken till specifika  IP-adresser måste brandväggen tillåta åtkomst för både [inkommande](logic-apps-limits-and-config.md#inbound) och utgående [IP-adresser](logic-apps-limits-and-config.md#outbound) som används av Logic Apps-tjänsten eller körningen i Azure-regionen där logikappen finns. Om logikappen även använder hanterade anslutningsappar, till exempel Office 365 Outlook-anslutningsappen eller SQL-anslutningsappen, eller använder anpassade anslutningsappar, måste brandväggen också tillåta åtkomst för alla utgående [IP-adresser](logic-apps-limits-and-config.md#outbound) för hanterade anslutningsappar i logikappens Azure-region. [](../connectors/managed.md) [](/connectors/custom-connectors/) 

## <a name="create-your-logic-app"></a>Skapa en logikapp

1. Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto. På Startsidan för Azure väljer du **Skapa en resurs.**

1. På Azure Marketplace väljer du **Integration**  >  **Logic App**.

   ![Skärmbild som visar Azure Marketplace med "Integration" och "Logic App" valda.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. I fönstret **Logikapp** anger du den information som beskrivs här om logikappen som du vill skapa.

   ![Skärmbild som visar skapandefönstret för logikappen och den information som ska tillhandahållas för den nya logikappen.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Prenumeration** | <*Azure-prenumerationens namn*> | Ditt Azure-prenumerationsnamn. I det här exemplet används `Pay-As-You-Go`. |
   | **Resursgrupp** | LA-MailingList-RG | Namnet på [Azure-resursgruppen](../azure-resource-manager/management/overview.md), som används för att organisera relaterade resurser. I det här exemplet skapas en ny resursgrupp med namnet `LA-MailingList-RG` . |
   | **Namn** | LA-MailingList | Logikappens namn, som endast får innehålla bokstäver, siffror, bindestreck ( `-` ), understreck ( `_` ), parenteser ( `(` , ) och punkter ( `)` `.` ). I det här exemplet används `LA-MailingList`. |
   | **Plats** | USA, västra | Den region där logikappens information ska lagras. I det här exemplet används `West US`. |
   | **Log Analytics** | Av | Behåll inställningen **Av** för diagnostisk loggning. |
   ||||

1. När du är klar väljer du **Granska + skapa**. När Azure har verifierat informationen om logikappen väljer du **Skapa**.

1. När Azure har distribuerat din app väljer **du Gå till resurs**.

   Azure öppnar fönstret Logic Apps mallval, som visar en introduktionsvideo, vanliga utlösare och mallmönster för logikapp.

1. Rulla nedåt förbi videoavsnitten och vanliga utlösare till **avsnittet** Mallar och välj **Tom logikapp.**

   ![Skärmbild som visar Logic Apps med "Tom logikapp" markerat.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Lägg sedan till en [Outlook-utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som lyssnar efter inkommande e-postmeddelanden med prenumerationsbegäranden. Varje logikapp måste börja med en utlösare som utlöses när en viss händelse inträffar eller när nya data uppfyller ett visst villkor. Mer information finns i [Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Lägga till utlösare för att övervaka e-post

1. I sökrutan Logic Apps Designer anger du och väljer utlösaren med namnet `when email arrives` När ett nytt **e-postmeddelande kommer**.

   * För Azure arbets- eller skolkonto väljer du **Office 365 Outlook**.
   * För personliga Microsoft-konton väljer du **Outlook.com**.

   Det här exemplet fortsätter genom att välja Office 365 Outlook.

   ![Skärmbild som visar sökrutan Logic Apps Designer som innehåller söktermen "när e-postmeddelandet kommer" och utlösaren "När ett nytt e-postmeddelande kommer" visas markerad.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

1. Om du inte redan har en anslutning loggar du in och autentiserar åtkomsten till ditt e-postkonto när du tillfrågas.

   Azure Logic Apps skapar en anslutning till ditt e-postkonto.

1. I utlösaren anger du kriterier för att kontrollera ny e-post.

   1. Ange mappen för att kontrollera e-postmeddelanden och behåll standardvärdena för de andra egenskaperna.

      ![Skärmbild som visar designern med åtgärden "När ett nytt e-postmeddelande kommer" och "Mapp" inställt på "Inbox" (Inkorg).](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

   1. Lägg till utlösarens **ämnesfilteregenskap** så att du kan filtrera e-postmeddelanden baserat på ämnesraden. Öppna listan **Lägg till ny parameter** och välj **Ämnesfilter.**

      ![Skärmbild som visar den öppna listan "Lägg till ny parameter" med "Ämnesfilter" valt.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Mer information om egenskaperna för den här utlösaren finns i referensen för [Office 365](/connectors/office365/) Outlook-anslutningsappen eller [referensen Outlook.com anslutningsappen](/connectors/outlook/).

   1. När egenskapen visas i utlösaren anger du följande text: `subscribe-test-members-ML`

      ![Skärmbild som visar egenskapen "Ämnesfilter" med texten "subscribe-test-members-ML" agivet.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Om du vill dölja utlösarinformationen för tillfället döljer du figuren genom att klicka inuti figurens namnlist.

   ![Skärmbild som visar den komprimerade utlösarformen.](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Spara logikappen. Välj Spara i **designerverktygsfältet.**

Logikappen har nu lanserats men gör inget annat än att kontrollera inkommande e-post. Därför ska vi lägga till en åtgärd som svarar när utlösaren utlöses.

## <a name="send-approval-email"></a>Skicka e-postmeddelande om godkännande

Nu när du har en utlösare lägger du till en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) som skickar ett e-postmeddelande för att godkänna eller avvisa begäran.

1. I Logic Apps Designer väljer du  Nytt steg under utlösaren När ett nytt **e-postmeddelande kommer.**

1. Under **Välj en åtgärd** anger du i sökrutan och väljer åtgärden Skicka e-postgodkännande. `send approval` 

   ![Skärmbild som visar listan "Välj en åtgärd" filtrerad efter "godkännande"-åtgärder och åtgärden "Skicka e-post för godkännande" markerad.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Ange nu värdena för de angivna egenskaperna som visas och beskrivs här. lämna standardvärdena för alla andra. Mer information om dessa egenskaper finns i referensen för [Office 365](/connectors/office365/) Outlook-anslutningsappen eller [Outlook.com för anslutningsappen.](/connectors/outlook/)

   ![Skärmbild som visar egenskaperna "Skicka e-post för godkännande"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Till** | <*approval-email-address*> | Godkännarens e-postadress. I testsyfte kan du använda din egen adress. I det här exemplet används den fiktiva `sophiaowen@fabrikam.com` e-postadressen. |
   | **Ämne** | `Approve member request for test-members-ML` | En beskrivande e-postrubrik |
   | **Användaralternativ** | `Approve, Reject` | Se till att den här egenskapen anger de svarsalternativ som godkännaren kan välja, som är **Godkänn** eller **Avvisa** som standard. |
   ||||

   > [!NOTE]
   > När du klickar i vissa redigeringsrutor visas listan med dynamiskt innehåll, som du kan ignorera för tillfället. Den här listan visar utdata från tidigare åtgärder som är tillgängliga för dig att välja som indata för efterföljande åtgärder i arbetsflödet.
 
1. Spara logikappen.

Lägg sedan till ett villkor som kontrollerar godkännaren valda svar.

## <a name="check-approval-response"></a>Kontrollera godkännandesvar

1. Under åtgärden **Skicka e-postmeddelande om** godkännande väljer du **Nytt steg.**

1. Under **Välj en åtgärd** väljer du **Inbyggd**. I sökrutan anger du `condition` och väljer åtgärden med namnet **Villkor.**

   ![Skärmbild som visar sökrutan "Välj en åtgärd" med "Inbyggd" markerad och "villkor" som sökterm, medan åtgärden "Villkor" visas markerad.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. I **namnlisten** Villkor väljer du **ellipsen** (**...**) och sedan Byt **namn på**. Byt namn på villkoret med den här beskrivningen: `If request approved`

   ![Skärmbild som visar den valda ellipsknappen med listan Inställningar öppen och kommandot "Byt namn" har valts.](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

1. Skapa ett villkor som kontrollerar om godkännaren har valt **Godkänn.**

   1. På villkorets vänstra sida klickar du i rutan **Välj ett** värde.

   1. I listan med dynamiskt innehåll som visas under **Skicka e-post för godkännande** väljer du egenskapen **SelectedOption.**

      ![Skärmbild som visar den dynamiska innehållslistan där utdata för "SelectedOption" visas markerade i avsnittet "Skicka e-post för godkännande".](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. I den mellersta jämförelserutan väljer du **operatorn är lika med.**

   1. Ange texten i rutan Välj ett värde **på villkorets högra** `Approve` sida.

      När du är klar ser villkoret ut som i det här exemplet:

      ![Skärmbild som visar det färdiga villkoret för exemplet på godkänd begäran](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Spara logikappen.

Ange sedan den åtgärd som utförs av logikappen när granskaren godkänner begäran. 

## <a name="add-member-to-mailchimp-list"></a>Lägg till medlem i MailChimp-lista

Lägg nu till en åtgärd som lägger till den godkända medlemmen i distributionslistan.

1. I villkorets **true-gren** väljer du Lägg **till en åtgärd**.

1. Under **sökrutan Välj en** åtgärd väljer du **Alla.** I sökrutan anger du `mailchimp` och väljer åtgärden Med namnet Lägg till medlem i **listan**.

   ![Skärmbild som visar rutan "Välj en åtgärd" med söktermen "mailchimp" och åtgärden "Lägg till medlem i listan" markerad.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Om du inte redan har en anslutning till ditt MailChimp-konto uppmanas du att logga in.

1. I åtgärden **Lägg till medlem i** listan anger du informationen som visas och beskrivs här:

   ![Skärmbild som visar åtgärdsinformationen "Lägg till medlem i listan".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **List Id** | Yes | <*mail-list-name*> | Välj namnet på mailChimp-distributionslistan. I det här exemplet används `test-members-ML`. |
   | **E-postadress** | Yes | <*new-member-email-address*> | I listan med dynamiskt innehåll som öppnas går du till avsnittet När ett nytt **e-postmeddelande** kommer, väljer **Från**, som är utdata från utlösaren och anger e-postadressen för den nya medlemmen. |
   | **Status** | Yes | <*status för medlemsprenumeration*> | Välj den prenumerationsstatus som ska anges för den nya medlemmen. Det här exemplet väljer `subscribed` . <p>Mer information finns i dokumentationen om att [hantera prenumeranter med MailChimp-API](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   |||||

   Mer information om åtgärdsegenskaperna **för Lägg till** medlem i listan finns i [referensen för MailChimp-anslutningsappen.](/connectors/mailchimp/)

1. Spara logikappen.

Sedan lägger till ett villkor så att du kan kontrollera om den nya medlemmen har lagts till på distributionslistan. På så sätt kan logikappen meddela dig om åtgärden lyckades eller misslyckades.

## <a name="check-for-success-or-failure"></a>Kontrollera om status är lyckad eller misslyckad

1. I **grenen True** går du till **liståtgärden Lägg till** medlem i listan och väljer Lägg till en **åtgärd**.

1. Under **Välj en åtgärd** väljer du **Inbyggd**. I sökrutan anger du `condition` och väljer åtgärden med namnet **Villkor.**

1. Byt namn på villkoret med den här beskrivningen: `If add member succeeded`

1. Skapa ett villkor som kontrollerar om den godkända medlemmen lyckas eller misslyckas med att ansluta till distributionslistan:

   1. På villkorets vänstra sida klickar du i rutan **Välj ett** värde. I listan med dynamiskt innehåll som visas går du **till avsnittet Lägg till medlem i** listan och väljer egenskapen **Status.**

      Villkoret ser till exempel ut som i det här exemplet:

      ![Skärmbild som visar villkorets vänstra sidas ruta "Välj ett värde" med "Status" angivet.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. I den mellersta jämförelserutan väljer du **operatorn är lika med.**

   1. Ange följande text i rutan Välj ett värde **på villkorets** högra sida: `subscribed`

      När du är klar ser villkoret ut som i det här exemplet:

      ![Skärmbild som visar det slutförda villkoret för att kontrollera en lyckad eller misslyckad prenumeration.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Konfigurera sedan e-postmeddelanden som ska skickas när den godkända medlemmen antingen lyckas eller misslyckas med att ansluta till distributionslistan.

## <a name="send-email-if-member-added"></a>Skicka e-post om medlem läggs till

1. Under villkoret **If add member succeeded (Om** lägg till medlem lyckades) går du till **grenen True** (Sant) och väljer Add an action **(Lägg till en åtgärd).**

   ![Skärmbild som visar villkoret "If add member succeeded" (Om lägg till medlem lyckades) med grenen "True" (Sant) med "Add an action" (Lägg till en åtgärd) markerad.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. I **sökrutan Välj en** åtgärd anger du `outlook send email` och väljer åtgärden Skicka ett **e-postmeddelande.**

   ![Skärmbild som visar sökrutan "Välj en åtgärd" med "outlook send email" angivet och åtgärden "Skicka ett e-postmeddelande" markerad för meddelande.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Byt namn på åtgärden med den här beskrivningen: `Send email on success`

1. I åtgärden **Skicka e-postmeddelande om** lyckad anger du informationen som visas och beskrivs här:

   ![Skärmbild som visar åtgärden "Skicka e-post vid lyckad åtgärd" och den information som angavs för e-postmeddelandet om att åtgärden lyckades.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Brödtext** | Yes | <*success-email-body*> | Brödtext i e-postmeddelandet. I den här självstudien följer du dessa steg: <p>1. Ange den här texten med ett avslutande blanksteg: `New member has joined "test-members-ML":` <p>2. I listan med dynamiskt innehåll som visas väljer du egenskapen **E-postadress.** <p>**Obs!** Om den här egenskapen inte visas väljer du **Visa mer** bredvid avsnittsrubriken Lägg till medlem **i listan.** <p>3. På nästa rad anger du den här texten med ett avslutande blanksteg: `Member opt-in status: ` <p>4. I listan med dynamiskt innehåll går du till **Lägg till medlem i listan** och väljer **egenskapen** Status. |
   | **Ämne** | Yes | <*success-email-subject*> | Ämnesraden för e-postmeddelandet. I den här självstudien följer du dessa steg: <p>1. Ange den här texten med ett avslutande blanksteg: `Success! Member added to "test-members-ML": ` <p>2. I listan med dynamiskt innehåll går du till **Lägg till medlem i listan** och väljer egenskapen **E-postadress.** |
   | **Till** | Yes | <*din e-postadress*> | E-postadress att skicka e-postmeddelandet till. I testsyfte kan du använda din egen e-postadress. |
   |||||

1. Spara logikappen.

## <a name="send-email-if-member-not-added"></a>Skicka e-post om medlem inte läggs till

1. Under villkoret **If add member succeeded (Om** lägg till medlem lyckades) går du **till grenen False** (Falskt) och väljer Add an action **(Lägg till en åtgärd).**

   ![Skärmbild som visar villkoret "If add member succeeded" (Om lägg till medlem lyckades) grenen "False" med "Add an action" (Lägg till en åtgärd) markerat.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. I **sökrutan Välj en** åtgärd anger du `outlook send email` och väljer åtgärden Skicka ett **e-postmeddelande.**

   ![Skärmbild som visar sökrutan "Välj en åtgärd" med "outlook send email" angivet och åtgärden "Skicka ett e-postmeddelande" markerad.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Byt namn på åtgärden med den här beskrivningen: `Send email on failure`

1. Ange information om den här åtgärden enligt beskrivningen här:

   ![Skärmbild som visar åtgärden "Skicka e-post vid fel" och den information som angavs för e-postmeddelandet om felet.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Brödtext** | Yes | <*body-for-failure-email*> | Brödtext i e-postmeddelandet. För den här självstudiekursen anger du den här texten: <p>`Member might already exist. Check your MailChimp account.` |
   | **Ämne** | Yes | <*subject-for-failure-email*> | Ämnesraden för e-postmeddelandet. Följ dessa steg för den här självstudien: <p>1. Ange den här texten med ett avslutande blanksteg: `Failed, member not added to "test-members-ML": ` <p>2. I listan med dynamiskt innehåll går du till **Lägg till medlem i listan** och väljer egenskapen **E-postadress.** |
   | **Till** | Yes | <*din e-postadress*> | E-postadress att skicka e-postmeddelandet till. I testsyfte kan du använda din egen e-postadress. |
   |||||

1. Spara logikappen. 

Nu testar du logikappen, som ser ut som i det här exemplet:

![Skärmbild som visar det färdiga arbetsflödet för logikappen.](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Kör logikappen

1. Skicka en begäran till dig själv om att ansluta till distributionslistan. Vänta tills begäran visas i inkorgen.

1. Starta logikappen manuellt genom att välja Kör i **designerverktygsfältet.** 

   Om e-postmeddelandet har ett ämne som matchar ämnesfiltret för utlösaren skickar logikappen ditt e-postmeddelande för att godkänna prenumerationsbegäran.

1. I det e-postmeddelande för godkännande som du får väljer du **Godkänn.**

1. Om prenumerantens e-postadress inte finns på distributionslistan lägger logikappen till personens e-postadress och skickar dig ett e-postmeddelande liknande det i det här exemplet:

   ![Skärmbild som visar exempel på ett e-postmeddelande för en lyckad prenumeration.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Om logikappen inte kan lägga till prenumeranten får du ett e-postmeddelande som liknar det i det här exemplet:

   ![Skärmbild som visar exempel-e-postmeddelandet för en misslyckad prenumeration.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

  > [!TIP]
  > Om du inte får e-post kan du titta i mappen Skräppost. Ditt skräppostfilter kan dirigera om dessa typer av e-post. Om du är osäker på om din logikapp kördes korrekt kan du läsa informationen om att [felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

Klart! Nu har du skapat och kör en logikapp som integrerar information över Azure, Microsoft-tjänster och andra SaaS-appar.

## <a name="clean-up-resources"></a>Rensa resurser

Logikappen fortsätter att köras tills du inaktiverar eller tar bort appen. När du inte längre behöver exempellogikappen tar du bort resursgruppen som innehåller logikappen och relaterade resurser.

1. I Azure Portal sökrutan anger du namnet på den resursgrupp som du skapade. Välj resursgruppen under **Resursgrupper** i resultatet.

   I det här exemplet skapades resursgruppen med namnet `LA-MailingList-RG` .

   ![Skärmbild som visar Azure-sökrutan med "la-mailinglist-rg" angivet och **LA-MailingList-RG** valt.](./media/tutorial-process-mailing-list-subscriptions-workflow/find-resource-group.png)

   > [!TIP]
   > Om azure-startsidan visar resursgruppen under **Senaste resurser** kan du välja gruppen från startsidan.

1. På resursgruppmenyn kontrollerar du att **Översikt** har valts. I verktygsfältet **i** fönstret Översikt väljer du Ta **bort resursgrupp.**

   ![Skärmbild som visar resursgruppens "Översikt"-fönster och i fönstrets verktygsfält är "Ta bort resursgrupp" markerat.](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. I bekräftelsefönstret som visas anger du resursgruppens namn och väljer Ta **bort.**

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en logikapp som hanterar godkännanden för begäranden om distributionslistor. Nu får du lära dig att skapa en logikapp som bearbetar och lagrar e-postbilagor genom att integrera Azure-tjänster, till exempel Azure Storage och Azure Functions.

> [!div class="nextstepaction"]
> [Bearbeta e-postbilagor](../logic-apps/tutorial-process-email-attachments-workflow.md)
