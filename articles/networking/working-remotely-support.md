---
title: Arbeta fjärran slutet med Azure nätverks tjänster
description: På den här sidan beskrivs hur du kan använda Azure Networking Services som är tillgängliga för att fjärrstyra och hur du kan undvika trafik problem som uppstår på grund av ökad antal personer som arbetar på distans.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: 835be5b867826d75732c9482743ad6a4ca2900e7
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231850"
---
# <a name="working-remotely-using-azure-networking-services"></a>Arbeta fjärran slutet med Azure nätverks tjänster

>[!NOTE]
> I den här artikeln beskrivs hur du kan använda Azures nätverks tjänster, Microsoft-nätverk och Azure-partnerns eko system för att arbeta med fjärrnätverket och minimera nätverks problem som kan uppstå på grund av COVID-19-krisen.

I den här artikeln beskrivs de alternativ som är tillgängliga för organisationer för att ställa in fjärråtkomst för sina användare eller för att komplettera sina befintliga lösningar med ytterligare kapacitet under perioder med högsta belastning. Nätverks arkitekter har följande utmaningar:

- Hantera en ökning av nätverks användningen.
- Tillhandahålla tillförlitlig anslutning till fler anställda av företaget och kunderna.
- Tillhandahålla anslutning till fjärranslutna platser över hela världen.

Alla nätverk (t. ex. privata WAN-och företags kärn nätverk) är inte överbelastade med belastningen på högsta fjärranslutna arbetare. Flask halsarna rapporteras vanligt vis endast i hem nätverk med bred band och VPN-gatewayer för lokala nätverk av företag.

Nätverks planeraren kan hjälpa till att under lätta Flask halsar och minska överbelastningen i nätverket genom att tänka på att olika trafik typer behöver olika nätverks behandlings prioriteter och av en omdirigering/distribution med Smart belastning. Till exempel är real tids tele-medecine trafik för läkare-patient interaktionen hög prioritet och fördröjning/Darr-känsliga. Replikering av samma trafik mellan lagrings enheter är inte fördröjnings känslig. Den tidigare trafiken måste dirigeras via den mest optimala nätverks Sök vägen med högre kvalitet på tjänsten. Det är acceptabelt att dirigera den senare trafiken via en under optimal väg.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Att dela våra bästa praxis – Azure Network har utformats för elastiskhet och hög tillgänglighet

Azure är utformat för att motstå plötsliga förändringar i användningen av resurserna och kan avsevärt hjälpa dig under perioder med högsta belastning. Microsoft underhåller och arbetar med ett av världars största nätverk. Microsofts nätverk har utformats för hög tillgänglighet som kan motstå olika typer av problem: från ett enskilt nätverks element som inte kan användas i en hel region.

Microsoft-nätverket är utformat för att uppfylla kraven och ger optimala prestanda för olika typer av nätverks trafik, inklusive fördröjnings känsliga multimedia trafik för Skype och Teams, CDN, real tids analys av stora data, Azure Storage, Bing och Xbox. För att ge optimala prestanda för olika typer av trafik, drar Microsoft-nätverket all trafik som är avsedd för eller som ska överföras genom sina resurser så nära trafikens ursprung som möjligt.

>[!NOTE] 
>Med hjälp av funktionerna för Azure-nätverk som beskrivs nedan utnyttjas trafik attraktionsvärdet beteendet i Microsofts globala nätverk för att ge en bättre kund upplevelse. Trafik attraktionsvärdet beteendet i Microsoft Network hjälper till att inaktivera trafik så snart som möjligt från de första/sista mil-nätverken som kan drabbas av överbelastning under perioder med hög belastning.
>

## <a name="enable-employees-to-work-remotely"></a>Gör det möjligt för anställda att arbeta på distans

Azure VPN-gateway har stöd för både punkt-till-plats (P2S) och plats-till-plats (S2S) VPN-anslutningar. Med hjälp av Azure VPN-gatewayen kan du skala dina anställdas anslutningar på ett säkert sätt till både dina Azure-distribuerade resurser och dina lokala resurser. Mer information finns i [så här gör du så att användare kan arbeta fjärran slutet](../vpn-gateway/work-remotely-support.md). 

