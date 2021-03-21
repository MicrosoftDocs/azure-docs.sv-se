---
title: 'Översikt över Azure ExpressRoute: Anslut via en privat anslutning'
description: Den tekniska översikten över ExpressRoute förklarar hur du kan använda en ExpressRoute-anslutning för att utöka ditt lokala nätverk till Azure över en privat anslutning.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: overview
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: 82562eae748753cd785851c5d91f5f152b4c9960
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92206959"
---
# <a name="what-is-azure-expressroute"></a>Vad är Azure ExpressRoute?
Med ExpressRoute kan du utöka ditt lokala nätverk till Microsofts moln över en privat anslutning med hjälp av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, t.ex. Microsoft Azure och Microsoft 365.

Anslutningen kan vara från ett ”any-to-any”-nätverk (IP VPN), en punkt-till-punkt-anslutning via Ethernet eller en virtuell korsanslutning via en anslutningsleverantör till ett samlokaliseringscenter. ExpressRoute-anslutningar går inte via det offentliga Internet. Detta gör att ExpressRoute-anslutningar ger bättre tillförlitlighet, snabbare hastigheter, konsekventa svarstider och högre säkerhet än typiska anslutningar via Internet. Information om hur du ansluter nätverket till Microsoft med ExpressRoute finns i ExpressRoute- [anslutnings modeller](expressroute-connectivity-models.md).

