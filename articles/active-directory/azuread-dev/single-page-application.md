---
title: Program med en sida i Azure Active Directory
description: Beskriver vilka SPAs (Single-Page Applications) som är och grunderna för protokoll flöde, registrering och token förfallo datum för den här typen av appar.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: adf3c5b5cd40a9ea3f07ba9c92cfc4544ca60f1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154754"
---
# <a name="single-page-applications"></a>Program med en enda sida

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

SPAs (Single-Page Applications) är vanligt vis strukturerade som ett JavaScript-presentations lager (klient del) som körs i webbläsaren och en server del för webb-API som körs på en server och implementerar programmets affärs logik. Om du vill veta mer om den implicita auktoriseringen och hjälpa dig att avgöra om det är rätt för ditt program scenario, se [förstå OAuth2 implicita bidrags flödet i Azure Active Directory](v1-oauth2-implicit-grant-flow.md).

I det här scenariot, när användaren loggar in, använder JavaScript-fronten [Active Directory-autentiseringsbibliotek för Java Script (ADAL.JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) och den implicita auktoriseringen för att hämta en ID-token (id_token) från Azure AD. Token cachelagras och klienten kopplar den till begäran som Bearer-token vid anrop till dess webb-API-Server del, som skyddas med OWIN mellanprogram.

## <a name="diagram"></a>Diagram

![Program diagram med en enda sida](./media/authentication-scenarios/single-page-app.png)

## <a name="protocol-flow"></a>Protokoll flöde

1. Användaren navigerar till webb programmet.
1. Programmet returnerar Java Script front end (presentations lager) till webbläsaren.
1. Användaren startar inloggningen, till exempel genom att klicka på en inloggnings länk. Webbläsaren skickar en GET till Azure AD Authorization-slutpunkten för att begära en ID-token. Den här begäran innehåller programmets ID och svars-URL i frågeparametrar.
1. Azure AD verifierar svars-URL: en mot den registrerade svars-URL som konfigurerades i Azure Portal.
1. Användaren loggar in på inloggnings sidan.
1. Om autentiseringen lyckas skapar Azure AD en ID-token och returnerar den som ett URL-fragment (#) till programmets svars-URL. För ett produktions program ska denna svars-URL vara HTTPS. Den returnerade token innehåller anspråk om användaren och Azure AD som krävs av programmet för att validera token.
1. Den JavaScript-klient kod som körs i webbläsaren extraherar token från det svar som ska användas för att säkra anrop till programmets webb-API-Server del.
1. Webbläsaren anropar programmets webb-API-Server del med ID-token i Authorization-huvudet. Azure AD-Autentiseringstjänsten utfärdar en ID-token som kan användas som en Bearer-token om resursen är samma som klient-ID: t (i det här fallet gäller detta att webb-API: et är appens egen server del).

## <a name="code-samples"></a>Kodexempel

Se [kod exemplen för program scenarier med en enda sida](sample-v1-code.md#single-page-applications). Kom ihåg att kontrol lera tillbaka ofta när nya exempel läggs till ofta.

## <a name="app-registration"></a>Appregistrering

* Enskild klient – om du skapar ett program precis för din organisation måste det registreras i företagets katalog med hjälp av Azure Portal.
* Flera innehavare – om du skapar ett program som kan användas av användare utanför organisationen måste det registreras i företagets katalog, men måste också registreras i varje organisations katalog som ska använda programmet. Om du vill göra ditt program tillgängligt i sin katalog kan du inkludera en registrerings process för dina kunder som gör det möjligt för dem att godkänna ditt program. När de registrerar sig för ditt program visas en dialog ruta som visar de behörigheter programmet kräver, och sedan alternativet för att godkänna. Beroende på vilka behörigheter som krävs kan en administratör i den andra organisationen behöva ge sitt medgivande. När användaren eller administratören samtycker, registreras programmet i sin katalog.

När du har registrerat programmet måste det konfigureras för att använda OAuth-protokollet OAuth 2,0. Som standard är det här protokollet inaktiverat för program. Om du vill aktivera OAuth2 implicita beviljande protokoll för ditt program redigerar du dess program manifest från Azure Portal och anger värdet "oauth2AllowImplicitFlow" till true. Mer information finns i [program manifestet](../develop/reference-app-manifest.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="token-expiration"></a>Förfallo datum för token

Med hjälp av ADAL.js kan du:

* Uppdaterar en utgången token
* Begära en åtkomsttoken för att anropa en webb-API-resurs

Efter en lyckad autentisering skriver Azure AD en cookie i användarens webbläsare för att upprätta en session. Observera att sessionen finns mellan användaren och Azure AD (inte mellan användaren och webb programmet). När en token går ut använder ADAL.js den här sessionen för att hämta en annan token tyst. ADAL.js använder en dold iFrame för att skicka och ta emot begäran med hjälp av OAuth-protokollet för implicit tilldelning. ADAL.js kan också använda samma mekanism för att få åtkomst till tokens i bakgrunden för andra webb-API-resurser. programmet anropas så länge dessa resurser stöder resurs delning mellan ursprung (CORS), är registrerade i användarens katalog och alla nödvändiga medgivande angavs av användaren under inloggningen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [program typer och scenarier](app-types.md)
* Lär dig mer om grunderna i Azure AD- [autentisering](v1-authentication-scenarios.md)
