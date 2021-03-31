---
title: Azure-frontend – övervakning av regel matchning för routning | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Azures frontend-dörr matchar vilken routningsprincip som ska användas för inkommande begäran
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 67940db973f494cd4a12c2f16db528e0b113d656
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91449206"
---
# <a name="how-requests-are-matched-to-a-routing-rule"></a>Hur begär Anden matchas mot en regel för routning

När du har upprättat en anslutning och slutfört en TLS-handskakning, och en begäran skickas till en front dörr miljö, är något av de första saker som front dörren att avgöra vilken specifik regel för routning som matchar begäran till och sedan utföra den definierade åtgärden i konfigurationen. I följande dokument förklaras hur front dörren avgör vilken väg konfiguration som ska användas vid bearbetning av en HTTP-begäran.

## <a name="structure-of-a-front-door-route-configuration"></a>Struktur för en väg konfiguration av en front dörr
En regel för routning av front dörr består av två huvud delar: en "vänstersida" och en "höger sida". Vi matchar den inkommande begäran till vänster i vägen medan den högra sidan definierar hur vi bearbetar begäran.

### <a name="incoming-match-left-hand-side"></a>Inkommande matchning (vänster sida)
Följande egenskaper avgör om den inkommande begäran matchar regeln för routning (eller vänster sida):

* **Http-protokoll** (http/https)
* **Värdar** (till exempel www \. -foo.com, \* . bar.com)
* **Sökvägar** (till exempel/ \* ,/users/ \* ,/file.gif)

De här egenskaperna expanderas internt så att varje kombination av protokoll/värd/sökväg är en möjlig matchnings uppsättning.

### <a name="route-data-right-hand-side"></a>Dirigera data (höger sida)
Beslutet om att bearbeta begäran beror på om cachelagring är aktiverat eller inte för den angivna vägen. Om vi inte har ett cachelagrat svar för begäran vidarebefordrar vi begäran till lämplig server del i den konfigurerade backend-poolen.

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
När du har fastställt den angivna klient dels värden och filtrerat möjliga routningsregler till bara vägarna med denna klient dels värd, filtrerar front dörren sedan routningsregler baserat på sökväg för begäran. Vi använder en liknande logik som värd för klient delen:

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
När vi har matchat till en enda regel för routning av en front dörr, måste du välja hur du vill bearbeta begäran. Om den matchade regeln för vidarebefordran har ett cachelagrat svar som är tillgängligt, kommer samma klient att hanteras tillbaka till klienten. Annars är nästa sak som utvärderas om du har konfigurerat [URL-omskrivning (anpassad vidarebefordrings Sök väg)](front-door-url-rewrite.md) för den matchade regeln för routning eller inte. Om ingen anpassad vidarebefordrings Sök väg har definierats vidarebefordras begäran till rätt server del i den konfigurerade backend-poolen som den är. Annars uppdateras sökvägen för begäran enligt den [anpassade vidarebefordrings Sök vägen](front-door-url-rewrite.md) som definieras och vidarebefordras sedan till Server delen.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
