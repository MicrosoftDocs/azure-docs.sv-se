---
title: Lägga till Switch-instruktioner till arbets flöden
description: Så här skapar du switch-instruktioner som styr arbets flödes åtgärder baserat på vissa värden i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 62f147104cf9e0e1605b02a420cb6d20190361b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89657462"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Skapa switch-instruktioner som kör arbets flödes åtgärder baserat på vissa värden i Azure Logic Apps

Om du vill köra vissa åtgärder baserat på värdena för objekt, uttryck eller token, lägger du till en *switch* -instruktion. Den här strukturen utvärderar objektet, uttrycket eller token, väljer det Skift läge som matchar resultatet och kör bara vissa åtgärder för det fallet. När switch-instruktionen körs ska endast ett fall matcha resultatet.

Anta till exempel att du vill ha en Logic-app som tar olika steg baserat på ett alternativ som valts i e-post. I det här exemplet kontrollerar Logic-appen en webbplats RSS-flöde för nytt innehåll. När ett nytt objekt visas i RSS-flödet skickar Logic-appen e-post till en god kännare. Beroende på om god kännaren väljer "Godkänn" eller "avvisa" följer Logic-appen olika steg.

> [!TIP]
> Precis som alla programmeringsspråk stöder switch-satser bara likhets operatorer. Om du behöver andra relationella operatorer, till exempel "större än", använder du en [villkors instruktion](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> För att säkerställa deterministiska körnings beteenden måste fall innehålla ett unikt och statiskt värde i stället för dynamiska tokens eller uttryck.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Om du vill följa exemplet i den här artikeln [skapar du den här exempel Logic-appen](../logic-apps/quickstart-create-first-logic-app-workflow.md) med ett Outlook.com-konto eller ett arbets-eller skol konto.

  1. När du lägger till åtgärden för att skicka e-post, leta upp och välj den här åtgärden i stället: **Skicka ett e-postmeddelande om godkännande**

     ![Välj "Skicka ett e-postmeddelande om godkännande"](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Ange de obligatoriska fälten, t. ex. e-postadressen för den person som får e-postmeddelandet. 
  Under **användar alternativ** anger du "Godkänn, avvisa".

     ![Ange e-postinformation](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Lägg till Switch-instruktion

1. I det här exemplet lägger du till en switch-instruktion i slutet av exempel arbets flödet. Välj **nytt steg** efter det sista steget.

   När du vill lägga till en switch-sats mellan stegen flyttar du pekaren över den pil där du vill lägga till Switch-instruktionen. Välj **plus tecknet** ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Skriv "Växla" som filter i sökrutan. Välj den här åtgärden: **växel kontroll**

   ![Lägg till växel](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   En switch-instruktion visas med ett skift läge och ett standard fall. 
   Som standard kräver en switch-instruktion minst ett fall plus standard fallet. 

   ![Tom standard växel instruktion](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Klicka i rutan **i** så att listan med dynamiskt innehåll visas. I listan väljer du fältet **SelectedOption** vars utdata bestämmer vilken åtgärd som ska utföras. 

   ![Välj "SelectedOption"](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. För att hantera de fall där god kännaren väljer `Approve` eller `Reject` , Lägg till ett annat Skift läge mellan **SKIFT läget** och **standard**. 

   ![Lägg till ett annat ärende](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Lägg till följande åtgärder i motsvarande fall:

   | Enskilt # | **SelectedOption** | Action |
   |--------|--------------------|--------|
   | Fall 1 | **Godkänn** | Lägg till Outlook **skicka en e-** poståtgärd för att skicka information om RSS-objektet endast när god kännaren valt **Godkänn**. |
   | Fall 2 | **Avvisa** | Lägg till Outlook **skicka en e-** poståtgärd för att meddela andra god kännare om att RSS-objektet avvisades. |
   | Standardvärde | Inget | Ingen åtgärd krävs. I det här exemplet är **standard** fallet tomt eftersom **SelectedOption** bara har två alternativ. |
   |||

   ![Instruktionen avslutad switch](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Spara logikappen. 

   Om du vill testa det här exemplet manuellt väljer du **Kör** tills Logic-appen hittar ett nytt RSS-objekt och skickar ett e-postmeddelande om godkännande. 
   Välj **Godkänn** för att se resultatet.

## <a name="json-definition"></a>JSON-definition

Nu när du har skapat en Logic app med en switch-instruktion ska vi titta på den övergripande kod definitionen bakom switch-instruktionen.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {}
         },
         "case" : "Approve"
      },
      "Case_2": {
         "actions": {
           "Send_an_email_2": {}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

| Etikett | Beskrivning |
|-------|-------------|
| `"Switch"`         | Namnet på Switch-instruktionen, som du kan byta namn på för läsbarhet |
| `"type": "Switch"` | Anger att åtgärden är en switch-instruktion |
| `"expression"`     | I det här exemplet anger god kännaren valt alternativ som utvärderas mot varje ärende som har deklarerats senare i definitionen |
| `"cases"` | Definierar ett valfritt antal fall. För varje fall `"Case_*"` är standard namnet för det fallet, som du kan byta namn på för läsbarhet |
| `"case"` | Anger Case-värdet, som måste vara ett konstant värde och ett unikt värde som switch-instruktionen använder för jämförelse. Om inga fall matchar resultatet av växel uttrycket körs åtgärderna i `"default"` avsnittet. | 
| | | 

## <a name="get-support"></a>Få support

* För frågor, besök [sidan Microsoft Q&en fråga för Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Om du vill skicka in eller rösta på funktioner eller förslag går du till [webbplatsen för Azure Logic Apps feedback från användare](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserat på ett villkor (villkorliga uttryck)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Köra och upprepa steg (slingor)](../logic-apps/logic-apps-control-flow-loops.md)
* [Köra eller sammanfoga parallella steg (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Kör steg baserat på grupperad åtgärds status (omfång)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
