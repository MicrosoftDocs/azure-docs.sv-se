---
title: Använd JavaScript för att skapa ett chattrum med Azure Functions och SignalR Service
description: En snabbstart för att använda Azure SignalR Service och Azure Functions för att skapa ett chattrum med hjälp av JavaScript.
author: sffamily
ms.author: zhshang
ms.date: 12/14/2019
ms.topic: quickstart
ms.service: signalr
ms.devlang: javascript
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 5e2c9dcd1efc85e1ea3fe6381cbfbff0c5e62fc5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533223"
---
# <a name="quickstart-use-javascript-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Snabbstart: Använda JavaScript för att skapa ett chattrum med Azure Functions och SignalR Service

Azure SignalR Service kan du enkelt lägga till realtidsfunktioner i ditt program och Azure Functions är en serverlös plattform där du kan köra din kod utan att hantera någon infrastruktur. I den här snabbstarten använder du JavaScript för att skapa ett serverlöst chattprogram i realtid med hjälp SignalR Service och Functions.

## <a name="prerequisites"></a>Förutsättningar

- En kodredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)version 2 eller senare. Används för att köra Azure Function-appar lokalt.
- [Node.js](https://nodejs.org/en/download/), version 10.x

   > [!NOTE]
   > Exemplen bör fungera med andra versioner av Node.js. Mer information [finns Azure Functions dokumentationen om](../azure-functions/functions-versions.md#languages) körningsversioner.

> [!NOTE]
> Den här snabbstarten kan köras på macOS, Windows eller Linux.

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss.](https://aka.ms/asrs/qsjs)

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <https://portal.azure.com/> med ditt Azure-konto.

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss.](https://aka.ms/asrs/qsjs)

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss.](https://aka.ms/asrs/qsjs)

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss.](https://aka.ms/asrs/qsjs)

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurera och köra Azure Functions-appen

1. I den webbläsare där Azure-portalen är öppnad bekräftar du att den SignalR Service-instans som du distribuerade tidigare skapades korrekt genom att söka efter dess namn i sökrutan längst upp i portalen. Välj instansen för att öppna den.

    ![Söka efter SignalR Service-instansen](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Välj **Nycklar** för att visa anslutningssträngarna för SignalR Service-instansen.

1. Markera och kopiera den primära anslutningssträngen.

    ![Skärmbild som visar den primära anslutningssträngen.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. Öppna mappen *src/chat/javascript* i den klonade lagringsplatsen i kodredigeraren.

1. Byt namn på *local.settings.sample.json* till *local.settings.json*.

1. I **local.settings.json** klistrar du in anslutningssträngen i värdet för inställningen **AzureSignalRConnectionString**. Spara filen.

1. JavaScript-funktioner är ordnade i mappar. Det finns två filer i varje mapp: *function.json* definierar de bindningar som används i funktionen, och *index.js* är brödtexten till funktionen. Det finns två HTTP-utlösta funktioner i den här funktionsappen:

    - **negotiate** (förhandla) – använder indatabindningen *SignalRConnectionInfo* för att skapa och returnera giltig anslutningsinformation.
    - **messages** (meddelanden) – tar emot ett chattmeddelande i begärandetexten och använder utdatabindningen *SignalR* för att skicka meddelandet till alla anslutna klientprogram.

1. Kontrollera att du befinner dig i mappen *src/chat/javascript* i terminalen. Kör funktionsappen.

    ```bash
    func start
    ```

    ![Skapa SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)
    
Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsjs).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsjs).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsjs).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade och körde du ett serverlöst realtidsprogram i VS Code. Som nästa steg ska du lära dig mer om hur du distribuerar Azure Functions via VS Code.

> [!div class="nextstepaction"]
> [Distribuera Azure Functions med VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)
