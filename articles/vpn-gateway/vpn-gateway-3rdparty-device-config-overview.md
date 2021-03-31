---
title: Konfiguration av partner-VPN-enheter för anslutning till Azure VPN-gatewayer
description: Den här artikeln innehåller en översikt över konfigurationer av partner VPN-enheter för anslutning till Azure VPN-gatewayer.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 484dad65310efaa60e8744b2f122b5e44ae13565
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880160"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Översikt över konfigurationer för VPN-enheter för partner
Den här artikeln innehåller en översikt över hur du konfigurerar lokala VPN-enheter för anslutning till Azure VPN-gatewayer. Ett Azure-exempel för virtuella Azure-nätverk och VPN-gateway används för att visa hur du ansluter till olika lokala VPN-enheters konfigurationer med samma parametrar.



## <a name="device-requirements"></a>Krav på enheten
Azure VPN-gatewayer använder standard-IPsec/IKE-protokoll för plats-till-plats (S2S) VPN-tunnlar. En lista över IPsec/IKE-parametrar och krypteringsalgoritmer för Azure VPN-gatewayer finns i [om VPN-enheter](vpn-gateway-about-vpn-devices.md). Du kan också ange exakta algoritmer och nyckel längder för en speciell anslutning enligt beskrivningen i [om kryptografiska krav](vpn-gateway-about-compliance-crypto.md).

## <a name="single-vpn-tunnel"></a><a name ="singletunnel"></a>Enskild VPN-tunnel
Den första konfigurationen i exemplet består av en enda S2S VPN-tunnel mellan en Azure VPN-gateway och en lokal VPN-enhet. Du kan också konfigurera [Border Gateway Protocol (BGP) i VPN-tunneln](#bgp).

![Diagram över en enskild S2S VPN-tunnel](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Steg-för-steg-instruktioner för att konfigurera en enda VPN-tunnel finns i [Konfigurera en plats-till-plats-anslutning](./tutorial-site-to-site-portal.md). Följande avsnitt anger anslutnings parametrarna för exempel konfigurationen och ger ett PowerShell-skript som hjälper dig att komma igång.

### <a name="connection-parameters"></a>Anslutningsparametrar
I det här avsnittet visas parametrarna för de exempel som beskrivs i föregående avsnitt.

| **Parameter**                | **Värde**                    |
| ---                          | ---                          |
| Prefix för virtuella nätverks adresser        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP för Azure VPN-gateway         | Azure VPN Gateway-IP         |
| Lokala adressprefix | 10.51.0.0/16<br>10.52.0.0/16 |
| IP för lokal VPN-enhet    | IP för lokal VPN-enhet    |
| * ASN för BGP för virtuella nätverk                | 65010                        |
| * Azure BGP peer IP           | 10.12.255.30                 |
| * Lokalt BGP-ASN         | 65050                        |
| * Lokal BGP peer-IP     | 10.52.255.254                |

\* Valfri parameter för endast BGP.

### <a name="sample-powershell-script"></a>Exempel på PowerShell-skript
Det här avsnittet innehåller ett exempel skript som hjälper dig att komma igång. Detaljerade anvisningar finns i [skapa en S2S VPN-anslutning med hjälp av PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subscription_Name"
$RG1           = "TestRG1"
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
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name="optional-use-custom-ipsecike-policy-with-usepolicybasedtrafficselectors"></a><a name ="policybased"></a>Valfritt Använd anpassad IPsec/IKE-princip med UsePolicyBasedTrafficSelectors
Om dina VPN-enheter inte stöder några-till-valfri trafik väljare, t. ex. routning eller VTI konfigurationer, skapar du en anpassad IPsec/IKE-princip med alternativet [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) .

> [!IMPORTANT]
> Du måste skapa en IPsec/IKE-princip för att aktivera alternativet **UsePolicyBasedTrafficSelectors** på anslutningen.


Exempel skriptet skapar en IPsec/IKE-princip med följande algoritmer och parametrar:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, SA-livstid 7 200 sekunder och 20 480 000 KB (20 GB)

Skriptet tillämpar IPsec/IKE-principen och aktiverar alternativet **UsePolicyBasedTrafficSelectors** på anslutningen.

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name="optional-use-bgp-on-s2s-vpn-connection"></a><a name ="bgp"></a>Valfritt Använd BGP på S2S VPN-anslutning
När du skapar en S2S VPN-anslutning kan du välja att använda [BGP för VPN-gatewayen](vpn-gateway-bgp-resource-manager-ps.md). Den här metoden har två skillnader:

* De lokala adressprefix kan vara en enda värd adress. Den lokala BGP-peer-IP-adressen anges på följande sätt:

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* När du skapar anslutningen måste du ange alternativet **-EnableBGP** för att $True:

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Nästa steg
Stegvisa instruktioner för att konfigurera aktiva och aktiva VPN-gatewayer finns i [Konfigurera aktiva, aktiva VPN-gatewayer för anslutningar mellan lokala nätverk och VNET-till-VNet](vpn-gateway-activeactive-rm-powershell.md).