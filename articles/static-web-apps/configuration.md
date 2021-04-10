---
title: Konfigurera statisk Azure-Web Apps
description: Lär dig hur du konfigurerar vägar, tillämpar säkerhets regler och globala inställningar för Azures statiska Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: cshoe
ms.openlocfilehash: b6779de0203246a60bdfa60ea110a0f0d5f26ff3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106457"
---
# <a name="configure-azure-static-web-apps"></a>Konfigurera statisk Azure-Web Apps

Konfiguration av statisk Web Apps i Azure definieras i _staticwebapp.config.jspå_ filen, som styr följande inställningar:

- Routning
- Autentisering
- Auktorisering
- Återgångsregler
- Åsidosättningar av HTTP-svar
- Globala definitioner för HTTP-huvud
- Anpassade MIME-typer

## <a name="file-location"></a>Fil Sök väg

Den rekommenderade platsen för _staticwebapp.config.jspå_ finns i den angivna mappen som `app_location` i [arbets flödes filen](./github-actions-workflow.md). Filen kan dock placeras var som helst i din program käll kods mapp.

Mer information finns i [exempel konfigurations](#example-configuration-file) filen.

> [!IMPORTANT]
> [ _routes.jspå_ filen](./routes.md) ignoreras om en _staticwebapp.config.js_ finns.

## <a name="routes"></a>Vägar

Med flödes regler kan du definiera mönstret för URL: er som tillåter åtkomst till ditt program till webben. Vägar definieras som en matris med regler för routning. Se [exempel på konfigurations filen](#example-configuration-file) för användnings exempel.

- Regler körs i samma ordning som de visas i `routes` matrisen.
- Regel utvärderingen stoppas vid den första matchningen-routningsregler sammanfogas inte.
- Du har fullständig kontroll över anpassade roll namn.
  - Det finns några inbyggda roll namn som inkluderar [`anonymous`](./authentication-authorization.md) och [`authenticated`](./authentication-authorization.md) .

Operationsföljden avser avsevärt överlappande av autentisering (identifierar användaren) och auktoriseringen (tilldelning av användarens förmågor). Se till att läsa guiden för [autentisering och auktorisering](authentication-authorization.md) tillsammans med den här artikeln.

Standard filen för statiskt innehåll är *index.html* -filen.

## <a name="defining-routes"></a>Definiera vägar

Varje regel består av ett väg mönster, tillsammans med en eller flera av de valfria regel egenskaperna. Se [exempel på konfigurations filen](#example-configuration-file) för användnings exempel.

| Regel egenskap  | Obligatorisk | Standardvärde | Kommentar                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Ja      | saknas          | Det väg mönster som anroparen begärt.<ul><li>[Jokertecken](#wildcards) stöds i slutet av väg Sök vägar.<ul><li>Route _admin/ \*_ matchar till exempel alla vägar under _admin_ -sökvägen.</ul></ul>|
| `rewrite`        | Inga       | saknas          | Definierar filen eller sökvägen som returneras från begäran.<ul><li>Kan inte anges samtidigt till en `redirect` regel<li>Omskrivnings regler ändrar inte webbläsarens plats.<li>Värdena måste vara relativa till appens rot</ul>  |
| `redirect`        | Inga       | saknas          | Definierar den fil eller sökväg som omdirigerar målet för en begäran.<ul><li>Är ömsesidigt uteslutande för en `rewrite` regel.<li>Omdirigera regler ändra webbläsarens plats.<li>Standard svars koden är en [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302) (tillfällig omdirigering), men du kan åsidosätta med en [`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301) (permanent omdirigering).</ul> |
| `allowedRoles` | Inga       | antal     | Definierar en lista över roll namn som krävs för att få åtkomst till en väg. <ul><li>Giltiga tecken är `a-z` , `A-Z` , `0-9` och `_` .<li>Den inbyggda rollen, [`anonymous`](./authentication-authorization.md) gäller för alla oautentiserade användare<li>Den inbyggda rollen [`authenticated`](./authentication-authorization.md) gäller för alla inloggade användare.<li>Användarna måste tillhöra minst en roll.<li>Roller matchas på en _eller_ -basis.<ul><li>Om en användare finns i någon av rollerna i listan beviljas åtkomst.</ul><li>Enskilda användare är kopplade till roller genom [inbjudningar](authentication-authorization.md).</ul> |
| `headers`<a id="route-headers"></a> | Inga | saknas | Uppsättning [http-huvuden](https://developer.mozilla.org/docs/Web/HTTP/Headers) som läggs till i svaret. <ul><li>Väg-/regionsspecifika huvuden åsidosätter [`globalHeaders`](#global-headers) när det väg bara huvudet är samma som den globala rubriken är i svaret.<li>Om du vill ta bort en rubrik ställer du in värdet på en tom sträng.</ul> |
| `statusCode`   | Inga       | `200`, `301` eller `302` för omdirigeringar | [HTTP-statuskod](https://developer.mozilla.org/docs/Web/HTTP/Status) för svaret. |
| `methods` | Inga | Alla metoder | Lista över metoder för begäran som matchar en väg. Tillgängliga metoder är:,,,,,,, `GET` `HEAD` `POST` `PUT` `DELETE` `CONNECT` `OPTIONS` `TRACE` och `PATCH` . |

Varje egenskap har ett specifikt syfte i pipelinen för begäran/svar.

| Syfte | Egenskaper |
|---|---|
| Matcha vägar | `route`, `methods` |
| Auktorisera när en väg har matchats | `allowedRoles` |
| Process när en regel har matchats och godkänts | `rewrite` (ändrar begäran) <br><br>`redirect`, `headers` , `statusCode` (ändrar svar) |

## <a name="securing-routes-with-roles"></a>Skydda vägar med roller

Vägar skyddas genom att lägga till ett eller flera rollnamn i en regels `allowedRoles` matris, och användare är kopplade till anpassade roller via [inbjudningar](./authentication-authorization.md). Se [exempel på konfigurations filen](#example-configuration-file) för användnings exempel.

Som standard tillhör alla användare den inbyggda `anonymous` rollen och alla inloggade användare är medlemmar i `authenticated` rollen.

Om du till exempel vill begränsa en väg till endast autentiserade användare lägger du till den inbyggda `authenticated` rollen i `allowedRoles` matrisen.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

Du kan skapa nya roller efter behov i `allowedRoles` matrisen. För att begränsa en väg till endast administratörer kan du definiera en egen roll med namnet `administrator` , i `allowedRoles` matrisen.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Du har fullständig kontroll över roll namn. Det finns ingen lista som dina roller måste följa.
- Enskilda användare är kopplade till roller genom [inbjudningar](authentication-authorization.md).

## <a name="wildcards"></a>Jokertecken

Regler för jokertecken matchar alla begär anden i ett väg mönster, stöds bara i slutet av en sökväg och kan filtreras efter fil namns tillägg. Se [exempel på konfigurations filen](#example-configuration-file) för användnings exempel.

Om du till exempel vill implementera vägar för ett kalender program kan du skriva om alla URL: er som ligger under _kalender_ vägen för att hantera en enda fil.

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

_calendar.html_ -filen kan sedan använda routning på klient sidan för att hantera en annan vy för URL-variationer som `/calendar/january/1` , `/calendar/2020` och `/calendar/overview` .

Du kan filtrera jokertecken matchningar med fil namns tillägget. Om du till exempel vill lägga till en regel som bara matchar HTML-filer i en specifik sökväg kan du skapa följande regel:

```json
{
  "route": "/articles/*.html",
  "headers" : {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Om du vill filtrera på flera fil namns tillägg inkluderar du alternativen inom klammerparenteser, som du ser i det här exemplet:

```json
{
  "route": "/images/thumbnails/*.{png,jpg,gif}",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Vanliga användnings fall för jokertecken är:

- Att betjäna en speciell fil för ett helt Sök vägs mönster
- Mappa olika HTTP-metoder till en hel Sök vägs mönster
- Framtvinga autentiserings-och auktoriseringsregler
- Implementera specialiserade regler för cachelagring

## <a name="fallback-routes"></a>Reserv vägar

Program med en enda sida är ofta beroende av routning på klient sidan. Dessa regler för routning av klient sidan uppdaterar webbläsarens fönster plats utan att göra förfrågningar tillbaka till servern. Om du uppdaterar sidan, eller navigerar direkt till URL: er som genereras av klient sidans regler för routning, krävs en återställnings väg på Server sidan för att kunna hantera lämplig HTML-sida (som vanligt vis är _index.html_ för din app på klient sidan).

Du kan konfigurera appen så att den använder regler som implementerar en återställnings väg som visas i följande exempel som använder jokertecken för en sökväg med fil filter:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  }
}
```

Exempel filens struktur nedan är följande resultat som är möjliga med den här regeln.

```files
├── images
│   ├── logo.png
│   ├── headshot.jpg
│   └── screenshot.gif
│
├── css
│   └── global.css
│
└── index.html
```

| Begär anden till... | Returnerar... | med statusen... |
| --- | --- | --- |
| *tar* | Filen */index.html* | `200` |
| */images/logo.png* | Avbildnings filen  | `200` |
| */images/icon.svg* | */index.html* -filen – eftersom *SVG* -filtillägget inte visas i `/images/*.{png,jpg,gif}` filtret   | `200` |
| */images/unknown.png* | Det gick inte att hitta filen  | `404` |
| */css/unknown.css* | Det gick inte att hitta filen  | `404` |
| */css/global.css* | Filen Stylesheet | `200` |
| Andra filer utanför */images* -eller */CSS* -mapparna | Filen */index.html* | `200` |

## <a name="global-headers"></a>Globala rubriker

`globalHeaders`Avsnittet innehåller en uppsättning [http-huvuden](https://developer.mozilla.org/docs/Web/HTTP/Headers) som används för varje svar, om inte åsidosätts av en [väg huvud](#route-headers) regel, annars returneras unionen av både huvudena från vägen och de globala huvudena.

Se [exempel på konfigurations filen](#example-configuration-file) för användnings exempel.

Om du vill ta bort en rubrik ställer du in värdet på en tom sträng ( `""` ).

Några vanliga användnings fall för globala huvuden är:

- Anpassade cachelagringsregler
- Tvinga säkerhets principer
- Kodnings inställningar

## <a name="response-overrides"></a>Åsidosättningar för svar

`responseOverrides`Avsnittet ger en möjlighet att definiera ett anpassat svar när servern annars skulle returnera en felkod. Se [exempel på konfigurations filen](#example-configuration-file) för användnings exempel.

Följande HTTP-koder är tillgängliga för åsidosättning:

| Statuskod | Innebörd | Möjlig orsak |
| --- | --- | --- |
| [400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) | Felaktig begäran | Ogiltig länk till inbjudan |
| [401](https://developer.mozilla.org/docs/Web/HTTP/Status/401) | Behörighet saknas | Begäran till begränsade sidor vid oautentiserad |
| [403](https://developer.mozilla.org/docs/Web/HTTP/Status/403) | Förbjudet |<ul><li>Användaren är inloggad men har inte de roller som krävs för att visa sidan.<li>Användaren är inloggad men körningen kan inte hämta användar information från sina identitets anspråk.<li>Det finns för många användare inloggade på platsen med anpassade roller, och därför kan inte körningen logga in användaren.</ul> |
| [404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) | Hittades inte | Filen hittades inte |

Följande exempel på konfiguration visar hur du åsidosätter en felkod.

```json
{
    "responseOverrides": {
        "400" : {
            "rewrite": "/invalid-invitation-error.html",
            "statusCode": 200
        },
        "401": {
            "statusCode": 302,
            "redirect": "/login"
        },
        "403": {
            "rewrite": "/custom-forbidden-page.html",
            "statusCode": 200
        },
        "404": {
            "rewrite": "/custom-404.html",
            "statusCode": 200
        }
    }
}
```

## <a name="example-configuration-file"></a>Exempel på konfigurations fil

```json
{
    "routes": [
        {
            "route": "/profile",
            "allowedRoles": ["authenticated"]
        },
        {
            "route": "/admin/*",
            "allowedRoles": ["administrator"]
        },
        {
            "route": "/images/*",
            "headers": {
                "cache-control": "must-revalidate, max-age=15770000"
            }
        },
        {
            "route": "/api/*",
            "methods": [ "GET" ],
            "allowedRoles": ["registeredusers"]
        },
        {
            "route": "/api/*",
            "methods": [ "PUT", "POST", "PATCH", "DELETE" ],
            "allowedRoles": ["administrator"]
        },
        {
            "route": "/api/*",
            "allowedRoles": ["authenticated"]
        },
        {
            "route": "/customers/contoso",
            "allowedRoles": ["administrator", "customers_contoso"]
        },
        {
            "route": "/login",
            "rewrite": "/.auth/login/github"
        },
        {
            "route": "/.auth/login/twitter",
            "statusCode": 404
        },
        {
            "route": "/logout",
            "redirect": "/.auth/logout"
        },
        {
            "route": "/calendar/*",
            "rewrite": "/calendar.html"
        },
        {
            "route": "/specials",
            "redirect": "/deals",
            "statusCode": 301
        }
    ],
    "navigationFallback": {
      "rewrite": "index.html",
      "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
    },
    "responseOverrides": {
        "400" : {
            "rewrite": "/invalid-invitation-error.html"
        },
        "401": {
            "redirect": "/login",
            "statusCode": 302
        },
        "403": {
            "rewrite": "/custom-forbidden-page.html"
        },
        "404": {
            "rewrite": "/404.html"
        }
    },
    "globalHeaders": {
        "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
    },
    "mimeTypes": {
        ".json": "text/json"
    }
}
```

Granska följande scenarier baserat på konfigurationen ovan.

| Begär anden till... | resultat i... |
| --- | --- |
| _/Profile_ | Autentiserade användare betjänar filen _/profile/index.html_ . Oautentiserade användare omdirigeras till _/login_. |
| _/admin_ | Autentiserade användare i rollen _administratör_ betjänar filen _/admin/index.html_ . Autentiserade användare som inte finns i _Administratörs_ rollen behandlas som `403` fel <sup>1</sup>. Oautentiserade användare omdirigeras till _/login_. |
| _/logo.png_ | Hanterar avbildningen med en anpassad cache-regel där maximal ålder är en aning över 182 dagar (15 770 000 sekunder). |
| _/api/admin_ | `GET` begär Anden från autentiserade användare i _registeredusers_ -rollen skickas till API: et. Autentiserade användare som inte finns i _registeredusers_ -rollen och oautentiserade användare hanteras som ett `401` fel.<br/><br/>`POST`, `PUT` , `PATCH` och `DELETE` begär Anden från autentiserade användare i rollen _administratör_ skickas till API: et. Autentiserade användare som inte tillhör rollen _administratör_ och oautentiserade användare hanteras som ett `401` fel. |
| _/customers/contoso_ | Autentiserade användare som tillhör antingen _Administratörs_ -eller _kund \_ contoso_ -roller betjänar _/Customers/contoso/index.html_ -filen. Autentiserade användare som inte är i _Administratörs_ -eller _kund \_ contoso_ -roller behandlas som `403` fel <sup>1</sup>. Oautentiserade användare omdirigeras till _/login_. |
| _/login_ | Oautentiserade användare ifrågasätts att autentisera med GitHub. |
| _/.auth/login/twitter_ | Som auktorisering med Twitter har inaktiverats av väg regeln `404` returneras ett fel som går tillbaka till att betjäna _/index.html_ med en `200` status kod. |
| _/logout_ | Användare loggas ut från en autentiseringsprovider. |
| _/calendar/2021/01_ | Webbläsaren betjänar filen _/calendar.html_ . |
| _/specials_ | Webbläsaren omdirigeras permanent till _/Deals_. |
| _/data.jspå_ | Filen som hanteras med `text/json` MIME-typen. |
| _/About_ eller en mapp som matchar klient sidans flödes mönster | _/index.html-_ filen hanteras med en `200` status kod. |
| En icke-befintlig fil i mappen _/images/_ | Ett `404` fel. |

<sup>1</sup> du kan ange en anpassad felsida genom att använda en [regel för åsidosättning av svar](#response-overrides).

## <a name="restrictions"></a>Begränsningar

Följande begränsningar finns för _staticwebapps.config.jsi_ filen.

- Maximal fil storlek är 100 KB
- Max 50 distinkta roller

Se [artikeln om kvoter](quotas.md) för allmänna begränsningar och begränsningar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera autentisering och auktorisering](authentication-authorization.md)
