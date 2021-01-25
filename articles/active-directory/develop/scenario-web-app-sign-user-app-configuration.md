---
title: Konfigurera en webbapp som loggar in användare | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar en webbapp som loggar in användare (kod konfiguration)
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
ms.openlocfilehash: 45f3a066283a921f60909a4aa3cfdc76f3faad06
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753276"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Webbapp som loggar in användare: kod konfiguration

Lär dig hur du konfigurerar koden för din webbapp som loggar in användare.

## <a name="libraries-for-protecting-web-apps"></a>Bibliotek för att skydda webb program

<!-- This section can be in an include for web app and web APIs -->
De bibliotek som används för att skydda en webbapp (och ett webb-API) är:

| Plattform | Bibliotek | Beskrivning |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_NET.png) | [Identitets modells tillägg för .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Som används direkt av ASP.NET och ASP.NET Core föreslår Microsoft Identity Model-tillägg för .NET en uppsättning dll: er som körs på både .NET Framework och .NET Core. Från en ASP.NET-eller ASP.NET Core-webbapp kan du kontrol lera token-verifieringen med hjälp av **TokenValidationParameters** -klassen (särskilt i vissa partner scenarier). I praktiken kapslas komplexiteten in i biblioteket [Microsoft. Identity. Web](https://aka.ms/ms-identity-web) |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Stöd för Java-webbprogram |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Stöd för python-webbprogram |

Välj den flik som motsvarar den plattform som du är intresse rad av:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Kodfragment i den här artikeln och följande extraheras från den [stegvisa självstudien för ASP.net Core Web App, kapitel 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Du kanske vill referera till den här självstudien för fullständig implementerings information.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Kodfragment i den här artikeln och följande extraheras från [exemplet på ASP.net-webbappar](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

Du kanske vill referera till det här exemplet för fullständig implementerings information.

# <a name="java"></a>[Java](#tab/java)

Kodfragment i den här artikeln och följande extraheras från [Java-webbprogrammet som anropar Microsoft Graph-](https://github.com/Azure-Samples/ms-identity-java-webapp) EXEMPLET i MSAL Java.

Du kanske vill referera till det här exemplet för fullständig implementerings information.

# <a name="python"></a>[Python](#tab/python)

Kodfragment i den här artikeln och följande extraheras från [python-webbprogrammet som anropar Microsoft Graph-](https://github.com/Azure-Samples/ms-identity-python-webapp) EXEMPLET i MSAL python.

Du kanske vill referera till det här exemplet för fullständig implementerings information.

---

## <a name="configuration-files"></a>Konfigurationsfiler

Webb program som loggar in användare med hjälp av Microsoft Identity Platform konfigureras via konfigurationsfiler. De inställningar som du behöver fylla i är:

- Moln instansen ( `Instance` ) om du vill att din app ska köras i nationella moln, till exempel
- Mål gruppen i klient-ID: t ( `TenantId` )
- Klient-ID ( `ClientId` ) för ditt program, som kopieras från Azure Portal

Ibland kan program parametrized av `Authority` , vilket är en sammanfogning av `Instance` och `TenantId` .

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

I ASP.NET Core finns de här inställningarna i [appsettings.jspå](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) filen i avsnittet "AzureAd".

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath": "/signout-oidc"
  }
}
```

I ASP.NET Core innehåller en annan fil ([properties\launchSettings.jspå](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) URL ( `applicationUrl` ) och TLS/SSL-porten ( `sslPort` ) för ditt program och olika profiler.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

I Azure Portal måste de svars-URI: er som du måste registrera på sidan **autentisering** för programmet matcha dessa URL: er. För de två föregående konfigurationsfilerna är de `https://localhost:44321/signin-oidc` . Orsaken är att `applicationUrl` `http://localhost:3110` , men har `sslPort` angetts (44321). `CallbackPath` är `/signin-oidc` , enligt definitionen i `appsettings.json` .

På samma sätt skulle utloggnings-URI: n vara inställd på `https://localhost:44321/signout-oidc` .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.NET konfigureras programmet via [Web.config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) -filen, rader 12 till 15.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

I Azure Portal måste de svars-URI: er som du måste registrera på sidan **autentisering** för programmet matcha dessa URL: er. Det vill säga att de ska vara `https://localhost:44326/` .

# <a name="java"></a>[Java](#tab/java)

I Java finns konfigurationen i filen [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) , som finns under `src/main/resources` .

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

I Azure Portal måste de svars-URI: er som du måste registrera på sidan **autentisering** för programmet matcha de `redirectUri` instanser som programmet definierar. Det vill säga att de ska vara `http://localhost:8080/msal4jsample/secure/aad` och `http://localhost:8080/msal4jsample/graph/me` .

# <a name="python"></a>[Python](#tab/python)

Här är python-konfigurationsfilen i [app_config. py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py):

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Den här snabb starten planerar att lagra klient hemligheten i konfigurations filen för enkelhetens skull. I din webbapp skulle du vilja använda andra sätt att lagra din hemliga information, till exempel ett nyckel valv eller en miljö variabel enligt beskrivningen i dokumentationen om [flaskor](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables).
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Initierings kod

Initierings koden skiljer sig beroende på plattform. För ASP.NET Core och ASP.NET delegeras inloggnings användare till OpenID Connect-mellanprogram. ASP.NET-eller ASP.NET Core-mallen genererar webb program för Azure Active Directory (Azure AD) v 1.0-slutpunkten. En del konfiguration krävs för att anpassa dem till Microsoft Identity Platform. När det gäller Java hanteras det av våren med programmets samarbete.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

I ASP.NET Core Web Apps (och webb-API: er) skyddas programmet eftersom du har ett `[Authorize]` attribut på styrenheterna eller styrenhets åtgärderna. Det här attributet kontrollerar att användaren är autentiserad. Koden som initierar programmet finns i filen *startup.cs* .

Om du vill lägga till autentisering med Microsoft Identity Platform (tidigare Azure AD v 2.0) måste du lägga till följande kod. Kommentarerna i koden bör vara själv för klar Ande.

> [!NOTE]
> Om du vill börja direkt med de nya ASP.NET Core-mallarna för Microsoft Identity Platform, som utnyttjar Microsoft. Identity. Web, kan du hämta ett för hands versions paket som innehåller projektmallar för .NET Core 3,1 och .NET 5,0. Sedan kan du direkt instansiera ASP.NET Core webb program (MVC eller blixt) när du har installerat. Mer information finns i [Microsoft. Identity. Web webbapp Project templates](https://aka.ms/ms-id-web/webapp-project-templates) . Detta är det enklaste sättet att utföra stegen nedan.
>
> Om du föredrar att starta projektet med det aktuella standard ASP.NET Core-webbprojektet i Visual Studio eller genom `dotnet new mvc --auth SingleAuth` att använda eller `dotnet new webapp --auth SingleAuth` kan du se kod som följande:
>
>```c#
>  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
>          .AddAzureAD(options => Configuration.Bind("AzureAd", options));
> ```
>
> Den här koden använder det bakåtkompatibla NuGet-paketet **Microsoft. AspNetCore. Authentication. AzureAD. UI** som används för att skapa ett Azure AD v 1.0-program. Den här artikeln beskriver hur du skapar ett Microsoft Identity Platform-program (Azure AD v 2.0) som ersätter koden.
>

1. Lägg till NuGet-paketen [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) och [Microsoft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) i projektet. Ta bort paketet Microsoft. AspNetCore. Authentication. AzureAD. UI NuGet om det finns.

2. Uppdatera koden i `ConfigureServices` så att den använder- `AddMicrosoftIdentityWebAppAuthentication` och- `AddMicrosoftIdentityUI` metoderna.

   ```c#
   public class Startup
   {
    ...
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
     services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd");

     services.AddRazorPages().AddMvcOptions(options =>
     {
      var policy = new AuthorizationPolicyBuilder()
                    .RequireAuthenticatedUser()
                    .Build();
      options.Filters.Add(new AuthorizeFilter(policy));
     }).AddMicrosoftIdentityUI();
    ```

3. I `Configure` -metoden i *startup.cs* aktiverar du autentisering med ett anrop till `app.UseAuthentication();`

   ```c#
   // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
    // more code here
    app.UseAuthentication();
    app.UseAuthorization();
    // more code here
   }
   ```

I koden ovan:
- `AddMicrosoftIdentityWebAppAuthentication`Tilläggs metoden definieras i **Microsoft. Identity. Web**. Företaget
  - Lägger till Autentiseringstjänsten.
  - Konfigurerar alternativ för att läsa konfigurations filen (här från avsnittet "AzureAD")
  - Konfigurerar anslutnings alternativen för OpenID så att utfärdaren är Microsoft Identity Platform.
  - Verifierar utfärdaren av token.
  - Säkerställer att de anspråk som motsvarar namnet mappas från `preferred_username` anspråket i ID-token.

- Förutom konfigurationsobjektet kan du ange namnet på konfigurations avsnittet när du anropar `AddMicrosoftIdentityWebAppAuthentication` . Som standard är det `AzureAd` .

- `AddMicrosoftIdentityWebAppAuthentication` har andra parametrar för avancerade scenarier. Till exempel kan spårning av OpenID ansluta mellanprogram händelser hjälpa dig att felsöka ditt webb program om autentiseringen inte fungerar. Om du anger den valfria parametern `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` till `true` visas hur information bearbetas med en uppsättning ASP.net Core mellanprogram när den fortskrider från http-svaret till användarens identitet i `HttpContext.User` .

- `AddMicrosoftIdentityUI`Tilläggs metoden definieras i **Microsoft. Identity. Web. UI**. Den tillhandahåller en standardkontrollant för att hantera inloggning och utloggning.

Du hittar mer information om hur Microsoft. Identity. Web ger dig möjlighet att skapa webbappar i <https://aka.ms/ms-id-web/webapp>

> [!WARNING]
> För närvarande stöder inte Microsoft. Identity. Web scenariot för **enskilda användar konton** (som lagrar användar konton i appen) när du använder Azure AD som och extern inloggnings leverantör. Mer information finns i: [AzureAD/Microsoft-Identity-Web # 133](https://github.com/AzureAD/microsoft-identity-web/issues/133)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Koden som är relaterad till autentisering i en ASP.NET-webbapp och webb-API: er finns i [App_Start/startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) -filen.

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

Java-exemplet använder våren-ramverket. Programmet skyddas eftersom du implementerar ett filter, vilket fångar varje HTTP-svar. I snabb starten för Java-webbappar är det här filtret `AuthFilter` i `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java` .

Filtret bearbetar OAuth 2,0 Authorization Code Flow och kontrollerar om användaren är autentiserad ( `isAuthenticated()` metod). Om användaren inte är autentiserad, beräknar den URL: en för Azure AD-Auktoriseringens slut punkter och omdirigerar webbläsaren till denna URI.

När svaret anländer, som innehåller auktoriseringskod, kommer den att förvärva token med hjälp av MSAL Java. När den slutligen tar emot token från token-slutpunkten (på omdirigerings-URI: n) är användaren inloggad.

Mer information finns i `doFilter()` metoden i [AuthFilter. java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> Koden för `doFilter()` är skriven i en något annorlunda ordning, men flödet är det som beskrivs.

Mer information om det auktoriseringskod som den här metoden utlöser finns i [Microsoft Identity Platform och OAuth 2,0 Authorization Code Flow](v2-oauth2-auth-code-flow.md).

# <a name="python"></a>[Python](#tab/python)

Python-exemplet använder kolv. Avställningen av kolven och MSAL python görs i [appen. py # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Nästa steg

I nästa artikel får du lära dig hur du utlöser inloggning och utloggning.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Gå vidare till nästa artikel i det här scenariot, [Logga in och logga ut](./scenario-web-app-sign-user-sign-in.md?tabs=aspnetcore).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Gå vidare till nästa artikel i det här scenariot, [Logga in och logga ut](./scenario-web-app-sign-user-sign-in.md?tabs=aspnet).

# <a name="java"></a>[Java](#tab/java)

Gå vidare till nästa artikel i det här scenariot, [Logga in och logga ut](./scenario-web-app-sign-user-sign-in.md?tabs=java).

# <a name="python"></a>[Python](#tab/python)

Gå vidare till nästa artikel i det här scenariot, [Logga in och logga ut](./scenario-web-app-sign-user-sign-in.md?tabs=python).

---
