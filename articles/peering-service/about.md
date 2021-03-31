---
title: Översikt över Azure peering-tjänsten
description: Läs mer om översikt över Azure peering-tjänsten
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 198ba23920179e71e095e498ee2173d7f0111d42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026737"
---
# <a name="azure-peering-service-overview"></a>Översikt över Azure peering-tjänsten

Azure peering-tjänsten är en nätverks tjänst som förbättrar kund anslutningen till Microsofts moln tjänster, till exempel Microsoft 365, Dynamics 365, SaaS-tjänster (Software as a Service), Azure eller Microsoft-tjänster som är tillgängliga via det offentliga Internet. Microsoft samarbetar med Internet leverantörer (ISP), Internet Exchange-partners (IXPs) och SDCI-leverantörer (Software-Defined Cloud Interconnect) i hela världen för att tillhandahålla tillförlitliga och högpresterande offentliga anslutningar med optimal routning från kunden till Microsoft-nätverket.

Med peering service kan kunder välja en väl ansluten partner tjänst leverantör i en specifik region. Offentlig anslutning är optimerad för hög tillförlitlighet och minimal fördröjning från moln tjänster till slut användar platsen.

![Distribuerad anslutning till Microsoft Cloud](./media/peering-service-about/peering-service-what.png)

Kunder kan också välja telemetri för peering-tjänster, till exempel användar fördröjnings mått till Microsoft Network, BGP Route-övervakning och aviseringar mot läckor och kapningar genom att registrera peering-tjänstens anslutning i Azure Portal. 

Om du vill använda peering-tjänsten behöver kunderna inte registreras hos Microsoft. Det enda kravet är att kontakta en [peering service-partner](location-partners.md) för att hämta tjänsten. För att du ska kunna använda telemetri för peering-tjänsten måste kunderna registreras för den i Azure Portal.

Anvisningar om hur du registrerar peering-tjänsten finns i [Registrera peering-tjänsten med hjälp av Azure Portal](azure-portal.md). 

> [!NOTE]
> Den här artikeln är avsedd för nätverks arkitekter som ansvarar för företags anslutningen till molnet och Internet.


## <a name="what-is-peering-service"></a>Vad är Peering Service?

Peering-tjänsten är:

- En IP-tjänst som använder det offentliga Internet. 
- En samarbets plattform med tjänst leverantörer och en mervärdes tjänst som är avsedd att erbjuda optimal och tillförlitlig routning till kunden via tjänste leverantörs partner till Microsoft-molnet via det offentliga nätverket.

Peering-tjänsten är inte en privat anslutnings produkt som Azure ExpressRoute eller en VPN-produkt.

> [!NOTE]
> Mer information om ExpressRoute finns i [ExpressRoute-dokumentationen](../expressroute/index.yml).
>

## <a name="background"></a>Bakgrund

Microsoft 365, Dynamics 365 och andra Microsoft SaaS-tjänster finns i flera Microsoft-datacenter och kan nås från valfri geografisk plats. Microsofts globala nätverk har Microsofts PoP-platser (Edge-of-Presence) runtom i världen där de kan ansluta till en slutanvändare via sina tjänste leverantörer. 

Microsoft-och partner tjänst leverantörer säkerställer att trafiken för de prefix som registrerats med en peering service-anslutning går in och avslutar de närmsta PoP-platserna för Microsoft Edge på Microsofts globala nätverk. Microsoft säkerställer att nätverks trafiken som kommer från de prefix som är registrerade med peering service-anslutningar tar de närmsta PoP-platserna med Microsoft Edge på Microsofts globala nätverk.

![Microsoft-nätverk och offentliga anslutningar](./media/peering-service-about/peering-service-background-final.png)

> [!NOTE]
> Mer information om Microsoft Global Network finns i [Microsofts globala nätverk](../networking/microsoft-global-network.md).
>

## <a name="why-use-peering-service"></a>Varför ska du använda peering-tjänsten?

Företag som letar efter Internet-första åtkomst till molnet eller som överväger SD-WAN-arkitektur eller med hög användning av Microsoft SaaS Services behöver robust och högpresterande Internet-anslutning. Kunderna kan göra att över gången sker med hjälp av peering-tjänsten. Microsoft och leverantörer har samarbetat med att leverera tillförlitlig och högpresterande offentlig anslutning till Microsoft-molnet. Några av de viktigaste kund funktionerna finns här:

