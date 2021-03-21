---
title: Lägga till meddelanden i en Azure Storage-kö med Functions
description: Använd Azure Functions för att skapa en funktion utan server som startas av en HTTP-begäran och skapar ett meddelande i en Azure Storage-kö.
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.topic: how-to
ms.date: 04/24/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: d2821a16e0b72b32cc392b7ae626d782734458a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98674209"
---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>Lägga till meddelanden i en Azure Storage-kö med Functions

I Azure Functions kan du använda indata- och utdatabindningar för att skapa data från externa tjänster som är tillgängliga för din kod. I den här snabbstarten använder du en utdatabindning för att skapa ett meddelande i en kö när en funktion utlöses av en HTTP-begäran. Du använder Azure Storage-behållare för att visa de Kömeddelanden som din funktion skapar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Följ anvisningarna i [Skapa din första funktion i Azure Portal](./functions-get-started.md) och utför inte steget **Rensa resurser**. Den här snabbstarten skapar funktionsappen och funktionen som du använder här.

## <a name="add-an-output-binding"></a><a name="add-binding"></a>Lägga till en utdatabindning

I det här avsnittet använder du portalens användargränssnitt för att lägga till en Queue Storage-utdatabindning till funktionen som du skapade tidigare. Den här bindningen gör det möjligt att skriva minimal kod för att skapa ett meddelande i en kö. Du behöver inte skriva kod för uppgifter som till exempel att öppna en lagringsanslutning, skapa en kö eller lägga till en referens i en kö. Azure Functions-körningen och utdatabindningen tar hand om de här aktiviteterna åt dig.

1. Öppna sidan för funktionsappen som du skapade i [Skapa din första funktion i Azure Portal](./functions-get-started.md) på sidan för funktionsappar i Azure Portal. Öppna sidan genom att söka efter och välja **Funktionsapp**. Välj sedan din Function-app.

1. Välj din Function-app och välj sedan den funktion som du skapade i den tidigare snabb starten.

1. Välj **integrering** och välj sedan **+ Lägg till utdata**.

   :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding.png" alt-text="Skapa en utgående bindning för din funktion." border="true":::

1. Välj typ av **Azure Queue Storage** -bindning och Lägg till inställningarna som anges i tabellen som följer den här skärm bilden: 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding-details.png" alt-text="Lägg till en Queue Storage-utdatabindning i en funktion på Azure Portal." border="true":::
    
    | Inställning      |  Föreslaget värde   | Beskrivning                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Meddelandeparameternamn** | outputQueueItem | Namnet på utdatabindningsparametern. | 
    | **Könamn**   | utkö  | Namnet på kön som ska anslutas till i ditt Storage-konto. |
    | **Lagringskontoanslutning** | AzureWebJobsStorage | Du kan antingen använda den lagringskontoanslutning som redan används i funktionsappen eller skapa en ny.  |

1. Välj **OK** för att lägga till bindningen.

Nu när du har definierat en utdatabindning måste du uppdatera koden så att bindningen används när meddelanden läggs till i en kö.  

## <a name="add-code-that-uses-the-output-binding"></a>Lägg till kod som använder utdatabindning

I det här avsnittet lägger du till kod som skriver ett meddelande till utdatakön. Meddelandet innehåller värdet som skickades till HTTP-utlösaren i frågesträngen. Om frågesträngen till exempel innehåller `name=Azure` så är kömeddelandet *Name passed to the function: Azure* (Namn som skickats till funktionen: Azure).

1. I din funktion väljer du **kod + test** för att Visa funktions koden i redigeraren.

1. Uppdatera funktionskoden beroende på ditt funktionsspråk:

    # <a name="c"></a>[C\#](#tab/csharp)

    Lägg till en **outputQueueItem**-parameter till metodsignaturen som visas i följande exempel.

    ```cs
    public static async Task<IActionResult> Run(HttpRequest req,
        ICollector<string> outputQueueItem, ILogger log)
    {
        ...
    }
    ```

    I meddelandetexten för funktionen precis före `return`-instruktionen lägger du till kod som använder parametern för att skapa ett kömeddelande.

    ```cs
    outputQueueItem.Add("Name passed to the function: " + name);
    ```

    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    Lägg till kod som använder utdatabindningen till `context.bindings`-objektet för att skapa ett kömeddelande. Lägg till den här koden före `context.done`-instruktionen.

    ```javascript
    context.bindings.outputQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ---

1. Välj **Spara** för att spara ändringarna.

## <a name="test-the-function"></a>Testa funktionen

1. När kod ändringarna har sparats väljer du **test**.
1. Bekräfta att testet matchar bilden nedan och välj **Kör**. 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png" alt-text="Testa bindningen för Queue Storage i Azure Portal." border="true":::

    Observera att **Begärandetexten** innehåller värdet `name`*Azure*. Värdet visas i kömeddelandet som skapas när funktionen anropas.
    
    Som ett alternativ till att välja **Kör** här kan du anropa funktionen genom att ange en URL i en webbläsare och ange värdet `name` i frågesträngen. Webbläsarmetoden visas i den [tidigare snabbstarten](./functions-get-started.md).

1. Kontrollera i loggarna att funktionen har slutförts utan fel. 

En ny kö med namnet **outqueue** skapas i ditt lagringskonto av funktionskörningen när utdatabindningen används för första gången. Du använder lagrings kontot för att kontrol lera att kön och ett meddelande i den har skapats.

### <a name="find-the-storage-account-connected-to-azurewebjobsstorage"></a>Hitta lagrings kontot som är anslutet till AzureWebJobsStorage


1. Gå till din Function-app och välj **konfiguration**.

1. Under **program inställningar** väljer du **AzureWebJobsStorage**.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-find-storage-account.png" alt-text="Skärm bild som visar konfigurations sidan med AzureWebJobsStorage valt." border="true":::

1. Leta upp och anteckna konto namnet.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-storage-account-name.png" alt-text="Leta upp lagrings kontot som är anslutet till AzureWebJobsStorage." border="true":::

### <a name="examine-the-output-queue"></a>Granska utdatakö

1. I resurs gruppen för din Function-app väljer du det lagrings konto som du använder för den här snabb starten.

1. Under **kötjänst** väljer du **köer** och **väljer kön som heter kön**. 

   Kön innehåller meddelandet som köutdatabindningen skapade när du körde den HTTP-utlösta funktionen. Om du startade en funktion med standardvärdet `name` för *Azure* så är kömeddelandet *Name passed to the function: Azure* (Namn som skickats till funktionen: Azure).

1. Kör funktionen igen så ser du ett nytt meddelande i kön.  

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lade du till en utdatabindning i en befintlig funktion. Mer information om bindning till Queue Storage finns i [Azure Functions Storage queue bindings](functions-bindings-storage-queue.md) (Azure Functions-lagringsköbindningar).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps-2.md)]
