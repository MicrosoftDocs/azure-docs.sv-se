---
title: Skriv en webbapp som loggar in/ut-användare | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar en webbapp som loggar in/ut-användare
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: f8fa5532a5664741c9ddb9b78b35d5eed8e2e4e0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937846"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Webbapp som loggar in användare: inloggning och utloggning

Lär dig hur du lägger till inloggning till koden för din webbapp som loggar in användare. Sedan lär du dig hur du kan logga ut.

## <a name="sign-in"></a>Logga in

Inloggning består av två delar:

- Inloggnings knappen på HTML-sidan
- Inloggnings åtgärden i bakomliggande kod i kontrollanten

### <a name="sign-in-button"></a>Inloggnings knapp

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

I ASP.NET Core, för Microsoft Identity Platform-program, visas knappen **Logga** in i `Views\Shared\_LoginPartial.cshtml` (för en MVC-app) eller `Pages\Shared\_LoginPartial.cshtm` (för en kniv-app). Den visas bara när användaren inte är autentiserad. Det innebär att den visas när användaren ännu inte har loggat in eller har loggat ut. På det motsatt visas knappen **Logga ut** när användaren redan är inloggad. Observera att konto styrenheten är definierad i NuGet **-paketet Microsoft. Identity. Web. UI** i avsnittet med namnet **MicrosoftIdentity**

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.NET MVC visas knappen Logga ut i `Views\Shared\_LoginPartial.cshtml` . Den visas bara när det finns ett autentiserat konto. Det innebär att den visas när användaren har loggat in tidigare.

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

