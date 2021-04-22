---
title: 'Snabbstart: Hämta token & anropa Microsoft Graph i en konsolapp | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabbstarten lär du dig hur en .NET Core-exempelapp kan använda flödet för klientautentiseringsuppgifter för att hämta en token och anropa Microsoft Graph.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: a31bf345f523eea940be5d56495890e8ab5c6dbd
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861651"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>Snabbstart: Hämta en token och anropa Microsoft Graph-API:et med hjälp av en konsolapps identitet

I den här snabbstarten hämtar och kör du ett kodexempel som visar hur ett .NET Core-konsolprogram kan hämta en åtkomsttoken för att anropa Microsoft Graph-API:et och visa en lista över användare i katalogen. [](/graph/api/user-list) Kodexe exemplet visar också hur ett jobb eller en Windows-tjänst kan köras med en programidentitet i stället för en användares identitet. Exempelkonsolprogrammet i den här snabbstarten är också ett daemonprogram, så det är ett konfidentiellt klientprogram.

> [!div renderon="docs"]
> Följande diagram visar hur exempelappen fungerar:
>
> ![Diagram som visar hur exempelappen som genereras av den här snabbstarten fungerar.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)
>

## <a name="prerequisites"></a>Förutsättningar

Den här snabbstarten [kräver .NET Core 3.1 SDK](https://dotnet.microsoft.com/download) men fungerar även med .NET 5.0 SDK.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-app"></a>Registrera och ladda ned appen

> [!div renderon="docs" class="sxs-lookup"]
>
> Du har två alternativ för att börja skapa ditt program: automatisk eller manuell konfiguration.
>
> ### <a name="automatic-configuration"></a>Automatisk konfiguration
>
> Om du vill registrera och konfigurera din app automatiskt och sedan ladda ned kodexe exemplet följer du dessa steg:
>
> 1. Gå till <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">Azure Portal för appregistrering.</a>
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ instruktionerna för att ladda ned och konfigurera det nya programmet automatiskt med ett klick.
>
> ### <a name="manual-configuration"></a>Manuell konfiguration
>
> Om du vill konfigurera ditt program och kodexempel manuellt använder du följande procedurer.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal </span> </a>.
> 1. Om du har åtkomst till flera klienter använder du filtret **Katalog +** prenumeration på den översta menyn för att välja den klientorganisation där du vill :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: registrera programmet.
> 1. Sök efter och välj **Azure Active Directory**.
> 1. Under **Hantera** väljer du **Appregistreringar**  >  **Ny registrering.**
> 1. I **Namn** anger du ett namn för ditt program. Ange till exempel **Daemon-console**. Användare av din app ser det här namnet och du kan ändra det senare.
> 1. Välj **Registrera** för att skapa programmet.
> 1. Välj **Certifikat och hemligheter** under **Hantera**.
> 1. Under **Klienthemligheter** väljer **du Ny klienthemlighet,** anger ett namn och väljer sedan Lägg **till**. Registrera det hemliga värdet på en säker plats för användning i ett senare steg.
> 1. Under **Hantera väljer** du **API-behörigheter Lägg** till en  >  **behörighet**. Välj **Microsoft Graph**.
> 1. Välj **Programbehörigheter**.
> 1. Under **noden** Användare väljer du **User.Read.All** och sedan Lägg **till behörigheter.**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Hämta och konfigurera din app för Snabbstart
>
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
> För att kodexe exemplet i den här snabbstarten ska fungera skapar du en klienthemlighet och lägger till Graph API **behörigheten User.Read.All.**
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-netcore-daemon/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-your-visual-studio-project"></a>Steg 2: Ladda ned ditt Visual Studio-projekt

> [!div renderon="docs"]
> [Ladda ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> Du kan köra det angivna projektet i antingen Visual Studio eller Visual Studio för Mac.


> [!div class="sxs-lookup" renderon="portal"]
> Kör projektet med hjälp av Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Ladda ned kodexe exemplet](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Steg 3: Konfigurera ditt Visual Studio-projekt
>
> 1. Extrahera ZIP-filen till en lokal mapp som är nära diskens rot. Extrahera till exempel till *C:\Azure-Samples*.
>
>    Vi rekommenderar att du extraherar arkivet till en katalog nära roten på enheten för att undvika fel som orsakas av begränsningar för sökvägslängd i Windows.
>
> 1. Öppna lösningen i Visual Studio: *1-Call-MSGraph\daemon-console.sln* (valfritt).
> 1. I *appsettings.jspå* ersätter du värdena för , och `Tenant` `ClientId` `ClientSecret` :
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>    I den koden:
>    - `Enter_the_Application_Id_Here` är program-ID:t (klienten) för det program som du registrerade.
        Om du vill hitta värdena för programmets (klientens) ID och katalog-ID:t (klient) går du till appens översiktssida i Azure Portal. 
>    - Ersätt `Enter_the_Tenant_Id_Here` med klient-ID eller klientnamn (till exempel `contoso.microsoft.com` ).
>    - Ersätt `Enter_the_Client_Secret_Here` med klienthemligheten som du skapade i steg 1.
    Om du vill generera en ny nyckel går du till **sidan Certifikat & hemligheter.**

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Steg 3: Administratörsmedgivande

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Steg 4: Administratörsmedgivande

Om du försöker köra programmet nu får du felet *HTTP 403 – Förbjuden:* "Otillräcklig behörighet för att slutföra åtgärden". Det här felet beror på att alla appbehörigheter kräver att en global administratör i din katalog ger sitt medgivande till ditt program. Välj något av följande alternativ, beroende på din roll.

##### <a name="global-tenant-administrator"></a>Global innehavaradministratör

> [!div renderon="docs"]
> Om du är global innehavaradministratör går du till **Företagsprogram** i Azure Portal. Välj din appregistrering och välj **Behörigheter** i **avsnittet** Säkerhet i det vänstra fönstret. Välj sedan den stora knappen Bevilja **administratörsmedgivande för {Klientnamn}** **(där {Klientnamn}** är namnet på din katalog).

> [!div renderon="portal" class="sxs-lookup"]
> Om du är global administratör går du till sidan **API-behörigheter** och väljer **Bevilja administratörsmedgivande för Enter_the_Tenant_Name_Here**.
> > [!div id="apipermissionspage"]
> > [Gå till sidan API-behörigheter]()

##### <a name="standard-user"></a>Standardanvändare

Om du är standardanvändare för din klientorganisation ber du en global administratör att bevilja administratörsmedgivande för ditt program. Gör detta genom att ge följande URL till administratören:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
> I den URL:en:
> * Ersätt `Enter_the_Tenant_Id_Here` med klientorganisations-ID:t eller klientorganisationens namn (till exempel `contoso.microsoft.com` ).
> * `Enter_the_Application_Id_Here` är program-ID (klient) för det program som du har registrerat.

Du kan se felet "AADSTS50011: Ingen svarsadress har registrerats för programmet" när du har gett medgivande till appen med hjälp av föregående URL. Det här felet beror på att programmet och URL:en inte har någon omdirigerings-URI. Du kan ignorera det.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Steg 4: Kör programmet

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Steg 5: Köra appen

Om du använder en Visual Studio eller Visual Studio för Mac trycker du på **F5** för att köra programmet. Annars kör du programmet via kommandotolken, konsolen eller terminalen:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
I den koden:
* `{ProjectFolder}` är den mapp där du extraherade ZIP-filen. Ett exempel är `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2`.

Du bör se en lista över användare i Azure Active Directory som resultat.

Det här snabbstartsprogrammet använder en klienthemlighet för att identifiera sig som en konfidentiell klient. Klienthemligheten läggs till som en oformaterad textfil i projektfilerna. Av säkerhetsskäl rekommenderar vi att du använder ett certifikat i stället för en klienthemlighet innan du överväger programmet som ett produktionsprogram. Mer information om hur du använder ett certifikat finns i [dessa instruktioner](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) på GitHub-lagringsplatsen för det här exemplet.

## <a name="more-information"></a>Mer information
Det här avsnittet ger en översikt över den kod som krävs för att logga in användare. Den här översikten kan vara användbar för att förstå hur koden fungerar, vad huvudargumenten är och hur du lägger till inloggning i ett befintligt .NET Core-konsolprogram.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Så här fungerar exemplet
>
> ![Diagram som visar hur exempelappen som genereras av den här snabbstarten fungerar.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

Microsoft Authentication Library (MSAL, i paketet [Microsoft.Identity.Client)](https://www.nuget.org/packages/Microsoft.Identity.Client) är det bibliotek som används för att logga in användare och begära token för åtkomst till ett API som skyddas av Microsofts identitetsplattform. Den här snabbstarten begär token med hjälp av programmets egen identitet i stället för delegerade behörigheter. Autentiseringsflödet i det här fallet kallas för ett [OAuth-flöde för klientautentiseringsuppgifter.](v2-oauth2-client-creds-grant-flow.md) Mer information om hur du använder MSAL.NET med ett flöde för klientautentiseringsuppgifter finns i [den här artikeln.](https://aka.ms/msal-net-client-credentials)

 Du kan installera MSAL.NET genom att köra följande kommando i Visual Studio Package Manager-konsolen:

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL-initiering

Du kan lägga till referensen för MSAL genom att lägga till följande kod:

```csharp
using Microsoft.Identity.Client;
```

Initiera sedan MSAL med hjälp av följande kod:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

 | Element | Beskrivning |
 |---------|---------|
 | `config.ClientSecret` | Klienthemligheten som skapats för programmet i Azure Portal. |
 | `config.ClientId` | Program-ID:t (klienten) för programmet som registrerats i Azure Portal. Du hittar det här värdet på appens **översiktssida** i Azure Portal. |
 | `config.Authority`    | (Valfritt) Sts-slutpunkten (Security Token Service) som användaren kan autentisera. Det är vanligtvis `https://login.microsoftonline.com/{tenant}` för det offentliga molnet, där är namnet `{tenant}` på din klientorganisation eller ditt klientorganisations-ID.|

Mer information finns i [referensdokumentationen för `ConfidentialClientApplication` ](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication).

### <a name="requesting-tokens"></a>Begära token

Om du vill begära en token med hjälp av appens identitet använder du `AcquireTokenForClient` metoden :

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|Element| Beskrivning |
|---------|---------|
| `scopes` | Innehåller de begärda omfången. För konfidentiella klienter bör det här värdet använda ett format som liknar `{Application ID URI}/.default` . Det här formatet anger att de begärda omfången är de som statiskt definieras i appobjektet som angetts i Azure Portal. För Microsoft Graph pekar `{Application ID URI}` på `https://graph.microsoft.com` . För anpassade webb-API:er `{Application ID URI}` definieras i Azure Portal under Application Registration (Preview) Expose an API **(Application Registration (Förhandsversion)**  >  **Expose an API**. |

Mer information finns i [referensdokumentationen för `AcquireTokenForClient` ](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om daemonprogram finns i scenarioöversikten:

> [!div class="nextstepaction"]
> [Daemon-program som anropar webb-API:er](scenario-daemon-overview.md)
