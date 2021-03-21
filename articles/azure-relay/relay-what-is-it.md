---
title: Vad är Azure Relay? | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure Relay-tjänsten, där du kan utveckla molnprogram som använder lokala tjänster som körs i företagets nätverk utan att öppna en brandväggsanslutning eller göra störande ändringar i din nätverksinfrastruktur.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: fbf1b2134a4c2dce7a3e6a62668d0852dc08c18a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97955390"
---
# <a name="what-is-azure-relay"></a>Vad är Azure Relay?
Med Azure Relay-tjänsten kan du på ett säkert sätt visa tjänster som körs i företagets nätverk för det offentliga molnet. Du kan göra det utan att behöva öppna en port i brandväggen eller göra störande ändringar i företagets nätverksinfrastruktur. 

Relay-tjänsten har stöd för följande scenarier mellan lokala tjänster och program som körs i molnet eller i en annan lokal miljö. 

- Traditionell enkelriktad trafik för begäranden/svar och peer-to-peer-kommunikation 
- Händelsedistribution på Internet-skala för att möjliggöra scenarier för publicering/prenumeration 
- Dubbelriktad och unbuffed socket-kommunikation över nätverks gränser

Azure Relay skiljer sig från integreringstekniker på nätverksnivå såsom VPN. Ett Azure Relay kan få omfång till en enskild programslutpunkt på en enda dator. VPN-teknik är mycket mer inkräktande eftersom den förlitar sig på att ändra nätverksmiljön. 

## <a name="basic-flow"></a>Grundläggande flöde
I det vidarebefordrade dataöverföringsmönstret omfattar de grundläggande stegen:

1. En lokal tjänst ansluter till den vidarebefordrande tjänsten via en utgående port. 
2. Den skapar en dubbelriktad socket för kommunikation som är kopplad till en viss adress. 
3. Klienten kan sedan kommunicera med den lokala tjänsten genom att skicka trafik till den vidarebefordrande tjänst som har den adressen som mål. 
4. Den vidarebefordrande tjänsten *vidarebefordrar* sedan data till den lokala tjänsten via den dubbelriktade socket som är dedikerad för klienten. Klienten behöver inte en direktanslutning till den lokala tjänsten. Den behöver inte känna till tjänstens plats. Och den lokala tjänsten behöver inte några ingående portar öppna i brandväggen.


## <a name="features"></a>Funktioner 
Azure Relay erbjuder två funktioner:

- [Hybridanslutningar](#hybrid-connections) – Använder den öppna standarden Web Sockets som ger stöd för scenarier med flera plattformar.
- WCF-reläer – Använder Windows Communication Foundation (WCF) för att aktivera RPC-anrop. WCF Relay är den äldre Relay-tjänsten som många kunder redan använder med sina WCF-programmeringsmodeller.

## <a name="hybrid-connections"></a>Hybridanslutningar

Funktionen Hybridanslutningar i Azure Relay är en säker utveckling med öppet protokoll av de Relay-funktioner som fanns tidigare. Du kan använda den på valfri plattform och på valfritt språk. Funktionen Hybridanslutningar i Azure Relay är baserad på protokollen HTTP och WebSockets. Den gör att du kan skicka begäranden och ta emot svar via webbsocketar eller HTTP(S). Den här funktionen är kompatibel med WebSocket API i vanliga webbläsare. 

Mer information om protokollet för Hybridanslutningar finns i [guiden om protokollet för Hybridanslutningar](relay-hybrid-connections-protocol.md). Du kan använda Hybridanslutningar med valfritt websocketbibliotek för alla körningar/språk.

> [!NOTE]
> Hybridanslutningar i Azure Relay ersätter den gamla hybridanslutningsfunktionen i BizTalk Services. Funktionen för Hybridanslutningar i BizTalk Services skapades på Azure Service Bus WCF Relay. Funktionen för Hybridanslutningar i Azure Relay kompletterar den befintliga WCF Relay-funktionen. De här två tjänstfunktionerna (WCF Relay och Hybridanslutningar) finns sida vid sida i Azure Relay-tjänsten. De delar en gemensam gateway, men är i övrigt olika implementeringar.

## <a name="wcf-relay"></a>WCF-relä
WCF Relay fungerar med det fullständiga .NET Framework (NETFX) och för WCF. Du skapar en anslutning mellan din lokala tjänst och den vidarebefordrande tjänsten med hjälp av en uppsättning ”vidarebefordrande” WCF-bindningar. I bakgrunden mappas vidarebefordringsbindningarna till nya transportbindningselement som är utformade för att skapa WCF-kanalkomponenter som integreras med Service Bus i molnet. Mer information finns i [Komma igång med WCF Relay](service-bus-relay-tutorial.md).

## <a name="hybrid-connections-vs-wcf-relay"></a>Hybridanslutningar jämfört med WCF Relay
Både Hybridanslutningar och WCF Relay tillhandahåller säker anslutning till resurser som finns i ett företagsnätverk. Användning av det ena eller andra beror på dina specifika behov, baserat på informationen i följande tabell:

|  | WCF-relä | Hybridanslutningar |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **Java Script/Node.js** | |x |
| **Standardbaserat öppet protokoll** | |x |
| **RPC-programmeringsmodeller** | |x |

## <a name="architecture-processing-of-incoming-relay-requests"></a>Arkitektur: Bearbetning av inkommande vidarebefordrade begäranden
Följande diagram visar hur inkommande reläbegäranden hanteras av Azure Relay-tjänsten:

![Bearbetning av inkommande WCF Relay-begäranden](./media/relay-what-is-it/ic690645.png)

1. Den lyssnande klienten skickar en lyssningsbegäran till Azure Relay-tjänsten. Azure-lastbalanseraren dirigerar begäran till någon av gatewaynoderna. 
2. Azure Relay-tjänsten skapar ett relä i gatewayarkivet. 
3. Den skickande klienten skickar en anslutningsbegäran till den lyssnande tjänsten. 
4. Den gateway som tar emot begäran söker efter reläet i gatewayarkivet. 
5. Gatewayen vidarebefordrar anslutningsbegäran till rätt gateway som nämns i gatewayarkivet. 
6. Gatewayen skickar en begäran till den lyssnande klienten om att skapa en tillfällig kanal till den gatewaynod som är närmast den skickande klienten. 
7. Den lyssnande klienten skapar en tillfällig kanal till den gateway som är närmast den skickande klienten. Nu när anslutningen har upprättats mellan klienter via en gateway kan klienterna utbyta meddelanden med varandra. 
8. Gatewayen vidarebefordrar alla meddelanden från lyssnings klienten till den sändande klienten. 
9. Gatewayen vidarebefordrar alla meddelanden från den skickande klienten till den lyssnande klienten.  

## <a name="next-steps"></a>Nästa steg
* [Komma igång med .NET Websockets](relay-hybrid-connections-dotnet-get-started.md)
* [Komma igång med .NET HTTP-begäranden](relay-hybrid-connections-http-requests-dotnet-get-started.md)
* [Komma igång med Node Websockets](relay-hybrid-connections-node-get-started.md)
* [Komma igång med Node HTTP-begäranden](relay-hybrid-connections-http-requests-node-get-started.md)
* [Vanliga frågor och svar om Relay](relay-faq.md)

