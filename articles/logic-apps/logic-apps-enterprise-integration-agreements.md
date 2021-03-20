---
title: Handels partner avtal
description: Skapa och hantera avtal mellan handels partner med hjälp av Azure Logic Apps och Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: c8cbfb619c9eed325161503f705bf5c4c0746265
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95992935"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Skapa och hantera handelspartneravtal i Azure Logic Apps

Ett [handels partner](../logic-apps/logic-apps-enterprise-integration-partners.md)  
 *avtal* hjälper organisationer och företag att kommunicera sömlöst med varandra genom att definiera det specifika bransch standard protokoll som ska användas för att utbyta B2B-meddelanden (Business-to-Business). Avtal ger vanliga fördelar, till exempel:

* Gör det möjligt för organisationer att utbyta information med hjälp av välkända format.
* Förbättra effektiviteten vid genomförande av B2B-transaktioner.
* Är enkelt att skapa, hantera och använda för att skapa lösningar för företags integrering.

Den här artikeln visar hur du skapar ett AS2-, EDIFACT-eller X12-avtal som du kan använda när du skapar lösningar för företags integrering för B2B-scenarier med hjälp av [Enterprise-integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md) och [Azure Logic Apps](../logic-apps/logic-apps-overview.md). När du har skapat ett avtal kan du använda AS2-, EDIFACT-eller X12-anslutningarna för att utväxla B2B-meddelanden.

Information om hur du skapar avtal för utbyte av RosettaNet-meddelanden finns i [Exchange RosettaNet-meddelanden](../logic-apps/logic-apps-enterprise-integration-rosettanet.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration ännu kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) för att lagra ditt avtal och andra B2B-artefakter. Det här integrations kontot måste vara associerat med din Azure-prenumeration.

* Minst två [handels partner](../logic-apps/logic-apps-enterprise-integration-partners.md) som du redan har skapat i ditt integrations konto. Ett avtal kräver både en värd partner och en gäst partner. Båda partnerna måste använda samma "affärs identitet"-kvalificerare som det avtal som du vill skapa, till exempel AS2, X12 eller EDIFACT.

* Valfritt: den Logic-app där du vill använda ditt avtal och en utlösare som startar din Logic app-arbetsflöde. Om du bara vill skapa ett integrations konto och B2B-artefakter behöver du inte någon logisk app. Men innan din Logic-app kan använda B2B-artefakter i ditt integrations konto måste du länka ditt integrations konto till din Logic app. Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Skapa avtal

1. Logga in på [Azure-portalen](https://portal.azure.com).
På huvud menyn i Azure väljer du **alla tjänster**. I rutan Sök anger du "integration" som filter. Välj den här resursen i resultatet: **integrations konton**

   ![Hitta ditt integrations konto](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. Under **integrations konton** väljer du det integrations konto där du vill skapa avtalet.

   ![Välj det integrations konto där du vill skapa avtalet](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. I den högra rutan under **komponenter** väljer du **avtals** panelen.

   ![Välj "avtal"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. Välj **Lägg till** under **avtal**. I fönstret **Lägg till** anger du information om ditt avtal, till exempel:

   ![Välj "Lägg till"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Namn** | Ja | <*avtals namn*> | Namnet på ditt avtal |
   | **Avtals typ** | Ja | **AS2**, **X12** eller **EDIFACT** | Protokoll typ för ditt avtal. När du skapar en avtals fil måste innehållet i den filen matcha avtals typen. | |  
   | **Värd partner** | Ja | <*värd partner-namn*> | Värd partnern representerar den organisation som specificerar avtalet |
   | **Värd identitet** | Ja | <*värd partner-ID*> | Värd partnerns identifierare |
   | **Gäst partner** | Ja | <*gäst-partner-Name*> | Gäst partnern representerar den organisation som gör affärer med värd partnern |
   | **Gäst identitet** | Ja | <*gäst-partner-ID*> | Gäst partnerns identifierare |
   | **Ta emot inställningar** | Det varierar | Det varierar | Dessa egenskaper anger hur värd partner tar emot alla inkommande meddelanden från gäst partnern i avtalet. Mer information finns i respektive avtals typ: <p>- [AS2 meddelande inställningar](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT meddelande inställningar](logic-apps-enterprise-integration-edifact.md) <br>- [X12 meddelande inställningar](logic-apps-enterprise-integration-x12.md) |
   | **Skicka inställningar** | Det varierar | Det varierar | De här egenskaperna anger hur värd partner skickar alla utgående meddelanden till gäst partnern i avtalet. Mer information finns i respektive avtals typ: <p>- [AS2 meddelande inställningar](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT meddelande inställningar](logic-apps-enterprise-integration-edifact.md) <br>- [X12 meddelande inställningar](logic-apps-enterprise-integration-x12.md) |
   |||||

   > [!IMPORTANT]
   > Upplösningen för ett avtal beror på matchning av de objekt som har definierats i partnern och inkommande meddelande:
   >
   > * Avsändarens kvalificerare och identifierare
   > * Mottagarens kvalificerare och identifierare
   >
   > Om dessa värden ändras för din partner, se till att du uppdaterar avtalet.

1. När du är klar med att skapa ditt avtal går du **till sidan Lägg till** och väljer **OK** och återgår till ditt integrations konto.

   **Avtals** listan visar nu ditt nya avtal.

## <a name="edit-agreements"></a>Redigera avtal

1. I [Azure Portal](https://portal.azure.com)på huvud menyn i Azure väljer du **alla tjänster**.

1. I rutan Sök anger du "integration" som filter. Välj den här resursen i resultatet: **integrations konton**

1. Under **integrations konton** väljer du det integrations konto som har det avtal som du vill redigera.

1. I den högra rutan under **komponenter** väljer du **avtals** panelen.

1. Under **avtal** väljer du ditt avtal och väljer **Redigera**.

1. Gör och spara ändringarna.

## <a name="delete-agreements"></a>Ta bort avtal

1. I [Azure Portal](https://portal.azure.com)på huvud menyn i Azure väljer du **alla tjänster**.

1. I rutan Sök anger du "integration" som filter. Välj den här resursen i resultatet: **integrations konton**

1. Under **integrations konton** väljer du det integrations konto som har det avtal som du vill ta bort.

1. I den högra rutan under **komponenter** väljer du **avtals** panelen.

1. Under **avtal** väljer du ditt avtal och väljer **ta bort**.

1. Bekräfta att du vill ta bort det valda avtalet.

## <a name="next-steps"></a>Nästa steg

* [Utbyta AS2-meddelanden](logic-apps-enterprise-integration-as2.md)
* [Utbyta EDIFACT-meddelanden](logic-apps-enterprise-integration-edifact.md)
* [Utbyta X12-meddelanden](logic-apps-enterprise-integration-x12.md)
