---
title: 'Snabb start: skydda ett ASP.NET Core webb-API med Microsoft Identity Platform | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten hämtar och ändrar du ett kod exempel som visar hur du skyddar ett ASP.NET Core webb-API med hjälp av Microsoft Identity Platform för auktorisering.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 30593c51f17b99989409ddd22c9c1caa28468039
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720839"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-the-microsoft-identity-platform"></a>Snabb start: skydda ett ASP.NET Core webb-API med Microsoft Identity Platform

I den här snabb starten laddar du ned ett ASP.NET Core webb-API kod exempel och kontrollerar hur det begränsar resurs åtkomsten till endast behöriga konton. Exemplet stöder auktorisering av personliga Microsoft-konton och konton i valfri Azure Active Directory (Azure AD)-organisation.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Förutsättningar
>
> - Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> - [Azure Active Directory klient](quickstart-create-new-tenant.md)
> - [.NET Core SDK 3.1 +](https://dotnet.microsoft.com/)
> - [Visual studio 2019](https://visualstudio.microsoft.com/vs/) eller [Visual Studio Code](https://code.visualstudio.com/)
>
> ## <a name="step-1-register-the-application"></a>Steg 1: registrera programmet
>
> Registrera först webb-API: et i Azure AD-klienten och Lägg till ett omfång genom att följa dessa steg:
>
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: på den översta menyn för att välja den klient som du vill registrera ett program i.
> 1. Sök efter och välj **Azure Active Directory**.
> 1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
> 1. I **namn** anger du ett namn för ditt program. Ange till exempel **AspNetCoreWebApi-snabb start**. Användare av appen ser det här namnet och du kan ändra det senare.
> 1. Välj **Register** (Registrera).
> 1. Under **Hantera** väljer du **exponera ett API**  >  **Lägg till ett omfång**. För **program-ID-URI** godkänner du standardvärdet genom att välja **Spara och fortsätt** och anger följande information:
>    - **Omfattnings namn**: `access_as_user`
>    - **Vem kan godkänna?**: **Administratörer och användare**
>    - **Visnings namn för administratörs medgivande**: `Access AspNetCoreWebApi-Quickstart`
>    - **Beskrivning av administratörs medgivande**: `Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **Visnings namn för användarens medgivande**: `Access AspNetCoreWebApi-Quickstart`
>    - **Beskrivning av användar medgivande**: `Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **Tillstånd**: **aktiverat**
> 1. Välj **Lägg till omfattning** för att slutföra omfångs tillägget.

## <a name="step-2-download-the-aspnet-core-project"></a>Steg 2: Ladda ned ASP.NET Core-projektet

> [!div renderon="docs"]
> [Hämta ASP.net Core lösning](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip) från GitHub.

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div renderon="docs"]
> ## <a name="step-3-configure-the-aspnet-core-project"></a>Steg 3: konfigurera det ASP.NET Core projektet
>
> I det här steget konfigurerar du exempel koden så att den fungerar med den app-registrering som du skapade tidigare.
>
> 1. Extrahera zip-arkivet till en mapp nära roten på enheten. Extrahera till exempel till *C:\Azure-samples*.
>
>    Vi rekommenderar att du extraherar arkivet till en katalog nära roten på enheten för att undvika fel som orsakas av begränsningar i Sök vägs längd på Windows.
>
> 1. Öppna lösningen i *WebAPI* -mappen i kod redigeraren.
> 1. Öppna filen *appsettings.js* och ändra följande kod:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - Ersätt `Enter_the_Application_Id_here` med program-ID: t (klient) för det program som du registrerade i Azure Portal. Du hittar program-ID: t (Client) på appens **översikts** sida.
>    - Ersätt `Enter_the_Tenant_Info_Here` med något av följande:
>       - Om ditt program bara har stöd **för konton i den här organisations katalogen** ersätter du det här värdet med katalogen (klient) ID (ett GUID) eller klient namn (till exempel `contoso.onmicrosoft.com` ). Du hittar klient-ID: t för katalogen (klient) på appens **översikts** sida.
>       - Om ditt program har stöd **för konton i en organisations katalog** ersätter du värdet med `organizations` .
>       - Om programmet har stöd för **alla Microsoft-konto användare**, lämna det här värdet som `common` .
>
> I den här snabb starten ska du inte ändra andra värden i *appsettings.js* i filen.

## <a name="how-the-sample-works"></a>Så här fungerar exemplet

Webb-API: et tar emot en token från ett klient program och koden i webb-API: et verifierar token. Det här scenariot förklaras i detalj i [scenariot: skyddat webb-API](scenario-protected-web-api-overview.md).

### <a name="startup-class"></a>Startklass

Mellanprogram *Microsoft. AspNetCore. Authentication* använder en `Startup` klass som körs när värd processen startar. I sin `ConfigureServices` metod kallas den `AddMicrosoftIdentityWebApi` tilläggs metod som tillhandahålls av *Microsoft. identitet. Web* .

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

`AddAuthentication()`Metoden konfigurerar tjänsten för att lägga till JwtBearer-baserad autentisering.

Den rad som innehåller `.AddMicrosoftIdentityWebApi` lägger till Microsoft Identity Platform-auktorisering i ditt webb-API. Den konfigureras sedan för att verifiera åtkomsttoken som utfärdats av Microsoft Identity Platform baserat på informationen i `AzureAD` avsnittet i *appsettings.jsi* konfigurations filen:

| *appsettings.jspå* nyckel | Beskrivning                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Program-ID för programmet som är registrerat i Azure Portal.                                                                                       |
| `Instance`             | STS-slutpunkt (Security Token Service) för användaren att autentisera. Det här värdet är vanligt vis `https://login.microsoftonline.com/` som anger det offentliga Azure-molnet. |
| `TenantId`             | Namnet på din klient organisation eller dess klient-ID (ett GUID) eller `common` för att logga in användare med arbets-eller skol konton eller personliga Microsoft-konton.                             |

`Configure()`Metoden innehåller två viktiga metoder `app.UseAuthentication()` och `app.UseAuthorization()` som aktiverar de namngivna funktionerna:

```csharp
// The runtime calls this method. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protecting-a-controller-a-controllers-method-or-a-razor-page"></a>Skydda en kontrollant, en styrenhets metod eller en kniv sida

Du kan skydda en kontrollant eller styrenhets metod med hjälp av- `[Authorize]` attributet. Det här attributet begränsar åtkomsten till kontrollanten eller metoderna genom att endast tillåta autentiserade användare. En autentiserings-utmaning kan startas för att få åtkomst till kontrollanten om användaren inte är autentiserad.

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validation-of-scope-in-the-controller"></a>Validering av omfattning i styrenheten

Koden i API: t verifierar att de nödvändiga omfattningarna finns i token genom att använda `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);` :

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

GitHub-lagringsplatsen som innehåller detta ASP.NET Core webb-API-kod exempel innehåller instruktioner och exempel på fler kod exempel som visar hur du:

- Lägg till autentisering till ett nytt ASP.NET Core webb-API.
- Anropa webb-API: et från ett Skriv bords program.
- Anropa underordnade API: er som Microsoft Graph och andra Microsoft API: er.

> [!div class="nextstepaction"]
> [ASP.NET Core Web API-självstudier på GitHub](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
