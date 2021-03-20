---
title: Webb-API-appar i Azure Active Directory
description: Beskriver vilka webb-API-program som är och grunderna om protokoll flöde, registrering och token förfallo datum för den här typen av app.
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
ms.openlocfilehash: 9cf5a9c81ca1d7a42a5a8e342dee55f335656c3e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154431"
---
# <a name="web-api"></a>Webb-API

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Webb-API-appar är webb program som behöver hämta resurser från ett webb-API. I det här scenariot finns det två identitets typer som webb programmet kan använda för att autentisera och anropa webb-API: et:

- **Program identitet** – i det här scenariot används OAuth 2,0-klientautentiseringsuppgifter för autentisering som program och åtkomst till webb-API: et. När du använder en program identitet kan webb-API: et bara identifiera att webb programmet anropar det eftersom webb-API: n inte får någon information om användaren. Om programmet tar emot information om användaren kommer det att skickas via applikations protokollet och det är inte signerat av Azure AD. Webb-API-förtroenden som webb programmet autentiserat användaren. Därför kallas det här mönstret ett betrott under system.
- **Delegerad användar identitet** – det här scenariot kan utföras på två sätt: OpenID Connect och OAuth 2,0-auktoriseringskod som ger en konfidentiell klient. Webb programmet hämtar en åtkomsttoken för användaren, som bevisar webb-API: et som användaren har autentiserat till webb programmet och att webb programmet kunde hämta en delegerad användar identitet för att anropa webb-API: et. Denna åtkomsttoken skickas i begäran till webb-API: et, som auktoriserar användaren och returnerar önskad resurs.

Både program identiteten och de delegerade användar identitets typerna beskrivs i flödet nedan. Den viktigaste skillnaden mellan dem är att den delegerade användar identiteten först måste hämta en auktoriseringskod innan användaren kan logga in och få åtkomst till webb-API: et.

## <a name="diagram"></a>Diagram

![Webb program till webb-API-diagram](./media/authentication-scenarios/web-app-to-web-api.png)

## <a name="protocol-flow"></a>Protokoll flöde

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Program identitet med beviljande av OAuth 2,0-klientautentiseringsuppgifter

1. En användare är inloggad i Azure AD i webb programmet (mer information finns i avsnittet **Web Apps** ).
1. Webb programmet måste skaffa en åtkomsttoken så att den kan autentisera till webb-API: et och hämta önskad resurs. Den skickar en begäran till Azure ADs token-slutpunkt, som tillhandahåller autentiseringsuppgifter, program-ID och webb-API: n för program-ID.
1. Azure AD autentiserar programmet och returnerar en JWT-åtkomsttoken som används för att anropa webb-API: et.
1. Via HTTPS använder webb programmet den returnerade JWT-åtkomsttoken för att lägga till JWT-strängen med beteckningen "Bearer" i Authorization-huvudet för begäran till webb-API: et. Webb-API: et validerar sedan JWT-token och om verifieringen lyckas returneras den önskade resursen.

### <a name="delegated-user-identity-with-openid-connect"></a>Delegerad användar identitet med OpenID Connect

1. En användare är inloggad på ett webb program med hjälp av Azure AD (se avsnittet webbläsare till webb program ovan). Om användaren av webb programmet ännu inte har samtyckt till att tillåta webb programmet att anropa webb-API: et för dess räkning måste användaren godkänna. Programmet kommer att visa de behörigheter som krävs, och om något av dessa är administratörs behörighet kan en normal användare i katalogen inte samtycka. Den här medgivande processen gäller endast för program med flera klienter, inte enskilda klient program, eftersom programmet redan har de behörigheter som krävs. När användaren är inloggad mottog webb programmet en ID-token med information om användaren, samt en auktoriseringskod.
1. Med hjälp av den auktoriseringskod som utfärdats av Azure AD skickar webb programmet en begäran till Azure ADs token-slutpunkt som innehåller auktoriseringskod, information om klient programmet (program-ID och omdirigerings-URI) och önskad resurs (program-ID-URI för webb-API).
1. Auktoriseringskod och information om webb programmet och webb-API: n verifieras av Azure AD. Vid lyckad verifiering returnerar Azure AD två tokens: en JWT-åtkomsttoken och en JWT-uppdateringstoken.
1. Via HTTPS använder webb programmet den returnerade JWT-åtkomsttoken för att lägga till JWT-strängen med beteckningen "Bearer" i Authorization-huvudet för begäran till webb-API: et. Webb-API: et validerar sedan JWT-token och om verifieringen lyckas returneras den önskade resursen.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Delegerad användar identitet med OAuth 2,0 Authorization Code Grant

