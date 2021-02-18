---
title: Konfigurera matchnings villkor för Azure front dörr standard/Premium
description: Den här artikeln innehåller en lista över olika matchnings villkor som är tillgängliga med Azures standard-/Premium regel uppsättning.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4c65d0e7f80fab59ca7df4849df7117d482352c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100516"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Regel uppsättning matchnings villkor för Azure front dörr standard/Premium (för hands version)

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

Den här självstudien visar hur du skapar en regel uppsättning med din första regel uppsättning i Azure Portal. I Azures front dörr standard/Premium [regel uppsättning](concept-rule-set.md)består en regel av noll eller flera matchnings villkor och en åtgärd. Den här artikeln innehåller detaljerade beskrivningar av matchnings villkoren som du kan använda i Azures standard-/Premium regel uppsättning.

Den första delen av en regel är ett matchnings villkor eller en uppsättning matchnings villkor. En regel kan bestå av upp till 10 matchnings villkor. Ett matchnings villkor identifierar vissa typer av begär Anden för vilka definierade åtgärder utförs. Om du använder flera matchnings villkor grupperas matchnings villkoren tillsammans med hjälp av och logik. För alla matchnings villkor som stöder flera värden (anges som "blankstegsavgränsad") antas operatorn "OR".

Du kan till exempel använda ett matchnings villkor för att:

* Filtrera förfrågningar baserat på en speciell IP-adress, land eller region.
* Filtrera förfrågningar efter rubrik information.
* Filtrera begär Anden från mobila enheter eller Skriv bords enheter.
* Filtrera begär Anden från begär fil namn och fil namns tillägg.
* Filtrera förfrågningar från begärd URL, protokoll, sökväg, frågesträng, post argument osv.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Följande matchnings villkor är tillgängliga för användning i Azures standard-/Premium regel uppsättning:

## <a name="device-type"></a>Enhetstyp

Identifierar förfrågningar som görs från en mobil enhet eller en stationär enhet.  

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|----------------
Lika med, inte lika med | Mobil, stationär

## <a name="post-argument"></a>Publicera argument

Identifierar begär Anden baserat på argument som definierats för metoden POST-begäran som används i begäran.

#### <a name="required-fields"></a>Obligatoriska fält

