---
title: Säkerhetstoken | Azure
titleSuffix: Microsoft identity platform
description: Lär dig grunderna om säkerhetstoken i Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 6d9f5538d377be1414089e591559344bde4f381a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98795632"
---
# <a name="security-tokens"></a>Säkerhetstoken

En centraliserad identitets leverantör är särskilt användbar för appar som har användare som finns i hela världen och som inte nödvändigt vis loggar in från företagets nätverk. Microsoft Identity Platform autentiserar användare och ger säkerhetstoken, till exempel [åtkomsttoken](developer-glossary.md#access-token), [uppdateringstoken](developer-glossary.md#refresh-token)och [ID-token](developer-glossary.md#id-token). Säkerhetstoken gör att ett [klient program](developer-glossary.md#client-application) kan komma åt skyddade resurser på en [resurs Server](developer-glossary.md#resource-server).

**Åtkomsttoken**: en åtkomsttoken är en säkerhetstoken som utfärdas av en [Authorization Server](developer-glossary.md#authorization-server) som en del av ett [OAuth 2,0](active-directory-v2-protocols.md) -flöde. Den innehåller information om användaren och resursen som token är avsedd för. Informationen kan användas för att få åtkomst till webb-API: er och andra skyddade resurser. Åtkomsttoken verifieras av resurser för att ge åtkomst till en klient app. Mer information om hur Microsoft Identity Platform utfärdar åtkomst-token finns i [åtkomsttoken](access-tokens.md).

**Uppdatera token**: eftersom åtkomsttoken bara är giltiga under en kort tids period, kommer auktoriseringsservern ibland att utfärda en uppdateringstoken på samma gång som åtkomsttoken utfärdas. Klient programmet kan sedan byta ut denna uppdateringstoken för en ny åtkomsttoken vid behov. Mer information om hur Microsoft Identity Platform använder Refresh tokens för att återkalla behörigheter finns i återkalla [token](access-tokens.md#token-revocation).

**ID-token**: ID-token skickas till klient programmet som en del av ett [OpenID Connect](v2-protocols-oidc.md) -flöde. De kan skickas tillsammans med eller i stället för en åtkomsttoken. ID-token används av klienten för att autentisera användaren. Mer information om hur Microsoft Identity Platform utfärdar ID-token finns i [ID-token](id-tokens.md).

> [!NOTE]
> Den här artikeln beskriver säkerhetstoken som används av OAuth2-och OpenID Connect-protokollen. Många företags program använder SAML för att autentisera användare. Information om SAML-kontroller finns i [Azure Active Directory referens för SAML-token](reference-saml-tokens.md).

## <a name="validate-security-tokens"></a>Verifiera säkerhetstoken

Det är upp till appen för vilken token har skapats, webbappen som signerade användaren eller webb-API: et som anropades för att validera token. Token signeras av auktoriseringsservern med en privat nyckel. Auktoriseringsservern publicerar motsvarande offentliga nyckel. För att validera en token verifierar appen signaturen med hjälp av den offentliga nyckeln för auktoriseringsservern för att verifiera att signaturen skapades med hjälp av den privata nyckeln.

Tokens är bara giltiga för en begränsad tid. Vanligt vis tillhandahåller auktoriseringsservern ett par token, till exempel:

* En åtkomsttoken som ansluter till programmet eller den skyddade resursen.
* En uppdateringstoken som används för att uppdatera åtkomst-token när åtkomsttoken ligger nära att gå ut.

Åtkomsttoken skickas till ett webb-API som Bearer-token i `Authorization` huvudet. En app kan tillhandahålla en uppdateringstoken för auktoriserings servern. Om användarens åtkomst till appen inte har återkallats, kommer den att få tillbaka en ny åtkomsttoken och en ny uppdateringstoken. Detta är hur scenariot för någon som lämnar företaget hanteras. När auktoriseringsservern tar emot uppdateringstoken, utfärdar den ingen annan giltig åtkomsttoken om användaren inte längre är auktoriserad.

## <a name="json-web-tokens-and-claims"></a>JSON-webbtoken och anspråk

Microsoft Identity Platform implementerar säkerhetstoken som JSON-webbtoken (JWTs) som innehåller *anspråk*. Eftersom JWTs används som säkerhetstoken kallas den här typen av autentisering även *JWT-autentisering*.

Ett [anspråk](developer-glossary.md#claim) ger intyg om en entitet, till exempel ett klient program eller en [resurs ägare](developer-glossary.md#resource-owner), till en annan entitet, till exempel en resurs Server. Ett anspråk kan också kallas för ett JWT-anspråk eller ett JSON Web Token-anspråk.

Anspråk är namn eller värdepar som vidarebefordrar fakta om ämnets token. Ett anspråk kan till exempel innehålla fakta om säkerhets objekt som autentiserats av auktoriseringsservern. De anspråk som förekommer i en specifik token är beroende av många saker, till exempel typen av token, vilken typ av autentiseringsuppgift som används för att autentisera ämnet och program konfigurationen.

Program kan använda anspråk för olika uppgifter, t. ex. för att:

* Verifiera token.
* Identifiera mottagarens token- [innehavare](developer-glossary.md#tenant).
* Visa användar information.
* Bestäm ämnets auktorisering.

Ett anspråk består av nyckel/värde-par som innehåller information som:

* Säkerhetstoken som genererade token.
* Datum när token genererades.
* Ämne (till exempel användare--utom för daemon).
* Mål grupp, som är appen för vilken token genererades.
* App (klienten) som bad om token. När det gäller Web Apps kan den här appen vara samma som mål gruppen.

Mer information om hur Microsoft Identity Platform implementerar token och anspråks information [finns i åtkomsttoken](access-tokens.md) och [ID-token](id-tokens.md).

## <a name="how-each-flow-emits-tokens-and-codes"></a>Hur varje flöde avger tokens och koder

Beroende på hur din klient har skapats kan den använda en (eller flera) av de autentiserings flöden som stöds av Microsoft Identity Platform. Dessa flöden kan producera olika tokens (ID-token, Refresh tokens, åtkomsttoken) och auktoriseringsregler. De kräver olika token för att de ska fungera. Den här tabellen ger en översikt.

|Flöden | Innebär | ID-token | Åtkomsttoken | Uppdatera token | Authorization code (Auktoriseringskod) |
|-----|----------|----------|--------------|---------------|--------------------|
|[Flöde för auktoriseringskod](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Implicit flöde](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybrid OIDC-flöde](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Uppdatera token-inlösen](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | Uppdatera token | x | x | x| |
|[On-Behalf-Of-flöde](v2-oauth2-on-behalf-of-flow.md) | Åtkomsttoken| x| x| x| |
|[Klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md) | | | x (endast app)| | |

Token som utfärdas via det implicita läget har en längd begränsning eftersom de skickas tillbaka till webbläsaren via URL: en, där `response_mode` är `query` eller `fragment` . Vissa webbläsare har en gräns för storleken på URL: en som kan placeras i webbläsarens fält och inte fungerar när den är för lång. Detta innebär att dessa token inte har `groups` eller är `wids` anspråk.

## <a name="next-steps"></a>Nästa steg

Mer information om autentisering och auktorisering i Microsoft Identity Platform finns i följande artiklar:

* Läs mer om grundläggande begrepp för autentisering och auktorisering i autentisering och [auktorisering](authentication-vs-authorization.md).
* Information om hur du registrerar ditt program för integrering finns i [program modell](application-model.md).
* Läs mer om inloggnings flödet för webb-, skriv bords-och mobilappar i [appens inloggnings flöde](app-sign-in-flow.md).
