---
title: X-EC-felsöka HTTP-huvuden för Azure CDN-regel motor | Microsoft Docs
description: Huvudet X-EC-debug-begäran om cacheminne innehåller ytterligare information om den cachelagrade princip som tillämpas på den begärda till gången. De här rubrikerna är bara för Verizon.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: allensu
ms.openlocfilehash: 4154c6a1e739f935022271e7a101f39d3ee5c500
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84343028"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>X-EC-felsöka HTTP-huvuden för Azure CDN-regel motor
Rubriken för felsöknings-cachens begär ande `X-EC-Debug` innehåller ytterligare information om den cachelagrade princip som tillämpas på den begärda till gången. Dessa huvuden är speciella för **Azure CDN Premium från Verizon** -produkter.

## <a name="usage"></a>Användning
Svaret som skickas från POP-servrarna till en användare inkluderar `X-EC-Debug` endast huvudet när följande villkor uppfylls:

- Funktionen för att [Felsöka cache-svarshuvuden](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) har Aktiver ATS i regel motorn för den angivna begäran.
- Den angivna begäran definierar den uppsättning av svars rubriker för debug-cachen som ska ingå i svaret.

## <a name="requesting-debug-cache-information"></a>Begär information om felsöknings-cache
Använd följande direktiv i den angivna begäran för att definiera den felsöknings-cache-information som ska ingå i svaret:

