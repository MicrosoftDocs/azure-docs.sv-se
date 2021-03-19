---
title: 'Snabb start: lägga till inloggning med Microsoft i ett ASP.NET Core-webbprogram | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur en app implementerar Microsoft-inloggning på en ASP.NET Core-webbapp med hjälp av OpenID Connect
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
ms.openlocfilehash: e7296b04e3e912e96ac8c2ed77b44288324c262f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578710"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Snabb start: lägga till inloggning med Microsoft i ett ASP.NET Core-webbprogram

I den här snabb starten laddar du ned och kör ett kod exempel som visar hur en ASP.NET Core webbapp kan logga in användare från valfri Azure Active Directory (Azure AD)-organisation.  

> [!div renderon="docs"]
> Följande diagram visar hur exempel appen fungerar:
>
> ![Diagram över interaktionen mellan webbläsaren, webbappen och Microsoft Identity Platform i exempel-appen.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Förutsättningar
>
> * [Visual studio 2019](https://visualstudio.microsoft.com/vs/) eller [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1 +](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-app"></a>Registrera och ladda ned appen
> Det finns två alternativ för att börja skapa ditt program: automatisk eller manuell konfiguration.
>
> ### <a name="automatic-configuration"></a>Automatisk konfiguration
> Följ dessa steg om du vill konfigurera din app automatiskt och sedan ladda ned kod exemplet:
>
> 1. Gå till <a href="https://aka.ms/aspnetcore2-1-aad-quickstart-v2/" target="_blank">Azure Portal sidan för registrering av appar</a>.
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och automatiskt konfigurera det nya programmet med ett klick.
>
> ### <a name="manual-configuration"></a>Manuell konfiguration
> Om du vill konfigurera ditt program och kod exempel manuellt använder du följande procedurer.
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: på den översta menyn för att välja den klient som du vill registrera programmet i.
> 1. Sök efter och välj **Azure Active Directory**.
> 1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
> 1. I **namn** anger du ett namn för ditt program. Ange till exempel **AspNetCore-snabb start**. Användare av appen ser det här namnet och du kan ändra det senare.
> 1. För **omdirigerings-URI** anger du **https://localhost:44321/signin-oidc** .
> 1. Välj **Register** (Registrera).
> 1. Under **Hantera** väljer du **autentisering**.
> 1. För **utloggnings-URL för frontend-kanal** anger du **https://localhost:44321/signout-oidc** .
> 1. Under **implicit beviljande och hybrid flöden** väljer du **ID-token**.
> 1. Välj **Spara**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
> Kod exemplet i den här snabb starten fungerar:
> - För **omdirigerings-URI**, anger du **https://localhost:44321/** och **https://localhost:44321/signin-oidc** .
> - För **utloggnings-URL för frontend-kanal** anger du **https://localhost:44321/signout-oidc** . 
>
> Behörighets slut punkten utfärdar ID-token för begäran.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-aspnet-webapp/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-aspnet-core-project"></a>Steg 2: Ladda ned ASP.NET Core-projektet

> [!div renderon="docs"]
> [Ladda ned ASP.NET Core-lösningen](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Kör projektet.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Steg 3: appen har kon figurer ATS och är redo att köras
> Vi har konfigurerat ditt projekt med värdena för appens egenskaper och är redo att köras.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Steg 3: Konfigurera ditt ASP.NET Core-projekt
> 1. Extrahera. zip-arkivet till en lokal mapp nära roten på enheten. Extrahera till exempel till *C:\Azure-samples*.
> 
>    Vi rekommenderar att du extraherar arkivet till en katalog nära roten på enheten för att undvika fel som orsakas av begränsningar i Sök vägs längd på Windows.
> 1. Öppna lösningen i Visual Studio 2019.
> 1. Öppna filen *appsettings.js* och ändra följande kod:
>
>    ```json
>    "Domain": "Enter the domain of your tenant, e.g. contoso.onmicrosoft.com",
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - Ersätt `Enter_the_Application_Id_here` med program-ID: t (klient) för det program som du registrerade i Azure Portal. Du hittar **ID-värdet för programmet (klienten)** på appens **översikts** sida.
>    - Ersätt `common` med något av följande:
>       - Om programmet har stöd **för konton i den här organisations katalogen** ersätter du det här värdet med katalog-ID (ett GUID) eller klient namnet (till exempel `contoso.onmicrosoft.com` ). Du hittar **ID-värdet för katalogen (klient)** på appens **översikts** sida.
>       - Om ditt program har stöd **för konton i en organisations katalog** ersätter du värdet med `organizations` .
>       - Om programmet har stöd för **alla Microsoft-konto användare**, lämna det här värdet som `common` .
>
> I den här snabb starten ska du inte ändra andra värden i *appsettings.js* i filen.
>
> #### <a name="step-4-build-and-run-the-application"></a>Steg 4: Skapa och kör programmet
>
> Skapa och kör appen i Visual Studio genom att välja menyn **felsök** > **Starta fel sökning** eller genom att trycka på F5-tangenten.
>
> Du uppmanas att ange dina autentiseringsuppgifter och sedan uppmanas att godkänna de behörigheter som din app kräver. Välj **Godkänn** i medgivande frågan.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Skärm bild av dialog rutan medgivande som visar de behörigheter som appen begär från användaren.":::
>
> När du har samtyckt till den begärda behörigheten visar appen att du har loggat in med dina Azure Active Directory autentiseringsuppgifter.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Skärm bild av en webbläsare som visar webb programmet som körs och den inloggade användaren.":::

## <a name="more-information"></a>Mer information

Det här avsnittet ger en översikt över den kod som krävs för att logga in användare. Den här översikten kan vara användbar för att förstå hur koden fungerar, vad de viktigaste argumenten är och hur du lägger till inloggning till ett befintligt ASP.NET Core-program.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Så här fungerar exemplet
>
> ![Diagram över interaktionen mellan webbläsaren, webbappen och Microsoft Identity Platform i exempel-appen.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Startklass

Mellanprogram *Microsoft. AspNetCore. Authentication* använder en `Startup` klass som körs när värd processen startar:

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

`AddAuthentication()`Metoden konfigurerar tjänsten för att lägga till cookie-baserad autentisering. Den här autentiseringen används i webb läsar scenarier och ställer in utmaningen till OpenID Connect.

Raden som innehåller `.AddMicrosoftIdentityWebApp` lägger till Microsoft Identity Platform-autentisering till ditt program. Programmet konfigureras sedan för att logga in användare baserat på följande information i avsnittet i `AzureAD` *appsettings.jsi* konfigurations filen:

| *appsettings.jspå* nyckel | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Program-ID för programmet som är registrerat i Azure Portal.                                                                                       |
| `Instance`             | STS-slutpunkt (Security Token Service) för användaren att autentisera. Det här värdet är vanligt vis `https://login.microsoftonline.com/` som anger det offentliga Azure-molnet. |
| `TenantId`             | Namnet på din klient organisation eller klient-ID (ett GUID) eller `common` för att logga in användare med arbets-eller skol konton eller personliga Microsoft-konton.                             |

`Configure()`Metoden innehåller två viktiga metoder `app.UseAuthentication()` `app.UseAuthorization()` som aktiverar de namngivna funktionerna. I `Configure()` -metoden måste du också registrera Microsoft Identity Web routes med minst ett anrop till `endpoints.MapControllerRoute()` eller ett anrop till `endpoints.MapControllers()` :

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

### <a name="attribute-for-protecting-a-controller-or-methods"></a>Attribut för att skydda en kontrollant eller metoder

Du kan skydda en kontrollant eller styrenhets metod med hjälp av- `[Authorize]` attributet. Det här attributet begränsar åtkomsten till kontrollanten eller metoderna genom att endast tillåta autentiserade användare. En autentiserings-utmaning kan sedan startas för att få åtkomst till kontrollanten om användaren inte är autentiserad.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

GitHub-lagrings platsen som innehåller den här ASP.NET Core själv studie kursen innehåller instruktioner och exempel på fler kod exempel som visar hur du:

- Lägg till autentisering i ett nytt ASP.NET Core-webbprogram.
- Anropa Microsoft Graph, andra Microsoft API: er eller dina egna webb-API: er.
- Lägg till auktorisering.
- Logga in användare i nationella moln eller med sociala identiteter.

> [!div class="nextstepaction"]
> [ASP.NET Core själv studie kurser om Web Apps på GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
