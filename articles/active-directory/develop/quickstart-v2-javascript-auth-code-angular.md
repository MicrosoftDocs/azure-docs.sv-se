---
title: 'Snabb start: Logga in användare i Java Script vinkel-och Enkels Ides appar (SPA) med auth-kod och anropa Microsoft Graph | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur du kan logga in användare av personliga konton, arbets konton och skol konton med hjälp av flödes kods flödet och anrops Microsoft Graph med hjälp av ett skript för en enkel sida.
services: active-directory
author: j-mantu
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/14/2021
ms.author: jamesmantu
ms.custom: aaddev, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 1d5fc097be188c820f9be15594f4bb382754b849
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103224936"
---
# <a name="quickstart-sign-in-and-get-an-access-token-in-an-angular-spa-using-the-auth-code-flow"></a>Snabb start: Logga in och hämta en åtkomsttoken i ett särskilt skydd med auth Code Flow

I den här snabb starten laddar du ned och kör ett kod exempel som visar hur en JavaScript-form (SPA) kan logga in användare och anropa Microsoft Graph med hjälp av kod flödet för auktorisering. Kod exemplet visar hur du hämtar en åtkomsttoken för att anropa Microsoft Graph API eller något webb-API. 

Se [hur exemplet fungerar](#how-the-sample-works) för en illustration.

I den här snabb starten används MSAL-vinkeln v2 med Authorization Code Flow. För en liknande snabb start som använder MSAL vinkel 1. x med det implicita flödet, se [snabb start: Logga in användare i Java Script-appar med en sida](./quickstart-v2-angular.md).

> [!IMPORTANT]
> MSAL-vinkel v2 [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en Azure-prenumeration kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) eller en annan kod redigerare

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrera och ladda ned snabbstartsprogrammet
> Starta snabb starts programmet med något av följande alternativ.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1 (Express): registrera och konfigurera appen automatiskt och ladda ned kod exemplet
>
> 1. Gå till snabb starts upplevelsen för <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Appregistreringar</a> .
> 1. Ange ett namn för ditt program.
> 1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
> 1. Välj **Register** (Registrera).
> 1. Gå till snabb starts fönstret och följ instruktionerna för att ladda ned och konfigurera det nya programmet automatiskt.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2 (manuell): registrera och konfigurera ditt program och kod exempel manuellt
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
>
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
> 1. Sök efter och välj **Azure Active Directory**.
> 1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
> 1. Ange ett **namn** för ditt program. Användare av appen kan se det här namnet och du kan ändra det senare.
> 1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
> 1. Välj **Register** (Registrera). På sidan **Översikt över** appar noterar du **programmets (klient) ID-** värde för senare användning.
> 1. Under **Hantera** väljer du **autentisering**.
> 1. Under **plattforms konfiguration** väljer du **Lägg till en plattform**. I fönstret som öppnas väljer du **program med en enda sida**.
> 1. Ange värdet för **omdirigerings-URI** `http://localhost:4200/` . Det här är standard porten NodeJS lyssnar på den lokala datorn. Vi kommer att returnera autentiserings svaret till denna URI när användaren har autentiserat användaren. 
> 1. Välj **Konfigurera** för att tillämpa ändringarna.
> 1. Expandera **program med en enda sida** under **plattforms konfiguration** .
> 1. Bekräfta att  ![ ](media/quickstart-v2-javascript/green-check.png) OMdirigerings-URI: n är berättigad till Flow-flödet med PKCE under beviljande typer som redan har kon figurer ATS.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
> Om du vill att kod exemplet i den här snabb starten ska fungera lägger du till en **omdirigerings-URI** för `http://localhost:4200/` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-javascript/green-check.png) Programmet konfigureras med de här attributen.

 #### <a name="step-2-download-the-project"></a>Steg 2: Ladda ned projektet