I vår Java snabb start finns inloggnings knappen i [main/Resources/templates/index.html-](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/resources/templates/index.html) filen.

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="python"></a>[Python](#tab/python)

I python-snabb starten finns det ingen inloggnings knapp. Bakomliggande kod efterfrågar automatiskt användaren för inloggning när den når webbappens rot. Se [app. py # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn` åtgärd för kontrollanten

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

I ASP.NET utlöser  `SignIn` åtgärden på kontroll panelen genom att välja inloggnings knappen i webbappen `AccountController` . I tidigare versioner av ASP.NET Core-mallarna `Account` har kontrollanten bäddats in med webbappen. Det är inte längre fallet eftersom kontrollanten nu är en del av NuGet **-paketet Microsoft. Identity. Web. UI** . Se [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) för mer information.

Den här styrenheten hanterar också Azure AD B2C program.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.NET utlöses utloggning från- `SignOut()` metoden på en kontrollant (till exempel [AccountController. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Den här metoden är inte en del av ASP.NET-ramverket (som strider mot vad som händer i ASP.NET Core). Den skickar en OpenID inloggnings utmaning när du föreslår en omdirigerings-URI.

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

I Java hanteras utloggning genom att anropa Microsoft Identity Platform- `logout` slutpunkten direkt och ange `post_logout_redirect_uri` värdet. Mer information finns i [AuthPageController. java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="python"></a>[Python](#tab/python)

Till skillnad från andra plattformar, tar MSAL python hand om att låta användaren logga in från inloggnings sidan. Se [app. py # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

`_build_msal_app()`Metoden definieras i [app. py # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) enligt följande:

```Python
def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

När användaren har loggat in på din app vill du aktivera dem för att logga ut.

## <a name="sign-out"></a>Logga ut

Att logga ut från en webbapp omfattar mer än att ta bort information om det inloggade kontot från webbappens tillstånd.
Webbappen måste också omdirigera användaren till Microsoft Identity Platform- `logout` slutpunkten för att logga ut.

När din webbapp omdirigerar användaren till `logout` slut punkten rensar den här slut punkten användarens session från webbläsaren. Om din app inte gick till `logout` slut punkten, kommer användaren att autentiseras igen till din app utan att ange sina autentiseringsuppgifter igen. Orsaken är att de har en giltig enkel inloggnings-session med Microsoft Identity Platform.

Mer information finns i avsnittet [skicka en utloggnings förfrågan](v2-protocols-oidc.md#send-a-sign-out-request) i dokumentationen för [Microsoft Identity Platform och OpenID Connect Protocol](v2-protocols-oidc.md) .

### <a name="application-registration"></a>Programregistrering

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Under program registreringen registrerar du en utloggnings-URL för frontend-kanal. I vår själv studie kurs registrerade du `https://localhost:44321/signout-oidc` i fältet Start **-och utloggnings-URL** på sidan **autentisering** . Mer information finns i [Registrera webApp-appen](scenario-web-app-sign-user-app-registration.md#register-an-app-by-using-the-azure-portal).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Under program registreringen behöver du inte registrera en extra start-URL för klient delen. Appen kommer att anropas igen på huvud-URL: en. 

# <a name="java"></a>[Java](#tab/java)

Ingen loggnings-URL för klient sidan krävs i program registreringen.

# <a name="python"></a>[Python](#tab/python)

Under program registreringen behöver du inte registrera en extra start-URL för klient delen. Appen kommer att anropas igen på huvud-URL: en.

---

### <a name="sign-out-button"></a>Knappen Logga ut

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

I ASP.NET utlöser åtgärden på styrenheten genom att välja knappen **Logga ut** i webbappen `SignOut` `AccountController` (se nedan)

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.NET MVC visas knappen Logga ut i `Views\Shared\_LoginPartial.cshtml` . Den visas bara när det finns ett autentiserat konto. Det innebär att den visas när användaren har loggat in tidigare.

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

I vår Java snabb start finns knappen Logga ut i Main/Resources/templates/auth_page.html-filen.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="python"></a>[Python](#tab/python)

I python-snabb starten finns knappen Logga ut i filen [templates/index.html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) .

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python web app</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut` åtgärd för kontrollanten

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

I tidigare versioner av ASP.NET Core-mallarna `Account` har kontrollanten bäddats in med webbappen. Det är inte längre fallet eftersom kontrollanten nu är en del av NuGet **-paketet Microsoft. Identity. Web. UI** . Se [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) för mer information.

- Ställer in en OpenID omdirigerings-URI till `/Account/SignedOut` så att styrenheten rings tillbaka när Azure AD har slutfört utloggningen.
- Anrop `Signout()` , som låter OpenID ansluta mellanprogram kontakta Microsoft Identity Platform- `logout` slutpunkten. Slut punkten sedan:

  - Tar bort sessions-cookien från webbläsaren.
  - Anropar omdirigerings-URI för efter utloggning. Som standard visar omdirigerings-URI: n för den inloggade [SignedOut.cshtml.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Pages/Account/SignedOut.cshtml.cs)på sidan. Den här sidan tillhandahålls också som en del av Microsoft. Identity. Web.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.NET utlöses utloggning från- `SignOut()` metoden på en kontrollant (till exempel [AccountController. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Den här metoden är inte en del av ASP.NET-ramverket, i motsats till vad som händer i ASP.NET Core. Företaget

- Skickar en OpenID-utloggnings utmaning.
- Rensar cacheminnet.
- Omdirigerar till den sida som den vill ha.

```csharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="java"></a>[Java](#tab/java)

I Java hanteras utloggning genom att anropa Microsoft Identity Platform- `logout` slutpunkten direkt och ange `post_logout_redirect_uri` värdet. Mer information finns i [AuthPageController. java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="python"></a>[Python](#tab/python)

Den kod som används för att logga ut användaren finns i [appen. py # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Spärr av anrop till `logout` slut punkten

Med hjälp av URI: n efter utloggning kan program delta i den globala utloggningen.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core OpenID Connect mellan program vara gör att din app kan fånga upp anropet till Microsoft Identity Platform- `logout` slutpunkten genom att tillhandahålla en OpenID Connect-händelse med namnet `OnRedirectToIdentityProviderForSignOut` . Detta hanteras automatiskt av Microsoft. identitet. Web (som rensar konton i de fall där webbappen anropar webb-API: er)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.NET delegerar du till mellanprogram för att köra utloggningen och rensa sessionens cookie:

```csharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="java"></a>[Java](#tab/java)

I Java-snabb starten visar omstarts-URI för omutloggning bara sidan index.html.

# <a name="python"></a>[Python](#tab/python)

I python-snabb starten visar omstarts-URL: en för omutloggning bara sidan index.html.

---

## <a name="protocol"></a>Protokoll

Om du vill veta mer om utloggning läser du protokoll dokumentationen som är tillgänglig från [Öppna-ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot, [Flytta till produktion](scenario-web-app-sign-user-production.md).