---
title: 'Snabb start: Logga in användare och anropa Microsoft Graph i en Universell Windows-plattform app | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur ett Universell Windows-plattform-program (UWP) kan hämta en åtkomsttoken och anropa ett API som skyddas av Microsoft Identity Platform.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/07/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: 8a016aa6690243ef2b933a10bba44af7cfe81a5b
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583119"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Snabbstart: Anropa Microsoft Graph API från en UWP-app (Universell Windows-plattform)

I den här snabb starten laddar du ned och kör ett kod exempel som visar hur ett Universell Windows-plattform-program (UWP) kan logga in användare och få en åtkomsttoken för att anropa Microsoft Graph API. 

Se [hur exemplet fungerar](#how-the-sample-works) för en illustration.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Förutsättningar
>
> * Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> [!div renderon="docs" class="sxs-lookup"]
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till snabb starts upplevelsen för <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Appregistreringar <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen med hjälp av följande steg:
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
> 1. Sök efter och välj **Azure Active Directory**.
> 1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
> 1. Ange ett **namn** för programmet, till exempel `UWP-App-calling-MsGraph` . Användare av appen kan se det här namnet och du kan ändra det senare.
> 1. I avsnittet **Kontotyper som stöds** väljer du **Konton alla organisationskataloger och personliga Microsoft-konton (till exempel Skype, Xbox och Outlook.com)**.
> 1. Välj **Registrera** för att skapa programmet och registrera sedan **program-ID: t (klient)** som ska användas i ett senare steg.
> 1. Under **Hantera** väljer du **autentisering**.
> 1. Välj **Lägg till en plattform**  >  **mobil-och skriv bords program**.
> 1. Under **omdirigerings-URI: er** väljer du `https://login.microsoftonline.com/common/oauth2/nativeclient` .
> 1. Välj **Konfigurera**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-the-application"></a>Steg 1: konfigurera programmet
> Om du vill att kod exemplet i den här snabb starten ska fungera lägger du till en **omdirigerings-URI** för `https://login.microsoftonline.com/common/oauth2/nativeclient` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-uwp/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-visual-studio-project"></a>Steg 2: Ladda ned Visual Studio-projektet

> [!div renderon="docs"]
> [Ladda ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Kör projektet med Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Steg 3: appen har kon figurer ATS och är redo att köras
> Vi har konfigurerat ditt projekt med värdena för appens egenskaper och är redo att köras.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-visual-studio-project"></a>Steg 3: Konfigurera Visual Studio-projektet
>
> 1. Extrahera. zip-arkivet till en lokal mapp nära enhetens rot. Till exempel i **C:\Azure-samples**.
> 1. Öppna projektet i Visual Studio. Installera arbets belastningen **universell Windows-plattform utveckling** och eventuella enskilda SDK-komponenter om du uppmanas att göra det.
> 1. I *mainpage.XAML.cs* ändrar du värdet för `ClientId` variabeln till **program-ID: t** för programmet som du registrerade tidigare.
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
>
>    Du hittar **program-ID: t (Client)** i appens **översikts** fönster i Azure Portal (**Azure Active Directory**  >  **Appregistreringar**  >  *{Your app Registration}*).
> 1. Skapa och välj sedan ett nytt självsignerat test certifikat för paketet:
>     1. I **Solution Explorer** dubbelklickar du på filen *Package. appxmanifest* .
>     1. Välj **Paketera**  >  **Välj certifikat...**  >  **Skapa...**.
>     1. Ange ett lösen ord och välj sedan **OK**.
>     1. Välj **Välj från fil...** och välj sedan den *Native_UWP_V2_TemporaryKey. pfx* -fil som du nyss skapade och välj **OK**.
>     1. Stäng filen *Package. appxmanifest* (Välj **OK** om du uppmanas att spara filen).
>     1. I **Solution Explorer** högerklickar du på **Native_UWP_V2** projektet och väljer **Egenskaper**.
>     1. Välj **signering** och välj sedan den. pfx som du skapade i list rutan **Välj en nyckel fil med starkt krypterat namn** .

#### <a name="step-4-run-the-application"></a>Steg 4: kör programmet

Så här kör du exempel programmet på den lokala datorn:

1. I verktygsfältet Visual Studio väljer du rätt plattform (antagligen **x64** eller **x86**, inte arm). Mål enheten bör ändras från *enheten* till den *lokala datorn*.
1. Välj **Felsökning** > **Starta utan felsökning**.
    
    Om du uppmanas att göra det kanske du först måste aktivera **utvecklarläge** och sedan **starta utan att felsöka** igen för att starta appen.

När appens fönster visas kan du välja API-knappen **anrops Microsoft Graph** , ange dina autentiseringsuppgifter och godkänna de behörigheter som har begärts av programmet. Om det lyckas visar programmet lite tokentyp och data som hämtats från anropet till Microsoft Graph-API: et.

## <a name="how-the-sample-works"></a>Så här fungerar exemplet

![Visar hur exempel appen som genereras av den här snabb starten fungerar](media/quickstart-v2-uwp/uwp-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client)) är det bibliotek som används för att logga in användare och begära säkerhetstoken. Säkerhetstoken används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform. Du kan installera MSAL genom att köra följande kommando i *Package Manager-konsolen* i Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL-initiering

Du kan lägga till referensen för MSAL genom att lägga till följande kod:

```csharp
using Microsoft.Identity.Client;
```

Sedan initieras MSAL med hjälp av följande kod:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

Värdet för `ClientId` är **program-ID: t** för den app som du registrerade i Azure Portal. Du hittar det här värdet på appens **översiktssida** på Azure-portalen.

### <a name="requesting-tokens"></a>Begära token

MSAL har två metoder för att förvärva tokens i en UWP-app: `AcquireTokenInteractive` och `AcquireTokenSilent` .

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Vissa situationer kräver tvingande av användare att interagera med Microsoft Identity Platform via ett popup-fönster för att antingen verifiera sina autentiseringsuppgifter eller ge samtycke. Några exempel är:

- Användare som loggar in på programmet första gången
- När användarna kan behöva ange sina autentiseringsuppgifter igen eftersom lösenordet har upphört att gälla
- När ditt program begär åtkomst till en resurs, så att användaren måste samtycka till
- När tvåfaktorsautentisering krävs

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

`scopes`Parametern innehåller de omfattningar som begärs, till exempel `{ "user.read" }` för Microsoft Graph eller `{ "api://<Application ID>/access_as_user" }` för anpassade webb-API: er.

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

Använd `AcquireTokenSilent` metoden för att hämta token för att få åtkomst till skyddade resurser efter den första `AcquireTokenInteractive` metoden. Du vill inte kräva att användaren validerar sina autentiseringsuppgifter varje gång de behöver åtkomst till en resurs. Det mesta av den tid som du vill ha token-förvärv och förnyelse utan användar interaktion

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

* `scopes` innehåller de omfattningar som begärs, till exempel `{ "user.read" }` för Microsoft Graph eller `{ "api://<Application ID>/access_as_user" }` för anpassade webb-API: er.
* `firstAccount` anger det första användar kontot i cachen (MSAL stöder flera användare i en enda app).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Prova att använda självstudien om Windows-skrivbordet för en komplett stegvis guide till att skapa appar och nya funktioner, däribland en fullständig förklaring av den här snabbstarten.

> [!div class="nextstepaction"]
> [UWP – Självstudie om att anropa Graph API](tutorial-v2-windows-uwp.md)
