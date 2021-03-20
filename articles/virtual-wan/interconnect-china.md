---
title: Samman koppling med Kina med Azure Virtual WAN och säker hubb
description: Lär dig att koppla samman med Kina med Azure Virtual WAN och en säker hubb.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: sukishen
ms.openlocfilehash: 169477ffdf6fd593f1b6f01469c7303f8bd3a488
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96531850"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Samman koppling med Kina med Azure Virtual WAN och säker hubb

När du tittar på vanliga bil-, tillverknings-och logistik branscher eller andra institut som embassies, finns det ofta frågor om hur du kan förbättra samtrafik med Kina. Dessa förbättringar är mest relevanta för att använda Cloud Services som Microsoft 365, Azures globala tjänster eller sammankoppla grenar inom Kina med ett stamnät för kunden.

I de flesta fall är kunderna kämpar med hög latens, låg bandbredd, instabil anslutning och höga kostnader som ansluter till utanför Kina (till exempel Europa eller USA).

En orsak till de här problemen är den "fantastiska brand väggen i Kina", som skyddar den kinesiska delen av Internet och filtrerar trafik till Kina. Nästan all trafik som körs från Folkrepubliken Kina till utanför Kina, förutom de särskilda administrations zonerna, t. ex. Hongkong och Macao, klarar den fantastiska brand väggen. Trafiken som körs via Hong Kong och Macao når inte den fantastiska brand väggen i full kraft, den hanteras av en del av den fantastiska brand väggen.

:::image type="content" source="./media/interconnect-china/provider.png" alt-text="Diagrammet visar Provider-Interconnect.":::

Med hjälp av Virtual WAN kan en kund upprätta en mer genomförd och stabil anslutning till Microsoft Cloud tjänster och en anslutning till företagets nätverk utan att behöva dela den kinesiska cybersäkerhet lagen.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Krav och arbets flöde

Om du vill vara kompatibel med den kinesiska cybersäkerhet lagen måste du uppfylla en uppsättning särskilda villkor.

Först måste du arbeta tillsammans med ett nätverk och en ISP som äger en ICP-licens (Internet Content Provider) för Kina. I de flesta fall får du en av följande providers:

* Kina, telekom, global Ltd.
* Kina, Mobile Ltd.
* Kina Unicom Ltd.
* PCCW global Ltd.
* Hong Kong telekom Ltd.

Beroende på leverantören och dina behov måste du nu köpa en av följande tjänster för nätverks anslutning för att koppla dina grenar i Kina.

* Ett MPLS-/IPVPN-nätverk 
* En programdefinierad WAN-Server (SDWAN)
* Dedikerad Internet åtkomst

Därefter måste du acceptera leverantören för att ge en grupp till Microsofts globala nätverk och dess gräns Nätverk – inkommande Hong Kong, inte i Beijing eller Shanghai. I det här fallet är Hong Kong mycket viktigt på grund av dess fysiska anslutning och plats till Kina.

De flesta kunder tror att man använder Singapore för Interconnect är det bästa fallet eftersom det ser ut nära Kina när man tittar på kartan. Detta är inte sant. När du följer nätverks fiber Maps går nästan all nätverks anslutning till Beijing, Shanghai och Hongkong. Detta gör Hong Kong ett bättre lokaliserings alternativ för att koppla samman till Kina.

Beroende på providern kan du få olika tjänst erbjudanden. I tabellen nedan visas ett exempel på leverantörer och tjänsten de erbjuder, baserat på information när den här artikeln skrevs.

| Tjänst | Exempel på leverantörer |
| --- | --- |
| MPLS/IPVPN-nätverk |PCCW, Kina, telekom, global |
|SDWAN| PCCW, Kina, telekom, global|
| Dedikerad Internet åtkomst | PCCW, Hong Kong Telecom, Kina, mobil|

Med din leverantör kan du enas om vilka av följande två lösningar som ska användas för att komma till Microsofts globala stamnät:

