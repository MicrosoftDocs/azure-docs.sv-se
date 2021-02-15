---
title: Migrera JavaScript-app med en enda sida från implicit beviljande till flöde för auktoriseringskod | Azure
titleSuffix: Microsoft identity platform
description: Så här uppdaterar du en JavaScript-SPA med MSAL.js 1. x och det implicita beviljade flödet till MSAL.js 2. x och koden för auktoriseringskod med PKCE-och CORS-stöd.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 3c11334fe1b4d77be6e64febfc1d3de6efa302c3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365948"
---
# <a name="migrate-a-javascript-single-page-app-from-implicit-grant-to-auth-code-flow"></a>Migrera en JavaScript-app med en sida från implicit beviljande till auth Code Flow

Microsoft Authentication Library för Java Script (MSAL.js) v 2.0 ger stöd för auktoriseringskod-flödet med PKCE och CORS till program med en enda sida på Microsoft Identity Platform. Följ stegen i avsnitten nedan för att migrera ditt MSAL.js 1. x-program med hjälp av implicit beviljande till MSAL.js 2.0 + (hädanefter *2. x*) och flödet för auth-koden.

MSAL.js 2. x ökar med MSAL.js 1. x genom att stödja auktoriseringskod i webbläsaren i stället för det implicita tilldelnings flödet. MSAL.js 2. x stöder **inte** det implicita flödet.

## <a name="migration-steps"></a>Migreringsanvisningar

Om du vill uppdatera ditt program till MSAL.js 2. x och auth Code Flow finns det tre primära steg:

1. Ändra dina omdirigerings-URI: er för [program registrering](#switch-redirect-uris-to-spa-platform) från **webb** plattformen till **en program plattform med enkel sida** .
1. Uppdatera [koden](#switch-redirect-uris-to-spa-platform) från MSAL.js 1. x till **2. x**.
1. Inaktivera det [implicita bidraget](#disable-implicit-grant-settings) i din app-registrering när alla program som delar registreringen har uppdaterats till MSAL.js 2. x och kod flödet för autentisering.

I följande avsnitt beskrivs varje steg i ytterligare information.

## <a name="switch-redirect-uris-to-spa-platform"></a>Ändra omdirigerings-URI till SPA-plattform

Om du vill fortsätta att använda din befintliga App-registrering för dina program använder du Azure Portal för att uppdatera registreringens omdirigerings-URI: er till SPA-plattformen. Detta gör att auktoriseringskod kan flöda med PKCE och CORS-stöd för appar som använder registreringen (du måste fortfarande uppdatera programmets kod till MSAL.js v2. x).

Följ de här stegen för app-registreringar som är konfigurerade med omdirigerings-URI: er för **webb** plattform:

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> och välj **Azure Active Directory** klient.
1. I **Appregistreringar** väljer du ditt program och sedan **autentisering**.
1. I panelen **webb** plattform under **omdirigerings-URI** väljer du varnings banderollen som anger att du bör migrera dina URI: er.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-01-implicit-warning-banner.png" alt-text="Varnings banderoll för implicit flöde på webbappens panel i Azure Portal":::
1. Välj *bara* de omdirigerings-URI: er vars program kommer att använda MSAL.js 2. x och välj sedan **Konfigurera**.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-02-select-redirect-uri.png" alt-text="Välj rutan omdirigerings-URI i fönstret SPA i Azure Portal":::

De här omdirigerings-URI: erna bör nu visas på en plattforms panel med **en enda sida** , som visar att CORS-stödet med Flow-flödet och PKCE är aktiverat för dessa URI: er.

:::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-03-spa-redirect-uri-tile.png" alt-text="En program panel med en sida i app-registrering i Azure Portal":::

Du kan också [skapa en ny app-registrering](scenario-spa-app-registration.md) i stället för att uppdatera omdirigerings-URI: erna i den befintliga registreringen.

## <a name="update-your-code-to-msaljs-2x"></a>Uppdatera koden till MSAL.js 2. x

I MSAL 1. x skapade du en program instans genom att initiera en [UserAgentApplication][msal-js-useragentapplication] på följande sätt:

```javascript
// MSAL 1.x
import * as msal from "msal";

const msalInstance = new msal.UserAgentApplication(config);
```

I MSAL 2. x, initiera istället en [PublicClientApplication][msal-js-publicclientapplication]:

```javascript
// MSAL 2.x
import * as msal from "@azure/msal-browser";

const msalInstance = new msal.PublicClientApplication(config);
```

En fullständig genom gång av hur du lägger till MSAL 2. x i programmet finns i [Självstudier: Logga in användare och anropa Microsoft Graph-API: et från en JavaScript-app med ett enda sid (Spa) med hjälp av auth Code Flow](tutorial-v2-javascript-auth-code.md).

Ytterligare ändringar som du kan behöva göra i koden finns i [migreringsguiden](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/v1-migration.md) för GitHub.

## <a name="disable-implicit-grant-settings"></a>Inaktivera inställningar för implicit beviljande

När du har uppdaterat alla produktions program som använder den här appens registrering och dess klient-ID till MSAL 2. x och koden för auktoriseringskod, bör du avmarkera de implicita beviljande inställningarna i appens registrering.

När du avmarkerar inställningarna för implicit beviljande i appens registrering inaktive ras det implicita flödet för alla program som använder registrering och dess klient-ID.

Inaktivera **inte** det implicita tilldelnings flödet innan du har uppdaterat alla program till MSAL.js 2. x och [PublicClientApplication][msal-js-publicclientapplication].

## <a name="next-steps"></a>Nästa steg

Mer information om flödet för auktoriseringskod, inklusive skillnaderna mellan koderna för implicit och auth-kod, finns i [Microsoft Identity Platform och OAuth 2,0 Authorization Code Flow](v2-oauth2-auth-code-flow.md).

Om du vill lära dig mer om Java Script-program med en enda sida på Microsofts identitets plattform, så kan du komma igång med hjälp av program serien med [en enda sida](scenario-spa-overview.md) med artiklar.

<!-- LINKS - external -->
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html
