---
title: 'Interoperabilitet i Azure: testa installationen | Microsoft Docs'
description: Den här artikeln beskriver en test konfiguration som du kan använda för att analysera interoperabilitet mellan ExpressRoute, en plats-till-plats-VPN och virtuell nätverks-peering i Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 206cc70b5ee636ec3cc54727c3e94a10ad2426d1
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234145"
---
# <a name="interoperability-in-azure--test-setup"></a>Interoperabilitet i Azure: testa installationen

Den här artikeln beskriver en test konfiguration som du kan använda för att analysera hur Azures nätverks tjänster samverkar på kontroll Plans nivån och data planet nivå. Låt oss ta en titt på Azures nätverks komponenter:

-   **Azure-ExpressRoute**: Använd privat peering i Azure ExpressRoute för att direkt ansluta privata IP-utrymmen i ditt lokala nätverk till dina Azure Virtual Network-distributioner. ExpressRoute kan hjälpa dig att uppnå högre bandbredd och en privat anslutning. Många ExpressRoute-eko partner erbjuder ExpressRoute-anslutning med service avtal. Mer information om ExpressRoute och information om hur du konfigurerar ExpressRoute finns i [Introduktion till ExpressRoute][ExpressRoute].
-   **Plats-till-plats-VPN**: du kan använda Azure VPN gateway som en plats-till-plats-VPN för att på ett säkert sätt ansluta ett lokalt nätverk till Azure via Internet eller genom att använda ExpressRoute. Information om hur du konfigurerar en plats-till-plats-VPN för att ansluta till Azure finns i [konfigurera VPN gateway][VPN].
-   **VNet-peering**: Använd virtuella nätverk (VNet) för att upprätta anslutningar mellan virtuella nätverk i Azure Virtual Network. Mer information om VNet-peering finns i [självstudien om VNet-peering][VNet].

## <a name="test-setup"></a>Testa installationen

Följande bild illustrerar test konfigurationen:

![1][1]

Centerpiece för test konfigurationen är hubbens VNet i Azure region 1. Hubb-VNet är anslutet till olika nätverk på följande sätt:

-   Hubbens VNet är anslutet till det virtuella eker-nätverket med hjälp av VNet-peering. Det virtuella eker-nätverket har fjärråtkomst till båda gatewayerna i hubbens VNet.
-   Hubbens VNet är anslutet till grenens VNet med plats-till-plats-VPN. Anslutningen använder eBGP för att utväxla vägar.
-   Hubbens VNet är anslutet till den lokala platsen 1-nätverket genom att använda ExpressRoute privata peering som primär sökväg. Den använder plats-till-plats-VPN-anslutning som sökväg för säkerhets kopiering. I resten av den här artikeln hänvisar vi till den här ExpressRoute-kretsen som ExpressRoute 1. Som standard tillhandahåller ExpressRoute-kretsar redundant anslutning för hög tillgänglighet. På ExpressRoute 1 är den sekundära kundens (CE)-routerns under gränssnitt som riktar sig mot den sekundära Microsoft Enterprise Edge-routern (MSEE: N) inaktive rad. En röd linje över den dubbelriktade pilen i föregående bild visar under gränssnittet inaktive rad CE-router.
-   Hubbens VNet är anslutet till den lokala plats 2-nätverket med hjälp av en annan ExpressRoute privat peering. I resten av den här artikeln hänvisar vi till den här andra ExpressRoute-kretsen som ExpressRoute 2.
-   ExpressRoute 1 ansluter också både hubb-VNet och den lokala plats 1-nätverket till ett fjärr-VNet i Azure region 2.

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute och plats-till-plats VPN-anslutning i tandem

###  <a name="site-to-site-vpn-over-expressroute"></a>Plats-till-plats-VPN via ExpressRoute

