---
title: Optimering av medie direkt uppspelning med Azure CDN
description: Lär dig mer om alternativ för att optimera strömmande media i Azure Content Delivery Network, t. ex. partiell cache-delning och cache Fill-vänte tid.
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
ms.topic: how-to
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: c3ab722f182e32cf2f3aca6bb2f3d5a9598264af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88192610"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Optimering av medie direkt uppspelning med Azure CDN 
 
Användning av hög upplöst video ökar på Internet, vilket skapar problem för effektiv leverans av stora filer. Kunderna förväntar sig en smidig uppspelning av video på begäran eller direktsända video till gångar på flera olika nätverk och klienter över hela världen. En snabb och effektiv leverans metod för Media Streaming-filer är viktig för att säkerställa en smidig och trevlig konsument upplevelse.  

Direkt uppspelning av media är särskilt svårt att leverera på grund av de stora storlekarna och antalet samtidiga användare. Långa fördröjningar gör att användarna lämnar. Eftersom real tids strömmar inte kan cachelagras i förväg och stora fördröjningar inte är acceptabla för användarna, måste videofragmenten levereras i rätt tid. 

De begärda mönstren för direkt uppspelning ger också några nya utmaningar. När en populär Live-ström eller en ny serie släpps för video på begäran kan tusentals till miljon tals användare begära data strömmen på samma gång. I det här fallet är konsolidering av smarta begär Anden viktigt att inte överbelasta ursprungs servrarna när till gångarna inte är cachelagrade än.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Optimeringar av medie direkt uppspelning för Azure CDN från Microsoft

**Azure CDN Standard från Microsoft** -slutpunkter levererar strömnings medie till gångar direkt med hjälp av den allmänna optimerings typen för webb leverans. 

Optimering av medie direkt uppspelning för **Azure CDN Standard från Microsoft** gäller för strömmande Live-eller video-on-demand-media som använder enskilda medie fragment för leverans. Den här processen skiljer sig från en enda stor till gång som överförs via progressiv nedladdning eller med hjälp av byte intervall begär Anden. Information om den här typen av medie leverans finns i [optimering av stor fil hämtning med Azure CDN](cdn-large-file-optimization.md).

De allmänna optimerings typerna för medie leverans eller video på begäran använder Azure Content Delivery Network (CDN) med Server dels optimeringar för att leverera medie till gångar snabbare. De använder också konfigurationer för medie till gångar baserat på bästa praxis som lärts över tid.

### <a name="partial-cache-sharing"></a>Partiell cache-delning
Partiell cache-delning gör att CDN kan betjäna delvis cachelagrat innehåll till nya begär Anden. Om den första begäran till CDN till exempel resulterar i en cache missar, skickas begäran till ursprunget. Även om det ofullständiga innehållet läses in i CDN-cachen, kan andra begär anden till CDN börja hämta dessa data. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Optimeringar av medie direkt uppspelning för Azure CDN från Verizon

**Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** -slutpunkter levererar strömmande medie till gångar direkt med hjälp av den allmänna optimerings typen för webb leverans. Några funktioner på CDN är direkt hjälp i att leverera medie till gångar som standard.

### <a name="partial-cache-sharing"></a>Partiell cache-delning

Partiell cache-delning gör att CDN kan betjäna delvis cachelagrat innehåll till nya begär Anden. Om den första begäran till CDN till exempel resulterar i en cache missar, skickas begäran till ursprunget. Även om det ofullständiga innehållet läses in i CDN-cachen, kan andra begär anden till CDN börja hämta dessa data. 

### <a name="cache-fill-wait-time"></a>Vänte tid för cachelagring

 Funktionen för att vänta på cache-tid tvingar Edge-servern att lagra eventuella efterföljande begär Anden för samma resurs tills HTTP-svarshuvuden tas från ursprungs servern. Om HTTP-svarshuvuden från ursprunget anländer innan timern upphör att gälla, kommer alla begär Anden som spärrats att hanteras från det växande cacheminnet. På samma gång fylls cachen av data från ursprunget. Som standard är vänte tiden för cachen inställd på 3 000 millisekunder. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Optimeringar av medie direkt uppspelning för Azure CDN från Akamai
 
**Azure CDN Standard från Akamai** erbjuder en funktion som levererar strömnings medie resurser effektivt till användare över hela världen i stor skala. Funktionen minskar fördröjningen eftersom belastningen på ursprungs servrarna minskar. Den här funktionen är tillgänglig med pris nivån standard Akamai. 

