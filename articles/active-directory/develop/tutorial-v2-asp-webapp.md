---
title: 'Självstudie: skapa en ASP.NET-webbapp som använder Microsoft Identity Platform för autentisering | Azure'
titleSuffix: Microsoft identity platform
description: I den här självstudien skapar du ett ASP.NET-webbprogram som använder Microsoft Identity Platform och OWIN mellanprogram för att aktivera användar inloggning.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: 17cff66d3f8c816099bcc61322a1d331bfe58872
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226312"
---
# <a name="tutorial-add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Självstudie: lägga till inloggning till Microsoft i en ASP.NET-webbapp

I den här självstudien skapar du en ASP.NET MVC-webbapp som loggar in användare med hjälp av program varan Open Web interface for .NET (OWIN) och Microsoft Identity Platform.

När du har slutfört den här guiden kommer ditt program att kunna godkänna inloggningar av personliga konton från outlook.com och live.com. Dessutom kommer arbets-och skol konton från alla företag eller organisationer som är integrerade med Microsoft Identity Platform att kunna logga in i din app.

I de här självstudierna har du

> [!div class="checklist"]
> * Skapa ett *ASP.NET-webbprogram-* projekt i Visual Studio
> * Lägg till komponenter för OWIN-mellanprogram (Open Web Interface för .NET)
> * Lägg till kod som stöd för användar inloggning och utloggning
> * Registrera appen i Azure Portal
> * Testa appen

## <a name="prerequisites"></a>Förutsättningar

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) med arbets belastningen **ASP.net och webb utveckling** installerad

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Hur exempel appen som genereras av den här hand boken fungerar

