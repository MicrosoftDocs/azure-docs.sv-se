---
title: 'Konfigurera ExpressRoute och S2S VPN samtidiga anslutningar: Azure PowerShell'
description: Konfigurera ExpressRoute och en VPN-anslutning för plats-till-plats som kan användas för Resource Manager-modellen med hjälp av PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/11/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: b20bb4df7524c179766a2b2f7f090fccbddd7f37
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122620"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-using-powershell"></a>Konfigurera ExpressRoute-och plats-till-plats-sambefintliga anslutningar med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell – Klassisk](expressroute-howto-coexist-classic.md)
> 
> 

Den här artikeln hjälper dig att konfigurera ExpressRoute och VPN-anslutningar för plats till plats som samexisterar. Att kunna konfigurera VPN för plats till plats och ExpressRoute har flera fördelar. Du kan konfigurera en VPN för plats till plats som en säker redundanssökväg för ExpressRoute, eller använda plats-till-plats-VPN för att ansluta till platser som inte är anslutna via ExpressRoute. Vi beskriver stegen för att konfigurera båda scenarierna i den här artikeln. Den här artikeln gäller distributionsmodellen i Resource Manager.

Att konfigurera VPN för plats till plats och samexisterande ExpressRoute-anslutningar har flera fördelar:

* Du kan konfigurera en VPN för plats till plats som en säker redundanssökväg för ExpressRoute. 
* Du kan också använda VPN för plats till plats för att ansluta till platser som inte är anslutna via ExpressRoute. 

Stegen för att konfigurera båda scenarierna beskrivs i den här artikeln. Den här artikeln gäller distributionsmodellen i Resource Manager, och PowerShell används. Du kan också konfigurera dessa scenarier med hjälp av Azure Portal, men dokumentationen är inte tillgänglig ännu. Du kan konfigurera antingen gatewayen först. Normalt uppstår ingen stillestånds tid när du lägger till en ny gateway eller gateway-anslutning.

>[!NOTE]
>Om du vill skapa en VPN-anslutning för plats till plats över en ExpressRoute-krets kan du läsa [den här artikeln](site-to-site-vpn-over-microsoft-peering.md).
>

