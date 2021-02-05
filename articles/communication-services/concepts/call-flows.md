---
title: Anropa flöden i Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om samtals flöden i Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 410f8ab4de0d93262647cbc07e0792cd39f7a844
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593645"
---
# <a name="call-flows"></a>Samtalsflöden

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Avsnittet nedan ger en översikt över samtals flödena i Azure Communication Services. Signalering och medie flöden beror på vilka typer av anrop som användarna gör. Exempel på samtals typer är ett-till-ett-VoIP, ett-till-ett-PSTN och grupp samtal som innehåller en kombination av VoIP-och PSTN-anslutna deltagare. Granska [samtals typer](./voice-video-calling/about-call-types.md).

## <a name="about-signaling-and-media-protocols"></a>Om signal-och medie protokoll

När du etablerar ett peer-to-peer-eller grupp anrop används två protokoll i bakgrunden-HTTP (REST) för signalering och SRTP för media. 

Signalering mellan klient biblioteken eller mellan klient bibliotek och kommunikations tjänst styrenheter hanteras med HTTP REST (TLS). För Real-Time Media trafik (RTP) rekommenderas UDP (User Datagram Protocol). Om användningen av UDP förhindras av brand väggen använder klient biblioteket Transmission Control Protocol (TCP) för media. 

Vi går igenom signal-och medie protokollen i olika scenarier. 

## <a name="call-flow-cases"></a>Ärende om samtals flöde

### <a name="case-1-voip-where-a-direct-connection-between-two-devices-is-possible"></a>Fall 1: VoIP där en direkt anslutning mellan två enheter är möjlig

I en-till-ett VoIP-eller video samtal föredrar trafiken den mest direkta sökvägen. "Direkt sökväg" innebär att om två klient bibliotek kan komma åt varandra direkt upprättar de en direkt anslutning. Detta är vanligt vis möjligt när två klient bibliotek finns i samma undernät (t. ex. i ett undernät 192.168.1.0/24) eller två när enheterna varje Live i undernät som kan se varandra (klient bibliotek i undernät 10.10.0.0/16 och 192.168.1.0/24 kan kontakta varandra).

:::image type="content" source="./media/call-flows/about-voice-case-1.png" alt-text="Diagram som visar ett direkt VOIP-anrop mellan användare och kommunikations tjänster.":::

### <a name="case-2-voip-where-a-direct-connection-between-devices-is-not-possible-but-where-connection-between-nat-devices-is-possible"></a>Fall 2: VoIP där det inte går att ansluta mellan enheter, men där det går att ansluta mellan NAT-enheter

Om två enheter finns i undernät som inte kan komma åt varandra (till exempel Alice arbetar från ett kafé och Bob arbetar från sitt hem kontor), men anslutningen mellan NAT-enheterna är möjlig, upprättar klient bibliotek på klient sidan anslutning via NAT-enheter. 

För Alice kommer det att vara NAT för kaféet och för Bob kommer det att bli NAT för hem kontoret. Alices enhet kommer att skicka den externa adressen till sin NAT och Bob gör samma sak. Klient biblioteken lär sig de externa adresserna från en STUN (sessions Traversal-verktyg för NAT) som Azure Communication Services erbjuder kostnads fritt. Den logik som hanterar hand skakningen mellan Alice och Robert är inbäddad i Azure Communication-tjänsterna angivna klient bibliotek. (Du behöver ingen ytterligare konfiguration)

:::image type="content" source="./media/call-flows/about-voice-case-2.png" alt-text="Diagram som visar ett VOIP-anrop som använder en STUN-anslutning.":::

### <a name="case-3-voip-where-neither-a-direct-nor-nat-connection-is-possible"></a>Fall 3: VoIP, där varken en direkt-eller NAT-anslutning är möjlig

Om en eller båda klient enheterna ligger bakom en symmetrisk NAT, krävs en separat moln tjänst för att vidarebefordra mediet mellan de två klient biblioteken. Den här tjänsten kallas TURN (Traversal med reläer runt NAT) och tillhandahålls också av kommunikations tjänsterna. Kommunikations tjänsterna som anropar klient biblioteket använder automatiskt TURN-tjänster baserat på identifierade nätverks förhållanden. Användningen av Microsofts tjänst för aktivering debiteras separat.

:::image type="content" source="./media/call-flows/about-voice-case-3.png" alt-text="Diagram som visar ett VOIP-anrop som använder en TURN-anslutning.":::
 
### <a name="case-4-group-calls-with-pstn"></a>Fall 4: gruppera samtal med PSTN

Både signal-och medie för PSTN-anrop använder telefoni resursen Azure Communication Services. Den här resursen är kopplad till andra operatörer.

PSTN-medie trafik flödar genom en komponent som kallas medie processor.

:::image type="content" source="./media/call-flows/about-voice-pstn.png" alt-text="Diagram som visar ett PSTN-gruppanrop med kommunikations tjänster.":::

> [!NOTE]
> För de som är bekanta med medie bearbetning är vår medie processor också tillbaka till användar agenten, enligt definitionen i [RFC 3261 SIP: sessions initierings protokoll](https://tools.ietf.org/html/rfc3261), vilket innebär att det kan översätta codecenheter vid hantering av anrop mellan Microsoft-och transport företags nätverk. Signal hanteraren för Azure Communication Services är Microsofts implementering av en SIP-proxy enligt samma RFC.

För grupp anrop flödar media och signalerar alltid via server delen för Azure Communication Services. Ljudet och/eller videon från alla deltagare är blandade i medie processor komponenten. Alla medlemmar i ett grupp samtal skickar sina ljud-och/eller video strömmar till medie processorn, vilket returnerar blandade medie strömmar.

Standardvärdet för real tids protokoll (RTP) för grupp anrop är UDP (User Datagram Protocol).

> [!NOTE]
> Medie processorn kan fungera som en Multipoint Control Unit (MCU) eller en selektiv vidarebefordrings enhet (SFU)

:::image type="content" source="./media/call-flows/about-voice-group-calls.png" alt-text="Diagram över UDP-medie process flöde inom kommunikations tjänsterna.":::

Om klient biblioteket inte kan använda UDP för media på grund av brand Väggs begränsningar, görs ett försök att använda Transmission Control Protocol (TCP). Observera att medie processor komponenten kräver UDP, så när detta inträffar läggs tjänsten för kommunikations tjänster till i grupp anropet för att översätta TCP till UDP. Om du gör det inaktive ras avgifterna i det här fallet om du inte inaktiverar funktionerna manuellt.

:::image type="content" source="./media/call-flows/about-voice-group-calls-2.png" alt-text="Diagram över TCP-medie process flöde inom kommunikations tjänsterna.":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med att anropa](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Följande dokument kan vara intressanta för dig:

- Läs mer om [samtals typer](../concepts/voice-video-calling/about-call-types.md)
- Lär dig mer om [klient-server arkitektur](./client-and-server-architecture.md)
