---
title: 'Självstudie: anropa Microsoft Graph i en Node.js-konsol program | Azure'
titleSuffix: Microsoft identity platform
description: I den här självstudien skapar du en konsol app för att anropa Microsoft Graph till en Node.js-konsol.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 33d3712e25a06419e0ccc5914cdddfae7d85a371
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645797"
---
# <a name="tutorial-call-the-microsoft-graph-api-in-a-nodejs-console-app"></a>Självstudie: anropa Microsoft Graph API i en Node.js-konsol program

I den här självstudien skapar du en-konsol app som anropar Microsoft Graph API med sin egen identitet. Den konsol app som du skapar använder [Microsoft Authentication Library (MSAL) för Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Följ stegen i den här självstudien för att:

> [!div class="checklist"]
> - Registrera programmet i Azure Portal
> - Skapa ett Node.js-konsolprogram för projekt
> - Lägg till autentiserings logik i din app
> - Lägg till registrerings information för appen
> - Lägga till en metod för att anropa ett webb-API
> - Testa appen

## <a name="prerequisites"></a>Förutsättningar

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) eller en annan kod redigerare

## <a name="register-the-application"></a>Registrera programmet

Börja med att utföra stegen i [Registrera ett program med Microsoft Identity Platform](quickstart-register-app.md) för att registrera din app.

Använd följande inställningar för din app-registrering:

- Namn: `NodeConsoleApp` (rekommenderas)
- Konto typer som stöds: **konton endast i den här organisations katalogen**
- API-behörigheter: **Microsoft API: er**  >  **Microsoft Graph**  >  **program behörigheter** > `User.Read.All`
- Klient hemlighet: `*********` (registrera det här värdet för användning i ett senare steg – det visas bara en gång)

## <a name="create-the-project"></a>Skapa projektet

Skapa en mapp som är värd för ditt program, till exempel *NodeConsoleApp*.

1. Ändra först till projekt katalogen i terminalen och kör sedan följande NPM-kommandon:

```console
    npm init -y
    npm install --save dotenv yargs axios @azure/msal-node
```

2. Skapa sedan en mapp med namnet *bin*. I den här mappen skapar du sedan en fil med namnet *index.js* och lägger till följande kod:

```JavaScript
#!/usr/bin/env node

// read in env settings
require('dotenv').config();

const yargs = require('yargs');

const fetch = require('./fetch');
const auth = require('./auth');

const options = yargs
    .usage('Usage: --op <operation_name>')
    .option('op', { alias: 'operation', describe: 'operation name', type: 'string', demandOption: true })
    .argv;

async function main() {
    console.log(`You have selected: ${options.op}`);

    switch (yargs.argv['op']) {
        case 'getUsers':

            try {
                // here we get an access token
                const authResponse = await auth.getToken(auth.tokenRequest);

                // call the web API with the access token
                const users = await fetch.callApi(auth.apiConfig.uri, authResponse.accessToken);

                // display result
                console.log(users);
            } catch (error) {
                console.log(error);
            }

            break;
        default:
            console.log('Select a Graph operation first');
            break;
    }
};

main();
```

Den här filen refererar till två andra Node-moduler: *auth.js* som innehåller en implementering av MSAL-noden för att förvärva åtkomsttoken och *fetch.js* som innehåller en metod för att göra en HTTP-begäran till Microsoft Graph API med en åtkomsttoken. När du har slutfört resten av självstudien bör filen och mappstrukturen i projektet se ut ungefär så här:

```
NodeConsoleApp/
├── bin
│   ├── auth.js
│   ├── fetch.js
│   ├── index.js
├── package.json
└── .env
```

## <a name="add-authentication-logic"></a>Lägg till autentiserings logik

I mappen *bin* skapar du en annan fil med namnet *auth.js* och lägger till följande kod för att förvärva en åtkomsttoken att presentera när du anropar Microsoft Graph-API: et.

```JavaScript
const msal = require('@azure/msal-node');

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

/**
 * With client credentials flows permissions need to be granted in the portal by a tenant administrator.
 * The scope is always in the format '<resource>/.default'. For more, visit:
 * https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow
 */
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'],
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + 'v1.0/users',
};

/**
 * Initialize a confidential client application. For more info, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md
 */
const cca = new msal.ConfidentialClientApplication(msalConfig);

/**
 * Acquires token with client credentials.
 * @param {object} tokenRequest
 */
async function getToken(tokenRequest) {
    return await cca.acquireTokenByClientCredential(tokenRequest);
}

module.exports = {
    apiConfig: apiConfig,
    tokenRequest: tokenRequest,
    getToken: getToken
};
```

I kodfragmentet ovan skapar vi först ett konfigurations objekt (*msalConfig*) och skickar det för att INITIERA en MSAL- [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md). Sedan skapar vi en metod för att hämta token via **klientautentiseringsuppgifter** och visar slutligen modulen som ska användas av *main.js*. Konfigurations parametrarna i den här modulen hämtas från en miljö fil som vi ska skapa i nästa steg.

## <a name="add-app-registration-details"></a>Lägg till registrerings information för appen

Skapa en miljö fil för att lagra registrerings informationen för appen som ska användas när du hämtar tokens. Det gör du genom att skapa en fil med namnet *. kuvert* i rotmappen i exemplet (*NodeConsoleApp*) och lägga till följande kod:

```
# Credentials
TENANT_ID=Enter_the_Tenant_Id_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
```

Fyll i informationen med de värden som du får från Azure App Registration Portal:

- `Enter_the_Tenant_Id_here` ska vara något av följande:
  - Om ditt program har stöd *för konton i den här organisations katalogen* ersätter du värdet med **klient-ID** eller **klient namn**. Till exempel `contoso.microsoft.com`.
  - Om ditt program har stöd *för konton i en organisations katalog* ersätter du värdet med `organizations` .
  - Om ditt program har stöd *för konton i en organisations katalog och personliga Microsoft-konton* ersätter du värdet med `common` .
  - Om du bara vill begränsa stödet till *personliga Microsoft-konton* ersätter du värdet med `consumers` .
- `Enter_the_Application_Id_Here`: **Program-ID: t (klient)** för det program som du har registrerat.
- `Enter_the_Cloud_Instance_Id_Here`: Azure Cloud-instansen där ditt program är registrerat.
  - För det huvudsakliga (eller *globala*) Azure-molnet anger du `https://login.microsoftonline.com` .
  - För **nationella** moln (till exempel Kina) kan du hitta lämpliga värden i [nationella moln](authentication-national-cloud.md).
- `Enter_the_Graph_Endpoint_Here` är instansen av det Microsoft Graph-API som programmet ska kommunicera med.
  - För den **globala** Microsoft Graph API-slutpunkten ersätter du båda instanserna av den här strängen med `https://graph.microsoft.com` .
  - För slut punkter i **nationella** moln distributioner, se [nationella moln distributioner](/graph/deployments) i Microsoft Graph-dokumentationen.

## <a name="add-a-method-to-call-a-web-api"></a>Lägga till en metod för att anropa ett webb-API

I mappen *bin* skapar du en annan fil med namnet *fetch.js* och lägger till följande kod för att göra REST-anrop till Microsoft Graph API:

```javascript
const axios = require('axios');

/**
 * Calls the endpoint with authorization bearer token.
 * @param {string} endpoint
 * @param {string} accessToken
 */
async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};

module.exports = {
    callApi: callApi
};
```

Här `callApi` används metoden för att göra en HTTP- `GET` begäran mot en skyddad resurs som kräver en åtkomsttoken. Begäran returnerar sedan innehållet till anroparen. Den här metoden lägger till den hämtade token i *http-Authorization-huvudet*. Den skyddade resursen här är Microsoft Graph-API [användarens slut punkt](/graph/api/user-list) som visar användarna i klient organisationen där den här appen är registrerad.

## <a name="test-the-app"></a>Testa appen

Du har slutfört skapandet av programmet och är nu redo att testa appens funktioner.

Starta Node.js-konsolen genom att köra följande kommando i roten i projektmappen:

```console
node . --op getUsers
```

Detta bör resultera i ett JSON-svar från Microsoft Graph API och du bör se en matris med användar objekt i-konsolen:

```console
You have selected: getUsers
request made to web API at: Fri Jan 22 2021 09:31:52 GMT-0800 (Pacific Standard Time)
{
    '@odata.context': 'https://graph.microsoft.com/v1.0/$metadata#users',
    value: [
        {
            displayName: 'Adele Vance'
            givenName: 'Adele',
            jobTitle: 'Retail Manager',
            mail: 'AdeleV@msaltestingjs.onmicrosoft.com',
            mobilePhone: null,
            officeLocation: '18/2111',
            preferredLanguage: 'en-US',
            surname: 'Vance',
            userPrincipalName: 'AdeleV@msaltestingjs.onmicrosoft.com',
            id: 'a6a218a5-f5ae-462a-acd3-581af4bcca00'
        }
    ]
}
```
:::image type="content" source="media/tutorial-v2-nodejs-console/screenshot.png" alt-text="Kommando rads gränssnitt visar diagram svar":::

## <a name="how-the-application-works"></a>Så här fungerar programmet

Det här programmet använder [OAuth 2,0](./v2-oauth2-client-creds-grant-flow.md)-klientautentiseringsuppgifter. Den här beviljandetypen används ofta för server-till-server-interaktioner som måste köras i bakgrunden, utan direkt interaktion med en användare. Flöde för autentiseringsuppgifter som tillåter en webb tjänst (konfidentiell klient) att använda sina egna autentiseringsuppgifter, i stället för att personifiera en användare, för att autentisera vid anrop till en annan webb tjänst. Den typ av program som stöds med den här autentiseringsmetoden är vanligt vis **daemon** eller **tjänst konton**.

Omfånget för att begära ett flöde för klientautentiseringsuppgifter är namnet på resursen följt av `/.default` . Den här texten talar om för Azure Active Directory (Azure AD) att använda behörigheter på program nivå som har deklarerats statiskt under program registrering. Dessa API-behörigheter måste också beviljas av en **innehavaradministratör**.

## <a name="next-steps"></a>Nästa steg

Om du vill gå djupare till Node.js-konsol program utveckling på Microsoft Identity Platform, se vår serie med flera delar:

> [!div class="nextstepaction"]
> [Scenario: daemon-program](scenario-daemon-overview.md)