Argumentnamn | Operator | Argumentvärde | Skift läges omvandling
--------------|----------|----------------|---------------
Sträng | [Operator lista](#operator-list) | Sträng, heltal | Gemener, versaler

## <a name="query-string"></a>Frågesträng

Identifierar begär Anden som innehåller en speciell frågesträngparametern. Den här parametern har angetts till ett värde som matchar ett angivet mönster. Parametrar för frågesträng (till exempel **parameter = värde**) i URL: en för begäran avgör om det här villkoret är uppfyllt. Detta matchnings villkor identifierar en frågesträngparametern efter dess namn och accepterar ett eller flera värden för parametervärdet.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Frågesträng | Skift läges omvandling
---------|--------------|---------------
[Operator lista](#operator-list) | Sträng, heltal | Gemener, versaler

## <a name="remote-address"></a>Fjärradress

Identifierar förfrågningar baserat på beställarens plats eller IP-adress.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|-----------------
Geo-matchning | Landskod
IP-matchning | IP-adress (blankstegsavgränsad)
Ingen geo-matchning | Landskod
Inte IP-matchning | IP-adress (blankstegsavgränsad)

#### <a name="key-information"></a>Viktig information

* Använd CIDR-notering.
* För flera IP-adresser och IP-adressblock körs logiken.
    * **IPv4-exempel**: om du lägger till två IP-adresser *1.2.3.4* och *10.20.30.40* matchas villkoret om några begär Anden kommer från adressen 1.2.3.4 eller 10.20.30.40.
    * **IPv6-exempel**: om du lägger till två IP-adresser *1:2:3:4:5:6:7:8* och *10:20:30:40:50:60:70:80* matchas villkoret om några begär Anden som kommer från adress 1:2:3:4:5:6:7:8 eller 10:20:30:40:50:60:70:80.
* Syntaxen för ett IP-adressblock är bas-IP-adressen följt av ett snedstreck och prefixets storlek. Exempel:
    * **IPv4-exempel**: *5.5.5.64/26* matchar alla begär Anden som kommer från adresser 5.5.5.64 via 5.5.5.127.
    * **IPv6-exempel**: *1:2:3:/48* matchar alla begär Anden som kommer från adresser 1:2:3:0:0:0:0:0 till 1:2:3: FFFF: FFFF: FFFF: FFFF: FFFF.

## <a name="request-body"></a>Begärandetext

Identifierar förfrågningar baserat på en speciell text som visas i bröd texten i begäran.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Begärandetext | Skift läges omvandling
---------|--------------|---------------
[Operator lista](#operator-list) | Sträng, heltal | Gemener, versaler

## <a name="request-header"></a>Begärandehuvud

Identifierar begär Anden som använder ett särskilt huvud i begäran.

#### <a name="required-fields"></a>Obligatoriska fält

Huvudnamn | Operator | Huvudvärde | Skift läges omvandling
------------|----------|--------------|---------------
Sträng | [Operator lista](#operator-list) | Sträng, heltal | Gemener, versaler

## <a name="request-method"></a>Metod för begäran

Identifierar begär Anden som använder den angivna förfrågnings metoden.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|----------------
Lika med, inte lika med | HÄMTA, POSTA, PLACERA, TA BORT, HUVUD, ALTERNATIV, SPÅRA

#### <a name="key-information"></a>Viktig information

Endast metoden GET Request kan generera cachelagrat innehåll i Azures frontend-dörr. Alla andra metoder för begäran är proxy via nätverket.

## <a name="request-protocol"></a>Protokoll för begäran

Identifierar begär Anden som använder det angivna protokoll som används.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|----------------
Lika med, inte lika med | HTTP, HTTPS

## <a name="request-url"></a>Begärans-URL

Identifierar begär Anden som matchar angiven URL.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Begärans-URL | Skift läges omvandling
---------|-------------|---------------
[Operator lista](#operator-list) | Sträng, heltal | Gemener, versaler

#### <a name="key-information"></a>Viktig information

När du använder det här regel villkoret ska du se till att inkludera protokoll information. Exempel: *https://www . \<yourdomain\> . com*.

## <a name="request-file-extension"></a>Begär fil tillägg

Identifierar begär Anden som innehåller det angivna fil namns tillägget i fil namnet i den begärda URL: en.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Anknytning | Skift läges omvandling
---------|-----------|---------------
[Operator lista](#operator-list)  | Sträng, heltal | Gemener, versaler

#### <a name="key-information"></a>Viktig information

Inkludera inte en inledande period för tillägg. Använd till exempel *HTML* i stället för *. html*.

## <a name="request-file-name"></a>Fil namn för begäran

Identifierar begär Anden som innehåller det angivna fil namnet i begärd URL.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Filnamn | Skift läges omvandling
---------|-----------|---------------
[Operator lista](#operator-list)| Sträng, heltal | Gemener, versaler

## <a name="request-path"></a>Sökväg för begäran

Identifierar begär Anden som innehåller den angivna sökvägen i begär ande-URL: en.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värde | Skift läges omvandling
---------|-------|---------------
[Operator lista](#operator-list) | Sträng, heltal | Gemener, versaler

## <a name="operator-list"></a><a name = "operator-list"></a>Operator lista

Följande operatorer är giltiga för regler som accepterar värden från standard operator listan:

* Valfri
* Lika med
* Innehåller
* Börjar med
* Slutar med
* Mindre än
* Mindre än eller lika med
* Större än
* Större än eller lika med
* Inte alla
* Innehåller inte
* Börjar inte med
* Slutar inte med
* Inte mindre än
* Inte mindre än eller lika med
* Inte större än
* Inte större än eller lika med
* Reguljärt uttryck

För numeriska operatorer som *mindre än* och *större än eller lika med*, baseras jämförelsen på längd. Värdet i matchnings villkoret ska vara ett heltal som är lika med den längd som du vill jämföra.

## <a name="regular-expression"></a>Reguljärt uttryck

Regex stöder inte följande åtgärder:

* Referens och inhämtning av under uttryck
* Godtyckliga kontroller med noll bredd
* Subrutin-referenser och rekursiva mönster
* Villkors mönster
* Bakspårning-verb
* Single-byte-direktivet \c
* Direktivet \Rs rad träff
* \K början av matchnings reset-direktivet
* Bild texter och inbäddad kod
* Atomiska grupperingar och possessive-kvantifierare

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [regel uppsättning](concept-rule-set.md).
* Lär dig hur du [konfigurerar din första regel uppsättning](how-to-configure-rule-set.md).
* Läs mer om [regel uppsättnings åtgärder](concept-rule-set-actions.md).
