---
title: 'Azure-ExpressRoute: Konfigurera S2S VPN via Microsoft-peering'
description: Konfigurera IPsec/IKE-anslutning till Azure via en ExpressRoute Microsoft-peering-krets med en plats-till-plats-VPN-gateway.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 366f27a0e2a22e9aa10dda20e105bf644255bdd4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89393147"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Konfigurera en plats-till-plats-VPN via ExpressRoute Microsoft-peering

Den här artikeln hjälper dig att konfigurera säker krypterad anslutning mellan ditt lokala nätverk och dina virtuella Azure-nätverk (virtuella nätverk) via en ExpressRoute privat anslutning. Du kan använda Microsoft-peering för att upprätta en plats-till-plats-IPsec/IKE VPN-tunnel mellan dina valda lokala nätverk och Azure-virtuella nätverk. Genom att konfigurera en säker tunnel över ExpressRoute kan du använda data utbyte med konfidentialitet, anti-omuppspelning, äkthet och integritet.

>[!NOTE]
>När du konfigurerar VPN för plats-till-plats via Microsoft-peering debiteras du för VPN-gatewayen och utgående VPN. Mer information finns i [VPN gateway prissättning](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="architecture"></a><a name="architecture"></a>Arkitektur


  ![anslutnings översikt](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


För hög tillgänglighet och redundans kan du konfigurera flera tunnlar över de två MSEE: N för en ExpressRoute-krets och aktivera belastnings utjämning mellan tunnlarna.

  ![alternativ för hög tillgänglighet](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

VPN-tunnlar via Microsoft-peering kan avbrytas antingen med hjälp av VPN-gateway eller med en lämplig virtuell nätverks installation (NVA) som är tillgänglig via Azure Marketplace. Du kan utbyta vägar statiskt eller dynamiskt över de krypterade tunnlarna utan att exponera väg utbytet till den underliggande Microsoft-peering. I exemplen i den här artikeln används BGP (skiljer sig från BGP-sessionen som användes för att skapa Microsoft-peering) för att dynamiskt byta ut prefix över de krypterade tunnlarna.

>[!IMPORTANT]
>För den lokala sidan avslutas vanligt vis Microsoft-peering på DMZ och privat peering avbryts på kärn nätverks zonen. De två zonerna skulle åtskiljas med hjälp av brand väggar. Om du konfigurerar Microsoft-peering exklusivt för att aktivera säker tunnel trafik över ExpressRoute, kom ihåg att filtrera igenom enbart offentliga IP-adresser som annonseras via Microsoft-peering.
>
>

## <a name="workflow"></a><a name="workflow"></a>Arbetsflöde

1. Konfigurera Microsoft-peering för din ExpressRoute-krets.
2. Annonsera markerade Azure regionala offentliga prefix till ditt lokala nätverk via Microsoft-peering.
3. Konfigurera en VPN-gateway och upprätta IPsec-tunnlar
4. Konfigurera den lokala VPN-enheten.
5. Skapa en plats-till-plats-IPsec/IKE-anslutning.
6. Valfritt Konfigurera brand väggar/filtrering på den lokala VPN-enheten.
7. Testa och verifiera IPsec-kommunikationen via ExpressRoute-kretsen.

## <a name="1-configure-microsoft-peering"></a><a name="peering"></a>1. Konfigurera Microsoft-peering

Om du vill konfigurera en plats-till-plats-VPN-anslutning via ExpressRoute måste du utnyttja ExpressRoute Microsoft-peering.

* Om du vill konfigurera en ny ExpressRoute-krets börjar du med artikeln [ExpressRoute-krav](expressroute-prerequisites.md) och [skapar och ändrar sedan en ExpressRoute-krets](expressroute-howto-circuit-arm.md).

* Om du redan har en ExpressRoute-krets, men inte har konfigurerat Microsoft-peering, konfigurerar du Microsoft-peering med hjälp av artikeln [skapa och ändra peering för en ExpressRoute-krets](expressroute-howto-routing-arm.md#msft) .

När du har konfigurerat din krets och Microsoft-peering kan du enkelt visa den med hjälp av sidan **Översikt** i Azure Portal.

![krets](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="2-configure-route-filters"></a><a name="routefilter"></a>2. Konfigurera väg filter

Med ett flödesfilter kan du identifiera tjänster som du vill använda via Microsoft-peering för din ExpressRoute-krets. Det är i stort sett en tillåten lista över alla värden för BGP-communityn. 

![flödes filter](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

I det här exemplet är distributionen endast i regionen *Azure västra USA 2* . En flödes filter regel läggs till för att tillåta enbart annonsering av Azure västra USA 2 regionala prefix, som har BGP-communityns värde *12076:51026*. Du anger de regionala prefix som du vill tillåta genom att välja **Hantera regel**.

I flödes filtret måste du också välja de ExpressRoute-kretsar som väg filtret gäller för. Du kan välja ExpressRoute-kretsar genom att välja **Lägg till krets**. I föregående bild är flödes filtret associerat med exempel ExpressRoute-kretsen.

### <a name="21-configure-the-route-filter"></a><a name="configfilter"></a>2,1 Konfigurera flödes filtret

Konfigurera ett flödes filter. Anvisningar finns i [Konfigurera väg filter för Microsoft-peering](how-to-routefilter-portal.md).

### <a name="22-verify-bgp-routes"></a><a name="verifybgp"></a>2,2 verifiera BGP-vägar

När du har skapat Microsoft-peering över din ExpressRoute-krets och kopplat ett flödes filter till kretsen, kan du kontrol lera BGP-vägarna som tas emot från msee på de PE-enheter som är peering med msee. Verifierings kommandot varierar beroende på operativ systemet för dina PE-enheter.

#### <a name="cisco-examples"></a>Cisco-exempel

I det här exemplet används ett Cisco IOS-XE-kommando. I exemplet används en VRF-instans (Virtual Routing and Forwarding) för att isolera peering-trafiken.

```
show ip bgp vpnv4 vrf 10 summary
```

Följande partiella utdata visar att 68-prefix togs emot från grannen \* . 243.229.34 med ASN 12076 (msee: n):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Om du vill se en lista över de prefix som tagits emot från grannen använder du följande exempel:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

För att bekräfta att du får rätt uppsättning prefix kan du välja mellan olika åtgärder. Följande Azure PowerShell kommandoutdata visar en lista över de prefix som annonseras via Microsoft-peering för var och en av tjänsterna och för var och en av Azure-regionen:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="3-configure-the-vpn-gateway-and-ipsec-tunnels"></a><a name="vpngateway"></a>3. Konfigurera VPN-gatewayen och IPsec-tunnlar

I det här avsnittet skapas IPsec VPN-tunnlar mellan Azure VPN-gatewayen och den lokala VPN-enheten. I exemplen används CSR1000-VPN-enheter (Cisco Cloud Service router).

Följande diagram visar de IPsec VPN-tunnlar som upprättats mellan den lokala VPN-enheten 1 och instans paret för Azure VPN gateway. De två VPN-tunnlar för IPsec som upprättats mellan den lokala VPN-enheten 2 och instans paret för Azure VPN-gateway visas inte i diagrammet och konfigurations informationen visas inte i listan. Att ha ytterligare VPN-tunnlar förbättrar dock hög tillgänglighet.

  ![VPN-tunnlar](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Via IPsec-tunnelns par upprättas en eBGP-session för att utväxla privata nätverks vägar. Följande diagram visar eBGP-sessionen som upprättats via IPsec-tunnel paret:

  ![eBGP-sessioner över tunnel par](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Följande diagram visar den abstrakta översikten över exempel nätverket:

  ![exempel nätverk](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Om exempel på Azure Resource Manager mallar

I exemplen konfigureras VPN-gatewayen och IPsec-tunnelns upphör ande med en Azure Resource Manager-mall. Om du är nybörjare på att använda Resource Manager-mallar, eller om du vill förstå grunderna i Resource Manager-mallen, kan du läsa [förstå strukturen och syntaxen för Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md). Mallen i det här avsnittet skapar en Bygg Azure-miljö (VNet). Men om du har ett befintligt VNet kan du referera till det i mallen. Om du inte är bekant med VPN-gatewayer för IPsec/IKE plats-till-plats-konfigurationer, se [skapa en plats-till-plats-anslutning](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Du behöver inte använda Azure Resource Manager mallar för att skapa den här konfigurationen. Du kan skapa den här konfigurationen med hjälp av Azure Portal eller PowerShell.
>
>

### <a name="31-declare-the-variables"></a><a name="variables3"></a>3,1 deklarera variablerna

I det här exemplet motsvarar variabel deklarationerna exempel nätverket. När du deklarerar variabler ändrar du det här avsnittet för att avspegla din miljö.

* Variabeln **localAddressPrefix** är en matris med lokala IP-adresser som avslutar IPSec-tunnlarna.
* **GatewaySku** identifierar VPN-dataflödet. Mer information om gatewaySku och vpnType finns [VPN gateway konfigurations inställningar](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). För priser, se [VPN gateway prissättning](https://azure.microsoft.com/pricing/details/vpn-gateway).
* Ange **vpnType** till **routningsbaserad**.

```json
"variables": {
  "virtualNetworkName": "SecureVNet",       // Name of the Azure VNet
  "azureVNetAddressPrefix": "10.2.0.0/24",  // Address space assigned to the VNet
  "subnetName": "Tenant",                   // subnet name in which tenants exists
  "subnetPrefix": "10.2.0.0/25",            // address space of the tenant subnet
  "gatewaySubnetPrefix": "10.2.0.224/27",   // address space of the gateway subnet
  "localGatewayName": "localGW1",           // name of remote gateway (on-premises)
  "localGatewayIpAddress": "X.243.229.110", // public IP address of the on-premises VPN device
  "localAddressPrefix": [
    "172.16.0.1/32",                        // termination of IPsec tunnel-1 on-premises 
    "172.16.0.2/32"                         // termination of IPsec tunnel-2 on-premises 
  ],
  "gatewayPublicIPName1": "vpnGwVIP1",    // Public address name of the first VPN gateway instance
  "gatewayPublicIPName2": "vpnGwVIP2",    // Public address name of the second VPN gateway instance 
  "gatewayName": "vpnGw",                 // Name of the Azure VPN gateway
  "gatewaySku": "VpnGw1",                 // Azure VPN gateway SKU
  "vpnType": "RouteBased",                // type of VPN gateway
  "sharedKey": "string",                  // shared secret needs to match with on-premises configuration
  "asnVpnGateway": 65000,                 // BGP Autonomous System number assigned to the VPN Gateway 
  "asnRemote": 65010,                     // BGP Autonmous Syste number assigned to the on-premises device
  "bgpPeeringAddress": "172.16.0.3",      // IP address of the remote BGP peer on-premises
  "connectionName": "vpn2local1",
  "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]",
  "gatewaySubnetRef": "[concat(variables('vnetID'),'/subnets/','GatewaySubnet')]",
  "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
  "api-version": "2017-06-01"
},
```

### <a name="32-create-virtual-network-vnet"></a><a name="vnet"></a>3,2 Skapa virtuellt nätverk (VNet)

Om du associerar ett befintligt VNet med VPN-tunnlar kan du hoppa över det här steget.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[variables('azureVNetAddressPrefix')]"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "[variables('subnetPrefix')]"
        }
      },
      {
        "name": "GatewaySubnet",
        "properties": {
          "addressPrefix": "[variables('gatewaySubnetPrefix')]"
        }
      }
    ]
  },
  "comments": "Create a Virtual Network with Subnet1 and Gatewaysubnet"
},
```

### <a name="33-assign-public-ip-addresses-to-vpn-gateway-instances"></a><a name="ip"></a>3,3 tilldela offentliga IP-adresser till VPN gateway-instanser
 
Tilldela en offentlig IP-adress för varje instans av en VPN-gateway.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName1')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the first instance of the VPN gateway"
  },
  {
    "apiVersion": "[variables('api-version')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName2')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the second instance of the VPN gateway"
  },
```

### <a name="34-specify-the-on-premises-vpn-tunnel-termination-local-network-gateway"></a><a name="termination"></a>3,4 Ange den lokala VPN-tunnelns avslutning (lokal nätverksgateway)

Lokala VPN-enheter kallas **lokal** nätverksgateway. Följande JSON-kodfragment anger också information om fjärrdatorns BGP-peer:

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/localNetworkGateways",
  "name": "[variables('localGatewayName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "localNetworkAddressSpace": {
      "addressPrefixes": "[variables('localAddressPrefix')]"
    },
    "gatewayIpAddress": "[variables('localGatewayIpAddress')]",
    "bgpSettings": {
      "asn": "[variables('asnRemote')]",
      "bgpPeeringAddress": "[variables('bgpPeeringAddress')]",
      "peerWeight": 0
    }
  },
  "comments": "Local Network Gateway (referred to your on-premises location) with IP address of remote tunnel peering and IP address of remote BGP peer"
},
```

### <a name="35-create-the-vpn-gateway"></a><a name="creategw"></a>3,5 skapa VPN-gateway

Det här avsnittet i mallen konfigurerar VPN-gatewayen med de inställningar som krävs för en aktiv-aktiv konfiguration. Tänk på följande krav:

* Skapa VPN-gatewayen med en **"routningsbaserad"** -VpnType. Den här inställningen är obligatorisk om du vill aktivera BGP-routning mellan VPN-gatewayen och VPN lokalt.
* För att upprätta VPN-tunnlar mellan de två instanserna av VPN-gatewayen och en angiven lokal enhet i aktivt-aktivt läge, anges parametern **"activeActive"** till **True** i Resource Manager-mallen. Mer information om VPN-gatewayer med hög tillgänglighet finns i [anslutningar med hög tillgänglighet VPN gateway](../vpn-gateway/vpn-gateway-highlyavailable.md).
* Om du vill konfigurera eBGP-sessioner mellan VPN-tunnlarna måste du ange två olika ASN: er på båda sidorna. Det är bättre att ange privata ASN-nummer. Mer information finns i [Översikt över BGP-och Azure VPN-gatewayer](../vpn-gateway/vpn-gateway-bgp-overview.md).

```json
{
"apiVersion": "[variables('api-version')]",
"type": "Microsoft.Network/virtualNetworkGateways",
"name": "[variables('gatewayName')]",
"location": "[resourceGroup().location]",
"dependsOn": [
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName1'))]",
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName2'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
],
"properties": {
  "ipConfigurations": [
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName1'))]"
        }
      },
      "name": "vnetGtwConfig1"
    },
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName2'))]"
        }
      },
          "name": "vnetGtwConfig2"
        }
      ],
      "sku": {
        "name": "[variables('gatewaySku')]",
        "tier": "[variables('gatewaySku')]"
      },
      "gatewayType": "Vpn",
      "vpnType": "[variables('vpnType')]",
      "enableBgp": true,
      "activeActive": true,
      "bgpSettings": {
        "asn": "[variables('asnVpnGateway')]"
      }
    },
    "comments": "VPN Gateway in active-active configuration with BGP support"
  },
