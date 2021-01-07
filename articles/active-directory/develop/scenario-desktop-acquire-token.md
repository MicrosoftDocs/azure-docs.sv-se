---
title: Hämta en token för att anropa ett webb-API (Skriv bords program) | Azure
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar en stationär app som anropar webb-API: er för att hämta en token för appen'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/06/2021
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: d5f5e1098b688fc307bae5ea3538c818cb529b0a
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97962405"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Skriv bords app som anropar webb-API: er: Hämta en token

När du har skapat en instans av det offentliga klient programmet använder du den för att hämta en token som du sedan använder för att anropa ett webb-API.

## <a name="recommended-pattern"></a>Rekommenderat mönster

Webb-API: et definieras med dess `scopes` . Vad som händer i ditt program är mönstret som används:

- Försök att hämta en token från token cache systematiskt genom att anropa `AcquireTokenSilent` .
- Om anropet Miss lyckas använder du det `AcquireToken` flöde som du vill använda, som visas här i `AcquireTokenXX` .

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>I MSAL.NET

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>I MSAL för iOS och macOS

Mål-C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Införliva

```swift
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
}
```
---

Här är de olika sätten att hämta tokens i ett Skriv bords program.

## <a name="acquire-a-token-interactively"></a>Hämta en token interaktivt

I följande exempel visas minimal kod för att få en token interaktivt för att läsa användarens profil med Microsoft Graph.

# <a name="net"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>I MSAL.NET

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

### <a name="mandatory-parameters"></a>Obligatoriska parametrar

`AcquireTokenInteractive` har endast en obligatorisk parameter, ``scopes`` som innehåller en uppräkning av strängar som definierar de omfång som en token krävs för. Om token är för Microsoft Graph, finns de obligatoriska omfattningarna i API-referensen för varje Microsoft Graph-API i avsnittet med namnet "Permissions". Om du till exempel vill [Visa användarens kontakter](/graph/api/user-list-contacts)måste omfattningen "User. Read", "Contacts. Read" användas. Mer information finns i [referens för Microsoft Graph-behörigheter](/graph/permissions-reference).

På Android måste du också ange den överordnade aktiviteten genom `.WithParentActivityOrWindow` att använda, som du ser, så att token återgår till den överordnade aktiviteten efter interaktionen. Om du inte anger det genereras ett undantag vid anrop `.ExecuteAsync()` .

### <a name="specific-optional-parameters-in-msalnet"></a>Vissa valfria parametrar i MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Användar gränssnittet är viktigt eftersom det är interaktivt. `AcquireTokenInteractive` har en speciell valfri parameter som kan ange, för plattformar som stöder den, det överordnade användar gränssnittet. När det används i ett Skriv bords program, `.WithParentActivityOrWindow` har en annan typ, som är beroende av plattformen. Alternativt kan du utelämna den valfria parametern för överordnad fönster för att skapa ett fönster, om du inte vill styra var inloggnings dialog rutan visas på skärmen. Detta gäller för program som är kommando radsbaserade, som används för att skicka anrop till en annan server dels tjänst och som inte behöver några Windows för användar interaktion.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

!

