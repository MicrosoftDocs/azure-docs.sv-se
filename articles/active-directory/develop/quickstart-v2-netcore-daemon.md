---
title: 'Snabb start: Hämta token & anropa Microsoft Graph i en konsol app | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur ett .NET Core-exempelprogram kan använda flödet för klientautentiseringsuppgifter för att hämta en token och anropa Microsoft Graph.
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
ms.openlocfilehash: 1b539c168deab7c1893f071a2453be28310fc132
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022933"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>Snabb start: Hämta en token och anropa Microsoft Graph-API med hjälp av en konsols Apps identitet

I den här snabb starten laddar du ned och kör ett kod exempel som visar hur ett .NET Core-konsolprogram kan hämta en åtkomsttoken för att anropa Microsoft Graph API och visa en [lista över användare](/graph/api/user-list) i katalogen. Kod exemplet visar också hur ett jobb eller en Windows-tjänst kan köras med en program identitet, i stället för en användares identitet. Exempel konsol programmet i den här snabb starten är också ett daemon-program, så det är ett konfidentiellt klient program.

> [!div renderon="docs"]
> Följande diagram visar hur exempel appen fungerar:
>
> ![Diagram som visar hur exempel appen som genereras av den här snabb starten fungerar.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)
>

## <a name="prerequisites"></a>Förutsättningar