```

### <a name="36-establish-the-ipsec-tunnels"></a><a name="ipsectunnel"></a>3,6 upprätta IPsec-tunnlar

Den slutliga åtgärden i skriptet skapar IPsec-tunnlar mellan Azure VPN-gatewayen och den lokala VPN-enheten.

```json
{
  "apiVersion": "[variables('api-version')]",
  "name": "[variables('connectionName')]",
  "type": "Microsoft.Network/connections",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworkGateways/', variables('gatewayName'))]",
    "[concat('Microsoft.Network/localNetworkGateways/', variables('localGatewayName'))]"
  ],
  "properties": {
    "virtualNetworkGateway1": {
      "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('gatewayName'))]"
    },
    "localNetworkGateway2": {
      "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('localGatewayName'))]"
    },
    "connectionType": "IPsec",
    "routingWeight": 0,
    "sharedKey": "[variables('sharedKey')]",
    "enableBGP": "true"
  },
  "comments": "Create a Connection type site-to-site (IPsec) between the Azure VPN Gateway and the VPN device on-premises"
  }
```

## <a name="4-configure-the-on-premises-vpn-device"></a><a name="device"></a>4. Konfigurera den lokala VPN-enheten

Azure VPN-gatewayen är kompatibel med många VPN-enheter från olika leverantörer. Information om konfigurations information och enheter som har verifierats för att fungera med VPN gateway finns i [om VPN-enheter](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

När du konfigurerar VPN-enheten behöver du följande objekt:

* En delad nyckel. Det här är samma delade nyckel som du anger när du skapar en plats-till-plats-VPN-anslutning. I exemplen används en grundläggande delad nyckel. Vi rekommenderar att du skapar och använder en mer komplex nyckel.
* Den offentliga IP-adressen för din VPN-gateway. Du kan visa den offentliga IP-adressen genom att använda Azure Portal, PowerShell eller CLI. Om du använder Azure Portal hittar du VPN-gatewayens offentliga IP-adress genom att gå till Virtuella nätverksgatewayer och klicka på namnet på din gateway.

Vanligt vis är eBGP-peer-datorer direkt anslutna (ofta via en WAN-anslutning). Men när du konfigurerar eBGP över IPsec-tunnlar via ExpressRoute Microsoft-peering finns det flera routningsdomäner mellan eBGP-peer-datorer. Använd kommandot **EBGP-multihop** för att upprätta en EBGP grann relation mellan två icke-direktanslutna peer-datorer. Det heltal som följer efter kommandot EBGP-multihop anger TTL-värdet i BGP-paketen. Kommandot **Max-Paths eibgp 2** aktiverar belastnings utjämning för trafik mellan de två BGP-Sök vägarna.

### <a name="cisco-csr1000-example"></a><a name="cisco1"></a>Cisco CSR1000-exempel

I följande exempel visas konfigurationen för Cisco-CSR1000 på en virtuell Hyper-V-dator som den lokala VPN-enheten:

```
!
crypto ikev2 proposal az-PROPOSAL
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy az-POLICY
 proposal az-PROPOSAL