## <a name="limits-and-limitations"></a>Gränser och begränsningar
* **Transit-routing stöds inte.** Du kan inte routa (via Azure) mellan ditt lokala nätverk som ansluter via plats-till-plats-VPN och ditt lokala nätverk som ansluter via ExpressRoute.
* **Basic-SKU-gatewayen stöds inte.** Du måste använda en icke-Basic SKU-gateway för både [ExpressRoute-gatewayen](expressroute-about-virtual-network-gateways.md) och [VPN-gatewayen](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **Enbart routebaserad VPN-gateway stöds.** Du måste använda en Route-baserad [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md). Du kan också använda en Route-baserad VPN-gateway med en VPN-anslutning som kon figurer ATS för "principbaserade trafik väljare" enligt beskrivningen i [Anslut till flera principbaserade VPN-enheter](../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).
* **Statiska vägar ska konfigureras för din VPN-gateway.** Om ditt lokala nätverk är anslutet både till ExpressRoute och en plats-till-plats-VPN så måste du ha konfigurerat en statisk väg i ditt lokala nätverk för att routa plats-till-plats-VPN-anslutningen till det offentliga Internet.
* **VPN Gateway som standard i ASN 65515 om inget värde anges.** Azure VPN Gateway stöder BGP-routningsprotokollet. Du kan ange ASN (AS Number) för ett virtuellt nätverk genom att lägga till-ASN-växeln. Om du inte anger den här parametern är standardvärdet som antal 65515. Du kan använda valfritt ASN för konfigurationen, men om du väljer något annat än 65515 måste du återställa gatewayen för att inställningen ska börja gälla.
* **Gateway-undernätet måste vara/27 eller ett kortare prefix**(till exempel/26,/25) eller så visas ett fel meddelande när du lägger till ExpressRoute-gatewayen för virtuella nätverk.
* **Samexistens i ett virtuellt nätverk med dubbla stackar stöds inte.** Om du använder ExpressRoute IPv6-stöd och en ExpressRoute-Gateway med dubbla stackar går det inte att använda VPN Gateway.

## <a name="configuration-designs"></a>Konfigurationsdesign
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Konfigurera en VPN för plats till plats som en redundanssökväg för ExpressRoute
Du kan konfigurera en VPN-anslutning för plats till plats som en säkerhetskopia av ExpressRoute. Den här anslutningen gäller endast virtuella nätverk som är länkade till Azures privata peering-sökväg. Det finns ingen VPN-baserad redundanslösning för tjänster som är tillgängliga via Azure Microsoft-peering. ExpressRoute-kretsen är alltid den primära länken. Data flödar endast via plats-till-plats-VPN-sökvägen om ExpressRoute-kretsen misslyckas. För att undvika asymmetrisk routning bör din lokala nätverkskonfiguration också prioritera ExpressRoute-kretsen framför VPN för plats till plats. Du kan prioritera ExpressRoute-sökvägen genom att ange högre lokala inställningar för vägarna som tas emot för ExpressRoute. 

> [!NOTE]
> Medan ExpressRoute-kretsen är prioriterad över plats-till-plats-VPN när bägge vägarna är samma, kommer Azure att använda den längsta prefixmatchning för att välja väg till paketets mål.
> 
> 

![Diagram som visar en plats-till-plats-VPN-anslutning som en säkerhets kopia för ExpressRoute.](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Konfigurera en VPN för plats till plats för att ansluta till platser som inte är anslutna via ExpressRoute
Du kan konfigurera nätverket där vissa platser ansluter direkt till Azure via VPN för plats till plats och vissa platser ansluter via ExpressRoute. 

![Samexistera](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> Det går inte att konfigurera ett virtuellt nätverk som en överföringsrouter.
> 
> 

## <a name="selecting-the-steps-to-use"></a>Välj vilka steg du ska använda
Det finns två uppsättningar procedurer för att välja bland. Vilken konfigurationsprocedur du väljer beror på om du har ett befintligt virtuellt nätverk som du vill ansluta till, eller om du vill skapa ett nytt virtuellt nätverk.

* Jag har inte något VNet och behöver skapa ett.
  
    Om du inte redan har ett virtuellt nätverk kan den här proceduren hjälpa dig att skapa ett nytt virtuellt nätverk med hjälp av distributionsmodellen i Resource Manager samt skapa nya ExpressRoute- och plats-till-plats-VPN-anslutningar. Om du vill konfigurera ett virtuellt nätverk följer du stegen i [Så här skapar du ett nytt virtuellt nätverk och samexisterande anslutningar](#new).
* Jag har redan en distributionsmodell för Resource Manager i VNet.
  
    Du kanske redan har ett virtuellt nätverk på plats med en befintlig VPN-anslutning för plats till plats eller en ExpressRoute-anslutning. I det här scenariot: om gatewaynätmasken är /28 eller mindre (/28, /29 osv.) måste du ta bort den befintliga gatewayen. Avsnittet [Så här konfigurerar du samexisterande anslutningar för ett befintligt VNet](#add) visar hur du tar bort gatewayen och sedan skapar nya ExpressRoute- och plats-till-plats-VPN-anslutningar.
  
    Om du tar bort och återskapar gatewayen får du driftstopp för anslutningar mellan platser. Men dina virtuella datorer och tjänster kommer fortfarande att kunna kommunicera ut via lastbalanseraren när du konfigurerar din gateway, om de är konfigurerade för att göra det.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]


## <a name="to-create-a-new-virtual-network-and-coexisting-connections"></a><a name="new"></a>Så här skapar du ett nytt virtuellt nätverk och samexisterande anslutningar
Den här proceduren vägleder dig genom att skapa ett VNet samt plats-till-plats- och ExpressRoute-anslutningar som ska finnas samtidigt. De cmdletar som du använder för den här konfigurationen kan se annorlunda ut än de du tidigare använt. Var noga med att använda de cmdletar som anges i instruktionerna.

1. Logga in och välj din prenumeration.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Ange variabler.

   ```azurepowershell-interactive
   $location = "Central US"
   $resgrp = New-AzResourceGroup -Name "ErVpnCoex" -Location $location
   $VNetASN = 65515
   ```
3. Skapa ett virtuellt nätverk, inklusive gateway-undernätet. Mer information om hur du skapar ett virtuellt nätverk finns i [Skapa ett virtuellt nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Mer information om hur du skapar ett undernät finns i [Skapa ett undernät](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet).
   
   > [!IMPORTANT]
   > Gateway-undernätet måste vara /27 eller ett kortare prefix (till exempel /26 eller /25).
   > 
   > 
   
    Skapa ett nytt VNet.

   ```azurepowershell-interactive
   $vnet = New-AzVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
   ```
   
    Lägg till undernät.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    Spara VNet-konfigurationen.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. <a name="vpngw"></a>Skapa sedan din VPN-gateway för plats till plats. Mer information om konfigurationen av VPN-gatewayen finns i [Konfigurera ett VNet med en plats-till-plats-anslutning](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). GatewaySku stöds bara för följande VPN-gatewayer: *VpnGw1*, *VpnGw2*, *VpnGw3*, *Standard* och *HighPerformance*. ExpressRoute VPN Gateway samexisterande konfigurationer stöds inte på Basic-SKU:n. VpnType måste vara *RouteBased*.

   ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1"
   ```
   
    Azure VPN-gateway har stöd för BGP-routningsprotokoll. Du kan ange ASN (AS Number) för det virtuella nätverket genom att lägga till växeln - Asn i följande kommando. Om du inte anger parametern blir standardvärdet 65515.

   ```azurepowershell-interactive
   $azureVpn = New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1" -Asn $VNetASN
   ```
   
    Du hittar BGP peering-IP och AS-numret som Azure använder för VPN-gatewayen i $azureVpn.BgpSettings.BgpPeeringAddress och $azureVpn.BgpSettings.Asn. Mer information finns i [Konfigurera BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) för Azure VPN-gateway.
5. Skapa en lokal plats för VPN-gatewayen. Det här kommandot konfigurerar inte din lokala VPN-gateway. I stället kan du ange lokala gateway-inställningar, som t.ex. offentlig IP-adress och lokalt adressutrymme, så att Azure VPN-gatewayen kan ansluta till den.
   
    Om din lokala VPN-enhet bara stöder statisk routning, kan du konfigurera de statiska vägarna på följande sätt:

   ```azurepowershell-interactive
   $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
   ```
   
    Om din lokala VPN-enhet stöder BGP och du vill aktivera dynamisk routning, måste du veta BGP peering IP och AS-numret som din lokala VPN-enhet använder.

   ```azurepowershell-interactive
   $localVPNPublicIP = "<Public IP>"
   $localBGPPeeringIP = "<Private IP for the BGP session>"
   $localBGPASN = "<ASN>"
   $localAddressPrefix = $localBGPPeeringIP + "/32"
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
   ```
6. Konfigurera din lokala VPN-enhet till att ansluta till en ny Azure VPN-gateway. Mer information om VPN-enhetskonfiguration finns i [VPN-enhetskonfiguration](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

7. Länka VPN-gatewayen för plats till plats på Azure till den lokala gatewayen.

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   New-AzVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
   ```
 

8. Om du ansluter till en befintlig ExpressRoute-krets hoppar du över steg 8 och 9 och går till steg 10. Konfigurera ExpressRoute-kretsar. Mer information om hur du konfigurerar ExpressRoute-krets finns i [skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md).


9. Konfigurera privat Azure-peering via ExpressRoute-kretsen. Mer information om hur du konfigurerar privat Azure-peering via ExpressRoute-kretsen finns i [konfigurera peering](expressroute-howto-routing-arm.md)

10. <a name="gw"></a>Skapa en ExpressRoute-gateway. Mer information om konfiguration av ExpressRoute-gateways finns i [Konfiguration av ExpressRoute-gateway](expressroute-howto-add-gateway-resource-manager.md). GatewaySKU:n måste vara *Standard*, *HighPerformance*, eller *UltraPerformance*.

    ```azurepowershell-interactive
    $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
    $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
    $gw = New-AzVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
    ```
11. Länka ExpressRoute-gatewayen till ExpressRoute-kretsen. När det här steget har slutförts har anslutningen mellan ditt lokala nätverk och Azure, via ExpressRoute, upprättats. Mer information om länken finns i [Länka VNets till ExpressRoute](expressroute-howto-linkvnet-arm.md).

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
    New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
    ```

## <a name="to-configure-coexisting-connections-for-an-already-existing-vnet"></a><a name="add"></a>Så här konfigurerar du samexisterande anslutningar för ett befintligt VNet
Om du har ett virtuellt nätverk med endast en virtuell nätverksgateway (t.ex. en VPN-gateway för plats till plats) och du vill lägga till en till gateway av en annan typ (t.ex. en ExpressRoute-gateway), kontrollerar du storleken på gateway-undernätet. Om gateway-undernätet är /27 eller större kan du hoppa över stegen nedan och följa stegen i föregående avsnitt för att lägga till en VPN-gateway för plats till plats eller en ExpressRoute-gateway. Om gateway-undernätet är /28 eller /29, måste du först ta bort den virtuella nätverksgatewayen och öka storleken för gateway-undernätet. Stegen i det här avsnittet visar hur du gör.

De cmdletar som du använder för den här konfigurationen kan se annorlunda ut än de du tidigare använt. Var noga med att använda de cmdletar som anges i instruktionerna.

1. Ta bort den befintliga ExpressRoute- eller VPN-gatewayen för plats till plats.

   ```azurepowershell-interactive 
   Remove-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
   ```
2. Ta bort gateway-undernätet. 

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
   ```
3. Lägg till ett Gateway-undernät som är /27 eller större.
   
   > [!NOTE]
   > Om du inte har tillräckligt med IP-adresser kvar i det virtuella nätverket för att kunna öka storleken på gateway-undernätet, måste du lägga till mer IP-adressutrymme.
   > 
   > 

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    Spara VNet-konfigurationen.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. Nu har du ett virtuellt nätverk utan gateways. Använd följande exempel för att skapa nya gatewayer och konfigurera anslutningarna:

   Ange variablerna.

    ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   ```

   Skapa gatewayen.

   ```azurepowershell-interactive
   $gw = New-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup> -Location <yourlocation> -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
   ```

   Skapa anslutningen.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
   New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
   ```

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Så här lägger du till punkt-till-plats-konfiguration till VPN-gateway

Du kan följa stegen nedan för att lägga till punkt-till-plats-konfiguration till din VPN-gateway i en samexistens-installation. Om du vill ladda upp VPN-rotcertifikatet måste du antingen installera PowerShell lokalt på datorn eller använda Azure Portal.

1. Lägga till VPN-klientadresspoolen.

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   Set-AzVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
   ```
2. Överför VPN-rotcertifikatet till Azure för din VPN-gateway. I det här exemplet förutsätts att rot certifikatet lagras på den lokala datorn där följande PowerShell-cmdlets körs och att du kör PowerShell lokalt. Du kan också ladda upp certifikatet med hjälp av Azure Portal.

   ```powershell
   $p2sCertFullName = "RootErVpnCoexP2S.cer" 
   $p2sCertMatchName = "RootErVpnCoexP2S" 
   $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
   if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
   $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) 
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
   ```

Mer information om punkt-till-plats-VPN finns i [Konfigurera en punkt-till-plats-anslutning](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="next-steps"></a>Nästa steg
Mer information om ExpressRoute finns i [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
