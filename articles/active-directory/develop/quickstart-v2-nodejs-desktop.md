---
title: 'Snabb start: anropa Microsoft Graph från en Node.js Desktop-app | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur ett Node.js Electron Skriv bords program kan logga in användare och få en åtkomsttoken att anropa ett API som skyddas av en Microsoft Identity Platform-slutpunkt
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: beef869b891fe6e3f0ea2f667763cb310008b2fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653277"
---
# <a name="quickstart-acquire-an-access-token-and-call-the-microsoft-graph-api-from-an-electron-desktop-app"></a>Snabb start: Hämta en åtkomsttoken och anropa Microsoft Graph-API: et från en Electron Desktop-app

I den här snabb starten laddar du ned och kör ett kod exempel som visar hur ett Electron Skriv bords program kan logga in användare och hämta åtkomsttoken för att anropa Microsoft Graph-API: et.

I den här snabb starten används [Microsoft Authentication Library för Node.js (MSAL Node)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) med ett [auktoriseringskod-flöde med PKCE](v2-oauth2-auth-code-flow.md).

## <a name="prerequisites"></a>Förutsättningar

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) eller en annan kod redigerare

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Registrera och ladda ned exempel programmet
>
> Kom igång genom att följa stegen nedan.
>
> #### <a name="step-1-register-the-application"></a>Steg 1: registrera programmet
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
> 1. Sök efter och välj **Azure Active Directory**.
> 1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
> 1. Ange ett **namn** för programmet, till exempel `msal-node-desktop` . Användare av appen kan se det här namnet och du kan ändra det senare.
> 1. Välj **Registrera** för att skapa programmet.
> 1. Under **Hantera** väljer du **autentisering**.
> 1. Välj **Lägg till en plattform**  >  **mobil-och skriv bords program**.
> 1. I avsnittet **omdirigerings-URI** anger du `msal://redirect` .
> 1. Välj **Konfigurera**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Steg 1: konfigurera programmet i Azure Portal
> För att kod exemplet för den här snabb starten ska fungera måste du lägga till en svars-URL som **msal://Redirect**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-windows-desktop/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-electron-sample-project"></a>Steg 2: Hämta Electron-exempelprojektet

> [!div renderon="docs"]
> [Ladda ned kod exemplet](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-electron-sample-project"></a>Steg 3: Konfigurera Electron-exempelprojektet
>
> 1. Extrahera zip-filen till en lokal mapp nära disk roten, till exempel *C:/Azure-samples*.
> 1. Redigera *. kuvert* och ersätt värdena för fälten `TENANT_ID` och `CLIENT_ID` med följande kodfragment:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here"
>    ```
>    Plats:
>    - `Enter_the_Application_Id_Here` – är **program-ID (klient)** för programmet som du har registrerat.
>    - `Enter_the_Tenant_Id_Here` – ersätt det här värdet med **klient-ID** eller **klientnamn** (t.ex. contoso.microsoft.com)
>
> > [!TIP]
> > För att hitta värdena för **program-ID (klient)**, **katalog-ID (klient)** och går du till appens **översiktssida** i Azure-portalen.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Steg 4: kör programmet

> [!div renderon="docs"]
> #### <a name="step-4-run-the-application"></a>Steg 4: kör programmet

Du måste installera beroendena för det här exemplet en gång:

```console
npm install
```

Kör sedan programmet via kommando tolken eller konsolen:

```console
npm start
```

Du bör se programmets användar gränssnitt med knappen **Logga in** .

## <a name="about-the-code"></a>Om koden

Nedan diskuteras några av de viktiga aspekterna av exempel programmet.

### <a name="msal-node"></a>MSAL-nod

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) är det bibliotek som används för att logga in användare och begära token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform. Mer information om hur du använder MSAL-noden med skrivbordsappar finns i [den här artikeln](scenario-desktop-overview.md).

Du kan installera MSAL-noden genom att köra följande NPM-kommando.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>MSAL-initiering

Du kan lägga till referensen för MSAL-noden genom att lägga till följande kod:

```javascript
const { PublicClientApplication } = require('@azure/msal-node');
```

Initiera sedan MSAL med hjälp av följande kod:

```javascript
const MSAL_CONFIG = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
    },
};

