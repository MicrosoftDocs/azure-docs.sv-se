---
title: Om Azure ExpressRoute-FastPath
description: Lär dig om Azure ExpressRoute-FastPath för att skicka nätverks trafik genom att kringgå gatewayen
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: duau
ms.openlocfilehash: ba23319c35aed1d09da652e6f84b60e5f8e9495e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740893"
---
# <a name="about-expressroute-fastpath"></a>Om ExpressRoute FastPath

ExpressRoute virtuella nätverksgateway är utformad för att utbyta nätverks vägar och dirigera nätverks trafik. FastPath är utformat för att förbättra data Sök vägens prestanda mellan ditt lokala nätverk och ditt virtuella nätverk. När aktive rad skickar FastPath nätverks trafik direkt till virtuella datorer i det virtuella nätverket, vilket kringgår gatewayen.

## <a name="requirements"></a>Krav

### <a name="circuits"></a>Kretsar

FastPath finns på alla ExpressRoute-kretsar.

### <a name="gateways"></a>Gateways

FastPath kräver fortfarande att en virtuell nätverksgateway skapas för att utväxla vägar mellan ett virtuellt nätverk och ett lokalt nätverk. Mer information om virtuella nätverks-gatewayer och ExpressRoute, inklusive prestanda information och gateway SKU: er, finns i [ExpressRoute-gatewayer för virtuella nätverk](expressroute-about-virtual-network-gateways.md).

Om du vill konfigurera FastPath måste den virtuella Nätverksgatewayen vara antingen:

* Ultra Performance
* ErGw3AZ

> [!IMPORTANT]
> Om du planerar att använda FastPath med IPv6-baserad privat peering över ExpressRoute, se till att välja ErGw3AZ för **SKU**.
> 
>

## <a name="limitations"></a>Begränsningar

Även om FastPath har stöd för de flesta konfigurationer, stöder den inte följande funktioner:

* UDR i Gateway-undernätet: om du tillämpar en UDR på Gateway-undernätet för det virtuella nätverket, kommer nätverks trafiken från ditt lokala nätverk även att skickas till den virtuella Nätverksgatewayen.

* VNet-peering: om du har andra virtuella nätverk som är peer-anslutna med det som är anslutet till ExpressRoute kommer nätverks trafiken från ditt lokala nätverk till de andra virtuella nätverken (d.v.s. det kallas "ekrar"-virtuella nätverk) att skickas vidare till den virtuella Nätverksgatewayen. Lösningen är att ansluta alla virtuella nätverk till ExpressRoute-kretsen direkt.

* Grundläggande Load Balancer: om du distribuerar en enkel intern belastningsutjämnare i det virtuella nätverket eller Azure PaaS-tjänsten som du distribuerar i ditt virtuella nätverk använder en grundläggande intern belastningsutjämnare, nätverks trafiken från ditt lokala nätverk till de virtuella IP-adresser som finns på den grundläggande belastningsutjämnaren skickas till den virtuella Nätverksgatewayen. Lösningen är att uppgradera den grundläggande belastningsutjämnaren till en [standard belastnings utjämning](../load-balancer/load-balancer-overview.md).

* Privat länk: om du ansluter till en [privat slut punkt](../private-link/private-link-overview.md) i ditt virtuella nätverk från ditt lokala nätverk, kommer anslutningen att gå via den virtuella Nätverksgatewayen.
 
## <a name="next-steps"></a>Nästa steg

Information om hur du aktiverar FastPath finns i [Länka ett virtuellt nätverk till ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
