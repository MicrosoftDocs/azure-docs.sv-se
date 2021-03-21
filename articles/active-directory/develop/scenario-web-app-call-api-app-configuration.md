---
title: 'Konfigurera en webbapp som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du konfigurerar koden för en webbapp som anropar webb-API: er'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: aa377547f7f4961e199ec8d62bf0f1435296f983
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104669312"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>En webbapp som anropar webb-API: er kod konfiguration

Som du ser i [webbappen som loggar in i användar](scenario-web-app-sign-user-overview.md) scenariot använder webbappen [OAuth 2,0-auktoriseringskod](v2-oauth2-auth-code-flow.md) för att signera användaren i. Det här flödet har två steg:

1. Begär en auktoriseringskod. Den här delen delegerar en privat dialog med användaren till Microsoft Identity Platform. Under den dialogen loggar användaren in och samverkar till användningen av webb-API: er. När den privata dialog rutan avslutas får webbappen en auktoriseringskod på omdirigerings-URI: n.
1. Begär en åtkomsttoken för API: et genom att lösa in auktoriseringskod.

[Webbappen som loggar in i användar](scenario-web-app-sign-user-overview.md) scenarier täcker bara det första steget. Här kan du lära dig hur du ändrar din webbapp så att den inte bara signerar användare i, men nu anropar webb-API: er.

## <a name="microsoft-libraries-supporting-web-apps"></a>Microsoft-bibliotek som stöder webbappar

Följande Microsoft-bibliotek stöder webbappar:

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

Välj fliken för den plattform som du är intresse rad av:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>Klient hemligheter eller klient certifikat

Under förutsättning att webbappen nu anropar ett underordnat webb-API, anger du en klient hemlighet eller ett klient certifikat i *appsettings.js* filen. Du kan också lägga till ett avsnitt som anger:

- URL: en för det underordnade webb-API: et
- De omfattningar som krävs för att anropa API: et

I följande exempel `GraphBeta` anger avsnittet de här inställningarna.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

I stället för en klient hemlighet kan du ange ett klient certifikat. Följande kodfragment visar hur du använder ett certifikat som lagras i Azure Key Vault.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

*Microsoft. Identity. Web* tillhandahåller flera olika sätt att beskriva certifikat, både genom konfiguration eller kod. Mer information finns i [Microsoft. Identity. Web – using certificates](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) on GitHub.

## <a name="startupcs"></a>Startup.cs

Din webbapp måste hämta en token för det underordnade API: et. Du anger det genom att lägga till `.EnableTokenAcquisitionToCallDownstreamApi()` raden efter `.AddMicrosoftIdentityWebApi(Configuration)` . Den här raden visar `ITokenAcquisition` tjänsten som du kan använda i din styrenhet och sid åtgärder. Men eftersom du ser i följande två alternativ kan det göras mer enkelt. Du måste också välja en implementation av tokenbaserad cache, till exempel `.AddInMemoryTokenCaches()` i *Start. cs*:

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

De omfattningar som skickas till `EnableTokenAcquisitionToCallDownstreamApi` är valfria och gör det möjligt för din webbapp att begära omfattningar och användarens medgivande till dessa omfattningar när de loggar in. Om du inte anger omfattningarna kommer *Microsoft. Identity. Web* att möjliggöra en stegvis tillstånds upplevelse.

Om du inte vill hämta token själv ger *Microsoft. Identity. Web* två mekanismer för att anropa ett webb-API från en webbapp. Vilket alternativ du väljer beror på om du vill anropa Microsoft Graph eller något annat API.

### <a name="option-1-call-microsoft-graph"></a>Alternativ 1: anropa Microsoft Graph

Om du vill anropa Microsoft Graph kan du  direkt använda `GraphServiceClient` (som exponeras av Microsoft Graph SDK) i dina API-åtgärder med hjälp av Microsoft. Identity. Web. För att exponera Microsoft Graph:

