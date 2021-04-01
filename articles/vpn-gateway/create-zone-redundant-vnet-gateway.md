---
title: Skapa en zon – redundant virtuell nätverksgateway i Azure-tillgänglighetszoner
description: Lär dig hur du distribuerar zoner – redundanta VPN-gatewayer och ExpressRoute-gatewayer i Azure-tillgänglighetszoner.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 2eaf1470e2d861ecfc1c1bc96f6040a1c3e0a644
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89425236"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Skapa en zon – redundant virtuell nätverksgateway i Azure-tillgänglighetszoner

Du kan distribuera VPN-och ExpressRoute-gatewayer i Azure-tillgänglighetszoner. Det ger flexibilitet, skalbarhet och högre tillgänglighet för virtuella nätverksgatewayer. Distribution av gatewayer i Azure-tillgänglighetszoner skiljer gatewayerna åt fysiskt och logiskt i en region, samtidigt som din lokala nätverksanslutning till Azure skyddas mot fel på zonnivå. Mer information finns i  [om zoner-redundanta virtuella nätverksgateway](about-zone-redundant-vnet-gateways.md) och [om Azure-tillgänglighetszoner](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-declare-your-variables"></a><a name="variables"></a>1. deklarera dina variabler

Deklarera de variabler som du vill använda. Använd följande exempel och ersätt värdena med dina egna om det behövs. Om du stänger PowerShell/Cloud Shell-sessionen när som helst under övningen ska du bara kopiera och klistra in värdena igen för att omdeklarera variablerna. När du anger plats kontrollerar du att den region som du anger stöds. Mer information finns i [vanliga frågor och svar](#faq).

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="2-create-the-virtual-network"></a><a name="configure"></a>2. skapa det virtuella nätverket

Skapa en resursgrupp.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Skapa ett virtuellt nätverk.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="3-add-the-gateway-subnet"></a><a name="gwsub"></a>3. Lägg till gateway-undernätet

Gateway-undernätet innehåller de reserverade IP-adresser som används av tjänsten för virtuell nätverksgateway. Använd följande exempel för att lägga till och ange ett Gateway-undernät:

Lägg till gateway-undernätet.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Ange Gateway-undernätets konfiguration för det virtuella nätverket.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="4-request-a-public-ip-address"></a><a name="publicip"></a>4. begär en offentlig IP-adress
 
I det här steget väljer du de instruktioner som gäller för den gateway som du vill skapa. Valet av zoner för att distribuera gatewayen beror på de zoner som anges för den offentliga IP-adressen.

### <a name="for-zone-redundant-gateways"></a><a name="ipzoneredundant"></a>För zoner-redundanta gateways

Begär en offentlig IP-adress med en **standard** -PUBLICIPADDRESS-SKU och ange ingen zon. I det här fallet är den standardiserade offentliga IP-adress som skapas en zon-redundant offentlig IP.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="for-zonal-gateways"></a><a name="ipzonalgw"></a>För zonindelade-gatewayer

Begär en offentlig IP-adress med en **standard** -PUBLICIPADDRESS-SKU. Ange zonen (1, 2 eller 3). Alla gateway-instanser kommer att distribueras i den här zonen.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="for-regional-gateways"></a><a name="ipregionalgw"></a>För regionala gatewayer

Begär en offentlig IP-adress med en **grundläggande** PUBLICIPADDRESS-SKU. I det här fallet distribueras gatewayen som en regional gateway och har ingen inbyggd zon redundans i gatewayen. Gateway-instanserna skapas i alla zoner.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="5-create-the-ip-configuration"></a><a name="gwipconfig"></a>5. skapa IP-konfigurationen

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="6-create-the-gateway"></a><a name="gwconfig"></a>6. skapa gatewayen

Skapa den virtuella Nätverksgatewayen.

### <a name="for-expressroute"></a>För ExpressRoute

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>För VPN Gateway

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a><a name="faq"></a>ASSURANCE

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Vad kommer att ändras när jag distribuerar dessa nya SKU: er?

I ditt perspektiv kan du distribuera dina gateways med zon-redundans. Det innebär att alla instanser av gatewayerna distribueras över Azure-tillgänglighetszoner och att varje tillgänglighets zon är ett annat fel och en annan uppdaterings domän. Detta gör dina gatewayer mer pålitliga, tillgängliga och elastiska för zon haverier.

### <a name="can-i-use-the-azure-portal"></a>Kan jag använda Azure Portal?

Ja, du kan använda Azure Portal för att distribuera de nya SKU: erna. Du kommer dock bara att se dessa nya SKU: er i de Azure-regioner som har Azure-tillgänglighetszoner.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Vilka regioner är tillgängliga för mig att använda de nya SKU: erna?

Se [Tillgänglighetszoner](../availability-zones/az-region.md) för den senaste listan över tillgängliga regioner.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Kan jag ändra/migrera/uppgradera mina befintliga virtuella nätverks-gatewayer till zoner-redundanta eller zonindelade-gatewayer?

Det finns för närvarande inte stöd för migrering av befintliga virtuella nätverksgateway till zoner-redundanta eller zonindelade-gatewayer. Du kan dock ta bort din befintliga gateway och återskapa en zon-redundant eller zonindelade Gateway.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Kan jag distribuera både VPN-och Express-flödesnoder i samma virtuella nätverk?

Samtidig användning av både VPN-och Express-flödesnoder i samma virtuella nätverk stöds. Du bör dock reservera ett/27 IP-adressintervall för gateway-undernätet.
