---
title: IPsec/IKE-princip för S2S VPN & VNet-till-VNet-anslutningar
titleSuffix: Azure VPN Gateway
description: Konfigurera IPsec/IKE-princip för S2S-eller VNet-till-VNet-anslutningar med Azure VPN-gatewayer med hjälp av Azure Resource Manager och PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 96931d2dd94a8a31021ebe62caaefc54f643b007
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94649270"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>Konfigurera IPsec-/IKE-princip för S2S VPN-anslutningar eller VNet-till-VNet-anslutningar

Den här artikeln vägleder dig genom stegen för att konfigurera IPsec/IKE-princip för plats-till-plats-VPN eller VNet-till-VNet-anslutningar med hjälp av distributions modellen för Resource Manager och PowerShell.



## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a><a name="about"></a>Om IPsec-och IKE-principinställningar för Azure VPN-gatewayer
IPsec-och IKE-protokoll standarden stöder en mängd olika krypteringsalgoritmer i olika kombinationer. Läs mer [om kryptografiska krav och Azure VPN-gatewayer](vpn-gateway-about-compliance-crypto.md) för att se hur detta kan hjälpa till att säkerställa att anslutningar mellan lokala nätverk och VNET-till-VNet fungerar som uppfyller dina krav på efterlevnad eller säkerhet.

Den här artikeln innehåller anvisningar för att skapa och konfigurera en IPsec/IKE-princip och tillämpa den på en ny eller befintlig anslutning:

