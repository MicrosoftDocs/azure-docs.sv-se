---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 5bc1d2ddb5b9ddb185a8349fb42d5f5db79c1498
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201358"
---
## <a name="run-the-function-locally"></a>Köra funktionen lokalt

Visual Studio Code integreras med [Azure Functions Core tools](../articles/azure-functions/functions-run-local.md) så att du kan köra det här projektet på den lokala utvecklings datorn innan du publicerar till Azure.

1. Du anropar funktionen genom att trycka på <kbd>F5</kbd> för att starta projektet för Function-appen. Utdata från Core Tools visas på panelen **Terminal**. Din app startar på panelen **Terminal** . Du kan se URL-slutpunkten för din HTTP-utlöst funktion som körs lokalt.

    ![Lokal funktion kontra utdata från kod](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

    Om du har problem med att köra i Windows måste du kontrol lera att standard terminalen för Visual Studio Code inte är inställd på **Wsl bash**.

1. Med kärn verktyg som körs går du till **Azure: Functions** -avsnittet. Under **funktioner**, expanderar du **lokala projekt**  >  **funktioner**. Högerklicka (Windows) eller <kbd>CTRL-</kbd> klicka (MacOS) på `HttpExample` funktionen och välj **Kör funktion nu..**..

    :::image type="content" source="media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Kör funktionen nu från Visual Studio Code":::
    
1. I **Ange brödtext för begäran** visas bröd texten för begär ande meddelandet `{ "name": "Azure" }` . Skicka meddelandet till din funktion genom att trycka på RETUR. 

   Du kan i stället skicka en HTTP GET-begäran till <http://localhost:7071/api/HttpExample> adressen i en webbläsare.

1. När funktionen körs lokalt och returnerar ett svar, aktive ras ett meddelande i Visual Studio Code. Information om funktions körningen visas på panelen **Terminal** .

1. Tryck på <kbd>CTRL + C</kbd> för att stoppa kärn verktygen och koppla från fel söknings programmet.
