---
title: Anslut till slack från Azure Logic Apps
description: Automatisera aktiviteter och arbets flöden som övervakar filer och hantera kanaler, grupper och meddelanden i ditt slack-konto med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 75f4cea0e5cd2b0bf42c28cbd81e199115834e11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87283970"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Övervaka och hantera slack med Azure Logic Apps

Med Azure Logic Apps och slack-kopplingen kan du skapa automatiserade uppgifter och arbets flöden som övervakar dina slack-filer och hantera dina slack-kanaler, meddelanden, grupper osv. till exempel:

* Övervaka när nya filer skapas.
* Skapa, lista och ansluta kanaler 
* Publicera meddelanden.
* Skapa grupper och Ställ in stör ej.

Du kan använda utlösare som får svar från ditt slack-konto och göra utdata tillgängliga för andra åtgärder. Du kan använda åtgärder som utför uppgifter med ditt slack-konto. Du kan också använda andra åtgärder för att använda utdata från slack-åtgärder. Till exempel kan du skicka e-post med Office 365 Outlook Connector när en ny fil skapas. Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ditt [slack](https://slack.com/) -konto och användarautentiseringsuppgifter

  Dina autentiseringsuppgifter ger din Logic-app behörighet att skapa en anslutning och komma åt ditt slack-konto.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill komma åt ditt slack-konto. [Skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md)för att börja med en slack-utlösare. Om du vill använda en slack-åtgärd startar du din Logic-app med en utlösare, till exempel en slack-utlösare eller en annan utlösare, till exempel **upprepnings**

## <a name="connect-to-slack"></a>Anslut till slack

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. Skriv "slack" som filter i rutan Sök i tomma Logi Kap par. Välj den utlösare som du vill använda under listan utlösare. 

   \- eller -

   För befintliga Logic Apps, under det sista steget där du vill lägga till en åtgärd, väljer du **nytt steg**. 
   Skriv "slack" som filter i rutan Sök. 
   Under listan åtgärder väljer du den åtgärd som du vill använda.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. 
   Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Om du uppmanas att logga in på slack loggar du in på din slack-arbetsyta. 

   ![Logga in på slack-arbetsytan](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Ge åtkomst till din Logic app.

   ![Auktorisera åtkomst till slack](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Ange nödvändig information för den valda utlösaren eller åtgärden. Lägg till fler åtgärder om du vill fortsätta att skapa din Logic app-arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/slack/).

## <a name="get-support"></a>Få support

* För frågor, besök [sidan Microsoft Q&en fråga för Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)

