---
title: Skapa eller koppla parallella grenar för åtgärder i arbets flöden
description: Lär dig hur du skapar eller sammanfogar parallella grenar för oberoende arbets flödes åtgärder i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 3514ce966e1de9af1741de6b966964aca2599610
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91269241"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Skapa eller Anslut parallella grenar för arbets flödes åtgärder i Azure Logic Apps

Som standard körs dina åtgärder i Logic app-arbetsflöden i tur och ordning. Om du vill utföra oberoende åtgärder samtidigt kan du skapa [parallella grenar](#parallel-branches)och sedan gå över [dessa grenar](#join-branches) senare i ditt flöde. 

> [!TIP] 
> Om du har en utlösare som tar emot en matris och vill köra ett arbets flöde för varje mat ris objekt, kan du *Avgruppera* matrisen med [egenskapen **SplitOn** trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Lägg till parallell gren

Om du vill köra oberoende steg på samma gång kan du lägga till parallella grenar bredvid ett befintligt steg. 

![Kör steg parallellt](media/logic-apps-control-flow-branches/parallel.png)

Din Logic app väntar tills alla grenar är klara innan arbets flödet fortsätter. Parallella grenar körs bara när deras `runAfter` egenskaps värden matchar statusen för det avslutade överordnade steget. Till exempel både `branchAction1` och `branchAction2` är inställda på att endast köras när `parentAction` statusen är slutförd `Succeeded` .

> [!NOTE]
> Innan du börjar måste din Logic app redan ha ett steg där du kan lägga till parallella grenar.

1. I <a href="https://portal.azure.com" target="_blank">Azure Portal</a>öppnar du din Logic app i Logic App Designer.

1. Flytta pekaren över pilen ovanför det steg där du vill lägga till parallella grenar. Välj **plus** tecknet ( **+** ) som visas och välj sedan **Lägg till en parallell gren**. 

   ![Lägg till parallell gren](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. I sökrutan söker du efter och väljer den åtgärd som du vill använda.

   ![Skärm bild som visar fönstret "Välj en åtgärd" i Logic App Designer.](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   Den valda åtgärden visas nu i parallell grenen, till exempel:

   ![Sök efter och välj den åtgärd du vill använda](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Lägg nu till de steg som du vill använda i varje parallell gren. Om du vill lägga till en annan åtgärd i en gren flyttar du pekaren under den åtgärd där du vill lägga till en sekventiell åtgärd. Välj **plus** tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

   ![Lägg till sekventiell åtgärd i parallell gren](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. I sökrutan söker du efter och väljer den åtgärd som du vill använda.

   ![Skärm bild som visar fönstret "Välj en åtgärd" och Sök-rutan i Logic Apps designer.](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   Den valda åtgärden visas nu i den aktuella grenen, till exempel:

   ![Sök och välj sekventiell åtgärd](media/logic-apps-control-flow-branches/added-sequential-action.png)

Om du vill sammanfoga grenar igen tillsammans går [du till dina parallell grenar](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Definition av parallell gren (JSON)

Om du arbetar i kodvyn kan du definiera parallell strukturen i din Logic Apps JSON-definition i stället, till exempel:

``` json
{
  "triggers": {
    "myTrigger": {}
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Koppla parallella grenar

Om du vill sammanfoga parallella grenar tillsammans lägger du bara till ett steg längst ned under alla grenar. Det här steget körs när alla parallella grenar har körts.

![Koppla parallella grenar](media/logic-apps-control-flow-branches/join.png)

1. I [Azure Portal](https://portal.azure.com)kan du söka efter och öppna din Logic app i Logic App Designer. 

1. Under de parallella grenar som du vill ansluta väljer du **nytt steg**. 

   ![Lägg till steg för att ansluta](media/logic-apps-control-flow-branches/add-join-step.png)

1. I sökrutan söker du efter och väljer den åtgärd som du vill använda som det steg som ansluter till grenar.

   ![Sök efter och Välj åtgärden som ansluter parallella grenar](media/logic-apps-control-flow-branches/join-steps.png)

   Dina parallella grenar är nu sammanfogade.

   ![Anslutna grenar](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Kopplings definition (JSON)

Om du arbetar i kodvyn kan du definiera kopplings strukturen i din Logic Apps JSON-definition i stället, till exempel:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>Få support

* För frågor, besök [sidan Microsoft Q&en fråga för Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Om du vill skicka in eller rösta på funktioner och förslag går du till [webbplatsen för Azure Logic Apps feedback från användare](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserat på ett villkor (villkorliga uttryck)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör steg baserade på olika värden (Switch-instruktioner)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Köra och upprepa steg (slingor)](../logic-apps/logic-apps-control-flow-loops.md)
* [Kör steg baserat på grupperad åtgärds status (omfång)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
