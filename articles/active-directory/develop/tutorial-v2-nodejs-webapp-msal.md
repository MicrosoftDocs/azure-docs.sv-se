---
title: 'Självstudie: inloggnings användare i en Node.js & Express-webbapp | Azure'
titleSuffix: Microsoft identity platform
description: I den här självstudien lägger du till stöd för inloggnings användare i en webbapp.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 3f1f26acbba0f5830421e760d6a68a11f618fa85
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648998"
---
# <a name="tutorial-sign-in-users-in-a-nodejs--express-web-app"></a>Självstudie: inloggnings användare i en Node.js & Express-webbapp

I den här självstudien skapar du en webbapp som loggar in användare. Den webbapp som du skapar använder [Microsoft Authentication Library (MSAL) för Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Följ stegen i den här självstudien för att:

> [!div class="checklist"]
> - Registrera programmet i Azure Portal
> - Skapa ett Express-webbprogram-projekt
> - Installera paket för autentiseringspaket
> - Lägg till registrerings information för appen
> - Lägg till kod för användar inloggning
> - Testa appen

## <a name="prerequisites"></a>Förutsättningar

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) eller en annan kod redigerare

## <a name="register-the-application"></a>Registrera programmet

Börja med att utföra stegen i [Registrera ett program med Microsoft Identity Platform](quickstart-register-app.md) för att registrera din app.

Använd följande inställningar för din app-registrering:

- Namn: `ExpressWebApp` (rekommenderas)
- Konto typer som stöds: **konton i valfri organisations katalog (alla Azure AD Directory-flera klienter) och personliga Microsoft-konton (t. ex. Skype, Xbox)**
- Plattforms typ: **webb**
- Omdirigerings-URI: `http://localhost:3000/redirect`
- Klient hemlighet: `*********` (registrera det här värdet för användning i ett senare steg – det visas bara en gång)

## <a name="create-the-project"></a>Skapa projektet

Skapa en mapp som är värd för ditt program, till exempel *ExpressWebApp*.

1. Ändra först till projekt katalogen i terminalen och kör sedan följande `npm` kommandon:

```console
    npm init -y
    npm install --save express
```

2. Skapa sedan en fil med namnet *index.js* och Lägg till följande kod:

```JavaScript
    const express = require("express");
    const msal = require('@azure/msal-node');

    const SERVER_PORT = process.env.PORT || 3000;

    // Create Express App and Routes
    const app = express();

    app.listen(SERVER_PORT, () => console.log(`Msal Node Auth Code Sample app listening on port ${SERVER_PORT}!`))
```

Nu har du en enkel webb server som körs på port 3000. Filen och mappstrukturen för projektet bör se ut ungefär så här:

```
ExpressWebApp/
├── index.js
└── package.json
```

## <a name="install-the-auth-library"></a>Installera auth-biblioteket

Leta upp roten i projekt katalogen i en Terminal och installera MSAL Node-paketet via NPM.

```console
    npm install --save @azure/msal-node
```

## <a name="add-app-registration-details"></a>Lägg till registrerings information för appen

Lägg till följande kod i *index.js* -filen som du skapade tidigare:

```JavaScript
    // Before running the sample, you will need to replace the values in the config,
    // including the clientSecret
    const config = {
        auth: {
            clientId: "Enter_the_Application_Id",
            authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Id_here",
            clientSecret: "Enter_the_Client_secret"
        },
        system: {
            loggerOptions: {
                loggerCallback(loglevel, message, containsPii) {
                    console.log(message);
                },
                piiLoggingEnabled: false,
                logLevel: msal.LogLevel.Verbose,
            }
        }
    };
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
- `Enter_the_Client_secret`: Ersätt det här värdet med den klient hemlighet som du skapade tidigare. Om du vill generera en ny nyckel använder du **certifikat & hemligheter** i appens registrerings inställningar i Azure Portal.

> [!WARNING]
> Eventuell hemlig text i käll koden utgör en ökad säkerhets risk. I den här artikeln används en klient hemlighet i klartext för enkelhetens skull. Använd [autentiseringsuppgifter](active-directory-certificate-credentials.md) i stället för klient hemligheter i dina konfidentiella klient program, särskilt de appar som du tänker distribuera till produktion.

## <a name="add-code-for-user-login"></a>Lägg till kod för användar inloggning

Lägg till följande kod i *index.js* -filen som du skapade tidigare:

```JavaScript
    // Create msal application object
    const cca = new msal.ConfidentialClientApplication(config);

    app.get('/', (req, res) => {
        const authCodeUrlParameters = {
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        // get url to sign user in and consent to scopes needed for application
        cca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            res.redirect(response);
        }).catch((error) => console.log(JSON.stringify(error)));
    });

    app.get('/redirect', (req, res) => {
        const tokenRequest = {
            code: req.query.code,
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        cca.acquireTokenByCode(tokenRequest).then((response) => {
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => {
            console.log(error);
            res.status(500).send(error);
        });
    });
```

## <a name="test-sign-in"></a>Testa inloggning

Du har slutfört skapandet av programmet och är nu redo att testa appens funktioner.

1. Starta Node.js-konsolen genom att köra följande kommando i roten i projektmappen:

```console
   node index.js
```

2. Öppna en webbläsare och gå till `http://localhost:3000`. Du bör se en inloggnings skärm:

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/sign-in-screen.png" alt-text="Inloggnings skärmen för Azure AD visas":::

3. När du har angett dina autentiseringsuppgifter bör du se ett medgivande fönster där du uppmanas att godkänna appens behörigheter.

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/consent-screen.png" alt-text="Skärmen Azure AD-godkännande visas":::

## <a name="how-the-application-works"></a>Så här fungerar programmet

I den här självstudien initierade du en MSAL Node [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) -objekt genom att skicka det till ett konfigurations objekt (*msalConfig*) som innehåller parametrar som hämtats från din Azure AD-app-registrering på Azure Portal. Webbappen som du skapade använder [OAuth 2,0-auktoriseringskod som ger](./v2-oauth2-auth-code-flow.md) inloggnings användare och hämtar ID och åtkomsttoken.

## <a name="next-steps"></a>Nästa steg

Om du vill få djupare inblick i Node.js & utveckling av webb program på Microsoft Identity Platform, se vår scenario serie med flera delar:

> [!div class="nextstepaction"]
> [Scenario: webb program som loggar in användare](scenario-web-app-sign-user-overview.md)