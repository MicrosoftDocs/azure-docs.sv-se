---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/22/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3877134f8a00cd627909d7f889fd5b104ccbd8b1
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863633"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>Stöds BGP på alla Azure VPN Gateway-SKU:er?
BGP stöds på alla Azure VPN Gateway SKU: er förutom Basic SKU.

### <a name="can-i-use-bgp-with-azure-policy-vpn-gateways"></a>Kan jag använda BGP med Azure Policy VPN-gatewayer?
Nej, BGP stöds endast på routning-baserade VPN-gatewayer.

### <a name="what-asns-autonomous-system-numbers-can-i-use"></a>Vilka ASN: er (autonoma system nummer) kan jag använda?
Du kan använda din egen offentliga ASN: er eller privata ASN: er för både dina lokala nätverk och virtuella Azure-nätverk. Du kan inte använda de intervall som reserver ATS av Azure eller IANA.

Följande ASN: er är reserverade för Azure eller IANA:
* ASN: er reserverade av Azure:
  * Offentliga ASN:er: 8074, 8075, 12076
  * Privata ASN:er: 65515, 65517, 65518, 65519, 65520
* ASN: er [reserverade av IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml):
   * 23456, 64496-64511, 65535-65551 och 429496729

Du kan inte ange dessa ASN: er för dina lokala VPN-enheter när du ansluter till Azure VPN-gatewayer.

### <a name="can-i-use-32-bit-4-byte-asns"></a>Kan jag använda 32-bitars (4-byte) ASN: er?
Ja, VPN Gateway stöder nu 32-bitars (4 byte) ASN: er. Om du vill konfigurera med hjälp av ASN i decimal format använder du PowerShell, Azure CLI eller Azure SDK.

### <a name="what-private-asns-can-i-use"></a>Vilken privat ASN: er kan jag använda?
De användbara intervallen privata ASN: er är:

* 64512-65514 och 65521-65534

Dessa ASN: er är inte reserverade för IANA eller Azure för användning och kan därför användas för att tilldela din Azure VPN-gateway.

### <a name="what-address-does-vpn-gateway-use-for-bgp-peer-ip"></a>Vilken adress använder VPN Gateway för BGP-peer-IP?

Som standard allokerar VPN Gateway en enskild IP-adress från *GatewaySubnet* -INTERVALLET för VPN-gatewayer med aktiv vänte läge eller två IP-adresser för aktiva, aktiva VPN-gatewayer. De här adresserna tilldelas automatiskt när du skapar VPN-gatewayen. Du kan få den faktiska BGP-IP-adress som allokeras med hjälp av PowerShell eller genom att leta upp den i Azure Portal. I PowerShell använder du **Get-AzVirtualNetworkGateway** och letar efter egenskapen **bgpPeeringAddress** . I Azure Portal på sidan Gateway- **konfiguration** tittar du under egenskapen **Konfigurera BGP ASN** .

Om dina lokala VPN-routrar använder **APIPA** IP-adresser (169.254. x. x) som BGP-IP-adresser, måste du ange ytterligare en **Azure APIPA BGP-IP-adress** på din Azure VPN-gateway. Azure VPN Gateway väljer APIPA-adressen som ska användas med den lokala APIPA BGP-peer som anges i den lokala Nätverksgatewayen eller den privata IP-adressen för en lokal BGP-peer som inte är APIPA. Mer information finns i [Configure BGP](../articles/vpn-gateway/bgp-howto.md).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Vilka är kraven för IP-adresserna för BGP-peer på min VPN-enhet?
Din lokala BGP-peer-adress får inte vara samma som den offentliga IP-adressen för VPN-enheten eller från det virtuella nätverkets adress utrymme för VPN-gatewayen. Använd en annan IP-adress på VPN-enheten för din BGP-peer-IP. Det kan vara en adress som tilldelats till loopback-gränssnittet på enheten (antingen en vanlig IP-adress eller en APIPA-adress). Om enheten använder en APIPA-adress för BGP måste du ange en APIPA BGP-IP-adress på din Azure VPN-gateway, enligt beskrivningen i [Konfigurera BGP](../articles/vpn-gateway/bgp-howto.md). Ange den här adressen i motsvarande lokala nätverksgateway som representerar platsen.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Vad ska jag ange som mina adressprefix för den lokala Nätverksgatewayen när jag använder BGP?

