---
title: Åtgärder i standard regel motorn för Azure CDN | Microsoft Docs
description: Referens dokumentation för åtgärder i standard regel motorn för Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 051737a9f5e0d4092cda26a3f7ce3df1d7f535ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87760132"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Åtgärder i standard regel motorn för Azure CDN

I [standard regel motorn](cdn-standard-rules-engine.md) för Azure Content Delivery Network (Azure CDN) består en regel av ett eller flera matchnings villkor och en åtgärd. Den här artikeln innehåller detaljerade beskrivningar av de åtgärder som du kan använda i standard regel motorn för Azure CDN.

Den andra delen av en regel är en åtgärd. En åtgärd definierar det beteende som används för den typ av begäran som ett matchnings villkor eller en uppsättning matchnings villkor identifierar.

## <a name="actions"></a>Åtgärder

Följande åtgärder är tillgängliga för användning i standard regel motorn för Azure CDN. 

### <a name="cache-expiration"></a>Förfallo tid för cache

Använd den här åtgärden om du vill skriva över TTL-värdet (Time to Live) för slut punkten för begär Anden som reglerna matchar villkoren anger.

#### <a name="required-fields"></a>Obligatoriska fält

Cache-beteende |  Beskrivning              
---------------|----------------
Kringgå cacheminne | När det här alternativet är markerat och regeln matchar cachelagras inte innehållet.
Åsidosätt | När det här alternativet är markerat och regeln matchar, skrivs det TTL-värde som returneras från ditt ursprung över med det värde som anges i åtgärden. Detta beteende gäller endast om svaret är cacheable. För Cache-Control-svarshuvuden med värden "no-cache", "privat", "No-Store", är åtgärden inte tillämplig.
Ange om det saknas | När det här alternativet är markerat och regeln matchar, om inget TTL-värde returnerades från ditt ursprung, anger regeln TTL till det värde som angavs i åtgärden. Detta beteende gäller endast om svaret är cacheable. För Cache-Control-svarshuvuden med värden "no-cache", "privat", "No-Store", är åtgärden inte tillämplig.

#### <a name="additional-fields"></a>Ytterligare fält

Dagar | Tider | Minuter | Sekunder
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>Frågesträng för cache-nyckel

Använd den här åtgärden för att ändra cache-nyckeln baserat på frågesträngar.

#### <a name="required-fields"></a>Obligatoriska fält

Beteende | Beskrivning
---------|------------
Inkludera | När det här alternativet är markerat och regeln matchar, inkluderas frågesträngarna som anges i parametrarna när cache-nyckeln genereras. 
Cachelagra varje unik URL | När det här alternativet är markerat och regeln matchar, har varje unik URL en egen cache-nyckel. 
Exclude | När det här alternativet är markerat och regeln matchar, undantas frågesträngarna som anges i parametrarna när cache-nyckeln genereras.
Ignorera frågesträngar | När det här alternativet är markerat och regeln matchar, beaktas inte frågesträngar när cache-nyckeln genereras. 

### <a name="modify-request-header"></a>Ändra rubrik för begäran

Använd den här åtgärden för att ändra rubriker som finns i begär Anden som skickas till ditt ursprung.

#### <a name="required-fields"></a>Obligatoriska fält

Action | Namn på HTTP-huvud | Värde
-------|------------------|------
Lägg till | När det här alternativet är markerat och regeln matchar, läggs rubriken som anges i **huvud namn** till i begäran med det angivna värdet. Om rubriken redan finns läggs värdet till i det befintliga värdet. | Sträng
Skriv över | När det här alternativet är markerat och regeln matchar, läggs rubriken som anges i **huvud namn** till i begäran med det angivna värdet. Om rubriken redan finns skriver det angivna värdet över det befintliga värdet. | Sträng
Ta bort | När det här alternativet är markerat, regeln matchar och rubriken som anges i regeln finns, raderas rubriken från begäran. | Sträng

### <a name="modify-response-header"></a>Ändra svars huvud

Använd den här åtgärden för att ändra huvuden som finns i svar som returneras till dina klienter.

#### <a name="required-fields"></a>Obligatoriska fält

Action | Namn på HTTP-huvud | Värde
-------|------------------|------
Lägg till | När det här alternativet är markerat och regeln matchar, läggs rubriken som anges i **huvud namn** till i svaret med hjälp av det angivna **värdet**. Om rubriken redan finns läggs **värdet** till i det befintliga värdet. | Sträng
Skriv över | När det här alternativet är markerat och regeln matchar, läggs rubriken som anges i **huvud namn** till i svaret med hjälp av det angivna **värdet**. Om rubriken redan finns skriver **värdet** över det befintliga värdet. | Sträng
Ta bort | När det här alternativet är markerat, regeln matchar och rubriken som anges i regeln är närvarande, tas rubriken bort från svaret. | Sträng

### <a name="url-redirect"></a>URL-omdirigering

Använd den här åtgärden för att omdirigera klienter till en ny URL. 

#### <a name="required-fields"></a>Obligatoriska fält

Fält | Beskrivning 
------|------------
Typ | Välj den svarstyp som ska returneras till begär ande: hittas (302), flyttad (301), tillfällig omdirigering (307) och permanent omdirigering (308).
Protokoll | Matcha begäran, HTTP, HTTPS.
Värdnamn | Välj värd namnet som du vill att begäran ska omdirigeras till. Lämna tomt om du vill bevara den inkommande värden.
Sökväg | Definiera sökvägen som ska användas i omdirigeringen. Lämna tomt om du vill behålla den inkommande sökvägen.  
Frågesträng | Definiera frågesträngen som används i omdirigeringen. Lämna tomt om du vill behålla inkommande frågesträng. 
Fragment | Definiera det fragment som ska användas i omdirigeringen. Lämna tomt om du vill behålla det inkommande fragmentet. 

Vi rekommenderar starkt att du använder en absolut URL. Om du använder en relativ URL kan du omdirigera Azure CDN URL: er till en ogiltig sökväg. 

### <a name="url-rewrite"></a>URL-omskrivning

Använd den här åtgärden för att skriva om sökvägen för en begäran som är en väg till ditt ursprung.

#### <a name="required-fields"></a>Obligatoriska fält

Fält | Beskrivning 
------|------------
Käll mönster | Definiera käll mönstret i URL-sökvägen som ska ersättas. För närvarande använder käll mönstret en prefix-baserad matchning. Använd ett snedstreck ( **/** ) som käll mönster värde för att matcha alla URL-sökvägar.
Mål | Definiera den mål Sök väg som ska användas vid omskrivning. Mål Sök vägen skriver över käll mönstret.
Bevara omatchad sökväg | Om värdet är **Ja**, läggs den återstående sökvägen efter käll mönstret till i den nya mål Sök vägen. 

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure CDN](cdn-overview.md)
- [Referens för standardregelmotor](cdn-standard-rules-engine-reference.md)
- [Matchnings villkor i standard regel motorn](cdn-standard-rules-engine-match-conditions.md)
- [Kräv HTTPS med hjälp av standardregelmotorn](cdn-standard-rules-engine.md)
