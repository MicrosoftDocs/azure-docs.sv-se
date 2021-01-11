---
title: Hämta en token för att anropa ett webb-API (mobilappar) | Azure
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar en mobilapp som anropar webb-API: er. (Hämta en token för appen.)'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 946908bf28a50bc8b523c4efd853e8720b115c7b
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063084"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Hämta en token för en mobilapp som anropar webb-API: er

Innan din app kan anropa skyddade webb-API: er måste den ha en åtkomsttoken. Den här artikeln vägleder dig genom processen för att hämta en token med hjälp av Microsoft Authentication Library (MSAL).

## <a name="define-a-scope"></a>Definiera ett omfång

När du begär en token måste du definiera ett omfång. Omfånget avgör vilka data som din app kan komma åt.

Det enklaste sättet att definiera ett omfång är att kombinera det önskade webb-API: et `App ID URI` med omfånget `.default` . Den här definitionen talar om för Microsoft Identity Platform att appen kräver alla omfattningar som anges i portalen.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Hämta token

### <a name="acquire-tokens-via-msal"></a>Hämta token via MSAL

MSAL gör att appar kan hämta token tyst och interaktivt. När du anropar `AcquireTokenSilent()` eller `AcquireTokenInteractive()` returnerar MSAL en åtkomsttoken för de begärda omfattningarna. Rätt mönster är att göra en tyst begäran och sedan återgå till en interaktiv begäran.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
```

#### <a name="ios"></a>iOS

Försök att hämta en token i bakgrunden:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";

MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        // Access token to call the web API
        NSString *accessToken = result.accessToken;
    }

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"

guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

    if (nsError.domain == MSALErrorDomain &&
        nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
         }
         return
     }

    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier

    // Access token to call the web API
    let accessToken = authResult.accessToken
}
```

Om MSAL returnerar `MSALErrorInteractionRequired` kan du försöka att förvärva token interaktivt:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(authPresentationViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

MSAL för iOS och macOS stöder olika modifierare för att få en token interaktivt eller tyst:
* [Vanliga parametrar för att hämta en token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parametrar för att hämta en interaktiv token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parametrar för att hämta en tyst token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

I följande exempel visas den minsta koden för att få en token interaktivt. Exemplet använder Microsoft Graph för att läsa användarens profil.

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

#### <a name="mandatory-parameters-in-msalnet"></a>Obligatoriska parametrar i MSAL.NET

`AcquireTokenInteractive` har endast en obligatorisk parameter: `scopes` . `scopes`Parametern räknar upp strängar som definierar de omfång som en token krävs för. Om token är för Microsoft Graph kan du hitta de omfattningar som krävs i API-referensen för varje Microsoft Graph-API. I referensen går du till avsnittet "behörigheter".

Om du till exempel vill [lista användarens kontakter](/graph/api/user-list-contacts)använder du avsnittet "User. Read", "Contacts. Read". Mer information finns i [referens för Microsoft Graph-behörigheter](/graph/permissions-reference).

På Android kan du ange överordnad aktivitet när du skapar appen med hjälp av `PublicClientApplicationBuilder` . Om du inte anger den överordnade aktiviteten vid den tiden kan du senare ange den genom att använda `.WithParentActivityOrWindow` som i följande avsnitt. Om du anger en överordnad aktivitet återgår token till den överordnade aktiviteten efter interaktionen. Om du inte anger det `.ExecuteAsync()` genererar anropet ett undantag.

#### <a name="specific-optional-parameters-in-msalnet"></a>Vissa valfria parametrar i MSAL.NET

I följande avsnitt beskrivs de valfria parametrarna i MSAL.NET.

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()`Parametern styr interaktivitet med användaren genom att ange en prompt.

![Bild som visar fälten i prompt strukturen. Dessa konstant värden styr interaktivitet med användaren genom att definiera den typ av prompt som visas av parametern WithPrompt ().](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

Klassen definierar följande konstanter:

- `SelectAccount` tvingar säkerhetstokentjänst (STS) att Visa dialog rutan för konto val. Dialog rutan innehåller de konton som användaren har en session för. Du kan använda det här alternativet om du vill låta användaren välja mellan olika identiteter. Det här alternativet MSAL för att skicka `prompt=select_account` till identitets leverantören.

    `SelectAccount`Konstanten är standard och ger den bästa möjliga upplevelsen baserat på tillgänglig information. Den tillgängliga informationen kan innehålla konto, närvaro för en session för användaren och så vidare. Ändra inte det här standardvärdet om du inte har en lämplig anledning att göra det.
- `Consent` gör att du kan uppmana användaren att ge sitt medgivande även om medgivande beviljades tidigare. I det här fallet skickar MSAL `prompt=consent` till identitets leverantören.

    Du kanske vill använda `Consent` konstanten i säkerhetsfokuserade program där organisationens styrning kräver att användare ser dialog rutan medgivande varje gången de använder programmet.
- `ForceLogin` gör att tjänsten kan fråga användaren om autentiseringsuppgifter även om frågan inte behövs.

    Det här alternativet kan vara användbart om hämtningen av token Miss lyckas och du vill låta användaren logga in igen. I det här fallet skickar MSAL `prompt=login` till identitets leverantören. Du kanske vill använda det här alternativet i säkerhetsfokuserade program där organisationens styrning kräver att användaren loggar in varje gång de kommer åt specifika delar av programmet.
- `Never` är endast för .NET 4,5 och Windows Runtime (WinRT). Den här konstanten kommer inte att fråga användaren, men den kommer att försöka använda den cookie som lagras i den dolda inbäddade vyn. Mer information finns i [använda webbläsare med MSAL.net](./msal-net-web-browsers.md).

    Om det här alternativet Miss lyckas `AcquireTokenInteractive` utlöses ett undantag för att meddela dig att det krävs en användar gränssnitts interaktion. Sedan måste du använda en annan `Prompt` parameter.
- `NoPrompt` skickar inte någon prompt till identitets leverantören.

    Det här alternativet är användbart endast för principer för redigering och profiler i Azure Active Directory B2C. Mer information finns i [B2C-information](https://aka.ms/msal-net-b2c-specificities).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Använd `WithExtraScopeToConsent` modifieraren i ett avancerat scenario där du vill att användaren ska ge sitt första medgivande till flera resurser. Du kan använda den här modifieraren när du inte vill använda ett stegvist godkännande, som normalt används med MSAL.NET eller Microsoft Identity Platform 2,0. Mer information finns i [få användar medgivande framför flera resurser](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

Här är ett kod exempel:

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Andra valfria parametrar

Mer information om de andra valfria parametrarna för `AcquireTokenInteractive` finns i [referens dokumentationen för AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods).

### <a name="acquire-tokens-via-the-protocol"></a>Hämta token via protokollet

Vi rekommenderar inte att du använder protokollet direkt för att hämta tokens. Om du gör det stöder appen inte vissa scenarier som inbegriper enkel inloggning (SSO), enhets hantering och villkorlig åtkomst.

När du använder protokollet för att hämta token för mobila appar, gör du två begär Anden:

* Hämta en auktoriseringskod.
* Exchange-koden för en token.

#### <a name="get-an-authorization-code"></a>Hämta en auktoriseringskod

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Få åtkomst till och uppdatera token

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot och [anropa ett webb-API](scenario-mobile-call-api.md).