const pca = new PublicClientApplication(MSAL_CONFIG);
```

> | Plats: |Beskrivning |
> |---------|---------|
> | `clientId` | Är **Program-ID (klient)** för det program som registrerats på Azure-portalen. Du hittar det här värdet på appens **översiktssida** på Azure-portalen. |
> | `authority`    | STS-slutpunkten för autentisering av användaren. Vanligtvis `https://login.microsoftonline.com/{tenant}` för offentligt moln, där {klient} är namnet på klientorganisationen eller klient-ID:t.|

### <a name="requesting-tokens"></a>Begära token

I den första delen av auktoriseringskod-flödet med PKCE förbereder och skickar du en begäran om auktoriseringskod med lämpliga parametrar. Sedan lyssnar du på svars koden i den andra delen av flödet. När koden har hämtats kan du byta ut den för att hämta en token.

```javascript
// The redirect URI you setup during app registration with a custom file protocol "msal"
const redirectUri = "msal://redirect";

const cryptoProvider = new CryptoProvider();

const pkceCodes = {
    challengeMethod: "S256", // Use SHA256 Algorithm
    verifier: "", // Generate a code verifier for the Auth Code Request first
    challenge: "" // Generate a code challenge from the previously generated code verifier
};

/**
 * Starts an interactive token request
 * @param {object} authWindow: Electron window object
 * @param {object} tokenRequest: token request object with scopes
 */
async function getTokenInteractive(authWindow, tokenRequest) {

    /**
     * Proof Key for Code Exchange (PKCE) Setup
     *
     * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod
     * parameters in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
     * second leg (acquireTokenByCode() API).
     */

    const {verifier, challenge} = await cryptoProvider.generatePkceCodes();

    pkceCodes.verifier = verifier;
    pkceCodes.challenge = challenge;

    const authCodeUrlParams = {
        redirectUri: redirectUri
        scopes: tokenRequest.scopes,
        codeChallenge: pkceCodes.challenge, // PKCE Code Challenge
        codeChallengeMethod: pkceCodes.challengeMethod // PKCE Code Challenge Method
    };

    const authCodeUrl = await pca.getAuthCodeUrl(authCodeUrlParams);

    // register the custom file protocol in redirect URI
    protocol.registerFileProtocol(redirectUri.split(":")[0], (req, callback) => {
        const requestUrl = url.parse(req.url, true);
        callback(path.normalize(`${__dirname}/${requestUrl.path}`));
    });

    const authCode = await listenForAuthCode(authCodeUrl, authWindow); // see below

    const authResponse = await pca.acquireTokenByCode({
        redirectUri: redirectUri,
        scopes: tokenRequest.scopes,
        code: authCode,
        codeVerifier: pkceCodes.verifier // PKCE Code Verifier
    });

    return authResponse;
}

/**
 * Listens for auth code response from Azure AD
 * @param {string} navigateUrl: URL where auth code response is parsed
 * @param {object} authWindow: Electron window object
 */
async function listenForAuthCode(navigateUrl, authWindow) {

    authWindow.loadURL(navigateUrl);

    return new Promise((resolve, reject) => {
        authWindow.webContents.on('will-redirect', (event, responseUrl) => {
            try {
                const parsedUrl = new URL(responseUrl);
                const authCode = parsedUrl.searchParams.get('code');
                resolve(authCode);
            } catch (err) {
                reject(err);
            }
        });
    });
}
```

> |Plats:| Beskrivning |
> |---------|---------|
> | `authWindow` | Aktuellt Electron-fönster i processen. |
> | `tokenRequest` | Innehåller de omfattningar som begärs, till exempel `"User.Read"` för Microsoft Graph eller `"api://<Application ID>/access_as_user"` för anpassade webb-API: er. |

## <a name="next-steps"></a>Nästa steg

Läs mer om Electron Desktop app Development med MSAL-noden i självstudien:

> [!div class="nextstepaction"]
> [Självstudie: Logga in användare och anropa Microsoft Graph API i en Electron Desktop-app](tutorial-v2-nodejs-desktop.md)