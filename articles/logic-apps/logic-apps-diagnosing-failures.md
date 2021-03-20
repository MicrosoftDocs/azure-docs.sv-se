---
title: Felsöka och diagnostisera fel i arbetsflödet
description: Lär dig hur du felsöker och diagnostiserar problem, fel och fel i dina arbets flöden i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995054"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Felsöka och diagnostisera fel med arbetsflöden i Azure Logic Apps

Din Logic app genererar information som kan hjälpa dig att diagnostisera och felsöka problem i din app. Du kan diagnostisera en Logic-app genom att granska varje steg i arbets flödet via Azure Portal. Du kan också lägga till några steg i ett arbets flöde för körnings fel sökning.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Kontrol lera utlösarens historik

Varje Logi Kap par körning börjar med ett Utlös ande försök, så om utlösaren inte utlöses följer du dessa steg:

1. Kontrol lera utlösarens status genom [att kontrol lera utlösarens historik](../logic-apps/monitor-logic-apps.md#review-trigger-history). Om du vill visa mer information om Utlösar-försöket väljer du den Utlös ande händelsen, till exempel:

   ![Visa utlösarens status och historik](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Kontrol lera utlösarens indata för att bekräfta att de visas som förväntat. Under **indata-länk** väljer du länken som visar fönstret **indata** .

   Trigger Inputs innehåller de data som utlösaren förväntar sig och kräver för att starta arbets flödet. Att granska dessa indata kan hjälpa dig att avgöra om utlösarens indata är korrekta och om villkoret uppfylldes så att arbets flödet kan fortsätta.

   Till exempel `feedUrl` har egenskapen här ett felaktigt värde för RSS-flöde:

   ![Granska utlöser indata för fel](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Kontrol lera utlösare utdata, om det finns, för att bekräfta att de visas som förväntat. Under **länken utdata** väljer du länken som visar fönstret **utdata** .

   Utlösa utdata inkluderar de data som utlösaren går vidare till nästa steg i arbets flödet. Att granska dessa utdata kan hjälpa dig att avgöra om rätt eller förväntade värden har överförts till nästa steg i arbets flödet, till exempel:

   ![Granska utlöser utdata för fel](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Om du hittar något innehåll som du inte känner igen kan du läsa mer om [olika innehålls typer](../logic-apps/logic-apps-content-type.md) i Azure Logic Apps.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>Check körnings historik

Varje gång utlösaren utlöses för ett objekt eller en händelse skapas och körs en separat arbets flödes instans för varje objekt eller händelse i Logic Appss motorn. Om körningen Miss lyckas följer du de här stegen för att granska vad som hände under körningen, inklusive status för varje steg i arbets flödet plus indata och utdata för varje steg.

1. Kontrol lera arbets flödets körnings status genom [att kontrol lera körnings historiken](../logic-apps/monitor-logic-apps.md#review-runs-history). Om du vill visa mer information om en misslyckad körning, inklusive alla steg i som körs i deras status, väljer du den misslyckade körningen.

   ![Visa körnings historik och välj misslyckad körning](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. Expandera det första misslyckade steget när alla steg i körningen visas.

   ![Expandera första misslyckades steget](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Kontrol lera indata för det misslyckade steget för att bekräfta om de visas som förväntat.

1. Granska informationen för varje steg i en speciell körning. Under **körnings historik** väljer du den körning som du vill undersöka.

   ![Granska körningshistorik](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Visa information om en logikappkörning](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Om du vill undersöka indata, utdata och eventuella fel meddelanden för ett särskilt steg väljer du det steget så att formen expanderar och visar informationen. Exempel:

   ![Visa information om steg](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Utför felsökning av körningen

Om du vill ha hjälp med fel sökning kan du lägga till diagnostiska steg till ett Logic app-arbetsflöde, tillsammans med att granska utlösaren och köra historiken. Du kan till exempel lägga till steg som använder [webhook tester](https://webhook.site/) -tjänsten så att du kan kontrol lera HTTP-begäranden och bestämma exakt storlek, form och format.

1. Gå till webbplatsen för [webhook-testaren](https://webhook.site/) och kopiera den genererade unika URL: en.

1. I din Logic-app lägger du till en HTTP POST-åtgärd plus det innehåll som du vill testa, till exempel ett uttryck eller en annan steg-utdata.

1. Klistra in din URL från webhook-testaren i HTTP POST-åtgärden.

1. Om du vill granska hur en begäran bildas när den genereras från Logic Apps-motorn kör du Logic-appen och går sedan tillbaka till webbplatsen webhook tester för mer information.

## <a name="next-steps"></a>Nästa steg

* [Övervaka din Logic app](../logic-apps/monitor-logic-apps.md)
