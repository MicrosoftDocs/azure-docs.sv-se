---
title: Azure CDN från Verizon Premium Rules Engine-funktioner
description: Referens dokumentation för Azure CDN från funktioner i verktyg för Verizon Premium-regler.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/02/2020
ms.author: allensu
ms.openlocfilehash: 0ea4f167b992ccfbc4156ac06c8f636d2ef4a355
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020423"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN från Verizon Premium Rules Engine-funktioner

Den här artikeln innehåller detaljerade beskrivningar av de tillgängliga funktionerna för [regel motorn](cdn-verizon-premium-rules-engine.md)för Azure Content Delivery Network (CDN).

Den tredje delen av en regel är funktionen. En funktion definierar vilken typ av åtgärd som ska tillämpas på den typ av begäran som identifieras av en uppsättning matchnings villkor.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a><a name="top"></a>Azure CDN från Verizon Premium Rules-funktioner referens

Tillgängliga typer av funktioner är:

* [Åtkomst](#access)
* [Cachelagring](#caching)
* [Kommentar](#comment)
* [Sidhuvuden](#headers)
* [Loggar](#logs)
* [Optimera](#optimize)
* [Kommer](#origin)
* [Specialitet](#specialty)
* [URL](#url)
* [Brandvägg för webbaserade program](#waf)

### <a name="access"></a><a name="access"></a>Access

Dessa funktioner är utformade för att styra åtkomsten till innehåll.

| Name       | Syfte                                                           |
|------------|-------------------------------------------------------------------|
| [Neka åtkomst (403)](https://docs.vdms.com/cdn/Content/HRE/F/Deny-Access-403.htm) | Anger om alla förfrågningar avvisas med ett 403-förbjudet svar. |
| [Token-autentisering](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm) | Anger om Token-Based autentisering ska användas för en begäran. |
| [Nekad token-auktoriseringskod](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm) | Bestämmer vilken typ av svar som ska returneras till en användare när en begäran nekas på grund av Token-Based autentisering. |
| [Ignorera URL-fall för token auth](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm) | Anger om URL-jämförelser som görs av Token-Based autentisering är Skift läges känslig. |
| [Parameter för token-autentisering](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm) | Anger om parametern Token-Based Authentication Query sträng ska byta namn. |

**[Tillbaka till början](#top)**

### <a name="caching"></a><a name="caching"></a>Cachelagring

Dessa funktioner är utformade för att anpassa när och hur innehållet ska cachelagras.

| Name       | Syfte                                                           |
|------------|-------------------------------------------------------------------|
| [Bandbredds parametrar](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Parameters.htm) | Bestämmer om bandbredds begränsnings parametrar (t. ex. ec_rate och ec_prebuf) ska aktive ras. |
| [Bandbredds begränsning](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Throttling.htm) | Begränsar bandbredden för svar som tillhandahålls av våra Edge-servrar. |
| [Kringgå cacheminne](https://docs.vdms.com/cdn/Content/HRE/F/Bypass-Cache.htm) | Bestämmer om begäran kan utnyttja vår caching-teknik. |
| [Hantering av Cache-Control-huvud](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Control-Header-Treatment.htm) |  Styr genereringen av Cache-Control sidhuvuden för Edge-servern när den externa Max-Age funktionen är aktiv. |
| [Cache-nyckel frågesträng](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Query-String.htm) | Bestämmer om **cache-Key** _ kommer att inkludera eller exkludera frågeparametrar som är associerade med en begäran. <br> _ _ En relativ sökväg som unikt identifierar en till gång för cachelagring.  Våra Edge-servrar använder den här relativa sökvägen vid kontroll av cachelagrat innehåll.  Som standard kommer en cache-nyckel inte att innehålla frågesträng parameters._ |
| [Cache-omarbetning av nyckel](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm) | Skriver om den cache-nyckel som är kopplad till en begäran. |
| [Slutför cache-fyllning](https://docs.vdms.com/cdn/Content/HRE/F/Complete-Cache-Fill.htm) | Bestämmer vad som händer när en begäran resulterar i en partiell cache missar på en Edge-Server. |
| [Komprimera filtyper](https://docs.vdms.com/cdn/Content/HRE/F/Compress-File-Types.htm) | Definierar de fil format som ska komprimeras på servern. | 
| [Standard intern max ålder](https://docs.vdms.com/cdn/Content/HRE/F/Default-Internal-Max-Age.htm) | Fastställer standardvärdet för max-ålder för Edge-Server till ursprunglig Server cache-omverifiering. |
| [Hantering av förfallo datum för huvud](https://docs.vdms.com/cdn/Content/HRE/F/Expires-Header-Treatment.htm) | Styr genereringen av Expires-huvuden av en Edge-Server när den externa Max-Age funktionen är aktiv. |
| [Extern högsta ålder](https://docs.vdms.com/cdn/Content/HRE/F/External-Max-Age.htm) | Fastställer Max-ålders intervallet för webbläsare till Edge Server-cache-omverifiering. |
| [Framtvinga intern max ålder](https://docs.vdms.com/cdn/Content/HRE/F/Force-Internal-Max-Age.htm) | Fastställer Max-ålders intervallet för Edge-Server till ursprungs serverns cache-omverifiering. |
| [Stöd för H. 264 (HTTP progressiv nedladdning)](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-HPD.htm) | Bestämmer vilka typer av H. 264-filformat som kan användas för att strömma innehåll. |
| [H. 264 stöder video Sök parametrar](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-VSP-HPD.htm) | Åsidosätter de namn som tilldelats parametrar som styr sökning via H. 264-media när HTTP-progressiv nedladdning används. |
| [Respektera No-Cache begäran](https://docs.vdms.com/cdn/Content/HRE/F/Honor-No-Cache-Request.htm) | Anger om en HTTP-klients begär Anden om antal cachen ska vidarebefordras till ursprungs servern. |
| [Ignorera ursprung no-cache](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Origin-No-Cache.htm) | Bestämmer om vårt CDN ska ignorera vissa direktiv som hanteras från en ursprungs Server. |
| [Ignorera områden som inte går att uppfylla](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Unsatisfiable-Ranges.htm) | Avgör vilket svar som returneras till klienter när en begäran genererar ett begärt intervall av 416 som inte kan uppfylla status koden. |
| [Intern Max-inaktuell](https://docs.vdms.com/cdn/Content/HRE/F/Internal-Max-Stale.htm) | Styr hur lång tid det normala förfallo datumet för en cachelagrad till gång kan hanteras från en Edge-Server när gräns servern inte kan omverifiera den cachelagrade till gången med ursprungs servern. |
| [Partiell cache-delning](https://docs.vdms.com/cdn/Content/HRE/F/Partial-Cache-Sharing.htm) | Anger om en begäran kan generera delvis cachelagrat innehåll. |
| [Förvalidera cachelagrat innehåll](https://docs.vdms.com/cdn/Content/HRE/F/Prevalidate-Cached-Content.htm) | Anger om cachelagrat innehåll kommer att bli kvalificerat för tidig omverifiering innan dess TTL går ut. |
| [Uppdatera Zero-Byte cache-filer](https://docs.vdms.com/cdn/Content/HRE/F/Refresh-Zero-Byte-Cache-Files.htm) | Bestämmer hur en HTTP-klients begäran för en cache-till-byte med 0 byte hanteras av våra Edge-servrar. |
| [Ange status koder för cachelagring](https://docs.vdms.com/cdn/Content/HRE/F/Set-Cacheable-Status-Codes.htm) | Definierar den uppsättning med status koder som kan leda till cachelagrat innehåll. |
| [Inaktuell innehålls leverans vid fel](https://docs.vdms.com/cdn/Content/HRE/F/Stale-Content-Delivery-on-Error.htm) | Anger om förfallet cachelagrat innehåll ska levereras när ett fel uppstår under cache-omverifieringen eller när det begärda innehållet hämtas från kund ursprungs servern. | 
| [Inaktuell vid omverifiering](https://docs.vdms.com/cdn/Content/HRE/F/Stale-While-Revalidate.htm) | Förbättrar prestandan genom att göra det möjligt för våra Edge-servrar att betjäna inaktuella klient till beställaren medan omverifieringen sker. |

**[Tillbaka till början](#top)**

### <a name="comment"></a><a name="comment"></a>Kommentar

Med kommentars funktionen kan du lägga till en anteckning i en regel.

**[Tillbaka till början](#top)**

### <a name="headers"></a><a name="headers"></a>Sidhuvuden

Dessa funktioner är utformade för att lägga till, ändra eller ta bort huvuden från begäran eller svaret.

| Name       | Syfte                                                           |
|------------|-------------------------------------------------------------------|
| [Ålders svars rubrik](https://docs.vdms.com/cdn/Content/HRE/F/Age-Response-Header.htm) | Avgör om ett ålders svars huvud ska inkluderas i svaret som skickas till beställaren. |
| [Felsöka cache-svarshuvuden](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) | Bestämmer om ett svar kan innehålla [huvudet X-EC-debug-svar](https://docs.vdms.com/cdn/Content/Knowledge_Base/X_EC_Debug.htm) som innehåller information om cache-principen för den begärda till gången. |
| [Ändra huvudet för klientbegäran](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm) | Skriver över, lägger till eller tar bort en rubrik från en begäran. |
| [Ändra huvudet för klient svaret](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm) | Skriver över, lägger till eller tar bort en rubrik från ett svar. |
| [Ange IP-adress för anpassade klienter](https://docs.vdms.com/cdn/Content/HRE/F/Set-Client-IP-Custom-Header.htm) | Tillåter att IP-adressen för den begär ande klienten läggs till i begäran som en anpassad begär ande rubrik. |

**[Tillbaka till början](#top)**

### <a name="logs"></a><a name="logs"></a>Loggar

Dessa funktioner är utformade för att anpassa data som lagras i råa loggfiler.

| Name       | Syfte                                                           |
|------------|-------------------------------------------------------------------|
| [Anpassat logg fält 1](https://docs.vdms.com/cdn/Content/HRE/F/Custom-Log-Field-1.htm) | Anger formatet och det innehåll som ska tilldelas till det anpassade logg fältet i en rå loggfil. |
| [Frågesträng för logg](https://docs.vdms.com/cdn/Content/HRE/F/Log-Query-String.htm) | Anger om en frågesträng ska lagras tillsammans med URL: en i Access-loggar. |

**[Tillbaka till början](#top)**

### <a name="optimize"></a><a name="optimize"></a>Optimera

Dessa funktioner avgör om en begäran kommer att genomgå de optimeringar som tillhandahålls av Edge optimering.

| Name       | Syfte                                                           |
|------------|-------------------------------------------------------------------|
| [Kant optimering](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer.htm) | Anger om Edge-optimering kan användas för en begäran. |
| [Edge-optimering – instansiera konfiguration](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer-Instantiate-Configuration.htm) | Instansierar eller aktiverar den kant optimerings konfiguration som är associerad med en plats. |

**[Tillbaka till början](#top)**

### <a name="origin"></a><a name="origin"></a>Kommer

Dessa funktioner är utformade för att styra hur CDN kommunicerar med en ursprungs Server.

| Name       | Syfte                                                           |
|------------|-------------------------------------------------------------------|
| [Maximalt antal Keep-Alive begär Anden](https://docs.vdms.com/cdn/Content/HRE/F/Maximum-Keep-Alive-Requests.htm) | Definierar maximalt antal begär Anden för en Keep-Alive anslutning innan den stängs. |
| [Särskilda rubriker för proxy](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) | Definierar den uppsättning [CDN-Specific begärandehuvuden](https://docs.vdms.com/cdn/Content/Knowledge_Base/Request-Format.htm#RequestHeaders) som ska vidarebefordras från en Edge-Server till en ursprungs Server. |

**[Tillbaka till början](#top)**

### <a name="specialty"></a><a name="specialty"></a>Specialitet

Dessa funktioner ger avancerade funktioner som endast ska användas av avancerade användare.

| Name       | Syfte                                                           |
|------------|-------------------------------------------------------------------|
| [HTTP-metoder för cachelagring](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-HTTP-Methods.htm) | Anger en uppsättning ytterligare HTTP-metoder som kan cachelagras i vårt nätverk. |
| [Brödtext storlek för cache-begäran](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-Request-Body-Size.htm) | Definierar tröskelvärdet för att avgöra om ett POST-svar kan cachelagras. |
| [QUIC](https://docs.vdms.com/cdn/Content/HRE/F/QUIC.htm) | Avgör om klienten ska informeras om att vår CDN-tjänst stöder QUIC. |
| [Strömnings optimering](https://docs.vdms.com/cdn/Content/HRE/F/Streaming-Optimization.htm) | Justerar konfigurationen för cachelagring för att optimera prestanda för Live-strömmar och minska belastningen på ursprungs servern. |
| [Användar variabel](https://docs.vdms.com/cdn/Content/HRE/F/User-Variable.htm) | Tilldelar ett värde till en användardefinierad variabel som skickas till din eker-lösning för trafik bearbetning. |

**[Tillbaka till början](#top)**

### <a name="url"></a><a name="url"></a>URL

Dessa funktioner tillåter att en begäran omdirigeras eller skrivs om till en annan URL.

| Name       | Syfte                                                           |
|------------|-------------------------------------------------------------------|
| [Följ omdirigeringar](https://docs.vdms.com/cdn/Content/HRE/F/Follow-Redirects.htm) | Bestämmer om begär Anden kan omdirigeras till det värdnamn som definierats i det plats huvud som returneras av en kund ursprungs Server. |
| [URL-omdirigering](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm) | Omdirigerar begär Anden via plats rubriken. |
| [URL-omskrivning](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm) | Skriver om URL: en för begäran. |

**[Tillbaka till början](#top)**

### <a name="web-application-firewall"></a><a name="waf"></a>Brandvägg för webbaserade program

Funktionen [brand vägg för webbaserade program](https://docs.vdms.com/cdn/Content/HRE/F/Web_Application_Firewall.htm) avgör om en begäran ska visas med brand vägg för webbaserade program.

**[Tillbaka till början](#top)**

De senaste funktionerna finns i [dokumentationen om Verizon Rules Engine](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions).

## <a name="next-steps"></a>Nästa steg

- [Regel motor referens](cdn-verizon-premium-rules-engine-reference.md)
- [Regelmotor – villkorliga uttryck](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regelmotor – matchningsvillkor](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Åsidosätt HTTP-beteende med regel motorn](cdn-verizon-premium-rules-engine.md)
- [Översikt över Azure CDN](cdn-overview.md)
