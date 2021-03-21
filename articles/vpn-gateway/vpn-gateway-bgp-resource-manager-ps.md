---
title: 'Azure-VPN Gateway: Konfigurera BGP: PowerShell'
description: Den här artikeln vägleder dig genom konfigurering av BGP med Azure VPN-gatewayer med hjälp av Azure Resource Manager och PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8573d9e55299382392927b532966a6e6fdd8c439
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659768"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>Så här konfigurerar du BGP på Azure VPN-gatewayer med PowerShell
Den här artikeln vägleder dig genom stegen för att aktivera BGP på en plats-till-plats-VPN-anslutning (S2S) och en VNet-till-VNet-anslutning med hjälp av distributions modellen för Resource Manager och PowerShell.



## <a name="about-bgp"></a>Om BGP
BGP är ett standardroutningsprotokoll som vanligen används på Internet för att utbyta information om routning och åtkomst mellan två eller flera nätverk. BGP gör att Azure VPN Gateway och dina lokala VPN-enheter, som kallas BGP-peers eller grannar, kan utbyta ”vägar” som informerar båda gatewayerna om åtkomsten för de prefix som ska passera genom de gatewayer eller routrar som berörs. BGP kan också möjliggöra överföringsroutning mellan flera nätverk genom att sprida vägar som BGP-gatewayen får information om från en BGP-peer till alla andra BGP-peers.

Se [Översikt över BGP med Azure VPN-gatewayer](vpn-gateway-bgp-overview.md) för mer information om fördelarna med BGP och förstå de tekniska kraven och övervägandena för att använda BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Komma igång med BGP på Azure VPN-gatewayer

Den här artikeln vägleder dig genom stegen för att utföra följande uppgifter:

