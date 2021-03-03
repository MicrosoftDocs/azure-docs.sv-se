---
title: Begära en åtkomsttoken – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du begär en åtkomsttoken från Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/26/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e5168d5e5e3935da267fb26f38735a88bdfd7837
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654484"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Begära en åtkomsttoken i Azure Active Directory B2C

En *åtkomsttoken* innehåller anspråk som du kan använda i Azure Active Directory B2C (Azure AD B2C) för att identifiera de behörigheter som dina API:er har beviljats. När du anropar en resursserver måste HTTP-begäran innehålla en åtkomsttoken. En åtkomsttoken visas som **access_token** i svaren från Azure AD B2C.

Den här artikeln beskriver hur du begär en åtkomsttoken för en webbapp och ett webb-API. Mer information om token i Azure AD B2C finns i [översikten över token i Azure Active Directory B2C](tokens-overview.md).

> [!NOTE]
> **Länkade webb-API:er (On-Behalf-Of) stöds inte i Azure AD B2C.** Många arkitekturer har ett webb-API som måste anropa ett annat underordnat webb-API, som båda skyddas av Azure AD B2C. Det här scenariot är vanligt med klienter som har ett webb-API på serversidan, som i sin tur anropar en annan tjänst. Det här scenariot med länkade webb-API:er kan användas genom en tilldelning av OAuth 2.0 JWT-ägarautentiseringsuppgifter, även kallat On-Behalf-Of-flöde. Detta flöde är emellertid inte implementerat i Azure AD B2C.

## <a name="prerequisites"></a>Förutsättningar

- [Skapa ett användarflöde](tutorial-create-user-flows.md) så att användare kan registrera sig och logga in i ditt program.
- Om du inte redan har gjort det [lägger du till ett webb-API-program i din Azure Active Directory B2C-klientorganisation](add-web-api-application.md).

## <a name="scopes"></a>Omfattningar

Med hjälp av omfång kan du hantera behörigheter för skyddade resurser. När en åtkomsttoken begärs måste klientprogrammet ange önskade behörigheter i **scope**-parametern för begäran. Om du till exempel vill ange **omfångsvärdet** `read` för API:et med ett **app-ID med URI:n** `https://contoso.onmicrosoft.com/api`, är omfånget `https://contoso.onmicrosoft.com/api/read`.

Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Till exempel kan användare av webb-API:et ha både läs- och skrivbehörighet, eller så har användare av webb-API:et kanske bara läsbehörighet. Om du vill hämta flera behörigheter i samma begäran kan du lägga till flera poster i samma **scope**-parameter i begäran, avgränsade med blanksteg.

I följande exempel visas omfång som är avkodade i en URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

I följande exempel visas omfång som är kodade i en URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Om du begär fler omfång än vad ditt klientprogram beviljas, lyckas anropet om minst en behörighet beviljas. **SCP**-anspråket i den resulterande åtkomsttoken innehåller endast de behörigheter som beviljades. 

### <a name="openid-connect-scopes"></a>OpenID Connect-omfång

OpenID Connect-standarden anger flera särskilda omfångsvärden. Följande omfång representerar behörigheten för att komma åt användarens profil:

- **openid** – Begär en ID-token.
- **offline_access** – Begär en uppdateringstoken via [auktoriseringskodflöden](authorization-code-flow.md).
- **00000000-0000-0000-0000-000000000000** – med hjälp av klient-ID som omfånget anger att appen behöver en åtkomsttoken som kan användas för din egen tjänst eller ditt webb-API som representeras av samma klient-ID.

Om parametern **response_type** i en `/authorize`-begäran innehåller `token`, måste **scope**-parametern innehålla minst ett annat resursomfång än `openid` och `offline_access` som kommer att beviljas. Annars misslyckas `/authorize`-begäran.

## <a name="request-a-token"></a>Begära en token

Du behöver en auktoriseringskod för att begära en åtkomsttoken. Exemplet nedan visar en begäran om en auktoriseringskod till `/authorize`-slutpunkten. Anpassade domäner kan inte användas med åtkomsttoken. Använd din tenant-name.onmicrosoft.com-domän i URL:en för begäran.

I följande exempel ersätter du dessa värden:

- `<tenant-name>` – Namnet på din Azure AD B2C-klient.
- `<policy-name>` – Namnet på din anpassade princip eller ditt anpassade användarflöde.
- `<application-ID>` – Programidentifieraren för webbappen som du registrerade för att stödja användarflödet.
- `<redirect-uri>` – **Omdirigerings-URI:n** som du angav när du registrerade klientprogrammet.

```http
GET https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

Svaret med auktoriseringskoden bör likna följande exempel:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

När du har tagit emot auktoriseringskoden kan du använda den för att begära en åtkomsttoken:

```http
POST <tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Nu bör du se utdata som liknar följande svar:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

När du använder https://jwt.ms för att undersöka den åtkomsttoken som returnerades, bör du se något som liknar följande exempel:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur du [konfigurerar token i Azure AD B2C](configure-tokens.md)