* Hämtning av ett Microsoft Azure ExpressRoute upphört i Hong Kong. Det kan vara fallet vid användning av MPLS/IPVPN. För närvarande är endast den enda ICP-licensservern med ExpressRoute till Hong Kong global. De kan dock också prata med andra leverantörer om de utnyttjar moln utbytes leverantörer som Megaport eller intercloud. Mer information finns i [ExpressRoute Connectivity providers](../expressroute/expressroute-locations-providers.md#partners).

* Använda en särskild Internet åtkomst direkt på någon av följande Internet Exchange-platser eller med hjälp av en anslutning för privata nätverk.

I följande lista visas möjliga Internet-byten i Hong Kong:

* AMS – IX Hong Kong
* BBIX Hong Kong
* Equinix Hong Kong
* HKIX

När du använder den här anslutningen måste ditt nästa BGP-hopp för Microsoft-tjänster vara Microsoft autonomt system nummer (AS #) 8075. Om du använder en enda plats eller SDWAN-lösning kan det vara valet av anslutning.

Med de aktuella ändringarna angående samanslutningar mellan Kina och Hongkong SAR, skapar de flesta av dessa nätverksproviders en MPLS-brygga mellan Kina och Hong Kong SAR.

Du kan se att plats-till-plats-VPN-anslutningar i Kina tillåts och är mest stabila. Samma gäller för plats-till-plats-anslutningar mellan grenar i resten av världen. Leverantörer skapar nu en VPN/SDWAN-aggregering på både sidor och Bridge via MPLS mellan dem.

:::image type="content" source="./media/interconnect-china/china-mpls-bridge.png" alt-text="Diagram visar Kinas MPLS-brygga.":::

Oavsett hur vi rekommenderar rekommenderar vi fortfarande att du har en andra och regelbundna Internet-grupp i Kina. Detta är att dela upp trafiken mellan företags trafik till moln tjänster som Microsoft 365 och Azure och enligt-Law reglerad Internet trafik.

En kompatibel nätverks arkitektur i Kina kan se ut som i följande exempel:

:::image type="content" source="./media/interconnect-china/multi-branch.png" alt-text="Diagrammet visar flera grenar.":::

I det här exemplet, med en samman koppling med Microsoft Global Nätverk – inkommande Hongkong, kan du nu börja utnyttja [Azures globala globala WAN-arkitektur](virtual-wan-global-transit-network-architecture.md) och ytterligare tjänster, till exempel Azures säkra virtuella WAN Hub, för att förbruka tjänster och sammanställa till dina grenar och data Center utanför Kina.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>NAV-till-hubb-kommunikation

I det här avsnittet använder vi virtuell WAN Hub-till-hubb-kommunikation till Interconnect. I det här scenariot skapar du en ny virtuell WAN Hub-resurs för att ansluta till en virtuell WAN-hubb i Hong Kong, andra regioner som du föredrar, en region där du redan har Azure-resurser eller var du vill ansluta.

En exempel arkitektur kan se ut som i följande exempel:

:::image type="content" source="./media/interconnect-china/sample.png" alt-text="Diagrammet visar exempel på WAN.":::

I det här exemplet ansluter Kina-grenarna till Azure Cloud Kina och varandra genom att använda VPN-eller MPLS-anslutningar. Grenar som måste vara anslutna till globala tjänster använder MPLS eller Internetbaserade tjänster som är anslutna direkt till Hong Kong. Om du vill använda ExpressRoute i Hong Kong och i den andra regionen måste du konfigurera [ExpressRoute-Global Reach](../expressroute/expressroute-global-reach.md) för att koppla samman båda ExpressRoute-kretsarna.

ExpressRoute-Global Reach är inte tillgänglig i vissa regioner. Om du behöver ansluta till Brasilien eller Indien måste du till exempel utnyttja [moln utbytes leverantörer](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) för att tillhandahålla routningstjänster.

I bilden nedan visas båda exemplen för det här scenariot.

:::image type="content" source="./media/interconnect-china/global.png" alt-text="Diagrammet visar Global Reach.":::

## <a name="secure-internet-breakout-for-microsoft-365"></a><a name="secure"></a>Säker Internet-grupp för Microsoft 365

Ett annat övervägande är nätverks säkerhet och loggning för start punkten mellan Kina och det etablerade stamnät komponenten för virtuella WAN och kundens stamnät. I de flesta fall behöver du grupp till Internet i Hong Kong för att direkt komma åt Microsoft Edge-nätverket och, med det, de Azure frontend-dörr servrar som används för Microsoft 365 Services.

För båda scenarierna med Virtual WAN utnyttjar du den [virtuella WAN-hubben i Azure](../firewall-manager/secured-virtual-hub.md). Med hjälp av Azure Firewall Manager kan du ändra en vanlig virtuell WAN-hubb till en säker hubb och sedan distribuera och hantera en Azure-brandvägg i hubben.

Följande bild visar ett exempel på det här scenariot:

:::image type="content" source="./media/interconnect-china/internet.png" alt-text="Diagram visar Internet-grupp för trafik på webben och Microsoft-tjänster.":::

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Arkitektur och trafikflöden

Den övergripande arkitekturen kan ändras något beroende på vad du väljer för anslutningen till Hongkong. Det här avsnittet visar tre tillgängliga arkitekturer i olika kombinationer med VPN eller SDWAN och/eller ExpressRoute.

Alla de här alternativen använder Azure Virtual WAN-skyddad hubb för direkt Microsoft 365 anslutning i Hong Kong. Dessa arkitekturer stöder också kraven för efterlevnad för [Microsoft 365 multi-geo](/microsoft-365/enterprise/microsoft-365-multi-geo) och behåller trafiken nära nästa Azure-frontend-plats. Därför är det också en förbättring av användningen av Microsoft 365 utanför Kina.

När du använder Azure Virtual WAN tillsammans med Internet anslutningar kan varje anslutning ha nytta av ytterligare tjänster som [Microsoft Azure peering-tjänster (Maps)](../peering-service/about.md). MAPS har skapats för att optimera trafik som kommer till Microsofts globala nätverk från tredjepartsleverantörer från tredje part.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Alternativ 1: SDWAN eller VPN

I det här avsnittet beskrivs en design som använder SDWAN eller VPN för Hong Kong och andra grenar. Det här alternativet visar användnings-och trafikflödet när du använder en ren Internet-anslutning på båda platserna av det virtuella WAN-stamnätet. I det här fallet kommer anslutningen till Hong Kong med hjälp av dedikerad Internet åtkomst eller en SDWAN-lösning för ICP-Provider. Andra grenar använder rent Internet-eller SDWAN-lösningar också.

:::image type="content" source="./media/interconnect-china/china-traffic.png" alt-text="Diagram visar Kina för Hongkong trafik.":::

I den här arkitekturen är alla platser anslutna till Microsofts globala nätverk med hjälp av VPN och Azure Virtual WAN. Trafiken mellan platserna och Hong Kong överförs via Microsoft Network och använder endast vanlig Internet anslutning vid den senaste omkostnaden.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Alternativ 2: ExpressRoute och SDWAN eller VPN

I det här avsnittet beskrivs en design som använder ExpressRoute i Hong Kong och andra grenar med VPN/SDWAN-grenar. Med det här alternativet visas användningen av och ExpressRoute avbruten i Hong Kong och andra grenar som är anslutna via SDWAN eller VPN. ExpressRoute i Hong Kong är för närvarande begränsad till en kort lista med leverantörer, som du hittar i listan över [Express Route-partner](../expressroute/expressroute-locations-providers.md#global-commercial-azure).

:::image type="content" source="./media/interconnect-china/expressroute.png" alt-text="Diagram visar Kina till Hong Kong Traffic-ExpressRoute.":::

Det finns också alternativ för att säga upp ExpressRoute från Kina, till exempel i Sydkorea eller Japan. Dock är Hong Kong för närvarande det bästa valet.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Alternativ 3: endast ExpressRoute

I det här avsnittet beskrivs en design där ExpressRoute används för Hong Kong och andra grenar. Med det här alternativet visas kopplingen med ExpressRoute i båda ändar. Här har du ett annat trafikflöde än det andra. Den Microsoft 365 trafiken kommer att flöda till det virtuella WAN-nav som skyddas av Azure och därifrån till Microsoft Edge-nätverket och Internet.

Trafiken som går till de sammankopplade grenarna eller från dem till platserna i Kina kommer att följa en annan metod i arkitekturen. För närvarande stöder Virtual WAN inte ExpressRoute till ExpressRoute-överföring. Trafiken utnyttjar ExpressRoute Global Reach eller från tredje parts sammanlänkning utan att skicka den virtuella WAN-hubben. Den flödar direkt från ett Microsoft Enterprise Edge (MSEE: N) till ett annat.

:::image type="content" source="./media/interconnect-china/expressroute-virtual.png" alt-text="Diagrammet visar ExpressRoute Global Reach.":::

För närvarande är ExpressRoute Global Reach inte tillgängligt i varje land/region, men du kan konfigurera en lösning med Azure Virtual WAN.

Du kan till exempel konfigurera en ExpressRoute med Microsoft-peering och ansluta en VPN-tunnel via peering till Azure Virtual WAN. Nu har du aktiverat, överföring mellan VPN-och ExpressRoute utan Global Reach och leverantörer och tjänster från tredje part, till exempel Megaport Cloud.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

* [Global överföring av nätverks arkitektur med Azure Virtual WAN](virtual-wan-global-transit-network-architecture.md)

* [Skapa en virtuell WAN-hubb](virtual-wan-site-to-site-portal.md)

* [Konfigurera en virtuell WAN-skyddad hubb](../firewall-manager/secure-cloud-network.md)

* [Översikt över Azure peering service för hands version](../peering-service/about.md)