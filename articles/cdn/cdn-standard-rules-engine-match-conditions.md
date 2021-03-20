---
title: Matchnings villkor i standard regel motorn för Azure CDN | Microsoft Docs
description: Referens dokumentation för matchnings villkor i standard regel motorn för Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b8050b973027ac91ede0ba98f4d1c76831da9828
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "81259942"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Matchnings villkor i standard regel motorn för Azure CDN

I [standard regel motorn](cdn-standard-rules-engine.md) för Azure Content Delivery Network (Azure CDN) består en regel av ett eller flera matchnings villkor och en åtgärd. Den här artikeln innehåller detaljerade beskrivningar av de matchnings villkor som du kan använda i standard regel motorn för Azure CDN.

Den första delen av en regel är ett matchnings villkor eller en uppsättning matchnings villkor. I standard regel motorn för Azure CDN kan varje regel ha upp till fyra matchnings villkor. Ett matchnings villkor identifierar vissa typer av begär Anden för vilka definierade åtgärder utförs. Om du använder flera matchnings villkor grupperas matchnings villkoren tillsammans med hjälp av och logik.

Du kan till exempel använda ett matchnings villkor för att:

- Filtrera förfrågningar baserat på en speciell IP-adress, land eller region.
- Filtrera förfrågningar efter rubrik information.
- Filtrera begär Anden från mobila enheter eller Skriv bords enheter.

## <a name="match-conditions"></a>Matchnings villkor

Följande matchnings villkor är tillgängliga för användning i standard regel motorn för Azure CDN. 

### <a name="device-type"></a>Enhetstyp 

Identifierar förfrågningar som görs från en mobil enhet eller en stationär enhet.  

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|----------------
Lika med, inte lika med | Mobil, stationär

### <a name="http-version"></a>HTTP-version

Identifierar begär Anden baserat på HTTP-versionen av begäran.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|----------------
Lika med, inte lika med | 2,0, 1,1, 1,0, 0,9, alla

### <a name="request-cookies"></a>Begär cookies

Identifierar förfrågningar baserat på cookie-information i inkommande begäran.

#### <a name="required-fields"></a>Obligatoriska fält

