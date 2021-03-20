---
title: 'Azure VPN Gateway: ansluta gatewayer till flera lokala principbaserade VPN-enheter'
description: Konfigurera en Azure Route-baserad VPN-gateway till flera principbaserade VPN-enheter med hjälp av Azure Resource Manager och PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 2a85204fef026940394a19934bef1c631a8e2d21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89418892"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Anslut Azure VPN-gatewayer till flera lokala principbaserade VPN-enheter med hjälp av PowerShell

Den här artikeln hjälper dig att konfigurera en Azure Route-baserad VPN-gateway för att ansluta till flera lokala principbaserade VPN-enheter som utnyttjar anpassade IPsec/IKE-principer på S2S VPN-anslutningar.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a><a name="about"></a>Om principbaserad och routning-baserade VPN-gatewayer

Principbaserade *eller* DIRIGERADE VPN-enheter skiljer sig åt i hur IPSec-trafikväljare anges i en anslutning:

* **Principbaserad** VPN-enheter använder kombinationer av prefix från båda nätverken för att definiera hur trafiken krypteras/dekrypteras via IPsec-tunnlar. Den bygger vanligt vis på brand Väggs enheter som utför paket filtrering. Kryptering och dekryptering av IPsec-tunnlar läggs till i motorn för paketfiltrering och bearbetning.
* **Route-baserad** VPN-enheter använder valfri-till-valfri (jokertecken) trafik väljare och låter tabeller dirigeras/vidarebefordras till olika IPsec-tunnlar. Den bygger vanligt vis på perronger där varje IPsec-tunnel modelleras som ett nätverks gränssnitt eller VTI (virtuellt tunnel gränssnitt).

I följande diagram markeras de två modellerna:

