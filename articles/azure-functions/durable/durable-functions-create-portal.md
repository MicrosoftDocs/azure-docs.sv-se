---
title: Skapa Durable Functions med hjälp av Azure Portal
description: Lär dig hur du installerar Durable Functions-tillägget för Azure Functions för Portal utveckling.
ms.topic: conceptual
ms.date: 04/10/2020
ms.reviewer: azfuncdf
ms.openlocfilehash: b029fa246977dfe4210f6e8df242415f7e4103f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87081924"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Skapa Durable Functions med hjälp av Azure Portal

[Durable Functions](durable-functions-overview.md) -tillägget för Azure Functions anges i NuGet-paketet [Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Tillägget måste vara installerat i din Function-app. Den här artikeln visar hur du installerar det här paketet så att du kan utveckla varaktiga funktioner i Azure Portal.

> [!NOTE]
> 
> * Om du utvecklar varaktiga funktioner i C# bör du i stället överväga att använda [Visual Studio 2019-utveckling](durable-functions-create-first-csharp.md).
> * Om du utvecklar varaktiga funktioner i Java Script bör du i stället överväga [Visual Studio Code Development](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en Function-app som är värd för körningen av en funktion. Med en Function-app kan du gruppera dina funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser. Du kan skapa en .NET-eller JavaScript-app.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Som standard använder Function-appen version 2. x av Azure Functions Runtime. Durable Functions-tillägget fungerar både i version 1. x och 2. x i Azure Functions runtime i C#, och version 2. x i Java Script. Men mallarna är bara tillgängliga när du riktar in dig på version 2. x av körnings miljön, oavsett vilket språk som valts.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Installera NPM-paketet bestående av funktioner (endast java script)

Om du skapar ett JavaScript-Durable Functions måste du installera [ `durable-functions` NPM-paketet](https://www.npmjs.com/package/durable-functions):

1. Från din funktions programs sida väljer du **Avancerade verktyg** under **utvecklingsverktyg** i det vänstra fönstret.

   :::image type="content" source="./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png" alt-text="Funktions plattforms funktioner Välj kudu":::

2. På sidan **Avancerade verktyg** väljer du **gå** till.

3. I kudu-konsolen väljer du **Felsök konsol** och sedan **cmd**.

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Kudu fel söknings konsol":::

3. Din funktions programmets fil katalog struktur bör visas. Gå till mappen `site/wwwroot`. Därifrån kan du ladda upp en `package.json` fil genom att dra och släppa den i fönstret fil katalog. Ett exempel `package.json` är nedan:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Kudu Ladda upp package.jspå":::

4. När din `package.json` har överförts kör du `npm install` kommandot från kudu-konsolen för fjärrkörning.

   ![Kudu kör NPM-installation](./media/durable-functions-create-portal/kudu-npm-install.png)
   
## <a name="create-an-orchestrator-function"></a>Skapa en Orchestrator-funktion

1. I din Function-app väljer du **Functions** i den vänstra rutan och väljer sedan **Lägg till** på den översta menyn. 

1. I Sök fältet på sidan **ny funktion** anger du `durable` och väljer sedan **Durable Functions http starter-** mall.

   :::image type="content" source="./media/durable-functions-create-portal/durable-functions-http-starter-template.png" alt-text="Välj Durable Functions HTTP-startstarter":::

1. För det **nya funktions** namnet anger du `HttpStart` och väljer sedan **skapa funktion**.

   Funktionen som skapas används för att starta dirigeringen.

1. Skapa en annan funktion i Function-appen, den här gången med hjälp av **Durable Functions Orchestrator** -mallen. Ge din nya Orchestration-funktion ett namn `HelloSequence` .

1. Skapa en tredje funktion med namnet `Hello` med hjälp av mallen för **Durable Functionss aktivitet** .

## <a name="test-the-durable-function-orchestration"></a>Testa den varaktiga funktions dirigeringen

1. Gå tillbaka till funktionen **HttpStart** , Välj **Hämta funktions webb adress** och välj ikonen **Kopiera till Urklipp** för att kopiera URL: en. Du kan använda den här URL: en för att starta funktionen **HelloSequence** .

1. Använd ett HTTP-verktyg som Postman eller sväng för att skicka en POST-begäran till den URL som du kopierade. Följande exempel är ett spiral kommando som skickar en POST-begäran till den varaktiga funktionen:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence --header "Content-Length: 0"
    ```

    I det här exemplet `{your-function-app-name}` är den domän som är namnet på din Function-app. Svarsmeddelandet innehåller en uppsättning URI-slutpunkter som du kan använda för att övervaka och hantera körningen, som ser ut som följande exempel:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Anropa `statusQueryGetUri` slut punkts-URI: n och se aktuell status för den varaktiga funktionen, som kan se ut som i det här exemplet:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Fortsätt att anropa `statusQueryGetUri` slut punkten tills statusen ändras till **slutförd** och du ser ett svar som följande exempel:

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Din första tåliga funktion är nu igång i Azure.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om vanliga mönster för beständiga funktioner](durable-functions-overview.md#application-patterns)
