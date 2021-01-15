---
title: 'Interoperabilitet i Azure: kontroll Plans analys'
description: Den här artikeln innehåller kontroll Plans analysen av test konfigurationen som du kan använda för att analysera interoperabilitet mellan ExpressRoute, en plats-till-plats-VPN och virtuell nätverks-peering i Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4dfd869b92e042e71eed1ee692d90fc44a8ac6c1
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234247"
---
# <a name="interoperability-in-azure--control-plane-analysis"></a>Interoperabilitet i Azure: kontroll Plans analys

I den här artikeln beskrivs analys av kontroll plan för [test konfigurationen][Setup]. Du kan också granska [konfigurationen av test konfigurationen][Configuration] och [data planet analys][Data-Analysis] av test konfigurationen.

Analys av kontroll plan undersöker i princip vägar som utbyts mellan nätverk i en topologi. Analys av kontroll plan kan hjälpa dig att förstå hur olika nätverk visar topologin.

## <a name="hub-and-spoke-vnet-perspective"></a>VNet-perspektiv för nav och ekrar

Följande bild illustrerar nätverket från ett virtuellt nätverk (VNet) i hubben och ett eker-VNet (markerat i blått). Bilden visar även det autonoma system numret (ASN) för olika nätverk och vägar som utbyts mellan olika nätverk: 

![1][1]

ASN för VNet: s Azure ExpressRoute Gateway skiljer sig från ASN för Microsoft Enterprise Edge-routrar (msee). En ExpressRoute-Gateway använder ett privat ASN (värdet **65515**) och msee använder offentligt ASN (värdet **12076**) globalt. När du konfigurerar ExpressRoute-peering, eftersom MSEE: N är peer, använder du **12076** som peer-ASN. På Azure-sidan upprättar MSEE: N eBGP-peering med ExpressRoute-gatewayen. Den dubbla eBGP-peering som MSEE: N upprättar för varje ExpressRoute-peering är transparent på kontroll planet nivå. När du visar en ExpressRoute-routningstabell visas därför VNet: s ExpressRoute-Gateway ASN för VNet: s prefix. 

Följande bild visar ett exempel på en ExpressRoute: 

![5][5]

I Azure är ASN bara betydande från ett peering-perspektiv. Som standard är ASN för både ExpressRoute-gatewayen och VPN-gatewayen i Azure VPN Gateway **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>Lokal plats 1 och fjärr-VNet-perspektiv via ExpressRoute 1

Både lokal plats 1 och fjärr-VNet är anslutna till hubbens VNet via ExpressRoute 1. De delar samma perspektiv i topologin, som du ser i följande diagram:

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>Lokal plats 1 och gren-VNet-perspektiv via en plats-till-plats-VPN

Både lokal plats 1 och gren-VNet är anslutna till ett Hubbs-VNet VPN-gateway via en plats-till-plats-VPN-anslutning. De delar samma perspektiv i topologin, som du ser i följande diagram:

![3][3]

## <a name="on-premises-location-2-perspective"></a>Lokal plats 2 – perspektiv

Lokal plats 2 är ansluten till ett nav-VNet via privat peering av ExpressRoute 2: 

![4][4]

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

Lär dig mer om [analys av data planet][Data-Analysis] i vyerna test konfiguration och Azure Network Monitoring.

Se [vanliga frågor och svar om ExpressRoute][ExR-FAQ] för att:
-   Lär dig hur många ExpressRoute-kretsar du kan ansluta till en ExpressRoute-Gateway.
-   Lär dig hur många ExpressRoute-gatewayer du kan ansluta till en ExpressRoute-krets.
-   Lär dig mer om andra skalnings gränser för ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Topologins virtuella nav och eker-perspektiv"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Plats 1 och fjärrnätverkets VNet-perspektiv via ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Plats 1 och gren VNet-perspektiv i topologin via en VPN för plats till plats"
[4]: ./media/backend-interoperability/Loc2View.png "Plats 2 perspektiv för topologin"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "ExpressRoute 1 väg tabell"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[Configuration]: ./connectivty-interoperability-configuration.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: ./connectivty-interoperability-configuration.md
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: ./connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md