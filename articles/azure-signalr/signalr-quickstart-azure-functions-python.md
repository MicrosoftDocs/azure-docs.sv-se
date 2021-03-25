---
title: Azure SignalR service utan snabb start – python
description: En snabb start för att använda Azure SignalR service och Azure Functions för att skapa ett chattrum med python.
author: anthonychu
ms.service: signalr
ms.devlang: python
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: antchu
ms.custom: devx-track-python
ms.openlocfilehash: 82a2e64d27fcbbb3801e6dab347343e086a0b70f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048229"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Snabb start: skapa ett chattrum med Azure Functions-och SignalR-tjänsten med python

Med Azure SignalR Service kan du enkelt lägga till realtidsfunktioner i ditt program. Azure Functions är en serverlös plattform som gör att du kan köra din kod utan att behöva hantera någon infrastruktur. I den här snabbstarten lär du dig hur du använder SignalR Service och Functions för att skapa ett serverlöst realtidschattprogram.

## <a name="prerequisites"></a>Förutsättningar

Den här snabbstarten kan köras på macOS, Windows eller Linux.

Se till att du har en kodredigerare såsom [Visual Studio Code](https://code.visualstudio.com/) installerad.

Installera [Azure Functions Core tools](https://github.com/Azure/azure-functions-core-tools#installing) (version 2.7.1505 eller högre) för att köra python Azure Function Apps lokalt.

Azure Functions kräver [python 3.6 +](https://www.python.org/downloads/). (Se [python-versioner som stöds](/azure/azure-functions/functions-reference-python#python-version))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qspython).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <https://portal.azure.com/> med ditt Azure-konto.

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qspython).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qspython).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qspython).

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurera och köra Azure Functions-appen

1. I den webbläsare där Azure-portalen är öppnad bekräftar du att den SignalR Service-instans som du distribuerade tidigare skapades korrekt genom att söka efter dess namn i sökrutan längst upp i portalen. Välj instansen för att öppna den.

    ![Söka efter SignalR Service-instansen](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Välj **Nycklar** för att visa anslutningssträngarna för SignalR Service-instansen.

1. Markera och kopiera den primära anslutningssträngen.

    ![Markera och kopiera den primära anslutningssträngen.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. I kod redigeraren öppnar du mappen *src/Chat/python* i den klonade lagrings platsen.

1. För att lokalt utveckla och testa python-funktioner måste du arbeta i en python 3,6-eller 3,7-miljö. Kör följande kommandon för att skapa och aktivera en virtuell miljö med namnet `.venv`.

    **Linux eller macOS:**

    ```bash
    python3.7 -m venv .venv
    source .venv/bin/activate
    ```

    **Aktivitets**

    ```powershell
    py -3.7 -m venv .venv
    .venv\scripts\activate
    ```

1. Byt namn på *local.settings.sample.json* till *local.settings.json*.

1. I **local.settings.json** klistrar du in anslutningssträngen i värdet för inställningen **AzureSignalRConnectionString**. Spara filen.

1. Python-funktioner ordnas i mappar. I varje mapp finns två filer: *function.jspå* definierar de bindningar som används i funktionen, och *\_ \_ init \_ \_ . py* är bröd texten i funktionen. Det finns två HTTP-utlösta funktioner i den här funktionsappen:

    - **negotiate** (förhandla) – använder indatabindningen *SignalRConnectionInfo* för att skapa och returnera giltig anslutningsinformation.
    - **messages** (meddelanden) – tar emot ett chattmeddelande i begärandetexten och använder utdatabindningen *SignalR* för att skicka meddelandet till alla anslutna klientprogram.

1. I terminalen med aktive rad virtuell miljö, se till att du är i mappen *src/Chat/python* . Installera de nödvändiga python-paketen med hjälp av PIP.

    ```bash
    python -m pip install -r requirements.txt
    ```

1. Kör funktionsappen.

    ```bash
    func start
    ```

    ![Kör Function-app](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)
    
Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qspython).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qspython).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qspython).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat och kört ett program utan server i real tid i VS Code. Som nästa steg ska du lära dig mer om hur du distribuerar Azure Functions via VS Code.

> [!div class="nextstepaction"]
> [Distribuera Azure Functions med VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)

