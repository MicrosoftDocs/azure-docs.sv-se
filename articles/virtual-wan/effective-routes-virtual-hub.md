---
title: 'Visa effektiva vägar för en virtuell hubb: Azure Virtual WAN | Microsoft Docs'
description: Så här visar du effektiva vägar för en virtuell hubb i Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ad487aa7492abcf02988c78ccfa2ba7cd4798249
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90983650"
---
# <a name="view-virtual-hub-effective-routes"></a>Visa effektiva vägar för virtuell hubb

Du kan visa alla vägar för ditt virtuella WAN Hub i Azure Portal. Den här artikeln vägleder dig genom stegen för att Visa effektiva vägar. Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>Välj anslutningar eller routningstabeller

1. Navigera till din virtuella hubb och välj sedan **routning**. På sidan routning väljer du **effektiva vägar**.
1. I list rutan kan du välja **routningstabell**. Om du inte ser något alternativ för routningstabellen innebär det att du inte har en anpassad eller standard väg tabell konfigurerad i den här virtuella hubben.

## <a name="view-output"></a><a name="output"></a>Visa utdata

I sidans utdata visas följande fält:

* **Prefix**: adressprefix som är känt för den aktuella entiteten (från den virtuella hubben router)
* **Nästa hopp typ**: kan vara Virtual Network anslutning, VPN_S2S_Gateway, ExpressRouteGateway, fjärrhubb eller Azure-brandvägg.
* **Nästa hopp**: det här är länken till resurs-ID: t för nästa hopp, eller visar bara on-Link för att innebära den aktuella hubben.
* **Ursprung**: resurs-ID för Dirigerings källa.
* **Som sökväg**: BGP-attributet as (autonom system)-sökvägen visar alla as-nummer som måste passera för att komma till platsen där prefixet som sökvägen är kopplad till annonseras från.

### <a name="example"></a><a name="example"></a>Exempel

Värdena i följande exempel tabell innebär att den virtuella Hub-anslutningen eller routningstabellen har lärt vägen för 10.2.0.0/24 (ett förgrenings prefix). Den har lärt vägen på grund av **nästa hopp typ** VPN_S2S_Gateway med **nästa hopp** VPN gateway resurs-ID. **Flödets ursprung** pekar på resurs-ID för den ursprungliga VPN-gatewayen/väg tabellen/anslutningen. **Som sökväg** anger GRENens as-sökväg.

Använd rullnings listen längst ned i tabellen om du vill visa "AS-sökväg".

| **Prefix** |  **Nästa hopptyp** | **Nästa hopp** |  **Flödes ursprung** |**SOM sökväg** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |/Subscriptions/ `<sub id>` /resourceGroups/- `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw|/Subscriptions/ `<sub id>` /resourceGroups/- `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw| 20000|

**Angående**

* Om 0.0.0.0/0 visas i utdata för att **Hämta effektiva vägar** , betyder det att vägen finns i en av väg tabellerna. Men om den här vägen har kon figurer ATS för Internet krävs en ytterligare flagga **"enableInternetSecurity": true** i anslutningen. Den effektiva vägen på den virtuella datorns nätverkskort kommer inte att visa vägen om flaggan "enableInternetSecurity" för anslutningen är "false".

* Fältet **sprida standard väg** visas i Azure Virtual WAN-portalen när du redigerar en virtuell nätverks anslutning, en VPN-anslutning eller en ExpressRoute-anslutning. Det här fältet anger **enableInternetSecurity** -flaggan, som alltid är "falskt" som standard för ExpressRoute-och VPN-anslutningar, men "true" för virtuella nätverks anslutningar.

* När du visar effektiva vägar på ett virtuellt dator nätverk kan du se nästa hopp som Virtual Network Gateway, vilket innebär att routern för virtuell hubb när den virtuella datorn finns i en eker som är ansluten till en virtuell WAN-hubb.

* Visa effektiva vägar för en virtuell hubb väg tabell fylls bara om den virtuella hubben har minst en typ av anslutning (VPN/ER/VNET) som är ansluten till den.

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuella WAN-nätverk finns i [översikten om virtuellt WAN](virtual-wan-about.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
