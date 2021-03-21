---
title: Om ExpressRoute virtuella nätverksgateway – Azure | Microsoft Docs
description: 'Läs om virtuella nätverks-gatewayer för ExpressRoute. Den här artikeln innehåller information om Gateway-SKU: er och typer.'
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: duau
ms.openlocfilehash: 038e018a22af3546f5d3c66f6d8ee3963483cce1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615065"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Om ExpressRoute-gatewayer för virtuella nätverk

Om du vill ansluta ditt virtuella Azure-nätverk och ditt lokala nätverk via ExpressRoute måste du först skapa en virtuell nätverksgateway. En virtuell nätverksgateway fungerar i två olika syfte: Exchange IP-vägar mellan nätverken och dirigera nätverks trafik. I den här artikeln beskrivs Gateway-typer, Gateway SKU: er och uppskattade prestanda per SKU. Den här artikeln beskriver också ExpressRoute [FastPath](#fastpath), en funktion som gör att nätverks trafiken från ditt lokala nätverk kan kringgå den virtuella Nätverksgatewayen för att förbättra prestandan.

## <a name="gateway-types"></a>Gateway-typer

När du skapar en virtuell nätverksgateway måste du ange flera inställningar. En av de obligatoriska inställningarna,-GatewayType, anger om gatewayen används för ExpressRoute eller VPN-trafik. De två Gateway-typerna är:

* **VPN** – om du vill skicka krypterad trafik via det offentliga Internet använder du Gateway-typen VPN. Detta kallas även för en VPN-gateway. Plats-till-plats-, punkt-till-plats- och VNet-VNet-anslutningar använder allihop en VPN-gateway.

* **ExpressRoute** – om du vill skicka nätverks trafik på en privat anslutning använder du Gateway-typen "ExpressRoute". Detta kallas även för en ExpressRoute-gateway och är den typ av gateway som används när du konfigurerar ExpressRoute.

Varje virtuellt nätverk kan bara ha en VNet-gateway per gateway-typ. Du kan exempelvis ha en VNet-gateway som använder -GatewayType Vpn och en som använder -GatewayType ExpressRoute.

## <a name="gateway-skus"></a><a name="gwsku"></a>Gateway-SKU:er
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Om du vill uppgradera din gateway till en mer kraftfull Gateway-SKU, kan du i de flesta fall använda PowerShell-cmdleten "Restore-AzVirtualNetworkGateway". Detta fungerar för uppgraderingar till standard-och HighPerformance-SKU: er. Men för att uppgradera till UltraPerformance SKU måste du återskapa gatewayen. Att återskapa en gateway medför avbrott.

### <a name="estimated-performances-by-gateway-sku"></a><a name="aggthroughput"></a>Beräknade prestanda per Gateway-SKU
I följande tabell visas de olika Gateway-typerna och de uppskattade prestanda. Tabellen gäller både för Resource Manager- och den klassiska distributionsmodellen.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Program prestandan beror på flera faktorer, till exempel svars tiden från slut punkt till slut punkt och antalet trafik flöden som programmet öppnar. Talen i tabellen representerar den övre gränsen som programmet kan åstadkomma teoretiskt i en perfekt miljö.
>
>

## <a name="gateway-subnet"></a><a name="gwsub"></a>Gatewayundernät

Innan du skapar en ExpressRoute-Gateway måste du skapa ett Gateway-undernät. Gateway-undernätet innehåller de IP-adresser som de virtuella nätverks-gatewayens virtuella datorer och tjänster använder. När du skapar en virtuell nätverksgateway distribueras virtuella gateway-datorer till gateway-undernätet och konfigureras med nödvändiga ExpressRoute Gateway-inställningar. Distribuera aldrig något annat (till exempel ytterligare virtuella datorer) till gateway-undernätet. Gateway-undernätet måste ha namnet GatewaySubnet för att fungera korrekt. Att namnge Gateway-undernätet "GatewaySubnet" låter Azure veta att det här är under nätet för att distribuera virtuella nätverksgateway VM och tjänster till.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

När du skapar gatewayundernätet anger du det antal IP-adresser som undernätet innehåller. IP-adresserna i Gateway-undernätet tilldelas till gateway-VM: ar och gateway-tjänsterna. Vissa konfigurationer kräver fler IP-adresser än andra. 

När du planerar storleken på Gateway-undernätet läser du dokumentationen för den konfiguration som du planerar att skapa. Till exempel kräver ExpressRoute-och VPN Gateway-konfigurationer som är samtidigt ett större Gateway-undernät än de flesta andra konfigurationer. Dessutom kanske du vill kontrol lera att Gateway-undernätet innehåller tillräckligt med IP-adresser för att kunna hantera framtida ytterligare konfigurationer. Även om du kan skapa ett Gateway-undernät så litet som/29, rekommenderar vi att du skapar ett Gateway-undernät på/27 eller större (/27,/26 osv.) om du har det tillgängliga adress utrymmet. Om du skapar ett undernät med dubbla stack-Gateway rekommenderar vi att du även använder ett IPv6-intervall på/64 eller större. Detta kommer att hantera de flesta konfigurationer.

Följande PowerShell-exempel i Resource Manager visar ett Gateway-undernät med namnet GatewaySubnet. Du kan se CIDR-noteringen anger en/27, vilket gör det möjligt för tillräckligt med IP-adresser för de flesta konfigurationer som för närvarande finns.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zone-redundant-gateway-skus"></a><a name="zrgw"></a>Zon – redundanta Gateway-SKU: er

Du kan också distribuera ExpressRoute-gatewayer i Azure-tillgänglighetszoner. Detta fysiskt och logiskt särskiljer dem till olika Tillgänglighetszoner, vilket skyddar din lokala nätverks anslutning till Azure från felaktiga zon nivåer.

![Zone-redundant ExpressRoute-Gateway](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Zone – redundanta gateways använder vissa nya gateway-SKU: er för ExpressRoute-Gateway.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Nya gateway-SKU: er stöder också andra distributions alternativ för att passa dina behov bäst. När du skapar en virtuell nätverksgateway med hjälp av nya gateway-SKU: er, kan du också välja att distribuera gatewayen i en speciell zon. Detta kallas en zonindelade-Gateway. När du distribuerar en zonindelade-Gateway distribueras alla instanser av gatewayen i samma tillgänglighets zon.

> [!IMPORTANT]
> Om du planerar att använda IPv6-baserad privat peering över ExpressRoute, se till att välja en AZ-SKU för den gateway som du distribuerar i ett undernät med dubbla stack-gatewayer.
> 
>

## <a name="fastpath"></a><a name="fastpath"></a>FastPath

ExpressRoute virtuella nätverksgateway är utformad för att utbyta nätverks vägar och dirigera nätverks trafik. FastPath är utformat för att förbättra data Sök vägens prestanda mellan ditt lokala nätverk och ditt virtuella nätverk. När aktive rad skickar FastPath nätverks trafik direkt till virtuella datorer i det virtuella nätverket, vilket kringgår gatewayen.

Mer information om FastPath, inklusive begränsningar och krav finns i [om FastPath](about-fastpath.md).

## <a name="rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>REST-API: er och PowerShell-cmdletar
För ytterligare tekniska resurser och särskilda syntax krav för att använda REST-API: er och PowerShell-cmdletar för konfigurationer för virtuella nätverksgateway, se följande sidor:

| **Form** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/servicemanagement/azure.service/#azure) |[PowerShell](/powershell/module/az.network#networking) |
| [REST-API](/previous-versions/azure/reference/jj154113(v=azure.100)) |[REST-API](/rest/api/virtual-network/) |

## <a name="next-steps"></a>Nästa steg

Mer information om tillgängliga anslutnings konfigurationer finns i [Översikt över ExpressRoute](expressroute-introduction.md).

Mer information om hur du skapar ExpressRoute-gatewayer finns i [skapa en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

Mer information om hur du konfigurerar zoner med redundanta gateways finns i [skapa en zon-redundant virtuell](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md)nätverksgateway.

Mer information om FastPath finns i [om FastPath](about-fastpath.md).