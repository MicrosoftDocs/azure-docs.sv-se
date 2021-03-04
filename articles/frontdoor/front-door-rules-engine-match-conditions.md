---
title: Regler för Azures frontend-dörr matchar villkor
description: Den här artikeln innehåller en lista över de olika matchnings villkoren som är tillgängliga med motor för Azures front dörrs regler.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: duau
ms.openlocfilehash: a352624fc802e4224aa1b60768c064c0054cdfd6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035882"
---
# <a name="azure-front-door-rules-engine-match-conditions"></a>Motor matchnings villkor för Azures frontend-dörr

I [AFD](front-door-rules-engine.md)-regel motor består en regel av noll eller flera matchnings villkor och en åtgärd. Den här artikeln innehåller detaljerade beskrivningar av de matchnings villkor som du kan använda i AFD-regel motorn.

Den första delen av en regel är ett matchnings villkor eller en uppsättning matchnings villkor. En regel kan bestå av upp till 10 matchnings villkor. Ett matchnings villkor identifierar vissa typer av begär Anden för vilka definierade åtgärder utförs. Om du använder flera matchnings villkor grupperas matchnings villkoren tillsammans med hjälp av och logik. För alla matchnings villkor som stöder flera värden (anges som "blankstegsavgränsad") antas operatorn "OR".

Du kan till exempel använda ett matchnings villkor för att:

- Filtrera förfrågningar baserat på en speciell IP-adress, land eller region.
- Filtrera förfrågningar efter rubrik information.
- Filtrera begär Anden från mobila enheter eller Skriv bords enheter.

Följande matchnings villkor är tillgängliga för användning i Azures motor för front dörrs regler.  

## <a name="device-type"></a>Enhetstyp 

Identifierar förfrågningar som görs från en mobil enhet eller en stationär enhet.  

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|----------------
Lika med, inte lika med | Mobil, stationär

## <a name="post-argument"></a>Publicera argument

Identifierar begär Anden baserat på argument som definierats för metoden POST-begäran som används i begäran. 

#### <a name="required-fields"></a>Obligatoriska fält

