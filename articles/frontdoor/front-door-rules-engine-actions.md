---
title: Motor åtgärder för Azures frontend-dörr
description: Den här artikeln innehåller en lista över de olika åtgärder som du kan utföra med Azures motor för front dörrs regler.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: ff61af192471bcfc9bdb9f1ce3970d5c22f39579
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91569779"
---
# <a name="azure-front-door-rules-engine-actions"></a>Åtgärder för Azure Front Door-regelmotorn

I [AFD](front-door-rules-engine.md)-regel motor består en regel av noll eller flera matchnings villkor och åtgärder. Den här artikeln innehåller detaljerade beskrivningar av de åtgärder som du kan använda i AFD-regel motorn.

En åtgärd definierar det beteende som används för den typ av begäran som ett matchnings villkor eller en uppsättning matchnings villkor identifierar. I AFD-regel motor kan en regel innehålla upp till fem åtgärder. Endast en som kan vara en åsidosättning av Route-konfiguration (vidarebefordra eller omdirigera).

Följande åtgärder är tillgängliga för användning i Azures motor för front dörrs regler.  

## <a name="modify-request-header"></a>Ändra rubrik för begäran

Använd den här åtgärden för att ändra rubriker som finns i begär Anden som skickas till ditt ursprung.

### <a name="required-fields"></a>Obligatoriska fält

Action | Namn på HTTP-huvud | Värde
-------|------------------|------
Lägg till | När det här alternativet markeras och regeln matchar, läggs rubriken som anges i **huvud namn** till i begäran med det angivna värdet. Om rubriken redan finns läggs värdet till i det befintliga värdet. | Sträng
Skriv över | När det här alternativet är markerat och regeln matchar, läggs rubriken som anges i **huvud namn** till i begäran med det angivna värdet. Om rubriken redan finns skriver det angivna värdet över det befintliga värdet. | Sträng
Ta bort | När det här alternativet väljs med matchande regler och den rubrik som anges i regeln finns, tas rubriken bort från begäran. | Sträng

## <a name="modify-response-header"></a>Ändra svars huvud

Använd den här åtgärden för att ändra huvuden som finns i svar som returneras till dina klienter.

### <a name="required-fields"></a>Obligatoriska fält

Action | Namn på HTTP-huvud | Värde
-------|------------------|------
Lägg till | När det här alternativet markeras och regeln matchar, läggs rubriken som anges i **huvud namn** till i svaret med hjälp av det angivna **värdet**. Om rubriken redan finns läggs **värdet** till i det befintliga värdet. | Sträng
Skriv över | När det här alternativet är markerat och regeln matchar, läggs rubriken som anges i **huvud namn** till i svaret med hjälp av det angivna **värdet**. Om rubriken redan finns skriver **värdet** över det befintliga värdet. | Sträng
Ta bort | När det här alternativet markeras och regeln matchar den rubrik som anges i regeln, tas rubriken bort från svaret. | Sträng

## <a name="route-configuration-overrides"></a>Åsidosättningar för routningskonfiguration 

### <a name="route-type-redirect"></a>Typ av väg: omdirigera

Använd den här åtgärden för att omdirigera klienter till en ny URL. 

#### <a name="required-fields"></a>Obligatoriska fält

Fält | Beskrivning 
------|------------
Omdirigera typ | Välj den svarstyp som ska returneras till begär ande: hittas (302), flyttad (301), tillfällig omdirigering (307) och permanent omdirigering (308).
Omdirigera protokoll | Matcha begäran, HTTP, HTTPS.
Målvärd | Välj värd namnet som du vill att begäran ska omdirigeras till. Lämna tomt om du vill bevara den inkommande värden.
Målsökväg | Definiera sökvägen som ska användas i omdirigeringen. Lämna tomt om du vill behålla den inkommande sökvägen.  
Frågesträng | Definiera frågesträngen som används i omdirigeringen. Lämna tomt om du vill behålla inkommande frågesträng. 
Målcachen | Definiera det fragment som ska användas i omdirigeringen. Lämna tomt om du vill behålla det inkommande fragmentet. 


### <a name="route-type-forward"></a>Typ av väg: vidarebefordra

Använd den här åtgärden för att vidarebefordra klienter till en ny URL. Den här åtgärden innehåller även under åtgärder för URL-omskrivning och cachelagring. 

Fält | Beskrivning 
------|------------
Serverdelspool | Välj den backend-pool som ska åsidosättas och betjänar begär Anden. då visas även alla förkonfigurerade backend-pooler i din profil för din front dörr. 
Protokoll för vidarebefordran | Matcha begäran, HTTP, HTTPS.
URL-omskrivning | Använd den här åtgärden för att skriva om sökvägen för en begäran som är en väg till ditt ursprung. Om aktive rad, se följande ytterligare fält som krävs
Cachelagring | Aktiverad, inaktiverad. Se följande ytterligare fält som krävs om det är aktiverat. 

#### <a name="url-rewrite"></a>URL-omskrivning

Använd den här inställningen om du vill konfigurera en valfri **anpassad vidarebefordrings Sök väg** som ska användas för att skapa begäran att vidarebefordra till Server delen.

Fält | Beskrivning 
------|------------
Anpassad vidarebefordrings Sök väg | Definiera sökvägen för att vidarebefordra begär anden till. 

#### <a name="caching"></a>Cachelagring

Använd de här inställningarna för att styra hur filer cachelagras för förfrågningar som innehåller frågesträngar. Om ditt innehåll ska cachelagras baserat på alla parametrar eller på valda parametrar. Du kan använda ytterligare inställningar för att skriva över TTL-värdet (Time to Live) för att styra hur lång tid innehållet förblir i cacheminnet. Om du vill framtvinga cachelagring som åtgärd anger du fältet cachelagring till "Enabled". När du framtvingar cachelagring visas följande alternativ: 

Cache-beteende |  Beskrivning              
---------------|----------------
Ignorera frågesträngar | När till gången har cachelagrats ignorerar alla begär ande förfrågningar frågesträngarna tills den cachelagrade till gången upphör att gälla.
Cachelagra varje unik URL | Varje begäran med en unik URL, inklusive frågesträngen, behandlas som en unik till gång med sin egen cache.
Ignorera angivna frågesträngar | Fråge strängar för URL: en som anges i inställningen frågeparametrar ignoreras för cachelagring.
Inkludera angivna frågesträngar | Fråge strängar för URL: en som anges i inställningen frågeparametrar används för cachelagring.

Ytterligare fält |  Beskrivning 
------------------|---------------
Dynamisk komprimering | Front dörren kan dynamiskt komprimera innehåll på gränsen, vilket resulterar i ett mindre och snabbare svar.
Frågeparametrar | En kommaavgränsad lista över tillåtna (eller otillåtna) parametrar som ska användas som grund för cachelagring.
Varaktighet för cache | Förfallo tid för cache i dagar, timmar, minuter, sekunder. Alla värden måste vara int. 

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du konfigurerar din första [regel motor](front-door-tutorial-rules-engine.md). 
- Lär dig mer om [regel motorns matchnings villkor](front-door-rules-engine-match-conditions.md)
- Läs mer om [Azures motor för front dörr](front-door-rules-engine.md)