- På .NET standard är det förväntade `object` `Activity` på Android, `UIViewController` på iOS, `NSWindow` på Mac och `IWin32Window` `IntPr` i Windows.
- I Windows måste du anropa `AcquireTokenInteractive` från UI-tråden så att den inbäddade webbläsaren får rätt kontext för UI-synkronisering. Att inte anropa från UI-tråden kan orsaka att meddelanden inte pumpar korrekt och död läges scenarier med användar gränssnittet. Ett sätt att anropa Microsoft Authentication libraries (MSALs) från UI-tråden om du inte är i UI-tråden redan är att använda `Dispatcher` på WPF.
- Om du använder WPF för att hämta ett fönster från en WPF-kontroll, kan du använda- `WindowInteropHelper.Handle` klassen. Anropet är sedan från en WPF-kontroll ( `this` ):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` används för att styra interaktivitet med användaren genom att ange en prompt.

![Bild som visar fälten i prompt strukturen. Dessa konstant värden styr interaktivitet med användaren genom att definiera den typ av prompt som visas av metoden WithPrompt ().](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

Klassen definierar följande konstanter:

- ``SelectAccount`` tvingar STS att presentera dialog rutan konto val som innehåller konton som användaren har en session för. Det här alternativet är användbart när programutvecklare vill låta användarna välja mellan olika identiteter. Det här alternativet MSAL för att skicka ``prompt=select_account`` till identitets leverantören. Det här alternativet är standardinställningen. Det är ett bra jobb att tillhandahålla bästa möjliga upplevelse baserat på tillgänglig information, till exempel konto och närvaro för en session för användaren. Ändra det inte om du inte har en lämplig anledning att göra det.
- ``Consent`` gör det möjligt för programutvecklaren att tvinga användaren att tillfrågas om medgivande, även om medgivande beviljades tidigare. I det här fallet skickar MSAL `prompt=consent` till identitets leverantören. Det här alternativet kan användas i vissa säkerhetsfokuserade program där organisationens styrning kräver att användaren visas i dialog rutan medgivande varje gången programmet används.
- ``ForceLogin`` gör det möjligt för programutvecklaren att be användaren att ange autentiseringsuppgifter för tjänsten, även om användaren inte behöver göra något. Det här alternativet kan vara användbart för att låta användaren logga in igen om det inte går att förvärva en token. I det här fallet skickar MSAL `prompt=login` till identitets leverantören. Ibland används den i säkerhetsfokuserade program där organisationens styrning kräver att användaren loggar in på nytt varje gång de kommer åt specifika delar av ett program.
- ``Never`` (endast för .NET 4,5 och WinRT) uppmanas inte användaren, utan försöker i stället använda den cookie som är lagrad i den dolda inbäddade vyn. Mer information finns i Web views in MSAL.NET. Det kan hända att du inte kan använda det här alternativet. I så fall, `AcquireTokenInteractive` genererar ett undantag för att meddela att en användar gränssnitts interaktion krävs. Du måste använda en annan `Prompt` parameter.
- ``NoPrompt`` Det går inte att skicka någon prompt till identitets leverantören. Det här alternativet är användbart endast för Azure Active Directory (Azure AD) B2C Redigera profil principer. Mer information finns i [Azure AD B2C information](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Den här modifieraren används i ett avancerat scenario där du vill att användaren ska godkänna flera resurser i förväg och du inte vill använda ett stegvist godkännande som normalt används med MSAL.NET/the Microsoft Identity Platform. Mer information finns i [få användar medgivande framför flera resurser](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Ett webb gränssnitt är en mekanism för att anropa en webbläsare. Den här mekanismen kan vara en särskild WebBrowser-kontroll för gränssnitt eller ett sätt att delegera öppning av webbläsaren.
MSAL tillhandahåller webb GRÄNSSNITTs implementeringar för de flesta plattformar, men det finns fall där du kanske vill vara värd för webbläsaren själv:

- Plattformar som inte uttryckligen omfattas av MSAL, till exempel blixt, Unity och mono på Station ära datorer.
- Du vill testa ditt program och använda en automatiserad webbläsare som kan användas med selen.
- Webbläsaren och den app som kör MSAL finns i separata processer.

##### <a name="at-a-glance"></a>Snabbt

För att uppnå detta ger du MSAL `start Url` , som måste visas i en valfri webbläsare så att användaren kan ange objekt som användar namn.
När autentiseringen är klar måste appen skickas tillbaka till MSAL `end Url` , som innehåller en kod från Azure AD.
Värden för `end Url` är alltid `redirectUri` . Fånga `end Url` genom att göra något av följande:

- Övervaka webbläsaren omdirigeras tills den `redirect Url` har nåtts.
- Låt webbläsaren omdirigera till en URL som du övervakar.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi är en utöknings punkt

`WithCustomWebUi` är en utöknings punkt som du kan använda för att tillhandahålla ditt eget användar gränssnitt i offentliga klient program. Du kan också låta användaren gå igenom/Authorize-slutpunkten för identitets leverantören och låta dem logga in och godkänna dem. MSAL.NET kan sedan lösa in autentiserings koden och hämta en token. Den används till exempel i Visual Studio för att låta electrons-program (till exempel Visual Studio feedback) tillhandahålla webb interaktionen, men lämna den till MSAL.NET för att göra merparten av arbetet. Du kan också använda den om du vill tillhandahålla UI-automatisering. I offentliga klient program använder MSAL.NET bevis nyckeln för Code Exchange (PKCE) standard för att säkerställa att säkerheten respekteras. Endast MSAL.NET kan lösa in koden. Mer information finns i [RFC 7636 – bevis nyckel för kod utbyte av offentliga OAuth-klienter](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Använd WithCustomWebUi

Följ dessa steg om du vill använda `.WithCustomWebUI` .

  1. Implementera gränssnittet `ICustomWebUi`. Mer information finns på [den här webbplatsen](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Implementera en `AcquireAuthorizationCodeAsync` metod och godkänn URL: en för auktoriseringskod som beräknats av MSAL.net. Låt sedan användaren gå igenom interaktionen med identitets leverantören och återgå till URL: en som identitets leverantören skulle ha anropat din implementering igen tillsammans med auktoriseringskod. Om du har problem bör implementeringen utlösa ett `MsalExtensionException` undantag till ett snyggt samarbete med MSAL.
  2. I ditt `AcquireTokenInteractive` anrop använder du `.WithCustomUI()` modifieraren som skickar instansen för ditt anpassade webb gränssnitt.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Exempel på implementering av ICustomWebUi i Test Automation: SeleniumWebUI

MSAL.NET-teamet har skrivit om UI-testerna för att använda den här utöknings bara mekanismen. Om du är intresse rad kan du titta på [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) -klassen i käll koden för MSAL.net.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Få en bra upplevelse med SystemWebViewOptions

Från MSAL.NET 4,1 [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions) kan du ange:

- URI: n för att gå till ( `BrowserRedirectError` ) eller det HTML-fragment som ska visas ( `HtmlMessageError` ) vid inloggnings-eller medgivande fel i system webbläsare.
- URI: n för att gå till ( `BrowserRedirectSuccess` ) eller det HTML-fragment som ska visas ( `HtmlMessageSuccess` ) i händelse av lyckat inloggnings försök eller medgivande.
- Den åtgärd som ska köras för att starta system webbläsaren. Du kan ange en egen implementering genom att ställa in `OpenBrowserAsync` ombudet. Klassen tillhandahåller också en standard implementering för två webbläsare: `OpenWithEdgeBrowserAsync` och `OpenWithChromeEdgeBrowserAsync` för Microsoft Edge och [Microsoft Edge på krom](https://www.windowscentral.com/faq-edge-chromium).

Om du vill använda den här strukturen skriver du något som liknar följande exempel:

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Andra valfria parametrar

Mer information om alla andra valfria parametrar för `AcquireTokenInteractive` finns i [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

MSAL python tillhandahåller inte en interaktiv metod för att hämta token direkt. I stället måste programmet skicka en auktoriseringsbegäran i dess implementering av användar interaktions flödet för att få en auktoriseringskod. Den här koden kan sedan skickas till- `acquire_token_by_authorization_code` metoden för att hämta token.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])

```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>I MSAL för iOS och macOS

