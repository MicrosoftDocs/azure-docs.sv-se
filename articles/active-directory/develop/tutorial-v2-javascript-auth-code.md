---
title: 'Självstudie: skapa en Java Script-app med en sida som använder auth Code Flow | Azure'
titleSuffix: Microsoft identity platform
description: 'I den här självstudien skapar du en JavaScript-SPA som kan logga in användare och använda auth Code Flow för att hämta en åtkomsttoken från Microsoft Identity Platform och anropa Microsoft Graph-API: et.'
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: b7d14ee321a1160420d106151276ae6aef513c5b
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064410"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-app-spa-using-auth-code-flow"></a>Självstudie: Logga in användare och anropa Microsoft Graph-API: et från en JavaScript-app med en enda sida (SPA) med auth Code Flow

I den här självstudien skapar du ett Java Script-program (Single-Side Application) som loggar in användare och anropar Microsoft Graph med hjälp av kod flödet för auktorisering med PKCE. Det SPA som du skapar använder Microsoft Authentication Library (MSAL) för Java Script v 2.0.

I de här självstudierna har du
> [!div class="checklist"]
> * Utför OAuth 2,0-auktoriseringskod med PKCE
> * Logga in personliga Microsoft-konton samt arbets-och skol konton
> * Hämta en åtkomsttoken
> * Anropa Microsoft Graph eller ditt eget API som kräver åtkomsttoken som hämtats från Microsoft Identity Platform-slutpunkten

MSAL.js 2,0 förbättrar MSAL.js 1,0 genom att stödja auktoriserings kod flödet i webbläsaren i stället för det implicita tilldelnings flödet. MSAL.js 2,0 har **inte** stöd för det implicita flödet.

## <a name="prerequisites"></a>Förutsättningar