Begärandehuvud | Beskrivning |
---------------|-------------|
X-EG-debug: x-EC-cache | [Status kod för cache](#cache-status-code-information)
X-EG-debug: x-EC-cache-Remote | [Status kod för cache](#cache-status-code-information)
X-EG-debug: x-EG-check-cacheable | [Kan cachelagras](#cacheable-response-header)
X-EG-debug: x-EG-cache-Key | [Cache-Key](#cache-key-response-header)
X-EG-debug: x-EC-cache-State | [Cache-tillstånd](#cache-state-response-header)

### <a name="syntax"></a>Syntax

Felsök cache-svarshuvuden kan begäras genom att inkludera följande rubrik och de angivna direktiven i begäran:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Exempel på X-EC – fel söknings huvud

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Information om cache status kod
Svars huvudet för X-EG-debug kan identifiera en server och hur den hanterar svaret genom följande direktiv:

Huvud | Beskrivning
-------|------------
X-EG-debug: x-EC-cache | Den här rubriken rapporteras när innehållet dirigeras via CDN. Den identifierar den POP-server som uppfyllde begäran.
X-EG-debug: x-EC-cache-Remote | Den här rubriken rapporteras endast när det begärda innehållet cachelagrades på en ursprungs skärms Server eller en och gateway-server.

### <a name="response-header-format"></a>Format för svars huvud

Status kod informationen för cache i X-EG-felsöknings huvudet i följande format:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

De termer som används i ovanstående syntax för svars huvud definieras enligt följande:
- StatusCode: anger hur det begärda innehållet hanterades av CDN, vilket representeras genom en cache-statuskod.
    
    TCP_DENIED status kod kan rapporteras i stället för ingen när en obehörig begäran nekas på grund av Token-Based autentisering. Men status koden ingen fortsätter att användas när du visar status rapporter eller rå data i cachen.

- Plattform: anger den plattform där innehållet begärdes. Följande koder är giltiga för det här fältet:

    Kod  | Plattform
    ------| --------
    ECAcc | HTTP-stor
    ECS   | HTTP-liten
    ECD   | Application Delivery Network (och)

- POP: anger den [pop](cdn-pop-abbreviations.md) som hanterade begäran. 

### <a name="sample-response-headers"></a>Exempel på svarshuvuden

Följande exempel rubriker tillhandahåller information om cache status kod för en begäran:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Svars huvud för cacheminne
`X-EC-Debug: x-ec-check-cacheable`Svars huvudet visar om det begärda innehållet kan ha cachelagrats.

Det här svars huvudet indikerar inte om cachelagringen ägde rum. I stället anger det om begäran har kvalificerats för cachelagring.

### <a name="response-header-format"></a>Format för svars huvud

`X-EC-Debug`Svars huvudets rapportering om en begäran kan ha cachelagrats är i följande format:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

Termen som används i ovanstående syntax för svars huvud definieras enligt följande:

Värde  | Beskrivning
-------| --------
JA    | Anger att det begärda innehållet är kvalificerat för cachelagring.
NO     | Anger att det begärda innehållet inte var berättigat för cachelagring. Den här statusen kan bero på någon av följande orsaker: <br /> -Customer-Specific konfiguration: en konfiguration som är speciell för ditt konto kan förhindra att pop-servrarna cachelagrar en till gång. Till exempel kan regel motorn förhindra att en till gång cachelagras genom att aktivera funktionen bypass cache för kvalificerings begär Anden.<br /> – Cache-svarshuvuden: den begärda till gångens Cache-Control och Expires-huvuden kan förhindra att POP-servrarna cachelagrar den.
UNKNOWN | Anger att servrarna inte kunde utvärdera om den begärda till gången var cachebar. Den här statusen inträffar vanligt vis när begäran nekas på grund av tokenbaserad autentisering.

### <a name="sample-response-header"></a>Exempel på svars rubrik

Följande exempel svars huvud visar om det begärda innehållet kan cachelagras:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Cache-Key svars huvud
`X-EC-Debug: x-ec-cache-key`Svars huvudet visar den fysiska cache-nyckel som är kopplad till det begärda innehållet. En fysisk cache-nyckel består av en sökväg som identifierar en till gång för cachelagring. Med andra ord kontrollerar servrarna om det finns en cachelagrad version av en till gång enligt dess sökväg som definieras av dess cache-Key.

Den här fysiska cachen-nyckeln börjar med ett dubbelt snedstreck (//) följt av protokollet som används för att begära innehållet (HTTP eller HTTPS). Det här protokollet följs av den relativa sökvägen till den begärda till gången, som börjar med innehålls åtkomst punkten (till exempel _/000001/_).

Som standard är HTTP-plattformarna konfigurerade att använda *standardcache*, vilket innebär att frågesträngarna ignoreras av mekanismen för cachelagring. Den här typen av konfiguration förhindrar cache-nyckeln från att inkludera frågesträngs data.

Om en frågesträng registreras i cache-nyckeln, konverteras den till dess hash-motsvarighet och infogas sedan mellan namnet på den begärda till gången och dess fil namns tillägg (till exempel till gångens &lt; hash-värde &gt; . html).

### <a name="response-header-format"></a>Format för svars huvud

`X-EC-Debug`Svars huvudet rapporterar information om fysiska cache-nycklar i följande format:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Exempel på svars rubrik

Följande exempel svars huvud visar den fysiska cache-nyckeln för det begärda innehållet:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Svars rubrik för cachelagring
`X-EC-Debug: x-ec-cache-state`Svars huvudet visar cache-statusen för det begärda innehållet vid den tidpunkt då begäran gjordes.

### <a name="response-header-format"></a>Format för svars huvud

`X-EC-Debug`Svars huvud rapporterar cachelagring av statusinformation i följande format:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

De termer som används i ovanstående syntax för svars huvud definieras enligt följande:

- MASeconds: anger den högsta-ålder (i sekunder) som definieras av det begärda innehållets Cache-Control huvuden.

- MATimePeriod: konverterar värdet för max-ålder (det vill säga MASeconds) till den ungefärliga motsvarigheten till en större enhet (till exempel dagar). 

- UnixTime: anger cache-tidsstämpeln för det begärda innehållet i UNIX-tid (även kallat POSIX-tid eller UNIX-epok). Tidsstämpeln för cachen anger start datum/tid då beräkningen av en till gångs-TTL beräknas. 

    Om ursprungs servern inte använder en HTTP-Caching-Server från tredje part eller om servern inte returnerar huvudet för ålders svar är cache-tidsstämpeln alltid den datum/tid när till gången hämtades eller omverifierades. Annars använder POP-servrarna fältet ålder för att beräkna till gångens TTL enligt följande: hämtnings-/RevalidateDateTime-ålder.

- DDD, DD MMM yyyy HH: mm: SS GMT: anger cache-tidsstämpeln för det begärda innehållet. Mer information finns i UnixTime-termen ovan.

- CASeconds: anger antalet sekunder som har förflutit sedan cache-tidsstämpeln.

- RTSeconds: anger hur många sekunder som det cachelagrade innehållet ska anses vara färskt. Detta värde beräknas enligt följande: RTSeconds = Max-Age-cache-ålder.

- RTTimePeriod: konverterar det återstående TTL-värdet (det vill säga RTSeconds) till den ungefärliga motsvarigheten till en större enhet (till exempel dagar).

- ExpiresSeconds: anger antalet sekunder kvar före datumet/tiden som anges i `Expires` svars huvudet. Om `Expires` svars huvudet inte ingår i svaret är värdet för den här termen *inget*.

### <a name="sample-response-header"></a>Exempel på svars rubrik

Följande exempel svars huvud visar cache-tillstånd för det begärda innehållet vid den tidpunkt då det begärdes:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