### <a name="policy-based-vpn-example"></a>Princip-baserat VPN-exempel
![Principbaserad](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>ROUT-based VPN-exempel
![Route-baserad](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Azure-stöd för principbaserad VPN
För närvarande stöder Azure båda lägena för VPN-gatewayer: routning-baserade VPN-gatewayer och principbaserad VPN-gatewayer. De bygger på olika interna plattformar, vilket resulterar i olika specifikationer:

| Kategori | Principbaserad VPN Gateway | Routningsbaserad VPN Gateway | Routningsbaserad VPN Gateway |
| -------- | ----------------------- | ---------------------- | ---------------------- |---                                                 |
| **Azure Gateway-SKU**    | Grundläggande                       | Grundläggande                            | VpnGw1, VpnGw2, VpnGw3, VpnGw4, VpnGw5  |
| **IKE-version**          | IKEv1                       | IKEv2                            | IKEv1 och IKEv2                         |
| **Högsta antal S2S-anslutningar** | **1**                       | 10                               | 30                     |
|                          |                             |                                  |                                                    |

Med den anpassade IPsec/IKE-principen kan du nu konfigurera Azure Route-baserade VPN-gatewayer för att använda prefixbaserade trafik väljare med alternativet "**PolicyBasedTrafficSelectors**" för att ansluta till lokala PRINCIPBASERADE VPN-enheter. Med den här funktionen kan du ansluta från ett virtuellt Azure-nätverk och en VPN-gateway till flera lokala principbaserade VPN/brand Väggs enheter och ta bort den enskilda anslutnings gränsen från de aktuella Azure-principbaserad VPN-gatewayerna.

> [!IMPORTANT]
> 1. För att aktivera den här anslutningen måste dina lokala principbaserade VPN-enheter stödja **IKEv2** för att ansluta till Azure Route-baserade VPN-gatewayer. Kontrol lera dina specifikationer för VPN-enheter.
> 2. De lokala nätverk som ansluter via principbaserad VPN-enheter med den här mekanismen kan bara ansluta till det virtuella Azure-nätverket. **de kan inte transitera till andra lokala nätverk eller virtuella nätverk via samma Azure VPN-gateway**.
> 3. Konfigurations alternativet är en del av den anpassade IPsec/IKE-anslutnings principen. Om du aktiverar alternativet principbaserade trafik väljare måste du ange den fullständiga principen (IPsec/IKE-kryptering och integritetsalgoritmer, nyckel styrkor och SA-livstider).

Följande diagram visar varför transit routning via Azure VPN Gateway inte fungerar med det principbaserade alternativet:

![principbaserad överföring](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Som visas i diagrammet har Azure VPN-gatewayen trafik väljare från det virtuella nätverket till var och en av de lokala nätverks prefixen, men inte prefixen mellan anslutningarna. Den lokala platsen 2, plats 3 och plats 4 kan till exempel kommunicera med VNet1, men kan inte ansluta via Azure VPN-gatewayen till varandra. Diagrammet visar de kors anslutnings väljare som inte är tillgängliga i Azure VPN-gatewayen under den här konfigurationen.

## <a name="workflow"></a><a name="workflow"></a>Arbetsflöde

Anvisningarna i den här artikeln följer samma exempel som beskrivs i [Konfigurera IPSec/IKE-princip för S2S-eller VNet-till-VNet-anslutningar](vpn-gateway-ipsecikepolicy-rm-powershell.md) för att upprätta en S2S VPN-anslutning. Detta visas i följande diagram:

![S2S-princip](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

Arbets flödet för att aktivera den här anslutningen:
1. Skapa det virtuella nätverket, VPN-gatewayen och den lokala Nätverksgatewayen för din anslutning.
2. Skapa en IPsec/IKE-princip.
3. Tillämpa principen när du skapar en S2S-eller VNet-till-VNet-anslutning och **Aktivera principbaserade trafik väljare** på anslutningen.
4. Om anslutningen redan har skapats kan du tillämpa eller uppdatera principen på en befintlig anslutning.

## <a name="before-you-begin"></a>Innan du börjar

* Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

* [!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enable-policy-based-traffic-selectors"></a><a name="enablepolicybased"></a>Aktivera principbaserad trafik väljare

Det här avsnittet visar hur du aktiverar principbaserad trafik väljare på en anslutning. Se till att du har slutfört [del 3 av artikeln Konfigurera IPSec/IKE-policy](vpn-gateway-ipsecikepolicy-rm-powershell.md). Stegen i den här artikeln använder samma parametrar.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Steg 1 – skapa det virtuella nätverket, VPN-gatewayen och den lokala Nätverksgatewayen

#### <a name="connect-to-your-subscription-and-declare-your-variables"></a>Anslut till din prenumeration och deklarera dina variabler

1. Om du kör PowerShell lokalt på datorn loggar du in med cmdleten *Connect-AzAccount* . Eller, i stället använder Azure Cloud Shell i webbläsaren.

2. Deklarera dina variabler. I den här övningen använder vi följande variabler:

   ```azurepowershell-interactive
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

#### <a name="create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Skapa det virtuella nätverket, VPN-gatewayen och den lokala Nätverksgatewayen

1. Skapa en resursgrupp.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
2. Använd följande exempel för att skapa det virtuella nätverket TestVNet1 med tre undernät och VPN-gatewayen. Om du vill ersätta värden är det viktigt att du alltid namnger ditt Gateway-undernät särskilt "GatewaySubnet". Om du ger det något annat namn går det inte att skapa gatewayen.

    ```azurepowershell-interactive
    $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
    
    New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
    
    $gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    
    New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
    
    New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
    ```

### <a name="step-2---create-an-s2s-vpn-connection-with-an-ipsecike-policy"></a>Steg 2 – Skapa en S2S VPN-anslutning med en IPsec/IKE-princip

1. Skapa en IPsec/IKE-princip.

   > [!IMPORTANT]
   > Du måste skapa en IPsec/IKE-princip för att aktivera alternativet "UsePolicyBasedTrafficSelectors" på anslutningen.

   I följande exempel skapas en IPsec/IKE-princip med följande algoritmer och parametrar:
    * IKEv2: AES256, SHA384, DHGroup24
    * IPsec: AES256, SHA256, PFS none, SA-livstid 14400 sekunder & 102400000KB

   ```azurepowershell-interactive
   $ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
   ```
1. Skapa en S2S VPN-anslutning med principbaserad trafik väljare och IPsec/IKE-princip och tillämpa IPsec/IKE-principen som skapades i föregående steg. Tänk på den ytterligare parametern "-UsePolicyBasedTrafficSelectors $True", som aktiverar principbaserad trafik väljare på anslutningen.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
   $lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

   New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
   ```
1. När du har slutfört stegen kommer VPN-anslutningen för S2S att använda IPsec/IKE-principen definierad och aktivera principbaserad trafik väljare på anslutningen. Du kan upprepa samma steg för att lägga till fler anslutningar till ytterligare lokala principbaserade VPN-enheter från samma Azure VPN-gateway.

## <a name="to-update-policy-based-traffic-selectors"></a><a name="update"></a>Så här uppdaterar du principbaserad trafik väljare
Det här avsnittet visar hur du uppdaterar alternativet principbaserade trafik väljare för en befintlig S2S VPN-anslutning.

1. Hämta anslutnings resursen.

   ```azurepowershell-interactive
   $RG1          = "TestPolicyRG1"
   $Connection16 = "VNet1toSite6"
   $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
   ```
1. Visa alternativet principbaserad trafik väljare.
Följande rad visar om principbaserade trafik väljare används för anslutningen:

   ```azurepowershell-interactive
   $connection6.UsePolicyBasedTrafficSelectors
   ```

   Om raden returnerar "**True**" konfigureras principbaserade trafik väljare på anslutningen. annars returnerar den "**false**".
1. När du har fått anslutnings resursen kan du aktivera eller inaktivera de principbaserad trafik väljarna på en anslutning.

   - För att aktivera

      I följande exempel aktive ras alternativet principbaserade trafik väljare, men IPsec/IKE-principen lämnas oförändrad:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
      ```

   - För att inaktivera

      I följande exempel inaktiverar du alternativet principbaserade trafik väljare, men inaktiverar IPsec/IKE-principen oförändrad:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
      ```

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/windows/quick-create-portal.md) för anvisningar.

Granska också [Konfigurera IPSec/IKE-princip för S2S VPN-eller VNet-till-VNet-anslutningar](vpn-gateway-ipsecikepolicy-rm-powershell.md) för mer information om anpassade IPSec/IKE-principer.