> [!IMPORTANT]
>
>Detta är en ändring från det tidigare dokumenterade kravet. Om du använder BGP för en anslutning lämnar du fältet **adress utrymme** tomt för motsvarande resurs för den lokala Nätverksgatewayen. Azure VPN Gateway lägger till en värd väg internt i den lokala BGP-peer IP-adressen via IPsec-tunneln. Lägg inte till vägen/32 i fältet **adress utrymme** . Den är redundant och om du använder en APIPA-adress som den lokala VPN-enhetens BGP IP kan den inte läggas till i det här fältet. Om du lägger till andra prefix i fältet **adress utrymme** läggs de till som statiska vägar på Azure VPN-gatewayen, utöver de vägar som har lärts via BGP.
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-virtual-networks"></a>Kan jag använda samma ASN för både lokala VPN-nätverk och virtuella Azure-nätverk?
Nej, du måste tilldela olika ASN: er mellan dina lokala nätverk och dina virtuella Azure-nätverk om du ansluter dem tillsammans med BGP. Azure VPN-gatewayer har ett fördefinierat ASN för 65515 som är tilldelat, om BGP är aktiverat eller inte för din anslutning mellan olika platser. Du kan åsidosätta det här standardvärdet genom att tilldela ett annat ASN när du skapar VPN-gatewayen, eller så kan du ändra ASN när gatewayen har skapats. Du måste tilldela din lokala ASN: er till motsvarande Azure lokala nätverksgateway.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Vilka adressprefix kommer Azure VPN-gatewayer att meddela mig?
Gatewayerna annonserar följande vägar till dina lokala BGP-enheter:

* Dina adressprefix för ditt virtuella nätverk.
* Adressprefix för varje lokal nätverksgateway som är ansluten till Azure VPN-gatewayen.
* Vägar som lärts från andra BGP-peering-sessioner anslutna till Azure VPN-gatewayen, förutom standard vägen eller vägarna som överlappar ett virtuellt nätverksprefix.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Hur många prefix kan jag annonsera till Azure VPN Gateway?
Azure VPN Gateway stöder upp till 4000 prefix. BGP-sessionen kommer att tas bort om antalet prefix överskrider gränsen.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Kan jag annonsera standardväg (0.0.0.0/0) till Azure VPN-gatewayer?
Ja. Observera att detta tvingar all utgående trafik från det virtuella nätverket till din lokala plats. Det förhindrar också att virtuella virtuella nätverk kan acceptera offentlig kommunikation direkt från Internet, till exempel RDP eller SSH från Internet till de virtuella datorerna.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Kan jag annonsera de exakta prefixen som mina virtuella nätverks-prefix?

Nej, samma prefix som någon av dina virtuella nätverks adressprefix kommer att blockeras eller filtreras av Azure. Du kan dock annonsera ett prefix som är en supermängd av vad du har i det virtuella nätverket. 

Om ditt virtuella nätverk till exempel har använt adress utrymmet 10.0.0.0/16 kan du annonsera 10.0.0.0/8. Men du kan inte annonsera 10.0.0.0/16 eller 10.0.0.0/24.

