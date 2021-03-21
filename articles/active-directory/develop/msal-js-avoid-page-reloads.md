---
title: Undvik sid omläsningar (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du undviker hämtning av sidor vid hämtning och förnyelse av token i tysthet med hjälp av Microsoft Authentication Library för Java Script (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 0c96d161e55261af1bbe04eae6ead1d245158d02
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064835"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Undvik hämtning av sidor vid hämtning och förnyelse av tokens tyst med MSAL.js
Microsoft Authentication Library för Java Script (MSAL.js) använder dolda `iframe` element för att hämta och förnya token tyst i bakgrunden. Azure AD returnerar token tillbaka till den registrerade redirect_uri som anges i Tokenbegäran (som standard är appens rot sida). Eftersom svaret är en 302 resulterar det i HTML-koden som motsvarar `redirect_uri` inläsningen i `iframe` . Vanligt vis är appens `redirect_uri` rot sida och det gör att den kan läsas in på nytt.

I andra fall kan det hända att om du navigerar till appens rot sida kräver autentisering, kan det leda till kapslade `iframe` element eller `X-Frame-Options: deny` fel.

Eftersom MSAL.js inte kan stänga 302 som utfärdats av Azure AD och som krävs för att bearbeta den returnerade token, kan den inte förhindra att `redirect_uri` läsas in i `iframe` .

För att undvika att hela appen laddas om igen eller andra fel som orsakas av detta, följer du dessa lösningar.

## <a name="specify-different-html-for-the-iframe"></a>Ange en annan HTML för iframe

Ställ in `redirect_uri` egenskapen på config på en enkel sida som inte kräver autentisering. Du måste kontrol lera att den matchar den `redirect_uri` registrerade i Azure Portal. Detta påverkar inte användarens inloggnings upplevelse eftersom MSAL sparar start sidan när användaren påbörjar inloggnings processen och dirigerar tillbaka till den exakta platsen när inloggningen har slutförts.

## <a name="initialization-in-your-main-app-file"></a>Initiera i huvud program filen

Om din app är strukturerad så att det finns en central JavaScript-fil som definierar appens initiering, Routning och andra saker kan du villkorligt läsa in dina app-moduler baserat på om appen läses in `iframe` eller inte. Exempel:

I AngularJS: app.js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

I vinkel: app. module. TS

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

MsalComponent:

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>Nästa steg
Lär dig mer om att [skapa ett enda webb program (Spa)](scenario-spa-overview.md) med hjälp av MSAL.js.