![Översikt över ExpressRoute-anslutning](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Viktiga fördelar

* Layer 3-anslutningen mellan ditt lokala nätverk och Microsoft Cloud via en anslutningsleverantör. Anslutningen kan vara från ett ”any-to-any”-nätverk (IPVPN), en ”point-to-point”-anslutning med Ethernet, eller med en virtuell korsanslutning via ett Ethernet-utbyte.
* Anslutning till Microsofts molntjänster i alla regioner i den geopolitiska regionen.
* Global anslutning till Microsofts tjänster i alla regioner med ExpressRoutes-premiumtillägget.
* Dynamisk routning mellan ditt nätverk och Microsoft via BGP.
* Inbyggd redundans i varje peeringplats för högre tillförlitlighet.
* [SLA](https://azure.microsoft.com/support/legal/sla/)för anslutnings drift tid.
* QoS-stöd för Skype för företag.

Mer information finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

## <a name="features"></a>Funktioner

### <a name="layer-3-connectivity"></a>Layer 3-anslutning
Microsoft använder BGP, branschens standardprotokoll för dynamisk routning för att utbyta routning mellan det lokala nätverket, dina instanser i Azure och Microsofts offentliga adresser. Vi upprättar flera BGP-sessioner med ditt nätverk för olika trafikprofiler. Mer information finns i artikeln [ExpressRoute-krets och routningsdomäner](expressroute-circuit-peerings.md).

### <a name="redundancy"></a>Redundans
Varje ExpressRoute-krets består av två anslutningar till två Microsoft Enterprise Edge-routrar (msee) på en [ExpressRoute-plats](./expressroute-locations.md#expressroute-locations) från anslutnings leverantören/din nätverks gräns. Microsoft kräver en dubbel BGP-anslutning från anslutningsleverantören/din nätverksgräns – en för varje MSEE. Du kan välja att inte distribuera redundanta enheter/Ethernet-kretsar. Dock använder anslutningsleverantörer redundanta enheter för att dina projekt ska lämnas över till Microsoft på ett redundant sätt. En redundant Layer 3-anslutningskonfiguration är ett krav för att vår [SLA](https://azure.microsoft.com/support/legal/sla/) ska vara giltig.

### <a name="connectivity-to-microsoft-cloud-services"></a>Anslutning till Microsofts molntjänster
ExpressRoute-anslutningar ger åtkomst till följande tjänster:
* Microsoft Azure-tjänster
* Microsoft 365 tjänster

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

En detaljerad lista över de tjänster som stöds via ExpressRoute finns på sidan [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Anslutning till alla regioner inom en geopolitisk region
Du kan ansluta till Microsoft på någon av våra [peeringplatser](expressroute-locations.md) och få åtkomst till regioner inom den geopolitiska regionen.

Om du till exempel ansluter till Microsoft i Amsterdam via ExpressRoute. Du har åtkomst till alla Microsoft-molntjänster som finns i norra och västra Europa. En översikt över geopolitiska regioner, tillhörande Microsoft-molnområden och motsvarande ExpressRoute-peeringplatser finns i [ExpressRoute-partners och peeringplatser](expressroute-locations.md).

### <a name="global-connectivity-with-expressroute-premium"></a>Global anslutning med ExpressRoute Premium
Du kan aktivera [ExpressRoute Premium](expressroute-faqs.md) för att utöka anslutningen mellan politiska gränser. Om du till exempel ansluter till Microsoft i Amsterdam via ExpressRoute får du till gång till alla Microsoft-molntjänster som finns i alla regioner över hela världen. Du kan också få åtkomst till tjänster som har distribuerats i Sydamerika eller Australien på samma sätt som du har åtkomst till regionerna Nord och Västeuropa. Nationella moln ingår inte.

### <a name="local-connectivity-with-expressroute-local"></a>Lokal anslutning med ExpressRoute Local
Du kan överföra data kostnads effektivt genom att aktivera den [lokala SKU: n](expressroute-faqs.md). Med lokal SKU kan du hämta dina data till en ExpressRoute-plats nära den Azure-region som du vill ha. Med lokal ingår data överföring i ExpressRoute port Charge. 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>Lokal anslutning till flera platser med ExpressRoute Global Reach
Du kan aktivera ExpressRoute Global Reach för att utbyta data på dina olika lokala platser genom att ansluta ExpressRoute-kretsarna. Om du till exempel har ett privat data Center i Kalifornien anslutet till en ExpressRoute-krets i Silicon dal och ett annat privat data Center i Texas anslutna till en ExpressRoute-krets i Borås. Med ExpressRoute Global Reach kan du ansluta dina privata data Center till varandra genom dessa två ExpressRoute-kretsar. Din trafik över datacenter passerar genom Microsofts nätverk.

Mer information finns i [ExpressRoute Global Reach](expressroute-global-reach.md).
### <a name="rich-connectivity-partner-ecosystem"></a>Utförligt ekosystem med anslutningspartner
ExpressRoute har ett ständigt växande ekosystem med anslutningsleverantörer och systemintegrerarpartner. Den senaste informationen finns i [ExpressRoute-partner och peeringplatser](expressroute-locations.md).

### <a name="connectivity-to-national-clouds"></a>Anslutning till nationella moln
Microsoft använder isolerade molnmiljöer för särskilda geopolitiska regioner och kundsegment. Se sidan [ExpressRoute-partner och peeringplatser](expressroute-locations.md) för en lista med nationella moln och leverantörer.

### <a name="expressroute-direct"></a>ExpressRoute Direct
Med ExpressRoute Direct får kunder möjligheten att ansluta direkt till Microsofts globala nätverk vid peering-platser strategiskt fördelade runt om i världen. ExpressRoute Direct tillhandahåller dubbla 100-Gbit/s-anslutningar, som har stöd för aktiv/aktiv anslutning i stor skala.

Viktiga funktioner som ExpressRoute Direct tillhandahåller inkluderar, men är inte begränsade till:

* Stora datainmatningar till tjänster som Storage och Cosmos DB
* Fysisk isolering för branscher som är reglerade och kräver dedikerade och isolerade anslutningar, till exempel bankväsende, myndigheter och detaljhandel
* Detaljerad kontroll över kretsfördelning utifrån affärsenheter

Mer information finns i [Om ExpressRoute Direct](./expressroute-erdirect-about.md).

### <a name="bandwidth-options"></a>Bandbreddsalternativ
Du kan köpa ExpressRoute-kretsar för en mängd olika bandbredder. De bandbredder som stöds visas som följer. Kontrollera med din anslutningsleverantör för att avgöra vilka bandbredder de stöder.

* 50 Mbit/s
* 100 Mbit/s
* 200 Mbit/s
* 500 Mbit/s
* 1 Gbit/s
* 2 Gbit/s
* 5 Gbit/s
* 10 Gbit/s

### <a name="dynamic-scaling-of-bandwidth"></a>Dynamisk skalning av bandbredd
Du kan öka ExpressRoute-kretsens bandbredd (baserat på bästa prestanda) utan att behöva avbryta dina anslutningar. Mer information finns i [ändra en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md#modify).

### <a name="flexible-billing-models"></a>Flexibla faktureringsmodeller
Du kan välja den faktureringsmodell som passar dig bäst. Välj mellan de fakturerings modeller som anges som följer. Mer information finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

* **Obegränsad data**. Fakturering baseras på en månatlig avgift. All inkommande och utgående dataöverföring ingår utan extra kostnad.
* **Avgiftsbelagda data**. Fakturering baseras på en månatlig avgift. All inkommande dataöverföring är kostnadsfri. Utgående dataöverföring debiteras per GB data som överförs. Dataöverföringskostnader varierar beroende på region.
* **ExpressRoutes premiumtillägg**. ExpressRoutes premium är ett tillägg till ExpressRoute-kretsen. ExpressRoutes premiumtillägg innehåller följande funktioner: 
  * Ökade väggränser för Azures offentliga och privata peering från 4 000 vägar till 10 000 vägar.
  * Global anslutning för tjänster. En ExpressRoute-krets som har skapats i vilken region som helst (exklusive nationella moln) har till gång till resurser i alla andra regioner i världen. Till exempel kan ett virtuellt nätverk som skapats i Europa, västra nås via en ExpressRoute-krets som etablerats i Silicon Valley.
  * Ökat antal VNet-länkar per ExpressRoute-krets från 10 till en högre gräns, beroende på kretsens bandbredd.

## <a name="faq"></a>Vanliga frågor
Vanliga frågor om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

## <a name="whats-new"></a><a name="new"></a>Vad är det senaste?

Prenumerera på RSS-flödet och Visa de senaste ExpressRoute-funktions uppdateringarna på sidan [Azure updates](https://azure.microsoft.com/updates/?category=networking&query=ExpressRoute) .

## <a name="next-steps"></a>Nästa steg
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).
* Läs mer om [ExpressRoute-anslutningsmodeller](expressroute-connectivity-models.md).
* Hitta en tjänstleverantör. Se [ExpressRoute-partners och peeringplatser](expressroute-locations.md).