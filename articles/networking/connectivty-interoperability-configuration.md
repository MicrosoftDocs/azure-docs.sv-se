---
title: 'Interoperabilitet i Azures Server dels anslutnings funktioner: konfigurations information | Microsoft Docs'
description: Den här artikeln beskriver konfigurations information för test konfigurationen som du kan använda för att analysera interoperabilitet mellan ExpressRoute, en plats-till-plats-VPN och virtuell nätverks-peering i Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 50fd5641c61d08939eca1f2cbafb1d077254e37d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98233278"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Interoperabilitet i Azures Server dels anslutnings funktioner: testa konfigurations information

I den här artikeln beskrivs konfigurations information för [test konfigurationen][Setup]. Test installationen hjälper dig att analysera hur Azures nätverks tjänster samverkar med kontroll Plans nivån och data planet nivån.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Eker VNet-anslutning med hjälp av VNet-peering

Följande bild visar information om Virtual Network peering i Azure för ett eker-virtuellt nätverk (VNet). Information om hur du konfigurerar peering mellan två virtuella nätverk finns i [Hantera VNet-peering][VNet-Config]. Om du vill att det virtuella eker-nätverket ska använda de gatewayer som är anslutna till hubbens VNet väljer du **Använd fjärrgatewayer**.

[![81.1]][1]

Följande bild visar information om VNet-peering för hubbens VNet. Om du vill att hubbens VNet ska tillåta att det virtuella eker-nätverket använder hubbens gatewayer väljer du **Tillåt Gateway-överföring**.

