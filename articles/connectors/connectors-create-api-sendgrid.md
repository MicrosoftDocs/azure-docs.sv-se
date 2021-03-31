---
title: Anslut till SendGrid från Azure Logic Apps
description: Automatisera aktiviteter och arbets flöden som skickar e-post och hantera e-postlistor i SendGrid med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: a140ae0f27c61959d8ebc6854c5bcb2a916a0fc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87290438"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Skicka e-postmeddelanden och hantera e-postlistor i SendGrid med hjälp av Azure Logic Apps

Med Azure Logic Apps och SendGrid-anslutningen kan du skapa automatiserade uppgifter och arbets flöden som skickar e-post och hanterar dina mottagar listor, till exempel:

* Skicka e-post.
* Lägg till mottagare i listor.
* Hämta, Lägg till och hantera global under tryckning.

Du kan använda SendGrid åtgärder i dina Logi Kap par för att utföra dessa uppgifter. Du kan också använda de andra åtgärderna för att använda utdata från SendGrid-åtgärder. 

Den här kopplingen tillhandahåller endast åtgärder, så för att starta din Logi Kap par, använder du en separat utlösare, till exempel en **upprepnings** utlösare. Om du till exempel regelbundet lägger till mottagare i dina listor kan du skicka e-post om mottagare och listor med hjälp av Office 365 Outlook Connector eller Outlook.com Connector.
Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ett [SendGrid-konto](https://www.sendgrid.com/) och en [SendGrid API-nyckel](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Din API-nyckel godkänner din Logic app för att skapa en anslutning och komma åt ditt SendGrid-konto.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill komma åt ditt SendGrid-konto. Om du vill använda en SendGrid-åtgärd startar du din Logic-app med en annan utlösare, till exempel utlösaren **upprepning** .

## <a name="connect-to-sendgrid"></a>Ansluta till SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. Välj en sökväg: 

   * Under det sista steget där du vill lägga till en åtgärd väljer du **nytt steg**. 

     \- eller -

   * Mellan stegen där du vill lägga till en åtgärd flyttar du pekaren över pilen mellan stegen. 
   Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. I rutan Sök anger du "SendGrid" som filter. Under listan åtgärder väljer du den åtgärd som du vill använda.

1. Ange ett namn på anslutningen. 

1. Ange din SendGrid API-nyckel och välj sedan **skapa**.

1. Ange nödvändig information för den valda åtgärden och fortsätt att skapa din Logic Apps-arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/sendgrid/).

## <a name="get-support"></a>Få support

* För frågor, besök [sidan Microsoft Q&en fråga för Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
