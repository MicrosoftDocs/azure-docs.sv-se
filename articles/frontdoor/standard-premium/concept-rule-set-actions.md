---
title: Konfigurera åtgärder för regel uppsättning för Azure-front dörren standard/Premium
description: Den här artikeln innehåller en lista över de olika åtgärder som du kan utföra med Azures regel uppsättning för front dörren.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c9995df0f292c5e528156a3280df5484db017fca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100517"
---
# <a name="azure-front-door-standardpremium-rule-set-actions"></a>Regel uppsättnings åtgärder för Azures främre dörr standard/Premium

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

En [regel uppsättning](concept-rule-set.md) för Azures frontend-dörr består av regler med en kombination av matchnings villkor och åtgärder. Den här artikeln innehåller en detaljerad beskrivning av de åtgärder som du kan använda i en regel uppsättning. Åtgärden definierar det beteende som tillämpas på en typ av begäran som ett matchnings villkor identifierar. I en regel uppsättning för Azures front dörr kan en regel innehålla upp till fem åtgärder. Server variabeln stöds för alla åtgärder.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Följande åtgärder är tillgängliga för användning i Azures front dörrs regel uppsättning.  

## <a name="cache-expiration"></a>Förfallo tid för cache

Använd den här åtgärden om du vill skriva över TTL-värdet (Time to Live) för slut punkten för begär Anden som reglerna matchar villkoren anger.

### <a name="required-fields"></a>Obligatoriska fält

Följande beskrivning gäller när du väljer dessa cache-beteenden och regeln matchar:

Cache-beteende |  Beskrivning              
---------------|----------------
Kringgå cacheminne | Innehållet är inte cachelagrat.
Åsidosätt | Det TTL-värde som returneras från ditt ursprung skrivs över med det värde som anges i åtgärden. Detta beteende gäller endast om svaret är cacheable. För Cache-Control-svarshuvuden med värdena "no-cache", "privat", "No-Store", är åtgärden inte tillämplig.
Ange om det saknas | Om inget TTL-värde returneras från ditt ursprung anger regeln TTL-värdet till det värde som anges i åtgärden. Detta beteende gäller endast om svaret är cacheable. För Cache-Control-svarshuvuden med värdena "no-cache", "privat", "No-Store", är åtgärden inte tillämplig.

### <a name="additional-fields"></a>Ytterligare fält

Dagar | Tider | Minuter | Sekunder
-----|-------|---------|--------
Int | Int | Int | Int 

## <a name="cache-key-query-string"></a>Frågesträng för cache-nyckel

Använd den här åtgärden för att ändra cache-nyckeln baserat på frågesträngar.

### <a name="required-fields"></a>Obligatoriska fält

Följande beskrivning gäller när du väljer dessa beteenden och regeln matchar:

Beteende | Beskrivning
---------|------------
Inkludera | Frågesträngar som anges i parametrarna kommer att tas med när cache-nyckeln genereras. 
Cachelagra varje unik URL | Varje unik URL har sin egen cache-nyckel. 
Exclude | Frågesträngarna som anges i parametrarna undantas när cache-nyckeln genereras.
Ignorera frågesträngar | Frågesträngar beaktas inte när cache-nyckeln genereras. 

## <a name="modify-request-header"></a>Ändra rubrik för begäran

Använd den här åtgärden för att ändra rubriker som finns i begär Anden som skickas till ditt ursprung.

### <a name="required-fields"></a>Obligatoriska fält

Följande beskrivning gäller när du väljer dessa åtgärder och regeln matchar:

Action | Namn på HTTP-huvud | Värde
-------|------------------|------
Lägg till | Rubriken som anges i **huvud namn** läggs till i begäran med det angivna värdet. Om rubriken redan finns läggs värdet till i det befintliga värdet. | Sträng
Skriv över | Rubriken som anges i **huvud namn** läggs till i begäran med det angivna värdet. Om rubriken redan finns skriver det angivna värdet över det befintliga värdet. | Sträng
Ta bort | Om rubriken som anges i regeln finns, tas rubriken bort från begäran. | Sträng

## <a name="modify-response-header"></a>Ändra svars huvud

Använd den här åtgärden för att ändra huvuden som finns i svar som returneras till dina klienter.

### <a name="required-fields"></a>Obligatoriska fält

Följande beskrivning gäller när du väljer dessa åtgärder och regeln matchar:

Action | Namn på HTTP-huvud | Värde
-------|------------------|------
Lägg till | Rubriken som anges i **rubrik namnet** läggs till i svaret med det angivna **värdet**. Om rubriken redan finns läggs **värdet** till i det befintliga värdet. | Sträng
Skriv över | Rubriken som anges i **rubrik namnet** läggs till i svaret med det angivna **värdet**. Om rubriken redan finns skriver **värdet** över det befintliga värdet. | Sträng
Ta bort | Om rubriken som anges i regeln finns, tas rubriken bort från svaret. | Sträng

