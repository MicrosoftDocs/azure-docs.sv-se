---
title: 'Så här använder du utvärderings aktiverade API: er för kontinuerlig åtkomst i dina program | Azure'
titleSuffix: Microsoft identity platform
description: Hur du ökar App Security och återhämtning genom att lägga till stöd för utvärdering av kontinuerlig åtkomst, aktivera token för lång livs längd som kan återkallas utifrån kritiska händelser och princip utvärdering.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2020
ms.author: nichola
ms.reviewer: ''
ms.openlocfilehash: f6ce792b3db0100d7356884bbc6ee2696580df10
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652066"
---
# <a name="how-to-use-continuous-access-evaluation-enabled-apis-in-your-applications"></a>Så här använder du utvärderings aktiverade API: er för kontinuerlig åtkomst i dina program

[Kontinuerlig åtkomst utvärdering](../conditional-access/concept-continuous-access-evaluation.md) (CAE) är en ny bransch standard som tillåter att åtkomsttoken återkallas baserat på [kritiska händelser](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) och [princip utvärdering](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview) i stället för att förlita sig på att token upphör att gälla utifrån livs längd. För vissa resurs-API: er, eftersom risk och principer utvärderas i real tid, kan detta öka token-livstiden upp till 28 timmar. De här lång livs längds-token kommer att proaktivt uppdateras av Microsoft Authentication Library (MSAL), vilket ökar återhämtningen av dina program.

Den här artikeln visar hur du använder CAE-aktiverade API: er i dina program.

## <a name="implementation-considerations"></a>Att tänka på vid implementering

Om du vill använda utvärdering av kontinuerlig åtkomst måste både appen och det resurs-API som den har åtkomst till vara CAE-aktiverat. Att förbereda din kod för att använda en CAE-aktiverad resurs förhindrar dock inte att du använder API: er som inte är CAE-aktiverade.

Om ett resurs-API implementerar CAE och ditt program deklarerar att det kan hantera CAE, får appen CAE-token för resursen. Av den anledningen måste ditt program hantera appen CAE-anspråk för alla resurs-API: er som accepterar Microsoft Identity Access-token om du deklarerar appen CAE Ready. Om du inte hanterar CAE-svar i dessa API-anrop kan din app få ett nytt försök att köra ett API-anrop i en loop med en token som fortfarande finns i den returnerade livs längd för token men som har återkallats på grund av CAE.

## <a name="the-code"></a>Koden

Det första steget är att lägga till kod som hanterar ett svar från resurs-API: et som avvisar anropet på grund av CAE. Med CAE returnerar API: erna en 401-status och ett WWW-Authenticate huvud när åtkomsttoken har återkallats eller om API: et identifierar en ändring i IP-adressen som används. WWW-Authenticates huvudet innehåller en anspråks utmaning som programmet kan använda för att hämta en ny åtkomsttoken.

Exempel:

```console
HTTP 401; Unauthorized
WWW-Authenticate=Bearer
  authorization_uri="https://login.windows.net/common/oauth2/authorize",
  error="insufficient_claims",
  claims="eyJhY2Nlc3NfdG9rZW4iOnsibmJmIjp7ImVzc2VudGlhbCI6dHJ1ZSwgInZhbHVlIjoiMTYwNDEwNjY1MSJ9fX0="
```

Din app söker efter:

- API-anropet returnerar 401-status
- förekomsten av ett WWW-Authenticate-huvud som innehåller:
  - en "fel"-parameter med värdet "insufficient_claims"
  - en "anspråks parameter"

När dessa villkor är uppfyllda kan appen extrahera och avkoda anspråks utmaningen.

```csharp
if (APIresponse.IsSuccessStatusCode)
{
    // ...
}
else
{
    if (APIresponse.StatusCode == System.Net.HttpStatusCode.Unauthorized
        && APIresponse.Headers.WwwAuthenticate.Any())
    {
        AuthenticationHeaderValue bearer = APIresponse.Headers.WwwAuthenticate.First
            (v => v.Scheme == "Bearer");
        IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
        var error = GetParameter(parameters, "error");

        if (null != error && "insufficient_claims" == error)
        {
            var claimChallengeParameter = GetParameter(parameters, "claims");
            if (null != claimChallengeParameter)
            {
                var claimChallengebase64Bytes = System.Convert.FromBase64String(claimChallengeParameter);
                var claimChallenge = System.Text.Encoding.UTF8.GetString(claimChallengebase64Bytes);
                var newAccessToken = await GetAccessTokenWithClaimChallenge(scopes, claimChallenge);
```

Din app använder sedan anspråks utmaningen för att hämta en ny åtkomsttoken för resursen.

```csharp
try
{
    authResult = await _clientApp.AcquireTokenSilent(scopes, firstAccount)
        .WithClaims(claimChallenge)
        .ExecuteAsync()
        .ConfigureAwait(false);
}
catch (MsalUiRequiredException)
{
    try
    {
        authResult = await _clientApp.AcquireTokenInteractive(scopes)
            .WithClaims(claimChallenge)
            .WithAccount(firstAccount)
            .ExecuteAsync()
            .ConfigureAwait(false);
    }
    // ...
```

När ditt program är redo att hantera anspråks utmaningen som returneras av en CAE-aktiverad resurs, kan du tala om för Microsoft Identity att din app är CAE-klar. Om du vill göra detta i MSAL-programmet skapar du en offentlig klient med hjälp av-klient funktionerna i "CP1".

```csharp
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
    .WithDefaultRedirectUri()
    .WithAuthority(authority)
    .WithClientCapabilities(new [] {"cp1"})
    .Build();
```

Du kan testa ditt program genom att logga in en användare till programmet och sedan använda Azure Portal för att återkalla användarens sessioner. Nästa gången appen anropar CAE-aktiverat API, uppmanas användaren att autentisera igen.

## <a name="next-steps"></a>Nästa steg

Läs mer i utvärderingen av [kontinuerlig åtkomst](../conditional-access/concept-continuous-access-evaluation.md).