!
crypto ikev2 keyring key-peer1
 peer azvpn1
  address 52.175.253.112
  pre-shared-key secret*1234
 !
!
crypto ikev2 keyring key-peer2
 peer azvpn2
  address 52.175.250.191
  pre-shared-key secret*1234
 !
!
!
crypto ikev2 profile az-PROFILE1
 match address local interface GigabitEthernet1
 match identity remote address 52.175.253.112 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer1
!
crypto ikev2 profile az-PROFILE2
 match address local interface GigabitEthernet1
 match identity remote address 52.175.250.191 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer2
!
crypto ikev2 dpd 10 2 on-demand
!
!
crypto ipsec transform-set az-IPSEC-PROPOSAL-SET esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile az-VTI1
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE1
!
crypto ipsec profile az-VTI2
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE2
!
!
interface Loopback0
 ip address 172.16.0.3 255.255.255.255
!
interface Tunnel0
 ip address 172.16.0.1 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.253.112
 tunnel protection ipsec profile az-VTI1
!
interface Tunnel1
 ip address 172.16.0.2 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.250.191
 tunnel protection ipsec profile az-VTI2
!
interface GigabitEthernet1
 description External interface
 ip address x.243.229.110 255.255.255.252
 negotiation auto
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
router bgp 65010
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 network 10.0.0.0 mask 255.255.255.0
 network 10.1.10.0 mask 255.255.255.128
 neighbor 10.2.0.228 remote-as 65000
 neighbor 10.2.0.228 ebgp-multihop 5
 neighbor 10.2.0.228 update-source Loopback0
 neighbor 10.2.0.228 soft-reconfiguration inbound
 neighbor 10.2.0.228 filter-list 10 out
 neighbor 10.2.0.229 remote-as 65000    
 neighbor 10.2.0.229 ebgp-multihop 5
 neighbor 10.2.0.229 update-source Loopback0
 neighbor 10.2.0.229 soft-reconfiguration inbound
 maximum-paths eibgp 2
