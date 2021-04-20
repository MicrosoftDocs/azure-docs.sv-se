---
title: Azure API Management principer för flera domäner | Microsoft Docs
description: Lär dig mer om de korsdomänprinciper som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/01/2021
ms.author: apimpm
ms.openlocfilehash: 6f074ff389971fa56da7838a9a46ec5c4d42dc5a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739106"
---
# <a name="api-management-cross-domain-policies"></a>Korsdomänprinciper för API Management
Det här avsnittet innehåller en referens för följande API Management principer. Information om hur du lägger till och konfigurerar principer [finns i Principer i API Management](./api-management-policies.md).

## <a name="cross-domain-policies"></a><a name="CrossDomainPolicies"></a> Principer mellan domäner

- [Tillåt anrop mellan domäner](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – Gör API:et tillgängligt från Adobe Flash- och Microsoft Silverlight-webbläsarbaserade klienter.
- [CORS](api-management-cross-domain-policies.md#CORS) – Lägger till CORS-stöd (Cross-Origin Resource Sharing) för en åtgärd eller ett API för att tillåta anrop mellan domäner från webbläsarbaserade klienter.
- [JSONP](api-management-cross-domain-policies.md#JSONP) – Lägger till stöd för JSON med utfyllnad (JSONP) till en åtgärd eller ett API för att tillåta anrop mellan domäner från JavaScript-webbläsarbaserade klienter.

## <a name="allow-cross-domain-calls"></a><a name="AllowCrossDomainCalls"></a> Tillåt anrop mellan domäner
Använd principen `cross-domain` för att göra API:et tillgängligt från Adobe Flash- och Microsoft Silverlight-webbläsarbaserade klienter.

### <a name="policy-statement"></a>Principutdrag

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>Exempel

```xml
<cross-domain>
        <allow-http-request-headers-from domain='*' headers='*' />
</cross-domain>
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|mellan domäner|Rotelementet. Underordnade element måste följa [specifikationen för Adobe-principfilen över flera domäner.](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)|Yes|

### <a name="usage"></a>Användning
Den här principen kan användas i följande principavsnitt [och](./api-management-howto-policies.md#sections) [omfång](./api-management-howto-policies.md#scopes).

- **Principavsnitt:** inkommande
- **Principomfång:** alla omfång

## <a name="cors"></a><a name="CORS"></a> CORS
Principen `cors` lägger till CORS-stöd (Cross-Origin Resource Sharing) för en åtgärd eller ett API för att tillåta anrop mellan domäner från webbläsarbaserade klienter. 

> [!NOTE]
> Om begäran matchar en åtgärd med en OPTIONS-metod som definierats i API:et körs inte bearbetningslogiken före flygbegäran som är associerad med CORS-principer. Därför kan sådana åtgärder användas för att implementera anpassad bearbetningslogik före flygning.

CORS gör att en webbläsare och en server kan interagera och avgöra om specifika begäranden om korsande ursprung ska tillåtas (d.v.s. XMLHttpRequests-anrop som görs från JavaScript på en webbsida till andra domäner). Detta ger större flexibilitet än att bara tillåta begäranden om samma ursprung, men det är säkrare än att tillåta alla begäranden om korsande ursprung.

Du måste tillämpa CORS-principen för att aktivera den interaktiva konsolen i utvecklarportalen. Mer information finns [i utvecklarportalens](./developer-portal-faq.md#cors) dokumentation.

### <a name="policy-statement"></a>Principsats

```xml
<cors allow-credentials="false|true" terminate-unmatched-request="true|false">
    <allowed-origins>
        <origin>origin uri</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="number of seconds">
        <method>http verb</method>
    </allowed-methods>
    <allowed-headers>
        <header>header name</header>
    </allowed-headers>
    <expose-headers>
        <header>header name</header>
    </expose-headers>
</cors>
```

### <a name="example"></a>Exempel
Det här exemplet visar hur du stöder begäranden före flygning, till exempel sådana med anpassade huvuden eller metoder som inte är GET och POST. Om du vill ha stöd för anpassade rubriker och ytterligare HTTP-verb använder du `allowed-methods` `allowed-headers` avsnitten och som visas i följande exempel.

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <!-- Localhost useful for development -->
        <origin>http://localhost:8080/</origin>
        <origin>http://example.com/</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="300">
        <method>GET</method>
        <method>POST</method>
        <method>PATCH</method>
        <method>DELETE</method>
    </allowed-methods>
    <allowed-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
        <header>x-zumo-version</header>
        <header>x-zumo-auth</header>
        <header>content-type</header>
        <header>accept</header>
    </allowed-headers>
    <expose-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
    </expose-headers>
</cors>
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|Standardvärde|
|----------|-----------------|--------------|-------------|
|cors|Rotelementet.|Yes|Ej tillämpligt|
|tillåtna ursprung|Innehåller `origin` element som beskriver tillåtna ursprung för begäranden mellan domäner. `allowed-origins` kan innehålla antingen ett `origin` enda element som anger att alla ursprung ska `*` tillåtas, eller ett eller `origin` flera element som innehåller en URI.|Yes|Ej tillämpligt|
|ursprung|Värdet kan vara `*` antingen för att tillåta alla ursprung eller en URI som anger ett enda ursprung. URI:en måste innehålla ett schema, en värd och en port.|Yes|Om porten utelämnas i en URI används port 80 för HTTP och port 443 används för HTTPS.|
|allowed-methods|Det här elementet krävs om andra metoder än GET eller POST tillåts. Innehåller `method` element som anger de HTTP-verb som stöds. Värdet anger `*` alla metoder.|No|Om det här avsnittet inte finns stöds GET och POST.|
|metod|Anger ett HTTP-verb.|Minst ett `method` element krävs om avsnittet `allowed-methods` finns.|Ej tillämpligt|
|allowed-headers|Det här elementet `header` innehåller element som anger namn på de huvuden som kan ingå i begäran.|No|Ej tillämpligt|
|exponera huvuden|Det här elementet `header` innehåller element som anger namn på de huvuden som ska vara tillgängliga för klienten.|No|Saknas|
|sidhuvud|Anger ett huvudnamn.|Minst ett `header` element krävs i eller om avsnittet `allowed-headers` `expose-headers` finns.|Ej tillämpligt|

### <a name="attributes"></a>Attribut

|Name|Beskrivning|Krävs|Standardvärde|
|----------|-----------------|--------------|-------------|
|tillåt autentiseringsuppgifter|Huvudet i det första svaret ställs in på värdet för det här attributet och påverkar klientens möjlighet att skicka `Access-Control-Allow-Credentials` autentiseringsuppgifter i begäranden mellan domäner.|No|falskt|
|terminate-unmatched-request|Det här attributet styr bearbetningen av begäranden om korsande ursprung som inte matchar CORS-principinställningarna. När OPTIONS-begäran bearbetas som en begäran före flygresan och inte matchar CORS-principinställningarna: Om attributet är inställt på avslutar du begäran omedelbart med ett tomt `true` 200 OK-svar. Om attributet är inställt på kontrollerar du om det finns andra CORS-principer inom omfång som är direkt underordnade till `false` det inkommande elementet och tillämpar dem.  Om inga CORS-principer hittas avslutar du begäran med ett tomt 200 OK-svar. När GET- eller HEAD-begäran innehåller ursprungshuvudet (och därför bearbetas som en begäran om korsande ursprung) och inte matchar CORS-principinställningarna: Om attributet är inställt på avslutar du begäran omedelbart med ett tomt svar på `true` 200 OK; Om attributet är inställt på tillåter du att begäran fortsätter normalt `false` och inte lägger till CORS-huvuden i svaret.|No|true|
|preflight-result-max-age|Huvudet i det första svaret ställs in på värdet för det här attributet och påverkar användaragentens möjlighet att cachelagra svar `Access-Control-Max-Age` före flygning.|No|0|

### <a name="usage"></a>Användning
Den här principen kan användas i följande principavsnitt [och](./api-management-howto-policies.md#sections) [omfång](./api-management-howto-policies.md#scopes).

- **Principavsnitt:** inkommande
- **Principomfång:** alla omfång

## <a name="jsonp"></a><a name="JSONP"></a> Jsonp
Principen lägger till stöd för JSON med utfyllnad (JSONP) till en åtgärd eller ett API för att tillåta korsdomän-anrop från `jsonp` JavaScript-webbläsarbaserade klienter. JSONP är en metod som används i JavaScript-program för att begära data från en server i en annan domän. JSONP kringgår den begränsning som tillämpas av de flesta webbläsare där åtkomsten till webbsidor måste finnas i samma domän.

### <a name="policy-statement"></a>Principsats

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Exempel

```xml
<jsonp callback-parameter-name="cb" />
```

Om du anropar metoden utan motringningsparametern ?cb=XXX returneras Oformaterad JSON (utan funktionsanropsomslutare).

Om du lägger till motringningsparametern returneras ett JSONP-resultat som omsluter de ursprungliga `?cb=XXX` JSON-resultaten runt återanropsfunktionen som `XYZ('<json result goes here>');`

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|Jsonp|Rotelementet.|Yes|

### <a name="attributes"></a>Attribut

|Name|Beskrivning|Krävs|Standardvärde|
|----------|-----------------|--------------|-------------|
|callback-parameter-name|JavaScript-funktionsanropet över flera domäner föregås av det fullständigt kvalificerade domännamnet där funktionen finns.|Yes|Ej tillämpligt|

### <a name="usage"></a>Användning
Den här principen kan användas i följande [principavsnitt](./api-management-howto-policies.md#sections) och [omfång.](./api-management-howto-policies.md#scopes)

- **Principavsnitt:** utgående
- **Principomfång:** alla omfång

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med principer finns i:

+ [Principer i API Management](api-management-howto-policies.md)
+ [Transformera API:er](transform-api.md)
+ [Principreferens](./api-management-policies.md) för en fullständig lista över principutdrag och deras inställningar
+ [Principexempel](./policy-reference.md)
