---
title: Använd Java för att skapa ett chattrum med Azure Functions-och SignalR-tjänsten
description: En snabb start för att använda Azure SignalR service och Azure Functions för att skapa ett chattrum med Java.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.custom: devx-track-java
ms.openlocfilehash: 62f31d8426e86cc20e957f3a47b30109dee0a634
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185682"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Snabb start: Använd Java för att skapa ett chattrum med Azure Functions-och SignalR-tjänsten

Med Azure SignalR service kan du enkelt lägga till real tids funktioner i ditt program och Azure Functions är en plattform utan server som gör att du kan köra din kod utan att behöva hantera någon infrastruktur. I den här snabb starten använder du Java för att bygga ett Server lös chatt-program i real tid med hjälp av SignalR service och functions.

## <a name="prerequisites"></a>Förutsättningar

- En kod redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core tools](https://github.com/Azure/azure-functions-core-tools#installing). Används för att köra Azure Function Apps lokalt.

   > [!NOTE]
   > De nödvändiga bindningarna för SignalR-tjänsten i Java stöds bara i Azure Function Core tools version 2.4.419 (värd version 2.0.12332) eller senare.

   > [!NOTE]
   > Om du vill installera tillägg måste [.net Core SDK](https://www.microsoft.com/net/download) vara installerat på Azure Functions Core tools. Dock krävs ingen kunskap om .NET för att skapa Azure Functions-appar med JavaScript.

- [Java Developer Kit](https://www.azul.com/downloads/zulu/), version 8
- [Apache Maven](https://maven.apache.org), version 3.0 eller senare

> [!NOTE]
> Den här snabbstarten kan köras på macOS, Windows eller Linux.

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsjava).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <https://portal.azure.com/> med ditt Azure-konto.

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsjava).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsjava).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsjava).

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurera och köra Azure Functions-appen

1. I den webbläsare där Azure-portalen är öppnad bekräftar du att den SignalR Service-instans som du distribuerade tidigare skapades korrekt genom att söka efter dess namn i sökrutan längst upp i portalen. Välj instansen för att öppna den.

    ![Söka efter SignalR Service-instansen](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Välj **Nycklar** för att visa anslutningssträngarna för SignalR Service-instansen.

1. Markera och kopiera den primära anslutningssträngen.

    ![Skapa SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. Öppna mappen *src/Chat/Java* i den klonade lagrings platsen i kod redigeraren.

1. Byt namn på *local.settings.sample.json* till *local.settings.json*.

1. I **local.settings.json** klistrar du in anslutningssträngen i värdet för inställningen **AzureSignalRConnectionString**. Spara filen.

1. Huvud filen som innehåller funktionerna finns i *src/Chat/Java/src/main/Java/com/Function/functions. java*:

    - **negotiate** (förhandla) – använder indatabindningen *SignalRConnectionInfo* för att skapa och returnera giltig anslutningsinformation.
    - **SendMessage** – tar emot ett chatt meddelande i begär ande texten och använder *signalens* utgående bindning för att skicka meddelandet till alla anslutna klient program.

1. Se till att du finns i mappen *src/Chat/Java* i terminalen. Bygg in Function-appen.

    ```bash
    mvn clean package
    ```

1. Kör Function-appen lokalt.

    ```bash
    mvn azure-functions:run
    ```
    
Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsjava).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsjava).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsjava).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat och kört ett program utan server i real tid med hjälp av Maven. Nu kan du läsa om hur du skapar Java-Azure Functions från grunden.

> [!div class="nextstepaction"]
> [Skapa din första funktion med Java och Maven](../azure-functions/create-first-function-cli-csharp.md?pivots=programming-language-java%2cprogramming-language-java)