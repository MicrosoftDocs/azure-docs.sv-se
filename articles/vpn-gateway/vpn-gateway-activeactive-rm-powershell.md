---
title: Konfigurera aktiva aktiva S2S Azure VPN Gateway-anslutningar
description: Den här artikeln vägleder dig genom konfigurationen av aktiva-aktiva anslutningar med Azure VPN-gatewayer med hjälp av Azure Resource Manager och PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.reviewer: cherylmc
ms.openlocfilehash: 022ccaab0b210cd2d656b69f505791d1a2aa963f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89440787"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Konfigurera aktiva, aktiva S2S VPN-anslutningar med Azure VPN-gatewayer

Den här artikeln vägleder dig genom stegen för att skapa aktiva, aktiva anslutningar mellan olika platser och VNet-till-VNet med hjälp av Resource Manager-distributions modellen och PowerShell. Du kan också konfigurera en aktiv-aktiv gateway i Azure Portal.

## <a name="about-highly-available-cross-premises-connections"></a>Om anslutningar mellan lokala anslutningar med hög tillgänglighet
För att uppnå hög tillgänglighet för anslutningar mellan olika platser och VNet-till-VNet bör du distribuera flera VPN-gatewayer och upprätta flera parallella anslutningar mellan dina nätverk och Azure. Se [hög tillgänglighet mellan lokala och VNET-till-VNET-anslutning](vpn-gateway-highlyavailable.md) för en översikt över anslutnings alternativ och topologi.

Den här artikeln innehåller instruktioner för att konfigurera en aktiv-aktiv VPN-anslutning mellan olika platser och aktiv-aktiv anslutning mellan två virtuella nätverk.

