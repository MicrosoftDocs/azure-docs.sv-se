---
title: Vägar i Azures statiska Web Apps
description: Läs om regler för routning av backend-regler och hur du skyddar vägar med roller.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 5cbcbcf8914a663a6d039abecd6a4488eaf677b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739652"
---
# <a name="routes-in-azure-static-web-apps-preview"></a>Vägar i för hands versionen av Azure statisk Web Apps

> [!IMPORTANT]
> Funktioner som definieras i *routes.jspå* filen är nu föråldrade och implementeras bättre i [konfigurations filen](./configuration.md#routes)för Azure statisk Web Apps.

Routning i Azures statiska Web Apps definierar regler för Routning och auktorisering av både statiskt innehåll och API: er<sup>1</sup>. Reglerna definieras som en uppsättning regler i _routes.js_ i filen.

- _routes.js_ filen måste finnas i roten i appens version av programartefakt.
- Regler körs i samma ordning som de visas i `routes` matrisen.
- Regel utvärderingen stoppas vid den första matchningen. Routningsregler sammanställs inte tillsammans.
- Roller definieras i _routes.jspå_ filen och användare är kopplade till roller via [inbjudningar](authentication-authorization.md).
- Du har fullständig kontroll över roll namn.

Ämnet i routningen överlappar avsevärt med autentiserings-och auktoriserings koncept. Se till att läsa guiden för [autentisering och auktorisering](authentication-authorization.md) tillsammans med den här artikeln.

Mer information finns i [exempel cirkulations filen](#example-route-file) .

## <a name="location"></a>Location

> [!IMPORTANT]
> Funktioner som definieras i *routes.jspå* filen är nu föråldrade och implementeras bättre i [konfigurations filen](./configuration.md#routes)för Azure statisk Web Apps.

_routes.js_ filen måste finnas i roten i appens version av programartefakt. Om din webbapp innehåller ett build-steg som kopierar skapade filer från en speciell mapp till din version av en artefakt, måste _routes.js_ filen finnas i den specifika mappen.

I följande tabell visas en lista över lämpliga platser för att lägga _routes.jspå_ en fil för ett antal klient dels ramverk och bibliotek.

|Ramverk/bibliotek | Location  |
|---------|----------|
| Angular | _tillgångar_   |
| React   | _public_  |
| Svelte  | _public_   |
| Vue     | _public_ |
| Blazor  | _wwwroot_ |

Tabellen ovan är bara representativ för några ramverk och bibliotek som är kompatibla med Azures statiska Web Apps. Mer information hittar du i [Konfigurera front-end-ramverk och-bibliotek](./front-end-frameworks.md) .

## <a name="defining-routes"></a>Definiera vägar

> [!IMPORTANT]
> Funktioner som definieras i *routes.jspå* filen är nu föråldrade och implementeras bättre i [konfigurations filen](./configuration.md#routes)för Azure statisk Web Apps.

Vägar definieras i _routes.jspå_ filen som en matris med väg regler i `routes` egenskapen. Varje regel består av ett väg mönster, tillsammans med en eller flera av de valfria regel egenskaperna. Se [exempel på väg filen](#example-route-file) för användnings exempel.

| Regel egenskap  | Obligatorisk | Standardvärde | Kommentar                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Ja      | saknas          | Det väg mönster som anroparen begärt.<ul><li>[Jokertecken](#wildcards) stöds i slutet av väg Sök vägar. Route _admin/ \*_ matchar till exempel alla vägar under _admin_ -sökvägen.<li>En vägs standard fil är _index.html_.</ul>|
| `serve`        | Inga       | saknas          | Definierar filen eller sökvägen som returneras från begäran. Fil Sök vägen och namnet kan inte vara samma som den begärda sökvägen. Om ett `serve` värde inte är definierat används den begärda sökvägen. QueryString-parametrar stöds inte. `serve` värdena måste peka på faktiska filer.  |
| `allowedRoles` | Inga       | antal     | En matris med roll namn. <ul><li>Giltiga tecken är `a-z` , `A-Z` , `0-9` och `_` .<li>Den inbyggda rollen `anonymous` gäller för alla oautentiserade användare.<li>Den inbyggda rollen `authenticated` gäller för alla inloggade användare.<li>Användarna måste tillhöra minst en roll.<li>Roller matchas på en _eller_ -basis. Om en användare finns i någon av rollerna i listan beviljas åtkomst.<li>Enskilda användare är kopplade till roller genom [inbjudningar](authentication-authorization.md).</ul> |
| `statusCode`   | Inga       | 200           | [Http-status kod](https://wikipedia.org/wiki/List_of_HTTP_status_codes) svaret för begäran. |

## <a name="securing-routes-with-roles"></a>Skydda vägar med roller

> [!IMPORTANT]
> Funktioner som definieras i *routes.jspå* filen är nu föråldrade och implementeras bättre i [konfigurations filen](./configuration.md#routes)för Azure statisk Web Apps.

Vägar skyddas genom att lägga till ett eller flera roll namn i en regels `allowedRoles` matris. Se [exempel på väg filen](#example-route-file) för användnings exempel.

Som standard tillhör alla användare den inbyggda `anonymous` rollen och alla inloggade användare är medlemmar i `authenticated` rollen. Om du till exempel vill begränsa en väg till endast autentiserade användare lägger du till den inbyggda `authenticated` rollen i `allowedRoles` matrisen.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

Du kan skapa nya roller efter behov i `allowedRoles` matrisen. Om du vill begränsa en väg till endast administratörer kan du definiera en `administrator` roll i `allowedRoles` matrisen.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Du har fullständig kontroll över roll namn. Det finns ingen lista som dina roller måste följa.
- Enskilda användare är kopplade till roller genom [inbjudningar](authentication-authorization.md).

## <a name="wildcards"></a>Jokertecken

> [!IMPORTANT]
> Funktioner som definieras i *routes.jspå* filen är nu föråldrade och implementeras bättre i [konfigurations filen](./configuration.md#routes)för Azure statisk Web Apps.

Jokertecken matchar alla begär Anden under ett givet väg mönster. Om du definierar ett `serve` värde i regeln, hanteras den namngivna filen eller sökvägen som svar.

Om du till exempel vill implementera vägar för ett kalender program kan du mappa alla URL: er som faller under _kalender_ vägen för att betjäna en enda fil.

```json
{
  "route": "/calendar/*",
  "serve": "/calendar.html"
}
```

_calendar.html_ -filen kan sedan använda routning på klient sidan för att hantera en annan vy för URL-variationer som `/calendar/january/1` , `/calendar/2020` och `/calendar/overview` .

Du kan också skydda vägar med jokertecken. I följande exempel kräver alla filer som begärs under _admin_ -sökvägen en autentiserad användare som är medlem i rollen _administratör_ .

```json
{
  "route": "/admin/*",
  "allowedRoles": ["administrator"]
}
```

> [!NOTE]
> Begär Anden för filer som en hanterad fil refererar till utvärderas endast för verifierings kontroller. Till exempel kan begär anden till en CSS-fil under en sökväg för jokertecken betjäna CSS-filen och inte vad som har definierats som `serve` filen. CSS-filen behandlas så länge användaren uppfyller de nödvändiga kraven för autentisering.

## <a name="fallback-routes"></a>Reserv vägar

> [!IMPORTANT]
> Funktioner som definieras i *routes.jspå* filen är nu föråldrade och implementeras bättre i [konfigurations filen](./configuration.md#routes)för Azure statisk Web Apps.

Program med en enda sida, oavsett om de använder front-end JavaScript-ramverk eller bibliotek eller WebAssembly-plattformar som till exempel blixt, är det ofta förlitande på klient sidans routning för navigering på webbappar. Dessa regler för routning av klient sidan uppdaterar webbläsarens fönster plats utan att göra förfrågningar tillbaka till servern. Om du uppdaterar sidan, eller navigerar direkt till platser som genereras av klient sidans routningsregler, krävs en återställnings väg på Server sidan för att kunna hantera rätt HTML-sida.

En vanlig återställnings väg visas i följande exempel:

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

Återställnings vägen måste anges sist i reglerna för routning, eftersom den fångar upp alla begär Anden som inte fångats av tidigare definierade regler.

## <a name="redirects"></a>Omdirigerar

> [!IMPORTANT]
> Funktioner som definieras i *routes.jspå* filen är nu föråldrade och implementeras bättre i [konfigurations filen](./configuration.md#routes)för Azure statisk Web Apps.

Du kan använda [301](https://en.wikipedia.org/wiki/HTTP_301) -och [302](https://en.wikipedia.org/wiki/HTTP_302) HTTP-statuskod för att omdirigera begär Anden från en väg till en annan.

Till exempel skapar följande regel en 301-omdirigering från _old-page.html_ till _new-page.html_.

```json
{
  "route": "/old-page.html",
  "serve": "/new-page.html",
  "statusCode": 301
}
```

Omdirigeringar fungerar också med sökvägar som inte definierar distinkta filer.

```json
{
  "route": "/about-us",
  "serve": "/about",
  "statusCode": 301
}
```

## <a name="custom-error-pages"></a>Anpassade felsidor

> [!IMPORTANT]
> Funktioner som definieras i *routes.jspå* filen är nu föråldrade och implementeras bättre i [konfigurations filen](./configuration.md#routes)för Azure statisk Web Apps.

Användare kan stöta på ett antal olika situationer som kan resultera i ett fel. Med hjälp av `platformErrorOverrides` matrisen kan du tillhandahålla en anpassad upplevelse som svar på dessa fel. Referera till [exempel cirkulations filen](#example-route-file) för placering av matrisen i _routes.jsi_ filen.

> [!NOTE]
> När en begäran gör det till plattformens åsidosättningar-nivå körs inte flödes regler igen.

I följande tabell visas de tillgängliga plattforms fel åsidosättningarna:

| Typ av fel  | HTTP-statuskod | Beskrivning |
|---------|---------|---------|
| `NotFound` | 404  | Det gick inte att hitta en sida på servern. |
| `Unauthenticated` | 401 | Användaren är inte inloggad med en [autentiseringsprovider](authentication-authorization.md). |
| `Unauthorized_InsufficientUserInformation` | 401 | Användarens konto på autentiseringsprovidern är inte konfigurerat för att exponera nödvändiga data. Det här felet kan inträffa i situationer som när appen ber autentiseringsprovider för användarens e-postadress, men användaren valde att begränsa åtkomsten till e-postadressen. |
| `Unauthorized_InvalidInvitationLink` | 401 | En inbjudan har antingen gått ut eller så har användaren följt en Inbjudnings länk som skapats för en annan mottagare.  |
| `Unauthorized_MissingRoles` | 401 | Användaren är inte medlem i en nödvändig roll. |
| `Unauthorized_TooManyUsers` | 401 | Platsen har uppnått det maximala antalet användare och servern begränsar ytterligare tillägg. Det här felet exponeras för klienten eftersom det inte finns någon gräns för antalet [inbjudningar](authentication-authorization.md) som du kan generera, och vissa användare kanske aldrig accepterar sin inbjudan.|
| `Unauthorized_Unknown` | 401 | Ett okänt problem har uppstått vid försök att autentisera användaren. En orsak till det här felet kan vara att användaren inte känns igen eftersom de inte beviljade något medgivande till programmet.|

## <a name="custom-mime-types"></a>Anpassade MIME-typer

> [!IMPORTANT]
> Funktioner som definieras i *routes.jspå* filen är nu föråldrade och implementeras bättre i [konfigurations filen](./configuration.md#routes)för Azure statisk Web Apps.

`mimeTypes`Objektet, som visas på samma nivå som `routes` matrisen, gör att du kan associera [MIME-typer](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types) med fil namns tillägg.

```json
{
    "routes": [],
    "mimeTypes": {
        "custom": "text/html"
    }
}
```

I exemplet ovan hanteras alla filer med `.custom` fil namns tillägget med MIME- `text/html` typen.

Följande överväganden är viktiga när du arbetar med MIME-typer:

- Nycklar får inte vara null eller tomma eller innehålla mer än 50 tecken
- Värden får inte vara null eller tomma eller mer än 1000 tecken

> [!NOTE]
> Statiska Web Apps förstår program vara och de förväntade MIME-typerna för WASM-och DLL-filer, du behöver inte lägga till mappningar för dem.

## <a name="default-headers"></a>Standard rubriker

> [!IMPORTANT]
> Funktioner som definieras i *routes.jspå* filen är nu föråldrade och implementeras bättre i [konfigurations filen](./configuration.md#routes)för Azure statisk Web Apps.

`defaultHeaders`Objektet, som visas på samma nivå som `routes` matrisen, gör att du kan lägga till, ändra eller ta bort [svarshuvuden](https://developer.mozilla.org/docs/Web/HTTP/Headers).

Genom att ange ett värde för ett sidhuvud läggs eller ändras rubriken. Om du anger ett tomt värde tas rubriken bort från att hanteras till klienten.

```json
{
    "routes": [],
    "defaultHeaders": {
      "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'",
      "cache-control": "must-revalidate, max-age=6000",
      "x-dns-prefetch-control": ""
    }
}
```

I exemplet ovan läggs en ny `content-security-policy` rubrik till, `cache-control` ändrar serverns standardvärde och `x-dns-prefetch-control` rubriken tas bort.

Följande överväganden är viktiga när du arbetar med huvuden:

- Nycklar får inte vara null eller tomma.
- Null eller tomma värden tar bort en rubrik från bearbetningen.
- Nycklar eller värden får inte överstiga 8 000 tecken.
- Definierade sidhuvuden hanteras med alla begär Anden.
- Sidhuvuden som definieras iroutes.jstillämpas endast _på_ statiskt innehåll. Du kan anpassa svars rubriker för en API-slutpunkt i funktionens kod.

## <a name="example-route-file"></a>Exempel på cirkulations fil

> [!IMPORTANT]
> Funktioner som definieras i *routes.jspå* filen är nu föråldrade och implementeras bättre i [konfigurations filen](./configuration.md#routes)för Azure statisk Web Apps.

I följande exempel visas hur du skapar väg regler för statiskt innehåll och API: er i en _routes.jspå_ en fil. Vissa vägar använder [ _/.auth_ -systemmappen](authentication-authorization.md) som använder autentiserings-relaterade slut punkter.

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
      "route": "/api/admin",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "serve": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": "404"
    },
    {
      "route": "/logout",
      "serve": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "serve": "/calendar.html"
    },
    {
      "route": "/specials",
      "serve": "/deals",
      "statusCode": 301
    }
  ],
  "platformErrorOverrides": [
    {
      "errorType": "NotFound",
      "serve": "/custom-404.html"
    },
    {
      "errorType": "Unauthenticated",
      "statusCode": "302",
      "serve": "/login"
    }
  ],
  "defaultHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
  },
  "mimeTypes": {
      "custom": "text/html"
  }
}
```

I följande exempel beskrivs vad som händer när en begäran matchar en regel.

| Begär anden till... | Resultat i... |
|--|--|--|
| _/Profile_ | Autentiserade användare betjänar filen _/profile/index.html_ . Oautentiserade användare Omdirigerad till _/login_. |
| _/admin/reports_ | Autentiserade användare i rollen _Administratörer_ betjänar filen _/admin/Reports/index.html_ . Autentiserade användare som inte tillhör rollen _Administratörer_ hanteras ett 401-fel <sup>2</sup>. Oautentiserade användare Omdirigerad till _/login_. |
| _/api/admin_ | Begär Anden från autentiserade användare i rollen _Administratörer_ skickas till API: et. Autentiserade användare som inte tillhör rollen _Administratörer_ och oautentiserade användare hanteras ett 401-fel. |
| _/customers/contoso_ | Autentiserade användare som tillhör antingen _Administratörer_ eller _kunder \_ contoso_ -roller behandlas som _/Customers/contoso/index.html_ -fil <sup>2</sup>. Autentiserade användare som inte tillhör _Administratörer_ eller _kunder \_ contoso_ -roller behandlas ett 401-fel. Oautentiserade användare Omdirigerad till _/login_. |
| _/login_ | Oautentiserade användare ifrågasätts att autentisera med GitHub. |
| _/.auth/login/twitter_ | Auktorisering med Twitter har inaktiverats. Servern svarar med ett 404-fel. |
| _/logout_ | Användare loggas ut från en autentiseringsprovider. |
| _/calendar/2020/01_ | Webbläsaren betjänar filen _/calendar.html_ . |
| _/specials_ | Webbläsaren omdirigeras till _/Deals_. |
| _/unknown-folder_ | Filen _/custom-404.html_ behandlas. |
| Filer med `.custom` tillägget | Hanteras med MIME- `text/html` typen |

Alla svar inkluderar `content-security-policy` rubrikerna med värdet `default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'` .

<sup>1</sup> väg regler för API-funktioner stöder bara [omdirigeringar](#redirects) och [skydd av vägar med roller](#securing-routes-with-roles).

<sup>2</sup> du kan ange en anpassad felsida genom att definiera en `Unauthorized_MissingRoles` regel i `platformErrorOverrides` matrisen.

## <a name="restrictions"></a>Begränsningar

- _routes.jspå_ filen får inte vara större än 100 kB
- _routes.jsi_ filen stöder högst 50 distinkta roller

Se [artikeln om kvoter](quotas.md) för allmänna begränsningar och begränsningar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera autentisering och auktorisering](authentication-authorization.md)
