---
title: Azure-front dörr | Microsoft Docs
description: Den här artikeln innehåller en översikt för Azure Front Door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: ef3d415f4f0ca82e79df84ef48acc0323a69099a
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952158"
---
# <a name="what-is-azure-front-door"></a>Vad är Azure Front Door?

> [!IMPORTANT]
> Den här dokumentationen är för Azures front dörr. Letar du efter information om Azure front dörr standard/Premium (för hands version)? Visa [här](standard-premium/overview.md).

Azures front dörr är en global, skalbar start punkt som använder Microsoft Global Edge-nätverket för att skapa snabba, säkra och mycket skalbara webb program. Med front dörren kan du omvandla dina globala konsument-och företags program till robusta, högpresterande anpassade moderna program med innehåll som når en global publik via Azure.

<p align="center">
  <img src="./media/front-door-overview/front-door-visual-diagram.png" alt="Front Door architecture" width="600" title="Azure Front Door">
</p>

Front dörren fungerar på Layer 7 (HTTP/HTTPS-lager) med anycast-protokollet med delad TCP och Microsofts globala nätverk för att förbättra den globala anslutningen. Baserat på din routningsmetod kan du se till att front dörren dirigerar klient begär anden till den snabbaste och mest tillgängliga program Server delen. En programserverdel är en Internetansluten tjänst i eller utanför Azure. Med front dörren får du en mängd olika [metoder för trafik](front-door-routing-methods.md) hantering och [övervaknings alternativ för hälso tillstånd](front-door-health-probes.md) som passar olika program behov och automatiska failover-scenarier. På samma sätt som [Traffic Manager](../traffic-manager/traffic-manager-overview.md)är front dörren elastisk till problem, inklusive problem med en hel Azure-region.

>[!NOTE]
> Med Azure har du tillgång till en uppsättning fullständigt hanterade belastningsutjämningslösningar för dina scenarier. 
> * Om du vill göra DNS-baserad global Routning och **inte** har kraven för Transport Layer Security (TLS) protokoll terminering ("SSL-avlastning"), per http/https-begäran eller bearbetning av program lager, granska [Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
> * Om du vill belastningsutjämna mellan servrarna i en region i program lagret granskar du [Application Gateway](../application-gateway/overview.md).
> * Om du vill använda belastnings utjämning för nätverks lager granskar du [Load Balancer](../load-balancer/load-balancer-overview.md). 
> 
> Dina scenarier från slut punkt till slut punkt kan dra nytta av att kombinera de här lösningarna efter behov.
> En alternativ jämförelse för Azure-belastnings utjämning finns i [Översikt över belastnings Utjämnings alternativ i Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="why-use-azure-front-door"></a>Varför ska jag använda Azures front dörr?

Med den första dörren kan du bygga, hantera och skala ut ditt dynamiska webb program och statiskt innehåll. Med front dörr kan du definiera, hantera och övervaka den globala routningen för din webb trafik genom att optimera prestanda och tillförlitlighet för den högsta nivån genom en snabb global redundans.

Viktiga funktioner som ingår i front dörren:

* Accelererade program prestanda med hjälp av **[delat TCP](front-door-routing-architecture.md#splittcp)**-baserat **[anycast-protokoll](front-door-routing-architecture.md#anycast)**.

* Övervakning av intelligent **[hälso avsökning](front-door-health-probes.md)** för Server dels resurser.

*  **[URL – sökväg baserad](front-door-route-matching.md)** routning för förfrågningar.

* Möjliggör värdskap för flera webbplatser för effektiv program infrastruktur. 

* Cookie-baserad **[session tillhörighet](front-door-routing-methods.md#affinity)**.

* **[SSL-avlastning](front-door-custom-domain-https.md)** och certifikat hantering.

* Definiera en egen **[anpassad domän](front-door-custom-domain.md)**. 

* Program säkerhet med integrerad  **[brand vägg för webbaserade program (WAF)](../web-application-firewall/overview.md)**.

* Omdirigera HTTP-trafik till HTTPS med **[URL-omdirigering](front-door-url-redirect.md)**.

* Anpassad vidarebefordrings Sök väg med **[URL-omskrivning](front-door-url-rewrite.md)**.

* Inbyggt stöd för IPv6-anslutning från slut punkt till slut punkt och **[http/2-protokoll](front-door-http2.md)**.

## <a name="pricing"></a>Priser

Information om priser finns i [Prissättning för Front Door](https://azure.microsoft.com/pricing/details/frontdoor/). Se [SLA för Azures front dörr](https://azure.microsoft.com/en-us/support/legal/sla/frontdoor/v1_0/).

## <a name="whats-new"></a>Nyheter

Prenumerera på RSS-flödet och Visa de senaste Azure-funktionerna för front dörren på sidan [Azure updates](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door) .

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