* [Del 1 – arbets flöde för att skapa och ange IPsec/IKE-princip](#workflow)
* [Del 2 – kryptografiska algoritmer och nyckel längder som stöds](#params)
* [Del 3 – skapa en ny S2S VPN-anslutning med IPsec/IKE-princip](#crossprem)
* [Del 4 – skapa en ny VNet-till-VNet-anslutning med IPsec/IKE-princip](#vnet2vnet)
* [Del 5 – hantera (skapa, lägga till, ta bort) IPsec/IKE-princip för en anslutning](#managepolicy)

> [!IMPORTANT]
> 1. Observera att IPsec/IKE-principen endast fungerar på följande Gateway-SKU: er:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (Route-based)
>    * ***Standard** _ och _ *_HighPerformance_** (Route-based)
> 2. Du kan bara ange ***en*** principkombination för en viss anslutning.
> 3. Du måste ange alla algoritmer och parametrar för både IKE (huvud läge) och IPsec (snabb läge). Partiell principspecifikationen tillåts inte.
> 4. Kontakta din VPN-enhets specifikationer för att se till att principen stöds på dina lokala VPN-enheter. S2S-eller VNet-till-VNet-anslutningar kan inte upprätta om principerna är inkompatibla.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a><a name ="workflow"></a>Del 1 – arbets flöde för att skapa och ange IPsec/IKE-princip
Det här avsnittet beskriver arbets flödet för att skapa och uppdatera IPsec/IKE-princip på en S2S VPN-eller VNet-till-VNet-anslutning:
1. Skapa ett virtuellt nätverk och en VPN-gateway
2. Skapa en lokal nätverksgateway för anslutning mellan platser eller ett annat virtuellt nätverk och en gateway för VNet-till-VNet-anslutning
3. Skapa en IPsec/IKE-princip med valda algoritmer och parametrar
4. Skapa en anslutning (IPsec eller VNet2VNet) med IPsec/IKE-principen
5. Lägg till/uppdatera/ta bort en IPsec/IKE-princip för en befintlig anslutning

Anvisningarna i den här artikeln hjälper dig att konfigurera och konfigurera IPsec/IKE-principer som visas i diagrammet:

![IPSec-IKE – princip](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name="part-2---supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>Del 2 – kryptografiska algoritmer som stöds & nyckel längder

I följande tabell visas de kryptografiska algoritmer som stöds och viktiga fördelar som kan konfigureras av kunderna:

| **IPsec/IKEv2**  | **Alternativ**    |
| ---  | --- 
| IKEv2-kryptering | AES256, AES192, AES128, DES3, DES  
| IKEv2 Integrity  | SHA384, SHA256, SHA1, MD5  |
| DH-grupp         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, ingen |
| IPsec-kryptering | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, None    |
| IPsec Integrity  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS-grupp        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, None 
| QM SA-livstid   | (**Valfritt**: standardvärden används om inget anges)<br>Sekunder (heltal. **min. 300**/standard 27 000 sekunder)<br>Kilobyte (heltal. **min. 1024**/standard 102400000 kilobyte)   |
| Trafikväljare | UsePolicyBasedTrafficSelectors * * ($True/$False; **Valfritt**, standard $FALSE om inget anges)    |
|  |  |

> [!IMPORTANT]
> 1. **Din lokala konfiguration för VPN-enheten måste stämma överens med eller innehålla följande algoritmer och parametrar som du anger på Azure IPsec/IKE-principen:**
>    * IKE-krypteringsalgoritm (huvud läge/fas 1)
>    * IKE-integritetsalgoritm (huvud läge/fas 1)
>    * DH-grupp (huvud läge/fas 1)
>    * IPsec-krypteringsalgoritm (snabb läge/fas 2)
>    * IPsec integritetsalgoritm (snabb läge/fas 2)
>    * PFS-grupp (snabb läge/fas 2)
>    * Trafik väljare (om UsePolicyBasedTrafficSelectors används)
>    * SA-livstider är lokala specifikationer och behöver inte matcha.
>
> 2. **Om GCMAES används som för IPsec-krypteringsalgoritm måste du välja samma GCMAES-algoritm och nyckel längd för IPsec-integriteten. Du kan till exempel använda GCMAES128 för båda**
> 3. I tabellen ovan:
>    * IKEv2 motsvarar huvud läget eller fas 1
>    * IPsec motsvarar snabb läge eller fas 2
>    * DH-grupp anger Diffie-Hellmen grupp som används i huvud läge eller fas 1
>    * PFS-gruppen angav Diffie-Hellmen gruppen som användes i snabb läge eller fas 2
> 4. IKEv2 Main Mode SA har en livslängd på högst 28 800 sekunder på Azure VPN-gatewayer
> 5. Om du anger "UsePolicyBasedTrafficSelectors" till $True på en anslutning konfigureras Azure VPN-gatewayen för att ansluta till principbaserad VPN-brandvägg lokalt. Om du aktiverar PolicyBasedTrafficSelectors måste du se till att VPN-enheten har de matchande trafik väljare som definierats med alla kombinationer av ditt lokala nätverk (lokal nätverksgateway) prefix till/från de virtuella Azure-nätverks prefixen i stället för alla-till-alla. Om ditt prefix för det lokala nätverket är 10.1.0.0/16 och 10.2.0.0/16 och ditt prefix för det virtuella nätverket är 192.168.0.0/16 och 172.16.0.0/16, måste du ange följande trafik väljare:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Mer information om principbaserad trafik väljare finns i [ansluta flera lokala principbaserade VPN-enheter](vpn-gateway-connect-multiple-policybased-rm-ps.md).

I följande tabell visas motsvarande Diffie-Hellman grupper som stöds av den anpassade principen:

| **Diffie-Hellman-grupp**  | **DHGroup**              | **PFSGroup** | **Nyckellängd** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768-bitars MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024-bitars MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048-bitars MODP  |
| 19                        | ECP256                   | ECP256       | 256-bitars ECP    |
| 20                        | ECP384                   | ECP384       | 384-bitars ECP    |
| 24                        | DHGroup24                | PFS24        | 2048-bitars MODP  |

Se [RFC3526](https://tools.ietf.org/html/rfc3526) och [RFC5114](https://tools.ietf.org/html/rfc5114) för mer information.

## <a name="part-3---create-a-new-s2s-vpn-connection-with-ipsecike-policy"></a><a name ="crossprem"></a>Del 3 – skapa en ny S2S VPN-anslutning med IPsec/IKE-princip

Det här avsnittet vägleder dig genom stegen för att skapa en S2S VPN-anslutning med en IPsec/IKE-princip. Följande steg skapar anslutningen som visas i diagrammet:

![S2S-princip](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Se [skapa en S2S VPN-anslutning](vpn-gateway-create-site-to-site-rm-powershell.md) för mer detaljerade stegvisa instruktioner för att skapa en S2S VPN-anslutning.

### <a name="before-you-begin"></a><a name="before"></a>Innan du börjar

* Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* Installera PowerShell-cmdletarna för Azure Resource Manager. Mer information om hur du installerar PowerShell-cmdlets finns i [Översikt över Azure PowerShell](/powershell/azure/) .

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>Steg 1 – skapa det virtuella nätverket, VPN-gatewayen och den lokala Nätverksgatewayen

#### <a name="1-declare-your-variables"></a>1. deklarera dina variabler

I den här övningen börjar vi med att deklarera våra variabler. Se till att ersätta värdena med dina egna när du konfigurerar för produktion.

```powershell
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Anslut till din prenumeration och skapa en ny resurs grupp

Se till att växla till PowerShell-läget för att kunna använda Resource Manager-cmdletarna. Mer information finns i [Använda Windows PowerShell med Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. skapa det virtuella nätverket, VPN-gatewayen och den lokala Nätverksgatewayen

I följande exempel skapas det virtuella nätverket, TestVNet1, med tre undernät och VPN-gatewayen. När du ersätter värden är det viktigt att du alltid namnger gateway-undernätet specifikt till GatewaySubnet. Om du ger det något annat namn går det inte att skapa gatewayen.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a><a name="s2sconnection"></a>Steg 2 – Skapa en S2S VPN-anslutning med en IPsec/IKE-princip

#### <a name="1-create-an-ipsecike-policy"></a>1. skapa en IPsec/IKE-princip

Följande exempel skript skapar en IPsec/IKE-princip med följande algoritmer och parametrar:

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS none, SA-livstid 14400 sekunder & 102400000KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Om du använder GCMAES för IPsec måste du använda samma GCMAES-algoritm och nyckel längd för både IPsec-kryptering och integritet. Till exempel ovan blir motsvarande parametrar "-IpsecEncryption GCMAES256-IpsecIntegrity GCMAES256" när du använder GCMAES256.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. skapa S2S VPN-anslutningen med IPsec/IKE-principen

Skapa en S2S VPN-anslutning och tillämpa IPsec/IKE-principen som skapades tidigare.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Du kan också lägga till "-UsePolicyBasedTrafficSelectors $True" i skapa anslutnings-cmdleten för att aktivera Azure VPN gateway att ansluta till principbaserade VPN-enheter lokalt, enligt beskrivningen ovan.

> [!IMPORTANT]
> När en IPsec/IKE-princip anges i en anslutning skickas eller accepteras bara IPsec/IKE-förslaget med angivna kryptografiska algoritmer och nyckel längder för den aktuella anslutningen i Azure VPN-gatewayen. Kontrol lera att den lokala VPN-enheten för anslutningen använder eller accepterar den exakta princip kombinationen, annars upprättas inte S2S VPN-tunneln.


## <a name="part-4---create-a-new-vnet-to-vnet-connection-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>Del 4 – skapa en ny VNet-till-VNet-anslutning med IPsec/IKE-princip

Stegen för att skapa en VNet-till-VNet-anslutning med en IPsec/IKE-princip liknar den för en S2S VPN-anslutning. Följande exempel skript skapar anslutningen som visas i diagrammet:

![V2V-princip](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Mer detaljerad information om hur du skapar en VNet-till-VNet-anslutning finns i [skapa en VNet-till-VNET-anslutning](vpn-gateway-vnet-vnet-rm-ps.md) . Du måste slutföra [del 3](#crossprem) för att skapa och konfigurera TestVNet1 och VPN gateway.

### <a name="step-1---create-the-second-virtual-network-and-vpn-gateway"></a><a name="createvnet2"></a>Steg 1 – skapa det andra virtuella nätverket och VPN-gatewayen

#### <a name="1-declare-your-variables"></a>1. deklarera dina variabler

Ersätt värdena med de som du vill använda för din konfiguration.

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. skapa det andra virtuella nätverket och VPN-gatewayen i den nya resurs gruppen

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Steg 2 – Skapa en VNet-toVNet-anslutning med IPsec/IKE-principen

Precis som VPN-anslutningen för S2S, skapar du en IPsec/IKE-princip och tillämpar sedan på principen för den nya anslutningen.

#### <a name="1-create-an-ipsecike-policy"></a>1. skapa en IPsec/IKE-princip

Följande exempelskript skapar en annan IPsec/IKE-princip med följande algoritmer och parametrar:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, SA-livstid 14400 sekunder & 102400000KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. skapa VNet-till-VNet-anslutningar med IPsec/IKE-principen

Skapa en VNet-till-VNet-anslutning och tillämpa den IPsec/IKE-princip som du skapade. I det här exemplet finns båda gatewayerna i samma prenumeration. Därför är det möjligt att skapa och konfigurera båda anslutningarna med samma IPsec/IKE-princip i samma PowerShell-session.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> När en IPsec/IKE-princip anges i en anslutning skickas eller accepteras bara IPsec/IKE-förslaget med angivna kryptografiska algoritmer och nyckel längder för den aktuella anslutningen i Azure VPN-gatewayen. Kontrol lera att IPsec-principerna för båda anslutningarna är desamma, annars upprättas inte VNet-till-VNet-anslutningen.

När du har slutfört de här stegen upprättas anslutningen på några minuter och du kommer att ha följande nätverkstopologi som visas i början:

![IPSec-IKE – princip](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name="part-5---update-ipsecike-policy-for-a-connection"></a><a name ="managepolicy"></a>Del 5 – uppdatera IPsec/IKE-princip för en anslutning

Det sista avsnittet visar hur du hanterar IPsec/IKE-princip för en befintlig S2S-eller VNet-till-VNet-anslutning. I övningen nedan går vi igenom följande åtgärder för en anslutning:

1. Visa IPsec/IKE-principen för en anslutning
2. Lägga till eller uppdatera IPsec/IKE-principen till en anslutning
3. Ta bort IPsec/IKE-principen från en anslutning

Samma steg gäller både S2S-och VNet-till-VNet-anslutningar.

> [!IMPORTANT]
> IPsec/IKE-principen stöds endast på routning-baserade VPN-gatewayer som *standard* och *HighPerformance* . Den fungerar inte på den grundläggande Gateway-SKU: n eller den principbaserad VPN-gatewayen.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Visa IPsec/IKE-principen för en anslutning

I följande exempel visas hur du hämtar IPsec/IKE-principen som kon figurer ATS på en anslutning. Skripten fortsätter också från övningarna ovan.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Det sista kommandot visar den aktuella IPsec/IKE-principen som kon figurer ATS för anslutningen, om det finns några. Följande är ett exempel på utdata för anslutningen:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES256
IkeIntegrity        : SHA384
DhGroup             : DHGroup24
PfsGroup            : PFS24
```

Om ingen IPsec/IKE-princip har kon figurer ATS är kommandot (PS> $connection 6. IpsecPolicies) får en tom RETUR. Det innebär inte att IPsec/IKE inte har kon figurer ATS för anslutningen, men att det inte finns någon anpassad IPsec/IKE-princip. Den faktiska anslutningen använder standard principen som förhandlas mellan den lokala VPN-enheten och Azure VPN-gatewayen.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Lägg till eller uppdatera en IPsec/IKE-princip för en anslutning

Stegen för att lägga till en ny princip eller uppdatera en befintlig princip på en anslutning är samma: skapa en ny princip och tillämpa den nya principen på anslutningen.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Om du vill aktivera "UsePolicyBasedTrafficSelectors" när du ansluter till en lokal principbaserad VPN-enhet lägger du till parametern "-UsePolicyBaseTrafficSelectors" i cmdleten, eller så ställer du in den på $False för att inaktivera alternativet:

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

Du kan få anslutningen igen för att kontrol lera om principen har uppdaterats.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Du bör se utdata från den sista raden, som du ser i följande exempel:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES128
IkeIntegrity        : SHA1
DhGroup             : DHGroup14
PfsGroup            : None
```

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. ta bort en IPsec/IKE-princip från en anslutning

När du tar bort den anpassade principen från en anslutning återgår Azure VPN-gatewayen tillbaka till [standard listan med IPSec/IKE-förslag](vpn-gateway-about-vpn-devices.md) och omförhandlar igen med din lokala VPN-enhet.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Du kan använda samma skript för att kontrol lera om principen har tagits bort från anslutningen.

## <a name="next-steps"></a>Nästa steg

Se [Anslut flera lokala principbaserade VPN-enheter](vpn-gateway-connect-multiple-policybased-rm-ps.md) för mer information om principbaserad trafik väljare.

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/windows/quick-create-portal.md) för anvisningar.