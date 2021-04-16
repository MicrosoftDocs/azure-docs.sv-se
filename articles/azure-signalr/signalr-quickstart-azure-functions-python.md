---
title: Azure SignalR Service snabbstart utan server – Python
description: En snabbstart för att använda Azure SignalR Service och Azure Functions för att skapa ett chattrum med Python.
author: anthonychu
ms.author: antchu
ms.date: 12/14/2019
ms.topic: quickstart
ms.service: signalr
ms.devlang: python
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: a279c1ed16cac69932001146d4108cec19203e22
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536636"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Snabbstart: Skapa ett chattrum med Azure Functions och SignalR Service med Python

Med Azure SignalR Service kan du enkelt lägga till realtidsfunktioner i ditt program. Azure Functions är en serverlös plattform som gör att du kan köra din kod utan att behöva hantera någon infrastruktur. I den här snabbstarten lär du dig hur du använder SignalR Service och Functions för att skapa ett serverlöst realtidschattprogram.

## <a name="prerequisites"></a>Förutsättningar

Den här snabbstarten kan köras på macOS, Windows eller Linux.

Se till att du har en kodredigerare såsom [Visual Studio Code](https://code.visualstudio.com/) installerad.

Installera [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (version 2.7.1505 eller senare) för att köra Python Azure Function-appar lokalt.

Azure Functions kräver [Python 3.6+](https://www.python.org/downloads/). (Se [Python-versioner som stöds](/azure/azure-functions/functions-reference-python#python-version))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qspython).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <https://portal.azure.com/> med ditt Azure-konto.

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qspython).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qspython).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qspython).

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurera och köra Azure Functions-appen

1. I den webbläsare där Azure-portalen är öppnad bekräftar du att den SignalR Service-instans som du distribuerade tidigare skapades korrekt genom att söka efter dess namn i sökrutan längst upp i portalen. Välj instansen för att öppna den.

    ![Söka efter SignalR Service-instansen](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Välj **Nycklar** för att visa anslutningssträngarna för SignalR Service-instansen.

1. Markera och kopiera den primära anslutningssträngen.

    ![Markera och kopiera den primära anslutningssträngen.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. Öppna mappen *src/chat/python* i den klonade lagringsplatsen i kodredigeraren.

1. Om du vill utveckla och testa Python-funktioner lokalt måste du arbeta i en Python 3.6- eller 3.7-miljö. Kör följande kommandon för att skapa och aktivera en virtuell miljö med namnet `.venv`.

    **Linux eller macOS:**

    ```bash
    python3.7 -m venv .venv
    source .venv/bin/activate
    ```

    **Windows:**

    ```powershell
    py -3.7 -m venv .venv
    .venv\scripts\activate
    ```

1. Byt namn på *local.settings.sample.json* till *local.settings.json*.

1. I **local.settings.json** klistrar du in anslutningssträngen i värdet för inställningen **AzureSignalRConnectionString**. Spara filen.

1. Python-funktioner är ordnade i mappar. I varje mapp finns två *filer:function.jsi* definierar de bindningar som används i funktionen och *\_ \_ init \_ \_ .py* är brödtexten i funktionen. Det finns två HTTP-utlösta funktioner i den här funktionsappen:

    - **negotiate** (förhandla) – använder indatabindningen *SignalRConnectionInfo* för att skapa och returnera giltig anslutningsinformation.
    - **messages** (meddelanden) – tar emot ett chattmeddelande i begärandetexten och använder utdatabindningen *SignalR* för att skicka meddelandet till alla anslutna klientprogram.

1. Kontrollera att du befinner dig i mappen *src/chat/python* i terminalen med den virtuella miljön aktiverad. Installera nödvändiga Python-paket med PIP.

    ```bash
    python -m pip install -r requirements.txt
    ```

1. Kör funktionsappen.

    ```bash
    func start
    ```

    ![Kör funktionsappen](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)
    
Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss.](https://aka.ms/asrs/qspython)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss.](https://aka.ms/asrs/qspython)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss.](https://aka.ms/asrs/qspython)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade och körde du ett serverlöst realtidsprogram i VS Code. Som nästa steg ska du lära dig mer om hur du distribuerar Azure Functions via VS Code.

> [!div class="nextstepaction"]
> [Distribuera Azure Functions med VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)
