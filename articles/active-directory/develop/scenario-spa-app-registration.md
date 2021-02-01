---
title: Registrera program med en enda sida (SPA) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar ett program med en enda sida (app Registration)
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: 39a675ff4947e7eca64298e1e68160cd6149f081
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226346"
---
# <a name="single-page-application-app-registration"></a>Program med en sida: app-registrering

Utför följande steg för att registrera ett program med en enda sida (SPA) i Microsoft Identity Platform. Registrerings stegen skiljer sig mellan MSAL.js 1,0, som har stöd för det implicita tilldelnings flödet och MSAL.js 2,0, som stöder auktoriseringskod med PKCE.

## <a name="create-the-app-registration"></a>Skapa registrerings appen

Börja med att utföra följande steg för att skapa den första appens registrering för både MSAL.js 1,0-och 2,0-baserade program.

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
1. Sök efter och välj **Azure Active Directory**.
1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
1. Ange ett **namn** för ditt program. Användare av appen kan se det här namnet och du kan ändra det senare.
1. Välj de **konto typer som stöds** för programmet. Ange **ingen** **omdirigerings-URI**. En beskrivning av de olika konto typerna finns i [Registrera ett program](quickstart-register-app.md).
1. Välj **Registrera** för att skapa appens registrering.

Konfigurera sedan appens registrering med en **omdirigerings-URI** för att ange var Microsoft Identity Platform ska omdirigera klienten tillsammans med alla säkerhetstoken. Använd de steg som är lämpliga för den version av MSAL.js som du använder i ditt program:

- [MSAL.js 2,0 med auth Code Flow](#redirect-uri-msaljs-20-with-auth-code-flow) (rekommenderas)
- [MSAL.js 1,0 med implicit flöde](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>Omdirigerings-URI: [MSAL.js 2,0 med auth Code Flow](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)

Följ dessa steg om du vill lägga till en omdirigerings-URI för en app som använder MSAL.js 2,0 eller senare. MSAL.js 2.0 + stöder auktoriseringskod-flödet med PKCE och CORS som svar på [webb läsar begränsningar från tredje part](reference-third-party-cookies-spas.md). Det implicita tilldelnings flödet stöds inte i MSAL.js 2.0 +.

1. I Azure Portal väljer du den app-registrering som du skapade tidigare i [skapa appens registrering](#create-the-app-registration).
1. Under **Hantera** väljer du **autentisering**  >  **Lägg till en plattform**.
1. Under **webb program** väljer du den **enda sidans program** panel.
1. Under **omdirigerings-URI** anger du en [omdirigerings-URI](reply-url.md). Markera **inte** någon av kryss rutorna under **implicit beviljande och hybrid flöden**.
1. Välj **Konfigurera** för att slutföra tillägg av omdirigerings-URI.

Du har nu slutfört registreringen av ett enda webb program (SPA) och konfigurerat en omdirigerings-URI som klienten ska omdirigeras till och eventuella säkerhetstoken kommer att skickas. Genom att konfigurera omdirigerings-URI: n med hjälp av program panelen på en **sida** i fönstret **Lägg till en plattform** konfigureras program registreringen för att stödja AUKTORISERINGSKOD med PKCE och CORS.

Följ [själv studie kursen](tutorial-v2-javascript-auth-code.md) för ytterligare vägledning.

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>Omdirigerings-URI: [MSAL.js 1,0 med implicit flöde](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)

Följ dessa steg om du vill lägga till en omdirigerings-URI för en app med en enda sida som använder MSAL.js 1,3 eller tidigare och det implicita tilldelnings flödet. Program som använder MSAL.js 1,3 eller tidigare stöder inte auth Code Flow.

1. I Azure Portal väljer du den app-registrering som du skapade tidigare i [skapa appens registrering](#create-the-app-registration).
1. Under **Hantera** väljer du **autentisering**  >  **Lägg till en plattform**.
1. Under **webb program** väljer du en fönster ruta med **en sida** .
1. Under **omdirigerings-URI** anger du en [omdirigerings-URI](reply-url.md).
1. Aktivera **implicit beviljande och hybrid flöden**:
    - Om ditt program loggar in användare väljer du **ID-token**.
    - Om ditt program också behöver anropa ett skyddat webb-API väljer du **åtkomsttoken**. Mer information om dessa typer av token finns i [ID-token](id-tokens.md) och [åtkomsttoken](access-tokens.md).
1. Välj **Konfigurera** för att slutföra tillägg av omdirigerings-URI.

Du har nu slutfört registreringen av ett enda webb program (SPA) och konfigurerat en omdirigerings-URI som klienten ska omdirigeras till och eventuella säkerhetstoken kommer att skickas. Genom att välja en eller båda av **ID-tokens** och **åtkomsttoken** har du aktiverat det implicita tilldelnings flödet.

Följ [själv studie kursen](tutorial-v2-javascript-spa.md) för ytterligare vägledning.

## <a name="note-about-authorization-flows"></a>Information om auktoriserings flöden

Som standard kan en app-registrering som skapats med hjälp av konfiguration med en enda sida-plattform Aktivera kod flödet för auktorisering. För att dra nytta av det här flödet måste ditt program använda MSAL.js 2,0 eller senare.

Som nämnts tidigare är program med en sida som använder MSAL.js 1,3 begränsade till det implicita tilldelnings flödet. Aktuella [OAuth 2,0-rekommenderade metoder](v2-oauth2-auth-code-flow.md) rekommenderar att du använder Authorization Code Flow i stället för det implicita flödet för SPAs. Om du har en token med begränsad livs längd hjälper det också ditt program att anpassa sig till [moderna webbläsare cookies sekretess begränsningar](reference-third-party-cookies-spas.md), till exempel Safari ITP.

När alla dina produktions program med en enda sida som representeras av en registrerad app-registrering använder MSAL.js 2,0 och koden för auktoriseringskod, avmarkerar du kryss rutan för den implicita beviljande inställningen i fönstret **Authentication** för appens registrering i Azure Portal. Program som använder MSAL.js 1. x och det implicita flödet kan fortsätta att fungera, men om du lämnar det implicita flödet aktiverat (markerat).

## <a name="next-steps"></a>Nästa steg

Konfigurera appens kod så att den använder den app-registrering som du skapade i föregående steg: [appens kod konfiguration](scenario-spa-app-configuration.md).
