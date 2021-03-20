---
title: Testa logikappar med testdata
description: Konfigurera statiska resultat för att testa Logi Kap par med produktionskostnader utan att påverka produktions miljöer
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: 711d753203aeaeba50cea692053a37fcab2e9c7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93027711"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testa Logic Apps med bilddata genom att konfigurera statiska resultat

När du testar dina Logi Kap par kanske du inte är redo att faktiskt anropa eller komma åt appar, tjänster och system av olika anledningar. I dessa scenarier kan du behöva köra olika villkors sökvägar, tvinga fel, tillhandahålla speciella meddelande svars texter eller till och med försöka hoppa över vissa steg. Genom att ställa in statiska resultat för en åtgärd i din Logic app, kan du skriva ut data från den åtgärden. Att aktivera statiska resultat för en åtgärd kör inte åtgärden, men returnerar de blå data i stället.

Om du till exempel ställer in statiska resultat för åtgärden skicka e-post i Outlook 365 returnerar Logic Apps-motorn bara de blå data som du har angett som statiska resultat, i stället för att anropa Outlook och skicka ett e-postmeddelande.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill konfigurera statiska resultat

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Konfigurera statiska resultat

1. Om du inte redan har gjort det går du till [Azure Portal](https://portal.azure.com)och öppnar din Logic-app i Logic Apps designer.

1. Följ dessa steg på den åtgärd där du vill konfigurera statiska resultat: 

   1. I åtgärdens övre högra hörn väljer du knappen med tre punkter (*...*) och väljer **statiskt resultat**, till exempel:

      ![Välj "statiskt resultat" > aktivera statiskt resultat "](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Välj **Aktivera statiskt resultat**. För obligatoriska (*) egenskaper anger du de värden för de värden som ska returneras för åtgärdens svar.

      Här är till exempel de obligatoriska egenskaperna för HTTP-åtgärden:

      | Egenskap | Beskrivning |
      |----------|-------------|
      | **Status** | Åtgärdens status att returnera |
      | **Status kod** | Den angivna status koden som ska returneras |
      | **Sidhuvuden** | Det rubrik innehåll som ska returneras |
      |||

      ![Välj "Aktivera statiskt resultat"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Om du vill ange ett JSON-format (modell data i JavaScript Object Notation) väljer du **Växla till JSON-läge** ( ![ Välj växla till JSON ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) -läge).

   1. För valfria egenskaper öppnar du listan **Välj valfria fält** och väljer de egenskaper som du vill använda för att modellera.

      ![Välj valfria egenskaper](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. När du är redo att spara väljer du **klar**.

   I åtgärdens övre högra hörn visar namn listen nu en testbägare ikon ( ![ ikon för statiska resultat ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ) som visar att du har aktiverat statiska resultat.

   ![Ikon som visar aktiverade statiska resultat](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   För att hitta tidigare körningar som använder blå data, se [find-körningar som använder statiska resultat](#find-runs-mock-data) senare i det här avsnittet.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Återanvänd tidigare utdata

Om din Logic-app har en tidigare körning med utdata som du kan återanvända som blå utdata, kan du kopiera och klistra in de utdata som körs.

1. Om du inte redan har gjort det går du till [Azure Portal](https://portal.azure.com)och öppnar din Logic-app i Logic Apps designer.

1. På din Logic Apps huvud meny väljer du **Översikt**.

1. I avsnittet **körnings historik** väljer du den logiska app som du vill använda.

1. Leta upp och expandera den åtgärd som har de utdata du vill ha i din Logic app-arbetsflöde.

1. Välj länken **Visa rå utdata** .

1. Kopiera antingen det fullständiga JavaScript Object Notation (JSON)-objektet eller det speciella underavsnitt som du vill använda, till exempel avsnittet utdata eller till och med bara rubrik avsnittet.

1. Följ stegen för att öppna den **statiska resultat** rutan för åtgärden i [Konfigurera statiska resultat](#set-up-static-results).

1. När den **statiska resultat** rutan öppnas väljer du något av stegen:

   * Om du vill klistra in ett fullständigt JSON-objekt väljer du **Växla till JSON-läge** ( ![ Välj växla till JSON ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) -läge):

     ![Välj växla till JSON-läge för det fullständiga objektet](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Om du vill klistra in bara ett JSON-avsnitt, bredvid avsnittets etikett, väljer du **Växla till JSON-läge** för avsnittet, till exempel:

     ![Välj växla till JSON-läge för utdata](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. Klistra in din tidigare kopierade JSON i JSON-redigeraren.

   ![JSON-läge](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. När du är klar väljer du **Klar**. Om du vill återgå till designern väljer du **Växla redigerings läge** ( ![ Välj "växla redigerings läge" ](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png) ).

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Hitta körningar som använder statiska resultat

Din Logic Apps körnings historik identifierar de körningar där åtgärderna använder statiska resultat. Följ dessa steg om du vill ta reda på de här körningarna:

1. På din Logic Apps huvud meny väljer du **Översikt**. 

1. Leta upp kolumnen **statiska resultat** i den högra rutan under **körnings historik**. 

   Alla körningar som innehåller åtgärder med resultat har kolumnen **statiska resultat** inställt på **aktive rad**, till exempel:

   ![Körnings historik – statisk resultat kolumn](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Om du vill visa åtgärder som använder statiska resultat väljer du den plats där kolumnen **statiska resultat** är inställd på **aktive rad**.

   Åtgärder som använder statiska resultat visar ikonen test bägare ( ![ ikon för statiska resultat ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ), till exempel:

   ![Kör historik – åtgärder som använder statiska resultat](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Inaktivera statiska resultat

Om du inaktiverar statiska resultat kastas inte värdena från den senaste konfigurationen. Så när du aktiverar statiska resultat nästa gång kan du fortsätta att använda dina tidigare värden.

1. Hitta den åtgärd där du vill inaktivera statiska utdata. I åtgärdens övre högra hörn väljer du ikonen test bägare ( ![ ikonen för statiska resultat ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ).

   ![Skärm bild som visar en H T T P-åtgärd där du kan välja ikonen test bägare.](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Välj **inaktivera statiskt resultat** har  >  **gjorts**.

   ![Skärm bild som visar alternativet Inaktivera statiskt resultat som du kan välja.](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Referens

Mer information om den här inställningen i de underliggande arbets flödes definitionerna finns i [statiska resultat – schema referens för arbets flödets definitions språk](../logic-apps/logic-apps-workflow-definition-language.md#static-results) och [runtimeConfiguration. staticResult-runtime Configuration Settings](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Logic Apps](../logic-apps/logic-apps-overview.md)