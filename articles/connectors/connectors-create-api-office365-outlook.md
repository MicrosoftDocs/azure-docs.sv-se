---
title: Integrera med Office 365 Outlook
description: Automatisera uppgifter och arbets flöden som hanterar e-post, kontakter och kalendrar i Office 365 Outlook med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/13/2020
tags: connectors
ms.openlocfilehash: 87a9c8a38ede7008d658068329c8c72e7e3c4117
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095465"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Hantera e-post, kontakter och kalendrar i Office 365 Outlook med hjälp av Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Office 365 Outlook Connector](/connectors/office365connector/)kan du skapa automatiserade uppgifter och arbets flöden som hanterar ditt arbets-eller skol konto genom att skapa Logi Kap par. Du kan till exempel automatisera dessa uppgifter:

* Hämta, skicka och svara på e-post.
* Schemalägg möten i din kalender.
* Lägg till och redigera kontakter.

Du kan använda en utlösare för att starta arbets flödet, till exempel när ett nytt e-postmeddelande tas emot, när ett Kalender objekt uppdateras eller när en händelse inträffar i en skillnads tjänst, till exempel Salesforce. Du kan använda åtgärder som svarar på utlösnings händelsen, till exempel skicka ett e-postmeddelande eller skapa en ny kalender händelse.

## <a name="prerequisites"></a>Förutsättningar