## <a name="url-redirect"></a>URL-omdirigering

Använd den här åtgärden för att omdirigera klienter till en ny URL. 

### <a name="required-fields"></a>Obligatoriska fält

Fält | Beskrivning 
------|------------
Omdirigera typ | Välj den svarstyp som ska returneras till begär ande: hittas (302), flyttad (301), tillfällig omdirigering (307) och permanent omdirigering (308).
Omdirigera protokoll | Matcha begäran, HTTP, HTTPS.
Målvärd | Välj värd namnet som du vill att begäran ska omdirigeras till. Lämna tomt om du vill bevara den inkommande värden.
Målsökväg | Definiera sökvägen som ska användas i omdirigeringen. Lämna tomt om du vill behålla den inkommande sökvägen.  
Frågesträng | Definiera frågesträngen som används i omdirigeringen. Lämna tomt om du vill behålla inkommande frågesträng. 
Målcachen | Definiera det fragment som ska användas i omdirigeringen. Lämna tomt om du vill behålla det inkommande fragmentet. 

## <a name="url-rewrite"></a>URL-omskrivning

Använd den här åtgärden för att skriva om sökvägen för en begäran som är en väg till ditt ursprung.

### <a name="required-fields"></a>Obligatoriska fält

Fält | Beskrivning 
------|------------
Käll mönster | Definiera käll mönstret i URL-sökvägen som ska ersättas. För närvarande använder käll mönstret en prefix-baserad matchning. Använd ett snedstreck ( **/** ) som käll mönster värde för att matcha alla URL-sökvägar.
Mål | Definiera den mål Sök väg som ska användas vid omskrivning. Mål Sök vägen skriver över käll mönstret.
Bevara omatchad sökväg | Om värdet är **Ja**, läggs den återstående sökvägen efter käll mönstret till i den nya mål Sök vägen. 

## <a name="server-variable"></a>Server variabel

### <a name="supported-variables"></a>Variabler som stöds

| Variabelnamn | Beskrivning                                                  |
| -------------------------- | :----------------------------------------------------------- |
| socket_ip                  | IP-adressen för den direkta anslutningen till Azures främre dörr kant. Om klienten använder en HTTP-proxy eller en belastningsutjämnare för att skicka begäran, är värdet för SocketIp IP-adressen för proxyservern eller belastningsutjämnaren. |
| client_ip                  | IP-adressen för klienten som gjorde den ursprungliga begäran. Om det fanns ett X-vidarebefordrat-för-huvud i begäran plockas klientens IP-adress från samma. |
| client_port                | IP-porten för den klient som gjorde begäran. |
| värdnamn                      | Värd namnet i begäran från klienten. |
| geo_country                     | Anger förfrågans land/region med hjälp av lands-/region koden. |
| http_method                | Den metod som används för att göra URL-begäran. Till exempel GET eller POST. |
| http_version               | Protokollet för begäran. Vanligt vis HTTP/1.0, HTTP/1.1 eller HTTP/2.0. |
| query_string               | Listan över variabel/värde-par som följer "?" i den begärda URL: en. Exempel: i begäran blir *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* QUERY_STRING värde *ID = 123&title = Fabrikam* |
| request_scheme             | Begär ande schema: http eller https. |
| request_uri                | Fullständig URI för ursprunglig begäran (med argument). Exempel: i begäran *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* kommer REQUEST_URI värde att */article.aspx? id = 123&title = Fabrikam* |
| server_port                | Porten för den server som godkände en begäran. |
| ssl_protocol    | Protokollet för en etablerad TLS-anslutning. |
| url_path                   | Identifierar den angivna resursen i värden som webb klienten vill ha åtkomst till. Detta är en del av URI: n för begäran utan argumenten. Exempel: i begäran blir *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* uri_path värde */article.aspx* |

### <a name="server-variable-format"></a>Server variabel format    

**Format:** {Variable: offset}, {variabel: offset: length}, {Variable}

### <a name="supported-server-variable-actions"></a>Server variabel åtgärder som stöds

* Begärandehuvud
* Svars huvud
* Frågesträng för cache-nyckel
* URL-omskrivning
* URL-omdirigering

## <a name="next-steps"></a>Nästa steg

* Läs mer om [stanard för Azures frontend-dörr/Premium-regel](concept-rule-set.md).
* Lär dig mer om [matchnings villkor för regel uppsättning](concept-rule-set-match-conditions.md).
