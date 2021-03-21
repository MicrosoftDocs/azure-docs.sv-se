---
title: Skapa en funktion i Azure som körs enligt ett schema
description: Lär dig hur du använder Azure Portal för att skapa en funktion som körs baserat på ett schema som du definierar.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: how-to
ms.date: 04/16/2020
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 14d918cc41f49b954f5cabf48572db5df829fd10
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98035197"
---
# <a name="create-a-function-in-the-azure-portal-that-runs-on-a-schedule"></a>Skapa en funktion i Azure Portal som körs enligt ett schema

Lär dig hur du använder Azure Portal för att skapa en funktion som kör [Server](https://azure.microsoft.com/solutions/serverless/) lös på Azure baserat på ett schema som du definierar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

+ Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-function-app"></a>Skapa en funktionsapp

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Din nya Function-app är redo att användas. Därefter skapar du en funktion i appen ny funktion.

:::image type="content" source="./media/functions-create-scheduled-function/function-app-create-success.png" alt-text="Funktionsappen skapades." border="true":::

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Skapa en timerutlöst funktion

1. I din Function-app väljer du **Functions** och väljer sedan **+ Lägg till** 

   :::image type="content" source="./media/functions-create-scheduled-function/function-add-function.png" alt-text="Lägg till en funktion i Azure Portal." border="true":::

1. Välj mallen **timer-utlösare** . 

    :::image type="content" source="./media/functions-create-scheduled-function/function-select-timer-trigger.png" alt-text="Välj timer-utlösaren i Azure Portal." border="true":::

1. Konfigurera den nya utlösaren med de inställningar som anges i tabellen nedanför bilden och välj sedan **skapa funktion**.

    :::image type="content" source="./media/functions-create-scheduled-function/function-configure-timer-trigger.png" alt-text="Skärm bild som visar den nya funktions sidan där mallen timer-utlösare har valts." border="true":::
    
    | Inställning | Föreslaget värde | Beskrivning |
    |---|---|---|
    | **Namn** | Standardvärde | Det här är namnet på den timerutlösta funktionen. |
    | **Schema** | 0 \* /1 \* \* \*\* | Ett [CRON-uttryck](functions-bindings-timer.md#ncrontab-expressions) med sex fält som schemalägger att funktionen ska köras varje minut. |

## <a name="test-the-function"></a>Testa funktionen

1. I din funktion väljer du **kod + test** och expanderar loggarna.

    :::image type="content" source="./media/functions-create-scheduled-function/function-test-timer-trigger.png" alt-text="Testa timer-utlösaren i Azure Portal." border="true":::

1. Verifiera körningen genom att visa informationen som skrivs till loggarna.

    :::image type="content" source="./media/functions-create-scheduled-function/function-view-timer-logs.png" alt-text="Visa timer-utlösaren i Azure Portal." border="true":::

Sedan ändrar du funktionens schema så att det körs en gång per timme i stället för varje minut.

## <a name="update-the-timer-schedule"></a>Uppdatera timerschemat

1. I din funktion väljer du **integrering**. Här kan du definiera indata och utdata-bindningar för din funktion och även ange schemat. 

1. Välj **Timer (timer)**.

    :::image type="content" source="./media/functions-create-scheduled-function/function-update-timer-schedule.png" alt-text="Uppdatera timer-schemat i Azure Portal." border="true":::

1. Uppdatera värdet **schema** till `0 0 */1 * * *` och välj sedan **Spara**.  

    :::image type="content" source="./media/functions-create-scheduled-function/function-edit-timer-schedule.png" alt-text="Uppdatera timer-schema för funktionen i Azure Portal." border="true":::

Nu har du en funktion som körs en gång i timmen, på timmen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har skapat en funktion som körs baserat på ett schema. Mer information timerutlösare finns i [Schedule code execution with Azure Functions](functions-bindings-timer.md) (Schemalägga kodkörning med Azure Functions).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