- Bästa offentliga routning via Internet till Microsoft Azure Cloud Services för bästa prestanda och tillförlitlighet.
- Möjlighet att välja önskad tjänstprovider för att ansluta till Microsoft-molnet.
- Trafik insikter som latens rapportering och prefix övervakning.
- Optimala nätverks hopp (som humle) från Microsoft-molnet.
- Route Analytics och Statistics: händelser för ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) väg avvikelser (läcka eller kapnings identifiering) och underoptimal routning.

### <a name="robust-reliable-peering"></a>Robust, tillförlitlig peering

Peering-tjänsten använder två typer av redundans:

- **Lokal redundans**

   Microsoft och tjänste leverantörer sammanställer över flera Microsoft Edge-PoP-platser för att leverera peering-tjänsten. På varje plats måste sammanlänkning stödja redundans över två routrar.

   Varje peering-plats är etablerad med redundanta och diverse peering-länkar.

- **GEO-redundans**

   Microsoft har sammankopplat med tjänst leverantörer på flera platser i tunnel trafik så att trafiken dirigeras till och från Microsoft via alternativa platser om en av kant-noderna har försämrade prestanda. Microsoft dirigerar trafik i det globala nätverket med hjälp av SDN för optimala prestanda.

    Den här typen av redundans använder den kortaste vägen genom att alltid välja närmaste Microsoft Edge-PoP till slutanvändaren och se till att kunden är en nätverks hopp (som humle) bort från Microsoft.

   ![Geo-redundans](./media/peering-service-about/peering-service-geo-shortest.png)

### <a name="optimal-routing"></a>Optimal routning

Följande routing-teknik rekommenderas:

-  **Kall-potatis-routning**

   Den programdefinierade metoden för kall-potatis-routning ger kontroll över nätverks trafik som kommer från Microsoft-molnet. Det säkerställer att trafiken hålls kvar på hög kapacitet, låg latens och mycket tillförlitligt Microsoft globalt nätverk tills det är så nära målet som möjligt.
   
   Routning som inte använder sig av kall-potatis-tekniken kallas för routning av snabb potatis. Med hjälp av routning av frekventa potatis går trafik som härstammar från Microsoft-molnet över Internet.

   ![Kall-potatis-routning](./media/peering-service-about/peering-service-cold-potato.png)

### <a name="monitoring-platform"></a>Övervaknings plattform

   Service övervakning erbjuds för att analysera kund trafik och routning, och tillhandahåller följande funktioner: 

-  **Identifiering av avvikelser i Internet BGP-väg**
          
   Den här tjänsten används för att identifiera och varna för eventuella väg avvikande händelser, t. ex. väg kapning av kundens prefix.

-  **Kund svars tid**

   Den här tjänsten övervakar Dirigerings prestanda mellan kundens plats och Microsoft. 
   
   Routnings prestanda mäts genom att verifiera den svars tid som tas från klienten för att uppnå Microsoft Edge-PoP. Kunder kan visa svars tids rapporter för olika geografiska platser.

   Övervakning fångar in händelserna i händelse av en tjänst försämring.

   ![Övervaknings plattform för peering-tjänsten](media/peering-service-about/peering-service-latency-report.png)

### <a name="traffic-protection"></a>Trafik skydd

Routning sker bara via en prioriterad sökväg som definieras när kunden registreras med peering-tjänsten.

Microsoft garanterar att trafiken dirigeras via önskade sökvägar även om skadlig aktivitet upptäcks.

BGP Route-avvikelser rapporteras i Azure Portal, om det finns några.

## <a name="next-steps"></a>Nästa steg

- Information om peering service-anslutningar finns i [peering service Connections](connection.md).
- Om du vill veta mer om telemetri för peering service-anslutning läser du [telemetri för peering service-anslutning](connection-telemetry.md).
- Information om hur du hittar en Service Provider-partner finns [peering service partners och platser](location-partners.md).
- Information om hur du integrerar en peering service-anslutning finns i avsnittet om integrering av [peering-tjänsten](onboarding-model.md).
- Om du vill registrera en anslutning med hjälp av Azure Portal, se [Registrera en peering service-anslutning med hjälp av Azure Portal](azure-portal.md).
- För att mäta telemetri, se [mått på telemetri för anslutning](measure-connection-telemetry.md).