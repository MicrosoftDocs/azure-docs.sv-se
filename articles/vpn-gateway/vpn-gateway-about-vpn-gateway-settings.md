---
title: 'Azure-VPN Gateway: konfigurations inställningar'
description: Lär dig mer om VPN Gateway-resurser och inställningar för ett virtuellt nätverk som skapats i distributions modellen för Resource Manager.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 1aba87b2139fb8a7d395fb3180d2074e47310fa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010874"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Om konfigurations inställningar för VPN Gateway

En VPN-gateway är en typ av virtuell nätverksgateway som skickar krypterad trafik mellan det virtuella nätverket och den lokala platsen via en offentlig anslutning. Du kan också använda en VPN-gateway för att skicka trafik mellan virtuella nätverk i Azure-stamnätet.

En VPN gateway-anslutning är beroende av konfigurationen av flera resurser, som var och en innehåller konfigurerbara inställningar. Avsnitten i den här artikeln beskriver de resurser och inställningar som relaterar till en VPN-gateway för ett virtuellt nätverk som skapats i distributions modellen för Resource Manager. Du kan hitta beskrivningar och Topology-diagram för varje anslutnings lösning i artikeln [om VPN gateway](vpn-gateway-about-vpngateways.md) .

Värdena i den här artikeln tillämpar VPN-gatewayer (virtuella nätverksgateway som använder VPN-GatewayType). Den här artikeln omfattar inte alla typer av gatewayer eller zoner som är redundanta.

