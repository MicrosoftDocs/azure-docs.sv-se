---
title: Anslut till Twilio från Azure Logic Apps
description: Automatisera aktiviteter och arbets flöden som hanterar globala SMS-, MMS-och IP-meddelanden via ditt Twilio-konto med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: d144960972f5a1b45e88cc3a0ea015925cae3b91
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87288152"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Hantera meddelanden i Twilio med Azure Logic Apps

Med Azure Logic Apps och Twilio-anslutningen kan du skapa automatiserade uppgifter och arbets flöden som hämtar, skickar och listar meddelanden i Twilio, som innehåller globala SMS-, MMS-och IP-meddelanden. Du kan använda dessa åtgärder för att utföra uppgifter med ditt Twilio-konto. Du kan också använda de andra åtgärderna för att använda utdata från Twilio-åtgärder. När ett nytt meddelande anländer kan du till exempel skicka meddelande innehållet med slack-kopplingen. Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Från [Twilio](https://www.twilio.com/): 

  * Ditt Twilio-konto-ID och [autentiseringstoken](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them), som du hittar på din Twilio-instrumentpanel

    Dina autentiseringsuppgifter ger din Logic-app behörighet att skapa en anslutning och komma åt ditt Twilio-konto från din Logic app. 
    Om du använder ett Twilio utvärderings konto kan du bara skicka SMS till *verifierade* telefonnummer.

  * Ett verifierat Twilio-telefonnummer som kan skicka SMS

  * Ett verifierat Twilio-telefonnummer som kan ta emot SMS

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill komma åt ditt Twilio-konto. Om du vill använda en Twilio-åtgärd startar du din Logic-app med en annan utlösare, till exempel utlösaren **upprepning** .

## <a name="connect-to-twilio"></a>Anslut till Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. Välj en sökväg: 

     * Under det sista steget där du vill lägga till en åtgärd väljer du **nytt steg**. 

       \- eller -

     * Mellan stegen där du vill lägga till en åtgärd flyttar du pekaren över pilen mellan stegen. 
     Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.
     
       I rutan Sök anger du "Twilio" som filter. 
       Under listan åtgärder väljer du den åtgärd som du vill använda.

1. Ange nödvändig information för anslutningen och välj sedan **skapa**:

   * Namnet som ska användas för anslutningen
   * Ditt Twilio-konto-ID 
   * Din Twilio Access-token (Authentication)

1. Ange nödvändig information för den valda åtgärden och fortsätt att skapa din Logic Apps-arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/twilio/).

## <a name="get-support"></a>Få support

* För frågor, besök [sidan Microsoft Q&en fråga för Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
