---
title: Vad är Azures främre dörr standard/Premium-väg?
description: Den här artikeln hjälper dig att förstå hur Azure-dörrens standard/Premium matchar vilken routningsprincip som ska användas för inkommande begäran.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: db026c4903aa30a0a4c8154af8ad6eeb4b72b706
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100520"
---
# <a name="what-is-azure-front-door-standardpremium-preview-route"></a>Vad är Azure front dörr standard/Premium (för hands version)?

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

Azures främre dörr standard/Premium-väg definierar hur trafiken hanteras när inkommande begäran anländer till Azures frontend-miljö. Med hjälp av väg inställningarna definieras en association mellan en domän och en underlagets grupp. Genom att aktivera avancerade funktioner som mönster för maskin vara, regel uppsättning, kan mer detaljerad kontroll över trafiken uppnås.

En konfiguration av en front dörrs standard-/Premium-routning består av två huvud delar: "vänster sida" och "höger sida". Vi matchar den inkommande begäran till vänster i vägen och den högra sidan definierar hur vi bearbetar begäran.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="incoming-match-left-hand-side"></a>Inkommande matchning (vänster sida)

Följande egenskaper avgör om den inkommande begäran matchar regeln för routning (eller vänster sida):

* **Http-protokoll** (http/https)
* **Värdar** (till exempel www \. -foo.com, \* . bar.com)
* **Sökvägar** (till exempel/ \* ,/users/ \* ,/file.gif)

De här egenskaperna expanderas internt så att varje kombination av protokoll/värd/sökväg är en möjlig matchnings uppsättning.

### <a name="route-data-right-hand-side"></a>Dirigera data (höger sida)

Beslutet om att bearbeta begäran beror på om cachelagring är aktiverat eller inte för vägen. Om ett cachelagrat svar inte är tillgängligt vidarebefordras begäran till rätt server del.

## <a name="route-matching"></a>Matchning av väg

I det här avsnittet kommer vi att fokusera på hur vi matchar en specifik Dirigerings regel för front dörren. Det grundläggande konceptet är att vi alltid stämmer överens med den **mest exakta matchningen först** som bara tittar på "vänster sida".  Vi matchar först baserat på HTTP-protokoll, sedan klient dels värd, och sedan sökvägen.

### <a name="frontend-host-matching"></a>Matchning av frontend-värd

Vid matchning av klient dels värdar använder vi den logik som definieras nedan:

1. Sök efter routning med en exakt matchning på värden.
2. Om inga exakta klient dels värdar matchar avvisar du begäran och skickar ett fel av en 400-Felaktig begäran.

För att förklara den här processen ytterligare, ska vi titta på ett exempel på en konfiguration av front dörrs vägar (endast vänster sida):

| Routingregeln | Frontend-värdar | Sökväg |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /Users\* |
| C | www \. -fabrikam.com, foo.Adventure-Works.com  | /\*, /images/\* |

Om följande inkommande begär Anden skickas till frontend-dörren, matchas de mot följande regler för routning från ovan:

| Inkommande klient dels värd | Matchade routningsregler |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www- \. fabrikam.com | C |
| images.fabrikam.com | Fel 400: felaktig begäran |
| foo.adventure-works.com | C |
| contoso.com | Fel 400: felaktig begäran |
| www- \. Adventure-Works.com | Fel 400: felaktig begäran |
| www- \. northwindtraders.com | Fel 400: felaktig begäran |

### <a name="path-matching"></a>Sök vägs matchning

När Azure-frontend-dörren är standard/Premium fastställs den angivna klient dels värden och filtrering av möjliga routningsregler till bara vägarna med den klient dels värden. Front dörren filtrerar sedan routningsregler utifrån sökvägen för begäran. Vi använder en liknande logik som värd för klient delen:

1. Sök efter en regel för routning med en exakt matchning på sökvägen
2. Om det inte finns några exakta matchnings sökvägar kan du söka efter routningsregler med en sökväg med jokertecken som matchar
3. Om inga regler för routning hittas med en matchande sökväg avvisar du begäran och returnerar en 400: felaktig begäran HTTP-svar.

>[!NOTE]
> Alla sökvägar utan jokertecken anses vara exakta matchnings vägar. Även om sökvägen slutar med ett snedstreck betraktas den fortfarande som exakt matchning.

Vi vill förklara ytterligare genom att titta på en annan uppsättning exempel:

| Routingregeln | Klient dels värd    | Sökväg     |
|-------|---------|----------|
| A     | www- \. contoso.com | /        |
| B     | www- \. contoso.com | /\*      |
| C     | www- \. contoso.com | /ab      |
| D     | www- \. contoso.com | /abc     |
| E     | www- \. contoso.com | Pia    |
| F     | www- \. contoso.com | Pia\*  |
| G     | www- \. contoso.com | /abc/def |
| H     | www- \. contoso.com | /Path   |

Med den här konfigurationen kommer följande exempel som matchar tabellen att resultera i:

| Inkommande begäran    | Matchad väg |
|---------------------|---------------|
| www- \. contoso.com/            | A             |
| www- \. contoso.com/a           | B             |
| www- \. contoso.com/AB          | C             |
| www- \. contoso.com/ABC         | D             |
| www- \. contoso.com/abzzz       | B             |
| www- \. contoso.com/ABC/        | E             |
| www- \. contoso.com/ABC/d       | F             |
| www- \. contoso.com/ABC/DEF     | G             |
| www- \. contoso.com/ABC/defzzz  | F             |
| www- \. contoso.com/ABC/DEF/GHI | F             |
| www- \. contoso.com/Path        | B             |
| www- \. contoso.com/Path/       | H             |
| www- \. contoso.com/Path/ZZZ    | B             |

>[!WARNING]
> </br> Om det inte finns några regler för routning av en klient dels värd med en catch-all väg Sök väg ( `/*` ), kommer det inte att finnas någon matchning till någon routningsprincip.
>
> Exempel på konfiguration:
>
> | Väg | Värd             | Sökväg    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /API\* |
>
> Matchande tabell:
>
> | Inkommande begäran       | Matchad väg |
> |------------------------|---------------|
> | profile.domain.com/other | Inga. Fel 400: felaktig begäran |

### <a name="routing-decision"></a>Dirigerings beslut

När Azures frontend-dörr standard/Premium har matchats med en enda regel för routning måste du välja hur begäran ska bearbetas. Om Azure frontend-dörren standard/Premium har ett cachelagrat svar som är tillgängligt för den matchade regeln, skickas begäran tillbaka till klienten. Nästa gång Azures främre dörr standard/Premium utvärderar är huruvida du har en regel uppsättning för regeln för matchad routning. Om ingen regel uppsättning har definierats vidarebefordras begäran till backend-poolen som den är. Annars körs regel uppsättningen i samma ordning som de har kon figurer ATS.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar en front dörrs standard/Premium](create-front-door-portal.md).
