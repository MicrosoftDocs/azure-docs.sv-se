---
title: Meddelanden och anslutningar i Azure SignalR Service
description: En översikt över viktiga begrepp gällande meddelanden och anslutningar i Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: zhshang
ms.openlocfilehash: 3c4d28addac0ecfc9605678582562550a1c96b8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491953"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Meddelanden och anslutningar i Azure SignalR Service

Faktureringsmodellen för Azure SignalR Service baseras på antalet anslutningar och antalet meddelanden. I den här artikeln förklaras hur meddelanden och anslutningar definieras och räknas.


## <a name="message-formats"></a>Meddelandeformat 

Azure SignalR service stöder samma format som ASP.NET Core Signalerare: [JSON](https://www.json.org/) och [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Meddelandestorlek

Azure SignalR Service har ingen storleksgräns för meddelanden.

Stora meddelanden delas upp i mindre meddelanden på högst 2 KB var som överförs separat. SDK:er hanterar delning och sammansättning av meddelanden. Inget utvecklararbete krävs.

Stora meddelanden påverkar meddelandeprestanda negativt. Använd mindre meddelanden när det är möjligt och testa dig fram till den optimala meddelandestorleken för varje användningsfall.

## <a name="how-messages-are-counted-for-billing"></a>Så beräknas meddelanden för fakturering

För fakturering räknas endast utgående meddelanden från Azure SignalR Service. Pingmeddelanden mellan klienter och servrar ignoreras.

Meddelanden som är större än 2 KB räknas som flera meddelanden på 2 KB vartdera. Diagrammet över antal meddelanden i Azure-portalen uppdateras vart 100:e meddelande för varje hubb.

Anta till exempel att du har en program Server och tre klienter:

App server skickar ett meddelande på 1 KB till alla anslutna klienter. meddelandet från App Server till tjänsten betraktas som ett kostnads fritt inkommande meddelande. Endast de tre meddelanden som skickas från tjänst till varje klient faktureras som utgående meddelanden.

Klient A skickar ett meddelande på 1 KB till en annan klient B, utan att gå via app server. Meddelandet från klient A till tjänst är ett kostnads fritt inkommande meddelande. Meddelandet från tjänst till klient B faktureras som utgående meddelande.

Om du har tre klienter och en program Server. En klient skickar ett meddelande på 4 KB som servern ska skicka till alla klienter. Antalet fakturerade meddelanden är åtta: ett meddelande från tjänsten till program servern och tre meddelanden från tjänsten till klienterna. Varje meddelande räknas som två meddelanden på 2 KB vartdera.

## <a name="how-connections-are-counted"></a>Så räknas anslutningar

Det finns Server anslutningar och klient anslutningar med Azure SignalR-tjänsten. Som standard startar varje program server med fem första anslutningar per hubb och varje klient har en klient anslutning.

Anta till exempel att du har två program servrar och att du definierar fem hubbar i kod. Antalet Server anslutningar är 50:2 App-servrar * 5 hubbar * 5 anslutningar per hubb.

Antalet anslutningar som visas i Azure Portal inkluderar server anslutningar, klient anslutningar, diagnostiska anslutningar och direktsända spårnings anslutningar. Anslutnings typerna definieras i följande lista:

- **Server anslutning**: ansluter Azure SignalR-tjänsten och App Server.
- **Klient anslutning**: ansluter Azure SignalR-tjänsten och klient programmet.
- **Diagnostisk anslutning**: en särskild typ av klient anslutning som kan skapa en mer detaljerad logg, vilket kan påverka prestandan. Den här typen av klient är utformad för fel sökning.
- **Direkt spårnings anslutning**: ansluter till Live Trace-slutpunkten och tar emot real tids spår för Azure SignalR-tjänsten. 
 
Observera att en direkt spårnings anslutning inte räknas som en klient anslutning eller som en server anslutning. 

ASP.NET SignalR beräknar serveranslutningar på ett annat sätt. Det omfattar en standardhubb utöver de hubbar som du definierar. Som standard behöver varje program Server fem fler inledande Server anslutningar. Det första antalet anslutningar för standard navet förblir konsekvent med andra hubbar.

Tjänsten och program servern håller på att synkronisera anslutnings status och gör justeringar till Server anslutningar för att få bättre prestanda och service stabilitet.  Så du kan se ändringar av serverns anslutnings nummer från tid till tid.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Så räknas inkommande/utgående trafik

Meddelandet som skickas till tjänsten är ett inkommande meddelande. Meddelande som skickats ut från tjänsten är utgående meddelande. Trafik beräknas i byte.

## <a name="related-resources"></a>Relaterade resurser

- [Sammansättningstyper i Azure Monitor](../azure-monitor/essentials/metrics-supported.md#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR-konfiguration](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
