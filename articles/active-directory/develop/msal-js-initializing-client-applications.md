---
title: Initiera MSAL.js-klientens appar | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om att initiera klient program med hjälp av Microsoft Authentication Library för Java Script (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/17/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: e12beafc81cbc41ea070fbdda1d68990f6f3fcda
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063696"
---
# <a name="initialize-client-applications-using-msaljs"></a>Initiera klient program med hjälp av MSAL.js

I den här artikeln beskrivs hur du initierar Microsoft Authentication Library för Java Script (MSAL.js) med en instans av ett användar agent program.

Användar agent programmet är en form av ett offentligt klient program där klient koden körs i en användar agent, till exempel en webbläsare. Sådana klienter lagrar inte hemligheter eftersom webb läsar kontexten är lättillgänglig.

Mer information om klient program typer och program konfigurations alternativ finns i [offentliga och konfidentiella klient program i MSAL](msal-client-applications.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du initierar ett program måste du först [registrera det med Azure Portal](scenario-spa-app-registration.md)och upprätta en förtroende relation mellan ditt program och Microsoft Identity Platform.

När du har registrerat din app behöver du några eller alla följande värden som finns i Azure Portal.

| Värde | Krävs | Beskrivning |
|:----- | :------: | :---------- |
| Program-ID (klient) | Krävs | Ett GUID som unikt identifierar ditt program i Microsoft Identity Platform. |
| Myndighet | Valfritt | Identitets leverantörens URL ( *instansen*) och *inloggnings mål gruppen* för ditt program. Instansen och inloggnings mål gruppen, när de sammanfogas, utgör *utfärdaren*. |
| Katalog-ID (klient) | Valfritt | Ange detta om du skapar ett branschspecifika program enbart för din organisation, vilket ofta kallas ett program för en *klient*. |
| Omdirigerings-URI | Valfritt | Om du skapar en webbapp anger i vilken `redirectUri` identitet leverantören (Microsoft Identity Platform) ska returnera de säkerhetstoken som den har utfärdat. |

## <a name="initialize-msaljs-2x-apps"></a>Initiera MSAL.js 2. x-appar

Initiera kontexten för MSAL-autentisering genom att instansiera en [PublicClientApplication][msal-js-publicclientapplication] med ett [konfigurations][msal-js-configuration] objekt. Den minsta konfigurations egenskapen som krävs är `clientID` av ditt program, som visas som **program-ID** på sidan **Översikt** i appens registrering i Azure Portal.

Här är ett exempel på ett konfigurations objekt och en instansiering av en `PublicClientApplication` :

```javascript
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111",
        authority: "https://login.microsoftonline.com/common",
        knownAuthorities: [],
        redirectUri: "https://localhost:3001",
        postLogoutRedirectUri: "https://localhost:3001/logout",
        navigateToLoginRequestUrl: true
    },
    cache: {
        cacheLocation: "sessionStorage",
        storeAuthStateInCookie: false
    },
    system: {
        loggerOptions: {
            loggerCallback: (level: LogLevel, message: string, containsPii: boolean): void => {
                if (containsPii) {
                    return;
                }
                switch (level) {
                    case LogLevel.Error:
                        console.error(message);
                        return;
                    case LogLevel.Info:
                        console.info(message);
                        return;
                    case LogLevel.Verbose:
                        console.debug(message);
                        return;
                    case LogLevel.Warning:
                        console.warn(message);
                        return;
                }
            },
            piiLoggingEnabled: false
        },
        windowHashTimeout: 60000,
        iframeHashTimeout: 6000,
        loadFrameTimeout: 0
    }
};

// Create an instance of PublicClientApplication
const msalInstance = new PublicClientApplication(msalConfig);

// Handle the redirect flows
msalInstance.handleRedirectPromise().then((tokenResponse) => {
    // Handle redirect response
}).catch((error) => {
    // Handle redirect error
});
```

### `handleRedirectPromise`

Anropa [handleRedirectPromise][msal-js-handleredirectpromise] när ditt program använder omdirigerings flöden. När du använder omdirigerings flöden `handleRedirectPromise` ska köras på alla sid inläsningar.

Det finns tre möjliga resultat från löftet:

- `.then` anropas och `tokenResponse` är truthy: programmet returneras från en omdirigering som lyckades.
- `.then` anropas och `tokenResponse` är falskt ( `null` ): programmet returnerar inget från en omdirigerings åtgärd.
- `.catch` anropas: programmet returneras från en omdirigering och ett fel uppstod.

## <a name="initialize-msaljs-1x-apps"></a>Initiera MSAL.js 1. x-appar

Initiera MSAL 1. x-autentisering genom att instansiera en [UserAgentApplication][msal-js-useragentapplication] med ett konfigurations objekt. Den minsta konfigurations egenskapen som krävs är `clientID` av ditt program, som visas som **program-ID** på sidan **Översikt** i appens registrering i Azure Portal.

För autentiseringsmetoder med omdirigerings flöden ([loginRedirect][msal-js-loginredirect] och [acquireTokenRedirect][msal-js-acquiretokenredirect]) i MSAL.js 1.2. x eller tidigare måste du explicit registrera ett återanrop för lyckad eller fel genom `handleRedirectCallback()` metoden. Det krävs explicit att motringningen registreras i MSAL.js 1.2. x och tidigare eftersom omdirigerings flöden inte returnerar löfte som metoder med en popup-upplevelse. Att registrera återanropet är *valfritt* i MSAL.js version 1.3. x och senare.

```javascript
// Configuration object constructed
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111"
    }
}

// Create UserAgentApplication instance
const msalInstance = new UserAgentApplication(msalConfig);

function authCallback(error, response) {
    // Handle redirect response
}

// Register a redirect callback for Success or Error (when using redirect methods)
// **REQUIRED** in MSAL.js 1.2.x and earlier
// **OPTIONAL** in MSAL.js 1.3.x and later
msalInstance.handleRedirectCallback(authCallback);
```

## <a name="single-instance-and-configuration"></a>Enskild instans och konfiguration

Både MSAL.js 1. x och 2. x är utformade för att ha en enda instans och konfiguration av `UserAgentApplication` respektive, `PublicClientApplication` för att representera en enda autentiserings kontext.

Flera instanser av `UserAgentApplication` eller `PublicClientApplication` rekommenderas inte eftersom de orsakar en motstridig cache-poster och beteende i webbläsaren.

## <a name="next-steps"></a>Nästa steg

Det här MSAL.js 2. x-kod exemplet på GitHub demonstrerar instansiering av en [PublicClientApplication][msal-js-publicclientapplication] med ett [konfigurations][msal-js-configuration] objekt:

[Azure-samples/MS-Identity-JavaScript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2)

<!-- LINKS - External -->
[msal-browser]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/
[msal-core]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/
[msal-js-acquiretokenredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/classes/_useragentapplication_.useragentapplication.html#acquiretokenredirect
[msal-js-configuration]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_configuration_.html
[msal-js-handleredirectpromise]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html#handleredirectpromise
[msal-js-loginredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/classes/_useragentapplication_.useragentapplication.html#loginredirect
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_useragentapplication_.html
