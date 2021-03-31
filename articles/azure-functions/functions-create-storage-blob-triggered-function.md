---
title: Skapa en funktion i Azure som utlöses av Blob Storage
description: Använd Azure Functions för att skapa en funktion utan server som anropas av objekt som lagts till i en Blob Storage-behållare.
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: bf6865d2756579f457dded90b247326d2eec137c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "83123130"
---
# <a name="create-a-function-in-azure-thats-triggered-by-blob-storage"></a>Skapa en funktion i Azure som utlöses av Blob Storage

Lär dig hur du skapar en funktion som utlöses när filer laddas upp till eller uppdateras i en Blob Storage-behållare.

## <a name="prerequisites"></a>Förutsättningar

+ En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-an-azure-function-app"></a>Skapa en Azure Functions-app

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Du har skapat din nya Function-app.

:::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-create-success.png" alt-text="Funktionsappen skapades." border="true":::

Därefter skapar du en funktion i den nya funktionsappen.

<a name="create-function"></a>

## <a name="create-an-azure-blob-storage-triggered-function"></a>Skapa en funktion som utlöses av Azure Blob Storage

1. Välj **Functions** och välj sedan **+ Lägg** till för att lägga till en ny funktion.

   :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-template.png" alt-text="Välj en funktions mal len i Azure Portal." border="true":::

1. Välj mallen för **Azure Blob Storage-utlösaren** .

1. Använd inställningarna som anges i tabellen nedanför bilden.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png" alt-text="Namnge och konfigurera Blob Storage-utlöst funktion." border="true":::

    | Inställning | Föreslaget värde | Beskrivning |
    |---|---|---|
    | **Ny funktion** | Ett unikt namn i funktionsappen | Namnge funktionen som utlöses av blobben. |
    | **Sökväg**   | samples-workitems/{namn}    | Platsen i Blob Storage som övervakas. Filnamnet för bloben skickas i bindningen som parametern _namn_.  |
    | **Lagringskontoanslutning** | AzureWebJobsStorage | Du kan antingen använda den lagringskontoanslutning som redan används i funktionsappen eller skapa en ny.  |

1. Välj **skapa funktion** för att skapa din funktion.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-3.png" alt-text="Skapa funktionen som utlöses av Blob Storage." border="true":::

Skapa sedan behållaren **samples-WorkItems** .

## <a name="create-the-container"></a>Skapa containern

1. I din funktion går du till sidan **Översikt** och väljer din resurs grupp.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-resource-group.png" alt-text="Välj din Azure Portal resurs grupp." border="true":::

1. Sök efter och välj din resurs grupps lagrings konto.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-account-access.png" alt-text="Åtkomst till lagrings kontot." border="true":::

1. Välj **behållare** och välj sedan **+ container**. 

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-add-container.png" alt-text="Lägg till behållare till ditt lagrings konto i Azure Portal." border="true":::

1. I fältet **namn** skriver `samples-workitems` du och väljer sedan **skapa**.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-name-blob-container.png" alt-text="Namnge lagrings behållaren." border="true":::

Nu när du har en blobcontainer kan du testa funktionen genom att ladda upp en fil till containern.

## <a name="test-the-function"></a>Testa funktionen

1. Gå tillbaka till den Azure Portal, bläddra till din funktion expandera **loggarna** längst ned på sidan och se till att logg strömningen inte är pausad.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-log-expander.png" alt-text="Expandera loggen i Azure Portal." border="true":::

1. I ett separat webbläsarfönster går du till din resurs grupp i Azure Portal och väljer lagrings kontot.

1. Välj **behållare** och välj sedan behållaren **samples-WorkItems** .

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-container.png" alt-text="Gå till exempel-WorkItems-behållaren i Azure Portal." border="true":::

1. Välj **Ladda upp** och välj sedan mappikonen för att välja en fil att ladda upp.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png" alt-text="Ladda upp en fil till blobcontainern." border="true":::

1. Bläddra till en fil på din lokala dator, till exempel en bildfil, och välj filen. Välj **Öppna** och sedan **Ladda upp**.

1. Gå tillbaka till funktionsloggarna och kontrollera att bloben har lästs.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png" alt-text="Visa meddelande i loggarna." border="true":::

    >[!NOTE]
    > Om din funktionsapp körs med standardförbrukningsplanen kan det dröja flera minuter från det att blobben läggs till eller uppdateras och att funktionen utlöses. Om du behöver låg latens i dina blobutlösta funktioner bör du köra funktionsapparna med en App Service-plan.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har skapat en funktion som körs när en blob läggs till eller uppdateras i Blob Storage. Mer information om Blob Storage-utlösare finns i [Azure Functions Blob storage bindings](functions-bindings-storage-blob.md) (Blob Storage-bindningar i Azure Functions).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
