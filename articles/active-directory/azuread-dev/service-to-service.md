---
title: Tjänst-till-tjänst-appar i Azure Active Directory
description: Beskriver vilka tjänst-till-tjänst-program och grunderna om protokoll flöde, registrering och token som upphör att gälla för den här typen av app.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 179034533d90dbbb6ca362fc6f72996f32873729
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154771"
---
# <a name="service-to-service-apps"></a>Tjänst-till-tjänst-appar

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Tjänst-till-tjänst-program kan vara ett daemon-eller serverprogram som behöver hämta resurser från ett webb-API. Det finns två under scenarier som gäller för det här avsnittet:

- En daemon som måste anropa ett webb-API som bygger på OAuth 2,0-klientens autentiseringsuppgifter för beviljande typ

    I det här scenariot är det viktigt att förstå några saker. För det första är användar interaktion inte möjligt med ett daemon-program, vilket kräver att programmet har sin egen identitet. Ett exempel på ett daemon-program är ett batch-jobb eller en operativ system tjänst som körs i bakgrunden. Den här typen av program begär en åtkomsttoken genom att använda dess program identitet och presentera dess program-ID, autentiseringsuppgift (lösen ord eller certifikat) och program-ID-URI till Azure AD. Efter en lyckad autentisering tar daemonen emot en åtkomsttoken från Azure AD, som sedan används för att anropa webb-API: et.

- Ett serverprogram (t. ex. ett webb-API) som måste anropa ett webb-API som bygger på OAuth 2,0 för utkast specifikation

    I det här scenariot är det att anta att en användare har autentiserats i ett internt program och att det här interna programmet måste anropa ett webb-API. Azure AD utfärdar en JWT-åtkomsttoken för att anropa webb-API: et. Om webb-API: et behöver anropa ett annat underordnat webb-API, kan det använda sig av flödet för att delegera användarens identitet och autentisera till webb-API: et på andra nivån.

## <a name="diagram"></a>Diagram

![Daemon-eller Server program till webb-API-diagram](./media/authentication-scenarios/daemon-server-app-to-web-api.png)

## <a name="protocol-flow"></a>Protokoll flöde

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Program identitet med beviljande av OAuth 2,0-klientautentiseringsuppgifter

1. Först måste serverprogrammet autentiseras med Azure AD som fristående, utan mänsklig interaktion, till exempel en interaktiv inloggnings dialog ruta. Den gör en begäran till Azure ADs token-slutpunkt, vilket ger autentiseringsuppgifter, program-ID och program-ID-URI.
1. Azure AD autentiserar programmet och returnerar en JWT-åtkomsttoken som används för att anropa webb-API: et.
1. Via HTTPS använder webb programmet den returnerade JWT-åtkomsttoken för att lägga till JWT-strängen med beteckningen "Bearer" i Authorization-huvudet för begäran till webb-API: et. Webb-API: et validerar sedan JWT-token och om verifieringen lyckas returneras den önskade resursen.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Delegerad användar identitet med OAuth 2,0 på uppdrag av utkast specifikation

Flödet som beskrivs nedan förutsätter att en användare har autentiserats i ett annat program (t. ex. ett internt program) och att deras användar identitet har använts för att hämta en åtkomsttoken till webb-API: et på första nivån.

1. Det interna programmet skickar åtkomsttoken till den första nivån i webb-API: et.
1. Webb-ID: t på den första nivån skickar en begäran till Azure ADs token-slutpunkt, vilket ger programmets ID och autentiseringsuppgifter, samt användarens åtkomsttoken. Dessutom skickas begäran med en on_behalf_of-parameter som anger att webb-API: et begär nya token för att anropa ett underordnat webb-API för den ursprungliga användarens räkning.
1. Azure AD kontrollerar att webb-API: et på den första nivån har behörighet att komma åt webb-API: et på andra nivån och validerar begäran, returnerar en JWT-åtkomsttoken och en JWT-uppdateringstoken till webb-API: et på första nivån.
1. Via HTTPS anropar webb-API: et för den första nivån genom att lägga till token-strängen i Authorization-huvudet i begäran. Webb-API: et på den första nivån kan fortsätta att anropa webb-API: et på andra nivån så länge åtkomsttoken och uppdaterade token är giltiga.

## <a name="code-samples"></a>Kodexempel

Se kod exemplen för daemon eller Server program till webb-API-scenarier: [Server-eller daemon-program till webb-API](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Appregistrering

* En enskild klient – både för program identiteten och den delegerade användarens identitets fall, måste daemon-eller Server programmet vara registrerat i samma katalog i Azure AD. Webb-API: et kan konfigureras för att visa en uppsättning behörigheter som används för att begränsa daemon eller serverns åtkomst till resurserna. Om en delegerad användar identitets typ används måste serverprogrammet välja önskade behörigheter. På sidan **API-behörighet** för program registreringen när du har valt **Lägg till en behörighet** och valt API-serien väljer du **delegerade behörigheter** och väljer sedan behörigheter. Det här steget krävs inte om program identitets typen används.
* Daemon-eller serverprogram har kon figurer ATS för att ange de behörigheter som krävs för att fungera med flera klient organisationer. Den här listan över nödvändiga behörigheter visas i en dialog ruta när en användare eller administratör i mål katalogen ger tillåtelse till programmet, vilket gör det tillgängligt för deras organisation. Vissa program kräver bara behörigheter på användar nivå, som alla användare i organisationen kan godkänna. Andra program kräver behörigheter på administratörs nivå, som en användare i organisationen inte kan godkänna. Endast en katalog administratör kan ge medgivande till program som kräver den här nivån av behörigheter. När användaren eller administratören samtycks, registreras båda webb-API: erna i katalogen.

## <a name="token-expiration"></a>Förfallo datum för token

När det första programmet använder sin auktoriseringskod för att hämta en JWT-åtkomsttoken får den också en JWT-uppdateringstoken. När åtkomsttoken upphör att gälla, kan uppdateringstoken användas för att autentisera användaren igen utan att begära autentiseringsuppgifter. Denna uppdateringstoken används sedan för att autentisera användaren, vilket resulterar i en ny åtkomsttoken och en uppdaterad token.

## <a name="next-steps"></a>Nästa steg

- Läs mer om andra [program typer och scenarier](app-types.md)
- Lär dig mer om grunderna i Azure AD- [autentisering](v1-authentication-scenarios.md)