1. Lägg till [Microsoft. Identity. Web. MicrosoftGraph NuGet-](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) paketet i projektet.
1. Lägg till `.AddMicrosoftGraph()` efter `.EnableTokenAcquisitionToCallDownstreamApi()` i filen *startup. cs* . `.AddMicrosoftGraph()` har flera åsidosättningar. Med den åsidosättning som tar ett konfigurations avsnitt som en parameter blir koden:

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>Alternativ 2: anropa ett underordnat webb-API förutom Microsoft Graph

Om du vill anropa ett webb-API förutom Microsoft Graph tillhandahåller *Microsoft. Identity. Web* `.AddDownstreamWebApi()` , som begär token och anropar det underordnade webb-API: et.

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, "AzureAd")
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="summary"></a>Sammanfattning

Precis som med webb-API: er kan du välja olika implementationer för cachelagring av token. Mer information finns i [Microsoft. Identity. Web-token cache-serialisering](https://aka.ms/ms-id-web/token-cache-serialization) på GitHub.

Följande bild visar de olika möjligheterna med *Microsoft. Identity. Web* och deras inverkan på filen *startup. cs* :

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Blockera diagram som visar tjänst konfigurations alternativ i Start punkt C S för att anropa ett webb-API och ange en implementation av token cache":::

> [!NOTE]
> För att fullständigt förstå kod exemplen här, bör du vara bekant med [ASP.net Core fundament ALS](/aspnet/core/fundamentals), särskilt med [beroende inmatning](/aspnet/core/fundamentals/dependency-injection) och [alternativ](/aspnet/core/fundamentals/configuration/options).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Eftersom användar inloggning är delegerad till mellanprogram OpenID Connect (OIDC) måste du interagera med OIDC-processen. Hur du interagerar beror på vilket ramverk du använder.

För ASP.NET ska du prenumerera på OIDC händelser för mellanprogram:

- Du får ASP.NET Core begära en auktoriseringskod med hjälp av Open-ID Connect-mellanprogram. ASP.NET eller ASP.NET Core gör det möjligt för användaren att logga in och medgivande.
- Du prenumererar på webbappen för att ta emot auktoriseringsregeln. Den här prenumerationen görs med hjälp av ett C#-ombud.
- När auktoriseringskod tas emot använder du MSAL-bibliotek för att lösa in den. De resulterande åtkomsttoken och uppdaterade tokens lagras i token-cachen. Cachen kan användas i andra delar av programmet, till exempel kontrollanter, för att hämta andra token tyst.

Kod exempel i den här artikeln och följande extraheras från exemplet på [ASP.net-webbappar](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Du kanske vill referera till det exemplet för fullständig implementerings information.

# <a name="java"></a>[Java](#tab/java)

Kod exempel i den här artikeln och följande extraheras från [Java-webbprogrammet som anropar Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), ett webb-app-exempel som använder MSAL för Java.
Exemplet tillåter för närvarande MSAL för Java att producera URL: en för auktoriseringskod och hanterar navigeringen till Authorization-slutpunkten för Microsoft Identity Platform. Du kan också använda Sprint säkerhet för att logga in användaren i. Du kanske vill referera till exemplet för fullständig implementerings information.

# <a name="python"></a>[Python](#tab/python)

Kod exempel i den här artikeln och följande extraheras från [python-webbprogrammet som anropar Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), ett exempel på en webbapp som använder MSAL. Python.
Exemplet tillåter för närvarande MSAL. Python genererar URL: en för auktoriseringskod och hanterar navigeringen till slut punkten för auktorisering för Microsoft Identity Platform. Du kanske vill referera till exemplet för fullständig implementerings information.

---

## <a name="code-that-redeems-the-authorization-code"></a>Kod som löser in auktoriseringskod

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. Identity. Web fören klar din kod genom att ange rätt OpenID Connect-inställningar, prenumerera på händelsen mottagen kod och lösa in koden. Ingen ytterligare kod krävs för att lösa in auktoriseringskod. Mer information om hur detta fungerar finns i [Microsoft. Identity. Web-källkod](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L140) .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET hanterar saker på samma sätt som ASP.NET Core, förutom att konfigurationen av OpenID Connect och prenumerationen på `OnAuthorizationCodeReceived` händelsen inträffar i [App_Start \STARTUP.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) -filen. Begreppen liknar dem i ASP.NET Core, förutom att i ASP.NET måste du ange `RedirectUri` i [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Den här konfigurationen är mindre stabil än den som finns i ASP.NET Core eftersom du måste ändra den när du distribuerar programmet.

Här är koden för start. auth. CS:

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

Se [webbapp som loggar in användare: kod konfiguration](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) för att förstå hur Java-exemplet hämtar auktoriseringskod. När appen har tagit emot koden [AuthFilter. java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Delegerar till `AuthHelper.processAuthenticationCodeRedirect` metoden i [AuthHelper. java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Anrop `getAuthResultByAuthCode` .

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

`getAuthResultByAuthCode`Metoden definieras i [AuthHelper. java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Den skapar en MSAL `ConfidentialClientApplication` och anropar sedan `acquireToken()` med `AuthorizationCodeParameters` att skapa från auktoriseringskod.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

Flow-flödet begärs som det visas i [webbappen som loggar in användare: kod konfiguration](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). Koden tas sedan emot i `authorized` funktionen, som drar vägar från `/getAToken` URL: en. Se [app. py # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) för den fullständiga kontexten för den här koden:

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

I stället för en klient hemlighet kan det konfidentiella klient programmet också bevisa sin identitet genom att använda ett klient certifikat eller en klient kontroll.
Användningen av klientens intyg är ett avancerat scenario, som beskrivs i [klient kontroll](msal-net-client-assertions.md).

## <a name="token-cache"></a>Token cache

> [!IMPORTANT]
> Implementeringen av token-cache för webbappar eller webb-API: er skiljer sig från implementeringen av Skriv bords program, som ofta är [filbaserade](scenario-desktop-acquire-token.md#file-based-token-cache).
> Av säkerhets-och prestanda skäl är det viktigt att säkerställa att för webbappar och webb-API: er det finns ett token-cache per användar konto. Du måste serialisera token-cachen för varje konto.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

I självstudien om ASP.NET Core används beroende inmatning för att du ska kunna bestämma token cache-implementering i filen startup. cs för ditt program. Microsoft. Identity. Web levereras med förbyggda token cache-serialiserare som beskrivs i [cachelagring av token](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application). En intressant möjlighet är att välja ASP.NET Core [distribuerade cacheminnen](/aspnet/core/performance/caching/distributed#distributed-memory-cache):

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi(
                initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache();

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Mer information om token-cache-providern finns i artikeln Microsoft. Identity. Web [token cache Serialization](https://aka.ms/ms-id-web/token-cache-serialization) , och i [självstudierna för ASP.net Core Web Apps | Fasen token cache](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) i guiden Web Apps.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Implementeringen av token-cache för webbappar eller webb-API: er skiljer sig från implementeringen av Skriv bords program, som ofta är [filbaserade](scenario-desktop-acquire-token.md#file-based-token-cache).

Implementeringen av webbappar kan använda ASP.NET-sessionen eller Server minnet. Se till exempel hur cache-implementeringen kopplas efter att MSAL.NET-programmet har skapats i [MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

MSAL Java tillhandahåller metoder för att serialisera och deserialisera token cache. Java-exemplet hanterar serialiseringen från sessionen, som du ser i `getAuthResultBySilentFlow` metoden i [AuthHelper. java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Detalj nivån för `SessionManagementHelper` klassen finns i [MSAL-exemplet för Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="python"></a>[Python](#tab/python)

I python-exemplet är en cache per konto säkerställd genom att du återskapar ett konfidentiellt klient program för varje begäran och sedan serialiserar det i kolvens cache:

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

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
```

---

## <a name="next-steps"></a>Nästa steg

När användaren loggar in lagras en token i token-cachen i det här läget. Nu ska vi se hur det används i andra delar av webbappen.

[Ta bort konton från cachen vid global utloggning](scenario-web-app-call-api-sign-in.md)
