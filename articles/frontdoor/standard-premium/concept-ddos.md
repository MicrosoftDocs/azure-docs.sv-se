---
title: 'Azure-front dörr: DDoS-skydd'
description: Den här sidan innehåller information om hur Azures främre dörr standard/Premium hjälper till att skydda mot DDoS-attacker
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: jodowns
ms.openlocfilehash: 9c67944717888439c0d6bd84e1615f51dd91fcac
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100553"
---
# <a name="ddos-protection-on-azure-front-door-standardpremium-preview"></a>DDoS-skydd på Azures främre dörr standard/Premium (för hands version)

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

Azures front dörr har flera funktioner och egenskaper som kan hjälpa till att förhindra DDoS-attacker (distributed denial of Service). Dessa funktioner kan förhindra att angripare når ditt program och påverkar programmets tillgänglighet och prestanda.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="integration-with-azure-ddos-protection-basic"></a>Integrering med Azure DDoS Protection Basic

Front dörren skyddas av Azure DDoS Protection Basic. Funktionen integreras i plattformen för front dörren som standard och utan extra kostnad. Full skala och kapacitet för front dörrens globalt distribuerade nätverk ger skydd mot vanliga attacker mot nätverks nivå genom Always trafik övervakning och i real tid. Det grundläggande DDoS-skyddet skyddar också mot de vanligaste, ofta förekommande, lager 7 DNS-frågor och lager 3-och 4-mätkolvar som riktar sig mot offentliga slut punkter. Den här tjänsten har även en beprövad spårnings post för att skydda Microsofts företags-och konsument tjänster från storskaliga attacker. Mer information finns i [Azure DDoS Protection](../../security/fundamentals/ddos-best-practices.md).

## <a name="protocol-blocking"></a>Blockera protokoll

Frontend-dörren accepterar bara trafik på HTTP-och HTTPS-protokollen och bearbetar endast giltiga begär Anden med ett känt `Host` huvud. Det här beteendet hjälper till att minimera vissa vanliga typer av DDoS-angrepp, inklusive-lösningar som kan spridas över ett intervall av protokoll och portar, DNS-förstärknings attacker och angrepp om TCP-förgiftning.

## <a name="capacity-absorption"></a>Kapacitets absorption

Front dörren är en storskaligt skalad, globalt distribuerad tjänst. Vi har många kunder, inklusive Microsofts egna storskaliga moln produkter, som får hundratals tusen förfrågningar varje sekund. Front dörren finns på kanten av Azures nätverk, absorberande och geografiskt isolerade stora volym attacker. Detta kan förhindra att skadlig trafik går utöver gränsen för Azure-nätverket.

## <a name="caching"></a>Cachelagring

[Frontend-funktioner för front dörren](concept-caching.md) kan användas för att skydda Server delar från stora trafik volymer som genereras av en attack. Cachelagrade resurser returneras från de främre dörr kant-noderna så att de inte vidarebefordras till Server delen. Jämna användnings tider för kort cache (sekunder eller minuter) på dynamiska svar kan avsevärt minska belastningen på backend-tjänster. Mer information om cachelagring av koncept och mönster finns i avsnittet [cachelagring](/azure/architecture/best-practices/caching) och [cache-undan mönster](/azure/architecture/patterns/cache-aside).

## <a name="web-application-firewall-waf"></a>Brandvägg för webbaserade program (WAF)

[Brand väggens brand vägg för webbaserade program (WAF)](../../web-application-firewall/afds/afds-overview.md) kan användas för att minska antalet olika typer av attacker:

* Att använda den hanterade regel uppsättningen ger skydd mot många vanliga attacker.
* Trafik från utanför en definierad geografisk region eller inom en definierad region kan blockeras eller omdirigeras till en statisk webb sida. Mer information finns i [geo-filtrering](../../web-application-firewall/afds/waf-front-door-geo-filtering.md).
* IP-adresser och intervall som du identifierar som skadlig kan blockeras.
* Hastighets begränsning kan användas för att förhindra att IP-adresser anropar tjänsten för ofta.
* Du kan skapa [anpassade WAF-regler](../../web-application-firewall/afds/waf-front-door-custom-rules.md) för automatisk blockering och hastighets begränsning av http-eller https-attacker som har kända signaturer.

## <a name="for-further-protection"></a>För ytterligare skydd

Om du behöver ytterligare skydd kan du aktivera [Azure DDoS Protection standard](../../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) på det VNet där dina Server delar distribueras. DDoS Protection standard kunder får fler fördelar, inklusive:

* Kostnads skydd
* SLA-garanti
* Få till gång till experter från DDoS Rapid Response-teamet för omedelbar hjälp vid ett angrepp.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar en front dörrs standard/Premium](create-front-door-portal.md).