![Visar hur exempel appen som genereras av den här själv studie kursen fungerar](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

Det exempel program som du skapar baseras på ett scenario där du kan använda webbläsaren för att få åtkomst till en ASP.NET-webbplats som gör det möjligt för användaren att autentisera via en inloggnings knapp. I det här scenariot sker merparten av renderingen av webbsidan på serversidan.

## <a name="libraries"></a>Bibliotek

I den här guiden används följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Mellanprogram som gör att ett program kan använda OpenIdConnect för autentisering|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Mellanprogram som gör det möjligt för ett program att underhålla en användarsession med hjälp av cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Mellanprogram som möjliggör OWIN-baserade program att köras på Internet Information Services (IIS) med hjälp av pipeline för ASP.NET-begäran|

## <a name="set-up-your-project"></a>Konfigurera projektet

I det här avsnittet beskrivs hur du installerar och konfigurerar en pipeline för autentisering genom OWIN mellan program i ett ASP.NET-projekt med hjälp av OpenID Connect.

> Vill du hämta det här exemplets Visual Studio-projekt i stället? [Ladda ned ett projekt](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) och hoppa till [registret registrera ditt program](#register-your-application) för att konfigurera kod exemplet innan du kör.

### <a name="create-your-aspnet-project"></a>Skapa ASP.NET-projektet

1. I Visual Studio: gå till **filen**  >  **nytt**  >  **projekt**.
2. Under **Visual C#\Web** väljer du **ASP.NET-webbprogram (.NET Framework)**.
3. Namnge ditt program och välj **OK**.
4. Välj **Tom** och markera sedan kryss rutan för att lägga till **MVC** -referenser.

## <a name="add-authentication-components"></a>Lägga till autentiseringskomponenter

1. I Visual Studio: gå till **verktyg**  >  **NuGet Package Manager**  >  **Package** Manager-konsolen.
2. Lägg till *NuGet-paket för OWIN-mellanprogram* genom att skriva följande i Package Manager-konsolfönstret:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

### <a name="about-these-libraries"></a>Om dessa bibliotek
Dessa bibliotek möjliggör enkel inloggning (SSO) med hjälp av OpenID Connect via cookie-baserad autentisering. När autentiseringen har slutförts och den token som representerar användaren skickas till ditt program, skapar OWIN-mellanprogrammet en sessions-cookie. Webbläsaren använder sedan denna cookie vid efterföljande begär Anden så att användaren inte behöver ange lösen ordet igen och ingen ytterligare verifiering krävs.

## <a name="configure-the-authentication-pipeline"></a>Konfigurera autentiseringspipelinen

Följande steg används för att skapa en OWIN-mellanprogram start klass för att konfigurera OpenID Connect-autentisering. Den här klassen körs automatiskt när IIS-processen startas.

> [!TIP]
> Om ditt projekt inte har en `Startup.cs`-fil i rotmappen:
> 1. Högerklicka på projektets rotmapp och välj sedan **Lägg till**  >  **nytt objekt**  >  **OWIN start klass**.<br/>
> 2. Ge den namnet **startup.cs**.
>
>> Kontrol lera att den valda klassen är en OWIN-startklass och inte en standard klass i C#. Bekräfta detta genom att kontrol lera att du ser [Assembly: OwinStartup (typeof ({NameSpace}. Start))] ovanför namn området.

1. Lägg till *OWIN* -och *Microsoft. IdentityModel* -referenser till startup.CS:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Ersätt startklassen med följande kod:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Microsoft identity platform.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed of the Microsoft identity platform and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false // This is a simplification
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

> [!NOTE]
> Inställningen `ValidateIssuer = false` är en förenkling för den här snabb starten. I verkliga program måste du verifiera utfärdaren.
> Se exemplen för att lära dig hur du gör det.

### <a name="more-information"></a>Mer information

De parametrar som du anger i *OpenIDConnectAuthenticationOptions* fungerar som koordinater för programmet att kommunicera med Microsoft Identity Platform. Eftersom OpenID Connect-programprogram använder cookies i bakgrunden, måste du också konfigurera cookie-autentisering som föregående kod visas. Värdet *ValidateIssuer* anger att OpenIdConnect inte begränsar åtkomsten till en speciell organisation.

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Lägga till en styrenhet för att hantera inloggnings- och utloggningsbegäranden

Följ dessa steg om du vill skapa en ny kontrollant för att exponera inloggnings-och utloggnings metoder:

1.  Högerklicka på mappen **kontrollanter** och välj **Lägg till**  >  **kontrollant**.
2.  Välj **MVC-kontrollant (.NET-version) – tom**.
3.  Välj **Lägg till**.
4.  Ge den namnet **HomeController** och välj sedan **Lägg till**.
5.  Lägg till OWIN-referenser till klassen:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Lägg till följande två metoder för att hantera inloggning och logga ut till din styrenhet genom att initiera en verifierings utmaning:

    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }

    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-for-user-sign-in"></a>Skapa appens start sida för användar inloggning

I Visual Studio skapar du en ny vy för att lägga till inloggnings knappen och Visa användar information efter autentisering:

1.  Högerklicka på mappen **Views\Home** och välj **Lägg till vy**.
2.  Namnge det nya vyns **index**.
3.  Lägg till följande HTML-kod, som innehåller inloggningsknappen, i filen:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

### <a name="more-information"></a>Mer information
 Den här sidan lägger till en inloggningsknapp i SVG-format med en svart bakgrund:<br/>![Logga in med Microsoft knapp](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> För fler inloggnings knappar går du till [rikt linjerna för anpassning](./howto-add-branding-in-azure-ad-apps.md "Riktlinjer för anpassning").

## <a name="add-a-controller-to-display-users-claims"></a>Lägg till en kontrollant för att Visa användarens anspråk
Den här kontrollanten demonstrerar hur `[Authorize]`-attributet kan skydda en kontrollant. Det här attributet begränsar åtkomsten till kontrollanten genom att endast tillåta autentiserade användare. Följande kod använder attributet för att Visa användar anspråk som hämtades som en del av inloggningen:

1.  Högerklicka på mappen **kontrollanter** och välj sedan **Lägg till**  >  **kontrollant**.
2.  Välj **MVC-kontrollant {version} – tom**.
3.  Välj **Lägg till**.
4.  Ge den namnet **ClaimsController**.
5.  Ersätt koden för din styrenhets klass med följande kod. Detta lägger till `[Authorize]` attributet i klassen:

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;

            //You get the user's first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;

            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;

            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;

            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;

            return View();
        }
    }
    ```

### <a name="more-information"></a>Mer information
På grund av användningen av `[Authorize]` -attributet kan alla metoder för den här styrenheten bara köras om användaren är autentiserad. Om användaren inte är autentiserad och försöker komma åt kontrollanten initierar OWIN en autentiserings-utmaning och tvingar användaren att autentisera sig. Föregående kod tittar på listan över anspråk för vissa användarattribut som ingår i användarens ID-token. Attributen omfattar användarens fullständiga namn och användar namn, samt ämnets globala användar identifierare. Den innehåller också *klient-ID*: t som representerar ID: t för användarens organisation.

## <a name="create-a-view-to-display-the-users-claims"></a>Skapa en vy för att visa användarens anspråk

Skapa en ny vy i Visual Studio för att visa användarens anspråk på en webbsida:

1.  Högerklicka på mappen **Views\Claims** och välj sedan **Lägg till vy**.
2.  Namnge det nya vyns **index**.
3.  Lägg till följande HTML-kod i filen:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```