Om du använder Secure Sockets Tunneling Protocol (SSTP) är antalet samtidiga anslutningar begränsad till 128. För att få ett större antal anslutningar föreslår vi över gången till OpenVPN eller IKEv2. Mer information finns i [över gång till OpenVPN-protokoll eller ikev2 från SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md
).

För att få åtkomst till dina resurser som distribueras i Azure kan fjärrutvecklare använda Azure skydds-lösningen i stället för VPN-anslutning för att få säker shell-åtkomst (RDP eller SSH) utan att behöva offentliga IP-adresser på de virtuella datorerna som används. Mer information finns i [arbeta fjärran slutet med Azure skydds](../bastion/work-remotely-support.md).

För att aggregera VPN-anslutningar med stor skala, för att ge stöd för anslutningar mellan resurser på olika lokala globala platser, i olika regionala hubbar och ekrar virtuella nätverk, och för att optimera användningen av flera hem bred band nätverk kan du använda Azure Virtual WAN. Mer information finns i [kämpar för att tillgodose arbete från hem behov? Här kan Azure Virtual WAN hjälpa dig](../virtual-wan/work-remotely-support.md).

Ett annat sätt att stödja en fjärran sluten arbets styrka är att distribuera en virtuell Skriv bords infrastruktur (VDI) som finns i det virtuella Azure-nätverket och som skyddas med en Azure-brandvägg. Till exempel är Windows Virtual Desktop (WVD) en Skriv bords-och app Virtualization-tjänst som körs i Azure. Med Windows Virtual Desktop kan du konfigurera en skalbar och flexibel miljö i din Azure-prenumeration utan att behöva köra några ytterligare Gateway-servrar. Du är bara ansvarig för de virtuella WVD-datorerna i det virtuella nätverket. Mer information finns i [Support för Azure Firewall Remote Work](../firewall/remote-work-support.md). 

Azure har också en omfattande uppsättning av miljö system partner. Våra partner nätverks virtuella apparater på Azure kan också hjälpa till att skala VPN-anslutningar. Mer information finns i [NVA-överväganden för virtuella nätverk](../vpn-gateway/nva-work-remotely-support.md).

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>Utöka medarbetarnas anslutning till att komma åt globalt distribuerade resurser

Följande Azure-tjänster kan hjälpa anställda att få åtkomst till dina globalt distribuerade resurser. Dina resurser kan finnas i alla Azure-regioner, lokala nätverk eller till och med i andra offentliga eller privata moln. 

- **Peering i Azure Virtual Network**: om du distribuerar dina resurser i fler än en Azure-region och/eller om du sammanställer anslutningen till fjärran vändare som använder flera virtuella nätverk kan du upprätta anslutningar mellan flera virtuella Azure-nätverk med hjälp av peering av virtuella nätverk. Mer information finns i [peering för virtuella nätverk][VNet-peer].

- **Azure VPN-baserad lösning**: för fjärran anställda som är anslutna till Azure via P2s eller S2S VPN, kan du aktivera åtkomst till lokala nätverk genom att konfigurera S2S VPN mellan dina lokala nätverk och Azure VPN-gatewayen. Mer information finns i [skapa en plats-till-plats-anslutning][S2S].

- **ExpressRoute**: med ExpressRoute privat peering kan du aktivera privat anslutning mellan dina Azure-distributioner och lokala infrastrukturer eller infrastrukturen i en gemensam plats. ExpressRoute, via Microsoft-peering, tillåter också åtkomst till offentliga slut punkter i Microsoft från ditt lokala nätverk. ExpressRoute-anslutningar går inte via offentligt Internet. De erbjuder säker anslutning, tillförlitlighet, högre data flöde, med lägre och konsekvent fördröjning än vanliga anslutningar via Internet. Mer information finns i [Översikt över ExpressRoute][ExR]. Att använda din befintliga nätverksprovider som redan ingår i vårt [ExpressRoute-partner eko system][ExR-eco] kan hjälpa till att minska tiden för att få stora bandbredds anslutningar till Microsoft.  Med [ExpressRoute Direct][ExR-D] kan du ansluta ditt lokala nätverk direkt till Microsoft-stamnätet. ExpressRoute Direct erbjuder två olika alternativ för linje hastighet på dubbla 10 Gbit/s eller 100 Gbit/s. 

