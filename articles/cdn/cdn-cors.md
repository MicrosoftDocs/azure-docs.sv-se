---
title: Använda Azure CDN med CORS | Microsoft Docs
description: Lär dig hur du använder Azure-Content Delivery Network (CDN) till resurs delning mellan ursprung (CORS).
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f7edf790e526329dd285d03a31137a26220e52ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018655"
---
# <a name="using-azure-cdn-with-cors"></a>Använda Azure CDN med CORS
## <a name="what-is-cors"></a>Vad är CORS?
CORS (Cross Origin Resource Sharing) är en HTTP-funktion som gör det möjligt för ett webbprogram som körs i en domän att komma åt resurser i en annan domän. För att minska risken för skript angrepp över flera webbplatser, implementerar alla moderna webbläsare en säkerhets begränsning som kallas princip för [samma ursprung](https://www.w3.org/Security/wiki/Same_Origin_Policy).  Detta förhindrar att en webb sida anropar API: er i en annan domän.  CORS är ett säkert sätt att tillåta ett ursprung (ursprungs domänen) att anropa API: er i ett annat ursprung.

## <a name="how-it-works"></a>Så här fungerar det
Det finns två typer av CORS-begäranden, *enkla begär Anden* och *komplexa begär Anden.*

### <a name="for-simple-requests"></a>För enkla begär Anden:

1. Webbläsaren skickar CORS-begäran med en ytterligare **källa** för http-begäran. Värdet för den här rubriken är ursprunget som hanterade den överordnade sidan, som definieras som kombinationen av *protokoll,* *domän* och *port.*  När en sida från https \: //www.contoso.com försöker komma åt en användares data i Fabrikam.com ursprung, skickas följande begär ande huvud till fabrikam.com:

   `Origin: https://www.contoso.com`

2. Servern kan svara med något av följande:

   * En **Access-Control-Allow-Origin** -rubrik i sitt svar som anger vilken ursprungs plats som tillåts. Exempel:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * En HTTP-felkod, till exempel 403, om servern inte tillåter Cross-Origin-begäran efter kontroll av käll rubriken

   * En **Access-Control-Allow-Origin** -rubrik med ett jokertecken som tillåter alla ursprung:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>För komplexa begär Anden:

En komplex begäran är en CORS-begäran där webbläsaren krävs för att skicka en *preflight-begäran* (det vill säga en preliminär avsökning) innan den aktuella CORS-begäran skickas. Preflight-begäran ber Server behörigheten om den ursprungliga CORS-begäran kan fortsätta och är en `OPTIONS` begäran till samma URL.

> [!TIP]
> Mer information om CORS-flöden och vanliga fall GRO par finns i [guiden till CORS för REST-API: er](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Scenarier med jokertecken eller enstaka ursprung
CORS på Azure CDN fungerar automatiskt utan ytterligare konfiguration när rubriken **åtkomst kontroll – Tillåt-ursprung** är inställd på jokertecken (*) eller ett enda ursprung.  CDN cachelagrar det första svaret och efterföljande begär Anden kommer att använda samma rubrik.

Om begär Anden redan har gjorts till CDN innan CORS har angetts i ditt ursprung måste du rensa innehållet i slut punkts innehållet för att läsa in innehållet igen med **Access-Control-Allow-Origin** -rubriken.

## <a name="multiple-origin-scenarios"></a>Scenarier med flera ursprung
Om du behöver tillåta en speciell lista över ursprung som ska tillåtas för CORS kan saker bli lite mer komplicerat. Problemet uppstår när CDN cachelagrar rubriken **Access-Control-Allow-Origin** för det första CORS-ursprunget.  När ett annat CORS-ursprung gör en efterföljande begäran kommer CDN att betjäna det cachelagrade **Access-Control-Allow-Origin-** huvudet, som inte matchar.  Det finns flera sätt att åtgärda detta.

### <a name="azure-cdn-standard-profiles"></a>Azure CDN Standard profiler
På Azure CDN Standard från Microsoft kan du skapa en regel i [standard regel motorn](cdn-standard-rules-engine-reference.md) för att kontrol lera **käll** rubriken på begäran. Om det är ett giltigt ursprung anger din regel **Access-Control-Allow-Origin-** huvudet med det önskade värdet. I det här fallet ignoreras rubriken för **Access-Control-Allow-Origin** från filens ursprungs Server och CDN: s regel motor hanterar de tillåtna CORS-ursprungen fullständigt.

![Regel exempel med standard regel motor](./media/cdn-cors/cdn-standard-cors.png)

> [!TIP]
> Du kan lägga till ytterligare åtgärder i regeln för att ändra ytterligare svarshuvuden, till exempel **Access-Control-Allow-Methods**.
> 

På **Azure CDN Standard från Akamai** är den enda mekanismen att tillåta för flera ursprung utan att använda jokertecken för att använda [cachelagring av frågesträngar](cdn-query-string.md). Aktivera inställningen för frågesträngen för CDN-slutpunkten och Använd sedan en unik frågesträng för begär Anden från varje tillåten domän. Detta leder till att CDN cachelagrar ett separat objekt för varje unik frågesträng. Den här metoden är inte idealisk, men eftersom det leder till att flera kopior av samma fil cachelagras i CDN.  

### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium från Verizon
Med hjälp av Verizon Premium Rules-motorn måste du [skapa en regel](./cdn-verizon-premium-rules-engine.md) för att kontrol lera **käll** rubriken på begäran.  Om det är ett giltigt ursprung anger din regel **Access-Control-Allow-Origin-** huvudet med ursprunget i begäran.  Om det ursprung som anges i **ursprungs** huvudet inte är tillåtet, ska regeln utesluta rubriken **Access-Control-Allow-Origin** , som gör att webbläsaren avvisar begäran. 

Det finns två sätt att göra detta med Premium Rules-motorn. I båda fallen ignoreras rubriken för **Access-Control-Allow-Origin** från filens ursprungs Server och CDN: s regel motor hanterar de tillåtna CORS-ursprungen fullständigt.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Ett reguljärt uttryck med alla giltiga ursprung
I det här fallet skapar du ett reguljärt uttryck som innehåller alla ursprung som du vill tillåta: 

```http
https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
```

> [!TIP]
> **Azure CDN Premium från Verizon** använder [perl-kompatibla reguljära uttryck](https://pcre.org/) som sin motor för reguljära uttryck.  Du kan använda ett verktyg som [reguljära uttryck 101](https://regex101.com/) för att validera ditt reguljära uttryck.  Observera att "/"-tecken är giltigt i reguljära uttryck och inte behöver vara undantagna, men det är inte nödvändigt att undanta det tecken som är ett bra tillvägagångs sätt och förväntas av några regex-verifierare.
> 
> 

Om det reguljära uttrycket matchar, kommer din regel att ersätta **Access-Control-Allow-Origin** -huvudet (om det finns) från ursprunget med det ursprung som skickade begäran.  Du kan också lägga till ytterligare CORS-rubriker, till exempel **Access-Control-Allow-Methods**.

![Regel exempel med reguljärt uttryck](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Begär ande huvud regel för varje ursprung.
I stället för reguljära uttryck kan du i stället skapa en separat regel för varje ursprung som du vill tillåta med hjälp av [matchnings villkoret](/previous-versions/azure/mt757336(v=azure.100)#match-conditions)för **förfrågnings huvudet** . Precis som med metoden för reguljära uttryck, ställer enbart regel motorn CORS-huvuden. 

![Regel exempel utan reguljärt uttryck](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> I exemplet ovan talar användningen av jokertecknet * till att regel motorn matchar både HTTP och HTTPS.
> 
>