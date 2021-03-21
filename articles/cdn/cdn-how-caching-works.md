---
title: Så här fungerar cachelagring | Microsoft Docs
description: Cachelagring är en process där data lagras lokalt så att framtida begär Anden om dessa data kan nås snabbare.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: allensu
ms.openlocfilehash: a226682c2580a871e1b2fc4db71f369f3bcc3abb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010171"
---
# <a name="how-caching-works"></a>Så här fungerar cachelagring

Den här artikeln innehåller en översikt över allmänna cachelagring-koncept och hur [Azure Content Delivery Network (CDN)](cdn-overview.md) använder cachelagring för att förbättra prestanda. Om du vill veta mer om hur du anpassar cachelagring av funktioner på CDN-slutpunkten, se [kontroll Azure CDN cachelagring med regler](cdn-caching-rules.md) för cachelagring och [kontroll Azure CDN cachelagring med frågesträngar](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Introduktion till cachelagring

Cachelagring är en process där data lagras lokalt så att framtida begär Anden om dessa data kan nås snabbare. I den vanligaste typen av cachelagring, cachelagring av webbläsare, lagrar en webbläsare kopior av statiska data lokalt på en lokal hård disk. Med hjälp av cachelagring kan du undvika att göra flera turer till-servern och i stället komma åt samma data lokalt, så att du sparar tid och resurser. Cachelagring passar bra för lokal hantering av små, statiska data som statiska bilder, CSS-filer och JavaScript-filer.

På samma sätt används cachelagring av ett Content Delivery Network på Edge-servrarna nära användaren för att undvika att förfrågningar skickas tillbaka till ursprunget och försämra slut användar fördröjningen. Till skillnad från en webbläsares cacheminne, som endast används för en enskild användare, har CDN en delad cache. I ett CDN-delat cacheminne kan en fil som begärs av en användare kommas åt senare av andra användare, vilket avsevärt minskar antalet begär anden till ursprungs servern.

Dynamiska resurser som ändras ofta eller som är unika för en enskild användare kan inte cachelagras. Dessa typer av resurser kan dock dra nytta av DSA-optimering (Dynamic site acceleration) i Azure Content Delivery Network för prestanda förbättringar.

Cachelagring kan ske på flera nivåer mellan ursprungs servern och slutanvändaren:

- Webb server: använder en delad cache (för flera användare).
- Content Delivery Network: använder en delad cache (för flera användare).
- Internet tjänst leverantör (ISP): använder en delad cache (för flera användare).
- Webbläsare: använder en privat cache (för en användare).

Varje cache hanterar vanligt vis sin egen resurs uppdatering och utför verifiering när en fil är inaktuell. Detta beteende definieras i specifikationen för HTTP caching, [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Resurs uppdatering

Eftersom en cachelagrad resurs kan vara inaktuell, eller inaktuell (jämfört med motsvarande resurs på ursprungs servern), är det viktigt att alla caching-mekanismer styr när innehållet uppdateras. För att spara tids-och bandbredds förbrukningen jämförs inte den cachelagrade resursen med versionen på ursprungs servern varje gång den nås. Om en cachelagrad resurs anses vara färsk antas den vara den senaste versionen och skickas direkt till klienten. En cachelagrad resurs anses vara ny när dess ålder är mindre än den ålder eller den period som definieras av en cache-inställning. När en webbläsare till exempel laddar upp en webb sida, verifierar den att varje cachelagrad resurs på hård disken är färsk och läser in den. Om resursen inte är uppdaterad (inaktuell) läses en uppdaterad kopia in från servern.

### <a name="validation"></a>Validering

Om en resurs anses vara inaktuell uppmanas ursprungs servern att verifiera den, det vill säga om data i cacheminnet fortfarande matchar det som finns på ursprungs servern. Om filen har ändrats på ursprungs servern, uppdaterar cacheminnet dess version av resursen. Annars, om resursen är färsk, levereras data direkt från cachen utan att först verifiera den.

### <a name="cdn-caching"></a>CDN-cachelagring

Cachelagring är till för att göra det möjligt för en CDN att snabbare leverera och minska ursprungs belastningen för statiska till gångar, till exempel bilder, teckensnitt och videor. I CDN-cachelagring lagras statiska resurser selektivt på strategiskt placerade servrar som är mer lokala för en användare och ger följande fördelar:

- Eftersom den mesta webb trafiken är statisk (till exempel bilder, teckensnitt och videor) minskar CDN-cachelagringen nätverks fördröjning genom att flytta innehållet närmare användaren, vilket minskar det avstånd som data överförs.

- Genom att avlasta arbete till en CDN kan cachelagring minska nätverks trafiken och belastningen på ursprungs servern. Detta minskar kraven på kostnad och resurs för programmet, även om det finns ett stort antal användare.

Precis som med hur cachelagring implementeras i en webbläsare kan du styra hur cachelagring ska utföras i ett CDN genom att skicka cache-direktiv-rubriker. Cache – direktiv rubriker är HTTP-huvuden, som vanligt vis läggs till av ursprungs servern. Även om de flesta av dessa huvuden ursprungligen konstruerades för cachelagring i klient webbläsare, används de också av alla mellanliggande cacheminnen, till exempel CDN. 

Två rubriker kan användas för att definiera cache-aktualitet: `Cache-Control` och `Expires` . `Cache-Control` är mer aktuell och prioriteras framför `Expires` , om båda finns. Det finns också två typer av huvuden som används för verifiering (kallas verifierare): `ETag` och `Last-Modified` . `ETag` är mer aktuell och prioriteras framför `Last-Modified` , om båda definieras.  

## <a name="cache-directive-headers"></a>Cache – direktiv rubriker

> [!IMPORTANT]
> Som standard ignorerar en Azure CDN-slutpunkt som är optimerad för DSA cache-direktiv-rubriker och kringgår cachelagring. För **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** -profiler kan du justera hur en Azure CDN-slutpunkt behandlar dessa huvuden med hjälp av [CDN caching-regler](cdn-caching-rules.md) för att aktivera cachelagring. För **Azure CDN Premium från Verizon** -profiler, använder du [regel motorn](./cdn-verizon-premium-rules-engine.md) för att aktivera cachelagring.

Azure CDN stöder följande HTTP-cache – direktiv-rubriker, som definierar cache-varaktighet och cache-delning.

**Cache-kontroll:**
- Introducerade i HTTP 1,1 för att ge webb utgivare mer kontroll över innehållet och för att åtgärda `Expires` rubrikernas begränsningar.
- Åsidosätter `Expires` rubriken, om både den och `Cache-Control` definieras.
- När de används i en HTTP-begäran från klienten till CDN-POP, `Cache-Control` ignoreras som standard av alla Azure CDN profiler.
- När det används i ett HTTP-svar från klienten till CDN-POP:
     - **Azure CDN Standard/Premium från Verizon** och **Azure CDN Standard från Microsoft** stöder alla `Cache-Control` direktiv.
     - **Azure CDN Standard från Akamai** stöder endast följande `Cache-Control` direktiv. alla andra ignoreras:
         - `max-age`: Ett cacheminne kan lagra innehållet i angivet antal sekunder. Till exempel `Cache-Control: max-age=5`. Detta direktiv anger den maximala tid som innehållet anses vara färskt.
         - `no-cache`: Cachelagra innehållet, men verifiera innehållet varje gång innan det levereras från cachen. Motsvarar `Cache-Control: max-age=0` .
         - `no-store`: Cachelagra aldrig innehållet. Ta bort innehåll om det har lagrats tidigare.

**Upphör att gälla**
- Äldre sidhuvud har introducerats i HTTP 1,0; stöds för bakåtkompatibilitet.
- Använder en datum-baserad förfallo tid med andra precision. 
- Liknande `Cache-Control: max-age` .
- Används när `Cache-Control` inte finns.

**Pragma**
   - Är som standard inte inlöst av Azure CDN.
   - Äldre sidhuvud har introducerats i HTTP 1,0; stöds för bakåtkompatibilitet.
   - Används som ett huvud för klient förfrågan med följande direktiv: `no-cache` . Detta direktiv instruerar servern att leverera en ny version av resursen.
   - `Pragma: no-cache` är ekvivalent med `Cache-Control: no-cache`.

## <a name="validators"></a>Verifierare

När cachen är inaktuell används HTTP cache-verifierare för att jämföra den cachelagrade versionen av en fil med versionen på ursprungs servern. **Azure CDN Standard/Premium från Verizon** stöder både `ETag` och `Last-Modified` verifierare som standard, men **Azure CDN Standard från Microsoft** och **Azure CDN Standard från Akamai** stöder endast `Last-Modified` som standard.

**ETag**
- **Azure CDN Standard/Premium från Verizon** stöder `ETag` som standard, medan **Azure CDN standard från Microsoft** och **Azure CDN Standard från Akamai** inte.
- `ETag` definierar en sträng som är unik för varje fil och version av en fil. Till exempel `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Introducerades i HTTP 1,1 och är mer aktuell än `Last-Modified` . Användbart när det senaste ändrings datumet är svårt att fastställa.
- Stöder både stark verifiering och svag verifiering. Azure CDN stöder dock endast stark verifiering. För stark validering måste de två resurs representationerna vara byte-till-byte identiska. 
- En cache verifierar en fil som använder `ETag` genom att skicka ett `If-None-Match` huvud med en eller flera `ETag` verifierare i begäran. Till exempel `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Om serverns version matchar en `ETag` verifierare i listan, skickar den status kod 304 (inte ändrad) i sitt svar. Om versionen skiljer sig från varandra svarar servern med status kod 200 (OK) och den uppdaterade resursen.

**Senast ändrad:**
- För **Azure CDN Standard/Premium från Verizon** `Last-Modified` används om `ETag` inte ingår i http-svaret. 
- Anger det datum och den tid som ursprungs servern har fastställt att resursen senast ändrades. Till exempel `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- En cache verifierar en fil med hjälp `Last-Modified` av genom att skicka ett `If-Modified-Since` huvud med ett datum och en tid i begäran. Ursprungs servern jämför det datumet med `Last-Modified` rubriken för den senaste resursen. Om resursen inte har ändrats sedan den angivna tiden, returnerar servern status kod 304 (inte ändrad) i sitt svar. Om resursen har ändrats returnerar servern status kod 200 (OK) och den uppdaterade resursen.

## <a name="determining-which-files-can-be-cached"></a>Avgöra vilka filer som kan cachelagras

Det går inte att cachelagra alla resurser. I följande tabell visas vilka resurser som kan cachelagras baserat på typen av HTTP-svar. Resurser som levereras med HTTP-svar som inte uppfyller alla dessa villkor kan inte cachelagras. För **Azure CDN Premium från Verizon** kan du använda regel motorn för att anpassa vissa av dessa villkor.

|                       | Azure CDN från Microsoft          | Azure CDN från Verizon | Azure CDN från Akamai        |
|-----------------------|-----------------------------------|------------------------|------------------------------|
| **HTTP-statuskoder** | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| **HTTP-metoder**      | HÄMTA, HUVUD                         | GET                    | GET                          |
| **Fil storleks begränsningar**  | 300 GB                            | 300 GB                 | – Allmän optimering av webb leverans: 1,8 GB<br />– Optimeringar för medie direkt uppspelning: 1,8 GB<br />– Optimering av stora filer: 150 GB |

För **Azure CDN Standard från Microsoft** -cachelagring för att fungera på en resurs måste ursprungs servern ha stöd för alla Head-och get HTTP-begäranden och Content-Length-värdena måste vara desamma för alla Head-och get http-svar för till gången. För en HEAD-begäran måste ursprungs servern ha stöd för HEAD-begäran och måste svara med samma rubriker som om den har tagit emot en GET-begäran.

## <a name="default-caching-behavior"></a>Standard beteende för cachelagring

I följande tabell beskrivs hur du aktiverar standardvärdet för cachelagring av Azure CDN produkter och deras optimeringar.

|    | Microsoft: allmän webb leverans | Verizon: allmän webb leverans | Verizon: DSA | Akamai: allmän webb leverans | Akamai: DSA | Akamai: stor fil nedladdning | Akamai: allmän eller VOD medie direkt uppspelning |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Respektera ursprung**       | Ja    | Ja   | Inga   | Ja    | Inga   | Ja   | Ja    |
| **Varaktighet för CDN-cache** | 2 dagar |7 dagar | Inget | 7 dagar | Inget | 1 dag | 1 år |

Förfallet **ursprung**: anger om det ska gå att använda cache-direktiv-huvuden som stöds, om de finns i http-svaret från ursprungs servern.

**Varaktighet för CDN-cache**: anger hur lång tid en resurs cachelagras på Azure CDN. Men om förfallet **ursprung** är ja och http-svaret från ursprungs servern innehåller rubriken cache-direktiv `Expires` eller `Cache-Control: max-age` , Azure CDN använder det varaktighets värde som anges av rubriken i stället. 

## <a name="next-steps"></a>Nästa steg

- Information om hur du anpassar och åsidosätter standardvärdet för cachelagring i CDN via regler för cachelagring finns i [styra Azure CDN cachelagring med regler för cachelagring](cdn-caching-rules.md). 
- Information om hur du använder frågesträngar för att styra cachelagring av funktioner finns i [kontroll Azure CDN cachelagring med frågesträngar](cdn-query-string.md).