---
title: Använda program i Azure Blockchain Workbench
description: Självstudie om hur du använder program kontrakt i för hands versionen av Azure blockchain Workbench.
ms.date: 10/14/2019
ms.topic: tutorial
ms.reviewer: brendal
ms.openlocfilehash: 5761bf1294691c2d50e0e389fe69ec286df4a06c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "74324045"
---
# <a name="tutorial-using-applications-in-azure-blockchain-workbench"></a>Självstudie: använda program i Azure blockchain Workbench

Du kan använda Blockchain Workbench för att skapa och utföra åtgärder med kontrakt. Du kan också visa information om kontrakt, till exempel status och transaktionshistorik.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett nytt kontrakt
> * Utföra åtgärder med ett kontrakt

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* En Blockchain Workbench-distribution. Mer information finns i [distribution av Azure blockchain Workbench](deploy.md) för information om distribution
* Ett distribuerat blockkedjeprogram i Blockchain Workbench. Mer information finns i avsnittet om hur du [skapar ett blockkedjeprogram i Azure Blockchain Workbench](create-app.md)

[Öppna Blockchain Workbench](deploy.md#blockchain-workbench-web-url) i webbläsaren.

![Blockchain Workbench](./media/use/workbench.png)

Du måste logga in som medlem i Blockchain Workbench. Om inga program visas i listan är du medlem i Blockchain Workbench men inte i något program. Blockchain Workbench-administratören kan tilldela medlemmar till program.

## <a name="create-new-contract"></a>Skapa nytt kontrakt

Om du vill skapa ett nytt kontrakt måste du vara medlem angiven som en kontrakts **initierare**. Information om hur du definierar programroller och initierare för kontraktet finns i [Arbetsflöden i konfigurationsöversikten](configuration.md#workflows). Information om hur du tilldelar medlemmar till programroller finns i avsnittet om hur du [lägger till en medlem i ett program](manage-users.md#add-member-to-application).

1. I avsnittet för Blockchain Workbench-program väljer du programpanelen som innehåller det kontrakt som du vill skapa. En lista över aktiva kontrakt visas.

2. Om du vill skapa ett nytt kontrakt väljer du **Nytt kontrakt**.

    ![Knappen Nytt kontrakt](./media/use/contract-list.png)

3. Rutan **Nytt kontrakt** visas. Ange de ursprungliga parametervärdena. Välj **Skapa**.

    ![Rutan Nytt kontrakt](./media/use/new-contract.png)

    Det nya kontraktet visas i listan med de andra aktiva kontrakten.

    ![Lista över aktiva kontrakt](./media/use/active-contracts.png)

## <a name="take-action-on-contract"></a>Utföra åtgärder med kontrakt

Beroende på kontraktets tillstånd kan medlemmarna utföra åtgärder för att övergå till nästa kontrakttillstånd. Åtgärder definieras som [övergångar](configuration.md#transitions) inom ett [tillstånd](configuration.md#states). Medlemmar som hör till ett tillåtet program eller en instansroll för övergången kan utföra åtgärder. 

1. I avsnittet för Blockchain Workbench-program väljer du programpanelen som innehåller det kontrakt som du vill utföra åtgärder med.
2. Välj kontraktet i listan. Information om kontraktet visas i olika avsnitt. 

    ![Kontraktinformation](./media/use/contract-details.png)

    | Avsnitt  | Beskrivning  |
    |---------|---------|
    | Status | Visar den aktuella statusen i kontraktfaserna |
    | Information | De aktuella värdena för kontraktet |
    | Action | Information om den senaste åtgärden |
    | Aktivitet | Kontraktets transaktionshistorik |
    
3. I avsnittet **Åtgärd** väljer du **Vidta åtgärd**.

4. Informationen om kontraktets aktuella tillstånd visas i en ruta. Välj den åtgärd du vill utföra i listrutan. 

    ![Välj åtgärd](./media/use/choose-action.png)

5. Välj **Vidta åtgärd** för att initiera åtgärden.
6. Om parametrar krävs för åtgärden anger du värdena för åtgärden.

    ![Vidta åtgärd](./media/use/take-action.png)

7. Välj **Vidta åtgärd** för att köra åtgärden.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Versionshantering för Azure Blockchain Workbench-program](version-app.md)