- **Azure Virtual WAN**: Azure Virtual WAN ger smidig samverkan mellan VPN-anslutningar och ExpressRoute-kretsar. Som tidigare nämnts har Azure Virtual WAN även stöd för anslutningar mellan resurser i olika lokal globala platser, i olika regionala hubbar och virtuella nätverk i ekrar

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Skala kund anslutning till klient dels resurser

Under de tillfällen då fler personer går online får många företags webbplatser ökad kund trafik. Azure Application Gateway kan hjälpa till att hantera denna ökade arbets belastning för klient delen. Mer information finns i [Application Gateway stöd för hög trafik](../application-gateway/high-traffic-support.md).

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Microsoft-Support för trafik i flera moln

För dina distributioner i andra offentliga moln kan Microsoft tillhandahålla global anslutning. Azure Virtual WAN, VPN eller ExpressRoute kan hjälpa dig i detta hänseende. Om du vill utöka anslutningen från Azure till andra moln kan du konfigurera S2S VPN mellan de två molnen. Du kan också upprätta anslutningar från Azure till andra offentliga moln med ExpressRoute. Oracle-molnet är en del av eko systemet för ExpressRoute-partner. Du kan [Konfigurera en direkt anslutning mellan Azure och Oracle Cloud Infrastructure][Az-OCI]. De flesta tjänst leverantörer som ingår i ExpressRoute-partnerns eko system erbjuder också privat anslutning till andra offentliga moln. Genom att utnyttja dessa tjänste leverantörer kan du upprätta en privat anslutning mellan dina distributioner i Azure och andra moln via ExpressRoute.

## <a name="next-steps"></a>Nästa steg

I följande artiklar diskuteras hur olika funktioner i Azure-nätverk kan användas för att skala användare till fjärranslutna datorer:

| **Artiklar** | **Beskrivning** |
| --- | --- |
| [Så här gör du det möjligt för användare att arbeta fjärran slutet](../vpn-gateway/work-remotely-support.md) | Granska tillgängliga alternativ för att konfigurera fjärråtkomst för användare eller för att komplettera sina befintliga lösningar med ytterligare kapacitet för din organisation.|
| [Kämpar för att kunna fungera från start behoven? Här kan Azure Virtual WAN hjälpa dig](../virtual-wan/work-remotely-support.md) | Använd Azure Virtual WAN för att hantera fjärr anslutnings behoven för din organisation.|
| [Stöd för höga trafikvolymer i Application Gateway](../application-gateway/high-traffic-support.md) | Använd Application Gateway med brand vägg för webbaserade program (WAF) för en skalbar och säker metod för att hantera trafik till dina webb program. |
| [NVA-överväganden för virtuella nätverk för fjärran sluten arbete](../vpn-gateway/nva-work-remotely-support.md)|Läs vägledning om hur du använder NVA i Azure för att tillhandahålla lösningar för fjärråtkomst. |
| [Över gång till OpenVPN-protokoll eller IKEv2 från SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md) | Lösa 128 samtidiga anslutnings gränsen för SSTP genom att övergå till OpenVPN-protokoll eller IKEv2.|
| [Arbeta via fjärr anslutning med Azure skydds](../bastion/work-remotely-support.md) | Tillhandahålla säker och sömlös RDP/SSH-anslutning till virtuella datorer i det virtuella Azure-nätverket, direkt i Azure Portal utan att använda en offentlig IP-adress. |
| [Använda Azure-ExpressRoute för att skapa hybrid anslutningar som stöder fjärran vändare](../expressroute/work-remotely-support.md) | Använd ExpressRoute för Hybrid anslutning för att göra det möjligt för användare i organisationen att arbeta på distans.|
| [Support för Azure Firewall Remote Work](../firewall/remote-work-support.md)|Skydda dina Azure Virtual Network-resurser med hjälp av Azure-brandväggen. |

<!--Link References-->
[VNet-peer]: ../virtual-network/virtual-network-peering-overview.md
[S2S]: ../vpn-gateway/tutorial-site-to-site-portal.md
[ExR]: ../expressroute/expressroute-introduction.md
[ExR-eco]: ../expressroute/expressroute-locations.md
[ExR-D]: ../expressroute/expressroute-erdirect-about.md
[Az-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md