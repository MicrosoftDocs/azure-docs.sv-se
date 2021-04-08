---
title: Azure-DDoS skydd
description: Den här sidan innehåller information om hur Azures front dörr hjälper till att skydda mot DDoS-attacker
services: frontdoor
documentationcenter: ''
author: johndowns
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: jodowns
ms.openlocfilehash: 24a8a559d72eae4dab0b6e740b34890f1d7ff114
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96350791"
---
# <a name="ddos-protection-on-front-door"></a>DDoS skydd på front dörren

Azures front dörr har flera funktioner och egenskaper som kan hjälpa till att förhindra DDoS-attacker (distributed denial of Service). Dessa funktioner kan förhindra att angripare når ditt program och påverkar programmets tillgänglighet och prestanda.

## <a name="integration-with-azure-ddos-protection-basic"></a>Integrering med Azure DDoS Protection Basic

Front dörren skyddas av Azure DDoS Protection Basic. Den integreras i plattformen för front dörren som standard och utan extra kostnad. Full skala och kapacitet för front dörrens globalt distribuerade nätverk ger skydd mot vanliga attacker mot nätverks nivå genom Always trafik övervakning och i real tid. Det grundläggande DDoS-skyddet skyddar också mot de vanligaste, ofta förekommande, lager 7 DNS-frågor och lager 3-och 4-mätkolvar som riktar sig mot offentliga slut punkter. Den här tjänsten har även en beprövad spårnings post för att skydda Microsofts företags-och konsument tjänster från storskaliga attacker. Mer information finns i [Azure DDoS Protection](../security/fundamentals/ddos-best-practices.md).

## <a name="protocol-blocking"></a>Blockera protokoll

Frontend-dörren accepterar bara trafik på HTTP-och HTTPS-protokollen och bearbetar endast giltiga begär Anden med ett känt `Host` huvud. Det här beteendet hjälper till att minimera vissa vanliga typer av DDoS-angrepp, till exempel vid angrepp som är spridda över ett intervall av protokoll och portar, DNS-förstärknings attacker och angrepp om TCP-förgiftning.

## <a name="capacity-absorption"></a>Kapacitets absorption

Front dörren är en storskaligt skalad, globalt distribuerad tjänst. Vi har många kunder, inklusive Microsofts egna storskaliga moln produkter, som får hundratals tusen förfrågningar varje sekund. Front dörren finns på kanten av Azures nätverk, absorberande och geografiskt isolerade stora volym attacker. Detta kan förhindra att skadlig trafik går utöver gränsen för Azure-nätverket.

## <a name="caching"></a>Cachelagring

[Frontend-funktioner för front dörren](./front-door-caching.md) kan användas för att skydda Server delar från stora trafik volymer som genereras av en attack. Cachelagrade resurser returneras från de främre dörr kant-noderna så att de inte vidarebefordras till Server delen. Jämna användnings tider för kort cache (sekunder eller minuter) på dynamiska svar kan avsevärt minska belastningen på backend-tjänster. Mer information om cachelagring av koncept och mönster finns i avsnittet [cachelagring](/azure/architecture/best-practices/caching) och [cache-undan mönster](/azure/architecture/patterns/cache-aside).

## <a name="web-application-firewall-waf"></a>Brandvägg för webbaserade program (WAF)

[Brand väggens brand vägg för webbaserade program (WAF)](../web-application-firewall/afds/afds-overview.md) kan användas för att minska antalet olika typer av attacker:

* Att använda den hanterade regel uppsättningen ger skydd mot ett antal vanliga attacker.
* Trafik från utanför en definierad geografisk region eller inom en definierad region kan blockeras eller omdirigeras till en statisk webb sida. Mer information finns i [geo-filtrering](../web-application-firewall/afds/waf-front-door-geo-filtering.md).
* IP-adresser och intervall som du identifierar som skadlig kan blockeras.
* Hastighets begränsning kan användas för att förhindra att IP-adresser anropar tjänsten för ofta.
* Du kan skapa [anpassade WAF-regler](../web-application-firewall/afds/waf-front-door-custom-rules.md) för automatisk blockering och hastighets begränsning av http-eller https-attacker som har kända signaturer.

## <a name="for-further-protection"></a>För ytterligare skydd

Om du behöver ytterligare skydd kan du aktivera [Azure DDoS Protection standard](../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) på det VNet där dina Server delar distribueras. DDoS Protection standard kunder får ytterligare fördelar, inklusive kostnads skydd, SLA-garanti och åtkomst till experter från DDoS Rapid Response-teamet för omedelbar hjälp vid ett angrepp.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du konfigurerar en [WAF-profil på front dörren](front-door-waf.md). 
- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).