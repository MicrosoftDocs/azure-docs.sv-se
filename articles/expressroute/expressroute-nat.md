---
title: 'Azure-ExpressRoute: NAT-krav för kretsar'
description: Den här sidan innehåller detaljerade krav för att konfigurera och hantera NAT för ExpressRoute-kretsar.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: a2c322c765d39a3afe4974ed88bf4dc18fd467a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89569653"
---
# <a name="expressroute-nat-requirements"></a>ExpressRoutes NAT-krav
För att kunna ansluta till Microsofts molntjänster med ExpressRoute måste du konfigurera och hantera NAT. Vissa anslutningsleverantörer erbjuder konfigurering och hantering av NAT som en hanterad tjänst. Fråga din anslutningsleverantör om de erbjuder denna tjänst. Om inte, måste du följa kraven som beskrivs nedan. 

Läs sidan [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md) om du vill få en översikt över de olika routningsdomänerna. För att uppfylla de offentliga IP-adresskraven för Azures offentliga och Microsofts peering, rekommenderar vi att du konfigurerar NAT mellan nätverket och Microsoft. Det här avsnittet innehåller en detaljerad beskrivning av den NAT-infrastruktur som du måste konfigurera.

## <a name="nat-requirements-for-microsoft-peering"></a>NAT-krav för Microsoft-peering
Med Microsofts peeringsökväg kan du ansluta till Microsofts molntjänster som inte stöds via Azures offentliga peeringsökväg. Listan över tjänster omfattar Microsoft 365 tjänster, till exempel Exchange Online, SharePoint Online och Skype för företag. Microsoft förväntas stödja dubbelriktad anslutning i Microsofts peering. Trafik till Microsofts molntjänster måste vara SNATed till giltiga offentliga IPv4-adresser innan de kommer in i Microsoft-nätverket. Trafik som är avsedd för ditt nätverk från Microsofts molntjänster måste vara SNATed i din Internet-anslutning för att förhindra [asymmetrisk routning](expressroute-asymmetric-routing.md). Bilden nedan innehåller en övergripande bild av hur NAT ska konfigureras för Microsoft-peering.

![Diagram på hög nivå över hur NAT ska konfigureras för Microsoft-peering.](./media/expressroute-nat/expressroute-nat-microsoft.png) 

### <a name="traffic-originating-from-your-network-destined-to-microsoft"></a>Trafik från nätverket till Microsoft
* Du måste se till att trafiken som kommer till Microsofts peeringsökväg har en giltig offentlig IPv4-adress. Microsoft måste kunna verifiera ägaren till IPv4 NAT-adresspoolen mot ett regionalt RIR (Routing Internet Registry) eller ett IRR (Internet Routing Registry). En kontroll utförs baserat på antalet AS-nummer som peerkopplas och de IP-adresser som används för NAT-enheten. Se sidan [ExpressRoute-routningskrav](expressroute-routing.md) för information om routningsregister.
* IP-adresser som används vid konfigurationen av Azures offentliga peering och andra ExpressRoute-kretsar får inte annonseras till Microsoft via BGP-sessionen. Det finns inga begränsningar för längden på NAT:s IP-prefix som annonseras via den här peeringen.
  
  > [!IMPORTANT]
  > NAT:s IP-pool som annonseras till Microsoft får inte annonseras till Internet. Detta bryter anslutningen till andra Microsoft-tjänster.
  > 
  > 

### <a name="traffic-originating-from-microsoft-destined-to-your-network"></a>Trafik från Microsoft till ditt nätverk
* Vissa scenarier kräver att Microsoft initierar anslutningen till tjänstens slutpunkter som finns i ditt nätverk. Ett typiskt exempel på scenariot är att ansluta till ADFS-servrar som finns i nätverket från Microsoft 365. I sådana fall måste du meddela lämpliga prefix från ditt nätverk till Microsofts peering. 
* Du måste använda SNAT för Microsoft-trafik för din Internet-anslutning för tjänstens slutpunkter i nätverket för att förhindra [asymmetrisk routning](expressroute-asymmetric-routing.md). Begäranden **och svar** med en mål-IP-adress som matchar en väg som tagits emot via ExpressRoute skickas alltid till ExpressRoute. Asymmetrisk routning sker om en begäran tas emot via Internet när svaret skickas via ExpressRoute. Om du använder SNAT för den inkommande Microsoft-trafiken på Internet-anslutningen tvingas svarstrafiken tillbaka till Internet-anslutningen, vilket löser problemet.

![Asymmetrisk routning med ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="nat-requirements-for-azure-public-peering"></a>NAT-krav för Azures offentliga peering

> [!NOTE]
> Offentlig Azure-peering är inte tillgänglig för nya kretsar.
> 

Med Azures offentliga peeringsökväg kan du ansluta till alla tjänster som finns i Azure med deras offentliga IP-adresser. Det inkluderar tjänster som finns i listan [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md) och tjänster med ISV:er i Microsoft Azure. 

> [!IMPORTANT]
> Anslutningen till Microsoft Azure-tjänster vid offentlig peering initieras alltid från ditt nätverk till Microsoft-nätverket. Därför kan du inte initiera sessioner från Microsoft Azure-tjänster till nätverket via ExpressRoute. Om du försöker göra det skickas paket till annonserade IP-adresser via internet istället för ExpressRoute.
> 

Trafik till Microsoft Azure vid offentlig peering måste vara SNATed till giltiga offentliga IPv4-adresser innan de kommer in i Microsoft-nätverket. I bilden nedan ges en översiktlig bild av hur NAT kan vara konfigurerat för att uppfylla ovanstående krav.

![Diagram på hög nivå över hur NAT kan konfigureras så att det blir SNATed till giltiga offentliga IPv4-adresser innan de går in i Microsoft-nätverket.](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### <a name="nat-ip-pool-and-route-advertisements"></a>NAT:s IP-pool och routningsmeddelanden
Du måste se till att trafiken som kommer in via Azures offentliga peeringsökväg har en giltig offentlig IPv4-adress. Microsoft måste kunna verifiera ägarskapet för IPv4 NAT-adresspoolen mot ett regionalt RIR (Routing Internet Registry) eller ett IRR (Internet Routing Registry). En kontroll utförs baserat på antalet AS-nummer som peerkopplas och de IP-adresser som används för NAT-enheten. Se sidan [ExpressRoute-routningskrav](expressroute-routing.md) för information om routningsregister.

Det finns inga begränsningar för längden på NAT:s IP-prefix som annonseras vid peeringen. Du måste övervaka NAT-poolen och se till att du inte har för få NAT-sessioner.

> [!IMPORTANT]
> NAT:s IP-pool som annonseras till Microsoft får inte annonseras till Internet. Detta bryter anslutningen till andra Microsoft-tjänster.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Se kraven för [Routning](expressroute-routing.md) och [QoS](expressroute-qos.md).
* Arbetsflödesinformation finns i [ExpressRoute-kretsens etablering av arbetsflöden och kretsstatus](expressroute-workflows.md).
* Konfigurera ExpressRoute-anslutningen.
  
  * [Skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurera routning](expressroute-howto-routing-portal-resource-manager.md)
  * [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-portal-resource-manager.md)