1. En användare har redan loggat in på ett webb program, vars autentiseringsmekanism är oberoende av Azure AD.
1. Webb programmet kräver en auktoriseringskod för att erhålla en åtkomsttoken, så den utfärdar en begäran via webbläsaren till Azure ADs Authorization-slutpunkt, vilket ger program-ID och omdirigerings-URI för webb programmet efter autentiseringen. Användaren loggar in på Azure AD.
1. Om användaren av webb programmet ännu inte har samtyckt till att tillåta webb programmet att anropa webb-API: et för dess räkning måste användaren godkänna. Programmet kommer att visa de behörigheter som krävs, och om något av dessa är administratörs behörighet kan en normal användare i katalogen inte samtycka. Detta medgivande gäller för både en och flera klient program. I det enskilda klient fallet kan en administratör utföra administratörs medgivande för användarens räkning. Detta kan göras med hjälp av `Grant Permissions` knappen i [Azure Portal](https://portal.azure.com). 
1. När användaren har samtyckt får webb programmet den auktoriseringskod som krävs för att hämta en åtkomsttoken.
1. Med hjälp av den auktoriseringskod som utfärdats av Azure AD skickar webb programmet en begäran till Azure ADs token-slutpunkt som innehåller auktoriseringskod, information om klient programmet (program-ID och omdirigerings-URI) och önskad resurs (program-ID-URI för webb-API).
1. Auktoriseringskod och information om webb programmet och webb-API: n verifieras av Azure AD. Vid lyckad verifiering returnerar Azure AD två tokens: en JWT-åtkomsttoken och en JWT-uppdateringstoken.
1. Via HTTPS använder webb programmet den returnerade JWT-åtkomsttoken för att lägga till JWT-strängen med beteckningen "Bearer" i Authorization-huvudet för begäran till webb-API: et. Webb-API: et validerar sedan JWT-token och om verifieringen lyckas returneras den önskade resursen.

## <a name="code-samples"></a>Kodexempel

Se kod exemplen för webb program till webb-API-scenarier. Och kom tillbaka ofta – nya exempel läggs till ofta. Webb [program till webb-API](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Appregistrering

Information om hur du registrerar ett program med Azure AD v 1.0-slutpunkten finns i [Registrera en app](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Enskild klient – både för program identiteten och den delegerade användarens identitets fall måste webb programmet och webb-API: t vara registrerat i samma katalog i Azure AD. Webb-API: et kan konfigureras för att visa en uppsättning behörigheter som används för att begränsa webb programmets åtkomst till resurserna. Om en delegerad användar identitets typ används måste webb programmet välja önskade behörigheter från List rutan **behörigheter till andra program** i Azure Portal. Det här steget krävs inte om program identitets typen används.
* För flera klient organisationer är webb programmet konfigurerat för att ange de behörigheter som krävs för att fungera. Den här listan över nödvändiga behörigheter visas i en dialog ruta när en användare eller administratör i mål katalogen ger tillåtelse till programmet, vilket gör det tillgängligt för deras organisation. Vissa program kräver bara behörigheter på användar nivå, som alla användare i organisationen kan godkänna. Andra program kräver behörigheter på administratörs nivå, som en användare i organisationen inte kan godkänna. Endast en katalog administratör kan ge medgivande till program som kräver den här nivån av behörigheter. När användaren eller administratören samtycks, är webb programmet och webb-API: et registrerade i deras katalog.

## <a name="token-expiration"></a>Förfallo datum för token

När webb programmet använder sin auktoriseringskod för att hämta en JWT-åtkomsttoken får den också en JWT-uppdateringstoken. När åtkomsttoken upphör att gälla, kan uppdateringstoken användas för att autentisera användaren igen utan att behöva logga in igen. Denna uppdateringstoken används sedan för att autentisera användaren, vilket resulterar i en ny åtkomsttoken och en uppdaterad token.

## <a name="next-steps"></a>Nästa steg

- Läs mer om andra [program typer och scenarier](app-types.md)
- Lär dig mer om grunderna i Azure AD- [autentisering](v1-authentication-scenarios.md)
