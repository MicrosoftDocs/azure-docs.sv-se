---
title: Skapa en funktion i Azure som utlöses av köa meddelanden
description: Använd Azure Functions för att skapa en funktion utan server som anropas av ett meddelande som skickas till en kö i Azure.
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: d722d420597bb459d3e7b6d2ca33fdc49bfe6f09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90981575"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Skapa en funktion som utlöses av Azure Queue Storage

Läs hur du skapar en funktion som utlöses när meddelanden skickas till en Azure Storage-kö.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-an-azure-function-app"></a>Skapa en Azure Functions-app

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-create-success.png" alt-text="Function-appen har skapats.." border="true":::

Därefter skapar du en funktion i den nya funktionsappen.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Skapa en funktion som utlöses av en kö

1. Välj **Functions** och välj sedan **+ Lägg** till för att lägga till en ny funktion.

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-app-quickstart-choose-template.png" alt-text="Välj en funktions mal len i Azure Portal." border="true":::

1. Välj mallen för **Azure Queue Storage-utlösaren** .

1. Använd inställningarna som anges i tabellen nedanför bilden.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png" alt-text="Namnge och konfigurera kö Storage-utlöst funktion." border="true":::


    | Inställning | Föreslaget värde | Beskrivning |
    |---|---|---|
    | **Namn** | Ett unikt namn i funktionsappen | Namnge funktionen som utlöses av kön. |
    | **Könamn**   | myqueue-items    | Namnet på den kö som ska anslutas till i ditt Storage-konto. |
    | **Lagringskontoanslutning** | AzureWebJobsStorage | Du kan antingen använda den lagringskontoanslutning som redan används i funktionsappen eller skapa en ny.  |    

1. Välj **skapa funktion** för att skapa din funktion.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-3.png" alt-text="Skapa den Utlös ande funktionen Queue Storage." border="true":::

Sedan ansluter du till ditt Azure Storage-konto och skapar lagrings kön för **kön objekt** .

## <a name="create-the-queue"></a>Skapa kön

1. I din funktion går du till sidan **Översikt** och väljer din resurs grupp.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-resource-group.png" alt-text="Välj din Azure Portal resurs grupp." border="true":::

1. Sök efter och välj din resurs grupps lagrings konto.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-account-access.png" alt-text="Åtkomst till lagrings kontot." border="true":::

1. Välj **köer** och välj sedan **+ kö**. 

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-add-queue.png" alt-text="Lägg till en kö till ditt lagrings konto i Azure Portal." border="true":::

1. I fältet **namn** skriver `myqueue-items` du och väljer sedan **skapa**.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-name-queue.png" alt-text="Namnge kön lagrings behållare." border="true":::

Nu när du har en lagringskö kan du testa funktionen genom att lägga till ett meddelande i kön.

## <a name="test-the-function"></a>Testa funktionen

1. Gå tillbaka till den Azure Portal, bläddra till din funktion expandera **loggarna** längst ned på sidan och se till att logg strömningen inte är pausad.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-queue-storage-log-expander.png" alt-text="Expandera loggen i Azure Portal." border="true":::

1. I ett separat webbläsarfönster går du till din resurs grupp i Azure Portal och väljer lagrings kontot.

1. Välj **köer** och välj sedan behållaren för **kön objekt** .

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue.png" alt-text="Gå till kön för kön objekt i Azure Portal." border="true":::

1. Välj **Lägg till meddelande** och skriv "Hello World!" i **meddelande text**. Välj **OK**.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue-test.png" alt-text="Skärm bild som visar knappen Lägg till meddelande markerad och meddelande text fältet är markerat." border="true":::

1. Vänta några sekunder och gå sedan tillbaka till dina funktionsloggar och kontrollera att det nya meddelandet har lästs från kön.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png" alt-text="Visa meddelande i loggarna." border="true":::

1. Gå tillbaka till lagrings kön, Välj **Uppdatera** och kontrol lera att meddelandet har bearbetats och att det inte längre finns i kön.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har skapat en funktion som körs när ett meddelande läggs till i en lagringskö. Mer information om Queue Storage-utlösare finns i [Azure Functions Storage queue bindings](functions-bindings-storage-queue.md) (Azure Functions-lagringsköbindningar).

Nu när du har skapat din första funktion ska vi lägga till en utdatabindning till funktionen som skriver ett meddelande till en annan kö.

> [!div class="nextstepaction"]
> [Lägga till meddelanden i en Azure Storage-kö med Functions](functions-integrate-storage-queue-output-binding.md)