[![11.2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Gren-VNet-anslutning med hjälp av en plats-till-plats-VPN

Konfigurera VPN-anslutning från plats till plats mellan hubben och grenen virtuella nätverk med hjälp av VPN-gatewayer i Azure VPN Gateway. Som standard använder VPN-gatewayer och Azure ExpressRoute-gatewayer ett privat autonomt system nummer (ASN)-värde på **65515**. Du kan ändra ASN-värdet i VPN Gateway. I test installationen ändras ASN-värdet för VPN-gatewayen för gren-VNet till **65516** för att stödja eBGP-routning mellan hubben och gren virtuella nätverk.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>Lokal plats 1 anslutning med hjälp av ExpressRoute och VPN för plats till plats

### <a name="expressroute-1-configuration-details"></a>Konfigurations information för ExpressRoute 1

Följande bild visar Azure region 1 ExpressRoute-krets konfigurationen mot lokala platser 1-Azure-routrar (Customer Edge):

[![4]][4]

Följande bild visar anslutnings konfigurationen mellan ExpressRoute 1-kretsen och hubbens VNet:

[![5]][5]

I följande lista visas den primära CE-routerkonfigurationen för ExpressRoute-anslutning för privat peering. (Cisco ASR1000-routrarna används som CE-routrar i test konfigurationen.) När plats-till-plats-VPN-och ExpressRoute-kretsar konfigureras parallellt för att ansluta ett lokalt nätverk till Azure prioriterar Azure ExpressRoute-kretsen som standard. För att undvika asymmetrisk routning bör det lokala nätverket även prioritera ExpressRoute-anslutning via plats-till-plats-VPN-anslutning. Följande konfiguration fastställer prioritet genom att använda attributet BGP **Local-Preference** :

```config
interface TenGigabitEthernet0/0/0.300
 description Customer 30 private peering to Azure
 encapsulation dot1Q 30 second-dot1q 300
 ip vrf forwarding 30
 ip address 192.168.30.17 255.255.255.252
!
interface TenGigabitEthernet1/0/0.30
 description Customer 30 to south bound LAN switch
 encapsulation dot1Q 30
 ip vrf forwarding 30
 ip address 192.168.30.0 255.255.255.254
 ip ospf network point-to-point
!
router ospf 30 vrf 30
 router-id 10.2.30.253
 redistribute bgp 65021 subnets route-map BGP2OSPF
 network 192.168.30.0 0.0.0.1 area 0.0.0.0
default-information originate always
 default-metric 10
!
router bgp 65021
 !
 address-family ipv4 vrf 30
  network 10.2.30.0 mask 255.255.255.128
  neighbor 192.168.30.18 remote-as 12076
  neighbor 192.168.30.18 activate
  neighbor 192.168.30.18 next-hop-self
  neighbor 192.168.30.18 soft-reconfiguration inbound
  neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
  neighbor 192.168.30.18 prefix-list Cust30_to_Private out
 exit-address-family
!
route-map prefer-ER-over-VPN permit 10
 set local-preference 200
!
ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
!
```

### <a name="site-to-site-vpn-configuration-details"></a>Konfigurations information för plats-till-plats-VPN

I följande lista visas den primära CE-routerkonfigurationen för plats-till-plats-VPN-anslutning:

```config
crypto ikev2 proposal Cust30-azure-proposal
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy Cust30-azure-policy
 match address local 66.198.12.106
 proposal Cust30-azure-proposal
!
crypto ikev2 keyring Cust30-azure-keyring
 peer azure
  address 52.168.162.84
  pre-shared-key local IamSecure123
  pre-shared-key remote IamSecure123
!
crypto ikev2 profile Cust30-azure-profile
 match identity remote address 52.168.162.84 255.255.255.255
 identity local address 66.198.12.106
 authentication local pre-share
 authentication remote pre-share
 keyring local Cust30-azure-keyring
!
crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile Cust30-azure-ipsec-profile
 set transform-set Cust30-azure-ipsec-proposal-set
 set ikev2-profile Cust30-azure-profile
!
interface Loopback30
 ip address 66.198.12.106 255.255.255.255
!
interface Tunnel30
 ip vrf forwarding 30
 ip address 10.2.30.125 255.255.255.255
 tunnel source Loopback30
 tunnel mode ipsec ipv4
 tunnel destination 52.168.162.84
 tunnel protection ipsec profile Cust30-azure-ipsec-profile
!
router bgp 65021
 !
 address-family ipv4 vrf 30
  network 10.2.30.0 mask 255.255.255.128
  neighbor 10.10.30.254 remote-as 65515
  neighbor 10.10.30.254 ebgp-multihop 5
  neighbor 10.10.30.254 update-source Tunnel30
  neighbor 10.10.30.254 activate
  neighbor 10.10.30.254 soft-reconfiguration inbound
 exit-address-family
!
ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30
```

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>Lokal plats 2 anslutning med hjälp av ExpressRoute

En andra ExpressRoute-krets, i nära närhet till lokal plats 2, ansluter lokalt plats 2 till hubb-VNet. Följande bild visar den andra ExpressRoute-konfigurationen:

[![3-6]][6]

Följande bild visar anslutnings konfigurationen mellan den andra ExpressRoute-kretsen och Hub VNet:

[![3,7]][7]

ExpressRoute 1 ansluter både hubb-VNet och lokal plats 1 till ett fjärrnätverk i en annan Azure-region:

[![8]][8]

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

Lär dig mer om [kontroll Plans analys][Control-Analysis] av test konfigurationen och vyer för olika virtuella nätverk eller VLAN i topologin.

Lär dig mer om [analys av data planet][Data-Analysis] i vyerna test konfiguration och Azure Network Monitoring.

Se [vanliga frågor och svar om ExpressRoute][ExR-FAQ] för att:
-   Lär dig hur många ExpressRoute-kretsar du kan ansluta till en ExpressRoute-Gateway.
-   Lär dig hur många ExpressRoute-gatewayer du kan ansluta till en ExpressRoute-krets.
-   Lär dig mer om andra skalnings gränser för ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "eker VNet VNet-peering"
[2]: ./media/backend-interoperability/HubVNet-peering.png "hubb för VNet VNet-peering"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "VPN gateway konfiguration av ett förgrenings-VNet"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute 1-konfiguration"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "anslutnings konfiguration för ExpressRoute 1 till en hubb-VNet ExR-Gateway"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute 2-konfiguration"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "anslutnings konfiguration för ExpressRoute 2 till en hubb VNet ExR Gateway"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "anslutnings konfiguration för ExpressRoute 2 till en fjärran sluten VNet ExR-Gateway"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Control-Analysis]: ./connectivty-interoperability-control-plane.md
[Data-Analysis]: ./connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md