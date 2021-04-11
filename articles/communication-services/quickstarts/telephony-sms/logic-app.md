---
title: Snabb start – skicka SMS-meddelanden i Azure Logic Apps med Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: I den här snabb starten får du lära dig hur du skickar SMS-meddelanden i Azure Logic Apps arbets flöden med hjälp av Azure Communication Services-anslutaren.
author: tophpalmer
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 11cce223a0802c20c5fc144eed681327f989a949
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103488388"
---
# <a name="quickstart-send-sms-messages-in-azure-logic-apps-with-azure-communication-services"></a>Snabb start: skicka SMS-meddelanden i Azure Logic Apps med Azure Communication Services

Genom att använda SMS-anslutaren för [Azure Communication Services](../../overview.md) och [Azure Logic Apps](../../../logic-apps/logic-apps-overview.md)kan du skapa automatiserade arbets flöden eller *Logic Apps* som kan skicka SMS-meddelanden. Den här snabb starten visar hur du automatiskt skickar SMS som svar på en Utlös ande händelse, vilket är det första steget i ett Logic app-arbetsflöde. En Utlös ande händelse kan vara ett inkommande e-postmeddelande, ett upprepnings schema, ett [Azure Event Grid](../../../event-grid/overview.md) resurs händelse eller andra [utlösare som stöds av Azure Logic Apps](/connectors/connector-reference/connector-reference-logicapps-connectors).

:::image type="content" source="./media/logic-app/azure-communication-services-connector.png" alt-text="Skärm bild som visar Azure Portal, som är öppen för Logic App Designer, och innehåller en exempel på en Logic-app som använder åtgärden skicka SMS för Azure Communication Services-anslutaren.":::

Även om den här snabb starten fokuserar på att använda anslutningen för att svara på en utlösare, kan du också använda anslutningen för att svara på andra åtgärder, vilket är de steg som följer utlösaren i ett arbets flöde. Om du är nybörjare på Logic Apps bör du läsa igenom [vad som är Azure Logic Apps](../../../logic-apps/logic-apps-overview.md) innan du börjar.

> [!NOTE]
> Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration eller [skapa ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- En aktiv Azure Communication Services-resurs eller [skapa en kommunikations tjänst resurs](../create-communication-resource.md).

- En aktiv Logic Apps resurs (Logic app) eller [skapa en tom Logic-app, men med den utlösare som du vill använda](../../../logic-apps/quickstart-create-first-logic-app-workflow.md). Azure Communication Services SMS-anslutaren tillhandahåller för närvarande endast åtgärder, så din Logi Kap par kräver en utlösare som är minst.

  Den här snabb starten använder **när ett nytt e-postmeddelande anländer** , vilket är tillgängligt med [Office 365 Outlook Connector](/connectors/office365/).

- Ett SMS-aktiverat telefonnummer eller [Skaffa ett telefonnummer](./get-phone-number.md).

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

## <a name="add-an-sms-action"></a>Lägg till en SMS-åtgärd

Om du vill lägga till åtgärden **skicka SMS** som ett nytt steg i arbets flödet med hjälp av SMS-anslutaren för Azure Communication Services, följer du dessa steg i [Azure Portal](https://portal.azure.com) med ditt Logic app-arbetsflöde öppet i Logic App Designer:

1. I designern, under steget där du vill lägga till den nya åtgärden, väljer du **nytt steg**. Du kan också lägga till den nya åtgärden mellan stegen genom att flytta pekaren över pilen mellan dessa steg, välja plus tecknet ( **+** ) och välja **Lägg till en åtgärd**.

1. I sökrutan **Välj en åtgärd** anger du `Azure Communication Services` . Välj **skicka SMS** i listan åtgärder.

   :::image type="content" source="./media/logic-app/select-send-sms-action.png" alt-text="Skärm bild som visar Logic App Designer och Azure Communication Services-anslutningen med åtgärden skicka SMS vald.":::

1. Skapa nu en anslutning till kommunikations tjänst resursen.

   1. Ange ett namn för anslutningen.

   1. Välj din Azure Communication Services-resurs.

   1. Välj **Skapa**.

   :::image type="content" source="./media/logic-app/send-sms-configuration.png" alt-text="Skärm bild som visar konfigurationen skicka SMS-åtgärd med exempel information.":::

1. Ange följande information i åtgärden **skicka SMS** : 

   * Käll-och mål telefonnummer. I test syfte kan du använda ditt eget telefonnummer som mål telefonnummer.

   * Det meddelande innehåll som du vill skicka, till exempel "Hej från Logic Apps!".

   Här är en **skicka SMS** -åtgärd med exempel information:

   :::image type="content" source="./media/logic-app/send-sms-action.png" alt-text="Skärm bild som visar åtgärden skicka SMS med exempel information.":::

1. När du är klar väljer du **Spara** i verktygsfältet designer.

Kör sedan din Logic app för testning.

## <a name="test-your-logic-app"></a>Testa din Logic app

Om du vill starta din Logic app manuellt går du till verktygsfältet i designern och väljer **Kör**. Du kan också vänta tills din Logic-app utlöser. I båda fallen ska Logic-appen skicka ett SMS-meddelande till ditt angivna mål telefonnummer. Mer information om hur du kör din Logic app finns i [så här kör du din Logic app](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#run-your-logic-app)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort en kommunikations tjänst prenumeration genom att ta bort kommunikations tjänst resursen eller resurs gruppen. Om du tar bort resurs gruppen tas även andra resurser i gruppen bort. Mer information finns i avsnittet [så här rensar du kommunikations tjänsternas resurser](../create-communication-resource.md#clean-up-resources).

Om du vill rensa ditt Logic app-arbetsflöde och relaterade resurser kan du läsa [om hur du rensar Logic Apps-resurser](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skickar SMS-meddelanden med hjälp av Azure Logic Apps och Azure Communication Services. Om du vill veta mer kan du fortsätta med att prenumerera på SMS-händelser:

> [!div class="nextstepaction"]
> [Prenumerera på SMS-händelser](./handle-sms-events.md)

Mer information om SMS i Azure Communication Services finns i följande artiklar:

- [SMS-begrepp](../../concepts/telephony-sms/concepts.md)
- [Telefonnummer typer](../../concepts/telephony-sms/plan-solution.md)
- [SMS SDK](../../concepts/telephony-sms/sdk-features.md)
