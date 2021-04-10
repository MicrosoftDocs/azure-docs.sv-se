---
title: 'Snabb start: Logga in användare i Java Script-appar med en sida | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur en JavaScript-app kan anropa ett API som kräver åtkomsttoken som utfärdats av Microsoft Identity Platform.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 8e35342bd704f662d41f676f58e2cc14b54f29a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023392"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Snabb start: Logga in användare och hämta en åtkomsttoken i ett Java Script SPA

I den här snabb starten laddar du ned och kör ett kod exempel som visar hur ett Java Script-program (Single-Side Application) kan logga in användare och anropa Microsoft Graph. Kod exemplet visar också hur du får en åtkomsttoken för att anropa Microsoft Graph API eller något webb-API.

Se [hur exemplet fungerar](#how-the-sample-works) för en illustration.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (för att redigera projektfiler)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrera och ladda ned snabbstartsprogrammet
> Starta snabb starts programmet med något av följande alternativ.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1 (Express): registrera och konfigurera appen automatiskt och ladda ned kod exemplet
>
> 1. Gå till snabb starts upplevelsen för <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Appregistreringar</a> .
> 1. Ange ett namn för ditt program.
> 1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
> 1. Välj **Register** (Registrera).
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt.
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
> 1. Den här snabbstarten kräver att [flödet för implicit beviljande](v2-oauth2-implicit-grant-flow.md) aktiveras. Under **Hantera** väljer du **autentisering**.
> 1.   >  **Lägg till en plattform** under plattforms konfiguration. Välj **webb**.
> 1. Ange **omdirigerings-URI** -värdet till `http://localhost:3000/` . 
> 1. Välj **åtkomsttoken** och **ID-token** under **implicit beviljande och hybrid flöden**  .
> 1. Välj **Konfigurera**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
> För att kod exemplet i den här snabb starten ska fungera lägger du till en **omdirigerings-URI** för `http://localhost:3000/` och aktiverar **implicit beviljande**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-javascript/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-project"></a>Steg 2: Ladda ned projektet

> [!div renderon="docs"]
> Om du vill köra projektet med en webb server med hjälp av Node.js [laddar du ned de centrala projektfilerna](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Kör projektet med en webb server genom att använda Node.js

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Steg 3: Konfigurera din JavaScript-app
>
> I mappen *JavaScriptSPA* redigerar du *authConfig.js* och anger `clientID` `authority` `redirectUri` värdena och `msalConfig` .
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Plats:
> - `Enter_the_Application_Id_Here` är **program-ID: t (Client)** för det program som du har registrerat.
>
>    Du hittar värdet för **program-ID (klient)** genom att gå till appens **översikts** sida i Azure Portal.
> - `Enter_the_Cloud_Instance_Id_Here` är instansen av Azure-molnet. För det största eller globala Azure-molnet anger du bara `https://login.microsoftonline.com/` . För **nationella** moln (till exempel Kina), se [nationella moln](./authentication-national-cloud.md).
> - `Enter_the_Tenant_info_here` är inställt på något av följande alternativ:
>    - Om ditt program har stöd *för konton i den här organisations katalogen* ersätter du värdet med **klient-ID** eller **klient namn** (till exempel `contoso.microsoft.com` ).
>
>    Om du vill hitta värdet för **katalog-ID: t** går du till sidan med registrerings **översikten** för appen i Azure Portal.
>    - Om ditt program har stöd *för konton i en organisations katalog* ersätter du värdet med `organizations` .
>    - Om ditt program har stöd *för konton i en organisations katalog och personliga Microsoft-konton* ersätter du värdet med `common` . Om du bara vill begränsa stödet till *personliga Microsoft-konton* ersätter du värdet med `consumers` .
>
>    Du hittar värdet för **konto typer som stöds** genom att gå till sidan med registrerings **översikten** för appen i Azure Portal.
> - `Enter_the_Redirect_Uri_Here` är `http://localhost:3000/`.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Steg 3: appen har kon figurer ATS och är redo att köras
> Vi har konfigurerat ditt projekt med värdena för appens egenskaper.

> [!div renderon="docs"]
>
> Sedan, fortfarande i samma mapp, redigerar du *graphConfig.js* -filen för att ange `graphMeEndpoint` och `graphMeEndpoint` för `apiConfig` objektet.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
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
> Plats:
> - *\<Enter_the_Graph_Endpoint_Here>* är slut punkten som API-anrop görs mot. För huvud-eller global Microsoft Graph API-tjänsten anger du bara `https://graph.microsoft.com/` . Mer information finns i [nationell moln distribution](/graph/deployments)
>
> #### <a name="step-4-run-the-project"></a>Steg 4: kör projektet

Kör projektet med en webb server genom att använda [Node.js](https://nodejs.org/en/download/):

1. Starta-servern genom att köra följande kommando från projekt katalogen:
    ```cmd
    npm install
    npm start
    ```
1. Öppna en webbläsare och gå till `http://localhost:3000/` .

1. Välj **Logga** in för att starta inloggningen och anropa Microsoft Graph API.

När webbläsaren har läst in programmet väljer du **Logga** in. Första gången du loggar in uppmanas du att ange ditt medgivande för att ge programmet åtkomst till din profil och att logga in dig. När du har loggat in visas din användar profil information på sidan.

## <a name="more-information"></a>Mer information

### <a name="how-the-sample-works"></a>Så här fungerar exemplet

![Så här fungerar JavaScript-exempel-SPA: 1. SPA påbörjar inloggningen. 2. SPA hämtar en ID-token från Microsoft Identity Platform. 3. SPA-anropen hämtar token. 4. Microsoft Identity Platform returnerar en åtkomsttoken till SPA. 5. SPA gör-och HTTP GET-begäran med ACE-token till Microsoft Graph-API: et. 6. Graph API returnerar ett HTTP-svar till SPA.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

MSAL-biblioteket loggar in användare och begär de token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform. Snabb starten *index.html* -filen innehåller en referens till biblioteket:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```

Du kan ersätta den tidigare versionen med den senaste versionen under [MSAL.js-versioner](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Om du har Node.js installerat kan du hämta den senaste versionen via Node.js Package Manager (NPM):

```cmd
npm install msal
```

### <a name="msal-initialization"></a>MSAL-initiering

Snabb starts koden visar också hur du initierar MSAL-biblioteket:

```javascript
  // Config object to be passed to Msal on creation
  const msalConfig = {
    auth: {
      clientId: "75d84e7a-40bx-f0a2-91b9-0c82d4c556aa", // this is a fake id
      authority: "https://login.microsoftonline.com/common",
      redirectUri: "http://localhost:3000/",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

const myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

|Var  | Beskrivning |
|---------|---------|
|`clientId`     | Program-ID för programmet som är registrerat i Azure Portal.|
|`authority`    | Valfritt Auktoritets-URL: en som stöder konto typer, enligt beskrivningen ovan i avsnittet konfiguration. Standard auktoriteten är `https://login.microsoftonline.com/common` . |
|`redirectUri`     | Program registreringens konfigurerade svars-/redirectUri. I det här fallet `http://localhost:3000/` . |
|`cacheLocation`  | Valfritt Anger webb läsar lagring för auth-tillstånd. Standardvärdet är sessionStorage.   |
|`storeAuthStateInCookie`  | Valfritt Biblioteket som lagrar det tillstånd för autentiseringsbegäran som krävs för verifiering av autentiserings flöden i webbläsarens cookies. Den här cookien är inställd för IE-och Edge-webbläsare för att minimera vissa [kända problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

Mer information om tillgängliga konfigurerbara alternativ finns i [initiera klient program](msal-js-initializing-client-applications.md).

### <a name="sign-in-users"></a>Logga in användare

Följande kodavsnitt visar hur du loggar in användare:

```javascript
// Add scopes for the id token to be used at Microsoft identity platform endpoints.
const loginRequest = {
    scopes: ["openid", "profile", "User.Read"],
};

myMSALObj.loginPopup(loginRequest)
    .then((loginResponse) => {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

|Var  | Beskrivning |
|---------|---------|
| `scopes`   | Valfritt Innehåller omfattningar som begärs för användar medgivande vid inloggnings tillfället. Till exempel `[ "user.read" ]` för Microsoft Graph eller `[ "<Application ID URL>/scope" ]` för anpassade webb-API: er (det vill säga `api://<Application ID>/access_as_user` ). |

Du kanske vill använda `loginRedirect` metoden för att omdirigera den aktuella sidan till inloggnings sidan i stället för ett popup-fönster.

### <a name="request-tokens"></a>Begära token

MSAL använder tre metoder för att hämta tokens: `acquireTokenRedirect` , `acquireTokenPopup` , och `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

Metoden `acquireTokenSilent` hanterar hämtning och förnyelse av token utan någon användarinteraktion. När metoden `loginRedirect` eller `loginPopup` har körts för första gången är `acquireTokenSilent` den metod som vanligtvis används för att hämta token som används för att komma åt skyddade resurser för efterföljande anrop. Anrop för att begära eller förnya token görs tyst.

```javascript

const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenSilent(tokenRequest)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

|Var  | Beskrivning |
|---------|---------|
| `scopes`   | Innehåller omfång som begärs att returneras i åtkomsttoken för API. Till exempel `[ "mail.read" ]` för Microsoft Graph eller `[ "<Application ID URL>/scope" ]` för anpassade webb-API: er (det vill säga `api://<Application ID>/access_as_user` ).|

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Det finns situationer där du tvingar användare att interagera med Microsoft Identity Platform. Exempel:
* Användare kan behöva ange sina autentiseringsuppgifter på grund av att deras lösen ord har upphört att gälla.
* Ditt program begär åtkomst till ytterligare resurs omfattningar som användaren behöver godkänna.
* Tvåfaktorautentisering krävs.

Det vanligaste rekommenderade mönstret för de flesta program är att anropa `acquireTokenSilent` först, sedan fånga undantagen och sedan anropa `acquireTokenPopup` (eller `acquireTokenRedirect` ) för att starta en interaktiv begäran.

Anropar `acquireTokenPopup` resultaten i ett popup-fönster för att logga in. (Eller `acquireTokenRedirect` leder till omdirigering av användare till Microsoft Identity Platform). I det fönstret måste användarna interagera genom att bekräfta sina autentiseringsuppgifter, ge det tillstånd till den begärda resursen eller utföra tvåfaktorautentisering.

```javascript
// Add here scopes for access token to be used at MS Graph API endpoints.
const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenPopup(requestObj)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> [!NOTE]
> I den här snabb starten används `loginRedirect` `acquireTokenRedirect` metoderna och med Microsoft Internet Explorer, på grund av ett [känt problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) som rör hanteringen av popup-fönster i Internet Explorer.

## <a name="next-steps"></a>Nästa steg

En mer detaljerad steg-för-steg-guide om hur du skapar programmet för den här snabb starten finns i:

> [!div class="nextstepaction"]
> [Självstudie: Logga in användare och anropa Microsoft Graph API från ett Java Script (Single-Side Application)](tutorial-v2-javascript-spa.md)