!
ip route 0.0.0.0 0.0.0.0 10.1.10.1
ip route 10.2.0.228 255.255.255.255 Tunnel0
ip route 10.2.0.229 255.255.255.255 Tunnel1
!
```

## <a name="5-configure-vpn-device-filtering-and-firewalls-optional"></a><a name="firewalls"></a>5. Konfigurera VPN-enhets filtrering och brand väggar (valfritt)

Konfigurera brand väggen och filtreringen enligt dina krav.

## <a name="6-test-and-validate-the-ipsec-tunnel"></a><a name="testipsec"></a>6. testa och verifiera IPsec-tunneln

Status för IPsec-tunnlar kan verifieras i Azure VPN-gatewayen med PowerShell-kommandon:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Exempel på utdata:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Använd följande exempel för att kontrol lera statusen för tunnlarna på Azure VPN gateway-instanserna oberoende av:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Exempel på utdata:

```azurepowershell
Tunnel                           : vpn2local1_52.175.250.191
ConnectionStatus                 : Connected
IngressBytesTransferred          : 4877438
EgressBytesTransferred           : 8754071
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:30

Tunnel                           : vpn2local1_52.175.253.112
ConnectionStatus                 : Connected
IngressBytesTransferred          : 5660773
EgressBytesTransferred           : 8980589
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:13
```

Du kan också kontrol lera tunnel statusen på din lokala VPN-enhet.

Cisco CSR1000-exempel:

```
show crypto session detail
show crypto ikev2 sa
show crypto ikev2 session detail
show crypto ipsec sa
```

Exempel på utdata:

```
csr1#show crypto session detail

Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: az-PROFILE2
Uptime: 00:52:46
Session status: UP-ACTIVE
Peer: 52.175.250.191 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.250.191
      Desc: (none)
  Session ID: 3
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.250.191/4500 Active
          Capabilities:DN connid:3 lifetime:23:07:14
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 279 drop 0 life (KB/Sec) 4607976/433
        Outbound: #pkts enc'ed 164 drop 0 life (KB/Sec) 4607992/433

