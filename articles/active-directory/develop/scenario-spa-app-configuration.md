---
title: Konfigurera en app med en enda sida | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar ett program med en enda sida (appens kod konfiguration)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 5cbb576a7fcfb2daf492a149130aa7c99fe10ac5
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753606"
---
# <a name="single-page-application-code-configuration"></a>Program med en sida: kod konfiguration

Lär dig hur du konfigurerar koden för en Enkels Ides applikation (SPA).

## <a name="msal-libraries-for-spas-and-supported-authentication-flows"></a>MSAL-bibliotek för SPAs och stödda autentiserings flöden

Microsoft Identity Platform tillhandahåller följande Microsoft-autentiseringspaket för Java Script (MSAL.js) som stöd för implicit flödes-och auktoriseringskod med PKCE med hjälp av bransch rekommenderade säkerhets metoder:

| MSAL-bibliotek | Flöden | Beskrivning |
|--------------|------|-------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (2. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) | Authorization Code Flow (PKCE) | Oformaterat JavaScript-bibliotek för användning i alla webb program på klient sidan som bygger på JavaScript-eller SPA-ramverk, till exempel vinkel, Vue.js och React.js. |
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (1. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) | Implicit flöde | Oformaterat JavaScript-bibliotek för användning i alla webb program på klient sidan som bygger på JavaScript-eller SPA-ramverk, till exempel vinkel, Vue.js och React.js. |
| ![MSAL-vinkel](media/sample-v2-code/logo_angular.png) <br/> [MSAL-vinkel](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Implicit flöde | Omslutningen av kärn MSAL.jss biblioteket för att förenkla användningen av appar på en sida som är byggda genom ram-ramverket. |

## <a name="application-code-configuration"></a>Program kod konfiguration

I ett MSAL-bibliotek skickas programmets registrerings information som konfiguration under biblioteks initieringen.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

Mer information om konfigurerbara alternativ finns i avsnittet [om att initiera program med MSAL.js](msal-js-initializing-client-applications.md).

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot, [Logga in och logga ut](scenario-spa-sign-in.md).
