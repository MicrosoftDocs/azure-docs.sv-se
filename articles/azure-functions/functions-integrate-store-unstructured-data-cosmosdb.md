---
title: Lagra ostrukturerade data med hjälp av Azure Cosmos DB och funktioner
description: Lagra ostrukturerade data i Azure Cosmos-databasen med hjälp av funktioner
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 5bc3895cb219338acde492b871dce806db70622b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91661167"
---
# <a name="store-unstructured-data-using-azure-functions-and-azure-cosmos-db"></a>Lagra ostrukturerade data i Azure Functions och Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) är ett väldigt bra sätt att lagra ostrukturerade och JSON-data. I kombination med Azure Functions, gör Cosmos DB lagring av data snabbt och enkelt med mycket mindre kod än vad som krävs för att lagra data i en relationsdatabas.

> [!NOTE]
> Just nu fungerar Azure Cosmos DB-utlösaren, indatabindningar och utdatabindningar endast med SQL API och Graph API-konton.

I Azure Functions kan du använda indata- och utdatabindningar för att ansluta till data i en extern tjänst från din funktion på ett deklarativt sätt. I den här artikeln lär du dig hur du uppdaterar en befintlig funktion för att lägga till en utdatabindning som lagrar ostrukturerade data i ett Azure Cosmos DB-dokument.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Du måste ha ett Azure Cosmos DB-konto som använder SQL API innan du skapar utdatabindningen.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-an-output-binding"></a>Lägg till en utdatabindning

1. I Azure Portal navigerar du till och väljer den Function-app som du skapade tidigare.

1. Välj **Functions och välj sedan** funktionen HttpTrigger.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-select-http-function.png" alt-text="Välj http-funktionen i Azure Portal." border="true":::

1. Välj **integrering** och **Lägg till utdata**.

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-add-output-binding.png" alt-text="Lägg till en Azure Cosmos DB utgående bindning." border="true":::

1. Använd inställningarna för att **skapa utdata** som anges i tabellen:

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-configure-cosmosdb-binding.png" alt-text="Konfigurera Azure Cosmos DB utgående bindning." border="true":::

    | Inställning      | Föreslaget värde  | Beskrivning                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Bindnings typ** | Azure Cosmos DB | Namnet på den bindnings typ som ska väljas för att skapa utgående bindningen till Azure Cosmos DB. |
    | **Dokumentparameternamn** | taskDocument | Namn som refererar till Cosmos DB-objektet i koden. |
    | **Databasnamn** | taskDatabase | Namnet på databasen där dokumenten ska sparas. |
    | **Samlingsnamn** | taskCollection | Databassamlingens namn. |
    | **Om värdet är true skapas Cosmos DB-databasen och -samlingen** | Ja | Samlingen finns inte redan, så du måste skapa den. |
    | **Cosmos DB kontoanslutning** | Ny inställning | Välj **nytt**, Välj **Azure Cosmos DB konto** och **databas konto** som du skapade tidigare och välj sedan **OK**. Skapar en programinställning för din kontoanslutning. Den här inställningen används av bindningen för anslutningen till databasen. |

1. Välj **OK** för att skapa bindningen.

## <a name="update-the-function-code"></a>Uppdatera funktionskoden

Ersätt den befintliga funktionskoden med följande kod, på ditt valda språk:

# <a name="c"></a>[C#](#tab/csharp)

Ersätt den befintliga C#-funktionen med följande kod:

```csharp
#r "Newtonsoft.Json"

using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, out object taskDocument, ILogger log)
{
    string name = req.Query["name"];
    string task = req.Query["task"];
    string duedate = req.Query["duedate"];

    // We need both name and task parameters.
    if (!string.IsNullOrEmpty(name) && !string.IsNullOrEmpty(task))
    {
        taskDocument = new
        {
            name,
            duedate,
            task
        };

        return (ActionResult)new OkResult();
    }
    else
    {
        taskDocument = null;
        return (ActionResult)new BadRequestResult();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ersätt den befintliga JavaScript-funktionen med följande kod:

```js
module.exports = async function (context, req) {

    // We need both name and task parameters.
    if (req.query.name && req.query.task) {

        // Set the output binding data from the query object.
        context.bindings.taskDocument = req.query;

        // Success.
        context.res = {
            status: 200
        };
    }
    else {
        context.res = {
            status: 400,
            body: "The query options 'name' and 'task' are required."
        };
    }
};
```
---

Det här kodexemplet läser frågesträngarna i HTTP-begäran och tilldelar dem till fält i `taskDocument`-objektet. `taskDocument`-bindningen skickar objektdata från den här bindningsparametern för lagring i den bundna dokumentdatabasen. Databasen skapas första gången funktionen körs.

## <a name="test-the-function-and-database"></a>Testa funktionen och databasen

1. Välj **Testa/kör**. Under **fråga** väljer du **+ Lägg till parameter** och lägger till följande parametrar i frågesträngen:

    + `name`
    + `task`
    + `duedate`

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png" alt-text="Testa funktionen." border="true":::


1. Välj **Kör** och kontrol lera att en 200-status returneras.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function-output.png" alt-text="Skärm bild som visar statusen för HTTP-svarskod 200 markerad när du har valt kör." border="true":::


1. I Azure Portal söker du efter och väljer **Azure Cosmos DB**.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-search-cosmos-db.png" alt-text="Sök efter tjänsten Cosmos DB." border="true":::

1. Välj ditt Azure Cosmos DB konto och välj sedan  **datautforskaren**.

1. Expandera **TaskCollection** -noderna, Välj det nya dokumentet och bekräfta att dokumentet innehåller dina frågesträngs värden, tillsammans med ytterligare metadata.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-data-explorer-check-document.png" alt-text="Verifiera sträng värden i dokumentet." border="true":::

Du har lagt till en bindning till din HTTP-utlösare för att lagra ostrukturerade data i en Azure Cosmos DB.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om att binda till en Cosmos DB-databas finns i [Azure Functions Cosmos DB-bindningar](functions-bindings-cosmosdb.md).

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps-2.md)]