### <a name="can-i-use-bgp-with-my-connections-between-virtual-networks"></a>Kan jag använda BGP med mina anslutningar mellan virtuella nätverk?
Ja, du kan använda BGP för både anslutningar mellan olika platser och anslutningar mellan virtuella nätverk.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Kan jag blanda BGP- med icke-BGP-anslutningar för mina Azure VPN- gatewayer?
Ja, du kan blanda både BGP- och icke-BGP-anslutningar för samma Azure VPN-gateway.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Stöder Azure-VPN Gateway BGP-transit routning?
Ja, BGP-transit routning stöds, med undantaget att Azure VPN-gatewayer inte annonserar standard vägar till andra BGP-peer-datorer. Om du vill aktivera överförings dirigering över flera Azure VPN-gatewayer måste du aktivera BGP på alla mellanliggande anslutningar mellan virtuella nätverk. Mer information finns i [om BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="can-i-have-more-than-one-tunnel-between-an-azure-vpn-gateway-and-my-on-premises-network"></a>Kan jag ha fler än en tunnel mellan en Azure VPN-gateway och mitt lokala nätverk?
Ja, du kan skapa fler än en VPN-tunnel för plats-till-plats (S2S) mellan en Azure VPN-gateway och ditt lokala nätverk. Observera att alla dessa tunnlar räknas mot det totala antalet tunnlar för dina Azure VPN-gatewayer, och du måste aktivera BGP i båda tunnlarna.

Om du till exempel har två redundanta tunnlar mellan din Azure VPN-gateway och ett av dina lokala nätverk, förbrukar de 2 tunnlar av den totala kvoten för din Azure VPN-gateway.

### <a name="can-i-have-multiple-tunnels-between-two-azure-virtual-networks-with-bgp"></a>Kan jag ha flera tunnlar mellan två virtuella Azure-nätverk med BGP?
Ja, men minst en av dina virtuella nätverksgateways måste ha en aktiv-aktiv-konfiguration.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-azure-expressroute-and-s2s-vpn-coexistence-configuration"></a>Kan jag använda BGP för S2S VPN i en Azure-ExpressRoute och konfiguration av S2S VPN-samexistens?
Ja. 

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Vad bör jag lägga till på min lokala VPN-enhet för BGP-peeringsessionen?
Lägg till en värd väg för Azure BGP-peer-IP-adressen på VPN-enheten. Den här vägen pekar på IPsec-S2S-VPN-tunneln. Om till exempel Azure VPN-peer-IP: en är 10.12.255.30, lägger du till en värd väg för 10.12.255.30 med ett Next-hop-gränssnitt för det matchande IPsec-gränssnittet på VPN-enheten.

### <a name="does-the-virtual-network-gateway-support-bfd-for-s2s-connections-with-bgp"></a>Stöder den virtuella Nätverksgatewayen BFD för S2S-anslutningar med BGP?
Nej. Upptäckt av dubbelriktad vidarebefordran (BFD) är ett protokoll som du kan använda med BGP för att identifiera intilliggande nedtid snabbare än du kan använda standard-BGP "KeepAlive". BFD använder andra timers som utformats för att fungera i LAN-miljöer, men inte över det offentliga Internet eller Wide Area Network-anslutningar.

För anslutningar via det offentliga Internet är vissa paket fördröjda eller till och med borttagna inte ovanliga, så vi kan lägga till instabilitet i dessa aggressiva timers. Den här instabiliteten kan orsaka att vägar dämpas av BGP. Som ett alternativ kan du konfigurera den lokala enheten med timers som är lägre än standard intervallet, 60-andra "keepalive-intervallet" och den 180-andra Hold-timern. Detta resulterar i en snabbare konvergens tid.

### <a name="do-azure-vpn-gateways-initiate-bgp-peering-sessions-or-connections"></a>Initierar Azure VPN-gatewayer BGP-peering-sessioner eller anslutningar?

Gatewayen initierar BGP-peering-sessioner till de lokala BGP-peer-IP-adresserna som anges i resurserna för lokal nätverksgateway med hjälp av de privata IP-adresserna på VPN-gatewayerna. Detta är oavsett om de lokala BGP-IP-adresserna finns i APIPA-intervallet eller vanliga privata IP-adresser. Om dina lokala VPN-enheter använder APIPA-adresser som BGP-IP måste du konfigurera BGP-högtalaren för att initiera anslutningarna.

