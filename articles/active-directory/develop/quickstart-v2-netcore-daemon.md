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
ms.openlocfilehash: 7e236a6f10394d2b9c8889383b6ef3813969832d
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178372"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-using-console-apps-identity"></a>Snabb start: Hämta en token och anropa Microsoft Graph-API med hjälp av appens identitet

I den här snabb starten laddar du ned och kör ett kod exempel som visar hur ett .NET Core-konsolprogram kan hämta en åtkomsttoken för att anropa Microsoft Graph API och visa en [lista över användare](/graph/api/user-list) i katalogen. Kod exemplet visar också hur ett jobb eller en Windows-tjänst kan köras med en program identitet, i stället för en användares identitet. 

Se [hur exemplet fungerar](#how-the-sample-works) för en illustration.

## <a name="prerequisites"></a>Krav

Den här snabb starten kräver [.net Core 3,1](https://www.microsoft.com/net/download/dotnet-core).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen

> [!div renderon="docs" class="sxs-lookup"]
>
> Det finns två alternativ för att starta ditt snabb starts program: Express (alternativ 1 nedan) och manuell (alternativ 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till snabb starts upplevelsen för <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Appregistreringar <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
> 1. Sök efter och välj **Azure Active Directory**.
> 1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
> 1. Ange ett **namn** för programmet, till exempel `Daemon-console` . Användare av appen kan se det här namnet och du kan ändra det senare.
> 1. Välj **Registrera** för att skapa programmet.
> 1. Välj **Certifikat och hemligheter** under **Hantera**.
> 1. Under **klient hemligheter** väljer du **ny klient hemlighet**, anger ett namn och väljer sedan **Lägg till**. Registrera det hemliga värdet på en säker plats för användning i ett senare steg.
> 1. Under **Hantera** väljer du **API-behörigheter**  >  **Lägg till en behörighet**. Välj **Microsoft Graph**.
> 1. Välj **Programbehörigheter**.
> 1. Under noden **användare** väljer du **User. Read. all** och väljer sedan **Lägg till behörigheter**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Hämta och konfigurera din app för Snabbstart
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
> För att kodexemplet för den här snabbstarten ska fungera måste du skapa en klienthemlighet och lägga till Graph-API:ts programbehörighet **User.Read.All**.
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
> Kör projektet med Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Steg 3: Konfigurera ditt Visual Studio-projekt
>
> 1. Extrahera zip-filen i en lokal mapp nära diskens rot, till exempel **C:\Azure-Samples**.
> 1. Öppna lösningen i Visual Studio- **1-Call-MSGraph\daemon-Console.SLN** (valfritt).
> 1. Redigera **appsettings.jspå** och ersätt värdena för fälten `ClientId` `Tenant` och `ClientSecret` med följande:
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>   Där:
>   - `Enter_the_Application_Id_Here` – är **program-ID (klient)** för programmet som du har registrerat.
>   - `Enter_the_Tenant_Id_Here` – ersätt det här värdet med **klient-ID** eller **klientnamn** (t.ex. contoso.microsoft.com)
>   - `Enter_the_Client_Secret_Here` – ersätt det här värdet med klienthemligheten som skapades i steg 1.

> [!div renderon="docs"]
> > [!TIP]
> > För att hitta värdena för **program-ID (klient)**, **katalog-ID (klient)** och går du till appens **översiktssida** i Azure-portalen. Generera en ny nyckel genom att gå till sidan **Certifikat och hemligheter**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Steg 3: administratörs medgivande

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Steg 4: Administratörsmedgivande

Om du försöker köra programmet nu får du ett *HTTP 403-otillåtet* fel: `Insufficient privileges to complete the operation` . Detta beror på att en *app-only-behörighet* kräver administratörs medgivande, vilket innebär att en global administratör för din katalog måste ge ditt program medgivande. Välj ett av alternativen nedan beroende på din roll:

##### <a name="global-tenant-administrator"></a>Global innehavaradministratör

> [!div renderon="docs"]
> Om du är global innehavaradministratör navigerar du till **företags program** i Azure Portal > väljer din app registration > Välj **"behörigheter"** i avsnittet säkerhet i det vänstra navigerings fönstret. Välj den stora knappen med namnet **bevilja administratörs medgivande för {klient organisationens namn}** (där {klient organisationens namn} är namnet på din katalog).

> [!div renderon="portal" class="sxs-lookup"]
> Om du är global administratör går du till sidan **API-behörigheter** och väljer **Bevilja administratörsmedgivande för Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Gå till sidan API-behörigheter]()

##### <a name="standard-user"></a>Standardanvändare

Om du är en standard användare av din klient organisation måste du be en global administratör att bevilja administrativt medgivande för ditt program. Gör detta genom att ge följande URL till administratören:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Där:
>> * `Enter_the_Tenant_Id_Here` – ersätt det här värdet med **klient-ID** eller **klientnamn** (t.ex. contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` – är **program-ID (klient)** för programmet som du har registrerat.

> [!NOTE]
> Du kan se felmeddelandet *”AADSTS50011: Ingen svarsadress registrerad för programmet”* när du har beviljat åtkomst till appen med föregående URL. Det här händer eftersom den här appen och URL:en inte har en omdirigerings-URI – ignorera felet.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Steg 4: kör programmet

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Steg 5: Köra appen

Om du använder Visual Studio eller Visual Studio för Mac, trycker du på **F5** för att köra programmet, annars kör du programmet via kommando tolken, konsolen eller terminalen:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```

> Där:
> * *{ProjectFolder}* är den mapp där du har extraherat zip-filen. Exempel **C:\Azure-Samples\active-directory-dotnetcore-daemon-v2**

Du bör se en lista över användare i Azure AD-katalogen som resultat.

> [!IMPORTANT]
> Det här snabbstartsprogrammet använder en klienthemlighet för att identifiera sig som en konfidentiell klient. Eftersom klienthemligheten läggs till som oformaterad text till dina projektfiler rekommenderar vi att du av säkerhetsskäl använder ett certifikat i stället för en klienthemlighet innan programmet används som produktionsprogram. Mer information om hur du använder ett certifikat finns i [dessa instruktioner](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) på GitHub-lagringsplatsen för det här exemplet.

## <a name="more-information"></a>Mer information

### <a name="how-the-sample-works"></a>Så här fungerar exemplet
![Visar hur exempel appen som genereras av den här snabb starten fungerar](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client)) är det bibliotek som används för att logga in användare och begära token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform. Som beskrivs i den här snabb starten begär denna token genom att använda programmets egna identitet i stället för delegerade behörigheter. Autentiseringsflödet som används i det här fallet kallas för *[oauth-flöde för klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md)*. Mer information om hur du använder MSAL.NET med flöde för klientautentiseringsuppgifter finns i [den här artikeln](https://aka.ms/msal-net-client-credentials).

 Du kan installera MSAL.NET genom att köra följande kommando i **Package Manager-konsolen** i Visual Studio:

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

> | Där: | Description |
> |---------|---------|
> | `config.ClientSecret` | Är klienthemligheten som skapats för appen i Azure-portalen. |
> | `config.ClientId` | Är **Program-ID (klient)** för det program som registrerats på Azure-portalen. Du hittar det här värdet på appens **översiktssida** på Azure-portalen. |
> | `config.Authority`    | (Valfritt) STS-slutpunkten för autentisering av användaren. Vanligtvis `https://login.microsoftonline.com/{tenant}` för offentligt moln, där {klient} är namnet på klientorganisationen eller klient-ID:t.|

Mer information finns i [referensdokumentationen för `ConfidentialClientApplication`](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication)

### <a name="requesting-tokens"></a>Begära token

Om du vill begära en token med appens identitet använder du metoden `AcquireTokenForClient`:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

> |Där:| Description |
> |---------|---------|
> | `scopes` | Innehåller omfattningarna som begärdes. För konfidentiella klienter bör ett format som liknar `{Application ID URI}/.default` användas för att ange att omfattningarna som begärs är dem som statiskt definieras i appobjektet som anges i Azure-portalen (för Microsoft Graph, `{Application ID URI}` pekar på `https://graph.microsoft.com`). För anpassade webb-API: er `{Application ID URI}` definieras under **exponera ett API** -avsnitt i Azure-portalens program registrering (för hands version). |

Mer information finns i [referensdokumentationen för `AcquireTokenForClient`](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om daemon-program finns i Scenario översikt:

> [!div class="nextstepaction"]
> [Daemon-program som anropar webb-API: er](scenario-daemon-overview.md)
