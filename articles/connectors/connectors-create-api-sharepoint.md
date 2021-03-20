---
title: Ansluta till SharePoint från Azure Logic Apps
description: Automatisera aktiviteter och arbets flöden som övervakar och hanterar resurser i SharePoint Online eller SharePoint Server lokalt genom att använda Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: c72330792e508361830c1bf391f85eefe78bdd1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87283987"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Övervaka och hantera SharePoint-resurser med Azure Logic Apps

Med Azure Logic Apps och SharePoint Connector kan du skapa automatiserade uppgifter och arbets flöden som övervakar och hanterar resurser, till exempel filer, mappar, listor, objekt, personer och så vidare, i SharePoint Online eller i SharePoint Server lokalt, till exempel:

* Övervaka när filer eller objekt skapas, ändras eller tas bort.
* Skapa, Hämta, uppdatera eller ta bort objekt.
* Lägg till, hämta eller ta bort bifogade filer. Hämta innehållet från bilagor.
* Skapa, kopiera, uppdatera eller ta bort filer. 
* Uppdatera fil egenskaper. Hämta innehåll, metadata eller egenskaper för en fil.
* Visa eller extrahera mappar.
* Hämta listor eller listvyer.
* Ange status för innehålls godkännande.
* Lösa personer.
* Skicka HTTP-begäranden till SharePoint.
* Hämta enhets värden.

Du kan använda utlösare som får svar från SharePoint och göra utdata tillgängliga för andra åtgärder. Du kan använda åtgärder i Logi Kap par för att utföra uppgifter i SharePoint. Du kan också använda andra åtgärder för att använda utdata från SharePoint-åtgärder. Om du till exempel regelbundet hämtar filer från SharePoint kan du skicka meddelanden till ditt team med hjälp av slack-kopplingen.
Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Din SharePoint-webbplats och användarautentiseringsuppgifter

  Dina autentiseringsuppgifter ger din Logic-app behörighet att skapa en anslutning och komma åt ditt SharePoint-konto. 

* Innan du kan ansluta Logic Apps till lokala system som SharePoint Server måste du [Installera och konfigurera en lokal datagateway](../logic-apps/logic-apps-gateway-install.md). På så sätt kan du ange att din gateway-installation ska användas när du skapar SharePoint Server-anslutningen för din Logic app.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill komma åt ditt SharePoint-konto. Börja med en SharePoint-utlösare genom att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en SharePoint-åtgärd startar du din Logic app med en utlösare, till exempel en Salesforce-utlösare, om du har ett Salesforce-konto.

  Du kan till exempel starta din Logi Kap par med **när en post skapas** i Salesforce-utlösare. 
  Den här utlösaren utlöses varje gång en ny post, till exempel ett lead, skapas i Salesforce. 
  Du kan sedan följa den här utlösaren med åtgärden **Skapa fil** i SharePoint. På så sätt skapar din Logic app en fil i SharePoint med information om den nya posten när den nya posten skapas.

## <a name="connect-to-sharepoint"></a>Anslut till SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. För tomma Logi Kap par anger du "SharePoint" som filter i rutan Sök. Välj den utlösare som du vill använda under listan utlösare. 

   \- eller -

   För befintliga Logic Apps, under det sista steget där du vill lägga till en SharePoint-åtgärd väljer du **nytt steg**. 
   I rutan Sök anger du "SharePoint" som filter. 
   Under listan åtgärder väljer du den åtgärd som du vill använda.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. 
   Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. När du uppmanas att logga in anger du nödvändig anslutnings information. Om du använder SharePoint Server måste du kontrol lera att du har valt **Anslut via lokal datagateway**. När du är klar väljer du **Skapa**.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa din Logic Apps-arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/sharepoint/).

## <a name="get-support"></a>Få support

* För frågor, besök [sidan Microsoft Q&en fråga för Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)

