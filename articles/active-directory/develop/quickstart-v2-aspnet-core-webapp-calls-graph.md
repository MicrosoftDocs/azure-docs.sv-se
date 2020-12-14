---
title: 'Snabb start: ASP.NET Core webbapp som loggar in användare och anropar Microsoft Graph | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur en app utnyttjar Microsoft. Identity. Web för att implementera Microsoft-inloggning i en ASP.NET Core webbapp med OpenID Connect och calls Microsoft Graph
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/10/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 95a0dbd61e44e9c5b1f2854f8b3215aa4ef9403c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97439651"
---
# <a name="quickstart-aspnet-core-web-app-that-signs-in-users-and-calls-microsoft-graph-on-their-behalf"></a>Snabb start: ASP.NET Core-webbapp som loggar in användare och anropar Microsoft Graph för deras räkning

I den här snabb starten laddar du ned och kör ett kod exempel som visar hur en ASP.NET Core webbapp kan logga in användare från valfri Azure Active Directory (Azure AD)-organisation och anropar Microsoft Graph.  

Se [hur exemplet fungerar](#how-the-sample-works) för en illustration.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Krav
>
> * [Visual studio 2019](https://visualstudio.microsoft.com/vs/) eller [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1 +](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-quickstart-app"></a>Registrera och hämta snabb starts appen
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till [Azure Portal-Appregistreringar](https://aka.ms/aspnetcore-webapp-calls-graph-quickstart-v2).
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com).
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
> 1. Sök efter och välj **Azure Active Directory**.
> 1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
> 1. Ange ett **namn** för programmet, till exempel `AspNetCoreWebAppCallsGraph-Quickstart` . Användare av appen kan se det här namnet och du kan ändra det senare.
> 1. Ange en **omdirigerings-URI** för `https://localhost:44321/signin-oidc`
> 1. Välj **Register** (Registrera).
> 1. Under **Hantera** väljer du **autentisering**.
> 1. Ange en **utloggnings-URL** för `https://localhost:44321/signout-oidc`
> 1. Välj **Spara**.
> 1. Under **Hantera** väljer du **certifikat & hemligheter**  >  **ny klient hemlighet**.
> 1. Ange en **Beskrivning**, till exempel `clientsecret1` .
> 1. Välj om du vill att hemlighetens förfallo datum ska gå ut **i 1 år** .
> 1. Välj **Lägg till** och registrera hemlighetens **värde** omedelbart för användning i ett senare steg. Det hemliga värdet *visas aldrig igen* och är irretrievable på något annat sätt. Registrera den på en säker plats som ett valfritt lösen ord.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
> För att kod exemplet för den här snabb starten ska fungera måste du lägga till svars-URL: er som `https://localhost:44321/signin-oidc` , lägga till URL: en för utloggning som `https://localhost:44321/signout-oidc` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-aspnet-webapp/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-aspnet-core-project"></a>Steg 2: Ladda ned ASP.NET Core-projektet

> [!div renderon="docs"]
> [Ladda ned ASP.NET Core-lösningen](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Kör projektet.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Steg 3: appen har kon figurer ATS och är redo att köras
> Vi har konfigurerat ditt projekt med värdena för appens egenskaper och är redo att köras.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Steg 3: Konfigurera ditt ASP.NET Core-projekt
> 1. Extrahera. zip-arkivet till en lokal mapp nära roten på enheten. Till exempel i *C:\Azure-samples*.
> 1. Öppna lösningen i Visual Studio 2019.
> 1. Öppna filen *appsettings.js* och ändra följande:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    "clientSecret": "Enter_the_Client_Secret_Here"
>    ```
>
>    - Ersätt `Enter_the_Application_Id_here` med **program-ID: t (klient)** för det program som du registrerade i Azure Portal. Du kan hitta **Program-ID (klient)** på appens **översiktssida**.
>    - Ersätt `common` med något av följande:
>       - Om ditt program bara har stöd **för konton i den här organisations katalogen** ersätter du det här värdet med **katalogen (klient) ID** (ett GUID) eller **klient namn** (till exempel `contoso.onmicrosoft.com` ). Du hittar **klient-ID: t för katalogen (klient)** på appens **översikts** sida.
>       - Om ditt program stöder **Konton i valfri organisationskatalog** ersätter du värdet med `organizations`
>       - Om programmet har stöd för **alla Microsoft-konto användare**, lämna det här värdet som `common`
>    - Ersätt `Enter_the_Client_Secret_Here` med den **klient hemlighet** som du skapade och registrerade i ett tidigare steg.
> 
> I den här snabb starten ska du inte ändra andra värden i *appsettings.js* i filen.
>
> #### <a name="step-4-build-and-run-the-application"></a>Steg 4: Skapa och kör programmet
>
> Skapa och kör appen i Visual Studio genom att välja menyn **felsök** > **Starta fel sökning** eller genom att trycka på `F5` nyckeln.
>
> Du uppmanas att ange dina autentiseringsuppgifter och sedan uppmanas att godkänna de behörigheter som krävs för din app. Välj **Godkänn** i medgivande frågan.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-01-consent.png" alt-text="Dialog rutan medgivande som visar de behörigheter som appen begär från > användare":::
>
> När du har samtyckt till de begärda behörigheterna visar appen att du har loggat in med dina Azure Active Directory autentiseringsuppgifter och du ser din e-postadress i avsnittet "API result" på sidan. Detta extraherades med hjälp av Microsoft Graph.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-02-signed-in.png" alt-text="Webbläsaren som visar den webbapp som körs och användaren är inloggad":::

## <a name="about-the-code"></a>Om koden

Det här avsnittet ger en översikt över den kod som krävs för att logga in användare och anropa Microsoft Graph-API: et för deras räkning. Den här översikten kan vara användbar för att förstå hur koden fungerar, huvudsakliga argument och även om du vill lägga till inloggning till ett befintligt ASP.NET Core program och anropa Microsoft Graph. Den använder [Microsoft. Identity. Web](microsoft-identity-web.md), som är en omslutning runt [MSAL.net](msal-overview.md).

### <a name="how-the-sample-works"></a>Så här fungerar exemplet
![Visar hur exempel appen som genereras av den här snabb starten fungerar](media/quickstart-v2-aspnet-core-webapp-calls-graph/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Startklass

Mellanprogram *Microsoft. AspNetCore. Authentication* använder en `Startup` klass som körs när värd processen initieras:

```csharp
  // Get the scopes from the configuration (appsettings.json)
  var initialScopes = Configuration.GetValue<string>("DownstreamApi:Scopes")?.Split(' ');

  public void ConfigureServices(IServiceCollection services)
  {
      // Add sign-in with Microsoft
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))

            // Add the possibility of acquiring a token to call a protected web API
            .EnableTokenAcquisitionToCallDownstreamApi(initialScopes)

                // Enables controllers and pages to get GraphServiceClient by dependency injection
                // And use an in memory token cache
                .AddMicrosoftGraph(Configuration.GetSection("DownstreamApi"))
                .AddInMemoryTokenCaches();

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });

      // Enables a UI and controller for sign in and sign out.
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

