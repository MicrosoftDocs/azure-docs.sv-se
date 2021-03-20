---
title: 'Azure-ExpressRoute: QoS-krav'
description: Den här sidan innehåller detaljerade krav för att konfigurera och hantera QoS. Skype för företag/röst tjänster diskuteras.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: duau
ms.openlocfilehash: 7f14f43fa341df40ecd35340b7311e1acd18004c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92204715"
---
# <a name="expressroute-qos-requirements"></a>QoS-krav för ExpressRoute
Skype för företag har olika arbetsbelastningar som kräver särskild QoS-behandling. Om du tänker använda rösttjänster via ExpressRoute bör du följa kraven som beskrivs nedan.

![Diagram som visar röst tjänsterna som går genom ExpressRoute.](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> QoS-kraven gäller endast för Microsoft-peering. De DSCP-värden i din nätverkstrafik som togs emot på Azures offentliga peering och Azure privata peering kommer att återställas till 0. 
> 
> 

Följande tabell innehåller en lista över DSCP-markeringar som används av Microsoft Teams och Skype för företag. Se [Hantera QoS för Skype för företag](/SkypeForBusiness/manage/network-management/qos/managing-quality-of-service-QoS) för mer information.

| **Trafikklass** | **Behandling (DSCP-markering)** | **Microsoft Teams och Skype för företag-arbetsbelastningar** |
| --- | --- | --- |
| **Röst** |EF (46) |Skype/Microsoft Teams/Lync röst |
| **Interaktiv** |AF41 (34) |Video, VBSS |
| |AF21 (18) |Appdelning | 
| **Standardvärde** |AF11 (10) |Filöverföring |
| |CS0 (0) |Annat |

* Du bör klassificera arbetsbelastningarna och markera rätt DSCP-värden. Följ riktlinjerna [här](/SkypeForBusiness/manage/network-management/qos/configuring-port-ranges-for-your-skype-clients#configure-quality-of-service-policies-for-clients-running-on-windows-10) om hur du anger DSCP-markeringar i nätverket.
* Du bör konfigurera och ge stöd för flera QoS-köer i nätverket. Rösten måste vara en fristående klass och ta emot den EF-behandling som anges i [RFC 3246](https://www.ietf.org/rfc/rfc3246.txt). 
* Du kan bestämma kömekanism, princip för överbelastningsidentifiering samt bandbreddsallokering per trafikklass. Men DSCP-markeringen för arbetsbelastningar i Skype för företag måste bevaras. Om du använder DSCP-markeringar som inte visas ovan, t.ex. AF31 (26), måste du skriva DSCP-värdet som 0 innan paketet skickas till Microsoft. Microsoft skickar endast paket som har markerats med DSCP-värdet i ovanstående tabellen. 

## <a name="next-steps"></a>Nästa steg
* Se kraven för [Routning](expressroute-routing.md) och [NAT](expressroute-nat.md).
* Se följande länkar för att konfigurera ExpressRoute-anslutningen.
  
  * [Skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md)
  * [Konfigurera routning](expressroute-howto-routing-classic.md)
  * [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-classic.md)