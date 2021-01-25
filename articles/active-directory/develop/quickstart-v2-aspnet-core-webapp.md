---
title: 'Snabb start: lägga till inloggning med Microsoft i ett ASP.NET Core-webbprogram | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur en app implementerar Microsoft-inloggning på en ASP.NET Core webbapp med OpenID Connect
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/11/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: c20685c0c392ece68dc80b57afc6680f2efd9e35
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754509"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Snabb start: lägga till inloggning med Microsoft i ett ASP.NET Core-webbprogram

I den här snabb starten laddar du ned och kör ett kod exempel som visar hur en ASP.NET Core webbapp kan logga in användare från valfri Azure Active Directory (Azure AD)-organisation.  

Se [hur exemplet fungerar](#how-the-sample-works) för en illustration.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Krav
>
> * [Visual studio 2019](https://visualstudio.microsoft.com/vs/) eller [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1 +](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till snabb starts upplevelsen för <a href="https://aka.ms/aspnetcore2-1-aad-quickstart-v2/" target="_blank">Azure Portal-Appregistreringar <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
> 1. Sök efter och välj **Azure Active Directory**.
> 1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
> 1. Ange ett **namn** för programmet, till exempel `AspNetCore-Quickstart` . Användare av appen kan se det här namnet och du kan ändra det senare.
> 1. Ange en **omdirigerings-URI** för `https://localhost:44321/` .
> 1. Välj **Register** (Registrera).
> 1. Under **Hantera** väljer du **autentisering**.
> 1. Under **omdirigerings-URI: er** väljer du **Lägg till URI** och anger sedan `https://localhost:44321/signin-oidc` .
> 1. Ange en **utloggnings-URL** för `https://localhost:44321/signout-oidc` .
> 1. Under **Implicit beviljande** väljer du **ID-token**.
> 1. Välj **Spara**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
> För kodexemplen för att den här snabbstarten ska fungera måste du lägga till svars-URL som `https://localhost:44321/` och `https://localhost:44321/signin-oidc`, lägga till Utloggnings-URL som `https://localhost:44321/signout-oidc` och begära att ID-token ska utfärdas av auktoriseringsslutpunkten.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-aspnet-webapp/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-your-aspnet-core-project"></a>Steg 2: Ladda ned ditt ASP.NET Core-projekt

> [!div renderon="docs"]
> [Ladda ned ASP.NET Core-lösningen](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Kör projektet.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

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
>    ```
>
>    - Ersätt `Enter_the_Application_Id_here` med **program-ID: t (klient)** för det program som du registrerade i Azure Portal. Du kan hitta **Program-ID (klient)** på appens **översiktssida**.
>    - Ersätt `common` med något av följande:
>       - Om ditt program bara har stöd **för konton i den här organisations katalogen** ersätter du det här värdet med **katalogen (klient) ID** (ett GUID) eller **klient namn** (till exempel `contoso.onmicrosoft.com` ). Du hittar **klient-ID: t för katalogen (klient)** på appens **översikts** sida.
>       - Om ditt program stöder **Konton i valfri organisationskatalog** ersätter du värdet med `organizations`
>       - Om programmet har stöd för **alla Microsoft-konto användare**, lämna det här värdet som `common`
>
> I den här snabb starten ska du inte ändra andra värden i *appsettings.js* i filen.
>
> #### <a name="step-4-build-and-run-the-application"></a>Steg 4: Skapa och kör programmet
>
> Skapa och kör appen i Visual Studio genom att välja menyn **felsök** > **Starta fel sökning** eller genom att trycka på `F5` nyckeln.
>
> Du uppmanas att ange dina autentiseringsuppgifter och sedan uppmanas att godkänna de behörigheter som krävs för din app. Välj **Godkänn** i medgivande frågan.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Dialog rutan medgivande som visar de behörigheter som appen begär från > användare":::
>
> När du har samtyckt till de begärda behörigheterna visar appen att du har loggat in med dina Azure Active Directory autentiseringsuppgifter.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Webbläsaren som visar den webbapp som körs och användaren är inloggad":::

## <a name="more-information"></a>Mer information

Det här avsnittet ger en översikt över den kod som krävs för att logga in användare. Den här översikten kan vara användbar för att förstå hur koden fungerar, huvudsakliga argument och även om du vill lägga till inloggning till ett befintligt ASP.NET Core-program.

### <a name="how-the-sample-works"></a>Så här fungerar exemplet
![Visar hur exempel appen som genereras av den här snabb starten fungerar](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Startklass

Mellanprogram *Microsoft. AspNetCore. Authentication* använder en `Startup` klass som körs när värd processen initieras:

```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

`AddAuthentication()`Metoden konfigurerar tjänsten för att lägga till cookie-baserad autentisering, som används i webb läsar scenarier och för att ställa in utmaningen till OpenID Connect.

Raden som innehåller `.AddMicrosoftIdentityWebApp` lägger till Microsoft Identity Platform-autentisering till ditt program. Den konfigureras sedan att logga in med Microsoft Identity Platform baserat på informationen i `AzureAD` avsnittet i *appsettings.jsi* konfigurations filen:

| *appsettings.jspå* nyckel | Beskrivning                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **Program-ID** för programmet som är registrerat i Azure Portal.                                                                                       |
| `Instance`             | STS-slutpunkt (Security Token Service) för användaren att autentisera. Det här värdet är vanligt vis `https://login.microsoftonline.com/` som anger det offentliga Azure-molnet. |
| `TenantId`             | Namnet på din klient organisation eller dess klient-ID (ett GUID) eller *vanligt* för att logga in användare med arbets-eller skol konton eller personliga Microsoft-konton.                             |

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

Du kan skydda en kontrollant eller kontrollantmetoder med attributet `[Authorize]`. Det här attributet begränsar åtkomsten till styrenheten eller metoderna genom att bara tillåta autentiserade användare, vilket innebär att autentiseringen kan startas för att få åtkomst till kontrollanten om användaren inte är autentiserad.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

GitHub-lagrings platsen som innehåller den här ASP.NET Core själv studie kursen innehåller instruktioner och exempel på fler kod exempel som visar hur du:

- Lägg till autentisering i ett nytt ASP.NET Core-webbprogram
- Anropa Microsoft Graph, andra Microsoft API: er eller dina egna webb-API: er
- Lägg till auktorisering
- Logga in användare i nationella moln eller med sociala identiteter

> [!div class="nextstepaction"]
> [ASP.NET Core själv studie kurser om Web Apps på GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