> [!div renderon="docs"]
> Om du vill köra projektet med en webb server med hjälp av Node.js [laddar du ned de centrala projektfilerna](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Kör projektet med en webb server genom att använda Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Steg 3: Konfigurera din JavaScript-app
>
> I mappen *src* öppnar du mappen *app* och öppnar sedan filen *app. module. TS* och uppdaterar `clientID` `authority` värdena,, och `redirectUri` i `auth` objektet.
>
> ```javascript
> // MSAL instance to be passed to msal-angular
> export function MSALInstanceFactory(): IPublicClientApplication {
>   return new PublicClientApplication({
>     auth: {
>       clientId: 'Enter_the_Application_Id_Here',
>       authority: 'Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here',
>       redirectUri: 'Enter_the_Redirect_Uri_Here'
>     },
>     cache: {
>       cacheLocation: BrowserCacheLocation.LocalStorage,
>       storeAuthStateInCookie: isIE, // set to true for IE 11
>     },
>   });
> }
> ```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Ändra värdena i `auth` avsnittet enligt beskrivningen här:
>
> - `Enter_the_Application_Id_Here` är **program-ID: t (Client)** för det program som du har registrerat.
>
>    Du hittar värdet för **program-ID: t** genom att gå till sidan med registrerings **översikten** för appen i Azure Portal.
> - `Enter_the_Cloud_Instance_Id_Here` är instansen av Azure-molnet. För huvud-eller globala Azure-molnet anger du `https://login.microsoftonline.com/` . För **nationella** moln (till exempel Kina), se [nationella moln](authentication-national-cloud.md).
> - `Enter_the_Tenant_info_here` är inställt på något av följande:
>   - Om ditt program har stöd *för konton i den här organisations katalogen* ersätter du värdet med **klient-ID** eller **klient namn**. Till exempel `contoso.microsoft.com`.
>
>    Om du vill hitta värdet för **katalog-ID: t** går du till sidan med registrerings **översikten** för appen i Azure Portal.
>   - Om ditt program har stöd *för konton i en organisations katalog* ersätter du värdet med `organizations` .
>   - Om ditt program har stöd *för konton i en organisations katalog och personliga Microsoft-konton* ersätter du värdet med `common` . **I den här snabb** starten använder du `common` .
>   - Om du bara vill begränsa stödet till *personliga Microsoft-konton* ersätter du värdet med `consumers` .
>
>    Du hittar värdet för **konto typer som stöds** genom att gå till sidan med registrerings **översikten** för appen i Azure Portal.
> - `Enter_the_Redirect_Uri_Here` är `http://localhost:4200/`.
>
> `authority`Värdet i din *app. module. TS* bör likna följande om du använder det huvudsakliga Azure-molnet (Globalt):
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Steg 3: appen har kon figurer ATS och är redo att köras
> Vi har konfigurerat ditt projekt med värdena för appens egenskaper.

> [!div renderon="docs"]
>
> Rulla ned i samma fil och uppdatera `graphMeEndpoint` . 
> - Ersätt strängen `Enter_the_Graph_Endpoint_Herev1.0/me` med `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` är slut punkten som API-anrop görs mot. För Main (global) Microsoft Graph API-tjänsten anger `https://graph.microsoft.com/` du (ta med avslutande snedstreck). Mer information finns i [dokumentationen](/graph/deployments).
>
>
> ```javascript
> export function MSALInterceptorConfigFactory(): MsalInterceptorConfiguration {
>   const protectedResourceMap = new Map<string, Array<string>>();
>   protectedResourceMap.set('Enter_the_Graph_Endpoint_Herev1.0/me', ['user.read']);
>
>   return {
>     interactionType: InteractionType.Redirect,
>     protectedResourceMap
>   };
> }
> ```
>
>
 #### <a name="step-4-run-the-project"></a>Steg 4: kör projektet

Kör projektet med en webb server genom att använda Node.js:

1. Starta-servern genom att köra följande kommandon inifrån projekt katalogen:
    ```console
    npm install
    npm start
    ```
1. Gå till `http://localhost:4200/`.

1. Välj **Logga in** för att starta inloggnings processen och anropa sedan Microsoft Graph-API: et.

    Första gången du loggar in uppmanas du att ange ditt medgivande så att programmet kan komma åt din profil och logga in dig. När du har loggat in klickar du på knappen **profil** för att visa din användar information på sidan.

## <a name="more-information"></a>Mer information

### <a name="how-the-sample-works"></a>Så här fungerar exemplet

![Diagram som visar auktoriserings kod flödet för ett program på en enda sida.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

MSAL.jss biblioteket loggar in användare och begär de token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform. 

Om du har Node.js installerat kan du hämta den senaste versionen med hjälp av Node.js Package Manager (NPM):

```console
npm install @azure/msal-browser @azure/msal-angular@2
```

## <a name="next-steps"></a>Nästa steg

En detaljerad steg-för-steg-guide om hur du skapar ett auth Code Flow-program med vanilj java script finns i följande självstudie:

> [!div class="nextstepaction"]
> [Självstudie för att logga in och anropa MS Graph](./tutorial-v2-javascript-auth-code.md)