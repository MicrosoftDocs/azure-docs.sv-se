---
title: 'Självstudie: skapa en vinkel-app som använder Microsoft Identity Platform för autentisering | Azure'
titleSuffix: Microsoft identity platform
description: 'I den här självstudien skapar du en vinkel med en enkel sida som använder Microsoft Identity Platform för att logga in användare och få en åtkomsttoken för att anropa Microsoft Graph-API: et för deras räkning.'
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 105353598a2af60c407bacf02b4527b2de84e450
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756161"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>Självstudie: Logga in användare och anropa Microsoft Graph-API: et från ett särskilt program på en sida

I den här självstudien skapar du en antecknings bild (SPA) i ett enda fönster som loggar in användare och anropar Microsoft Graph-API: et.

I de här självstudierna har du

> [!div class="checklist"]
> * Skapa ett vinkel projekt med `npm`
> * Registrera programmet i Azure Portal
> * Lägg till kod som stöd för användar inloggning och utloggning
> * Lägg till kod för anrop Microsoft Graph API
> * Testa appen

## <a name="prerequisites"></a>Krav

* [Node.js](https://nodejs.org/en/download/) för att köra en lokal webb server.
* [Visual Studio Code](https://code.visualstudio.com/download) eller annan redigerare för att ändra projektfiler.

## <a name="how-the-sample-app-works"></a>Så här fungerar exempel appen

![Diagram som visar hur exempel appen som genereras i den här självstudien fungerar](./media/tutorial-v2-angular/diagram-auth-flow-spa-angular.svg)

Exempel programmet som skapas i den här självstudien gör det möjligt för ett vinkel SPA att fråga Microsoft Graph-API eller ett webb-API som accepterar token som utfärdats av Microsoft Identity Platform. Den använder Microsoft Authentication Library (MSAL) för vinkel, en omslutning av kärn MSAL.jss biblioteket. MSAL-vinkeln gör det möjligt för företag att kunna autentisera företags användare genom att använda Azure Active Directory (Azure AD) och även användare med Microsoft-konton och sociala identiteter som Facebook, Google och LinkedIn. Biblioteket gör det också möjligt för program att få åtkomst till Microsofts moln tjänster och Microsoft Graph.

I det här scenariot begärs en åtkomsttoken efter att användaren loggar in och läggs till i HTTP-begäranden via Authorization-huvudet. Hämtning av token och förnyelse hanteras av MSAL.

### <a name="libraries"></a>Bibliotek

I den här självstudien används följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library för Java Script, vinkel beslutning|

Du hittar käll koden för MSAL.js bibliotek i [AzureAD/Microsoft-Authentication-Library-for-JS](https://github.com/AzureAD/microsoft-authentication-library-for-js) -lagringsplatsen på GitHub.

## <a name="create-your-project"></a>Skapa projektet

Generera ett nytt vinkel program med hjälp av följande NPM-kommandon:

```bash
npm install -g @angular/cli@8                    # Install the Angular CLI
ng new my-application --routing=true --style=css # Generate a new Angular app
cd my-application                                # Change to the app directory
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>Registrera ditt program

Följ [anvisningarna för att registrera ett program på en sida](./scenario-spa-app-registration.md) i Azure Portal.

På sidan **Översikt** över appen i registreringen noterar du **programmets (klient) ID-** värde för senare användning.

Registrera ditt **omdirigerings-URI** -värde som **http://localhost:4200/** och aktivera implicita beviljande inställningar.

## <a name="configure-the-application"></a>Konfigurera programmet

1. I mappen *src/app* redigerar du *app. module. TS* och lägger till `MSALModule` både till och `imports` `isIE` konstanten:

    ```javascript
    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;
    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          },
        }, {
          popUp: !isIE,
          consentScopes: [
            'user.read',
            'openid',
            'profile',
          ],
          unprotectedResources: [],
          protectedResourceMap: [
            ['https://graph.microsoft.com/v1.0/me', ['user.read']]
          ],
          extraQueryParameters: {}
        })
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    ```

    Ersätt följande värden:

    |Värdenamn|Om|
    |---------|---------|
    |Enter_the_Application_Id_Here|På sidan **Översikt** i program registreringen är det här ditt **program (klient) ID-** värde. |
    |Enter_the_Cloud_Instance_Id_Here|Detta är instansen av Azure-molnet. För huvud-eller globala Azure-molnet anger du **https://login.microsoftonline.com** . För nationella moln (till exempel Kina), se [nationella moln](./authentication-national-cloud.md).|
    |Enter_the_Tenant_Info_Here| Ange ett av följande alternativ: om programmet har stöd *för konton i den här organisations katalogen* ersätter du det här värdet med katalogen (klient) ID: t eller klient namnet (till exempel **contoso.Microsoft.com**). Om ditt program har stöd *för konton i en organisations katalog* ersätter du värdet med **organisationer**. Om ditt program har stöd *för konton i en organisations katalog och personliga Microsoft-konton* ersätter du värdet med **vanligt**. Om du bara vill begränsa stödet till *personliga Microsoft-konton* ersätter du värdet med **konsumenter**. |
    |Enter_the_Redirect_Uri_Here|Ersätt med **http://localhost:4200** .|

    Mer information om tillgängliga konfigurerbara alternativ finns i [initiera klient program](msal-js-initializing-client-applications.md).

2. Lägg till följande import uttryck längst upp i samma fil:

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. Lägg till följande import uttryck överst i `src/app/app.component.ts` :

    ```javascript
    import { MsalService, BroadcastService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
## <a name="sign-in-a-user"></a>Logga in en användare

Lägg till följande kod för `AppComponent` att logga in en användare:

```javascript
export class AppComponent implements OnInit {
    constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

    ngOnInit() { }

    login() {
        const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

        if (isIE) {
          this.authService.loginRedirect({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        } else {
          this.authService.loginPopup({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        }
    }
}
```

> [!TIP]
> Vi rekommenderar att du använder `loginRedirect` för Internet Explorer-användare.

## <a name="acquire-a-token"></a>Hämta en token

### <a name="angular-interceptor"></a>Vinkel spärren

MSAL-vinkel ger en `Interceptor` klass som automatiskt hämtar token för utgående begär Anden som använder sig av den vinkel bara `http` klienten till kända skyddade resurser.

Börja med att inkludera `Interceptor` klassen som en provider för ditt program:

```javascript
import { MsalInterceptor, MsalModule } from "@azure/msal-angular";
import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http";

@NgModule({
    // ...
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ]
}
```

Sedan anger du en karta över skyddade resurser till `MsalModule.forRoot()` som `protectedResourceMap` och inkluderar de här omfattningarna i `consentScopes` . De URL: er som du anger i `protectedResourceMap` samlingen är Skift läges känsliga.

```javascript
@NgModule({
  // ...
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_here', // This is your client ID
        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
      },
      cache: {
        cacheLocation: 'localStorage',
        storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
      },
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      unprotectedResources: [],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ],
      extraQueryParameters: {}
    })
  ],
});
```

Slutligen hämtar du en användares profil med en HTTP-begäran:

```JavaScript
const graphMeEndpoint = "https://graph.microsoft.com/v1.0/me";

getProfile() {
  this.http.get(graphMeEndpoint).toPromise()
    .then(profile => {
      this.profile = profile;
    });
}
```

### <a name="acquiretokensilent-acquiretokenpopup-acquiretokenredirect"></a>acquireTokenSilent, acquireTokenPopup, acquireTokenRedirect
MSAL använder tre metoder för att hämta tokens: `acquireTokenRedirect` , `acquireTokenPopup` och `acquireTokenSilent` . Vi rekommenderar dock att du använder `MsalInterceptor` -klassen i stället för vinkel-appar, som du ser i föregående avsnitt.

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

`acquireTokenSilent`Metoden hanterar hämtning av token och förnyelse utan användar interaktion. När `loginRedirect` metoden eller körs `loginPopup` för första gången `acquireTokenSilent` används ofta för att hämta tokens som används för att komma åt skyddade resurser i senare anrop. Anrop för att begära eller förnya token görs tyst.

```javascript
const requestObj = {
    scopes: ["user.read"]
};

this.authService.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

I den koden `scopes` innehåller omfattningar som begärs att returneras i åtkomsttoken för API: et.

Ett exempel:

* `["user.read"]` för Microsoft Graph
* `["<Application ID URL>/scope"]` för anpassade webb-API: er (dvs. `api://<Application ID>/access_as_user` )

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Ibland behöver du användaren för att kunna interagera med Microsoft Identity Platform. Ett exempel:

* Användare kan behöva ange sina autentiseringsuppgifter på grund av att deras lösen ord har upphört att gälla.
* Ditt program begär åtkomst till ytterligare resurs omfattningar som användaren behöver godkänna.
* Tvåfaktorautentisering krävs.

Rekommenderat mönster för de flesta program är att anropa `acquireTokenSilent` först, sedan fånga undantagen och sedan anropa `acquireTokenPopup` (eller `acquireTokenRedirect` ) för att starta en interaktiv begäran.

Anropa `acquireTokenPopup` resultat i ett popup-fönster för inloggning. Alternativt `acquireTokenRedirect` omdirigerar användare till Microsoft Identity Platform. I det fönstret måste användare bekräfta sina autentiseringsuppgifter, ge medgivande till den begärda resursen eller utföra en fullständig tvåfaktorautentisering.

```javascript
  const requestObj = {
      scopes: ["user.read"]
  };

  this.authService.acquireTokenPopup(requestObj).then(function (tokenResponse) {
      // Callback code here
      console.log(tokenResponse.accessToken);
  }).catch(function (error) {
      console.log(error);
  });
```

> [!NOTE]
> I den här snabb starten används `loginRedirect` `acquireTokenRedirect` metoderna och med Microsoft Internet Explorer på grund av ett [känt problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) som rör hanteringen av popup-fönster i Internet Explorer.

## <a name="log-out"></a>Logga ut

Lägg till följande kod för att logga ut en användare:

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>Lägga till användargränssnitt
Ett exempel på hur du lägger till användar gränssnittet med hjälp av komponent biblioteket för ett vinkel material finns i [exempel programmet](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular).

## <a name="test-your-code"></a>Testa koden

1.  Starta webb servern för att lyssna på porten genom att köra följande kommandon i kommando tolken från programmappen:

    ```bash
    npm install
    npm start
    ```
1. I webbläsaren anger **http://localhost:4200** eller **http://localhost:{port}** , där *port* är den port som webb servern lyssnar på.


### <a name="provide-consent-for-application-access"></a>Ge tillstånd för program åtkomst

Första gången du börjar logga in till ditt program uppmanas du att ge den åtkomst till din profil och att du ska kunna logga in dig:

![Fönstret "behörigheter begärd"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

## <a name="add-scopes-and-delegated-permissions"></a>Lägg till omfattningar och delegerade behörigheter

Microsoft Graph-API: t kräver att *User. Read* -omfånget läser en användar profil. Som standard läggs det här omfånget automatiskt till i varje program som är registrerat på registrerings portalen. Andra API: er för Microsoft Graph, samt anpassade API: er för backend-servern, kan kräva ytterligare omfång. Till exempel kräver Microsoft Graph-API: n *kalendrar. Läs* omfattning för att lista användarens kalendrar.

Om du vill komma åt användarens kalendrar i ett programs kontext lägger du till *kalendrarna. Läs* behörighet för program registrerings informationen. Lägg sedan till *kalendrarna. Läs* omfång till `acquireTokenSilent` anropet.

>[!NOTE]
>Användaren kan tillfrågas om ytterligare medgivanden när du ökar antalet omfång.

Om ett Server dels-API inte kräver ett omfång (rekommenderas inte), kan du använda *clientId* som omfånget i anropen för att hämta tokens.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Gå djupare i utvecklingen av en Enkels Ides applikation (SPA) på Microsofts identitets plattform i vår artikel serie för flera delar.

> [!div class="nextstepaction"]
> [Scenario: ett program med en sida](scenario-spa-overview.md)