* [Del 1 – aktivera BGP på Azure VPN-gatewayen](#enablebgp)
* Del 2 – upprätta en anslutning mellan platser med BGP
* [Del 3 – upprätta en VNet-till-VNet-anslutning med BGP](#v2vbgp)

Varje del av anvisningarna utgör ett grundläggande Bygg block för att aktivera BGP i din nätverks anslutning. Om du har slutfört alla tre delarna skapar du topologin som visas i följande diagram:

![BGP-topologi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Du kan kombinera delar tillsammans för att bygga ett mer komplext, flera hopp, överförings nätverk som uppfyller dina behov.

## <a name="part-1---configure-bgp-on-the-azure-vpn-gateway"></a><a name ="enablebgp"></a>Del 1 – Konfigurera BGP på Azure-VPN Gateway
Konfigurations stegen ställer in BGP-parametrarna för Azure VPN-gatewayen som visas i följande diagram:

![BGP-Gateway](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Innan du börjar
* Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* Installera PowerShell-cmdletarna för Azure Resource Manager. Mer information om hur du installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/). 

### <a name="step-1---create-and-configure-vnet1"></a>Steg 1 – Skapa och konfigurera VNet1
#### <a name="1-declare-your-variables"></a>1. deklarera dina variabler
I den här övningen börjar vi med att deklarera våra variabler. I följande exempel deklaras variablerna med hjälp av värdena för den här övningen. Se till att ersätta värdena med dina egna när du konfigurerar för produktion. Du kan använda dessa variabler om du använder anvisningarna för att bekanta dig med den här typen av konfiguration. Ändra variablerna samt kopiera och klistra in dem i PowerShell-konsolen.

```powershell
$Sub1 = "Replace_With_Your_Subscription_Name"
$RG1 = "TestBGPRG1"
$Location1 = "East US"
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
$GWIPName1 = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection12 = "VNet1toVNet2"
$Connection15 = "VNet1toSite5"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Anslut till din prenumeration och skapa en ny resurs grupp
Om du vill använda Resource Manager-cmdlets, se till att växla till PowerShell-läge. Mer information finns i [Använda Windows PowerShell med Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. skapa TestVNet1
Följande exempel skapar ett virtuellt nätverk med namnet TestVNet1 och tre undernät, ett som kallas GatewaySubnet, ett som kallas FrontEnd och en anropad Server del. När du ersätter värden är det viktigt att du alltid namnger gateway-undernätet specifikt till GatewaySubnet. Om du ger det något annat namn går det inte att skapa gatewayen.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Steg 2 – Skapa VPN Gateway för TestVNet1 med BGP-parametrar
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. skapa IP-och Subnet-konfigurationer
Begär en offentlig IP-adress som ska allokeras till den gateway som du ska skapa för det virtuella nätverket. Du definierar också nödvändiga undernät och IP-konfigurationer.

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Skapa VPN-gatewayen med AS-numret
Skapa den virtuella nätverksgatewayen för TestVNet1. BGP kräver en Route-Based VPN-gateway och även parametern addition,-ASN, för att ange ASN (AS Number) för TestVNet1. Om du inte anger ASN-parametern tilldelas ASN 65515. Att skapa en gateway kan ta ett tag (30 minuter eller mer att slutföra).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Hämta IP-adressen för Azure BGP-peer
När gatewayen har skapats måste du hämta IP-adressen för BGP-peer på Azure-VPN Gateway. Den här adressen krävs för att konfigurera Azure-VPN Gateway som en BGP-peer för dina lokala VPN-enheter.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

Det sista kommandot visar motsvarande BGP-konfigurationer på Azure-VPN Gateway; exempel:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

När gatewayen har skapats kan du använda denna gateway för att upprätta en anslutning mellan platser eller VNet-till-VNet-anslutning med BGP. Följande avsnitt beskriver stegen för att slutföra övningen.

## <a name="part-2---establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembbgp"></a>Del 2 – upprätta en anslutning mellan platser med BGP

Om du vill upprätta en anslutning mellan olika platser måste du skapa en lokal nätverksgateway som representerar din lokala VPN-enhet och en anslutning för att ansluta VPN-gatewayen till den lokala Nätverksgatewayen. Även om det finns artiklar som vägleder dig genom de här stegen innehåller den här artikeln de ytterligare egenskaper som krävs för att ange BGP-konfigurationsparametrar.

![BGP för mellan platser](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Innan du fortsätter bör du kontrol lera att du har slutfört [del 1](#enablebgp) av den här övningen.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Steg 1 – Skapa och konfigurera den lokala Nätverksgatewayen

#### <a name="1-declare-your-variables"></a>1. deklarera dina variabler

Den här övningen fortsätter att bygga konfigurationen som visas i diagrammet. Ersätt värdena med de som du vill använda för din konfiguration.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

Några saker att tänka på för lokala nätverksgateway:

* Den lokala Nätverksgatewayen kan finnas på samma eller en annan plats och resurs grupp som VPN-gatewayen. Det här exemplet visar dem i olika resurs grupper på olika platser.
* Det prefix du behöver deklarera för den lokala Nätverksgatewayen är värd adressen för din BGP-peer-IP-adress på din VPN-enhet. I det här fallet är det a/32-prefixet "10.52.255.254/32".
* Som en påminnelse måste du använda olika BGP-ASN: er mellan dina lokala nätverk och Azure VNet. Om de är desamma måste du ändra ditt VNet ASN om din lokala VPN-enhet redan använder ASN för att peer-koppla med andra BGP-grannar.

Innan du fortsätter kontrollerar du att du fortfarande är ansluten till Prenumeration 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. skapa den lokala Nätverksgatewayen för site5

Se till att skapa resurs gruppen om den inte skapas innan du skapar den lokala Nätverksgatewayen. Lägg märke till de två ytterligare parametrarna för den lokala Nätverksgatewayen: ASN och BgpPeerAddress.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Steg 2 – ansluta VNet-gatewayen och den lokala Nätverksgatewayen

#### <a name="1-get-the-two-gateways"></a>1. Hämta de två gatewayerna

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. skapa TestVNet1-till-site5-anslutningen

I det här steget skapar du anslutningen från TestVNet1 till site5. Du måste ange "-EnableBGP $True" för att aktivera BGP för den här anslutningen. Som vi nämnt tidigare är det möjligt att ha både BGP-och icke-BGP-anslutningar för samma Azure-VPN Gateway. Om inte BGP är aktiverat i anslutnings egenskapen, kommer Azure inte att aktivera BGP för den här anslutningen även om BGP-parametrar redan har kon figurer ATS på båda gatewayerna.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

I följande exempel visas de parametrar som du anger i avsnittet BGP-konfiguration på din lokala VPN-enhet för den här övningen:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Anslutningen upprättas efter några minuter och BGP-peering-sessionen startar när IPsec-anslutningen har upprättats.

## <a name="part-3---establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Del 3 – upprätta en VNet-till-VNet-anslutning med BGP

Det här avsnittet lägger till en VNet-till-VNet-anslutning med BGP, som du ser i följande diagram:

![Diagram som visar en V net-till-V-nätanslutning.](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Följande instruktioner fortsätter från föregående steg. Du måste slutföra [del i](#enablebgp) för att kunna skapa och konfigurera TestVNet1 och VPN gateway med BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Steg 1 – Skapa TestVNet2 och VPN-gatewayen

Det är viktigt att se till att IP-adressutrymmet för det nya virtuella nätverket, TestVNet2, inte överlappar något av dina VNet-intervall.

I det här exemplet tillhör de virtuella nätverken samma prenumeration. Du kan konfigurera VNet-till-VNet-anslutningar mellan olika prenumerationer. Mer information finns i [Konfigurera en VNet-till-VNET-anslutning](vpn-gateway-vnet-vnet-rm-ps.md). Se till att lägga till "-EnableBgp $True" när du skapar anslutningarna för att aktivera BGP.

#### <a name="1-declare-your-variables"></a>1. deklarera dina variabler

Ersätt värdena med de som du vill använda för din konfiguration.

```powershell
$RG2 = "TestBGPRG2"
$Location2 = "West US"
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
$GWIPName2 = "VNet2GWIP"
$GWIPconfName2 = "gwipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. skapa TestVNet2 i den nya resurs gruppen

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. Skapa VPN-gatewayen för TestVNet2 med BGP-parametrar

Begär en offentlig IP-adress som ska allokeras till den gateway som du ska skapa för ditt VNet och definiera nödvändiga undernät och IP-konfigurationer.

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Skapa VPN-gatewayen med AS-numret. Du måste åsidosätta standard-ASN på dina Azure VPN-gatewayer. ASN: er för den anslutna virtuella nätverk måste vara olika för att aktivera BGP och transit routning.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Steg 2 – ansluta TestVNet1-och TestVNet2-gatewayerna

I det här exemplet finns båda gatewayerna i samma prenumeration. Du kan slutföra det här steget i samma PowerShell-session.

#### <a name="1-get-both-gateways"></a>1. Hämta båda gatewayerna

Kontrollera att du har loggat in och anslutit till Prenumeration 1.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. skapa båda anslutningarna

I det här steget skapar du anslutningen från TestVNet1 till TestVNet2 och anslutningen från TestVNet2 till TestVNet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Se till att aktivera BGP för båda anslutningarna.
> 
> 

När du har slutfört de här stegen upprättas anslutningen efter några minuter. BGP-peering-sessionen är upp när VNet-till-VNet-anslutningen har slutförts.

Om du har slutfört alla tre delarna i den här övningen har du upprättat följande nätverkstopologi:

![BGP för VNet-till-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/windows/quick-create-portal.md) för anvisningar.