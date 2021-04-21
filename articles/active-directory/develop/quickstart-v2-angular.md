---
title: 'Snabbstart: Logga in användare i ensidesappar i Angular – Azure'
titleSuffix: Microsoft identity platform
description: I den här snabbstarten lär du dig hur en Angular-app kan anropa ett API som kräver åtkomsttoken som utfärdats av Microsoft Identity Platform.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: d53ce97c4af302801098d9abaa633ced98c93f3a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814036"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Snabbstart: Logga in användare och hämta en åtkomsttoken i ett ensidesapplikation för Angular

I den här snabbstarten laddar du ned och kör ett kodexempel som visar hur en Engular-ensidesapplikation (SPA) kan logga in användare och anropa Microsoft Graph. Kodexe exemplet visar hur du hämtar en åtkomsttoken för att anropa Microsoft Graph API eller ett webb-API.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. [Skapa en utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/).
* [Visual Studio Kod](https://code.visualstudio.com/download) för att redigera projektfiler eller [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) för att köra projektet.

> [!div renderon="docs"]
>
> ## <a name="register-and-download-the-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
>
> Starta snabbstartsappen med något av följande alternativ.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Alternativ 1 (express): Registrera och konfigurera appen automatiskt och ladda sedan ned kodexe exemplet
>
> 1. Gå till <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure Portal – Appregistreringar</a> snabbstart.
> 1. Ange ett namn för programmet och välj sedan **Registrera**.
> 1. Leta reda på Angular-snabbstarten i snabbstartsfönstret. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Alternativ 2 (manuell): Registrera och konfigurera programmet och kodexe exemplet manuellt
>
> #### <a name="step-1-register-the-application"></a>Steg 1: Registrera programmet
>
> 1. Följ anvisningarna för [att registrera ett ensidesprogram](./scenario-spa-app-registration.md) i Azure Portal.
> 1. Lägg till en ny plattform i **fönstret Autentisering** i appregistreringen och registrera omdirigerings-URI:en: `http://localhost:4200/` .
> 1. Den här snabbstarten använder [det implicita beviljandeflödet](v2-oauth2-implicit-grant-flow.md). I avsnittet **Implicit beviljande och hybridflöden** väljer du **ID-token och** **Åtkomsttoken.** ID-token och åtkomsttoken krävs eftersom den här appen loggar in användare och anropar ett API.

> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Steg 1: Konfigurera programmet i Azure Portal
>
> För att kodexe exemplet i den här snabbstarten ska fungera måste du lägga till en omdirigerings-URI `http://localhost:4200/` till och aktivera Implicit **beviljande**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-javascript/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-code-sample"></a>Steg 2: Ladda ned kodexe exemplet
>[!div renderon="docs"]
>Om du vill köra projektet med en webbserver med [](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) hjälp Node.js klonar du exempeldatabasen [eller laddar ned kärnprojektfilerna](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Öppna filerna i ett redigeringsprogram, till exempel Visual Studio Code.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Ladda ned kodexe exemplet](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Steg 3: Konfigurera JavaScript-appen
>
> I mappen *src/app* redigerar du *app.module.ts och* anger `clientId` värdena och under `authority` `MsalModule.forRoot` .
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here

> [!div renderon="docs"]
>
> Ersätt dessa värden:
>
>|Värdenamn|Description|
>|---------|---------|
>|Enter_the_Application_Id_Here|På sidan **Översikt** i programregistreringen är det här värdet **för program-ID (klient).** |
>|Enter_the_Cloud_Instance_Id_Here|Det här är Azure-molninstansen. För det huvudsakliga eller globala Azure-molnet anger du `https://login.microsoftonline.com` . Information om nationella moln (till exempel Kina) finns [i Nationella moln.](./authentication-national-cloud.md)|
>|Enter_the_Tenant_Info_Here| Ange till något av följande alternativ: Om ditt program stöder konton i den här organisationskatalogen ersätter du det här värdet med katalog-ID:t (klientorganisations-ID:t) eller klientorganisationens namn (till exempel `contoso.microsoft.com` ). Om ditt program stöder *konton i en organisationskatalog ersätter* du det här värdet med `organizations` . Om ditt program stöder konton *i valfri organisationskatalog och personliga Microsoft-konton* ersätter du det här värdet med `common` . Om du endast vill begränsa *stödet till personliga Microsoft-konton* ersätter du det här värdet med `consumers` . |
>|Enter_the_Redirect_Uri_Here|Ersätt med `http://localhost:4200` .|
>|cacheLocation  | (Valfritt) Ange webbläsarlagring för autentiseringstillståndet. Standardvärdet är `sessionStorage`.   |
>|storeAuthStateInCookie  | (Valfritt) Identifiera biblioteket som lagrar tillståndet för autentiseringsbegäran. Det här tillståndet krävs för att verifiera autentiseringsflödena i webbläsarens cookies. Den här cookien anges för Internet Explorer och Microsoft Edge för att hantera dessa två webbläsare. Mer information finns i kända [problem.](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues) |
>
> För att hitta värdena för **program-ID (klient)**, **katalog-ID (klient)** och **Kontotyper som stöds** går du till appens **översiktssida** i Azure-portalen.

> Mer information om tillgängliga konfigurerbara alternativ finns i [Initiera klientprogram.](msal-js-initializing-client-applications.md)

> Du hittar källkoden för MSAL.js biblioteket på [lagringsplatsen AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) på GitHub.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Steg 3: Din app är konfigurerad och redo att köras
> Vi har konfigurerat ditt projekt med värden för appens egenskaper.

> [!div renderon="docs"]
>
> Rulla nedåt i samma fil och uppdatera `graphMeEndpoint` . 
> - Ersätt strängen `Enter_the_Graph_Endpoint_Herev1.0/me` med `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` är den slutpunkt som API-anrop görs mot. För den huvudsakliga (globala) Microsoft Graph `https://graph.microsoft.com/` API-tjänsten anger du (inkludera avslutande snedstreck). Mer information finns i [dokumentationen](https://docs.microsoft.com/graph/deployments).
>
>
> ```javascript
>      protectedResourceMap: [
>        ['Enter_the_Graph_Endpoint_Herev1.0/me', ['user.read']]
>      ],
> ```
>
>

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Steg 4: Kör projektet

Om du använder Node.js:

1. Starta servern genom att köra följande kommandon från projektkatalogen:

   ```console
   npm install
   npm start
   ```

1. Gå till `http://localhost:4200/`.
1. Välj **Logga in.** Första gången du loggar in uppmanas du att tillåta programmet att komma åt din profil och logga in dig automatiskt.
1. Välj **Profil för** att anropa Microsoft Graph. Din användarprofilinformation visas på sidan.

## <a name="how-the-sample-works"></a>Så här fungerar exemplet

![Diagram som visar hur exempelappen i den här snabbstarten fungerar.](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du loggar in en användare och hämtar token i Självstudie om Angular:

> [!div class="nextstepaction"]
> [Självstudie om Angular](./tutorial-v2-angular.md)
