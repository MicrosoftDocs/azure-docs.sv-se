---
title: Snabbstart – Skapa ditt Logic Apps arbetsflöde – Azure Portal
description: Skapa ditt första automatiserade Logic Apps arbetsflöde i Azure Portal den här snabbstartsguiden. Lär dig grunderna i systemintegrering och EAI-lösningar (Enterprise Application Integration) i Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: d05566c0734f95e14335c6165de0b2104fa19bc6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764863"
---
# <a name="quickstart-create-your-first-logic-apps-workflow---azure-portal"></a>Snabbstart: Skapa ditt Logic Apps arbetsflöde – Azure Portal

Den här snabbstarten förklarar hur du skapar ditt [första arbetsflöde i Azure Logic Apps](logic-apps-overview.md) genom [Azure Portal](https://portal.azure.com). Den här introduktionsguiden förklarar också grundläggande begrepp för Logic Apps-tjänsten, inklusive hur du skapar en ny logikapp, lägger till en utlösare och åtgärd i logikappen och testar logikappen. Följ den här snabbstarten för att skapa en exempellogikapp som regelbundet kontrollerar ett RSS-flöde och skickar ett e-postmeddelande för nya objekt. Följande skärmbild visar det avancerade arbetsflödet för den här exempellogikappen:

![Skärmbild av Logic Apps Designer som visar exempellogikappen där utlösaren är ett RSS-flöde och åtgärden skickar ett e-postmeddelande.](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

<a name="prerequisites"></a>

Om du vill lära dig hur du skapar och hanterar din första logikapp via andra gränssnitt och appar kan du läsa följande Logic Apps snabbstarter: 

* [Skapa och hantera logikappar med hjälp av Azure Command-Line Interface (Azure CLI)](quickstart-logic-apps-azure-cli.md)
* [Skapa och hantera logikappar i Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)
* [Skapa och hantera logikappar i Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du inte har ett konto kan du [registrera dig för ett kostnadsfritt Azure-konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Ett e-postkonto från en tjänst som stöds av Logic Apps (till exempel Office 365 Outlook eller Outlook.com). För andra e-postleverantörer som [stöds granskar du listan över anslutningsappar.](/connectors/)

    > [!IMPORTANT]
    > Om du använder [Gmail-anslutningsappen](/connectors/gmail/)bör du observera att endast G Suite-konton kan använda den här anslutningsappen utan begränsning i Logic Apps. Om du har ett Gmail-konsumentkonto kan du bara använda den här anslutningsappen med specifika Google-godkända tjänster, såvida du inte skapar en Google-klientapp som ska användas för autentisering med [Gmail-anslutningsappen.](/connectors/gmail/#authentication-and-bring-your-own-application) Mer information finns i [Datasäkerhets- och sekretesspolicyer för Google-anslutningsappar i Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Om logikappen behöver kommunicera via en brandvägg som begränsar trafiken till specifika  IP-adresser måste brandväggen tillåta åtkomst för både [inkommande](logic-apps-limits-and-config.md#inbound) och utgående [IP-adresser](logic-apps-limits-and-config.md#outbound) som används av Logic Apps-tjänsten eller körningen i Azure-regionen där logikappen finns. Om logikappen även använder hanterade anslutningsappar, till exempel Office 365 Outlook-anslutningsappen eller SQL-anslutningsappen, eller använder anpassade anslutningsappar, måste brandväggen också tillåta åtkomst för alla utgående [IP-adresser](logic-apps-limits-and-config.md#outbound) för hanterade anslutningsappar i logikappens Azure-region. [](../connectors/managed.md) [](/connectors/custom-connectors/) 

<a name="create-logic-app"></a>

## <a name="create-your-logic-app"></a>Skapa en logikapp

1. Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto.

1. I sökrutan Azure Portal du `logic apps` och väljer **Logic Apps**.

   ![Skärmbild av Azure Portal som visar sökrutan med "logic apps" som sökterm och "Logic Apps" som det valda sökresultatet.](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. På sidan **Logic Apps** väljer du Lägg **till**.

   ![Skärmbild av Logic Apps-tjänstsidan i Azure Portal, som visar listan över logikappar och den valda knappen "Lägg till".](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. I fönstret **Logikapp** anger du grundläggande information och inställningar för logikappen. Skapa en ny [resursgrupp](../azure-resource-manager/management/overview.md#terminology) för den här exempellogikappen.

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | <*logic-app-name*> | Logikappens namn, som måste vara unikt i olika regioner. Namnet får bara innehålla bokstäver, siffror, bindestreck ( `-` ), understreck ( `_` ), parenteser ( `(` , ), och punkter ( `)` `.` ). I det här exemplet används "My-First-Logic-App". |
   | **Prenumeration** | <*Azure-prenumerationens namn*> | Namnet på din Azure-prenumeration. |
   | **Resursgrupp** | <*Azure-resource-group-name*> | Namnet på den [Azure-resursgrupp](../azure-resource-manager/management/overview.md#terminology) där du skapar logikappen. Resursgruppens namn måste vara unikt i olika regioner. I det här exemplet används "My-First-LA-RG". |
   | **Plats** | <*Azure-region*> | Den Azure-region där logikappens information ska lagras. I det här exemplet används "USA, västra". |
   | **Log Analytics** | Av | Inställningen för diagnostisk loggning, som är **Av** som standard. I det här exemplet behåller du **inställningen Av.** |
   ||||

   ![Skärmbild av Logic Apps som visar fönstret med information om den nya logikappen.](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

1. När du är klar väljer du **Granska + skapa.** Bekräfta informationen som du angav och välj **Skapa.**

1. När Azure har distribuerat din app väljer du **Gå till resurs**. Du kan också hitta och välja din logikapp genom att skriva namnet i sökrutan.

   ![Skärmbild av sidan för resursdistribution med den valda knappen "Gå till resurs".](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Logic Apps Designer öppnas och det visas en sida med en introduktionsvideo och vanliga utlösare. Under **Mallar** väljer du **Tom logikapp**.

   ![Skärmbild av Logic Apps Designer med mallgalleriet och den valda mallen Tom logikapp.](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Lägg sedan [till en utlösare i logikappen](#add-rss-trigger).

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Lägga till RSS-utlösaren

Varje logikapp måste börja med [en utlösare](../logic-apps/logic-apps-overview.md#how-do-logic-apps-work), som utlöses när en viss händelse inträffar eller när ett visst villkor uppfylls. Varje gång utlösaren hittar nya objekt utlöses den och Logic Apps skapar en logikappinstans som startar och kör arbetsflödet. Om utlösaren inte hittar nya objekt utlöses inte utlösaren och skapar inte någon instans eller kör inte arbetsflödet vid den här kontrollen.

I det här snabbstartsexempel som visas när du har skapat en [logikapp](#create-your-logic-app)lägger du till en utlösare som söker efter nya objekt i ett RSS-flöde och utlöses när det finns nya objekt. Du kan också skapa logikappar med olika typer av utlösare, till exempel i självstudien för att [skapa automatiska godkännandebaserade arbetsflöden.](tutorial-process-mailing-list-subscriptions-workflow.md)

1. I **Logikappdesignern** går du till sökrutan och väljer **Alla.**

1. Du hittar RSS-anslutningsappen genom att ange i `rss` sökrutan. I listan **Utlösare** väljer du RSS-utlösaren när **ett flödesobjekt publiceras.**

   ![Skärmbild som Logic Apps Designer med "rss" i sökrutan och den valda RSS-utlösaren "När ett flödesobjekt publiceras".](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Ange RSS-feed-URL:en för utlösaren. Definiera sedan utlösarschemat genom att ange intervall och frekvens.

   | Egenskap | Värde | Beskrivning |
   | -------- | ----- | ----------- |
   | **RSS-feedens webbadress** | <*RSS-feed-URL*> | URL:en för rss-flödet som du vill övervaka. I det här exemplet används RSS-flödet för Wall Street Journal på `https://feeds.a.dj.com/rss/RSSMarketsMain.xml` . Men i det här exemplet kan du använda alla RSS-feedar som inte kräver HTTP-auktorisering. Välj ett RSS-flöde som publiceras ofta, så att du enkelt kan testa logikappen senare. |
   | **Intervall** | 1 | Antalet intervall som ska vänta mellan RSS-flödeskontroller. I det här exemplet används 1 minuts intervall. |
   | **Frekvens** | Minut | Tidsenhet för varje intervall mellan RSS-flödeskontroller. I det här exemplet används 1 minuts intervall. |
   ||||

   ![Skärmbild som Logic Apps Designer med RSS-utlösarinställningar, inklusive RSS-URL, frekvens och intervall.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

1. Dölj utlösarinformationen för tillfället genom att klicka i namnlisten.

   ![Skärmbild som Logic Apps designer med komprimerad logikappsform.](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Spara logikappen genom att **välja Spara** i designerverktygsfältet.

Logikappen är nu live, men den gör inget annat än att kontrollera RSS-flödet. Lägg sedan [till en åtgärd för](#add-email-action) att definiera vad som händer när utlösaren utlöses.

<a name="add-email-action"></a>

## <a name="add-the-send-email-action"></a>Lägg till åtgärden "skicka e-postmeddelande"

När du [har lagt till en](#add-rss-trigger)utlösare [](../logic-apps/logic-apps-overview.md#logic-app-concepts) för logikappen måste du sedan lägga till en åtgärd för att fastställa svaret när logikappen kontrollerar RSS-flödet och ett nytt objekt visas. Du kan också skapa logikappar med mycket mer komplexa åtgärder, till exempel i självstudien för bearbetning av [e-postmeddelanden med Logic Apps, Azure Functions och Azure Storage](./tutorial-process-email-attachments-workflow.md).

> [!NOTE]
> I det här exemplet används Office 365 Outlook som e-posttjänst. Om du använder en annan e-posttjänst som stöds i logikappen kan användargränssnittet se annorlunda ut. De grundläggande begreppen för att ansluta till en annan e-posttjänst förblir dock desamma.

1. Under **utlösaren När ett flödesobjekt publiceras** väljer du **Nytt steg.**

   ![Skärmbild av Logic Apps Designer som visar ett arbetsflöde med den valda knappen "Nytt steg".](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. Under **Välj en åtgärd** och sökrutan väljer du **Alla.**

1. I sökrutan anger du så `send an email` att du kan hitta anslutningsappar som erbjuder den här åtgärden. Om du vill filtrera åtgärdslistan till en specifik app eller tjänst kan du välja den appen eller tjänsten först.

   Om du till exempel använder ett arbets- eller skolkonto från Microsoft och vill använda Office 365 Outlook väljer du **Office 365 Outlook.** Om du använder ett personligt Microsoft-konto du välja Outlook.com. Det här exemplet fortsätter med Office 365 Outlook:

   ![Skärmbild av Logic Apps Designer som visar åtgärdssteget med den valda e-postanslutningsappen, "Office 365 Outlook".](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   Nu kan du enkelt hitta och välja den åtgärd som du vill använda, till exempel `send an email` :

   ![Skärmbild av Logic Apps Designer med en lista över filtrerade åtgärder för e-postanslutningsappen, "Office 365 Outlook".](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. Om det valda e-postanslutningsappen uppmanar dig att autentisera din identitet slutför du det steget nu. Du måste skapa en anslutning mellan logikappen och e-posttjänsten för att det här exemplet ska fungera. 

    > [!NOTE]
    > Det här exemplet visar manuell autentisering för Office 365 Outlook-anslutningsappen. Andra anslutningsappar kan dock ha stöd för olika autentiseringstyper.
    > Du kan också hantera autentisering för dina logikappar på olika sätt, beroende på ditt användningsfall. Om du till exempel använder Azure Resource Manager för distribution kan du parameterisera för att förbättra säkerheten för indata som ändras ofta, till exempel anslutningsinformation. Mer information finns i de här ämnena:
   > * [Mallparametrar för distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [Auktorisera OAuth-anslutningar](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Autentisera åtkomst med hanterade identiteter](../logic-apps/create-managed-service-identity.md)
   > * [Autentisera anslutningar för distribution av logikapp](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. I åtgärden **Skicka ett e-postmeddelande** anger du vilken information som ska ingå i e-postmeddelandet.

   1. Ange mottagarens e-postadress i fältet **Till**. I det här exemplet använder du din e-postadress.

        > [!NOTE]
        > Listan **Lägg till dynamiskt** innehåll visas när du klickar i rutan **Till** och vissa andra inmatningsrutor i Logic Apps Designer. I det här exemplet används dynamiskt innehåll i ett senare steg. I **listan Lägg till** dynamiskt innehåll visas alla tillgängliga utdata från föregående steg, som du kan använda som indata för den aktuella åtgärden.

   1. I rutan **Ämne** anger du ämne för din e-postavisering. I det här exemplet anger du följande text med ett avslutande blanksteg: `New RSS item: `

      ![Skärmbild av Logic Apps Designer med åtgärden "Skicka ett e-postmeddelande" och markören i egenskapsrutan Ämne.](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. I listan **Lägg till dynamiskt innehåll** väljer du **Flödesrubrik**, som är utdata från utlösaren, **När ett flödesobjekt publiceras.** Din e-postavisering använder dessa utdata för att hämta rubriken för RSS-objektet.

      ![Skärmbild av Logic Apps Designer som visar åtgärden "Skicka ett e-postmeddelande" och markören i egenskapsrutan "Ämne" med en öppen lista med dynamiskt innehåll och valda utdata, "Flödesrubrik".](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > Om inga utdata visas från utlösaren När ett **flödesobjekt** publiceras i listan med dynamiskt innehåll väljer du Visa mer bredvid åtgärdens sidhuvud bredvid **åtgärdens sidhuvud.**
      > 
      > ![Skärmbild av Logic Apps Designer med en öppen lista med dynamiskt innehåll och "Visa mer" valt för utlösaren.](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      När du är klar ser ämnet ut som i det här exemplet:

      ![Skärmbild av Logic Apps Designer, som visar åtgärden "Skicka ett e-postmeddelande" och ett exempel på ett e-postmeddelande med egenskapen "Flödesrubrik".](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      Om en "For each"-loop visas i designern har du valt en token för en matris. till exempel token **categories-Item.** För sådana typer av token lägger designern automatiskt till den här loopen omkring åtgärden som refererar till token. På så sätt kan din logikappsåtgärd utföra samma åtgärd på varje element i matrisen. Om du vill ta bort loopen väljer **du ellipserna** (**...**) i loopens namnlist och väljer sedan Ta **bort**.

   1. I rutan **Brödtext** anger du innehåll för e-postmeddelandet. I det här exemplet innehåller innehållet tre egenskaper med beskrivande text för var och en: , egenskapen för flödesrubriken, , feeden som publicerats på egenskapen `Title:` och ,  `Date published:`  `Link:` **länkegenskapen för primärt flöde.** Tryck på Skift + Enter för att lägga till tomma rader i en redigeringsruta.

      | Egenskap | Beskrivning |
      |----------|-------------|
      | **Flödesrubrik** | Objektets rubrik |
      | **Flödet publicerat den** | Objektets publiceringsdatum och -tid |
      | **Flödets primära länk** | Objektets webbadress |
      |||

      ![Skärmbild av Logic Apps Designer med åtgärden "Skicka ett e-postmeddelande" och valda egenskaper i rutan "Brödtext".](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

1. Spara logikappen. Välj **Spara** på designermenyn.

Testa sedan [att logikappen fungerar](#test-logic-app).

<a name="test-logic-app"></a>

## <a name="run-your-logic-app"></a>Kör logikappen

När du har skapat exempellogikappen bekräftar du att arbetsflödet har konfigurerats korrekt. Du kan vänta tills logikappen kontrollerar RSS-flödet baserat på det angivna schemat. Du kan också köra logikappen manuellt genom att välja **Kör** i Logic Apps Designer-verktygsfältet, som du ser i följande skärmbild. 

Om RSS-flödet innehåller nya objekt skickar din logikapp ett e-postmeddelande för varje nytt objekt. Annars väntar logikappen till nästa intervall för att kontrollera RSS-flödet igen. 

![Skärmbild av Logic Apps Designer med knappen "Kör" markerad i designerverktygsfältet.](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

Följande skärmbild visar ett exempel på ett e-postmeddelande från den här exempellogikappen. E-postmeddelandet innehåller information om varje RSS-flödesobjekt som valts i designern, samt den beskrivande texten som lagts till för var och en.

![Skärmbild av Outlook som visar ett exempel på ett e-postmeddelande som tas emot när ett nytt RSS-flödesobjekt visas med objektrubrik, publicerat datum och länk.](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Om du inte får e-postmeddelanden från logikappen som förväntat:

* Kontrollera skräppostmappen för ditt e-postkonto om meddelandet har filtrerats felaktigt.
* Kontrollera att RSS-flödet som du använder har publicerat objekt sedan den senaste schemalagda eller manuella kontrollen.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med att testa den här exempellogikappen rensar du logikappen och relaterade resurser genom att ta bort resursgruppen som du skapade för det här exemplet.

> [!NOTE]
> När du [tar bort en logikapp](manage-logic-apps-with-azure-portal.md#delete-logic-apps)instansieras inga nya körningar. Alla pågående och väntande körningar avbryts. Om du har flera tusen körningar kan det ta relativt lång tid att avbryta dem.

1. I Azure-sökrutan anger `resource groups` du och väljer sedan **Resursgrupper.**

   ![Skärmbild som Azure Portal en sökruta med söktermen "resursgrupper".](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. Leta upp och välj logikappens resursgrupp. I fönstret **Översikt** väljer du Ta **bort resursgrupp.**

   ![Skärmbild som Azure Portal med vald resursgrupp och knapp för "Ta bort resursgrupp".](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. När bekräftelsefönstret visas anger du resursgruppens namn och väljer Ta **bort.**

   ![Skärmbild som Azure Portal med bekräftelsefönstret och det resursgruppsnamn som ska tas bort.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du din första logikapp i Azure Portal för att söka i ett RSS-flöde efter uppdateringar enligt ett schema och skicka ett e-postmeddelande om varje nytt flödesobjekt. 

Information om hur du skapar mer avancerade schemabaserade arbetsflöden i Logic Apps finns i följande självstudie:

> [!div class="nextstepaction"]
> [Kontrollera trafik med en schemabaserad logikapp](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)