* Ett Outlook-konto där du loggar in med ett [arbets-eller skol konto](https://www.office.com/). Om du har ett @outlook.com @hotmail.com -eller-konto kan du använda [Outlook.com-anslutaren](../connectors/connectors-create-api-outlook.md) i stället. Om du vill ansluta till Outlook med ett annat användar konto, till exempel ett tjänst konto, se [Anslut med andra konton](#connect-using-other-accounts).

* Ett Azure-konto och prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Den Logic app där du vill komma åt ditt Outlook-konto. För att starta arbets flödet med en Office 365 Outlook-utlösare måste du ha en [Tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). För att lägga till en Office 365 Outlook-åtgärd i ditt arbets flöde måste din Logic app redan ha en utlösare.

## <a name="add-a-trigger"></a>Lägga till en utlösare

En [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) är en händelse som startar arbets flödet i din Logic app. I den här exempel Logic-appen används en "avsöknings utlösare" som söker efter uppdaterade Kalender händelser i ditt e-postkonto baserat på angivet intervall och frekvens.

1. I [Azure Portal](https://portal.azure.com)öppnar du din tomma Logic-app i Logic App Designer.

1. I rutan Sök anger `office 365 outlook` du som filter. Det här exemplet väljer **när en kommande händelse börjar snart**.
   
   ![Välj utlösare för att starta din Logic app](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Om du inte har en aktiv anslutning till ditt Outlook-konto uppmanas du att logga in och skapa anslutningen. Om du vill ansluta till Outlook med ett annat användar konto, till exempel ett tjänst konto, se [Anslut med andra konton](#connect-using-other-accounts). Annars anger du informationen för utlösarens egenskaper.

   > [!NOTE]
   > Din anslutning upphör inte förrän den har återkallats, även om du ändrar dina inloggnings uppgifter. Mer information finns i [konfigurations bara livs längder för token i Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   I det här exemplet väljs den kalender som utlösaren kontrollerar, till exempel:

   ![Konfigurera utlösarens egenskaper](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. Ange **frekvens** -och **intervall** värden i utlösaren. Om du vill lägga till andra tillgängliga Utlösar egenskaper, t. ex. **tidszon**, väljer du dessa egenskaper i listan **Lägg till ny parameter** .

   Om du till exempel vill att utlösaren ska kontrol lera kalendern var 15: e minut, ange **frekvens** till **minut** och ange **intervall** till `15` . 

   ![Ange frekvens och intervall för utlösaren](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. I verktygsfältet designer väljer du **Spara**.

Lägg nu till en åtgärd som körs när utlösaren utlöses. Du kan till exempel lägga till åtgärden Twilio **Skicka meddelande** , som skickar en text när en kalender händelse startar om 15 minuter.

## <a name="add-an-action"></a>Lägga till en åtgärd

En [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är en åtgärd som körs av arbets flödet i din Logic app. Den här exempel på Logic-appen skapar en ny kontakt i Office 365 Outlook. Du kan använda utdata från en annan utlösare eller åtgärd för att skapa kontakten. Anta till exempel att din Logic app använder Dynamics 365-utlösaren **när en post skapas**. Du kan lägga till Office 365 Outlook-åtgärden för att **skapa kontakter** och använda utdata från Salesforce-utlösaren för att skapa den nya kontakten.

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

1. Om du vill lägga till en åtgärd som sista steg i arbets flödet väljer du **nytt steg**. 

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. I rutan Sök anger `office 365 outlook` du som filter. I det här exemplet väljer du **skapa kontakt**.

   ![Välj den åtgärd som ska köras i din Logic app](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Om du inte har en aktiv anslutning till ditt Outlook-konto uppmanas du att logga in och skapa anslutningen. Om du vill ansluta till Outlook med ett annat användar konto, till exempel ett tjänst konto, se [Anslut med andra konton](#connect-using-other-accounts). Annars anger du informationen för åtgärdens egenskaper.

   > [!NOTE]
   > Din anslutning upphör inte förrän den har återkallats, även om du ändrar dina inloggnings uppgifter. Mer information finns i [konfigurations bara livs längder för token i Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   Det här exemplet väljer mappen kontakter där åtgärden skapar den nya kontakten, till exempel:

   ![Konfigurera åtgärdens egenskaper](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Om du vill lägga till andra tillgängliga åtgärds egenskaper väljer du dessa egenskaper i listan **Lägg till ny parameter** .

1. I verktygsfältet designer väljer du **Spara**.

<a name="connect-using-other-accounts"></a>

## <a name="connect-using-other-accounts"></a>Anslut med andra konton

Om du försöker ansluta till Outlook genom att använda ett annat konto än det som för närvarande är inloggat på Azure kan du få [enkel inloggning (SSO)](../active-directory/manage-apps/what-is-single-sign-on.md) -fel. Det här problemet uppstår när du loggar in på Azure Portal med ett konto, men använder ett annat konto för att skapa anslutningen. Designern förväntar dig att du använder kontot som är inloggat på Azure Portal. För att lösa det här problemet har du följande alternativ:

* Konfigurera det andra kontot med **deltagar** rollen i din Logic Apps resurs grupp.

  1. Välj **åtkomst kontroll (IAM)** på din Logic Apps resurs grupps meny. Konfigurera det andra kontot med **deltagar** rollen. 
  
     Mer information finns i [tilldela Azure-roller med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md).

  1. När du har konfigurerat den här rollen loggar du in på Azure Portal med kontot som har deltagar behörighet. Nu kan du använda det här kontot för att skapa anslutningen till Outlook.

* Konfigurera det andra kontot så att ditt arbets-eller skol konto har behörigheten "Skicka som".

   Om du har administratörs behörighet för tjänst kontots post låda konfigurerar du ditt arbets-eller skol konto med antingen **Skicka som** eller **Skicka åt** behörigheter. Mer information finns i [ge Mailbox-behörigheter till en annan användar administratörs hjälp](/microsoft-365/admin/add-users/give-mailbox-permissions-to-another-user). Du kan sedan skapa anslutningen med hjälp av ditt arbets-eller skol konto. Du kan nu använda tjänst kontots e-postadress i utlösare eller åtgärder där du kan ange avsändaren.

   Till exempel har åtgärden **Skicka ett e-postmeddelande** en valfri parameter **från (skicka som)** som du kan lägga till i åtgärden och använda tjänst kontots e-postadress som avsändare. Följ dessa steg om du vill lägga till den här parametern:

   1. I åtgärden **Skicka ett e-postmeddelande** öppnar du listan **Lägg till en parameter** och väljer parametern **från (Send As)** .

   1. När parametern visas i åtgärden anger du tjänst kontots e-postadress.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om den här anslutningen, till exempel utlösare, åtgärder och gränser, som beskrivs av kopplingens Swagger-fil, finns på [kopplingens referens sida](/connectors/office365/). 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
