---
title: Konfigurera Azure Static Web Apps
description: Lär dig hur du konfigurerar vägar, framtvingar säkerhetsregler och globala inställningar för Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 9494bcc9941491bbb82c6a948dce720cb9e51424
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502291"
---
# <a name="configure-azure-static-web-apps"></a>Konfigurera Azure Static Web Apps

Konfiguration för Azure Static Web Apps definieras i _staticwebapp.config.jspå_ filen, som styr följande inställningar:

- Routning
- Autentisering
- Auktorisering
- Återställningsregler
- Åsidosättningar av HTTP-svar
- Globala HTTP-huvuddefinitioner
- Anpassade MIME-typer

## <a name="file-location"></a>Filplats

Den rekommenderade platsen för _staticwebapp.config.jspå_ finns i mappen som anges som `app_location` i [arbetsflödesfilen](./github-actions-workflow.md). Filen kan dock placeras på valfri plats i programmets källkodsmapp.

Mer information finns i exempelkonfigurationsfilen. [](#example-configuration-file)

> [!IMPORTANT]
> Den [ _routes.jspå_ filen](./routes.md) ignoreras om enstaticwebapp.config.js _på_ finns.

## <a name="routes"></a>Vägar

Med vägregler kan du definiera mönstret för URL:er som ger åtkomst till ditt program till webben. Vägar definieras som en matris med routningsregler. Användningsexempel finns i exempelkonfigurationsfilen. [](#example-configuration-file)

- Regler definieras i `routes` matrisen, även om du bara har en väg.
- Regler körs i den ordning som de visas i `routes` matrisen.
- Regelutvärderingen stoppas vid den första matchningen – routningsreglerna är inte sammankedjade.
- Du har fullständig kontroll över anpassade rollnamn.
  - Det finns några inbyggda rollnamn som innehåller [`anonymous`](./authentication-authorization.md) och [`authenticated`](./authentication-authorization.md) .

Routningen överlappar avsevärt autentiseringen (identifiera användaren) och auktoriseringsbegreppen (tilldela funktioner till användaren). Läs igenom autentiserings- [och auktoriseringsguiden](authentication-authorization.md) tillsammans med den här artikeln.

Standardfilen för statiskt innehåll är _index.html._

## <a name="defining-routes"></a>Definiera vägar

Varje regel består av ett vägmönster, tillsammans med en eller flera av de valfria regelegenskaperna. Vägregler definieras i `routes` matrisen. Användningsexempel finns i exempelkonfigurationsfilen. [](#example-configuration-file)

| Regelegenskap                       | Obligatorisk | Standardvärde                        | Kommentar                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------------- | -------- | ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `route`                             | Ja      | saknas                                  | Vägmönstret som begärdes av anroparen.<ul><li>[Jokertecken](#wildcards) stöds i slutet av vägsökvägarna.<ul><li>Vägadministratören/ matchar _till exempel \*_ valfri väg under _administratörssökvägen._</ul></ul>                                                                                                                                                                                                                                                                                                                                                                                                        |
| `rewrite`                           | Inga       | saknas                                  | Definierar filen eller sökvägen som returneras från begäran.<ul><li>Är ömsesidigt uteslutande för en `redirect` regel<li>Omskrivningsregler ändrar inte webbläsarens plats.<li>Värdena måste vara relativa till appens rot</ul>                                                                                                                                                                                                                                                                                                                                                                                                      |
| `redirect`                          | Inga       | saknas                                  | Definierar filen eller sökvägens omdirigeringsmål för en begäran.<ul><li>Är ömsesidigt uteslutande för en `rewrite` regel.<li>Omdirigeringsregler ändrar webbläsarens plats.<li>Standardsvarskoden är [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302) en (tillfällig omdirigering), men du kan åsidosätta med en [`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301) (permanent omdirigering).</ul>                                                                                                                                                                                                              |
| `allowedRoles`                      | Inga       | Anonym                            | Definierar en lista över rollnamn som krävs för att få åtkomst till en väg. <ul><li>Giltiga tecken är `a-z` , `A-Z` , och `0-9` `_` .<li>Den inbyggda [`anonymous`](./authentication-authorization.md) rollen, , gäller för alla oauterade användare<li>Den inbyggda [`authenticated`](./authentication-authorization.md) rollen, , gäller för alla inloggade användare.<li>Användare måste tillhöra minst en roll.<li>Roller matchas på _OR-basis._<ul><li>Om en användare finns i någon av rollerna i listan beviljas åtkomst.</ul><li>Enskilda användare associeras med roller via [inbjudningar](authentication-authorization.md).</ul> |
| `headers`<a id="route-headers"></a> | Inga       | saknas                                  | En uppsättning [HTTP-huvuden](https://developer.mozilla.org/docs/Web/HTTP/Headers) som lagts till i svaret. <ul><li>Vägspecifika huvuden åsidosätts [`globalHeaders`](#global-headers) när det vägspecifika huvudet är detsamma som det globala huvudet finns i svaret.<li>Om du vill ta bort en rubrik anger du värdet till en tom sträng.</ul>                                                                                                                                                                                                                                                                                          |
| `statusCode`                        | Inga       | `200`, `301` eller `302` för omdirigeringar | [HTTP-statuskoden](https://developer.mozilla.org/docs/Web/HTTP/Status) för svaret.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `methods`                           | Inga       | Alla metoder                          | Lista över förfrågningsmetoder som matchar en väg. Tillgängliga metoder är: `GET` , , , , , , , , `HEAD` och `POST` `PUT` `DELETE` `CONNECT` `OPTIONS` `TRACE` `PATCH` .                                                                                                                                                                                                                                                                                                                                                                                                                                                             |

Varje egenskap har ett specifikt syfte i pipelinen för begäran/svar.

| Syfte                                        | Egenskaper                                                                                   |
| ---------------------------------------------- | -------------------------------------------------------------------------------------------- |
| Matcha vägar                                   | `route`, `methods`                                                                           |
| Auktorisera när en väg matchas             | `allowedRoles`                                                                               |
| Bearbeta när en regel matchas och auktoriserats | `rewrite` (ändrar begäran) <br><br>`redirect`, `headers` , `statusCode` (ändrar svar) |

## <a name="securing-routes-with-roles"></a>Skydda vägar med roller

Vägar skyddas genom att ett eller flera rollnamn läggs till i en regels matris, och användare associeras med `allowedRoles` anpassade roller via [inbjudningar.](./authentication-authorization.md) Användningsexempel finns i exempelkonfigurationsfilen. [](#example-configuration-file)

Som standard tillhör varje användare den inbyggda `anonymous` rollen och alla inloggade användare är medlemmar i `authenticated` rollen.

Om du till exempel vill begränsa en väg till endast autentiserade användare lägger du till den `authenticated` inbyggda rollen i `allowedRoles` matrisen.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

Du kan skapa nya roller efter behov i `allowedRoles` matrisen. Om du vill begränsa en väg till endast administratörer kan du definiera en egen roll med `administrator` namnet , i `allowedRoles` matrisen.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Du har fullständig kontroll över rollnamn. det finns ingen lista som rollerna måste följa.
- Enskilda användare är associerade med roller via [inbjudningar](authentication-authorization.md).

## <a name="wildcards"></a>Jokertecken

Jokerteckenregler matchar alla begäranden i ett vägmönster, stöds endast i slutet av en sökväg och kan filtreras efter filnamnstillägg. Användningsexempel finns i exempelkonfigurationsfilen. [](#example-configuration-file)

Om du till exempel vill implementera vägar för ett kalenderprogram kan du skriva om alla URL:er som finns under _kalendervägen_ för att visa en enda fil.

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

Filen _calendar.htmkan sedan_ använda routning på klientsidan för att visa en annan vy för URL-varianter som , och `/calendar/january/1` `/calendar/2020` `/calendar/overview` .

Du kan filtrera matchningar med jokertecken efter filnamnstillägg. Om du till exempel vill lägga till en regel som endast matchar HTML-filer i en viss sökväg kan du skapa följande regel:

```json
{
  "route": "/articles/*.html",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Om du vill filtrera på flera filnamnstillägg inkluderar du alternativen inom kparenteser, som du ser i det här exemplet:

```json
{
  "route": "/images/thumbnails/*.{png,jpg,gif}",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Vanliga användningsområden för jokervägar är:

- Betjäna en specifik fil för ett helt sökvägsmönster
- Mappa olika HTTP-metoder till ett helt sökvägsmönster
- Framtvinga regler för autentisering och auktorisering
- Implementera specialiserade cachelagringsregler

## <a name="fallback-routes"></a>Reservvägar

Ensidesprogram förlitar sig ofta på routning på klientsidan. Dessa routningsregler på klientsidan uppdaterar webbläsarens fönsterplats utan att göra förfrågningar tillbaka till servern. Om du uppdaterar sidan eller går direkt till URL:er som genereras av routningsregler på klientsidan krävs en reservväg på serversidan för att hantera lämplig HTML-sida (vilket vanligtvis är _index.html_ för din klientapp).

Du kan konfigurera din app att använda regler som implementerar en reservväg enligt följande exempel som använder ett jokertecken med sökväg med filfilter:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  }
}
```

Exempelfilstrukturen nedan. Följande resultat är möjliga med den här regeln.

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

| Begäranden om att...                                         | Returnerar...                                                                                                    | med statusen ... |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------- | ------------------ |
| _/about/_                                              | Filen _/index.html_                                                                                        | `200`              |
| _/images/logo.png_                                     | Bildfilen                                                                                                | `200`              |
| _/images/icon.svg_                                     | Filen _/index.html_ – eftersom _svg-filtillägget_ inte visas i `/images/*.{png,jpg,gif}` filtret | `200`              |
| _/images/unknown.png_                                  | Felet Filen hittades inte                                                                                          | `404`              |
| _/css/unknown.css_                                     | Felet Filen hittades inte                                                                                          | `404`              |
| _/css/global.css_                                      | Formatmallfilen                                                                                           | `200`              |
| Andra filer utanför _mapparna /images_ _eller /css_ | Filen _/index.html_                                                                                        | `200`              |

## <a name="global-headers"></a>Globala huvuden

Avsnittet innehåller en uppsättning HTTP-huvuden som tillämpas på varje svar, såvida de inte åsidosätts av en regel för väghuvuden, annars returneras union av båda huvudena från vägen och de globala `globalHeaders` huvudena. [](https://developer.mozilla.org/docs/Web/HTTP/Headers) [](#route-headers)

Användningsexempel finns i exempelkonfigurationsfilen. [](#example-configuration-file)

Om du vill ta bort en rubrik anger du värdet till en tom sträng ( `""` ).

Några vanliga användningsfall för globala huvuden är:

- Anpassade cachelagringsregler
- Framtvinga säkerhetsprinciper
- Kodningsinställningar

## <a name="response-overrides"></a>Åsidosättningar av svar

Avsnittet `responseOverrides` ger en möjlighet att definiera ett anpassat svar när servern annars skulle returnera en felkod. Användningsexempel finns i exempelkonfigurationsfilen. [](#example-configuration-file)

Följande HTTP-koder kan åsidosätta:

| Statuskod                                                   | Innebörd      | Möjlig orsak                                                                                                                                                                                                                                                                                     |
| ------------------------------------------------------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) | Felaktig begäran  | Ogiltig inbjudningslänk                                                                                                                                                                                                                                                                            |
| [401](https://developer.mozilla.org/docs/Web/HTTP/Status/401) | Behörighet saknas | Begäran till begränsade sidor vid oauticerad                                                                                                                                                                                                                                                  |
| [403](https://developer.mozilla.org/docs/Web/HTTP/Status/403) | Förbjudet    | <ul><li>Användaren är inloggad men har inte de roller som krävs för att visa sidan.<li>Användaren är inloggad men körningen kan inte hämta användarinformation från sina identitetsanspråk.<li>Det finns för många användare som är inloggade på webbplatsen med anpassade roller, och därför kan inte körningen logga in användaren.</ul> |
| [404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) | Hittades inte    | Filen hittades inte                                                                                                                                                                                                                                                                                     |

Följande exempelkonfiguration visar hur du åsidosätter en felkod.

```json
{
  "responseOverrides": {
    "400": {
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

## <a name="example-configuration-file"></a>Exempelkonfigurationsfil

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
      "methods": ["GET"],
      "allowedRoles": ["registeredusers"]
    },
    {
      "route": "/api/*",
      "methods": ["PUT", "POST", "PATCH", "DELETE"],
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
    "400": {
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

| Begäranden om att...                                                    | resulterar i...                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| _/profile_                                                        | Autentiserade användare får filen _/profile/index.htm_ l. Oauthenticerade användare omdirigeras till _/login_.                                                                                                                                                                                                                                                                                                                              |
| _/admin/_                                                         | Autentiserade användare med _administratörsrollen_ får _filen /admin/index.html._ Autentiserade användare som inte _har administratörsrollen_ får felet `403` <sup>1</sup>. Oauthenticerade användare omdirigeras till _/login_.                                                                                                                                                                                                          |
| _/logo.png_                                                       | Visar bilden med en anpassad cacheregel där maxåldern är lite över 182 dagar (15 770 000 sekunder).                                                                                                                                                                                                                                                                                                                                   |
| _/api/admin_                                                      | `GET` begäranden från autentiserade användare i _rollen registrerade användare_ skickas till API:et. Autentiserade användare som inte _är registreradeanvändare_ och icke-autentiserade användare får ett `401` felmeddelande.<br/><br/>`POST`, `PUT` , och begäranden från `PATCH` `DELETE` autentiserade användare i _administratörsrollen_ skickas till API:et. Autentiserade användare som inte _har administratörsrollen_ och oautenterade användare får ett `401` felmeddelande. |
| _/customers/contoso_                                              | Autentiserade användare som antingen tillhör administratörs- _eller customers_contoso-roller_ betjänas av _filen /customers/contoso/index.html._  Autentiserade användare som inte är _administratör_ eller _customers_contoso-roller_ får `403` ett felmeddelande <sup>1</sup>. Oauthenticerade användare omdirigeras till _/login_.                                                                                                                            |
| _/login_                                                          | Oautenterade användare måste autentiseras med GitHub.                                                                                                                                                                                                                                                                                                                                                                             |
| _/.auth/login/twitter_                                            | Eftersom auktorisering med Twitter inaktiveras av vägregeln returneras felet, som faller tillbaka till att betjäna `404` _/index.html med_ en `200` statuskod.                                                                                                                                                                                                                                                                                     |
| _/logout_                                                         | Användare loggas ut från alla autentiseringsproviders.                                                                                                                                                                                                                                                                                                                                                                                          |
| _/calendar/2021/01_                                               | Webbläsaren betjänas av _/calendar.html-filen._                                                                                                                                                                                                                                                                                                                                                                                              |
| _/specials_                                                       | Webbläsaren omdirigeras permanent till _/deals_.                                                                                                                                                                                                                                                                                                                                                                                            |
| _/data.jspå_                                                      | Filen som hanteras med `text/json` MIME-typen.                                                                                                                                                                                                                                                                                                                                                                                               |
| _/about_, eller en mapp som matchar routningsmönster på klientsidan | Filen _/index.html_ får en `200` statuskod.                                                                                                                                                                                                                                                                                                                                                                                    |
| En fil som inte finns i _mappen /images/_                     | Ett `404` fel.                                                                                                                                                                                                                                                                                                                                                                                                                                |

<sup>1 Du</sup> kan ange en anpassad felsida med hjälp av en regel [för åsidosättning av svar.](#response-overrides)

## <a name="restrictions"></a>Begränsningar

Följande begränsningar gäller förstaticwebapps.config.js _på_ filen.

- Maximal filstorlek är 100 KB
- Max 50 distinkta roller

I artikeln [Kvoter finns](quotas.md) allmänna begränsningar och begränsningar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera autentisering och auktorisering](authentication-authorization.md)