* För värden som gäller för-GatewayType ' ExpressRoute ', se [Virtual Network gateways för ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

* För zoner-redundanta gateways, se [om zoner-redundanta gatewayer](about-zone-redundant-vnet-gateways.md).

* För virtuellt WAN-nätverk, se [om virtuellt WAN](../virtual-wan/virtual-wan-about.md).

## <a name="gateway-types"></a><a name="gwtype"></a>Gateway-typer

Varje virtuellt nätverk kan bara ha en virtuell nätverksgateway av varje typ. När du skapar en virtuell nätverksgateway måste du kontrol lera att Gateway-typen är korrekt för din konfiguration.

De tillgängliga värdena för-GatewayType är:

* Vpn
* ExpressRoute

VPN-gatewayen kräver `-GatewayType` *VPN*-gatewayen.

Exempel:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gateway-skus"></a><a name="gwsku"></a>Gateway-SKU:er

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Konfigurera en gateway-SKU

**Azure-portalen**

Om du använder Azure Portal för att skapa en virtuell nätverksgateway i Resource Manager kan du välja Gateway-SKU: n med hjälp av list rutan. De alternativ som visas motsvarar den gateway-typ och VPN-typ som du väljer.

**PowerShell**

Följande PowerShell-exempel anger `-GatewaySku` as-VpnGw1. När du använder PowerShell för att skapa en gateway måste du först skapa IP-konfigurationen och sedan använda en variabel för att referera till den. I det här exemplet är konfigurations variabeln $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

**Azure CLI**

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizing-or-changing-a-sku"></a><a name="resizechange"></a>Ändra storlek på eller ändra en SKU

Om du har en VPN-gateway och vill använda en annan gateway-SKU, måste du antingen ändra storlek på Gateway-SKU: n eller ändra till en annan SKU. När du byter till en annan gateway-SKU tar du bort den befintliga gatewayen helt och skapar en ny. Det kan ta upp till 45 minuter att skapa en gateway. När du ändrar storlek på en gateway-SKU finns det inte mycket avbrott eftersom du inte behöver ta bort och återskapa gatewayen. Om du har möjlighet att ändra storlek på Gateway-SKU: n, i stället för att ändra den, ska du göra det. Det finns dock regler för storleks ändring:

1. Med undantag för Basic SKU kan du ändra storlek på en VPN gateway-SKU till en annan VPN gateway-SKU i samma generation (Generation1 eller Generation2). Till exempel kan VpnGw1 av Generation1 ändras till VpnGw2 för Generation1, men inte till VpnGw2 Generation2.
2. När du arbetar med gamla gatewayen-SKU: er, kan du ändra storlek mellan Basic, Standard och HighPerformance SKU: er.
3. Du **kan inte** ändra storlek från Basic/standard/HighPerformance SKU: er till VpnGw SKU: er. Du måste i stället [ändra](#change) till de nya SKU: erna.

#### <a name="to-resize-a-gateway"></a><a name="resizegwsku"></a>Ändra storlek på en gateway

**Azure-portalen**

[!INCLUDE [Resize a SKU - portal](../../includes/vpn-gateway-resize-gw-portal-include.md)]

**PowerShell**

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="to-change-from-an-old-legacy-sku-to-a-new-sku"></a><a name="change"></a>Ändra från en gammal (äldre) SKU till en ny SKU

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connection-types"></a><a name="connectiontype"></a>Anslutningstyper

I distributions modellen för Resource Manager kräver varje konfiguration en speciell Anslutnings typ för virtuell nätverksgateway. Tillgängliga PowerShell-värden i Resource Manager för `-ConnectionType` är:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

I följande PowerShell-exempel skapar vi en S2S-anslutning som kräver anslutnings typen *IPSec*.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpn-types"></a><a name="vpntype"></a>VPN-typer

När du skapar en virtuell nätverksgateway för en VPN gateway-konfiguration måste du ange en VPN-typ. Vilken VPN-typ du väljer beror på vilken anslutnings topologi som du vill skapa. Till exempel kräver en P2S-anslutning en Routningsbaserad VPN-typ. En VPN-typ kan också vara beroende av den maskin vara som du använder. S2S-konfigurationer kräver en VPN-enhet. Vissa VPN-enheter stöder bara en viss VPN-typ.

Den VPN-typ du väljer måste uppfylla alla anslutnings krav för den lösning som du vill skapa. Om du till exempel vill skapa en S2S VPN gateway-anslutning och en P2S VPN-gateway för samma virtuella nätverk, använder du VPN-typen *routningsbaserad* eftersom P2s kräver en routningsbaserad VPN-typ. Du måste också kontrol lera att VPN-enheten har stöd för en Routningsbaserad VPN-anslutning. 

När en virtuell nätverksgateway har skapats kan du inte ändra VPN-typen. Du måste ta bort den virtuella Nätverksgatewayen och skapa en ny. Det finns två VPN-typer:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Följande PowerShell-exempel anger `-VpnType` as- *routningsbaserad*. När du skapar en gateway måste du kontrollera att -VpnType är rätt för din konfiguration.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="gateway-requirements"></a><a name="requirements"></a>Gateway-krav

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gateway-subnet"></a><a name="gwsub"></a>Gatewayundernät

Innan du skapar en VPN-gateway måste du skapa ett Gateway-undernät. Gateway-undernätet innehåller de IP-adresser som de virtuella nätverks-gatewayens virtuella datorer och tjänster använder. När du skapar en virtuell nätverksgateway distribueras virtuella gateway-datorer till gateway-undernätet och konfigureras med nödvändiga inställningar för VPN gateway. Distribuera aldrig något annat (till exempel ytterligare virtuella datorer) till gateway-undernätet. Gateway-undernätet måste ha namnet GatewaySubnet för att fungera korrekt. Att namnge Gateway-undernätet "GatewaySubnet" låter Azure veta att det här är under nätet för att distribuera virtuella nätverksgateway VM och tjänster till.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

När du skapar gatewayundernätet anger du det antal IP-adresser som undernätet innehåller. IP-adresserna i Gateway-undernätet tilldelas till gateway-VM: ar och gateway-tjänsterna. Vissa konfigurationer kräver fler IP-adresser än andra. 

När du planerar storleken på Gateway-undernätet läser du dokumentationen för den konfiguration som du planerar att skapa. Till exempel kräver ExpressRoute-och VPN Gateway-konfigurationer som är samtidigt ett större Gateway-undernät än de flesta andra konfigurationer. Dessutom kanske du vill kontrol lera att Gateway-undernätet innehåller tillräckligt med IP-adresser för att kunna hantera framtida ytterligare konfigurationer. Även om du kan skapa ett Gateway-undernät så litet som/29, rekommenderar vi att du skapar ett Gateway-undernät på/27 eller större (/27,/26 osv.) om du har det tillgängliga adress utrymmet. Detta kommer att hantera de flesta konfigurationer.

Följande PowerShell-exempel i Resource Manager visar ett Gateway-undernät med namnet GatewaySubnet. Du kan se CIDR-noteringen anger en/27, vilket gör det möjligt för tillräckligt med IP-adresser för de flesta konfigurationer som för närvarande finns.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="local-network-gateways"></a><a name="lng"></a>Lokala nätverksgatewayer

En lokal nätverksgateway skiljer sig från en virtuell nätverksgateway. När du skapar en VPN gateway-konfiguration representerar den lokala Nätverksgatewayen vanligt vis ditt lokala nätverk och motsvarande VPN-enhet. I den klassiska distributionsmodellen kallades den lokala nätverksgatewayen för Lokal plats.

Du ger den lokala Nätverksgatewayen ett namn, den offentliga IP-adressen eller det fullständigt kvalificerade domän namnet (FQDN) för den lokala VPN-enheten och anger de adressprefix som finns på den lokala platsen. Azure tittar på prefix för mål adresser för nätverks trafik, kontrollerar konfigurationen som du har angett för din lokala nätverksgateway och dirigerar paket enligt detta. Om du använder Border Gateway Protocol (BGP) på din VPN-enhet anger du BGP-peer-IP-adressen för VPN-enheten och det autonoma system numret (ASN) för ditt lokala nätverk. Du anger också lokala nätverks-gatewayer för VNet-till-VNet-konfigurationer som använder en VPN gateway-anslutning.

Följande PowerShell-exempel skapar en ny lokal nätverksgateway:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Ibland behöver du ändra inställningarna för den lokala Nätverksgatewayen. Till exempel när du lägger till eller ändrar adress intervallet, eller om IP-adressen för VPN-enheten ändras. Se [ändra inställningar för lokal nätverksgateway med hjälp av PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="rest-apis-powershell-cmdlets-and-cli"></a><a name="resources"></a>REST-API: er, PowerShell-cmdletar och CLI

För ytterligare tekniska resurser och särskilda syntax krav för att använda REST-API: er, PowerShell-cmdletar eller Azure CLI för VPN Gateway konfigurationer, se följande sidor:

| **Form** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/az.network/#networking) |[PowerShell](/powershell/module/az.network#vpn) |
| [REST-API](/previous-versions/azure/reference/jj154113(v=azure.100)) |[REST-API](/rest/api/network/virtualnetworkgateways) |
| Stöds inte | [Azure CLI](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Nästa steg

Mer information om tillgängliga anslutnings konfigurationer finns i [om VPN gateway](vpn-gateway-about-vpngateways.md).