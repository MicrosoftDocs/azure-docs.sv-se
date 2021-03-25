---
title: 'Snabb start: Logga in användare i Java Script-appar med en sida (SPA) med auth-kod | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur ett Java Script-program (Single-Page Application) kan logga in användare av personliga konton, arbets konton och skol konton med hjälp av flödet för auktoriseringskod.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 1e99e8ff25d895ba2248ddd1ba2520e9f14871a7
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022814"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow-with-pkce"></a>Snabb start: Logga in användare och hämta en åtkomsttoken i ett Java Script SPA med auth Code Flow med PKCE 

I den här snabb starten kan du ladda ned och köra ett kod exempel som visar hur ett Java Script-program (Single-Side Application) kan logga in användare och anropa Microsoft Graph med hjälp av auktoriseringskod med bevis nyckel för Code Exchange (PKCE). Kod exemplet visar hur du hämtar en åtkomsttoken för att anropa Microsoft Graph API eller något webb-API. 

Se [hur exemplet fungerar](#how-the-sample-works) för en illustration.

I den här snabb starten används MSAL.js v2 med Authorization Code Flow. En liknande snabb start som använder MSAL.js v1 med det implicita flödet finns i [snabb start: Logga in användare i Java Script-appar med en sida](./quickstart-v2-javascript.md).

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en Azure-prenumeration kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) eller en annan kod redigerare

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrera och ladda ned snabbstartsprogrammet
> Starta snabb starts programmet med något av följande alternativ.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1 (Express): registrera och konfigurera appen automatiskt och ladda ned kod exemplet
>
> 1. Gå till <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Appregistreringar</a>.
> 1. Ange ett namn för ditt program.
> 1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
> 1. Välj **Register** (Registrera).
> 1. Gå till snabb starts fönstret och följ instruktionerna för att ladda ned och konfigurera det nya programmet automatiskt.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2 (manuell): registrera och konfigurera ditt program och kod exempel manuellt
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
>
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
> 1. Sök efter och välj **Azure Active Directory**.
> 1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
> 1. Ange ett **namn** för ditt program. Användare av appen kan se det här namnet och du kan ändra det senare.
> 1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
> 1. Välj **Register** (Registrera). På sidan **Översikt över** appar noterar du **programmets (klient) ID-** värde för senare användning.
> 1. Under **Hantera** väljer du **autentisering**.
> 1. Under **plattforms konfiguration** väljer du **Lägg till en plattform**. I fönstret som öppnas väljer du **program med en enda sida**.
> 1. Ange **omdirigerings-URI** -värdet till `http://localhost:3000/` .
> 1. Välj **Konfigurera**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
> Om du vill att kod exemplet i den här snabb starten ska fungera lägger du till en **omdirigerings-URI** för `http://localhost:3000/` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-javascript/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-project"></a>Steg 2: Ladda ned projektet

