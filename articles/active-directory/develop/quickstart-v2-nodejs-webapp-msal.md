---
title: 'Snabb start: lägga till autentisering i en Node-webbapp med MSAL-noden | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur du implementerar autentisering med en Node.js-webbapp och Microsoft Authentication Library (MSAL) för Node.js.
services: active-directory
author: mmacy
manager: celested
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2020
ms.author: marsma
ms.custom: aaddev, scenarios:getting-started, languages:js, devx-track-js
ms.openlocfilehash: 7fc76259f30f96d95c63b7e9369ec5e9647d923e
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98107454"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-node-web-app-using-the-auth-code-flow"></a>Snabb start: Logga in användare och hämta en åtkomsttoken i en Node-webbapp med hjälp av kod flödet för autentisering

I den här snabb starten laddar du ned och kör ett kod exempel som visar hur en Node.js webbapp kan logga in användare med hjälp av flödes kods flödet. Kod exemplet visar också hur du får en åtkomsttoken för att anropa Microsoft Graph API. 

Se [hur exemplet fungerar](#how-the-sample-works) för en illustration.

I den här snabb starten används Microsoft Authentication Library för Node.js (MSAL Node) med kod flödet för auktorisering.

> [!IMPORTANT]
> MSAL-nod [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en Azure-prenumeration kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) eller en annan kod redigerare

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrera och ladda ned snabbstartsprogrammet
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
>
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
> 1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
> 1. Ange ett **namn** för ditt program. Användare av appen kan se det här namnet och du kan ändra det senare.
> 1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
> 1. Ange **omdirigerings-URI** -värdet till `http://localhost:3000/redirect` .
> 1. Välj **Register** (Registrera). 
> 1. På sidan **Översikt över** appar noterar du **programmets (klient) ID-** värde för senare användning.
> 1. Under **Hantera** väljer du **certifikat & hemligheter**  >  **ny klient hemlighet**.  Lämna beskrivningen Tom och standard förfallo datum och välj sedan **Lägg till**.
> 1. Notera **värdet** för **klient hemligheten** för senare användning.

#### <a name="step-2-download-the-project"></a>Steg 2: Ladda ned projektet

> [!div renderon="docs"]
> Om du vill köra projektet med en webb server med hjälp av Node.js [laddar du ned de centrala projektfilerna](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Kör projektet med en webb server genom att använda Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-node-app"></a>Steg 3: Konfigurera din Node-app
>
> Extrahera projektet och öppna mappen *MS-Identity-Node-main* och öppna sedan *index.js* -filen.
> Ange `clientID` **ID för programmet (klient)**.
> Ange `clientSecret` **värdet** för **klient hemligheten**.
>
>```javascript
>const config = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "https://login.microsoftonline.com/common",
>        clientSecret: "Enter_the_Client_Secret_Here"
>    },
>    system: {
>        loggerOptions: {
>            loggerCallback(loglevel, message, containsPii) {
>                console.log(message);
>            },
>            piiLoggingEnabled: false,
>            logLevel: msal.LogLevel.Verbose,
>        }
>    }
>};
> ```

> [!div renderon="docs"]
>
> Ändra värdena i `config` avsnittet enligt beskrivningen här:
>
> - `Enter_the_Application_Id_Here` är **program-ID: t (Client)** för det program som du har registrerat.
> - `Enter_the_Client_Secret_Here` är **värdet** för **klient hemligheten** för det program som du har registrerat.
>
> `authority`Standardvärdet representerar det största (globala) Azure-molnet:
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> > [!TIP]
> > Du hittar värdet för **program-ID: t** genom att gå till sidan med registrerings **översikten** för appen i Azure Portal. Gå till **certifikat & hemligheter** för att hämta eller generera en ny **klient hemlighet**.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Steg 3: appen har kon figurer ATS och är redo att köras
>
> [!div renderon="docs"]
>
> #### <a name="step-4-run-the-project"></a>Steg 4: kör projektet

Kör projektet genom att använda Node.js:

1. Starta-servern genom att köra följande kommandon inifrån projekt katalogen:
    ```console
    npm install
    npm start
    ```
1. Gå till `http://localhost:3000/`.

1. Välj **Logga** in för att starta inloggnings processen.

    Första gången du loggar in uppmanas du att ange ditt medgivande så att programmet kan komma åt din profil och logga in dig. När du har loggat in visas ett logg meddelande på kommando raden.

## <a name="more-information"></a>Mer information

### <a name="how-the-sample-works"></a>Så här fungerar exemplet

Exemplet, vid körning, är värd för en webb server på localhost, Port 3000. När en webbläsare får åtkomst till den här platsen omdirigerar exemplet omedelbart användaren till en Microsoft-autentiseringsnyckel. Därför innehåller exemplet inte några *HTML-* eller visnings element. Autentiseringen lyckades visar meddelandet, "OK".

### <a name="msal-node"></a>MSAL-nod

MSAL-nodadressen loggar in användare och begär de token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform. Du kan hämta den senaste versionen med hjälp av Node.js Package Manager (NPM):

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till autentisering i ett befintligt GitHub kod exempel >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/auth-code)
