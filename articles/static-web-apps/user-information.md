---
title: Åtkomst till användar information i Azure static Web Apps
description: Lär dig mer om att läsa Authorization Provider-returnerade användar data.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: d5a1d810c357aa83b8069023b00d76352da124df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94844803"
---
# <a name="accessing-user-information-in-azure-static-web-apps-preview"></a>Åtkomst till användar information i Azures statiska Web Apps för hands version

Azures statiska Web Apps tillhandahåller autentiserings-relaterad användar information via en [slut punkt för direkt åtkomst](#direct-access-endpoint) och till [API-funktioner](#api-functions).

Många användar gränssnitt förlitar sig kraftigt på användar verifierings data. Slut punkten för direkt åtkomst är ett verktygs-API som visar användar information utan att behöva implementera en anpassad funktion. Slut punkten för direkt åtkomst är inte beroende av kall start fördröjningar som är associerade med arkitektur utan server.

## <a name="client-principal-data"></a>Klientens huvud data

Klientens huvud data objekt visar information som är identifierbar för användaren i din app. Följande egenskaper är aktuella i klientens huvud objekt:

| Egenskap  | Beskrivning |
|-----------|---------|
| `identityProvider` | Namnet på [identitets leverantören](authentication-authorization.md). |
| `userId` | En statisk Web Apps unikt ID för användaren i Azure. <ul><li>Värdet är unikt för varje app. Till exempel returnerar samma användare ett annat `userId` värde på en annan statisk Web Apps resurs.<li>Värdet kvarstår för en användares livs längd. Om du tar bort och lägger till samma användare tillbaka till appen genereras en ny `userId` .</ul>|
| `userDetails` | Användarens användar namn eller e-postadress. Vissa providers returnerar [användarens e-postadress](authentication-authorization.md), medan andra skickar [användar referensen](authentication-authorization.md). |
| `userRoles`     | En matris med [användarens tilldelade roller](authentication-authorization.md). |

Följande exempel är ett exempel på klientens huvud objekt:

```json
{
  "identityProvider": "facebook",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "user@example.com",
  "userRoles": [ "anonymous", "authenticated" ]
}
```

## <a name="direct-access-endpoint"></a>Slut punkt för direkt åtkomst

Du kan skicka en `GET` begäran till `/.auth/me` vägen och få direkt åtkomst till klientens huvud data. Använd den här metoden för bästa prestanda när tillståndet för vyn är beroende av auktoriseringsdata.

För inloggade användare innehåller svaret ett klient objekts-JSON-objekt. Förfrågningar från oautentiserade användare returneras `null` .

Med hjälp av API: t [Hämta](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> kan du komma åt klientens huvud data med hjälp av följande syntax.

```javascript
async function getUserInfo() {
  const response = await fetch("/.auth/me");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>API-funktioner

De API-funktioner som är tillgängliga i statiska Web Apps via server delen Azure Functions har åtkomst till samma användar information som ett klient program. Även om API: et tar emot information som är identifierbar för användaren, utför den inte egna kontroller om användaren är autentiserad eller om de matchar en nödvändig roll. Regler för åtkomst kontroll definieras i [`routes.json`](routes.md) filen.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Klientens huvud data skickas till API-funktioner i `x-ms-client-principal` begär ande huvudet. Klientens huvud data skickas som en [base64](https://www.wikipedia.org/wiki/Base64)-kodad sträng som innehåller ett SERIALISERAt JSON-objekt.

Följande exempel funktion visar hur du läser och returnerar användar information.

```javascript
module.exports = async function (context, req) {
  const header = req.headers["x-ms-client-principal"];
  const encoded = Buffer.from(header, "base64");
  const decoded = encoded.toString("ascii");

  context.res = {
    body: {
      clientPrincipal: JSON.parse(decoded)
    }
  };
};
```

Om du antar att funktionen ovan heter `user` , kan du använda webb läsar API: erna [Hämta](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> för att få åtkomst till API: et svar med hjälp av följande syntax.

```javascript
async function getUser() {
  const response = await fetch("/api/user");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(await getUser());
```

# <a name="c"></a>[C#](#tab/csharp)

I en C#-funktion är användar informationen tillgänglig från `x-ms-client-principal` rubriken som kan avserialiseras till ett `ClaimsPrincipal` objekt eller din egen anpassade typ. Följande kod visar hur du packar upp rubriken i en mellanliggande typ, `ClientPrincipal` som sedan inaktive ras i en `ClaimsPrincipal` instans.

```csharp
  public static class StaticWebAppsAuth
  {
    private class ClientPrincipal
    {
        public string IdentityProvider { get; set; }
        public string UserId { get; set; }
        public string UserDetails { get; set; }
        public IEnumerable<string> UserRoles { get; set; }
    }

    public static ClaimsPrincipal Parse(HttpRequest req)
    {
        var principal = new ClientPrincipal();

        if (req.Headers.TryGetValue("x-ms-client-principal", out var header))
        {
            var data = header[0];
            var decoded = Convert.FromBase64String(data);
            var json = Encoding.ASCII.GetString(decoded);
            principal = JsonSerializer.Deserialize<ClientPrincipal>(json, new JsonSerializerOptions { PropertyNameCaseInsensitive = true });
        }

        principal.UserRoles = principal.UserRoles?.Except(new string[] { "anonymous" }, StringComparer.CurrentCultureIgnoreCase);

        if (!principal.UserRoles?.Any() ?? true)
        {
            return new ClaimsPrincipal();
        }

        var identity = new ClaimsIdentity(principal.IdentityProvider);
        identity.AddClaim(new Claim(ClaimTypes.NameIdentifier, principal.UserId));
        identity.AddClaim(new Claim(ClaimTypes.Name, principal.UserDetails));
        identity.AddClaims(principal.UserRoles.Select(r => new Claim(ClaimTypes.Role, r)));

        return new ClaimsPrincipal(identity);
    }
  }
```

---

<sup>1</sup> det finns inte stöd för att [Hämta](https://caniuse.com/#feat=fetch) API och [vänta](https://caniuse.com/#feat=mdn-javascript_operators_await) -operator i Internet Explorer.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera appinställningar](application-settings.md)
