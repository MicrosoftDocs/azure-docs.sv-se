---
title: ta med fil
description: ta med fil
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: include
ms.date: 02/18/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 53d837883daefddd5fa3f0f543eae1d116a5e86a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101102945"
---
| Resurs | Gräns |
| --- | --- |
| Resurser för Azures frontend-dörr per prenumeration | 100 |
| Klient dels värdar som innehåller anpassade domäner per resurs | 500 |
| Regler för routning per resurs | 500 |
| Server dels pooler per resurs | 50 |
| Backend-ändar per backend-pool | 100 |
| Sök vägs mönster som matchar en regel för routning | 25 |
| URL: er i ett enda rensnings anrop för cache | 100 |
| Anpassade brand Väggs regler för webb program per princip | 100 |
| Brand Väggs princip för webb program per prenumeration | 100 |
| Villkor för brand vägg för webbaserade programs matchning per anpassad regel | 10 |
| Webb program brand vägg IP-adressintervall per matchnings villkor | 600 |
| Webb programmets brand vägg sträng matchnings värden per matchnings villkor | 10 |
| Webb programmets brand vägg sträng matchnings värde längd | 256 |
| Brand vägg för webbaserade program POST, parameter namn längd | 256 |
| Webb program brand väggens HTTP-huvud namn längd | 256 |
| Cookie-namn längd för webb program brand vägg | 256 |
| Webb program brand vägg, HTTP-begärans text storlek har kontroller ATS | 128 kB |
| Webb program brand väggens anpassade svars text längd | 2 KB |

### <a name="azure-front-door-standardpremium-preview-service-limits"></a>Tjänst begränsningar för Azure frontend standard/Premium (för hands version)

*** *Högsta **500** totala standard-och Premium-profiler per prenumeration.*

| Resurs | Standard-SKU-gräns | Premium SKU-gräns |
| --- | --- | --- |
| Maximal slut punkt per profil  | 10 | 25 |
| Maximal anpassad domän per profil | 100 | 200 |
| Högsta ursprungs grupp per profil | 100 | 200 |
| Högsta antal hemligheter per profil | 100 | 200 |
| Högsta säkerhets princip per profil | 100 | 200 |
| Högsta regel uppsättning per profil | 100 | 200 |
| Högsta antal regler per regel uppsättning | 100 | 100 |
| Maximalt ursprung per ursprungs grupp | 50 | 50 |
| Maximalt antal vägar per slut punkt | 100 | 200 |
| Villkor för brand vägg för webbaserade programs matchning per anpassad regel | 10 | 10 |
| Webb program brand vägg IP-adressintervall per matchnings villkor | 600 | 600 |
| Webb programmets brand vägg sträng matchnings värden per matchnings villkor | 10 | 10 |
| Webb programmets brand vägg sträng matchnings värde längd | 256 | 256 |
| Brand vägg för webbaserade program POST, parameter namn längd | 256 | 256 |
| Webb program brand väggens HTTP-huvud namn längd | 256 | 256 |
| Cookie-namn längd för webb program brand vägg | 256 | 256|
| Webb program brand vägg, HTTP-begärans text storlek har kontroller ATS | 128 kB | 128 kB |
| Webb program brand väggens anpassade svars text längd | 2 KB | 2 KB |

### <a name="timeout-values"></a>Timeout-värden
#### <a name="client-to-front-door"></a>Klient till front dörr
* Front dörren har en inaktiv TCP-anslutningstimeout på 61 sekunder.

#### <a name="front-door-to-application-back-end"></a>Frontend-dörr till programmets Server del
* Om svaret är ett segmenterat svar returneras en 200 om eller när det första segmentet tas emot.
* När HTTP-begäran har vidarebefordrats till Server delen väntar front dörren i 30 sekunder för det första paketet från Server delen. Sedan returneras ett 503-fel till klienten. Det här värdet kan konfigureras via fältet sendRecvTimeoutSeconds i API: et.
    * För cachelagring kan denna timeout inte konfigureras och så om en begäran cachelagras och den tar över 30 sekunder för det första paketet från Front dörren eller från Server delen, returneras ett 504-fel till klienten. 
* När det första paketet tas emot från Server delen väntar front dörren i 30 sekunder i en tids gräns för inaktivitet. Sedan returneras ett 503-fel till klienten. Detta timeout-värde kan inte konfigureras.
* Front dörren till backend-timeoutvärdet för TCP-sessioner är 90 sekunder.

### <a name="upload-and-download-data-limit"></a>Ladda upp och ladda ned data gräns

|  | Med Chunked Transfer Encoding (common Table EXPRESSIONS) | Utan HTTP-segment |
| ---- | ------- | ------- |
| **Ladda ned** | Det finns ingen gräns för nedladdnings storleken. | Det finns ingen gräns för nedladdnings storleken. |
| **Överför** |    Det finns ingen gräns så länge som varje common Table EXPRESSIONS uppladdning är mindre än 2 GB. | Storleken får inte vara större än 2 GB. |

### <a name="other-limits"></a>Andra gränser
* Maximal URL-storlek – 8 192 byte-anger den högsta tillåtna längden för den råa URL: en (schema + värdnamn + port + sökväg + frågesträng för URL)
* Maximal Frågesträngs storlek – 4 096 byte-anger den maximala längden för frågesträngen, i byte.
* Maximal rubrik storlek för HTTP-svar från hälso avsöknings-URL: 4 096 byte-anger den maximala längden för alla svars huvuden i hälso avsökningar. 
