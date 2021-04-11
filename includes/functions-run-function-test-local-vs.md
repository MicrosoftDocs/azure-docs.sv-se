---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: bff2f05a95faf9c475189cb5a8003cb7fd9f69be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701406"
---
1. Tryck på <kbd>F5</kbd> i Visual Studio för att köra funktionen. Du kan behöva aktivera ett brand Väggs undantag så att verktygen kan hantera HTTP-förfrågningar. Tillstånds nivåer tillämpas aldrig när du kör en funktion lokalt.

2. Kopiera URL:en för funktionen från dina Azure Functions-utdata.

    ![Lokal Azure-körningsmiljö](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Lägg till frågesträngen `?name=<YOUR_NAME>` till denna URL och kör begäran. Följande bild visar svaret i webbläsaren till den lokala GET-begäran som returnerades av funktionen: 

    ![Svar för funktion-localhost i webbläsaren](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Stoppa fel sökningen genom att trycka på <kbd>SKIFT</kbd> + <kbd>F5</kbd> i Visual Studio.
