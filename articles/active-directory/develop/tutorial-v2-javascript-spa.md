---
title: 'Självstudie: skapa en Java Script-app med en sida som använder Microsoft Identity Platform för autentisering | Azure'
titleSuffix: Microsoft identity platform
description: 'I den här självstudien skapar du en JavaScript-app med en enda sida (SPA) som använder Microsoft Identity Platform för att logga in användare och få en åtkomsttoken för att anropa Microsoft Graph-API: et för deras räkning.'
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/06/2020
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: d790b466f669ed067863b6643c8f59662eb628a7
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226445"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Självstudie: Logga in användare och anropa Microsoft Graph API från ett Java Script (Single-Side Application)

I den här självstudien skapar du ett Java Script-program med en enda sida (SPA) som loggar in användare och anropar Microsoft Graph med hjälp av det implicita flödet. Det SPA som du skapar använder Microsoft Authentication Library (MSAL) för Java Script v 1.0.

I de här självstudierna har du

> [!div class="checklist"]
> * Skapa ett JavaScript-projekt med `npm`
> * Registrera programmet i Azure Portal
> * Lägg till kod som stöd för användar inloggning och utloggning
> * Lägg till kod för anrop Microsoft Graph API
> * Testa appen

>[!TIP]
> I den här självstudien används MSAL.js v1. x som är begränsad till att använda det implicita beviljande flödet för program med en enda sida. Vi rekommenderar att alla nya program använder i stället [MSAL.js 2. x och auktoriseringskod med stöd för PKCE och CORS](tutorial-v2-javascript-auth-code.md) .

## <a name="prerequisites"></a>Förutsättningar

