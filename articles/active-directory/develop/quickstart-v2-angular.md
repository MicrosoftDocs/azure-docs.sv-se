---
title: 'Snabb start: Logga in användare i en vinkel på en enda sida-appar – Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur en vinkel-app kan anropa ett API som kräver åtkomsttoken som utfärdats av Microsoft Identity Platform.
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
ms.openlocfilehash: 95747bb68a904212e7e075fc29b2497932d66e5f
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103863"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Snabb start: Logga in användare och hämta en åtkomsttoken i ett angulande Enkels Ides program

I den här snabb starten laddar du ned och kör ett kod exempel som visar hur ett särskilt SPA-program (Single-Side Application) kan logga in användare och anropa Microsoft Graph. Kod exemplet visar hur du hämtar en åtkomsttoken för att anropa Microsoft Graph API eller något webb-API.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.js](https://nodejs.org/en/download/).
* [Visual Studio Code](https://code.visualstudio.com/download) för att redigera projektfiler eller [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) för att köra projektet.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>Registrera och hämta snabb starts appen
> Starta snabb starts programmet med något av följande alternativ.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Alternativ 1 (Express): registrera och konfigurera appen automatiskt och hämta sedan kod exemplet
>
> 1. Gå till snabb starts upplevelsen för <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Appregistreringar</a> .
> 1. Ange ett namn för programmet och välj sedan **Registrera**.
> 1. Gå till snabb starts fönstret och Visa vinkel snabb starten. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Alternativ 2 (manuell): registrera och konfigurera programmet och kod exemplet manuellt
>
> #### <a name="step-1-register-the-application"></a>Steg 1: registrera programmet
>
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
> 1. Följ anvisningarna för att [Registrera ett program på en sida](./scenario-spa-app-registration.md) i Azure Portal.
> 1. Lägg till en ny plattform i fönstret **verifiering** i appens registrering och registrera omdirigerings-URI: n: `http://localhost:4200/` .
> 1. I den här snabb starten används det [implicita tilldelnings flödet](v2-oauth2-implicit-grant-flow.md). I avsnittet **implicit beviljande och hybrid flöden** väljer du **ID-tokens** och **åtkomsttoken**. ID-token och åtkomsttoken krävs eftersom den här appen loggar in användare och anropar ett API.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Steg 1: konfigurera programmet i Azure Portal
> För att kod exemplet i den här snabb starten ska fungera måste du lägga till en omdirigerings-URI som **http://localhost:4200/** och aktivera **implicit beviljande**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-javascript/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-code-sample"></a>Steg 2: Hämta kod exemplet
>[!div renderon="docs"]
>Om du vill köra projektet med en webb server med hjälp av Node.js, [klona exempel lagrings platsen](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) eller [Ladda ned de grundläggande projektfilerna](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Öppna filerna med hjälp av ett redigerings program, till exempel Visual Studio Code.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Steg 3: Konfigurera JavaScript-appen
>
> I mappen *src/app* redigerar du *app. module. TS* och anger `clientId` `authority` värdena och under `MsalModule.forRoot` .
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
> Ersätt följande värden:
>
>|Värdenamn|Description|
>|---------|---------|
>|Enter_the_Application_Id_Here|På sidan **Översikt** i program registreringen är det här ditt **program (klient) ID-** värde. |
>|Enter_the_Cloud_Instance_Id_Here|Detta är instansen av Azure-molnet. För huvud-eller globala Azure-molnet anger du **https://login.microsoftonline.com** . För nationella moln (till exempel Kina), se [nationella moln](./authentication-national-cloud.md).|
>|Enter_the_Tenant_Info_Here| Ange ett av följande alternativ: om programmet har stöd *för konton i den här organisations katalogen* ersätter du det här värdet med katalogen (klient) ID: t eller klient namnet (till exempel **contoso.Microsoft.com**). Om ditt program har stöd *för konton i en organisations katalog* ersätter du värdet med **organisationer**. Om ditt program har stöd *för konton i en organisations katalog och personliga Microsoft-konton* ersätter du värdet med **vanligt**. Om du bara vill begränsa stödet till *personliga Microsoft-konton* ersätter du värdet med **konsumenter**. |
>|Enter_the_Redirect_Uri_Here|Ersätt med **http://localhost:4200** .|
>|cacheLocation  | Valfritt Ange webb läsar lagring för autentiseringens tillstånd. Standardvärdet är **SessionStorage**.   |
>|storeAuthStateInCookie  | Valfritt Identifiera biblioteket som lagrar status för autentiseringsbegäran. Detta tillstånd krävs för att validera autentiserings flöden i webbläsarens cookies. Den här cookien är inställd för Internet Explorer och Edge för att hantera dessa två webbläsare. Mer information finns i [kända problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
> > [!TIP]
> > För att hitta värdena för **program-ID (klient)**, **katalog-ID (klient)** och **Kontotyper som stöds** går du till appens **översiktssida** i Azure-portalen.

Mer information om tillgängliga konfigurerbara alternativ finns i [initiera klient program](msal-js-initializing-client-applications.md).

Du hittar käll koden för MSAL.js bibliotek i [AzureAD/Microsoft-Authentication-Library-for-JS](https://github.com/AzureAD/microsoft-authentication-library-for-js) -lagringsplatsen på GitHub.

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>Steg 3: kör projektet

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Steg 4: kör projektet

Om du använder Node.js:

1. Starta-servern genom att köra följande kommandon från projekt katalogen:

   ```console
   npm install
   npm start
   ```

1. Bläddra till **http://localhost:4200/**.
1. Välj **inloggning**.
1. Välj **profil** för att anropa Microsoft Graph.

När webbläsaren har läst in programmet väljer du **Logga in**. Första gången du börjar logga in uppmanas du att ange ditt medgivande för att ge programmet åtkomst till din profil och logga in dig. När du har loggat in väljer du **profil** så visas din användar profil information på sidan.

## <a name="how-the-sample-works"></a>Så här fungerar exemplet

![Diagram som visar hur exempel programmet i den här snabb starten fungerar](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)


## <a name="next-steps"></a>Nästa steg

Lär dig sedan att logga in en användare och hämta tokens i den vinkel bara självstudien:

> [!div class="nextstepaction"]
> [Vinkel kurs](./tutorial-v2-angular.md)