---
title: Hämta token för att anropa en webb-API (daemon-app) – Microsoft Identity Platform | Azure
description: 'Lär dig hur du skapar en daemon-app som anropar webb-API: er (hämtar token)'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 19ead7fe063992e95588641f7fd739081cf54a2f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578421"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Daemon-app som anropar webb-API: er – hämta en token

När du har skapat ett konfidentiellt klient program kan du hämta en token för appen genom att anropa `AcquireTokenForClient` , skicka omfånget och eventuellt framtvinga en uppdatering av token.

## <a name="scopes-to-request"></a>Omfattningar som ska begäras

Omfånget för att begära ett flöde för klientautentiseringsuppgifter är namnet på resursen följt av `/.default` . Den här texten talar om för Azure Active Directory (Azure AD) att använda *behörigheter på program nivå* som har deklarerats statiskt under program registrering. Dessa API-behörigheter måste också beviljas av en innehavaradministratör.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```JavaScript
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'], // e.g. 'https://graph.microsoft.com/.default'
};
```

# <a name="python"></a>[Python](#tab/python)

I MSAL python ser konfigurations filen ut som detta kodfragment:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

---

### <a name="azure-ad-v10-resources"></a>Azure AD-resurser (v 1.0)

Det omfång som används för klientautentiseringsuppgifter bör alltid vara resurs-ID följt av `/.default` .

> [!IMPORTANT]
> När MSAL begär en åtkomsttoken för en resurs som accepterar en version 1,0-åtkomsttoken, parsar Azure AD den önskade mål gruppen från det begärda omfånget genom att ta allt före det sista snedstrecket och använda det som resurs-ID.
> Så om till exempel Azure SQL Database (**https: \/ /Database.Windows.net**) förväntar sig en mål grupp som slutar med ett snedstreck (för Azure SQL Database `https://database.windows.net/` ), måste du begära en omfattning `https://database.windows.net//.default` . (Observera det dubbla snedstrecket.) Se även MSAL.NET problem [#747: resurs-URL: en avslutande snedstreck utelämnas, vilket orsakade SQL-auth-fel](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient-API

Om du vill hämta en token för appen använder du `AcquireTokenForClient` eller motsvarande, beroende på plattform.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

### <a name="acquiretokenforclient-uses-the-application-token-cache"></a>AcquireTokenForClient använder Application token cache

I MSAL.NET `AcquireTokenForClient` använder Application token cache. (Alla andra AcquireToken *XX* -metoder använder user token cache.) Anropa inte `AcquireTokenSilent` innan du anropar `AcquireTokenForClient` , eftersom `AcquireTokenSilent` använder cacheminnet för *användarens* token. `AcquireTokenForClient` kontrollerar cacheminnet för *program* -token och uppdaterar det.

# <a name="java"></a>[Java](#tab/java)

Den här koden extraheras från [MSAL java-dev-exempel](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/).

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Kodfragmentet nedan illustrerar hämtning av token i en MSAL-nods konfidentiellt klient program:

```JavaScript
try {
    const authResponse = await cca.acquireTokenByClientCredential(tokenRequest);
    console.log(authResponse.accessToken) // display access token
} catch (error) {
    console.log(error);
}
```

# <a name="python"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

---

### <a name="protocol"></a>Protokoll

Om du ännu inte har ett bibliotek för ditt valda språk, kanske du vill använda protokollet direkt:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>Första fallet: få åtkomst till Tokenbegäran med hjälp av en delad hemlighet

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Andra fallet: få åtkomst till Tokenbegäran med hjälp av ett certifikat

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

Mer information finns i protokoll dokumentationen: [Microsoft Identity Platform och OAuth 2,0-klientens autentiseringsuppgifter Flow](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Felsökning

### <a name="did-you-use-the-resourcedefault-scope"></a>Har du använt Resource/. default-omfånget?

Om du får ett fel meddelande om att du har använt ett ogiltigt omfång använder du antagligen inte `resource/.default` omfattningen.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Glömde du att ge administratörs tillåtelse? Daemon-appar behöver!

Om du får **otillräcklig behörighet för att slutföra åtgärds** felet när du anropar API: t måste klient organisationens administratör bevilja behörighet till programmet. Se steg 6 i registrera klient programmet ovan.
Du ser vanligt vis ett fel som ser ut ungefär så här:

```json
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

### <a name="are-you-calling-your-own-api"></a>Anropar du ditt eget API?

Om du anropar ditt eget webb-API och inte kunde lägga till en app-behörighet till appens registrering för daemon-appen, exponerade du en app-roll i ditt webb-API?

Mer information finns i [exponera program behörigheter (app-roller)](scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles) och särskilt [Se till att Azure AD utfärdar token för ditt webb-API till endast tillåtna klienter](scenario-protected-web-api-app-registration.md#ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients).

## <a name="next-steps"></a>Nästa steg

# <a name="net"></a>[.NET](#tab/dotnet)

Gå vidare till nästa artikel i det här scenariot och [anropa ett webb-API](./scenario-daemon-call-api.md?tabs=dotnet).

# <a name="java"></a>[Java](#tab/java)

Gå vidare till nästa artikel i det här scenariot och [anropa ett webb-API](./scenario-daemon-call-api.md?tabs=java).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Gå vidare till nästa artikel i det här scenariot och [anropa ett webb-API](./scenario-daemon-call-api.md?tabs=nodejs).

# <a name="python"></a>[Python](#tab/python)

Gå vidare till nästa artikel i det här scenariot och [anropa ett webb-API](./scenario-daemon-call-api.md?tabs=python).

---