Argumentnamn | Operator | Argumentvärde | Skift läges omvandling
--------------|----------|----------------|---------------
Sträng | [Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Gemener, versaler, trim, ta bort blank steg, URL-kod, URL-avkodning

## <a name="query-string"></a>Frågesträng

Identifierar begär Anden som innehåller en speciell frågesträngparametern. Den här parametern har angetts till ett värde som matchar ett angivet mönster. Parametrar för frågesträng (till exempel **parameter = värde**) i URL: en för begäran avgör om det här villkoret är uppfyllt. Detta matchnings villkor identifierar en frågesträngparametern efter dess namn och accepterar ett eller flera värden för parametervärdet.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Frågesträng | Skift läges omvandling
---------|--------------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Gemener, versaler, trim, ta bort blank steg, URL-kod, URL-avkodning

## <a name="remote-address"></a>Fjärradress

Identifierar förfrågningar baserat på beställarens plats eller IP-adress.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|-----------------
Geo-matchning | Landskod
IP-matchning | IP-adress (blankstegsavgränsad)
Ingen geo-matchning | Landskod
Inte IP-matchning | IP-adress (blankstegsavgränsad)

#### <a name="key-information"></a>Viktig information

- Använd CIDR-notering.
- Om du vill ange flera IP-adresser och IP-adressblock använder du ett enda blank steg mellan värdena:
  - **IPv4-exempel**: *1.2.3.4 10.20.30.40* matchar alla begär Anden som kommer från adressen 1.2.3.4 eller 10.20.30.40.
  - **IPv6-exempel**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* matchar alla begär Anden som kommer från adress 1:2:3:4:5:6:7:8 eller 10:20:30:40:50:60:70:80.
- Syntaxen för ett IP-adressblock är bas-IP-adressen följt av ett snedstreck och prefixets storlek. Exempel:
  - **IPv4-exempel**: *5.5.5.64/26* matchar alla begär Anden som kommer från adresser 5.5.5.64 via 5.5.5.127.
  - **IPv6-exempel**: *1:2:3:/48* matchar alla begär Anden som kommer från adresser 1:2:3:0:0:0:0:0 till 1:2:3: FFFF: FFFF: FFFF: FFFF: FFFF.

## <a name="request-body"></a>Begärandetext

Identifierar förfrågningar baserat på en speciell text som visas i bröd texten i begäran.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Begärandetext | Skift läges omvandling
---------|--------------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Gemener, versaler, trim, ta bort blank steg, URL-kod, URL-avkodning

## <a name="request-header"></a>Begärandehuvud

Identifierar begär Anden som använder ett särskilt huvud i begäran.

#### <a name="required-fields"></a>Obligatoriska fält

Huvudnamn | Operator | Huvudvärde | Skift läges omvandling
------------|----------|--------------|---------------
Sträng | [Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Gemener, versaler, trim, ta bort blank steg, URL-kod, URL-avkodning

## <a name="request-method"></a>Metod för begäran

Identifierar begär Anden som använder den angivna förfrågnings metoden.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|----------------
Lika med, inte lika med | HÄMTA, POSTA, PLACERA, TA BORT, HUVUD, ALTERNATIV, SPÅRA

#### <a name="key-information"></a>Viktig information

- Endast metoden GET Request kan generera cachelagrat innehåll i Azures frontend-dörr. Alla andra metoder för begäran är proxy via nätverket. 

## <a name="request-protocol"></a>Protokoll för begäran

Identifierar begär Anden som använder det angivna protokoll som används.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|----------------
Lika med, inte lika med | HTTP, HTTPS

## <a name="request-url"></a>Begärans-URL

Identifierar begär Anden som matchar angiven URL.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Begärans-URL | Skift läges omvandling
---------|-------------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Gemener, versaler, trim, ta bort blank steg, URL-kod, URL-avkodning

#### <a name="key-information"></a>Viktig information

- När du använder det här regel villkoret ska du se till att inkludera protokoll information. Exempel: `https://www.<yourdomain\>.com`.

## <a name="request-file-extension"></a>Begär fil tillägg

Identifierar begär Anden som innehåller det angivna fil namns tillägget i fil namnet i den begärda URL: en.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Anknytning | Skift läges omvandling
---------|-----------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Gemener, versaler, trim, ta bort blank steg, URL-kod, URL-avkodning

#### <a name="key-information"></a>Viktig information

- Inkludera inte en inledande period för tillägg. Använd till exempel *HTML* i stället för *. html*.

## <a name="request-file-name"></a>Fil namn för begäran

Identifierar begär Anden som innehåller det angivna fil namnet i begärd URL.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Filnamn | Skift läges omvandling
---------|-----------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Gemener, versaler, trim, ta bort blank steg, URL-kod, URL-avkodning

#### <a name="key-information"></a>Viktig information

- Om du vill ange flera fil namn avgränsar du varje fil namn genom att trycka på RETUR. 

## <a name="request-path"></a>Sökväg för begäran

Identifierar begär Anden som innehåller den angivna sökvägen i begär ande-URL: en.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värde | Skift läges omvandling
---------|-------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Gemener, versaler, trim, ta bort blank steg, URL-kod, URL-avkodning

#### <a name="key-information"></a>Viktig information

- Observera att matchningen börjar efter den första av sökvägen när du använder det här regel villkoret `/` . Till exempel: `https://www.<yourdomain>.com/folder/page` skulle börja matcha på `folder/page` .

## <a name="standard-operator-list"></a>Lista med standard operatorer

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

För numeriska operatorer som *mindre än* och *större än eller lika med*, baseras jämförelsen på längd. Värdet i matchnings villkoret ska vara ett heltal som är lika med den längd som du vill jämföra. 


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du konfigurerar din första [regel motor](front-door-tutorial-rules-engine.md). 
- Lär dig mer om [regel motor åtgärder](front-door-rules-engine-actions.md)
- Läs mer om [Azures motor för front dörr](front-door-rules-engine.md)
