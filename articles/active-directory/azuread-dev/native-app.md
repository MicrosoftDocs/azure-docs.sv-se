---
title: Inbyggda appar i Azure Active Directory
description: Beskriver vad inbyggda appar är och grunderna om protokoll flöde, registrering och token förfallo datum för den här typen av app.
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
ms.openlocfilehash: 9ecf711f5442b6f21de53d2735ad1c94d7cb6223
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154805"
---
# <a name="native-apps"></a>Inbyggda appar

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Inbyggda appar är program som anropar ett webb-API för en användares räkning. Det här scenariot bygger på en beviljande typ för OAuth 2,0-auktoriseringskod med en offentlig klient, enligt beskrivningen i avsnittet 4,1 i [OAuth 2,0-specifikationen](https://tools.ietf.org/html/rfc6749). Det interna programmet får en åtkomsttoken för användaren med hjälp av OAuth 2,0-protokollet. Denna åtkomsttoken skickas sedan i begäran till webb-API: et, som auktoriserar användaren och returnerar önskad resurs.

## <a name="diagram"></a>Diagram

![Inbyggt program i webb-API-diagram](./media/authentication-scenarios/native-app-to-web-api.png)

## <a name="protocol-flow"></a>Protokoll flöde

Om du använder AD Authentication-biblioteken hanteras de flesta protokoll detaljer som beskrivs nedan, till exempel webbläsarens popup-fönster, cachelagring av token och hantering av uppdateringstoken.

1. Med hjälp av ett popup-fönster skickar det interna programmet en begäran till behörighets slut punkten i Azure AD. Den här begäran innehåller program-ID och omdirigerings-URI för det interna programmet som visas i Azure Portal och program-ID-URI: n för webb-API: et. Om användaren inte redan har loggat in uppmanas användaren att logga in igen
1. Azure AD autentiserar användaren. Om det är ett program för flera innehavare och det krävs att du använder programmet, måste användaren godkänna om de inte redan har gjort det. Efter att ha beviljat medgivande och vid lyckad autentisering utfärdar Azure AD ett auktoriseringskod-svar tillbaka till klient programmets omdirigerings-URI.
1. När Azure AD utfärdar ett auktoriseringskod tillbaka till omdirigerings-URI: n, stoppar klient programmet webb läsar interaktion och extraherar auktoriseringskod från svaret. Med den här auktoriseringskod skickar klient programmet en begäran till Azure ADs token-slutpunkt som innehåller auktoriseringskod, information om klient programmet (program-ID och omdirigerings-URI) och önskad resurs (program-ID-URI för webb-API).
1. Auktoriseringskod och information om klient programmet och webb-API: n verifieras av Azure AD. Vid lyckad verifiering returnerar Azure AD två tokens: en JWT-åtkomsttoken och en JWT-uppdateringstoken. Dessutom returnerar Azure AD grundläggande information om användaren, till exempel visnings namn och klient-ID.
1. Via HTTPS använder klient programmet den returnerade JWT-åtkomsttoken för att lägga till JWT-strängen med beteckningen "Bearer" i Authorization-huvudet för begäran till webb-API: et. Webb-API: et validerar sedan JWT-token och om verifieringen lyckas returneras den önskade resursen.
1. När åtkomsttoken upphör att gälla får klient programmet ett fel meddelande som anger att användaren måste autentisera igen. Om programmet har en giltig uppdateringstoken kan den användas för att hämta en ny åtkomsttoken utan att användaren uppmanas att logga in igen. Om uppdateringstoken upphör att gälla måste programmet interaktivt autentisera användaren en gång.

> [!NOTE]
> Uppdateringstoken som utfärdats av Azure AD kan användas för att få åtkomst till flera resurser. Om du till exempel har ett klient program som har behörighet att anropa två webb-API: er, kan uppdateringstoken användas för att hämta en åtkomsttoken till det andra webb-API: et.

## <a name="code-samples"></a>Kodexempel

Se kod exemplen för inbyggt program i webb-API-scenarier. Och kom tillbaka ofta – vi lägger till nya exempel ofta. [Inbyggt program i webb-API](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>Appregistrering

Information om hur du registrerar ett program med Azure AD v 1.0-slutpunkten finns i [Registrera en app](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* En enskild klient – både det inbyggda programmet och webb-API: et måste registreras i samma katalog i Azure AD. Webb-API: et kan konfigureras för att visa en uppsättning behörigheter som används för att begränsa det ursprungliga programmets åtkomst till resurserna. Klient programmet väljer sedan önskade behörigheter från den nedrullningsbara menyn "behörigheter till andra program" i Azure Portal.
* Med flera klient organisationer först registreras det interna programmet i utvecklaren eller utgivarens katalog. För det andra är det interna programmet konfigurerat för att ange de behörigheter som krävs för att fungera. Den här listan över nödvändiga behörigheter visas i en dialog ruta när en användare eller administratör i mål katalogen ger tillåtelse till programmet, vilket gör det tillgängligt för deras organisation. Vissa program kräver bara behörigheter på användar nivå, som alla användare i organisationen kan godkänna. Andra program kräver behörigheter på administratörs nivå, som en användare i organisationen inte kan godkänna. Endast en katalog administratör kan ge medgivande till program som kräver den här nivån av behörigheter. När användaren eller administratören samtycks, är det bara webb-API: et som är registrerat i deras katalog. 

## <a name="token-expiration"></a>Förfallo datum för token

När det interna programmet använder sin auktoriseringskod för att hämta en JWT-åtkomsttoken får den också en JWT-uppdateringstoken. När åtkomsttoken upphör att gälla, kan uppdateringstoken användas för att autentisera användaren igen utan att behöva logga in igen. Denna uppdateringstoken används sedan för att autentisera användaren, vilket resulterar i en ny åtkomsttoken och en uppdaterad token.

## <a name="next-steps"></a>Nästa steg

- Läs mer om andra [program typer och scenarier](app-types.md)
- Lär dig mer om grunderna i Azure AD- [autentisering](v1-authentication-scenarios.md)
