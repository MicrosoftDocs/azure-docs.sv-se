---
title: Använd Java för att skapa ett chattrum med Azure Functions och SignalR Service
description: En snabbstart för att använda Azure SignalR Service och Azure Functions för att skapa ett chattrum med Hjälp av Java.
author: sffamily
ms.author: zhshang
ms.date: 03/04/2019
ms.topic: quickstart
ms.service: signalr
ms.devlang: java
ms.custom:
- devx-track-java
- mode-api
ms.openlocfilehash: 0d93b9b645aaf4190a36dbc523d40dec2757a18b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869805"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Snabbstart: Använda Java för att skapa ett chattrum med Azure Functions och SignalR Service

Azure SignalR Service kan du enkelt lägga till realtidsfunktioner i ditt program och Azure Functions är en serverlös plattform där du kan köra din kod utan att hantera någon infrastruktur. I den här snabbstarten använder du Java för att skapa ett serverlöst chattprogram i realtid med hjälp SignalR Service och Functions.

## <a name="prerequisites"></a>Förutsättningar

- En kodredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing). Används för att köra Azure Function-appar lokalt.

   > [!NOTE]
   > De obligatoriska SignalR Service-bindningar i Java stöds endast i Azure Function Core Tools version 2.4.419 (värdversion 2.0.12332) eller senare.

   > [!NOTE]
   > Om du vill installera Azure Functions Core Tools måste [.NET Core SDK](https://dotnet.microsoft.com/download) installerats. Dock krävs ingen kunskap om .NET för att skapa Azure Functions-appar med JavaScript.

- [Java Developer Kit](https://www.azul.com/downloads/zulu/), version 8
- [Apache Maven](https://maven.apache.org), version 3.0 eller senare

> [!NOTE]
> Den här snabbstarten kan köras på macOS, Windows eller Linux.

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss.](https://aka.ms/asrs/qsjava)

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <https://portal.azure.com/> med ditt Azure-konto.

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss.](https://aka.ms/asrs/qsjava)

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurera och köra Azure Functions-appen

1. I den webbläsare där Azure-portalen är öppnad bekräftar du att den SignalR Service-instans som du distribuerade tidigare skapades korrekt genom att söka efter dess namn i sökrutan längst upp i portalen. Välj instansen för att öppna den.

    ![Söka efter SignalR Service-instansen](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Välj **Nycklar** för att visa anslutningssträngarna för SignalR Service-instansen.

1. Markera och kopiera den primära anslutningssträngen.

    ![Skapa SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. Öppna mappen *src/chat/java* i den klonade lagringsplatsen i kodredigeraren.

1. Byt namn på *local.settings.sample.json* till *local.settings.json*.

1. I **local.settings.json** klistrar du in anslutningssträngen i värdet för inställningen **AzureSignalRConnectionString**. Spara filen.

1. Huvudfilen som innehåller funktionerna finns i *src/chat/java/src/main/java/com/function/Functions.java*:

    - **negotiate** (förhandla) – använder indatabindningen *SignalRConnectionInfo* för att skapa och returnera giltig anslutningsinformation.
    - **sendMessage** – tar emot ett chattmeddelande i begärandetexten och använder SignalR-utdatabindningen för att sända meddelandet till alla anslutna klientprogram. 

1. Kontrollera att du befinner dig i mappen *src/chat/java* i terminalen. Skapa funktionsappen.

    ```bash
    mvn clean package
    ```

1. Kör funktionsappen lokalt.

    ```bash
    mvn azure-functions:run
    ```
    
Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsjava).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsjava).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsjava).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade och körde du ett serverlöst realtidsprogram med Maven. Lär dig sedan hur du skapar Java-Azure Functions från grunden.

> [!div class="nextstepaction"]
> [Skapa din första funktion med Java och Maven](../azure-functions/create-first-function-cli-csharp.md?pivots=programming-language-java%2cprogramming-language-java)