Mål-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
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

Införliva

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```
---

## <a name="integrated-windows-authentication"></a>Integrerad Windows-autentisering

Om du vill logga in en domän användare på en domän eller en Azure AD-ansluten dator använder du integrerad Windows-autentisering (IWA).

### <a name="constraints"></a>Villkor

- Integrerad Windows-autentisering är användbart för *federerade +* användare, det vill säga användare som skapats i Active Directory och som backas upp av Azure AD. Användare som skapats direkt i Azure AD utan Active Directory säkerhets kopiering, som kallas *hanterade* användare, kan inte använda det här autentiseringsschemat. Den här begränsningen påverkar inte användar flödet och lösen ordet.
- IWA är avsedd för appar som är skrivna för .NET Framework-, .NET Core-och Universell Windows-plattform-plattformar (UWP).
- IWA kringgår inte [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md). Om MFA konfigureras kan IWA Miss förväntas om en MFA-utmaning krävs, eftersom MFA kräver användar interaktion.
  > [!NOTE]
  > Det här är en knepig. IWA är icke-interaktivt, men MFA kräver användar-interaktivitet. Du styr inte när identitets leverantören begär MFA för att utföras, klient organisationens administratör gör. Från våra observationer krävs MFA när du loggar in från ett annat land/en annan region, om du inte är ansluten via VPN till ett företags nätverk och ibland även när du är ansluten via VPN. Vänta inte med en deterministisk uppsättning regler. Azure AD använder AI för att kontinuerligt lära sig om MFA krävs. Återgå till en användar prompt som interaktiv autentisering eller enhets kod flöde om IWA Miss lyckas.

- Utfärdaren som skickades `PublicClientApplicationBuilder` måste vara:
  - Som innehavare av formuläret `https://login.microsoftonline.com/{tenant}/` , där `tenant` är antingen det GUID som representerar klient-ID: t eller en domän som är associerad med klienten.
  - För arbets-och skol konton: `https://login.microsoftonline.com/organizations/` .
  - Microsoft-personliga konton stöds inte. Du kan inte använda/vanliga-eller/consumers-klienter.

