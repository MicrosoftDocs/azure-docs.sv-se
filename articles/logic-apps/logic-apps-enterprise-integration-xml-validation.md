---
title: Validera XML for B2B Enterprise-integration
description: Validera XML med hjälp av scheman i Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "74792158"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Verifiera XML för B2B-företagsintegrering i Azure Logic Apps med Enterprise-integrationspaket

I B2B-scenarier måste handels partner i ett avtal ofta se till att de meddelanden som de utbyter är giltiga innan data bearbetning kan starta. Du kan validera dokument mot ett fördefinierat schema med hjälp av XML-verifieringen, som är tillgänglig med Enterprise-integrationspaket.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration ännu kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* En tom eller befintlig Logic-app där du vill använda XML-verifieringen. Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som är associerat med din Azure-prenumeration är länkat till den Logic-app där du planerar att använda XML-valideringen och innehåller det schema som du vill använda för att validera XML-innehåll. Både din Logic app och ditt integrations konto måste finnas på samma plats eller i Azure-regionen.

## <a name="add-xml-validation-action"></a>Lägg till XML-validerings åtgärd

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

1. Om du har en tom Logic-app, i Logic App Designer, i sökrutan, anger du `HTTP request` som filter och väljer alternativet **när en http-begäran tas emot** . Annars fortsätter du till nästa steg.

1. Under det sista steget i arbets flödet väljer du **nytt steg**.

   Om du vill lägga till en åtgärd mellan befintliga steg flyttar du pekaren över pilen som kopplar stegen så att plus tecknet ( **+** ) visas. Välj plus tecknet och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd** väljer du **inbyggt**. I rutan Sök anger `xml validation` du som filter. I listan åtgärder väljer du **XML-validering**.

   ![Sök och Välj åtgärden "XML-verifiering"](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. Om du vill ange det XML-innehåll som du vill verifiera klickar du i **innehålls** rutan så att listan med dynamiskt innehåll visas.

   ![Öppna listan med dynamiskt innehåll](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   Listan med dynamiskt innehåll visar de egenskaps-token som representerar utdata från föregående steg i arbets flödet. Om listan inte visar en förväntad egenskap, kontrollerar du utlösaren eller åtgärds rubriken om du kan välja **Se mer**.

1. I listan med dynamiskt innehåll väljer du den egenskap som har det innehåll som du vill validera.

   I det här exemplet väljs **texten** utdata från utlösaren.

   ![Välj innehåll att validera](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. Om du vill ange det schema som du vill använda för verifiering öppnar du listan **schema namn** och väljer det verifierings schema som du har lagt till i det länkade integrations kontot.

   ![Välj schema som ska användas för verifiering](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. Spara logikappen.

   Nu är du klar med att konfigurera verifieringen. I en verklig värld kan du vilja lagra de verifierade data i en affärsrelaterad app (LOB), till exempel SalesForce. Lägg till en åtgärd för att skicka de verifierade utdata till Salesforce.

1. Du kan testa din verifierings åtgärd genom att skicka en begäran om att utlösa din Logic app-arbetsflöde.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Enterprise-integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md)