---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010871"
---
När du skapar en virtuell nätverksgateway måste du ange vilken gateway-SKU som du vill använda. Välj den SKU som uppfyller dina krav baserat på typerna av arbetsbelastning, dataflöden, funktioner och serviceavtal. För virtuella nätverksgateway-SKU: er i Azure-tillgänglighetszoner, se [Azure-tillgänglighetszoner Gateway-SKU: er](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>Gateway-SKU:er efter tunnel, anslutning och dataflöde

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> VpnGw SKU: er (VpnGw1, VpnGw1AZ, VpnGw2, VpnGw2AZ, VpnGw3, VpnGw3AZ, VpnGw4, VpnGw4AZ, VpnGw5 och VpnGw5AZ) stöds endast för distributions modellen Resource Manager. Klassiska virtuella nätverk bör fortsätta att använda de gamla (äldre) SKU: erna.
>  * Information om hur du arbetar med äldre Gateway-SKU: er (Basic, standard och HighPerformance) finns i [arbeta med VPN gateway SKU: er (äldre SKU](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md): er).
>  * ExpressRoute Gateway-SKU: er finns i [Virtual Network gatewayer för ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).
>

###  <a name="gateway-skus-by-feature-set"></a><a name="feature"></a>Gateway-SKU: er efter funktions uppsättning

De nya VPN gateway-SKU: erna effektiviserar de funktions uppsättningar som erbjuds i gatewayerna:

| **SKU**| **Funktioner**|
| ---    | ---         |
|**Basic** (* *)   | **Route-based VPN**: 10 tunnlar för S2S/anslutningar; ingen RADIUS-autentisering för P2S; inget IKEv2 för P2S<br>**PRINCIPBASERAD VPN**: (ikev1): 1 S2S/anslutnings tunnel; ingen P2S|
| **Alla Generation1-och Generation2-SKU: er förutom Basic** | **Routing-baserad VPN**: upp till 30 tunnlar (*), P2s, BGP, Active-Active, anpassad IPsec/IKE-princip, ExpressRoute/VPN-samexistens |
|        |             |

(*) Du kan konfigurera "PolicyBasedTrafficSelectors" för att ansluta en Route-baserad VPN-gateway till flera lokala, principbaserade brand Väggs enheter. Referera till [Ansluta VPN-gatewayer till flera lokala principbaserad VPN-enheter med hjälp av PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) för mer information.

( \* \* ) Basic SKU anses vara en äldre SKU. Basic SKU:n har vissa funktionsbegränsningar. Du kan inte ändra storlek på en gateway som använder en Basic SKU till en av de nya gateway-SKU:erna. Du måste istället ändra till en ny SKU, vilket innebär att ta bort och återskapa din VPN-gateway.

###  <a name="gateway-skus---production-vs-dev-test-workloads"></a><a name="workloads"></a>Gateway SKU – produktion jämfört med Dev-Test arbets belastningar

På grund av skillnader i serviceavtal och funktioner, rekommenderar vi följande SKU: er för produktion jämfört med utv-test:

| **Arbetsbelastning**                       | **SKU:er**               |
| ---                                | ---                    |
| **Produktion, kritiska arbetsbelastningar** | Alla Generation1-och Generation2-SKU: er förutom Basic |
| **Utv-test eller konceptbevis**   | Basic (* *)                 |
|                                    |                        |

( \* \* ) Basic SKU anses vara en äldre SKU och har funktions begränsningar. Kontrollera att funktionen du behöver stöds innan du använder Basic SKU:n.

Om du använder gamla SKU: er (äldre), är Production SKU-rekommendationerna standard och HighPerformance. Information och anvisningar för gamla SKU: er finns i [Gateway SKU: er (bakåtkompatibelt)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
