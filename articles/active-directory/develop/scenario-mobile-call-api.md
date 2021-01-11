---
title: Anropa ett webb-API från en mobilapp | Azure
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar en mobilapp som anropar webb-API: er. (Anropa ett webb-API.)'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 018b5396563ac3a4b92b371d396c38afa3d2962f
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064274"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Anropa ett webb-API från en mobilapp

När din app loggar in en användare och tar emot tokens, visar Microsoft Authentication Library (MSAL) information om användaren, användarens miljö och utfärdade token. Din app kan använda dessa värden för att anropa ett webb-API eller visa ett välkomst meddelande till användaren.

I den här artikeln tittar vi först på MSAL-resultatet. Sedan ska vi titta på hur man använder en åtkomsttoken från `AuthenticationResult` eller `result` för att anropa ett skyddat webb-API.

## <a name="msal-result"></a>MSAL resultat
MSAL innehåller följande värden: 

- `AccessToken` anropar skyddade webb-API: er i en HTTP Bearer-begäran.
- `IdToken` innehåller användbar information om den inloggade användaren. Den här informationen omfattar användarens namn, hem klienten och en unik identifierare för lagring.
- `ExpiresOn` är giltighets tiden för token. MSAL hanterar en Apps automatiska uppdatering.
- `TenantId` är identifieraren för den klient där användaren är inloggad. För gäst användare i Azure Active Directory (Azure AD) B2B identifierar det här värdet innehavaren där användaren är inloggad. Värdet identifierar inte användarens hem klient.  
- `Scopes` anger de omfattningar som har beviljats med din token. De beviljade omfattningarna kan vara en delmängd av de omfattningar som du har begärt.

MSAL tillhandahåller också en abstraktion av ett `Account` värde. Ett `Account` värde representerar den aktuella användarens inloggade konto:

- `HomeAccountIdentifier` identifierar användarens hem klient.
- `UserName` är användarens önskade användar namn. Det här värdet kan vara tomt för Azure AD B2C användare.
- `AccountIdentifier` identifierar den inloggade användaren. I de flesta fall är det här värdet detsamma som `HomeAccountIdentifier` värdet om inte användaren är en gäst i en annan klient.

## <a name="call-an-api"></a>Anropa ett API

När du har åtkomst-token kan du anropa ett webb-API. Din app kommer att använda token för att skapa en HTTP-begäran och sedan köra begäran.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>MSAL för iOS och macOS

Metoderna för att hämta tokens returnerar ett `MSALResult` objekt. `MSALResult` exponerar en `accessToken` egenskap. Du kan använda `accessToken` för att anropa ett webb-API. Lägg till den här egenskapen i HTTP Authorization-huvudet innan du anropar åtkomst till det skyddade webb-API: et.

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>Gör flera API-begäranden

Om du behöver anropa samma API flera gånger, eller om du behöver anropa flera API: er, bör du tänka på följande när du skapar din app:

- **Stegvist godkännande**: med Microsoft Identity Platform kan appar få användar medgivande när behörigheter krävs i stället för alla vid starten. Varje gången appen är redo att anropa ett API bör den endast begära de omfattningar som krävs.

- **Villkorlig åtkomst**: när du gör flera API-begäranden i vissa scenarier kan du behöva uppfylla ytterligare krav för villkorlig åtkomst. Kraven kan öka på det här sättet om den första begäran inte har några principer för villkorlig åtkomst och appen försöker få tyst åtkomst till ett nytt API som kräver villkorlig åtkomst. För att hantera det här problemet, se till att fånga fel från tysta begär Anden och Förbered dig för att skapa en interaktiv begäran.  Mer information finns i [rikt linjer för villkorlig åtkomst](../azuread-dev/conditional-access-dev-guide.md).

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Anropa flera API: er med hjälp av stegvisa medgivande och villkorlig åtkomst

Om du behöver anropa flera API: er för samma användare kan du, efter att du har skaffat en token för användaren, undvika att upprepade gånger be användaren om autentiseringsuppgifter genom att sedan anropa `AcquireTokenSilent` för att hämta en token:

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Interaktion krävs när:

- Den användare som har skickat för det första API: et, men som nu måste godkänna för fler omfattningar. I det här fallet använder du ett stegvist godkännande.
- Det första API: t kräver inte [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md), men nästa API gör.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot, [Flytta till produktion](scenario-mobile-production.md).