> [!div renderon="docs"]
> Om du vill köra projektet med en webb server med hjälp av Node.js [laddar du ned de centrala projektfilerna](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Kör projektet med en webb server genom att använda Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Steg 3: Konfigurera din JavaScript-app
>
> I mappen *app* öppnar du *authConfig.js* -filen och uppdaterar `clientID` `authority` värdena,, och `redirectUri` i `msalConfig` objektet.
>
> ```javascript
> // Config object to be passed to Msal on creation
> const msalConfig = {
>   auth: {
>     clientId: "Enter_the_Application_Id_Here",
>     authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>     redirectUri: "Enter_the_Redirect_Uri_Here",
>   },
>   cache: {
>     cacheLocation: "sessionStorage", // This configures where your cache will be stored
>     storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>   }
> };
> ```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Ändra värdena i `msalConfig` avsnittet enligt beskrivningen här:
>
> - `Enter_the_Application_Id_Here` är **program-ID: t (Client)** för det program som du har registrerat.
>
>    Du hittar värdet för **program-ID: t** genom att gå till sidan med registrerings **översikten** för appen i Azure Portal.
> - `Enter_the_Cloud_Instance_Id_Here` är instansen av Azure-molnet. För huvud-eller globala Azure-molnet anger du `https://login.microsoftonline.com/` . För **nationella** moln (till exempel Kina), se [nationella moln](authentication-national-cloud.md).
> - `Enter_the_Tenant_info_here` är inställt på något av följande:
>   - Om ditt program har stöd *för konton i den här organisations katalogen* ersätter du värdet med **klient-ID** eller **klient namn**. Till exempel `contoso.microsoft.com`.
>
>    Om du vill hitta värdet för **katalog-ID: t** går du till sidan med registrerings **översikten** för appen i Azure Portal.
>   - Om ditt program har stöd *för konton i en organisations katalog* ersätter du värdet med `organizations` .
>   - Om ditt program har stöd *för konton i en organisations katalog och personliga Microsoft-konton* ersätter du värdet med `common` . **I den här snabb** starten använder du `common` .
>   - Om du bara vill begränsa stödet till *personliga Microsoft-konton* ersätter du värdet med `consumers` .
>
>    Du hittar värdet för **konto typer som stöds** genom att gå till sidan med registrerings **översikten** för appen i Azure Portal.
> - `Enter_the_Redirect_Uri_Here` är `http://localhost:3000/`.
>
> `authority`Värdet i *authConfig.js* bör likna följande om du använder det huvudsakliga Azure-molnet (Globalt):
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Steg 3: appen har kon figurer ATS och är redo att köras
> Vi har konfigurerat ditt projekt med värdena för appens egenskaper.

> [!div renderon="docs"]
>
> Sedan, fortfarande i samma mapp, redigerar du *graphConfig.js* -filen och uppdaterar `graphMeEndpoint` och- `graphMailEndpoint` värdena i `apiConfig` objektet.
>
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>
> [!div renderon="docs"]
>
> `Enter_the_Graph_Endpoint_Here` är slut punkten som API-anrop görs mot. För Main (global) Microsoft Graph API-tjänsten anger `https://graph.microsoft.com/` du (ta med avslutande snedstreck). Mer information om Microsoft Graph i nationella moln finns i [nationell moln distribution](/graph/deployments).
>
> `graphMeEndpoint`Värdena och `graphMailEndpoint` i *graphConfig.js* -filen bör likna följande om du använder den primära (globala) Microsoft Graph API-tjänsten:
>
> ```javascript
> graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
> graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
> ```
>
> #### <a name="step-4-run-the-project"></a>Steg 4: kör projektet

Kör projektet med en webb server genom att använda Node.js:

1. Starta-servern genom att köra följande kommandon inifrån projekt katalogen:
    ```console
    npm install
    npm start
    ```
1. Gå till `http://localhost:3000/`.

1. Välj **Logga** in för att starta inloggnings processen och anropa sedan Microsoft Graph-API: et.

    Första gången du loggar in uppmanas du att ange ditt medgivande så att programmet kan komma åt din profil och logga in dig. När du har loggat in visas din användar profil information på sidan.

## <a name="more-information"></a>Mer information

### <a name="how-the-sample-works"></a>Så här fungerar exemplet

![Diagram som visar auktoriserings kod flödet för ett program på en enda sida.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

MSAL.jss biblioteket loggar in användare och begär de token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform. Exemplets *index.html* -fil innehåller en referens till biblioteket:

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```

Om du har Node.js installerat kan du hämta den senaste versionen med hjälp av Node.js Package Manager (NPM):

```console
npm install @azure/msal-browser
```

## <a name="next-steps"></a>Nästa steg

En mer detaljerad steg-för-steg-guide om hur du skapar programmet som används i den här snabb starten finns i följande självstudie:

> [!div class="nextstepaction"]
> [Självstudie för att logga in och anropa MS Graph](./tutorial-v2-javascript-auth-code.md)
