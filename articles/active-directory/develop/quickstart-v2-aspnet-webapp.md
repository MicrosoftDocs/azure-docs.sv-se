---
title: 'Snabb start: lägga till inloggning med Microsoft i en ASP.NET-webbapp | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten lär du dig hur du implementerar Microsoft-inloggning på en ASP.NET-webbapp med OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, contperf-fy21q1
ms.openlocfilehash: 87948ed04f7b50820d94993d4c4fbcf2dfd94b31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578693"
---
# <a name="quickstart-add-microsoft-identity-platform-sign-in-to-an-aspnet-web-app"></a>Snabb start: lägga till Microsoft Identity Platform-inloggning till en ASP.NET-webbapp

I den här snabb starten hämtar och kör du ett kod exempel som visar hur en ASP.NET-webbapp kan logga in användare från valfri Azure Active Directory (Azure AD)-organisation. 

> [!div renderon="docs"]
> Följande diagram visar hur exempel appen fungerar:
>
> ![Diagram över interaktionen mellan webbläsaren, webbappen och Microsoft Identity Platform i exempel-appen.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Förutsättningar
>
> * Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
> * [.NET Framework 4.7.2 +](https://dotnet.microsoft.com/download/visual-studio-sdks)
>
> ## <a name="register-and-download-the-app"></a>Registrera och ladda ned appen
> Det finns två alternativ för att börja skapa ditt program: automatisk eller manuell konfiguration.
>
> ### <a name="automatic-configuration"></a>Automatisk konfiguration
> Följ dessa steg om du vill konfigurera din app automatiskt och sedan ladda ned kod exemplet:
>
> 1. Gå till <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs" target="_blank">Azure Portal sidan för registrering av appar</a>.
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och automatiskt konfigurera det nya programmet med ett klick.
>
> ### <a name="manual-configuration"></a>Manuell konfiguration
> Om du vill konfigurera ditt program och kod exempel manuellt använder du följande procedurer.
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
>
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: på den översta menyn för att välja den klient som du vill registrera programmet i.
> 1. Sök efter och välj **Azure Active Directory**.
> 1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
> 1. I **namn** anger du ett namn för ditt program. Ange till exempel **ASPNET-snabb start**. Användare av appen ser det här namnet och du kan ändra det senare.
> 1. Lägg till **https://localhost:44368/** i **omdirigerings-URI** och välj **Registrera**.
> 1. Under **Hantera** väljer du **autentisering**.
> 1. I avsnittet **implicit beviljande och hybrid flöden** väljer du **ID-token**.
> 1. Välj **Spara**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
> För att kod exemplet i den här snabb starten ska fungera anger du **https://localhost:44368/** för **omdirigerings-URI**.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Programmet har redan kon figurer ](media/quickstart-v2-aspnet-webapp/green-check.png) ATS med det här attributet.

#### <a name="step-2-download-the-project"></a>Steg 2: Ladda ned projektet

> [!div renderon="docs"]
> [Ladda ned Visual Studio 2019-lösningen](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Kör projektet med hjälp av Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Steg 3: appen har kon figurer ATS och är redo att köras
> Vi har konfigurerat ditt projekt med värdena för appens egenskaper.

> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Steg 3: köra Visual Studio-projektet

1. Extrahera. zip-filen till en lokal mapp som är nära rotmappen. Extrahera till exempel till *C:\Azure-samples*.
   
   Vi rekommenderar att du extraherar arkivet till en katalog nära roten på enheten för att undvika fel som orsakas av begränsningar i Sök vägs längd på Windows.
2. Öppna lösningen i Visual Studio (*AppModelv2-webapp-OpenIDConnect-dotNet. SLN*).
3. Beroende på versionen av Visual Studio kan du behöva Högerklicka på projektet **AppModelv2-webapp-OpenIDConnect-dotNet** och sedan välja **Återställ NuGet-paket**.
4. Öppna Package Manager-konsolen genom att välja **Visa**  >  **annan Windows**  >  **Package Manager-konsol**. Kör sedan `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`.

> [!div renderon="docs"]
> 5. Redigera *Web.config* och ersätt parametrarna `ClientId` , `Tenant` , och `redirectUri` med:
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    <add key="redirectUri" value="https://localhost:44368/" />
>    ```
>    I den koden:
>
>    - `Enter_the_Application_Id_here` är program-ID: t (klient) för den app-registrering som du skapade tidigare. Hitta program-ID: t (klient) på appens **översikts** sida i **Appregistreringar** i Azure Portal.
>    - `Enter_the_Tenant_Info_Here` är ett av följande alternativ:
>      - Om programmet endast stöder **min organisation** ersätter du det här värdet med katalogen (klient) ID eller klient namn (till exempel `contoso.onmicrosoft.com` ). Leta reda på klient-ID: t på appens **översikts** sida i **Appregistreringar** i Azure Portal.
>      - Om ditt program har stöd **för konton i en organisations katalog** ersätter du värdet med `organizations` .
>      - Om programmet har stöd för **alla Microsoft-konto användare ersätter du** värdet med `common` .
>    - `redirectUri` är **omdirigerings-URI: n** som du angav tidigare i **Appregistreringar** i Azure Portal.
>

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>Mer information

Det här avsnittet ger en översikt över den kod som krävs för att logga in användare. Den här översikten kan vara användbar för att förstå hur koden fungerar, vad de viktigaste argumenten är och hur du lägger till inloggning i ett befintligt ASP.NET-program.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Så här fungerar exemplet
>
> ![Diagram över interaktionen mellan webbläsaren, webbappen och Microsoft Identity Platform i exempel-appen.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>OWIN-mellanprogrammets NuGet-paket

Du kan ställa in pipeline för autentisering med cookie-baserad autentisering genom att använda OpenID Connect i ASP.NET med OWIN mellan paket. Du kan installera dessa paket genom att köra följande kommandon i Package Manager-konsolen i Visual Studio:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>OWIN start klass

OWIN mellanprogram använder en *Start klass* som körs när värd processen startar. I den här snabb starten finns filen *startup. cs* i rotmappen. Följande kod visar de parametrar som används i den här snabb starten:

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the client ID, authority, and redirect URI as obtained from Web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it's using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the code id_token, which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.CodeIdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organization, set ValidateIssuer to true and the 'tenant' setting in Web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use the ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to the OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Var  | Beskrivning |
> |---------|---------|
> | `ClientId`     | Program-ID: t från programmet som är registrerat i Azure Portal. |
> | `Authority`    | STS-slutpunkten (Security Token Service) för användaren som ska autentiseras. Det är vanligt vis `https://login.microsoftonline.com/{tenant}/v2.0` för det offentliga molnet. I den URL: en är *{Tenant}* namnet på din klient, ditt klient-ID eller `common` en referens till den gemensamma slut punkten. (Den vanliga slut punkten används för program med flera klienter.) |
> | `RedirectUri`  | URL: en där användare skickas efter autentisering mot Microsoft Identity Platform. |
> | `PostLogoutRedirectUri`     | URL: en där användare skickas efter utloggning. |
> | `Scope`     | Listan över omfattningar som begärs, avgränsade med blank steg. |
> | `ResponseType`     | Begäran om att svaret från autentiseringen innehåller en auktoriseringskod och en ID-token. |
> | `TokenValidationParameters`     | En lista över parametrar för tokenvalidering. I det här fallet `ValidateIssuer` anges till `false` för att indikera att den kan acceptera inloggningar från vilken typ av konto som helst, arbete eller skola. |
> | `Notifications`     | En lista över ombud som kan köras på `OpenIdConnect` meddelanden. |


> [!NOTE]
> Inställningen `ValidateIssuer = false` är en förenkling för den här snabb starten. I verkliga program validerar du utfärdaren. Se exemplen för att förstå hur du gör det.

### <a name="authentication-challenge"></a>Autentiserings-Challenge

Du kan tvinga en användare att logga in genom att begära en autentiseringsfråga i kontrollanten:

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> Att begära en autentisering med hjälp av den här metoden är valfritt. Du använder normalt det när du vill att en vy ska vara åtkomlig från både autentiserade och oautentiserade användare. Du kan också skydda kontrollanter med hjälp av metoden som beskrivs i nästa avsnitt.

### <a name="attribute-for-protecting-a-controller-or-a-controller-actions"></a>Attribut för att skydda en styrenhet eller styrenhets åtgärder

Du kan skydda en kontroll enhets-eller styrenhets åtgärd genom att använda- `[Authorize]` attributet. Det här attributet begränsar åtkomsten till kontrollanten eller åtgärderna genom att endast tillåta autentiserade användare åtkomst till åtgärder i kontrollanten. En verifierings utmaning görs sedan automatiskt när en oautentiserad användare försöker få åtkomst till en av de åtgärder eller kontroller som dekorerats av `[Authorize]` attributet.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

En fullständig steg-för-steg-guide om hur du skapar program och nya funktioner, inklusive en fullständig förklaring av den här snabb starten, finns i ASP.NET-självstudierna.

> [!div class="nextstepaction"]
> [Lägga till inloggning till en ASP.NET-webbapp](tutorial-v2-asp-webapp.md)
