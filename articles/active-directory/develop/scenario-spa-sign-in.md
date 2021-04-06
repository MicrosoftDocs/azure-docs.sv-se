---
title: Inloggnings & inloggnings program på en sida
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar ett program med en enda sida (inloggning)
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
ms.openlocfilehash: bdfffbf8d2e416c87dd5abb8f6383c58ad270231
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584371"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Program med en sida: inloggning och utloggning

Lär dig hur du lägger till inloggning till koden för ditt program på en sida.

Innan du kan hämta token för att få åtkomst till API: er i ditt program behöver du en autentiserad användar kontext. Du kan logga in användare i ditt program i MSAL.js på två sätt:

* [Popup-fönster](#sign-in-with-a-pop-up-window)med hjälp av `loginPopup` metoden
* [Omdirigera](#sign-in-with-redirect)med hjälp av `loginRedirect` metoden

Du kan också välja att skicka omfattningarna för de API: er som du vill att användaren ska godkänna vid tidpunkten för inloggning.

> [!NOTE]
> Om programmet redan har åtkomst till en autentiserad användar kontext eller ID-token kan du hoppa över inloggnings steget och hämta token direkt. Mer information finns i [SSO utan MSAL.js inloggning](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Välja mellan en pop-up-eller Redirect-upplevelse

Du kan inte använda båda metoderna pop-up och Redirect i ditt program. Valet mellan en popup-eller omdirigerings upplevelse beror på ditt program flöde:

* Om du inte vill att användarna ska kunna flytta bort från huvud program sidan under autentiseringen rekommenderar vi popup-metoden. Eftersom omdirigeringen av autentisering sker i ett popup-fönster bevaras huvud programmets tillstånd.

* Om användarna har begränsningar i webbläsaren eller principer där popup-fönster har inaktiverats kan du använda metoden Redirect. Använd metoden Redirect med Internet Explorer-webbläsaren, eftersom det finns [kända problem med popup-fönster i Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Logga in med ett popup-fönster


# <a name="javascript-msaljs-2x"></a>[Java Script (MSAL.js 2. x)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success

        // In case multiple accounts exist, you can select
        const currentAccounts = myMsal.getAllAccounts();
    
        if (currentAccounts === null) {
            // no accounts detected
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
        }
    
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="javascript-msaljs-1x"></a>[Java Script (MSAL.js 1. x)](#tab/javascript1)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="angular"></a>[Angular](#tab/angular)

Med MSAL vinkel omslutning kan du skydda specifika vägar i programmet genom att lägga till dem i `MsalGuard` väg definitionen. Det här skydds metoden anropar metoden för att logga in när den vägen nås.

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

Aktivera konfigurations alternativet för en popup-fönster upplevelse `popUp` . Du kan också skicka de omfattningar som kräver medgivande enligt följande:

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["User.ReadWrite"]
        })
    ]
})
```
---

## <a name="sign-in-with-redirect"></a>Logga in med omdirigering

# <a name="javascript-msaljs-2x"></a>[Java Script (MSAL.js 2. x)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

function handleResponse(response) {
    //handle redirect response

    // In case multiple accounts exist, you can select
    const currentAccounts = myMsal.getAllAccounts();

    if (currentAccounts === null) {
        // no accounts detected
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
    }
}

myMsal.handleRedirectPromise(handleResponse);

myMsal.loginRedirect(loginRequest);
```

# <a name="javascript-msaljs-1x"></a>[Java Script (MSAL.js 1. x)](#tab/javascript1)

Omdirigerings metoderna returnerar inte ett löfte på grund av att huvud-appen har flyttats bort. Om du vill bearbeta och komma åt de returnerade tokens registrerar du lyckade och felfria återanrop innan du anropar omdirigerings metoderna.

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

myMsal.handleRedirectCallback(authCallback);

myMsal.loginRedirect(loginRequest);
```

# <a name="angular"></a>[Angular](#tab/angular)

Koden här är samma som beskrivs tidigare i avsnittet om att logga in med ett popup-fönster. Standard flödet är omdirigerat.

---

## <a name="sign-out"></a>Logga ut

MSAL-biblioteket innehåller en `logout` metod som rensar cacheminnet i webbläsarens lagring och skickar en inloggningsbegäran till Azure Active Directory (Azure AD). Efter utloggning omdirigerar biblioteket tillbaka till program start sidan som standard.

Du kan konfigurera den URI som den ska omdirigeras till efter utloggning genom att ange `postLogoutRedirectUri` . Denna URI bör också registreras som utloggnings-URI i program registreringen.

# <a name="javascript-msaljs-2x"></a>[Java Script (MSAL.js 2. x)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByUsername(username)
}

myMsal.logout(logoutRequest);
```

# <a name="javascript-msaljs-1x"></a>[Java Script (MSAL.js 1. x)](#tab/javascript1)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new UserAgentApplication(config);

myMsal.logout();
```

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot och hämta [en token för appen](scenario-spa-acquire-token.md).