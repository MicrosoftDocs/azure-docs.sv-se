---
title: ta med fil
description: ta med fil
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 2af9c1dfd13d3aeafa7cf1ac76537117ecc15aff
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "84317645"
---
## <a name="run-the-web-application"></a>Köra webbprogrammet

1. För att förenkla klient testningen öppnar du webbläsaren till vårt exempel webb program med en enda sida [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/) . 

    > [!NOTE]
    > Källan till HTML-filen finns på [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html). Om du vill vara värd för HTML-koden själv startar du en lokal HTTP-server, till exempel [http-server](https://www.npmjs.com/package/http-server) i */docs/demo/Chat-v2* -katalogen. Se till att ursprunget läggs till i `CORS` inställningen i *local.settings.jspå* liknande sätt som exemplet.
    > 
    > ```javascript
    > "Host": {
    >  "LocalHttpPort": 7071,
    >  "CORS": "http://localhost:8080,https://azure-samples.github.io",
    >  "CORSCredentials": true
    > }
    >
    > ```

1. När du tillfrågas om funktionsappens bas-URL anger du `http://localhost:7071`.

1. Ange ett användarnamn när du tillfrågas.

1. Webbprogrammet anropar funktionen *GetSignalRInfo* i funktionsappen för att hämta anslutningsinformation för att ansluta till Azure SignalR Service. När anslutningen är klar visas inmatningsrutan för chattmeddelanden.

1. Skriv ett meddelande och tryck på Retur. Programmet skickar meddelandet till funktionen *SendMessage* i Azure Functions-appen, som använder SignalR-utdatabindning för att skicka meddelandet till alla anslutna klienter. Om allt fungerar korrekt bör meddelandet visas i programmet.

    ![Kör programmet](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Öppna en till instans av webbprogrammet i ett annat webbläsarfönster. Där ser du att alla meddelanden som skickas visas i alla instanser av programmet.
