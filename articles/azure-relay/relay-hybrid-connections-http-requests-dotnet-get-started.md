---
title: Azure Relay Hybridanslutningar HTTP-begäranden i .NET
description: Skriva ett C#-konsolprogram för HTTP-begäranden för Azure Relay-hybridanslutningar i .NET.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 7a11abb984da3601a4d6aa921224e01f94d0871c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88922590"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>Komma igång med HTTP-begäranden för Relay-hybridanslutningar i .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

I den här snabbstarten skapar du ett sändar- och mottagarprogram i .NET som skickar och tar emot meddelanden med HTTP-protokollet. Programmen använder funktionen Hybridanslutningar i Azure Relay. Läs mer om Azure Relay i allmänhet i [Azure Relay](relay-what-is-it.md). 

I den här snabbstarten gör du följande:

1. Skapa ett Relay-namnområde med Azure Portal.
2. Skapa en hybridanslutning i det namnområdet med Azure Portal.
3. Skriva ett serverkonsolprogram (lyssnare) för att ta emot meddelanden.
4. Skriva ett klientkonsolprogram (avsändare) för att ta emot meddelanden.
5. Kör program. 

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

* [Visual Studio 2015 eller senare](https://www.visualstudio.com). I exemplen i den här självstudiekursen används Visual Studio 2017.
* En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-namespace"></a>Skapa ett namnområde
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Skapa en hybridanslutning
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Skapa ett serverprogram (lyssnare)
För att lyssna på och ta emot meddelanden från Relay skriver du ett C#-konsolprogram i Visual Studio.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Skapa ett klientprogram (avsändare)
För att skicka meddelanden till Relay skriver du ett C#-konsolprogram i Visual Studio.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Köra programmen
1. Kör serverprogrammet. Du kan se följande text i konsolfönstret:

    ```
    Online
    Server listening
    ```
1. Kör klientprogrammet. `hello!` visas i klientfönstret. Klienten har skickat en HTTP-begäran till servern och servern svarade med en `hello!`. 
3. Om du vill stänga konsolfönstret nu trycker du på **RETUR** i båda konsolfönsterna. 

Grattis, du har skapat ett komplett Hybridanslutningar-program!

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du .NET-klient- och -serverprogram som använde HTTP för att skicka och ta emot meddelanden. Funktionen Hybridanslutningar i Azure Relay stöder också användningen av WebSockets för att skicka och ta emot meddelanden. Läs om hur du använder WebSockets med Azure Relay-hybridanslutningar i [WebSockets-snabbstarten](relay-hybrid-connections-dotnet-get-started.md).

I den här snabbstarten använde du .NET Framework för att skapa klient- och serverprogram. Om du vill läsa om hur du skriver klient- och serverprogram med hjälp av Node.js läser du [Node.js-snabbstarten](relay-hybrid-connections-node-get-started.md) eller [Node.jsT HTTP-snabbstarten](relay-hybrid-connections-http-requests-dotnet-get-started.md).