Den här snabb starten kräver [.net core 3,1](https://www.microsoft.com/net/download/dotnet-core) men kommer även att fungera med .net Core 5,0.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-app"></a>Registrera och ladda ned appen

> [!div renderon="docs" class="sxs-lookup"]
>
> Det finns två alternativ för att börja skapa ditt program: automatisk eller manuell konfiguration.
>
> ### <a name="automatic-configuration"></a>Automatisk konfiguration
>
> Följ dessa steg om du vill registrera och konfigurera appen automatiskt och sedan ladda ned kod exemplet:
>
> 1. Gå till <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">Azure Portal sidan för registrering av appar</a>.
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och automatiskt konfigurera det nya programmet med ett klick.
>
> ### <a name="manual-configuration"></a>Manuell konfiguration
>
> Om du vill konfigurera ditt program och kod exempel manuellt använder du följande procedurer.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal </span> </a>.
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: på den översta menyn för att välja den klient som du vill registrera programmet i.
> 1. Sök efter och välj **Azure Active Directory**.
> 1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
> 1. I **namn** anger du ett namn för ditt program. Ange till exempel **daemon-Console**. Användare av appen ser det här namnet och du kan ändra det senare.
> 1. Välj **Registrera** för att skapa programmet.
> 1. Välj **Certifikat och hemligheter** under **Hantera**.
> 1. Under **klient hemligheter** väljer du **ny klient hemlighet**, anger ett namn och väljer sedan **Lägg till**. Registrera det hemliga värdet på en säker plats för användning i ett senare steg.
> 1. Under **Hantera** väljer du **API-behörigheter**  >  **Lägg till en behörighet**. Välj **Microsoft Graph**.
> 1. Välj **Programbehörigheter**.
> 1. Välj **User. Read. all** under noden **användare** och välj sedan **Lägg till behörigheter**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Hämta och konfigurera din app för Snabbstart
>
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
> Om du vill att kod exemplet i den här snabb starten ska fungera skapar du en klient hemlighet och lägger till Graph API **användare. Läs. alla** program behörigheter.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-netcore-daemon/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-your-visual-studio-project"></a>Steg 2: Ladda ned ditt Visual Studio-projekt

> [!div renderon="docs"]
> [Ladda ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> Du kan köra det tillhandahållna projektet i antingen Visual Studio eller Visual Studio för Mac.


> [!div class="sxs-lookup" renderon="portal"]
> Kör projektet med hjälp av Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Steg 3: Konfigurera ditt Visual Studio-projekt
>
> 1. Extrahera. zip-filen till en lokal mapp som är nära roten på disken. Extrahera till exempel till *C:\Azure-samples*.
>
>    Vi rekommenderar att du extraherar arkivet till en katalog nära roten på enheten för att undvika fel som orsakas av begränsningar i Sök vägs längd på Windows.
>
> 1. Öppna lösningen i Visual Studio: *1-Call-MSGraph\daemon-Console.SLN* (valfritt).
> 1. I *appsettings.jspå*, ersätter du värdena för `Tenant` , `ClientId` , och `ClientSecret` :
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>    I den koden:
>    - `Enter_the_Application_Id_Here` är program-ID: t (Client) för det program som du har registrerat.
        Om du vill hitta värdena för program-ID: t och katalog-ID: t går du till appens **översikts** sida i Azure Portal.
>    - Ersätt `Enter_the_Tenant_Id_Here` med klient-ID eller klient namn (till exempel `contoso.microsoft.com` ).
>    - Ersätt `Enter_the_Client_Secret_Here` med den klient hemlighet som du skapade i steg 1.
    Om du vill generera en ny nyckel går du till sidan **certifikat & hemligheter** .

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Steg 3: administratörs medgivande

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Steg 4: Administratörsmedgivande

Om du försöker köra programmet nu får du ett *HTTP 403-förbjudet* fel: "otillräcklig behörighet för att slutföra åtgärden". Det här felet beror på att en app-only-behörighet kräver en global administratör för din katalog för att ge ditt program tillstånd. Välj något av följande alternativ, beroende på din roll.

##### <a name="global-tenant-administrator"></a>Global innehavaradministratör

> [!div renderon="docs"]
> Om du är global klient administratör går du till **företags program** i Azure Portal. Välj din app-registrering och välj **behörigheter** i avsnittet **säkerhet** i det vänstra fönstret. Välj sedan den stora knappen med namnet **bevilja administratörs medgivande för {klient organisationens namn}** (där **{klient organisationens namn}** är namnet på din katalog).

> [!div renderon="portal" class="sxs-lookup"]
> Om du är global administratör går du till sidan **API-behörigheter** och väljer **bevilja administrativt medgivande för Enter_the_Tenant_Name_Here**.
> > [!div id="apipermissionspage"]
> > [Gå till sidan API-behörigheter]()

##### <a name="standard-user"></a>Standardanvändare

Om du är en standard användare av klienten kan du be en global administratör att bevilja ditt program administratörs medgivande. Gör detta genom att ge följande URL till administratören:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
> I URL: en:
> * Ersätt `Enter_the_Tenant_Id_Here` med klient-ID eller klient namn (till exempel `contoso.microsoft.com` ).
> * `Enter_the_Application_Id_Here` är program-ID: t (Client) för det program som du har registrerat.

Du kan se felet "AADSTS50011: ingen svars adress har registrerats för programmet" när du har beviljat medgivande till appen med hjälp av föregående URL. Det här felet beror på att det här programmet och URL: en inte har någon omdirigerings-URI. Du kan ignorera det.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Steg 4: kör programmet

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Steg 5: Köra appen

Om du använder Visual Studio eller Visual Studio för Mac, trycker du på **F5** för att köra programmet. Annars kör du programmet via kommando tolken, konsolen eller terminalen:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
I den koden:
* `{ProjectFolder}` är mappen där du extraherade. zip-filen. Ett exempel är `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2`.

Du bör se en lista över användare i Azure Active Directory som resultat.

I det här snabb starts programmet används en klient hemlighet för att identifiera sig själv som en konfidentiell klient. Klient hemligheten läggs till som en textfil med oformaterad text till dina projektfiler. Av säkerhets skäl rekommenderar vi att du använder ett certifikat i stället för en klient hemlighet innan du överväger programmet som ett produktions program. Mer information om hur du använder ett certifikat finns i [dessa instruktioner](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) på GitHub-lagringsplatsen för det här exemplet.

## <a name="more-information"></a>Mer information
Det här avsnittet ger en översikt över den kod som krävs för att logga in användare. Den här översikten kan vara användbar för att förstå hur koden fungerar, vad de viktigaste argumenten är och hur du lägger till inloggning i ett befintligt .NET Core-konsolprogram.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Så här fungerar exemplet
>
> ![Diagram som visar hur exempel appen som genereras av den här snabb starten fungerar.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

Microsoft Authentication Library (MSAL, i [Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client) -paketet) är det bibliotek som används för att logga in användare och begära token för åtkomst till ett API som skyddas av Microsoft Identity Platform. Den här snabb starten begär token med hjälp av programmets egna identitet i stället för delegerade behörigheter. Autentiserings flödet i det här fallet kallas OAuth- [flöde](v2-oauth2-client-creds-grant-flow.md)för klientautentiseringsuppgifter. Mer information om hur du använder MSAL.NET med ett flöde för klientautentiseringsuppgifter finns i [den här artikeln](https://aka.ms/msal-net-client-credentials).

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
 | `config.ClientSecret` | Klient hemligheten som skapades för programmet i Azure Portal. |
 | `config.ClientId` | Program-ID: t (Client) för programmet som är registrerat i Azure Portal. Du hittar det här värdet på appens **översikts** sida i Azure Portal. |
 | `config.Authority`    | Valfritt STS-slutpunkten (Security Token Service) för användaren som ska autentiseras. Det är vanligt vis `https://login.microsoftonline.com/{tenant}` för det offentliga molnet, där `{tenant}` är namnet på din klient organisation eller klient-ID.|

Mer information finns i [referens dokumentationen för `ConfidentialClientApplication` ](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication).

### <a name="requesting-tokens"></a>Begära token

Om du vill begära en token genom att använda appens identitet använder du `AcquireTokenForClient` metoden:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|Element| Beskrivning |
|---------|---------|
| `scopes` | Innehåller de begärda omfattningarna. För konfidentiella klienter bör det här värdet använda ett format som liknar `{Application ID URI}/.default` . Det här formatet indikerar att de begärda omfattningarna är de som definieras statiskt i app-objektet som anges i Azure Portal. För Microsoft Graph, `{Application ID URI}` pekar på `https://graph.microsoft.com` . För anpassade webb-API: er `{Application ID URI}` definieras i Azure Portal under **program registrering (för hands version)**  >  **exponeras ett API**. |

Mer information finns i [referens dokumentationen för `AcquireTokenForClient` ](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om daemon-program finns i Scenario översikt:

> [!div class="nextstepaction"]
> [Daemon-program som anropar webb-API: er](scenario-daemon-overview.md)
