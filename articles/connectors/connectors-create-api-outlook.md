---
title: Anslut till Outlook.com
description: Automatisera uppgifter och arbets flöden som hanterar e-post, kalendrar och kontakter i Outlook.com med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75707194"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>Hantera e-post, kalendrar och kontakter i Outlook.com med hjälp av Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) -och [Outlook.com-anslutningen](/connectors/outlook/)kan du skapa automatiserade uppgifter och arbets flöden som hanterar ditt @outlook.com eller- @hotmail.com konto genom att skapa Logic Apps. Du kan till exempel automatisera dessa uppgifter:

* Hämta, skicka och svara på e-post.
* Schemalägg möten i din kalender.
* Lägg till och redigera kontakter.

Du kan använda en utlösare för att starta arbets flödet, till exempel när ett nytt e-postmeddelande tas emot, när ett Kalender objekt uppdateras eller när en händelse inträffar i en skillnads tjänst. Du kan använda åtgärder som svarar på utlösnings händelsen, till exempel skicka ett e-postmeddelande eller skapa en ny kalender händelse.

> [!NOTE]
> Om du vill automatisera uppgifter för ett Microsoft Work-konto, till exempel @fabrikam.onmicrosoft.com , använder du [Office 365 Outlook Connector](../connectors/connectors-create-api-office365-outlook.md).

## <a name="prerequisites"></a>Förutsättningar

* Ett [Outlook.com-konto](https://outlook.live.com/owa/)

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Den Logic app där du vill komma åt ditt Outlook.com-konto. För att starta arbets flödet med en Outlook.com-utlösare måste du ha en [Tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill lägga till en Outlook.com-åtgärd i ditt arbets flöde måste din Logic app redan ha en utlösare.

## <a name="add-a-trigger"></a>Lägga till en utlösare

En [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) är en händelse som startar arbets flödet i din Logic app. I den här exempel Logic-appen används en "avsöknings utlösare" som söker efter nya e-postmeddelanden i ditt e-postkonto baserat på angivet intervall och frekvens.

1. I [Azure Portal](https://portal.azure.com)öppnar du din tomma Logic-app i Logic App Designer.

1. I rutan Sök anger du "outlook.com" som filter. I det här exemplet väljer du **när ett nytt e-postmeddelande kommer**.

1. Om du uppmanas att logga in anger du dina Outlook.com-autentiseringsuppgifter så att din Logic app kan ansluta till ditt konto. Annars, om anslutningen redan finns, anger du informationen för utlösarens egenskaper:

1. Ange **frekvens** -och **intervall** värden i utlösaren.

   Om du till exempel vill att utlösaren ska avsöka var 15: e minut ställer du in **frekvensen** på **minut** och anger **intervallet** till **15**.

1. I verktygsfältet designer väljer du **Spara**, som sparar din Logic app.

Lägg till en annan åtgärd för att svara på utlösaren. Du kan till exempel lägga till åtgärden Twilio **Skicka meddelande** , som skickar en text när ett e-postmeddelande tas emot.

## <a name="add-an-action"></a>Lägga till en åtgärd

En [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är en åtgärd som körs av arbets flödet i din Logic app. Den här exempel-Logic-appen skickar ett e-postmeddelande från ditt Outlook.com-konto. Du kan använda utdata från en annan utlösare för att fylla i åtgärden. Anta till exempel att din Logic app använder SalesForce **när ett objekt skapas** utlösare. Du kan lägga till Outlook.com **skicka en e-** poståtgärd och använda utdata från Salesforce-utlösaren i e-postmeddelandet.

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

1. Om du vill lägga till en åtgärd som sista steg i arbets flödet väljer du **nytt steg**. 

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. I rutan Sök anger du "outlook.com" som filter. I det här exemplet väljer du **Skicka ett e-postmeddelande**. 

1. Om du uppmanas att logga in anger du dina Outlook.com-autentiseringsuppgifter så att din Logic app kan ansluta till ditt konto. Annars, om anslutningen redan finns, anger du informationen för åtgärds egenskaperna.

1. I verktygsfältet designer väljer du **Spara**, som sparar din Logic app.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare, åtgärder och gränser, som beskrivs av kopplingens Swagger-fil, finns på [kopplingens referens sida](/connectors/outlook/). 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