Namn på cookie | Operator | Cookie-värde | Skift läges omvandling
------------|----------|--------------|---------------
Sträng | [Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

#### <a name="key-information"></a>Viktig information

- Du kan inte använda jokertecken (inklusive asterisker ( \* )) när du anger ett cookie-namn. du måste använda ett exakt cookie-namn.
- Du kan bara ange ett enda cookie-namn per instans av matchnings villkoret.
- Cookie-namn jämförelser är inte Skift läges känsliga.
- Om du vill ange flera cookie-värden använder du ett enda blank steg mellan varje cookie-värde. 
- Cookie-värden kan dra nytta av jokertecken.
- Om ett jokertecken inte har angetts uppfyller det här matchnings villkoret bara en exakt matchning. Till exempel kommer "värde" att matcha "värde" men inte "värde1". 

### <a name="post-argument"></a>Publicera argument

Identifierar begär Anden baserat på argument som definierats för metoden POST-begäran som används i begäran. 

#### <a name="required-fields"></a>Obligatoriska fält

Argumentnamn | Operator | Argumentvärde | Skift läges omvandling
--------------|----------|----------------|---------------
Sträng | [Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

### <a name="query-string"></a>Frågesträng

Identifierar begär Anden som innehåller en speciell frågesträngparametern. Den här parametern har angetts till ett värde som matchar ett angivet mönster. Parametrar för frågesträng (till exempel **parameter = värde**) i URL: en för begäran avgör om det här villkoret är uppfyllt. Detta matchnings villkor identifierar en frågesträngparametern efter dess namn och accepterar ett eller flera värden för parametervärdet.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Frågesträng | Skift läges omvandling
---------|--------------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

### <a name="remote-address"></a>Fjärradress

Identifierar förfrågningar baserat på beställarens plats eller IP-adress.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|-----------------
Valfri | Ej tillämpligt
Geo-matchning | Landskod
IP-matchning | IP-adress (blankstegsavgränsad)
Inte alla | Ej tillämpligt
Ingen geo-matchning | Landskod
Inte IP-matchning | IP-adress (blankstegsavgränsad)

#### <a name="key-information"></a>Viktig information

- Använd CIDR-notering.
- Om du vill ange flera IP-adresser och IP-adressblock använder du ett enda blank steg mellan värdena:
  - **IPv4-exempel**: *1.2.3.4 10.20.30.40* matchar alla begär Anden som kommer från adressen 1.2.3.4 eller 10.20.30.40.
  - **IPv6-exempel**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* matchar alla begär Anden som kommer från adress 1:2:3:4:5:6:7:8 eller 10:20:30:40:50:60:70:80.
- Syntaxen för ett IP-adressblock är bas-IP-adressen följt av ett snedstreck och prefixets storlek. Exempel:
  - **IPv4-exempel**: *5.5.5.64/26* matchar alla begär Anden som kommer från adresser 5.5.5.64 via 5.5.5.127.
  - **IPv6-exempel**: *1:2:3:/48* matchar alla begär Anden som kommer från adresser 1:2:3:0:0:0:0:0 till och med 1:2: 3: FFFF: FFFF: FFFF: FFFF: FFFF.

### <a name="request-body"></a>Begärandetext

Identifierar förfrågningar baserat på en speciell text som visas i bröd texten i begäran.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Begärandetext | Skift läges omvandling
---------|--------------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

### <a name="request-header"></a>Begärandehuvud

Identifierar begär Anden som använder ett särskilt huvud i begäran.

#### <a name="required-fields"></a>Obligatoriska fält

Huvudnamn | Operator | Huvudvärde | Skift läges omvandling
------------|----------|--------------|---------------
Sträng | [Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

### <a name="request-method"></a>Metod för begäran

Identifierar begär Anden som använder den angivna förfrågnings metoden.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|----------------
Lika med, inte lika med | HÄMTA, POSTA, PLACERA, TA BORT, HUVUD, ALTERNATIV, SPÅRA

#### <a name="key-information"></a>Viktig information

- Endast GET Request-metoden kan generera cachelagrat innehåll i Azure CDN. Alla andra metoder för begäran är proxy via nätverket. 

### <a name="request-protocol"></a>Protokoll för begäran

Identifierar begär Anden som använder det angivna protokoll som används.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|----------------
Lika med, inte lika med | HTTP, HTTPS

### <a name="request-url"></a>Begärans-URL

Identifierar begär Anden som matchar angiven URL.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Begärans-URL | Skift läges omvandling
---------|-------------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

#### <a name="key-information"></a>Viktig information

- När du använder det här regel villkoret ska du se till att inkludera protokoll information. Exempel: *https://www . \<yourdomain\> . com*.

### <a name="url-file-extension"></a>URL-filtillägg

Identifierar begär Anden som innehåller det angivna fil namns tillägget i fil namnet i den begärda URL: en.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Anknytning | Skift läges omvandling
---------|-----------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

#### <a name="key-information"></a>Viktig information

- Inkludera inte en inledande period för tillägg. Använd till exempel *HTML* i stället för *. html*.

### <a name="url-file-name"></a>URL-filnamn

Identifierar begär Anden som innehåller det angivna fil namnet i begärd URL.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Filnamn | Skift läges omvandling
---------|-----------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

#### <a name="key-information"></a>Viktig information

- Om du vill ange flera fil namn avgränsar du varje fil namn med ett enda blank steg. 

### <a name="url-path"></a>URL-sökväg

Identifierar begär Anden som innehåller den angivna sökvägen i begär ande-URL: en.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värde | Skift läges omvandling
---------|-------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

#### <a name="key-information"></a>Viktig information

- Ett fil namns värde kan dra nytta av jokertecken. Varje fil namns mönster kan till exempel bestå av en eller flera asterisker (*), där varje asterisk matchar en sekvens med ett eller flera tecken.

## <a name="reference-for-rules-engine-match-conditions"></a>Referens för regel motorns matchnings villkor

### <a name="standard-operator-list"></a>Lista med standard operatorer

Följande operatorer är giltiga för regler som accepterar värden från standard operator listan:

- Valfri
- Lika med 
- Innehåller 
- Börjar med 
- Slutar med 
- Mindre än
- Mindre än eller lika med
- Större än
- Större än eller lika med
- Inte alla
- Innehåller inte
- Börjar inte med 
- Slutar inte med 
- Inte mindre än
- Inte mindre än eller lika med
- Inte större än
- Inte större än eller lika med

För numeriska operatorer som *mindre än* och *större än eller lika med*, baseras jämförelsen på längd. I det här fallet ska värdet i matchnings villkoret vara ett heltal som är lika med den längd som du vill jämföra. 

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure CDN](cdn-overview.md)
- [Referens för standardregelmotor](cdn-standard-rules-engine-reference.md)
- [Åtgärder i standard regel motorn](cdn-standard-rules-engine-actions.md)
- [Kräv HTTPS med hjälp av standardregelmotorn](cdn-standard-rules-engine.md)
