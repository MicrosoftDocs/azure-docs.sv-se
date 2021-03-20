---
title: Vad är Azure SignalR Service?
description: Lär dig mer om vanliga scenarier för användnings fall som använder Azure-Signalerare och lär dig viktiga fördelar med Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 92da400616ace9f24da121d81798d5ebd9222e35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88921893"
---
# <a name="what-is-azure-signalr-service"></a>Vad är Azure SignalR Service?

Azure SignalR Service förenklar arbetet med att lägga till webbfunktioner i realtid för program via HTTP. Denna realtidsfunktion gör att tjänsten kan skicka innehållsuppdateringar till anslutna klienter, till exempel en webbplats med en sida eller ett mobilt program. Därmed uppdateras klienterna utan att man behöver avsöka servern eller skicka nya HTTP-begäranden om uppdateringar.


Den här artikeln ger en översikt över Azure SignalR Service.

## <a name="what-is-azure-signalr-service-used-for"></a>Vad används Azure SignalR Service till?

Alla scenarier som kräver data överföring från servern till klienten i real tid kan använda Azure SignalR-tjänsten.

Traditionella funktioner i real tid som ofta kräver avsökning från servern, kan också använda Azure SignalR-tjänsten.

Azure SignalR service har använts i en mängd olika branscher, för alla program typer som kräver innehålls uppdateringar i real tid. Vi listar några exempel som är lämpliga att använda Azure SignalR-tjänsten:

* **Data uppdateringar med hög frekvens:** spel, röstning, avsökning, auktion.
* **Instrument paneler och övervakning:** företags instrument panel, finansiell marknads information, direkt försäljnings uppdatering, spel tavla för flera spelare och IoT-övervakning.
* **Chatt:** Live chatt-rummet, chat-robot, online-kundsupport, real tids shopping Assistant, Messenger, spel-chatt och så vidare.
* **Real tids plats på kartan:** logistisk spårning, leverans status spårning, transport status uppdateringar, GPS-appar.
* **Real tids mål för annonser:** personliga real tids push-annonser och erbjudanden, interaktiva annonser.
* **Samarbets program:** samprojektering, whiteboard-appar och team Mötes program.
* **Push-meddelanden:** sociala nätverk, e-post, spel, rese avisering.
* **Sändning i real tid:** direkt sändning av ljud/video, direkt textning, översättning, händelser/diskussions grupps sändningar.
* **IoT och anslutna enheter:** real tids statistik för IoT, fjärr styrning, real tids status och plats spårning.
* **Automation:** real tids utlösare från överordnade händelser.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Vad är fördelarna med att använda Azure SignalR-tjänsten?

**Standard-baserat:**

SignalR ger en abstrakt samling tekniker som används för att skapa webbappar av realtidstyp. [WebSockets](https://wikipedia.org/wiki/WebSocket) är den optimala transportmetoden, men andra metoder som [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) och lång avsökning används när andra alternativ inte är tillgängliga. SignalR identifierar och initierar automatiskt lämplig transportmetod baserat på de funktioner som stöds på servern och klienten.

**Stöd för inbyggd ASP.NET Core:**

SignalR-tjänsten tillhandahåller inbyggd programmering med ASP.NET Core-och ASP.NET. Det krävs minimalt arbete för att utveckla ett nytt signal program med signal tjänst eller migrering från befintlig Signalbaserade program till SignalR-tjänsten.
SignalR service stöder också ASP.NET Cores nya funktion, blixt på Server sidan.

**Omfattande klient support:**

SignalR-tjänsten fungerar med en mängd olika klienter, till exempel webb-och mobil webbläsare, skrivbordsappar, mobilappar, Server processer, IoT-enheter och spel konsoler. SignalR-tjänsten erbjuder SDK: er på olika språk. Förutom interna ASP.NET Core eller ASP.NET C# SDK: er tillhandahåller signal service även Java Script client SDK, för att aktivera webb klienter och många JavaScript-ramverk. Java-klient-SDK stöds också för Java-program, inklusive Android-inbyggda appar. SignalR service stöder REST API och Server lös genom integreringar med Azure Functions och Event Grid.

**Hantera storskaliga klient anslutningar:**

SignalR service är utformad för storskaliga program i real tid. Med SignalR service kan flera instanser fungera tillsammans för att skala till miljon tals klient anslutningar. Tjänsten stöder också flera globala regioner för horisontell partitionering, hög tillgänglighet eller katastrof återställning.

**Ta bort belastningen till självbetjänings signalering:**

Jämfört med egen värdbaserade SignalR-program, kan du växla till signal tjänsten för att ta bort behovet av att hantera back plan som hanterar skalningarna och klient anslutningarna. Den fullständigt hanterade tjänsten fören klar också webb program och sparar värd kostnader. SignalR service erbjuder global räckvidd och data Center och nätverk i världs klass, som kan skalas till miljon tals anslutningar, garanterar SLA och ger all efterlevnad och säkerhet på Azure standard.

![Hanterad signal tjänst](./media/signalr-overview/managed-signalr-service.png)

**Erbjud omfattande API: er för olika meddelande mönster:**

Med SignalR-tjänsten kan servern skicka meddelanden till en viss anslutning, alla anslutningar eller en delmängd av anslutningar som tillhör en viss användare eller har placerats i en godtycklig grupp.

## <a name="how-to-use-azure-signalr-service"></a>Så använder du Azure SignalR Service

Det finns många olika sätt att program med Azure SignalR service, som några av de exempel som visas här:

- **[Skala en ASP.NET Core SignalR-App](signalr-concept-scale-aspnet-core.md)** – Integrera Azure SignalR Service med ett ASP.NET Core SignalR-program för att skala ut till hundratusentals anslutningar.
- **[Skapa serverlösa realtidsappar](signalr-concept-azure-functions.md)** – Använd Azure Functions-integrering med Azure SignalR Service för att skapa serverlösa realtidsprogram med språk som JavaScript, C# och Java.
- **[Skicka meddelanden från servern till klienter via REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** – Azure SignalR Service har REST API så att program kan skicka meddelanden till klienter som är anslutna med SignalR Service i alla REST-kompatibla programmeringsspråk.
