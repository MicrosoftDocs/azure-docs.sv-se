---
title: Anropa Flow-topologier i Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om att anropa Flow-topologier i Azure Communication Services.
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 526e3a1e4eeb6ef6a31a33498241d9a7443cca35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490644"
---
# <a name="call-flow-topologies"></a>Topologier för anrops flöden
Den här artikeln beskriver topologier för Azure Communication Services-anrops flöden. Det här är en bra artikel för att se om du är företags kund som integrerar kommunikations tjänster i ett nätverk som du hanterar. En introduktion till kommunikations tjänstens samtals flöden finns i [konceptuell dokumentation om samtals flöden](./call-flows.md).

## <a name="background"></a>Bakgrund

### <a name="network-concepts"></a>Nätverks koncept

Innan du granskar topologier för anrops flöden definierar vi några termer som refereras till i hela dokumentet.

Ett **kund nätverk** innehåller alla nätverks segment som du hanterar. Detta kan omfatta kabelanslutna och trådlösa nätverk på kontoret eller mellan kontor, data Center och Internet leverantörer.

Ett kund nätverk har vanligt vis flera nätverks-perimeter med brand väggar och/eller proxyservrar som upprätthåller din organisations säkerhets principer. Vi rekommenderar att du utför en [omfattande nätverks utvärdering](/microsoftteams/3-envision-evaluate-my-environment) för att säkerställa optimala prestanda och kvalitet på kommunikations lösningen.

**Kommunikations tjänst nätverket** är det nätverks segment som stöder Azure Communication Services. Det här nätverket hanteras av Microsoft och distribueras över hela världen med kanter nära de flesta kund nätverk. Det här nätverket ansvarar för transport relä, medie bearbetning för grupp anrop och andra komponenter som stöder omfattande real tids medie kommunikation.

### <a name="types-of-traffic"></a>Typer av trafik

Kommunikations tjänster skapas främst på två typer av trafik: **real tids medier** och **signalering**.

**Real tids medier** överförs med real tids transport protokollet (RTP). Det här protokollet stöder ljud-, video-och skärm delning data överföring. Dessa data är känsliga för problem med nätverks fördröjning. Även om det är möjligt att överföra real tids medier med TCP eller HTTP rekommenderar vi att du använder UDP som transport lager protokoll för att ge stöd för slut användar upplevelser med hög prestanda. Medie nytto laster som skickas över RTP skyddas med SRTP.

Användare av kommunikations tjänst lösningen kommer att ansluta till dina tjänster från sina klient enheter. Kommunikationen mellan enheterna och dina servrar hanteras med **signalering**. Exempel: samtals initiering och real tids chatt stöds genom att signalera mellan enheter och din tjänst. Den flesta signal trafik använder HTTPS-REST, men i vissa fall kan SIP användas som Signaling av trafik protokoll. Även om den här typen av trafik är mindre känslig för svars tid, kommer en varning med låg latens användare av din lösning att Pleasant slutanvändarens upplevelse.

### <a name="interoperability-restrictions"></a>Begränsningar för samverkan

Media som passerar genom kommunikations tjänster begränsas enligt följande:

**Medie reläer från tredje part.** Samverkan med en tredje parts medie relä stöds inte. Om någon av dina medie slut punkter är kommunikations tjänster, kan Media bara gå igenom Microsofts inbyggda medie reläer, inklusive de som stöder Microsoft Teams och Skype för företag.

En SBC (Borders Border Controller) på gränsen med PSTN bör avsluta RTP/RTCP-dataströmmen, som skyddas med SRTP och inte vidarebefordrar den till nästa hopp. Om du vidarebefordrar flödet till nästa hopp kanske det inte tolkas.

**SIP-proxyservrar från tredje part.** Kommunikations tjänster som signalerar SIP-dialogrutan med en SBC från tredje part och/eller gateway kan gå igenom Microsofts interna SIP-proxyservrar (precis som Team). Samverkan med SIP-proxyservrar från tredje part stöds inte.

**B2BUA från tredje part (eller SBC).** Kommunikations tjänsternas medie flöde till och från PSTN avslutas av en SBC från tredje part. Samverkan med en SBC från tredje part i kommunikations tjänst nätverket (där en SBC från tredje part är ett stöd för två kommunikations tjänst slut punkter) stöds inte.

### <a name="unsupported-technologies"></a>Tekniker som inte stöds