* [Node.js](https://nodejs.org/en/download/) för att köra en lokal webb server.
* [Visual Studio Code](https://code.visualstudio.com/download) eller annan redigerare för att ändra projektfiler.
* En modern webbläsare. **Internet Explorer** **stöds inte** av den app som du skapar i den här självstudien på grund av appens användning av [ES6](http://www.ecma-international.org/ecma-262/6.0/) -konventioner.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Hur exempel appen som genereras av den här hand boken fungerar

![Visar hur exempel appen som genereras av den här själv studie kursen fungerar](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

Exempel programmet som skapats i den här guiden gör det möjligt för ett Java Script-SPA att fråga Microsoft Graph-API eller ett webb-API som accepterar tokens från Microsoft Identity Platform. I det här scenariot begärs en åtkomsttoken efter att användaren loggar in och läggs till i HTTP-begäranden via Authorization-huvudet. Denna token kommer att användas för att hämta användarens profil och e-post via **MS Graph API**.

Hämtning av token och förnyelse hanteras av [Microsoft Authentication Library (MSAL) för Java Script](https://github.com/AzureAD/microsoft-authentication-library-for-js).

## <a name="set-up-your-web-server-or-project"></a>Konfigurera din webb server eller ditt projekt

> Vill du ladda ned det här exemplets projekt i stället? [Ladda ned projektfilerna](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> Om du vill konfigurera kod exemplet innan du kör det går du vidare till [konfigurations steget](#register-your-application).

## <a name="create-your-project"></a>Skapa projektet

Kontrol lera att du har [Node.js](https://nodejs.org/en/download/) installerat och skapa sedan en mapp som är värd för ditt program. Där ska vi implementera en enkel [Express](https://expressjs.com/) -webbserver för att hantera din `index.html` fil.

1. Använd en Terminal (till exempel Visual Studio Code Integrated Terminal), leta upp projektmappen och skriv:

   ```console
   npm init
   ```

2. Installera sedan de nödvändiga beroendena:

   ```console
   npm install express --save
   npm install morgan --save
   ```

1. Skapa nu en. js-fil med namnet `server.js` och Lägg sedan till följande kod:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');

   //initialize express.
   const app = express();

   // Initialize variables.
   const port = 3000; // process.env.PORT || 3000;

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use(express.static('JavaScriptSPA'))

   // Set up a route for index.html.
   app.get('*', function (req, res) {
       res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log('Listening on port ' + port + '...');
   ```

Nu har du en enkel server som kan hantera din SPA. Den avsedda mappstrukturen i slutet av den här självstudien är följande:

![en text som beskriver den avsedda SPA-mappstrukturen](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Skapa SPA-ANVÄNDARGRÄNSSNITTET

1. Skapa en `index.html` fil för Java Script Spa. Den här filen implementerar ett användar gränssnitt som skapats med **bootstrap 4 Framework** och importerar skriptfiler för konfigurations-, autentiserings-och API-anrop.

   I `index.html` filen lägger du till följande kod:

   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
       <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

       <!-- msal.js with a fallback to backup CDN -->
       <script type="text/javascript" src="https://alcdn.msauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
       <script type="text/javascript">
         if(typeof Msal === 'undefined')document.write(unescape("%3Cscript src='https://alcdn.msftauth.net/lib/1.2.1/js/msal.js' type='text/javascript' integrity='sha384-m/3NDUcz4krpIIiHgpeO0O8uxSghb+lfBTngquAo2Zuy2fEF+YgFeP08PWFo5FiJ' crossorigin='anonymous'%3E%3C/script%3E"));
       </script>

       <!-- adding Bootstrap 4 for UI components  -->
       <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
     </head>
     <body>
       <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
         <a class="navbar-brand" href="/">MS Identity Platform</a>
         <div class="btn-group ml-auto dropleft">
           <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign In</button>
           <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign Out</button>
       </div>
       </nav>
       <br>
       <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
       <br>
       <div class="row" style="margin:auto" >
       <div id="card-div" class="col-md-3 d-none">
       <div class="card text-center">
         <div class="card-body">
           <h5 class="card-title" id="welcomeMessage">Please sign-in to see your profile and read your mails</h5>
           <div id="profile-div"></div>
           <br>
           <br>
           <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
           <br>
           <br>
           <button class="btn btn-primary d-none" id="readMail" onclick="readMail()">Read Mails</button>
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

       <!-- replace next line with authRedirect.js if you would like to use the redirect flow -->
       <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
       <script type="text/javascript" src="./authPopup.js"></script>
       <script type="text/javascript" src="./graph.js"></script>
     </body>
   </html>
   ```

   > [!TIP]
   > Du kan ersätta den tidigare versionen av MSAL.js i skriptet med den senaste versionen under [MSAL.js versioner](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

2. Nu ska du skapa en. js-fil med namnet `ui.js` , som kommer att få åtkomst till och uppdatera dom-element och lägga till följande kod:

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
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

Registrera ditt program på **Azure Active Directory** innan du fortsätter med autentiseringen.

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
1. Sök efter och välj **Azure Active Directory**.
1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
1. Ange ett **namn** för ditt program. Användare av appen kan se det här namnet och du kan ändra det senare.
1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
1. I avsnittet **omdirigerings-URI** väljer du **webb** plattformen i list rutan och anger sedan värdet till den program-URL som är baserad på din webb server.
1. Välj **Register** (Registrera).
1. På sidan **Översikt över** appar noterar du **programmets (klient) ID-** värde för senare användning.
1. Under **Hantera** väljer du **autentisering**.
1. I avsnittet **implicit beviljande och hybrid flöden** väljer du **ID-tokens** och **åtkomsttoken**. ID-token och åtkomsttoken krävs eftersom den här appen måste logga in användare och anropa ett API.
1. Välj **Spara**.

> ### <a name="set-a-redirect-url-for-nodejs"></a>Ange en omdirigerings-URL för Node.js
>
> För Node.js kan du ange webb Server porten i *server.js* -filen. I den här självstudien används port 3000, men du kan använda någon annan tillgänglig port.
>
> Om du vill konfigurera en omdirigerings-URL i program registrerings informationen växlar du tillbaka till fönstret för **program registrering** och gör något av följande:
>
> - Ange *`http://localhost:3000/`* som **omdirigerings-URL**.
> - Om du använder en anpassad TCP-port använder du *`http://localhost:<port>/`* (där *\<port>* är det anpassade TCP-portnumret).
>   1. Kopiera **URL** -värdet.
>   1. Växla tillbaka till fönstret **program registrering** och klistra in det kopierade värdet som **omdirigerings-URL**.
>

### <a name="configure-your-javascript-spa"></a>Konfigurera ditt Java Script SPA

Skapa en ny. js-fil med namnet `authConfig.js` , som innehåller dina konfigurations parametrar för autentisering och Lägg till följande kod:

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
    scopes: ["openid", "profile", "User.Read"]
  };

  // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
    scopes: ["Mail.Read"]
  };
```

 Plats:
 - *\<Enter_the_Application_Id_Here>* är **program-ID: t (Client)** för det program som du har registrerat.
 - *\<Enter_the_Cloud_Instance_Id_Here>* är instansen av Azure-molnet. För det största eller globala Azure-molnet anger du bara *https://login.microsoftonline.com* . För **nationella** moln (till exempel Kina), se [nationella moln](./authentication-national-cloud.md).
 - *\<Enter_the_Tenant_info_here>* är inställt på något av följande alternativ:
   - Om ditt program har stöd *för konton i den här organisations katalogen* ersätter du värdet med **klient-ID** eller **klient namn** (till exempel *contoso.Microsoft.com*).
   - Om ditt program har stöd *för konton i en organisations katalog* ersätter du värdet med **organisationer**.
   - Om ditt program har stöd *för konton i en organisations katalog och personliga Microsoft-konton* ersätter du värdet med **vanligt**. Om du bara vill begränsa stödet till *personliga Microsoft-konton* ersätter du värdet med **konsumenter**.


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Använd Microsoft Authentication Library (MSAL) för att logga in användaren

Skapa en ny. js-fil med namnet `authPopup.js` , som innehåller din autentiserings-och token förvärvs logik och Lägg till följande kod:

   ```JavaScript
   const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   function signIn() {
     myMSALObj.loginPopup(loginRequest)
       .then(loginResponse => {
         console.log('id_token acquired at: ' + new Date().toString());
         console.log(loginResponse);

         if (myMSALObj.getAccount()) {
           showWelcomeMessage(myMSALObj.getAccount());
         }
       }).catch(error => {
         console.log(error);
       });
   }

   function signOut() {
     myMSALObj.logout();
   }

   function callMSGraph(theUrl, accessToken, callback) {
       var xmlHttp = new XMLHttpRequest();
       xmlHttp.onreadystatechange = function () {
           if (this.readyState == 4 && this.status == 200) {
              callback(JSON.parse(this.responseText));
           }
       }
       xmlHttp.open("GET", theUrl, true); // true for asynchronous
       xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
       xmlHttp.send();
   }

   function getTokenPopup(request) {
     return myMSALObj.acquireTokenSilent(request)
       .catch(error => {
         console.log(error);
         console.log("silent token acquisition fails. acquiring token using popup");

         // fallback to interaction when silent call fails
           return myMSALObj.acquireTokenPopup(request)
             .then(tokenResponse => {
               return tokenResponse;
             }).catch(error => {
               console.log(error);
             });
       });
   }

   function seeProfile() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(loginRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
           profileButton.classList.add('d-none');
           mailButton.classList.remove('d-none');
         }).catch(error => {
           console.log(error);
         });
     }
   }

   function readMail() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(tokenRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
         }).catch(error => {
           console.log(error);
         });
     }
   }
   ```

### <a name="more-information"></a>Mer information

När en användare väljer knappen **Logga in** för första gången, `signIn` anropar metoden `loginPopup` för att logga in användaren. Den här metoden öppnar ett popup-fönster med *Microsoft Identity Platform-slutpunkten* för att fråga och verifiera användarens autentiseringsuppgifter. Efter en lyckad inloggning omdirigeras användaren tillbaka till den ursprungliga *index.html* -sidan. En token tas emot, bearbetas av `msal.js` och den information som finns i token cachelagras. Denna token kallas *ID-token* och innehåller grundläggande information om användaren, t. ex. användarens visnings namn. Om du planerar att använda data som tillhandahålls av denna token i något syfte, måste du se till att denna token verifieras av backend-servern för att garantera att token har utfärdats till en giltig användare för ditt program.

Det SPA som genereras av den här guiden anropar `acquireTokenSilent` och/eller `acquireTokenPopup` hämtar en *åtkomsttoken som* används för att fråga Microsoft Graph-API: t för användar profil information. Ta en titt på [det här](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub Active-Directory-JavaScript-singlepageapp-dotNet-WebAPI-v2-exempel") exempel programmet i GitHub om du behöver ett exempel som VALIDERAR ID-token. Exemplet använder ett ASP.NET webb-API för verifiering av token.

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Efter den första inloggningen vill du inte be användarna att autentisera varje gång de behöver för att begära en token för att få åtkomst till en resurs. Så *acquireTokenSilent* bör användas mest av tiden för att hämta tokens. Det finns dock situationer där du måste tvinga användare att interagera med Microsoft Identity Platform. Exempel:

- Användare måste ange sina autentiseringsuppgifter på grund av att lösen ordet har upphört att gälla.
- Ditt program begär åtkomst till en resurs och du behöver användarens medgivande.
- Tvåfaktorautentisering krävs.

När du anropar *acquireTokenPopup* öppnas ett popup-fönster (eller *acquireTokenRedirect* omdirigerar användare till Microsoft Identity Platform). I det fönstret måste användarna interagera genom att bekräfta sina autentiseringsuppgifter, ge tillåtelse till den begärda resursen eller utföra tvåfaktorautentisering.

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

`acquireTokenSilent`Metoden hanterar hämtning av token och förnyelsen utan några åtgärder från användaren. När `loginPopup` (eller `loginRedirect` ) körs för första gången `acquireTokenSilent` är metoden ofta använd för att hämta tokens som används för att komma åt skyddade resurser för efterföljande anrop. (Anrop till begäran eller förnya token görs i bakgrunden.) `acquireTokenSilent` kan Miss lyckas i vissa fall. Användarens lösen ord kan till exempel ha upphört att gälla. Ditt program kan hantera detta undantag på två sätt:

1. Gör ett anrop `acquireTokenPopup` direkt, vilket utlöser en användar inloggnings meddelande. Det här mönstret används ofta i online-program där det inte finns något oautentiserat innehåll i programmet som är tillgängligt för användaren. I det exempel som genereras av den här guidade installationen används det här mönstret.

1. Program kan också göra en visuell indikation på användaren om att en interaktiv inloggning krävs, så att användaren kan välja rätt tid för att logga in, eller så kan programmet försöka igen `acquireTokenSilent` vid ett senare tillfälle. Detta används vanligt vis när användaren kan använda andra funktioner i programmet utan att störas. Det kan till exempel finnas oautentiserat innehåll tillgängligt i programmet. I så fall kan användaren bestämma när de vill logga in för att komma åt den skyddade resursen eller uppdatera den inaktuella informationen.

> [!NOTE]
> I den här snabb starten används `loginPopup` `acquireTokenPopup` som standard metoderna och. Om du använder Internet Explorer som webbläsare rekommenderar vi att du använder `loginRedirect` och `acquireTokenRedirect` metoder, på grund av ett [känt problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) som är relaterade till hur Internet Explorer hanterar popup-fönster. Om du vill se hur du uppnår samma resultat med hjälp av `Redirect methods` , kan du [läsa mer i](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Anropa Microsoft Graph-API: et genom att använda den token som du nyss hämtade

1. Börja med att skapa en. js-fil med namnet `graphConfig.js` , som lagrar dina rest-slutpunkter. Lägg till följande kod:

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   Plats:
   - *\<Enter_the_Graph_Endpoint_Here>* är instansen av MS Graph API. För den globala MS Graph API-slutpunkten ersätter du bara den här strängen med `https://graph.microsoft.com` . För nationella moln distributioner, se Graph API- [dokumentationen](/graph/deployments).

1. Skapa sedan en. js-fil med namnet `graph.js` , som kommer att göra ett rest-anrop till Microsoft Graph API och lägga till följande kod:

   ```javascript
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
       .catch(error => console.log(error))
   }
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mer information om hur du gör ett REST-anrop mot ett skyddat API

I det exempel program som skapats av den här guiden `callMSGraph()` används metoden för att göra en HTTP- `GET` begäran mot en skyddad resurs som kräver en token. Begäran returnerar sedan innehållet till anroparen. Den här metoden lägger till den hämtade token i *http-Authorization-huvudet*. För det exempel program som skapas av den här hand boken är resursen den Microsoft Graph API: t *mig* -slutpunkt, som visar användarens profil information.

## <a name="test-your-code"></a>Testa koden

1. Konfigurera servern så att den lyssnar på en TCP-port som är baserad på platsen för din *index.html* -fil. För Node.js startar du webb servern för att lyssna på porten genom att köra följande kommandon i kommando tolken från programmappen:

   ```bash
   npm install
   npm start
   ```
1. I webbläsaren anger **http://localhost:3000** eller **http://localhost:{port}** , där *port* är den port som webb servern lyssnar på. Du bör se innehållet i *index.html* -filen och knappen **Logga in** .

När webbläsaren har läst in *index.html* -filen väljer du **Logga** in. Du uppmanas att logga in med Microsoft Identity Platform:

![Inloggnings fönstret för Java scripts SPA-konto](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Ge tillstånd för program åtkomst

Första gången du loggar in på ditt program uppmanas du att ge den åtkomst till din profil och logga in dig:

![Fönstret "behörigheter begärd"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Visa program resultat

När du har loggat in returneras din användar profil information i det Microsoft Graph API-svar som visas:

![Resultat från API-anropet Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om omfattningar och delegerade behörigheter

Microsoft Graph-API: t kräver att *User. Read* -omfånget läser en användar profil. Som standard läggs det här omfånget automatiskt till i varje program som är registrerat på registrerings portalen. Andra API: er för Microsoft Graph, samt anpassade API: er för backend-servern, kan kräva ytterligare omfång. Till exempel kräver Microsoft Graph-API: t *e-post. Read-* omfånget för att kunna lista användarens e-post.

> [!NOTE]
> Användaren kan tillfrågas om ytterligare medgivanden när du ökar antalet omfång.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Gå djupare in i en Enkels Ides programs utveckling (SPA) på Microsofts identitets plattform i vårt scenario med flera delar.

> [!div class="nextstepaction"]
> [Scenario: ett program med en sida](scenario-spa-overview.md)