Interface: Tunnel0
Profile: az-PROFILE1
Uptime: 00:52:43
Session status: UP-ACTIVE
Peer: 52.175.253.112 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.253.112
      Desc: (none)
  Session ID: 2
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.253.112/4500 Active
          Capabilities:DN connid:2 lifetime:23:07:17
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 668 drop 0 life (KB/Sec) 4607926/437
        Outbound: #pkts enc'ed 477 drop 0 life (KB/Sec) 4607953/437
```

Rad protokollet i det virtuella tunnel gränssnittet (VTI) ändras inte till "upp" förrän IKE-fas 2 har slutförts. Följande kommando verifierar säkerhets associationen:

```
csr1#show crypto ikev2 sa

IPv4 Crypto IKEv2  SA

Tunnel-id Local                 Remote                fvrf/ivrf            Status
2         10.1.10.50/4500       52.175.253.112/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3277 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status
3         10.1.10.50/4500       52.175.250.191/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3280 sec

IPv6 Crypto IKEv2  SA

csr1#show crypto ipsec sa | inc encaps|decaps
    #pkts encaps: 177, #pkts encrypt: 177, #pkts digest: 177
    #pkts decaps: 296, #pkts decrypt: 296, #pkts verify: 296
    #pkts encaps: 554, #pkts encrypt: 554, #pkts digest: 554
    #pkts decaps: 746, #pkts decrypt: 746, #pkts verify: 746
