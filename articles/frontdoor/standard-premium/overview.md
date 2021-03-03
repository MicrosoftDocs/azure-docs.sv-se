---
title: Standard/Premium för Azure-dörren | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azures front dörr standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: bcfff1a2c8490a05f4b96a8e2ff68186348f596f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742372"
---
# <a name="what-is-azure-front-door-standardpremium-preview"></a>Vad är Azures främre dörr standard/Premium (för hands version)?

> [!IMPORTANT]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [Azures front dörrs dokument](../front-door-overview.md).

Azures front dörr standard/Premium är en snabb, tillförlitlig och säker modern moln-CDN som använder Microsoft Global Edge-nätverket och integreras med intelligent hot skydd. Den kombinerar funktionerna i Azure-frontend, Azure Content Delivery Network (CDN) standard och Azure WebApplication-brandväggen (WAF) till en enda säker moln CDN-plattform.

Med Azures front dörr standard/Premium kan du omvandla dina globala konsument-och företags program till säkra och högpresterande anpassade moderna program med innehåll som når en global publik på nätverks gränsen nära användaren. Det gör det också möjligt för ditt program att skala ut utan att värma upp samtidigt som du får från Global HTTP-belastnings utjämning med omedelbar redundans.

   :::image type="content" source="../media/overview/front-door-overview.png" alt-text="Azure frontend-standard/Premium-arkitektur" lightbox="../media/overview/front-door-overview-expanded.png":::

Azures främre dörr standard/Premium fungerar på Layer 7 (HTTP/HTTPS-skiktet) med anycast-protokoll med delad TCP och Microsofts globala nätverk för att förbättra den globala anslutningen. Baserat på din anpassade routningsmetod med regel uppsättning kan du se till att Azure-frontend dirigerar klient begär anden till det snabbaste och mest tillgängliga ursprunget. Ett programs ursprung är en Internet-riktad tjänst som finns i eller utanför Azure. Azures front dörr standard/Premium tillhandahåller en mängd olika metoder för trafik hantering och alternativ för hälso övervakning av ursprung för att passa olika program behov och automatiska failover-scenarier. På samma sätt som Traffic Manager är front dörren elastisk till problem, inklusive problem med en hel Azure-region.

Azures front dörr skyddar också din app på kanterna med integrerade brand Väggs skydd för webb program, bot-skydd och inbyggd att placera 3/Layer 4-DDoS-skydd (distributed denial of Service). Det skyddar också dina privata backend-ändar med tjänsten Private Link. Azures front dörr ger dig Microsofts bästa säkerhet i global skala.  

>[!NOTE]
> Med Azure har du tillgång till en uppsättning fullständigt hanterade belastningsutjämningslösningar för dina scenarier.
>
> * Om du vill göra DNS-baserad global Routning och **inte** har kraven för Transport Layer Security (TLS) protokoll terminering ("SSL-avlastning"), per http/https-begäran eller bearbetning av program lager, granska [Traffic Manager](../../traffic-manager/traffic-manager-overview.md).
> * Om du vill belastningsutjämna mellan servrarna i en region i program lagret granskar du [Application Gateway](../../application-gateway/overview.md)
> * Om du vill använda belastnings utjämning för nätverks lager granskar du [Load Balancer](../../load-balancer/load-balancer-overview.md).
>
> Dina scenarier från slut punkt till slut punkt kan dra nytta av att kombinera de här lösningarna efter behov.
> En alternativ jämförelse för Azure-belastnings utjämning finns i [Översikt över belastnings Utjämnings alternativ i Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

> [!IMPORTANT]
> Azure-front dörren standard/Premium är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="why-use-azure-front-door-standardpremium-preview"></a>Varför ska jag använda Azures front dörr standard/Premium (för hands version)?

Azures front dörr standard/Premium tillhandahåller en enda enhetlig plattform som följer både dynamisk och statisk acceleration med inbyggd nyckel färdig säkerhets integrering och en enkel och förutsägbar pris modell. Med front dörren kan du också definiera, hantera och övervaka den globala routningen för din app.

Viktiga funktioner som ingår i Azures främre dörr standard/Premium (för hands version):

- Accelererade program prestanda med hjälp av **[delat TCP-baserat anycast-](../front-door-routing-architecture#splittcp.md)** protokoll.

- Övervakning av intelligent **[hälso avsökning](concept-health-probes.md)** och belastnings utjämning mellan **[ursprung](concept-origin.md)**.

- Definiera en egen **[anpassad domän](how-to-add-custom-domain.md)** med flexibel domän validering.

- Program säkerhet med integrerad **[brand vägg för webbaserade program (WAF)](../../web-application-firewall/afds/afds-overview.md)**.

- SSL-avlastning och integrerad **[certifikat hantering](how-to-configure-https-custom-domain.md)**.

- Skydda dina ursprung med **[privat länk](concept-private-link.md)**.  

- Anpassningsbar trafik dirigering och optimeringar via **[regel uppsättning](concept-rule-set.md)**.

- **[Inbyggda rapporter](how-to-reports.md)** med allt-i-ett-instrument panel för både främre dörr-och säkerhets mönster.

- Övervakning och aviseringar i **[real tid](how-to-monitor-metrics.md)** som integreras med Azure-övervakning.

- **[Loggning](how-to-logs.md)** för varje begäran om front dörren och misslyckade hälso avsökningar.

- Inbyggt stöd för IPv6-anslutning från slut punkt till slut punkt och HTTP/2-protokoll.

## <a name="pricing"></a>Prissättning

Azures front dörr standard/Premium har två SKU: er, standard och Premium. Se [jämförelse av nivåer](tier-comparison.md). Information om priser finns i [Prissättning för Front Door](https://azure.microsoft.com/pricing/details/frontdoor/). 

## <a name="whats-new"></a>Nyheter

Prenumerera på RSS-flödet och Visa de senaste Azure-funktionerna för front dörren på sidan [Azure updates](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door) .

## <a name="next-steps"></a>Nästa steg

* Läs hur du [skapar en Front Door](create-front-door-portal.md).