## <a name="register-your-application"></a>Registrera ditt program

Om du vill registrera ditt program och lägga till program registrerings informationen i din lösning har du två alternativ:

### <a name="option-1-express-mode"></a>Alternativ 1: Express läge

Följ dessa steg för att snabbt registrera ditt program:

1. Gå till snabb starts upplevelsen för <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Appregistreringar <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .  
1. Ange ett namn för programmet och välj **Registrera**.
1. Följ anvisningarna för att ladda ned och automatiskt konfigurera det nya programmet i ett enda klick.

### <a name="option-2-advanced-mode"></a>Alternativ 2: Avancerat läge

Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:

1. Öppna Visual Studio och gör sedan följande:
   1. i Solution Explorer väljer du projektet och visar Fönstret Egenskaper (om du inte ser någon Fönstret Egenskaper trycker du på F4).
   1. Ändra SSL till `True` .
   1. Högerklicka på projektet i Visual Studio, Välj **Egenskaper** och välj sedan fliken **webb** . I avsnittet **servrar** ändrar du projekt- **URL** -inställningen till **SSL-URL: en**.
   1. Kopiera SSL-URL: en. Du kommer att lägga till denna URL i listan över omdirigerings-URI: er i registrerings portalens lista över omdirigerings-URI: er i nästa steg.<br/><br/>![Projektegenskaper](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
   
1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
1. Sök efter och välj **Azure Active Directory**.
1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
1. Ange ett **namn** för programmet, till exempel `ASPNET-Tutorial` . Användare av appen kan se det här namnet och du kan ändra det senare.
1. Lägg till SSL-URL: en som du kopierade från Visual Studio i steg 1 (till exempel `https://localhost:44368/` ) i **omdirigerings-URI**.
1. Välj **Register** (Registrera).
1. Under **Hantera** väljer du **autentisering**.
1. I avsnittet **implicit beviljande och hybrid flöden** väljer du **ID-token** och väljer sedan **Spara**.
1. Lägg till följande i web.config-filen, som finns i rotmappen i `configuration\appSettings` avsnittet:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Ersätt `ClientId` med det program-ID som du precis har registrerat.
1. Ersätt `redirectUri` med SSL-URL: en för projektet.

## <a name="test-your-code"></a>Testa koden

Om du vill testa programmet i Visual Studio trycker du på F5 för att köra projektet. Webbläsaren öppnas till <span></span> platsen http://localhost: {port} och du ser knappen **Logga in med Microsoft** . Klicka på knappen för att starta inloggnings processen.

När du är redo att köra testet använder du ett Azure AD-konto (arbets-eller skol konto) eller en personlig Microsoft-konto (<span>Live.</span> com eller <span>Outlook.</span> com) för att logga in.

![Logga in med Microsoft knapp som visas på inloggnings sidan för webbläsare i webbläsaren](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Logga in på din Microsoft-konto](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Behörigheter och medgivande i Microsoft Identity Platform
Program som integreras med Microsoft Identity Platform följer en auktoriserings modell som ger användare och administratörer kontroll över hur data kan nås. När en användare autentiseras med Microsoft Identity Platform för att komma åt det här programmet uppmanas de att godkänna de behörigheter som begärs av programmet ("Visa din grundläggande profil" och "upprätthålla åtkomst till data som du har gett dem åtkomst till"). När de här behörigheterna har accepterats fortsätter användaren med programmets resultat. Användaren får dock i stället uppmanas att ha en sida med **godkännande för administratörer** om något av följande inträffar:

- Programutvecklaren lägger till ytterligare behörigheter som kräver **Administratörs medgivande**.
- Eller klient organisationen är konfigurerad (i **företags program – > användar inställningar**) där användare inte kan godkänna appar som har åtkomst till företags data för deras räkning.

Mer information finns [i behörigheter och medgivande i Microsoft Identity Platform](./v2-permissions-and-consent.md).

### <a name="view-application-results"></a>Visa program resultat

När du har loggat in omdirigeras användaren till start sidan för din webbplats. Start sidan är HTTPS-URL: en som anges i din program registrerings information i registrerings portalen för Microsoft-program. Start sidan innehåller ett *"Hello \<user> "* -välkomst meddelande, en länk för att logga ut och en länk för att Visa användarens anspråk. Länken för användarens anspråk ansluts till den anspråks kontroll som du skapade tidigare.

### <a name="view-the-users-claims"></a>Visa användarens anspråk

Om du vill visa användarens anspråk väljer du länken för att bläddra till vyn kontrollant som bara är tillgänglig för autentiserade användare.

#### <a name="view-the-claims-results"></a>Visa anspråks resultatet

När du bläddrar till vyn kontrollant bör du se en tabell som innehåller grundläggande egenskaper för användaren:

|Egenskap |Värde |Beskrivning |
|---|---|---|
|**Namn** |Användarens fullständiga namn | Användarens för-och efter namn
|**Användarnamn** |användarvänlig<span>@domain.com</span> | Det användar namn som används för att identifiera användaren|
|**Ämne** |Ämne |En sträng som unikt identifierar användaren på webben|
|**Klientorganisations-ID** |GUID | Ett **GUID** som unikt representerar användarens Azure AD-organisation|

Dessutom bör du se en tabell över alla anspråk som finns i autentiseringsbegäran. Mer information finns i [listan över anspråk som finns i en ID-token](./id-tokens.md).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testa åtkomst till en metod som har ett auktoriserat attribut (valfritt)

Följ dessa steg om du vill testa åtkomst som en anonym användare till en kontrollant som skyddas av `Authorize` attributet:

1. Välj länken för att logga ut användaren och slutför utloggnings processen.
2. I webbläsaren skriver du http:// <span></span> localhost: {port}/anspråk för att komma åt din styrenhet som skyddas av `Authorize` attributet.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Förväntade resultat efter åtkomst till en skyddad styrenhet

Du uppmanas att autentisera för att använda vyn skyddad kontrollant.

## <a name="advanced-options"></a>Avancerade alternativ

### <a name="protect-your-entire-website"></a>Skydda hela webbplatsen

Om du vill skydda hela webbplatsen går du till filen **Global. asax** och lägger till `AuthorizeAttribute` attributet i `GlobalFilters` filtret i `Application_Start` metoden:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```

### <a name="restrict-who-can-sign-in-to-your-application"></a>Begränsa vem som kan logga in på ditt program

Som standard när du skapar programmet som skapats av den här guiden, accepterar ditt program inloggnings uppgifter för personliga konton (inklusive outlook.com, live.com och andra) samt arbets-och skol konton från alla företag eller organisationer som är integrerade med Microsoft Identity Platform. Detta är ett rekommenderat alternativ för SaaS-program.

För att begränsa användarens inloggnings åtkomst för ditt program är flera alternativ tillgängliga.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Alternativ 1: begränsa användare från endast en organisations Active Directory instans för att logga in i ditt program (en enda klient)

Det här alternativet används ofta för *LOB-program*: om du vill att programmet endast ska acceptera inloggningar från konton som tillhör en specifik Azure AD-instans (inklusive *gäst konton* för den instansen) följer du dessa steg:

1. I web.config-filen ändrar du värdet för `Tenant` parametern från `Common` till organisationens organisations namn, till exempel `contoso.onmicrosoft.com` .
2. I din [OWIN-startklass](#configure-the-authentication-pipeline)anger `ValidateIssuer` du argumentet till `true` .

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>Alternativ 2: begränsa åtkomsten till användare i en speciell lista över organisationer

Du kan begränsa inloggnings åtkomsten till enbart de användar konton som finns i en Azure AD-organisation som finns på listan över tillåtna organisationer:
1. I din [OWIN-startklass](#configure-the-authentication-pipeline)anger `ValidateIssuer` du argumentet till `true` .
2. Ange värdet för `ValidIssuers` parametern till listan över tillåtna organisationer.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Alternativ 3: Använd en anpassad metod för att validera utfärdare

Du kan implementera en anpassad metod för att verifiera utfärdare med hjälp av parametern **IssuerValidator** . Mer information om hur du använder den här parametern finns i [TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters) -klass.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Läs om hur du anropar skyddade webb-API: er från Web Apps med Microsoft Identity Platform:

> [!div class="nextstepaction"]
> [Webb program som anropar webb-API: er](scenario-web-app-sign-user-overview.md)