Du kan konfigurera en plats-till-plats-VPN genom att använda ExpressRoute Microsoft-peering för att utbyta data mellan ditt lokala nätverk och ditt Azure-virtuella nätverk. Med den här konfigurationen kan du utbyta data med konfidentialitet, äkthet och integritet. Data utbytet är också en anti-omuppspelning. Mer information om hur du konfigurerar en plats-till-plats IPsec-VPN i tunnel läge med ExpressRoute Microsoft-peering finns i [plats-till-plats-VPN över ExpressRoute Microsoft-peering][S2S-Over-ExR]. 

Den primära begränsningen för att konfigurera en plats-till-plats-VPN som använder Microsoft-peering är data flöde. Data flödet över IPsec-tunneln begränsas av VPN-gatewayens kapacitet. Data flödet för VPN-gatewayen är lägre än ExpressRoute-dataflödet. I det här scenariot kan du använda IPsec-tunneln för hög säker trafik och använda privat peering för all annan trafik för att optimera ExpressRoute bandbredds användning.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Plats-till-plats-VPN som en säker redundans för ExpressRoute

ExpressRoute fungerar som ett redundant krets par för att säkerställa hög tillgänglighet. Du kan konfigurera Geo-redundant ExpressRoute-anslutning i olika Azure-regioner. Som du ser i vår test konfiguration, i en Azure-region, kan du också använda en plats-till-plats-VPN för att skapa en sökväg för redundans för ExpressRoute-anslutningen. När samma prefix annonseras över både ExpressRoute och en plats-till-plats-VPN prioriterar Azure ExpressRoute. För att undvika asymmetrisk routning mellan ExpressRoute och plats-till-plats-VPN, bör den lokala nätverks konfigurationen också vara kolv med ExpressRoute-anslutning innan den använder VPN-anslutning från plats till plats.

Mer information om hur du konfigurerar sambefintliga anslutningar för ExpressRoute och en plats-till-plats-VPN finns i [ExpressRoute och plats-till-plats-samexistens][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Utöka backend-anslutningen till ekrar virtuella nätverk och filial platser

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Eker VNet-anslutning med hjälp av VNet-peering

NAV-och eker VNet-arkitekturen används ofta. Hubben är ett VNet i Azure som fungerar som en central punkt för anslutningen mellan ekrarnas virtuella nätverk och det lokala nätverket. Ekrarna är virtuella nätverk som peer-kopplas med hubben och som du kan använda för att isolera arbets belastningar. Trafik flöden mellan det lokala data centret och hubben via en ExpressRoute-eller VPN-anslutning. Mer information om arkitekturen finns i [implementera en nätverkstopologi för NAV-ekrar i Azure][Hub-n-Spoke].

I VNet-peering inom en region kan eker-virtuella nätverk använda hubb-gatewayer (både VPN-och ExpressRoute-gatewayer) för att kommunicera med fjärrnätverk.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Gren-VNet-anslutning med plats-till-plats-VPN

Du kanske vill använda Branch virtuella nätverk, som finns i olika regioner och lokala nätverk för att kommunicera med varandra via ett nav-VNet. Den interna Azure-lösningen för den här konfigurationen är plats-till-plats-VPN-anslutning med hjälp av en VPN. Ett alternativ är att använda en virtuell nätverks installation (NVA) för routning i hubben.

Mer information finns i [Vad är VPN gateway?][VPN] och [distribuera en hög tillgänglig NVA][Deploy-NVA].

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [konfigurations information][Configuration] för test miljön.

Lär dig mer om [kontroll Plans analys][Control-Analysis] av test konfigurationen och vyer för olika virtuella nätverk eller VLAN i topologin.

Lär dig mer om [analys av data planet][Data-Analysis] i vyerna test konfiguration och Azure Network Monitoring.

Se [vanliga frågor och svar om ExpressRoute][ExR-FAQ] för att:
-   Lär dig hur många ExpressRoute-kretsar du kan ansluta till en ExpressRoute-Gateway.
-   Lär dig hur många ExpressRoute-gatewayer du kan ansluta till en ExpressRoute-krets.
-   Lär dig mer om andra skalnings gränser för ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "Diagram över test sto pol Ogin"

<!--Link References-->
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha