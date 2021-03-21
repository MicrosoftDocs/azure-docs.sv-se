---
title: Bygg godkännande-baserade automatiserade arbets flöden
description: Självstudie – Skapa ett godkännande baserat automatiserat arbets flöde som bearbetar prenumerationer på e-postlistor med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/07/2020
ms.openlocfilehash: 1690b8d143b86e5caa691f5f8f479f715f57f0c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99054660"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Självstudie: skapa automatiska godkännande-baserade arbets flöden med hjälp av Azure Logic Apps

Den här självstudien visar hur du skapar en exempel- [Logic-app](../logic-apps/logic-apps-overview.md) som automatiserar ett godkännande-baserat arbets flöde. Mer specifikt bearbetar den här exempel Logic-appen prenumerations begär Anden för en e-postlista som hanteras av [MailChimp](https://mailchimp.com/) -tjänsten. Den här Logic-appen innehåller olika steg, som börjar med att övervaka ett e-postkonto för förfrågningar, skickar dessa begär Anden om godkännande, kontrollerar om begäran får godkännande, lägger till godkända medlemmar i e-postlistan och bekräftar om nya medlemmar läggs till i listan.

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

![Översikt över färdiga Logic-appar på hög nivå](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ett MailChimp-konto där du tidigare har skapat en lista med namnet "test-members-ML" där din Logic app kan lägga till e-postadresser för godkända medlemmar. Om du inte har ett konto kan du [Registrera dig för ett kostnads fritt konto](https://login.mailchimp.com/signup/)och sedan lära dig [hur du skapar en MailChimp-lista](https://us17.admin.mailchimp.com/lists/#).

* Ett e-postkonto från en e-postleverantör som stöds av Logic Apps, till exempel Office 365 Outlook, Outlook.com eller Gmail. För andra providrar [läser du listan med anslutningsappar här](/connectors/). I den här snabb starten används Office 365 Outlook med ett arbets-eller skol konto. Om du använder ett annat e-postkonto förblir de allmänna stegen desamma, men användar gränssnittet kan skilja sig något.

* Ett e-postkonto i Office 365 Outlook eller Outlook.com, som stöder arbets flöden för godkännande. Den här självstudien använder Office 365 Outlook. Om du använder ett annat e-postkonto är stegen desamma, men användargränssnittet kan vara lite annorlunda.

* Om din Logi Kap par behöver kommunicera via en brand vägg som begränsar trafik till vissa IP-adresser, måste brand väggen tillåta åtkomst  för både [inkommande](logic-apps-limits-and-config.md#inbound) och [utgående](logic-apps-limits-and-config.md#outbound) ip-adresser som används av Logic Apps tjänst eller körning i den Azure-region där din Logic app finns. Om din Logic app även använder [hanterade anslutningar](../connectors/apis-list.md#managed-api-connectors), till exempel Office 365 Outlook Connector eller SQL-anslutning, eller använder [anpassade anslutningar](/connectors/custom-connectors/), måste brand väggen också tillåta åtkomst för *alla* [utgående IP-adresser för hanterad anslutning](logic-apps-limits-and-config.md#outbound) i din Logic app Azure-region.

## <a name="create-your-logic-app"></a>Skapa en logikapp

1. Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto. På Azures start sida väljer du **skapa en resurs**.

1. På menyn för Azure Marketplace väljer du **integrering**  >  **Logic app**.

   ![Skärm bild som visar menyn för Azure Marketplace med "integration" och "Logic app" valt.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. I fönstret **Logic app** anger du den information som beskrivs här om den Logic-app som du vill skapa.

   ![Skärm bild som visar fönstret för att skapa logiska appar och den information som ska tillhandahållas för den nya Logic-appen.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Prenumeration** | <*Azure-prenumeration-namn*> | Namnet på din Azure-prenumeration. I det här exemplet används `Pay-As-You-Go`. |
   | **Resursgrupp** | LA-MailingList-RG | Namnet på Azure- [resurs gruppen](../azure-resource-manager/management/overview.md)som används för att organisera relaterade resurser. I det här exemplet skapas en ny resurs grupp med namnet `LA-MailingList-RG` . |
   | **Namn** | LA-MailingList | Din Logic Apps namn, som endast får innehålla bokstäver, siffror, bindestreck ( `-` ), under streck ( `_` ), parenteser ( `(` , `)` ) och punkter ( `.` ). I det här exemplet används `LA-MailingList`. |
   | **Plats** | USA, västra | Den region där du vill lagra information om din Logic Apps. I det här exemplet används `West US`. |
   | **Log Analytics** | Av | Behåll inställningen **Av** för diagnostisk loggning. |
   ||||

1. När du är klar väljer du **Granska + skapa**. När Azure har verifierat informationen om din Logic app väljer du **skapa**.

1. När Azure har distribuerat din app väljer **du gå till resurs**.

   Azure öppnar fönstret Välj mall för val av Logic Apps som visar en introduktions video, vanliga utlösare och mönster för Logic app-mallar.

1. Rulla nedåt i avsnitten video och vanliga utlösare i avsnittet **mallar** och välj **Tom Logic app**.

   ![Skärm bild som visar fönstret för val av mall för Logic Apps med "Tom Logic app" markerat.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Lägg sedan till en Outlook- [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som lyssnar efter inkommande e-postmeddelanden med prenumerations begär Anden. Varje Logi Kap par måste börja med en utlösare som utlöses när en enskild händelse inträffar eller när nya data uppfyller ett angivet villkor. Mer information finns i [Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Lägga till utlösare för att övervaka e-post

1. I sökrutan i Logic Apps designer anger `when email arrives` du och väljer utlösaren med namnet **när ett nytt e-postmeddelande tas emot**.

   * För Azure arbets- eller skolkonto väljer du **Office 365 Outlook**.
   * För personliga Microsoft-konton väljer du **Outlook.com**.

   Det här exemplet fortsätter genom att välja Office 365 Outlook.

   ![Skärm bild som visar sökrutan i Logic Apps designer som innehåller Sök termen "när e-post anländer", och utlösaren "när ett nytt e-postmeddelande anländer" visas som markerad.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

1. Om du inte redan har en anslutning loggar du in och autentiserar åtkomsten till ditt e-postkonto när du uppmanas till det.

   Azure Logic Apps skapar en anslutning till ditt e-postkonto.

1. I utlösaren anger du villkoren för att kontrol lera ny e-post.

   1. Ange mappen för att kontrol lera e-postmeddelanden och se till att de andra egenskaperna har angetts till standardvärdena.

      ![Skärm bild som visar design verktyget med alternativet "när ett nytt e-postmeddelande kommer" och "mapp" inställt på "Inkorg".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

   1. Lägg till egenskapen **subject filter** för utlösare så att du kan filtrera e-postmeddelanden baserat på ämnes raden. Öppna listan **Lägg till ny parameter** och välj **subject filter**.

      ![Skärm bild som visar listan "Lägg till ny parameter" med alternativet "ämnes filter" markerat.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Mer information om den här utlösarens egenskaper finns i [referens för Office 365 Outlook Connector](/connectors/office365/) eller [kopplings referens för Outlook.com](/connectors/outlook/).

   1. När egenskapen visas i utlösaren anger du den här texten: `subscribe-test-members-ML`

      ![Skärm bild som visar egenskapen "ämnes filter" med texten "Prenumerera-test-members-ML" angiven.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Om du vill dölja utlösarens information för tillfället kan du dölja formen genom att klicka inuti formens namn List.

   ![Skärm bild som visar formen komprimerad utlösare.](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

Logikappen har nu lanserats men gör inget annat än att kontrollera inkommande e-post. Därför ska vi lägga till en åtgärd som svarar när utlösaren utlöses.

## <a name="send-approval-email"></a>Skicka e-postmeddelande om godkännande

Nu när du har en utlösare lägger du till en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) som skickar ett e-postmeddelande för att godkänna eller avvisa begäran.

1. Välj **nytt steg** under **när ett nytt e-postmeddelande anländer** till utlösaren i Logic Apps designer.

1. Under **Välj en åtgärd** i sökrutan anger du `send approval` och väljer åtgärden **skicka e-postmeddelande för godkännande**.

   ![Skärm bild som visar listan "Välj en åtgärd" som filtrerats efter "godkännande"-åtgärder och åtgärden "skicka e-post för godkännande" vald.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Ange värdena för de angivna egenskaperna som visas och beskrivs här. lämna alla andra med standardvärdena. Mer information om dessa egenskaper finns i referens för [Office 365 Outlook Connector](/connectors/office365/) eller [kopplings referens för Outlook.com](/connectors/outlook/).

   ![Skärm bild som visar egenskaperna "skicka e-post för godkännande"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Till** | <*godkännande-e-postadress*> | Godkännarens e-postadress. I testsyfte kan du använda din egen adress. I det här exemplet används den fiktiva `sophiaowen@fabrikam.com` e-postadressen. |
   | **Ämne** | `Approve member request for test-members-ML` | En beskrivande e-postrubrik |
   | **Användaralternativ** | `Approve, Reject` | Kontrol lera att den här egenskapen anger de svars alternativ som god kännaren kan välja, som är **godkänns** eller **avvisas** som standard. |
   ||||

   > [!NOTE]
   > När du klickar i vissa redigerings rutor visas den dynamiska innehålls listan, som du kan ignorera för tillfället. I den här listan visas utdata från tidigare åtgärder som du kan välja som indata till efterföljande åtgärder i ditt arbets flöde.
 
1. Spara logikappen.

Lägg sedan till ett villkor som kontrollerar god kännars valda svar.

## <a name="check-approval-response"></a>Kontrollera godkännandesvar

1. Under åtgärden **skicka e-post för godkännande** väljer du **nytt steg**.

1. Under **Välj en åtgärd** väljer du **inbyggt**. I sökrutan anger du `condition` och väljer åtgärden med namnet **villkor**.

   ![Skärm bild som visar sökrutan "Välj en åtgärd" med "inbyggda" valda och "villkor" som Sök villkor, medan åtgärden "villkor" visas som markerad.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. I **villkorets** rubrik fält väljer du knappen med **tre punkter** (**...**) och väljer sedan **Byt namn**. Byt namn på villkoret med den här beskrivningen: `If request approved`

   ![Skärm bild som visar knappen ovaler som marker ATS med listan "Inställningar" öppnad och kommandot "Byt namn" har marker ATS.](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

1. Bygg ett villkor som kontrollerar om god kännaren har valt **Godkänn**.

   1. Klicka i rutan **Välj ett värde** på villkorets vänstra sida.

   1. I listan med dynamiskt innehåll som visas under **skicka e-post för godkännande** väljer du egenskapen **SelectedOption** .

      ![Skärm bild som visar den dynamiska innehålls listan i avsnittet "skicka e-postgodkännande" visas "SelectedOption"-utdata markerat.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. I rutan mellan jämförelse väljer du operatorn **är lika** med.

   1. Ange texten i rutan **Välj ett värde** på villkorets högra sida `Approve` .

      När du är klar ser villkoret ut som i det här exemplet:

      ![Skärm bild som visar det färdiga villkoret för det godkända begäran-exemplet](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Spara logikappen.

Ange sedan den åtgärd som utförs av logikappen när granskaren godkänner begäran. 

## <a name="add-member-to-mailchimp-list"></a>Lägg till medlem i MailChimp-lista

Lägg nu till en åtgärd som lägger till den godkända medlemmen i din distributions lista.

1. I villkorets **sanna** gren väljer du **Lägg till en åtgärd**.

1. Under Sök rutan **Välj en åtgärd** väljer du **alla**. I rutan Sök anger du `mailchimp` och väljer åtgärden **Lägg till medlem i listan**.

   ![Skärm bild som visar rutan "Välj en åtgärd" med sökordet "MailChimp" och åtgärden "Lägg till medlem i lista" vald.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Om du inte redan har en anslutning till ditt MailChimp-konto uppmanas du att logga in.

1. I åtgärden **Lägg till medlem i lista** anger du den information som visas och beskrivs här:

   ![Skärm bild som visar åtgärds information om att lägga till medlem i listan.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **List Id** | Ja | <*post-lista-namn*> | Välj namnet på din MailChimp-e-postlista. I det här exemplet används `test-members-ML`. |
   | **E-postadress** | Ja | <*ny medlem – e-postadress*> | I listan med dynamiskt innehåll som öppnas från avsnittet **när ett nytt e-postmeddelande anländer** väljer du **från**, som är utdata från utlösaren och anger e-postadressen för den nya medlemmen. |
   | **Status** | Ja | <*medlem-prenumeration-status*> | Välj den prenumerations status som ska anges för den nya medlemmen. Det här exemplet väljer `subscribed` . <p>Mer information finns i dokumentationen om att [hantera prenumeranter med MailChimp-API](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   |||||

   Mer information om åtgärds egenskaperna **Lägg till medlem i lista** finns i [referens för MailChimp-koppling](/connectors/mailchimp/).

1. Spara logikappen.

Sedan lägger till ett villkor så att du kan kontrollera om den nya medlemmen har lagts till på distributionslistan. På så sätt kan din Logic-app meddela dig om den här åtgärden lyckades eller misslyckades.

## <a name="check-for-success-or-failure"></a>Kontrollera om status är lyckad eller misslyckad

1. I den **sanna** grenen, under åtgärden **Lägg till medlem i listan** , väljer du **Lägg till en åtgärd**.

1. Under **Välj en åtgärd** väljer du **inbyggt**. I sökrutan anger du `condition` och väljer åtgärden med namnet **villkor**.

1. Byt namn på villkoret med den här beskrivningen: `If add member succeeded`

1. Skapa ett villkor som kontrollerar om den godkända medlemmen lyckas eller misslyckas med att ansluta till distributionslistan:

   1. Klicka i rutan **Välj ett värde** på villkorets vänstra sida. Från listan med dynamiskt innehåll som visas i avsnittet **Lägg till medlem i listan** väljer du egenskapen **status** .

      Ditt villkor ser t. ex. ut så här:

      ![Skärm bild som visar villkorets vänstra sida "Välj ett värde" med "status" angiven.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. I rutan mellan jämförelse väljer du operatorn **är lika** med.

   1. Ange följande text i rutan **Välj ett värde** på villkorets högra sida: `subscribed`

      När du är klar ser villkoret ut som i det här exemplet:

      ![Skärm bild som visar det färdiga villkoret för att kontrol lera att prenumerationen lyckades eller misslyckades.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Konfigurera sedan de e-postmeddelanden som ska skickas när den godkända medlemmen lyckas eller Miss lyckas med att ansluta till din distributions lista.

## <a name="send-email-if-member-added"></a>Skicka e-post om medlem läggs till

1. Under villkoret **om Lägg till medlem lyckades** , i den **sanna** grenen, väljer du **Lägg till en åtgärd**.

   ![Skärm bild som visar "sant"-grenen "om Lägg till medlem lyckades" med "Lägg till en åtgärd" markerat.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. I sökrutan **Välj en åtgärd** anger du `outlook send email` och väljer åtgärden **Skicka ett e-postmeddelande**.

   ![Skärm bild som visar sökrutan "Välj en åtgärd" där "Outlook Send e-mail" har angetts och åtgärden "Skicka ett e-postmeddelande" har marker ATS för meddelande.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Byt namn på åtgärden med den här beskrivningen: `Send email on success`

1. I åtgärden **skicka e-post vid lyckad** , anger du informationen som visas och beskrivs här:

   ![Skärm bild som visar åtgärden "skicka e-post vid lyckad åtgärd" och informationen om e-postmeddelandet.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Brödtext** | Ja | <*klar – e-postbrödtext*> | Brödtext i e-postmeddelandet. I den här självstudien följer du dessa steg: <p>1. Ange den här texten med ett avslutande blank steg: `New member has joined "test-members-ML":` <p>2. i listan med dynamiskt innehåll som visas väljer du egenskapen **e-postadress** . <p>**Obs!** om den här egenskapen inte visas, bredvid rubriken **Lägg till medlem i listan** , väljer du **Visa mer**. <p>3. i nästa rad anger du den här texten med ett avslutande blank steg: `Member opt-in status: ` <p>4. i listan med dynamiskt innehåll väljer du egenskapen **status** under **Lägg till medlem i listan**. |
   | **Ämne** | Ja | <*lyckades – e-post-subject*> | Ämnesraden för e-postmeddelandet. I den här självstudien följer du dessa steg: <p>1. Ange den här texten med ett avslutande blank steg: `Success! Member added to "test-members-ML": ` <p>2. gå till listan med dynamiskt innehåll och välj egenskapen **e-postadress** under **Lägg till medlem i listan**. |
   | **Till** | Ja | <*din-e-postadress*> | E-postadress att skicka e-postmeddelandet till. I testsyfte kan du använda din egen e-postadress. |
   |||||

1. Spara logikappen.

## <a name="send-email-if-member-not-added"></a>Skicka e-post om medlem inte läggs till

1. Under villkoret **om Lägg till medlem lyckades** i den **falska** grenen väljer du **Lägg till en åtgärd**.

   ![Skärm bild som visar "" falskt "-grenen" om Lägg till medlem lyckades "med" Lägg till en åtgärd "markerat.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. I sökrutan **Välj en åtgärd** anger du `outlook send email` och väljer åtgärden **Skicka ett e-postmeddelande**.

   ![Skärm bild som visar sökrutan "Välj en åtgärd" med "Outlook Send e-mail" och åtgärden "skicka e-post" vald.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Byt namn på åtgärden med den här beskrivningen: `Send email on failure`

1. Ange information om den här åtgärden som det visas och beskrivs här:

   ![Skärm bild som visar åtgärden "skicka e-post vid haveri" och informationen om e-postmeddelandet.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Brödtext** | Ja | <*Body-för-Failure-e-post*> | Brödtext i e-postmeddelandet. För den här självstudiekursen anger du den här texten: <p>`Member might already exist. Check your MailChimp account.` |
   | **Ämne** | Ja | <*Subject-för-Failure-e-post*> | Ämnesraden för e-postmeddelandet. I den här självstudien följer du dessa steg: <p>1. Ange den här texten med ett avslutande blank steg: `Failed, member not added to "test-members-ML": ` <p>2. gå till listan med dynamiskt innehåll och välj egenskapen **e-postadress** under **Lägg till medlem i listan**. |
   | **Till** | Ja | <*din-e-postadress*> | E-postadress att skicka e-postmeddelandet till. I testsyfte kan du använda din egen e-postadress. |
   |||||

1. Spara logikappen. 

Nu testar du logikappen, som ser ut som i det här exemplet:

![Skärm bild som visar exempel på Logic app-arbetsflöde.](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Kör logikappen

1. Skicka en begäran till dig själv om att ansluta till distributionslistan. Vänta tills begäran visas i inkorgen.

1. Om du vill starta din Logic app manuellt går du till verktygsfältet i designern och väljer **Kör**. 

   Om e-postmeddelandet har ett ämne som matchar ämnesfiltret för utlösaren skickar logikappen ditt e-postmeddelande för att godkänna prenumerationsbegäran.

1. Välj **Godkänn** i e-postmeddelandet som du får.

1. Om prenumerantens e-postadress inte finns på distributionslistan lägger logikappen till personens e-postadress och skickar dig ett e-postmeddelande liknande det i det här exemplet:

   ![Skärm bild som visar exempel-e-postmeddelandet för en lyckad prenumeration.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Om logikappen inte kan lägga till prenumeranten får du ett e-postmeddelande som liknar det i det här exemplet:

   ![Skärm bild som visar ett exempel på ett e-postmeddelande om en misslyckad prenumeration.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

  > [!TIP]
  > Om du inte får e-post kan du titta i mappen Skräppost. Ditt skräppostfilter kan dirigera om dessa typer av e-post. Om du är osäker på om din logikapp kördes korrekt kan du läsa informationen om att [felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

Klart! Nu har du skapat och kör en logikapp som integrerar information över Azure, Microsoft-tjänster och andra SaaS-appar.

## <a name="clean-up-resources"></a>Rensa resurser

Din Logic app fortsätter att köras tills du inaktiverar eller tar bort appen. När du inte längre behöver appen exempel logik tar du bort resurs gruppen som innehåller din Logic app och relaterade resurser.

1. I sökrutan Azure Portal anger du namnet på resurs gruppen som du skapade. Välj resurs gruppen under **resurs grupper** i resultaten.

   I det här exemplet skapades resurs gruppen med namnet `LA-MailingList-RG` .

   ![Skärm bild som visar Azure Search-rutan med "La-Mailinglist-RG" angiven och * * LA-MailingList-RG * * valt.](./media/tutorial-process-mailing-list-subscriptions-workflow/find-resource-group.png)

   > [!TIP]
   > Om Azures start sida visar resurs gruppen under de **senaste resurserna** kan du välja gruppen från start sidan.

1. På menyn resurs grupp kontrollerar du att **Översikt** är markerat. I verktygsfältet i **översikts** fönstret väljer du **ta bort resurs grupp**.

   ![Skärm bild som visar resurs gruppens översikts fönster och i fönstrets verktygsfält, "ta bort resurs grupp" är markerat.](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. I bekräftelse fönstret som visas anger du resurs gruppens namn och väljer **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en Logic-app som hanterar godkännanden för begär Anden om distributions listor. Nu får du lära dig att skapa en logikapp som bearbetar och lagrar e-postbilagor genom att integrera Azure-tjänster, till exempel Azure Storage och Azure Functions.

> [!div class="nextstepaction"]
> [Bearbeta e-postbilagor](../logic-apps/tutorial-process-email-attachments-workflow.md)