Optimering av medie direkt uppspelning för **Azure CDN Standard från Akamai** är effektiv för strömnings medier i real tid eller video på begäran som använder enskilda medie fragment för leverans. Den här processen skiljer sig från en enda stor till gång som överförs via progressiv nedladdning eller med hjälp av byte intervall begär Anden. Information om den här typen av medie leverans finns i [optimering av stora filer](cdn-large-file-optimization.md).

De allmänna optimerings typerna för medie leverans eller video på begäran använder en CDN med Server dels optimeringar för att leverera medie till gångar snabbare. De använder också konfigurationer för medie till gångar baserat på bästa praxis som lärts över tid.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Konfigurera en Akamai CDN-slutpunkt för att optimera medie direkt uppspelning
 
Du kan konfigurera din CDN-slutpunkt (Content Delivery Network) för att optimera leveransen för stora filer via Azure Portal. Du kan också använda REST-API: erna eller klient-SDK: er för att göra detta. Följande steg visar processen via Azure Portal för en **Azure CDN Standard från Akamai** profil:

1. Om du vill lägga till en ny slut punkt väljer du **slut punkt** på sidan Akamai **CDN Profile** .
  
    ![Ny slut punkt](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. I list rutan **optimerad för** väljer du **video på begäran medie strömning** för video på begäran. Om du gör en kombination av direkt uppspelning av Live och video på begäran väljer du **allmän medie direkt uppspelning**.

    ![Strömning vald](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
När du har skapat slut punkten tillämpar den optimeringen för alla filer som matchar vissa villkor. I följande avsnitt beskrivs den här processen. 

### <a name="caching"></a>Cachelagring

Om **Azure CDN Standard från Akamai** upptäcker att till gången är ett strömmande manifest eller fragment, använder den olika utgångs tider för cachelagring från allmän webb leverans. (Se den fullständiga listan i följande tabell.) Som alltid är Cache-Control eller Expires-huvuden som skickas från ursprunget. Om till gången inte är en medie till gång cachelagras den med hjälp av förfallo tiderna för allmän webb leverans.

Tiden för kort negativ cachelagring är användbar för ursprungs avlastning när många användare begär ett fragment som inte finns än. Ett exempel är en Live-ström där paketen inte är tillgängliga från ursprunget som andra. Det längre intervallet för cachelagring hjälper också till att avlasta begär Anden från ursprunget, eftersom video innehållet normalt inte ändras.

| Cachelagring  | Allmän webb leverans | Allmän medie direkt uppspelning | Video-on-demand-mediedirektuppspelning  
|--- | --- | --- | ---
| Cachelagring: positiv <br> HTTP 200, 203, 300, <br> 301, 302 och 410 | 7 dagar |365 dagar | 365 dagar   
| Cachelagring: negativ <br> HTTP 204, 305, 404, <br> och 405 | Inget | 1 sekund | 1 sekund
 
### <a name="deal-with-origin-failure"></a>Hantera ursprungs problem  

Allmänt medium leverans-och video-på-begäran-medie leverans har även ursprungs tids gränser och en återförsöks logg baserat på metod tips för typiska mönster för förfrågningar. Till exempel eftersom den allmänna medie leveransen är till för Live-och video-på-begäran-medie leverans, använder den en kortare tids gräns för anslutningen på grund av den tidskänsligaste typen av direkt uppspelning.

När en anslutnings tid är slut försöker CDN ett antal gånger innan det skickar ett fel meddelande om "504-Gateway-timeout" till klienten. 

När en fil matchar listan fil typ och storleks villkor använder CDN beteendet för medie direkt uppspelning. Annars används allmän webb leverans.
   
### <a name="conditions-for-media-streaming-optimization"></a>Villkor för optimering av medie direkt uppspelning 

I följande tabell visas en uppsättning villkor som ska uppfyllas för optimering av medie direkt uppspelning: 
 
Typer av strömning som stöds | Filnamnstillägg  
--- | ---  
Apple-HLS | M3U8, m3u, m3ub, nyckel, TS, AAC
Adobe-HDS | f4m, f4x, drmmeta, bootstrap, F4F,<br>Seg-Frag URL-struktur <br> (matchande regex: ^ (/. *) SEQ (\d +)-Frag (\d +)
DASH | MPD, bindestreck, DivX, ISMV, M4S, M4V, MP4, MP4V, <br> sidx, webm, mp4a, m4a, isma
Smidig strömning | /manifest/, /QualityLevels/Fragments/
  
 