- Eftersom integrerad Windows-autentisering är ett tyst flöde:
  - Användaren av ditt program måste tidigare ha samtyckt till att använda programmet.
  - Eller så måste klient administratören tidigare ha samtyckt till alla användare i klienten för att kunna använda programmet.
  - Med andra ord:
    - Antingen har du som utvecklare valt knappen **bevilja** i Azure Portal själv.
    - Eller så har en innehavaradministratör valt knappen **bevilja/återkalla administratörs medgivande för {klient domän}** på fliken **API-behörigheter** i registreringen för programmet. Mer information finns i [lägga till behörigheter för åtkomst till ditt webb-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).
    - Eller så har du gett ett sätt för användare att godkänna programmet. Mer information finns i [begära individuell användar medgivande](./v2-permissions-and-consent.md#requesting-individual-user-consent).
    - Eller så har du angett ett sätt för klient organisations administratören att samtycka till programmet. Mer information finns i [Administratörs medgivande](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

- Det här flödet är aktiverat för .NET Desktop-, .NET Core-och UWP-appar.

Mer information om medgivande finns i [behörigheter för Microsoft Identity Platform och medgivande](./v2-permissions-and-consent.md).

### <a name="learn-how-to-use-it"></a>Lär dig hur du använder den

# <a name="net"></a>[.NET](#tab/dotnet)

I MSAL.NET måste du använda:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Du behöver normalt bara en parameter ( `scopes` ). Beroende på hur din Windows-administratör har konfigurerat principerna kanske programmen på din Windows-dator inte får söka efter den inloggade användaren. I så fall kan du använda en andra metod `.WithUsername()` och skicka in användar namnet för den inloggade användaren som ett UPN-format, till exempel `joe@contoso.com` . I .NET Core är det bara den överlagring som tar användar namnet tillgängligt eftersom .NET Core Platform inte kan be användar namnet till operativ systemet.

Följande exempel visar det mest aktuella fallet med förklaringar av vilken typ av undantag du kan få och deras åtgärder.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

Listan över möjliga modifierare på AcquireTokenByIntegratedWindowsAuthentication finns i [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

Detta utdrag är från [MSAL java dev-exempel](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Det här flödet stöds inte ännu i MSAL python.

# <a name="macos"></a>[macOS](#tab/macOS)

Det här flödet gäller inte för macOS.

---

## <a name="username-and-password"></a>Användarnamn och lösenord

Du kan också hämta en token genom att ange användar namn och lösen ord. Det här flödet är begränsat och rekommenderas inte, men det finns fortfarande användnings fall där det är nödvändigt.

### <a name="this-flow-isnt-recommended"></a>Det här flödet rekommenderas inte

Det här flödet *rekommenderas inte* eftersom ett program ber användaren att lösen ordet inte är säkert. Mer information finns i [Vad är lösningen på det växande problemet med lösen ord?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Det prioriterade flödet för att hämta en token tyst på Windows-domänanslutna datorer är [integrerad Windows-autentisering](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Du kan också använda [enhets kod flödet](https://aka.ms/msal-net-device-code-flow).

> [!NOTE]
> Att använda ett användar namn och ett lösen ord är användbart i vissa fall, till exempel DevOps-scenarier. Men om du vill använda ett användar namn och lösen ord i interaktiva scenarier där du anger ditt eget användar gränssnitt, bör du tänka på hur du ska flytta bort från det. Genom att använda ett användar namn och lösen ord har du ett antal saker:
>
> - Kärn Tenets av modern identitet. Ett lösen ord kan få phished och spelas upp eftersom en delad hemlighet kan fångas upp. Den är inte kompatibel med lösen ord.
> - Användare som behöver göra MFA kan inte logga in eftersom det inte finns någon interaktion.
> - Användare kan inte utföra enkel inloggning (SSO).

### <a name="constraints"></a>Villkor

Följande begränsningar gäller också:

- Användar namnet och lösen ordet är inte kompatibelt med villkorlig åtkomst och Multi-Factor Authentication. Det innebär att om din app körs i en Azure AD-klient där klient organisationen kräver Multi-Factor Authentication, kan du inte använda det här flödet. Många organisationer gör det.
- Det fungerar bara för arbets-och skol konton (inte MSA).
- Flödet är tillgängligt på .NET Desktop och .NET Core, men inte på UWP.

### <a name="b2c-specifics"></a>B2C-information

Mer information finns i [autentiseringsuppgifter för resurs ägar lösen ord (ROPC) med B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Använd den

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`innehåller metoden `AcquireTokenByUsernamePassword` .

I följande exempel presenteras ett förenklat fall.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

Följande exempel visar det mest aktuella fallet med förklaringar av vilken typ av undantag du kan få och deras åtgärder.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Mer information om alla modifierare som kan tillämpas på `AcquireTokenByUsernamePassword` finns i [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

Följande utdrag är från [MSAL java dev-exempel](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Detta utdrag är från [MSAL python-dev-exempel](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

# <a name="macos"></a>[macOS](#tab/macOS)

Det här flödet stöds inte på MSAL för macOS.

---

## <a name="command-line-tool-without-a-web-browser"></a>Kommando rads verktyg utan webbläsare

### <a name="device-code-flow"></a>Enhets kod flöde

Om du skriver ett kommando rads verktyg som inte har webb kontroller, och du inte vill använda de tidigare flödena, måste du använda enhets kod flödet.

Interaktiv autentisering med Azure AD kräver en webbläsare. Mer information finns i [användningen av webbläsare](https://aka.ms/msal-net-uses-web-browser). För att autentisera användare på enheter eller operativ system som inte tillhandahåller en webbläsare, tillåter enhets kod flödet att användaren använder en annan enhet, till exempel en dator eller en mobil telefon för att logga in interaktivt. Genom att använda enhets kod flödet hämtar programmet token via en två stegs process som är utformad för dessa enheter eller operativ system. Exempel på sådana program är program som körs på iOT eller kommando rads verktyg (CLI). Tanken är att:

1. När användarautentisering krävs tillhandahåller appen en kod för användaren. Användaren uppmanas att använda en annan enhet, till exempel en Internet-ansluten smartphone, för att gå till en URL, till exempel `https://microsoft.com/devicelogin` . Sedan uppmanas användaren att ange koden. På så sätt får webb sidan användaren en normal autentisering, som innehåller medgivande frågor och Multi-Factor Authentication, om det behövs.

2. Vid lyckad autentisering tar kommando rads appen emot de token som krävs via en back kanal och använder dem för att utföra de webb-API-anrop som IT behöver.

### <a name="use-it"></a>Använd den

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`innehåller en metod med namnet `AcquireTokenWithDeviceCode` .

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Den här metoden tar som parametrar:

- `scopes`Att begära en åtkomsttoken för.
- Ett återanrop som tar emot `DeviceCodeResult` .

  ![Egenskaper för DeviceCodeResult](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Följande exempel kod visar sammanfattningen av de mest aktuella fallen, med förklaringar av vilken typ av undantag som du kan få och hur de kan undvikas. Ett fullständigt funktionellt kod exempel finns i [Active-Directory-dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) på GitHub.

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }
}

private static async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }

    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Detta utdrag är från [MSAL java dev-exempel](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Detta utdrag är från [MSAL python-dev-exempel](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

# <a name="macos"></a>[macOS](#tab/macOS)

Det här flödet gäller inte för macOS.

---

## <a name="file-based-token-cache"></a>Filbaserad token-cache

I MSAL.NET anges en token i minnet som standard.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>Serialisering är anpassningsbar i Windows-skrivbordsappar och webbappar eller webb-API: er

Om du inte gör något extra i .NET Framework-och .NET Core varar token i minnet i cacheminnet för programmets varaktighet. För att förstå varför serialisering inte har angetts direkt, kom ihåg att MSAL .NET Desktop-eller .NET Core-program kan vara konsol-eller Windows-program (som skulle ha åtkomst till fil systemet), *men även* webb program eller webb-API: er. Dessa webbappar och webb-API: er kan använda vissa olika cache-mekanismer som databaser, distribuerade cacheminnen och Redis-cache. Om du vill ha ett program med beständig token cache i .NET Desktop eller .NET Core måste du anpassa serialiseringen.

Klasser och gränssnitt som ingår i cachelagring av token cache är följande typer:

- ``ITokenCache``, som definierar händelser för att prenumerera på begär Anden om cachelagring av token och metoder för att serialisera eller deserialisera cachen i olika format (ADAL v 3.0, MSAL 2. x och MSAL 3. x = ADAL v).
- ``TokenCacheCallback`` är ett återanrop som skickas till händelserna så att du kan hantera serialiseringen. De anropas med argument av typen ``TokenCacheNotificationArgs`` .
- ``TokenCacheNotificationArgs`` innehåller bara programmet ``ClientId`` och en referens till den användare som token är tillgänglig för.

  ![Diagram över cachelagring av token-cache](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET skapar token-cacheminnen för dig och ger dig `IToken` cachen när du anropar ett programs `UserTokenCache` och `AppTokenCache` Egenskaper. Du ska inte implementera gränssnittet själv. Ditt ansvar när du implementerar en anpassad token cache-serialisering är att:
>
> - Reagera på `BeforeAccess` och `AfterAccess` händelser eller deras *async* -motsvarighet. `BeforeAccess`Delegaten ansvarar för deserialisering av cacheminnet. `AfterAccess`Delegaten ansvarar för serialisering av cachen.
> - Förstå den delen av de här händelserna eller Läs in blobbar som skickas genom händelse argumentet till vilken lagring du vill ha.

Strategierna skiljer sig beroende på om du skriver en cachelagring för token-cache för ett offentligt klient program, till exempel ett skriv bord eller ett konfidentiellt klient program, till exempel en webbapp eller ett webb-API eller en daemon-app.

Sedan MSAL v2. x har du flera alternativ. Vad du väljer beror på om du vill serialisera cachen till MSAL.NET-formatet, vilket är en enhetlig format-cache som är vanlig med MSAL, men också på olika plattformar. Eller så kanske du också vill stödja den [äldre](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) cachelagringen av token i ADAL v3.

Anpassningen av cachelagring av token för att dela SSO-tillstånd mellan ADAL.NET 3. x, ADAL.NET 5. x och MSAL.NET förklaras i del av exemplet [Active-Directory-dotNet-v1-till-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

### <a name="simple-token-cache-serialization-msal-only"></a>Simple token cache-serialisering (endast MSAL)

Följande exempel är en naïve-implementering av anpassad serialisering av ett token-cache för Skriv bords program. Här finns användarens token cache i en fil i samma mapp som programmet eller, i en mapp per användare per app, i det fall där appen är ett [paketerat Skriv bords program](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-behind-the-scenes). Fullständig kod finns i följande exempel: [Active-Directory-dotNet-Desktop-msgraph-v2](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2).

När du har skapat programmet aktiverar du serialiseringen genom att anropa ``TokenCacheHelper.EnableSerialization()`` och skicka programmet `UserTokenCache` .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Den här hjälp klassen ser ut som följande kodfragment:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
   try
   {
    // For packaged desktop apps (MSIX packages) the executing assembly folder is read-only. 
    // In that case we need to use Windows.Storage.ApplicationData.Current.LocalCacheFolder.Path + "\msalcache.bin" 
    // which is a per-app read/write folder for packaged apps.
    // See https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-behind-the-scenes
    CacheFilePath = System.IO.Path.Combine(Windows.Storage.ApplicationData.Current.LocalCacheFolder.Path, "msalcache.bin3");
   }
   catch (System.InvalidOperationException)
   {
    // Fall back for an un-packaged desktop app
    CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin";
   }
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static string CacheFilePath { get; private set; }

  private static readonly object FileLock = new object();

  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

En för hands version av en filbaserad cachelagring med token för produkt kvalitet för offentliga klient program för Skriv bords program som körs på Windows, Mac och Linux är tillgänglig från biblioteket [Microsoft. Identity. client. Extensions. Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) med öppen källkod. Du kan ta med den i dina program från följande NuGet-paket: [Microsoft. Identity. client. Extensions. Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Fri skrivning: biblioteket Microsoft. Identity. client. Extensions. Msal är ett tillägg över MSAL.NET. Klasser i de här biblioteken kan göra sitt sätt i MSAL.NET i framtiden, som är eller med större ändringar.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Dubbla token cache-serialisering (MSAL Unified cache + ADAL v3)

Du kanske vill implementera serialisering av token-cache med formatet enhetligt cacheminne. Det här formatet är vanligt för ADAL.NET 4. x och MSAL.NET 2. x och andra MSALs av samma generation eller äldre versioner på samma plattform. Få inspiration av följande kod:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Den här gången ser hjälp klassen ut som följande kod:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>Erfar Åtkomst till användarens cachelagrade token i appar och tjänster i bakgrunden

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot, [anropa ett webb-API från Skriv bords appen](scenario-desktop-call-api.md).
