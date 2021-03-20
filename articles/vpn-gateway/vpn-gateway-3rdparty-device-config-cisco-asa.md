---
title: Exempel på konfiguration för anslutning av Cisco ASA-enheter till Azure VPN-gatewayer
description: Den här artikeln innehåller en exempel konfiguration för att ansluta Cisco ASA-enheter till Azure VPN-gatewayer.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.openlocfilehash: 82e00e0b519040c57fdfd798d8992086c61e5481
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89426187"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Exempel på konfiguration: Cisco ASA-enhet (IKEv2/No BGP)
Den här artikeln innehåller exempel på konfigurationer för att ansluta Cisco adaptiv Security-enheter (ASA) till Azure VPN-gatewayer. Exemplet gäller Cisco ASA-enheter som kör IKEv2 utan Border Gateway Protocol (BGP). 

## <a name="device-at-a-glance"></a>En snabb enhet

* Enhets tillverkare: **Cisco**
* Enhets modell: **ASA**           
* Mål version: **8,4 och senare**
* Testad modell: **ASA 5505**
* Testad version: **9,2**             
* IKE-version: **IKEv2**                  
* BGP: **Nej**      
* Typ av Azure VPN-Gateway: **routning-baserad VPN-gateway**

> [!NOTE]
> Exempel konfigurationen ansluter en Cisco ASA-enhet till en Azure **Route-baserad** VPN-gateway. Anslutningen använder en anpassad IPsec/IKE-princip med alternativet **UsePolicyBasedTrafficSelectors** enligt beskrivningen i [den här artikeln](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> Exemplet kräver att ASA-enheter använder **IKEv2** -principen med åtkomst listbaserade konfigurationer, inte VTI-baserade. Kontakta din VPN-enhets specifikationer för att kontrol lera att IKEv2-principen stöds på dina lokala VPN-enheter.


## <a name="vpn-device-requirements"></a>Krav för VPN-enhet
Azure VPN-gatewayer använder standard-IPsec/IKE-protokollsviten för att upprätta VPN-tunnlar för plats-till-plats (S2S). De detaljerade parametrarna för IPsec/IKE-protokollet och standardkrypteringsalgoritmerna för Azure VPN-gatewayer finns i [om VPN-enheter](vpn-gateway-about-vpn-devices.md).

> [!NOTE]
> Du kan också ange en exakt kombination av krypteringsalgoritmer och nyckel styrkor för en speciell anslutning, enligt beskrivningen i [om kryptografiska krav](vpn-gateway-about-compliance-crypto.md). Om du anger en exakt kombination av algoritmer och nyckel längder, måste du använda motsvarande specifikationer på VPN-enheterna.

## <a name="single-vpn-tunnel"></a>Enskild VPN-tunnel
Den här konfigurationen består av en enda S2S VPN-tunnel mellan en Azure VPN-gateway och en lokal VPN-enhet. Du kan också konfigurera BGP över VPN-tunneln.

![Enkel VPN-tunnel för S2S](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Steg-för-steg-instruktioner för att skapa Azure-konfigurationer finns i [konfiguration av enskild VPN-tunnel](vpn-gateway-3rdparty-device-config-overview.md#singletunnel).

### <a name="virtual-network-and-vpn-gateway-information"></a>Information om virtuellt nätverk och VPN-gateway
I det här avsnittet visas parametrarna för exemplet.

| **Parameter**                | **Värde**                    |
| ---                          | ---                          |
| Prefix för virtuella nätverks adresser        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP för Azure VPN-gateway         | Azure_Gateway_Public_IP      |
| Lokala adressprefix | 10.51.0.0/16<br>10.52.0.0/16 |
| IP för lokal VPN-enhet    | OnPrem_Device_Public_IP     |
| * ASN för BGP för virtuella nätverk                | 65010                        |
| * Azure BGP peer IP           | 10.12.255.30                 |
| * Lokalt BGP-ASN         | 65050                        |
| * Lokal BGP peer-IP     | 10.52.255.254                |
|                              |                              |

\* Valfri parameter för endast BGP.

### <a name="ipsecike-policy-and-parameters"></a>IPsec/IKE-princip och parametrar
I följande tabell visas de IPsec/IKE-algoritmer och parametrar som används i exemplet. Kontakta VPN-enhetens specifikationer för att kontrol lera vilka algoritmer som stöds för dina VPN-enheters modeller och versioner av inbyggd program vara.

| **IPsec/IKEv2**  | **Värde**                            |
| ---              | ---                                  |
| IKEv2-kryptering | AES256                               |
| IKEv2 Integrity  | SHA384                               |
| DH-grupp         | DHGroup24                            |
| * IPsec-kryptering | AES256                               |
| * IPsec-integritet  | SHA1                                 |
| PFS-grupp        | PFS24                                |
| QM SA-livstid   | 7 200 sekunder                         |
| Trafikväljare | UsePolicyBasedTrafficSelectors $True |
| I förväg delad nyckel   | PreSharedKey                         |
|                  |                                      |

\* På vissa enheter måste IPsec-integritet vara ett null-värde när IPsec-krypteringsalgoritmen är AES-GCM.

### <a name="asa-device-support"></a>ASA-enhetens support

* Stöd för IKEv2 kräver ASA version 8,4 och senare.

* Stöd för DH-grupp och PFS-grupp utöver grupp 5 kräver ASA version 9. x.

* Stöd för IPsec-kryptering med AES-GCM och IPsec-integritet med SHA-256, SHA-384 eller SHA-512 kräver ASA version 9. x. Det här support kravet gäller för nyare ASA-enheter. Vid publicerings tillfället stöder inte ASA-modellerna 5505, 5510, 5520, 5540, 5550 och 5580 dessa algoritmer. Kontakta VPN-enhetens specifikationer för att kontrol lera vilka algoritmer som stöds för dina VPN-enheters modeller och versioner av inbyggd program vara.


### <a name="sample-device-configuration"></a>Exempel på enhets konfiguration
Skriptet innehåller ett exempel som baseras på konfigurationen och parametrarna som beskrivs i föregående avsnitt. Konfigurationen av S2S VPN-tunnel består av följande delar:

1. Gränssnitt och vägar
2. Åtkomst listor
3. IKE-princip och parametrar (fas 1 eller huvud läge)
4. IPsec-princip och parametrar (fas 2 eller snabb läge)
5. Andra parametrar, t. ex. TCP MSS ihopfogning

> [!IMPORTANT]
> Utför följande steg innan du använder exempel skriptet. Ersätt plats hållarnas värden i skriptet med enhets inställningarna för din konfiguration.

* Ange gränssnitts konfigurationen för både inifrån och utanför gränssnitt.
* Identifiera vägarna för dina inifrån/privata och externa/offentliga nätverk.
* Se till att alla namn och princip nummer är unika på enheten.
* Se till att de kryptografiska algoritmerna stöds på enheten.
* Ersätt följande **plats hållarnas värden** med de faktiska värdena för din konfiguration:
  - Utanför gränssnittets namn: **utanför**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Namn på virtuella nätverk och lokala nätverksgateway: **VNetName** och **LNGName**
  - **Prefix** för virtuella nätverk och lokala nätverks adresser
  - Lämpliga **nätmasker**

#### <a name="sample-script"></a>Exempelskript

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec proposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Enkla fel söknings kommandon

Använd följande ASA-kommandon för fel söknings syfte:

* Visa IPsec eller IKE säkerhets Association (SA):
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Ange fel söknings läge:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    `debug`Kommandona kan generera avsevärda utdata i-konsolen.

* Visa de aktuella konfigurationerna på enheten:
    ```
    show run
    ```
    Använd `show` under kommandon för att lista vissa delar av enhets konfigurationen, till exempel:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>Nästa steg
Information om hur du konfigurerar aktiva anslutningar mellan lokala nätverk och VNet-till-VNet finns i [Konfigurera aktiva VPN-gatewayer](vpn-gateway-activeactive-rm-powershell.md).
