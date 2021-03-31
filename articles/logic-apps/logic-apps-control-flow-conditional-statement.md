---
title: Lägg till villkorliga uttryck i arbets flöden
description: Skapa villkor som styr åtgärder i arbets flöden i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 19f42270e37c42cf56bbde04f73c01027a56ff63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89657387"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Skapa villkorliga uttryck som styr arbets flödes åtgärder i Azure Logic Apps

Lägg till en *villkors instruktion* för att köra specifika åtgärder i din Logic app enbart efter att ha överfört ett angivet villkor. Den här kontrollstrukturen jämför data i arbetsflödet med vissa värden eller fält. Du kan sedan ange olika åtgärder som körs baserat på om data uppfyller villkoret eller inte. Du kan kapsla villkor inuti varandra.

Anta till exempel att du har en logisk app som skickar för många e-postmeddelanden när nya objekt visas på en webbplats RSS-flöde. Du kan lägga till en villkorlig instruktion för att endast skicka e-post när det nya objektet innehåller en speciell sträng. 

> [!TIP]
> Om du vill köra olika steg baserat på olika värden använder du en [*switch-instruktion*](../logic-apps/logic-apps-control-flow-switch-statement.md) i stället.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Om du vill följa exemplet i den här artikeln [skapar du den här exempel Logic-appen](../logic-apps/quickstart-create-first-logic-app-workflow.md) med ett Outlook.com-konto eller ett arbets-eller skol konto.

## <a name="add-condition"></a>Lägg till villkor

1. I <a href="https://portal.azure.com" target="_blank">Azure Portal</a>öppnar du din Logic app i Logic App Designer.

1. Lägg till ett villkor på den plats som du vill använda. 

   Om du vill lägga till ett villkor mellan stegen flyttar du pekaren över den pil där du vill lägga till villkoret. Välj **plus tecknet** ( **+** ) som visas och välj sedan **Lägg till en åtgärd**. Exempel:

   ![Lägg till åtgärd mellan steg](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   När du vill lägga till ett villkor i slutet av arbets flödet väljer du  **nytt steg** > **Lägg till en åtgärd** längst ned i din Logic app.

1. I rutan Sök anger du "Condition" som filter. Välj den här åtgärden: **villkor-kontroll**

   ![Lägg till villkor](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. Bygg ditt villkor i rutan **villkor** . 

   1. I den vänstra rutan anger du de data eller fält som du vill jämföra.

      När du klickar i den vänstra rutan, visas listan över dynamiskt innehåll så att du kan välja utdata från föregående steg i din Logic app. 
      I det här exemplet väljer du sammanfattningen RSS-feed.

      ![Bygg ditt villkor](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. I rutan i mitten väljer du den åtgärd som ska utföras. 
   I det här exemplet väljer du "**innehåller**". 

   1. Ange ett värde eller ett fält som kriterier i den högra rutan. 
   I det här exemplet anger du strängen: **Microsoft**

   Här är det kompletta villkoret:

   ![Slutför villkor](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Om du vill lägga till en annan rad i villkoret väljer du **Lägg till**  >  **Lägg till rad**. 
   Om du vill lägga till en grupp med under villkor väljer du **Lägg till**  >  **Lägg till grupp**. 
   Om du vill gruppera befintliga rader markerar du kryss rutorna för dessa rader, klickar på knappen med tre punkter (...) för alla rader och väljer sedan **Skapa grupp**.

1. Under **om sant** och **falskt**, Lägg till de steg som ska utföras baserat på om villkoret är uppfyllt. Exempel:

   ![Villkor med "If true" och "If false" sökvägar](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Du kan dra befintliga åtgärder till **IF True** och om det är **false** sökvägar.

1. Spara logikappen.

Den här Logic-appen skickar nu endast e-post när de nya objekten i RSS-flödet uppfyller ditt villkor.

## <a name="json-definition"></a>JSON-definition

Här är en kod definition på hög nivå bakom en villkors instruktion:

``` json
"actions": {
  "Condition": {
    "type": "If",
    "actions": {
      "Send_an_email": {
        "inputs": {},
        "runAfter": {}
    },
    "expression": {
      "and": [ 
        { 
          "contains": [ 
            "@triggerBody()?['summary']", 
            "Microsoft"
          ]
        } 
      ]
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Få support

* För frågor, besök [sidan Microsoft Q&en fråga för Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Om du vill skicka in eller rösta på funktioner och förslag går du till [webbplatsen för Azure Logic Apps feedback från användare](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserade på olika värden (Switch-instruktioner)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Köra och upprepa steg (slingor)](../logic-apps/logic-apps-control-flow-loops.md)
* [Köra eller sammanfoga parallella steg (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Kör steg baserat på grupperad åtgärds status (omfång)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
