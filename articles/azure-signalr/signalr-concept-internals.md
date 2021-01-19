---
title: Azure SignalR Service – internt
description: Lär dig om interna Azure SignalR-tjänster, arkitekturen, anslutningarna och hur data överförs.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: afb63b76666f47217f9c19376d81aa4ed73991bf
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572569"
---
# <a name="azure-signalr-service-internals"></a>Azure SignalR Service – internt

Azure SignalR Service är byggt på ASP.NET Core SignalR-ramverket. Det stöder också ASP.NET-SignalR genom att återimplementerar ASP.NET-signalers data protokoll ovanpå ASP.NET Core Framework.

Du kan enkelt migrera ett lokalt ASP.NET Core SignalR-program eller ASP.NET SignalR-program för att fungera med signal tjänsten, med några rader kod ändringar.

Diagrammet nedan beskriver den vanliga arkitekturen när du använder SignalR Service med programservern.

Skillnaderna mot det lokala ASP.NET Core SignalR-programmet diskuteras också.

![Arkitektur](./media/signalr-concept-internals/arch.png)

## <a name="server-connections"></a>Serveranslutningar

Den lokala ASP.NET Core SignalR-programservern lyssnar efter och ansluter klienter direkt.

Med SignalR Service tar programservern inte längre emot beständiga klientanslutningar, i stället händer följande:

1. En `negotiate`-slutpunkt exponeras av Azure SignalR Service SDK för varje hubb.
1. Den här slutpunkten kommer att besvara klientens förhandlingsbegäran och omdirigera klienter till SignalR Service.
1. Slutligen ansluts klienterna till SignalR Service.

Mer information finns i [Klientanslutningar](#client-connections).

När programservern har startats händer följande: 
- För ASP.NET Core SignalR öppnar Azure SignalR Service SDK 5 WebSocket-anslutningar per hubb till SignalR Service. 
- För ASP.NET SignalR öppnar Azure SignalR Service SDK 5 WebSocket-anslutningar per hubb till SignalR Service och en per program WebSocket-anslutning.

5 WebSocket-anslutningar är standardvärdet som kan ändras under [Konfiguration](https://github.com/Azure/azure-signalr/blob/dev/docs/run-asp-net-core.md#connectioncount).

Meddelanden till och från klienter kommer att multiplexeras till dessa anslutningar.

Dessa anslutningar kommer fortfarande att vara anslutna till SignalR Service hela tiden. Om en serveranslutning kopplas ifrån på grund av ett nätverksproblem,
- kommer alla klienter som hanteras av den här serveranslutningen att kopplas ifrån (mer information finns i [Data överföring mellan klient och server](#data-transmit-between-client-and-server));
- serveranslutningen börjar återansluta automatiskt.

## <a name="client-connections"></a>Klientanslutningar

När du använder SignalR Service ansluter klienter till SignalR Service i stället för en programserver.
Det finns två steg för att upprätta beständiga anslutningar mellan klienten och SignalR Service.

1. Klienten skickar en förhandlingsbegäran till programservern. Med Azure SignalR Service SDK returnerar programservern et omdirigeringssvar med SignalR Service:s URL och åtkomsttoken.

- För ASP.NET Core SignalR ser ett typiskt omdirigeringssvar ut så här:
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- För ASP.NET SignalR ser ett typiskt omdirigeringssvar ut så här:
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. Efter att ha fått omdirigeringssvaret använder klienten den nya URL: en och åtkomsttoken för att starta den normala processen för att ansluta till SignalR Service.

Läs mer om ASP.NET Core SignalR:s [transportprotokoll](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md).

## <a name="data-transmit-between-client-and-server"></a>Dataöverföring mellan klient och server

När en klient är ansluten till SignalR Service hittar service runtime en serveranslutning för att hantera den här klienten
- Detta sker bara en gång och är en en-till-en-mappning mellan klient- och serveranslutningar.
- Mappningen underhålls i SignalR Service tills klienten eller servern kopplas från.

Programservern får då en händelse med information från den nya klienten. En logisk anslutning till klienten skapas i programservern. Datakanalen upprättas från klienten till programservern via SignalR Service.

Signalerar tjänsten överför data från klienten till länknings applikations servern. Data från programservern kommer att skickas till de mappade klienterna.

SignalR-tjänsten sparar eller lagrar inte kund information, alla mottagna kund uppgifter överförs till mål servern eller klienterna i real tid.

Som du ser är Azure SignalR Service i grunden ett logiskt transportlager mellan programserver och klienter. Alla beständiga anslutningar avlastas till SignalR Service.
Programservern behöver bara hantera affärslogiken i hubbklassen, utan att tänka på klientanslutningar.