* [Node.js](https://nodejs.org/en/download/) för att köra en lokal webbserver
* [Visual Studio Code](https://code.visualstudio.com/download) eller en annan kod redigerare

## <a name="how-the-tutorial-app-works"></a>Hur kursen fungerar

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="Diagram som visar auktoriserings kod flödet i ett program med en sida":::

Det program som du skapar i den här självstudien gör det möjligt för ett Java Script SPA att fråga Microsoft Graph-API genom att förvärva säkerhetstoken från slut punkten för Microsoft Identity Platform. I det här scenariot begärs en åtkomsttoken efter att användaren loggar in och läggs till i HTTP-begäranden i Authorization-huvudet. Hämtning av token och förnyelse hanteras av Microsoft Authentication Library för Java Script (MSAL.js).

I den här självstudien används följande bibliotek:

[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) Microsoft Authentication Library för Java Script v 2.0 Browser-paket

## <a name="get-the-completed-code-sample"></a>Hämta det ifyllda kod exemplet

Vill du ladda ned det här självstudiet exempel projektet i stället? För att köra projektet med hjälp av en lokal webb server, till exempel Node.js, klona [MS-Identity-JavaScript-v2-](https://github.com/Azure-Samples/ms-identity-javascript-v2) databasen:

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Om du vill konfigurera kod exemplet innan du kör det går du vidare till [konfigurations steget](#register-your-application).

Om du vill fortsätta med självstudien och bygga programmet själv kan du gå vidare till nästa avsnitt, [krav](#prerequisites).

## <a name="create-your-project"></a>Skapa projektet

När du har [Node.js](https://nodejs.org/en/download/) installerat skapar du en mapp som är värd för ditt program, till exempel *msal – Spa – självstudier*.

Implementera sedan en liten [Express](https://expressjs.com/) -webbserver för att hantera din *index.html* -fil.

1. Ändra först till projekt katalogen i terminalen och kör sedan följande `npm` kommandon:
    ```console
    npm init -y
    npm install @azure/msal-browser
    npm install express
    npm install morgan
    npm install yargs
    ```
2. Skapa sedan en fil med namnet *server.js* och Lägg till följande kod:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

Nu har du en liten server för att hantera din SPA. När du har slutfört resten av självstudien bör filen och mappstrukturen i projektet se ut ungefär så här:

```
msal-spa-tutorial/
├── app
│   ├── authConfig.js
│   ├── authPopup.js
│   ├── authRedirect.js
│   ├── graphConfig.js
│   ├── graph.js
│   ├── index.html
│   └── ui.js
└── server.js
```

## <a name="create-the-spa-ui"></a>Skapa SPA-ANVÄNDARGRÄNSSNITTET

1. Skapa en *app* -mapp i din projekt katalog och skapa en *index.html* -fil för Java Script Spa. Den här filen implementerar ett användar gränssnitt som skapats med **bootstrap 4 Framework** och importerar skriptfiler för konfigurations-, autentiserings-och API-anrop.

    I *index.html* -filen lägger du till följande kod:

    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <title>Tutorial | MSAL.js JavaScript SPA</title>

        <!-- IE support: add promises polyfill before msal.js  -->
        <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
        <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.4/js/msal-browser.js" integrity="sha384-7sxY2tN3GMVE5jXH2RL9AdbO6s46vUh9lUid4yNCHJMUzDoj+0N4ve6rLOmR88yN" crossorigin="anonymous"></script>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
          <a class="navbar-brand" href="/">Microsoft identity platform</a>
          <div class="btn-group ml-auto dropleft">
              <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                Sign In
              </button>
          </div>
        </nav>
        <br>
        <h5 class="card-header text-center">JavaScript SPA calling Microsoft Graph API with MSAL.js</h5>
        <br>
        <div class="row" style="margin:auto" >
        <div id="card-div" class="col-md-3" style="display:none">
        <div class="card text-center">
          <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mail</button>
          </div>
        </div>
        </div>
        <br>
        <br>
          <div class="col-md-4">
            <div class="list-group" id="list-tab" role="tablist">
            </div>
          </div>
          <div class="col-md-5">
            <div class="tab-content" id="nav-tabContent">
            </div>
          </div>
        </div>
        <br>
        <br>

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
        <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

        <!-- importing app scripts (load order is important) -->
        <script type="text/javascript" src="./authConfig.js"></script>
        <script type="text/javascript" src="./graphConfig.js"></script>
        <script type="text/javascript" src="./ui.js"></script>

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authPopup.js"></script>
        <script type="text/javascript" src="./graph.js"></script>
      </body>
    </html>
    ```

2. Därefter skapar du en fil med namnet *ui.js* i mappen *app* och lägger till följande kod. Den här filen kommer att få åtkomst till och uppdatera DOM-element.

    ```JavaScript
    // Select DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("SignIn");
    const cardDiv = document.getElementById("card-div");
    const mailButton = document.getElementById("readMail");
    const profileButton = document.getElementById("seeProfile");
    const profileDiv = document.getElementById("profile-div");

    function showWelcomeMessage(account) {
        // Reconfiguring DOM elements
        cardDiv.style.display = 'initial';
        welcomeDiv.innerHTML = `Welcome ${account.username}`;
        signInButton.setAttribute("onclick", "signOut();");
        signInButton.setAttribute('class', "btn btn-success")
        signInButton.innerHTML = "Sign Out";
    }

    function updateUI(data, endpoint) {
        console.log('Graph API responded at: ' + new Date().toString());

        if (endpoint === graphConfig.graphMeEndpoint) {
            const title = document.createElement('p');
            title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
            const email = document.createElement('p');
            email.innerHTML = "<strong>Mail: </strong>" + data.mail;
            const phone = document.createElement('p');
            phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
            const address = document.createElement('p');
            address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
            profileDiv.appendChild(title);
            profileDiv.appendChild(email);
            profileDiv.appendChild(phone);
            profileDiv.appendChild(address);

        } else if (endpoint === graphConfig.graphMailEndpoint) {
            if (data.value.length < 1) {
                alert("Your mailbox is empty!")
            } else {
                const tabList = document.getElementById("list-tab");
                tabList.innerHTML = ''; // clear tabList at each readMail call
                const tabContent = document.getElementById("nav-tabContent");

                data.value.map((d, i) => {
                    // Keeping it simple
                    if (i < 10) {
                        const listItem = document.createElement("a");
                        listItem.setAttribute("class", "list-group-item list-group-item-action")
                        listItem.setAttribute("id", "list" + i + "list")
                        listItem.setAttribute("data-toggle", "list")
                        listItem.setAttribute("href", "#list" + i)
                        listItem.setAttribute("role", "tab")
                        listItem.setAttribute("aria-controls", i)
                        listItem.innerHTML = d.subject;
                        tabList.appendChild(listItem)

                        const contentItem = document.createElement("div");
                        contentItem.setAttribute("class", "tab-pane fade")
                        contentItem.setAttribute("id", "list" + i)
                        contentItem.setAttribute("role", "tabpanel")
                        contentItem.setAttribute("aria-labelledby", "list" + i + "list")
                        contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
                        tabContent.appendChild(contentItem);
                    }
                });
            }
        }
    }
    ```

## <a name="register-your-application"></a>Registrera ditt program

Följ stegen i [ett program med en sida: app-registrering](scenario-spa-app-registration.md) för att skapa en app-registrering för din Spa.

I [omdirigerings-URI: MSAL.js 2,0 med steget auth Code Flow](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) anger `http://localhost:3000` du, standard platsen där den här självstudien körs.

Om du vill använda en annan port anger du `http://localhost:<port>` , där `<port>` är det önskade TCP-portnumret. Om du anger ett annat port nummer än `3000` kan du också uppdatera *server.js* med det port nummer som du föredrar.

### <a name="configure-your-javascript-spa"></a>Konfigurera ditt Java Script SPA

Skapa en fil med namnet *authConfig.js* i mappen *app* som innehåller dina konfigurations parametrar för autentisering och Lägg sedan till följande kod:

```javascript
const msalConfig = {
  auth: {
    clientId: "Enter_the_Application_Id_Here",
    authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
    redirectUri: "Enter_the_Redirect_Uri_Here",
  },
  cache: {
    cacheLocation: "sessionStorage", // This configures where your cache will be stored
    storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
  }
};

// Add scopes here for ID token to be used at Microsoft identity platform endpoints.
const loginRequest = {
 scopes: ["openid", "profile", "User.Read"]
};

// Add scopes here for access token to be used at Microsoft Graph API endpoints.
const tokenRequest = {
 scopes: ["User.Read", "Mail.Read"]
};
```

Ändra värdena i `msalConfig` avsnittet enligt beskrivningen här:

- `Enter_the_Application_Id_Here`: **Program-ID: t (klient)** för det program som du har registrerat.
- `Enter_the_Cloud_Instance_Id_Here`: Azure Cloud-instansen där ditt program är registrerat.
  - För det huvudsakliga (eller *globala*) Azure-molnet anger du `https://login.microsoftonline.com` .
  - För **nationella** moln (till exempel Kina) kan du hitta lämpliga värden i [nationella moln](authentication-national-cloud.md).
- `Enter_the_Tenant_info_here` ska vara något av följande:
  - Om ditt program har stöd *för konton i den här organisations katalogen* ersätter du värdet med **klient-ID** eller **klient namn**. Ett exempel är `contoso.microsoft.com`.
  - Om ditt program har stöd *för konton i en organisations katalog* ersätter du värdet med `organizations` .
  - Om ditt program har stöd *för konton i en organisations katalog och personliga Microsoft-konton* ersätter du värdet med `common` .
  - Om du bara vill begränsa stödet till *personliga Microsoft-konton* ersätter du värdet med `consumers` .
- `Enter_the_Redirect_Uri_Here` är `http://localhost:3000`.

`authority`Värdet i *authConfig.js* bör likna följande om du använder det globala Azure-molnet:

```javascript
authority: "https://login.microsoftonline.com/common",
```

Skapa en fil med namnet *graphConfig.js* i mappen *app* . Lägg till följande kod för att tillhandahålla ditt program konfigurations parametrar för att anropa API: et för Microsoft Graph:

```javascript
// Add the endpoints here for Microsoft Graph API services you'd like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
};
```

Ändra värdena i `graphConfig` avsnittet enligt beskrivningen här:

- `Enter_the_Graph_Endpoint_Here` är instansen av det Microsoft Graph-API som programmet ska kommunicera med.
  - För den **globala** Microsoft Graph API-slutpunkten ersätter du båda instanserna av den här strängen med `https://graph.microsoft.com` .
  - För slut punkter i **nationella** moln distributioner, se [nationella moln distributioner](/graph/deployments) i Microsoft Graph-dokumentationen.

`graphMeEndpoint`Värdena och `graphMailEndpoint` i *graphConfig.js* bör likna följande om du använder den globala slut punkten:

```javascript
graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
```

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-user"></a>Använd Microsoft Authentication Library (MSAL) för att logga in användare

### <a name="pop-up"></a>Popup-fönster

I mappen *app* skapar du en fil med namnet *authPopup.js* och lägger till följande autentiserings-och token anskaffnings kod för inloggnings-popup:

```JavaScript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let username = "";

function loadPage() {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    const currentAccounts = myMSALObj.getAllAccounts();
    if (currentAccounts === null) {
        return;
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
        console.warn("Multiple accounts detected.");
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
        showWelcomeMessage(currentAccounts[0]);
    }
}

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        loadPage();
    }
}

function signIn() {
    myMSALObj.loginPopup(loginRequest).then(handleResponse).catch(error => {
        console.error(error);
    });
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenPopup(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
        console.warn("silent token acquisition fails. acquiring token using redirect");
        if (error instanceof msal.InteractionRequiredAuthError) {
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
                console.log(tokenResponse);

                return tokenResponse;
            }).catch(error => {
                console.error(error);
            });
        } else {
            console.warn(error);
        }
    });
}

function seeProfile() {
    getTokenPopup(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenPopup(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}

loadPage();
```

### <a name="redirect"></a>Omdirigera

Skapa en fil med namnet *authRedirect.js* i mappen *app* och Lägg till följande kod för autentisering och token-hämtning för inloggnings omdirigering:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;
let username = "";

// Redirect: once login is successful and redirects with tokens, call Graph API
myMSALObj.handleRedirectPromise().then(handleResponse).catch(err => {
    console.error(err);
});

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        /**
         * See here for more info on account retrieval:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */
        const currentAccounts = myMSALObj.getAllAccounts();
        if (currentAccounts === null) {
            return;
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
            console.warn("Multiple accounts detected.");
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
            showWelcomeMessage(currentAccounts[0]);
        }
    }
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenRedirect(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            if (error instanceof msal.InteractionRequiredAuthError) {
                // fallback to interaction when silent call fails
                return myMSALObj.acquireTokenRedirect(request);
            } else {
                console.warn(error);
            }
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenRedirect(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}
```

### <a name="how-the-code-works"></a>Så här fungerar koden

När en användare väljer knappen **Logga in** för första gången, `signIn` anropar metoden `loginPopup` för att logga in användaren. `loginPopup`Metoden öppnar ett popup-fönster med *Microsoft Identity Platform-slutpunkten* för att fråga och verifiera användarens autentiseringsuppgifter. Efter en lyckad inloggning initierar *msal.js* [auktoriseringskod-flödet](v2-oauth2-auth-code-flow.md).

I det här fallet skickas en PKCE till den CORS-skyddade token-slutpunkten och utbyts för token. En ID-token, åtkomsttoken och uppdaterad token tas emot av ditt program och bearbetas av *msal.js* och informationen i tokens cachelagras.

ID-token innehåller grundläggande information om användaren, t. ex. visnings namnet. Om du planerar att använda data från ID-token *måste* din backend-server verifiera den för att garantera att token har utfärdats till en giltig användare för ditt program.

Åtkomsttoken har en begränsad livs längd och upphör att gälla efter 24 timmar. Uppdateringstoken kan användas för att tyst hämta nya åtkomsttoken.

Det SPA du har skapat i den här självstudien anropar `acquireTokenSilent` och/eller `acquireTokenPopup` hämtar en *åtkomsttoken som* används för att fråga Microsoft Graph-API: t för användar profil information. Om du behöver ett exempel som validerar ID-token, se exempel programmet [Active-Directory-JavaScript-singlepageapp-dotNet-WebAPI-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) på GitHub. Exemplet använder ett ASP.NET webb-API för verifiering av token.

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Efter den första inloggningen ska appen inte be användarna att autentisera varje gång de behöver åtkomst till en skyddad resurs (det vill säga en token). Anropa för att förhindra sådana omautentiserings begär Anden `acquireTokenSilent` . Det finns dock vissa situationer där du kan behöva tvinga användare att interagera med Microsoft Identity Platform-slutpunkten. Till exempel:

- Användarna måste ange sina autentiseringsuppgifter på nytt eftersom lösen ordet har upphört att gälla.
- Ditt program begär åtkomst till en resurs och du behöver användarens medgivande.
- Tvåfaktorautentisering krävs.

Anrop `acquireTokenPopup` öppnar ett popup-fönster (eller `acquireTokenRedirect` omdirigerar användare till Microsoft Identity Platform-slutpunkten). I det fönstret måste användarna interagera genom att bekräfta sina autentiseringsuppgifter, ge tillåtelse till den begärda resursen eller utföra tvåfaktorautentisering.

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

`acquireTokenSilent`Metoden hanterar hämtning av token och förnyelsen utan några åtgärder från användaren. När `loginPopup` (eller `loginRedirect` ) körs för första gången `acquireTokenSilent` är metoden ofta använd för att hämta tokens som används för att komma åt skyddade resurser för efterföljande anrop. (Anrop till begäran eller förnya token görs i bakgrunden.) `acquireTokenSilent` kan Miss lyckas i vissa fall. Användarens lösen ord kan till exempel ha upphört att gälla. Ditt program kan hantera detta undantag på två sätt:

1. Gör ett anrop till `acquireTokenPopup` omedelbart för att utlösa en användar inloggnings uppmaning. Det här mönstret används ofta i online-program där det inte finns något oautentiserat innehåll i programmet som är tillgängligt för användaren. I det exempel som genereras av den här guidade installationen används det här mönstret.
1. Visar visuellt för användaren att en interaktiv inloggning krävs så att användaren kan välja rätt tid för att logga in, eller så kan programmet försöka igen `acquireTokenSilent` vid ett senare tillfälle. Den här tekniken används ofta när användaren kan använda andra funktioner i programmet utan att störas. Det kan till exempel finnas oautentiserat innehåll tillgängligt i programmet. I så fall kan användaren bestämma när de vill logga in för att komma åt den skyddade resursen eller uppdatera den inaktuella informationen.

> [!NOTE]
> I den här självstudien används `loginPopup` `acquireTokenPopup` metoderna och som standard. Om du använder Internet Explorer rekommenderar vi att du använder-och- `loginRedirect` `acquireTokenRedirect` metoderna på grund av ett [känt problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) med Internet Explorer och popup-fönster. Ett exempel på att uppnå samma resultat genom att använda omdirigerings metoder finns [*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js) på GitHub.

## <a name="call-the-microsoft-graph-api"></a>Anropa API: et för Microsoft Graph

Skapa en fil med namnet *graph.js* i mappen *app* och Lägg till följande kod för att göra REST-anrop till Microsoft Graph API:

```javascript
// Helper function to call Microsoft Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
```

I det exempel program som skapas i den här självstudien `callMSGraph()` används metoden för att göra en HTTP- `GET` begäran mot en skyddad resurs som kräver en token. Begäran returnerar sedan innehållet till anroparen. Den här metoden lägger till den hämtade token i *http-Authorization-huvudet*. I det exempel program som skapats i den här självstudien är den skyddade resursen den Microsoft Graph *-API:* t-slutpunkt som visar den inloggade användarens profil information.

## <a name="test-your-application"></a>Testa ditt program

Du har slutfört skapandet av programmet och är nu redo att starta Node.js webb servern och testa appens funktioner.

1. Starta Node.js-webbservern genom att köra följande kommando i roten i projektmappen:

   ```console
   npm start
   ```
1. I webbläsaren navigerar du till `http://localhost:3000` eller `http://localhost:<port>` , där `<port>` är den port som webb servern lyssnar på. Du bör se innehållet i *index.html* -filen och knappen **Logga in** .

### <a name="sign-in-to-the-application"></a>Logga in på programmet

När webbläsaren har läst in *index.html* -filen väljer du **Logga** in. Du uppmanas att logga in med Microsoft Identity Platform-slutpunkten:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-01-signin-dialog.png" alt-text="Webbläsare som visar dialog rutan för inloggning":::

### <a name="provide-consent-for-application-access"></a>Ge tillstånd för program åtkomst

Första gången du loggar in på ditt program uppmanas du att ge den åtkomst till din profil och logga in dig:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="Dialog rutan innehåll visas i webbläsaren":::

Om du godkänner de begärda behörigheterna visar webb programmen ditt användar namn, vilket indikerar en lyckad inloggning:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-03-signed-in.png" alt-text="Resultatet av en lyckad inloggning i webbläsaren":::

### <a name="call-the-graph-api"></a>Anropa Graph API

När du har loggat in väljer du **Se profil** för att Visa användar profil informationen som returneras i svaret från anropet till Microsoft Graph-API:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-04-see-profile.png" alt-text="Profil information från Microsoft Graph som visas i webbläsaren":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om omfattningar och delegerade behörigheter

Microsoft Graph-API: t kräver att *User. Read* -omfånget läser en användar profil. Som standard läggs denna omfattning automatiskt till i varje program som är registrerat i Azure Portal. Andra API: er för Microsoft Graph, samt anpassade API: er för backend-servern, kan kräva ytterligare omfång. Till exempel kräver Microsoft Graph-API: t *e-post. Read* -omfånget för att kunna lista användarens e-post.

När du lägger till omfattningar kan användarna uppmanas att ange ytterligare medgivande för de tillagda omfattningarna.

Om ett Server dels-API inte kräver ett omfång, vilket inte rekommenderas, kan du använda `clientId` som omfång i anropen för att hämta tokens.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Om du vill gå djupare till Java Script-program med en enda sida på Microsoft Identity Platform kan du läsa vår scenario serie med flera delar:

> [!div class="nextstepaction"]
> [Scenario: ett program med en sida](scenario-spa-overview.md)