**VPN-nätverk.** Kommunikations tjänsterna stöder inte medie överföring via VPN. Om användarna använder VPN-klienter bör klienten dela och dirigera medie trafik över en icke-VPN-anslutning som anges i [aktivera Lync-media för att kringgå en VPN-tunnel.](https://techcommunity.microsoft.com/t5/skype-for-business-blog/enabling-lync-media-to-bypass-a-vpn-tunnel/ba-p/620210)

*Lägg. Även om rubriken visar Lync, gäller Azure Communication Services och Teams.*

**Paket formers.** Paket klippning, paket inspektion eller paket formnings enheter stöds inte och kan försämra kvaliteten markant.

### <a name="call-flow-principles"></a>Principer för anrops flöden

Det finns fyra allmänna principer som underhåller kommunikations tjänsternas anrops flöden:

* **Den första deltagaren i ett kommunikations tjänst grupp anrop bestämmer den region där anropet finns**. Det finns undantag till den här regeln i vissa topologier, som beskrivs nedan.
* **Den medie slut punkt som används för att stödja ett kommunikations tjänst samtal väljs utifrån medie bearbetnings behoven** och påverkas inte av antalet deltagare i ett samtal. Till exempel kan ett punkt-till-punkt-anrop använda en medie slut punkt i molnet för att bearbeta medier för avskrift eller inspelning, medan ett samtal med två deltagare inte kan använda några medie slut punkter. Grupp anrop använder en medie slut punkt för att blanda och dirigera. Den här slut punkten väljs baserat på regionen där anropet finns. Medie trafik som skickas från en klient till medie slut punkten kan dirigeras direkt eller också kan den använda ett transport relä i Azure om begränsningar för kund nätverks brand väggen kräver det.
* **Medie trafik för peer-to-peer-anrop använder den mest direkta vägen som är tillgänglig**, förutsatt att anropet inte behöver en medie slut punkt i molnet. Den önskade vägen är direkt till fjärr-peer (klienten). Om en direkt väg inte är tillgänglig, kommer en eller flera transport reläer att vidarebefordra trafiken. Medie trafiken ska inte vara transversella servrar som fungerar som paket former, VPN-servrar eller andra funktioner som kan fördröja bearbetningen och försämra slut användar upplevelsen.
* **Signal trafik skickas alltid till den server som är närmast användaren.**

Mer information om den valda medie Sök vägen finns i [dokumentationen om samtals flöden](./call-flows.md).


## <a name="call-flows-in-various-topologies"></a>Anropa flöden i olika topologier

### <a name="communication-services-internet"></a>Kommunikations tjänster (Internet)

Den här topologin används av kunder som använder kommunikations tjänster från molnet utan någon lokal distribution, t. ex. SIP-gränssnitt. I den här topologin flödar trafik till och från kommunikations tjänsterna via Internet.

:::image type="content" source="./media/call-flows/detailed-flow-general.png" alt-text="Azure Communication Services-topologi.":::

*Bild 1 – topologi för kommunikations tjänster*

Pilens riktning i diagrammet ovan återspeglar initierings riktningen för den kommunikation som påverkar anslutningen till företagets perimeter. Om det gäller UDP för media kan de första paketen flöda i omvänd riktning, men paketen kan blockeras tills paket i den andra riktningen flödar.

Flödes beskrivningar:
* Flow 2 * – representerar ett flöde som initieras av en användare på kund nätverket till Internet som en del av användarens kommunikations tjänst upplevelse. Exempel på de här flödena är bland annat överföring av DNS-och peer-till-peer-medier.
* Flow 2 – representerar ett flöde som initieras av en fjärran sluten mobil kommunikations tjänst användare, med VPN till kund nätverket.
* Flow 3 – representerar ett flöde som initierats av en fjärran sluten Mobile Communication Services-användare till kommunikations tjänsternas slut punkter.
* Flow 4 – representerar ett flöde som initierats av en användare på kund nätverket till kommunikations tjänster.
* Flow 5 – representerar ett peer-till-peer-medium mellan en kommunikations tjänst användare och en annan i kundens nätverk.
* Flow 6 – representerar ett peer-till-peer-medium mellan en fjärran sluten mobil kommunikations tjänst användare och en annan fjärran sluten mobil kommunikations tjänst användare via Internet.

### <a name="use-case-one-to-one"></a>Användnings fall: en-till-en

Ett-till-ett-anrop använder en gemensam modell där anroparen får en uppsättning kandidater som består av IP-adresser/portar, inklusive lokal, relä och reflexiv (offentlig IP-adress för klienten som visas av reläet). Anroparen skickar dessa kandidater till den anropade parten. den anropade parten får också en liknande uppsättning kandidater och skickar dem till anroparen. STUN anslutnings kontroll meddelanden används för att ta reda på vilka anropare/-som kallas för parts medie sökvägar och den bästa arbets vägen är markerad. Media (dvs. RTP/RTCP-paket som skyddas med SRTP) skickas sedan med det valda kandidat paret. Transport reläet distribueras som en del av Azure Communication Services.

Om den lokala IP-adressen/port kandidater eller reflexiv kandidater har anslutning, väljs den direkta sökvägen mellan klienterna (eller med hjälp av en NAT) för media. Om klienterna både är i kund nätverket, ska den direkta sökvägen väljas. Detta kräver direkt UDP-anslutning i kundens nätverk. Om klienterna både är Nomadic moln användare, och beroende på NAT/brand vägg, kan Media använda direkt anslutning.

Om en klient är intern i kundens nätverk och en klient är extern (till exempel en mobil moln användare), är det osannolikt att direkt anslutning mellan de lokala eller reflexiva kandidaterna fungerar. I det här fallet är ett alternativ att använda en av transport relä kandidater från antingen klienten (till exempel den interna klienten hämtade en relä kandidat från transport reläet i Azure. den externa klienten måste kunna skicka STUN/RTP/RTCP-paket till transport reläet). Ett annat alternativ är att den interna klienten skickar till den relä kandidat som erhållits av den mobila moln klienten. Även om UDP-anslutning för media rekommenderas, stöds TCP.

**Steg på hög nivå:**
1.  Kommunikation Services-användare A matchar URL-domännamn (DNS) med Flow 2.
2.  Användare A allokerar en Media Relay-port i team transport reläet med Flow 4.
3.  Kommunikation tjänster användare A skickar en "inbjudan" med ICE-kandidater som använder Flow 4 till kommunikations tjänster.
4.  Kommunikations tjänsterna meddelar användare B genom att använda Flow 4.
5.  Användare B allokerar en Media Relay-port i team transport Relay som använder Flow 4.
6.  Användare B skickar "svar" med ICE-kandidater som använder Flow 4, som vidarebefordras tillbaka till användare A med Flow 4.
7.  Användare A och användare B anropar ICE-anslutnings test och den bästa tillgängliga medie Sök vägen har valts (se diagram nedan för olika användnings fall).
8.  Båda användarna skickar telemetri till kommunikations tjänster med Flow 4.

### <a name="customer-network-intranet"></a>Kund nätverk (intranät)

:::image type="content" source="./media/call-flows/one-to-one-internal.png" alt-text="Trafikflöde i kundens nätverk.":::

*Figur 2 – inom kund nätverk*

I steg 7 väljs peer-to-peer Media Flow 5.

Denna medie överföring är dubbelriktad. Riktningen för Flow 5 indikerar att en sida initierar kommunikationen från ett anslutnings perspektiv. I det här fallet spelar det ingen roll vilken riktning som används eftersom båda slut punkterna är i kundens nätverk.

### <a name="customer-network-to-external-user-media-relayed-by-teams-transport-relay"></a>Kund nätverk till extern användare (media som vidarebefordras av team transport Relay)

:::image type="content" source="./media/call-flows/one-to-one-via-relay.png" alt-text="Ett till ett samtals flöde via ett relä.":::

*Bild 3 – kund nätverk till extern användare (media som vidarebefordras av Azure transport Relay)*

I steg 7, är flöde 4 (från kund nätverket till kommunikations tjänster) och flöde 3 (från en fjärran sluten mobil kommunikations tjänst användare till Azure Communication Services) markerade.

Dessa flöden vidarebefordras av team transport relay i Azure.

Denna medie överföring är dubbelriktad. Riktningen anger vilken sida som initierar kommunikationen från ett anslutnings perspektiv. I det här fallet används dessa flöden för signalering och media, med olika transport protokoll och-adresser.

### <a name="customer-network-to-external-user-direct-media"></a>Kund nätverk till extern användare (direkt Media)

:::image type="content" source="./media/call-flows/one-to-one-with-extenal.png" alt-text="Ett till ett anrops flöde med en extern användare.":::

*Bild 4 – kund nätverk till extern användare (direkt Media)*

I steg 7 väljs flöde 2 (från kund nätverket till klientens peer via Internet).

Direkt mediet med en fjärran sluten mobil användare (som inte vidarebefordras via Azure) är valfritt. Med andra ord kan du blockera sökvägen för att genomdriva en medie Sök väg genom ett transport relä i Azure.

Denna medie överföring är dubbelriktad. Riktningen för flöde 2 till fjärran sluten mobil användare anger att en sida initierar kommunikationen från ett anslutnings perspektiv.

### <a name="vpn-user-to-internal-user-media-relayed-by-teams-transport-relay"></a>VPN-användare till intern användare (media som vidarebefordras av team transport Relay)

:::image type="content" source="./media/call-flows/vpn-to-internal-via-relay.png" alt-text="Ett till ett anrops flöde med en VPN-användare via reläet.":::

*Bild 5 – VPN-användare till intern användare (medie vidarebefordras av Azure Relay*

Signalering mellan VPN-nätverket och kund nätverket använder Flow 2 *. Signalering mellan kund nätverket och Azure använder Flow 4. Mediet kringgår dock VPN och dirigeras med flöden 3 och 4 till Azure Media Relay.

### <a name="vpn-user-to-internal-user-direct-media"></a>VPN-användare till intern användare (direkt Media)

:::image type="content" source="./media/call-flows/vpn-to-internal-direct-media.png" alt-text="Ett till ett samtals flöde (intern användare) med ett VPN med direkt Media":::

*Bild 6 – VPN-användare till intern användare (direkt Media)*

Signalering mellan VPN-nätverket och kund nätverket använder Flow 2. Signalera mellan kund nätverket och Azure använder Flow 4. Mediet kringgår dock inte VPN och dirigeras med hjälp av Flow 2 från kund nätverket till Internet.

Denna medie överföring är dubbelriktad. Riktningen för Flow 2 till fjärran sluten mobil användare anger att en sida initierar kommunikationen från ett anslutnings perspektiv.

### <a name="vpn-user-to-external-user-direct-media"></a>VPN-användare till extern användare (direkt Media)

:::image type="content" source="./media/call-flows/vpn-user-to-external-user.png" alt-text="Ett till ett samtals flöde (extern användare) med ett VPN med direkt Media":::

*Bild 7 – VPN-användare till extern användare (direkt Media)*

Signalering mellan VPN-användaren för kund nätverket använder Flow 2 * och Flow 4 till Azure. Mediet kringgår dock VPN och dirigeras med hjälp av Flow 6.

Denna medie överföring är dubbelriktad. Riktningen för Flow 6 till fjärran sluten mobil användare anger att en sida initierar kommunikationen från ett anslutnings perspektiv.

### <a name="use-case-communication-services-client-to-pstn-through-communication-services-trunk"></a>Användnings fall: kommunikations tjänsters klient till PSTN genom trunk för kommunikations tjänster

Kommunikations tjänster gör det möjligt att placera och ta emot samtal från det offentliga switchade telefonnätet (PSTN). Om PSTN-trunken är ansluten med telefonnummer från kommunikations tjänsterna finns det inga särskilda anslutnings krav för det här användnings fallet. Om du vill ansluta din egen lokala PSTN-trunk till Azure Communication Services kan du använda SIP-gränssnittet (tillgängligt i CY2021).

:::image type="content" source="./media/call-flows/acs-to-pstn.png" alt-text="Ett till ett samtal med en PSTN-deltagare":::

*Figur 8 – kommunikations tjänst användare till PSTN genom Azure-trunkering*

I det här fallet signalerar och media från kund nätverket till Azure använder du Flow 4.

### <a name="use-case-communication-services-group-calls"></a>Användnings fall: kommunikations tjänster grupp anrop

Tjänsten ljud/video-/skärm delning (VBSS) är en del av Azures kommunikations tjänster. Den har en offentlig IP-adress som måste gå att komma åt från kundens nätverk och måste kunna användas från en Nomadic moln klient. Varje klient/slut punkt måste kunna ansluta till tjänsten.

Interna klienter får lokala, reflexiva och relä kandidater på samma sätt som beskrivs för ett-till-ett-anrop. Klienterna skickar dessa kandidater till tjänsten i en inbjudan. Tjänsten använder inte ett relä eftersom den har en offentligt nåbar IP-adress, så den svarar med sin lokala IP-adress kandidat. Klienten och tjänsten kontrollerar anslutningen på samma sätt som beskrivs för ett-till-ett-anrop.

:::image type="content" source="./media/call-flows/acs-group-calls.png" alt-text="OACS grupp anrop":::

*Bild 9 – grupp anrop för kommunikations tjänster*

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med att anropa](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Följande dokument kan vara intressanta för dig:

- Läs mer om [samtals typer](../concepts/voice-video-calling/about-call-types.md)
- Lär dig mer om [klient-server arkitektur](./client-and-server-architecture.md)