```

### <a name="verify-end-to-end-connectivity-between-the-inside-network-on-premises-and-the-azure-vnet"></a><a name="verifye2e"></a>Verifiera slutpunkt-till-slutpunkt-anslutningen mellan det lokala nätverket och det virtuella Azure-nätverket

Om IPsec-tunnlarna är upp och de statiska vägarna är korrekt inställda, ska du kunna pinga IP-adressen för den fjärranslutna BGP-peer-datorn:

```
csr1#ping 10.2.0.228
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/5/5 ms

#ping 10.2.0.229
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.229, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/6 ms
```

### <a name="verify-the-bgp-sessions-over-ipsec"></a><a name="verifybgp"></a>Verifiera BGP-sessioner via IPsec

Verifiera statusen för BGP-peer på Azure VPN-gatewayen:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Exempel på utdata:

```azurepowershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

För att verifiera listan över nätverks prefix som tas emot via eBGP från VPN-koncentratoren lokalt kan du filtrera efter attribut "Origin":

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

I exempel resultatet är ASN 65010 det autonoma system numret för BGP i VPN lokalt.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

Så här visar du en lista över annonserade vägar:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Exempel på utdata:

```azurepowershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

Exempel för lokal Cisco-CSR1000:

```
csr1#show ip bgp neighbors 10.2.0.228 routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.2.0.0/24      10.2.0.228                             0 65000 i
 r>   172.16.0.1/32    10.2.0.228                             0 65000 i
 r>   172.16.0.2/32    10.2.0.228                             0 65000 i
 r>   172.16.0.3/32   10.2.0.228                             0 65000 i

Total number of prefixes 4
```

Listan över nätverk som annonseras från den lokala Cisco-CSR1000 till Azure VPN-gatewayen kan listas med hjälp av följande kommando:

```
csr1#show ip bgp neighbors 10.2.0.228 advertised-routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.0.0.0/24      0.0.0.0                  0         32768 i
 *>   10.1.10.0/25     0.0.0.0                  0         32768 i

Total number of prefixes 2
```

## <a name="next-steps"></a>Nästa steg

* [Konfigurera övervakare av nätverksprestanda för ExpressRoute](how-to-npm.md)

* [Lägga till en plats-till-plats-anslutning till ett VNet med en befintlig VPN gateway-anslutning](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