`AddAuthentication()`Metoden konfigurerar tjänsten för att lägga till cookie-baserad autentisering, som används i webb läsar scenarier och för att ställa in utmaningen till OpenID Connect.

Raden som innehåller `.AddMicrosoftIdentityWebApp` lägger till Microsoft Identity Platform-autentisering till ditt program. Detta tillhandahålls av [Microsoft. Identity. Web](microsoft-identity-web.md). Den konfigureras sedan att logga in med hjälp av Microsoft Identity Platform-slutpunkten baserat på informationen i `AzureAD` avsnittet i *appsettings.jsi* konfigurations filen:

| *appsettings.jspå* nyckel | Beskrivning                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **Program-ID** för programmet som är registrerat i Azure Portal.                                                                                       |
| `Instance`             | STS-slutpunkt (Security Token Service) för användaren att autentisera. Det här värdet är vanligt vis `https://login.microsoftonline.com/` som anger det offentliga Azure-molnet. |
| `TenantId`             | Namnet på din klient organisation eller dess klient-ID (ett GUID) eller *vanligt* för att logga in användare med arbets-eller skol konton eller personliga Microsoft-konton.                             |

`EnableTokenAcquisitionToCallDownstreamApi`Metoden gör att ditt program kan hämta en token för att anropa skyddade webb-API: er. `AddMicrosoftGraph` gör det möjligt för dina kontrollanter eller kniv sidor att dra nytta av direkt `GraphServiceClient` (genom beroende insprutning) och `AddInMemoryTokenCaches` metoderna gör att din app kan dra nytta av en token-cache.

`Configure()`Metoden innehåller två viktiga metoder `app.UseAuthentication()` `app.UseAuthorization()` som aktiverar de namngivna funktionerna. I `Configure()` -metoden måste du också registrera Microsofts identitets Webbs vägar med minst ett anrop till `endpoints.MapControllerRoute()` eller ett anrop till `endpoints.MapControllers()` .

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Skydda en kontrollant eller en kontrollants metod

Du kan skydda en kontrollant eller dess metoder genom att använda `[Authorize]` attributet för kontroll enhetens klass eller en eller flera av dess metoder. Det här `[Authorize]` attributet begränsar åtkomsten genom att endast tillåta autentiserade användare. Om användaren inte redan har autentiserats kan en autentiserings-utmaning startas för att få åtkomst till kontrollanten. I den här snabb starten läses omfattningarna från konfigurations filen:

```CSharp
[AuthorizeForScopes(ScopeKeySection = "DownstreamApi:Scopes")]
public async Task<IActionResult> Index()
{
    var user = await _graphServiceClient.Me.Request().GetAsync();
    ViewData["ApiResult"] = user.DisplayName;

    return View();
}
 ```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

GitHub-lagrings platsen som innehåller ASP.NET Core kod exemplet som refereras i den här snabb starten innehåller instruktioner och exempel på fler kod exempel som visar hur du:

- Lägg till autentisering i ett nytt ASP.NET Core-webbprogram
- Anropa Microsoft Graph, andra Microsoft API: er eller dina egna webb-API: er
- Lägg till auktorisering
- Logga in användare i nationella moln eller med sociala identiteter

> [!div class="nextstepaction"]
> [ASP.NET Core själv studie kurser om Web Apps på GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
