---
title: Web Apps i Azure Active Directory
description: Beskriver vilka webb program som är och grunderna för protokoll flöde, registrering och token som upphör att gälla för den här typen av app.
services: active-directory
documentationcenter: ''
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
ms.openlocfilehash: e4a7fb72d40f5db65e8e30264e9d68b2727749e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154414"
---
# <a name="web-apps"></a>Webbappar

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Web Apps är program som autentiserar en användare i en webbläsare till ett webb program. I det här scenariot dirigerar webb programmet användarens webbläsare för att logga in på Azure AD. Azure AD returnerar ett inloggnings svar via användarens webbläsare, som innehåller anspråk om användaren i en säkerhetstoken. Det här scenariot stöder inloggning med hjälp av OpenID Connect, SAML 2,0 och WS-Federation protokoll.

## <a name="diagram"></a>Diagram

![Autentiserings flöde för webbläsare till webb program](./media/authentication-scenarios/web-browser-to-web-api.png)

## <a name="protocol-flow"></a>Protokoll flöde

1. När en användare besöker programmet och behöver logga in, omdirigeras de via en inloggningsbegäran till autentiseringens slut punkt i Azure AD.
1. Användaren loggar in på inloggnings sidan.
1. Om autentiseringen lyckas skapar Azure AD en autentiseringstoken och returnerar ett inloggnings svar till programmets svars-URL som konfigurerades i Azure Portal. För ett produktions program ska denna svars-URL vara HTTPS. Den returnerade token innehåller anspråk om användaren och Azure AD som krävs av programmet för att validera token.
1. Programmet verifierar token med hjälp av en offentlig signerings nyckel och information om utfärdare som är tillgänglig i dokumentet för federationsmetadata för Azure AD. När programmet har validerat token startar den en ny session med användaren. Den här sessionen ger användaren åtkomst till programmet tills den upphör att gälla.

## <a name="code-samples"></a>Kodexempel

Se exempel på kod exempel för webbläsare till webb program scenarier. Och kom tillbaka ofta när nya exempel läggs till ofta.

## <a name="app-registration"></a>Appregistrering

Information om hur du registrerar en webbapp finns i [Registrera en app](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Enskild klient – om du skapar ett program precis för din organisation måste det registreras i företagets katalog med hjälp av Azure Portal.
* Flera innehavare – om du skapar ett program som kan användas av användare utanför organisationen måste det registreras i företagets katalog, men måste också registreras i varje organisations katalog som ska använda programmet. Om du vill göra ditt program tillgängligt i sin katalog kan du inkludera en registrerings process för dina kunder som gör det möjligt för dem att godkänna ditt program. När de registrerar sig för ditt program visas en dialog ruta som visar de behörigheter programmet kräver, och sedan alternativet för att godkänna. Beroende på vilka behörigheter som krävs kan en administratör i den andra organisationen behöva ge sitt medgivande. När användaren eller administratören samtycker, registreras programmet i sin katalog.

## <a name="token-expiration"></a>Förfallo datum för token

Användarens session upphör att gälla när livs längden för token som utfärdas av Azure AD upphör att gälla. Ditt program kan förkorta den här tids perioden om du vill, till exempel Logga ut användare baserat på en period av inaktivitet. När sessionen upphör att gälla uppmanas användaren att logga in igen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [program typer och scenarier](app-types.md)
* Lär dig mer om grunderna i Azure AD- [autentisering](v1-authentication-scenarios.md)
