---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b283add6cff1400cc3141f4fba3f0f3939ee34aa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "97665121"
---
|  | **Punkt-till-plats** | **Plats-till-plats** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Tjänster som stöds av Azure** |Cloud Services och Virtual Machines |Cloud Services och Virtual Machines |[Tjänstlista](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Vanliga bandbredder** |Baserat på gateway-SKU |Vanligtvis < 1 Gbit/s sammanlagt |50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps |
| **Protokoll som stöds** |Secure Sockets Tunneling Protocol (SSTP), OpenVPN och IPsec |IPsec |Direktanslutning över VLAN, NSP:er VPN-teknologier (MPLS, VPLS,...) |
| **Routning** |RouteBased (dynamisk) |Vi stöder principbaserad (statisk routning) och ruttbaserad (dynamisk routning VPN) |BGP |
| **Anslutningsåterhämtning** |aktiv-passiv |aktiv-passiv eller aktiv-aktiv |aktiv-aktiv |
| **Vanligt användningsfall** |Säker åtkomst till virtuella Azure-nätverk för fjärran vändare |Scenarier för utveckling/testning/labb och små till medel stora produktions arbets belastningar för moln tjänster och virtuella datorer |Åtkomst till alla Azure-tjänster (validerad lista), Enterprise-klass och verksamhetskritiska arbetsbelastningar, säkerhetskopiering, Big Data, Azure som en DR-plats |
| **Serviceavtal** |[Serviceavtal](https://azure.microsoft.com/support/legal/sla/) |[Serviceavtal](https://azure.microsoft.com/support/legal/sla/) |[Serviceavtal](https://azure.microsoft.com/support/legal/sla/) |
| **Prissättning** |[Prissättning](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Prissättning](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Prissättning](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Teknisk dokumentation** |[VPN Gateway dokumentation](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[VPN Gateway dokumentation](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Dokumentation om ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| **Vanliga frågor och svar** |[Vanliga frågor och svar om VPN Gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Vanliga frågor och svar om VPN Gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Vanliga frågor och svar för ExpressRoute](../articles/expressroute/expressroute-faqs.md) |