* [Del 1 – Skapa och konfigurera din Azure VPN-gateway i aktivt-aktivt läge](#aagateway)
* [Del 2 – upprätta aktiva, aktiva anslutningar mellan platser](#aacrossprem)
* [Del 3 – upprätta Active-aktiva VNet-till-VNet-anslutningar](#aav2v)

Om du redan har en VPN-gateway kan du:
* [Uppdatera en befintlig VPN-gateway från aktiv-standby till aktiv-aktiv eller vice versa](#aaupdate)

Du kan kombinera dessa tillsammans för att bygga en mer komplex nätverks topologi med hög tillgänglighet som uppfyller dina behov.

> [!IMPORTANT]
> Det aktiva aktiva läget är tillgängligt för alla SKU: er förutom Basic.

## <a name="part-1---create-and-configure-active-active-vpn-gateways"></a><a name ="aagateway"></a>Del 1 – Skapa och konfigurera aktiva-aktiva VPN-gatewayer
Följande steg konfigurerar din Azure VPN-gateway i aktiva-aktiva lägen. De viktigaste skillnaderna mellan de aktiva och aktiva standby-gatewayerna:

* Du måste skapa två Gateway-IP-konfigurationer med två offentliga IP-adresser
* Du måste ange flaggan EnableActiveActiveFeature
* Gateway-SKU: n måste vara VpnGw1, VpnGw2, VpnGw3 eller HighPerformance (äldre SKU).

De andra egenskaperna är desamma som de som inte är aktiva-aktiva gatewayer. 

### <a name="before-you-begin"></a>Innan du börjar
* Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* Du måste installera Azure Resource Manager PowerShell-cmdlets om du inte vill använda CloudShell i webbläsaren. Mer information om hur du installerar PowerShell-cmdlets finns i [Översikt över Azure PowerShell](/powershell/azure/) .

### <a name="step-1---create-and-configure-vnet1"></a>Steg 1 – Skapa och konfigurera VNet1
#### <a name="1-declare-your-variables"></a>1. deklarera dina variabler

I den här övningen börjar vi med att deklarera våra variabler. Om du använder Cloud Shell "prova" kan du ansluta automatiskt till ditt konto. Om du använder PowerShell lokalt kan du använda följande exempel för att ansluta:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
```

I exemplet nedan deklarerar vi variablerna med de värden som gäller för den här övningen. Se till att ersätta värdena med dina egna när du konfigurerar för produktion. Du kan använda dessa variabler om du använder anvisningarna för att bekanta dig med den här typen av konfiguration. Ändra variablerna samt kopiera och klistra in dem i PowerShell-konsolen.

```azurepowershell-interactive
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-create-a-new-resource-group"></a>2. skapa en ny resurs grupp

Använd exemplet nedan för att skapa en ny resurs grupp:

```azurepowershell-interactive
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. skapa TestVNet1
Exemplet nedan skapar ett virtuellt nätverk med namnet TestVNet1 och tre undernät, där ett kallas GatewaySubnet, ett kallas FrontEnd och ett kallas BackEnd. När du ersätter värden är det viktigt att du alltid namnger gateway-undernätet specifikt till GatewaySubnet. Om du ger det något annat namn går det inte att skapa gatewayen.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Steg 2 – Skapa VPN-gateway för TestVNet1 med aktivt-aktivt läge
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. skapa offentliga IP-adresser och Gateway-IP-konfigurationer
Begär två offentliga IP-adresser som ska allokeras till den gateway som du ska skapa för ditt VNet. Du definierar också de undernät och IP-konfigurationer som krävs.

```azurepowershell-interactive
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Skapa VPN gateway med aktiv-aktiv konfiguration
Skapa den virtuella nätverksgatewayen för TestVNet1. Observera att det finns två GatewayIpConfig-poster och flaggan EnableActiveActiveFeature har angetts. Det kan ta en stund att skapa en gateway (45 minuter eller mer).

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Hämta gatewayens offentliga IP-adresser och IP-adressen för BGP-peer
När gatewayen har skapats måste du hämta IP-adressen för BGP-peer på Azure-VPN Gateway. Den här adressen krävs för att konfigurera Azure-VPN Gateway som en BGP-peer för dina lokala VPN-enheter.

```azurepowershell-interactive
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Använd följande cmdlets för att visa de två offentliga IP-adresser som allokerats för din VPN-gateway och deras motsvarande BGP-peer-IP-adresser för varje gateway-instans:

```azurepowershell-interactive
PS D:\> $gw1pip1.IpAddress
40.112.190.5

PS D:\> $gw1pip2.IpAddress
138.91.156.129

PS D:\> $vnet1gw.BgpSettingsText
{
  "Asn": 65010,
  "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
  "PeerWeight": 0
}
```

Ordningen på offentliga IP-adresser för gateway-instanserna och motsvarande BGP-peering-adresser är desamma. I det här exemplet använder den virtuella gateway-datorn med offentlig IP-adress för 40.112.190.5 10.12.255.4 som sin BGP-peering-adress, och gatewayen med 138.91.156.129 kommer att använda 10.12.255.5. Den här informationen behövs när du konfigurerar dina lokala VPN-enheter som ansluter till den aktiva-aktiva gatewayen. Gatewayen visas i diagrammet nedan med alla adresser:

![aktiv-aktiv Gateway](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

När gatewayen har skapats kan du använda den här gatewayen för att upprätta en aktiv-aktiv anslutning mellan platser eller VNet-till-VNet-anslutning. Följande avsnitt beskriver stegen för att slutföra övningen.

## <a name="part-2---establish-an-active-active-cross-premises-connection"></a><a name ="aacrossprem"></a>Del 2 – upprätta en aktiv-aktiv anslutning mellan platser
Om du vill upprätta en anslutning mellan olika platser måste du skapa en lokal nätverksgateway som representerar din lokala VPN-enhet och en anslutning för att ansluta Azure VPN-gatewayen till den lokala Nätverksgatewayen. I det här exemplet är Azure VPN-gatewayen i aktivt-aktivt läge. Det innebär att även om det bara finns en lokal VPN-enhet (lokal nätverksgateway) och en anslutnings resurs, upprättar båda Azure VPN gateway-instanserna VPN-tunnlar med den lokala enheten.

Se till att du har slutfört [del 1](#aagateway) av den här övningen innan du fortsätter.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Steg 1 – Skapa och konfigurera den lokala Nätverksgatewayen
#### <a name="1-declare-your-variables"></a>1. deklarera dina variabler
Den här övningen fortsätter att bygga konfigurationen som visas i diagrammet. Ersätt värdena med de som du vill använda för din konfiguration.

```azurepowershell-interactive
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Några saker att tänka på för lokala nätverksgateway:

* Den lokala Nätverksgatewayen kan finnas på samma eller en annan plats och resurs grupp som VPN-gatewayen. Det här exemplet visar dem i olika resurs grupper men på samma Azure-plats.
* Om det bara finns en lokal VPN-enhet som visas ovan kan den aktiva-aktiva anslutningen fungera med eller utan BGP-protokoll. I det här exemplet används BGP för anslutningen mellan platser.
* Om BGP är aktiverat är det prefix du behöver deklarera för den lokala Nätverksgatewayen är värd adressen för din IP-adress för BGP-peer på din VPN-enhet. I det här fallet är det a/32-prefixet "10.52.255.253/32".
* Som en påminnelse måste du använda olika BGP-ASN: er mellan dina lokala nätverk och Azure VNet. Om de är desamma måste du ändra ditt VNet ASN om din lokala VPN-enhet redan använder ASN för att peer-koppla med andra BGP-grannar.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. skapa den lokala Nätverksgatewayen för site5
Innan du fortsätter kontrollerar du att du fortfarande är ansluten till Prenumeration 1. Skapa resurs gruppen om den inte redan har skapats.

```azurepowershell-interactive
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Steg 2 – ansluta VNet-gatewayen och den lokala Nätverksgatewayen
#### <a name="1-get-the-two-gateways"></a>1. Hämta de två gatewayerna

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. skapa TestVNet1-till-site5-anslutningen
I det här steget skapar du anslutningen från TestVNet1 till Site5_1 med "EnableBGP" inställd på $True.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. VPN-och BGP-parametrar för din lokala VPN-enhet
Exemplet nedan visar en lista över de parametrar som du kommer att ange i avsnittet BGP-konfiguration på din lokala VPN-enhet för den här övningen:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.253
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Anslutningen ska upprättas efter några minuter och BGP-peering-sessionen startar när IPsec-anslutningen har upprättats. I det här exemplet har vi bara konfigurerat en lokal VPN-enhet, vilket resulterar i diagrammet som visas nedan:

![aktiv-aktiv-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Steg 3 – ansluta två lokala VPN-enheter till den aktiva, aktiva VPN-gatewayen
Om du har två VPN-enheter i samma lokala nätverk kan du uppnå dubbel redundans genom att ansluta Azure VPN-gatewayen till den andra VPN-enheten.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. skapa den andra lokala Nätverksgatewayen för site5
Gatewayens IP-adress, adressprefix och BGP-peering-adress för den andra lokala Nätverksgatewayen får inte överlappa den tidigare lokala Nätverksgatewayen för samma lokala nätverk.

```azurepowershell-interactive
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Anslut VNet-gatewayen och den andra lokala Nätverksgatewayen
Skapa anslutningen från TestVNet1 till Site5_2 med inställningen "EnableBGP" till $True

```azurepowershell-interactive
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. VPN-och BGP-parametrar för din andra lokala VPN-enhet
På samma sätt visar en lista över de parametrar som du kommer att ange i den andra VPN-enheten:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

När anslutningen (tunnlarna) har upprättats har du dubbla redundanta VPN-enheter och tunnlar som ansluter ditt lokala nätverk och Azure:

![dubbel redundans – crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name="part-3---establish-an-active-active-vnet-to-vnet-connection"></a><a name ="aav2v"></a>Del 3 – upprätta en aktiv-aktiv VNet-till-VNet-anslutning
Det här avsnittet skapar en aktiv-aktiv VNet-till-VNet-anslutning med BGP. 

Anvisningarna nedan fortsätter från föregående steg ovan. Du måste slutföra [del 1](#aagateway) för att skapa och konfigurera TestVNet1 och VPN gateway med BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Steg 1 – Skapa TestVNet2 och VPN-gatewayen
Det är viktigt att se till att IP-adressutrymmet för det nya virtuella nätverket, TestVNet2, inte överlappar något av dina VNet-intervall.

I det här exemplet tillhör de virtuella nätverken samma prenumeration. Du kan konfigurera VNet-till-VNet-anslutningar mellan olika prenumerationer. Mer information finns i [Konfigurera en VNet-till-VNET-anslutning](vpn-gateway-vnet-vnet-rm-ps.md) . Se till att lägga till "-EnableBgp $True" när du skapar anslutningarna för att aktivera BGP.

#### <a name="1-declare-your-variables"></a>1. deklarera dina variabler
Ersätt värdena med de som du vill använda för din konfiguration.

```azurepowershell-interactive
$RG2 = "TestAARG2"
$Location2 = "East US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. skapa TestVNet2 i den nya resurs gruppen

```azurepowershell-interactive
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. skapa en aktiv-aktiv VPN-gateway för TestVNet2
Begär två offentliga IP-adresser som ska allokeras till den gateway som du ska skapa för ditt VNet. Du definierar också de undernät och IP-konfigurationer som krävs.

```azurepowershell-interactive
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Skapa VPN-gatewayen med AS-och flaggan "EnableActiveActiveFeature". Observera att du måste åsidosätta standard-ASN på dina Azure VPN-gatewayer. ASN: er för den anslutna virtuella nätverk måste vara olika för att aktivera BGP och transit routning.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Steg 2 – ansluta TestVNet1-och TestVNet2-gatewayerna
I det här exemplet finns båda gatewayerna i samma prenumeration. Du kan slutföra det här steget i samma PowerShell-session.

#### <a name="1-get-both-gateways"></a>1. Hämta båda gatewayerna
Kontrollera att du har loggat in och anslutit till Prenumeration 1.

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. skapa båda anslutningarna
I det här steget ska du skapa anslutningen från TestVNet1 till TestVNet2 och anslutningen från TestVNet2 till TestVNet1.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Se till att aktivera BGP för båda anslutningarna.
> 
> 

När du har slutfört de här stegen upprättas anslutningen på några minuter och BGP-peering-sessionen kommer att vara upp när VNet-till-VNet-anslutningen har slutförts med dubbel redundans:

![aktiv-aktiv-V2V](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name="update-an-existing-vpn-gateway"></a><a name ="aaupdate"></a>Uppdatera en befintlig VPN-gateway

När du ändrar en Active-standby-Gateway till aktiv-aktiv skapar du en annan offentlig IP-adress och lägger sedan till en andra Gateway-IP-konfiguration. Det här avsnittet hjälper dig att ändra en befintlig Azure VPN-gateway från aktiv-standby till aktivt-aktivt läge eller vice versa med PowerShell. Du kan också ändra en gateway i Azure Portal på **konfigurations** sidan för den virtuella Nätverksgatewayen.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Ändra en gateway med aktiv standby till en aktiv-aktiv Gateway

I följande exempel konverteras en aktiv-standby-Gateway till en aktiv-aktiv Gateway. 

#### <a name="1-declare-your-variables"></a>1. deklarera dina variabler

Ersätt följande parametrar som används för exemplen med de inställningar som du behöver för din egen konfiguration och deklarera sedan dessa variabler.

```azurepowershell-interactive
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

När du har deklarerat variablerna kan du kopiera och klistra in det här exemplet till din PowerShell-konsol.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. skapa den offentliga IP-adressen och Lägg sedan till IP-konfigurationen för den andra gatewayen

```azurepowershell-interactive
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Aktivera aktivt-aktivt läge och uppdatera gatewayen

I det här steget aktiverar du aktivt-aktivt läge och uppdaterar gatewayen. I exemplet använder VPN gateway för närvarande en äldre standard-SKU. Aktiv-aktiv stöder dock inte standard-SKU: n. Om du vill ändra storlek på den äldre SKU: n till en som stöds (i det här fallet HighPerformance) anger du bara den äldre SKU som stöds som du vill använda.

* Du kan inte ändra en äldre SKU till en av de nya SKU: erna med det här steget. Du kan bara ändra storlek på en äldre SKU till en annan äldre SKU som stöds. Du kan till exempel inte ändra SKU från standard till VpnGw1 (även om VpnGw1 stöds för aktiv-aktiv) eftersom standard är en äldre SKU och VpnGw1 är en aktuell SKU. Mer information om hur du ändrar storlek och migrerar SKU: er finns i [Gateway-SKU: er](vpn-gateway-about-vpngateways.md#gwsku).

* Om du vill ändra storlek på en aktuell SKU, till exempel VpnGw1 till VpnGw3, kan du göra det med hjälp av det här steget eftersom SKU: er finns i samma SKU-serie. Det gör du genom att använda värdet: ```-GatewaySku VpnGw3```

Om du inte behöver ändra storlek på gatewayen när du använder detta i din miljö behöver du inte ange-GatewaySku. Observera att i det här steget måste du ange Gateway-objektet i PowerShell för att utlösa den faktiska uppdateringen. Uppdateringen kan ta 30 till 45 minuter, även om du inte ändrar storlek på din gateway.

```azurepowershell-interactive
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Ändra en aktiv-aktiv Gateway till en gateway med aktiv vänte läge
#### <a name="1-declare-your-variables"></a>1. deklarera dina variabler

Ersätt följande parametrar som används för exemplen med de inställningar som du behöver för din egen konfiguration och deklarera sedan dessa variabler.

```azurepowershell-interactive
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

När du har deklarerat variablerna hämtar du namnet på den IP-konfiguration som du vill ta bort.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. ta bort gatewayens IP-konfiguration och inaktivera aktivt-aktivt läge

Använd det här exemplet för att ta bort gatewayens IP-konfiguration och inaktivera aktivt-aktivt läge. Observera att du måste ange Gateway-objektet i PowerShell för att utlösa den faktiska uppdateringen.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Uppdateringen kan ta upp till 30 till 45 minuter.

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för anvisningar.
